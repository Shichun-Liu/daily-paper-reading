---
tags:
  - MCTS
  - decision
---
[[2305.14078] Large Language Models as Commonsense Knowledge for Large-Scale Task Planning](https://arxiv.org/abs/2305.14078)
[LLM的快思考与慢思考路线之MCTS - 知乎](https://zhuanlan.zhihu.com/p/659230417)

## 摘要
自然语言为人类交流提供了一个自然的界面，但由于其抽象性质和固有的模糊性，机器人很难理解。大型语言模型 (LLM) 包含**常识知识**，可以帮助解决语言歧义并生成抽象规范的可能解决方案。虽然 LLM 已显示出作为少样本规划策略的前景，但它们规划复杂任务的潜力并未完全挖掘。本文表明 LLM 可以**用作世界的常识模型和搜索算法中的启发式策略**，例如蒙特卡洛树搜索 (MCTS)。MCTS 探索了从 LLM 中采样的可能世界状态，以促进更好的决策。LLM 的常识策略引导搜索树的相关部分，大大降低了搜索的复杂性。我们展示了我们的方法在日常任务规划实验中的有效性，并强调了它优于仅使用 LLM 作为策略的优势。

![image.png|700](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102203546.png)

该方法的核心思路如上图所示。该文章主要处理language-instructed object rearrangement tasks。核心在于:

1. state-representation。该文章主要解决 object rearrangement，其状态包括 object、robot-position、moveable-items和moveable-containers。
2. action-representation。object picking, object placing, moving, opening a container, and closing a container。
3. MCTS的node和edge。将objects建模为node，将物体之间的关系建模为edge。
4. For each **simulation** in the MCTS algorithm, we sample from the **commonsense belief** to obtain an initial state of the world and use the LLM as **heuristics** to guide the trajectory to promising parts of the search tree.

该文章将MCTS与ChatGPT结合，MCTS的使用较为常规。但在MCTS的加持下，ChatGPT具备了更强的任务规划能力，相比ChatGPT-policy，提升了10个点以上。

### reward 信号
利用LLM给出的常识获得一个状态的belief；
- A **commonsense prior belief** of states can improve the effectiveness of object and location searches by prioritizing the search to appropriate locations. Our approach utilizes LLM's commonsense knowledge to generate the initial belief of states, which is updated with each action and observation in the real world. MCTS **samples from the belief in simulation** to estimate the value of the action.
- belief表示出来就是一个映射表，一个物品最有可能出现在什么位置，比方说苹果出现在厨房；
- 完成goal可以得到一个较大的reward；
- 由于是机器人任务，所以action是固定的，这里会用一个sentence-BERT来完成自然语言的嵌入与动作匹配；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102205812.png)

- 对于每个模拟，MCTS 从根的信念 b(s) 中采样一个状态（第 4 行）。它独立地为每个对象采样一个位置以构建状态 s。然后在模拟中使用这种采样状态 s，生成新的树轨迹。
- **选择**：根据Q值、访问计数和LLM策略(第28行和第29行)，在模拟过程中选择一个动作a\*。观察和转换函数，表示为 G，在给定所选动作 a\* 和采样状态 s 的情况下预测下一个状态 s'，从而进入模拟的后续步骤（第 30 节和第 31 行）。
- **模拟**：当遇到树中的叶节点时，MCTS 扩展树并为相应的节点执行随机rollout（第 23 到 26 行）。采用统一的策略对推出中的动作进行采样，然后返回折扣奖励（第 14 到 17 行）。
- **回溯**：在完成任务或达到最大深度后，backup累积奖励，更新每个节点的估计 Q 值（第 32 到 35 行）。在 N 次模拟之后，输出动作是根据估计的 Q 值确定的（第 3 到 8 行）。
- 在完成搜索过程后，代理将执行一个动作并接收一个新的观察结果。为简单起见，我们假设观察函数和转换函数是确定性的和已知的。在检测到对象的情况下，其信念中的相应位置将与观察到的位置更新。相反，如果对象在某些位置仍未被检测到，则关于其在这些位置中存在的信念设为0.