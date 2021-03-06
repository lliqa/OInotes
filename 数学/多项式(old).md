* 拉格朗日插值

给出 $n + 1$ 个点 $(x_i, y_j)$ 来确定一个 $n$ 次多项式 $f(x)$.

现在仅考虑 $n = 2$ 的情况.

观察二次多项式的形式:

$$
f(x) = a_0 + a_1x+a_2x^2
$$

现在我们要求她在 $x_1$ 处取到 $y_1$, 在 $x_2$ 处取到 $y_2$, 还要在 $x_3$ 处取到 $y_3$. 感觉上就有点棘手, 因为也许当我试图满足 $x_1$ 的条件时 $x_2$ 就不满足了. 但这启发我这样想, 回想线性代数中的基本概念, 是否可以把 $f(x)$ 看成一个线性组合, 拆成若干个互相独立的 $f_i(x)$ 分别满足各自的条件然后相加?

就像这样:

$$
	f(x) = y_1f_1(x) + y_2f_2(x) + y_3f_3(x)
$$

其中 $f_i(x) = [x==x_i]$.

考虑如何构造 $f_i(x)$ : $f_i(x) = \prod_{j \neq i} \frac{x - x_j}{x_i - x_j}$. 思考一下是 $ok$ 的.

现在, 就能 $O(n^2)$ 地解决上面的问题了!

</br>

快速傅里叶变换 $FFT$

1. $n$ 次复根

$x^n=1$ 在复数意义下的解即为 $n$ 次复根 $w_n^k \ (k = 0, 1, 2, ..., n - 1)$. 单位复根记为 $w_n$.

$k = n - 1$ 说明解有 $n$ 个. 为什么? 首先需要用欧拉定理可得 :

$$
e^{i\theta} = \cos \theta + i \sin \theta
$$

观察上式, 可以理解为是模长为 $1$ 且幅角为 $\theta$ 的一个复数. 换一个角度, 把 $e^{i \theta}$ 看作复平面上复数 $(1, 0)$ 逆时针旋转 $\theta$ 后的结果. 

怎么样? 是不是有点意思了呢? 再来推一下式子:

$$
x^n=1=e^{i2k\pi}\\
\implies x = e^{i\frac{2k\pi}{n}}\\
k \in \Z
$$

考虑其周期性, 于是有 $k < n => k = 0, 1, 2, ..., n - 1$. 或者, 考虑 $w^k_n = (w_n)^k$, 复数相乘幅角相加, $w_n$ 幅角的 $n$ 倍等于 $2\pi$, 也就是说转 $n$ 次就会回到起点.

接下来有几个 $n$ 次复根的**重要**性质:

> 1. 周期性
>
> 	$w^{k+n}_n=w^k_n$
>
> 2. 消去引理
>
> 	$w^{pk}_{pn}=w^k_n$
>
> 3. 对称性
>
> 	$w^{k+\frac{n}{2}}_n=-w^k_n$
>
> 4. 折半引理
>
> 	$(w^k_n)^2=w^k_{\frac{n}{2}}$
>
> 5. 求和引理
>
> 	$\sum_{j=0}^{n-1} (w^k_n)^j=0 \ (k > 0)$

上面的性质从 $n$ 次单位根旋转的角度来看较易, 故不予证明.

1. 系数表示转点值表示 $DFT$

现在要计算 $n - 1$ 次多项式 $f(x)$ 在 $w^0_n, w^1_n, ..., w^{n - 1}_n$ 处的值. (假定 $n$ 为 $2$ 的次幂)

设 $f(x) = \sum_{i=0}^{n - 1} a_ix^i$.

将 $f(x)$ 每一项按次数奇偶分组得

$$
f(x) = (a_0 + a_2x^2 + a_4x^4 + ... + a_{n - 2}x^{n - 2}) + (a_1x + a_3x^3 + a_5x^5 + ... + a_{n - 1}x^{n - 1})
$$

设两个函数

$$
	g(x) = (a_0 + a_2x^1 + a_4x^2 + ... + a_{n - 2}x^{\frac{n - 2}{2}})\\
	h(x) = (a_1 + a_3x^1 + a_5x^2 + ... + a_{n - 1}x^{\frac{n - 2}{2}})
$$
	
把 $f(x)$ 用 $g(x)$ 与 $h(x)$ 表示

