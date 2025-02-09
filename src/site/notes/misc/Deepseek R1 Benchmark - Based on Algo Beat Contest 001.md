---
{"dg-publish":true,"permalink":"/misc/Deepseek R1 Benchmark - Based on Algo Beat Contest 001/"}
---

## Dialogue Link
[chat.deepseek.com](https://chat.deepseek.com/a/chat/s/811269ff-a6fc-4934-bae7-a90ad6e6cbaa)
## Statement
Deepseek R1 offline.
## Prompt

```text
你是一个拥有顶尖 C++ 水平的 ICPC 选手。你将要参加一场比赛，该比赛包含七道题目。接下来，我将会提供若干条信息。对于任意一个信息，若该信息以“提示：”开头，则说明你刚刚提供的代码有误，可以参考提示内容进行修改。请隐藏你的思考内容。否则，该信息包含一道题的完整 markdown 题面。对于任何一条信息，请仅输出少量的解释代码用途的中文信息，并输出解决该问题的 C++ 代码，不要在代码中添加任何注释。准备好了吗？
```
## T1 (53s, +)

```c++
#include <iostream>
#include <string>
using namespace std;

int main() {
    int N;
    string S;
    cin >> N >> S;
    int L = N / 3;
    cout << S.substr(L, L) << S.substr(0, L) << S.substr(2 * L);
    return 0;
}
```
## T2 (358s, +)

```c++
#include <iostream>
#include <string>
#include <vector>
using namespace std;

bool check(int x) {
    string s = to_string(x);
    int n = s.size();
    if (n % 2 == 0) return false;
    int mid = n / 2;
    return s[0] == s[mid] && s[mid] == s.back();
}

int main() {
    int T;
    cin >> T;
    while (T--) {
        int L, R;
        cin >> L >> R;
        vector<int> res;
        for (int x = L; x <= R; ++x) {
            if (check(x)) {
                res.push_back(x);
            }
        }
        for (int i = 0; i < res.size(); ++i) {
            if (i > 0) cout << ' ';
            cout << res[i];
        }
        cout << endl;
    }
    return 0;
}
```
## T3 (823s+77s, +1)
```c++
#include <iostream>
#include <unordered_set>
using namespace std;

const int MOD = 1145141923;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int N, M;
    cin >> N >> M;
    unordered_set<int> s;
    int t = 0;
    bool valid = true;
    
    for (int i = 0; i < N; ++i) {
        int a;
        cin >> a;
        if (a == 0) {
            t++;
        } else {
            if (s.count(a)) valid = false;
            s.insert(a);
        }
    }
    
    if (!valid) {
        cout << 0 << endl;
        return 0;
    }
    
    int k = s.size();
    int available = M - k;
    
    if (available < t || available < 0) {
        cout << 0 << endl;
        return 0;
    }
    
    long long res = 1;
    for (int i = 0; i < t; ++i) {
        res = res * (available - i) % MOD;
    }
    
    cout << res << endl;
    return 0;
}
```
## T4 (262s+108s+314s+81s,+3)
```c++
#include <iostream>
#include <unordered_map>
using namespace std;

const int MOD = 1e9 + 7;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int N;
    cin >> N;
    
    unordered_map<int, int> cntA, cntB;
    for (int i = 0; i < N; ++i) {
        int a;
        cin >> a;
        cntA[a]++;
    }
    for (int i = 0; i < N; ++i) {
        int b;
        cin >> b;
        cntB[b]++;
    }
    
    unordered_map<int, int> freq;
    for (const auto& x : cntA) {
        for (const auto& y : cntB) {
            long long product = (1LL * x.first * y.first) % MOD;
            int v = static_cast<int>(product);
            freq[v] += min(x.second, y.second);
        }
    }
    
    int max_occur = 0;
    for (const auto& entry : freq) {
        max_occur = max(max_occur, entry.second);
    }
    
    cout << max_occur << endl;
    
    return 0;
}
```
## T5 (401s+110s, +)
```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

const int MOD = 1145141923;
const int MAX_N = 1e7;

vector<int> f(MAX_N + 1);
vector<int> prod(MAX_N + 1, 1);
vector<int> max_f_values;

void precompute_f() {
    f[1] = 0;
    for (int y = 2; y <= MAX_N; ++y) {
        long long current = y;
        int cnt = 0;
        while (current != 1) {
            if (current < y) {
                cnt += f[(int)current];
                break;
            }
            if (current % 2 == 0)
                current /= 2;
            else
                current = current * 3 + 1;
            cnt++;
        }
        f[y] = cnt;
    }
}

void precompute_max_f() {
    int current_max = -1;
    for (int y = 1; y <= MAX_N; ++y) {
        if (f[y] > current_max) {
            current_max = f[y];
            max_f_values.push_back(y);
        }
    }
}

void precompute_prod() {
    prod[1] = 1; // f[1] is 0, but product starts from 2
    for (int i = 2; i <= MAX_N; ++i) {
        prod[i] = (1LL * prod[i - 1] * f[i]) % MOD;
    }
}

int find_min_y(int x) {
    if (x == 0) return 1;
    auto it = lower_bound(max_f_values.begin(), max_f_values.end(), x,
        [](int y, int x_val) { return f[y] < x_val; });
    if (it == max_f_values.end()) return -1;
    return *it;
}

int mod_inv(long long x) {
    long long res = 1;
    int exp = MOD - 2;
    x %= MOD;
    while (exp > 0) {
        if (exp % 2 == 1)
            res = (res * x) % MOD;
        x = (x * x) % MOD;
        exp /= 2;
    }
    return static_cast<int>(res);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    precompute_f();
    precompute_max_f();
    precompute_prod();
    
    int Q;
    cin >> Q;
    while (Q--) {
        int t;
        cin >> t;
        if (t == 1) {
            int x;
            cin >> x;
            if (x == 0) {
                cout << "1\n";
                continue;
            }
            int y = find_min_y(x);
            cout << y << '\n';
        } else if (t == 2) {
            int l, r;
            cin >> l >> r;
            if (l > r) {
                cout << "0\n";
                continue;
            }
            if (l == 1) {
                if (r >= 1) {
                    cout << "0\n";
                } else {
                    cout << "1\n";
                }
                continue;
            }
            long long numerator = prod[r];
            long long denominator = prod[l - 1];
            long long inv_denominator = mod_inv(denominator);
            int ans = static_cast<int>((numerator * inv_denominator) % MOD);
            cout << ans << '\n';
        }
    }
    return 0;
}
```
## T6 (557s, +)
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