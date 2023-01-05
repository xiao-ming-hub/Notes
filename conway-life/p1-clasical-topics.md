# **Part I** - Classical Topics - 2022.11.12
1. Early Life 生命游戏的早期发现
2. Still Lifes 静物
3. Oscillators 振荡器
4. Spaceships and Moving Objects 移动物体

## **Chapter 1**. Early Life
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

switch engine 会把自己移动到另一个地方并改变方向，然后在原来那留下一堆垃圾。不同于 queen bee 和 twin bee，switch engine 不会掉头，它会越走越远。
> An object like this one, which moves but leaves periodic junk behind it, is called a **puffer**.

移动时留下周期性的垃圾的结构叫做 puffer。
> In this particular case, any puffer created by using two switch engines is called an **ark**.

使用两个 switch engine 创建的 puffer 叫做 ark。
> A pattern that takes exceptionally long to stabilize, relative to other similarly sized patterns, is called a **methuselah**.

要经过很长时间才能稳定的结构叫做 methuselah。据说 Methuselah 是圣经中寿命最长的人
（969岁）。
> Generation $0$ refers to the starting configuration before applying the Life rules to it. Generation $1$ is the pattern that is obtained by applying the Life rules once, and generation $n$ is the pattern obtained by applying the Life rules $n$ times.

第 $0$ 代是初始状态，对于 $n=1,2,3,...$，第 $n$ 代就是指第 $n-1$ 代迭代一次后的状态。
> That is, does there exist a pattern that cannot ever appear in the evolution of any other pattern, but rather can only ever appear in generation 0? A pattern with this property is called a **Garden of Eden**.<br/>

不存在 parent 的图案叫做 Garden of Eden。这个词来源于元胞自动机（**cellular automata**），在康威生命游戏提出前就存在了。
> **定理 1.1** Garden of Eden 存在性。<br/>
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
在早期（生命游戏刚提出的时候）并没有现成的软件（如现在的 Golly）去模拟生命游戏，所以那时的爱好者多数是在草稿纸上写或者用石头模拟生命游戏的。在这种条件下他们仍能发现许多有趣的结构，这种探索精神值得我们学习。

第一章讲了一些结构的分类，以及一些结构的由来。即使是第一章，也讲了很多是目前国内的科普视频没有的内容（也可能是我没有找到，截至 2022.11.5）。

## **Chapter 2**. Still Lifes
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

能把滑翔机吃掉的图案叫 eater。通常用静物实现（振荡器也不是不可以）。下面这个东西叫 **eater 1**，它在很早的时候就被很多爱好者以静物的身份发现了它，但它能吃掉滑翔机的性质是 1971 年 Bill Gosper 的小组在 MIT 发现的。它遇到滑翔机时经过 4 代就能恢复原样，所以称它有“**recovery time** of 4 generations”。
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

## **Chapter 3**. Oscillators
1. Billiard Tables
2. Stabilizing Corners
3. Composite Periods and Sparks
4. Hasslers and Shuttles
5. Glider Loops and Reflectors 滑翔机循环、反射器
6. Herschel Tracks<br/>
   Herschel 轨道
7. Omniperiodicity
8. Phoenices

> Recall that an oscillator is a pattern that returns to its initial phase after 2 or more generations, and the smallest number of generations required is its **period**.

如果一个振荡器经过 $n\ge2$ 代后回到了原来的样子，满足条件的最小的 $n$ 是这个振荡器的**周期**。虽然可以把静物考虑成周期为 1 的振荡器，但在这本书里术语“振荡器”要求周期大于 1。“period $n$”可以缩写为“$\text pn$”，表示周期为 $n$。

最古老、最简单的构造振荡器的方法之一是构造一个 **billiard table**：准备一个缺角的矩形，在矩形外放置感应线圈，在矩形里放一些碎片使它成为一个振荡器。实际上也不一定要矩形，使用其它封闭图形构造的做法也很常见。
> In an oscillator, the cells that oscillate are called its **rotor** and the cells that stay alive for all generations are called its **stator**. A billiard table is thus an oscillator that has its rotor enclosed within its stator.

