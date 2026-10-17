---
title: "2026 ICPC 서울 예선 풀이"
date: 2026-10-17 00:00:00
categories:
- icpc
tags:
- icpc
---

### 서론
2026년 10월 17일에 개최된 2026 ICPC 서울 예선(2026 ICPC Asia Korea National First Round Programming Contest) *비공식* 풀이입니다.

이 글에서 설명한 풀이 외에도 문제를 해결하는 여러 방법이 존재할 수 있습니다. 제가 생각하는 난이도 순서대로 설명합니다.

### K. Square

$(x_1, y_1)$과 $(x_2, y_2)$를 연결하는 선분을 한 변으로 하는 정사각형의 넓이는 $(x_2-x_1)^2+(y_2-y_1)^2$입니다. 따라서 $n$을 1개 또는 2개의 제곱수의 합으로 표현할 수 있는지 확인하면 됩니다. $x_2-x_1=0$ 또는 $y_2-y_2=0$인 경우도 확인해야 함에 유의해야 합니다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int N; cin >> N;
    for(int i=0; i*i<=N; i++){
        int j = sqrt(N-i*i);
        if(i * i + j * j == N){ cout << "YES"; return 0; }
    }
    cout << "NO";
}
```

### G. Korean Characters

다양한 풀이가 있고, 제가 생각하는 가장 간단한 방법을 설명합니다.

이 문제를 풀 때 가장 까다로운 부분은 `s[i]`를 초성, `s[i+1]`를 중성이라고 했을 때, `s[i+3]`를 보기 전까지는 `s[i+2]`가 종성이 될지 다음 글자의 초성이 될지 알 수 없다는 것입니다. 이는 자음-모음과 자음-모음-자음이 같은 prefix를 공유하고 있기 때문에 발생하는 문제입니다.

하지만 문자열을 뒤집으면 어떨까요? 종성이 없는 글자는 모음-자음, 종성이 있는 글자는 자음-모음-자음이 되기 때문에 `s[i]`만 보더라도 두 글자를 가져갈지 세 글자를 가져갈지 알 수 있습니다.

주어진 알파벳이 자음과 모음 모두 아닌 글자일 수 있다는 것에 유의해야 합니다.

PL이나 컴파일러 과목의 용어를 빌리자면, 문자열을 뒤집었을 때 `A → VC | CVC` 가 되어 두 규칙의 FIRST 집합이 서로소가 되므로, 한 토큰만 읽어도 규칙을 결정할 수 있다고 생각할 수도 있습니다.

```cpp
#include <bits/stdc++.h>
using namespace std;

bool ja(char c){ return string("gndrmbsxjcktph").find(c) != string::npos; }
bool mo(char c){ return string("aAeEoOuUwi").find(c) != string::npos; }
void die(){ cout << "-ERROR-"; exit(0); }

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    string s, res; cin >> s;
    reverse(s.begin(), s.end());

    int n = s.size(), p = 0;
    while(p < n){
        int len = -1;
        if(p + 2 < n && ja(s[p]) && mo(s[p+1]) && ja(s[p+2])) len = 3;
        else if(p + 1 < n && mo(s[p]) && ja(s[p+1])) len = 2;
        else die();
        if(p) res += "-";
        res += s.substr(p, len);
        p += len;
    }

    reverse(res.begin(), res.end());
    cout << res;
}
```

### H. Marathon

위험도가 $x$ 이하인 구간을 만들 수 있는지 판별하는 함수 $f(x)$를 정의합시다. 파라메트릭 서치를 이용해 $f(x) = 1$인 가장 작은 $x$를 찾으면 문제를 해결할 수 있습니다.

어떤 구간 $[l, r]$의 위험도를 $x$로 만들기 위해서는, $S_x(l,r)=\sum_{i=l}^r \max(0,A_i-x)$ 만큼의 안전 요원이 필요합니다. 문제를 효율적으로 해결하기 위해서는, 정수 $x$가 주어졌을 때 $S_x(1, L), S_x(2, L+1), \cdots, S_x(N-L+1, N)$을 빠르게 구할 수 있어야 합니다. 이는 새로운 배열 $B_i = \max(0,A_i-x)$를 정의한 뒤, $B_i$의 누적 합을 구하는 것으로 $O(N)$ 시간에 처리할 수 있습니다.

따라서 전체 시간 복잡도는 $O(N \log N)$입니다.

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

ll N, L, K, A[202020], B[202020];

bool Check(ll x){
    for(int i=1; i<=N; i++) B[i] = max(0LL, A[i] - x);
    partial_sum(B+1, B+N+1, B+1);
    for(int i=1, j=L; j<=N; i++, j++) if(B[j] - B[i-1] <= K) return true;
    return false;
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N >> L >> K;
    for(int i=1; i<=N; i++) cin >> A[i];
    ll l = 0, r = 1e9;
    while(l < r){
        ll m = (l + r) / 2;
        if(Check(m)) r = m;
        else l = m + 1;
    }
    cout << r;
}
```

