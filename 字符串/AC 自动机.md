# AC 自动机学习笔记

## 概述

AC 自动机是一种建立在 Trie 上的自动机，拥有处理多模式串匹配的力量。



## 概念

AC 自动机中 Trie 上每一节点都是一个状态，同时也对应着某些模式串的**前缀**。

Trie 上的边即为状态之间的转移。

AC 自动机还拥有一个辅助数组 Fail。Fail 即**后缀链接**，连接了互为后缀的状态。



## 构建

考虑 Trie 中的当前结点 $p$ ：

1. 存在转移 $\text{Trans} [p, k] = q$，则 $\text{Fail} [q] = \text{Trans}[\text{Fail}[p],k]$
2. 不存在转移 $\text{Trans} [p, k]$，则 $\text{Trans}[p, k] = \text{Trans}[\text{Fail}[p],k]$

发现 $2.$ 实际上会修改 Trie 的结构，补全了一些不存在的边，使其变为 **Trie图**。

一些理解：

将 $\text{Trans}$ 看作是匹配中不断新增字符的过程，一切豁然开朗。

$\text {Fail}$ 是缩减前缀，$\text{Trans}$ 是增加后缀的同时调整前缀。



## 应用

### 自动机上 DP

#### 一般状态设计

$\text{F}(i, j)$ 为当前所在结点为 $i$ ，已处理长度为 $j$ 的 DP 值。

#### 一般转移

自动机上沿 $\text{Trans}$ 方向转移。

$\text{F} (\text{Trans}[i][k], j + 1) \gets^{opt} F(i, j) + \text{Val} (\text{Trans}[i][k]$) 

$\text{Val} (i)$ 为 $i$ 结点的贡献。AC 自动机上 DP 计算一段前缀的贡献是独立的，即每次单独计算**一个**前缀的贡献。

求出 $\text{Val} (i)$ 就需要从 $\text{Fail (i)}$ 处继承转移