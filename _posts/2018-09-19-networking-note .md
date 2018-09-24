---
layout:     post
title:      Data Mining Note
excerpt:    Details about my intern project
date:       2018-09-19 10:00:00
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Note
    - Networking
---
UDP structure:
1. data field. application data occupies the data field of the UDP segment.
2. src and des port nume: demultiplexing, the receiver pass data to the correct process running on the destination end system.
3. checksum. error detection, whether bits has been altered as it moves from src to des. Cause: Noise in the links or stored in the router. one's complement of the sum of all 16-bit word, with any overflow being around.

