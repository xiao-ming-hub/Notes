# 矩阵取数游戏 | int128 - TueNov26 2024
`__int128` 是占用 16 字节的变量类型。64 位 GCC/G++ 已支持，但**不在** C/C++ 标准里，使用要加双下划线，**不在** `std` 命名空间里，**不**属于标准库，使用时不需额外引入头文件。

因为它只是扩展了字节数，和高精度的实现不同，所以我把它成为“大精度”。 int128 的最大最小值分别为：
$$2^{127}-1=170141183460469231731687303715884105727>1.7\times10^{38}$$
$$-2^{127}=-170141183460469231731687303715884105728<-1.7\times10^{38}$$

它的无符号类型 `unsigned __int128` 相应最大值（最小值是 0）：
$$2^{128}=340282366920938463463374607431768211456>3.4\times10^{38}$$

在支持的编译器里，它支持基本类型都具有的所有**运算方式**(模板库、库函数不是这部分)，比如四则运算、类型转换、位运算、赋值。

它支持除了标准库里**除了需要根据实际类型确定**的所有模板函数。比如它能使用 `std::max/min/sort/unique` 等，不能使用 `std::i/ostream` 及其子类（大部分不支持的都是这种）。具体地，这些都不能直接用：
- `std::cin/cout/cerr/clog` 标注输入/输出/错误流/日志流
- `std::[i/o/io][s/f]stream` 输入/输出字符串/文件流。

C 语言没有模板，当然也没有针对这种类型的函数。除非你愿意损失精度来强制转换成普通的类型（那还要 int128 干嘛）。

让标准库能支持这个类型也很简单，自己写个运算符重载就好。
```cpp
using ll = __int128; // __int128_t
std::istream &operator>>(
    std::istream &cin, ll &x) {
  int a; cin >> a; x = a;
  return cin;
}

std::ostream &operator<<(
    std::ostream &cout, ll x) {
  if (!x) cout << '0';
  else if (x & ~((ll(1)<<60)-1))
    cout << x/100000000 << x%100000000;
  else cout << (long long)x;
  return cout;
}
```
多数题目输入/输出不涉及大精度，只在运算过程涉及；少数在输出数据也是大精度。当输入也是大精度时也好办，读入一个字符串一位位赋值就好。这个板子不是重点，全局运算符重载才是核心。毕竟现在已经很少考高精度了，但运算符重载是 OI 和实际工程都用得到的东西。

## 使用例
> [Luogu P1005](https://www.luogu.com.cn/problem/P1005)
> 有一个给定的 $m$ 行 $n$ 列的矩阵。
> 1.  每一次取数，从每行（行首或行尾）各取走一个，共 $n$ 个。$m$ 次后取完所有元素；
> 1.  在第 $i(\in[1,n])$ 次取数时，每取走一个 $x$，会得到 $2^ix$ 的分数；
> 1.  总得分为 $m$ 次取数得分之和。
> 
> 最大化总得分。
> $1\le n,m\le 80$，$0\le a_{i,j}\le1000$。

每行都是独立的。设 $f(i,j)$ 表示这行内的数取完后的最大得分，这一行的答案是 $f(1,n)$。转移：
$$f(i,j)=\max[f(i+1,j)\cdot wa_i,\ f(i,j-1)\cdot wa_j]$$
$$w=2^{n+i-j}$$

边界：$f(i,i)=2^na_i$. 长度要不要加 1 减 1 视实现确定，取个特殊值就好了~~不想算~~。

### AC Code
```cpp
#include <iostream>
using ll = __int128;

std::istream &operator>>(
    std::istream &cin, ll &x) {
  int a; cin >> a; x = a;
  return cin;
}

std::ostream &operator<<(
    std::ostream &cout, ll x) {
  if (!x) cout << '0';
  else if (x & ~((ll(1)<<60)-1))
    cout << x/100000000 << x%100000000;
  else cout << (long long)x;
  return cout;
}

const int N = 88;
ll f[N][N], a[N], ans;
int n, m;

int main() {
  std::cin >> m >> n;
  while (m--) {
    for (int i=0; i<n; i++) {
      std::cin >> a[i];
      f[i][i] = (a[i] << n);
    }

    ll w = (ll(1) << n-1);
    for (int d=1; d<n; d++, w>>=1)
      for (int i=0; i+d<n; i++)
        f[i][i+d] = std::max(
          f[i+1][i+d] + a[i]*w,
          f[i][i+d-1] + a[i+d]*w);
    ans += f[0][n-1];
  }

  std::cout << ans << '\n';
}
```
