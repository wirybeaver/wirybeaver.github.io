---
layout:     post
title:      WhatsApp
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - System Design
---

### Use Case
Two People Chat
Group Chat
Check online status

### Assumption
DAU: 100M user
Write QPS: 100M \* 20 / 86400 = 20K
Peak QPS: 5\*20K = 100K

### High Level Architect
Application Tier: Message Service, Real-time Service, Online Status Monitor Service

### Schema
```
message:
    message_id
    thread_id (row_key)
    created_time (column_key)
    String content
    sender_id
    
p2p_thread_info:
    thread_id
    user_id_small_large (big int)
    update_time
    
p2p_info:
    self_id
    friend_id
    is_muted
    nickname

group_thread_public_info:
    thread_id (primary index)
    participants (text)
    update_time

group_thread_private_info:
    <thread_id, user_id> (primary index)
    is_muted

User:
    int user_id
    String password

```
### Business Logic

** list thread by the decreasing update time **
ThreadDB.listThread(user_id)

** go into a specific thread **
MessageDB.listMessage(thread_id)

** create a thread **
ThreadDB.insertThread(user1_id, user2_id)

** Send a message **
MessageDB.insertMessage(message)
ThreadDB.updateTime(message.create_time)
AsyncRealTimeServiceProvider.notify(message, to_user_id)
AsyncRealTimeServiceConsumer.consume()

### Scale out





