---
layout: post
title: Codility Lessons - Permutation Missing Element
category: Algorithms
tags:
---

[PermutationsMissingElement](https://app.codility.com/programmers/lessons/3-time_complexity/perm_missing_elem/)

I figured that the given array contains numbers that match their indexes (sort of like a hash map) except for the one element that is missing. So, all I have to do is to create a new array of length N + 1 and loop around the given array and assign each numbers to their corresponding(`number - 1`) indexes. Then, go through new array to find the index number in which `newArray[index] == 0` and return what number is missing ie.`index + 1`.

```java
public int solution(int[] A) {
        int[] appearedNumbers = new int[A.length + 1];

        for(int number : A){
            appearedNumbers[number - 1] = number;
        }

        for(int index = 0; index < appearedNumbers.length; index++){
            if(appearedNumbers[index] == 0){
                return index + 1;
            }
        }
        return 0;
    }
```
