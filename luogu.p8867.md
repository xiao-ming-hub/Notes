# 建造军营 | 计数/加乘法原理 - WedNov27 2024
> [Luogu P8867](https://www.luogu.com.cn/problem/P8867)
> 给出一个 $n$ 点 $m$ 边无向连通图，统计这样的方案数：
> - 选至少一个点、任意数量的边。
> - 如果有边没选，任意删去一条没选的边后，选择的点都连通。
> 
> 输出方案数模 $P=10^9+7$ 的值。$1\le n\le5\times10^5$，$n-1\leq m\leq10^6$。

对于任意一个选择方案，任意修改桥“选与不选”的状态，得到的新方案一定合法。所以考虑统计出桥的数量。假设有 $(t-1)$ 座桥，答案（不取模时）一定有 $2^{m-t+1}$ 的因子。然后考虑所有非桥边都选上的方案数。

边双缩点成树，设点双 $u$ 里有 $c(u)$ 个点，剩下的问题转化为：
> 有一颗 $t$ 节点的树，每个点可以染成 $2^{c(u)}-1$ 中颜色之一，也可以不染。可以选任意数量的树边（桥），求满足下面限制的方案数：
> - 至少一个点染色。
> - 所有的染色点关于选择的边连通。

用树形 dp 和加/乘法原理进行计算。设状态 $(u,i)$ 表示以 $u$ 为根节点，只考虑前 $i(\ge0)$ 个子树。
> **NOTE** 不是“子树外面的点/边不选”，而是“没有外边的子树”。

然后放宽“选至少一个点”的要求，只要求连通性来确定子问题。有三个组合数：
- $f$：选至少一个点。
- $g$：选至少一个点，不论是否选根，选的点、边和子树的根连通。
- $h$：不选点的方案数。

答案是 $f(1)$。下文中，除边界外，所有的状态都是这样转移：
$$(u,i) \times (v,|\mathrm{ch}\ v|)\rightarrow(u,i+1)$$
所以我把 $(u,i)$、$(u,i+1)$、$(v,|\mathrm{ch}\ v|)$ 记作 $X$、$X'$、$X_v$，$X\in{f,g,h}$。


转移想了比较久，我们先考虑加入一个子树的情况。首先说 $f$ 怎么求。对于边 $(u,v)$，有几种可能：
- 只选一侧的点，这条边有 2 种选法（选与不选）
    - $u$ 一侧有点：$fh_v$.
    - $v$ 一侧有点：$hf_v$.
- 选两侧的点，这条边一定要选。$u$ 侧的已选点和 $u$ 连通，$v$ 侧的已选点和 $v$ 连通，所以方案数是 $gg_v$.
把上面的加起来得到：
$$f'=2(fh_v+hf_v)+gg_v$$

$g$ 的情况类似。但是只有 $v$ 一侧有点时，$(u,v)$ 一定要选。
$$g'=2gh_v + hg_v + gg_v$$

$h$ 的更简单，简单到很多讨论甚至不屑于把它设出来。
$$h'=2hh_v$$

边界：$f=g=2^c-1$，$h=1$。$c$ 表示点双 $u$ 内点的个数。

## AC Code
```cpp
#include <iostream>
using ll = long long;
const int N = 5e5+5, M = 1e6+6;
const ll P = 1e9+7;

struct St {
  ll f, g, h;
  void operator+=(St v) {
    f = 2 * (f * v.h % P + h * v.f % P) % P + g * v.g % P;
    g = 2 * g * v.h % P + h * v.g % P + g * v.g % P;
    h = 2 * h * v.h % P;
    f %= P, g %= P, h %= P;
  }
};

int n, m, hd[N], to[M*2], nx[M*2],  // 存原图
    // 缩点，有 tot 个点双
    be[N], ti[N], lo[N], st[N], top, tot,
    pa[N], hh[N], tt[N*2], nn[N*2]; // 重建树
ll c[N];

int fd(int x) { return x == pa[x] ? x : pa[x] = fd(pa[x]); }
void din(int &a, int v) { if (a>v) a=v; }

void add(int u, int v) {
  static int i = 0;
  tt[i] = v, nn[i] = hh[u], hh[u] = i++;
}

void tar(int u, int q) {
  static int tm = 1;
  ti[u] = lo[u] = ++tm;
  st[top++] = u;
  for (int p=hd[u], v; ~p; p=nx[p])
    if (!ti[v=to[p]])
      tar(to[p], p), din(lo[u], lo[v]);
    else if (p ^ q ^ 1) 
      din(lo[u], ti[v]);

  if (lo[u] == ti[u]) {
    ++tot, c[u] = 1;
    int cnt = 0;
    while (true) {
      int v = st[--top];
      be[v] = pa[v] = u;
      cnt++;
      if (u == v) break;
    } // 把 c[u] 转换为 f 和 g 的初值
    while (cnt--) c[u]<<=1, c[u]%=P;
    --c[u];
  }
}

St dp(int u, int pa) {
  St uu = {c[u], c[u], 1};
  for (int p=hh[u]; ~p; p=nn[p])
    if (tt[p] != pa)
      uu += dp(tt[p], u);
  return uu;
}

int main() {
  std::cin >> n >> m;
  for (int i=1; i<=n; i++)
    hd[i] = hh[i] = -1;

  for (int i = 0, u, v; i < m * 2;) {
    std::cin >> u >> v;
    to[i] = v, nx[i] = hd[u], hd[u] = i++;
    to[i] = u, nx[i] = hd[v], hd[v] = i++;
  }

  tar(1, -1);
  for (int u=1; u<=n; u++)
    for (int p=hd[u]; ~p; p=nx[p]) {
      if (fd(u)==fd(to[p])) continue;
      pa[pa[u]] = pa[to[p]];
      add(be[u], be[to[p]]);
      add(be[to[p]], be[u]);
    }

  ll f = dp(be[1], be[1]).f;
  tot = m - (tot - 1);
  while (tot--) f <<= 1, f %= P;
  std::cout << f << '\n';
}
```

## 其它做法
状态和转移不是唯一的答案。曾经在写这题时我也参考了题解，现在我把其它题解的思路都放在一起，供读者比较。

### By Chy12321 / Fanch100 / Liveddd / dbxxx / OMG_wc
我们不妨把非桥边放在一起算，而不是单独拿出来看。
- $V(u)$ $E(u)$：$u$ 对应原点双的点/边数。
- $s(u)$：子树内边数，点双里的也算上。

那这时就要考虑点双内点/边的选择情况了。其实变化不大，只是边界变了，$g$ 要减去点双内 “所有点都不选” 的情况。
$$s=E\quad g=2^E(2^V-1)\quad h=2^E$$

$$s'=s+s_v+1$$
$$g'=2gh_v + hg_v + gg_v$$
$$h'=2hh_v$$

> 下面统计答案的思路在 [Chy12321](https://www.luogu.com.cn/article/cpfddy4h) / [Fanch100](https://www.luogu.com.cn/article/axzfpvc4) / [Liveddd](https://www.luogu.com.cn/article/d3adogwf) 有提到。

dp 时，根据 “选的点能到达的最浅的节点” 分类求和。如果 $u$ 有上级节点 $x$，那么把 $u$ 子树添加进整棵树后，限制 $(x,u)$ 的树边不选，就能避开重复讨论。除了 $(x,u)$ 的树边和 $u$ 里面的边以外，整棵树还有 $(m-s_u-1)$ 条边，这时的方案数为：
$$2^{m-s(u)-1}g(u)$$
当 $u=1$，节点 $x$ 不存在时，自然没有 “子树外的情况”，这时答案即为 $g_1$.
$$A=\sum_ug(u)\times\begin{cases}
1,&\ u=1,\\
2^{m-s(u)-1},&\ u\ne1.
\end{cases}$$

> 下面的在 [dbxxx](https://www.luogu.com.cn/article/gfh818po) / [OMG_wc](https://www.luogu.com.cn/article/ndpgsg8e) 有提到。

统计答案的另外一个方法：不根据边分类，而根据选点的 LCA 分类。当只在 $u$ 子树内选点，且已选点的 LCA 是 $u$ 有 2 中情况：
- $u$ 选了。
- 不选 $u$，而且至少 2 棵子树有选点。

它的反面是 2 个情况同时满足：
- 不选 $u$ 。
- 只有 1 棵子树内有选点（选 0 棵的不在 $g$ 范围内）。

当然，这里的“反面”是相对于 $g$ 的设法确定的，这些情况中，选择的点仍然在 $u$ 的子树里。接着，枚举选的是哪一棵子树，由于要求选点和 $u$ 连通，所以子树内仍是 $g_v$，而 $(u,v)$ 必选，$u$ 只剩下 $(s_u-s_v-1)$ 条边能自由选择。

除了 $u$ 子树里的边，外面的 $t-s(u)$ 可以任选【不像上面的要去掉边 $(x,u)$】。因此，根据 “$u$ 是已选点之 LCA” 枚举的答案可以写成：
$$A=\sum_u2^{t-s(u)}\left[g_u-\sum_v2^{s(u)-s(v)-1}g_v\right]$$

### By [Ecrade_](https://www.luogu.com.cn/article/d1ghpb6r)
非常巧妙地把 Tarjan 缩点和 dp 结合起来了。

我们不妨把所有点都放一起看，而不是缩成边双后一个个处理。下面三种说的都是同一类点，我们称它为荧点（证明不重要，只是希望选择容易接受的表达方式）：
- 搜索树上的桥，远离根的一侧的节点。
- 一个边双内 `dfn` 最小的节点。
- 子树中，不存在任何一条连接到其父亲某个祖先的返祖边的点。

这个方法还能反过来用来求桥：[oiwiki/差分算法](https://oi-wiki.org/graph/bcc/#差分算法)

$v$ 是坏节点：
- f 2 至少选一个点，在至少 1 个坏节点的子树内有选点
- g 1 至少选一个点，每个坏节点的子树内不选点
- h 0 不选点

$$f'=2h​f_v​+2f​h_v​+h​g_v​​$$
$$g'​=h​g_v​+2g​h_v​+g​g_v​$$
$$h'​=2h​h_v​$$

### By [SDqwq](https://www.luogu.com.cn/article/h6qwnca3)
取 4 种状态，表示 u 子树内/外，有/没有选点

### By [ningago](https://www.luogu.com.cn/article/62lqyx8c)
我们对于每个点考虑这个点和他的父亲边被不被选，围绕四种情况讨论：

### By [Nullptr_qwq](https://www.luogu.com.cn/article/u6ymo9yc)

### By [蒟蒻君 HJT](https://www.luogu.com.cn/article/81nnynm1)

### By [Lazy_Labs](https://www.luogu.com.cn/article/rg8iv0fx)

### By [spdarkle](https://www.luogu.com.cn/article/x4v1lj2c)

### By [jifbt](https://www.luogu.com.cn/article/1zwjhmdp)

### By [Missa](https://www.cnblogs.com/purplevine/p/16930223.html)

## 请求添加题解

@
@
[文章链接](https://www.luogu.com.cn/article/q2a31g0n)

做题思路已足够丰富了，我把题解出现的所有做法进行了比较，同时给出详细说明

我的题解关注转移式的推导过程。我看很多题解会直接给出带求和、求积号的转移方程，而没有给方程本身清晰的解释。所以我很详细地解释了
