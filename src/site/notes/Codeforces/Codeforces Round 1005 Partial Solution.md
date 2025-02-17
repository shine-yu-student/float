---
{"dg-publish":true,"permalink":"/Codeforces/Codeforces Round 1005 Partial Solution/"}
---

A 掉 $5$ 题，非常开心，于是来写一下题解。
## A. Brogramming Contest
显然可以通过（连通块数量 $-1$）次操作分离 $0$ 和 $1$。但是由于 $1$ 必须放在 $t$，那么若输入的 $s$ 以 $1$ 开头时还需要再将答案加 $1$。
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
#include <string>
using namespace std;
const int N = 1e5 + 10;
int n;
string s;
int res;
void init_global()
{
}
void init_local()
{
	cin >> n >> s;
}
void run()
{
	// cout << s << '\n';
	res = 0;
	for (int i = 1; i < n; i++)
		res += (s[i] != s[i - 1]);
	// printf("%d\n", res);
	printf("%d\n", res + s[0] - '0');
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
## B. Variety is discouraged
由题知删除出现次数大于 $1$ 的数一定不优。将这些数删掉，选出长度最大的连通块输出即可。
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
const int N = 2e5 + 10;
int n, a[N], l, r, buc[N];
void init_global()
{
}
void init_local()
{
	scanf("%d", &n);
	memset(buc + 1, 0, n << 2);
	for (int i = 1; i <= n; i++)
	{
		scanf("%d", a + i);
		buc[a[i]]++;
	}
}
void run()
{
	l = 0;
	r = -1;
	for (int i = 1, j; i <= n; i++)
	{
		j = i;
		while (j <= n and buc[a[j]] == 1)
			j++;
		j--;
		if (j - i > r - l)
			l = i, r = j;
		if (i <= j)
			i = j;
		// fprintf(stderr, "%d\n", i);
	}
	if (!l)
		puts("0");
	else
		printf("%d %d\n", l, r);
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
## C. Remove the Ends
**一定不要把这题想复杂**。
经过手玩发现合法操作方案一定由左半部分正数及右半部分负数组成。
预处理前缀正和以及后缀负和，枚举正负分界点取最大值即可。
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
using namespace std;
const int N = 2e5 + 10;
using ll = long long;
ll a[N], pfp[N], pfn[N], res;
int n;
void init_global()
{
}
void init_local()
{
	scanf("%d", &n);
	for (int i = 1; i <= n; i++)
		scanf("%lld", a + i);
	for (int i = 1; i <= n; i++)
	{
		pfp[i] = pfp[i - 1];
		pfn[i] = pfn[i - 1];
		if (a[i] > 0)
			pfp[i] += a[i];
		if (a[i] < 0)
			pfn[i] -= a[i];
	}
}
void run()
{
	res = 0;
	for (int i = 0; i <= n; i++)
	{
		res = max(res, pfp[i] + pfn[n] - pfn[i]);
	}
	printf("%lld\n", res);
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
## D. Eating
好题。
暴力往前吃显然无法符合时间限制，考虑优化。
考虑位运算的性质。
$x>y$ 的充分不必要条件是 $\operatorname{highbit}(x)>\operatorname{highbit}(y)$。
$x<y$ 的充分不必要条件是 $\operatorname{highbit}(x)<\operatorname{highbit}(y)$。
$x=y$ 的必要不充分条件是 $\operatorname{highbit}(x)=\operatorname{highbit}(y)$。
分情况讨论。
如果碰到一个 $\operatorname{highbit}$ 更大的数，显然无法吃掉，停下。
如果碰到一个 $\operatorname{highbit}$ 更小的数，显然可以吃掉，吃下。
如果碰到一个 $\operatorname{highbit}$ 相同的数，判断大小关系决定是否可以继续。
由此，可以对每个 $\operatorname{highbit}$ 预处理出前缀 $\operatorname{highbit}$ 更大或相同的最大位置，每次从 $\operatorname{highbit}$ 更大或相同的位置中选一个更大的往前跳即可。
时间复杂度 $O(n+q)\log v$。
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
const int N = 2e5 + 10;
int hb[N], ps[N], idx;
int n, a[N], pfx[N], pfp[30][N], pfpeq[30][N], q;
void init_global()
{
}
void init_local()
{
	scanf("%d%d", &n, &q);
	for (int i = 1; i <= n; i++)
	{
		scanf("%d", a + i);
		pfx[i] = (pfx[i - 1] ^ a[i]);
		for (int j = 29; ~j; j--)
		{
			if (a[i] & (1 << j))
			{
				hb[i] = j;
				break;
			}
		}
	}
	for (int i = 0; i < 30; i++)
	{
		for (int j = 1; j <= n; j++)
		{
			pfp[i][j] = pfp[i][j - 1];
			if (hb[j] > i)
				pfp[i][j] = j;
		}
	}
	for (int i = 0; i < 30; i++)
	{
		for (int j = 1; j <= n; j++)
		{
			pfpeq[i][j] = pfpeq[i][j - 1];
			if (hb[j] == i)
				pfpeq[i][j] = j;
		}
	}
}
void run()
{
	for (int i = 1, x, thb, flg; i <= q; i++)
	{
		scanf("%d", &x);
		flg = 0;
		for (thb = 29; (~thb) and (x >> thb & 1 ^ 1); thb--)
			;
		for (int j = n; j; j--)
		{
			if (thb == -1)
			{
				printf("%d%c", n - j, " \n"[i == q]);
				flg = 1;
				break;
			}
			if (pfpeq[thb][j] > pfp[thb][j])
			{
				if (pfpeq[thb][j] != j)
					x ^= (pfx[j] ^ pfx[pfpeq[thb][j]]);
				if (x < a[pfpeq[thb][j]])
				{
					printf("%d%c", n - pfpeq[thb][j], " \n"[i == q]);
					flg = 1;
					break;
				}
				x ^= a[pfpeq[thb][j]];
				j = pfpeq[thb][j];
			}
			else
			{
				printf("%d%c", n - pfp[thb][j], " \n"[i == q]);
				flg = 1;
				break;
			}
			for (; (~thb) and (x >> thb & 1 ^ 1); thb--)
				;
		}
		if (flg)
			continue;
		printf("%d%c", n, " \n"[i == q]);
	}
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
## E. Mycraft Sand Sort
重力排序确实挺有趣的，可惜太慢了，不实用。但是可以用来出题。
考虑按列从前往后考虑。
由于 $p$ 是个排列，所以每次都会从一列中删掉一行。
显然，从一列中删掉与原来的一列中删掉的位置处在同一个**极大同色连通块**中的位置不会影响后一列的形态。
所以解法显然。使用链表+并查集动态处理删除的位置以及极大连通块的大小，将删除途中所有所在极大同色连通块的大小乘起来即为答案。
时间复杂度 $O(n\alpha(n))$。
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
const int N = 2e5 + 10, mod = 998244353;
int n, a[N], b[N], prv[N], nxt[N], f[N], sz[N], rnk[N], res;
inline int find(int x)
{
	return x == f[x] ? x : f[x] = find(f[x]);
}
inline void merge(int x, int y)
{
	x = find(x), y = find(y);
	if (x == y)
		return;
	f[y] = x;
	sz[x] += sz[y];
	sz[y] = 0;
}
void init_global()
{
}
void init_local()
{
	scanf("%d", &n);
	for (int i = 1; i <= n; i++)
		scanf("%d", a + i), f[i] = i, sz[i] = 1, rnk[a[i]] = i;
	for (int i = 1; i <= n; i++)
		scanf("%d", b + i), nxt[i] = i + 1, prv[i] = i - 1;
	nxt[0] = 1, prv[n + 1] = n, b[n + 1] = 0;
}
void run()
{
	res = 1;
	for (int i = 1; i < n; i++)
	{
		if (b[i] == b[i + 1])
			merge(i, i + 1);
	}
	for (int i = 1, j; i <= n; i++)
	{
		j = rnk[i];
		res = 1ll * res * sz[find(j)] % mod;
		sz[find(j)]--;
		if (b[nxt[j]] == b[prv[j]])
			merge(prv[j], nxt[j]);
		prv[nxt[j]] = prv[j], nxt[prv[j]] = nxt[j];
	}
	printf("%d\n", res);
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
## F. We Be Summing
我不会，所以，没有！