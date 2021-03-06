# Test_2021_0316_Summary_LC

### 总览

|      题目      |             factor              |              phi               |       expr        |
| :------------: | :-----------------------------: | :----------------------------: | :---------------: |
| 期望/实际 用时 | $1 \mathrm{h}/ 45 \mathrm{min}$ | $1\mathrm{h} / 1.2 \mathrm{h}$ | $10 \mathrm{min}$ |
| 期望/实际 得分 |              $80$               |              $45$              |       $12$        |

1. 得分情况：$80 + 45 + 12 = 137$

2. 做题策略：

	* 顺序：$1 -> 3 -> 2$

	* 通读题目：$20\mathrm{min}$
	* 思考时间：$40\mathrm{min} + 20\mathrm{min}+5\mathrm{min}$



### 细节

* **factor**

  首先可以把求出最大真因数转化为求出最小质因数 	

  然后就能线性筛加上另一档部分分拿到 $80\mathrm{pts}$。

  结果我写个线性筛调了一年。

* **phi**

	用 $\mathrm{SGT}+\mathrm{bitset}$ 可以做到满分，直接维护质因数的存在状况。考场上我没有意识到数据纯随机的优秀性质，打了 $45 \mathrm{pts}$ 的裸暴力，甚至未使用线段树。 

	又调了一年。

* **expr**

	期望套上初中见过许多次的表达式求值。可惜我只会人口普查的 $12 \mathrm{pts}$ 。



### 总结

再一次意识到自己代码能力的薄弱，代码能力的提升需要持久的练习。

部分分得分能力也有待提高。

本场考试唯一值得肯定的地方是**factor**发现关键性质较快，并且想到了十分接近正解的 DP