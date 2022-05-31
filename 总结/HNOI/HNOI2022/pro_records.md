[TOC]

前言：表格不支持数学公式，还是用 markdown 写进程表算了。

---

## 2022/02/16

### [BZOJ4671 异或图](https://hydro.ac/d/bzoj/p/4671)

设 $f_i$ 表示将 $n$ 个点分成 $i$ 组，组组之间无连边，组内随意连的方案数。$g_i$ 为将 $n$ 个点分成 $i$ 个连通块的方案数，所求为 $g_1$。有 $f_i = \sum \begin{Bmatrix}j\\ i\end{Bmatrix} g_j$，只需求出所有 $f_i$，就能斯特林反演求出 $g_1$。DFS 枚举子集划分，每个图仅保留组间边，求有多少个子集异或和为 0。答案即为 $2^{m-|S|}$，$|S|$ 为线性基大小。

---

### [CF932E Team Work](https://www.luogu.com.cn/problem/CF932E)

$$
\sum_{i=1}^n \dbinom{n}{i}i^k = \sum_{i=1}^n \dbinom{n}{i} \sum_{j} \begin{Bmatrix}k\\ j\end{Bmatrix} i^{\underline{j}} = \sum_{i=1}^n \sum_{j} \begin{Bmatrix}k\\ j\end{Bmatrix} \left( \dbinom{n}{i} i^{\underline{j}} = \dbinom{n-j}{i-j} n^{\underline{j}}\right)\\
= \sum_{i=1}^n \sum_{j} \begin{Bmatrix}k\\ j\end{Bmatrix} \dbinom{n-j}{i-j} n^{\underline{j}} = \sum_{j} \begin{Bmatrix}k\\ j\end{Bmatrix} n^{\underline{j}} \sum_{i=1}^n \dbinom{n-j}{i-j} = \sum_{j} \begin{Bmatrix}k\\ j\end{Bmatrix} n^{\underline{j}} 2^{n-j}
$$

$O(k^2)$ 计算。

---

### [CF1278F Cards](https://www.luogu.com.cn/problem/CF1278F)

先列出要计算的式子：

$$
\sum_{i=1}^n i^k \dbinom{n}{i} \left(\dfrac{1}{m}\right)^i \left(\dfrac{m-1}{m}\right)^{n-i}
$$

意思是枚举第一张牌为王牌的次数 $i$，$\dbinom{n}{i}$ 意思是从 $n$ 次洗牌选出 $i$ 次第一张是王牌（方案数），后面一坨是每个方案的概率。

然后用上题的方式化简成：

$$
\sum_{j} \begin{Bmatrix}k \\ j\end{Bmatrix} n^{\underline{j}} \dfrac{1}{m^j}
$$

$O(k^2)$ 计算。

---

### [HAOI2018 染色](https://www.luogu.com.cn/problem/P4491)

题目看着就一脸容斥样。先确定要求什么：对所有 $i$，求满足条件的颜色恰有 $i$ 种的方案数 $g_i$。  
“恰有” 很不好搞，考虑一个弱化的东西，强行钦定 $i$ 个满足条件的颜色及其位置，其他位置任意填，这个方案数记为 $f_i$。在 $f_i$ 中，一个有 $j$ 个满足条件的颜色的染色方案会被统计 $\dbinom{j}{i}$ 次，即 $f_i = \sum_{j \geqslant i} \dbinom{j}{i} g_j$。二项式反演可得 $g_i = \sum_{j \geqslant i} (-1)^{j-i} \dbinom{j}{i} f(j)$。把所有东西展开后发现是个卷积的形式，用 NTT 加速。另外，本题模数 $1004535809$ 的原根为 $3$。

---

## 2022/02/17

### [LOJ6485 LJJ 学二项式定理](https://loj.ac/p/6485)

单位根反演入门题。

需要求下式：

$$
\sum_{i=0}^n \dbinom{n}{i}s^i a_{i \bmod 4}
$$

