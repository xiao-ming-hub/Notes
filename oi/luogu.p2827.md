# 蚯蚓 | 队列 - FriJan10 2025
[Luogu 2827](https://www.luogu.com.cn/problem/P2827)
维护一个大根堆，每次把堆顶的数 $x$ 取出来，把 $\lfloor px\rfloor$ 和 $x-\lfloor px\rfloor$ 入队，其余的数都自增 $q$。

输出第 $kt(k\in\mathbb N_+)$ 次操作时 $x$ 的长度和 $m$ 次操作后堆里大小排名整除 $t$ 的数。

> $p\in\mathbb Q\cap(0,1)$，$0\le q\le200$，$n\le10^5$，$m\le7\times10^6$。

## 做法
直接模拟，建堆 $O(n\log n)$，第 $i$ 次操作用时 $O(n+i)$，总时间
$$O\{n\log n+\frac m2[n+(n+m-1)]\}=O(n\log n+mn+m^2)$$

就堆里面的序关系而言，我们只需要维护堆里元素的相对大小。枚举第 $i(1\le i\le m)$ 次操作，我们维护每个数和 $iq$ 的差，这样就不需要每次遍历所有数了
$$O(n\log n+\sum_{j=n}^{m+n}\log j)=O[(n+m)\log n]$$

$q=0$ 时 $x$ 切开的两段都是单调不减的，所以可以考虑用数组代替单调队列。除了输入的数组，再加 $2$ 个数组 $\{b\}$ 和 $\{c\}$，让每个切开的数分别放到这两个数组末尾。只要保证按从大到小的顺序切，就能保证这两个数组具有单调性。
$$x-\lfloor px\rfloor=x+\lceil-px\rceil=\lceil(1-p)x\rceil$$
对于 $q>0$ 这个方法也成立。假设依次切开的两段是 $x$、$(y+q)$，那么借助
$$px+q>px+pq=p(x+q)>p(y+q)$$
就很容易比较切开后四段的大小（因为分别存在 $2$ 个数组里，我们只需要比较 $2$ 组大小）：
$$\lfloor px\rfloor+q=\lfloor px+q\rfloor\ge\lfloor p(y+q)\rfloor$$
$$\lceil (1-p)x\rceil+q=\lceil (1-p)x+q\rceil\ge\lceil (1-p)(y+q)\rceil$$
自然，同样的方法也能保持单调性。用时 $O(n\log n+m)$

## AC Code
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using pii=std::pair<int, int>;
int n, m, q, u, v, t, ha, hb, hc;
std::vector<int> a, b, c;

int get(std::vector<int> &v, int h) {
  return h < v.size() ? v[h] : 0x80000000;
}

int get() {
  pii x = std::max({
      pii(get(a, ha), 0),
      pii(get(b, hb), 1),
      pii(get(c, hc), 2)});
  if (x.second == 0) ha++;
  else if (x.second == 1) hb++;
  else hc++;
  return x.first;
}

int main() {
  std::cin >> n >> m >> q
    >> u >> v >> t;
  a.resize(n);
  b.reserve(m), c.reserve(m);
  for (int i=0; i<n; i++)
    std::cin >> a[i];
  std::sort(a.begin(), a.end(),
      std::greater<int>());
  for (int i=1; i<=m; i++) {
    long long x = get() + (i-1)*q, y = x*u/v;
    if (i % t == 0) 
      std::cout << x << ' ';
    b.emplace_back(y - i*q);
    c.emplace_back(x - y - i*q);
  }
  std::cout << '\n';
  for (int i=1; i<=n+m; i++) {
    int x = get() + m*q;
    if (i % t  == 0)
      std::cout << x << ' ';
  }
  std::cout << '\n';
}
```