在一个振荡器里，振荡的细胞叫做 rotor，振荡时不动的叫做 stator。以 billiard table 为例，矩形和外面的感应线圈是 stator，里面的东西是 rotor。

另一种构造振荡器的方法是把已知的图案放在一起，比如在 1.3 节里构造的 queen bee shuttle。在 2.3 节里介绍的 eater 1 能吃很多东西。**two eaters** 是直接把两个 eater 1 放在一起构成的 p3 振荡器。一个可能的操作是用几个 eater 1 把一个图案围起来，把图案产生的垃圾清理掉。

一个非常简单的构造某种周期的振荡器是把两个振荡器组合在一起，构成一个周期是它们周期的最小公倍数的振荡器。比如，把 blinker 和 pulsar 看成一个整体，就组成了一个周期为 $\text{lcm}(2,3)=6$ 的振荡器。但这个太平凡了，我们希望能把两个振荡器互相发生反应。某些图案燃烧时当时会撒出一些小颗粒，我们就能利用这些小颗粒让几个这些图案组成振荡器，它们既会互相干扰，又能保持振荡。这些小颗粒就像是接口，原书里称其为 **spark**；具有 spark(s) 的图案叫做 **sparker**。我们还可以给 sparks 分一下类。
- domino spark：与外接矩形平行的两个独立出来的细胞；
- pipsquirters：与外接矩形垂直的两个独立出来的细胞；
- dot spark：单个独立出来的细胞；
- finger spark：有单个连接（上下左右）的单个细胞；
- thumb spark：有单个连接（左上、左下、右上、右下）的单个细胞；
- duoplet：斜着连接的两个独立出来的细胞；
- banana spark：见下图 `[]`。
```
banana spark[]
        [][]
```
最后的两类能反射滑翔机，这个性质很重要。

> We say that one pattern **hassles** another one if it repeatedly moves or changes it, typically in a periodic way so as to create an oscillator or gun.

如果 A 平凡地移动或者更改 B，特别是以一种周期的方式创建振荡器或枪，那么我们认为 A **hassles** B。以这种方式构造出的振荡器叫做 **hassler**。特别地，当一个 hassler 两个方向往返移动某个物件时，这个 hassler 被称为 shuttle（比如 1.3 节讲到的 queen bee shuttle）。
> This was the first p 23 oscillator found. Its name is a reference to the famous mathematician of the same name who published a list of 23 important unsolved (at that time) mathematical problems in 1900.

有一个 p23 振荡器叫 **David Hilbert**，它是第一个发现的 p23 振荡器。用这个名字是因为这位数学家在 1900 年提出了 23 个重要的当时未解决的难题（希尔伯特 23 问）。
> In particular, we would like to find a stationary pattern (i.e., a still life or an oscillator) with the property that if a glider hits it then the stationary pattern is unaffected and another glider is output in a different direction. Such a pattern is called a **reflector**.

我们想找到一些站点图案（振荡器或静物），它能够改变击中它的滑翔机的方向。这些图案叫做 reflector（反射者？）。
> How many generations are needed between subsequent gliders colliding with the reflector, which is called its **repeat time**.

一个滑翔机撞上 reflector 后需要多少代才能让另一个滑翔机撞上，这被称为它的 repeat time。
> Fortunately, faster stable reflectors are now known, with the smallest and fastest one being the **Snark**, with a repeat time of 43 generations.

最小的、最快的 stable reflector 是 Snark。
> The Snark was“almost”found by Dietrich Leithner sometime around 1998, but with the large unnamed still life at the right replaced by another block. In Leithner’s original pattern, the glider was still reflected 90 degrees, but the second block was deleted in the process. It wasn’t until about 15 years later, in April 2013, that the reflector was completed by Mike Playle, who wrote a custom search program to find a stable pattern that could replace the block and be unaffected by the reflection.

1998 年的某天，Dietrich Leithner“几乎”发现了这个东西，但它只是作为未命名的静物和一个 block 存在。它能反射滑翔机，但反射之后这个东西会自毁。15 年后，在 2013 年 4 月，Mike Playle 使用计算机搜索 block 的正确位置，成功修复了这个 bug。
> The name“Snark”comes from Lewis Carroll’s poem *The Hunting of the Snark*, in which ten characters try to hunt an imaginary animal bearing that name. Playle’s search program that found the Snark was similarly called *Bellman*, after one of the main characters in the poem.

