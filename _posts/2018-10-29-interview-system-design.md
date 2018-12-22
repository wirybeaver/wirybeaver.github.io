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

Imagine an appplication which reach out millions of users. Request on the server should never fail even if theres is a hardware failure. We should have multiple copies of servers in the system. We should make sure information in each server is the same. This is important because the user should not get contradicting information when their requests is relayed by the load balancer and served on different servers over time.

