[TOC]

## 简介

Min_25筛 因由 Min_25 发明而得名，根据其算法思想又名 Extended Eratosthenes Sieve，即扩展埃筛。

以 $O(\dfrac{n^{\frac{3}{4}}}{\log n})$ 或 $O(n^{1 - \epsilon})$ 时间复杂度解决一类积性函数的前缀和问题。

要求：
1. $f(p)$ 是一个关于 $p$ 的项数较少的多项式或可以快速求值。
2. $f(p^c)$ 可以快速求值。

## 记号与约定

1. $p$ 为 $[1, n]$ 中全体质数集合。
2. $p_i$ 为第 $i$ 小的质数。
3. $mip (i)$ 为 $i(i > 1)$ 的最小质因子。

## 算法流程

### 1. 求 $\sum_{i \in p} f(i)$

构造一个完全积性函数 $f'(i)$，在 $p$ 中取值与 $f(i)$ 相同，所求即为 $\sum_{i \in p} f'(i)$。  
分析埃筛过程，从 2 开始小到大枚举数，若当前数未被标记，则标记当前数所有非自身倍数。最后未被标记的数即为全体质数。  
扩展该思想，设 $g (n, i)$ 表示 $[1, n]$ 的正整数，经过前 $i$ 小质数的筛选后未被标记位置的 $f'$ 之和。形式化来说，有：

$$
g (n, i) = \sum_{j=2}^n [mip (j) > p_i \operatorname{or} j \in p] f'(j)
$$

考虑从 $g (*, i - 1)$ 扩展到 $g (*, i)$，只要减去 $p_i$ 新标记到的所有位置的 $f'$ 之和。整理可得：

$$
g(n, i) = g (n, i - 1) - f'(p_i) \left( g(\lfloor \dfrac{n}{p_i} \rfloor, i - 1) - \sum_{j=1}^{i-1}f' (p_j) \right)
$$

括号中减去 $\sum_{j=1}^{i-1}f' (p_j)$ 是因为 $g(\lfloor \dfrac{n}{p_i} \rfloor, i - 1)$ 包括了前 $i - 1$ 小的所有质数。

最后，$g (n, |p|) = \sum_{i\in p}f'(i) = \sum_{i \in p}f(i)$，即为所求。

### 2. 求 $\sum_{i=1}^n f(i)$

依然从埃筛的思想上进行扩展，设 $s (n, i)$ 表示在经过前 $i-1$ 小质数筛选，并标记这些质数后未被标记位置的 $f$ 之和。形式化来说，有：

$$
s (n, i) = \sum_{i=2}^n [mip (i) \geq p_{i}]f(i)
$$

对质数和合数分别求和，整理可得：

$$
s (n, i) = g (n, |p|) - \sum_{j=1}^{i-1}f (p_j) + \sum_{k \geq j}\sum_{c=1}^{p_k^{c + 1} \leq n} f(p_k^c) s(\lfloor \dfrac{n}{p_k^c} \rfloor, k + 1) + f(p_k^{c+1})
$$

$s (n, 1) = \sum_{i=2}^n f (i)$，$s (n, 1) + f(1)$ 即为所求答案。

## 实现细节

1. $g (n, i)$ 和 $s (n, i)$ 中 $i$ 的取值类型为 $\lfloor \dfrac{n}{x} \rfloor$，只有 $O(\sqrt{n})$ 个不同取值。对 $\leq \sqrt {n}$ 和 $> \sqrt{n}$ 的取值分别建立映射，实现离散化。
2. $g (n, i)$ 的第一维可以滚动优化。