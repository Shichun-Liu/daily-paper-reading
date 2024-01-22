---
tags:
  - reasonging
  - RL
date: 2024-01-17
---
- 非常新；[GitHub - lqtrung1998/mwp\_ReFT](https://github.com/lqtrung1998/mwp_ReFT)；
- 在数学问题求解中，训练数据中通常只存在**一个**已标注的推理路径。从直觉上讲，对于一个问题，学习从**多个**已标注的推理路径中进行微调会更好。为了解决这个问题，我们提出了一个简单而有效的称为强化微调（ReFT）的方法，以增强LLMs为推理的学习能力。

![image.png|320](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240122113316.png)

- ReFT首先通过SFT热身模型，然后采用本文中的PPO算法进行在线强化学习。在问题提出时，会自动**生成大量的推理路径**，同时从**真实答案中自然生成奖励**。在GSM8K、MathQA和SVAMP数据集上进行的广泛实验证明，ReFT显著优于SFT，而且可以通过结合推理时间策略（如多数投票和重新排名）进一步提高性能。需要注意的是，ReFT通过从**相同的训练问题中学习**来获得改进，而没有依赖于额外的或增强的训练问题。这表明ReFT具有卓越的泛化能力。
