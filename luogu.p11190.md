# 反回文串 | 构造 - FriNov1 2024
[Luogu P11190](https://www.luogu.com.cn/problem/P11190)：给定一个长度为 $n$ 的字符串 $s$，把 $s$ 分成若干个非空子序列，使每一个子序列都**不**回文，最大化划分成的子序列数。要求所有子序列的元素不重不漏地和原序列一一对应。

## 做法
### 特殊性质 I
> $2|n$，且 $s$ 中每个字符的出现次数都不超过 $\frac n2$。

尝试两两配对，维护两个数组 $f$ 和 $g$ 和两个下标 $h$ 和 $t$（本文中 $t$ 均不指数据组数）。$h$ 表示已经匹配一半的数量，$t$ 表示完全匹配的。遍历每个数，如果没有匹配一半的，就优先匹配 $f$ 那一半；否则就考虑作为另一半。如果变成另一半后会回文，那么加到 $f$ 的末尾。可以发现这个匹配时 $f$ 多出来的永远是同样的字符。以 $\textrm{acabaccc}$ 为例（下标表示在原数组的位置）：
$$\begin{array}{c:cc}
&&&t&&&h\\ \hdashline
f&\mathrm a_1&\mathrm a_3&\mathrm a_5&\mathrm c_7&\mathrm c_8&\mathrm c_9\\
g&\mathrm c_2&\mathrm b_4&\mathrm c_6
\end{array}$$
后面的尾巴怎么办？可以把前面已经有的拆开，和后面的组合：
$$\begin{array}{c:}f&x&r_1&r_2\\ g&y
\end{array}\quad\longrightarrow\quad\begin{array}c
x&y\\ r_1&r_2\end{array}$$
实现中，我把 $r_1$ 当成尾巴的第一个字符，$r_2$ 当成尾巴的最后一个。这样就能省去一个数组标记谁处理过了，只需要处理完 2 个后修改 $h$ 和 $t$。

注意 $x$ 和 $y$ 不要和 $r$ 相等，可以维护 `beg` 表示将要拆哪一对。

如果 $n$ 是奇数，尾巴还会剩下一个数。这时枚举一下那个剩的加到哪对里即可。注意这个数不一定在原序列最后一个位置，加进去后不一定是加到末尾。
$$\begin{array}{c:cc}
f&\mathrm a_1&\mathrm a_3&\mathrm a_5&\mathrm b_4\\
g&\mathrm c_2&\mathrm c_7&\mathrm c_6&\mathrm c_9\\
&\mathrm c_8
\end{array}$$
特殊性质 I 用于保证上面的操作一定能完成。比如能找到可以拆开的那一对。

### 特殊性质 II
> $s$ 中仅有 `a` 和 `b`。

记出现次数多的为 `m`，另外一种字符叫 `l`。也是两两配对，一个 `m` 对一个 `l`，设匹配了 $t$ 对，最后一定会剩下一些 `m`。把剩下的分成两部分：
- A 是第一个 `l` 左边的 `m`，设有 $a$ 个。
- B 是第一个 `l` 右边的 `m`，设有 $b$ 个。

A 和 B 都可能是空串。下面每个分类讨论都在否定它上面假设的前提下进行，不能交换顺序。
1.  $t=0$：无解
1.  `lm`：`lmB`
1.  `ml` 且 $a+1\ne b$：`mAlB`
1.  `ml` 且 $a+1=b$ 且 $t=1$：无解
1.  `ml ml` 且 $a+1=b$：`AlB mml`
1.  `ml lm` 且 $a+1=b$：`mAl lmB`

### 一般情况
如果不满足特殊性质 I，那么存在唯一的出现最多的字符 $m$。把剩下的少数看成相同的字符（由于最优解不需要两个少数出现在同一个子序列里），就是 II 的情况了。

## AC Code
```cpp
#include <iostream>
#include <sstream>
#include <vector>
#include <string>
const int N = 1e5+5;

int t, n, cnt[26], f[N], g[N];
char m;
std::vector<int> a, b;
std::string st;
std::ostringstream sout;

void woa() {
  int ft = 0, tl = 0;
  for (int i = 1; i <= n; i++)
    if (ft == tl || st[f[tl]] == st[i]) f[ft++] = i;
    else g[tl++] = i;

  int beg = 0;
  while (ft - tl >= 2) {
    while (st[f[beg]] == st[f[ft-1]] ||
        st[g[beg]] == st[f[ft-1]]) beg++;
    g[tl] = f[tl];
    f[tl++] = g[beg];
    g[beg++] = f[--ft];
  } // ababcccc

  int tag = -1;
  if (ft > tl) do tag++;
    while (st[f[tag]] == st[f[ft-1]]);

  std::cout << "Huoyu\n" << tl << '\n';
  for (int i=0; i<tl; i++) {
    if (i == tag && g[i] > f[ft-1])
      std::swap(g[i], f[ft-1]);
    std::cout << "23"[i==tag] << ' ' << f[i] << ' ' << g[i];
    if (i == tag) std::cout << ' ' << f[ft-1];
    std::cout << '\n';
  }
}

void wob() {
  int i=0, j=0, top=0;
  while (true) {
    do j++; while (j <= n && st[j] == m);
    if (j > n) break;
    do i++; while (st[i] != m);
    if (i < j) f[top] = i, g[top] = j;
    else f[top] = j, g[top] = i;
    top++;
  }

  if (!top) {
    std::cout << "Shuiniao\n";
    return;
  }

  int pos = (st[f[0]] == m ? g[0] : f[0]);
  a.clear(), b.clear();
  while (true) {
    do i++; while (i <= n && st[i] != m);
    if (i > n) break;
    if (i < pos) a.emplace_back(i);
    else b.emplace_back(i);
  }

  int va = a.size(), vb = b.size();
  sout.str(""), pos = 0;
  sout << "Huoyu\n" << top << '\n';
  if (st[f[0]] != m) {
    sout << vb + 2  // lmB
      << ' ' << f[0] << ' ' << g[0];
    for (int x : b) sout << ' ' << x;
    pos = 1, sout << '\n';

  } else if (va + 1 != vb) {
    sout << va + vb + 2 << ' ' << f[0]; // mAlB
    for (int x : a) sout << ' ' << x;
    sout << ' ' << g[0];
    for (int x : b) sout << ' ' << x;
    pos = 1, sout << '\n';

  } else if (top == 1) {
    std::cout << "Shuiniao\n";
    return;

  } else if (st[f[1]] == m) {
    sout << va + vb + 1;  // AlB
    for (int x : a) sout << ' ' << x;
    sout << ' ' << g[0];
    for (int x : b) sout << ' ' << x;
    sout << "\n3 " << f[0] << ' ' << f[1] // mml
      << ' ' << g[1] << '\n', pos = 2;

  } else {
    sout << va + 2 << ' ' << f[0];  // mAl
    for (int x : a) sout << ' ' << x;
    sout << ' ' << g[0] << '\n' // lmB
      << vb + 2 << ' ' << f[1] << ' ' << g[1];
    for (int x : b) sout << ' ' << x;
    sout << '\n', pos = 2;
  }

  for (std::cout << sout.str(); pos < top; pos++)
    std::cout << "2 " << f[pos] << ' ' << g[pos] << '\n';
}

int main() {
  std::cin >> t >> t;
  while (t--) {
    std::cin >> n >> st, st = ' ' + st;
    for (int i=0; i<26; i++) cnt[i] = 0;
    for (int i=1; i<=n; i++) cnt[st[i] - 'a']++;

    m = 0;
    for (int i=1; i<26; i++)
      if (cnt[i] > cnt[m]) m = i;
    m += 'a';

    if (cnt[m-'a'] * 2 <= n) woa(); else wob();
  }
}
```