### A. Citation Index

인용 횟수가 $x$ 이상인 논문이 $x$편 이상 존재하는지 판별하는 함수 $f(x)$를 정의합시다. $f(0) = 1$, $f(N+1) = 0$이고, 어떤 수 $x_0$에 대해, $x \le x_0$ 이면 $f(x) = 1$, $x > x_0$이면 $f(x) = 0$입니다. 따라서 h-index, 즉 $f(x) = 1$인 가장 큰 $x$를 찾는 것은 파라메트릭 서치를 이용해 $f(x)$를 $O(\log N)$번 호출하여 수행할 수 있습니다.

사전 지식이 충분하다는 가정하에, 가장 떠올리기 쉬운 방법은 세그먼트 트리나 적당한 BBST를 이용해 $f(x)$를 $O(\log N)$ 시간에 계산하는 것입니다. 이 경우 시간 복잡도는 $O(N \log N + Q \log^2 N)$입니다. 세그먼트 트리를 이용해 문제를 해결할 때는 h-index는 항상 $N$ 이하라는 점을 이용해서 인용 수를 $N$ 이하로 관리하는 것이 좋습니다.

```cpp
#include <bits/extc++.h>
using namespace std;
using namespace __gnu_pbds;
using ll = long long;
template <typename T> using ordered_set = tree<T, null_type, less<T>, rb_tree_tag, tree_order_statistics_node_update>;

ll N, Q, A[202020];
ordered_set<pair<ll,ll>> T;

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N >> Q;
    for(int i=1; i<=N; i++) cin >> A[i];
    for(int i=1; i<=N; i++) T.insert({A[i], i});
    for(int q=1; q<=Q; q++){
        ll x, v; cin >> x >> v;
        T.erase({A[x], x});
        T.insert({A[x]+=v, x});

        int l = 0, r = N;
        while(l < r){
            int m = (l + r + 1) / 2;
            if(m == 0 || T.find_by_order(N-m)->first >= m) l = m;
            else r = m - 1;
        }
        cout << l << "\n";
    }
}
```

사실은 매 쿼리마다 이분 탐색을 하지 않아도 됩니다. 어떤 한 논문의 인용 수가 증가할 때마다 h-index는 최대 1 만큼 증가할 수 있습니다. 따라서 초기 h-index를 구한 뒤에는 1번의 $f(x)$ 호출만으로 답을 갱신할 수 있습니다. 이 경우 시간 복잡도는 $O((N+Q) \log N)$입니다.

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
constexpr int SZ = 1 << 18;

int N, Q, A[202020], T[SZ];
void Add(int x, int v){ for(x+=3; x<SZ; x+=x&-x) T[x] += v; }
int Get(int x){ int res = 0; for(x+=3; x; x-=x&-x) res += T[x]; return res; }
int Get(int l, int r){ return l <= r ? Get(r) - Get(l-1) : 0; }