变换可得

$$
\sum_{j=0}^3 a_j \sum_{i=0}^n \dbinom{n}{i}s^i [(i-j) \bmod 4 = 0] = \sum_{j=0}^3 a_j \sum_{i=0}^n \dbinom{n}{i}s^i \dfrac{1}{4}\sum_{k=0}^3 w_4^{(i-j)k} = \dfrac{1}{4}\sum_{j=0}^3\sum_{k=0}^3 a_j w_4^{-jk} \sum_{i=0}\dbinom{n}{i}s^i w_4^{ik}
$$

对最后面那一坨与 $i$ 相关的和式用二项式定理化简可得

$$
\dfrac{1}{4}\sum_{j=0}^3\sum_{k=0}^3 a_j w_4^{-jk} (sw_4^k + 1)^n
$$

---

## 2022/02/21

### [CF1245F Daniel and Spring Cleaning](https://www.luogu.com.cn/problem/CF1245F)

注意到 $a+b = a \oplus b$ 当且仅当 $a \And b = 0$，即 $a, b$ 不能在二进制下某一位同时为 $1$。容斥掉下界，用数位 DP 沿着上界转移，设 $f (i, 0/1, 0/1)$ 表示从高位往低位考虑到第 $i$ 位，$a, b$ 是否贴上界的方案数。转移枚举当前位的所有可能情况 $01, 10, 00$ 转移。

---

### [CF1250E The Coronation](https://www.luogu.com.cn/problem/CF1250E)

用并查集维护两两字符串之间的关系。  
对于字符串 $s, t$，因为 $s$ 翻转等价于 $t$ 翻转，只考虑 $s$ 的翻转。

* 如果 $s$ 翻或不翻都和 $t$ 不相似，无解
* 如果 $s$ 翻或不翻都和 $t$ 相似，不考虑
* $s$ 与 $t$ 相似，连边 $(s, t), (s', t')$
* $s$ 翻转后与 $t$ 相似，连边 $(s', t), (s, t')$

一个联通块的意义表示其中所有点的状态必须同时满足或干脆都不满足。根据连边方式可知图是对称的，考虑每一对对称的联通块，必须满足其中之一的所有状态，贪心选择需要翻转次数少的那块。

---

## 2022/02/22

### [CF613D Kingdom and its Cities](https://www.luogu.com.cn/problem/CF613D)

建虚树，然后树形 DP，设 $f(i, 0/1)$ 表示考虑到以 $i$ 为根的子树，有/没有 联通的重要城市的最小代价，简单转移。

---

## 2022/02/23

### [HNOI2014 世界树](https://www.luogu.com.cn/problem/P3233)

先建虚树，然后在虚树上两遍 DFS 求出每个点的最近议事处，最后一遍 DFS 求答案。考虑调整法，先把所有点最优答案设成根的最优答案，到虚树上一个点时如果与父亲最优答案不一样，倍增找到分界点，把原树中分界点的子树设成该点的最优答案。正确性不难证明。

---

### [LuoguP1453 城市环路](https://www.luogu.com.cn/problem/P1453)

基环树上最大独立集，先找环，对每个子树 DP 完，再合并环上答案。

---

### [ZJOI2008 骑士](https://www.luogu.com.cn/problem/P2607)

做法同上，只不过给的图是基环树森林，对每个连通块分别求解。

---

## 2022/02/24

### [BZOJ4144 Petrol](https://hydro.ac/d/bzoj/p/4144)

