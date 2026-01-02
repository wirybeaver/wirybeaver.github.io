---
layout:     post
title:      Distributed Mutual Exclusion
excerpt:    ""
author:     "Xuanyi"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Note
    - Distributed System
---

Details about two algorithms below can be refered from [slides](http://web.cse.ohio-state.edu/~agrawal.28/760/Slides/feb7.pdf) by Prof. Gagan.
# Lamport Algo.

FIFO: required<br>
Lamport Clock: not

**What's the problem if FIFO is not satisfied?**<br>
Some thread could enter into critical section at the same time.

<img src="https://i.imgur.com/IAViyC8.jpg" width="60%" border="5">

# Ricart-Agrawala Algo.

FIFO: not required<br>
Lamport Clock: required

<img src="https://i.imgur.com/wuRqGVz.jpg" width="60%" border="5">

**What happens if the system violates the rule of Lamport Clock?**<br>
The critical section could be accessed by different threads simultaneously.
