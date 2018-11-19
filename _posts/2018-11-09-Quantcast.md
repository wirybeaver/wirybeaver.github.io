---
layout:     post
title:      Quantcast Interview
excerpt:    Quantcast interview problems
date:       2018-11-19 9:00:00
author:     "Shane"
header-img: "img/bg-mac.jpg"
catalog: true
tags:
    - Interview
---

## LFU
```java
class LFUCache {
    private int cap;
    private HashMap<Integer, Integer> valueMap = new HashMap<>();
    private HashMap<Integer, Node> nodeMap = new HashMap<>();
    private DoubleLinkedList dblist = new DoubleLinkedList();
    public LFUCache(int capacity) {
        cap = capacity;
    }
    
    public int get(int key) {
        if(!valueMap.containsKey(key)){
            return -1;
        }
        else{
            incFreq(key);
            return valueMap.get(key);
        }
    }
    
    public void put(int key, int value) {
        if(cap<=0){
            return;
        }
        if(valueMap.containsKey(key)){
            valueMap.put(key, value);
            incFreq(key);
        }
        else{
            valueMap.put(key, value);
            if(valueMap.size()>cap){
                removeOld();
            }
            addNewKey(key);
        }
    }
    
    private void addNewKey(int key){
        if(dblist.head.next.freq == 1){
            nodeMap.put(key, dblist.head.next);
            dblist.head.next.store.add(key);
        }
        else{
            Node fresh = new Node(1);
            fresh.store.add(key);
            nodeMap.put(key, fresh);
            dblist.insert(dblist.head, fresh);
        }
    }
    
    private void removeOld(){
        Node least = dblist.head.next;
        if(least == dblist.tail){
            return;
        }
        int old = -1;
        for(int ky : least.store){
            old = ky;
            break;
        }
        valueMap.remove(old);
        nodeMap.remove(old);
        least.store.remove(old);
        if(least.store.isEmpty()){
            dblist.delete(least);
        }
    }
    
    private void incFreq(int key){
        Node node = nodeMap.get(key);
        node.store.remove(key);
        nodeMap.remove(key);
        if(node.freq+1 == node.next.freq){
            node.next.store.add(key);
            nodeMap.put(key, node.next);
        }
        else{
            Node fresh = new Node(node.freq+1);
            fresh.store.add(key);
            dblist.insert(node, fresh);
            nodeMap.put(key, fresh);
        }
        if(node.store.isEmpty()){
            dblist.delete(node);
        }
    }
    
    private class DoubleLinkedList{
        final Node head;
        final Node tail;
        DoubleLinkedList(){
            head = new Node(Integer.MIN_VALUE);
            tail = new Node(Integer.MAX_VALUE);
            head.next = tail;
            tail.prev = head;
        }
        
        void insert(Node pre, Node fresh){
            Node post = pre.next;
            fresh.next = post;
            post.prev = fresh;
            pre.next = fresh;
            fresh.prev = pre;
        }
        
        void delete(Node node){
            node.prev.next = node.next;
            node.next.prev = node.prev;
            node.next = node.prev = null;
        }
    }
    
    private class Node{
        int freq;
        Node prev;
        Node next;
        Set<Integer> store;
        Node(int freq){
            this.freq = freq;
            prev = next = null;
            store = new LinkedHashSet<>();
        }
    }
}
```
