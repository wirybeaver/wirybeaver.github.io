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

## [Transcation Isolation level](https://mydbops.wordpress.com/2018/06/22/back-to-basics-isolation-levels-in-mysql/)
- read uncommited: Uncommited data changes is visible to other transactions.
    - How: No locks.
    - Problem: dirty read
- read commited: Avoid dirty read, i.e., uncommited data changes is visible to other transactions. "Perplexed" state.
    - How: Each SELECT has its own snapshot of commited data that was commited before the execution of the SELECT.
    - Problem: non-repeatable read, running same SELECT multiple times during the same transaction could obtain different results.
- repeatable read: Avoid unreaptable read, i.e., running same SELECT multiple times during the same transaction could obtain same results.
    - How: Same SELECT use same snapshot.
    - Problem: phantom reads. Phantom: a row apprears where it is not visible before
- serializable read: complete isolated
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
BRF: circular wait with the assumption mutual exclusion and no preemption
Four Conditions:
1. Mutual exclustion: Only one thread at a time can access a resource
2. Hold and wait: holding one and waiting another held by other threads
3. No preemption: voluntarily release
4. Circular wait

## Virtual Memory
contiguous bytes array stored on the disk. Use physical memory (DRAM) as a cache.

Benifits:
1. Memory Efficiency: use DRAM as a cache.
2. Memory management: each process gets uniform linear address space.
    - Simplify mem allocation: mappend to any PP. 
    - Sharing code and data among processes.
    - Linking and Loading in the same region at virtual space.
3. Memory Protection: Add additional check field in Page tables: READ, WRTIE, EXEC. Check by MMU.

PM and VM are divided into fixed size blocks called pages. Address the fragmentation problem.

Page Table: an array of page table entries that maps virtual pages to physical pages. 

Page Hit: reference to VM word that is in PM. (Judge by the field valid)

Page Fault: reference to VM word that is not in PM. Select a victim block to be evicted if PM is full.

## Lock Implementation


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






