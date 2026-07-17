---
{"dg-publish":true,"permalink":"/misc/Deepseek V3 Benchmark - Based on Algo Beat Contest 001/","dg-note-properties":{}}
---

## Dialogue Link
[chat.deepseek.com](https://chat.deepseek.com/a/chat/s/c976a6ad-f20a-454b-86b2-98cda7f0ee7f)
## Statement
Deepseek V3 offline.
## Prompt
```text
你是一个拥有顶尖 C++ 水平的 ICPC 选手。你将要参加一场比赛，该比赛包含七道题目。接下来，我将会提供若干条信息。对于任意一个信息，若该信息以“提示：”开头，则说明你刚刚提供的代码有误，可以参考提示内容进行修改。请隐藏你的思考内容。否则，该信息包含一道题的完整 markdown 题面。对于任何一条信息，请仅输出少量的解释代码用途的中文信息，并输出解决该问题的 C++ 代码，不要在代码中添加任何注释。准备好了吗？
```
## T1 (+)
```c++
#include <bits/stdc++.h>
using namespace std;

int main() {
    int N;
    string S;
    cin >> N >> S;
    int part = N / 3;
    string head = S.substr(part, part);
    string body = S.substr(0, part);
    string eyes = S.substr(2 * part, part);
    cout << head << body << eyes << endl;
    return 0;
}
```
## T2 (+)
```c++
#include <bits/stdc++.h>
using namespace std;

bool isSpecial(int x) {
    string s = to_string(x);
    int n = s.length();
    if (n % 2 == 0) return false;
    int mid = n / 2;
    return s[0] == s[mid] && s[mid] == s[n - 1];
}

int main() {
    int T;
    cin >> T;
    while (T--) {
        int L, R;
        cin >> L >> R;
        for (int i = L; i <= R; ++i) {
            if (isSpecial(i)) {
                cout << i << " ";
            }
        }
        cout << endl;
    }
    return 0;
}
```
## T3 (+4)
```c++
#include <bits/stdc++.h>
using namespace std;
const int MOD = 1145141923;

int main() {
    int N, M;
    cin >> N >> M;
    vector<int> A(N);
    for (int i = 0; i < N; ++i) {
        cin >> A[i];
    }

    if (N == 1) {
        cout << (A[0] == 0 ? M : 1) << endl;
        return 0;
    }

    unordered_set<int> used;
    for (int i = 0; i < N; ++i) {
        if (A[i] != 0) {
            if (used.count(A[i])) {
                cout << 0 << endl;
                return 0;
            }
            used.insert(A[i]);
        }
    }

    long long result = 1;
    int available = M - used.size();
    int zeroCount = 0;
    for (int i = 0; i < N; ++i) {
        if (A[i] == 0) {
            zeroCount++;
        }
    }

    if (available < zeroCount) {
        cout << 0 << endl;
        return 0;
    }

    for (int i = 0; i < zeroCount; ++i) {
        result = (result * (available - i)) % MOD;
    }

    cout << result << endl;
    return 0;
}
```
