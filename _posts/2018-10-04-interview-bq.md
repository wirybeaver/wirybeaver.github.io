---
layout:     post
title:      Behavioral Question
date:       2018-10-04 09:00:00
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Interview
---
https://crane-yuan.github.io/2016/08/15/The-map-of-java-sorted-by-value/

# impressive bug

# proudest pieces of code

# team leader experience

# hate project

# biggest failuer

# 3 key factors important for you in your job search?
Frist, fast team growth. A challengable job would stimulate me develop new skill sets and I could ignite productivity in turn. This is a win-win situation.

Besides, positive company culture. Coworks should respect and trust to each other. We are definitely encounter some problems that are hard to deal with by individuals. But with others' help, problems are likely done in a short span of time. As a result, we save time and learn new skills.

Last but not least, work life balance. Good health could help me restore energy, and thus improve the work effeciency.

# Your weakness
To be honest, my speaking English is not fluent. But I am trying to overcome it by following up some tutorials on youtube and making friends with native speaks. Although it is far away to be as fluent as a native speaker, I would never give up.

# What is your current workflow
My workflow is kind of agile development. It is an iterative and team based approach. Basicaly, time is split into phases called ‘sprint’. Typically the span of a sprint is one week. At the start of sprint, teammates would meet together and negotiate the running list of deliverables. All deliverables would be prioritized by business value. Once a functionality is done, I have to design a unit test and notify others to review my code. The system would automatically check the code style and report conflict with other branches. The feature cannot be merged into the production branch until all those stuff are done. If all features cannot be completed, work is reprioritized at the future sprint.
Pro: customer could early see the work and make decisions.
Drawback: refactoring.

# What conflict?
Yeah, I never have a major one but conflicts definitely occurs in some scenarios. Generally, I would meet with my teammate or boss, be a thoughtful listener to understand their concerns and provided my solution. If compromise cannot be reached, I would documented the conversion, include the item on which we didn’t agree and my solution. Finally, I would submit it to my boss.

I wanna share a conflict with my advisor during my summer research at Singapore. Well, my research focused on multi-task neural network for text classification. We were disagreed on what kind of neural networks should be introduced at shared layer for. At first, I preferred the RNN because it is very popular in the field of machine translation and it could capture the long dependency of words in sentences. But my advisor suggested me to use CNN. He said my task is to detect the sentiment polarity, and objectivity simultaneously. In other words, those tasks are classification problem and CNN fits classification problem. I thought his theory was kind of reasonable but was still specious about it. Thus I came back to set up an experiment to compare the performance. Finally, the result proved that the assumption of my advisor was true. I reported it to my advisor. He was happy for my critical thinking and suggested me to write the experiment result into my paper to make the conclusion more rigorous. So you see, the conflict is not always bad and sometime even brings a positive impact if you deal with it a right way.

# Self-introduction
I am a computer science master student and will graduate by May next year. My career goal is to become a software architect. This summber, I took a backend internship at Alibaba and built an internal platform to accelerate machine learning development. The framework we use are mainly Sprint Boot and some Alibaba internal middlewares. When I leave the team, the platform prototype has already empowered the coupon team to reduce one week for updating their algorithm.

My responsiblity is to connect the offline platform with other modules, like preprocessing and online deployment. I really enjoyed this internship and gained a lot knowledge like Java annotation, reflection, some elegant design patterns as well as the key concept of distributed systems. More than that, I understood the production workflow better. You know, I had to document, draw UML and sequence diagram, manipulate MySQL table, commit code with GitLab, wrote Unit test with JUnit and Mockito, collebrated with frontend engineers with PostMan. It gives me a sense of fulfillment. Besided, I finished some infrastructure projects with Java and Python, like the implementation of lisp interpreter, simple TCP protocol and simulating restaurant using Java multithread programming. In addition, I am interested in advanced techniques like neural network and published a paper about multi-task deep learning in IEEE when I am an undergraduate. In summary, I am a fast learner, a good team player.

# Project
## Sketch
I took a back-end internship at Alibaba this summer. I built an internal service to accelarate AI workflow with 4 engineers. The bankend frameworks we use are mainly SpringBoot and some Alibaba internal distributed middlewares. Before I leave the team, the service has already empowered a coupon business team to reduce one week for updating their recommendation algorithm. I learn a lot sernior programing skill and understood the production flow and teamwork responsibility better.

<!-- My team mission is to fully automate the workflow of AI product. The platform empowered the coupon team to reduce 1 week for developing recommendation functionality. In the team, I was responsible to connect offline training module with other platforms. Specially, I have to achieve parameters from the preprocessing step and algorithm model, trigger training and send the model to clients. I adopted many senior Java techniques (reflection, annotation) and design patterns (builder, template method, abstract function) to make the code succinct and extendable.

