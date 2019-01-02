---
layout:     post
title:      WhatsApp
date:       2018-12-30 09:00:00
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - System Design
---

### Use Case

**Functional Requirement**

**Performance**
HA

**Out of Scope**

### Constraint 
1. read to write ratio
2. bandwidth
3. Qps

### Abstract Design

### Schema
Message Table, NoSQL (large Volume, immutable), sharding key: message_id, column key: created_time <br>
message_id, thread_id (sharding key), sender_id, content, created_time

Thread Table, SQL (secondary index), primary key: owner_id, thread_id <br>
owner_id (used to identify private information), thread_table_id, paticipant_ids, update_time, is_muted, nickname

BiUser Table, SQL
user1_id, user2_id, thread_id

### Solution

#### Deliver Message
Case1: Two Users<br>
API: send(from_user, to_user, message)<br>
1. getThreadId(from_user, to_user)
2. insertMsg(from_user, thread_id, message)

Case2: Group Chat<br>

### Scale out

### Foot Note