因为起点终点都是加油站，考虑原图中每一条边 $(u, v, w)$，假设存在一条起点终点均为加油站的路径 $S \to T$ 经过了此边，设到 $u$ 点时油量为 $b - dis (x, u)$（$x$ 为**路径上** $u$ 前面最近一个加油站），记 $y$ 为原图中离 $u$ 最近加油站，那么到 $u$ 时去一趟 $y$ 补充燃料再返回一定不劣。因为 $dis (y, u) \leq dis (x, u)$，所以有 $b - dis (y, u) \geq b - dis (x, u)$，同理，到达 $v$ 去一趟离 $v$ 最近的加油站 $z$ 也是不劣的。  
整理这些信息，可以得到在一个点时的最优油量是固定的且也是保证有解的最宽条件，因此每条边对答案的限制也是互相独立的。要经过 $(u, v)$ 这条边必须满足且仅需满足 $b \geq dis (x, u) + w + dis (z, v)$，将原图所有边重赋权跑 Kruskal 即可。

参考：

* https://www.cnblogs.com/Paul-Guderian/p/10204966.html
* https://www.cnblogs.com/Mychael/p/9093111.html
* https://www.cnblogs.com/CQzhangyu/p/7898644.html

---

### [LuoguP5464 缩小社交圈](https://www.luogu.com.cn/problem/P5464)

要满足是一颗树等价于连通且无环。连通相当于选出的所有线段并是一段区间，无环相当于不能有一个位置被超过 $2$ 条线段覆盖，设 $f(i, j)$ 表示最后一条线段选 $i$，倒数第二条选 $j$ 的方案数，转移分情况用前缀和优化。

---

## 2022/02/25

### [POI2018 Plan metra](https://www.luogu.com.cn/problem/P5959)

如果 $1$ 和 $n$ 直接相连，那么边 $(1, n)$ 是可以随意变化的，只要对于所有 $i$，满足 $|dis (i, 1) - dis (i, n)|$ 均相等就有合法构造方案。  

否则 $1, n$ 路径上有别的点，一定是 $dis (i, 1) + dis (i, n)$ 最小的哪些点。把这些路径上点弄下来，对于其他点 $j$，只关心是否存在路径上点 $i$ 满足 $dis (j, 1) - dis (j, n) = dis (i, 1) - dis (i, n)$，若存在则连边 $(i, j, dis (j, 0) - dis (i, 0))$，否则无解。

## 2022/02/28

### [AGC030D Inversion Sum](https://www.luogu.com.cn/problem/AT4513)

考虑一下答案长啥样：$ans = \sum_{opt} \operatorname{invcnt}(opt)$，其中 $opt$ 为一个操作序列。$opt$ 的总数为 $2^q$，转换前式可得：

$$
ans = 2^q \sum_{opt} \dfrac{1}{2^q} \operatorname{invcnt}(opt) = 2^q E (invcnt)
$$

期望线性性可知，只需求出每一对 $i, j$ 的期望贡献即可。设 $f (i, j, k)$ 表示考虑完了前 $k$ 个操作，$a_i > a_j$ 的概率。根据交换操作 $x, y$ 转移，只会转移到点对中存在 $x, y$ 的状态，这样的状态数是 $O(n)$ 的，暴力枚举即可。此外，状态第三维可以滚动节省空间。

### [POI2016 Korale](https://www.luogu.com.cn/problem/P5967)

求第 $k$ 小价值是一个经典模型，用堆维护二元组，增量法扩展。  
求出第 $k$ 小价值后，搜索输出方案。

## 2022/03/03

### [JOI2021 Final 雪玉](https://www.luogu.com.cn/problem/P7405)

点 $i$ 只可能得到 $[A_{i-1}, A_{i+1}]$ 区间中的贡献，记录 $w$ 前缀和的最左/右位置，二分找到有交的位置，计算答案。  

## 2022/03/09

### [JOI2021 Final 集合写真](https://www.luogu.com.cn/problem/P7406)

先确定一个合法的序列长啥样。  
考虑 $1$ 的位置，$1$ 的前面一定是 $2$，$2$ 的前面一定是 $3$，……前提是不会撞墙。进一步分析可得一个合法的序列可以被划分成若干连续递减段，设第 $i$ 段为 $[l_i, r_i]$，还要满足 $r_j < l_i(j < i)$。预处理一些信息就能 DP 求解。复杂度 $O(n^2)$。

