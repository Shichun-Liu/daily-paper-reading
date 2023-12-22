---
tags:
  - DPO
date: 2023-05-30
---
**资料**
- [Site Unreachable](https://www.youtube.com/watch?v=pzh2oc6shic)
- [DPO——RLHF 的替代之《Direct Preference Optimization: Your Language Model is Secretly a Reward Model》论文阅读 - 知乎](https://zhuanlan.zhihu.com/p/634705904)

- 绕过对于reward的估计以及RL，直接**在LM上**对于偏好进行学习；
- DPO更直接，但是对于性能的损失大；RL的泛化性更好，但是成本更高；
- 核心思想是分析从 reward function 到 optimal policy 的映射，便于迁移loss function ；因此可以跳过显式建立reward model 的流程，而是直接对于现有模型在偏好数据上进行优化；

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231222004946.png)


![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231222005251.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231222005500.png)

