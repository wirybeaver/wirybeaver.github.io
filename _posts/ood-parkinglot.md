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

### Use Case
get avaliable count

park vehicle

unpark vehicle

charge fee

### Assumption

n layers, each layer have m rows, each row has k parking spots

each spot has the same size

various vechile type

parking strategy: motorcyle and car occupy 1 spot, bus occupy 5 sequential spots in the same row

charge fee is propotional to parking time

### Core object
- Bus,Car, Motorcyle extends Vehicle
- Parking Lot
- Level
- Parking Spot

### Class
```java
abstract class Vehicle{
    int size;
    public int getSize();
}

class Car extends Vehicle{
    Car(){
        super();
        this.size  = 1;
    }

    public int getSize(){
        return this.size;
    }
}

class Bus extends Vehicle{
    Bus(){
        super();
        this.size  = 5;
    }

    public int getSize(){
        return this.size;
    }
}

class Ticket{
    Vehicle vehicle;
    List<Spot> spots;
    int level;
    int row;
    long startTime;
}

class Level{
    List<List<Spot>> spots = new ArrayList();
    int count;
    int getAvailableSpots();
}

class Spot{
    boolean available;
    int col;
    public boolean isAvailable();
    public void markAvailable();
    public void markUnavailable();
}

class ParkingLot{
    List<Level> levels = new ArrayList<>();
    public int getAvailableSpots(){
        int sum = 0;
        for(Level level: levels){
            sum += level.getAvailableSpots();
        }
        return sum;
    }
    public Ticket park(Vehicle v) throws ParkingLotFullException{
        int l=0;
        for(int l=0; l<levels.size(); l++){
            Level level = levels.get(l);
            for(int r=0; r<level.size(); r++){
                List<Spot> row = level.get(r);
                int i=0; 
                while(i <= row.size()-v.getSize()){
                    int j=0;
                    while(j<v.getSize() && row.get(i+j)!=null && row.get(i+j).isAvailable()){
                        j++;
                    }
                    if(j==v.getSize()){
                        for(int k=0; k<j; k++){
                            row.get(i+k).markAvailable();
                        }
                        level.count -= v.getSize();
                        return new Ticket(v, row.subList(i, i+j), l, System.currentTimeMillis());
                    }
                    i += j+1;
                }
            }
        }
        throw new ParkingLotFullException("full");
    }
    public float unpark(Ticket t) throws InvalidTicketException;
    private void clearSpot(Ticket t);
    private float calculateFee(Ticket t);
}

```

### Optimize
Stratege Pattern for the park method()

Singleton Pattern for the object ParkingLot






