---
layout:     post
title:      Distributed Mutual Exclusion
excerpt:    Partial details about my intern project
date:       2018-02-08 20:00:00
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Note
    - Distributed System
---

**Mlab** is a platform served for business developers who are not familiar with machine learning algorithms. This platfrom tries to abstract a general machine learning development process and automate all steps, including sample aggregation, feature preprocessing, offline training, online deployment, real-time computing, etc. I am responsible for offline training while assisting in dynmaic sql generation and online deployment. 

- Our platform is accessed by Taobao seller union activity whose DAU is 10 million, shorterning development cycle to 1 week.

- Adopted three design patterns, namely Builder, Abstract Factory (done by Java Enumeration) and Template Method, to work out algorithm template node diversity, mainly due to different parameter SERDER (serialization and deserialization) and frontend form exhibition.

- Implemented exclusion strategy in Gson with Java annotation for self-defined SERDER.

- Assembled parameters of all nodes belong to a certain algorithm template and generated a http post request by Apache http client to trigger training on PAI (Alibaba offline training Platform).

- Assisted in developing the model transfer module: Mlab server swarm read offline model from PAI, persist it to MySQL, cache customized online model to Tair and push the corresponding version object, containing extra split ratio for A/B testing, to Diamond Server. Client threads asynchronously listen to the version change and poll the latest model from Tair for real time prediction.

*Framework Sketch*
<img src="https://i.imgur.com/ZDMwwOM.jpg" width="60%">

*Sketch about Dynamic SQL Generation*
<img src="https://i.imgur.com/zfIk8hX.jpg" width="60%">

*UML on Offline Training*
<img src="https://i.imgur.com/BIWfT81.jpg" width="60%">

*Sequence Diagram of Online Deployment*
<img src="https://i.imgur.com/ORue9sO.jpg" width="60%">