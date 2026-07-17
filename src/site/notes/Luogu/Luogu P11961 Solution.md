---
{"dg-publish":true,"permalink":"/Luogu/Luogu P11961 Solution/","dg-note-properties":{}}
---

明天就要月考了，但是看到这道题还是想写一下。
然后，思考时间 3min，切。
**理论上，以下解法并不要求对“原根”这一概念有额外的事先理解。**
首先，根据费马小定理，对于任意质数 $p$ 和任意小于 $p$ 的**正**整数 $a$，满足 $a^{p-1}\equiv 1\pmod p$，故可以直接省略第二个条件。
随后，可以证明，对于任何小于 $p$ 的**正**整数 $x$，对于**最小**的**正**整数 $k$ 满足 $x^k\equiv 1\pmod p$，$k|(p-1)$。证明也足够显然，若最小的 $k$ 不是 $(p-1)$ 的因数，那么 $x^{p-1}\not\equiv 1\pmod p$，与费马小定理相悖，显然不存在这种情况。
由此，解法显然。枚举 $(p-1)$ 的所有因数 $f$，若存在 $f\neq 1$ 且 $f\neq p-1$ 满足 $a^f\equiv 1\pmod p$，则不是原根，否则是。
时间复杂度 $O(\sum \sqrt{p-1}\log p)$。
```c++
// #define Redshift_Debug
#ifdef Redshift_Debug
#define debug(...) fprintf(stderr, __VA_ARGS__)
#include <chrono>
#else
#define debug(...)
#endif
#include <cstdio>

using namespace std;
const int N = 1e5 + 10;
int a, p;
inline int fsp(int x, int y)
{
	int res = 1;
	while (y)
	{
		if (y & 1)
			res = 1ll * res * x % p;
		x = 1ll * x * x % p;
		y >>= 1;
	}
	return res;
}
void init_global()
{
}
void init_local()
{
	scanf("%d%d", &a, &p);
}
void run()
{
	for (int i = 2; i * i <= p - 1; i++)
	{
		if ((p - 1) % i)
			continue;
		if (fsp(a, i) == 1 or fsp(a, (p - 1) / i) == 1)
		{
			puts("No");
			return;
		}
	}
	puts("Yes");
}
int main()
{
#ifdef Redshift_Debug
	auto st = chrono::high_resolution_clock::now();
#endif
	int T = 1;
	scanf("%d", &T);
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