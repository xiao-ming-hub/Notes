# 抉择 | dp/子序列 - TueOct22 2024
好像是七、八月的模拟赛时做的，挖了个坑现在才来填。
- [QOJ 1086 - Bank Security Unification](https://qoj.ac/problem/1086)
- [Public Judge 21792 - 抉择](http://pjudge.ac/problem/21792)

给一个长度为 $n$ 的非负整数序列 $\{a\}$，求它所有子序列 $\{a_{i1},a_{i2},\cdots,a_{ik}\}\ (1\le i_1<i_2<\cdots<i_k\le n)$ 中相邻两位的按位与之和
$$\sum^{k−1}_{j=1}a_{ij}\odot a_{i(j+1)}$$
的最大值。本文里用 $\odot$ 表示二进制按位与，优先级与乘法相同。

子序列：从原序列中任意删除若干个（可以是 0 个）数，将剩下的数按原来的相对顺序拼起来，得到的序列。
> $1\le n\le10^6$，$0\le a_i\le10^{12}$。

## 平方时间 25pts
设 $f_i$ 表示只考虑以下标 $i$ 结尾的子序列答案。转移：
$$f_i=\max_{1\le j<i}(f_j+a_i\odot a_j)$$

## $O(n\log a)$ 100pts
类比 LIS（[最长不下降子序列](lis-print.md)），以二进制位为状态。

设状态 $(i, j)$ 表示只考虑下标在 $[1, i]$ 内，最后一位的第 $j$ 位是 1 的所有子序列。 $f_{ij}$ 表示只考虑这些子序列的答案，为了转移，设 $g_{ij}$ 表示 $f_{ij}$ 的“答案”对应的子序列最后一位。

计算时 $1\rightarrow n$ 枚举 $i$。如果 $a_i$ 的第 $j$ 位是 0，该状态下的结果与 $(i-1,j)$ 相同，否则需要更新 $(i,j)$：
$$f_{ij}=\max_{1\le2^k\le a_i}[f_{(i-1)k} + g_{(i-1)k}\odot a_i]$$
由于保证了 $g_{(i-1)j}\odot a_i\ne0$，每次更新得到的一定会更优。
$$g_{ij}=a_i$$
虽然 $f_{ij}$ 的表达式里有个最大值，可是它和 $j$ 无关，可以枚举每个 $k$ 后算出这个最大的值，再逐一更新 $j$ 即可，分摊到每个状态的时间是常数。

有个细节， $a_i$ 可以加入到末尾和它位与等于 0 的子序列里。这就是为什么 $k$ 的取值允许 $g_{(i-1)k}\odot a_i=0$。下面代码的写法有些不同，我用 $h$ 表示“位与等于 0”时的 $f$，然后把它设为 $t$ 的初值。

## AC Code
```cpp
#include <iostream>
using ll=long long;
const int V=41;

int n; ll f[V], g[V], h;
void dax(ll &a, ll x) { if (a<x) a=x; }

int main() {
  std::cin >> n; while (n--) {
    ll a, t = h; std::cin >> a;

    for (ll j=0, k=1; k<=a; j++, k<<=1)
      if (a & k) dax(t, f[j] + (g[j] & a));

    for (ll j=0, k=1; k<=a; j++, k<<=1)
      if (a & k) f[j] = t, g[j] = a;

    h = t;
  }

  ll ans = 0;
  for (int i=0; i<V; i++) dax(ans, f[i]);
  std::cout << ans << '\n';
}
```
