---
layout:     post
title:      Tech Question
date:       2018-10-29 09:00:00
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Interview
---

# Core Java
http://www.developersbook.com/corejava/interview-questions/corejava-interview-questions-faqs.php

# Functional programming
## What
Functianl programming is so called because a program consists entirely functional. The main function is defined by other functions, which in turn are still difined by more functions, until at the bottom level the functions are language primitives.

## Why
- no assignment. Variab les never changes once given a value.
- no side-affect. A function have no affect other that compute itself.
- no flow control. Expression's execution order is irrelevant.
- eliminate a major source of bugs.
- relief programmers of the burden of prescibing the flow control

# Algorithm

## Topological sort
format a linear ordering s.t for each directed edge u->v, u comes before v in the ordering.

Observation: if the node has no incoming edges, it should be sorted first.

Straight forward way: Start with a node having no incoming edges, put it into the ordering list. And it from the graph as well as its outgoing edges. Repeat on the remaining graph

In practice, generate an array to keep track of the indegree of each node. We don't delete the outgoing edge right on the graph. Instead, we decrease the indegree of destination node. The other thing I typically use is a FIFO queue, which temporarily store nodes with zero indegree. If the q is not empty, iterate once more. In each
while loop, pop out a node, append it to the ultimate ordering. Walk through all outgoing edges, decrease the indegree of neighboring node. If we find the indegree become zero, we put that node into the q. Repeat the while loop.

Eventually, check if all node's indegree becomes zero. If not, it indicates there is a cycle in the graph.

## Union Find
keep track of elements partitioned into disjoint sets. Unify operation: merge two sets. Find option: find the id of the associated set.

Use a tree to represent a set. The identify of a set is the root of the tree.

Find operation: 
- Straigh forward is going from bottom to up until we arrive at the root. Worse case, take linear time.
- Optimize to amortized constant time using path compression. Use an array to keep track of the root instead of the immediate parent. Everything along the path got compressed. 

Union operation: given two elments, get two roots using find operation respectively. Make one of them pointing to anohter.

## Complete binary tree
every level is completely filled except possibly the last level. All nodes are as far left as possible.

## Heap
keep track of the largest or smallest elment. Use a complete binary tree, For each subtree, the top node is the largers one. In practice, use an array to represent a complete binary tree. Zero based, given index i, then the left child index is 2i+1 and the right child index is 2i+2, the parent index is the floor of half of i-1. 

Add a node. temporarily append it to the array. Swap out of order parent-child pair from bottom to up until find a right position.

Delete a node. Replace the root with the tailing element, swap out of order parent-child pait from up to bottom until find a right position.

## Binary Index Tree
Query Interval Summation

## Dijstra

## HashMap

### Data Structure
lookup table. An array of bucket, each bucket is associated with a linked list

### How to locate bucket
Step 1. Key could be arbitrary type, we have translate the key to an interger. Fortunately, each java object has a hashCode() function, call Key's hashCode().<br>
Step 2. The interger obtained in the previous step may have bad quality. Amelirate with HashMap's internal hash().<br>
Step 3. mod by table length.<br>
hash(): XORs with higher 16 bits. Ratinale: spread the impact of higher bits downward. Otherwise higher bits would never be used in index calculations if the table length is small;

### When to resize
number of entries > loadFactor * capacity, where capacity is consistent to the table length.

### How to resize
Prerequisite: power of two table length, power of tow expansion.

Observation: For each bucket in the old table, associated elements must either stay at same index or move forward with a fixed offset in the new table. The offset equals old table length. In short, one old bucket is splitted into two new bucket, the index difference is exactly old table length.

Advantage: insertion order is kept.

### ![Source Code](https://leetcode.com/problems/design-hashmap/discuss/205285/reproduce-hash-and-resize-from-Java-8-source-code)

# Database
## inner join
only produce records that match both left and right table

## left join
produce all records in left table, with matching records from right table where available. If no match, the right side would be null

## full join
produce all records in left table and right table, with matching records from both side where available. If no match, the left side or right side would be null.

## ACID

## ![Transcation Isolation level](https://mydbops.wordpress.com/2018/06/22/back-to-basics-isolation-levels-in-mysql/)
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

## ![Clustered Index and Secondary Index](https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html)
Clustered Index: data raws with similiar index values would be physically stored together. InnoDB, a leaf node is a disk page, storing the actual data. Pro: provide linear access to data and save disk I/O.

Secondary Index: a leaf node stores primary keys.

# OS
## CAS

## Dead Lock

## Virtual Memory

# Project

## Sprint Boot

## Microservice

## What you learned

### Production workflow
Document: UML, Sequence 

Git

Unit Test

### Teamwork Responsibility
Integration Test with front-end engineer

### Senior Development Tech
Design Pattern:

Scale web service: Learn the theory and make a practice (RPC, Message broker, Config Management System, Cache)



