# 做题记录
很多题会综合运用几种算法，我把它归到思维难度（对于这道题，而不是知识点本身）最大的那一类里。当然要是你点开一道题发现它是黄的或者绿的，那大概要么是这道题很典看标题就想起来了，要么就是有一些思想在里面。

## 图
- 最短路
  - [单源最短路径（弱化版）](https://www.luogu.com.cn/problem/P3371)
  - [全源最短路（Johnson）](https://www.luogu.com.cn/problem/P5905)
  - [棋盘](luogu.p3956/doc.md) 实现细节
  - [假期计划](https://www.luogu.com.cn/problem/P8817)
- [LCA](https://www.luogu.com.cn/problem/P3379)
  - [货车运输](https://www.luogu.com.cn/problem/P1967) MST
  - [Speaker](https://www.luogu.com.cn/problem/P11324) 观察到 $L(x,y)+L(y,z)$ 可以转化成 $L(x,t)+2L(t,y)+L(t,z)$。对于每个 $t$，预处理 $(t,y)$ 最大的利润，用 LCA 求路径和、点权最大值即可（点权指的是预处理后的，不是输入的 $c$）。
  - [仓鼠找 sugar](https://www.luogu.com.cn/problem/P3398) $P=(x1,y1)$，$Q=(x2,y2)$，两条路径相交仅当 $\operatorname{lca}P\in Q$ 或 $\operatorname{lca}Q\in P$。判断点在路径上可以用距离算。
- [连通性](connectivity/doc.md)
  - 差分约束
    - [糖果](https://www.luogu.com.cn/problem/P3275)
  - [SCC](https://www.luogu.com.cn/problem/P3387)
  - [割点](https://www.luogu.com.cn/problem/P3388)
  - [E-DCC](https://www.luogu.com.cn/problem/P8436)
  - [V-DCC](https://www.luogu.com.cn/problem/P8435)
- 二分图
  - [二分图最大匹配](https://www.luogu.com.cn/problem/P3386) 匈牙利算法板子
  - [西湖有雅座](https://www.luogu.com.cn/problem/P11409) 给不能共存的零件连边，有解仅当构成二分图，给每个连通块计数即可。
- [Flip Digits 2](https://atcoder.jp/contests/typical90/tasks/typical90_aw) 也是区间转换成图
- [冻结](https://www.luogu.com.cn/problem/P4822) 经典分层图

## 贪心
- 区间覆盖
  - [凌乱的 yyy](https://www.luogu.com.cn/problem/P1803)
  - [超速检测](https://www.luogu.com.cn/problem/P11232)
- 反悔
  - [Cow Coupons G](luogu.p3045.md) 上一次见还是 dinic
- 邻项交换排序 [ouuan: 应用及注意事项](https://ouuan.github.io/post/浅谈邻项交换排序的应用以及需要注意的问题)
  - [国王游戏](https://www.luogu.com.cn/problem/P1080) 高精度毒瘤 根据 $a_ib_i$ 排序
  - [皇后游戏](https://www.luogu.com.cn/problem/P2123) 上面注意事项的第二道例题
  - [加工生产调度](https://www.luogu.com.cn/problem/P1248) cmp 要满足严格弱序
  - [Mountain Climbing S](https://www.luogu.com.cn/problem/P1561) 虽然比上面的限制放松了，可是不影响最优方案
- [模拟工厂](https://www.luogu.com.cn/problem/P3161) 枚举
- [Strange Train Game](https://www.luogu.com.cn/problem/P11146) 反悔贪心 还有一些区间到图的转换思想
- [The Enchanted Forest](https://codeforces.com/problemset/problem/1687/A)
- [排队接水](https://www.luogu.com.cn/problem/P1223) 纯粹的排序不等式
- [猫耳小](https://www.luogu.com.cn/problem/P9202) 当时想复杂了。遍历，删除使 mex 为 $k$ 的数即可
- [骑士的工作](https://www.luogu.com.cn/problem/P2695) 尽可能让 zi 小的人砍尽可能小的头。
- [小A的糖果](https://www.luogu.com.cn/problem/P3817) 从 2 个和 3 个盒子开始递推，每遇到多的就把它吃掉
- [跳跳！](https://www.luogu.com.cn/problem/P4995)
  > 只需要证明第一步跳到了 $H=\max h$. 假设存在更优解，第一步是 $h_0(h_0\ne H)$.
  > - 若 $H$ 是终点，把顺序全部反过来，总贡献增大 $H^2-h^2$.
  > - 若 $H$ 不是终点，设第 $k$ 步跳到了 $H$，第 $(k+1)$ 步跳到了 $h'$. 把第 $1\sim k$ 步反转，总贡献增大
  > $$H^2-h_0^2+(h_0-h')^2-(H-h')^2=2h'(H-h)>0.$$
- [Strange Cake Game](https://www.luogu.com.cn/problem/P11143) 2 人决策，先考虑后手，分析先手的策略。
- [游戏预言](https://www.luogu.com.cn/problem/P2649)
  > 假设 J 总是把牌从大到小出，其余所有的牌都拿在一个人 F 手里，每次出 $(m-1)$ 张。如果 F 有牌能赢得这轮，剩下要出的 $(m-2$ 张牌拿最小的占位即可。
  >
  > 之后从大到小枚举每一张牌。对于 $i(1\le i\le mn)$ 号牌：
  > - 它不是 J 的牌，把它标记为 F 的牌。
  > - 它是 J 的牌。如果 F 的牌至少有 1 张能够压住 J，那么 F 总是应该选择压上。因为对于 F 的任意 2 张比 J 的这张大的牌，用哪张盖上 J 的这一张牌，另一张都可以在后续回合里继续创造胜利。
  >
  > 用桶存下 J 的每一张牌，从 $mn$ 到 1 反向枚举，如果是 F 的牌则加入储备牌库（$c\gets c+1$）；是 J 的就看看牌库有没有牌，如果有 $c\gets c-1$，否则 $a\gets a+1$。

## hash
  - [星战](https://www.luogu.com.cn/problem/P8819)

## dp
- 初步
  - [过河卒](https://www.luogu.com.cn/problem/P1002)
  - [染色](https://www.luogu.com.cn/problem/P11233)
  - [装箱问题](https://www.luogu.com.cn/problem/P1049)
  - [数字三角形 Number Triangles](https://www.luogu.com.cn/problem/P1216)
  - [最大正方形](https://www.luogu.com.cn/problem/P1387)
- 背包
  - [Cow Exhibition G](https://www.luogu.com.cn/problem/P2340) 设容量时要刚好等于
  - [采药](https://www.luogu.com.cn/problem/P1048)
  - [选课](https://www.luogu.com.cn/problem/P2014) DAG
  - [A+B Problem（再升级）](https://www.luogu.com.cn/problem/P1832)
  - [最大约数和](https://www.luogu.com.cn/problem/P1734)
  - [kkksc03考前临时抱佛脚](https://www.luogu.com.cn/problem/P2392)
  - [数列](luogu.p7961.md)
- 状态压缩
  - [互不侵犯](https://www.luogu.com.cn/problem/P1896) 轮廓线
  - [Grouping](https://atcoder.jp/contests/dp/tasks/dp_u)
- 子序列
  - [最长上升子序列 LIS](lis-print.md)
  - [最长公共子序列 LCS](https://atcoder.jp/contests/dp/tasks/dp_f)
  - [抉择](pjudge.1792.md)
  - [消消乐](https://www.luogu.com.cn/problem/P9753) 记录 $g_i$ 为让 $[j,i]$ 可消的最大 $j$。
  - [上升点列](https://www.luogu.com.cn/problem/P8816)
  - [书本整理](https://www.luogu.com.cn/problem/P1103)
- [树形](dp-tree.md)
  - [Rebuilding Roads](http://poj.org/problem?id=1947) 代码见上面的题解
  - [树上染色](https://www.luogu.com.cn/problem/P3177)
  - [函数调用](https://www.luogu.com.cn/problem/P7077)
  - [没有上司的舞会](https://www.luogu.com.cn/problem/P1352)
  - [LUK-Triumphal arch](https://www.luogu.com.cn/problem/P3554)
  - [Mag](https://www.luogu.com.cn/problem/P6287) 要观察最优路径的特点
  - [贪吃的九头龙](https://www.luogu.com.cn/problem/P4362)
    > 一棵树，把点分成 $m$ 组，每组不能空。包含节点 1 的那组恰好有 $k$ 个点。
    > 分配方法的代价是一些边权和，这些边的端点属于同一组。最小化分配代价。
    >
    > $1\le k\le n\le 300; 2\le m\le n. 1\le u\le v\le n; 0\le w\le 1e5.$
    >
    > - $(u,i,j)$ 表示状态 $(u,i)$ 子树里，$j$ 个点在 1 组；
    > - $f/g$ 表示 $u$ 在/不在 1 组时的最小代价。
    >
    > 转移：设
    > $$\begin{aligned}
    > f' & = f(u,i+1,j) & g' & = g(u,i+1,j)\\
    > f_u & = f(u,i,x) & g_u & = g(u,i,x)\\
    > f_v & = f(v,|\operatorname{ch}v|,j-x) & g_v & = g(v,|\operatorname{ch}v|,j-x)\\
    > \end{aligned}$$
    > 那么（记得处理无解的状态）
    > $$\begin{aligned}
    > f' & \gets\min_{0\le x\le j}\{f_u+f_v+w, f_u+g_v\}\\
    > g' & \gets\min_x\{g_u+f_v, g_u+g_v+[m=2]\times w\}
    > \end{aligned}$$
- DAG
  - [尼克的任务](https://www.luogu.com.cn/problem/P1280) 如果 $i$ 完成后能直接做 $j$ 任务，就连 $i\rightarrow j$ 的边，权值是空闲时间。
  - [车站分级](https://www.luogu.com.cn/problem/P1983) 如果 $n$ 和 $m$ 个点要两两连边，可以转化成 $n$ 个点到 $u$，$u$ 到 $m$ 个点，大幅减少边的数量。
- 优化
  - 单调队列
    - [股票交易](https://www.luogu.com.cn/problem/P3161)
  - 线段树
    - [杂赛选比](https://www.luogu.com.cn/problem/P10381) 只是用来优化最值
- [圣诞树](https://www.luogu.com.cn/problem/P9119)

## 数据结构
数据结构不是看完题想用什么实现合适，而是先想清楚要维护什么，再选择对应的数据结构。

- 链表
  - [Two Teams](http://codeforces.com/problemset/problem/1154/E)
- [单调队列](https://www.luogu.com.cn/problem/P1886)
  - [NOIP 2018 J1 T23](noip2018j1t23.md) 双向链表也可以做，但这个做法更通用
  - [Patrik 音乐会的等待](https://www.luogu.com.cn/problem/P1823)
  - [Flowerpot S](https://www.luogu.com.cn/problem/P2698)
- [单调栈](https://www.luogu.com.cn/problem/P5788)
- [并查集](https://www.luogu.com.cn/problem/P3367)
  - [程序自动分析](https://loj.ac/p/2129)
  - [星球大战](https://www.luogu.com.cn/problem/P1197)
  - [食物链](https://www.luogu.com.cn/problem/P2024)
  - [连续攻击游戏](https://www.luogu.com.cn/problem/P1640)
  - [Campus](https://codeforces.com/problemset/problem/571/D) 非常好的不能用路径压缩的例子。难点是协调合并时间和修改时间。
  - [三值逻辑](https://www.luogu.com.cn/problem/P9869)
- [ST 表](https://www.luogu.com.cn/problem/P3865)
  - [策略游戏](https://www.luogu.com.cn/problem/P8818)
- 树状数组 [1](https://www.luogu.com.cn/problem/P3374) [2](https://www.luogu.com.cn/problem/P3368)
  - 二阶前缀和
    - [无聊的数列](luogu.p1438.md)
- [线段树](segment-tree.md)
  - [Sasha and Array](https://codeforces.com/problemset/problem/718/C) 还有数列递推转化成矩阵乘法
- 扫描线
  - [矩形面积并](https://www.luogu.com.cn/problem/P5490)
  - [矩形周长](https://www.luogu.com.cn/problem/P1856)
  - [HH 的项链](https://www.luogu.com.cn/problem/P1972)
  - [园丁的烦恼](https://www.luogu.com.cn/problem/P2163)
  - [Maximum Waterfall](https://codeforces.com/contest/269/problem/D) 用 `set` 迭代器时记得考虑
- `std::set` / 平衡树
  - [报表统计](https://www.luogu.com.cn/problem/P1110) 代码细节想了很久
- `std::priorirty_queue` / 堆
  - [Multiplication Table](http://codeforces.com/problemset/problem/448/D)
  - [蚯蚓](luogu.p2827.md) 有没有平凡的单调性时可以考虑数组代替堆

## 模拟
- 高精度
  - [阶乘之和](https://www.luogu.com.cn/problem/P1009)
  - [高精度减法](https://www.luogu.com.cn/problem/P2142)
  - [A\*B Problem](https://www.luogu.com.cn/problem/P1303)
  - [A+B Problem（高精）](https://www.luogu.com.cn/problem/P1601)
- 表达式
  - [逻辑表达式](luogu.p8815.md) 表达式解析
  - [表达式求值](https://www.luogu.com.cn/problem/P1981)
- [侦探推理](https://www.luogu.com.cn/problem/P1039)
- [Mayan 游戏](https://www.luogu.com.cn/problem/P1312)
- [结构体](luogu.p9754.md) 代码规范
- [降雨量](luogu.p2471.md) 分类讨论
- [阅览室](https://www.luogu.com.cn/problem/P1844)
- [公交换乘](https://www.luogu.com.cn/problem/P5661)
- [消棋子](luogu.p3341.md)
  > - bfs 不要写假了，队列可能要开 $8N$，因为一个点可能 8 次重复入队。
  > - `check` 里要判断 `couldrm`。
  > - 取第 2 位是和 2 位与，取低 2 位是和 3 位与

## 构造
- 周期相关
  - [Vika and Price Tags](https://codeforces.com/problemset/problem/1848/C)
  - [Sequence of points](https://codeforces.com/problemset/problem/24/C)
- [反回文串](luogu.p11190.md)
- [均分纸牌](https://www.luogu.com.cn/problem/P1031)
  > 1.  算平均数；
  > 1.  求每堆纸牌与平均数的关系（多1记为1，少1记为-1）；
  > 1.  允许纸牌数量为负数情况下强行从右边的拿（可撤销贪心）。
- [Sherlock and his girlfriend](http://codeforces.com/problemset/problem/776/B) 质数一个色，合数一个色。
- [Vladik and fractions](http://codeforces.com/problemset/problem/743/C) $\frac2n=\frac1n+\frac1{n+1}+\frac1{n(n+1)}$
- [Milena and Admirer](https://codeforces.com/problemset/problem/1898/B)
- [Coloring Edges](https://codeforces.com/problemset/problem/1217/D)
  > 每一个环上一定含有在与不在 DAG 上的两种边。DAG 一种颜色，其它边另一种颜色即可。没有环的话直接全部同色，有环时拓扑序可以任意规定。
  >
  > 无重边的无向图里 DAG 对应生成树，每一个环一定经过树边和非树边。有重边情况就多了。
- [Strange Madoka Game](https://www.luogu.com.cn/problem/P11144)
- [神奇的幻方](https://www.luogu.com.cn/problem/P2615) 就题意而言其实是模拟，但这也给出了一种构造幻方的方法。
- [格雷码](https://www.luogu.com.cn/problem/P5657)
- [Center of the Earth](https://www.luogu.com.cn/problem/P5562)
- [Happy Card](https://www.luogu.com.cn/problem/P11323) 观察到删 4 个和删 3+1 是同一类，然后只要处理重复 1 2 3 次的牌即可。
- [三国游戏](https://www.luogu.com.cn/problem/P1199) 找每行次大值的最大。能更优的只能是某行最大值，这时它这一列一定有比它更大的，因为对称，这个数会在它这列标对应的行标以次大值出现。
- [TABOVI](https://www.luogu.com.cn/problem/P6446) 差分，记得从整体上考虑。

## 搜索
- 分治
  - 点 [点分治 1](https://www.luogu.com.cn/problem/P3806)
    - [Tree](https://www.luogu.com.cn/problem/P4178)
  - 分块
    - [线段树](luogu.p6025.md) 不是数据结构 类似数论分块
    - [余数求和](https://www.luogu.com.cn/problem/P2261)
    - [RMQ 的分块做法](https://www.luogu.com.cn/record/186252766)
- [靶形数独](https://www.luogu.com.cn/problem/P1074)
- [小木棍](https://www.luogu.com.cn/problem/P1120)
- [最大公约数](https://www.luogu.com.cn/problem/P7243) 纯粹的 BFS
- [生日蛋糕](https://www.luogu.com.cn/problem/P1731) 经典剪枝，剪一下多 10 分。
- [平面上的最接近点对](https://www.luogu.com.cn/problem/P1257)
- [骑士精神](https://www.luogu.com.cn/problem/P2324) IDA*，最后一次移动可以一步还原两个格子，记住不要只是 $z=2$ 时减一，是 $z\ge2$ 时都要减。

## 数学
- 数论
  - GCD
    - [最大公约数和最小公倍数问题](https://www.luogu.com.cn/problem/P1029)
      > $xy=pq$，在 $pq$ 的因子里枚举。
    - [Row GCD](https://codeforces.com/problemset/problem/1458/A)
    - [二元一次不定方程 (exgcd)](https://www.luogu.com.cn/problem/P5656)
  - [CRT](https://www.luogu.com.cn/problem/P1495)
    - [Strange Madoka Game](luogu.p11144.md) 伪高精度
  - 勾股数
    - [Triangle](https://codeforces.com/problemset/problem/407/A)
  - [卢卡斯定理/Lucas 定理](https://www.luogu.com.cn/problem/P3807)
  - [模意义下的乘法逆元](https://www.luogu.com.cn/problem/P3811)
  - 筛法 [线性筛素数](https://www.luogu.com.cn/problem/P3383)
    - 区间筛 [素数密度](https://www.luogu.com.cn/problem/P1835)
      > 把 $[2,\sqrt R]$ 的素数找到，对每个素数给 $[L,R]$ 内的合数标记。时间 $O(\sqrt R + (R-L)\log \log R)$
      - [A % B Problem](https://www.luogu.com.cn/problem/P1865)
- [线性方程组](https://www.luogu.com.cn/problem/P2455)
  - [高斯消元法](https://www.luogu.com.cn/problem/P3389)
  - [文明](bzoj.2854.md) CRT
  - [球形空间产生器](https://www.luogu.com.cn/problem/P4035)
- 整式
  - [轮换式](luogu.p5084.md)
- 计数
  - Catalan 数
    - [栈](https://www.luogu.com.cn/problem/P1044)
  - 加乘法原理
    - [A-B 数对](https://www.luogu.com.cn/problem/P1102)
    - [建造军营](luogu.p8867.md) dp 树形
    - [对角线](https://www.luogu.com.cn/problem/P2181) 任选 4 个点互相连线，只能形成 1 个点（另外 2 个交于图形外），所以是 $\binom n4$
  - 容斥原理
    - [皇帝的烦恼](luogu.p4409.md) dp 二分

## 基础算法
- 二分
  - [水杯降温](luogu.p11189.md) 不等式 差分
  - [种树](https://www.luogu.com.cn/problem/P9755)
  - [Widespread](https://atcoder.jp/contests/abc063/tasks/arc075_b)
  - [逛画展](https://www.luogu.com.cn/problem/P1638) 滑动窗口 维护不同数字个数
  - [重生](https://pjudge.ac/contest/1390/problem/21793) 一轮选择深度思考次数有 $c$ 的限制，这时只需要从整体考虑即可。
  - [数列分段 Section II](https://www.luogu.com.cn/problem/P1182) 最小化最大值，二分最大值
  - [排序](https://www.luogu.com.cn/problem/P2824) 很恶毒的线段树，如果下标 $[0,n)$ 注意特判 $[n,n)$ 的查询，不然会因为标记为 0 表示有标记而无限递归（如果用其它的代号也不见得会多好）。CCF 真题可以去 [LibreOJ](https://loj.ac) 下数据。
- 倍增
  - [64 位整数乘法](https://www.luogu.com.cn/problem/P10446)
  - [快速幂](https://www.luogu.com.cn/problem/P1226)
  - [矩阵快速幂](https://www.luogu.com.cn/problem/P3390)
  - [矩阵加速（数列）](https://www.luogu.com.cn/problem/P1939)
  - [Xor-Paths](https://codeforces.com/problemset/problem/1006/F) 和算序列里和为定值的数对做法相同
- 枚举
  - [回文质数 Prime Palindromes](https://www.luogu.com.cn/problem/P1217) 一些奇怪优化，比如排除偶数位数
  - [Binary search](https://www.luogu.com.cn/problem/P8481)
  - [麻将](https://www.luogu.com.cn/problem/P4050)
- 前缀和
  - [让我们异或吧](https://www.luogu.com.cn/problem/P2420)
- [快速读入](https://www.luogu.com.cn/problem/P10815)
- [矩阵取数游戏](luogu.p1005.md) 有区间 dp，但重点是 `int128` 使用方法。
- [PASTE](https://www.luogu.com.cn/problem/P1188) O(nk) = 1e8 可以直接做

## todo
- [跳石头](https://www.luogu.com.cn/problem/P2678)
- [搞清洁](https://www.luogu.com.cn/problem/P2684)
- [编辑距离](https://www.luogu.com.cn/problem/P2758)
- [奶酪](https://www.luogu.com.cn/problem/P3958)
- [最小生成树](https://www.luogu.com.cn/problem/P3366)
- [Cow Hurdles S](https://www.luogu.com.cn/problem/P2888)
- [Meteor Shower S](https://www.luogu.com.cn/problem/P2895)
- [Patting Heads S](https://www.luogu.com.cn/problem/P2926)
- [Diamond Collector S](https://www.luogu.com.cn/problem/P3143)
- [路障](https://www.luogu.com.cn/problem/P3395)
- [会议座位](https://www.luogu.com.cn/problem/P5149)
- [线段](https://www.luogu.com.cn/problem/P3842)
- [道路重建](https://www.luogu.com.cn/problem/P3905)
- [最大食物链计数](https://www.luogu.com.cn/problem/P4017)
- [消失之物](https://www.luogu.com.cn/problem/P4141)
- [单源最短路径（标准版）](https://www.luogu.com.cn/problem/P4779)
- [铺设道路](https://www.luogu.com.cn/problem/P5019)
- [简单题](https://www.luogu.com.cn/problem/P5057)
- [Xor Sum 4](https://www.luogu.com.cn/problem/AT_abc147_d)
- [Vacation](https://www.luogu.com.cn/problem/AT_dp_c)
- [Knapsack 2](https://www.luogu.com.cn/problem/AT_dp_e)
- [Longest Path](https://www.luogu.com.cn/problem/AT_dp_g)
- [Stones](https://www.luogu.com.cn/problem/AT_dp_k)
- [Deque](https://www.luogu.com.cn/problem/AT_dp_l)
- [Slimes](https://www.luogu.com.cn/problem/AT_dp_n)
- [Cows on Skates G](https://www.luogu.com.cn/problem/P6207)
- [包子凑数](https://www.luogu.com.cn/problem/P8646)
- [兔已着陆](https://www.luogu.com.cn/problem/P7870)
- [网络连接](https://www.luogu.com.cn/problem/P7911)
- [报数](https://www.luogu.com.cn/problem/P7960)
- [集合运算 2](https://www.luogu.com.cn/problem/B3633)
- [123](https://www.luogu.com.cn/problem/P8762)
- [Sequence Query](https://www.luogu.com.cn/problem/AT_abc241_d)
- [Shapes](https://www.luogu.com.cn/problem/AT_abc218_c)
- [E-梅莉的市场经济学](https://www.luogu.com.cn/problem/P8873)
- [原神](https://www.luogu.com.cn/problem/P9228)
- [Shift vs. CapsLock](https://www.luogu.com.cn/problem/AT_abc303_d)
- [方格取数](https://www.luogu.com.cn/problem/P1004)
- [传纸条](https://www.luogu.com.cn/problem/P1006)
- [加分二叉树](https://www.luogu.com.cn/problem/P1040)
- [金明的预算方案](https://www.luogu.com.cn/problem/P1064)
- [细胞分裂](https://www.luogu.com.cn/problem/P1069)
- [Hankson 的趣味题](https://www.luogu.com.cn/problem/P1072)
- [灾后重建](https://www.luogu.com.cn/problem/P1119)
- [机器人搬重物](https://www.luogu.com.cn/problem/P1126)
- [最短路计数](https://www.luogu.com.cn/problem/P1144)
- [中位数](https://www.luogu.com.cn/problem/P1168)
- [表达式的转换](https://www.luogu.com.cn/problem/P1175)
- [最大数](https://www.luogu.com.cn/problem/P1198)
- [排序](https://www.luogu.com.cn/problem/P1347)
- [油滴扩展](https://www.luogu.com.cn/problem/P1378)
- [八数码难题](https://www.luogu.com.cn/problem/P1379)
- [吃奶酪](https://www.luogu.com.cn/problem/P1433)
- [无聊的数列](https://www.luogu.com.cn/problem/P1438)
- [最长公共子序列](https://www.luogu.com.cn/problem/P1439)
- [通往奥格瑞玛的道路](https://www.luogu.com.cn/problem/P1462)
- [关押罪犯](https://www.luogu.com.cn/problem/P1525)
- [乌龟棋](https://www.luogu.com.cn/problem/P1541)
- [序列合并](https://www.luogu.com.cn/problem/P1631)
- [宝物筛选](https://www.luogu.com.cn/problem/P1776)
- [黑匣子](https://www.luogu.com.cn/problem/P1801)
- [石子合并](https://www.luogu.com.cn/problem/P1880)
- [团伙](https://www.luogu.com.cn/problem/P1892)
- [发射站](https://www.luogu.com.cn/problem/P1901)
- [Nim 游戏](https://www.luogu.com.cn/problem/P2197)
- [RoadBlock](https://w的ww.luogu.com.cn/problem/P2176)
- [荷马史诗](https://www.luogu.com.cn/problem/P2168)
- [贪婪大陆](https://www.luogu.com.cn/problem/P2184)
- [Cow Contest S](https://www.luogu.com.cn/problem/P2419)
- [着色方案](https://www.luogu.com.cn/problem/P2476)
- [时间复杂度](https://www.luogu.com.cn/problem/P3952)
- [KMP](https://www.luogu.com.cn/problem/P3375)
- [异或序列](https://www.luogu.com.cn/problem/P3917)
- [棋盘](https://www.luogu.com.cn/problem/P3956)
- [不开心的金明](https://www.luogu.com.cn/problem/P3985)
- [种花](https://www.luogu.com.cn/problem/P8865)
- [绿豆蛙的归宿](https://www.luogu.com.cn/problem/P4316)
- [分组](https://www.luogu.com.cn/problem/P4447)
- [最长异或路径](https://www.luogu.com.cn/problem/P4551)
- [密室](https://www.luogu.com.cn/problem/P4943)
- [大师](https://www.luogu.com.cn/problem/P4933)
- [货币系统](https://www.luogu.com.cn/problem/P5020)
- [纪念品](https://www.luogu.com.cn/problem/P5662)
- [加工零件](https://www.luogu.com.cn/problem/P5663)
- [网格图](https://www.luogu.com.cn/problem/P5687)
- [Milk Visits S](https://www.luogu.com.cn/problem/P5836)
- [Coins](https://www.luogu.com.cn/problem/AT_dp_i)
- [Candies](https://www.luogu.com.cn/problem/AT_dp_m)
- [表达式](https://www.luogu.com.cn/problem/P7073)
- [方格取数](https://www.luogu.com.cn/problem/P7074)
- [小熊的果篮](https://www.luogu.com.cn/problem/P7912)
- [廊桥分配](https://www.luogu.com.cn/problem/P7913)
- [商务旅行](https://www.luogu.com.cn/problem/P8855)
- [幂次](https://www.luogu.com.cn/problem/P9118)
- [商店砍价](https://www.luogu.com.cn/problem/P11188)
- [瑰丽华尔兹](https://www.luogu.com.cn/problem/P2254)
