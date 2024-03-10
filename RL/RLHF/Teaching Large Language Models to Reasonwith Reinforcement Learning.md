---
tags:
  - reasonging
  - RL
date: 2024-03-08
---
- meta, UCB;
- Inspired by the success of RLHF, we study the performance of multiple algorithms that learn from feedback (**Expert Iteration, Proximal Policy Optimization (PPO), Return-Conditioned RL**) on improving LLM reasoning capabilities. 
- 所有算法的性能相当，其中**专家迭代**在大多数情况下表现最佳;
- 专家迭代的样本复杂度与 PPO 相似，最多需要 106 个样本才能收敛;
	- 原因在于在 RL 训练过程中，模型无法探索远远超出 SFT 模型已经产生的解决方案的范围；
- 讨论了我们的研究结果对 RLHF 的影响以及 RL 在 LLM 微调中的未来作用。

## Intro
- 第一段引文非常全面，可以借用；
- 