---
layout: post
title: Codility Lessons - Frog Jump
category: Algorithms
tags:
---

[FrogJump](https://app.codility.com/programmers/lessons/3-time_complexity/frog_jmp/)

I needed to make sure to account for the extra jump when `quotient * D < distance` and be aware to use long type because the integers can range from 1 - 1,000,000,000.

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
