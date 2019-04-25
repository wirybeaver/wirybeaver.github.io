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
MsgService.createMessage(String message, int threadId){
    MesssageDAO.insertMessage(message, threadId);
    ThreadDAO.updateTime(threadId);
}

```
### Scale out
```
// server login, sharding user id
void WhatsApp.init(int userID){
    int serverip = RealtimeService.getPushServerIP(int userID);
    List<Integer> threadIDList = MsgService.getThreadIdList(userID);
    RealtimeService.insertNewUser(userID, threadIDList);
    
    // client sent its own userID in application layer, push server maitain a hash table: userID -> socket descriptor
    void handShake(serverip, userID);
}

void RealTimeService.getPushServerIP(int userID){
    int ip = consistantHash(userID);
}

void RealtimeService.insertNewUser(int userID, List<Integer> threadIDList){
    // distributed cache, sharding by userID
    cacheFacade.add(userID);
}

void RealTimeService.disPatch(String msg, userID, List<Integer> members){
    // note: exclude userID 
    List<Integer> aliveIDs = cacheFacade.getAlive(userID, members);
    
    // aggregate push server
    Map<Integer, List<Integer>> pushMap = new HashMap<>();
    for(int aid : aliveIDs){
        int pushIP = consistantHash(aid);
        // ignore null pointer exception here
        pushMap.get(pushIP).add(aid);
    }
    
    for(int ip : pushMap.keySet(ip)){
        RealTimeService.batchSend(msg, pushMap.get(ip));
    }
}

void WhatsApp.sendMsg(int threadID, String msg, int userID){
    MsgService.createMessage(msg, threadID);
    RealtimeSerice.disPatch(msg, userID, MsgService.getMemberIDList(threadID));
}

// Pull model to update online status. update through cacheFacade
```
