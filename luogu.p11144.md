# Strange Madoka Game | CRT/伪高精度 - SatOct12 2024
称之为伪高精，是因为这用较低常数稍微扩充了的变量范围。它的范围仍然有限，只不过做到了 `long long` 差一点点就能做到的事，如 [BZOJ 2854](bzoj.2854.md)。

[Luogu P11144](luogu.com.cn/problem/P11144) 交互题，每次询问给定整数 $m$，回答 $x\bmod m$，最多用 2 次询问求得 $x$。

> $x\in[0, 10^{17}]$；$m\in[1,4\times 10^8]$。多组数据，每个测试点不超过 100 组。

## 评价
一道很适合引入中国剩余定理（CRT）的题目。这篇题解面向熟悉快速幂思想，将要学习 CRT 的读者。


## 思路
联想天干地支纪年法：用年份模 10 和 12 的结果来表示年份模 60 的结果。

取两个大质数，设为 $P$，$Q$。根据询问的答案 $m_1$，$m_2$ 可以列出方程组
$$\begin{cases}
x \bmod P = m_1\\
x \bmod Q = m_2
\end{cases}$$
为什么用质数？首先我们希望 $\text{lcm}(P,Q)$ 能涵盖所有 $x$ 的范围；其次希望询问出的结果能比较简单地合并解出 $x$。满足这两个原则下，比较简单的选择是质数（天生互质、方便寻找、方便合并）。

“比较简单地合并”能有多简单？我们想设
$$
x \equiv w_1m_1+w_2m_2 \pmod {PQ}
$$
- $w_1$ 是 $Q$ 的倍数，而且除以 $P$ 的余数是 1。
- $w_2$ 是 $P$ 的倍数，而且除以 $Q$ 的余数是 1。

可以验证这样的形式符合上面的方程组，实际上这是 CRT 的特殊形式。由于已经要求 $PQ$ 包含所有 $x$ 的范围，这里的同余号实际能取等。

## 代码实现
这是寻找 $P$ 的代码。要寻找 Q，从 $i$ 的起点修改为 $P-1$ 即可。
```cpp
using ll = long long; // 后面省略这行

void prime() {
  for (ll i = 1e17; i; i--)
    if ([](int i) -> bool {
      for (int j = 2; j * j <= i; j++)
        if (i % j == 0) return false;
      return true;
    }(i)) {
      std::cout << i << '\n';
      break;
    }
}
```
然后我找到了 $P=399999959$，$Q=399999949$。$M=PQ=159999963200002091>1.5\times10^{17}$。

这是寻找 $w_1$ 的代码。要寻找 $w_2$，把 $P$ 换成 $Q$，把 $Q$ 换成 $P$ 即可。
```cpp
void weight() {
  for (ll i = 1; i < P; i++)
    if (i * Q % P == 1) {
      std::cout << i << '\n';
      break;
    }
}
```
我找到了 $w_1=143999966840001887$，$w_2=15999996360000205$。

接着是合并。因为相乘的两个数 $x_i$、$m_i$ 的数量级比较大，会溢出，所以模仿快速幂的做法（二进制拆解，分解为多个数求和），设计出算乘法的程序。注意乘法是在模 $M=PQ$ 的意义下进行的。
```cpp
const ll M = P * Q;

ll multiply(ll x, ll y) {
  ll a = 0;
  while (y) {
    if (y & 1) a = (a + x) % M;
    x = x * 2 % M, y >>= 1;
  }
  return a;
}
```

## AC Code
```cpp
#include <iostream>
using ll = long long;
const ll w1 = 143999966840001887ll,
      w2 = 15999996360000205ll, 
      M = 159999963200002091ll;

ll f(ll x, ll y) {
  ll a = 0;
  while (y) {
    if (y & 1) a = (a + x) % M;
    x = x * 2 % M, y >>= 1;
  }
  return a;
}

int main() {
  int t; std::cin >> t; while (t--) {
    ll m1, m2;
    std::cout << "? 399999959" << std::endl;
    std::cin >> m1;
    std::cout << "? 399999949" << std::endl;
    std::cin >> m2;
    std::cout << "! " << (f(w1, m1) + f(w2, m2)) % M << std::endl;
  }
}
```
