# ✴️🌟手写线段树指南🌟⭐ WedJul17 2024
> [线段树](https://oi-wiki.org/ds/seg)是算法竞赛中常用的用来维护**区间信息**的数据结构.

但是我认为几乎所有涉及区间分治的问题都能拿线段树做. 线段树就像是保存了分治时每个子问题的答案, 当数据被修改时重新计算变化的那个子问题即可. 当涉及大规模的修改时, 还能引入懒惰标记(lazytag)来批量标记修改.
> ~~我们家线段树真的是太好用啦~~.

在这个文档里我会记录刚开始写线段树时犯过的错误~~并且让你/您也犯一次~~. 尽管这里代码数组下标和大部分人的习惯不同, 我还是希望读者看完后能熟悉线段树的流程, 以及那些函数怎么来的.
> 当你会写线段树后, 你会觉得“线段树就该这么写”.

## 只能区间加的线段树
线段树思想很朴素, ~~实现起来也简单~~?
> [Luogu P3372](https://www.luogu.com.cn/problem/P3372) 线段树 I <br/>
> 维护一个序列, 允许区间加、求区间和. 操作数和序列长度不超过 `1e5`.

线段树是二叉树, 可以把它建在数组上. 对于节点 $u$, 用 $2u+1$ 和 $2u+2$ 表示左右子结点. 线段树还要有懒惰标记, 所以我们来两个数组:
```cpp
const int N=1e5+5;  // 大小记得开 4N 不然诅咒你 RE!
ll sum[N*4];  // 这个节点对应区间的真实区间和
ll tag[N*4];  // 表示这个节点子树每个叶子将要加上的值
// 懒惰标记不能太懒惰 要保证访问到节点时 sum 一定要准确!
```

线段树不是树吗? 那我们来建个树:
```cpp
void init();  // 有人会写成 build
```

这里线段树要有两个功能, 一个是区间加，一个是区间和，那么当然要两个函数:
```cpp
// [lf, rg) 是修改区间, k 是区间要加上的数字
void range_add(int lf, int rg, ll k);
ll get_sum(int lf, int rg);
```
然后来想想函数怎么写……首先是建树，肯定要递归建树对吧:
- 初始化标记
- 如果是叶子 (区间长度等于 1)
  - 把数组元素放进叶子里
  - 完成建树
- 建左子树
- 建~~柚子树~~右子树
- 设置当前节点的区间和是两个子区间的区间和

看吧非常好的代码:
```cpp
void init(int u, int ul, int ur) {
  // tag[u]=0;  // tag作为全局数组已经初始化为 0
  if (ul+1==ur) {
    sum[u]=a[ul];
    return;
  }
  // 有些地方的写法是 ul+ur>>1, 下面这样写可以避免两个正数加时溢出
  int um=ul+(ur-ul>>1); // [ul, ur) -> [ul, um) + [um, ur)
  // u*2+1 左子结点, u*2+2 右子结点
  init(u*2+1, ul, um);
  init(u*2+2, um, ur);
  sum[u]=sum[u*2+1]+sum[u*2+2];
}
```
有人要说: “这不是要 `pushup` 吗?”但是先别急我们先假设没写过线段树的同学不知道什么是 `pushup`.

然后我们来写区间加. 如果要查找的区间 `[lf,rg)` 不是某个节点的管理区间的话, 线段树就把它切开让左右子树分别处理. 比如说样例给的序列建出了这个树:
```
输入:[1 5 4 2 3]  节点的意思: (u sum[u])[ul,ur)

                 (0 15)[0,5)
                 /          \
       (1 6)[0,2)          (2 9)[2,5)
        /      \            /     \
(3 1)[0,1) (4 5)[1,2) (5 4)[2,3) (6 5)[3,5)
                                   /    \
                           (13 2)[3,4) (14 3)[4,5)
```
要给 [0,4) 加上某个数, 线段树把它拆成 $[0,2)\cup[2,3)\cup[3,4)$, 对应 1、5、13号节点. 按着这个思路可以知道区间加的代码应该有这些步骤:
- 如果修改区间等于当前节点的管理区域
  - 打上懒惰标记
  - 退出函数
- 把当前区间分成两半
- 更新左半边区间
- 更新右半边区间

很合理的递归, 不是吗? 当然不是.
- 懒惰标记只是用来避免子树大量修改, 而要保证这个节点记录的区间和必须等于真实的区间和, 所以它自己的 `sum` 也要更新.
- 按照懒惰标记的意思, 叶节点不应该有懒惰标记. 打标记前要先判断是不是叶节点, 可以通过管理区间长度是不是1来检测.
- 对于某个节点, 如果它管理的区间不用整个修改, 按上面的流程它并不会更新. 但是我们希望即使某个子树更新了, 这个节点保存的区间和也会更新.

根据上面捉到的虫, 区间加更完整的流程如下(`[]`表示增加的部分):
- 如果修改区间等于当前节点的管理区域
  - \[区间和 `sum` 增加修改的值×区间长度\]
  - \[如果不是叶子\]
    - 打上懒惰标记
  - 退出函数
- 把当前区间分成两半
- 分别更新两半边区间
- \[更新当前节点的区间和\]

注意到这里“更新当前节点的区间和”和建树时“设置当前节点的区间和是两个子区间的区间和”是同一个操作, 我们把这两个流程合并起来(另外一个理由是, 在某些线段树题里, 合并两个区间不只是一个加号那么简单):
```cpp
void push_up(int u) { sum[u]=sum[u*2+1]+sum[u*2+2]; }
```

看起来我就要写模板了, 但是这样修改后又有了新的问题. 如果当前的懒惰标记没有下放 (就是子结点对应的区间和还要加上懒惰标记), 最后面 `pushup` 时又简单地用子结点和来设置区间和, 那么就是把两个不真实的答案合并成了新的答案, 这样不总能得到正确的和.
- 如果修改区间等于当前节点的管理区域
  - 区间和增加修改的值×区间长度
  - 如果不是叶子
    - 打上懒惰标记
  - 退出函数
- \[如果当前节点有懒惰标记\]
  - \[把标记下放到左节点\]
  - \[把标记下放到右节点\]
  - \[清空懒惰标记\]
- 把当前区间分成两半
- 分别更新两半边区间
- \[`push_up`\]更新当前节点的区间和

如果你尝试实现这个流程, 你会发现这里“标记下放到节点”时的操作和“如果修改区间等于当前节点的管理区域”里的操作相同, 所以我把它们合并到一个函数里:
```cpp
void node_add(ll k, int u, int ul, int ur) {
  sum[u] += k*(ur-ul);        // 区间和增加修改的值×区间长度
  if (ul+1!=ur) tag[u] += k;  // 如果不是叶子 打上懒惰标记
}
```
> 虽然线段树里会从外部调用的函数只有三个, 然而实现里却会多出另外三个函数. 当写这种很长的代码时, 总要记得把重复的代码合并成函数(但是记得要区分开, 它们重复是恰巧重复还是它们本身就是要实现相同的功能).
```cpp
// 写默认参数的话不需要每次外部调用时都填一次 (0, 0, n) 了
void range_add(int lf, int rg, ll k, int u=0, int ul=0, int ur=n) {
  if (lf==ul &&  rg==ur) {
    node_add(k, u, ul, ur);
    return;
  }
  int um=ul+(ur-ul>>1);
  if (tag[u]) {
    node_add(k, u*2+1, ul, um);
    node_add(k, u*2+2, um, ur);
    tag[u]=0;
  }
  if (lf<um) range_add(lf, std::min(rg, um), k, u*2+1, ul, um);
  if (um<rg) range_add(std::max(lf, um), rg, k, u*2+2, um, ur);
  push_up(u);
}
```
好啦现在我们来写求区间和的程序. 分治思想和区间修改的一样. 由于求区间和只是访问线段树而没有修改(也许你会觉得下放标记修改了线段树的数组, 可是已经保证了对于`tag[u]`, `u` 和它上级的节点保存的区间和无关, 所以下放标记不会改变上级节点), 无需在最后一步向上更新.
- 如果\[查询\]区间等于当前节点的管理区域
  - \[返回当前节点保存的区间和\]
- 如果当前节点有懒惰标记
  - 把标记下放到左节点
  - 把标记下放到右节点
  - 清空懒惰标记
- 把当前区间分成两半
- 分别\[计算\]两半边区间的区间和
- \[返回两半的和\]

你看这里又有重复的步骤了, 那我们再提取出一个 `push_down` 函数:
```cpp
void push_down(int u, int ul, int ur) {
  int um=ul+(ur-ul>>1);
  node_add(tag[u], ul, um); // 把标记下放到左节点
  node_add(tag[u], um, ur); // 把标记下放到右节点
  tag[u]=0;                 // 清空懒惰标记
}
```
把查询区间和的流程写成代码是这个样子
```cpp
ll get_sum(int lf, int rg, int u=0, int ul=0, int ur=n) {
  if (lf==ul && rg==ur) return sum[u];
  if (tag[u]) push_down(u, ul, ur);
  int um=ul+(ur-ul>>1); ll ret=0;
  if (lf<um) ret+=get_sum(lf, std::min(rg, um), u*2+1, ul, um);
  if (um<rg) ret+=get_sum(std::max(lf, um), rg, u*2+2, um, ur);
  return ret;
}
```
🎆🎆🎆太好了所有功能都完成啦! 来总结以下线段树有哪些函数:
- `void node_add(ll k, int u, int ul, int ur)`
  - 单独更新某个节点, 非叶子的话通过懒惰标记更新子树.
- `void push_up(int u)`
  - 当子树里维护的某个数据 (这道题里是区间和) 更新后节点 `u` 也要重新计算.
- `void push_down(int u, int ul, int ur)`
  - 要访问子结点数据 (这道题里是子区间和) 前记得下放标记.
  - 懒惰标记不要太懒, 不然会 WA.
- `void init(int u, int ul, int ur)`
  - 建树. 很多人也叫 `build`.
- `void range_add(int lf, int rg, ll k, int u=0, int ul=0, int ur=n)`
  - 区间修改 (这道题里是区间加), 等下我会解释更多区间操作的代码怎么写.
  - 示例 `range_add(2,5,3)` 下标在[2,5) or 下标为 2~4 or 第 3~5 个数加 3.
- `ll get_sum(int lf, int rg, int u=0, int ul=0, int ur=n)`
  - 求区间和. `get_sum(0, n)` 返回整个序列的和.

🙏🏼🙏🏼🙏🏼希望不要有人再被调试一个线段树的板子浪费数天时间了, 在此附上完整线段树板子+调试工具:
```cpp
/* * * * * * * * * * * * * * *
 * 祝福写注释的 oier 都能 AC *
 * * * * * * * * * * * * * * */
#include <iostream>
using ll=long long;
const int N=1e5+3;
int n, t, a[N];         // tag 子树将要加上的值; sum 的区间和已经加上了tag
ll sum[N*4], tag[N*4];  // 访问到 sum 时要保证 sum 等于实际的区间和
void node_add(ll k, int u, int ul, int ur) {
  sum[u] += k*(ur-ul);
  if (ul+1!=ur) tag[u] += k;  // 不要忘了懒惰标记
}
void push_up(int u) {
  sum[u] = sum[u*2+1] + sum[u*2+2];
}
void push_down(int u, int ul, int ur) {
  int um=ul+(ur-ul>>1);
  node_add(tag[u], u*2+1, ul, um);  // 注意是node_add不只是添加
  node_add(tag[u], u*2+2, um, ur);  // 还有懒惰标记
  tag[u] = 0;
}
void init(int u, int ul, int ur) {
  if (ul+1==ur) { sum[u]=a[ul]; return; }
  int um=ul+(ur-ul>>1);
  init(u*2+1, ul, um), init(u*2+2, um, ur);
  push_up(u); // 子树更新之后记得更新上级节点
}
void range_add(int lf, int rg, ll k, int u=0, int ul=0, int ur=n) {
  // 这行代码能让'#'号夹住的部分提交到 OJ 上时自动删除
#ifndef ONLINE_JUDGE
  if (!u) fprintf(stderr, "### range_add(%d %d %lld)\n", lf, rg, k);
  else fprintf(stderr, "range_add(%2d %2d %2d %2d %2d)\n", lf, rg, u, ul, ur);
#endif
  if (lf==ul && rg==ur) { node_add(k, u, ul, ur); return; }
  if (tag[u]) push_down(u, ul, ur); // 懒惰标记不要太懒记得及时更新
  int um=ul+(ur-ul>>1);
  // 记得用 min max 把多余的区间截掉不然会无限递归 (RE/TLE/段错误)
  // 当然也可以不截, 把递归边界修改成“查询区间包含在节点管理区间里”
  if (lf<um) range_add(lf, std::min(rg, um), k, u*2+1, ul, um);
  if (um<rg) range_add(std::max(lf, um), rg, k, u*2+2, um, ur);
  push_up(u);
}
ll get_sum(int lf, int rg, int u=0, int ul=0, int ur=n) {
#ifndef ONLINE_JUDGE
  if (!u) fprintf(stderr, "### get_sum(%d %d)\n", lf, rg);
  else fprintf(stderr, "get_sum(%2d %2d %2d %2d %2d)\n", lf, rg, u, ul, ur);
#endif
  if (lf==ul && rg==ur) return sum[u];
  if (tag[u]) push_down(u, ul, ur);
  // 注意要开 long long 时这两个变量不要放在一起声明了
  int um=ul+(ur-ul>>1); ll ret=0;
  if (lf<um) ret += get_sum(lf, std::min(rg, um), u*2+1, ul, um);
  if (um<rg) ret += get_sum(std::max(lf, um), rg, u*2+2, um, ur);
  return ret; // 这两个函数长得很像复制粘贴后记得修改
}
// =====DEBUG=====
void print_list(int step=1) {  // 对连续 step 个数字求和
  for (int i=0; i+step<=n; i++)
    std::cout << get_sum(i, i+step) << ' ';
  std::cout << '\n';
}
void print_tree(int u=0, int ul=0, int ur=n) {
  if (!u) std::cerr << "====print_tree.begin====\n"
    "type id [ul ur) sum tag\n";
  std::fprintf(stderr, "%s %2d [%-2d %2d) %3lld %lld\n",
      (ul+1==ur ? "lf  ":"  nd"), u, ul, ur, sum[u], tag[u]);
  if (ul+1==ur) return;
  int um=ul+(ur-ul>>1);
  print_tree(u*2+1, ul, um), print_tree(u*2+2, um, ur);
  if (!u) std::cerr << "----print_tree.end------\n";
}
// -----DEBUG-----
// main 略去
```
## 不仅能加还能乘的线段树
目前为止我们的线段树还只支持加法. 那如果引入乘法呢?
> [Luogu P3373](https://www.luogu.com.cn/problem/P3373) 线段树 II <br/>
> 维护一个序列, 允许区间加、区间乘、求区间模 $M$ 意义下的和.<br/>
> 操作数和序列长度不超过 1e5, $M=571373=11\times127\times409$.

As we know(正如我们知道的), 懒惰标记用来快速修改, 区间和用来快速查询. 懒惰标记向下走(`push_down`), 区间和向上更新(`push_up`). 这里增加的是修改操作, 那我们当然也用两个懒惰标记快速修改. 
```cpp
ll mul[N], plu[N];  // mul 表示将要乘上的值, plu 表示将要乘上的值
```
如果你去翻题解, 你会发现几乎大家都在讨论先乘后加还是先加后乘的事情. 这是什么意思啊? Recall that(回顾) 懒惰标记的意思是子树将要进行的操作. 那假设某个叶子结点的数字是 sum, 它对应的懒惰标记是 plu 和 mul, 那它实际的值是 sum × plu + mul 还是 (sum + mul) × plu? 我们先说前者, 即先乘后加.

先乘后加的另外一个意思是, 两次查询间总是会加一下乘一下又加加乘乘的, 而我们想要把所有加法后面的乘法操作, 等效成在加法前面乘法操作. 具体而言, 假设某个叶子结点的数字是 sum, 它对应的懒惰标记是 plu 和 mul, 然后现在我们来了一组新的操作: 乘 $k$ 加 $b$. 那如何把新的操作合并到旧的懒惰标记呢? 按照一些运算律可以发现:
$$\begin{aligned}
&(\mathrm{sum}\times\mathrm{mul}+\mathrm{plu})\times k+b\\
={}& \mathrm{sum}\times\mathrm{mul}\times k+(\mathrm{plu}\times k+b)
\end{aligned}$$
对于加的操作, 可以令$k=1$; 对于乘法操作, 可以令 $b=0$. 这就是 `node_update` 要变化的地方.
```cpp
// 在上一题这个函数叫 node_add
void node_update(ll k, ll b, int u, int ul, int ur) {
  sum[u] = (sum[u]*k+b*(ur-ul)) % m;
  if (ul+1==ur) return;
  mul[u] = mul[u]*k % m;      // (x*mul+plu)*k+b = x*mul*k + plu*k+b
  plu[u] = (plu[u]*k+b) % m;  // mul <- mul*k; plu <- plu*k+b
}
```
这道题最难想通的就是这里了. 其它修改都是细枝末节, 见下面的注释.
```cpp
// 虽然幸运值守恒 但我还是相信多写点注释肯定会获得额外幸运点
const int N=1e5+5;
using ll=long long;
int n, m, t, a[N];
ll sum[N*4], mul[N*4], plu[N*4];
// 由于乘法和加法取模运算来说都是自由的 为了防止溢出 算一步模一步
void push_up(int u) { sum[u] = (sum[u*2+1]+sum[u*2+2]) % m; }
void init(int u, int ul, int ur) {
  mul[u]=1; // mul 不是初始化为 0 为了找这个 bug 调了一天
  if (ul+1==ur) { sum[u]=a[ul]; return; }
  int um=ul+(ur-ul>>1);
  init(u*2+1, ul, um), init(u*2+2, um, ur);
  push_up(u);
}
void node_update(ll k, ll b, int u, int ul, int ur) {
  sum[u]=(sum[u]*k+b*(ur-ul)) % m;
  if (ul+1==ur) return;
  mul[u] = mul[u]*k % m;      // (x*mul+plu)*k+b = x*mul*k + (plu*k+b)
  plu[u] = (plu[u]*k+b) % m;  // mul <- mul*k; plu <- plu*k+b
}
void push_down(int u, int ul, int ur) {
  int um=ul+(ur-ul>>1);
  node_update(mul[u], plu[u], u*2+1, ul, um);
  node_update(mul[u], plu[u], u*2+2, um, ur);
  mul[u]=1, plu[u]=0; // 同理 mul 初始化为 1
}
void range_update(int lf, int rg, ll k, ll b,
    int u=0, int ul=0, int ur=n) {
  // 两种操作: 加法时 k=1; 乘法时 b=0
  if (lf==ul&&rg==ur) {
    node_update(k, b, u, ul, ur); // 参数顺序不能写错
    return;
  } // 检查标记时不要弄反 mul 和 plu
  if (mul[u]!=1 || plu[u]) push_down(u, ul, ur);
  int um=ul+(ur-ul>>1);
  if (lf<um) range_update(lf, std::min(rg, um), k, b, u*2+1, ul, um);
  if (um<rg) range_update(std::max(lf, um), rg, k, b, u*2+2, um, ur);
  push_up(u);
}
ll get_sum(int lf, int rg, int u=0, int ul=0, int ur=n) {
  if (lf==ul && rg==ur) return sum[u];
  if (mul[u]!=1 || plu[u]) push_down(u, ul, ur);
  int um=ul+(ur-ul>>1); ll ret=0; // 该开 long long 记得开.
  if (lf<um) ret += get_sum(lf, std::min(rg, um), u*2+1, ul, um);
  if (um<rg) ret += get_sum(std::max(lf, um), rg, u*2+2, um, ur);
  return ret % m;
}
// main 略去
```
然后来说先加后乘会发生什么. 还是同样的思路:
$$\begin{aligned}
&[(\mathrm{sum}+\mathrm{plu})\times\mathrm{mul}+b]\times k\\
={}&[(\mathrm{sum}+\mathrm{plu}+b\times\mathrm{mul}^{-1})\times\mathrm{mul}\times k\\
\end{aligned}$$
你会发现这要(yào)求乘法逆元. 很遗憾无论是数据的模数 571373 还是样例 38 都是个合数, 它们的剩余系(除以 $M$ 可能的余数)里总有些数和 $M$ 不互素(不互质/最大公约数比1大), 没有乘法逆元, 这种做法行不通.
