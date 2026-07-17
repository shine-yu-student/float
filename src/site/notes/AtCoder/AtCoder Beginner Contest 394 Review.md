---
{"dg-publish":true,"permalink":"/AtCoder/AtCoder Beginner Contest 394 Review/","dg-note-properties":{}}
---

## 闲话
最后两分钟，绝地反击，rating 回升。
谁家比赛会把最难的题目放在 E 啊。
## 经历
ABCD 简单得像是洛谷入门赛。
E 最难。瞪了半个小时没有思路，看到排行榜有人先做最后一题，果断切题。
然后看 G，题面太长了，不想看。
然后看 F，发现能做，打，错了两发。
绝望之中发现 G 是最小瓶颈路模板，打，错一发，发现忘记特判，交，过。
接下来看 F。横看竖看无法发现问题，然后发现优先队列没打 greater，成小丑，遂再交一发，过。
最后没时间了。
## 题解
### A. 22222
如果不会这道题还是别学 OI 了罢。
代码就不放了。
### B. cat
sort 函数的第三个参数可以放自定义比较函数。直接在里面指定字符串长度作为排序关键字，这题就做完了。
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
#include <iostream>
#include <string>
using namespace std;
const int N = 1e5 + 10;
int n;
string s[N];
void run()
{
	scanf("%d", &n);
	for (int i = 1; i <= n; i++)
		cin >> s[i];
	sort(s + 1, s + n + 1, [&](string &x, string &y) { return x.size() < y.size(); });
	for (int i = 1; i <= n; i++)
		cout << s[i];
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
### C. Debug
将 WA 变成 AC 等价于将一个 A 往前推一格。
找到每个 A 前面接的一串 W，将最前一个 W 变为 A，将 A 以及除了第一个 W 之外的 W 变为 C。
然后就做完了。
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
string s;
int n;
void run()
{
	cin >> s;
	n = s.size();
	for (int i = 0, j = 0; i < n; i++)
	{
		if (s[i] != 'A')
			continue;
		if (!i or s[i - 1] != 'W')
			continue;
		j = i - 1;
		while (~j and s[j] == 'W')
			j--;
		j++;
		s[j] = 'A';
		j++;
		while (j <= i)
			s[j] = 'C', j++;
	}
	cout << s;
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
### D. Colorful Bracket Sequence
多括号匹配模板题。
在基础括号匹配的基础上利用 map 进行额外的判断即可。
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
// #include <map>
#include <string>
using namespace std;
const int N = 2e5 + 10;
string s;
int idx;
char stk[N], mp[256];
void run()
{
	cin >> s;
	mp[')'] = '(';
	mp[']'] = '[';
	mp['>'] = '<';
	for (auto &i : s)
	{
		if (!mp[i])
		{
			stk[++idx] = i;
			continue;
		}
		if (stk[idx] != mp[i])
		{
			puts("No");
			return;
		}
		idx--;
	}
	puts(idx ? "No" : "Yes");
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
### E. Palindromic Shortest Path
我不会，所以，没有！:)
### F. Alkane
根据题面，一个 Alkane 由若干个核心和若干个叶子组成。考虑拓扑排序。
从叶子开始考虑。设 $f_i$ 为以 $i$ 作为核心的**非完整 Alkane** 的最大节点数量**减一**。此处，非完整指使得 $i$ 的度数为 $3$ 而不是 $4$。
按照拓扑序，走到一个节点 $i$ 时，若其邻接节点中有至少 $4$ 个已经被访问过，那么可以直接提取出其中最大的 $4$ 个尝试更新答案。接下来，去掉最小的一个，计算 $f_i$，对剩下的节点重复该过程。
时间复杂度 $O(n)$。
```c++
// #define Redshift_Debug
#ifdef Redshift_Debug
#define debug(...) fprintf(stderr, __VA_ARGS__)
#include <chrono>
#else
#define debug(...)
#endif
#include <cstdio>
#include <queue>
#include <vector>
using namespace std;
const int N = 2e5 + 10;
int n, deg[N], idx, q[N], res = -1, va[N];
vector<int> road[N];
bool vis[N];
priority_queue<int, vector<int>, greater<int>> qr;
void run()
{
	scanf("%d", &n);
	for (int i = 1, x, y; i < n; i++)
	{
		scanf("%d%d", &x, &y);
		road[x].emplace_back(y);
		road[y].emplace_back(x);
		deg[x]++, deg[y]++;
	}
	for (int i = 1; i <= n; i++)
	{
		if (deg[i] > 1)
			continue;
		q[++idx] = i;
	}
	for (int j = 1, i, tmp, flg; j <= n; j++)
	{
		i = q[j];
		vis[i] = true;
		while (qr.size())
			qr.pop();
		tmp = flg = 0;
		for (auto &k : road[i])
		{
			if (!vis[k])
			{
				flg = 1;
				deg[k]--;
				if (deg[k] == 1)
					q[++idx] = k;
				continue;
			}
			qr.emplace(va[k] + 1);
			tmp += va[k] + 1;
			if (qr.size() > 4)
				tmp -= qr.top(), qr.pop();
		}
		if (qr.size() == 4)
			res = max(res, tmp + 1), tmp -= qr.top(), qr.pop();
		if (qr.size() == 3)
			va[i] = tmp;
		if (qr.size() == 3 and flg)
			res = max(res, tmp + 2);
	}
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
### G. Dense Buildings
首先，注意到一个重要事实。取所有建筑的一个横截面，截面位置越低，切过的建筑就越多。这启发我们使用离线算法。
将所有建筑物的位置按照建筑物高度降序排序。按照该顺序遍历所有建筑物，并将其与之前所有遍历过的建筑物连通。由此，我们可以得到任意点对之间无代价连通的最高高度。那么如何利用这个信息呢？
这个时候要用到一个典得不能再典的算法——并查集上询问挂载。将所有的询问编号挂在各自的坐标上，每次连通两个连通块时检查是否有相同的询问编号存在。若存在，即更新答案。**合并时需要使用启发式合并，这很重要。若不使用，可能导致时间超限**。
时间复杂度 $O(HW\log HW+HW\alpha (HW)\log Q+Q)$。
```c++
// #define Redshift_Debug
// #define __GLIBCXX_DEBUG
#ifdef Redshift_Debug
#define debug(...) fprintf(stderr, __VA_ARGS__)
#include <chrono>
#else
#define debug(...)
#endif
#include <algorithm>
#include <cmath>
#include <cstdio>
#include <set>
#include <tuple>
using namespace std;
const int N = 5e2 + 10, Q = 2e5 + 10;
int n, m, f[N * N], res[Q], a[N][N], idx, q;
set<int> st[N * N];
using t3i = tuple<int, int, int>;
bool act[N][N];
t3i ps[N * N];
const int dx[]{0, 1, 0, -1}, dy[]{1, 0, -1, 0};
struct str
{
	int a, b, y, c, d, z;
	void input()
	{
		scanf("%d%d%d%d%d%d", &a, &b, &y, &c, &d, &z);
	}
};
str qry[Q];
inline int find(int x)
{
	return x == f[x] ? x : f[x] = find(f[x]);
}
inline void merge(int x, int y, int z)
{
	x = find(x), y = find(y);
	if (x == y)
		return;
	if (st[x].size() < st[y].size())
		swap(x, y);
	for (auto &i : st[y])
	{
		if (st[x].count(i))
			st[x].erase(i), res[i] = z;
		else
			st[x].emplace(i);
	}
	f[y] = x;
	st[y].clear();
}
inline int rmp(int x, int y)
{
	return (x - 1) * m + y;
}
void run()
{
	scanf("%d%d", &n, &m);
	for (int i = 1; i <= n; i++)
	{
		for (int j = 1; j <= m; j++)
		{
			scanf("%d", a[i] + j);
			ps[++idx] = {-a[i][j], i, j};
			f[rmp(i, j)] = rmp(i, j);
		}
		// fprintf(stderr, "%d\n", i);
	}
	sort(ps + 1, ps + idx + 1);
	scanf("%d", &q);
	for (int i = 1; i <= q; i++)
	{
		qry[i].input();
		st[rmp(qry[i].a, qry[i].b)].emplace(i);
		st[rmp(qry[i].c, qry[i].d)].emplace(i);
	}
	for (int i = 1; i <= idx; i++)
	{
		auto &[va, x, y] = ps[i];
		va = -va;
		act[x][y] = true;
		for (int j = 0, nx, ny; j < 4; j++)
		{
			nx = x + dx[j], ny = y + dy[j];
			if (nx < 1 or nx > n or ny < 1 or ny > m or !act[nx][ny])
				continue;
			merge(rmp(x, y), rmp(nx, ny), va);
		}
	}
	for (int i = 1; i <= q; i++)
	{
		if (min(qry[i].y, qry[i].z) <= res[i] or (qry[i].a == qry[i].c and qry[i].b == qry[i].d))
		{
			printf("%d\n", abs(qry[i].y - qry[i].z));
			continue;
		}
		printf("%d\n", qry[i].y + qry[i].z - res[i] * 2);
	}
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