## 2022/03/12

### [省选联考2020 冰火战士](https://www.luogu.com.cn/problem/P6619)

来补个代码。  
比赛的过程不重要，消耗总能量就是两倍的最小能量。设 $f (x)$ 表示温度为 $x$ 时的冰系战士总能量，$g (x)$ 表示温度为 $x$ 时的火系战士总能量。$f(x)$ 单增，$g(x)$ 单减，因此 $\min \{f(x), g(x) \}$ 是一个单峰函数。由于存在相同值域段，三分正确性不能保证。最优的温度一定是某个战士的温度（如果不是，调整到右边第一个战士的温度一定更优），先把操作转化为单点修改、前缀查询，在树状数组上二分找到最大的 $k$ 满足 $f (k) < g (k)$，最优能量能在 $k, k+1$ 处取到。如果 $k$ 处能量大，答案就在 $k$ 处取到。否则，为保证温度最大，还要二分找到最靠右的 $k'$ 满足 $g(k') = g(k)$。

## 2022/03/16

### [LuoguP3792 由乃与大母神原型和偶像崇拜](https://www.luogu.com.cn/problem/P3792)

先将所有数离散化，为保留值域连续的性质，把所有数本身和其加一一起离散化。  
然后考虑一个随机算法：
给值域上每个数随机一个权值，每次查询区间 $[l, r]$ 时先找到该区间可能的值域区间 $[vl,vr]$，然后查询 $[l,r]$ 的区间权值异或和，对比 $[vl, vr]$ 的值域区间异或和，即可判断值域是否连续。

### [CF1187F Expected Square Beauty](https://www.luogu.com.cn/problem/CF1187F)

$$
E (B^2(x)) = E ((\sum_{i=1}^n [x_i \neq x_{i-1}])^2) = \sum_{i, j} E ([x_i \neq x_{i -1}][x_j \neq x_{j-1}])
$$

然后一路平推。

### [PA2013 Konduktorzy](https://www.luogu.com.cn/problem/P4857)

大概思路是先把所有检票员在保证合法的情况下尽可能地移动，然后对剩下的指令用堆来模拟。  
具体来说找到最大的 $x$ 满足 $\sum \lceil \dfrac{x}{a_i} \rceil \leq n$，这说明所有检票员一定都能移动 $> \lfloor \dfrac{x - 1}{a_i} \rfloor$ 步，剩下的步数就能用堆模拟了。

## 2022/03/22

### [CF1236F Alice and the Cactus](https://www.luogu.com.cn/problem/CF1236F)

先把式子化成 $E(x^2) - E^2(x)$。  
由于图是仙人掌，连通块数量 = 点数(a) - 边数(b) + 环数(c)，于是相当于要求 $E((a+b-c)^2) - E^2(a+b-c)$，根据期望线性性，不停拆分贡献。最后相当于要求点点、点边、点环、边边、边环、环环 同时出现对数的期望，大力分类讨论。

### [CF1342F Make It Ascending](https://www.luogu.com.cn/problem/CF1342F)

状压 DP。考虑操作的意义，最终得到的序列每个元素都是原序列一个子集和，且互不相交，并为全集。增量法，设 $f (i, s, j)$ 表示构造到了最终序列第 $i$ 位，已经使用了原序列中集合 $s$，且最终序列第 $i$ 位对应原序列第 $j$ 位，最小的该位权值。转移枚举第 $i+1$ 位使用的集合，在保证序列单调同时贪心的把对应位调到最小，就能给后面状态提供更多转移空间。输出方案格外记录。复杂度 $O(n^2 3^n)$。

## 2022/03/23

### [CF710F String Set Queries](https://www.luogu.com.cn/problem/CF710F)

由于贡献有可减性，可以不管删除操作，只研究插入操作和在线查询。

