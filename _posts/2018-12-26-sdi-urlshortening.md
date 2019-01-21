---
layout:     post
title:      URL Shortening
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - System Design
---

### Use Case

1. shortering
2. rediretion
3. custom url
4. HA

**Out of Scope**
1. analytics
2. no guess
3. expiration

### Clarify Constraint
1. read to write ratio
2. bandwidth
3. Qps: 10k

### Abstract Design
Sketch out high level design  
1. Application service layer (serve the request)
- shortening service
- redirection service
- custom service
2. Data storage layer (keep track of the hash -> url mapping)

### Core Function

### Back-envelop

### Foot Note