int Index(){
    int l = 0, r = N;
    while(l < r){
        int m = (l + r + 1) / 2;
        if(Get(m, N) >= m) l = m;
        else r = m - 1;
    }
    return l;
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N >> Q;
    for(int i=1; i<=N; i++) cin >> A[i], A[i] = min(A[i], N);
    for(int i=1; i<=N; i++) Add(A[i], 1);

    int res = Index();

    for(int q=1; q<=Q; q++){
        int x, v; cin >> x >> v;
        Add(A[x], -1);
        A[x] = min(A[x]+v, N);
        Add(A[x], +1);

        if(res < N && Get(res+1, N) >= res+1) res++;
        cout << res << "\n";
    }
}
```

이 밖에도 $O(N+Q)$ 시간에 푸는 방법도 존재합니다.

### C. Convex Hull

convex set $A$에 들어가야만 하는 정점들을 찾는 방법에 대해 생각해 봅시다:

1. $A = S$로 시작
2. $u,v\in A$ 사이의 어떤 최단 경로 위에 있는 정점 $w\not\in A$를 $A$에 추가
3. 더 이상 추가할 정점이 없을 때까지 (2) 반복

위 과정의 결과가 순서와 무관하게 유일하고, 이렇게 만들어진 집합 $A$가 정답임을 증명할 수 있습니다. (2)에서 추가하는 정점이 모두 정답 집합에 포함되며, 위 과정이 종료되었을 때의 상태가 convex set임을 보이면, 최종 결과가 가장 작은 convex set이라는 것을 보일 수 있습니다.

(2)를 계산할 때 매번 모든 $u,v \in A$ 쌍을 검사하면 $D(u,v) = D(u,w) + D(w,v)$ 여부를 검사하기 위해 $O(N^3)$ 만큼의 연산이 필요하고, 정점이 최대 $O(N)$번 추가될 수 있으므로 전체 시간 복잡도가 $O(N^4)$이 되어 제한 시간 안에 문제를 해결할 수 없습니다. 정점 $w$를 새로 추가할 때, $w$와 $v \in A$의 쌍만 검사하면 시간 복잡도를 $O(N^3)$으로 만들 수 있습니다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int N, M, K, G[555][555], C[555];
vector<int> A, R;

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N >> M >> K; A.resize(K);
    for(auto &i : A) cin >> i;
    memset(G, 0x3f, sizeof G);
    for(int i=1; i<=N; i++) G[i][i] = 0;
    for(int i=1,u,v; i<=M; i++) cin >> u >> v, G[u][v] = G[v][u] = 1;
    for(int k=1; k<=N; k++) for(int i=1; i<=N; i++) for(int j=1; j<=N; j++) G[i][j] = min(G[i][j], G[i][k] + G[k][j]);

    queue<int> Q;
    for(auto i : A) Q.push(i), C[i] = 1;
    while(!Q.empty()){
        int v = Q.front(); Q.pop();
        for(auto i : R) for(int j=1; j<=N; j++) if(!C[j] && G[v][i] == G[v][j] + G[i][j]) Q.push(j), C[j] = 1;
        R.push_back(v);
    }

    sort(R.begin(), R.end());

    cout << R.size() << "\n";
    for(auto i : R) cout << i << " ";
}
```

### J. Secret Agent

proper interval graph에서 maximum induced path를 구하는 문제입니다.

어떠한 구간도 다른 구간을 포함하지 않는다는 성질을 이용하면, 문제의 조건을 아래와 같이 다시 쓸 수 있습니다.

1. 모든 $2 \le i \le k$에 대해 $v_{i-1}$과 $v_i$가 인접함
2. 모든 $3 \le i \le k$에 대해, $v_{i-2}$와 $v_i$가 인접하지 않음

구간을 시작점 $s_i$의 오름차순으로 정렬합시다. induced path $v_1, v_2, \cdots, v_k$의 끝에 새로운 정점 $v_{k+1}$을 추가할 수 있는지는 마지막 두 정점만 이용해 판별할 수 있습니다. 따라서 아래와 같이 마지막 두 정점을 인자로 하는 점화식을 세울 수 있습니다.

> $D(i,j):=$ 마지막 두 정점이 $j, i$인 induced path의 최대 가중치. 단, $j<i$이고 두 구간은 겹쳐야 함

