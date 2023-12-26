---
tags:
  - MCTS
  - PPO
date: 2023-10-18
---
- PPO-MCTS；
- 对策略网络的文本进行编码是不丢弃value-network；在推理生成过程中，将value-function与policy network相结合；
- 与其他基于MCTS的受控文本生成的方法相比，该方法减少训练和测试之间部分输出的评分机制的不匹配；
- 实验：在4个文本生成任务上，PPO-MCTS极大提高了生成文本的偏好性；搜索算法具有较好前景。

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226182255.png)

