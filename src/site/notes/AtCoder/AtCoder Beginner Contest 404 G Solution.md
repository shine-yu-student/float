---
{"dg-publish":true,"permalink":"/at-coder/at-coder-beginner-contest-404-g-solution/","dg-note-properties":{}}
---

## 背景
两周不打 ABC，码力急剧下降。不来补一篇题解不行了。而且这是线性规划+图论建模的好题，不能错过。
## 题解
简要描述本题题意：
构造长度为 $n$ 的正整数序列 $a$ 满足 $\forall 1\le i\le m, \sum_{j=l_i}^{r_i}a_i=s_i$。判断该序列是否能够构造，若可以，输出 $\min\sum_{i=1}^n a_i$。
构造序列 $a$ 的前缀和序列 $b$，满足 $b_i=\sum_{j=1}^i a_i$。不难发现其可以转换为线性规划问题的基本模型，而该模型可以被描述为一张带权有向图。该模型的详细定义可以参考 AtCoder 官方题解界面。
具体的说，对于每个节点 $i$，向 $i-1$ 连一条权值为 $-1$ 的边；对于每个限制 $(l,r,s)$，从 $r$ 到 $l-1$ 连权值为 $-s$ 的边，而向 $l-1$ 到 $r$ 连权值为 $s$ 的边。通过这个限制，你会发现无解的情况极易判断——只需要判断是否存在负环即可。具体的，由上面的连边方式可以得知正权边只会从前向后连，并且唯一对应一条负权边。若出现负环，则可以证明一定出现了相同区间内所需的权值总和超出了某些限制的容量。
最终的答案即为节点 $n$ 到 $0$ 的最短路长度**的相反数**。
由于存在负权边，Dijkstra 算法得到的结果无法保证正确性，因此只能使用 Bellman-Ford 算法，时间复杂度 $O(nm)$。
该思想一定程度上与网络流中的“退流”操作吻合，因此也可以说线性规划与网络流是相通的。
## 代码
```c++
// #define Redshift_Debug
#ifdef Redshift_Debug
#define debug(...) fprintf(stderr, __VA_ARGS__)
#include <chrono>
#else
#define debug(...)
#endif
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
#include <tuple>
#include <utility>
#include <vector>
using namespace std;
const int N = 1.2e4 + 10;
int n, m, pvb, idx, tag;
using ll = long long;
ll dis[N];
using pii = pair<int, int>;
using t3i = tuple<int, int, int>;
t3i edge[N];
void run()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);
	cin >> n >> m;
	memset(dis, 0x3f, n << 3);
	for (int i = 1; i <= n; i++)
	{
		edge[++idx] = {i, i - 1, -1};
	}
	for (int i = 1, x, y, z; i <= m; i++)
	{
		cin >> x >> y >> z;
		edge[++idx] = {x - 1, y, z};
		edge[++idx] = {y, x - 1, -z};
	}
	for (int k = 1; k <= ((n + m) << 1) + 5; k++)
	{
		tag = 0;
		for (int j = 1; j <= idx; j++)
		{
			auto &[x, y, v] = edge[j];
			if (dis[y] <= dis[x] + v)
				continue;
			dis[y] = dis[x] + v;
			tag = 1;
		}
		if (!tag)
			break;
	}
	if (tag)
	{
		cout << -1 << '\n';
		return;
	}
	cout << -dis[0] << '\n';
}
int main()
{
#ifdef Redshift_Debug
	auto st = chrono::high_resolution_clock::now();
#endif
	run();
#ifdef Redshift_Debug
	auto ed = chrono::high_resolution_clock::now();
	fprintf(stderr, "%.9lf\n", (ed - st).count() / 1e9);
#endif
}
```