“Snark”来源与诗《The Hunting of the Snark》，其中十个角色试图猎杀 Snark（一种虚构动物）。而 Playle 写的那个搜索 Snark 的程序被称为 Bellman（诗中的主角之一）。
> In order to actually be able to make use of a Herschel, we will need to find a pattern that it can interact with (called a **conduit**) so as to move it from one place to another, since left alone a Herschel will just explode.

其实我们不一定要滑翔机循环，还可以用些别的东西做成循环。以 Herschel 为例，它的爆炸很随机。如果想要利用它构成循环，那么需要找到一个图案与 Herchel 发生反应，并且在另外一个地方生成 Herchel。否则，Herchel 会在爆炸中把自己弄没。concuit 就是我们需要的那一类东西，可以把它理解为广义的 reflector。
> **定理 3.1** 多数周期大于等于 61 的 Herschel 轨道振荡器<br/>
> $\forall p\in \mathbb Z\cap[0,+\infty],\ \exists k[(256+616k)\mid p]  \Leftrightarrow (p\mod11)(p\mod17)\ne0$。 包含 4 个 R64 conduit 和每边 $2k$ 个 Fx77 conduit（共 $8k$ 个）的方形 Herschel 轨道能构造任何周期大于等于 61 且不是 7 或 11 的倍数。

注意到 $(256+616k)\mid p\Leftrightarrow\exists a\in\mathbb Z\cap[1,+\infty](pa=256+616k)$。问题转化为讨论什么样的 $p$ 能使得关于 $a$、$k$ 的方程 $pa-616k=256$ 有整数解。裴蜀定理（[定理 A.1](a-mathematical-miscellany.md)）告诉我们，当 $256\mid\gcd(p,616)$ 时有解。而 $616=2^3\times7\times11$，$256 = 2^8$，616 的质因数里 7、11 都不是 256 的质因数。所以，证毕。
> return to the question that we have been dancing around for the entirety of this chapter: do there exist oscillators with all periods in Conway’s Game of Life? If so, then Life is said to be **omniperiodic**, and this omniperiodicity problem is one of its oldest and most well-studied questions.

是否存在任意周期的振荡器？如果是的话，那么就认为生命游戏是 omniperiodic，我把它理解为全周期性。这个全周期性问题是最老的、研究最多的问题之一。目前周期为 19、34 和 41 的振荡器仍未找到。即使我们可以把周期为 2 的 blinker 和 周期为 17 的 honey thieves 看成一个周期为 34 的振荡器，但这个振荡器是“trivial”（平凡的）的，而我们想要的是“non-trivial”（不平凡的）振荡器。
> ...all of its live cells die every generation, yet the pattern as a whole lives. A pattern with this property is called a **phoenix**, after the mythological bird that is cyclically reborn after dying.

有一些图案，对于每一代，下一代里这一代的所有细胞都会死亡，同时又有新的细胞出生。这种图案就像神话中的凤凰，重复死亡、重生。我们把这种图案称为 phoenix（凤凰？）,下图就是一种。是否有某些更杂乱的图案也是凤凰，比如某些飞行器或者 puffers？定理 3.3 给出了否定回答。
```
      []..
    ..[]..[]
  []        ..
....        [][]
[][]        ....
  ..        []
    []..[]..
        []
```
> **定理 3.3** 凤凰都是振荡器 <br/>
> 不存在能不停拓展的凤凰，所以所有的凤凰都会演化成振荡器。

> **定理 3.4** 不存在周期为 3 的凤凰

## **Chapter 4**. Spaceships and Moving Objects
1. The Glider
2. The Light, Middle, and Heavyweight Spaceships
3. Corderships
4. Puffers and Rakes
5. Speed Limits
6. Speed and Period Status

