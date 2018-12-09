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

# Topological sort

format a linear ordering s.t for each directed edge u->v, u comes before v in the ordering.

Observation: if the node has no incoming edges, it should be sorted first.

Straight forward way: Start with a node having no incoming edges, put it into the ordering list. And it from the graph as well as its outgoing edges. Repeat on the remaining graph

In practice, generate an array to keep track of the indegree of each node. We don't delete the outgoing edge right on the graph. Instead, we decrease the indegree of destination node. The other thing I typically use is a FIFO queue, which temporarily store nodes with zero indegree. If the q is not empty, iterate once more. In each
while loop, pop out a node, append it to the ultimate ordering. Walk through all outgoing edges, decrease the indegree of neighboring node. If we find the indegree become zero, we put that node into the q. Repeat the while loop.

Eventually, check if all node's indegree becomes zero. If not, it indicates there is a cycle in the graph.

# Union Find
keep track of elements partitioned into disjoint sets. Unify operation: merge two sets. Find option: find the id of the associated set.

Use a tree to represent a set. The identify of a set is the root of the tree.

Find operation: 
- Straigh forward is going from bottom to up until we arrive at the root. Worse case, take linear time.
- Optimize to amortized constant time using path compression. Use an array to keep track of the root instead of the immediate parent. Everything along the path got compressed. 

Union operation: given two elments, get two roots using find operation respectively. Make one of them pointing to anohter.

# complete binary tree
every level is completely filled except possibly the last level. All nodes are as far left as possible.

# Heap
keep track of the largest or smallest elment. Use a complete binary tree, For each subtree, the top node is the largers one. In practice, use an array to represent a complete binary tree. Zero based, given index i, then the left child index is 2i+1 and the right child index is 2i+2, the parent index is the floor of half of i-1. 

Add a node. temporarily append it to the array. Swap out of order parent-child pair from bottom to up until find a right position.

Delete a node. Replace the root with the tailing element, swap out of order parent-child from up to bottom until find a right position.