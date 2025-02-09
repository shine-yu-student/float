---
{"dg-publish":true,"permalink":"/AtCoder/AtCoder Beginner Contest 388 G Solution/"}
---

## 闲话
还好我根据观察排行榜得出了 G 比 F 简单的结论，要不然就要卡死了。
赛后惊喜地发现我对做法和两篇日文题解都不太一样，所以来写一篇题解。
## 题解
由于 Kagamimochi = 镜饼，接下来我会用镜饼来解释我的做法。
我们将组成镜饼的两张饼称为“左饼”和“右饼”。其中，左饼的大小不大于右饼的一半。
由于给出的序列 $A$ 单调不降，对于序列中的每一张饼，将其作为“左饼”时，为了使一个区间内能匹配的镜饼更多，我们总是选择下标最小的可以与其匹配的“右饼”进行匹配。为了实现这一点，我们可以以从后往前的顺序枚举左饼，并使用双指针和并查集查找可以匹配的下标最小的右饼。**注意，这里的右饼不能重复，也就是说每张饼只能被作为右饼使用一次**。
找到最优匹配后，将查询按左端点降序排序，同时将对应的最优匹配位置拍到树状数组上做前缀求和即可。然而有时候会发现查询结果大于答案，这个时候只需要将查询结果与理论最大配对数取最小值即可，因为在这种情况下，只需要取前半部分作为左饼，后半部分作为右饼，即可保证构法成立。
时间复杂度 $O((n+q)\log n)$，但是由于树状数组常数较小，其实际表现可能优于线段树或 ST 表。
## 代码
```cpp
// #define Redshift_Debug
#ifdef Redshift_Debug
#define debug(...) fprintf(stderr, __VA_ARGS__)
#else
#define debug(...)
#endif
#include <algorithm>
#include <chrono>
#include <cstdio>
#include <tuple>
using namespace std;
const int N = 2e5 + 10;
int n, a[N], tr[N], q, f[N], res[N];
using t3i = tuple<int, int, int>;
inline void update(int x, int v)
{
	while (x <= n)
		tr[x] += v, x += (x & -x);
}
inline int query(int x)
{
	int res = 0;
	while (x)
	{
		res += tr[x];
		x -= (x & -x);
	}
	return res;
}
inline int find(int x)
{
	return x == f[x] ? x : f[x] = find(f[x]);
}
inline void merge(int x, int y)
{
	f[find(x)] = find(y);
}
t3i qry[N];
void run()
{
	scanf("%d", &n);
	for (int i = 1; i <= n; i++)
	{
		f[i] = i;
		scanf("%d", a + i);
	}
	f[n + 1] = n + 1;
	scanf("%d", &q);
	for (int i = 1; i <= q; i++)
	{
		auto &[l, r, id] = qry[i];
		scanf("%d%d", &l, &r);
		l = -l;
		id = i;
	}
	sort(qry + 1, qry + q + 1);
	for (int i = 1, rp = n + 1, rn = n; i <= q; i++)
	{
		auto &[l, r, id] = qry[i];
		l = -l;
		while (rn >= l)
		{
			while (a[rp - 1] >= a[rn] * 2)
				rp--;
			update(find(rp), 1);
			rn--;
			if (find(rp) > n)
				continue;
			merge(rp, find(rp) + 1);
		}
		res[id] = min((r - l + 1) / 2, query(r));
	}
	for (int i = 1; i <= q; i++)
		printf("%d\n", res[i]);
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
