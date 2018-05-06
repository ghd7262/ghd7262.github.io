---
layout: post
title: Codility Lessons - Passing Cars
category: Algorithms
tags:
---

The easiest solution would be to have nested loop to count how many 1's appear after each 0's, which would have a time complexity of `O(n^2)`. However, as the problem requires a time complexity of `O(n)`, only one level of loop is allowed to be used.

I figured that each count of 1's need to be multiplied by however many 0's had appeared previously before adding it to the total count:

```java
public int solution(int[] A) {
        long eastCount = 0;
        long count = 0;

        for(int i = 0; i < A.length; i++){
            if(A[i] == 0){
                eastCount += 1;
            }
            if(A[i] == 1){
                count += eastCount * 1;
            }
        }

        return count > 1000000000 ? -1 : (int)count;
    }
```
