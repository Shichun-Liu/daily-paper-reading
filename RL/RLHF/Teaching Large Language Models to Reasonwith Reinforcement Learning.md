---
tags:
  - reasonging
  - RL
date: 2024-03-08
---
- meta, UCB;
- Inspired by the success of RLHF, we study the performance of multiple algorithms that learn from feedback (**Expert Iteration, Proximal Policy Optimization (PPO), Return-Conditioned RL**) on improving LLM reasoning capabilities. 
- 所有算法的性能相当，其中 **专家迭代(EI)** 在大多数情况下表现最佳;
- 专家迭代的样本复杂度与 PPO 相似，最多需要 106 个样本才能收敛;
	- 原因在于在 RL 训练过程中，模型无法探索远远超出 SFT 模型已经产生的解决方案的范围；
- 讨论了我们的研究结果对 RLHF 的影响以及 RL 在 LLM 微调中的未来作用。
- 主旨：由于任务、预训练数据、监督微调数据、使用的 RL 算法和奖励来源的巨大差异，仍然不清楚哪些因素在 RL 微调过程中产生最大影响。我们的工作对**所有这些因素进行了彻底的分析**，以准确了解不同算法在提高 LLM 推理能力时的比较情况。

## Intro
- 第一段引文非常全面，可以借用；
- 四种指标：1次采样maj@1，96次+投票maj@96，96次+Reward Model选择 rerank@96，96次+最接近真实答案 pass@96；
- EI水平最佳，接近PPO的样本利用率，只需要a few thousand samples完成收敛；
- 经过RL微调之后，pretrain模型和SFT性能缩小，且模型越大缩小越大；
- 增益来自于数据多样性，RL模型在训练过程中的采样增加了更多数据；
- 解释，为什么EI以及return conditioned RL与PPO可比：1. 现有的推理任务有完全确定型的env，PPO更适用于高度随机性的任务；2. 模型在RL微调过程中探索程度不足，因此样本多样性没有饱和，PPO没有发挥出样本复杂性的优势；


## Method
### Expert iteration 
- online，off-policy；
- 在进行policy improvement之前，从初始策略出发，在训练集每个问题上重复采样K次，得到近似的专家策略 $\hat \pi^*_0$ ；这个过程中采样得到了rollouts 数据集$D_1$ ，然后通过交叉熵loss训练出策略 $\pi_1$ ；这个过程的迭代得到了系列数据集 $D_i=R_i\cup D_{i-1}$ （$R_i$代表策略$\pi_{i-1}$贡献的rollout），微调得到专家策略$\pi_i$；
- 