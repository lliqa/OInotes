# 2021 NOIP_Summary_LC

## 总览

|   题目  |  报数  |  数列  |   方差  | 棋局 |
| :------------: | :---------------------------: | :----------------------------: | :---------------: | :-:|
| 期望/实际 得分 |              $100$               |              $100$              |       $20$        | $32$ |

1. 得分情况（民间数据）：$100 + 100 + 20 + 32 = 252$

2. 做题策略：

	* 顺序：$1 \to 4 \to 2 \to 3$

	* 通读题目：$5\mathrm{min}$

---

## 细节

**报数**

题目类型较为奇怪，和 NOIP 的风格有点出入。正解是类埃筛。

**数列**

首先感觉像个 DP。  
仔细读了几遍发现只需从小到大放数就能无后效性转移，设了很多维状态，调的有点久，不过最后还是过了。

**方差**

操作很熟悉，转化为交换差分数组相邻两项。但然后就没有思路了，写了个 $O(n!)$。看了下样例解释猜它可能是个 V 字形，可惜不会证明，也没有试着去实现。

结果这道题是最亏的。多测也没有，数据范围也开的小，一堆乱搞都能过或是拿到很高的分数。$O(2^n)$ 竟然能过 $n=50$。很遗憾。

**棋局**

是一道令人不适的题目，题面很长很丑。一开始就奔着 $32$ 去的，很快就过了样例，还不错。

---

## 总结

* 本次 NOIP 最遗憾的就是 **方差** 这一题了。哪怕在考场上写个玄学暴力也不止这点分数，或者大胆地实现一下猜想的做法。吸取教训吧。