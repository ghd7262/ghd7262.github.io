---
layout: post
title: Codility Lessons - Odd Occurrences In Array
category: Algorithms
tags: Odd_Occurrences_In_Array
---

First thought that came in my mind was to use a HashMap because counts of each element in the given array is required to find an element that appears an odd number of times.


```java
public int solution(int[] A) {
        HashMap<Integer, Integer> countsOfNumbers = new HashMap<>();
        for(int number : A){
            if(!countsOfNumbers.containsKey(number)){
               countsOfNumbers.put(number, 1);
               continue;
            }
            countsOfNumbers.put(number, countsOfNumbers.get(number) + 1);
        }
        for(int number : countsOfNumbers.keySet()){
            if(countsOfNumbers.get(number) % 2 == 1){
                return number;
            }
        }
        return 0;
    }
```

Although this solution's time complexity is O(n), its space complexity is O(n). I didn't like the fact that I used a Java api for what seems to be a simple problem. So I searched and found a way to solve the problem using XOR operation. But I think I'll dig into it later and move on for now.
