---
layout:     post
title:      LakeHouse Note
excerpt:    Brain Dump when read through the source code of modern data technologies
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Data
---

## Flink's Iceberg Sink

This is the core of how the Flink Iceberg sink works, and it's a great "dance" between the Flink runtime and the two main operators. Let's walk through the lifecycle step-by-step.

### The Two Main Actors

1.  `IcebergStreamWriter`: Its job is to take incoming data records, buffer them, and write them into data files (like Parquet or ORC) on a distributed filesystem. It has many parallel instances (subtasks), one for each sink parallelism.
2.  `IcebergFilesCommitter`: Its job is to collect the information about all the data files created by all the `IcebergStreamWriter` instances and commit them to the Iceberg table as a new snapshot. It runs as a single, non-parallel operator (parallelism = 1).

---

### The Lifecycle and Method Calls

Here is the fundamental sequence of events:

#### Phase 1: Job Initialization

1.  **`initializeState(context)` is called.**
    *   **On `IcebergStreamWriter`:**
        *   It gets its runtime context (like its unique subtask ID).
        *   It initializes the `TaskWriterFactory`, which knows how to create writers for data files.
        *   **It creates the very first `TaskWriter` instance.** This is the initial buffer for writing Parquet files.
    *   **On `IcebergFilesCommitter`:**
        *   It loads the Iceberg `Table` metadata.
        *   If it's restoring from a previous Flink checkpoint, it inspects the old snapshots in the Iceberg table to find the last checkpoint ID that was successfully committed. This is crucial to avoid re-committing data after a failure.
        *   It initializes its state, which is where it will keep track of files ready to be committed.

#### Phase 2: Steady State - Processing Data

2.  **`processElement(record)` is called.**
    *   **On `IcebergStreamWriter`:**
        *   This is called for every single data record coming into the sink.
        *   The implementation is simple: `writer.write(element.getValue())`.
        *   The `TaskWriter` instance handles the buffering and spilling of data to the underlying Parquet/ORC file on disk. The file is kept open.
    *   **On `IcebergFilesCommitter`:**
        *   This method is *also* called, but its input is not the raw data. It receives the `FlinkWriteResult` that the `IcebergStreamWriter` emits.
        *   It takes this result and stores it in an internal map (`writeResultsSinceLastSnapshot`) keyed by the checkpoint ID. It does **not** commit anything yet; it just collects the file information.

#### Phase 3: The Checkpoint Dance (The Most Important Part)

This is triggered periodically by Flink's JobManager.

3.  **`prepareSnapshotPreBarrier(checkpointId)` is called.**
    *   **On `IcebergStreamWriter`:**
        *   This is the signal that a Flink checkpoint is about to happen. This operator must now finalize all work related to the *current* checkpoint window.
        *   It calls its internal `flush()` method.
        *   Inside `flush()`:
            *   `writer.complete()` is called. This **releases the buffer**: it finalizes the current Parquet file, closes it, and returns a `WriteResult` object containing the file's metadata (path, statistics, etc.).
            *   It emits this `WriteResult` downstream to the `IcebergFilesCommitter` using `output.collect()`.
        *   Immediately after the `flush()`, it **creates a new `TaskWriter` instance**. This is the new, empty buffer that will be used for all the records that arrive in the *next* checkpoint window.

4.  **`snapshotState(context)` is called.**
    *   **On `IcebergStreamWriter`:** Its state is very simple and managed by Flink automatically.
    *   **On `IcebergFilesCommitter`:**
        *   This is a critical step for fault tolerance. The committer takes all the `WriteResult` objects it has received for the current checkpoint.
        *   It writes them into a temporary *manifest file*. This file is a list of all the new data files for this checkpoint.
        *   It then saves the *bytes* of this manifest file into Flink's durable state backend (e.g., RocksDB on HDFS/S3).
        *   **Why?** If the job fails right after this, upon recovery, the committer can restore this state and know exactly which files were ready to be committed, ensuring no data is lost.

5.  **`notifyCheckpointComplete(checkpointId)` is called.**
    *   **On `IcebergStreamWriter`:** This method is not used; its work is done for this checkpoint.
    *   **On `IcebergFilesCommitter`:**
        *   This is the final signal from the Flink JobManager. It means the checkpoint has been successfully completed across the *entire* Flink job. It is now safe to make an external commit.
        *   **This is when the committer sends data to Iceberg.**
        *   It retrieves the manifest files for the completed checkpoint from its state.
        *   It creates an Iceberg transaction (`AppendFiles` or `RowDelta`).
        *   It adds the new files to the transaction and calls `operation.commit()`.
        *   This commit creates a new snapshot in the Iceberg table, making the new data visible to all other query engines. It also writes the `checkpointId` into the snapshot summary properties for tracking.

### Buffer Creation / Release and File Commit

*   **When does `IcebergStreamWriter` create a new buffer?**
    *   On initialization (`initializeState`).
    *   At the end of every `prepareSnapshotPreBarrier` call, right after flushing the previous one.

*   **When are those buffers released?**
    *   During the `prepareSnapshotPreBarrier` call, when `writer.complete()` is invoked. This finalizes the file and "releases" the buffer by making it ready for commit.

*   **When does `IcebergFilesCommitter` send data files to Iceberg?**
    *   Only inside the `notifyCheckpointComplete(checkpointId)` method. This ensures that data is only made visible in the Iceberg table after Flink guarantees the state has been durably saved, achieving exactly-once semantics.


### The core interaction IcebergStreamWriter and IcebergFilesCommiter 
That line of code inside `IcebergStreamWriter::flush()`, `output.collect(new StreamRecord<>(new FlinkWriteResult(checkpointId, result)))`, is precisely how a Flink operator sends data to the next operator in the stream graph.

Let's break it down:

*   `output`: This is an object of type `org.apache.flink.streaming.api.operators.Output` that Flink provides to every operator. It's the gateway to all downstream operators.
*   `.collect(...)`: This is the method you call on the `output` object to emit a single record downstream.
*   `new StreamRecord<>(...)`: This is the standard wrapper Flink uses for all records flowing through a stream. It contains the actual data element and potentially other metadata like a timestamp.
*   `new FlinkWriteResult(checkpointId, result)`: This is the payload. The `result` object (of type `WriteResult`) contains the list of data files and delete files that were just written and closed by the `writer.complete()` call. This payload is what the next operator needs to work with.

So, the `IcebergStreamWriter`'s responsibility ends here. It writes data files and then emits the results of that work downstream for another operator to handle the commit phase.