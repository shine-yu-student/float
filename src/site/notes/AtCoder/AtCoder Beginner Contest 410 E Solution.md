---
{"dg-publish":true,"permalink":"/AtCoder/AtCoder Beginner Contest 410 E Solution/","dg-note-properties":{}}
---

## 前言
一道难度不高但挺有意思的 DP 题。
## 题解
注意到数据范围保证 $n,h,m\le 3\times 10^3$，直接想 $O(nh)$ DP。不过你想写 $O(nm)$ 也不是不行。
看到题目第一眼后可以注意到这道题是让你找到最大的 $k$ 使得高桥君可以打倒 $k$ 个怪兽。但是你要知道你又要维护两个维度又要维护此时打倒的怪兽数量，那玩意完全搞不了。
要是你想到这一步你应该开始怀疑你是不是理解错题目了，事实也确实如此。题目名字（Battles **in a Row**）和题面都明确说明了，你要打倒的怪兽不是任意 $k$ 个，而是前 $k$ 个。
既然如此，解法显然：维护数组 $d_i$ 表示目前打倒前面全部怪兽且消耗体力值为 $i$ 所需要的最小魔力值，面对一个怪兽 $(x,y)$ 时，转移式为 $d_i\leftarrow \min(d_i+y,d_{i-x})$，因为两个力必须至少使用一个。判断是否可以继续击杀的最直接办法是检查 $d_h\le m$，若满足该条件则显然可以继续击杀怪兽，否则高桥君领盒饭，直接输出结果。
时间复杂度 $O(nh)$，不过结合官方题解来看也不是没有用 `bitset` 卡过 $O(nhm)$ 的狠人。
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
#include <cstdio>
#include <iostream>
using namespace std;
const int N = 3e3 + 10;
int n, h, m, x, y, dp[N];
void run()
{
	cin.tie(0);
	cout.tie(0);
	ios::sync_with_stdio(0);
	cin >> n >> h >> m;
	for (int i = 1, x, y; i <= n; i++)
	{
		cin >> x >> y;
		for (int j = h; j >= x; j--)
		{
			dp[j] = min(dp[j] + y, dp[j - x]);
		}
		for (int j = x - 1; ~j; j--)
			dp[j] += y;
		if (dp[h] > m)
		{
			cout << i - 1 << '\n';
			return;
		}
	}
	cout << n << '\n';
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