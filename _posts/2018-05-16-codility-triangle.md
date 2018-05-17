---
layout: post
title: Codility Lessons - Triangle
category: Algorithms
tags:
---

[Codility - Triangle](https://app.codility.com/programmers/lessons/6-sorting/triangle/)

I had to first realize that not all combinations of the points in the given array need to be checked (which is obvious from the fact that the problem requires O(N*logN) time complexity). This is true because if you try the smallest three values and they do not form a triangle ie. `a + b > c`, then substituting in `c` for a bigger value obviously won't work, and substituting in `b` for a bigger value than `c` will make `a + c > b` false. So, I only need to try the first set of three smallest values, then move on to another set:
- given `A = {a, b, c, d}`
- check `a + b > c`, if false:
  - check `b + c > d`

```java
public int solution(int[] A) {
    //check if array has a length of at least 3
    if(A.length < 3){
        return 0;
    }
    //sort array in ascending order
    Arrays.sort(A);

    for(int i = 0; i < A.length - 2; i++){
        //upcast to long type in case A[x] is out of int type range
        long a = A[i];
        long b = A[i + 1];
        long c = A[i + 2];
        if(a + b > c){
            return 1;
        }
    }

    return 0;
}
```
