---
layout: post
title: Codility Lessons - Genomic Range Query
category: Algorithms
tags:
---

In this problem, prefix sum is used to keep record of how many times A, C, G had appeared at each index since the start of the DNA sequence. Then, for each slice of the sequence, check whether A had appeared, then C, then G. If there was an occurrence(count > 0), assign the corresponding impact factor to a result array.

```java
public int[] solution(String S, int[] P, int[] Q) {
    int[][] prefixSum = new int[3][S.length() + 1]; //see next comment

    for(int i = 0; i < S.length(); i++){
        int a = 0, c = 0, g = 0;
        switch(S.charAt(i)){
            case 'A':
                a = 1;
            case 'C':
                c = 1;
            case 'G':
                g = 1;
        }
        //prefixSum[x][0] is equal to the initial value of 0 - hence the +1
        prefixSum[0][i + 1] = prefixSum[0][i] + a;
        prefixSum[1][i + 1] = prefixSum[1][i] + c;
        prefixSum[2][i + 1] = prefixSum[2][i] + g;
    }

    int[] minFactor = new int[P.length];
    for(int K = 0; K < P.length; K++){
        int from = P[K];
        int to = Q[K] + 1; //see previous comment

        if(prefixSum[0][to] - prefixSum[0][from] > 0){
            minFactor[K] = 1;
        } else if(prefixSum[1][to] - prefixSum[1][from] > 0){
            minFactor[K] = 2;
        } else if(prefixSum[2][to] - prefixSum[2][from] > 0){
            minFactor[K] = 3;
        } else{
            minFactor[K] = 4;
        }
    }

    return minFactor;
}
```
