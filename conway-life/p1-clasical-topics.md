# Conway's Game of Life: Mathematics and Construction 学习笔记 - SatNov12 2022
> Free PDF. See <https://conwaylife.com/book> for pattern files and print version.

记录专有名词和一些定理证明。
1. Classical Topics 入门
2. Circuitry and Logic 逻辑电路
3. Constructions 结构

## **Part I** - Classical Topics
1. Early Life 生命游戏的早期发现
2. Still Lifes 静物
3. Oscillators 振荡器
4. Spaceships and Moving Objects 移动物体

### **Chapter 1**. Early Life
1. Our First Technique: Random Fumbling <br/>
   介绍生命游戏的基本规则
2. Common Evolutionary Sequences <br/>
   常见演化序列
3. The Queen Bee
4. The B-Heptomino and Twin Bees
5. The Switch Engine
6. Methuselahs and Stability <br/>
   长寿者和稳定的定义
7. Gardens of Eden
8. Notes and Historical Remarks <br/>
   应该是注释和历史信息（讲故事），我后面会把这一章省略掉 <br/>
- Exercises 练习题，也是一样以后略去

一个细胞上下左右的四个邻居（**neighbor**）叫 **von Neumann neighborhood**，加上左上、左下、右上和右下四个邻居，共八个邻居，叫 **Moore neighborhood**。

如果一个结构（**pattern**） A 经过一次迭代后变成了结构 B，那么成 A 是 B 的 **parent**，B 是 A 的 **child**。特别地，方块（block）的 parent 可以是自己，它的 child 是自己。一个结构可能有多个（也可能没有）parent，但只有一个 child。
> The configurations that they take on in individual generations are called **phases**.

每一代的状态称为 phases。
> Random starting configurations like this one are sometimes called **soup**, and the objects that they leave behind are called **ash**.

soup 是初始状态，ash 是“燃烧后的灰烬”。
> A **polyomino** is a pattern made up of orthogonally connected live cells, and a **tetromino** is a polyomino with 4 live cells. More generally, polyominoes with 2, 3, 4, . . . , 8 live cells are called **dominoes**, **triominoes**, **tetrominoes**, **pentominoes**, **hexominoes**, **heptominoes**, and **octominoes**.

几连块的英语
| 中     | 英           |
| ------ | ------------ |
| 二连块 | dominoes     |
| 三连块 | triominoes   |
| 四连块 | tetrominoes  |
| 五连块 | pentominoes  |
| 六连块 | hexominoes   |
| 七连块 | heptominoes  |
| 八连块 | octominoes   |

有两种滑翔机枪 (Glider gun)，一种是基于 queen bee 的 Gosper glider gun（queen bee shuttle），另一种是基于 twin bee 的 twin bees shuttle。
> Just like the queen bee and twin bees, the **switch engine** leaves behind some debris and reappears later on in its evolution (this time after 48 generations), but in a different location and orientation. However, unlike the queen bee and twin bees, the switch engine does not return to its original position after repeating this reaction. Instead, the switch engine continually moves farther and farther away from where it started, just like a spaceship.

switch engine 会把自己移动到另一个地方然后在原来那留下一堆垃圾。不同于 queen bee 和 twin bee，switch engine 不会掉头，它会越走越远。
> An object like this one, which moves but leaves periodic junk behind it, is called a **puffer**.

移动时留下周期性的垃圾的结构叫做 puffer。
> In this particular case, any puffer created by using two switch engines is called an **ark**.

使用两个 switch engine 创建的 puffer 叫做 ark。
> A pattern that takes exceptionally long to stabilize, relative to other similarly sized patterns, is called a **methuselah**.

要经过很长时间才能稳定的结构叫做 methuselah。据说 Methuselah 是圣经中寿命最长的人
（969岁）。
> Generation $0$ refers to the starting configuration before applying the Life rules to it. Generation $1$ is the pattern that is obtained by applying the Life rules once, and generation $n$ is the pattern obtained by applying the Life rules $n$ times.

第 $0$ 代是初始状态，对于 $n=1,2,3,...$，第 $n$ 代就是指第 $n-1$ 代迭代一次后的状态。
> That is, does there exist a pattern that cannot ever appear in the evolution of any other pattern, but rather can only ever appear in generation 0? A pattern with this property is called a **Garden of Eden**.<br />

