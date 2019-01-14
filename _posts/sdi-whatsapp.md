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

Two People Chat
Group Chat
Check online

Address Book
Login/Logout
Multi Device

### Assumption
DAU: 100M user
Write QPS:

### High Level Architect
Application Tier: User Service, Contacts Service, Message Service, Real-time Service

### Schema
```
Message:
    message_id
    thread_id (row_key)
    created_time (column_key)
    String content
    sender_id

Thread:
    <user_id, thread_id> (primary index)
    participants
    update_time
    is_muted
    nickname

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

### Foot Note





