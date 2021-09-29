## 题意

> 给一个长为 $n$ 的循环序列 $a$，序列中元素只有 $1, -1$。求有多少个位置，满足以该位置为起点的所有前缀和均 $>0$。

</br>
---


## 题目分析

令 $sum = \sum a_i$。

> 结论：答案为 $\max \{ sum, 0 \}$。

> 证明：  
> 数学归纳法。  
> $n \leq 2$ 时成立；  
> $n > 2$ 时，考虑序列中是否存在相邻的 $1, -1$：  
> 若不存在，要么序列中不存在 $1$，要么序列中所有 $1$ 后面都是 $1$ 即序列中元素都是 $1$。两种情况结论都成立。
> 如果存在，发现相邻的 $1, -1$ 只会对以这两个位置作为起点有影响，并且都不合法。而对其他位置的起点没有影响，因为前缀和累加到 $1, -1$ 这两个位置是不会变小。因此可以直接把 $1, -1$ 删去，变成规模为 $n - 2$ 的子问题。  
> 证毕。

## 代码实现

```cpp
#include <cstdio>

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

int test;

int n;

int main() {
  gi (test);
  while (test--) {
    gi (n);
    int sum = 0;
    for (int i = 1; i <= n; ++i) {
      int tap;
      gi (tap), sum += tap; 
    
    }pr ((sum > 0) ? sum : 0), putc ('\n');
  }return 0;
}
```