점화식은 조건을 만족하는 $i,j,k$에 대해 $D(j,i)=w_i+\max(w_j,\max_{k<j} D(k,j))$와 같은 식을 이용해 $O(N^3)$ 시간에 계산할 수 있습니다. 이를 $O(N^2)$으로 줄이기 위해서는 각 상태의 답을 $O(N)$ 대신 $O(1)$ 만큼의 연산으로 구할 수 있어야 합니다.

$p_i = \max\lbrace j; j<i, e_j<s_i\rbrace$를 정의합시다. $i$보다 앞에 있으면서 $i$와 겹치지 않는 맨 뒤에 있는 구간을 의미합니다. prefix max 배열 $M_j(x)=\max(w_j,\max_{k\le x} D(k, j))$를 미리 계산해 두면, 점화식은 $D(j,i)=w_i+M_j(p_i)$와 같은 식을 이용해 $O(N^2)$ 시간에 답을 구할 수 있습니다.

이차원 배열의 인덱스 접근 순서에 따라 실행 시간이 2-3배 정도 차이 날 수 있음에 유의해야 합니다.

```cpp
#include <bits/stdc++.h>
using namespace std;
struct Info{ int s, e, w; };

int N, P[8080], D[8080][8080], M[8080][8080], R;
Info A[8080];

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N;
    for(int i=1; i<=N; i++) cin >> A[i].s >> A[i].e >> A[i].w;
    sort(A+1, A+N+1, [](auto a, auto b){ return a.s < b.s; });
    for(int i=1, j=1; i<=N; i++){
        while(j < i && A[j].e < A[i].s) j++;
        P[i] = j - 1;
    }
    for(int i=1; i<=N; i++){
        for(int j=P[i]+1; j<i; j++) D[i][j] = A[i].w + M[j][P[i]];
        M[i][0] = A[i].w;
        for(int j=1; j<i; j++) M[i][j] = max(M[i][j-1], D[i][j]);
    }
    for(int i=1; i<=N; i++) R = max(R, A[i].w);
    for(int i=1; i<=N; i++) for(int j=1; j<=N; j++) R = max(R, D[i][j]);
    cout << R;
}
```

### B. Coloring

입력으로 주어진 $2N$개의 점을 모두 포함하면서 축에 평행한 가장 작은 직사각형(bounding box)를 생각해 봅시다. 각 정사각형이 bounding box에서 서로 마주 보는 두 꼭짓점을 하나씩 포함하는 경우만 고려해도 답을 구할 수 있습니다.

설명과 구현의 편의를 위해 x좌표와 y좌표의 최솟값을 0, 최댓값을 각각 $X, Y$라고 합시다. 한 정사각형은 $(0, 0)$, 다른 정사각형은 $(X, Y)$를 포함할 때 변의 길이를 최소화하는 문제는 그리디하게 $O(N)$에 시간에 풀 수 있습니다. 이후 $(x, y)$를 $(x, Y-y)$로 변환한 뒤 같은 문제를 다시 해결하면 $O(N)$ 시간에 답을 구할 수 있습니다.

자세한 방법은 코드의 `Solve()` 함수에서 확인할 수 있습니다.

```cpp
#include <bits/stdc++.h>
using namespace std;

int N, X[2020202], Y[2020202], X1, Y1, X2, Y2;

int Solve(){
    int res = 0;
    for(int i=2; i<=N+N; i+=2){
        int px = X[i-1], py = Y[i-1], qx = X[i], qy = Y[i];
        int d1 = max({px, py, X2-qx, Y2-qy});
        int d2 = max({X2-px, Y2-py, qx, qy});
        res = max(res, min(d1, d2));
    }
    return res;
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N;
    for(int i=1; i<=N+N; i++) cin >> X[i] >> Y[i];
    X1 = *min_element(X+1, X+N+N+1);
    Y1 = *min_element(Y+1, Y+N+N+1);
    X2 = *max_element(X+1, X+N+N+1);
    Y2 = *max_element(Y+1, Y+N+N+1);

    for(int i=1; i<=N+N; i++) X[i] -= X1;
    for(int i=1; i<=N+N; i++) Y[i] -= Y1;
    X2 -= X1; Y2 -= Y1; X1 = Y1 = 0;

    int res = Solve();
    for(int i=1; i<=N+N; i++) Y[i] = Y2 - Y[i];
    res = min(res, Solve());

    cout << res;
}
```

