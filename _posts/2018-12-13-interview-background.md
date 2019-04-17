---
layout:     post
title:      CS Background Question
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Interview
---
# Language
## [Core Java](http://www.developersbook.com/corejava/interview-questions/corejava-interview-questions-faqs.php)

### polymorphism
A method or an object can have more than one behavior. Java achieve polymorphism with overload and override.

### Exception and Error
Exception: could be handled by program. An event occurs during the program execution and disrupts the norm flow.
Error: unrecoverable, JVM report it, e.g. OutOfMemoryError
Unchecked Exception: Runtime exception, e.g. IndexOutOfBounds, NullPointer, IllegalArgument, ClassCast
checked Exception: exceptions excludeind run time excpetion. e.g. NoSuchMethod, ClassNotFound, IO


## Functional programming
**What**<br>
Functianl programming is so called because a program consists entirely functional. The main function is defined by other functions, which in turn are still difined by more functions, until at the bottom level the functions are language primitives.

**Why**<br>
- no assignment. Variab les never changes once given a value.
- no side-affect. A function have no affect other that compute itself.
- no flow control. Expression's execution order is irrelevant.
- eliminate a major source of bugs.
- relief programmers of the burden of prescibing the flow control

# Database
## join
**inner join**<br>
only produce records that match both left and right table

**left join**<br>
produce all records in left table, with matching records from right table where available. If no match, the right side would be null

**full join**<br>
produce all records in left table and right table, with matching records from both side where available. If no match, the left side or right side would be null.

## ACID

## Lock
pessimistic solutions. prevent bad interleaving.

### 2PL
– requests a lock on a data object when it needs the object. Cannot requests a lock anymore after it has unlocked an object.
- acquire phase, lock point and release phase. Note: release only after acquire all locks.
- problem 1: dead lock
- problem 2: cascaded roll-back. strict 2 phase: hold all locks until commit.

### Timestamp Locking


### Optimistic Concurrency Control
Key idea: execute transactions without concurrency control, and validate their execution is appropriate before commit.

Implementation:
- record a timestamp for transaction
- read from the DB and write to a copy.
- validate: check conflicts. Depends on isolation level. Snapshot isolation: whethr updated objects are modified after T1 starts. Serializability: option 1: check serializability graph. option 2: read object modified after T1 starts.

### BCC.
OCC on serializability graph: any edge of from T1 to T2.
BCC: any edge of from T1 to T2 and any edge from T2 to T1.

## Transaction

### Clear Serializability
– every read operation reads from the same write in both logs
– both logs have the same final writes

### [Transcation Isolation level](https://mydbops.wordpress.com/2018/06/22/back-to-basics-isolation-levels-in-mysql/)
- read uncommited: Uncommited data changes is visible to other transactions.
    - How: No lock.
    - Problem: dirty read
    - Lock Based Definition: lock before access and unlock after access
- read commited: Avoid dirty read, i.e., uncommited data changes is visible to other transactions. "Perplexed" state.
    - How: Each SELECT has its own snapshot of commited data that was commited before the execution of the SELECT.
    - Problem: non-repeatable read, running same SELECT multiple times during the same transaction could obtain different results.
    - Lock Based Definition: hold write lock to the end; release read lock after access.
- repeatable read: Avoid unreaptable read, i.e., running same SELECT multiple times during the same transaction could obtain same results.
    - How: Same SELECT use same snapshot.
    - Problem: phantom reads, a row apprears where it is not visible before; Write screw.
    - Lock Based Definition: hold lock to the end. Stronger then the definition 1. Equivalent to the strict 2PL.
- serializable read: complete isolated
    - How: hold lock to the end + range lock
    - How: sequential transaction.

## [Clustered Index and Secondary Index](https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html)
Clustered Index: data raws with similiar index values would be physically stored together. InnoDB, a leaf node is a disk page, storing the actual data. Pro: provide linear access to data and save disk I/O.

Secondary Index: a leaf node stores primary keys.

# Computer Organization

Bus: a collection of parallel wires that carry address, data and control signals. Buses are typically shared by multiple devices.

Memory read transaction (movq A, %rax)</br>
1. CPU places addr A on the memory bus.
2. Main memory reads A from the memory bus, retrives word x , and places it on the bus.
3. CPU read word x from the bus and copies it into register %rax.

Memory write transaction (movq %rax, A)</br>
1. CPU places address A on bus. Main memory reads A and waits for the corresponding data word to arrive.
2. CPU places the word y on the bus.
3. Main memory reads data word y from the bus and stores it at address A.

Disk controller: the hardware/firmware maintains the mapping between logical blocks and (surface, track, sector) triples.

Reading a Disk Sector </br>
1. CPU put a command, logic block number and destination memory address to a port(address) associated with the disk controller.
2. Disk controller reads the sector and performs a direct memory access (DMA) transfer into main memory.
3. Disk controller notifies the CPU with an interrupt (i.e., asserts a special "interrupt" pin on the CPU)

Linking and Loading:
1. Memory Space of a program: Code(Read Only), Data, Heap, Page Table, Stack
2. Loading: allocates VP for code and data and create PTEs.

# OS
## CAS

## Dead Lock
Def: a thread is waiting for a condition that will never be true.

