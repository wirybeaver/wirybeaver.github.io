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

take order

checkout

### Clarify

variant table size, no two parities share the same table at the same time

No dine-out or delivery

No happy hour

Search Criteria: parity size, time interval (start time, end time)

No table preference, skip the selection process, go right into the confirmation process

No deposit for reservation

Edge Case: parity size > the maximum size of available tables, throw an exception, let customers split their parity size.

### Class

```java
class Parity{
    int size;
}

class Reservation{
    Table t;
    Parity p;
    int start;
    int end;
}

class Restaurant{
    List<Table> table;
    List<Meal> meal;
    Map<Table, TreeMap<Integer, Integer>> reserveMap;
    List<Order> orders;

    Reservation book(Parity p, int start, int end);
    void confirm(Reservation r);
    void cancel(Reservation r);

    Order checkin(Reservation r);
    void addMeal(Order o, Meal m);
    void deleteMeal(Order o, Meal m);
    float checkout(Order o);
}

class Order{
    Table t;
    Parity p;
    int startTime;
    List<Meal> meals;

    void addMeal(Meal m);
    void deleteMeal(Meal, m);
}

class Table{
    boolean occupied;
    void markOccupied();
    void unmarkOccupied();
}

class Meal{
    float price;
}

class LargeParityException extends RuntimeException;
class NoIdleTableException extends RuntimeException;
```






