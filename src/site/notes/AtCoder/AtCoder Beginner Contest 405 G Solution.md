---
{"dg-publish":true,"permalink":"/AtCoder/AtCoder Beginner Contest 405 G Solution/","dg-note-properties":{}}
---

## 背景
世界名画之全校只有我一个没有 AK 本场比赛且我是唯一一个掉分人。
破防了，于是写一篇题解。
## 题解
考虑如下问题。
> 序列 $a$ 中有 $k$ 种数，其中第 $i$ 种有 $c_i$ 个。求将其随机重排后能得到的本质不同的序列数量。

这个问题的组合意义答案非常显然。令 $n=\sum_{i=1}^k c_i$，则答案为
$$
\binom{n}{c_1}\binom{n-c_1}{c_2}\binom{n-c_1-c_2}{c_3}\dots\binom{n-\sum_{i=1}^{k-1}c_i}{c_k}
$$
但是这个东西是不好动态维护的。考虑简化。
由于
$$
\binom{n}{m}=\frac{n!}{m!(n-m)!}
$$
所以上式显然可以简化为
$$
\frac{n!}{\prod_{i=1}^kc_i!}
$$
这个东西就很好维护了。但是，问题来了。
它是区间，还是带权值的区间查询。难道要上可持久化线段树？不行。他干不了这件事。
**以上就是我赛时直到比赛结束的想法**。

---
这是一个悲伤的故事，因为我当时完全忘记了莫队算法这个东西。
这里简单复习一下。

---
莫队算法指的是一种易于实现的区间查询排列思想，其可以大幅优化一类可以高效对查询区间进行单位长度更新并快速更新答案的问题。显然，本题属于此类。
对于区间序列 $(l_1,r_1),(l_2,r_2),\dots,(l_q,r_q)$，莫队算法通过以下方式进行排序：
1. 若 $\lfloor\sqrt{l_x}\rfloor\neq\lfloor\sqrt{l_y}\rfloor$，则将 $\lfloor{\sqrt{l}}\rfloor$ 小的排在前面。
2. 否则，将 $r$ 小的排在前面。
令所有区间满足 $1\le l\le r\le n$，设单位长度扩展/收缩的时间复杂度为 $O(\text{op})$。显然，由于我们将序列分成了 $\lfloor\sqrt n\rfloor$ 组，则对于每一组，左端点的移动次数最多为 $\sqrt n\times\sqrt n=n$，而右段点从小到大覆盖剩余位置，移动次数最多为 $n$。由此，我们证明了莫队算法的时间复杂度为 $O(\text{op}\times\sum_{i=1}^{q-1}|l_i-l_{i+1}|+|r_i-r_{i+1}|)=O(n\sqrt n\times \text{op})$。

---
在本题上使用莫队算法，维护两个序列 $s_i$ 和 $p_i$，分别表示数 $i$ 在当前区间的出现次数和其阶乘。对于查询的 $x$，答案为 $\frac{(\sum_{i=1}^{x-1}s_i)!}{\prod_{i=1}^{x-1}p_i}$。在实现时，为了提高效率，我们直接存储 $p$ 在模 $998244353$ 意义下的逆元。由于每个数的逆元可以在 $O(n)$ 内全部完成计算，所以这个方法可以直接省去大量除法运算。看到前缀和，很多人，包括我，会直接想到树状数组。我们不妨分析一下这么做的时间复杂度。
在使用树状数组时，$\text{op}=\log n$。根据上面的分析，转移区间的总时间复杂度为 $O(q\sqrt n\log n)$。而对于询问，每次询问只需要在树状数组上进行一次查询，时间复杂度为 $O(q\log n)$。由于 $n,q\le 2.5\times 10^5$，这显然是不够的。在本题的数据栏内你甚至可以看到它明晃晃地写着 `anti_fenwick`。注意到查询时间复杂度远小于区间转移，我们需要进行平衡。
具体的，发现问题出在 $\text{op}$ 上。由此，我们引入新的思想：数论分块。
具体的，额外设 $s'_i=\sum_{\lfloor\sqrt j\rfloor=i} s_i$，$p'_i=\prod_{\lfloor\sqrt j\rfloor=i} p_i$。因为每个位置的根号分组确定，所以通过这个方法，我们将 $\text{op}$ 降为了 $O(1)$，区间转移时间复杂度变为 $O(n\sqrt n)$。那么，查询呢？和树状数组不同，这次，我们直接将前缀的整块化为一个变量，查询时直接累加，时间复杂度为 $O(\sqrt n)$。于是查询时间复杂度为 $O(q\sqrt n)$。于是我们惊奇地发现，经过上面的平衡，时间复杂度变为 $O((n+q)\sqrt n)$，可以通过！
但是，到了这一步，还没结束。
为了优化常数，我们提出以下优化：
> 在莫队排序过程中，两个区间左端点分组相同的情况下，若 $\lfloor{\sqrt{l}}\rfloor$ 为偶数，则将 $r$ 小的排在前面，否则将 $r$ 大的排在前面。

