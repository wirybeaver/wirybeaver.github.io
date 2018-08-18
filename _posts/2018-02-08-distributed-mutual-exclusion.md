---
layout:     post
title:      Distributed Mutual Exclusion
date:       2018-02-08 20:00:00
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Note
    - Distributed System
---
Some assignment problems helping understand principles of some mutual exclusion algorithms.
<!--more-->
Details about two algorithms below can be refered from [slides](http://web.cse.ohio-state.edu/~agrawal.28/760/Slides/feb7.pdf) by Prof. Gagan.

# Lamport Algo.

FIFO: required<br>
Lamport Clock: not

**What's the problem if FIFO is not satisfied?**<br>
Some thread could enter into critical section at the same time.

![](https://i.imgur.com/IAViyC8.jpg)

# Ricart-Agrawala Algo.

FIFO: not required<br>
Lamport Clock: required

![](https://i.imgur.com/wuRqGVz.jpg)

**What happens if the system violates the rule of Lamport Clock?**<br>
The critical section could be accessed by different threads simultaneously.
