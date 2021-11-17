## 题意

> 给一个长度为 $n$ 的序列 $a$，求有多少个子序列，满足子序列中元素两两异或和 $\geq x$。  
> $n \leq 3 \times 10^5, a_i, x < 2^{60}$

</br>
---

## 题目分析

注意到计数的对象是子序列，指关心选了哪些元素，而与顺序无关。  
先考虑如何判断一个子序列是否满足条件。两两异或和 $\geq x$ $\iff$ 最小异或和 $\geq x$。然后考虑最小异或和怎么求。

> 结论：若干非负整数的最小两两异或和等于将所有数排序后相邻两项的最小异或和。

> 证明：  
> 上述结论等价于 $\forall 0 \leq x < y < z$，$\min \{x \oplus y, y \oplus z \} < x \oplus z$（$\oplus$ 为按位异或操作）  
> 放到 $\texttt{01trie}$ 上来看，由于 $\texttt{01trie}$ 是二叉树，**不存在** 一个结点同时为 $3$ 个结点两两的 $\texttt{LCA}$，因此 $\texttt{LCA} (x, y)$ 和 $\texttt{LCA} (y, z)$ 深度皆大于或等于 $\texttt{LCA} (x, z)$。再反推回去即得证。

借助这个结论，发现可以先把原序列排序，然后就可以从前往后 DP 了。设 $f (i)$ 为以 $i$ 结尾的合法子序列数。

$$
f(i) = 1 + \sum_{j < i, a_j \oplus a_i \geq x} f (j)
$$

最后考虑优化 DP，即如何快速找到 $a_j \oplus a_i \geq x$ 的 $\sum f(j)$。这个可以用 $\texttt{01trie}$ 优化，把 $f (j)$ 挂到 $\texttt{01trie}$ 上根到 $a_j$ 的路径上，求 $f(i)$ 就在 $\texttt{01trie}$ 上像数位 DP 一样压上界走，累加答案。

</br>
---

## 代码实现

```cpp
#include <cstdio>
#include <algorithm>
 
namespace IO {
  const int SIZE = (1 << 21) + 1;
  char ibuf[SIZE], *iS, *iT, obuf[SIZE], *oS = obuf, *oT = oS + SIZE - 1, c, qu[55]; int f, qr;
  #define gc() (iS == iT ? (iT = (iS = ibuf) + fread (ibuf, 1, SIZE, stdin), (iS == iT ? EOF : *iS++)) : *iS++)
  inline void flush (){fwrite (obuf, 1, oS - obuf, stdout);oS = obuf;}
  inline void putc (char x){*oS++ = x;if (oS == oT) flush ();}
  template <class I>
  inline void gi (I &x) {
    for (f = 1, c = gc(); c < '0' || c > '9'; c = gc()) if (c == '-') f = -1;
    for (x = 0; c <= '9' && c >= '0'; c = gc()) x = x * 10 + (c & 15); x *= f;
  }template <class I>
  inline void pr (I x) {
    if (!x) putc ('0'); if (x < 0) putc ('-'), x = -x; while(x) qu[++qr] = x % 10 + '0',  x /= 10; while (qr) putc (qu[qr--]);
  }struct Flusher_ {~Flusher_(){flush();}} io_flusher_;
}using IO::gi;
using IO::putc;
using IO::pr;
 
using ll = long long;
 
const int N = 3e5;
const ll MOD = 998244353;
 
int n;
ll A[N + 5], x;
 
ll F[N + 5];
 
namespace Trie {
  const int SIZE = 1.8e7;
  const int LEN = 60;
 
  int rt = 1, tot = 1;
  struct Node {
    int Nxt[2];
    ll f;
  }S[SIZE + 5];
 
  void Ins (int id) {
    int p = rt;
    for (int i = LEN - 1; i >= 0; --i) {
      int k = (A[id] >> i) & 1;
      if (!S[p].Nxt[k]) S[p].Nxt[k] = ++tot;
      p = S[p].Nxt[k], S[p].f += F[id];
    }
  }ll Qry (ll val) {
    int p = rt;
    ll ret = 0;
    for (int i = LEN - 1; i >= 0; --i) {
      int k = (val >> i) & 1;
      if (x & (1ll << i)) p = S[p].Nxt[k ^ 1];
      else ret += S[S[p].Nxt[k ^ 1]].f, p = S[p].Nxt[k];
    }return ret + S[p].f;
  }
}using namespace Trie;
 
int main() {
  gi (n), gi (x);
  for (int i = 1; i <= n; ++i) gi (A[i]);
  std::sort (A + 1, A + 1 + n);
 
  ll ans = 0;
  for (int i = 1; i <= n; ++i)
    ans += (F[i] = (Qry (A[i]) + 1) % MOD), Ins (i);
  return pr (ans % MOD), 0;
}
```