不存在 parent 的图案叫做 Garden of Eden。这个词来源于元胞自动机（**cellular automata**），在康威生命游戏提出前就存在了。
> **定理 1.1** Garden of Eden 存在性。<br />
> 在康威生命游戏中必然存在 Garden of Eden。

证明思路是，因为存在两个不同的图案，它们有着相同的 child，所以 Garden of Eden 必然存在。这个思路还有一些细节要考虑，比如说生命游戏的网格是无穷大的等。原书中的 $2^{36} − 1$ 要减一是因为已经找到了一组本身不同的、child 相同的图案（block 和 pre-block）。把 $-1$ 替换成 $-2$、$-3$ 都是可以的。练习 1.6 进一步探讨了这个证明（比如把 $6\times6$ 替换成 $5\times5$、$4\times4$ 证明还是否成立）。

Nicolay Beluchenko 提出了一种构造 Garden of Eden 的方法。思路是，不断给一种图案添加一个细胞（）格子，这时有两种选择，即添加活的和死的。我们分别计算出两种选择得到的新图案的 parent 的数量，选择数量较少的一种作为新的图案。一直重复就能构造出一个 Garden of Eden。另外要按照顺（或逆）时针顺序添加新细胞。

> Patterns like this one that cannot be any part of the evolution of another pattern (equivalently, they are still a Garden of Eden no matter what other cells are placed around them) are called **orphans**. Every pattern containing an orphan is (by definition) a Garden of Eden,

如果一个图案无论在周围如何放置其它细胞得到的新图案都是 Garden of Eden，那么这个图案是一种 orphans（孤儿？）。这个词由康威提出。另外，定理 1.1 的证明也证明了 orphans 的存在，不只是 Garden of Eden。

包含至少一个 orphans 的图案都是 Garden of Eden。这个命题逆过来也成立——每一个 Garden of Eden 包含一个 orphans。
> **定理 1.2** No Thin Gardens of Eden<br/>
> 不存在高度为 1 的 Garden of Eden。或者说，每一个高度为 1 的图案都存在至少一个 parent。

只要找到一种构造 parent 的方法即可。先把原来的图案围一圈（指下面的 `[]`；`..` 是原来的图案），这些东西在下一代必然会死掉，然后往 `--` 处添加细胞，使中间的原来的图案在下一代保持不变即可。这样，在下一代里除了 `..` 其它的活细胞都会消失。
```
[]  [][]  [][]  [][]  [][]  [][]  [][]  [][]  [][]  [][]  [][]  [][]  []
  [][][][][][][][][][][][][][][[][][]][][][][][][][][][][][][][][][][]
[][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][]
[][][]------------------------------------------------------------[][][]
  [][]--........................................................--[][]
[][][]------------------------------------------------------------[][][]
[][][][][][][][][][][][][][][][[][][]][][][][][][][][][][][][][][][][][]
  [][][][][][][][][][][][][][][[][][]][][][][][][][][][][][][][][][][]
[]  [][]  [][]  [][]  [][]  [][] [][]   [][]  [][]  [][]  [][]  [][]  []
```
具体的添加方法有点复杂。把上下两行替换为中间的取反。接着，从左到右遍历所有活细胞，如果它下一代会因为拥挤而亡，那么就把它右下角的活细胞往右移动一格即可。
```
  ()()  ()()()  ()    ()()      ()  ()()  ()()  ()()()  ()  ()()

↓↓↓

[]    []      []  [][]    [][][]  []    []    []      []  []    []
  ()()  ()()()  ()    ()()      ()  ()()  ()()  ()()()  ()  ()()
[]    []      []  [][]    [][][]  []    []    []      []  []    []

↓↓↓

()    ()      ()  ()()    ()()()  ()    ()    ()      ()  ()    ()
  ()()  ()()()  ()    ()()      ()  ()()  ()()  ()()()  ()  ()()
()    ()      ()    []    ()()()    []    []    []    ()    []
```
在早期（生命游戏刚提出的时候）并没有现成的软件（如现在的 Golly）去模拟生命游戏，所以那时的爱好者多数是在草稿纸上写或者用石头模拟生命游戏的。在这种条件下他们仍能发现许多有趣的结构，这种探索精神是值得我们去学习的。

