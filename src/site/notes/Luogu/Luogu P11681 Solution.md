---
{"dg-publish":true,"permalink":"/Luogu/Luogu P11681 Solution/"}
---

应该是这场比赛除了 G 之外最有思维难度的题。
首先考虑长度为 $2$ 的数组。由于它不能有唯一众数，所以两个数不同。
接下来考虑长度为 $3$ 的数组。由于满足前面的条件，那么 $a_1\neq a_2$，$a_2\neq a_3$，若 $a_1=a_3$ 则有唯一众数为 $a_1$，不符合条件。所以 $a_1$ 到 $a_3$ 互不相同。
如果写下去，你就会发现如下规律：
考虑长度为 $k$ 的数组，由于满足前面的条件，那么 $a_1$ 到 $a_{k-1}$ 互不相同，$a_2$ 到 $a_k$ 互不相同，若 $a_1=a_k$ 则有唯一众数为 $a_1$，不符合条件。所以 $a_1$ 到 $a_k$ 互不相同。
然后你就会发现题目只是在问使得所有的数不相同的填法数量。
太智慧了。
时间复杂度 $O(n)$。
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
const int N = 1e6 + 10, mod = 1145141923;
int n, m, a[N], cnt, res;
void run()
{
	scanf("%d%d", &n, &m);
	for (int i = 1; i <= n; i++)
	{
		scanf("%d", a + i);
		if (a[i])
			continue;
		i--, n--, cnt++;
	}
	sort(a + 1, a + n + 1);
	for (int i = 1; i < n; i++)
	{
		if (a[i] == a[i + 1])
		{
			puts("0");
			return;
		}
	}
	m -= n;
	res = 1;
	for (int i = 1; i <= cnt; i++)
	{
		res = 1ll * res * (m - i + 1) % mod;
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
**但是你先别急**！我把这个问题丢给了 DeepSeek R1，他在长达 $823$ 秒的思考后说出了这句话：
> 该方法确保替换后的数组满足相邻元素不同且所有元素互不相同的条件，从而保证任何子数组都没有唯一众数。

结果你猜怎么着？他给的代码甚至无法通过样例！于是我再次强调所有元素互不相同，在 $77$ 秒的思考后才输出了这段能通过的代码：
```c++
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;

const int MOD = 1145141923;

int main() {
    int N, M;
    cin >> N >> M;
    vector<int> A(N);
    for (int& a : A) cin >> a;

    unordered_set<int> unique_vals;
    bool valid = true;
    for (int i = 0; i < N; ++i) {
        if (A[i] != 0) {
            if (unique_vals.count(A[i])) {
                valid = false;
                break;
            }
            unique_vals.insert(A[i]);
        }
    }

    if (!valid) {
        cout << 0 << endl;
        return 0;
    }

    for (int i = 0; i < N - 1; ++i) {
        if (A[i] != 0 && A[i + 1] != 0 && A[i] == A[i + 1]) {
            valid = false;
            break;
        }
    }

    if (!valid) {
        cout << 0 << endl;
        return 0;
    }

    int c = unique_vals.size();
    vector<int> zeros;
    for (int i = 0; i < N; ++i) {
        if (A[i] == 0) {
            zeros.push_back(i);
        }
    }

    long long result = 1;
    unordered_set<int> used;

    for (int pos : zeros) {
        unordered_set<int> adj;
        if (pos > 0 && A[pos - 1] != 0) adj.insert(A[pos - 1]);
        if (pos < N - 1 && A[pos + 1] != 0) adj.insert(A[pos + 1]);

        int available = M - c - adj.size();
        available -= used.size();

        if (available <= 0) {
            cout << 0 << endl;
            return 0;
        }

        result = result * available % MOD;
        c += 1;
    }

    cout << result << endl;
    return 0;
}
```
于是我得出一个恐怖的结论：在 DeepSeek 眼里，[F 比 C 简单](https://www.luogu.com.cn/article/i5s0wa95)。