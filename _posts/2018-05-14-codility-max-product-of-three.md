---
layout: post
title: Codility Lessons - Max Product Of Three
category: Algorithms
tags:
---

[MaxProductOfThree](https://app.codility.com/programmers/lessons/6-sorting/max_product_of_three/)

In this problem, I implemented a merge-sort algorithm. Then, I realized that there are two possible candidate answers:

1. Number produced by multiplying the two largest negative numbers together with the largest positive number.
2. Number produced by multiplying the three largest positive numbers.


```java
public int solution(int[] A) {
    A = mergeSort(A);
    int last = A.length - 1;

    int candidate1 = A[0] * A[1] * A[last];
    int candidate2 = A[last] * A[last - 1] * A[last - 2];

    return candidate1 > candidate2 ? candidate1 : candidate2;
}

public int[] mergeSort(int[] A){
    int len = A.length;
    if(len < 2){
        return A;
    }

    int midPoint = A.length / 2;
    int[] left = new int[midPoint];
    int[] right = new int[len - midPoint];
    for(int i = 0; i < midPoint; i++){
        left[i] = A[i];
    }
    for(int j = midPoint; j < len; j++){
        right[j - midPoint] = A[j];
    }

    mergeSort(left);
    mergeSort(right);
    return merge(A, left, right);
}

public int[] merge(int[] A, int[] left, int[] right){
    int i = 0, j = 0, k =0;
    while(i < left.length && j < right.length){
        if(left[i] < right[j]){
            A[k] = left[i];
            i += 1;
        } else {
            A[k] = right[j];
            j += 1;
        }
        k += 1;
    }
    while(i < left.length){
        A[k] = left[i];
        i += 1;
        k += 1;
    }
    while(j < right.length){
        A[k] = right[j];
        j += 1;
        k += 1;
    }
    return A;
}
```