第一章讲了一些结构的分类，以及一些结构的由来。即使是第一章，也讲了很多是目前国内的科普视频没有的内容（也可能是我没有找到，截至 2022.11.5）。

Exercise 1.4 (a) `()` 表示活细胞，其余是空。
```
                      ()
                    ()()
                  ()()
      () 1 2 3 4 5  ()()  ()() 1 2 3 4 5 6 7 8 9()
  ()()()                                        ()()()
()                                                    ()
  ()()()                                        ()()()
      ()            ()()  ()()                  ()
                  ()()
                    ()()
                      ()
```

### **Chapter 2**. Still Lifes
1. Strict and Pseudo Still Lifes 严格静物、伪静物
2. Still Life Grammar 静物命名规则
3. Eaters 吞噬者
4. Welded and Constrained Still Lifes 焊接静物
5. Still Life Density 静物密度

> we define a **strict still life** to be a still life that is either connected, or is disconnected but its different connected components cannot be partitioned into two or more sets that are still lifes by themselves

定义严格静物为相连的、或不相连但不可分割的图案。比如下面 `()` 和 `[]` 虽然都有两个部分组成，但因为前者可分割为两个静物（block），后者的两个部分不可分割，所以只有后者是严格静物。严格静物是静物的子集。
```
()()  ()()  [][]
()()  ()()  []    []
                [][]
```
> To capture this difference, we define a **pseudo still life** to be a disconnected still life with the property that its connected components can be partitioned into two or more sets that are individually still lifes, and furthermore at least one dead cell has more than 3 live neighbors in the overall pattern but has fewer than 3 live neighbors in the subpatterns.

伪静物是静物与严格静物的差集的子集。一个静物是伪静物要满足：
- 其连接的图案可以分成两个或多个单独的静物；
- 图案里存在至少死细胞有超过 3 个邻居，每个单独的静物包含的死细胞少于 3 个邻居（每个单独的静物挨得很近，之间的空隙不能再放活细胞了）。
> **定理 2.1** Four-Partitions of Pseudo Still Lifes <br/>
> 每一个伪静物能分成最多 4 类，使得相邻的两个静物不属于同一类。

这里可以类比成“染色”，也就是说最多用 4 种颜色就能把一个伪静物的所有严格静物区分开。书里只讲了它与染色问题的关系，引用“four color theorem”，证毕。

关于对部分很长的静物的命名法则：第 $(n+1)$ 小的前缀是 $\textsf{long}^n=\textsf{very}^{n-1}\ \textsf{long}=\textsf{extra}^{n-2}\ \text{long}$ 如 $\textsf{long}^3\ \textsf{snake}$ 也叫做 long long long snake、very very long snake、extra snake。

有时会在物体的一端添加一点活细胞让它稳定，这时得到的静物会冠以“with tail”后缀。如下面的“tub with tail”，其中 `[]` 是它的尾巴。
```
()()
()  ()
  ()
    [][][]
        []
```
当通过在一条一细胞宽的路径周围放置结构时构成的静物称为 **induction coils**（感应线圈？）。
> Deleting gliders is the simplest of these tasks, and it can be done with objects called **eaters**.

能把滑翔机吃掉的图案叫 eater。通常用静物实现（震荡器也不是不可以）。下面这个东西较 **eater 1**，它在很早的时候就被很多爱好者以静物的身份发现了它，但它能吃掉滑翔机的性质是 1971 年 Bill Gosper 的小组在 MIT 发现的。它遇到滑翔机时经过 4 代就能恢复原样，所以称它有“**recovery time** of 4 generations”。
```
()()
()  ()
    ()
    ()()
```
这东西除了能吃滑翔机，还能吃 LWSS、MWSS、pre-beehive、blinkers 等。
> **定理 2.2** 静物密度 I<br/>
> $n \times n$ 范围内的静物不超过 $\lfloor n^2/2\rfloor+2n$ 个活细胞。当 $n\to\infty$ 时，细胞密度不会大于 $1/2$。

