---
layout: post
title: Codility Lessons - Count Div
category: Algorithms
tags:
---

The first thought that came to mind was to divide the end points A and B by K, then subtract the two to exclude the number divisions before A, and it worked out. Just had to remember to count the division `A/K` if it is divisible by K.

```java
public int solution(int A, int B, int K) {
        long count = (B/K) - (A/K);
        if(A % K == 0){
            return (int)count + 1;
        }

        return (int)count;
    }
```
