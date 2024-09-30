# 文明 CRT+线性方程组 - MonSep30 2024
chnlich 非常喜欢文明这款游戏，为此他上网查了不少攻略。看了茫茫多的攻略后，chnlich 发现，有一个攻略根据城市的 $n$ 种资源的数量，对地图上许多位置的城市都计算了一个价值分数。现在他
把这些数据给了你。他希望你能帮他算出，每种资源的价值是多少，这样他就能够自己计算出其它城市的价值分数了。

简单说，就是解线性方程组 $A\vec x=\vec b$，其中：
- 保证有唯一自然数解，参数都是自然数。
- $a_ij\in[0, 10^9]$.
- $b_i\in[0, 10^{200}]$.
- $x_i\in[0, 10^{18}]$.

直接消元不行的，因为 x 和 b 都很大，x 乘一下就溢出了。可以用两个质数 $P=10^9+7$ 和 $Q=10^9+9$ 的余数来表示 x，而且这样写分数类也省了：
```cpp
ll f(ll x, const ll M) {
  ll p = M - 2, y = 1;
  while (p) {
    if (p & 1) y = y * x % M;
    p >>= 1, x = x * x % M;
  }
  return y;
}
```
接着原题没给 b 的取值范围的，下载数据才发现它连 `long long` 都放不下，要写成字符串。
```cpp
// copy
for (char ch: c[i])
  b[i][n] = (b[i][n] * 10 + int(ch - '0')) % M;

// input
for (int j=0; j<n; j++)
  std::cin >> a[i][j];
std::cin >> c[i];
```
还有算出余数后 CRT 合并结果的步骤，根快速幂一样的道理，直接乘会溢出，分开几次加就不会溢出了。
```cpp
ll h(ll x, ll y) {
  const ll M = P * Q;
  ll r = 0;
  while (y) {
    if (y & 1) r = (r + x) % M;
    y >>= 1, x = (x << 1) % M;
  }
  return r;
}
```
## AC Code
70 来行，大概 1420 字符。
```cpp
#include <iostream>
using ll = long long;
const int N = 205;
const ll P = 1e9+7, Q = 1e9+9;

int n; std::string c[N];
ll a[N][N], b[N][N], u[N], v[N];

ll f(ll x, const ll M) {
  ll p = M - 2, y = 1;
  while (p) {
    if (p & 1) y = y * x % M;
    p >>= 1, x = x * x % M;
  }
  return y;
}

ll h(ll x, ll y) {
  const ll M = P * Q;
  ll r = 0;
  while (y) {
    if (y & 1) r = (r + x) % M;
    y >>= 1, x = (x << 1) % M;
  }
  return r;
}

void g(ll x[], const ll M) {
  for (int i=0; i<n; i++)
    for (int j=0; j<n; j++)
      b[i][j] = a[i][j] % M;

  for (int i=0; i<n; i++) {
    b[i][n] = 0;
    for (char ch: c[i])
      b[i][n] = (b[i][n] * 10 + int(ch - '0')) % M;
  }

  for (int i=0; i<n; i++) {
    int t = i;
    while (!b[t][i]) t++;
    if (t == n) break;
    for (int j=0; j<=n; j++)
      std::swap(b[t][j], b[i][j]);

    for (int j=n; j>=i; j--)
      b[i][j] = b[i][j] * f(b[i][i], M) % M;

    for (int k=0; k<n; k++) if (k^i)
      for (int j=n; j>=i; j--)
        b[k][j] = (b[k][j] + M - b[i][j] * b[k][i] % M) % M;
  }

  for (int i=0; i<n; i++)
    x[i] = b[i][n];
}

int main() {
  std::cin >> n;
  for (int i=0; i<n; i++) {
    for (int j=0; j<n; j++)
      std::cin >> a[i][j];
    std::cin >> c[i];
  }

  g(u, P), g(v, Q);
  const ll p = f(P % Q, Q) * P,
           q = f(Q % P, P) * Q,
           m = P * Q;
  for (int i=0; i<n; i++)
    std::cout << (h(u[i], q) + h(v[i], p)) % m << '\n';
}
```
