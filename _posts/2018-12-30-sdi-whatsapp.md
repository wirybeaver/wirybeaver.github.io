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
DAU: 100M user <br>
Write QPS: 100M \* 20 / 86400 = 20K <br>
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
    
p2p_thread_public_info:
    thread_id
    user_id_small_large (big int, candidate key)
    update_time
    
p2p_thread_private_info:
    <user_id, thread_id> (primary key)
    is_blocked
    
group_thread_public_info:
    thread_id (primary index)
    participants (text, not necessarily unique)
    update_time

group_thread_private_info:
    <thread_id, user_id> (primary index)
    is_muted

User:
    int user_id
    String password

```

### Business Logic
```
// list thread by the decreasing update time
MsgService.getThreadList(int user_id){
    List<Thread> sorted1 = P2PDAO.getThreadList(user_id);
    List<Thread> sorted2 = GroupDAO.getThreadList(user_id);
    return mergetSortedList(sorted1, sorted2);
}

// dive into a specific thread, order by timestamp in sql
MsgService.getMessageList(int thread_id);

// create a thread
MsgService.createThread(int userId, List<Integer> otherIds){
    if(otherIds.size()==1){
        int otherIdx = otherIds.get(0);
        long[] tmp = {userId, otherId};
        Arrays.sort(tmp);
        long combineKey = (tmp[0] << 32) | tmp[1];
        if(P2PDAO.contains(combineKey)){return P2PDAO.getThread(combineKey);}
        else{return P2PDAO.insertThread(combineKey);}
    }
    else{
        otherIds.add(userId);
        return GroupDAO.insertThread(otherIds);
    }
}

// Send a message
MesssageDAO.insertMessage(message, threadId);
ThreadDAO.updateTime(message.create_time, message.isGroup);
```
### Scale out