### D. Cross Roller

이런 문제는 뒤에서부터 생각해서, 색칠하는 것 대신 한 겹씩 걷어낸다고 생각하는 것이 편합니다. ([참고](https://codeforces.com/contest/1638/problem/D))

같은 칸에서 두 번 칠할 필요는 없기 때문에, 색칠은 최대 $N^2$번만 해도 됩니다.

행과 열마다 각 색깔이 등장한 횟수와, 한 번 이상 등장한 색의 목록을 관리합시다. $k \le 50$ 이므로 후자는 64비트 정수 하나로 관리할 수 있습니다. 가장 마지막으로 색을 칠한 칸은 $O(N^2)$ 시간에 찾을 수 있습니다. 그 칸을 $(r_0, c_0)$라고 합시다.

마지막에 $(r_0, c_0)$에서 색을 칠하기로 결정했다면, 그보다 앞선 행동에서는 $r_0$번째 행이나 $c_0$번째 행을 자유롭게 사용할 수 있습니다. 따라서 마지막 행동의 위치를 결정했다면, 나머지 행동은 후보가 $N^2$가지인 격자 칸 대신 후보가 $2N$가지인 행 또는 열만 결정해도 충분합니다.

여기까지 왔다면, 사실은 필요한 색칠 횟수가 $O(N^2)$이 아닌 $O(N)$이라는 것을 알 수 있습니다. 따라서 매번 색칠할 행 또는 열을 결정하는 데 $O(N)$ 만큼의 연산을 수행해도 문제없이 제한 시간 안에 해결할 수 있습니다.

행과 열에서 각 색깔이 등장하는 횟수와, 등장하는 색의 목록을 관리하고 있다면, 어떤 행(또는 열)을 이번 차례에 색칠할 수 있는지 $O(1)$ 시간에 판정할 수 있고, 행과 열을 색칠할 때 정보를 갱신하는 것 또한 $O(1)$ 시간에 할 수 있습니다. 따라서 매번 모든 행과 열을 확인하면서 색칠할 칸을 $O(N)$ 시간에 정하면 됩니다.

전체 시간 복잡도는 $O(N^2)$, 공간 복잡도는 $O(N^2K)$입니다.

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

int N, K, A[1212][1212];
ll Row[1212], Col[1212];
int RC[1212][55], CC[1212][55];
vector<array<int,3>> R;

int GetColor(ll *arr, int i){
    if(arr[i] % 2 == 1 || __builtin_popcountll(arr[i]) != 1) return -1;
    return __lg(arr[i]);
}

void Remove(int r, int c){
    if(A[r][c] == -1) return;
    int v = A[r][c];
    if(!--RC[r][v]) Row[r] ^= 1LL << v;
    if(!--CC[c][v]) Col[c] ^= 1LL << v;
    A[r][c] = -1;
}

void Paint(int r, int c, int v){
    R.push_back({r, c, v});
    for(int i=1; i<=N; i++) Remove(r, i), Remove(i, c);
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N >> K;
    for(int i=1; i<=N; i++) for(int j=1; j<=N; j++) cin >> A[i][j];
    for(int i=1; i<=N; i++) for(int j=1; j<=N; j++) RC[i][A[i][j]]++, CC[j][A[i][j]]++;
    for(int i=1; i<=N; i++) for(int j=0; j<=K+1; j++) if(RC[i][j]) Row[i] |= 1LL << j;
    for(int i=1; i<=N; i++) for(int j=0; j<=K+1; j++) if(CC[i][j]) Col[i] |= 1LL << j;

    if(count(Row+1, Row+N+1, 1) == N){ cout << "YES\n0\n"; return 0; }

    int r = -1, c = -1;
    for(int i=1; i<=N; i++) for(int j=1; j<=N; j++) if(GetColor(Row, i) != -1 && GetColor(Col, j) != -1) r = i, c = j;
    if(r == -1 || c == -1){ cout << "NO"; return 0; }

    Paint(r, c, A[r][c]);
    while(true){
        bool change = false;
        for(int i=1; i<=N; i++){
            if(int v=GetColor(Row, i); v != -1){ Paint(i, c, v); change = true; break; }
            if(int v=GetColor(Col, i); v != -1){ Paint(r, i, v); change = true; break; }
        }
        if(!change) break;
    }

    bool finish = true;
    for(int i=1; i<=N; i++) for(int j=1; j<=N; j++) finish &= A[i][j] <= 0;
    if(!finish){ cout << "NO"; return 0; }

    reverse(R.begin(), R.end());
    cout << "YES\n" << R.size() << "\n";
    for(auto [i,j,v] : R) cout << i << " " << j << " " << v << "\n";
}
```

### F. Interactive String Matching

쿼리 횟수 제한이 $2\times (\vert S\vert + \vert P\vert)$ 라는 것에서 KMP를 사용하면 된다는 생각은 어렵지 않게 할 수 있습니다. 하지만 표준적인 KMP 알고리즘의 구현에서는 실패 함수를 구할 때 $P_i$와 $P_j$를 비교하기 때문에 이 문제에 바로 적용할 수 없습니다.

```cpp
vector<int> GetFail(const string &p){
    int n = p.size();
    vector<int> fail(n);
    for(int i=1, j=0; i<n; i++){
        while(j && p[i] != p[j]) j = fail[j-1];
        if(p[i] == p[j]) fail[i] = ++j;
    }
    return fail;
}

vector<int> Match(const string &s, const string &p){
    int n = s.size(), m = p.size();
    vector<int> fail = GetFail(p), res;
    for(int i=0, j=0; i<n; i++){
        while(j && s[i] != p[j]) j = fail[j-1];
        if(s[i] == p[j] && ++j == m) res.push_back(i-m+1), j = fail[j-1];
    }
    return res;
}
```

하지만 실패 함수를 어떻게든 구했다면, 문자열 매칭을 실제로 수행하는 것은 기존 방식과 똑같이 할 수 있습니다.

Match 함수에서 `fail[j-1]`에 접근할 일이 생겼다는 것은, $P$의 첫 $j$글자와 일치하는 부분 문자열이 $S$에 존재함을 의미합니다. 즉, $P_0, P_1, \cdots, P_{j-1}$과 일치하는 문자가 모두 $S$에서 등장합니다. 따라서 $P$의 각 문자가 $S$에서 등장하는 위치를 구해놓는다면, `fail[j-1]`이 필요할 때 lazy하게 실패 함수를 계산할 수 있습니다.

따라서 KMP 알고리즘을 이용해 이 문제를 해결할 수 있습니다. `while(j && s[i] != p[j])` 반복문이 종료되었을 때 `j > 0`이라면, 비교 없이 `if(s[i] == p[j])`의 결과를 할 수 있음에 유의해야 합니다.

```cpp
#include <bits/stdc++.h>
using namespace std;

bool Compare(int i, int j){
    cout << "? " << i + 1 << " " << j + 1 << endl;
    int res; cin >> res; return res;
}

vector<int> Solve(int n, int m){
    vector<int> fail(m, -1), pos(m, -1), res; fail[0] = 0;

    auto calc = [&](int x) -> void {
        static int i = 1, j = 0;
        if(fail[x] != -1) return;
        bool cmp = false;
        for(; i<=x; i++){
            while(j && !(cmp=Compare(pos[i], j))) j = fail[j-1];
            if(j == 0) cmp = Compare(pos[i], j);
            if(cmp) fail[i] = ++j; else fail[i] = 0;
        }
    };

    for(int i=0, j=0; i<n; i++){
        bool cmp = false;
        while(j && !(cmp=Compare(i, j))) calc(j-1), j = fail[j-1];
        if(j == 0) cmp = Compare(i, j);
        if(cmp){
            pos[j] = i;
            if(++j == m) res.push_back(i-m+1), calc(j-1), j = fail[j-1];
        }
    }
    return res;
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int n, m; cin >> n >> m;
    auto res = Solve(n, m);
    cout << "! " << res.size() << " ";
    for(auto i : res) cout << i + 1 << " ";
}
```

### I. Pumpkin Friends

이런 문제는 뒤에서부터 생각해서, 원소를 삭제하는 문제 대신 원소를 추가하는 문제라고 생각하는 것이 편합니다. 구간이나 컴포넌트를 쪼개는 것보다는 합치는 것이 쉽기 때문입니다. ([참고](https://oj.uz/problem/view/KOI16_treeM?locale=ko))

처음에 수열의 모든 원소가 제거된 상태에서 시작해서, 원소를 하나씩 추가하다가, 이번에 원소 $A_i$를 추가하는 상황이라고 생각합시다. $A_i$를 포함하는 구간 중 구간의 합이 $L$ 이상 $R$ 이하인 것의 개수를 세야 합니다.

$A_i$를 포함하는 가장 긴 구간을 $[l_i, r_i]$라고 합시다. 수열 $A$의 누적 합 배열을 $S$라고 하면, 이 문제는 $l_i \le x \le i \le y \le r_i$ 이면서 $L \le S_y - S_{x-1} \le R$ 인 순서쌍 $(x, y)$의 개수를 세는 문제라고 생각할 수 있습니다.

먼저, $[l_i, r_i]$를 구하는 방법부터 생각해 봅시다. 만약 $A_i$가 $P_i$번째로 추가되는 원소라면, $(l_i - 1)$은 $j < i$, $P_j > P_i$를 만족하는 가장 큰 $j$입니다. 마찬가지로 $(r_i+1)$은 $i < j$, $P_j > P_i$를 만족하는 가장 작은 $j$입니다. 따라서 monotone stack을 이용해 $O(N)$ 시간에 모두 구할 수 있습니다.

또는, 원소가 아직 추가되지 않은 위치들을 BBST 등으로 관리하면서, 원소를 추가할 때마다 $O(\log N)$ 시간에 $l_i$와 $r_i$를 찾고 자료구조를 갱신하면 $O(N \log N)$ 시간에 구할 수도 있습니다.

이제 조건을 만족하는 구간의 개수를 구하는 방법에 대해 알아봅시다. 만약 $x$가 고정되어 있다면, $L+S_{x-1} \le S_y \le R+S_{x-1}$을 만족하는 $y$의 개수를 구하는 것이므로 이분 탐색을 이용해 $O(\log N)$ 시간에 계산할 수 있습니다. 반대로 $y$가 고정되어 있다면, $S_y-R \le S_{x-1} \le S_y-R$을 만족하는 $x$의 개수를 구하는 것이므로 마찬가지로 $O(\log N)$ 시간에 계산할 수 있습니다.

그러므로 원소를 삽입할 때마다 $O(\min(i-l_i, r_i-i) \log \max(i-l_i, r_i-i))$ 시간에 $A_i$를 포함하면서 조건을 만족하는 구간의 개수를 셀 수 있고, 원소를 삽입할 때마다 두 개의 구간이 병합됩니다. 따라서 small to large의 원리를 이용하면 $\sum \min(i-l_i, r_i-i) \in O(N \log N)$임을 알 수 있고, 전체 시간 복잡도는 $O(N \log^2 N)$이 됩니다.

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

ll N, L, R, A[505050], B[505050], S[505050];

ll Count(int l, int r, ll lo, ll hi){
    return upper_bound(S+l, S+r+1, hi) - lower_bound(S+l, S+r+1, lo);
}

ll Solve(int x, int l, int r){
    ll res = 0;
    if(x - l < r - x){
        for(int i=l; i<=x; i++) res += Count(x, r, S[i-1]+L, S[i-1]+R);
    }
    else{
        for(int j=x; j<=r; j++) res += Count(l-1, x-1, S[j]-R, S[j]-L);
    }
    return res;
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    cin >> N >> L >> R;
    for(int i=1; i<=N; i++) cin >> A[i];
    for(int i=1; i<=N; i++) cin >> B[i];
    partial_sum(A+1, A+N+1, S+1);
    reverse(B+1, B+N+1);

    set<ll> empty;
    for(int i=0; i<=N+1; i++) empty.emplace_hint(empty.end(), i);

    vector<ll> res{0};
    for(int i=1; i<=N; i++){
        int x = B[i];
        auto it = empty.find(x);
        int l = *prev(it) + 1, r = *next(it) - 1;
        res.push_back(Solve(x, l, r));
        empty.erase(x);
    }

    partial_sum(res.begin(), res.end(), res.begin());
    reverse(res.begin(), res.end());
    for(auto i : res) cout << i << " ";
}
```

이 밖에도 lower_bound와 upper_bound 연산을 하는 값을 미리 모두 구할 수 있다는 점을 이용해 $O(N \log N)$ 시간에 푸는 방법, 그리고 조건을 만족하는 구간의 개수를 적절한 수식으로 계산해 $O(N)$ 시간에 푸는 방법 또한 존재합니다.

### E. Eating Candies

문제에서 다루는 조합 게임은 자신이 수를 둘 때마다 상태가 더 불리해지는 cold game입니다. cold game은 각 게임판의 유리도(점수)를 모두 더했을 때 그 값이 양수면 선공, 0이거나 음수면 후공이 이기게 됩니다. 각 문자열의 점수를 계산하는 방법에 대해 알아봅시다.

일반성을 잃지 않고 B가 선공이라고 생각합시다. 만약 문자열이 총 $x$개의 B만으로 이루어져 있다면, 선공이 방해받지 않고 $x$번 행동할 수 있습니다. 반대로 문자열이 총 $y$개의 R만으로 이루어져 있다면 후공이 $y$번 행동할 수 있습니다. 따라서 두 문자열의 점수를 각각 $x$와 $-y$로 정의합니다.

두 플레이어는 항상 문자열의 한쪽 끝에 있는 문자만 제거하므로, 주어진 문자열의 부분 문자열만 고려해도 됩니다. 어떤 부분 문자열 $s$에 대해, 선공이 한 번 행동해서 만들 수 있는 점수의 최댓값을 $b$, 후공이 한 번 행동해서 만들 수 있는 점수의 최솟값을 $r$이라고 하면, 부분 문자열 $s$의 점수는 $b+1, 0, r-1$의 중앙값이 됩니다.

따라서 각 문자열에서 구간 DP를 이용해 점수를 구하면 $O(\sum \vert s\vert^2)$ 시간에 문제를 해결할 수 있습니다.

선형 시간에 해결하는 방법도 존재합니다.

```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int nINF = 0xc0c0c0c0;
constexpr int pINF = 0x3f3f3f3f;

int f(int a, int b, int c){
    if(a > b) swap(a, b);
    if(a > c) swap(a, c);
    if(b > c) swap(b, c);
    return b;
}

int Solve(string s, char c){
    if(c == 'R') for(auto &i : s) i = i != 'B' ? 'B' : 'R';

    int n = s.size();
    vector<vector<int>> dp(n, vector<int>(n));
    for(int i=0; i<n; i++) dp[i][i] = s[i] == 'B' ? 1 : -1;
    for(int d=1; d<n; d++){
        for(int i=0, j=d; j<n; i++, j++){
            int blue = nINF, red = pINF;
            if(s[i] == 'B' && dp[i+1][j] != nINF) blue = max(blue, dp[i+1][j]+1);
            if(s[j] == 'B' && dp[i][j-1] != nINF) blue = max(blue, dp[i][j-1]+1);
            if(s[i] == 'R' && dp[i+1][j] != pINF) red = min(red, dp[i+1][j]-1);
            if(s[j] == 'R' && dp[i][j-1] != pINF) red = min(red, dp[i][j-1]-1);
            dp[i][j] = f(blue, red, 0);
        }
    }
    return dp[0][n-1];
}

int main(){
    ios_base::sync_with_stdio(false); cin.tie(nullptr);
    int N, R=0; char C; cin >> N >> C;
    for(int i=0; i<N; i++){
        string s; cin >> s;
        R += Solve(s, C);
    }

    if(C == 'B') cout << (R > 0 ? 'B' : 'R');
    else cout << (R > 0 ? 'R' : 'B');
}
```
