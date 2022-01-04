## 题意

给一个长为 $n$ 的字符串，仅由 $\texttt{a, b, c}$ 三种字符组成。

可以对其进行如下两种操作任意次数：

1. 选择两个相邻字符，将第一个字符替换成第二个
2. 选择两个相邻字符，将第二个字符替换成第一个

求能得到多少个串满足 $\texttt{a, b, c}$ 三种字符两两数量差 $\leq 1$。

$n \leq 150$

</br>
 
## 分析

设原串为 $S$，考虑任意所得串 $T$ 的性质。  
易发现，$T$ 串中，一段连续的相同字符必定对应 $S$ 串的某个字符，$S$ 串的某个字符也必定对应 $T$ 串中一段连续的相同字符（可以长度为0）。而且，$S$ 串中两个字符的位置关系在 $T$ 串中的依然得到了保持（即，两对应字符区间不相交，且位置关系与 $S$ 串两字符位置关系相同）。  
进一步的，设 $\operatorname{Dec} (X)$ 表示将串 $X$ 合并所有相邻且相同字符得到的新串，那么原串中的任意子序列 $S_t$ 能操作得到串 $T$ 当且仅当 $S_t = \operatorname{Dec} (T)$。

最后考虑如何统计合法串 $T$ 数量。为防止算重，使用代表元法，钦定每个 $T$ 仅在第一个出现（字典序最小）的等于 $\operatorname{Dec} (T)$ 的 $S$ 子序列处统计到。

万事俱备。用 DP 来描述这些过程，设 $f (i, cnt_a, cnt_b, cnt_c)$ 表示现在在 $S$ 串位置 $i$，已知 $T$ 串中 $\texttt{a, b, c}$ 三种字符数量的串数。考虑转移：

$$
f (i, cnt_a, cnt_b, cnt_c) 
\to^{+}
\begin{cases}
  f (nxt (i, a), cnt_a+1, cnt_b, cnt_c) \\
  f (nxt (i, b), cnt_a, cnt_b+1, cnt_c) \\
  f (nxt (i, c), cnt_a, cnt_b, cnt_c+1)
\end{cases}
$$

> 其中 $nxt (i, k)$ 为 $i$ 后面第一个字符 $k$ 的位置（包含 $i$）。

在 $cnt_a + cnt_b + cnt_c = n$ 处统计答案即可。  
因为 $cnt_{a/b/c}$ 上界在 $\frac{n}{3}$ 处左右，复杂度为 $O(\frac{n^4}{27})$。

## 代码实现

```cpp
#include <cstdio>
#include <algorithm>

using ll = long long;

const int N = 150;
const ll MOD = 51123987;

int n;
char S[N + 5];

int Nxt[N + 5][3];
int F[N + 5][N / 3 + 3][N / 3 + 3][N / 3 + 3];

int main() {
  scanf ("%d%s", &n, S + 1);
  for (int i = n; i >= 0; --i) {
    for (int j = 0; j < 3; ++j) Nxt[i][j] = Nxt[i + 1][j];
    if (i) Nxt[i][S[i] - 'a'] = i;
  
  }int ans = 0;
  F[0][0][0][0] = 1;
  for (int i = 0; i <= n; ++i)
    for (int j = 0; j <= n / 3 + 1; ++j)
      for (int k = 0; k <= n / 3 + 1; ++k)
        for (int l = 0; l <= n / 3 + 1; ++l) {
          int f = F[i][j][k][l];
          if (Nxt[i][0]) (F[Nxt[i][0]][j + 1][k][l] += f) %= MOD;
          if (Nxt[i][1]) (F[Nxt[i][1]][j][k + 1][l] += f) %= MOD;
          if (Nxt[i][2]) (F[Nxt[i][2]][j][k][l + 1] += f) %= MOD;

          if (j + k + l == n && std::abs (j - k) <= 1 && std::abs (j - l) <= 1 && std::abs (k - l) <= 1) (ans += f) %= MOD;
        }
  printf ("%d\n", ans);
  return 0;
}
```