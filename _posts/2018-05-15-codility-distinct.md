---
layout: post
title: Codility Lessons - Distinct
category: Algorithms
tags:
---

[Distinct](https://app.codility.com/programmers/lessons/6-sorting/distinct/)

I've tried solving the problem without using any Java APIs, but I couldn't quite come up with a solution, which I think would probably involve quick sort.

With the use of an API, the problem becomes so simple as this (which has time complexity O(1)):

```java
public int solution(int[] A) {
        HashSet<Integer> distinct = new HashSet();
        for(int number : A){
            distinct.add(number);
        }

        return distinct.size();
    }
```
