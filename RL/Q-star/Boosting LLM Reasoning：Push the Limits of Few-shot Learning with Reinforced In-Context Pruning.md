---
tags:
  - reasonging
  - RL
date: 2023-12-26
---
**CoT-Influx** addresses the challenges of the **selection of useful examples** and **limited number of examples** due to restricted context window length.
提出了一个从粗到细的剪枝器(coarse-to-fine pruner)作为 LLM 的即插即用模块，该模块首先尽可能识别尽可能多的关键 CoT 示例，然后在上下文窗口中进一步修剪不重要的标记；
为了训练修剪器，我们收集了一个具有不同难度和步骤的数学推理数据集，引入了一个奖励来衡量输入对数学推理和标记长度约束的有效性，并提出了一种新的强化学习训练方法。

**任务**：数学推理
**数据集**：使用 GPT-4 (OpenAI, 2023) 和 Evol-Instruct (Xu et al., 2023) 来创建一个名为 **MRD3** 的数学推理数据集；
## intro
- 核心问题：what's the **upper limit** of LLM performance gain in math reasoning achievable through inputting as many **few-shot CoTs** as possible?
- 约束条件：LLM有限的上下文窗口大小；提示压缩在数学问题上表现不佳；选择有用的CoT示例并不容易（基于启发式和检索的方法不是最优）；
- CoT-Influx 的动机是观察到由于自然语言输入中示例和token-level的冗余，当前的 LLM 上下文窗口尚未得到充分利用。因此，这些冗余输入可以被修剪以释放空间以获得更多信息的上下文。CoT-Influx 的核心思想是输入长长的 CoT 示例，识别目标 LLM 的关键示例，然后修剪冗余标记以适应原始 LLM 上下文窗口。因此，通过输入更有帮助的 CoT 示例，每个示例仅由信息性标记和较短的长度组成，我们大大提高了 LLM 解决数学问题的能力。

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240112162457.png)

- 由于两个因素，训练修剪器带来了挑战：（1）由于我们在 LLM 分词器之前识别离散标记，LLM 损失梯度不能通过分词器反向传播以更新修剪器； (2) 许多数学问题的高度困难，无论压缩的少样本示例的质量如何，始终产生不正确的答案，这对我们的修剪器模块的有效训练提出了挑战。为此，我们引入了一种新的**强化学习**训练方法来缓解梯度问题。我们设计了一个奖励函数来衡量数学推理的少样本有效性；Then, we design a **difficulty-aware dataloader** that filtering appropriate problems and employ REINFORCE (Williams, 1992) to maximize the reward.
