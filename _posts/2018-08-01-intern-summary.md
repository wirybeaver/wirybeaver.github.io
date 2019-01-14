---
layout:     post
title:      Intern Summary
excerpt:    Intern Project Introduction
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Other
---

**Mlab** is a business intelligence platform I did during my Alibaba intern, helping machine learning engineers to reduce the development cycle of AI product. We tried to constitute a general but flexible machine learning process and automate all inner steps consecutively, including data aggregation, feature preprocessing, offline training, online deployment, real time computing, etc. I implemented offline training module as well as assisting in dynamic sql generation and online model deployment. Finally, our platform is accessed by Taobao seller union activity whose DAU is 10 million, shorterning development cycle to 1 week. This awesome and challengeable intern let me know how to set up an enterprise platform and gives me a sense of fulfillment.

- Adopted three design patterns, namely Builder, Abstract Factory (done by Java Enumeration) and Template Method, to work out algorithm template node diversity, mainly due to different parameter SERDER (serialization and deserialization) and frontend form exhibition.

- Implemented exclusion strategy in Gson with Java annotation for self-defined SERDER.

- Assembled parameters of all nodes belong to a certain algorithm template and generated a http post request by Apache http client to trigger training on PAI (Alibaba offline training Platform).

- Assisted in developing the model transfer module: Mlab server swarm read offline model from PAI, persist it to MySQL, cache customized online model to Tair and push the corresponding version object, containing extra split ratio for A/B testing, to Diamond Server. Client threads asynchronously listen to the version change and poll the latest model from Tair for real time prediction.

*Framework Sketch*
<img src="https://i.imgur.com/ZDMwwOM.jpg" width="80%">

*Sketch about Dynamic SQL Generation*
<img src="https://i.imgur.com/zfIk8hX.jpg" width="80%">

*UML on Offline Training*
<img src="https://i.imgur.com/BIWfT81.jpg" width="80%">

*Sequence Diagram of Online Deployment*
<img src="https://i.imgur.com/ORue9sO.jpg" width="80%">
