---
layout: post
title: Codility Lessons - Frog Jump
category: Algorithms
tags: Frog_Jump
---

This problem was quite straight forward. Just needed to account for the extra jump when `quotient * D < distance` and be aware to use long type because the integers can range from 1 - 1,000,000,000.

```java
public int solution(int X, int Y, int D) {
        long distance = Math.abs(Y - X);
        long numberOfJumps = distance / D;

        if(numberOfJumps * D < distance){
            numberOfJumps += 1;
        }

        return (int)numberOfJumps;
    }
```
