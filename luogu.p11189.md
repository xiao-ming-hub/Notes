# 水杯降温 | 不等式 二分 - SunOct20 2024
[Luogu P11189](https://www.luogu.com.cn/problem/P11189) 有一棵包含 $n$ 个节点的有根树，且根为节点 $1$。节点 $i\ (1\le i\le n)$ 的点权为 $a_i$。两种操作：
- 使以 $i$ 为根的子树内所有点的点权均增加 $1$。
- 选择一条连接根和某片叶子的路径，路径上每个点均减少 $1$。

判断能否有限次操作，将所有节点上的水杯的水温都变为 $0$。

> 多组数据 $1\leq t\leq 1\,000$；
> $2\leq n\leq 10^5$，一个测试点内 $\sum n\le 10^6$；
> $\forall2\le i\le n,\ 1\le f_i<i$；
> $\forall1\le i\le n,\ |a_i|\leq10^{12}$。

## 做法
先减后加。减完后能加到 0 的条件：
- 根节点权值非正，$a_1\le0$。
- 所有节点权值小于等于其父亲，$a_u\ge a_v$。

设差分值 $b_i=a(i)-a(f_i)$，$b_1=a_1$，目标变为 $\forall i,\ b_i\le 0$。

每次操作，某片叶子到根**路径上所有点的下结点**的差分值 +1，同时根节点 -1。为了考虑子问题，把链减拆成一层层进行：
1. 根节点 -1，所有子树 +1；
2. 选择一个孩子，在它的子树里重复操作。

阅读时你大概能想象到一条路径从数根蔓延到叶子的动画。两个断言：
1. 操作时，非根节点差分值不能变少。如果一开始存在非根节点，它的差分值是正，一定**无解**；
2. 排除上述条件后，如果 $b_1\le0$，一定**有解**。

要尽可能将 $b_1$ 降到 0 以下，如果降不下去一定无解。每一次向下，就是一个决策。把所有的链减放一起考虑应该会容易些。

设 $c(u)\in\mathbb N$ 表示 $u$ 的子树里最多几次链减；$c$ 次中，选择某个子树 $v$ 的次数是 $p(v)$。可列出刻画 $c$ 和 $p$ 的等价的代数式：
$$c(u)= \sum p(v)$$
$$\forall v,\ b_v + c(u) - p(v) \le0$$
$$\forall v,\ 0\le p(v)\le c(v)$$

这两个边界可以直接判断：
- 当 $t_u=0$ 时，$u$ 是叶子，令 $c(u)=+\infty$。
- 当 $t_u=1$（$u$ 只有 1 个孩子 $v$）时，上式退化成 $c(u)\le c(v)$，直接转移即可。

当 $t_u\ge2$ 时，可以二分查得最大值。在二分前我们需要一个上界。根据 (2)：
$$\forall v,\ b_v + c(u)\le p(v)$$
把这个不等式对所有的 $v$ 求和：
$$\sum b_v + t_uc(u)\le c(u)$$
由于 $t_u-1\ge1$，解得：
$$c_u\le\left\lfloor\frac{\sum-b_v}{t_u-1}\right\rfloor$$
对 (3) 第二个不等号的所有 $v$ 求和：
$$c(u)\le\sum c(v)$$
合并上面两个不等式：
$$c(u)=\min\left \{\sum c(v),\left\lfloor\frac{\sum-b_v}{t_u-1}\right\rfloor\right\}$$
如果 $c_u<0$，无解。然而保证了 $b\ge0$，这种情况并不会发生。

二分时，我们需要检查
1. 不考虑 (1) 时，$c(u)$ 是否在 $\sum p(v)$ 的取值范围里。
   $$\sum\max{^1}\{b_v+c(u),0\}\le c(u)\le\sum c(v)$$
   右边的等号是二分上界，不用考虑。也就是：
   $$\sum\max\{ b_v+c(u),0\}\le c(u)$$
2. 保证每个 $v$，$p(v)$ 都能存在²。
   $$\forall v,\ \max\{ b_v+c(u),0\}\le c(v)$$

注意:
1. 不要忘记左边的 max，如果没有，就不必二分了。当然，你会在样例 2 的第 85 个测试点 WA：
   ```txt
   1 1 5
   1 2 1 1
   3 2 -2 2 -5
   # Shuiniao
   ```
2. 不考虑的反例是样例 3 第 4 个测试点：
   ```txt
   1 1 6
   1 1 3 3 5
   99 -27 99 68 45 42
   # Shuiniao
   ```
3. 默认的栈空间不能完成样例 5。linux 下使用 `ulimit -s` 查看、修改栈空间大小。本比赛里栈空间内存限制与题目的内存限制一致。修改时，第二个参数是栈空间字节数。

---
以样例 1 第 2 棵树为例。差分略去。对于节点 2：
$$c_2=p_3+p_4$$
$$-3+c_2\le p_3$$
$$-4+c_2\le p_4$$
上界是 $c_2\le7$，二分后发现可以取等，$c_2=7$。

对于节点 1：
$$c_1=p_2+p_3$$
$$-1+c_1\le p_2\le7$$
$$-5+c_1\le p_3$$
上界 $c_1\le6$，二分后发现可以取等，而 $b_1-c_1=6-6=0$，也就是有解。

## AC Code
```cpp
#include <iostream>
using ll = long long;
const int N = 1e5+5;
const ll inf = 0x7fffffffffff0000ll;

int q, n, f[N], hd[N], to[N], nx[N];
ll a[N], c[N];

bool check(int u, ll mi) {
  ll sum = 0;
  for (int p = hd[u]; p; p=nx[p]) {
    ll ss = std::max(a[to[p]] + mi, 0ll);
    if (ss > c[to[p]]) return false;
    sum += ss;
  }
  return mi >= sum;
}

ll floor(ll up, ll dn) {
  return up > 0 ? up / dn : (up - dn + 1) / dn;
}

ll dp(int u) {
  ll aa = 0, cc = 0;
  int cnt = 0;
  for (int p=hd[u], v; p; p=nx[p]) {
    dp(v = to[p]);
    cnt++;
    aa -= a[v];
    if (cc != inf && c[v] != inf)
      cc += c[v];
    else cc = inf;
  }
  if (cnt == 0) return c[u] = inf;
  if (cnt == 1) return c[u] = c[to[hd[u]]];
  ll lf = 1, rg = std::min(cc, floor(aa, cnt - 1)) + 1;
  while (lf < rg) {
    ll mi = lf + (rg - lf) / 2;
    if (check(u, mi)) lf = mi + 1;
    else rg = mi;
  }
  return c[u] = lf-1;
}

bool calc() {
  for (int i=n; i>1; i--)
    if ((a[i] -= a[f[i]]) > 0)
      return false;
  if (a[1] <= 0) return true;
  return dp(1) >= a[1];
}

int main() {
  std::ios::sync_with_stdio(0);
  std::cin.tie(0);
  std::cin >> q >> q; while (q--) {
    std::cin >> n;
    for (int i=1; i<=n; i++) hd[i] = 0;
    for (int i=2; i<=n; i++) {
      std::cin >> f[i];
      to[i-1] = i;
      nx[i-1] = hd[f[i]];
      hd[f[i]] = i-1;
    }
    for (int i=1; i<=n; i++) std::cin >> a[i];
    std::cout << (calc() ? "Huoyu\n" : "Shuiniao\n");
  }
}
```