就像振荡器，定义飞行器的 **period**（周期） 是能使它迭代回原来形态的最小正周期数。飞行器的 **speed**（速度）与它每迭代一个周期移动的距离有关。生命游戏里速度的单位是 **speed of light**，符号是 c，代表一个细胞每代。比如，滑翔机周期是 4，每 4 代它会往对角方向移动一个细胞，所以它的速度是 c/4；LWSS 每 4 代水平移动 2 个单位，所以它的速度是 c/2。
> The term **orthogonal** means straight left–right or up–down, as opposed to diagonal.

与 diagonal（对角、斜着）不同，术语“orthogonal”是上下或者左右方向。

因为找到从垃圾里找到新的基本飞行器太难了，所以这章会着重研究飞行器之间的反应。

我们定义滑翔机的一个属性 **color**（色相？），或者叫 **parity**（奇偶性），是一个滑翔机在移动过程中不变的量。它的色相可以理解为，当某个滑翔机的状态达到下面四个之一时，`()` 处的细胞在平面直角坐标系中横、纵坐标之和的奇偶性。通常我们不会说一个滑翔机是奇还是偶，而是说某两个滑翔机的色相是否相同。
```
  []      []()  ()[][]  []
    []  []  []  []      []  []
[][]()      []    []    ()[]
```
如果某个反射者反射的滑翔机与输入的滑翔机色相相同，那么这个滑翔机就是 **color-preserving**（保色）reflector。相反地，如果改变，那么就是 **color-changing**（变色）reflector。

另外两个量是 **lanes**（轨道）和 **timing**（时刻），它们都只同时考虑同向的滑翔机。这两个参数能明确、定量地指示两个滑翔机的相对位置。滑翔机的奇偶性等价于 lanes 的奇偶性。
> Some spaceships, especially ones that emit accessible sparks, are capable of carrying other objects along with them as they move. Such objects are called **tagalongs**...

有些能发射可用 sparks 的飞行器能在移动时带着别的对象，这些飞行器叫 tagalong（尾随物）。

有一种图案能把 tub 变成 barge，然后 long barge，long long barge 等等，这种图案叫做 **tubstretcher**。更一般地，如果我们不考虑具有这种性质的图案拉伸的是什么，那么它就是 **wickstretcher**。而有一些尾随物是飞行器在后，前面有一坨东西的，这种尾随物叫 pushalongs。两个特别有用于 xWSS 的尾随物是 **Schick engine** 和 **Coe ship**，4.4 小节讨论了这两个东西。
> ...we similarly distinguish between spaceships\* and **pseudo spaceships**, which are flotillae in which none of the component spaceships actually change their evolution at all, but at least one dead cell has more than 3 live neighbors in the flotilla but has fewer than 3 live neighbors when only one of the component spaceships is present.

就像我们区分严格静物和伪静物一样，我们区分飞行器\*和伪飞行器。严格定义我就不翻了。
> \*The term “strict spaceship” is not used in practice, and if the term “spaceship” is used unqualified then it is typically assumed that it cannot be broken down into smaller spaceships.

\*在练习里“strict spaceship”不会出现，如果使用术语“spaceship”时没有特殊说明，那么假设它不能分割成更小的飞行器。

也许我们能把一些飞行器放在一起，让它们互相干预，就像在 3.3 小节用 sparks 把 sparkers 放在一起那样。以这种方式创造的飞行器叫 flotillae（舰队）。
> Since flotillae can be made up of multiple copies of any of these three standard c/2 orthogonal spaceships–LWSS, MWSS, or HWSS–it is often convenient to refer to any of these components as an **xWSS**. This term comes from the idea of “x” being an unknown, which can be replaced by one of the other letters, “L”, “M”, or “H”.

我们把 LWSS，MWSS，HWSS 统称为 xWSS。
> One way to construct flotillae that are a bit less trivial is to consider what might happen if we were to construct a spaceship that followed the same pattern as the light, middle, and heavyweight spaceships, but is even longer than the heavyweight spaceship, such as the one displayed in Figure 4.17. This object is called an **overweight spaceship** (or **OWSS** for short), but its name is deceiving—it is not actually a spaceship at all.

