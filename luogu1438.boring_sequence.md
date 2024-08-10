# Luogu P1438 无聊的数列 SatAug10 2024
二阶差分和前缀。
## 题目描述
给出一个数列 $\{f\}$，两种操作：
 - `1 l r e d` 给子串 [l, r] 加上一个首项 e，公差 d 的等差数列；
 - `2 p` 输出数列第 p 项 $f_p$。

数列长为 n，操作有 m 次。

0 ≤ n ≤ 1e5；-200 ≤ a, e, d ≤ 200；1 ≤ l ≤ r ≤ n；1≤ p ≤ n。

## 做法
先介绍一下差分。对于长为 n 的数组 $\{f\}$，它的差分长度也是 n，每一项是原来相邻两项的差，第一项不变，表示成：
$$f_{1i}=(\Delta f)_i=\begin{cases}
f_i-f_{i-1},&i\ge2\\
f_1,&i=1
\end{cases}$$
然后根据差分数组可以算出原来的数组，这个操作叫做前缀和。
$$
f_i=\sum_{j=1}^i(\Delta f)_j = f_{11}+f_{12}+\cdots+f_{1i}
$$
可以验证：两个数组先差分再加起来，和先加起来再差分，结果不会变。
$$
(f_i-f_{i-1})+(g_i-g_{i-1})=(f_i+g_i)-(f_i-g_{i-1})
$$

这样好处是区间改变成了单点修改。虽然单点查询变成了前缀和，可是这两个操作能用树状数组优化成 O(log n)。

这道题里修改变成了区间加等差，差分一次肯定不够的，那就差分两次。差分两次后的数组叫做二阶差分，求两次前缀和叫做二阶前缀和。
$$
f_{2i}=(\Delta f_1)_i=(\Delta^2f)_i=(\Delta\Delta f)_i
$$

### 二阶差分
先说修改，就是二阶差分。虽然加上的是子区间 [l, r]，可是我们把子区间外的用 0 填充，这样就是两个等大的序列相加。我们求一下要加上的序列差分两次后的结果
```
s ← r - l
<1 ... l  l+1  l+2  ... r     r+1        r+2  ... n>
 0 ... e  e+d  e+2d ... e+sd  0          0    ... 0
 0 ... e  d    d    ... d    -(e+sd)     0    ... 0
 0 ... e  d-e  0    ... 0    -[e+(s+1)d] e+sd ... 0
```
所以在二阶差分数组里修改四个点即可。
$$\begin{align*}
f_l    &\leftarrow f_l+e\\
f_{l+1}&\leftarrow f_{l+1}+d-e\\
f_{r+1}&\leftarrow f_{r+1}-[e+(s+1)d]\\
f_{r+2}&\leftarrow f_{r+2}+e+sd
\end{align*}\qquad (s=r-l)
$$
我们知道，区间末尾减去一些数是为了不影响后面的序列。所以当下标越界时直接忽略就好。这也是树状数组的特性，意味着甚至不需要判断就能自动忽略。

### 二阶前缀和
假如说用树状数组维护一阶前缀，暴力求二阶前缀，时间是 O(n log n)，很不友好。模拟这个过程会发现，二阶前缀相当于一个带权的前缀和：
```
f4 = f21
   + f21 + f22
   + f21 + f22 + f23
   + f21 + f22 + f23 + f24
   = 4*f21 + 3*f22 + 2*f23 + 1*f24
```
可是你直接维护带权前缀和，你会意识到并不方便求出任意的前缀和。所以我们考虑再维护一个前缀和（就是不带权的）。
```
f4 =     4*f21 +     3*f22 +     2*f23 +     1*f24
   = (4+k)*f21 + (3+k)*f22 + (2+k)*f23 + (1+k)*f24
   -     k*(f21 + f22 + f23 + f24)   (k+4=n)
   = n*(f21)   + (n-1)*f22 + (n-2)*f23 + (n-3)*f24
   - (n-4)*(f21 + f22 + f23 + f24)   (k=n-4)
```
可是这里的权重是倒着走很不方便，我们把它变成正着走的。具体而言，设
$$a_i=f_{1i};\quad b_i=i\cdot f_{1i}$$
用它们表示二阶前缀和
$$\begin{align*}
f_i&=\sum_{j=1}^if_{1j}=\sum_{j=1}^i\sum_{k=1}^jf_{2k}=\sum_{1\le k\le j\le i}f_{2k}\\
&=\sum_{k=1}^i\sum_{j=k}^if_{2k}=\sum_{k=1}^i(i+1-k)f_{2k}\\
&=(i+1)\sum_{k=1}^if_{2k}-\sum_{k=1}^ikf_{2k}\\
&=(i+1)a_i-b_i
\end{align*}$$
然后就 AC 了。

## AC Code
```cpp
#include <iostream>

using ll=long long;
const int N=1e5+10;

int n, t;
ll a[N], b[N];

int lb(int i) { return i & -i; }

void work_bf() {
  while (t--) {
    int op; std::cin >> op;
    if (op == 1) {
      int lf, rg; ll e, d;
      std::cin >> lf >> rg >> e >> d;
      for (int i=lf; i<=rg; i++, e+=d)
        a[i] += e;
    } else {
      int p; std::cin >> p;
      std::cout << a[p] << '\n';
    }
  }
}

void init_tree(ll v[]) {
  for (int j=2; j<=n; j<<=1)
    for (int i=j; i<=n; i+=j)
      v[i] += v[i - j/2];
}

void pos_add(int pos, ll k) {
  for (int i=pos; i<=n; i+=lb(i))
    a[i] += k, b[i] += pos*k;
}

ll prefix_sum(ll v[], int pos) {
  ll ret = 0;
  for (int i=pos; i; i -= lb(i))
    ret += v[i];
  return ret;
}

int main() {
  std::cin >> n >> t;
  for (int i=1; i<=n; i++)
    std::cin >> a[i];

  if (n<=5) return work_bf(), 0;

  for (int _=0; _<2; _++)
    for (int i=n; i>=2; i--)
      a[i] -= a[i-1];

  for (int i=1; i<=n; i++)
    b[i] = a[i] * i;
  init_tree(a), init_tree(b);

  while (t--) {
    int op; std::cin >> op;
    if (op == 1) {
      int lf, rg; ll e, d;
      std::cin >> lf >> rg >> e >> d;
      pos_add(lf, e);
      pos_add(lf+1, d-e);
      int s = rg-lf;
      pos_add(rg+1, -(e+(s+1)*d));
      pos_add(rg+2, e+s*d);
    } else {
      int p; std::cin >> p;
      ll ans = (p+1) * prefix_sum(a, p);
      ans -= prefix_sum(b, p);
      std::cout << ans << '\n';
    }
  }
}
```
