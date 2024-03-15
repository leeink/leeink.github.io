---
title: HackerRank Number Line Jumps
date: 2024-03-09 20:10:00 +09:00
categories: [ HackerRank ]
tags: [ HackerRank, Algorithm, Math, Programming ]
---

# Number Line Jumps
두 마리의 캥거루가 있고 둘 다 시점, 뛰는 간격이 입력으로 주어진다.
두 마리 중 한 마리가 나머지 한 마리를 따라 잡을 수 있냐 없냐를 판단 하는 문제다.

> 입력: 캥거루 A의 시점, 뛰는 간격 x1, v1. 캥거루 B의 시점, 뛰는 간격 x2, v2

C++로 문제풀이를 진행하겠다.

## 1. 먼저 안되는 케이스 없애기
캥거루 A가 0지점 캥거루 B가 2지점에 있을 때 캥거루 B의 뛰는 간격이
캥거루 A의 뛰는 간격보다 크거나 같을 때 따라잡을 수 없다.
다음과 같이 코드화 할 수 있다.

```cpp
if ((x2 > x1 && v2 >= v1) || (x1 > x2 && v1 >= v2))     
        return "NO";
```

## 2. 등차수열
캥거루가 뛰는 모습이 마치 등차수열과 같다. 매번 같은 간격으로 뛰고 맨 처음
시작지점이 있는 것이 그렇다. 다음과 같이 정리할 수 있겠다.

```cpp
//  캥거루 A의 일반항: x1 + n * v1
//  캥거루 B의 일반항: x2 + n * v2
```
계산을 편하게 하기 위해 n = 0일 때를 초항으로 한다.
n을 구하려면 두 일반항이 같을 때를 계산하면 된다.

```cpp
x1 - x2 = n * (v2 - v1)
n = (x1 - x2) / (v2 - v1)
```

(x1 - x2) % (v2 - v1)의 값이 0이라면 n의 값이 정수로 존재한다는 것이니
두 캥거루가 만날 수 있다는 것이다.

## 3. 마무리
내가 제출한 답안 코드는 이렇다.

```cpp
string kangaroo(int x1, int v1, int x2, int v2) {
    if ((x2 > x1 && v2 >= v1) || (x1 > x2 && v1 >= v2))     
        return "NO";
    else if ((x1 - x2) % (v2 - v1) == 0) 
        return "YES";
    else 
        return "NO";
}
```
