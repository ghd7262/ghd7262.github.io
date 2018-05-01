---
layout: post
title: Codility Lessons - Cyclic Rotation
category: Algorithms
tags: Cyclic_Rotation
---

Despite the fact that this is a super easy problem, I got it wrong in my first try.
My first approach was to do `newIndex = newIndex - size` in the if block. The lesson learned here is that simply subtracting (or adding) to an index is never a good idea. It's something very very basic, and I'm glad I got this problem wrong. In problems that involve some sort of cyclic mechanism such as this, cases for more than one cycle need to be accounted for. Instead of using subtraction/addition, use division(`/` `%`)/multiplication.

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
