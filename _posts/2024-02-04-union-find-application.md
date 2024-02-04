---
title: "Union Find 200% 활용하기"
date: 2024-02-04 00:00:00
categories:
- Medium-Algorithm
tags:
- Union-Find
---

## 0. 목차
1. Union Find 복습
2. 오프라인 쿼리
3. Small to Large
4. 이분 그래프 표현
5. std::set 대체
6. 두 원소의 차이 관리

## 1. Union Find 복습

Union Find은 다음과 같은 연산을 지원하는 자료구조입니다. 구현 방법에 따라 union, find를 연산당 $O(n)$, $O(\log n)$, amortized $O(\log n)$, amortized $O(\log^\ast n)$, 또는 amortized $O(\alpha(n))$ 시간에 수행할 수 있습니다. 이때 $\alpha(n)$은 [Ackermann function](https://en.wikipedia.org/wiki/Ackermann_function)의 역함수($\alpha(n) = A(k,3) \geq n$을 만족하는 가장 작은 $k$)로, 매우 느리게 증가하는 함수입니다.

* init(n): 0~n-1번 원소를 각각 자기 자신만 있는 집합에 속하도록 초기화
* find(x): x번 원소가 속한 집합의 대푯값을 반환
* union(u, v) 또는 merge(u, v): u번 원소가 속한 집합과 v번 원소가 속한 집합을 병합

이후 설명에서 사용하는 코드의 이해를 돕기 위해, 제가 사용하는 Union Find의 구현을 아주 간단하게 소개합니다.

#### 1-1. Union Find의 구현

Union Find의 가장 기본적인 구현 방법은 다음과 같습니다. `std::iota(first, last, value)`는 `[first, last)` 구간을 `value, value+1, value+2, ...`로 채우는 함수입니다. find 연산의 시간 복잡도는 트리의 높이에 비례합니다. 따라서 최악의 경우 $O(n)$이며, find를 2번 수행하는  merge 연산의 시간 복잡도 또한 $O(n)$이라는 것을 쉽게 알 수 있습니다.

```cpp
struct union_find{
    vector<int> p;
    union_find(int n) : p(n) {
        iota(p.begin(), p.end(), 0);
    }
    int find(int v){
        if(v == p[v]) return v;
        else return find(p[v]);
    }
    bool merge(int u, int v){
        u = find(u); v = find(v);
        if(u == v) return false;
        p[u] = v; return true;
    }
};
```

#### 1-2. Union by Rank

rank[x]를 x가 루트인 트리의 높이라고 정의합시다. 높이가 큰 트리 밑에 작은 트리를 붙이는 방식으로 merge 연산을 구현하면 트리의 높이가 항상 $O(\log n)$이하가 된다는 것을 증명할 수 있습니다. 정점이 $2^h-1$개 이하인 트리의 높이는 항상 $h$ 이하임을 보이면 됩니다. 구현은 다음과 같습니다.

```cpp
struct union_find{
    vector<int> p, r;
    union_find(int n) : p(n), r(n) {
        iota(p.begin(), p.end(), 0);
        fill(r.begin(), r.end(), 1);
    }
    int find(int v){
        if(v == p[v]) return v;
        else return find(p[v]);
    }
    bool merge(int u, int v){
        u = find(u); v = find(v);
        if(u == v) return false;
        if(r[u] > r[v]) swap(u, v);
        if(r[u] == r[v]) r[v]++;
        p[u] = v; return true;
    }
};
```

#### 1-3. Union by Size

size[x]를 x가 루트인 트리에 포함된 정점의 개수라고 정의합시다. 크기가 큰 트리 밑에 크기가 작은 트리를 붙이는 방식으로 merge 연산을 구현하면 트리의 높이가 항상 $O(\log n)$ 이하가 된다는 것을 알 수 있습니다. 어떤 정점이 속한 트리의 크기가 증가하는 경우, 즉 어떤 정점의 깊이가 증가하는 경우에는 항상 2배 이상 증가하게 되고, 트리의 최대 크기는 $n$이므로 각 정점의 깊이가 최대 $O(\log n)$번만 증가할 수 있기 때문입니다. 구현은 다음과 같습니다.

```cpp
struct union_find{
    vector<int> p, s;
    union_find(int n) : p(n), s(n) {
        iota(p.begin(), p.end(), 0);
        fill(s.begin(), s.end(), 1);
    }
    int find(int v){
        if(v == p[v]) return v;
        else return find(p[v]);
    }
    bool merge(int u, int v){
        u = find(u); v = find(v);
        if(u == v) return false;
        if(s[u] > s[v]) swap(u, v);
        p[u] = v; s[v] += s[u];
        return true;
    }
};
```

Union by size의 시간 복잡도 증명 아이디어는 small to large, heavy light decomposition 등 다양한 알고리즘의 시간 복잡도 증명에 사용되기 때문에 잘 이해하고 넘어가야 합니다.

#### 1-4. Path Compression

아래 코드와 같이 경로 압축을 수행하면 find 연산의 시간 복잡도가 amortized $O(\log n)$이 됨을 증명할 수 있습니다. 증명은 ([링크](https://kim1109123.tistory.com/84))에서 확인할 수 있습니다. 분할 상환 분석을 통해 얻은 시간 복잡도이기 때문에, 연산 한 번에는 worst $O(n)$이 될 수 있음에 유의해야 합니다.

```cpp
struct union_find{
    vector<int> p;
    union_find(int n) : p(n) {
        iota(p.begin(), p.end(), 0);
    }
    int find(int v){
        if(v == p[v]) return v;
        else return p[v] = find(p[v]);
    }
    bool merge(int u, int v){
        u = find(u); v = find(v);
        if(u == v) return false;
        p[u] = v; return true;
    }
};
```

삼항 연산자와 논리 연산자를 활용하면 더 짧게 구현할 수도 있습니다.

```cpp
struct union_find{
    vector<int> p;
    union_find(int n) : p(n) { iota(p.begin(), p.end(), 0); }
    int find(int v){ return v == p[v] ? v : p[v] = find(p[v]); }
    bool merge(int u, int v){ return find(p[u]) != find(p[v]) && (p[p[u]]=p[v], true); }
};
```

path compression과 union by rank를 함께 사용하면 amortized $O(\log^\ast n)$, 더 나아가 amortized $O(\alpha(n))$이 됨을 증명할 수 있지만, 이 글의 범위를 벗어나므로 생략합니다([링크1](https://github.com/infossm/infossm.github.io/blob/master/_posts/2021-04-19-Union-Find-Time-Complexity-Proof.md), [링크2](https://codeforces.com/blog/entry/98275)). union by rank를 함께 사용하면 연산을 한 번 수행할 때도 worst $O(\log n)$이 됩니다.

## 2. 오프라인 쿼리

[BOJ 13306 트리](https://www.acmicpc.net/problem/13306) 문제를 봅시다. 아래 두 가지 쿼리를 처리해야 하는 문제입니다.

* 0 x : x와 x의 부모 정점을 연결하는 간선 **제거**
* 1 c d : c번 정점과 d번 정점이 같은 컴포넌트에 있는지 확인

일반적으로 Union Find는 그래프에 간선이 추가되는 상황에서 정점들의 연결 여부를 관리(incremental dynamic connectivity)할 때 사용하는 자료구조입니다. 하지만 이 문제는 간선이 추가되는 상황이 추가되는 상황이 아닌, 간선이 제거되는 상황에서 정점들의 연결 여부를 관리(**decremental** dynamic connectivity)해야 합니다. 다행히도 온라인 저지의 채점 방식 특성상 쿼리가 주어질 때마다 바로바로 정답을 구하지 않아도 되기 때문에, 쿼리를 모두 입력받은 다음 뒤에서부터 쿼리를 처리하면 incremental 상황으로 바꿀 수 있습니다.

0번 쿼리가 정확히 N-1번 주어지기 때문에, 쿼리를 뒤에서부터 처리하면 정점들이 모두 흩어져 있는 상황에서 시작합니다. 원래 간선을 제거하는 쿼리였던 0번 쿼리는 간선을 추가하는 쿼리로 바뀌게 됩니다. 따라서 아래 코드처럼 Union Find를 이용해 문제를 해결할 수 있습니다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int P[202020];
int Find(int v){ return v == P[v] ? v : P[v] = Find(P[v]); }
void Merge(int u, int v){ if(Find(u) != Find(v)) P[Find(u)] = Find(v); }

int N, Q, G[202020];
int A[404040], B[404040], C[404040];
vector<int> R;

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N >> Q; Q += N-1;
    for(int i=2; i<=N; i++) cin >> G[i];
    for(int i=1; i<=Q; i++){
        cin >> A[i];
        if(A[i] == 0) cin >> B[i];
        else cin >> B[i] >> C[i];
    }

    iota(P+1, P+N+1, 1);
    for(int i=Q; i>=1; i--){
        if(A[i] == 0) Merge(B[i], G[B[i]]);
        else R.push_back(Find(B[i]) == Find(C[i]));
    }

    reverse(R.begin(), R.end());
    for(auto i : R) cout << (i ? "YES" : "NO") << "\n";
}
```

이런 식으로 쿼리를 모두 입력받은 다음, 쿼리의 순서를 원하는 대로 수정해서 처리하는 것을 오프라인 쿼리라고 부릅니다. 출제자가 오프라인 풀이를 막으려고 할 때는 [BOJ 13309 트리](https://www.acmicpc.net/problem/13309), [BOJ 22306 트리의 색깔과 쿼리 2](https://www.acmicpc.net/problem/22306), [BOJ 17465 동적 연결성과 쿼리](https://www.acmicpc.net/problem/17465)처럼 이전 쿼리의 결과를 이용해 다음 쿼리의 파라미터를 생성하게 하는데, 이런 문제는 반드시 쿼리를 주어지는 순서대로 처리해야 합니다.

## 3. Small to Large

[설명 바로가기](https://justicehui.github.io/medium-algorithm/2019/09/23/small-to-large/)

## 4. 이분 그래프 표현

이 단락에서는 Union Find에 몇 가지 추가 정보를 저장해서 간선이 추가되는 상황에서 각 컴포넌트가 **이분 그래프**인지 판별하는 문제를 푸는 방법을 알아볼 것입니다. 이분 그래프란, 그래프 $G = (V, E)$를 $E \subset \{(u, v);\ u \in L, v \in R\}$이 성립하도록 정점 집합을 $V = L \cup R$로 분할할 수 있는 그래프를 의미합니다. 쉽게 말해, 정점 집합을 두 개의 그룹 $L, R$으로 분할하려고 할 때, 같은 그룹에 속한 두 정점을 연결하는 간선이 없도록 분할 가능한 그래프가 이분 그래프입니다.

#### 4-1. BOJ 1765 닭싸움 팀 정하기

[BOJ 1765 닭싸움 팀 정하기](https://www.acmicpc.net/problem/1765) 문제를 봅시다. 다음과 같은 조건이 주어질 때 가능한 친구 그룹 개수의 최댓값을 구하는 문제입니다.

* F p q : p와 q는 친구이다.
* E p q : p와 q는 원수이다.
* 단, 친구의 친구는 친구이며, 원수의 원수도 친구이다.

사람을 정점으로, 친구 그룹을 정점 그룹으로 생각하면 이 문제를 이분 그래프의 컴포넌트 개수(와 비슷한 것)을 관리하는 문제로 바꿀 수 있습니다.

* F p q : p와 q는 반드시 같은 그룹에 속해야 한다.
* E p q : p와 q는 반드시 다른 그룹에 속해야 한다.

이런 문제는 무조건 같은 그룹에 포함되어야 하는 정점들을 Union Find를 이용해 묶으면서, x번 정점과 무조건 다른 그룹에 포함되어야 하는 정점 하나(`enemy[x]`)를 관리하는 방식으로 해결할 수 있습니다. "p와 q는 친구이다" 라는 정보가 주어지면 단순히 union하면 되고, "p와 q는 원수이다" 라는 정보가 주어지면 enemy[p]와 q, 그리고 enemy[q]와 p를 union 하면 됩니다. 문제에서 요구하지는 않지만, 각 컴포넌트가 이분 그래프인지 판별하는 것은 x와 enemy[x]가 서로 다른 집합에 있는지 확인하면 됩니다.

```cpp
struct friendship_bipartite_union_find{
    vector<int> p, e; // parent, enemy
    bipartite_union_find(int n) : p(n), e(n, -1) {
        iota(p.begin(), p.end(), 0);
    }
    int find(int v){ return v == p[v] ? v : p[v] = find(p[v]); }
    bool merge(int u, int v){ return find(u) != find(v) && (p[p[u]]=p[v], true); }
    int set_friend(int u, int v){ return merge(u, v); }
    int set_enemy(int u, int v){
        int res = 0;
        if(e[u] == -1) e[u] = v;
        else res += set_friend(e[u], v);
        if(e[v] == -1) e[v] = u;
        else res += set_friend(e[v], u);
        return res;
    }
};
```

#### 4-2. BOJ 28121 산책과 쿼리

[BOJ 28121 산책과 쿼리](https://www.acmicpc.net/problem/28121) 문제를 봅시다. 간선이 추가되는 상황에서 산책의 자유도가 높은 자취방의 개수를 구하는 문제입니다. 어떤 자취방 $v$의 자유도가 높다는 것은, $10^6$ 이상의 모든 정수 $t$에 대해, $v$에서 출발해 $v$로 돌아오는 길이가 $t$인 사이클이 존재함을 의미합니다. $v$와 연결된 정점이 하나라도 있으면 그 정점을 왕복하는 것으로 항상 짝수 길이 사이클을 만들 수 있으니 홀수 길이 사이클에만 집중하면 됩니다. 여기에서 **어떤 그래프가 이분 그래프인 것과 그래프에 홀수 사이클이 없는 것은 동치이다** 라는 사실을 이용하면, 결국 이 문제는 이분 그래프가 아닌 컴포넌트의 크기의 합을 구하는 문제라는 것을 알 수 있습니다.

사실 이 문제는 이분 그래프보다는 홀수 사이클에 초점을 맞추는 것이 더 편합니다. 각 정점 $v$를 두 개의 정점 $v_0, v_1$로 분할합시다. $v_0$은 짝수 시간에 $v$에 위치하는 상태, $v_1$은 홀수 시간에 $v$에 위치하는 상태를 의미합니다. 만약 $v_0$에서 $v_1$로 이동할 수 있으면 $v$를 포함하는 홀수 사이클이 존재합니다. 따라서 간선 $(a, b)$가 추가될 때마다 $a_0$과 $b_1$, 그리고 $a_1$과 $b_0$을 union한 다음, $v_0$과 $v_1$이 같은 집합에 속하는 $v$의 개수를 구하면 문제를 해결할 수 있습니다.

```cpp
struct bipartite_union_find{
    int n, sum; vector<int> p, s;
    bipartite_union_find(int n) : n(n), sum(0), p(n+n), s(n+n) {
        iota(p.begin(), p.end(), 0);
        fill(s.begin(), s.begin()+n, 1);
    }
    int neg(int v){ return v < n ? v + n : v - n; } // v_0 <=> v_1
    int find(int v){ return v == p[v] ? v : p[v] = find(p[v]); }
    void merge(int u, int v){
        u = find(u); v = find(v);
        if(u == v) return;
        if(find(neg(u)) == u) sum -= s[u];
        if(find(neg(v)) == v) sum -= s[v];
        p[u] = v; s[v] += s[u];
        if(find(neg(v)) == v) sum += s[v];
    }
    void add_edge(int u, int v){
        merge(u, neg(v));
        merge(v, neg(u));
    }
};
```

## 5. std::set 대체

std::set(또는 balanced binary search tree)은 삽입(insert), 삭제(erase), x 이상인 최소 원소(lower bound), x 초과인 최대 원소(upper bound) 등의 연산을 $O(\log n)$에 수행하는 멋진 자료구조지만, 시간 복잡도에 붙는 상수 계수가 상당히 크다는 단점이 있습니다. 이 단락에서는 아래 조건을 만족하는 상황에서 Union Find를 이용해 lower bound와 upper bound 연산을 구현하는 방법에 대해 다룹니다.

1. 집합에 1부터 n까지의 원소가 정확히 하나씩 들어있는 상황에서 시작
2. 원소를 제거할 수만 있고, 삽입할 수 없음

즉, 원소가 추가되지 않을 때 erase, lower bound, upper bound 연산을 $O(\log N)$, 또는 amortized $O(\alpha(n))$ 정도에 구현하는 것이 목표입니다.

핵심 아이디어는 연속한 위치에 있는 삭제된 원소들을 구간을 관리하는 것입니다. 만약 2~4가 삭제된 상황이라면 2~4를 union한 뒤, 이 구간의 왼쪽 끝점과 오른쪽 끝점이 각각 2와 4라고 기록하면 됩니다. union 할 때마다 구간의 최솟값과 최댓값을 관리하면 시간 복잡도의 변화 없이 이런 정보를 관리할 수 있습니다. x 이상인 최소 원소를 찾는 연산(lower bound)은 x가 삭제되었는지 확인한 다음 삭제되었다면 (x가 포함된 구간의 최댓값) + 1을 반환하면 되고, upper bound도 비슷하게 구현할 수 있습니다. 자세한 구현은 아래 코드에서 확인할 수 있습니다.

```cpp
struct union_find{
    vector<int> p, l, r;
    union_find(int n) : p(n), l(n), r(n) {
        iota(p.begin(), p.end(), 0);
        iota(l.begin(), l.end(), 0);
        iota(r.begin(), r.end(), 0);
    }
    int find(int v){ return v == p[v] ? v : p[v] = find(p[v]); }
    void merge(int u, int v){
        u = find(u); v = find(v);
        if(u == v) return; p[u] = v;
        l[v] = min(l[v], l[u]);
        r[v] = max(r[v], r[u]);
    }
    int prev(int x){ return find(l[find(x)]-1); }
    int next(int x){ return find(r[find(x)]+1); }
};

struct uf_set{ // 1-based
    union_find uf;
    vector<int> chk;
    uf_set(int n) : uf(n+2), chk(n+2) {}
    void erase(int x){
        chk[x] = 1;
        if(chk[x-1]) uf.merge(x-1, x);
        if(chk[x+1]) uf.merge(x, x+1);
    }
    int prev(int x){
        do x = uf.prev(x); while(chk[x]);
        return x;
    }
    int next(int x){
        do x = uf.next(x); while(chk[x]);
        return x;
    }
    int front(){ return next(0); }
    int back(){ return prev(chk.size()-1); }
};
```

[BOJ 10775 공항](https://www.acmicpc.net/problem/10775) 문제를 봅시다. 이 문제는 $g_i$가 주어질 때마다, 아직 도킹 되지 않은 게이트 중 번호가 $g_i$ 이하인 가장 큰 게이트에 도킹시키는 그리디 풀이가 성립함을 어렵지 않게 알 수 있습니다. 따라서 std::set을 이용하면 다음과 같이 문제를 해결할 수 있습니다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int N, Q; cin >> N >> Q;
    set<int> S;
    for(int i=1; i<=N; i++) S.insert(i);
    for(int i=1; i<=Q; i++){
        int t; cin >> t;
        auto it = S.upper_bound(t);
        if(it == S.begin()){ cout << i - 1; return 0; }
        else S.erase(--it);
    }
    cout << Q;
}
```

위에서 구현한 uf_set을 이용하면 다음과 같이 구현할 수도 있습니다. std::set을 사용한 프로그램의 실행 시간은 60ms, uf_set을 사용한 프로그램의 실행 시간은 16ms 입니다.

```cpp
int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int N, Q; cin >> N >> Q;
    uf_set S(N);
    for(int i=1; i<=Q; i++){
        int t; cin >> t;
        int pos = S.prev(t+1);
        if(pos == 0){ cout << i - 1; return 0; }
        else S.erase(pos);
    }
    cout << Q;
}
```

이 문제와 같이 "x 이하인 가장 큰 원소"를 구하는 연산만 주어질 때는 아래 코드처럼 더 간단하게 구현할 수도 있습니다. 항상 번호가 작은 정점 아래에 번호가 큰 정점을 붙이는 방식으로 union을 진행하며, x를 삭제할 때마다 x-1과 x를 union해서 find(x)가 항상 x 이하인 가장 큰 원소를 가리키게 하는 방법입니다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int N, Q, P[101010];
int Find(int v){ return v == P[v] ? v : P[v] = Find(P[v]); }

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N >> Q;
    iota(P, P+101010, 0);
    for(int i=1; i<=Q; i++){
        int t; cin >> t;
        int pos = Find(t);
        if(pos == 0){ cout << i - 1; return 0; }
        else P[pos] = Find(pos-1);
    }
    cout << Q;
}
```

## 6. 두 원소의 차이 관리

Union Find를 이용하면 다음과 같은 쿼리가 주어지는 문제([BOJ 3830 교수님은 기다리지 않는다](https://www.acmicpc.net/problem/3830), 2021 SCPC 1차 예선 5번)도 해결할 수 있습니다.

* 1 u v w : $X_v - X_u = w$ 조건 추가
* 2 u v : $X_v - X_u$ 출력, 만약 두 변수의 차를 계산할 수 없으면 `NC`, 두 변수의 차를 계산하는 데 모순이 발생하면 `CF` 출력

기본적인 아이디어는 Union Find에서 루트 정점의 값은 항상 0으로 고정하고, 각 정점마다 부모 정점과의 차이를 저장하는 것입니다. $f(v) = X_v - X_{find(v)}$의 값을 구할 때는 단순히 $v$에서 루트 정점으로 갈 때 거치는 간선의 가중치를 모두 더하면 되고, 따라서 find 연산에서 경로 압축도 어렵지 않게 구현할 수 있습니다. $X_v - X_u$는 $f(v) - f(u)$를 구하면 되므로 2번의 find 연산으로 답을 구할 수 있습니다. 단, $u$와 $v$가 서로 다른 집합에 포함되어 있는 경우를 따로 처리해야 합니다.

merge 연산은 피연산자로 $u, v$가 주어지지만 실제로 연산을 수행할 때는 $find(u), find(v)$를 다뤄야 한다는 점에서 다른 연산에 비해 조금 복잡합니다. 만약 $u, v$가 이미 같은 집합에 속해있을 때는 $f(v) - f(u) = w$를 만족해야 합니다. 따라서 $f(v) - f(u) - w \ne 0$이면 해당 집합에 모순이 발생했다고 표시해야 합니다. 모순이 발생하지 않으면 $find(u)$의 부모 정점을 $find(v)$로 지정하면서 간선의 가중치 $e$를 결정해야 합니다. $w = X_v - X_u = f(v) - f(u) - e$를 만족해야 하므로 $e = f(v) - f(u) - w$로 설정하면 됩니다. 구현은 다음과 같이 할 수 있습니다.

```cpp
template<typename cost_t>
struct weighted_union_find{
    vector<int> p, die;
    vector<cost_t> d;
    weighted_union_find(int n) : p(n), die(n,0), d(n,0) { iota(p.begin(), p.end(), 0); }
    pair<int,cost_t> find(int v){
        if(v == p[v]) return {v, 0};
        auto [root,diff] = find(p[v]);
        p[v] = root; d[v] += diff;
        return {p[v], d[v]};
    }
    void merge(int u, int v, cost_t w){
        auto [pu,wu] = find(u);
        auto [pv,wv] = find(v);
        if(pu == pv){ die[pv] |= wv - wu - w != 0; return; }
        p[pu] = pv; die[pv] |= die[pu];
        d[pu] = wv - wu - w;
    }
    pair<bool,cost_t> get_difference(int u, int v){ // return W[v] - W[u]
        auto [pu, du] = find(u);
        auto [pv, dv] = find(v);
        if(pu == pv && !die[pv]) return {true, dv - du};
        else if(pu == pv && die[pv]) return {false, cost_t(-1)};
        else return {false, cost_t(0)};
    }
};
```

## 연습 문제 목록

* 오프라인 쿼리
  * [BOJ 13306 트리](https://www.acmicpc.net/problem/13306)
* Small to Large
  * [BOJ 28277 뭉쳐야 산다](https://www.acmicpc.net/problem/28277)
  * [BOJ 17469 트리의 색깔과 쿼리](https://www.acmicpc.net/problem/17469)
  * [BOJ 13309 트리](https://www.acmicpc.net/problem/13309)
  * [BOJ 22306 트리의 색깔과 쿼리 2](https://www.acmicpc.net/problem/22306) ([풀이](https://justicehui.github.io/ps/2021/08/29/BOJ22306/))
* 이분 그래프 표현
  * [BOJ 1765 닭싸움 팀 정하기](https://www.acmicpc.net/problem/1765)
  * [BOJ 28121 산책과 쿼리](https://www.acmicpc.net/problem/28121)
* std::set 대체
  * [BOJ 10775 공항](https://www.acmicpc.net/problem/10775)
  * [BOJ 9938 방 청소](https://www.acmicpc.net/problem/9938) (10775 공항 문제 설명의 마지막 코드 변형)
  * [BOJ 2957 이진 탐색 트리](https://www.acmicpc.net/problem/2957) ([풀이](https://justicehui.github.io/coci/2019/08/24/BOJ2957/))
  * [BOJ 9264 Intercity](https://www.acmicpc.net/problem/9264) ([밀집 그래프에서의 BFS - jung2381187](https://casterian.net/algo/dense-bfs.html) 참고)
* 두 원소의 차이 관리
  * [BOJ 3830 교수님은 기다리지 않는다](https://www.acmicpc.net/problem/3830)
  * 2021 SCPC 1차 예선 5번 (Codeground 142, SCPC 7회 예선) 차이
  * [BOJ 26469 N-1 Truths and a Lie](https://www.acmicpc.net/problem/26469) ([offline dynamic connectivity - stonejjun03](https://stonejjun.tistory.com/171) 참고)
* 추가 문제
  * [BOJ 21725 더치페이](https://www.acmicpc.net/problem/21725) ([풀이](https://justicehui.github.io/ps/2023/02/08/BOJ21725/))
  * [BOJ 1396 크루스칼의 공](https://www.acmicpc.net/problem/1396)
  * [BOJ 16911 그래프와 쿼리](https://www.acmicpc.net/problem/16911)
  * [BOJ 23299 화질 - 자동 (480p)](https://www.acmicpc.net/problem/23299) ([풀이](https://justicehui.github.io/ps/2023/02/19/BOJ23299/))
