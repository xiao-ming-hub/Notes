# ZJOI 2006 皇帝的烦恼 - SatJul20 2024
> [Luogu P4409](https://www.luogu.com.cn/problem/P4409)<br/>
> 给出 n 个环形互斥集合 $A_i$ 的大小 $|A_i|=a_i$, 求 $\min|\bigcup Ai|$.<br/>
> 环形互斥: $A_1\cap A_2=A_2\cap A_3=...=A_{n-1}\cap A_n=A_n\cap A_1=\emptyset$. <br/>
> 1 ≤ n ≤ 2e4, $a_i$ ≤ 1e5.

虽然这里给出了形式的描述, 还是建议看看原题面, 不然下面的说法可能有歧义.

在本文里设对于某种选法, $r=|\bigcup A_i|$. 因为相邻集合都互斥, 所以对于任意的相邻集, $r\ge|A_i\cup A_{i+1}|$, 也就是 $r\ge s=\max|A_i\cup A_{i+1}|$. **n 是偶数时可以取等**, 只需要令第奇数个集合尽量取前面的, 第偶数个集合尽量取后面的.

n 是奇数时上面的取法行不通. 尽管不知道有没有别的取法让 s 成为答案, 但我们知道如果答案比 s 小, 那肯定会有至少两个相邻集合的冲突, 一定不可以成为答案. **s 是答案的可能下界.**

假如某种选法里, $|\bigcup Ai|=r_0$, 那么任取 $r\ge r_0$, 肯定能安排出一种选法让 $|\bigcup A_i|\le r$ (就是等于 $r_0$ 的那种选法). 这句话说的是**答案具有单调性, 可以二分**. 按上一段的分析, 二分下界是 $s=\max|A_i\cup A_{i+1}|$, 而如果要求 n 个集合两两完全不一样, 那么可以得到**必然可以的上界** $t=\sum a_i$.

---

假设二分出一个大小 mi, 现在讲怎样验证能不能安排出总大小不超过 mi 的方案.

设 $A_i$ 和 $A_{i-1}$ 互斥时, 设 $u_i=|A_i\cap A_1|$ 最小 $f_i$, 最大为 $g_i$ (下面姑且让下标和后接括号表示同一个意思).

要想让 $u_i$ 更小, 就要让 u(i-1) 更大, 即 u(i-1) = g(i-1) 时, $u_i=f_i$.

这时 $A_i$ 要先把 $A_1$ 外的取完, 如果还不够的话, 取 A(i-1) 拿剩的.

因为二分下界 $s\ge|A_i\cup A_{i+1}|$ 可以保证相邻 (除了 1 和 n) 两个一定拿得完.

对于 A(i-1), 最多有 g(i-1) 在 $A_1$ 里.

剩余的 (a(i-1)-g(i-1)) 个都在外面 (按交集的定义, 这肯定不是负数).

然后因为 $A_1$ 确定了, 外面刚好有 $(\mathrm{mi}-a_1)$ 个.

也就是 $A_i$ 最多从外面取 $\mathrm{tmp}=((\mathrm{mi}-a_1)-(a(i-1)-g(i-1)))$ 个,

取完这些后再从 A1 里面取 $(a_i-\mathrm{tmp})$ 个 (如果 $\mathrm{tmp}\ge a_i$, 就没必要从 $A_1$ 里继续拿了).
$$
f_i=\max(0, a_i-\biggl\{[\mathrm{mi}-a_1]-\bigl[a(i-1)-g(i-1)\bigr]\biggr\})
$$
要想让 $u_i$ 更大, 就要让 u(i-1) 更小. u(i-1)=f(i-1)时, $u_i=g_i$.

这时就先让 $A_i$ 把 $A_1$ 里剩余的 $(a_1-f(i-1))$ 个取完.

或者说, $A_1$ 里的空位只有 $(a_1-f(i-1))$ 个, 比较空位和 $a_i$ 够不够用就好了.
$$
g_i=\min(a_i, a_1-f(i-1))
$$
---
AC code:
```cpp
#include <iostream>
const int N=200'05, A=100'005;
int n, a[N], lf, mi, rg;
bool ch() {
  int ff=a[0], gg=a[1], f, g;
  for (int i=1; i<n; i++) {
    // ff=f(i-1), gg=g(i-1), f=f[i], g=g[i]
    f=std::max(0, a[i]-((mi-a[0]) - (a[i-1]-gg)));
    g=std::min(a[i], a[0]-ff);
    ff=f, gg=g;
  }
  return !ff; // ff 不为 0 的话意味着 An 和 A1 冲突了
}
int main() {
  using std::cin;
  cin >> n;
  for (int i=0; i<n; i++) cin >> a[i], rg+=a[i];
  if (n==1) return std::cout << a[0] << '\n', 0;
  lf=a[0]+a[1];
  if (n==2) return std::cout << lf << '\n', 0;
  for (int i=1; i+1<n; i++)
    lf=std::max(lf, a[i]+a[i+1]);
  if (n%2==0) return std::cout << lf << '\n', 0;
  // 保证 n>=3 && n 是奇数;
  while (lf<rg) {
    mi=lf+(rg-lf>>1);
    if (ch()) rg=mi;
    else lf=mi+1;
  }
  std::cout << rg << '\n';
}
```
