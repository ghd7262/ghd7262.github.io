---
layout: post
title: Codility Lessons - Missing Integer
category: Algorithms
tags: Missing_Integer
---

A relatively easy problem. Just had to use an array like a hashmap and find the first zero:

```java
public int solution(int[] A) {
        int max = 0;
        for(int number : A){
            if(number > max){
                max = number;
            }
        }

        int[] rangeOfNumbers = new int[max + 1];
        for(int i = 0; i < A.length; i++){
            if(A[i] < 0){
                continue;
            }
            rangeOfNumbers[A[i]] = A[i];
        }

        for(int i = 1; i < rangeOfNumbers.length; i++){
            if(rangeOfNumbers[i] == 0){
                return i;
            }
        }

        return max + 1;
    }
```