经过上述优化后，我们可以发现，我们省去了一半的收缩区间开销。在本题，这个优化可以将程序运行时间缩短至原来的 $\frac{4}{5}$。
同时，通过调整块长至（上取整意义下）最接近的 $2$ 的幂，程序的常数会有微小的下降。
由此，本题完成。
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
#include <cmath>
#include <cstdio>
#include <iostream>
using namespace std;
const int N = 2.5e5 + 10, mod = 998244353, B = 5e2 + 50;
#define F(x) ((x) % mod)
int n, q, a[N], buc[N], bucr[N], fac[N], inv[N], trs[B], trp[B], brs[N], res[N], blks, cl = 1, cr;
struct st
{
	int l, r, x, id;
	bool operator<(const st &ele) const
	{
		if (brs[l] != brs[ele.l])
			return brs[l] < brs[ele.l];
		if (r != ele.r)
			return (r < ele.r) ^ (brs[l] & 1 ^ 1);
		if (x != ele.x)
			return x < ele.x;
		return id < ele.id;
	}
} qry[N];
inline int fsp(int x, int y)
{
	int res = 1;
	while (y)
	{
		if (y & 1)
			res = F(1ll * res * x);
		x = F(1ll * x * x);
		y >>= 1;
	}
	return res;
}
inline void updates(int x, int v)
{
	buc[x] += v, trs[brs[x]] += v;
}
inline void updatep(int x, int v)
{
	bucr[x] = F(1ll * bucr[x] * v), trp[brs[x]] = F(1ll * trp[brs[x]] * v);
}
inline int querys(int x)
{
	int cp = 0, crp = 0, res = 0;
	while (crp + blks < x)
	{
		res += trs[cp];
		cp++, crp += blks;
	}
	for (crp++; crp <= x; crp++)
	{
		res += buc[crp];
	}
	return res;
}
inline int queryp(int x)
{
	int cp = 0, crp = 0, res = 1;
	while (crp + blks < x)
	{
		// cout << x << ' ' << trp[cp] << '\n';
		res = F(1ll * res * trp[cp]);
		cp++, crp += blks;
	}
	for (crp++; crp <= x; crp++)
	{
		res = F(1ll * res * bucr[crp]);
	}
	return res;
}
void expd(int l, int r)
{
	while (cr < r)
	{
		cr++;
		updates(a[cr], 1);
		updatep(a[cr], inv[buc[a[cr]]]);
	}
	while (cl > l)
	{
		cl--;
		updates(a[cl], 1);
		updatep(a[cl], inv[buc[a[cl]]]);
	}
}
void srnk(int l, int r)
{
	while (cr > r)
	{
		updatep(a[cr], buc[a[cr]]);
		updates(a[cr], -1);
		cr--;
	}
	while (cl < l)
	{
		updatep(a[cl], buc[a[cl]]);
		updates(a[cl], -1);
		cl++;
	}
}
void run()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);

	cin >> n >> q;
	inv[0] = fac[0] = 1;
	for (int i = 1; i <= n; i++)
	{
		bucr[i] = 1;
		cin >> a[i];
		fac[i] = inv[i] = F(1ll * inv[i - 1] * i);
	}
	inv[n] = fsp(inv[n], mod - 2);
	for (int i = n, tinv; i; i--)
	{
		tinv = F(1ll * inv[i] * inv[i - 1]);
		inv[i - 1] = F(1ll * inv[i] * i);
		inv[i] = tinv;
	}
	// cout << inv[1] << '\n';
	blks = sqrt(n);
	if ((1 << (31 - __builtin_clz(blks))) != blks)
	{
		blks = (1 << (32 - __builtin_clz(blks)));
	}
	// cout << blks << '\n';
	for (int i = 0; i <= blks + 1; i++)
		trp[i] = 1;
	for (int i = 1; i + blks <= n; i++)
		brs[i + blks] = brs[i] + 1;
	for (int i = 1; i <= q; i++)
	{
		cin >> qry[i].l >> qry[i].r >> qry[i].x;
		qry[i].id = i;
	}
	sort(qry + 1, qry + q + 1);
	for (int i = 1; i <= q; i++)
	{
		expd(qry[i].l, qry[i].r);
		srnk(qry[i].l, qry[i].r);
		// cout << qry[i].x << ' ' << queryp(qry[i].x - 1) << '\n';
		res[qry[i].id] = F(1ll * fac[querys(qry[i].x - 1)] * queryp(qry[i].x - 1));
	}
	for (int i = 1; i <= q; i++)
		cout << res[i] << '\n';
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