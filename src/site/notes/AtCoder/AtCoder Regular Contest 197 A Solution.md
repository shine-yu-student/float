---
{"dg-publish":true,"permalink":"/AtCoder/AtCoder Regular Contest 197 A Solution/"}
---

## 题意
已经非常简洁了，建议自己看题面。
## 题解
显然，对于任意满足题面的替换方法，行进至第 $i$ 步时，$x+y=i+2$。不难想到对于每个 $x+y$ 确定可以到达的节点个数。
而方案个数，显然就是在前 $i$ 个字符中的问号位置填上 D 或 R 使得最终存在将后面的问号补上来到达 $(n,m)$ 的方案数。
做一些简单的数学运算和分类讨论即可，时间复杂度 $O(n+m)$。
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
#include <string>
using namespace std;
const int N = 1e5 + 10;
string s;
int n, m, dst;
using ll = long long;
ll res;
void init_global()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);
}
void init_local()
{
	cin >> n >> m >> s;
	for (auto &i : s)
	{
		n -= (i == 'D');
		m -= (i == 'R');
	}
	res = dst = 1;
}
void run()
{
	for (auto &i : s)
	{
		// cout << res << '\n';
		dst += (i == '?');
		if (dst <= n and dst <= m)
		{
			res += dst;
			// cout << " 1\n";
			continue;
		}
		// cout << n << ' ' << m << ' ' << dst << '\n';
		if (dst > n and dst > m)
		{
			if (n + m - dst <= n and n + m - dst + 1 <= m)
			{
				res += n + m - dst;
				continue;
			}
			res += min(n, m);
			continue;
		}
		res += min(n, m);
	}
	cout << res << '\n';
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