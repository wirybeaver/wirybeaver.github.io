---
layout:     post
title:      System Design Primer
author:     "Shane"
excerpt:    "Key concept in system design interview and distributed system"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - System Design
---

### General bottleneck
1. Qps and bandwidth
2. how to improve the performance: async, non-blocking reactor pattern, scale out, rate limiting

### Rate limiter and load shedder

when users can afford to change the pace at which they hit your API endpoints without affecting the outcome of their request, then a rate limiter is appropriate.

load shedding: drop low-priority requests, make critical requests get throught.

#### Request rate limiter
Each user have at most *N* (1000) requests per second.

#### Concurrent rate limiter
Each user have at most *M* (20) API requests in the process at the same time.

Intuition: resource-intensive API, chances are users get frustrated and then retry

#### Fleet usage load shedder
Reservce a fraction of infrastructure for critical requests. 

How: divide up traffice into two types: critical and not critcal. Use Redis cluster to count how many requests we have of each type.

#### Worker utilization load shedder

When a worker is too busy to handle its request volume (starts getting back up with requests), then this will shed lower-priority requests. If shedding gets it back into a good state, then start to slowly bring traffic back. Otherwise, this will escalate and start shedding even more traffic.

traffic priority: critial methods > POST > GET > test mode traffic

### Scaling
Scalability: the ability to handle more requests by adding more resources.

#### X Scaling

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

#### Z Scaling
router **inspect the request** and use some attributes to route the request to a server. In the world of database that would be sharding, using the primary key to determine on which database rows should **reside**

> Consistent Hash

**Motivation**<br/>
decouple the data-partition map and partition-machine map. Adding or deleteing a phycial machine impact less on the partiton-machine map.

**Method**<br/>
Map request ID and server ID into a same ring hash space. Clockwise assign the request to the nearest server. 

**What's the catch?**<br/>
In real world, the amount of physical servers is relatively less than the hashing space. Thus **skewed load** is likely to occur. For example, if we remove one physical server, the successor has to take all load from the presessor, whose amount is possbibly large.

**How**<br/>
Make virtual servers with K hash functions. As a result, one phycial server manage K evenly distributed virtual node in the ring hash space. If one server is removed , the corresponding space would evenly hit multiple regions. The load increasement of each existing server is expectedly uniform.

#### Y scaling
[Video 1 by Confluent](https://videos.confluent.io/watch/8MLuNHnE3uSZPgstdzSk4Q?)

[Video 2 Saga](https://youtu.be/YPbGW3Fnmbc?list=WL)

microservice, function decomposition. Break down large systems into pieces of standalone services, each service has its own DB. API gateway acts as a facade and expose public API to clients.

Pros: autonomous work, agile development, independently deployable and scalable, adopt new tech

<p id="pz">Cons: complexity of distributed system</p>
- service discovery. Zookeeper kicks in.
- inter-process communication across servers. RPC deal kicks in.
- transactions across database. Saga kicks in.
- test.
- deployment. Docker take care of this awful task

> Saga

Somthing Complicated<br>
1. Rollback using compensating transactions.<br>
Each local transcation Ti has an associated compensating transaction Ci.
2. Request obviously create a saga. But when to response?<br>
Prefer to send back immediatesly instead of after completes. Good news is *highly availability*. No longer dependent on all components being up. They don't have to be capable of repsonding in a **timely** way. That order will still get verified. Downside is that response doesn't specify the outcome. Client must poll or notified.<br>
Minimal UI impact<br>
- UI hides asynchronous API from the user. Humans' brain is slow.
- Saga will usually appear instantaneous. <= 100s
- If it takes longer -> Ui displays "processing" popup
- Server can push notifications to UI with Websocket.
3. compliate the business logic. Other transaction see "inconsistent data" due to the pendting state.

How to sequence saga?<br>
Orchestration-based coordination: centrialized decision making. 

*Orchestration*<br>
state machine: send out command, invoke participants, wait response, move to next state. 

*Participants*<br>

### Kafka
[Video](https://www.youtube.com/watch?v=UEg40Te8pnE&t=1609s) 

**Role**  
message queue, pull model

**Essence**  
an event redger could go back in time, distirbuted commit log

<p id="pzb">Fundamentals</p> 
- distributed (horizontal scaling, auto-rebalancing)
- redundant (creat multiple copies of events)
- persisted
- order guaranteed
- multiple subscriber
- pull

<p id="pzb">Component</p>
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

<p id="pzb">Distribution</p>
- Producer view: Topic could have multiple partions over servers. A server is a leader for some partitions and a follower for others.
- Consumer view: each partition is consumed by exactly one consumer in the group. Message consumption is balanced across all consumers in a group. 


### [Webhooks](https://blog.restcase.com/webhooks-role-in-the-api-world/)

The main advantage of the webhooks pattern is that your application doesn’t have to make periodic calls to APIs while it’s waiting for changes. Instead, APIs will call your application on a specific endpoint informing that something interesting has happened. What’s missing is a way to programmatically tell APIs that you’re interested in receiving calls and registering endpoints.

A webhook (also called a web callback or HTTP push API) is a way for an app to provide other applications with real-time information. A webhook delivers data to other applications as it happens, meaning you get data immediately.

[Stripe WebEndpoint example](https://stripe.com/docs/api/webhook_endpoints/create)

### Zookeeper

### Load Balancer VS Reverse Proxy

Load Balancer distributes load across multiple servers as the function of their current load

Reverse proxy is "public face". It centr

<p id="pzb">Commonality</p>
sit between clients and servers, accept requests from the former and deliver responses from the latter.

<p id="pzb">Difference</p>   
Deploying a load balancer makes sense only when you have multiple servers whereas it makes sense to deploying a reverse proxy even with just one server. For me, reverse proxy is more than a load balancer.

<p id='pzb'>Load Balancer</p>
- load balancing
    - pro: prevent overload on any server
- health check
    - pro: when servers go down, divert requests away from them to the other servers in the group.
    - simple way: intercept error responses to regular requests
    - flexible and sophisticated way: separate health-check request
- session persistence (sticky session)
    - def: send all requests from a particular client to the same server
    - use case: online chart

<p id='pzb'>Reverse Proxy</p>
<p id ='pz'>a web server centralizes internal services and provides unified interfaces to the public.</p>
- security: protect from DDoS using blacklist and  rate limit
- flexibility: free to change backend configuration
 - web acceleration
    - SSL termination
    - Compression
        - reduce bandwidth
    - Content Caching
        - decrease RT
        - reduce load on backend servers

### Questions
1. Given a (typically) long URL, how would you design a TinyURL or bit.ly (a URL shortening service), the unique ID for a shortened form of the same web address?
2. Design a social network, such as Facebook, Quora or Reddit, on which users can view and post messages, comments, and links.
3. Design a global chat service, such as WhatsApp or Messenger, that is both user-friendly and secure.
4. Design a global video streaming service, like YouTube, including essential features, such as data recording and social commenting.
5. Design a global file storage and sharing service, optimized for simultaneous use by multiple users.
6. Design a search engine or related services such as a web crawler or type ahead.

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

### Footnote




