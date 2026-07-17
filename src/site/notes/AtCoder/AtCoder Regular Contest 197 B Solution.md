---
{"dg-publish":true,"permalink":"/AtCoder/AtCoder Regular Contest 197 B Solution/","dg-note-properties":{}}
---

## 题意
找到一个长度为 $n$ 的序列 $a$ 中的一个子序列使得其内部严格大于其平均值的元素数量最大，输出这个数量。
## 题解
将 $a$ 升序排序后会发现选取一个前缀总是最优的，因为对于任何序列，往序列里面加小于平均数的数字不会减小符合条件的元素数量。
然后就做完了，排序完之后用双指针扫一下就可以了。
时间复杂度 $O(n\log n)$。
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
#include <iostream>
using namespace std;
const int N = 2e5 + 10;
int n, a[N], res;
using ll = long long;
ll sm;
void init_global()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);
}
void init_local()
{
	cin >> n;
	for (int i = 1; i <= n; i++)
		cin >> a[i];
	sm = 0;
	sort(a + 1, a + n + 1);
}
void run()
{
	res = 0;
	for (int i = 1, j = 1; i <= n; i++)
	{
		sm += a[i];
		while (j <= i and 1ll * a[j] * i <= sm)
			j++;
		res = max(res, i - j + 1);
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