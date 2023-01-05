# **A** - Mathematical Miscellany - 2022.11.20
1. Modular Congruence 模算术
2. Greatest Common Divisor and Least Common Multiple 最大公因数、最小公倍数
3. Big-Θ Notation 大 Θ 记号

> **定理 A.1** 裴蜀定理
> 方程 $ax+by=c$ 有整数解当且仅当 $c\mid\gcd(a,b)$。

对于充分性，$a\mid\gcd(a,b)\land b\mid\gcd(a,b)\Rightarrow\forall(x,y)[c=(ax+by)\mid\gcd(a,b)]$。对于必要性，使用 exgcd 构造解即可。
