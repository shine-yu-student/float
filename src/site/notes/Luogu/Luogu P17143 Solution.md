---
{"dg-publish":true,"permalink":"/Luogu/Luogu P17143 Solution/","dg-note-properties":{}}
---

原数据下的题目：好题。
QOJ Extra Test：nm。
由以上文字可知，以下解法无法通过 QOJ Extra Test \#7，只能获得 97 分。可以通过原数据。
[洛谷题目链接](https://www.luogu.com.cn/problem/P17143)。

---
中位数可行性问题的一个经典套路是转为大小关系的 1/-1 序列。根据本题定义，对于任意数字 $x$，将所有 $a_i\ge x$ 映射为 $1$，$a_i<x$ 映射为 $-1$，则一个内部元素和为非负数的子区间的中位数不小于 $x$。
显然，对于给出的 $k$，需要构造 $\lceil\frac{k}{2}\rceil$ 个满足条件的子区间，同时需要保证去除这些子区间后可以在剩下的部分中构造出恰好 $\lfloor\frac{k}{2}\rfloor$ 个区间。
对于 $k$ 较小的情况，可以利用离散化/树状数组辅助 DP 在 $O(nk\log n)$ 的时间内解决问题。以下均认为 $k>5$。

---
显然，情况合法的一个必要条件是序列中至少存在 $\lceil\frac{k}{2}\rceil$ 个 $1$。根据直觉，在尽可能不浪费 $1$ 的原则下，选出 $\lceil\frac{k}{2}\rceil$ 个 $1$ 并让它们分别成段。根据具体情况，删去选中的 $1$ 后，剩下的序列可能最多形成 $\lceil\frac{k}{2}\rceil+1$ 个连续段，可能不符合要求，因此需要一些判断。
若 $k\equiv 0\pmod 2$，则删去后形成 $\lceil\frac{k}{2}\rceil+1$ 个连续段，当且仅当 $1$ 之间两两不相邻且不位于开头和结尾。然而，一个 $1$ 带上一个与其相邻的 $0$ 不影响结果，因此两个间隔不超过 $3$ 的 $1$ 可以相连，与开头或结尾距离不超过 $2$ 的也可以消掉一个不应存在的段。只需要判断这些即可，因为形成 $\lceil\frac{k}{2}\rceil$ 个连续段是可以被接受的。
若 $k\equiv 1\pmod 2$，则删去后形成 $\lceil\frac{k}{2}\rceil+1$ 个连续段，当且仅当 $1$ 之间两两不相邻且不位于开头和结尾。此时最多允许 $\lceil\frac{k}{2}\rceil-1$ 个连续段存在，因此需要消去两处间隙。处理两种情况：两对 $1$ 分别距离不超过 $3$（将与开头/结尾距离不超过 $2$ 的情况同时考虑），或者三个 $1$ 位于 $6$ 格之内且中间的 $1$ 与两侧 $1$ 的间隔均不超过 $3$。在 $k>5$ 的情况下只需考虑这两种情况。
由此本题解决。

---
```c++
#include "median.h"
#include <algorithm>
#include <cstring>
#include <iostream>

const int N = 1e6 + 10;

int ta[N], prv[N], tn, tk, buc[N], dst[N], didx, stk[N], sidx, l, r, mid, dp[N], tr[N << 1];

void init(int c, int t)
{
	return;
}

inline void update(int x, int v)
{
	while (x <= 2 * tn)
		tr[x] = std::max(tr[x], v), x += (x & -x);
}

inline int query(int x)
{
	int res = -0x3f3f3f3f;
	while (x)
		res = std::max(res, tr[x]), x -= (x & -x);
	return res;
}

bool check_force(int x)
{
	// std::cout << "guess " << x << '\n';
	memset(dp + 1, -0x3f, tn << 2);
	for (int i = 1; i <= tn; i++)
		prv[i] = prv[i - 1] + (ta[i] >= x ? 1 : -1);
	for (int i = 1; i <= tk; i++)
	{
		memset(tr + 1, -0x3f, tn << 3);
		update(prv[i - 1] + tn, dp[i - 1]);
		for (int j = i, buf, cmx = dp[i - 1]; j <= tn; j++)
		{
			buf = std::max(cmx, query(prv[j] + tn) + 1);
			cmx = std::max(cmx, dp[j]);
			update(prv[j] + tn, dp[j]);
			dp[j] = buf;
		}
	}
	return dp[tn] * 2 >= tk;
}

bool check(int x)
{
	sidx = 0;
	// std::cout << "guess " << x << '\n';
	for (int i = 1; i <= tn; i++)
	{
		if (ta[i] >= x)
			stk[++sidx] = i;
	}
	// std::cout << '\n';
	if (sidx * 2 < tk)
		return false;
	stk[sidx + 1] = tn + 2;
	int lft = 0, rgt = 0;
	for (int i = 1; i <= sidx + 1; i++)
	{
		if (stk[i] - stk[i - 1] <= 3)
		{
			lft = i;
			break;
		}
	}
	for (int i = sidx; ~i; i--)
	{
		if (stk[i + 1] - stk[i] <= 3)
		{
			rgt = i;
			break;
		}
	}
	if (!lft)
		return false;
	if (tk & 1 ^ 1)
		return true;
	if (lft == rgt + 1)
		return false;
	if (rgt > lft)
		return true;
	return stk[lft + 1] - stk[lft - 1] < 6;
}

int median(int n, int k, std::vector<int> a)
{
	stk[0] = -1;
	tk = k;
	for (int i = 0; i < n; i++)
	{
		ta[i + 1] = a[i];
	}
	tn = n;
	for (int i = 1; i <= n; i++)
		dst[i] = ta[i];
	std::sort(dst + 1, dst + n + 1);
	didx = std::unique(dst + 1, dst + n + 1) - dst - 1;
	// 0010001100010100100
	l = 1, r = didx + 1;
	while (l < r)
	{
		mid = (l + r) >> 1;
		if (tk < 7 ? check_force(dst[mid]) : check(dst[mid]))
			l = mid + 1;
		else
			r = mid;
	}
	return dst[l - 1];
}
```
