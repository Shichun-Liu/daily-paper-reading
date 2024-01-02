---
tags:
  - MCTS
  - PPO
date: 2023-10-18
---
> [!abstract]
> 
提出一种新型值导向解码算法PPO-MCTS，在基于强化学习的自然语言生成模型中集成 MCTS 算法，通过紧密结合值网络和策略网络，显著提高生成文本的可接受性。

[[2309.15028] Don't throw away your value model! Making PPO even better via Value-Guided Monte-Carlo Tree Search decoding](https://arxiv.org/abs/2309.15028)
[LLM的快思考与慢思考路线之MCTS - 知乎](https://zhuanlan.zhihu.com/p/659230417)
[爱可可 AI 前沿推介(10.1) - 知乎](https://zhuanlan.zhihu.com/p/659193746)
## 主要内容
- 使用value-function来估计中间节点的reward而不需要rollout；
- 使用state value来近似action value；默认所有action是均匀分布的；
- 当进行backpropagation时，通过以下方式更新state-action-value-function(state-action-value-function的标准更新方法):

![image.png|525](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102143415.png)


- **方法**：对策略网络的文本进行编码时不丢弃value-network；在推理生成过程中，将value-function与policy network相结合；
- **优势**：与其他基于MCTS的受控文本生成的方法相比，该方法减少训练和测试之间部分输出的**评分机制**的不匹配；从而显著改善生成文本的可接受性。在4个文本生成任务上，PPO-MCTS极大提高了生成文本的偏好性；搜索算法具有较好前景。

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226182255.png)

左：来自 PPO 策略的贪婪解码不满足任务约束。右图：具有相同 PPO 策略和附加值模型的 PPO-MCTS 满足任务约束。P 是 PPO 策略模型给出的先验概率； V 和 Q 是从 PPO 值模型的输出导出的。（相同部分是解码第一个token。通过重复这个过程来解码完整的序列。）

**要点**
- PPO算法同时训练策略模型和价值模型。通常只使用策略模型进行解码，而丢弃价值模型。
- 价值模型实际上捕获了对部分文本序列进行评估的有用信息，专门用于预测不完整输出的价值/回报。
- 本文提出使用PPO价值模型来引导文本解码，具体是在蒙特卡洛树搜索(MCTS)中将其用作评估函数，该方法称为PPO-MCTS。
- PPO-MCTS允许生成比仅使用PPO策略模型更高质量的文本，支持在解码时进行前瞻以满足约束和目标。
- 关键的创新之处在于**使用PPO价值模型**而不是**启发式规则或独立模型**，来引导解码，价值模型非常适合作为MCTS的评估函数。
- 在情感控制、毒性降低、常识问答和人工偏好微调任务上的实验表明，PPO-MCTS相比直接PPO策略解码提高了结果。消融实验证明了使用价值模型而不是其他替代方案(如奖励模型)以及使用MCTS而不是更简单的解码方案的必要性和优势。结果展示了PPO价值模型在推理时的有用性。应该保存和使用价值模型以启用增强的解码。