我们会展示一个能在每个细胞的 von Neumann neighborhood 中重新分配那些标记（token）的、使每个细胞最终有至少 4 个标记的做法。如果我们能找到这样的做法，那么就能知道任意的 $n\times n$ 正方形里会有至少 $$
\underbrace{2n^2}_{原来\ n\times n\ 正方形里的}+\underbrace{8n}_{正方形周围的\ 4n\ 个邻居}=2(n^2+4n)\ \textsf{个标记。}$$
另一方面，如果在 $n\times n$ 正方形里有 $L$ 个活细胞，那么因为每个活细胞有至少 $4$ 个标记，所以 $$
\begin{aligned}
4L&\le n\times n\ 正方形里活细胞的\\
&\le n\times n\ 正方形里所有细胞的\\
&\le 2(n^2 + 4n)\textsf。
\end{aligned}$$
不等式两边同时除以 $4$ 得到 $L \le n^2/2+2n$，这说明静物密度不能超过 $1/2$。我们现在把注意力放到寻找能重新分配标记从而使每个活细胞有至少 4 个标记的方法（从而证明定理）。主要的思路是让死细胞把自己的标记分给四周的活细胞。然后书里分配完后就证毕了（？？？）。我还是没明白里面的 token 代表什么东西。
> **定理 2.3** 静物密度 II<br/>
> $n \times n$ 范围内的静物不超过 $\lfloor n^2/2\rfloor+\lceil2n/3\rceil$ 个活细胞。

实际上，$n\times n$ 正方形边缘不会有 $4n$ 个邻居能把自己的标记传递过来，最多也只有 $4\lceil2n/3\rceil$ 能进入那个正方形里。于是，定理 2.2 就很自然地出现了。对于 $n$ 比较大的情况，这两个定理差别不大，但在 $n$ 小的时候，这很明显更精确了（比如说带入 $n = 3$）。再极端一点，我们希望能得到最准确的上界，或者说，不仅仅是上界，而且这个数量必须能达到。经过计算机搜索，$1\le n\le60$ 时的最大值已经求出。定理 2.4 回答了 $n\ge61$ 时的一般情况。
> **定理 2.3** 静物密度 III<br/>
> 对于 $n\ge61$，$n \times n$ 范围内静物最大活细胞数量 $M(x)$ 满足下面的公式：$$
M(n)=\begin{cases}
  \begin{aligned}
    \lfloor n^2/2+17n/27-2\rfloor,\ n\equiv\ &0, 1, 3, 8, 9, 11, 16, 17, 19, 25, 27,\\
    &31, 33, 39, 41, 47, 49 \pmod {54}
  \end{aligned}\\
  \lfloor n^2/2+17n/27-1\rfloor,\ \textsf{otherwise}
\end{cases}$$

“它的证明超出了这本书的范围，有兴趣的读者自行阅读下面的资料”。
> [CS12] Geoffrey Chu and Peter J. Stuckey. A complete solution to the Maximum Density Still Life Problem. *Artificial Intelligence*, 184:1–16, 2012.
> | 成果      | 发现者                                          | 时间  |
> | --------- | ----------------------------------------------- | ----- |
> | $n\le10$  | Robert Bosch                                    | 1999  |
> | $n\le15$  | Bosch 和 Michael Trick                          | 2004  |
> | $n\le20$  | Javier Larrosa，Enric Morancho，和 David Niso   | 2005  |
> | $n\le27$  | Geoffrey Chu 等                                 | 2009  |
> | 完整解法  | Geoffrey Chu 和 Peter J. Stuckey                | 2012  |

### **Chapter 3**. Oscillators
1. Billiard Tables
2. Stabilizing Corners
3. Composite Periods and Sparks
4. Hasslers and Shuttles
5. Glider Loops and Reflectors
6. Herschel Tracks
7. Omniperiodicity
8. Phoenices

### **Chapter 4**. Spaceships and Moving Objects
1. The Glider
2. The Light, Middle, and Heavyweight Spaceships
3. Corderships
4. Puffers and Rakes
5. Speed Limits
6. Speed and Period Status
