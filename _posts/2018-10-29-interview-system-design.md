---
layout:     post
title:      System Design
date:       2018-12-21 09:00:00
author:     "Shane"
excerpt:    "Key concept in system design interview and distributed system"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Interview
---

### Resource List
- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [Tian Pan's Post](https://puncsky.com/hacking-the-software-engineer-interview)
- [Distributed System for fun and Profit](http://book.mixu.net/distsys)
- Others
    - [System Design Interview Questions](http://blog.gainlo.co/index.php/category/system-design-interview-questions/)
    - [educative.io](https://www.educative.io/collection/page/5668639101419520/5649050225344512/5668600916475904)
    - [interviewbit](https://www.interviewbit.com/courses/system-design/topics/storage-scalability/)
    - [tackling-the-system-design-interview](https://cternus.net/blog/2018/01/26/tackling-the-system-design-interview/)
    - [OOD Questions](https://www.careercup.com/page?pid=object-oriented-design-interview-questions&n=1)

### Scaling

Scalability: the ability to handle more requests by adding more resources.

| Horizontal Scaling | Vertical Scaling|
|------------------|-------------------|
|buy more machines|buy a bigger machine|
|Load Balanceing required[^1]|N/A|
|resilient|single point of failure|
|network calls (RPC)|inter process communitation|
|data consistency is a real issure[^2]|consistent|
|scales well as users increase[^3] |hardware limit|

[^1]: The request falls on any of the machine

[^2]: The data could be sent from ont server to another. Data is complicated to maintain. If there's a transaction where operation have to be atomic, what could happens is to lock all server's read which is impractical. So usually what happens is we have some sort of lose transactional guarantee.

[^3]: scales well in a sense that the amount of servers you throw at the problem is linear in terms of how many users are added

### Consistent Hash
Motivation: decouple the data-partition map and partition-machine map. Adding or deleteing a phycial machine impact less on the partiton-machine map.

Method: Map request ID and server ID into a same ring hash space. Clockwise assign the request to the nearest server. 

**What's the catch?**<br>
In real world, the amount of physical servers is relatively less than the hashing space. Thus **skewed load** is likely to occur. For example, if we remove one physical server, the successor has to take all load from the presessor, whose amount is possbibly large.

**How**<br>
Make virtual servers with K hash functions. As a result, one phycial server manage K evenly distributed virtual node in the ring hash space. If one server is removed , the corresponding space would evenly hit multiple regions. The load increasement of each existing server is expectedly uniform.

### Kafka
[Video](https://www.youtube.com/watch?v=UEg40Te8pnE&t=1609s) 

**Role**<br>
distributed stream platform

**Essence**<br>
an event redger could go back in time, distirbuted commit log

**Fundamentals**<br>
- distributed (horizontal scaling, auto-rebalancing)
- redundant (creat multiple copies of events)
- persisted
- order guaranteed
- multiple subscriber
- pull

**Component**<br>
- Producer: write data to a broker
- Consumer: read data from a broker
- Broker: a node in the broker
- Cluster: a group of computers sharing workload for a common purposes.
- Record: key, value, timestamp. Immutable, persisited
- Partition: sequence of record. Do partitioning by hand or based on key. Kafka would do something like a round-robin accross the partition if you produce a message without partitioning.
    - Append-only
    - Strong ordered: order is only guaranteed in a certain partition. May use timestamp to achieve total ordering across partitions within the topic.
    - Replicated: each partition has one leader server and zero or more follewer surver. Replication Factor based on topic
- Topic: logic name to which data subscribe. Composed of one or more partition.

**Distribution**
- Producer view: Topic could have multiple partions over servers. A server is a leader for some partitions and a follower for others.
- Consumer view: each partition is consumed by exactly one consumer in the group. Message consumption is balanced across all consumers in a group. 

### Zookeeper

### Load Balancer VS Reverse Proxy
**Commonality**<br>
sit between clients and servers, accept requests from the former and deliver responses from the latter.

**Difference**  
Deploying a load balancer makes sense only when you have multiple servers whereas it makes sense to deploying a reverse proxy even with just one server. For me, reverse proxy is more than a load balancer.

**Load Balancer**<br>
- load balancing
    - pro: prevent overload on any server
- health check
    - pro: when servers go down, divert requests away from them to the other servers in the group.
    - simple way: intercept error responses to regular requests
    - flexible and sophisticated way: separate health-check request
- session persistence (sticky session)
    - def: send all requests from a particular client to the same server
    - use case: online chart

**Reverse Proxy**<br>
- security
- web acceleration





