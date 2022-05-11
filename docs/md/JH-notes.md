[TOC]

#### 3 确定性方法

##### 3.1 简介

假定 $P\neq NP$ ，那么 NP-hard 问题在多项式时间内是不可解的

但对于实际情况下的 NP-hard 问题，是可能存在确定性算法的求解的

Insight: slightly modify the problem specification & weaken the problem constraint 缩小问题空间，扩大解空间

1.对实际中的大部分输入高效的指数复杂度算法，解决问题的子问题

2.增长速度慢的指数复杂度算法

3.不需要解出问题，只需要提供一个可行解

##### 3.2 伪多项式时间算法 Pseudo-Polynomial-Time Algorithms

###### 基本概念

integer-valued problems

 输入是一串整数

TSP 背包问题 整数规划 节点覆盖问题 均是此类问题

目的是寻找在输入规模不太大的情况下的可行算法

动态规划和背包问题 动态规划的核心就是去寻找输入问题的子问题，且我们可以利用这些子问题高效地解决原问题

此类问题的输入有两个维度，一是数的大小，二是数的个数

$Value(h)-U$ 就是限制输入数大小到 h 的 问题 U，很显然这是原问题的一个子问题

当问题 U 不是多项式问题时，问题 $Value(h)-U$ 可以是

这里需要注意，伪多项式算法的定义 TimeA(x) = O(p(lxl, Max-Int(x))) ，其中x是问题的一个实例

此类伪多项式算法遵循了第一个方法，即通过寻找问题空间中的简单子类

###### 动态规划与背包问题

状态空间的转移 DPKP算法

###### 最大流问题和Ford-Fulkerson

最大流问题和最小割问题，一组经典的对偶问题

核心是寻找增广路径，增广路径和简单路径的区别在于增广路径的方向可以任意

###### 局限

强 NP-hard 问题

TSP旅行商问题是强 NP-hard 问题，证明的关键在于将 HC 哈密顿回路问题（已知的 NP-hard 问题）规约成 输入的数是多项式的TSP 问题

##### 3.3 参数化复杂度

###### 基本概念

还是遵循第一个方法，缩小问题空间，通过参数化来划分问题空间，通常更适用于连续问题

参数化多项式时间算法的定义：

(i) A solves U, and  

(ii) there exists a polynomial p and a function f : IN $\to$ IN such that, for every  x $\in$ L,  TimeA(x) f(Par(x)) . p(lxl). 

一个好的参数应该把握特定输入的本质难度，知道那些真正使问题变难的东西

p决定了对于给定参数，算法的效率，而f决定了哪些参数会使问题易处理

###### 参数化复杂度的应用

VC 点覆盖问题

方法一：对于度大于k的点，其一定在答案集合中，而对于度小于等于k的点，可以穷举其是否包含一个m的点覆盖

方法二：分治策略，随机选一条边，对两个点导出的新图，分别考虑点覆盖问题

##### 3.4 分支与界限(Branch and Bound)

###### 基本概念

这类方法是用来解决优化问题的。它基于回溯算法——一种遍历所有可行解空间的方法。问题在于搜索空间太大了，以至于到非多项式的大小。大致思想是去加速回溯的过程，通过剪去搜索过程中一些不可能的解空间，剪枝的标准是你在遍历该解空间之前，已经可以判断该解空间不存在可行解了。

回溯算法本质上是一种深度优先搜索，分支与界限优化的本质就是缩小解空间对应树的大小

剪枝的标准需要一些预处理得到一些bound

一些经典的计算界限的方法：

approximation algorithms ？

通过退化成线性方程的松弛 ？

随机取样 ？

本地搜索 ？

启发式算法 ？

关键在于剪枝剪的好不好

###### 在MAX-SAT和TSP问题的应用

MAX-SAT简单版本：在深度优先搜索树的过程中，但凡遇到使得某一子句不成立，即可剪枝

效率取决于：使用何种搜索，建树的顺序，剪枝的标准对问题具有唯一性

TSP预处理的版本：TSP首先是哈密顿回路，然后有两个观察：对于每个节点的度都是2，然后哈密顿回路，计算值的方式就是当前所有边权加起来，然后加上构成一个哈密顿回路所缺的边数

主要的优势在于对于此类问题，有了一定输入实例的可行解

关键就在于算bound

由于没有一般的搜索策略，所以提高算法效率的方法是：一个能够给到尽可能紧的bound的好的算法；一个能高效计算每个节点的bound的算法

