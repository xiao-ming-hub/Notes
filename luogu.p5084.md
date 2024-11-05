# 轮换式 | 数学/整式 - TueNov5 2024
[Luogu P5084](https://www.luogu.com.cn/problem/P5084) 给出 $n$ 元 $1\sim n$ 次基本对称式的值，求这 $n$ 个变量 $m$ 次和模 $P=10^7+29$ 的值。
> $n\in\{1,2,3,4\}$，$m\in\mathbb N\land m\le10^5$。

$n$ 元 $k(\le n)$ 次基本对称式指：
$$\sum_\binom nkx_{i1}x_{i2}\cdots x_{ik}$$
作为例子，设四个变量为 $x,y,z,u$，$4$ 元 $2$ 次基本对称式为
$$\sum_6xy=xy+xz+xu+yz+yu+zu$$
另一个常见的地方是韦达定理。下面这个多项式：
$$(x-x_1)(x-x_2)\cdots(x-x_n)$$
展开后 $n-k$ 次的系数（忽略 $\pm1$ 的系数）正是 $n$ 元 $k(\le n)$ 次基本对称式

## 做法
本文中所有求和号均表示对称求和 $\sum_\mathrm{syn}$，为了减少错误率，在求和和号下标数字表示展开后的系数和，也就是所有变量都取 $1$ 时的值。

本文的所有结论都可以用乘法分配律直接证明，所以我详细讲探索的过程。

设输入中 $n$ 元 $i$ 次基本对称式的值为 $a_i(1\le i\le n)$，$j$ 次方和为 $f_j$。尝试建立递推关系。可以发现，$f_0=n$ 和 $f_1=a_1$。

### $n=1$
$f_i=x^i=a_1^i$。为了形式上的统一，我写成 $f_{k+1}=a_1f_k$。

### $n=2$
中考数学。通过这种方法升幂：
$$a_1f_k=(x+y)(x^k+y^k)=x^{k+1}+y^{k+1}+xy(x^{k-1}+y^{k-1})=f_{k+1}+a_2f_{k-1}$$
$$f_{k+1}=a_1f_k-a_2f_{k-1}$$
以前数学老师讲过一种可以把时间降为 $O(\log n)$ 的方法：用分治，通过 $f_k$ 转移到 $f_{2k}$ 和 $f_{2k+1}$。这种方法和矩阵快速幂是一个道理。

### $n=3$
尝试用一个 $j$ 次式和 $(k+1-j)$ 次式相乘来得出 $(k+1)$ 次，$j=\lfloor n/2\rfloor$ 就是上文的分治。为了方便，我令 $j=1$，$1$ 次对称式是唯一的，$k$ 次的我取 $f_k$：
$$\begin{align}
a_1f_k & = (x+y+z)(x^k+y^k+z^k)\\
&=x^{k+1}+y^{k+1}+z^{k+1}+\sum_6xy^k\\
&=f_{k+1}+\sum_6xy^k
\end{align}$$
你会发现末尾多了个很难看的求和。我们再取 $j=2$，两个对称式为 $a_2$ 和 $f_{k-1}$：
$$\begin{align}
a_2f_{k-1}&=(xy+xz+yz)(x^{k-1}+y^{k-1}+z^{k-1})\\
&=\sum_6xy^k+xyz(x^{k-2}+y^{k-2}+z^{k-2})\\
&=\sum_6xy^k+a_3f_{k-2}
\end{align}$$
把两式相加：
$$\begin{align}
a_1f_k & =f_{k+1}{\color{Violet}+\sum_6xy^k}\\
{\color{Violet}\sum_6xy^k+}a_3f_{k-2}&=a_2f_{k-1}\\
a_1f_k+a_3f_{k-2}&=f_{k+1}+a_2f_{k-1}
\end{align}$$
$$f_{k+1}=a_1f_k-a_2f_{k-1}+a_3f_{k-2},\quad k\ge2$$
边界：
$$\begin{align}
a_1^2&=(x+y+z)^2\\
&=x^2+y^2+z^2+2\sum_3xy\\
&=f_2+2a_1
\end{align}$$
你会发现这个等式和 $n=2$ 的情形完全一样：
$$f_2=a_1^2-2a_2=a_1f_1-a_2f_0$$
**注意：** 这里 $f_0=2$。

### $n=4$
同样地操作：
$$\begin{align}
a_1f_k&=(x+y+z+u)\left(\sum_4x^k\right)\\
&=\sum_4x^{k+1}+\sum_{12}xy^k\\
&=f_{k+1}+\sum_{12}xy^k
\end{align}$$
$$\begin{align}
a_2f_{k-1}&=\left(\sum_{6}xy\right)\left(\sum_4x^{k-1}\right)\\
&=\sum_{12}xy^k+\sum_{12}xyz^{k-1}
\end{align}$$
$$\begin{align}
a_3f_{k-2}&=\left(\sum_{4}xyz\right)\left(\sum_4x^{k-2}\right)\\
&=\sum_{12}xyz^{k-1}+xyzu\left(\sum_4x^{k-3}\right)\\
&=\sum_{12}xyz^{k-1}+a_4f_{k-3}
\end{align}$$
三式相加
$$\begin{align}
a_1f_k&=f_{k+1}{\color{Violet}+\sum_{12}xy^k}\\
{\color{Violet}\sum_{12}xy^k+}{\color{Tan}\sum_{12}xyz^{k-1}}&=a_2f_{k-1}\\
a_3f_{k-2}&={\color{Tan}\sum_{12}xyz^{k-1}}+a_4f_{k-3}\\
a_1f_k+a_3f_{k-2}&=f_{k+1}+a_2f_{k-1}+a_4f_{k-3}
\end{align}$$
$$f_{k+1}=a_1f_k-a_2f_{k-1}+a_3f_{k-2}-a_4f_{k-3}$$
在 $n=3$ 的边界里我们发现了一个“巧合”。现在我们验证它在 $n=4$ 时是否成立。
$$\begin{align}
a_1^2-2a_2&=(x+y+z+w)^2-2\left(\sum_{6}xy\right)\\
&=x^2+y^2+z^2+w^2=f_2
\end{align}$$
Amazing！非常完美地取等了。$n=3$ 中
$$f_3 = a_1f_2-a_2f_1+a_3f_0= a_1f_2-a_1a_2+3a_3$$
**注意：** 这里 $f_0=3$。

用乘法分配率可以证明 $n=4$ 时这个等式也成立（不想打 latex 了）。

至此，所有情况的递推式、边界条件已推导完毕。直接模拟转移 $O(nm)$，矩阵快速幂 $O(n^3\log m)$，当然这道题用不到。

## Code
```cpp
#include <iostream>
using ll = long long;
const int N = 7, M = 1e5+5;
const ll P = 1e7+29;

int n, m;
ll a[N], f[M];

int main() {
  std::cin >> n >> m;
  for (int i=1; i<=n; i++) std::cin >> a[i];

  int i=2; f[0] = n, f[1] = a[1];
  // f2 = a1^2 - 2a2
  if (n>=3) f[2] = (a[1] * a[1] % P + P - 2 * a[2] % P) % P, i++;
  // f3 = a1f2 - a1a2 + 3a3
  if (n>=4) f[3] = (a[1] * f[2] % P + P - a[1] * a[2] % P + 3 * a[3] % P) % P, i++;

  while (i <= m) {
    for (int j=1, w=1; j<=n; j++, w=-w)
      f[i] = (f[i] + P + a[j] * f[i-j] % P * w) % P;
    i++;
  }

  std::cout << f[m] << '\n';
}
```

## 后记
$n=3$ 时让 $k=2$：
$$f_3=a_1f_2-a_2f_1+a_3f_0$$
由于 $f_0=3$，$f_1=a_1$ 移项，合并同类项：
$$f_3-3a_3=a_1(f_2-a_2)$$
于是得到了这个经典的公式：
$$x^3+y^3+z^3-3xyz=(x+y+z)(x^2+y^2+z^2-xy-xz-yz)$$

---
根据[对称多项式基本定理](https://zhuanlan.zhihu.com/p/623516736)：
> $K[x_1,x_2,\cdots,x_n]$ 中任意一个对称整式 $f$，存在唯一的整式 $g$ 使
> $$f=g(a_1,a_2,\cdots,a_n)$$

这道题肯定有解的。
