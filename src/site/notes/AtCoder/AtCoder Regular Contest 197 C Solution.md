---
{"dg-publish":true,"permalink":"/at-coder/at-coder-regular-contest-197-c-solution/","dg-note-properties":{}}
---

## 题意
定义集合 $S=\mathbb{N}^+$，$T$ 次操作，每次从 $S$ 中删去 $a$ 的倍数并求 $S$ 中第 $b$ 小值。
**注意：$1\le b\le 10^5$。**
## 题解
数据范围十分重要，因为它告诉了我们答案的上界是第 $(2\times 10^5)$ 个质数，也就是 $2750159$。
由此，我们可以直接使用树状数组维护 $S$ 中有哪些数仍然留存。若碰到没有被删除过的 $a$ 就直接 $O(\frac{V}{a}\log V)$  修改。查询的时候直接在树状数组上二分即可。
时间复杂度 $O(V\log V)$，其中 $V=2750159$。
## 代码
```c++
// #define Redshift_Debug
#ifdef Redshift_Debug
#define debug(...) fprintf(stderr, __VA_ARGS__)
#include <chrono>
#else
#define debug(...)
#endif
#include <cstdio>
#include <iostream>

using namespace std;
const int N = 2e5 + 10, M = 3e6 + 10;
int pr[N], idx, tr[M], a, b;
bool vis[M], rel[M];
void update(int x, int v)
{
	while (x < M)
		tr[x] += v, x += (x & -x);
}
int trv(int tar)
{
	int res = 0, cur = 0;
	for (int i = 21; ~i; i--)
	{
		if ((res ^ (1 << i)) >= M or cur + tr[res ^ (1 << i)] >= tar)
			continue;
		res ^= (1 << i);
		cur += tr[res];
	}
	return res + 1;
}
void init_global()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);
	for (int i = 1; i < M; i++)
		tr[i] = (i & -i);
}
void init_local()
{
	cin >> a >> b;
}
void run()
{
	if (a >= M or rel[a])
	{
		cout << trv(b) << '\n';
		return;
	}
	for (int i = a; i < M; i += a)
	{
		if (rel[i])
			continue;
		rel[i] = true;
		update(i, -1);
	}
	cout << trv(b) << '\n';
}
int main()
{
#ifdef Redshift_Debug
	auto st = chrono::high_resolution_clock::now();
#endif
	int T = 1;
	init_global();
	cin >> T;
	while (T--)
	{
		init_local();
		run();
	}
#ifdef Redshift_Debug
	auto ed = chrono::high_resolution_clock::now();
	fprintf(stderr, "%.9lf\n", (ed - st).count() / 1e9);
#endif
}
```