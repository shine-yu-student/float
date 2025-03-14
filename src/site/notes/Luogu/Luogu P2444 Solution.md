---
{"dg-publish":true,"permalink":"/Luogu/Luogu P2444 Solution/"}
---

## 前言
我写这道题的题解纯粹是因为这是我又一道独立做出的紫题。
## 题解
首先，多模字符串匹配，自然地想到 AC 自动机。
而要求一个长度无限的字符串全部失配，可以想到要在 AC 自动机上构造一个不包含终止节点的环。
由此，思路自然——
1. 构建 AC 自动机。
2. **使用 BFS 使 AC 自动机结构闭合化**。该方法在 OI Wiki 中有记载，此处不再提及。这一步**非常重要**，因为它可以省去很多多余的处理。
3. 删除所有终止节点，**以及 `fail` 树上所有终止节点的祖先节点**。
4. 使用拓扑排序检验 AC 自动机的剩余结构中是否存在环。
显然，若存在环，则可以构造，否则不能。
时间复杂度 $O(\sum |s|)$。
其实本题的字符串长度和字符集大小都可以再大一点的，但没办法，这毕竟是 2000 年的题目。
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
#include <iostream>
#include <string>
#include <vector>
using namespace std;
const int N = 3e4 + 10, S = 2;
struct st
{
	int sn[S], fail;
	bool ed;
} tr[N];
int n, idx, q[N], qidx, ind[N];
string s;
vector<int> rvf[N];
void insert()
{
	int tmp = 0;
	for (auto &i : s)
	{
		if (!tr[tmp].sn[i - '0'])
			tr[tmp].sn[i - '0'] = ++idx;
		tmp = tr[tmp].sn[i - '0'];
	}
	tr[tmp].ed = true;
}
void bfs()
{
	for (int i = 0; i < S; i++)
	{
		if (tr[0].sn[i])
			q[++qidx] = tr[0].sn[i];
	}
	for (int j = 1, i = 0; j <= qidx; j++)
	{
		i = q[j];
		for (int j = 0; j < S; j++)
		{
			if (!tr[i].sn[j])
			{
				tr[i].sn[j] = tr[tr[i].fail].sn[j];
				continue;
			}
			tr[tr[i].sn[j]].fail = tr[tr[i].fail].sn[j];
			q[++qidx] = tr[i].sn[j];
		}
	}
}
void pick()
{
	for (int i = 1; i <= idx; i++)
	{
		rvf[tr[i].fail].emplace_back(i);
	}
	qidx = 0;
	for (int i = 1; i <= idx; i++)
	{
		if (tr[i].ed)
			q[++qidx] = i;
	}
	for (int j = 1, i; j <= qidx; j++)
	{
		i = q[j];
		for (auto &tk : rvf[i])
		{
			if (tr[tk].ed)
				continue;
			tr[tk].ed = true;
			q[++qidx] = tk;
		}
	}
}
bool ring()
{
	for (int i = 0; i <= idx; i++)
	{
		if (tr[i].ed)
			continue;
		for (int j = 0; j < S; j++)
		{
			ind[tr[i].sn[j]]++;
		}
	}
	qidx = 0;
	for (int i = 0; i <= idx; i++)
	{
		// printf("deg %d %d\n", i, ind[i]);
		if (ind[i] or tr[i].ed)
			continue;
		q[++qidx] = i;
	}
	for (int j = 1, i; j <= qidx; j++)
	{
		i = q[j];
		// printf("bfs %d\n", i);
		for (int tk = 0; tk < S; tk++)
		{
			if (tr[tr[i].sn[tk]].ed or !ind[tr[i].sn[tk]])
				continue;
			// printf(" del %d %d\n", i, tr[i].sn[tk]);
			ind[tr[i].sn[tk]]--;
			if (ind[tr[i].sn[tk]])
				continue;
			q[++qidx] = tr[i].sn[tk];
		}
	}
	for (int i = 0; i <= idx; i++)
	{
		if (tr[i].ed or !ind[i])
			continue;
		return true;
	}
	return false;
}
void run()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);
	cin >> n;
	for (int i = 1; i <= n; i++)
	{
		cin >> s;
		insert();
	}
	bfs();
	pick();
	// print();
	puts(ring() ? "TAK" : "NIE");
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