查询可以用 AC自动机做，但动态维护 AC自动机的转移图和 fail树的复杂度过高。暴力做查询复杂度是 $O(\sum|S|)$，插入为 $O(m\sum {|s_i|})$。  
一个用来平衡这种强制在线复杂度的做法叫“二进制分组”。大概思路是维护 $\log n$ 个自动机，自动机大小分配就是当前串数的二进制分解。查询时在每个自动机上都做一遍，插入串时仅对该串建 AC自动机，然后不断合并相同大小的自动机，模拟串数 + 1 时的所有进位操作。  
然后分析复杂度：查询 $O(\sum|S| \log n)$。每次合并自动机的复杂度是 $O(\sum {|s_i|})$，因为每个串所在自动机合并一次后大小就翻倍，所以每个串最多被合并 $\log n$ 次，总插入复杂度是 $O(\sum {|s_i|} \log n)$

### [COCI2015 Divljak](https://www.luogu.com.cn/problem/P5840)

对所有 $S$ 建 AC自动机，把 fail树拿出来，添加字符串时找到每个前缀对应的 fail树上点标记，表示这些点的祖先都被该字符串包含。离线询问然后线段树合并。

## 2022/03/24

### [POI2017 Flappy Bird](https://www.luogu.com.cn/problem/P5957)

首先注意到一个性质：$(x_1, y_1) \to (x_2, y_2)$ 向上向下走的次数是唯一确定的。要最小化向上走的次数，只需最小化经过最后一个障碍物时的 $y$ 坐标即可。
另外，如果按障碍物 $x$ 轴分段，任意时刻能到的 $y$ 坐标是一段区间中同奇偶的整数。不断更新当前 $y$ 坐标的合法区间，最后取左端点即可。

### [USACO12OPEN Balanced Cow Subsets G](https://www.luogu.com.cn/problem/P3067)

折半搜索。
分成两个大小为 $\dfrac{n}{2}$ 的部分进行 $O(3^n)$ 的搜索，然后 $O(zoe)$ 合并答案。

## 2022/03/29

### [CF704B Ant Man](https://www.luogu.com.cn/problem/CF704B)

连续段 DP。从小到大插入元素，设 $f (i, j)$ 表示已插完前 $i$ 个元素，构成了 $j$ 个连续段。转移考虑新增一个元素的位置：

1. 新建一段
2. 插到一段头或尾
3. 合并两段

另外要注意满足首尾元素的限制。

### [JOI Open 2016 摩天大楼](https://loj.ac/p/2743)

先将元素按 $a_i$ 升序排序，拆分贡献去掉绝对值，$a_{i+1} - a_i$ 的贡献次数就是相邻的分别满足 $\leq i, > i$ 的点对数。这个点对数其实就是 $[1, i]$ 所有元素构成的连续段的端点数。考虑连续段 DP，设 $f (i, j, k, d)$ 表示已插入前 $i$ 个元素，构成了 $j$ 个连续段，当前权值和为 $k$，已固定了 $d$ 个边界的方案数。转移分情况讨论。

## 2022/03/30

### [CQOI2017 小 Q 的表格](https://www.luogu.com.cn/problem/P3700)

分析给出的两个条件，手玩可以发现能互相影响的两个位置 $(a, b)$ 和 $(c, d)$ 一定满足 $\dfrac{f (a, b)}{ab} = \dfrac{f(c, d)}{cd}$。  
考虑修改 $f (a, b)$ 会波及哪些点。根据条件2 可得 $(a, b)$ 和 $(a, b + ka)$ 是能互相影响的。即 $(a, b)$ 和 $(a, b \bmod a)$ 能互相影响。可以把这看作是辗转相除的过程，最终一定能到达 $(\gcd (a, b), \gcd (a, b))$。两个条件都不会影响坐标 $gcd$ 的变化，即相互影响的所有坐标 $gcd$ 都相同，不同 $gcd$ 相互独立。综上，一次修改仅改变相同 $gcd$ 的所有位置，又根据手玩出的结论每个 $f$ 都能被对角线上的 $f$ 表示，因此只修改对角线上的 $f$ 即可。