Four Conditions:
1. Mutual exclustion: Only one thread at a time can access a resource
2. Hold and wait: holding one and waiting another held by other threads
3. No preemption: voluntarily release
4. Circular wait

Stratege 1: Prevention. Granted requests never leads to deadlock. Processes accquire resources in order. <br>

Stratege 2: Avoidance. Resources are granted only if the resulting system state is safe, i.e.,
there is at least one sequence of execution in which all processes run to completion.

Stratege 3: Detection. Periodically checks for deadlocks, and a recovery procedure will be started if a deadlock is detected. 

Graph Reduction Method.<br>
Idea: Use a graph to test set of all operations on the system. Reduction is the most optimistic set of operations that may unblock one or more blocked processes.

GRG (General Resource Graph): nodes represents processes, consumable resources and reusable resources; edges represents the interaction between resources. GRG represents the snapshot of the system.

Complete Reduciable: a set of reductions done on unblocked processes could delete all edges.

Expedient State: All grantable requests are granted.

Reusable Resource System: CR == No DeadLock. Reduction Order doens't matter. Proof the truth of Banker's Algorithm.

Reusable Resource Single Unit System: Expedient State + Cycle == Dead Lock. Dead Lock Detection.

Banker's Algorithm <br>
Idea: Each thread estimates maximum resources it needs. When a thread actually asks resouces, checking: <br>
1. Check if any thread’s (maximal resource – acquired resource) can be satisfied.
2. If yes, release all the thread’s acquired resource, because this thread will not be blocked. Go to step 1.
3. If no, then the graph is not completely reducible.
4. If all threads can be released, then the graph is completely reducible.

## [Virtual Memory](http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/lectures/17-vm-concepts.pdf)
contiguous bytes array stored on the disk. Use physical memory (DRAM) as a cache.

Benifits:
1. Memory Efficiency: use DRAM as a cache. Only because of  locality
2. Memory management: each process gets uniform linear address space.
    - Simplify mem allocation: mappend to any PP.
    - Sharing code and data among processes. Many VP could be mapped to one PP.
    - Linking and Loading in the same region at virtual space.
3. Memory Protection: Add additional check field in Page tables: READ, WRTIE, EXEC. Check by MMU.

PM and VM are divided into fixed size blocks called pages. Address the fragmentation problem.

Page Table: an array of page table entries that maps virtual pages to physical pages. 

Page Hit: reference to VM word that is in PM. (Judge by the field valid)

Page Fault: reference to VM word that is not in PM. Select a victim block to be evicted if PM is full. If victim is dirty, page into the disk.

1. CPU generate VA (Virtual Addr). VA = VPN (Virtual Page Number, index of Page Table) -- VPO (Virtual Page Offset)

2. MMU send PTEA (Page Table Entry Addr) to PM (physical memory) and fetch PTE from the memory. PTEA = VPN+@PTBR(Page Table Base Register)

3. check the valid bit in the PTE.
    - case valid:
        4. grab the PPN. Send PA to PM. PA = PPN -- PPO, PPO = VPO
        5. PM send the word to CPU.

    - case invalid:
        4. trigger page fault exception
        5. handler idenfity victim page. If dirtry, page it out to disk.
        6. handler page in new VP and updates PTE in memory.
        7. hanlder returns to original process. Restart offending instruction.

TLE: cache Page Table Entries, eliminate memory access for PTE.

VA = VPN -- VPO. VPN = TLB tag -- TLB index. Use TLB index to select sets. TLBT matches tag of cache line within the set.

K-level page tables.

## Synchronization

### Producer and Consumer

### Reader and Writer

## Malloc


# SE
## Aigle Development
It is an iterative and team based approach. Basically, time is split into phases called ‘sprint’. Typically the span of a sprint is one week. At the start of sprint, teammates would meet together and negotiate the running list of deliverables. All deliverables would be prioritized by business value. Once a functionality is done, I have to design a unit test and notify others to review my code. The system would automatically check the code style and report conflict with other branches. The feature cannot be merged into the production branch until all those stuff are done. If all features cannot be completed, work is reprioritized at the future sprint.
Pro: customer could early see the work and make decisions.
Drawback: refactoring.


## Unit Test Demo
```java
public class ResourceManagerImplTest {
    private CloudProvider provider;

    @Before
    public void init(){
        provider = mock(CloudProvider.class);
    }


    @Test
    public void testPlaceContainerCaseA(){
        // basic case: instanceSize is divisible by containerSize
        // sequential request
        double instanceSize = 2;
        double containerSize = 1;
        when(provider.requestInstance()).thenAnswer( prov -> new InstanceImpl(instanceSize));
        ResourceManager manager = new ResourceManagerImpl(provider, instanceSize, containerSize);
        for(int i=0; i<100; i++){
            manager.placeContainers(1);
        }
        verify(provider, times(50)).requestInstance();
    }
}
```
 
# Frequent git command
> git checkout -b <local new branch>
Note: create new local branch based on the current local branch

> git checkout --track <repo/branch>
Note: create new local branch based on the remote branch, the new local branch default

> git branch -u <repo/branch> (<local branch>)
Note: used to set upstream for current local branch. If no branch is specified it defaults to the current branch.

> git branch --unset-upstream (<local branch>)
Note: If no branch is specified it defaults to the current branch.

> git branch -d <local branch>

> git branch -d -r <repo/branch>

> git fetch --all






