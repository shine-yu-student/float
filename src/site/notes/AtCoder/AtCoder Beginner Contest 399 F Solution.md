---
{"dg-publish":true,"permalink":"/AtCoder/AtCoder Beginner Contest 399 F Solution/"}
---

## 背景
这是我第一次在赛时独立完成分治算法的实现。
并且，截至开始写稿，AtCoder 上出现的三篇日文题解与我的解法**全部不同**。
## 题解
根据简单数学知识可得
$$
(x+y)^k=\sum_{i=0}^k x^iy^{k-i}\binom{k}{i}
$$
由此，使用分治。
令答案为 $s$，初始为 $0$。
定义对区间 $[l,r]$ 的计算如下：
1. 若 $l=r$，则令 $s\leftarrow s+p_i^k$，结束计算过程。
2. 取中点值 $m=\lfloor\frac{l+r}{2}\rfloor$。
3. 对 $0\le i\le k$，计算 $p_i=\sum_{j=l}^{m}(\sum_{t=j}^m a_j)^i,q_i=\sum_{j=m+1}^{r}(\sum_{t=m+1}^j a_j)^i$。
4. 对 $0\le i\le k$，令 $s\leftarrow s+p_iq_{k-i}\binom{k}{i}$。
5. 对区间 $[l,m]$ 和 $[m+1,r]$ 分别进行如上运算。
对区间 $[1,n]$ 进行运算后 $s$ 模 $998244353$ 意义下的值即为答案。
上述方法可行的原因是，按照上述方法进行分治后，对任意的 $1\le i\le j\le n$，**必定存在恰好一个**被进行过运算的区间 $[l,r]$，满足 $l\le i\le \lfloor\frac{l+r}{2}\rfloor<j\le r$，**或** $i=j$。而在上述的分治过程中，$p$ 和 $q$ 的实际存在意义是对满足 $l\le i\le \lfloor\frac{l+r}{2}\rfloor<j\le r$ 的所有 $i,j$ 对进行捆绑计算，加快运算速度。
因此，上述计算方法正确。
上述代码的时间复杂度为 $O(nk\log n)$。并且，由于上述的解法在实际实现时容易使得内存访问连续，若在进行模加法时进行简单优化，其运行性能可以与频繁进行一般模运算 的 $O(nk^2)$ DP 解法相近。
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
#include <cstring>
using namespace std;
const int N = 2e5 + 10, B = 20, mod = 998244353;
int n, m, p[B], res, a[N];
inline int adm(int x, int y)
{
	return (x + y >= mod ? x + y - mod : x + y);
}
void dvin(int l, int r)
{
	static int pl[B], pr[B];
	if (l == r)
	{
		int tmp = 1;
		for (int i = 1; i <= m; i++)
			tmp = 1ll * tmp * a[l] % mod;
		res = adm(res, tmp);
		return;
	}
	memset(pl, 0, (m + 1) << 2);
	memset(pr, 0, (m + 1) << 2);
	int mid = (l + r) >> 1;
	for (int i = mid, tmp = 0; i >= l; i--)
	{
		tmp = adm(tmp, a[i]);
		for (int j = 0, tmp2 = 1; j <= m; j++)
		{
			pl[j] = adm(pl[j], tmp2);
			tmp2 = 1ll * tmp2 * tmp % mod;
		}
	}
	for (int i = mid + 1, tmp = 0; i <= r; i++)
	{
		tmp = adm(tmp, a[i]);
		for (int j = 0, tmp2 = 1; j <= m; j++)
		{
			pr[j] = adm(pr[j], tmp2);
			tmp2 = 1ll * tmp2 * tmp % mod;
		}
	}
	for (int i = 0; i <= m; i++)
	{
		res = adm(res, 1ll * pl[i] * pr[m - i] % mod * p[i] % mod);
	}
	dvin(l, mid);
	dvin(mid + 1, r);
}
void run()
{
	scanf("%d%d", &n, &m);
	for (int i = 1; i <= n; i++)
		scanf("%d", a + i);
	p[0] = 1;
	for (int i = 1; i <= m; i++)
	{
		for (int j = i; j; j--)
			p[j] += p[j - 1];
	}
	dvin(1, n);
	printf("%d\n", res);
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