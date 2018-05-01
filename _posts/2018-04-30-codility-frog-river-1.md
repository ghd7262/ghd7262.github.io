---
layout: post
title: Codility Lessons - Frog River 1
category: Algorithms
tags: Frog_River
---

To find out whether all positions were 'filled' for each K, a solution with O(n) time complexity (ie. without using doubly nested loops) required some way to keep track of:
1. Whether *each* number had already appeared -- hence the boolean array to map position to boolean value.
2. Whether *all* numbers had appeared -- hence the `requiredSum` to see if all positions are filled.

```java
public int solution(int X, int[] A) {
        int requiredSum = 0;
        for(int number = 1; number <= X; number++){
            requiredSum += number;
        }

        boolean[] leaves = new boolean[X];
        for(int K = 0; K < A.length; K++){
            if(leaves[A[K] - 1] == true){ //duplicate check
                continue;
            }

            if(A[K] > 0 && A[K] <= X){ //filter value < 0 and value > X
                leaves[A[K] - 1] = true;
                requiredSum -= A[K];
            }

            if(requiredSum == 0){
                return K;
            }
        }
        return -1;
    }
```
