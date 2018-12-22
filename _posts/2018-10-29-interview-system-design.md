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

Imagine an appplication which reaches out millions of users. Request on the server should never fail even if theres is a hardware failure. We should have multiple copies of servers in the system. We should make sure information in each server is the same. This is important because the user should not get contradicting information when their requests is relayed by the load balancer and served on different servers over time.

Nothing really. Could is a set of computers. You pay could solution providers and they give you computation power. Computation power is nothing but a desktop that they have somewhere and run your algorithm. The reason we like to do this is because the configuration and the reliability can be taken care of to a large extent by clouder solution providers. 

Scalability: the ability to handle more requests by adding more resources.

Like we said.

| Vertical Scaling | Horizontal Scaling|
|------------------|-------------------|
|buy a bigger machine|buy more machines|
|Load Balanceing required[^1]|N/A|
|resilient|single point of failure|
|network calls (RPC)|inter process communitation|
|data consistency is a real issure[^2]|consistent|
|scales well as users increase[^3] |hardware limit|

[^1]: The request falls on any of the machine

[^2]: The data could be sent from ont server to another. Data is complicated to maintain. If there's a transaction where operation have to be atomic, what could happens is to lock all server's read which is impractical. So usually what happens is we have some sort of lose transactional guarantee.

[^3]: scales well in a sense that the amount of servers you throw at the problem is linear in terms of how many users are added




