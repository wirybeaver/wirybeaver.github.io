---
layout:     post
title:      Restaurant
date:       2018-12-29 13:00:00
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
tags:
    - OOD
---

> General booking use case
search, select, (modify), cancel

search criteria -> Search() -> List<Result> -> Select() -> Receipt

### Use case

reserve tables

find table

add meals into the order

cook order

checkout

### Clarify

variant table size, no two parities share the same table at the same time

No dine-out or delivery

No happy hour

Search Criteria: parity size, time interval (start time, end time)

No table preference, skip the selection process, go right into the confirmation process

No deposit for reservation

Edge Case 1: parity size > the maximum size of available tables, throw an exception, let customers split their parity size.

Edge Case 2: cu

### Class

```java
class Parity{
    int size;
}

class SearchRequest{
    TimeSlot TimeSlot;
}

class TimeSlot{
    long start;
    long end;
}

class Reservation{
    Table t;
    Parity p;
    int start;
    int end;
}

class Restaurant{
    List<Table> table;
    List<MealExample> mealExamples;
    Map<Table, TreeMap<Long, Long>> reserveMap;
    List<Order> orders;
    List<Reservation> reservations;

    Reservation book(Parity p, SearchRequest searchRequest);
    void confirm(Reservation r);
    void cancel(Reservation r);

    Order checkin(Reservation r);
    void addMeal(Order o, MealExample me, int copy);
    void cookOrder(Order o);
    float checkout(Order o);
}

class Order{
    Table t;
    Parity p;
    int startTime;
    List<Meal> meals;
    void addMeal(Meal m);
}

class Table{
    boolean occupied;
    void markOccupied();
    void unmarkOccupied();
}

class MealExample{
    private float price;
    private String mealName;
}

class Meal{
    private MealExample MealExample;
    private boolean isCooked;
    private int copy;
    cook();
}

class LargeParityException extends RuntimeException;
class NoIdleTableException extends RuntimeException;
```






