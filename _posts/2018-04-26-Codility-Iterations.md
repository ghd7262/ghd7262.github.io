---
layout: post
title: Codility Lessons - Binary Gap
category: Algorithms
tags: Binary-Gap
---

Description:

어떤 숫자 N을 받았을 때 그 숫자의 binary representation에서 가장 긴 0의 연속의 길이를 찾는 문제.

예를 들면 10001001은 000이 가장 기니까 답은 3이여야 한다.

```java
class Solution {
    public int solution(int N) {
        String s = Integer.toBinaryString(N);
        long count = 0;
        long maxCount = 0;
        for(char c : s.toCharArray()){
            if(c == '0'){
                count++;
                continue;
            }
            if(count > maxCount){
                maxCount = count;
            }
            count = 0;
        }
        return (int)maxCount;
    }
}
```
