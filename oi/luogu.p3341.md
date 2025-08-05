# 消棋子 | 模拟 - WedJan8 2025
[Luogu P3341](https://www.luogu.com.cn/problem/P3341)
在一个棋盘上，每个格子要么是空，要么是一种颜色的棋子。同一种颜色的棋子恰好有两个。每一轮玩家选择一个空格子和“上下左右”四个方向中的两个方向。如果在这两个方向上
- 存在有棋子的格子
- 沿着这两个方向上第一个遇到的棋子颜色相同

那么可以将这两个棋子拿走。如果不满足就忽略这个操作。给出这样一个棋盘上棋子的布局和一个人每轮的选择。
1. 指出此人能消去多少棋子。 
1. 输出能消去最多棋子数量。

棋盘宽度和颜色数量不超过 $10^5$。

## 做法
用 `std::set` 维护每行、列的棋子，可以直接模拟这个人的操作。对于最多消去数量，可以用 BFS 完成。每轮取出将要消去的 $2$ 个同颜色棋子，把它们消去。并且对于 $2$ 个棋子的每一个，判断它四个方向上的第一个棋子（如果存在）能不能消掉。

这个贪心是最优的，因为任意一对可消去的棋子，在拿走棋盘上任意数量的棋子它们一定还能消去。

## 实现
变量：
- $n$：$颜色个数 = 棋子数 / 2$。
- $(a, b, c, d)_i$：颜色为 $i$ 的 $2$ 颗棋子坐标。
- $\{\mathrm{px}\}_x\ \{\mathrm{py}\}_y$：每 $x/y$ 相同的存在同一个 `set` 里。
- $\mathrm{St}$：`std::set` 的元素类型，维护位置和颜色。
- $(\mathrm{qu}, \mathrm{hd}, \mathrm{tl})$：BFS 维护将要移除的棋子颜色。
- $\mathrm{ip}_i$：记录颜色 $i$ 的棋子还在不在棋盘上。

我用 $2$ 位二进制数表示一个方向。
- 低位表示这个方向上变化的坐标，$0$ 对应 $x$，$1$ 对应 $y$。
- 高位坐标增减方向，$0$ 减 $1$ 增。
- `int otb(char) / dtb(int, int)` 把表示方向的字符 / 坐标变化量转化成方向编码。

为了减少重复的代码，设计下面的函数。
- `init()` 把不在棋盘上的棋子摆到棋盘上。
- `int get(x, y, w)` 搜索从 $(x,y)$ 开始，$w$ 方向上的第一颗棋子的颜色。如果这个位置有棋子或这个方向上没有棋子，返回非法标记 `-1`。
- `int tryrm(x, y, w1, w2)` 从 $(x,y)$ 开始，选择 $w_1$、$w_2$ 两个方向上能不能消去同颜色的棋子。如果可以返回棋子颜色，不可以就返回非法标记 `-1`。
- `remove(i)` 把颜色是 $i$ 的 $2$ 颗棋子从棋盘上拿走。返回值不使用。
- `bool tryrmpos(x, y, i)` 判断选择 $(x,y)$ 的格子能不能删去颜色是 $i$ 的 $2$ 颗棋子。
- `bool couldrm(i)` 判断颜色是 $i$ 的 $2$ 颗棋子能不能删。
- `check(x, y)` BFS 时取出一个颜色的 $2$ 颗棋子，分别调用这个函数扩展搜索。

可能在实现时遇到的问题：[提示后人](https://www.luogu.com.cn/discuss/1034318)。

## 时间复杂度
$m$ 次询问里，每次询问是常数次查找，每次查找时间 $O(\log n)$，总用时 $O(m\log n)$。

BFS 里，入队次数和 $n$ 同价，单次扩展时间 $O(\log n)$，总共 $O(n\log n)$。

所以总时间是 $O[(m+n)\log n]$。

## AC Code
```cpp
#include <set>
#include <iostream>
#include <cassert>
const int N = 1e5 + 5;

struct St {
  int pos, coo;
  St(int a, int b): pos(a), coo(b) {}
  bool operator<(const St &o) const {
    return pos < o.pos;
  }
};
using It = std::set<St>::iterator;

int n, m, cnt, x1[N], y1[N], x2[N], y2[N],
    hd, tl, qu[N*8];
std::set<St> px[N], py[N];
bool ip[N];

void init() {
  for (int i=0; i<n; i++) if (!ip[i]) {
    px[x1[i]].emplace(y1[i], i);
    px[x2[i]].emplace(y2[i], i);
    py[y1[i]].emplace(x1[i], i);
    py[y2[i]].emplace(x2[i], i);
    ip[i] = true;
  }
}

int otb(char o) {
  if (o == 'U') return 0b00;
  if (o == 'D') return 0b10;
  if (o == 'L') return 0b01;
  if (o == 'R') return 0b11;
  return assert(0), -1;
}

int dtb(int dx, int dy) {
  assert((!dx && dy) || (!dy && dx));
  if (dy) return (dy < 0 ? 0b01 : 0b11);
  return         (dx < 0 ? 0b00 : 0b10);
}

int get(int x, int y, int w) {
  int i; std::set<St> *pl;
  if (w&1) i = y, pl = px + x;
  else     i = x, pl = py + y;

  It it = pl->lower_bound({i, 0});
  if (it != pl->end() && it->pos == i) return -1;
  if (w&2) return (it == pl->end() ? -1 : it->coo);
  return (it == pl->begin() ? -1 : (--it)->coo);
}

int tryrm(int x, int y, int w1, int w2) {
  int c1 = get(x, y, w1), c2 = get(x, y, w2);
  return (~c1 && ~c2 && c1 == c2 ? c1 : -1);
}

bool remove(int i) {
  px[x1[i]].erase({y1[i], 0});
  px[x2[i]].erase({y2[i], 0});
  py[y1[i]].erase({x1[i], 0});
  py[y2[i]].erase({x2[i], 0});
  return ip[i] = false, cnt++;
}

bool tryrmpos(int x, int y, int i) {
  return tryrm(x, y, dtb(x1[i]-x, y1[i]-y),
      dtb(x2[i]-x, y2[i]-y)) == i;
}

bool couldrm(int i) {
  if (x1[i] == x2[i]) {
    int y = (y1[i] + y2[i])/2;
    return y!=y1[i] && y!=y2[i] &&
      tryrmpos(x1[i], y, i);
  }

  if (y1[i] == y2[i]) {
    int x = (x1[i] + x2[i])/2;
    return x!=x1[i] && x!=x2[i] &&
      tryrmpos(x, y1[i], i);
  }

  return tryrmpos(x1[i], y2[i], i)
    || tryrmpos(x2[i], y1[i], i);
}

void check(int x, int y) {
  for (int w=0; w<4; w++) {
    int i = get(x, y, w);
    if (~i && couldrm(i)) qu[tl++] = i;
  }
}

int main() {
  std::cin >> n >> n >> n;
  for (int i=0; i<n; i++)
    std::cin >> x1[i] >> y1[i]
      >> x2[i] >> y2[i];

  init(), std::cin >> m;
  while (m--) {
    int x, y, c; char o1, o2;
    std::cin >> x >> y >> o1 >> o2;
    c = tryrm(x, y, otb(o1), otb(o2));
    ~c && remove(c);
  }
  std::cout << cnt << ' ';

  init(), cnt = 0;
  for (int i=0; i<n; i++)
    if (couldrm(i)) qu[tl++] = i;

  while (hd < tl) {
    int i = qu[hd++];
    if (ip[i]) remove(i);
    else continue;
    check(x1[i], y1[i]);
    check(x2[i], y2[i]);
  }
  std::cout << cnt << '\n';
}
```
