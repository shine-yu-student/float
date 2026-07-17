---
{"dg-publish":true,"permalink":"/Luogu/Luogu P11830 Solution/","dg-note-properties":{}}
---

## 前言
明天，就是终点了。
我的记忆将会被重置。
经过 4 个月“腐蚀”的我，还能拥有现在的实力吗？
又或者，我在已经在“那场挑战”中败下阵来，失去自身的灵魂，成为流水线上的一个零件？
## 题解
按照 NOIP T1 的经验教训，我们要在可行的前提下尽可能将解法想简单。
实际上，容易发现，为了让一个数 $x$ 有更大的概率成为中位数，我们需要干的事情是：
1. 让 $x$ 尽可能多。
2. 让小于 $x$ 的数和大于 $x$ 的数的**数量差**尽可能小。

而由于 $n$ 的值域（$2\times 10^5$）相对于 $x$ 的值域（$10^9$）很小，不难发现在很多个 $x$ 中，不同信息对于对应情况的贡献都是一样的。
那么可以离散化处理。
对于每个数 $x$，可以通过以下方式判断其是否可以成为中位数：
1. 令所有信息中值域区间包含 $x$ 的全部变成 $x$，数量取最大值。
2. 记录所有只能小于/大于 $x$ 的数的数量的最小值和最大值。
3. 进行分类讨论：
	1. 若 $x$ 的出现次数为 $0$，则 $x$ 无法成为最大值。
	2. 计算小于/大于 $x$ 的数的数量的最小差值 $t$。**此处的 $t$ 的计算方式是小于减去大于**。
	3. 若 $t=0$，则 $x$ 可以成为中位数。
	4. 若 $t<0$，且 $-t$ 小于 $x$ 的最大出现次数，则 $x$ 可以成为中位数。
	5. 若 $t>0$，且 $t$ **不大于** $x$ 的最大出现次数，则 $x$ 可以成为中位数。
	6. 否则，$x$ 不能成为中位数。

上述过程中用到的各个变量显然可以在将 $x$ 增大的过程中动态维护，所以，该解法的时间复杂度为 $O(n\log n)$。
**注意：下方代码在分类讨论第 $2$ 到 $5$ 步的处理略有不同**。
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
#include <cctype>
#include <cstdio>
#include <utility>
#include <vector>
using namespace std;
const int N = 4e5 + 10;
int c;
int n, v[N], idx, res;
struct st
{
	int nm_l, nm_r, va_l, va_r;
};
st dtb[N];
using pii = pair<int, int>;
vector<pii> cid[N];
using ll = long long;
ll lft_l, lft_r, rgt_l, rgt_r, curv;
template <typename _Tp> inline void read(_Tp &x)
{
	static char ch;
	while (ch = getchar(), !isdigit(ch))
		;
	x = (ch ^ 48);
	while (ch = getchar(), isdigit(ch))
		x = (x << 3) + (x << 1) + (ch ^ 48);
}
template <typename _Tp, typename... _Args> inline void read(_Tp &x, _Args &...args)
{
	read(x);
	read(args...);
}
void init_global()
{
}
void init_local()
{
	res = 0;
	read(n);
	for (int i = 1; i <= idx; i++)
		cid[i].clear();
	idx = 0;
	for (int i = 1; i <= n; i++)
	{
		read(dtb[i].nm_l, dtb[i].nm_r, dtb[i].va_l, dtb[i].va_r);
		v[++idx] = dtb[i].va_l;
		v[++idx] = dtb[i].va_r + 1;
	}
}
bool check()
{
	if (!curv)
		return false;
	if (!(lft_r < rgt_l or rgt_r < lft_l))
		return true;
	if (lft_l + curv >= rgt_r and lft_l < ((lft_l + curv + rgt_r + 1) >> 1))
		return true;
	if (lft_r < curv + rgt_l and rgt_l <= lft_r + curv + rgt_l - ((lft_r + curv + rgt_l + 1) >> 1))
		return true;
	return false;
}
void run()
{
	lft_l = lft_r = rgt_l = rgt_r = curv = 0;
	sort(v + 1, v + idx + 1);
	idx = unique(v + 1, v + idx + 1) - v - 1;
	for (int i = 1; i <= n; i++)
	{
		dtb[i].va_l = lower_bound(v + 1, v + idx + 1, dtb[i].va_l) - v;
		dtb[i].va_r = lower_bound(v + 1, v + idx + 1, dtb[i].va_r + 1) - v;
		cid[dtb[i].va_l].emplace_back(1, i);
		cid[dtb[i].va_r].emplace_back(-1, i);
	}
	for (int i = 1; i <= n; i++)
	{
		rgt_l += dtb[i].nm_l, rgt_r += dtb[i].nm_r;
	}
	for (int i = 1; i <= idx; i++)
	{
		if (check())
			res += v[i] - v[i - 1];
		for (auto &j : cid[i])
		{
			if (~j.first)
			{
				rgt_l -= dtb[j.second].nm_l, rgt_r -= dtb[j.second].nm_r;
				curv += dtb[j.second].nm_r;
				continue;
			}
			curv -= dtb[j.second].nm_r;
			lft_l += dtb[j.second].nm_l, lft_r += dtb[j.second].nm_r;
		}
	}
	printf("%d\n", res);
}
int main()
{
#ifdef Redshift_Debug
	auto st = chrono::high_resolution_clock::now();
#endif
	int T = 1;
	read(c, T);
	init_global();
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
