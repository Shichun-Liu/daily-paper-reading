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

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231223220615.png)

- 我们发现具有较多**偏好外数据**的策略优化，通过利用RM的**泛化能力**显著提高了性能；In particular, even when providing the policy model with a **good feature representation**, we find that policy optimization with adequate out-of-preference data significantly improves performance by harnessing the reward model's generalization capabilities.
- 比较**直接偏好优化 (DPO) 和基于奖励模型的策略优化 (RMB-PO)**。还考虑了 RMB-PO 的一种变体，称为 RMB-PO+；

