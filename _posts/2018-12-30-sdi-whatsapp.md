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
    
thread_public_info:
    thread_id (primary index)
    participants (text, not necessarily unique)
    update_time
    
thread_private_info:
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
    List<Integer> tmp = ThreadPrivateDAO.getThreadIdList(user_id);
    List<Thread> ans = ThreadPublicDAO.getThreadList(tmp);
    // sort by timestamp
    Collections.sort(ans, ...);
}

// dive into a specific thread, order by timestamp in sql
MsgService.getMessageList(int thread_id);

// create a thread
MsgService.createThread(int userId, List<Integer> otherIds){
    if(otherIds.size()==1){
        int otherIdx = otherIds.get(0);
        int[] tmp = {userId, otherId};
        Arrays.sort(tmp);
        String combineKey = tmp[0]+""+tmp[1];
        if(ThreadDAO.contains(combineKey)){return ThreadDAO.getThread(combineKey);}
        else{return ThreadDAO.insertThread(combineKey);}
    }
    else{
        otherIds.add(userId);
        return ThreadDAO.insertThread(otherIds);
    }
}

// Send a message
MsgService.create
MesssageDAO.insertMessage(message, threadId);
ThreadDAO.updateTime(message.create_time, message.isGroup);
```
### Scale out





