---
title: "Rotating Sweep Line Technique (Bulldozer Trick)"
date: 2022-03-30 23:59:00 +0900
categories:
- Hard-Algorithm
tags:
---

### 서론
문제를 많이 풀어본 분들이라면 데이터가 정렬되어 있다는 것이 얼마나 좋은 성질인지 잘 알고 계실 것입니다. 이진 탐색을 이용할 수도 있고, 모든 원소가 서로 다른 배열에서 $A_i$보다 작은 가장 큰 원소와 같은 질의를 단순히 $A_{i-1}$을 반환하는 것으로 해결할 수 있습니다.<br>
주어진 데이터가 정수와 같이 순서가 자명하게 정의되어 있다면 단순히 정렬하면 되지만, 2차원상의 점이라면 어떤 축을 기준으로 보느냐에 따라 정렬의 결과가 달라질 수 있습니다. 흔히 "불도저 트릭"이라고 불리는 이 테크닉은 서로 다른 정렬의 결과가 $O(N^2)$가지임을 보이고, 가능한 모든 정렬 결과를 $O(N^2 \log N)$에 순회하는 테크닉입니다.

아직 널리 쓰이는 이름이 없습니다. 제 주변에서 많이 사용하는 명칭 두 개를 임의로 선택해 제목으로 정한 점 양해 부탁드립니다.

### 정렬의 경우의 수
보통 점 $(x_i, y_i)$를 정렬하라고 하면 x좌표 오름차순으로 정렬합니다. 이 정렬 방법은 y축에 평행한 직선(기울기가 $\infty$인 직선)이 왼쪽부터 오른쪽으로 차례대로 이동하면서 만나는 점들에 순서대로 번호를 붙이는 것과 동일합니다. 마찬가지로, y좌표 내림차순으로 정렬하는 것은 x축에 평행한 직선(기울기가 $0$인 직선)이 한 방향으로 이동하면서 만나는 점들에 순서대로 번호를 붙이는 것입니다.<br>
그러므로 서로 다른 정렬의 결과가 $O(N^2)$가지임을 보이는 것은, 정렬 기준으로 유효한 기울기가 $O(N^2)$개밖에 없다는 것을 보이면 됩니다.

