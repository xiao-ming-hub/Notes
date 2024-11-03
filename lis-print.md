# dp/最长上升子序列 - WedJul10 2024
最长上升子序列（longest increasing subsequence，LIS），上升即严格单调递增；不严格单调递增（前≤后）叫做不下降.

先是朴素的平方时间做法. 设 $f_i$ 表示以 $a_i$ 结尾的 LIS 长度，考虑转移：来了一个新的数 $a_i$，加入谁最合适呢？当然要加入末尾比自己小而且最大的 LIS：
```cpp
for (int i=1; i<n; i++) {
  for (int j=0, m=0; j<i; j++)
    if (a[j] < a[i] && f[j] > f[m])
      m=j;
  f[i]=f[j]+1;
}
```
可以发现有一些无用的转移. 比如说, 已经遍历 $a$ 的前 6 个数 $[4,5,6,1,2,3]$，找到了 2 条一样长的 LIS $[1,2,3]_1，[4,5,6]_2$，那么后面那条一定没用的. 因为如果 $a_7$ 能延长 2 的话，那么一定能延长 1；能延长 1 的不一定能延长 2. 然而每次转移时都会遍历 2, 这就是多余转移.

解决方法是，不根据元素来设 dp，而根据 LIS 的长度来设. 设 $f_i$ 表示长度为 $i$ 的 LIS 末尾元素最小值. 为什么是最小？因为小的能连接更多的数. 按这个设法可以发现 $f$ 一定单调递增，转移时二分找到能插入的位置即可，用时 $O(n\log n)$.
```cpp
for (int i=0; i<n; i++) {
std::cin >> a[i];
int l=0, r=tot, m;
while (l<r) {
  m=l+(r-l>>1);
  if (f[m]<a[i]) l=m+1;
  else r=m;
}
f[r]=a[i], tot += (r==tot);
```
这道题要求输出方案，只需要记录转移，输出时顺着记录找即可. 设 `pos[i]` 表示 `f[i]` 在原数组的下标，`pre[i]` 表示 `a[i]` 所在的 LIS 的前一位. 这样子仍然能求出以 `a[i]` 为结尾的 LIS，因为 dp 完成后 `pre` 实际上记录了这样的树：
```
input: [3,1,4,1,5,9,2,6,5,3,5,8,9,7]
3
1-4-5-9
 \2  \6
  |\5
  \-3-5-8-9
         \7-9
output: [1,2,3,5,8,7,9]
```
从上图可以发现，这个方法输出的 LIS 一定是字典序最小的. AC code：
```cpp
#include <iostream>
const int N=100006;
int n, a[N], f[N], pos[N], pre[N], tot;
int main() {
  std::cin >> n;
  for (int i=0; i<n; i++) {
    std::cin >> a[i];
    int l=0, r=tot, m;
    while (l<r) {
      m=l+(r-l>>1);
      if (f[m]<a[i]) l=m+1;
      else r=m;
    }
    f[r]=a[i], tot += (r==tot);
    pos[r]=i;
    pre[i]=(r ? pos[r-1] : -1);
  }
  for (int p=pos[tot-1], i=tot; ~p; p=pre[p])
    f[--i]=a[p];
  for (int i=0; i<tot; i++)
    std::cout << f[i] << " \n"[i+1==tot];
}
```
