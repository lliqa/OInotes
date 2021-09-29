## 题目分析

首先发现本问题有两层**嵌套**问题：

1. 判断**最大的**连续**白色**子**正**方形的**边长**（记作 $\text{len}$）是否等于 $i$。（判定性问题）
2. 对于 $\forall k \in \Z, 0 \leq k \leq n$，求出符合 $\text{len} = k$ 的合法染色**方案数**。（计数问题）

这是典型的 **DP 套 DP** 的应用。  
简单介绍一下 DP 套 DP：

> 将内层 DP 的结果作为外层 DP 的状态进行 DP 的方法。将内层 DP 及其转移图看作一颗自动机，DP 套 DP 也即是在自动机上 DP 的过程。

回到本题。

发现直接求 $\text{len} = k$ 的方案数不好求，一个经典的转化是求 $\text{len} \leq k$ 的方案数然后差分求出 $\text{len} = k$ 的方案数。  
现在考虑对于每个 $k$ 求出 $\text{len} \leq k$ 的方案数。

首先解决判定性问题，即如何求出 $\text{len}$。  
借助**悬线法**的思想，逐行考虑，假设当前为第 $i$ 行，求出每一**列**向上延展不遇到障碍的**最长长度**。观察题面发现 $n \leq 8$，特别小，于是可以用 $n$ 位 $k + 1$ 进制数来表示。但这样会使内层 DP 状态数过多，导致 $\color{darkblue}\texttt{MLE}$。考虑去除冗余信息，事实上只需知道每相邻 $k + 1$ 列的**最长长度**的**最小值**即可。于是改写状态为 $n - k$ 位 $k + 1$ 进制数，第 $i - 1$ 位为 $i \sim i + k$ 的**最长长度**的**最小值**。这样，状态数大大减小。

然后解决计数问题，设 DP $f (i, s)$ 为考虑到第 $i$ 行，状态为 $s$ 时 $len \leq k$ 的方案数。  
转移先枚举第 $i$ 行的**染色方案**，再枚举第 $i - 1$ 行的状态，联立求出第 $i$ 行的状态，然后更新即可。注意，要保证每一步都合法。

然后再用开始提到的差分就做完了。

## 代码

```cpp
#include <cstdio>
#include <iostream>
#include <cstring>

using std::cin;
using std::cout;
using LL = long long;

const int N = 8;
const int SN = 1024;
const LL MOD = 1e9 + 7;

int test;

int n;
int Map[N + 5];// Map[i] 第 j - 1 位为 1 即代表 (i, j) 为障碍

int Pk[N + 5]; //Pk[i] 为 k 的 i 次方
LL F[2][SN + 5], Ans[N + 5]; //F 为 DP 数组，Ans[i] 为 len <= i 的方案数。

int main() {
  std::ios::sync_with_stdio (0);

  cin >> test;
  while (test--) {
    cin >> n;
    Ans[0] = Ans[n] = 1; // Ans[0] 只有一种即全染黑，Ans[n]包含所有方案。都可以预先求出从而减少 DP 次数
    for (int i = 1; i <= n; ++i) {
      Map[i] = 0;
      for (int j = 1; j <= n; ++j) {
        char tap;
        cin >> tap;
        if (tap == '*') Map[i] |= (1 << (j - 1)); 
        else Ans[n] = Ans[n] * 2 % MOD;
      }
    }for (int k = 2; k <= n; ++k) { // 图个方便，这里求的是 len < k 的方案数。
      int m = n - k + 1;
      memset (F[0], 0, sizeof (F[0])), F[0][0] = Pk[0] = 1;
      for (int i = 1; i <= m; ++i) Pk[i] = Pk[i - 1] * k;

      for (int i = 1; i <= n; ++i) { //i 为当前行
        int now = (i & 1), lst = ((i - 1) & 1); // 滚动数组
        memset (F[now], 0, sizeof (F[now]));
        for (int sc = 0; sc < (1 << n); ++sc) { // sc 为第 i 行的染色情况
          if (sc & Map[i]) continue; // 染了有障碍的店，不合法。
          
          for (int s = 0; s < Pk[m]; ++s) { // 枚举第 i - 1 行的状态
            int sn = 0;
            bool flag = true;
            for (int j = 1; j <= m; ++j)
              if (((sc >> (j - 1)) & ((1 << k) - 1)) == (1 << k) - 1) { //要 [j, j + k - 1] 列都是白色才能继承 i - 1 行状态
                if (((s / Pk[j - 1]) % k) == k - 1) { // 出现了 len >= k，不合法。
                  flag = false; break;
                }sn += (((s / Pk[j - 1]) % k) + 1) * Pk[j - 1];
              }
            if (!flag) continue;
            F[now][sn] = (F[now][sn] + F[lst][s]) % MOD;
          }
        }
      }Ans[k - 1] = 0;
      for (int s = 0; s < Pk[m]; ++s) Ans[k - 1] = (Ans[k - 1] + F[n & 1][s]) % MOD;
    
    }for (int i = 0; i <= n; ++i)
      cout << ((i == 0) ? Ans[i] : ((Ans[i] - Ans[i - 1]) % MOD + MOD) % MOD) << "\n"; // 差分
  }return 0;
}
```