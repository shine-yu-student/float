---
{"dg-publish":true,"permalink":"/Luogu/Luogu P11684 Solution/","dg-note-properties":{}}
---

确实挺 Foolish 的。
首先有一个经典结论：
对于任何正整数 $x,k$，使得 $\lfloor\frac{k}{y}\rfloor<\lfloor\frac{k}{x}\rfloor$ 的最小的 $y$ 为 $\lfloor\frac{k}{\lfloor\frac{k}{x}\rfloor}\rfloor+1$。
然后是一个对于这一题的结论：
对于正整数 $x,y$ 满足 $x<y$，$y-x=\gcd(x,y)$ 当且仅当存在正整数 $v$ 满足 $x=v\times \gcd(x,y)$ 且 $y=x+\gcd(x,y)$。读者自证不难。
于是一个显然的思路就出现了。枚举 $v$ 的值，使用除法判断 $x$ 和 $y$ 分别可取的 $\gcd(x,y)$ 区间求交即可。但是范围在 $10^{12}$，显然不够。
考虑整除分块，计算会让某个区间边界发生改变的最小 $v$ 值，做乘法加速即可。
时间复杂度 $O(\sqrt{a}+\sqrt{b}+\sqrt{c}+\sqrt{d})$。
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
const int N = 1e5 + 10;
using ll = long long;
ll a, b, c, d, res;
const ll linf = 1e18;
void run()
{
	scanf("%lld%lld%lld%lld", &a, &b, &c, &d);
	a--, c--;
	for (ll i = 1, nxt = 0; i <= max({a, b, c, d});)
	{
		nxt = min({a < i ? linf : a / (a / i) + 1, b < i ? linf : b / (b / i) + 1, c < i + 1 ? linf : c / (c / (i + 1)),
				   d < i + 1 ? linf : d / (d / (i + 1))});
		// printf("%lld %lld %lld\n", i, res, nxt);
		res += (nxt - i) * max(0ll, min(b / i, d / (i + 1)) - max(a / i, c / (i + 1)));
		i = nxt;
	}

	printf("%lld\n", res);
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
**但是你先别急！**
我同时把这个问题丢给了 DeepSeek R1，他在长达 $557$ 秒的思考后一发入魂，直接写出了比我的程序快 $3$ 倍的代码！接下来是他写的题解：
```c++
#include <iostream>
#include <algorithm>
using namespace std;

typedef long long ll;

const int K = 1e6;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    ll a, b, c, d;
    cin >> a >> b >> c >> d;
    
    ll ans = 0;
    
    // Step 1: Process small m (1 <= m <= K)
    for (ll m = 1; m <= K; ++m) {
        if (m > b) break;
        ll ceil_am = (a + m - 1) / m;
        ll ceil_cm1 = (c + m) / (m + 1);
        ll lower_g = max(ceil_am, ceil_cm1);
        ll floor_bm = b / m;
        ll floor_dm1 = d / (m + 1);
        ll upper_g = min(floor_bm, floor_dm1);
        if (upper_g >= lower_g) {
            ans += upper_g - lower_g + 1;
        }
    }
    
    // Step 2: Process large m (m > K) by iterating g0
    ll max_g0 = b / (K + 1);
    for (ll g0 = 1; g0 <= max_g0; ++g0) {
        ll ceil_ag0 = (a + g0 - 1) / g0;
        ll ceil_cg0_minus1 = (c - 1) / g0;
        ll m_low = max({ceil_ag0, ceil_cg0_minus1, K + 1LL});
        ll floor_bg0 = b / g0;
        ll floor_dg0_minus1 = (d / g0) - 1;
        ll m_high = min(floor_bg0, floor_dg0_minus1);
        if (m_high >= m_low) {
            ans += m_high - m_low + 1;
        }
    }
    
    cout << ans << '\n';
    
    return 0;
}
```
1. **问题分析**：题目要求找到满足特定条件的正整数对(x, y)，其中y - x等于它们的最大公约数，且x和y分别在给定区间内。
2. **数学推导**：将x和y表示为x = g_m和y = g_(m+1)，其中g是它们的最大公约数。这样问题转化为找到满足条件的(g, m)对。
3. **分块处理**：
    - **小m处理**：直接枚举m从1到K（取1e6），计算每个m对应的g的范围。
    - **大m处理**：枚举可能的g值，计算对应的m范围，避免直接枚举大m。
4. **优化**：
    - **快速计算上下界**：利用整数除法和取模运算快速计算上下界。
    - **提前终止**：当m超过b时提前终止循环，减少不必要的计算。
该方法通过分块处理，有效减少了计算量，确保在大数据范围内高效运行。