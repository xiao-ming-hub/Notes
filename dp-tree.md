# dp/树形 - TueAug20 2024
与其说是树形 DP，不如说动态物品背包。毕竟这两道题太平凡了。

## [HAOI2015](https://www.luogu.com.cn/problem/P3177) 树上染色
给出 n 个点的树和 m，把树的点黑白染色，白的 m 个，黑的 (n-m) 个。最大化所有黑点点对和白点点对最大距离和（称为价值）。1 ≤ m ≤ n ≤ 2000。

比如这个树（n=5，m=2）：
```
                    4
                    |2>
5 --<1>-- 1 --<3>-- 2
                    |1>
                    3
```
把 {1, 2} 染成白色，那么黑点点对距离和为(1+3+2)+(1+3+1)+(2+1)=14，白点2+1=3，总价值 14+3=17 最大。所以对于这个样例输出 17。

这道题给的经验有两个，一个是点对计数转换成边权带权和，另一个是转移顺序。

### 点对计数
对于转移时枚举到的某个方案，直接 O(n²) 枚举点对肯定不行。可以枚举边，我们只要数出这条边两端分别有多少个白（黑）点，就可以知道它在计算贡献时重复数了多少次。

对于节点 u，它的某个孩子 v，v 子树大小 s(v)。v 子树里有 j 个白点，外面就有 (m-j) 个白点；里面有 $[s(v)-j]$ 个黑点，外面就有 $[(n-m)-(s(v)-j)]$ 个黑点。(u, v) 这条边分别对 / 白黑点对 / 的重复次数是：
$$\mathrm{tot}_j(u,v)=j(m-j)+(s_v-j)[(n-m)-(s_v-j)]$$

和普通树形 dp 一样，设 f(u, t, k) 表示 u 为根的子树里只考虑 u 和前 k 个子树，t 个点染成白色的最大贡献。这个状态有没有后效性呢？按上面分析，我们已经把贡献转换成了边的带权和，这个权重可以说由子树外的点决定，也可以说由子树内的点决定。这样，就没有后效性了。

转移方程（因为要讲3种dp顺序，所以变量多了些，|ch v| 表示 v 的孩子数，i+j=t）：
$$
f(u,t,k)=\max_{\begin{array}{c}0\le i\le s(u)\\0\le j\le s(v)\end{array}}
f(u,i,k-1)+f(v,j, |\mathrm{ch}\ v|)+w\cdot\mathrm{tot}_j(u,v)
$$
如果 j>m 那么这条边的贡献是负数不影响答案。

### 转移顺序
如果直接开三维数组然后模拟转移，n 有 2000，n³ = 8e9 个 int 大概是 29 GB，所以一定要滚动数组。比如把第三维k压掉。按 dfs 序枚举 u，从大到小枚举 t 是很自然的想法。我来说说所谓“j 从小到大枚举”是怎么回事。按理说，最大值肯定有交换律，可是改变 j 的枚举顺序竟然会 WA？

