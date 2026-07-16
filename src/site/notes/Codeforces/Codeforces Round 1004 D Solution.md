---
{"dg-publish":true,"permalink":"/codeforces/codeforces-round-1004-d-solution/","dg-note-properties":{}}
---

这道题非常神，想必这就是[这条评论](https://mirror.codeforces.com/blog/entry/139300?#comment-1245306)中说的“见过的最神的交互题”吧。
首先假设 Jury 脑子里想的是 A。$n$ 个点，$n$ 条**有向**边，对询问的 $(i,j)$ 若 $i$ 无法到达 $j$ 返回 $0$。想到了什么？
结论 $1$：若 $x$ 不是长度为 $n$ 的排列，则可以通过**一次**询问解决问题。
原因 $1$：容易找出一个未出现在 $x$ 中的元素 $a$，令 $i=a$，$j$ 为任意一个在 $[1,n]$ 范围内且不等于 $i$ 的整数。此时，若答案为 A，则 $a$ 出度为 $0$，无法到达除自己之外的任意一个节点。在此基础上，题目保证所有的 $(x_i,y_i)$ 互不相同，筛除了答案为 B 时两点处在同一位置上的可能。由此可以判断答案。
好了。筛掉了不是排列的答案，那如果是排列呢？
结论 $2$：找到 $p$ 和 $q$ 满足 $x_p=1,y_p=n$。令 $i=p,j=q$，若返回结果**不为** $n-1$，则可以通过**一次**询问解决问题。
原因 $2$：此时假设 Jury 脑子里想的是 B。由于返回的是 $|x_i-x_j|+|y_i-y_j|$，那么上述询问返回的结果必然不小于 $n-1$。如果小于 $n-1$，则一定是 A。在一个 $n$ 个点的边权为 $1$ 的有向图中，连通点对的最大距离（以边计）为 $n-1$，读者自证不难。若返回值大于 $n-1$，则一定**不为** A，答案为 B。
最后讨论 $n-1$ 的情况。
结论 $3$：直接询问 $(q,p)$ 可得到答案。
原因 $3$：若答案为 A，则唯一一种可能情况是 $p$ 通过 $n-1$ 条边达到 $q$，形成一条链。在此基础上，若希望使 $q$ 到 $p$ 的最短路同样等于 $n-1$，在 $n\ge 3$ 的前提下需要至少 $2$ 条额外的边，同样自证不难。综上，若 $(q,p)$ 的询问结果为 $n-1$，答案不可能为 A，则答案为 B。否则，答案为 A。
**Bonus**：实际上，我们同样可知，$(q,p)$ 询问的结果的范围是 $\mathbb{Z}\cap([0,1]\cup [n-1,2n-4])$。该结论的证明留作课后作业。
至此，所有情况讨论完毕。
```c++
/*
Distinct: Rings
Non-Distinct: Leaf
*/
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
int n, a[N], tx, ty, tr;
bool flg, vis[N];
int ask(int x, int y)
{
	static int res;
	printf("? %d %d\n", x, y);
	fflush(stdout);
	scanf("%d", &res);
	return res;
}
void init_global()
{
}
void init_local()
{
	scanf("%d", &n);
	memset(vis + 1, 0, n);
	flg = false;
	for (int i = 1; i <= n; i++)
	{
		scanf("%d", a + i);
		flg |= vis[a[i]];
		vis[a[i]] = true;
	}
}
void run()
{
	if (flg)
	{
		for (int i = 1; i <= n; i++)
		{
			if (!vis[i])
			{
				printf("! %c\n", ask(i, (i == n) ? 1 : i + 1) ? 'B' : 'A');
				fflush(stdout);
				break;
			}
		}
		return;
	}
	for (int i = 1; i <= n; i++)
	{
		if (a[i] == 1)
			tx = i;
		if (a[i] == n)
			ty = i;
	}
	tr = ask(tx, ty);
	if (tr < n - 1)
	{
		puts("! A");
		fflush(stdout);
		return;
	}
	if (tr >= n)
	{
		puts("! B");
		fflush(stdout);
		return;
	}
	if (tr != ask(ty, tx))
	{
		puts("! A");
		fflush(stdout);
		return;
	}
	puts("! B");
	fflush(stdout);
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