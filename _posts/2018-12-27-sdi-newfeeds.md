---
layout:     post
title:      New Feeds
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - System Design
---

### Use Case
Post a tweet </br>
get one's own timeline </br>
follow/unfollow </br>
like </br>

### Assumption
DAU: 150M
Read Query per user per day : 60
Ave Qps: 150M \* 60 / 86400 = 100k
Peak: 3\*100k = 300k
Read Qps: 3M
Read to Write Ratio: 10
Write Qps: 30k
Bandwidth: Write Qps\* content size = 30k * 200B = 6M (Not a bottleneck)
Ave followers: 20
news feed: top 50

### High Level Architect
Webtier -> Application Tier: user service (Login/Register, MySQL), Tweet service (Post a tweet, One's Timeline, News Feed, Cassandra), Media Service (Upload Photo/Video, S3), Friendship Service (Follow/Unfollow, NoSQL)

### Data Schema
```
User:
    int user_id
    String user_name
    String password
    boolean isStar

Tweet:
    int owner_id
    <long timestamp, int tweet_id> (Column key)
    String content

Friendship:
    int from_user_id
    int to_user_id

NewFeed:
    int user_id (Row key)
    <long timestamp, int tweet_id> (Column key)

```

### Business Logic

**TweetService**<br>
1. TweetService.getNewFeed(request)<br>
    return NewFeedDB.getNewFeed(request.user_id)
2. TweetService.postTweet(request)<br>
    tweet = TweetDB.insertTweet(request.user_id, request.tweetcontent)<br>
    FanoutAsyncProvider.notify(tweet)<br>
    return success
3. FanoutAsyncConsumer.consume(tweet)
    friend_id_list = FriendDB.getFriendList(tweet.owner_id)
    for each friend_id in friend_id_list:
        NewfeedDB.insertTweet(friend_id, tweet)

**FriendService**
1. FriendService.follow(request)<br>
    FriendDB.delete(request.from_id, request.to_id)
    FollowAsyncProvider.notify(request.from_id, request.to_id)
2. FollowAsyncProvider.consume(request.from_id, request.to_id)<br>
    timeline = TweetDB.getTimeLine(friend_id)
    for each tweet in timeline:
        NewFeedDB.insertTweet(request.from_id, tweet)


### Scale
Step 1: Optimize by dealing with special cases and adding more features<br>

Follow up1: when a movie star post a tweet, take multiple hours to fanout<br>
Solution 1 (recommend): add more push servers to parallelization
Solution 2 (combine pull model, cache aside): 
1. TweetService.getNewFeed(request)<br>
    tweet_id_list = NewFeedDB.getNewFeedList(request.user_id)
    for each friend_id in FriendDB.getFriendList(rquest.user_id):
        if UserService.isStar(friend_id):
            timeline = TweetDB.getOnesTimeline(friend_id)
            tweet_id_list.merge(timeline)
2. TweetService.postTweet(request)<br>
    tweet = TweetDB.insertTweet(request.user_id, request.tweetcontent)<br>
    if not UserService.isStar(request.user_id):
        FanoutAsyncProvider.notify(tweet)<br>
    return success
3. FanoutAsyncConsumer.consume(tweet)
    friend_id_list = FriendDB.getFriendList(tweet.owner_id)
    for each friend_id in friend_id_list:
        NewfeedDB.insertTweet(friend_id, tweet)

Follow up2: majority of movie star's followers unfollow the movie star <br>
Solution 1: movie star is not determined by follower numbers. The configuration of a movie star is processed by hand
Solution 2: the combined pull model could deal with this problem eventually

Follow up3: like. denormalization

```
Tweet:
    int owner_id
    <long timestamp, int tweet_id> (Column key)
    String content
    int likenum
    int retweetnum
    int commentnum    

Like:
    int id
    int user_id
    int tweet_id
```

Step2: Maintance

Robust: replia/sharding, master/slaver

Scalability: x-scaling, cache, facebook lease get for thundering herd phenomenon (dist mutex or "never time out with passivly updaing expired value")





