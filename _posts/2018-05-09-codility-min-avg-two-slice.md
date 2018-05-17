---
layout: post
title: Codility Lessons - Min Average Two Slice
category: Algorithms
tags:
---
[MinAvgTwoSlice](https://app.codility.com/programmers/lessons/5-prefix_sums/min_avg_two_slice/)

Although the problem is under the "Prefix Sum" lesson, I couldn't really come up with a solution using prefix sum.

Instead, I referred to this document by Min Tran Dao - [Proof for MinAvgTwoSlice](https://github.com/daotranminh/playground/blob/master/src/codibility/MinAvgTwoSlice/proof.pdf), and learned that all I need to do is loop through the given array and, at each index, calculate the averages of two elements and the averages of three elements (ie. `Avg(A[i], A[i + 1])` and `Avg(A[i], A[i + 1], A[i + 2])`), then compare the previous minimum average with these values.

```java
public int solution(int[] A) {
    if(A.length == 2){ //in case array is only has two elements
        return 0;
    }

    //starting values
    double firstTwo = (A[0] + A[1]) / 2.0;
    double firstThree = (A[0] + A[1] + A[2]) / 3.0;
    double minAverage = Math.min(firstTwo, firstThree);
    int startingPoint = 0;

    //compare each value (leave last two)
    for(int i = 1; i < A.length - 2; i++){
        double twoElementsAverage = (A[i] + A[i + 1]) / 2.0;
        double threeElementsAverage = (A[i] + A[i + 1] + A[i + 2]) / 3.0;
        double currentMin = Math.min(twoElementsAverage, threeElementsAverage);
        if(currentMin < minAverage){
            minAverage = currentMin;
            startingP = i;
        }
    }

    //compare with last two
    double lastTwo = (A[A.length - 2] + A[A.length - 1]) / 2.0;
    if(lastTwo < minAverage){
        return A.length - 2;
    }
    return startingPoint;
}
```
