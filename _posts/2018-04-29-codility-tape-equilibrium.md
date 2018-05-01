---
layout: post
title: Codility Lessons - Tape Equilibrium
category: Algorithms
tags: Tape_Equilibrium
---

This problem kind of caught me off guard.

First of all, I needed to make sure that the boundaries are not added/subtracted redundantly.

And I had this mistaken thought that the candidate differences would be adjacent to each other. As a result, I checked for the 'mid-point' where the two candidates would be with the following condition: `leftSum > rightSum`. But this was obviously wrong considering the existence of negative numbers.

So I learned two things:
1. Be aware of boundary conditions.
2. Be aware of negatives.


```java
public int solution(int[] A) {
        long rightSum = 0;
        for(int i = 1; i < A.length; i++){
            rightSum += A[i];
        }

        long leftSum = A[0];
        long minDiff = Math.abs(leftSum - rightSum);
        for(int i = 1; i < A.length - 1; i++){
            leftSum += A[i];
            rightSum -= A[i];

            long currDiff = Math.abs(leftSum - rightSum);
            if(currDiff <= minDiff){
                minDiff = currDiff;
            }
        }
        return (int)minDiff;
    }
```
