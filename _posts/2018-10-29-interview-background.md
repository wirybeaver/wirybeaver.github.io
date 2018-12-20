---
layout:     post
title:      CS Background Question
date:       2018-10-29 09:00:00
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Interview
---
# Language
## Core Java
http://www.developersbook.com/corejava/interview-questions/corejava-interview-questions-faqs.php

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

# OS
## CAS

## Dead Lock

## Virtual Memory

# OOD
## Management System
General Use case <br>
- reserve
- serve
- checkout

Clarify<br>
- What.
    - parking lot: multi-level
    - vehicle: multiple size
    - parking spot: same size
- How
    - parking strategy
    - tips by time

Core object<br>
- Bus,Car, Motorcyle
- Parking Lot
- Level
- Parking Spot

Use case<br>
- get available count
- park vehicle
    - check vehicle size
    - get list of available spots
    - vehicle take the spot
- clear spot
    - update spot
    - update level's avaliable count
- calculate price

Class<br>

- ParkingLot
    - private List<Level>
    - public int getAvailableCount()

- Level
    - private List<Spot>
    - private int availableCount
    - public int getAvailableCount()

- Spot
    - boolean isAvailable
    - boolean isAvailable()
    - void takeSpot()
    - void leaveSpot()

- Vehicle
    - protected int size
    - public int getSize()