这种最值转移[最保险的方法](https://www.luogu.com.cn/discuss/702385)是，把局部最值保存在一个临时变量里，这样你怎么枚举 j 都是正解：
```cpp
void dp(int u) {
  s[u] = 1;
  for (int p=hd[u], v; p; p=nx[p]) {
    if (s[v = to[p]]) continue;
    dp(v); ll w = wg[p];
    for (int t = std::max(s[u]+s[v], m); t>=0; --t) {
      ll tmp = 0;
      for (int j = std::min(t, s[v]); j >= std::max(t-s[u], 0); j--) {
        // 这样写 j 即使从小到大也能过
        // 0 <= t-j <= s[u] <=> t-s[u] <= j <= t
        ll g = f[u][t-j] + f[v][j] + w*j*(m-j)
          + w*(s[v]-j)*(n-m-(s[v]-j));
        if (tmp < g) max = g;
      }
      f[u][t] = tmp;
    }
    s[u] += s[v];
  }
}
```
如果不用临时变量的话，这样写\[1\]是错的
```
for t: max(s[u]+s[v], m) -> 0
  for j: min(t, s[v]) -> max(t-s[u], 0)
    用 f[u][t-j] 更新 f[u][t]
```
然后问题来了：只要换一下第二行 j 的枚举顺序立刻 AC\[2\]：
```
for t: max(s[u]+s[v], m) -> 0
  for j: max(t-s[u], 0) -> min(t, s[v])
    用 f[u][t-j] 更新 f[u][t]
```
题解说因为要先转移 j=0 的情况（如果取得到），但并没有说为什么 j=0 这么特殊。我们知道要求 r 个数 a1~ar 的最值，可以维护一个**局部最值**，表示 a1 ~ ai 的最值，然后把 a(i+1) 添加进去，如此往复。

假如说要求这些数经过一些运算（称为 g()）后的最值
```
f[t], f[t-1], f[t-2], ..., f[t-t]
```
对于上面的方法，我们把这个“局部最值”存在 `f[t]` 里。\[1\] 的漏洞在于，`f[t]` 保存的是不含 `g(f[t])` 的最值。结果就是，`f[t]` 被覆盖掉了。\[2\]能避免这个漏洞，因为 `f[t]` 保存的最值包含了 `g(f[t])`。这就是 “先更新 k=0”的意思。

我还看到了[第三种写法](https://www.luogu.com.cn/article/ru1g7107)：
```
for i: s[u] -> 0
  for j: s[v] -> 0
    用 f[u][i] 更新 f[u][i+j]
```
提一下dp实现里，填表法和刷表法的区别。下面两个求斐波那契数列第n项，第一个是填表，第二个是刷表。
```cpp
f[1] = f[2] = 1; for (int i=3; i<=n; i++) f[i] = f[i-1] + f[i-2];

f[1] = 1; for (int i=1; i<n; i++) f[i+1] += f[i], f[i+2] += f[i];
```
第三种写法就是刷表法。我们对于每个 i，第一次更新 `f[u][i+j]` 时，一定是用“i+j 为定值，j取最小”的状态去更新`f[u][i+j]`，跟上面“先更新 k=0 ”是一个意思。这个写法很简洁，所以 AC Code 也用这个写法。

### 时间复杂度
子树大小从 O(n) ~ O(1) 都会有，树形 DP 子问题规模不尽相同，不能暴力用“状态数 × 转移数”算。不然你会算出高达 O(n³) 的时间。

dfs 枚举 n 个点，对于每个 u，沿用第三种写法的变量名，j 枚举一个 v 子树黑点数量的枚举量和 `s[v]` 相同，i 枚举量同 `s[u]`，加上每个 v 子问题求解，总枚举量和 u 子树里有序点对数量相同，时间为 O(n²)。

> [菲斯斯夫斯基](https://www.luogu.com.cn/article/0bm5ce54)：<br/>
> 任意两个点只会在 lca 处合并一次，所以时间复杂度是 O(n²) 的。

- [上下界优化保证平方复杂度](https://www.luogu.com.cn/discuss/708044)
- [本题大量题解代码有误](https://www.luogu.com.cn/discuss/808933)

这个看题解好像也吵了一阵子，但其实不只是时间复杂度不对。我觉得即使数组不越界，即使这样能 AC，但是使用了非法的状态来转移，给非法状态设特殊值都是很流氓的做法。“非法状态”在这里的意思是正向越界，比如对于 `f(v, j, ...)`，且 j 大于 v 子树大小这种状态。

~~好吧我的代码里也有黑点数大于 m（总黑点数）的状态可是那也只是常数大了而已~~

他们没加所谓的“上下界优化”，实际上就是用了这种非法转移。你用能从非法状态转移出正确答案，只是因为你设的非法状态初始化值能让你的普通状态过，而不是把问题转移到这个非法的子问题上。

在我看来这和 `#define int long long` 一样都是投机取巧。但凡写的时候想想怎样避开非法状态，你的代码也不会退化到 O(n³)。

### AC Code
```cpp
#include <iostream>
using ll=long long;
const int N=2e3+4;

int n, m, s[N], hd[N], to[N*2], nx[N*2];
ll f[N][N], wg[N*2];

void dp(int u) {
  s[u] = 1;
  for (int p=hd[u], v; p; p=nx[p]) {
    if (s[v = to[p]]) continue;
    dp(v); ll w = wg[p];
    for (int i=s[u]; i>=0; i--)
      for (int j=s[v]; j>=0; j--) {
        ll g = f[u][i] + f[v][j] + w*j*(m-j)
          + w*(s[v]-j)*(n-m-(s[v]-j));
        if (f[u][i+j] < g) f[u][i+j] = g;
      }
    s[u] += s[v];
  }
}

int main() {
  std::cin >> n >> m;
  for (int i=1; i<n; i++) {
    int u, v, w;
    std::cin >> u >> v >> w;
    to[i]  =v, wg[i]  =w, nx[i]  =hd[u], hd[u]=  i;
    to[i+n]=u, wg[i+n]=w, nx[i+n]=hd[v], hd[v]=i+n;
  }
  dp(1); std::cout << f[1][m] << '\n';
}
```

## [POJ 1947](http://poj.org/problem?id=1947) Rebuilding Roads
> 给定一棵节点数为 n(1 ≤ n ≤ 150) 的树，删边使某棵子树有 m 个节点。<br/>
> 最小化删除的边数。

设 f(u, t) 表示 u 子树切出大小为 t 子子树的最小删除边数。然后你发现这不好转移，因为枚举 v 时你不知道 `f[v]` 表示的子树包不包含 v。所以我们要求 f(u, t) 是包含 u 大小为 t 的子树，这样转移好办了。注意这里枚举的边界是 1 不是 0。
$$f(u,t,k)=\min_{\begin{array}{}1\le j\le s(v)\\1\le t-j\le s(u)\end{array}}
f(u,t-j,k-1)+f(v,j,|\mathrm{ch}\ v|)$$
不要漏掉 j=0 的转移。j=0相当于 v 这棵子树全剪掉，也要剪一刀。
$$f(u,t,k)\le f(u,t,k-1)+1,\quad t\le s(u)$$

### AC Code
```cpp
const int N=155;
int n, m, hd[N], to[N*2], nx[N*2];
int f[N][N], s[N];

void dfs(int u) {
  s[u] = 1; f[u][1] = 0;
  for (int p=hd[u], v; p; p=nx[p]) {
    if (s[v = to[p]]) continue;
    dfs(v);
    for (int i=s[u]; i; i--) {
      for (int j=s[v]; j; j--) {
        int g = f[u][i] + f[v][j];
        if (g < f[u][i+j]) f[u][i+j] = t;
      }
      f[u][i]++;
    }
    s[u] += s[v];
  }
}

// dfs(1); int ans = f[1][m];
// for (int i=2; i<=n; i++)
//   ans = std::min(ans, f[i][m]+1);
```