如果等待太久，一种可能是结束搜索，然后返回当前最优解，还有一种就是把要求改低，只要求解与最优解相差不超过百分之多少，这样剪枝的时候，当发现子树的解最优优不过百分之多少时，就可以剪枝了

NNS问题， 最近邻问题

##### 3.6 局部搜索(Local Search)

局部搜索是针对优化问题的一种算法设计方法

它的重要性在于：

- 它是高阶局部搜索，比如模拟退火算法(SA)和 禁忌搜索算法(tabu search)，的基础
- 可以更好地考虑问题复杂性，从而确定易处理的搜索优化问题的边界

局部搜索的本质就是实现一个受限制的搜索，而受限的搜索是依赖于一个叫邻域(neighborhood)的概念

Tips: $Pot(X)$是指$X$的幂集

在实际应用中，邻域的寻找通常是通过在可行解上的转移(local transformations)完成的。比如MAX-SAT问题中改变一个变量的布尔值。

局部搜索算法从一个候选解开始，[反复](https://zh.wikipedia.org/wiki/迭代)探索其[邻域](https://zh.wikipedia.org/wiki/邻域)，并移动到邻域中的最后解。所以要使用局部搜索算法必须首先在搜索空间中定义邻域。比如，在最小顶点覆盖问题中，一个覆盖的邻域可以是只改变一个顶点能够到达的另一种覆盖；在布尔可满足性问题中，一个变量赋值的邻域可以是只改变一个变量的值能到达的所有赋值。对于同一个问题可以有很多种邻域的定义。如果一个局部优化算法将邻域定义为只改变 **k** 个成分能够到达的解，那么这个算法可以称为 **k-opt**。

通常，每个候选解的邻域包含多个解。所谓**局部**搜索，就是只根据[邻域](https://zh.wikipedia.org/wiki/邻域)中的信息来决定移动方向。如果选择的是最大化[目标函数](https://zh.wikipedia.org/w/index.php?title=目标函数&action=edit&redlink=1)的解，那么这种局部搜索算法又可以叫做[爬山算法](https://zh.wikipedia.org/wiki/爬山算法)。当邻域中已经没有比当前最优的解的时候，局部搜索就困在了局部最优解。

能尽快找到全局最优解的决定因素有：初始解的选择，邻域的划分以及转移的策略

##### 3.7 松弛算法

Linear Programming (LP) is an optimization problem that can be solved in  polynomial time, while Oil-linear programming (O/l-LP) and integer linear  programming (IP) are NP-hard. 

This approach is called relaxation  because one relaxes the requirement of finding an optimal solution over {O, I}  or over positive integers by searching for an optimal solution over reals. 

(1) Reduction  Express a given instance x of an optimization problem U as an input  instance I(x) of O/l-LP or IP.  (2) Relaxation  Consider I(x) as an instance of LP and compute an optimal solution 0: to  I(x) by an algorithm for linear programming.  (3) Solving the original problem  Use 0: to either compute an optimal solution for the original problem, or  to find a high-quality feasible solution for the original problem.

#### 4 近似算法

##### 4.1 介绍

- 给出优化问题逼近概念的基本原理，包括根据多项式时间逼近性对优化问题进行分类
- 展示一些近似算法的透明示例，以展示设计高效近似算法的成功技术
- 给出证明多项式时间不可近似性下界的方法的一些基本思想

##### 4.2 基础

我们从近似算法的基本定义开始。 通俗地说，优化问题的近似算法是一种提供可行解的算法，其质量与最优解的质量相差不大。

**几个定义**

relative error

approximation ratio

δ-approximation algorithm

f(n)-approximation algorithm

(GMS)GREEDY MAKESPAN SCHEDULE

**优化问题的分类**

近似的稳定性

##### 4.3 算法设计

###### Cover Problems, Greedy Method, and Relaxation to Linear Programming

###### Maximum Cut Problem and Local Search

###### Knapsack Problem and PTAS

###### Traveling Salesperson Problem and Stability of Approximation

###### 装箱问题，调度问题和对偶近似算法

这节最终目标是求解MS.

(i)设计装箱问题的一个dual polynomial-time approximation scheme(dual PTAS)

问题描述：给定n个不同重量的物品和每个包裹的容量，将每个物品分配到一个包裹中，使得使用的包裹数量最少.

BIN-P问题的输入是一个在$[0,1]$上的有理数向量$I=(r_1,r_2,...,r_n)$. 一个输入$I$的可行解是$\{0,1\}^n$向量组成的集合$S$，其中对于任意的$Y\in S$，
$$
Y^T\cdot(r_1,r_2,...,r_n)\le 1,
$$
并且
$$
\sum_{Y\in S}Y=(1,1,...,1).
$$
目标是使得$S$的势最小.

我们定义一个函数$h:L_I\times\sum_{O}^*\to\mathbb{R}^{\ge 0}$如下，
$$
h(I,S)=\max\{\max\{Y^T\cdot(r_1,r_2,...,r_n|Y\in S)\}-1,0\}.
$$
第一步：使用动态规划的方法，设计一个针对包含常数个不同值的BIN-P问腿的输入实例的多项式算法DPB-P
$$
\text{Bin-P}(m_1,...,m_s)=1+\min_{x_1,...,x_s}\{\text{Bin-P}(m_1-x_1,...,m_s,x_s)|\sum_{i=1}^sx_iq_i\le1\}.
$$


第二步：应用上述算法来获得一个输入实例不包含非常小的值的BIN-P问题的h-dual PTAS

想法是去近似每一个输入实例$I=(q_1,...,q_n)$通过将这n个q舍入到$s=\frac{\log_2{(1/\epsilon)}}{\epsilon}$个固定值的方式，然后使用DPB-P来求解近似完的输入.

(i) r = cost(T1, ... ,Tr) = Bin-P(n1, ... ,ns):::; OptBin-p(I), where (Tl"'"  Tr) is the optimal solution for the input Round( 1) = (h, ... , Is, nl, ... , ns)  computed by BP-PTAe [Ti is the set of the multiplicatives of the indices  of the values packed in the ith bin], and

(ii) for every j = 1, ... ,r, 2:aETJ qa :::; 1 + c.

第三步：使用上述h-dual PTAS来设计一个针对普遍BIN-P的h-dual PTAS算法

在上一步的基础上，把那些小于$\epsilon$的q值逐一塞进已经填装的包裹中，如若不够就再加一个包，重复至完成

#### 5.随机算法

###### 5.2 随机算法的分类与设计范式

对于算法$A$，输入$x$的所有随机运行中使用的最大随机比特数记作$Random_A(x)$，对于任意的输入大小$n$，记$Random_A(n)=\max \{Random_A(x)|x\text{ is an input of size }n\}$.

上述量的意义：

(1)伪随机的代价

(2)去随机化的代价

对于输入$x$，算法$A$输出$y$的概率，记作$Prob(A(x)=y)$，大小上等于所有$Prob_{A,x(C)}$的和，其中$C$是输出$y$的运行.
$$
Exp\text{-}Time_A(x)=E[Time]=\sum_{C}Prob_{A,x(C)}\cdot Time(C)
$$

$$
Exp\text{-}Time_A(n)=\max\{Exp-Time_A(x)|x\text{ is an input of size }n\}
$$

$$
Time_A(x)=\max\{Time(C)|C\text{ is a run of }A\text{ on }x\}
$$

$$
Time_A(n)=\max\{Time_A(x)|x\text{ is an input of size }n\}
$$

随机算法运行无穷的运行，我们总能限制一个时间让循环终止。

**LAS VEGAS ALGORITHMS**

Common point 从不输出错误解

定义1：

$Prob(A(x) = F(x)) = 1$ 

$e.x.$ 随机快排

复杂度一般讨论期望

定义2：

$Prob(A(x) = F(x)) \ge 1/2$

复杂度一般讨论最差情况

**one-way (communication) protocol**

**ONE-SIDED-ERROR MONTE CARLO ALGORITHMS**

(i) $\text{for every }x\in L, Prob(A(x) = 1) \ge 1/2, \text{and}$  

(ii) $\text{for every }x\not\in L, Prob(A(x) = 0) = 1$ 

这种随机算法只能用来考虑决定问题(yes/no)

**TWO-SIDED-ERROR MONTE CARLO ALGORITHMS**

$Prob(A(x) = F(x)) \ge\frac{1}{2} + \epsilon$

策略就是让算法对给定的输入运行$t$次，然后选择那个出现频数大于$t/2$的输入作为结果

**UNBOUNDED-ERROR MONTE CARLO ALGORITHMS**

$Prob(A(x) = F(x))>\frac{1}{2}$

**RANDOMIZED OPTIMIZATION ALGORITHMS**

第一种需要实现近似比不超过$\delta$的概率至少1/2

第二种需要实现近似比的期望不超过$\delta$

**随机算法设计的范式**

FOILING AN ADVERSARY.

ABUNDANCE OF WITNESSES.

FINGERPRINTING. 

RANDOM SAMPLING.

RELAXATION AND RANDOM ROUNDING. 
