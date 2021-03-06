[TOC]

## Relaxing Relationship Queries on Graph Data

##### Author: Shuxin Li, Gong Cheng, Chengkai Li

选择直径为参数划分，这是一个减小问题空间的通用方法

解决的问题是：现有的算法在查询实体很远时，会返回空结果，而新的方法可以减少失败的发生并且提供替代结果

怎么解决的：于从原始失败查询中删除最少数量的实体，以形成最⼤成功的子查询，从⽽最⼤限度地减少因松弛造成的结果质量损失

结果是什么：我们提出了一种最佳优先搜索算法，一种基于度数，另一种基于距离

该算法的意义在于，有现实效益，但是带来的后果是会频繁出现查询失败

平衡搜索性能与搜索质量

一种直接的方法是把约束扩大，表现不是很好

令一种方法是将原语义搜索的范围松弛，然后返回一个替代结果

Steiner tree

最小斯坦纳树允许在给定点外增加额外的点，使生成的最短网络开销最小

与现有的斯坦纳树算法的区别

第一个直观的方法：按顺序遍历子查询，对每个子查询使用现有的SA算法

第二个直观的方法：计算每对查询实体之间的距离，判定的条件，每对查询实体之间的距离不大于直径约束，这是必要条件，但不够充分

基本想法：对于D是偶数...

对于D是奇数...

算法优化

相比于原算法对整张图中每个点至多遍历一次，新算法独立地跑|Q|次

看起来慢了，实则不然

## Magnetic control of tokamak plasmas through deep reinforcement learning

关键词：

- Tokamak 托卡马克(一种环状大电流的箍缩等离子体实验装置)

Insight:

- 深度强化学习
- 控制问题

## 资源受限边缘计算系统中的自适应联合学习-Adaptive Federated Learning in Resource Constrained Edge Computing Systems



## NetView: Towards on-demand network-wide telemetry in the data center

difficulty:

- On-demand telemetry 实时该边的遥感需求
- Coverage 覆盖范围
- Scalability 可扩展的（可以扩大的）
