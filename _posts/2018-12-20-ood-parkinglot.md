---
layout:     post
title:      Parking Lot
date:       2018-12-20 23:00:00
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
tags:
    - OOD
---

### Clarify
- What.
    - parking lot: multi-level
    - vehicle: multiple size
    - parking spot: same size
- How
    - parking strategy: large size vehicle could occupy consequent parking spots in horizontal direction.
    - charging strategy: by time

### Core object
- Bus,Car, Motorcyle
- Parking Lot
- Level
- Parking Spot

### Use case
- get available count
- park vehicle
    - check vehicle size
    - get list of available spots
    - vehicle take the spot
- clear spot
    - update spot
    - update level's avaliable count
- calculate price

### Class
- ParkingLot
    - private List<Level>
    - public int getAvailableCount()
- Level
    - private List<Spot>
    - private int availableCount
    - public int getAvailableCount()
- Spot
    - boolean isAvailable
    - boolean isAvailable()
    - void takeSpot()
    - void leaveSpot()
- Vehicle
    - protected int size
    - public int getSize()






