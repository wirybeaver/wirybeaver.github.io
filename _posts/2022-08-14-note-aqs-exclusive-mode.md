---
layout:     post
title:      Java Abstract Queued Synchronizer
author:     "Xuanyi"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Concurrency
---


## Dummy Node
The dummy head node never holds a thread, but you can assume the dummy head node represents the thread having the lock in exclusive locking mode. The immediate successor of dummy head is the actual head of wait Queue. In AQS, only the actual head has the right to tryAcquire, other following nodes are parked.

Prev-link is mandorary and Next-link is optional
AQS is a CLH variant. CLH is used for spin locks, AQS implements blocking synchronizer. The essence is the same, the control info is held in the pred node.waitStatus. (waitStatus is overloaded, when waitStatus > 0, it indicate the current node is canceled; when waitStatus < 0, it represent the control info of the successor)

CLH doesn’t have a physical prev-link due to the nature of spin lock. The cur thread can constantly poll the pred node’s status. You may be wondering how a CLH node gets the pred node when the prev-link is absent. When looking at the implementation, you would notice that the local variable pred is an alternative to prev-link, which is returned by CAS getAndSet.

AQS needs physical prev-link to deal with the cancellation case. In the extreme case, there exists a block of chained cancel nodes. The successor of a canceled node needs to use prev-link to traverse backward till find a non canceled predecessor and be re-linked

In order to implement the blocking semantic, the thread holding the lock needs to unpark the blocked non-canceled successor. Next-link is an optimization when the dummy head’s next is non-canceled. Otherwise, traverse backwardly to find the leftmost non-canceled node and unpark the tied thread.

## The Subtlety Intent of UnparkSuccessor
As literally stated, the superficial intent of unparkSuccessor is obvious. Release method would call it when the head waitStatus!=0.

A more advanced yet easier overlooked intent is that the unparkSuccessor is a last resort to stabilize the successor on the new predecessor upon cancellation. Specifically, even though the unparkSuccessor(X) doesn’t modify any queue topology, the leftmost non-canceled thread on the right side of X, which is woken up by unparkSuccessor, will further continue the spin loop and skip the canceled node by invoking shouldParkAfterAcquireFailure. In other words, unparkSuccessor delegates the stabilization to the unparked thread.

As an optimization to reduce the unnecessary overhead of unparking a thread which would be parked again, if the immediate successor of the canceled node is non-canceled, the predecessor’s next can be re-linked to that successor instead of invoking non-predecessor. However, the optimization is not applicable if the predecessor is a head.

Backwardly skipping canceled nodes happens in shouldParkAfterAcquire and cancelAcquire.

## Update Prev-link and Next-link
The prev link is always updated by the current node to avoid data race issues on the prev link.

The node itself never updates the next link. The successors use CAS to update pred’s next link. When does the data race on the next link happen? Considering the cancellation situation, multiple successor can update the same predecessor.

As a performance savviness trick, the Pred.next can be directly relinked if the current thread hasn’t been in the frame of cancelAcquried, which further indicates the node is not in the canceled state.

The first example of CAS exemption happens when inserting a new node to the wait queue. Let say a new node V is added to the queue via CASTail, we can preclude the data race issue caused by cancellation. The thread of V hasn't gone inside the acquireQueued method, V’s waitStatus=0. Even if following nodes are added and canceled, the procedure of backingwardly skipping the canceled node will stop at this new node. In other words, V is the only successor which has the chance to access pred, thus can directly modify pred’s next-link. Similarly, the shouldParkAfterAcquire directly modifies pred.next after backwardly skipping canceled nodes.

## Case Study
Acquire

<img src="https://i.imgur.com/fBYTPgN.png" width="60%" border="5">

Cancel And Release

<img src="https://i.imgur.com/2KWliKq.png" width="60%" border="5">
