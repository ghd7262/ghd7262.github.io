---
layout: post
title: Codility Lessons - Permutation Check
category: Algorithms
tags:
---

[PermutationCheck](https://app.codility.com/programmers/lessons/4-counting_elements/perm_check/)

Had to remember to take into account any element value that is greater than max the index value of the array.

```java
public int solution(int[] A) {
        boolean[] permCheck = new boolean[A.length];
        for(int number : A){
            if(number - 1 > A.length - 1){ //check for number > max index
                return 0;
            }
            if(permCheck[number - 1] == true){ //check for duplicate
                return 0;
            }
            permCheck[number - 1] = true;
        }

        for(boolean exists : permCheck){
            if(exists == false){ //check for missing
                return 0;
            }
        }

        return 1;
    }
```