$$
f(x) = g(x^2) + x \ h(x^2)
$$
	
分别把 $w_n^k, \ w_n^{k + n / 2} \ (k < \frac{n}{2})$ 代入 $f(x)$ 得

$$
f(w_n^k) = g((w_n^k)^2) + w_n^k \ h((w_n^k)^2)\\
= g(w_{n / 2}^k) + w_n^k \ h(w_{n / 2}^k)\\
f(w_n^{k + n / 2}) = g((w_n^{k + n / 2})^2) + w_n^{k + n / 2} \ h((w_n^{k + n / 2})^2)\\
= g(w_{n / 2}^k) - w_n^k \ h(w_{n / 2}^k)
$$
	
> 上面的推导用到了性质 $1, 3, 4$.
	
观察发现 $f(w_n^k)$ 与 $f(w_n^{k + n / 2})$ 仅仅是一个符号的区别而已. 这样每次计算的规模仅为之前的 $\frac{1}{2}$. 因此根据主定理可知这样 $DFT$ 递归处理的过程时间复杂度为 $O(n\log n)$.
	
3. 蝴蝶变换

递归 $FFT$ 常数大, 并不优秀, 尝试将其改为非递归版.

追踪 $f(x)$ 每一项系数的最终去向, 发现 $a_i$ 最后的位置为 $rev (i)$. $rev (i)$ 为 $i$ 二进制反转后的值. 

正确性: 思考一下递归划分的过程, 每一段新的求解的区间下标会继承其父亲的所有奇下标或偶下标. 而这些下标在新的求解区间中会缩小为自身的 $\frac{1}{2}$.

即 $y_{i / 2} = x_i \ (i = 2k + 0 \ or \ 1)$ 而每一段的划分过程实际上会把下标二进制最低位为 $0$ 的放到左侧, 为 $1$ 放到右侧. 于是最后得到的最终序列就是下标二进制反转后的序列.
于是可以预先将所有系数作蝴蝶变换, 然后 $FFT$ 自底向上合并系数即可. (这里强烈建议模拟一下合并过程)

如何 $O(n)$ 求出 $rev (0 \ to \ (n-1))$?

直接上代码片段:

```cpp
  for (int i = 0; i < n; ++i) {
      Rev[i] = (Rev[i >> 1] >> 1);
      if (i & 1) Rev[i] |= (n >> 1);
  }
```

4. 点值表示下多项式相乘

这个 $O(n)$ 完事, 没什么好说的.

5. 点值表示转系数表示 $IDFT$

现在已知 $n - 1$ 次多项式 $f(x)$ 的点值表示 $\left\{(w^0_n, y_0), (w_n^1, y_1), ..., (w_n^{n - 1}, y_{n - 1})\right\}$, 要求 $f(x)$ 的系数表示 $\left\{a_0, a_1, ..., a_{n - 1}\right\}$.

单位根反演即可。具体实现只需将 $w_n$ 视作 $w_n^{-1}$，最后每一项乘上 $\dfrac{1}{n}$。
	
1. 代码实现

```cpp
#include <cstdio>
#include <cmath>
#include <algorithm>

typedef double Db;
const int N = 4e6 + 5;
const Db Pi = acos (-1.0);

struct Cpx {
	Db x, y;
	
	Cpx operator + (Cpx B) const { return (Cpx) {x + B.x, y + B.y}; }
	Cpx operator - (Cpx B) const { return (Cpx) {x - B.x, y - B.y}; }
	Cpx operator * (Cpx B) const { return (Cpx) {x * B.x - y * B.y, x * B.y + y * B.x; }
};

int n, m;
Cpx F[N + 5], G[N + 5];

int nL;
int Rev[N + 5];

void Input();
void FFT (Cpx *, int);

int main() {
	Input();
	for (nL = 1; nL <= n + m; nL <<= 1);
	for (int i = 0; i < nL; ++i) {
		Rev[i] = (Rev[i >> 1] >> 1);
		if (i & 1) Rev[i] |= (nL >> 1);
	
	} FFT (F, 1), FFT (G, 1);
	for (int i = 0; i < nL; ++i) F[i] = F[i] * G[i];
	FFT (F, -1);
	
	for (int i = 0; i <= n + m; ++i) printf ("%d ", (int) (F[i].x + 0.5));
	return 0;

} void FFT (Cpx *A, int Flag) { // Flag = 1 为 DFT, Flag = -1 为 IDFT
	for (int i = 0; i < nL; ++i)
		if (Rev[i] > i) std::swap (A[i], A[Rev[i]]); // 蝴蝶变换
	for (int i = 2; i <= nL; i <<= 1) { // i 枚举区间长度
		Cpx Wn = (Cpx){ cos (2 * Pi / i), Flag * sin (2 * Pi / i) }; // 单位复根
		for (int j = 0; j < nL; j += i) {
			Cpx Base = (Cpx){ 1, 0 };
			for (int k = j; k < j + i / 2; ++k) {
				Cpx tap, tbp;
				tap = A[k], tbp = Base * A[k + i / 2];
				A[k] = tap + tbp, A[k + i / 2] = tap - tbp;
				Base = Base * Wn;
			}
		}
	} if (Flag == -1) for (int i = 0; i < nL; ++i) A[i].x /= nL;
}
```

