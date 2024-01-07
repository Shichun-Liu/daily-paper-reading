---
tags:
  - DPO
  - RLHF
date: 2023-12-17
---
[GitHub - liziniu/policy\_optimization](https://github.com/liziniu/policy_optimization)
[[2312.10584] Policy Optimization in RLHF: The Impact of Out-of-preference Data](https://arxiv.org/abs/2312.10584)
[DPO替代RLHF可造成多一倍的性能损失 - 知乎](https://zhuanlan.zhihu.com/p/673047773)
[DPO注解 - 知乎](https://zhuanlan.zhihu.com/p/672293068)
[20231220.md](https://github.com/LLaMafia/llamafia.github/blob/main/Log/20231220.md)


![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231223220615.png)

- 我们发现具有较多**偏好外数据**的策略优化，通过利用RM的**泛化能力**显著提高了性能；In particular, even when providing the policy model with a **good feature representation**, we find that policy optimization with adequate out-of-preference data significantly improves performance by harnessing the reward model's generalization capabilities.
- 比较**直接偏好优化 (DPO) 和基于奖励模型的策略优化 (RMB-PO)**。还考虑了 RMB-PO 的一种变体，称为 RMB-PO+；

A1: DPO替代RLHF可造成多一倍的性能损失，用dpo泛化能力会较rlhf弱得明显,Li.etc 表示 因为DPO 偷懒了，用empirical data distribution 替代了真实的distribution，

A2: 可以看看statistical rejection sampling，有类似结论, gap没有在optimal policy采样，也没有reward model做泛化，不能explicit知道在optimal policy下generate samples谁好谁坏.

A3: 还是RL的经典的state distribution shift问题

A4: 搞个10次rej sampling + DPO估计也能打平

A5: PPO就是调参有挑战

A6: offline distribution跟optimal policy distribution的domain shift，Xiong, etc ”Gibbs Sampling from Human Feedback“ 论文的Sec 6有详细讨论DPO和RSO。

A7: DPO的训练方式还是会存在training-test gap，即训练时teacher-forcing的方式与实际测试有差异。这点就不如基于PPO的RLHF。