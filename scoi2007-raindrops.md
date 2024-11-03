# 降雨量 | 模拟/分类讨论 - ThuAug8 2024
[LuoguP2471](https://www.luogu.com.cn/problem/P2471)：设 u 和 v 是两个年份 (u < v)，对于某一年x，用 r(x) 表示这年的降雨量。定义 P(u, v) 表示 “v 年是自 u 年以来降雨量最多的”。形式化地，P(u, v) 为真仅当：
1. $r(u) \ge r(v)$
2. $\forall w,\ u<w<v\Rightarrow r(w)<r(v)$

给出 n 个年份及其降雨量的数据 y，r。m 次询问，每次给出 u，v。判断 P(u, v) 一定成立/不成立/不确定。

$1\le n\le \mathrm{5e4};\ 1\le m\le\mathrm{1e4};\ |y|,|u|,|v|\le\mathrm{1e9};\ 1\le r\le\mathrm{1e9}$。 

## 解析
对于 2，我们只需要判断区间最值。由于是静态询问，使用 ST 表维护即可。剩余的是一些边界情况。

这道题的边界很多，包括了左/右和中间有没有记录、记录是否完整。考虑从判断“成立”入手，接着向判断里依次加入“不成立”和“不确定”的分支。

可以发现，如果一定成立，那么从 u 到 v（含边界）所有年份必须有记录。注意先看有没有，再判断是不是（就像不要给空指针解引用）。结合上面分析，可以写出判真的流程：
- 如果段点都有记录
  - 如果端点不满足要求 1
    - return ...
  - 如果没有中间区间（$u+1=v$）
    - return “true”
  - 如果中间没有记录
    - return ...
  - 设 w 为中间已记录部分的最大值
  - 如果已记录的部分里有 2 的反例
    - return ...
  - 如果记录完整
    - return “true”
  - return ...
- return ...

然后考虑判假的情况。按道理，P 是用两个命题用“且”连接的复合命题，要否定 P 相当于否定某一个条件，所以检测两种情况（对应两个条件的否定）即可。但是这漏掉一种情况，如果对于某些空缺的数据，他们取某个值时能让1真2假（1 和 2 指代两个条件），取另外一些值时，会让1假2真。分开看，1 和 2 的取值不确定，但是合起来一定让 P 是假命题。
     
具体地说，1 和 2 对应一条不等式链 $\max\{u < w < v\}r(w) < r(v)\le r(u)$。 对于中间区间存在，左端点和整个中间区间都有记录，右端点没记录，而且 $\max r(w)\ge r(u)$，就是一个反例，这也要特判。综上，假命题有可能出现在三个分支里：
- 如果左右端点都有记录：不满足 1 / 最值不满足 2
- 右端点有记录，左端点没记录：最值不满足 2
- 有左边没有右边：$\max r(w)\ge r(u)$

然后就得到了判断假的代码。剩余的肯定就是不确定了（见 `check` 函数）。另外在用二分查找年份存储位置时要注意过程和找不到数据时的边界。


## AC code
```cpp
#include <iostream>

const int N=5e4+5, L=20;
const char *ans[]{"true", "false", "maybe"};

int n, q, st[N][L], year[N], lgn[N]{-1};
int u, v;
// 下标在左闭右开区间 [lf, rg) 的最大降雨量
int get_max(int lf, int rg) {
  int de = lgn[rg-lf];
  // 如果是空区间，会让de=-1报错 所以每次调用前要保证 lf < rg
  return std::max(st[lf][de], st[rg-(1<<de)][de]);
}

int ind(int y) {
  return std::lower_bound(year, year+n, y) - year;
  // 第一个[插入后保持序列不减的位置]
  // 如果是 upper_bound 就是最后一个[插入后保持序列不减的位置]
}

int check() {
  int iu=ind(u), iv=ind(v);
  if (year[iu]==u && year[iv]==v) {
    if (st[iu][0] < st[iv][0]) return 1;
    if (u+1==v) return 0;
    if (iu+1==iv) return 2;
    int m = get_max(iu+1, iv);
    if (m >= st[iv][0]) return 1;
    if (v-u == iv-iu) return 0;
    return 2;
  }
  if (u+1==v) return 2;
  if (year[iv] == v) {
    if (iu == iv) return 2;
    int m = get_max(iu, iv);
    if (m >= st[iv][0]) return 1;
    return 2;
  }
  if (year[iu] == u) {
    if (iu+1 == iv) return 2;
    int m = get_max(iu+1, iv);
    if (st[iu][0] <= m) return 1;
    return 2;
  }
  return 2;
}

int main() {
  std::cin >> n;
  for (int i=0; i<n; i++)
    std::cin >> year[i] >> st[i][0];
  // x最大1e9 用 x == ind(x) 检测x有没有记录时不会误判
  year[n]=1e9+100;

  for (int i=1; i<=n; i++)
    lgn[i] = lgn[i/2] + 1;

  for (int j=1, k=2; k<=n; j++, k<<=1)
    for (int i=0; i+k<=n; i++)
      st[i][j] = std::max(st[i][j-1], st[i+k/2][j-1]);

  std::cin >> q;
  while (q--) {
    std::cin >> u >> v;
    std::cout << ans[check()] << '\n';
  }
}
```