一次询问相当于要求：

$$
\sum_{d=1}^k f (d, d)\dfrac{1}{d^2} \sum_{i=1, j=1}^{k} [\gcd (i, j) = d]ij
$$

经过化简，记 $g (n) = \sum_{i=1}^n i^2 \varphi(i)$，所求即为

$$
\sum_{d=1}^k f(d, d) g(\lfloor \dfrac{k}{d} \rfloor)
$$

修改为单点修改，查询为整除分块套区间和。因为查询复杂度有个 $\sqrt {n}$ 的系数，需要平衡复杂度，分块维护前缀和支持 $O(\sqrt{n})$ 单点修改，$O(1)$ 区间查询。

### [LuoguP3396 哈希冲突](https://www.luogu.com.cn/problem/P3396)

根号分治。  
如果 $p > \sqrt {n}$，那么查询暴力找所有满足 $i \bmod p = x$ 的 $i$ 的复杂度是 $O(\sqrt {n})$，修改 $O(1)$。  
如果 $p \leq \sqrt{n}$，$p$ 一共只有 $\sqrt {n}$ 个，维护每个 $(p, x)$ 的答案，修改 $O(\sqrt{n})$，查询 $O(1)$。

### [CF1515E Phoenix and Computers](https://www.luogu.com.cn/problem/CF1515E)

先想想手动开启的电脑集合长啥样，一定是一堆连续段，相邻连续段之间只有一个空位，这个空位会自动开启。  
连续段是相互独立的，考虑一个连续段的贡献怎么算。设 $g (n)$ 表示手动开启长为 $n$ 的连续段的方案数。枚举第一次开哪个，那么一定会从这个位置向两侧开，有 $g (n) = \sum_{i=1}^n \binom{n-1}{i-1} = 2^{n-1}$。  
然后考虑 DP 计数，设 $f (i, j)$ 表示前 $i$ 个位置分成 $j$ 个连续段的方案数，转移枚举最后一段长度，系数是 $g (len) \times \binom{i-j+1}{len}$。

### [CF1372E Omkar and Last Floor](https://www.luogu.com.cn/problem/CF1372E)

$\sum_{i=1}^m q_i$ 一定，要使 $\sum_{i=1}^m q_i$ 尽量大，肯定是贪心地把 $1$ 堆到一些列上去。但不好确定具体是哪些列、什么顺序。考虑用区间 DP 来描述这个状态，设 $f (i, j)$ 表示列 $[i, j]$ 的最大贡献。转移枚举一列 $k \in [i, j]$，把列 $[i, j]$ 中所有包含 $k$ 的段的 $1$ 全部堆到 $k$ 列，$f (i, j) = \max_k \{ f (i, k - 1) + f (k + 1, j) + val (k, i, j) \}$

## 2022/04/04

### [省选联考2021 B卷 取模](https://www.luogu.com.cn/problem/P7521)

考虑枚举模数 $a_k$ 咋做。
把其他所有的 $i$ 对应的 $a_i \bmod a_k$ 丢到新数组 $b$，然后分情况讨论：

1. $b_i+b_j < a_k$：双指针。
2. $b_i+b_j \geq a_k$：因为 $b_i, b_j < a_k$，因此 $b_i + b_j \bmod a_k = b_i + b_j - a_k$，找最大的两个 $b$ 就行。

于是得到了 $O(n \log n)$ 解决固定模数的做法。  
考虑优化，降序枚举 $a_k$，如果 $a_k - 1 \leq ans$ 则跳过，其中 $a_k - 1$ 为 $a_k$ 的贡献上界，$ans$ 为当前答案。

这样做的复杂度是什么？  
降序枚举的过程中 $ans$ 不降，$a_k$ 不增，于是会进行操作的模数是一段后缀。假设这段后缀为 $[p, n]$。最终状态有 $ans \geq (a_i+a_{i+1}) \bmod a_{i+2} \geq a_i + a_{i+1} - a_{i+2} \ (p \leq i < n - 1)$。  
改写不等式可得

