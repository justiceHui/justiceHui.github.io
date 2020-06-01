---
layout: page
title: BOJ-Guide
---
### 목차
1. 탐색 / 정렬
2. 기초 자료구조
3. 기초 수학(소수, gcd)
4. 완전탐색(백트래킹, 비트마스크)
5. DFS, BFS
6. 기초 DP
7. 기초 그리디
8. 파라메트릭 서치
9. 기초 분할정복
10. dp
11. 유니온 파인드 / MST
12. 위상정렬
13. 최단경로

### 탐색 / 정렬
* 공부
  * O(N<sup>2</sup>)정렬은 구현만 하자.
  * O(NlgN)정렬도 구현만 하자.
  * std::sort의 사용법을 잘 익히자.(compare함수, 연산자 오버로딩 등)
  * 이진탐색을 잘 구현하자.
  * std::lower_bound, std::upper_bound 사용법을 익히자.
* 문제
  * [수 정렬하기](https://www.acmicpc.net/problem/2750) - O(N<sup>2</sup>)정렬
  * [수 정렬하기2](https://www.acmicpc.net/problem/2751) - O(NlgN)정렬
  * [수 찾기](https://www.acmicpc.net/problem/1920) - 이진탐색 구현
  * [좌표 정렬하기](https://www.acmicpc.net/problem/11650) - std::sort연습, pair형
  * [좌표 정렬하기2](https://www.acmicpc.net/problem/11651)
  * [숫자 카드](https://www.acmicpc.net/problem/10816) - lower_bound, upper_bound
  * [두 용액](https://www.acmicpc.net/problem/2470)
  * [합이 0인 네 정수](https://www.acmicpc.net/problem/7453) - 도전 문제

### 기초 자료구조
* 공부
  * 연결리스트, 스택, 큐, 덱 작동 방식과 구현 방법을 익히자.
  * 각 자료구조 별 STL 사용법을 익히자.
  * 잘 활용하자.
* 문제
  * [스택](https://www.acmicpc.net/problem/10828) - 구현
  * [큐](https://www.acmicpc.net/problem/10845)
  * [덱](https://www.acmicpc.net/problem/10866)
  * [에디터](https://www.acmicpc.net/problem/1406) - 리스트 문제, 스택 2개 사용해서 풀 수도 있다.
  * [조세퍼스 문제](https://www.acmicpc.net/problem/1158) - 리스트 문제, 큐를 사용해서 풀 수도 있다.
  * [괄호](https://www.acmicpc.net/problem/9012) - 유명한 스택 문제
  * [쇠막대기](https://www.acmicpc.net/problem/10799) - 스택 문제
  * [카드1](https://www.acmicpc.net/problem/2161) - 큐 문제
  * [카드2](https://www.acmicpc.net/problem/2164) - 큐 문제
  * [옥상 정원 꾸미기](https://www.acmicpc.net/problem/6198) - 도전 문제

### 기초 수학
* 공부
  * O(√N) 소수 판별법
  * 에라토스테네스의 체
  * 유클리드 호제법
* 문제
  * [소수 찾기](https://www.acmicpc.net/problem/1978) - 소수 판별
  * [소수 구하기](https://www.acmicpc.net/problem/1929) - 에라토스
  * [최대공약수와 최소공배수](https://www.acmicpc.net/problem/2609) - GCD
  * [링](https://www.acmicpc.net/problem/3036) - GCD
  * [골드바흐의 추측](https://www.acmicpc.net/problem/6588) - 소수
  * [소인수분해](https://www.acmicpc.net/problem/11653)
  * [서로소](https://www.acmicpc.net/problem/9359) - 도전 문제

### 완전 탐색
* 공부
  * 재귀함수를 이용해 모든 경우를 탐색 해보자.
  * next_permutation, prev_permutation 사용법을 알아보자.
  * 비트마스크 기법을 활용해 모든 경우를 탐색 해보자.
* 문제
  * [n과 m시리즈](https://www.acmicpc.net/workbook/view/2052) - 재귀함수 연습용 문제
  * [N-Queen](https://www.acmicpc.net/problem/9663) - 매우 유명한 백트래킹 문제
  * [연산자 끼워넣기](https://www.acmicpc.net/problem/14888) - 재귀함수로 풀어보자.
  * [로또](https://www.acmicpc.net/problem/6603)
  * [부분수열의 합](https://www.acmicpc.net/problem/1182) - 비트마스크를 이용하면 간단하게 짤 수 있다.
  * [스도쿠](https://www.acmicpc.net/problem/2580) - 가지치기가 많이 필요하다.
  * [괄호 제거](https://www.acmicpc.net/problem/2800) - 스택 + 비트마스크
  * [부분수열의 합2](https://www.acmicpc.net/problem/1208) - 도전 문제

### DFS, BFS
* 공부
  * 그래프 용어
  * 그래프의 표현 방법
  * DFS와 BFS를 활용하여 다양한 문제를 해결해보자.
  * 모든 간선의 가중치가 1인 그래프에서는 BFS를 이용해 최단 거리를 구할 수 있다.
* 문제
  * [DFS와 BFS](https://www.acmicpc.net/problem/1260) - DFS와 BFS를 구현하자.
  * [미로 탐색](https://www.acmicpc.net/problem/2178) - 2차원 격자를 그래프로 모델링하자. (dx, dy와 같은 방향 데이터를 활용해보자.)
  * [단지번호붙이기](https://www.acmicpc.net/problem/2667)
  * [치즈](https://www.acmicpc.net/problem/2636)
  * [토마토](https://www.acmicpc.net/problem/7569) - 3차원 격자를 그래프로 모델링하자. (dx, dy, dz)
  * [숨바꼭질](https://www.acmicpc.net/problem/1697) - 각 상황(상태)를 정점으로, 다른 상황(상태)로 이동하는 것을 간선으로 잡고 모델링하자.
  * [Count Circle Groups](https://www.acmicpc.net/problem/10216) - 도전 문제(그래프 모델링 연습)

### 기초 DP
* 공부
  * DP가 뭔지, 어떤 조건을 만족해야 DP로 풀 수 있는지 알아보자.
  * 아주 간단한 DP문제를 풀어보자.
* 문제
  * [피보나치 수2](https://www.acmicpc.net/problem/2748) - 유명한 문제
  * [피보나치 함수](https://www.acmicpc.net/problem/1003) - 손으로 계산하다 보면 어디서 많이 본 수열이?
  * [2xn 타일링](https://www.acmicpc.net/problem/11726) - 손으로 계산하다 보면 어디서 많이 본 수열이?
  * [2xn 타일링](https://www.acmicpc.net/problem/11727)
  * [1로 만들기](https://www.acmicpc.net/problem/1463) - 쉽다.
  * [쉬운 계단 수](https://www.acmicpc.net/problem/10844) - [길이][뒷 자리]
  * [이친수](https://www.acmicpc.net/problem/2193)
  * [파도반 수열](https://www.acmicpc.net/problem/9461) - 손으로 계산하면 규칙이 보입니다.
  * [동전 1](https://www.acmicpc.net/problem/2293) - 유명한 문제
  * [가장 긴 증가하는 부분 수열](https://www.acmicpc.net/problem/11053) - 유명한 문제
  * [LCS](https://www.acmicpc.net/problem/9251) - 검색을 해봅시다.
  * [LCS2](https://www.acmicpc.net/problem/9252) - 도전 문제(LCS 역추적)

### 기초 그리디
* 공부
  * 그리디 문제를 풀어보자.
* 문제
  * [동전 0](https://www.acmicpc.net/problem/11047) - 직접 동전으로 해보자.
  * [회의실배정](https://www.acmicpc.net/problem/1931) - 일찍 끝나는 작업?
  * [로프](https://www.acmicpc.net/problem/2217)
  * [개미](https://www.acmicpc.net/problem/4307) - 발상의 전환

### 파라메트릭 서치
* 공부
  * 이분 탐색, 삼분 탐색
  * 가능한 최대를 구하여라 -&gt; 이 값이 정답이 될 수 있는지 판단해라
* 문제
  * [예산](https://www.acmicpc.net/problem/2512) - 상한을 x로 잡았을 때 배정이 가능한지 구해보자.
  * [나무 자르기](https://www.acmicpc.net/problem/2805)
  * [차이를 최소로](https://www.acmicpc.net/problem/3090) - 그리디 + 이분탐색
  * [합이 0인 네 정수](https://www.acmicpc.net/problem/7453) - 두 배열을 하나로 합쳐볼까?
  * [카누](https://www.acmicpc.net/problem/9007)
  * [세 용액](https://www.acmicpc.net/problem/2473)
  * [전봇대](https://www.acmicpc.net/problem/8986) - 삼분 탐색
  * [XCorr](https://www.acmicpc.net/problem/15976) - 도전 문제

### 기초 분할정복
* 공부
  * 분할정복이란?
  * 문제를 적당히 반 정도로 나눠보자.
* 문제
  * [종이의 개수](https://www.acmicpc.net/problem/1780) - 종이를 3*3으로 나눠서 보자.
  * [Z](https://www.acmicpc.net/problem/1074) - 문제를 2*2로 나눠서 보자.
  * [쿼드 트리](https://www.acmicpc.net/problem/1992)
  * [곱셈](https://www.acmicpc.net/problem/1629) - (a^b)%c를 구하는 문제
  * [행렬 제곱](https://www.acmicpc.net/problem/10830) - (X^b)%c를 구하는 문제. 단, X는 행렬

### DP
* 공부
  * 조금 더 어렵고, 재밌고, 다양한 문제를 풀어보자.
* 문제
  * [이항 계수2](https://www.acmicpc.net/problem/11051) - <sub>n</sub>C<sub>r</sub> = <sub>n-1</sub>C<sub>r</sub> + <sub>n-1</sub>C<sub>r-1</sub>
  * [격자상의 경로](https://www.acmicpc.net/problem/10164) - 초6 수학 문제
  * [소형 기관차](https://www.acmicpc.net/problem/2616) - 기관차 i개, 객실 j개
  * [행렬 곱셈 순서](https://www.acmicpc.net/problem/11049) - 유명한 문제
  * [파일 합치기](https://www.acmicpc.net/problem/11066) - 행렬 곱셈 순서와 유사한 문제
  * [LCS3](https://www.acmicpc.net/problem/1958) - 3개의 문자열에 대해 LCS 구하기
  * [정수 삼각형](https://www.acmicpc.net/problem/1932)

### 유니온 파인드 / MST
* 공부
  * 유니온 파인드와 유니온 파인드 최적화 방법을 알아보자.
  * 최소 신장 트리를 구해보자.
* 문제
  * [집합의 표현](https://www.acmicpc.net/problem/1717) - 유니온 파인드 구현
  * [트리](/koi/2018/11/01/BOJ13306/) - 발상의 전환
  * [문명](/koi/2018/12/16/BOJ14868/) - BFS + 유니온 파인드
  * [최소 스패닝 트리](https://www.acmicpc.net/problem/1197) - MST 구현
  * [별자리 만들기](https://www.acmicpc.net/problem/4386)
  * [레드 블루 스패닝 트리](/ps/2019/02/19/BOJ4792/) - 수학적 직관이 있으면 편할 수도?
  * [정복자](/university/2019/05/16/BOJ14950/) - t가 거슬린다.
  * [두더지가 정보섬에 올라온 이유](https://www.acmicpc.net/problem/17132) - 도전 문제

###
