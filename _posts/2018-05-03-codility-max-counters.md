---
layout: post
title: Codility Lessons - Max Counters
category: Algorithms
tags:
---

This problem helped me refine my knowledge of time complexity. Time complexity is not defined by the number of nested `while` or `for` keyword, but rather, it is defined by how many times the operation has to be repeated. For example, on my first try:

```java
public int[] solution(int N, int[] A) {

        int[] counter = new int[N];

        int K = 0;
        int index = 0;
        int max = 0;
        while(K != A.length){
            int X = A[K];

            if(X >= 1 && X <= N){
                int count = counter[X - 1] + 1;
                counter[X - 1] = count;
                if(count > max){
                    max = count;
                }
                K++;
                continue;
            }

            if(X == N + 1){
                counter[index] = max;
                index++;
                if(index == counter.length){
                    index = 0;
                    K++;
                }
            }
        }

        return counter;
    }
```
Here the time complexity is O(NM) because the loop has to perform the operation for the length of A (N), and for each loop, there are as many as `index`(M) number of times the loop has to repeat the operation. On code it seems as though the loop depth is one. However, the program has to perform the operation for N*M number of times.

Then, I thought too hard and confused myself with the idea that max counter operations (A[K] == N + 1) before the very last one don't affect the final outcome since every counter is just going to be set to the maximum value at the occurrence of the last max counter operation (*which is wrong*):

```java
public int[] solution(int N, int[] A) {
    int[] counter = new int[N];
    int max = 0;
    int indexOfLatestMaxCounter = A.length - 1;
    boolean maxCounterExists;

    //get index of last max counter operation
    for(int K = A.length - 1; K >= 0; K--){
        if(A[K] == N + 1){
            indexOfLatestMaxCounter = K;
            maxCounterExists = true;
            break;
        }
    }

    //increment counters until the index of last max counter operation
    for(int K = 0; K <= indexOfLatestMaxCounter; K++){
        int X = A[K];
        if(X >= 1 && X <= N){
            counter[X - 1] += 1;
            if(counter[X - 1] > max){
                max = counter[X - 1];
            }
        }
    }


    if(maxCounterExists){
        //overwrite counters
        for(int i = 0; i < counter.length; i++){
            counter[i] = max;
        }
        //continue counter increment for the rest
        for(int K = indexOfLatestMaxCounter + 1; K < A.length; K++){
            int X = A[K];
            if(X >= 1 && X <= N){
                counter[X - 1] += 1;
            }
        }
    }

    return counter;
}
```
I realized this is wrong because it disregards the fact that previous max counter operations affect what the maximum value at another occurrence will be. For example, given (N = 3, A = [1,1,1,4,3,3,4]) the above function will return [3,3,3]) when it should return (5,5,5).

My third try, which is a *valid solution*, involves making sure each counter is up-to-date with the current max counter, and it updates the current max counter when a new max counter operation appears.

```java
public int[] solution(int N, int[] A) {
    int[] counter = new int[N];        
    int max = 0;
    int tempMax = 0;
    for (int K = 0; K < A.length; K++) {
        int X = A[K];
        if (1 <= X && X <= N) {
            if (counter[X-1] < max) { //if not up-to-date with current max
                counter[X-1] = max + 1;
            } else { //is up-to-date: simply increment
                counter[X-1]++;
            }

            if(counter[X-1] > tempMax) { //keep temporary max in case another max counter operation appears
                tempMax = counter[X-1];
            }  
        }
        if(X == N + 1){ //update max counter if new max counter operation appears
            max = tempMax;    
        }
    }

    for (int i = 0; i < counter.length; i++) {
        if (counter[i] < max) {
            counter[i] = max;
        }
    }
    return counter;
}
```