More than that, this internship developed me strong teamwork and I had closer understanding of production workflow, breaking a large task into manageable pieces and collaborating with the team to achieve those goals. I continuously interacted with front engineers to provide dynamic parameter forms, and implemented coordinated server end programs for the complex module of online model deployment. Sometimes I well documented and drew professional diagrams to interpret the code structure, the dependency of components to one another.
 -->
## back ground
Alibaba has great AI teams in good place. But it is challenging to translate the efforts into business outcome in a short span of time. Date engineer, data scientist and product developers use disjoint tools. It makes collaboration harder. My team mission is to connect different platforms, unify pipeline and thus fully automate the AI workflow. Our platform achived great performance. It empowered the coupon team to reduce 1 week for developing the recommendation functionality. In the team, I am responsible to bridge offline train module with other upstream and downstream module. Specially, I have to collect parameters of preprocessing and algorithm model, trigger training and send the model to clients. I adopted many senior techniques like design patterns and microservices to make the code succinct and extendable.

## Sprint Boot
IOC, DI.

## Microservice

## difficulty

## Why you like it. What your learned
### Production workflow
Document: UML, Sequence Digram

Aone: internal DevOps platform, integrating GitLab, Maven, Docker. <br>
Step 1: creat new feature branch in Aone, fill description. Aone would return back the git url.
Step 2: fix bug in PC, merge and push.
Step 3: Maven bundle files.
Step 4: deploy new Docker Image.

Unit Test<br>
- JUnit: for instance POJO CRUD. Annotation.
- Mockito: replace an object with a test double in order to verify some expectations, for example asserting that a certain method has been called.

```java
public class ResourceManagerImplTest {
    private CloudProvider provider;

    @Before
    public void init(){
        provider = mock(CloudProvider.class);
    }


    @Test
    public void testPlaceContainerCaseA(){
        // basic case: instanceSize is divisible by containerSize
        // sequential request
        double instanceSize = 2;
        double containerSize = 1;
        when(provider.requestInstance()).thenAnswer( prov -> new InstanceImpl(instanceSize));
        ResourceManager manager = new ResourceManagerImpl(provider, instanceSize, containerSize);
        for(int i=0; i<100; i++){
            manager.placeContainers(1);
        }
        verify(provider, times(50)).requestInstance();
    }
}
```

### Teamwork Responsibility
Integration Test with front-end engineer, postman.

### Senior Development Tech
Design Pattern:

Scale web service: Learn the theory and make a practice (RPC, Message broker, Config Management System, Cache)

### Gson

# learning style

# Why company

## General
- remove many pain points in XX
- smooth the user experience
- approach the problem of XX from a far different perspective
- created a fertile environment for programmers and developers in the process
- Coupled with my rich hands-on experience in Java based backend, I could grow fast with the team and ignite productivity.


## Thumbtack
Removes many pain points in hiring local professionals and changes the way life operates. Life seems like an non-endless todo list. Repair devices in your room, shoot a video for presentation, plan a wedding, a lot of stuff. Previously, when people wanna find professionals, they would often pay fees to consultants and let them to find fit candidates. But on thumbtack, people could hire professionals for almost anything and pay free. It’s a tremendous stride and I believe the perspective is promising.

## Databricks
Databricks created from Apache Spark and now is collabrating with Azure and AWS. I have watched videos about the production called unified analytics platform. It simplify data engineering, unifies people. Data engineers could build data pipeling unifying both stream and historical datas. Data scientists could build model using various frameworks in one place while businees analytists could consume dashboard in near real time. In other words, it accelerate AI and innovations. For me, it is really ground breaking and may change the way AI business operates. The prospect is a whole lot promising.

## Pinterest
Pinterest is building the visual searching engine. When people wanna get some ideas, they could use Pinterest to look up interesing photoes, click on them, move foraward the source website and then refer detailed materials. Such pattern is very appealing to me and I think it has advantages compared with the conventional text search engine like Google. In the past, when we wanna promote vague ideas, we type in some key words. But human idea is complicated and languages is limitted to explain the idea sufficiently just using a few words. Photos fit in here, since phone contains more information. When people see photoes, they comp up with ideas faster instead of seeing text. Therefore innovation could be accelerated. In short, Pinterest has huge potential and I believe Pinterest would be an essential tool in future daily life.

## Quantcast
Quantcast is an AI-driven company and leverages machine learning to enable other companies grow business. I know Quantcast has a CNN based product called Q for Publishers. It powers publishers to shape their user story, customize a fit model, thus grow revenue and smooth users' experience.

## Appfolio

Appfolio focused on providing software as a service for startups and middle sized company. The first product is a property management software for real estate business. It kinds of like an employee who never sleeps and takes care of a ton of management tasks, including the maitainance, payment, leasing, charging late fees. Liberate humanbeings and employees could focus on meaningful things.

