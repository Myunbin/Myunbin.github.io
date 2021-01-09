
---
title: "[BOJ백준 2618] 경찰차"
categories:
- PS
---

- 사용한 알고리즘 : dp
- 시간 복잡도 : $$O\left( N^{2} \right)$$

핵심은 경찰차의 위치가 자기가 해결한 마지막 사건이라는 것이다.
원래 $$go(i, k) = $$"$$i$$번째 사건을 $$k$$번째 경찰차가 해결했을 때 두 경찰차의 이동거리 합의 최소" 로 정의했다가,
그럼 $$i$$번째 이전에는 어느 경찰차가 해결했는 지에 대한 정보를 담고있지 않아 버리고 다음과 같이 재정의했다.

핵심은 두 경찰차가 마지막으로 해결한 사건이다.
$$go(i, j) = $$"$$1$$번 경찰차가 $$i$$번, $$2$$번 경찰차가 $$j$$번을 마지막으로 해결했을 때 두 차의 이동거리 합의 최소값"
다음 사건 번호는 $$idx = \max(i, j) + 1$$ 이다.
각각 i번째와 j번째 사건이 일어난 위치와 다음 사건 위치의 거리를 dist_i, dist_j라고 하겠다.
그럼 idx번째 사건을 1번이 해결하는 경우 2번이 해결하는 경우 두 가지가 존재하므로,

$$go(i, j) = \min(go(idx, j) + dist_i, go(i, idx) + dist_j)$$ 로 점화식 작성이 가능하다.
이때 $$dist_{i, j}$$는 각각의 경찰차의 위치와 다음 사건의 거리다.
첫 사건을 해결하기 전은 예외로 빼준다.

```c++
#include <bits/stdc++.h>
using namespace std;
typedef pair<int, int> pii;
const int MAX_N = 1001;
int n, w;
int dp[MAX_N][MAX_N];
vector<pii> a;

int go(int i, int j) {
	if (i == w || j == w) return 0;

	int &ret = dp[i][j];
	if (ret != -1) return ret;
	
	int idx = max(i, j) + 1;
	pii nxt = a[idx];
	
	int dist_i = abs(a[i].first - nxt.first) + abs(a[i].second - nxt.second);
	int dist_j = abs(a[j].first - nxt.first) + abs(a[j].second - nxt.second);
	if (i == 0) dist_i = nxt.first + nxt.second - 2;
	if (j == 0) dist_j = 2 * n - (nxt.first + nxt.second);
	
	return ret = min(go(idx, j) + dist_i, go(i, idx) + dist_j);
}

void trackAns(int i, int j) {
	if (i == w || j == w) return;
	
	int idx = max(i, j) + 1;
	pii nxt = a[idx];
	
	int dist_i = abs(a[i].first - nxt.first) + abs(a[i].second - nxt.second);
	int dist_j = abs(a[j].first - nxt.first) + abs(a[j].second - nxt.second);
	if (i == 0) dist_i = nxt.first + nxt.second - 2;
	if (j == 0) dist_j = 2 * n - (nxt.first + nxt.second);
	
	if (go(idx, j) + dist_i < go(i, idx) + dist_j) {
		cout << 1 << '\n';
		trackAns(idx, j);
	}
	else {
		cout << 2 << '\n';
		trackAns(i, idx);
	}
}

int main() {
	ios_base::sync_with_stdio(false);
  	cin.tie(nullptr);
	
	memset(dp, -1, sizeof(dp));
	cin >> n >> w;
	a.resize(w + 1);
	for (int i = 1; i <= w; i++) 
		cin >> a[i].first >> a[i].second;
	
	cout << go(0, 0) << '\n';
	trackAns(0, 0);
	return 0;
}

```
