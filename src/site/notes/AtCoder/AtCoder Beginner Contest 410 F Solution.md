---
{"dg-publish":true,"permalink":"/at-coder/at-coder-beginner-contest-410-f-solution/","dg-note-properties":{}}
---

## 前言
这个相比于 E 来说就是纯纯的式。
## 题解
矩形内部两元素数量相等，一眼正负号矩阵，直接转换为 $1$ 和 $-1$ 计算前缀和，然后搞点神奇数据结构把时间控制在 $O(HW\log HW)$ 之内？不。大错特错。不要被看似高级的想法冲昏了头脑。你要仔细思考一下，如果这道题想考你的暴力实现呢？
题目保证 $\sum HW\le 3\times 10^5$，不妨假设最坏情况，即 $HW=3\times 10^5$，有没有一个运算量合理且容易实现的算法？有的兄弟，有的，我们直接~~钦定~~选定 $H$ 边或者 $W$ 边暴力地对于每个区间计算满足有一条边就是这个区间的矩形的数量即可。
啊？那不会被卡到 $O((HW)^2)$ 吗？不不不。你要思考一下，随便选定显然是愚蠢的。我们要选短的那一边。这样计算量是 $O(HW\min(H,W))$。显然 $\min(H,W)$ 的最大值是 $\sqrt{HW}$。那么时间复杂度就是 $O(HW\sqrt{HW})$。
对于每个区间，维护一个桶进行操作即可。桶需要用懒标记清空。不要想着用 STL，那玩意不是巨大常数就是带个 $\log$，会直接让你的代码过不去。
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
#include <vector>
using namespace std;
const int N = 3e5 + 10;
int n, m, tidx;
vector<vector<int>> a;
int tbuc[N << 1], *buc = tbuc + N;
int tvis[N << 1], *vis = tvis + N;
char ch;
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
	res = 0;
	cin >> n >> m;
	if (n >= m)
	{
		a.assign(n, vector<int>(m + 1));
		for (auto &i : a)
		{
			for (int j = 1; j <= m; j++)
			{
				cin >> ch;
				if (ch == '#')
					i[j] = 1;
				else
					i[j] = -1;
				i[j] += i[j - 1];
			}
		}
	}
	else
	{
		swap(n, m);
		a.assign(n, vector<int>(m + 1));
		for (int j = 1; j <= m; j++)
		{
			for (int i = 0; i < n; i++)
			{
				cin >> ch;
				if (ch == '#')
					a[i][j] = 1;
				else
					a[i][j] = -1;
				a[i][j] += a[i][j - 1];
			}
		}
	}
}
void run()
{
	for (int i = 0; i < m; i++)
	{
		for (int j = i + 1; j <= m; j++)
		{
			vis[0] = tidx;
			buc[0] = 1;
			for (int k = 0, sm = 0; k < n; k++)
			{
				sm += a[k][j] - a[k][i];
				if (vis[sm] != tidx)
					vis[sm] = tidx, buc[sm] = 0;
				res += buc[sm];
				buc[sm]++;
			}
			tidx++;
		}
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