$$
a_{i+2} - ans \geq a_i - ans + a_{i+1} - ans
$$

记 $f_i = a_i - ans$，即有

$$
f_{i+2} \geq f_i + f_{i+1}
$$

因为 $f_n = a_n - ans \leq V=10^8$，又有 $f_i > 0 \ (i \geq p)$，$f_i$ 的增长速度大于 $fib$ 数，于是 $f_n$ 坍缩 $O(\log V)$ 次接近 $0$，即这段后缀长度为 $O(\log V)$，于是复杂度为 $O(n \log n \log V)$。

## 2022/04/05

### [ARC070B No Need](https://www.luogu.com.cn/problem/AT2346)

如何判断一个数字 $a_i$ 是否“可有可无”：

1. $a_i < k$
2. 不存在一个不包含 $i$ 的集合 $S$，满足 $k - a_i \leq V(S) < k$

预处理每个后缀每个值是否存在子集后，就能 $O(k)$ 判断数字合法。

## 2022/04/06

### [CF484E Sign on Fence](https://www.luogu.com.cn/problem/CF484E)

题目要求最大化最小值，不难想到二分。二分最小值 $x$，问题转化为判定 $[l, r]$ 所有 $\geq x$ 的位置能否形成 $\geq k$ 的连续段。可以先用主席树预处理。查询时二分然后在主席树上询问。

### [CF449C Jzzhu and Apples](https://www.luogu.com.cn/problem/CF449C)

估计答案上界：

* $1$ 不能配对
* 对于所有质数 $p$，若 $2p > n$，则 $p$ 不可能配对。
* 剩下的数若有奇数个，则至少一个不能配对。

如何达到上界：

从大到小枚举质数 $p \ (2p \leq n)$，提出 $p$ 所有未匹配的倍数，有偶数个则直接两两匹配；否则留 $2p$，剩下两两匹配。

证明：

最后枚举到 $p=2$ 时，已经匹配了偶数个数。剩下的所有数都是 $2$ 的倍数。  
若剩下偶数个，则两两完全匹配；否则，说明可能匹配的总数也是奇数个，必然至少剩下一个，也能达到上界。

### [CF1559D2 Mocha and Diana (Hard Version)](https://www.luogu.com.cn/problem/CF1559D2)

首先可以归纳证明最多加边数就是 $n-1 - \max \{m1, m2\}$，并且无论以何种顺序尽量加边，都能达到这个上界。  
因此，首先可以尽量连 $(1, i)$。能与 $1$ 相连的点满足在两个图中均与 $1$ 不连通。把 $1$ 能连的边连完后，记 $A$ 为图一中 $1$ 连通的点集，$B$ 为图二中 $1$ 连通的点集。有 $A \cup B = U, \overline{A} \cap \overline{B} = \varnothing$。如果 $A, B \neq U$，那么剩下的连边能且仅能横跨 $\overline{A}, \overline{B}$。维护 $\overline{A}, \overline{B}$ 中的点，每次各取出一个点相连，然后不断弹出不满足条件的点。

总结：上面两题的共同之处在于都能从==答案上界的估计==入手，进而考虑能否达到上界，得出构造方案。

### [CF741C Arpa’s overnight party and Mehrdad’s silent entering](https://www.luogu.com.cn/problem/CF741C)

第一印象是一道 SAT 题，但相邻三人的逻辑关系难以描述。  
把关于相邻三人的限制改为 $2i, 2i-1 \ (1 \leq i \leq n)$ 的食物必须不同。这个限制严格强于原本的限制，即满足该限制的决策集合是满足原本限制的决策集合的子集。  
但同时，在这个缩小的决策空间中限制得到了简化。把所有不能选相同食物的点对连边，问题转化为求一个合法的二分图染色。分析可知该图连续的一段边一定是邻边、情侣边交错，且所有点度数为 $2$，不可能存在奇环，因此一定存在解。（ps：如果不存在解不能说明原问题无解，此时缩小决策空间不再适用。）

