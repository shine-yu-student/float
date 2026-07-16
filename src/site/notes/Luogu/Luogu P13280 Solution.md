---
{"dg-publish":true,"permalink":"/luogu/luogu-p13280-solution/","dg-note-properties":{}}
---

解决本题的前提是意识到了以下两个**关键**的条件：
1. 最终的 $x$ 与初始时 $x$ 所在置换环的大小 $i$ 与 $m$ 在模 $i$ 意义下的余数有关。
2. 若 $i\mid m$，则最终 $x$ 不变；否则，保持 $i$ 不变，则最终的 $x$ 在与初始 $x$ 不同前提下在 $1$ 到 $n$ 中**任选其一**。

对于第二个条件，可以认为对于所有原 $x$ 所在置换环大小为 $i$ 且终点为 $y$ 且 $x\neq y$ 的排列组成的集合 $P$，存在映射 $P\rightarrow Q$ 使得 $Q$ 中所有排列满足原 $x$ 所在置换环大小为 $i$ 且终点为 $z$ 且 $x\neq z$。
首先，考虑计算长度为 $n$ 且 $x$ 所在置换环大小为 $i$ 的排列数量：
$$
\begin{aligned}
f(n,x,i) & =\binom{n-1}{i-1}\times (i-1)!\times (n-i)!\\
& =\frac{(n-1)!(i-1)!(n-i)!}{(i-1)!(n-i)!}\\
& =(n-1)!
\end{aligned}
$$
没错。只有你上手推了之后才会发现计算那么简单。
根据上述结论，易得终点不变和变化的答案分别为 $k(n-1)!$ 和 $\frac{(n(n+1)-2k)(n-2)!}{2}$。由于 $T\le 10^3$，暴力枚举因数即可。时间复杂度 $O(V+T\sqrt m)$。
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
const int N = 1e7 + 10, mod = 998244353, dv2 = mod / 2 + 1;
int n, m, k, fac[N], inv[N], res, tc;
inline int fsp(int x, int y)
{
	int res = 1;
	while (y)
	{
		if (y & 1)
			res = 1ll * res * x % mod;
		x = 1ll * x * x % mod;
		y >>= 1;
	}
	return res;
}
void init_global()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);
	fac[0] = 1;
	for (int i = 1; i < N; i++)
		fac[i] = 1ll * fac[i - 1] * i % mod;
	inv[N - 1] = fsp(fac[N - 1], mod - 2);
	for (int i = N - 1; i; i--)
		inv[i - 1] = 1ll * inv[i] * i % mod;
}
inline int C(int x, int y)
{
	return 1ll * fac[x] * inv[y] % mod * inv[x - y] % mod;
}
inline int adm(int x, int y)
{
	return (x + y >= mod ? x + y - mod : x + y);
}
void init_local()
{
	cin >> n >> m >> k;
}
void run()
{
	tc = 1ll * adm(1ll * n * (n - 1) / 2 % mod, n - k) * fac[n - 2] % mod;
	res = 1ll * tc * n % mod;
	tc = adm(1ll * k * fac[n - 1] % mod, mod - tc);
	// cout << res << ' ' << tc << '\n';
	if (!m)
	{
		res = adm(res, 1ll * tc * n % mod);
		cout << res << '\n';
		return;
	}
	for (int i = 1; i * i <= m; i++)
	{
		if (m % i or i > n)
			continue;
		res = adm(res, tc);
		if (i * i != m and m / i <= n)
			res = adm(res, tc);
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