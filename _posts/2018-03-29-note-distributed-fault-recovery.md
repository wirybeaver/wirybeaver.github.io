---
layout:     post
title:      Distributed Fault Recovery
excerpt:    Prove the consistency of checkpoint algorithm by contradiction.
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
mathjax: true
tags:
    - Note
    - Distributed System
---

Please read [slides](http://web.cse.ohio-state.edu/~agrawal.28/760/Slides/mar28.pdf) on fault recovery alogrithm prepared by Prof. Gagan first.

**Claim:** Orphan messages are not acceptable by this algorihm.<br>
**Proof:** <br>
<img width="60%" src="https://i.imgur.com/SkYA9dr.jpg" border="5"/>

Assume an orphan message **M** exit between thread **Y** and **Z**. Without the loss of generality, consider **Z** as the sender and **Y** as the receiver. By definition of orphan message, **M** must be sent after $$t_3$$ and received before $$t_2$$. By the checkpoint algorithm, no message is allowed to sent at the interval between tentative point and commit point. Thus, **M** could only be sent after $$t_5$$. In other words, $$t_5 < t_6$$ and $$t_7 < t_2$$ .

By checkpoint algorithm, we could obtain the conclusion $$t_2 < t_4 < t_5$$. Revisit the relations at the end of above paragragh, the final time chain would be $$t_7 < t_6$$.

Contradiction: The sending time is larger than the receiving time! 
