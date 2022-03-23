[TOC]

##### 1 导论

###### 1.1 强化学习

强化学习问题：

- 形式化细节见第3章
- **基本思想**：就是在智能体为了实现目标而不断与环境产生交互过程中，抓住智能体所面对的真实问题的主要方面.
- 马尔可夫决策过程

**有监督学习vs无监督学习**：监督的含义是指数据有无标注

**强化学习vs有监督学习**：有监督学习反映的是一种推断、泛化的学习方式；而强化学习反映的是一种从交互中学习的方式，在一个未知领域，从自身的经验中学习是必要的

**强化学习vs无监督学习**：无监督学习的目的是寻找隐含结构；而强化学习的目的是最大化收益

强化学习的关键特征：

- “试探”与“开发”之间的折中权衡
- 明确考虑了目标导向的智能体与不确定的环境交互这整个问题
- 与其他工程和科学学科之间实质性的、富有成果的互动

###### 1.2 示例

国际象棋

自适应石油控制器

羚羊幼崽学会奔跑

...

关键词：交互，智能体的动作会影响未来环境的状态，无法完全预测动作的影响

###### 1.3 强化学习要素

智能体、环境、**策略**、**收益信号**、**价值函数**以及（可选）**对环境建立的模型**

**策略**定义了学习智能体在特定时间的行为方式，是环境状态到动作的映射.它对应于心理学中被称为“刺激-反应”的规则或关联关系.可能是简单的函数或查询表，也可能是复杂的搜索.一般来说，策略可能是环境所在状态和智能体所采取的动作的随机函数.

**收益信号**定义了强化学习问题中的目标.在生物系统中，收益与痛苦或愉悦的体验类似.

收益信号表明了在短时间内什么是好的，而**价值函数**则表示了从长远的角度看什么是好的.简单地说，一个状态的价值是一个智能体从这个状态开始，对将来累积的总收益的期望.

我们寻求能带来最高价值而不是最高收益的状态的动作，因为这些动作从长远来看会为我们带来最大的累积收益.不幸的是，确定价值要比确定收益难得多.收益基本上是由环境直接给予的，但是价值必须综合评估，并根据**智能体在整个过程中观察到的收益序列**重新评估.事实上，价值评估方法才是几乎所有强化学习算法中最重要的组成部分.

**对环境建立的模型**，是一种对环境的反应模式的模拟，或者更一般地说，它允许对外部环境地行为进行推断.环境模型会被用来做**规划**.规划，就是在真正经历之前，先考虑未来可能发生的各种情境从而预先决定采取何种动作.使用环境模型和规划来解决强化学习问题的方法被称为**有模型**的方法.

###### 1.4 局限性与适用范围

本书的关注点在于根据给定状态信号来决定采取什么动作

本书中讨论的大多数强化学习方法必须建立在对价值函数的估计上.但是这并不是解决强化学习问题的必由之路.比如，一些优化方法，遗传算法、遗传规划、模拟退火算法以及其他一些方法，都可以用来解决强化学习问题，而不用显示地计算价值函数，这些方法采取大量静态策略.我们称其**进化方法**，采用这类方法的场景一般为，策略空间充分小、可以很好地结构化以找到好的策略，有充分的时间来搜索，智能体不能精确感知环境状态.

###### 1.5 扩展实例：井字棋

经典算法：

- “极大极小”算法
- 动态规划
- 进化方法

使用价值函数的方法.**状态的价值定义为当前状态取胜的概率**.假设我们下的是X，那么在所有有三个连续的X的状态下获胜的概率是1，同样，当出现有三个连续的O的状态，或者说是棋盘已经被下满了时，获胜概率是0.所有其他状态的初始价值为0.5，表示我们猜测会有50%的获胜机会.

大多数时候，我们贪心地行动，选择价值最高的动作，也就是说，选择获胜概率最高的动作.然而，偶尔我们会从其他动作中随机选择.这些被称为试探性走棋行动.

在博弈的过程中，我们会不断改变自身所处的状态的价值.为了做到这一点，我们将在贪心动作之后得到的状态所对应的价值“回溯更新”到动作之前的状态上.更准确地说，我们是对早先的状态的价值进行调整，使其更接近于后面的状态所对应的价值.这可以通过将早先的状态的价值向后面的状态的价值方向移动一个增量来完成.更新过程可以写成，
$$
V(S_t)\gets V(S_t)+\alpha [V(S_{t+1})-V(S_t)]
$$
其中，$\alpha$是一个小的正分数，称为步长参数，其会影响学习速率.这个更新规则是**时序差分**的一个例子.

上述方法在这个任务上表现得很好，它会收敛于最优策略下每个状态下得真正获胜概率.如果步长参数没有随时间得推移降低到零，那么这个玩家也能很好地对抗缓慢改变他们策略的对手.

**进化方法vs基于价值函数的方法**：进化方法每一次策略的改变都基于很多次博弈，只有每局比赛最后的结果会被考虑，而在博弈中间发生的事情将会被忽略.相反，基于价值函数的方法允许我们对单个状态进行评估.

虽然井字棋是双人博弈，但强化学习其实也适用于没有外部对手的情况，可以认为是“与自然博弈”.

强化学习不局限于井字棋这类问题，它也在动作空间无限连续，适用于不同幅度的收益可以实时获取的情况，它也适用于不能分解成如井字棋这样为离散时间步长的问题,也就是适用于连续时间问题.

结合强化学习与人工神经网络学习，可以处理状态集很大，甚至无穷大的问题.神经网络为程序提供了从其经验中进行归纳的能力.一个强化学习系统在有如此大的状态集的问题中能起到多大的作用，与它从过去的经验中进行归纳总结推广的能力密切相关.从这个角度说，**在强化学习中融入有监督学习**就变得尤为重要.

最后，井字棋玩家可以知道施加每个动作之后可能产生的后续状态.为了做到这一点，它必须有一个**对环境建立的模型**.但也有些问题没有这样的模型，甚至连动作可以产生的短期效果都不知道.让人欣慰的是，强化学习可以应用在任何情况下，模型不是必须的.