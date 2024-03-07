---
tags:
  - reasonging
  - RL
date: 2023-06-09
---
- 树搜索的重要组成部分是对于每个node的evaluation function；
- 通过object-level的特征，来选择某个node进行拓展；
- We introduce a **reward function** for this problem based on **value of computation estimates** with respect to improving the policy for the underlying problem.
- 在程序生成任务上有所改善；

## Intro
- 树搜索，MCTS在planning领域的简介、作用；
	- 现有方法：扩展给定评估函数的子节点，从起始状态到目标状态构建搜索树。
	- 在选择每个子节点进行扩展时，启发式搜索使用到目标状态的距离估计（Hansen 和 Zhou 2007）；
	- 蒙特卡洛树搜索使用平衡探索和开发的度量（Browne，Powley 等人，2012 年）。
- 以上算法依赖于反映每个子节点可取性的评估函数。**元推理**方法，要求明确地推理如何构建搜索树（metareasoning approaches that explicitly reason about how to build a search tree）。
- mete-level decision problem的一个方向是 **meta-level policy**，从state映射到action；在树搜索场景中，state为搜索树，action为node expansion；
- 要找的最优的mete-level policy需要计算时间相关的utility，即需要计算state和action在所有轨迹下的期望，这实际上不可行；作为代替方案的近似计算策略只能在简单场景适用，难以拓展到具有连续state space和action space的复杂场景；
- 本文：通过Deep RL优化树搜索的node expansion selection 来最大化reward function；引入一个meta-level decision problem，使用函数$\hat Q^*(s,a)$ 估计给定s和a的最优 **object-level** Q-value；
- 
- 