![](https://i.imgur.com/rCZ8FgL.png)

정렬 기준 기울기를 아주 미세하게 변화시키더라도 정렬 결과는 바뀌지 않는다는 것은 직관적으로 알 수 있습니다. 아래 그림에서 초록색 화살표는 빨간색 화살표를 시계방향으로 4도 회전시킨 것이고, 정렬 결과가 바뀌지 않은 것을 확인할 수 있습니다.

![](https://i.imgur.com/O7BjTSc.png)

시계방향으로 계속 회전시키다 보면 4번 점과 5번 점을 잇는 선분과 평행해지는 시점에 4번 점과 5번 점의 우선순위가 동등해집니다. 이 상황에서 반시계방향으로 아주 조금 돌리면 x좌표가 더 작은 점이 앞에 오고, 시계방향으로 아주 조금 돌리면 x좌표가 더 큰 점이 앞에 오게 됩니다. 즉, 4번 점과 5번 점을 잇는 선분의 기울기를 기점으로 정렬 결과가 달라집니다.<br>
그다음은 2번 점과 3번 점을 잇는 선분과 평행해지는 시점에 두 점의 순서가 바뀝니다.

![](https://i.imgur.com/FpuDkK7.png)

위 그림에서 알 수 있듯, 정렬 기준과 두 점을 잇는 선분과 평행해지는 시점에만 정렬 결과가 달라집니다. 또한 세 점이 한 직선 위에 존재하지 않는다면 항상 두 점의 순서만 바뀌고, 순서가 바뀌는 두 점은 정렬 순서상에서 인접합니다. 한 점을 기준으로 여러 기울기를 그렸을 때 자신보다 앞에 있는 점 집합과 뒤에 있는 점 집합이 어떻게 변화하는지 살펴보면 쉽게 알 수 있습니다.<br>
두 점을 잇는 선분의 기울기만 봐도 모든 정렬의 결과를 확인할 수 있으므로, 가능한 정렬 결과의 경우의 수는 $O(N^2)$가지입니다. $O(N^2)$개의 기울기를 모두 구해서 정렬한 다음, 차례대로 순회하면서 점들의 순서를 바꾸는 방식으로 구현합니다.

<video autoplay controls loop preload="auto" style="max-width:100%">
    <source src="/img/rotating-sweep-line.mp4" type="video/mp4">
</video>


### 활용
구현 방법은 뒤에서 살펴보고, 이 테크닉을 어디에 활용할 수 있을지 먼저 알아보겠습니다.

#### BOJ 9484. 최대삼각형, 최소삼각형
$N$개의 점이 주어졌을 때, 3개의 점을 선택해서 만들 수 있는 삼각형 중 넓이의 최댓값과 최솟값을 구하는 문제입니다.

삼각형의 넓이는 밑변의 길이와 높이의 곱을 2로 나눠서 구할 수 있습니다. 만약 밑변으로 사용할 두 꼭짓점을 고정한다면, 두 꼭짓점을 잇는 직선과 가장 가까운 점을 선택하면 최소 넓이 삼각형을 얻을 수 있습니다. 마찬가지로 직선과 가장과 가장 먼 점을 선택하면 최대 넓이를 얻을 수 있습니다.

이 문제는 Rotating Sweep Line을 이용해 해결할 수 있습니다. 두 점을 잇는 선분의 기울기를 기준으로 정렬했고, 두 점의 번호를 각각 $u, v\ (u < v)$라고 하겠습니다. 두 점을 잇는 직선과 가장 가까운 점은 $u-1, v+1$ 중 하나이며, 가장 먼 점은 $1, N$ 중 하나입니다. 그러므로 단순히 점들의 정렬 순서만 관리하면 이 문제를 해결할 수 있습니다.

#### BOJ 3121. 빨간점, 파란점
좌표 평면 위에 빨간 점과 파란 점이 주어집니다. 평행선 사이에 파란 점이 존재하지 않도록 두 평행선을 그었을 때, 평행선 사이에 들어갈 수 있는 빨간 점 개수의 최댓값을 구하는 문제입니다.

평행선의 기울기를 기준으로 정렬했을 때 인접한 점이 두 평행선 사이에 함께 들어갑니다. 그러므로 기울기를 고정했다면, 수열에서 0이 포함되지 않고 1로만 구성된 가장 긴 연속 부분 수열을 구하는 문제라고 생각할 수 있습니다. 이러한 문제는 흔히 금광 세그라고 불리는 세그먼트 트리를 이용해 쉽게 해결할 수 있습니다.

$O(N^2)$개의 기울기에 대해 모두 수열에서의 문제를 푸는 것도 어렵지 않게 할 수 있습니다. 기울기마다 순서가 바뀌는 점은 2개이므로, 단순히 세그먼트 트리에서 리프 정점의 정보를 갱신하는 것으로 정렬 결과의 변화를 반영할 수 있습니다. 따라서 $O(N^2 \log N)$에 문제를 해결할 수 있습니다.

### 구현
세 점 이상이 한 직선 위에 있는 경우, 해당 기울기에서 그들의 순서를 바꾸는 것은 구간을 뒤집는 것으로 생각할 수 있습니다.

![](https://i.imgur.com/SSGziv5.png)

정렬 순서상에서 인접한 점끼리만 위치를 교환해야 하기 때문에 평행한 선분을 아무 순서대로 처리하면 안 됩니다. $i, i+1, \cdots, j$번 점을 뒤집을 때, 먼저 $i$를 $i+1,\cdots,j$와 교환해서 맨 뒤로 보내고, $i+1$를 $i+2,\cdots,j$와 교환해서 뒤로 보내는 식으로 구간을 뒤집으면 됩니다.

아래 코드는 BOJ 9484. 최대삼각형, 최소삼각형의 코드입니다. 점들의 초기 인덱스는 x좌표 오름차순으로 정렬했을 때의 순서로 정의했습니다.<br>
기울기가 같은 선분을 잘 처리하기 위해서 23번째 줄에서 기울기가 같은 경우, 선분이 연결하는 두 점의 초기 인덱스를 비교해서 정렬합니다.

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

struct Point{
    ll x, y;
    bool operator < (const Point &p) const {
        return tie(x, y) < tie(p.x, p.y);
    }
    bool operator == (const Point &p) const {
        return tie(x, y) == tie(p.x, p.y);
    }
};

struct Line{
    ll i, j, dx, dy; // i < j, dx >= 0
    Line(int i, int j, const Point &pi, const Point &pj)
        : i(i), j(j), dx(pj.x-pi.x), dy(pj.y-pi.y) {}

    // dy / dx < l.dy / l.dx
    bool operator < (const Line &l) const {
        ll le = dy * l.dx, ri = l.dy * dx;
        return tie(le, i, j) < tie(ri, l.i, l.j);
    }
    bool operator == (const Line &l) const {
        return dy * l.dx == l.dy * dx;
    }
};

ll TriangleArea(const Point &p1, const Point &p2, const Point &p3){
    ll cross = (p2.x - p1.x) * (p3.y - p2.y) - (p3.x - p2.x) * (p2.y - p1.y);
    return abs(cross);
}

int N, Pos[2020];
Point A[2020];

void Solve(){
    sort(A+1, A+N+1);
    for(int i=1; i<=N; i++) Pos[i] = i;

    vector<Line> V;
    for(int i=1; i<=N; i++) for(int j=i+1; j<=N; j++) V.emplace_back(i, j, A[i], A[j]);
    sort(V.begin(), V.end());

    ll Min = 0x3f3f3f3f3f3f3f3f, Max = 0xc0c0c0c0c0c0c0c0;
    for(int i=0, j=0; i<V.size(); i=j){
        while(j < V.size() && V[i] == V[j]) j++; // [i, j) -> same slope
        for(int k=i; k<j; k++){
            int u = V[k].i, v = V[k].j; // point id
            swap(Pos[u], Pos[v]);
            swap(A[Pos[u]], A[Pos[v]]);
            if(Pos[u] > Pos[v]) swap(u, v);
            if(Pos[u] > 1){
                Min = min(Min, TriangleArea(A[Pos[u]], A[Pos[v]], A[Pos[u]-1]));
                Max = max(Max, TriangleArea(A[Pos[u]], A[Pos[v]], A[1]));
            }
            if(Pos[v] < N){
                Min = min(Min, TriangleArea(A[Pos[u]], A[Pos[v]], A[Pos[v]+1]));
                Max = max(Max, TriangleArea(A[Pos[u]], A[Pos[v]], A[N]));
            }
        }
    }
    cout << Min/2 << "." << Min%2*5 << " ";
    cout << Max/2 << "." << Max%2*5 << "\n";
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    while(true){
        cin >> N; if(!N) break;
        for(int i=1; i<=N; i++) cin >> A[i].x >> A[i].y;
        Solve();
    }
}
```

### 연습 문제
[https://www.acmicpc.net/workbook/view/3610](https://www.acmicpc.net/workbook/view/3610)
