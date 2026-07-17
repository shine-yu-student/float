---
{"dg-publish":true,"permalink":"/Luogu/Luogu P11833 Solution/","dg-note-properties":{}}
---

**本题解的风格相对于普通题解略有变动，请审核明察**。
## 前言
在联合省选的广东赛区中，监考员第二次发出了考试结束的指令。
与伙伴们分别后，我坐在父母的车上，处在回家的路上。
坐在车上，我想起今天相比昨天更具有戏剧性的调试过程，感叹道：要是我的线段树没有写错，我后面的部分分能再多拿一点吗？
## 题解
首先，显然可以得知必须先处理时间限制更小的点。
根据这个性质，我们不难得到一个 $O(n^2)$ 的暴力解法：暴力地将需要移动的点移动到能够让目标点移动到对应位置的位置。
然而，由于 $n\le 2\times 10^5$，这显然是不够的，考虑优化。
题目中给出的坐标是单调上升的，同时操作时所有点的坐标也是单调上升的，非常不美观，考虑将第 $i$ 个数的起始坐标和目标坐标同时减去 $i$，不难发现条件从单调上升变为单调不降外没有改动原来的规则。
所以，现在，操作被转换为：
1. 若点 $i$ 的目标点在目前坐标的左侧，则在左侧找到第一个目前坐标大于 $i$ 目标点的位置 $j$，将 $j$ 和 $i$ 之间的所有点移至 $i$ 目标点并更新答案。
2. 否则，在右侧找到最后一个目前坐标小于 $i$ 目标点的位置 $j$，将 $i$ 和 $j$ 之间的所有点移至 $i$ 目标点并更新答案。
上述操作显然可以使用线段树进行优化。但是，由于坐标变换是批量操作，如何计算每个批量操作的点的答案呢？
实际上，由于变换后 $b$ 是单调不降的，批量变换后达到目标位置的点一定形成了一个区间。此时，我们可以暴力枚举这个区间内的所有位置，并更新答案。而由于批量变换的波式传播，从 $i$ 开始，每个往外延伸的达到目标坐标的点所用的时间都比前一个少 $1$。
至此，本题解决，时间复杂度 $O(n\log n)$。
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
using namespace std;
const int N = 2e5 + 10;
int n, a[N], b[N], p[N], clb, crb, buf;
using ll = long long;
ll t[N], trs[N];
int mx[N << 2], tag[N << 2];
ll sm[N << 2], curt;
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
inline void grb(int x)
{
	mx[x] = max(mx[x << 1], mx[x << 1 | 1]);
	sm[x] = sm[x << 1] + sm[x << 1 | 1];
}
void build(int x, int l, int r)
{
	tag[x] = -1;
	if (l == r)
	{
		mx[x] = sm[x] = a[l];
		return;
	}
	int mid = (l + r) >> 1;
	build(x << 1, l, mid);
	build(x << 1 | 1, mid + 1, r);
	grb(x);
}
void psh(int x, int l, int r)
{
	if (!~tag[x])
		return;
	int mid = (l + r) >> 1;
	mx[x << 1] = mx[x << 1 | 1] = tag[x];
	sm[x << 1] = 1ll * tag[x] * (mid - l + 1);
	sm[x << 1 | 1] = 1ll * tag[x] * (r - mid);
	tag[x << 1] = tag[x << 1 | 1] = tag[x];
	tag[x] = -1;
}
void update(int x, int l, int r)
{
	if (l >= clb and r <= crb)
	{
		tag[x] = buf;
		mx[x] = buf;
		sm[x] = 1ll * buf * (r - l + 1);
		return;
	}
	psh(x, l, r);
	int mid = (l + r) >> 1;
	if (clb <= mid)
		update(x << 1, l, mid);
	if (crb > mid)
		update(x << 1 | 1, mid + 1, r);
	grb(x);
}
int lwb(int x, int l, int r)
{
	if (l == r)
		return mx[x] >= buf ? l : l + 1;
	psh(x, l, r);
	int mid = (l + r) >> 1, ret = 0;
	if (mx[x << 1] >= buf)
		ret = lwb(x << 1, l, mid);
	else
		ret = lwb(x << 1 | 1, mid + 1, r);
	grb(x);
	return ret;
}
ll query(int x, int l, int r)
{
	if (l >= clb and r <= crb)
		return sm[x];
	psh(x, l, r);
	int mid = (l + r) >> 1;
	ll res = 0;
	if (clb <= mid)
		res += query(x << 1, l, mid);
	if (crb > mid)
		res += query(x << 1 | 1, mid + 1, r);
	grb(x);
	return res;
}
void init_global()
{
}
void init_local()
{
	read(n);
	for (int i = 1; i <= n; i++)
		trs[i] = -1, p[i] = i, read(a[i], b[i], t[i]), a[i] -= i, b[i] -= i;
	sort(p + 1, p + n + 1, [&](int x, int y) { return t[x] < t[y]; });
	build(1, 1, n);
	curt = 0;
}
void run()
{
	for (int j = 1, i, tps; j <= n; j++)
	{
		i = p[j];
		// printf("%d %lld\n", i, curt);
		if (~trs[i])
			continue;
		if (a[i] == b[i])
		{
			trs[i] = 0;
			continue;
		}
		if (a[i] < b[i])
		{
			buf = b[i];
			tps = lwb(1, 1, n) - 1;
			clb = i, crb = tps;
			curt += 1ll * b[i] * (tps - i + 1) - query(1, 1, n);
			update(1, 1, n);
			for (ll k = i, trt = curt; k <= crb; k++)
			{
				if (~trs[k])
					break;
				if (b[k] != b[i])
					break;
				trs[k] = trt;
				trt--;
			}
			continue;
		}
		buf = b[i];
		tps = lwb(1, 1, n);
		clb = tps, crb = i;
		curt += query(1, 1, n) - 1ll * (i - tps + 1) * b[i];
		update(1, 1, n);
		for (ll k = i, trt = curt; k >= clb; k--)
		{
			if (~trs[k])
				break;
			if (b[k] != b[i])
				break;
			trs[k] = trt;
			trt--;
		}
	}
	for (int i = 1; i <= n; i++)
	{
		// printf("%lld%c", trs[i], " \n"[i == n]);
		if (trs[i] > t[i])
		{
			puts("No");
			return;
		}
	}
	puts("Yes");
}
int main()
{
    freopen("move.in","r",stdin);
    freopen("move.out","w",stdout);
#ifdef Redshift_Debug
	auto st = chrono::high_resolution_clock::now();
#endif
	int T = 1;
	scanf("%*d%d", &T);
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
## 后记
拿起手机，同伴们都在讨论今天的比赛情况。还记得在考场外，有一位同学对我 T1 的结论提出了质疑。而在车上，他在 QQ 群里肯定了我的猜测。为什么？此时，另外一位同学发了一条消息。这条消息的大意是：
> 此事在 [AtCoder Beginner Contest 371 F - Takahashi in Narrow Road](https://atcoder.jp/contests/abc371/tasks/abc371_f) 中亦有记载。

打开这道题，查看提交记录中的代码。我看见当时我用 $30$ 分钟解决了这道题。再仔细看看代码，我发现，当时我在家里写出线段树二分和我在考场上写出的在细节上没有任何差别。
真快啊。
半年就这么过去了，由 ABC 产生的记忆，跨越了半年，又再一次被唤醒了。