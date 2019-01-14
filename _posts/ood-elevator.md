---
layout:     post
title:      Elevator
date:       2018-12-22 09:00:00
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
tags:
    - OOD
---

```java
interface DispatchStrategy{
    Elevator selectElevator(List<Elevator> elevators, ExternalRequest request);
}


public enum Direction{
    UP, DOWN;
}

public enum status{
    UP, DOWN, IDLE;
}

class ExternalRequest{
    int level;
    Direction Direction;
}

class InternalRequest{
    int level;
}


class ElevatorSystem{
    List<Elevator> elevators;
    DispatchStrategy strategy;

    void setDispatchStrategy(DispatchStrategy strategy);
    void handleExternalRequest(ExternalRequest request){
        strategy.selectElevator(elevators, request).handleExternalRequest(request);
    }
    void run{
        while(true){
            for(Elevator e: elevators){
                e.makeDecision();
            }
            Thread.sleep(1000);
        }
    }
}

class Elevator{
    int level;
    Status status;
    boolean isDoorOpen;
    TreeSet<Integer> upstops;
    TreeSet<Integer> downstops;
    float weightLimit;

    void handleExternalRequest(ExternalRequest  request); 
    void handleInternalRequest(InternalRequest request);
    boolean isInternalRequestValid(InternalRequest request);
    boolean checkOverweight();
    private boolean needOpen(){
        return status==UP && upstops.contains(level) || status==DOWN && downstops.contains(level) || status==IDLE && upstops.contains(level) || status==IDLE && downstops.contains(level);
    }
    private void openDoor(){
        isDoorOpen = true;
        if(status==UP){
            upstops.remove(level);
        }
        else if(status == DOWN){
            downstops.remove(level);
        }
        else{
            upstops.remove(level);
            downstops.remove(level);
        }
    }
    private void closeDoor(){
        isDoorOpen = false;
    }
    
    private boolean checkOverweight();
    public void makeDecision(){
        if(isDoorOpen){
            if(checkOverweight()){
                throw new OverWeightException();
            }
            else{
                closeDoor();
            }
        }
        else if(needOpen()){
            openDoor();
        }
        else{
            if(status == UP){
                if(upstops.higherKey(level)!=null || downstops.higherKey(level)!=null){
                    level++;
                }
                else if(downstops.size()>0 || upstops.size()>0){
                    status = DOWN;
                    level--;
                }
                else{
                    status = IDLE;
                }
            }
            else if(status == IDLE){
                if(upstops.higherKey(level)!=null){
                    status = UP;
                }
                else if(downstops.lowerKey(level)!=null){
                    status = DOWN;
                }
                else if(upstops.size()>0){
                    status = DOWN;
                }
                else if(downstops.size()>0){
                    status = UP;
                }
            }
            else{
                // status == DOWN
            }
        }
    }
}

class InvalidInternalRequest extends RuntimeException{

}

```






