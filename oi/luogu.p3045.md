# Cow Coupons G | 贪心/反悔 - SunNov17 2024
> [Luogu P3045](https://www.luogu.com.cn/problem/P3045) $n$ 头奶牛，$k$ 张优惠券，钱包有 $m$ 块钱。每头牛原价 $p_i$，优惠价 $c_i$。最大化购买数量。
> 
> $1\le k\le n\le5\times10^4$ $1\le c\le p\le1e9$ $1\le m\le10^{14}$

首先把 $c$ 最小的 $k$ 个拿走。设：
-   当前拿了的构成 $A$。
-   其中优惠的那 $k$ 个构成集合 $B$。
-   $n$ 头奶牛构成 $S$。

如果还能拿，那么有 2 种方法：
1.  拿 $p$ 最小的：代价 $p_i$；
1.  把 $A$ 的某个 $c_j$ 换成 $p_j$，选择另一个 $c_i$：
    代价 $c_i+(p_j-c_j)$.

维护三个数据结构：
-   `{pp tp}`：$S-A$ 中的 $\min p_i$.
-   `{pc tc}`：$S-A$ 中的 $\min c_i$.
-   `pq`：$B$ 中的 $\min(p_j-c_j)$.

每一轮决策中，可以证明当前价格是同数量 $|A|$ 的最小值。

两种操作的实现伪代码：
```cpp
1.  pp.remove(i), del(i), m -= pj;

2.  pc.remove(i), del(i),
    pq.remove(j), pq.add(pi-ci),
    m -= ci + (pj-cj)
```
`pp` 和 `pc` 不需要插入，可以用排序和栈。

因为线性表不容易快速删除中间元素，`del(i)` 通过标记 $i$ 在 $B$ 里实现，对应下面代码的 `ud[]`。每个元素只会弹出一次，均摊时间常数。

`pp` 和 `pc` 要记录下标，`pq` 只要记录值。

## Code
```cpp
#include <iostream>
#include <queue>
#include <algorithm>

using ll = long long;
using pii=std::pair<ll, int>;

const int N = 5e4+5;
int n, k, cnt, c[N], p[N],
    pp[N], tp, pc[N], tc;
bool ud[N]; ll m;
std::priority_queue<ll,
  std::vector<ll>, std::greater<ll>> pq;

int calc() {
  std::sort(pp, pp+n, [](int i, int j) -> bool {
    return p[i] > p[j]; });
  std::sort(pc, pc+n, [](int i, int j) -> bool {
    return c[i] > c[j]; });

  tp = tc = cnt = n;
  while (cnt--, k--) {
    int i = pc[--tc];
    ud[i] = true, pq.emplace(p[i] - c[i]);
    if ((m -= c[i]) < 0) return n - cnt - 1;
  }

  cnt++; while (cnt--) {
    // 给不起就是无穷大
    ll w1 = m+100, w2 = m+100;

    while (tp && ud[pp[tp-1]]) tp--;
    if (tp) w1 = p[pp[tp-1]];
    while (tc && ud[pc[tc-1]]) tc--;
    if (tc) w2 = c[pc[tc-1]] + pq.top();

    if (std::min(w1, w2) > m) return n - cnt - 1;
    if (w1 < w2) ud[pp[--tp]] = true, m -= w1;
    else {
      int i = pc[--tc];
      ud[i] = true, m -= w2, pq.pop();
      pq.emplace(p[i] - c[i]);
    }
  }
  return n;
}

int main() {
  std::cin >> n >> k >> m;
  for (int i=0; i<n; i++) {
    std::cin >> p[i] >> c[i];
    pp[i] = pc[i] = i;
  }
  std::cout << calc() << '\n';
}
```