> 提交地址: [Luogu P3803 【模板】多项式乘法 (FFT)](https://www.luogu.com.cn/problem/P3803)

* 快速数论变换 $NTT$

0. 为什么选择 $NTT$

$FFT$ 借助 $n$ 次复根的特殊性质简化计算, 但难免会有精度误差. 而 $NTT$ 正是借助了另一种具有相似性质的**原根**巧妙绕开精度误差, 在膜意义下解决问题.

1. 阶与原根

若 $\gcd (a, p) = 1$, 使得 $a^k \equiv 1 \ (mod \ p)$ 成立的最小正整数 $k$ 即为 $a$ 关于膜 $p$ 的阶.

原根则是膜 $p$ 下阶为 $\varphi (p)$ 的数 $a$. 换句话说, 原根就是使得 $a^0, a^1, ..., a^{\varphi(p) - 1}$ 互不相同的 $a$. 欧拉定理提到 $a^{\varphi (p)} \equiv 1 \ (mod \ p)$, 也就是原根 $a$ 的膜 $p$ 下的幂每 $\varphi (p)$ 一循环. 不禁联想到了 $n$ 次复根中的周期性.

2. 膜数 $p$ 的选取 ($p$ 为质数)

$p = qn + 1 \ (n = 2^k)$. $Why?$

$p$ 为质数时 $\varphi (p) = p - 1 = qn$. 这样保证了周期为偶数, 且每次递归折半时不会出现小数.

3. 原根的性质

令 $w_n = g^{\frac{{\varphi (p)}}{n}} = g^q$

1. 周期性: 参见 "阶与原根", 成立

2. 互异性 (名字编的)

$g ^ 0, g^q, g^{2q}, ..., g^{(n - 1)q}$ 互不相同, 成立.

3. 折半引理

$w_n^{2k} = g^{2k\frac{{\varphi (p)}}{n}} = g^{\frac{{k\varphi (p)}}{n/2}} = w_{n/2}^k$, 成立
  	
4. 对称性

$$
\left(w_n^{\frac{n}{2}}\right)^2 = w_n^n = g^{\varphi(n)} = 1\\
$$

因为原根性质 $w_n^{\frac{n}{2}} \neq w_n^n$

$$
=> w_n^{\frac{n}{2}} = -1 \ \text{(互异性)}
$$
然后套进对称性的式子里就成立了.
  	
5. 求和引理
  	
自证不难.
  	
4. 原根用法
  
$w_n \equiv g^{\frac{{\varphi (p)}}{n}} \ (mod \ p)$, 在 $FFT$ 中把 $w_n$ 替换成 $g^{\frac{{\varphi (p)}}{n}}$ 即可.
  
5. 总结
  
$FFT$ 与 $NTT$ 都是利用了复根和原根共有的几个性质简化了 $DFT$ 与 $IDFT$ 的过程.
  
  
* $FFT / NTT$ 加速卷积

卷积形式: $\sum_{i=0}^n f (i) \ g(n - i)$

多项式乘法: $(f*g)(x)$ 第 $n$ 项**系数**: $\sum_{i=0}^n a_ib_{n-i}$

发现可以把卷积看为多项式乘法的系数部分, 即 $f(i)=a_i, g(i)=b_i$. 此时 $(f*g)(x)$ 第 $n$ 项系数即为 $\sum_{i=0}^n f (i) \ g(n - i)$.

因此 $FFT/NTT$ 能够加速卷积运算.