一种没那么平凡地构造舰队的方法是构造 OWSS（超重飞行器），它是一种看着像飞行器、比 HWSS 更大的东西，但都不满足 spaceship 的定义。我们通常要在它周围放置一些东西来使它们成为飞行器。
> Named after Charles Corderman, who discovered the switch engine and most of the simple puffers based on it in 1971.

我们可以在 switch engine 后面添加 spaceship 来消除它产生的垃圾，从而构造飞行器。以这种方式构造出来的飞行器叫 Cordership。这个名字来源于 Charles Corderman，他在 1971 发现了 switch engine，和多数基于它的简单 puffer。

4.4 小节再次提到了 puffers，所以这里也再次记录一下。类比之前的 Gosper glider gun，我们想要构造移动的“枪”，或者说，**rake**（扫射枪）。构造一个扫射枪有两步：
1.  我们构造一个会留下垃圾的飞船，即曾经提到的 **puffers**；
2.  添加 xWSS 把垃圾变成滑翔机。

发射向前的滑翔机的扫射枪叫做 **forward space rake**，而发射向后的叫 **backward space rake**。

有时已有的扫射枪发射频率太快了，我们希望能删除一些滑翔机来降低发射频率，而 **Schick engine** 就能完成这一任务。
> **定理 4.1** 飞船速度极限<br/>
> 水平（斜向）飞行的有限飞船的最大速度是 c/2（c/4）。

这个定理由康威自己在简短地介绍生命游戏后证明。

定理 4.1 只是考虑在 **vacuum**（真空，即死细胞的空间）中移动，而如果在其它介质的情况则会有所不同，甚至达到光速。在这类非空介质中的移动物体叫 **signal**（信号；有时在真空中的也可叫做信号，比如滑翔机，也能在两个位置建立通信），而它移动的空间叫 **wire**（导线）。下面的这种导线叫 Zebra Stripes（斑马线）。
```
[][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][]

[][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][]

[][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][][]
```
> **定理 4.2** 平行斑马线速度极限 <br/>
> 方向与斑马线平行的有限移动物体速度都是 c。

为了利用信号，我们需要能创建信号的 **source**（信号源）、接受（摧毁）信号的 **sink**，还有能改变传播方向（甚至转换信号、连接不同介质）的 **signal elbow**。它们都能分别类比为滑翔机枪、eater 和 reflector。
> **定理 4.3** 垂直斑马线的速度极限 <br/>
> 方向垂直于斑马线的有限移动物体速度最大值是 2c/3。

> ...it is also possible for objects to pass through wires and destroy the wire in the process. When this happens, the wire is instead called a wick, and the objects that “burns” through the **wick** is called a **fuse**.

有些东西会穿过一条线，然后摧毁这条线。这种东西是 **fuse**（保险丝；导火线；引信），而 **wick**（灯芯；烛芯）是这条线。

4.5.3 Teleportation 讲的是一种“传送”飞行物的技术。它让飞行物撞上其它的东西，经过足够短的时间，在新的位置生成一个一样的飞行物，看上去就像加速飞行了一样。注意因为生命游戏规则的限制，这并没有超光速。**fast forward force field** 就是一个例子。但这种技术难以用于构造电路，因为很难在这么小的空间里稳定制造这么多东西，如果有的话，这些电路会变得很大。
> ...are called **knightships**, in reference to the knight from chess that moves in the same way. This particular knightship is called **Sir Robin**, after a knight from Monty Python.

存在飞行方向不是平行于网格或对角线方向的飞船（即路径斜率不是±1）。这种飞船叫 **oblique spaceship**（倾斜的飞船？）。我们规定每 $n$ 代水平移动 $x$ 格、纵向移动 $y$ 格的飞船的速度是 $(x,y)c/n$。因为斜着移动的飞船速度慢，所以特别难找。但还是有人找到了，比如说 Adam P. Goucher 在 2018 年 3 月基于 Tomas Rokicki 发现的 partial spaceship（部分的飞船？），构造出 **Sir Robin**。它是一个 $(2,1)c/6$ 的飞船；速度与这相同的飞船叫做 **knightships**，参考国际象棋里移动方式与这相同的棋子 knight（骑士？）。