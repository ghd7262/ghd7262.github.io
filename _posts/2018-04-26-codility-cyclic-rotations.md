---
layout: post
title: Codility Lessons - Cyclic Rotation
category: Algorithms
tags:
---

My first approach was to do `newIndex = newIndex - size` in the if block. The lesson learned here is that simply subtracting (or adding) to an index is never a good idea. 

In problems that involve some sort of cyclic mechanism such as this, cases for more than one cycle need to be accounted for. Instead of using subtraction/addition, use division(`/` `%`)/multiplication.

```java
public int[] solution(int[] A, int K) {
        int size = A.length;
        int[] newArray = new int[size];

        for(int currentIndex = 0; currentIndex < size; currentIndex++){
            int newIndex = currentIndex + K;
            if(newIndex >= size){
                newIndex = newIndex % size;
            }
            newArray[newIndex] = A[currentIndex];
        }
        return newArray;
    }
```
