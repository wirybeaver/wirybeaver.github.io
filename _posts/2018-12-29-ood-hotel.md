---
layout:     post
title:      Hotel
date:       2018-12-29 16:00:00
author:     "Shane"
excerpt:    ""
header-img: "img/bg-mac.jpg"
tags:
    - OOD
---

### Use Case

book

pay

### Clarification

target: multiple hotels

if group size > single room size, the user choose multiple rooms by themselves

search criterion: date range, price range, no price fluctuation

multi payment method

### Class
```java

class SearchRequest{
    Location location;
    SearchCriteriaDate searchCriteriaDate;
    SearchCriteriaPrice searchCriteriaPrice;
}

class SearchCriteriaDate{
    Date startDate;
    Date endDate;
}

class SearchCriteriaPrice{
    float lowPrice;
    float highPrice;
}

class ReservationRequest{
    Map<RoomType, Integer> roomNeeds;
    SearchCriteriaDate date;
}

class Reservation{
    Hotel hotel;
    Date startDate;
    Date endDate;
    List<Room> rooms;
    float total price;
    User user;
}

class Location{
    String city;
}

class Hotel{
    List<Room> rooms;
    List<Reservation> reservations;
    Map<Room, TreeMap<Date, Date>> reservedMap;
    LRU<SearchCriteriaDate, List<Room>> searchCache;
    Location location;

    public boolean neighborLocation(Location srcLocation);
    public Map<RoomType, Integer> handleSearchRequest(SearchRequest req);
    private List<Room> filterDate(SearchCriteriaDate searchCriteriaDate, List<Room> rooms);
    private List<Room> filterPrice(SearchCriteriaDate searchCriteriaPrice, List<Room> rooms);
    private Map<RoomType, Integer> transfer(List<Room> rooms);

    public Reservation askReservation(ReservationRequest reservationRequest, User user);
    public void confirmReservation(Reservation r);
    public void cancelReservation(Reservation r);

}

class BookingSystem{
    List<Hotel> hotels;
    PayMentStrategy payMentStrategy;

    public List<Hotel> findHotel(SearchRequest req);
    public Map<RoomType, Integer> findRooms(Hotel h, SearchRequest req);
    public Reservation askReservation(Hotel h, ReservationRequest reservationRequest, User user);
    public void confirmReservation(Reservation r){
        r.getHotel().confirmReservation(r);
    }
    public void cancelReservation(Reservation r);
}

enum RoomType{
    SINGLE(1, 1, 100);
    DOUBLE(2, 2, 190);

    private int id;
    private int capacity;
    private int price;

    public void setPrice(int p){
        price = p;
    }
}

class Room{
    int id;
    RoomType roomtype;
    boolean occupied;
}

class LackOfRoomException extends RuntimeException;

```






