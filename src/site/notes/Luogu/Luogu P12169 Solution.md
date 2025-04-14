---
{"dg-publish":true,"permalink":"/Luogu/Luogu P12169 Solution/"}
---

## 背景
看到小波发的讨论来做这题。
然后谔谔。
建议删除分治标签，改为单调栈和并查集。
## 题解
首先注意到只要有大的在前小的在后他们就是双向连通的。这点启发我们使用并查集和单调栈解决问题。
那么显而易见地，这个单调栈是递增形态的。但是你会发现一个问题。
那就是如果你在把所有大过它的都 merge 进去之后只留下这个位置就会发现后面本来能被 merge 的位置 merge 不进去了。
所以正确方法是在弹掉所有能 merge 的元素之后重新把最大的元素压进去。这样是最优的。
然后就做完了。
时间复杂度 $O(nm\alpha(nm))$。
很难想象当时蓝桥杯出题组给这题定 5s 限时时的心理状态。
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
#include <iomanip>
#include <iostream>
using namespace std;
const int N = 1e6 + 10;
int n, m, f[N], a[N], stk[N], ps;
constexpr int rmp(int x, int y)
{
	return x * m + y;
}
inline int find(int x)
{
	return x == f[x] ? x : f[x] = find(f[x]);
}
inline void merge(int x, int y)
{
	x = find(x), y = find(y);
	if (x == y)
		return;
	if (a[x] < a[y])
		swap(x, y);
	f[y] = x;
}
using ll = long long;
ll res;
void run()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);
	cin >> n >> m;
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
		{
			cin >> a[rmp(i, j)];
			f[rmp(i, j)] = rmp(i, j);
		}
	}
	for (int i = 0; i < n; i++)
	{
		ps = 0;
		for (int j = 0, ori = 0; j < m; j++)
		{
			ori = ps;
			stk[ps + 1] = rmp(i, j);
			while (ps and a[stk[ps]] > a[rmp(i, j)])
				merge(stk[ps], rmp(i, j)), ps--;
			if (ps != ori)
			{
				stk[++ps] = stk[ori];
				continue;
			}
			else
				ps++;
		}
	}
	for (int j = 0; j < m; j++)
	{
		ps = 0;
		for (int i = 0, ori = 0; i < n; i++)
		{
			ori = ps;
			stk[ps + 1] = rmp(i, j);
			while (ps and a[stk[ps]] > a[rmp(i, j)])
				merge(stk[ps], rmp(i, j)), ps--;
			if (ps != ori)
			{
				stk[++ps] = stk[ori];
				continue;
			}
			else
				ps++;
		}
	}
	for (int i = 0; i < n * m; i++)
	{
		res += a[find(i)];
	}
	cout << fixed << setprecision(6) << 1.0 * res / (n * m) << '\n';
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