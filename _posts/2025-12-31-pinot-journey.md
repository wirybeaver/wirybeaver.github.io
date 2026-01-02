---
layout:     post
title:      A 4-Year Journey Inside Apache Pinot
excerpt:    ""
author:     "Xuanyi"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Data
---

Apache Pinot is known for its ability to deliver low-latency analytics on immutable data at massive scale. But "scale" isn't a static target; it requires constant reinvention of the underlying segment commit protocols, indexing strategies, and query execution paths.

Over the last four years, my contributions have focused on hardening Pinot’s core infrastructure—segment commit protocol, cross-cluster table replication, indexing, query routing and query execution.

# 1. Segment Commit Protocol

In distributed data systems, data movement is often the killer of reliablity. When a Pinot cluster rebalances or restarts, thousands of segments must be downloaded to servers.

**Peer-to-Peer Segment Download**

The Problem: Historically, Pinot servers downloaded offline segments exclusively from the Deep Store (e.g., S3, HDFS). During mass restarts, this created a massive egress spike, throttling the Deep Store and slowing down segment availability.

The Solution: I designed and implemented a Peer-to-Peer (P2P) Segment Download mechanism ([PR #9710](https://github.com/apache/pinot/pull/9710); [Design Doc](https://docs.google.com/document/d/1HtU8NsYz84NiAKGh16Yv8WKxyhdXHx-KNsINcWZ7MEI/edit?usp=sharing)).

* How it works: Servers can now act as download peers. When a server needs a segment, it first checks if other active servers in the cluster already hold that segment.
* Impact: This drastically reduces load on the Deep Store and accelerates cluster convergence time during scaling events.

**Segment Lifecycle Hygiene**

To further improve storage reliability, I addressed the "SplitCommit" protocol. I introduced a mechanism to periodically delete temporary segment files left behind by the SplitCommit End phase ([PR #10815](https://github.com/apache/pinot/pull/10815)). This ensures that disk space is reclaimed efficiently, preventing gradual storage bloat on long-running nodes.

# 2. Cross-Cluster Table Replication

Migrating or replicating real-time tables across clusters is notoriously difficult because consuming segments are in constant flux.

The Feature: I led the design and implementation of Real-Time Table Replication ([PR #17235](https://github.com/apache/pinot/pull/17235); [Design Doc](https://docs.google.com/document/d/1DDSW8aG1UHa3eCBmecS2Y9zsj7AeP29tCjlCMHW0Q5Q/edit?usp=sharing)).

The Challenge:
Unlike static data, real-time segments rapidly transition between `IN_PROGRESS`, `COMMITTING`, and `DONE` states. The core difficulty is ensuring Data Consistency: the target cluster must resume consumption exactly where the source left off without missing a single Kafka message or duplicating data, all while handling race conditions in Zookeeper metadata.

The Solution:
I architected a One-Time Replication Protocol that solves this via two distinct phases:

* Phase 1: Consuming Segment Creation (The "Watermark"):
The system calculates a precise "watermark" (Kafka offset and segment sequence) from the source cluster. It then "manufactures" the initial consuming segments on the target cluster, forcing it to start ingestion from that exact point to ensure zero data loss.

* Phase 2: Async Historical Backfill:
Once the target is actively consuming new data, a background Controller Job asynchronously backfills the immutable historical segments from the source.

This feature is critical for Disaster Recovery (DR) bootstrapping and Cluster Migration, allowing operators to clone tables across regions with a single API call.

# 3. Indexing Optimizations

The index is where the CPU spends most of its time. My work here focused on memory efficiency and fixing subtle concurrency bugs.

**V4 Index: Reducing Allocation Overhead**

In high-throughput systems, Garbage Collection (GC) pressure is a major latency source. I optimized the V4 Index by eliminating useless byte array allocations ([PR #12978](https://github.com/apache/pinot/pull/12978)). By reusing buffers and avoiding temporary object creation during index reads, we significantly smoothed out the p99 latency curve.

**V2 Index: Plugging Memory Leaks**

I identified a critical memory leak in the V2 Index caused by improper `ThreadLocal` usage ([PR #12242](https://github.com/apache/pinot/pull/12242)). `ThreadLocal` variables are powerful but can retain references to heavy objects if not cleaned up, leading to gradual heap exhaustion. This fix improved the long-term stability of Pinot servers under heavy load.

**JSON Index Enhancements**

With semi-structured data becoming the norm, I also contributed improvements to the JSON Index ([PR #12466](https://github.com/apache/pinot/pull/12466)), enhancing its robustness when parsing and querying complex nested JSON structures.

# 4. Query Routing and Execution
Finally, how does the Broker handle the query, and what kind of SQL can we support?

**Intelligent Query Routing**

The Broker is the "traffic controller" of Pinot. I worked on optimizing Query Routing ([PR #15203](https://github.com/apache/pinot/pull/15203)). This improvement refines how the Broker selects which servers to query, enabling the canary query isolation.

**Multiple Window Group Support**

Pinot has been steadily increasing its SQL compliance. One major gap was complex Window Functions. I implemented support for Multiple Window Groups ([PR #16109](https://github.com/apache/pinot/pull/16109)).

* Before: Queries were limited in how they could partition and order window functions.
* After: Users can now run queries with multiple `OVER (PARTITION BY ... ORDER BY ...)` clauses in a single statement, unlocking complex analytical capabilities (like calculating running totals and moving averages simultaneously) directly within Pinot.

# Conclusion
From the low-level byte management in the json index to the sophisticated state management of cross-cluster replication, these last four years have been about making Apache Pinot faster and more reliable.