总结：当限制很松时，往往决策空间难以简单描述。可以尝试强化限制，缩小决策空间。但前提是在缩小的决策空间中的解能代表原问题的解。

## 2022/4/12

### [CF986C AND Graph](https://www.luogu.com.cn/problem/CF986C)

一个找连通块数目的方法是：每次找到一个还未染色的点，对其所在连通块染色。染色的次数就是连通块个数。  
此题中，一个点 $x$ 和补集的所有子集有连边。  
建立 $2^n$ 个辅助点 $x'$，$x'$ 连向所有去掉一位的子集 $y'$，集合中的点 $x$ 连边 $x' \to x \to (U - x)'$。  
这样只需从 $x$ 出发在辅助图上遍历，就能快速找到和 $x$ 连通的所有点。时间复杂度 $O(m + 2^n)$。

### [CF1368E Ski Accidents](https://www.luogu.com.cn/problem/CF1368E)

点数的限制非常奇怪，先不管他。  
考虑删边后的图，因为每条路径至多有一条边，所以一定能分成两个不交点集 $A, B$，$A$ 为路径的起点集合，$B$ 为路径的终点集合。特别的，独立点放到 $A$ 集合中。  
然后再考虑上删掉的点集 $C$，一共三个点集 $A, B, C$，他们互不相交，并为全集。而本题的目标就是找到一个合法的分配点集方案，满足 $|C| \leq \dfrac{4}{7}n$。  

发现依旧难以下手，尝试按照拓扑序直接构造：

* $A$：入度为 $0$ 或入点均属于 $C$。
* $B$：入点无 $B$ 有 $A$。
* $C$：入点有 $B$。

首先这样的构造一定满足合法要求。然后考虑 $|C|$ 的大小限制。因为 $B$ 中点一定有 $A$ 的入点，$C$ 中点一定有 $B$ 的入点，而每个点出度至多为 $2$，于是有 $|C| \leq 2|B| \leq 4|A| \implies |C| \leq \dfrac{4}{7}n$。

### [CF1481E Sorting Books](https://www.luogu.com.cn/problem/CF1481E)

关键性质：
1. 每本书最多操作一次。
2. 最小化操作次数 $\iff$ 最大化保留位置数。

如果确定了保留哪些位置，把要操作的位置拿出来，在保留的序列后面能摆成任何顺序。  
考虑判定一个保留方案是否合法。经分析，一个合法的保留方案在序列上分为两部分：前一部分完整地保留若干颜色，且不同颜色之间不能交叉，后一部分可以保留若干相同颜色，不要求完整。对前一部分的方案 DP 求出每个前缀的最大保留数，枚举两部分的分隔点，分别取到最优。

## 2022/4/15

### [CF348C Subset Sums](https://www.luogu.com.cn/problem/CF348C)

不好直接维护。注意到 $\sum |S| \leq 10^5$，考虑根号分治，把所有 $S$ 按 $|S| \leq \sqrt{10^5}$ 与否分成小块和大块。

预处理一个数组 $C (i, j)$ 表示大块 $i$ 与块 $j$ 的重复位置数。

大块 $i$ 维护累加标记 $add_i$ 和答案 $v_i$。  
下记操作块编号为 $p$。

查询：

* 小块：包含的 $\sum a$ 加上 $\sum_i add_i \times C (i, p)$
* 大块：$v_p$

修改：

* 小块：包含的 $a \gets a + x$，大块 $v_i \gets v_i + x \times C (i, p)$
* 大块：$add_p \gets add_p + x$，大块 $v_i \gets v_i + x \times C (i, p)$

时间复杂度 $O(n\sqrt{n})$。（$n, m, siz$ 均同阶）