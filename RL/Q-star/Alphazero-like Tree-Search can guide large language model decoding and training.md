---
tags:
  - MCTS
  - decision
date: 2023-09-29
---
[LLM的快思考与慢思考路线之MCTS - 知乎](https://zhuanlan.zhihu.com/p/659230417)
[EBM-based Global Rank+MCTS for COT - 知乎](https://zhuanlan.zhihu.com/p/650438958)
[[2309.17179] Alphazero-like Tree-Search can Guide Large Language Model Decoding and Training](https://arxiv.org/abs/2309.17179)


当我们将LLM与MCTS结合时，赋予LLM慢思考的能力，一般需要解决以下问题：
1. 高效的rollout(采样)
2. evaluation-function
3. node-representation & edge-representation

## 摘要
- 提出了基于Alphazero-like Tree-Search的LLM学习框架TS-LLM（MCTS+value function）；
- 学习到的value-function还可以用于推理之外的**其他任务**，如RLHF对齐；
- guide大模型在推理和训练的编码过程；
- 通过推理、规划和RLHF对齐任务的实证评估验证了TS-LLM的有效性，即使是在深度为64的树上（远超ToT和RAP的深度）。

## intro
- MCTS在解决大规模MDP的搜索问题上取得了显著成果（AlphaZero算法）；
- 初步工作为ToT+DFS，Reasoning-via-Planing(RAP)+MCTS（[[2305.14992] Reasoning with Language Model is Planning with World Model](https://arxiv.org/abs/2305.14992)），They successfully demonstrated a performance boost of searching on trees expanded by LLM through **self-evaluation**.
- 目前工作局限性：1. 只能处理推理任务；2. reward function/value function 通过对于LLM在step-level/trajectory-level的提示来获得；因此这些算法缺乏可用性、需要一个强的模型给出可靠的reward/value estimation；

TS-LLM主要贡献
- 提供了一个通用且可扩展的pipeline：1. 可以解决多种任务；2. With a **learned value function**, TS-LLM can be applied to LLMs of any size.
- 除了推理之外，可以作为新的LLM训练范式：将树搜索操作作为policy improvement算子；交替进行PI和PE进行训练。
- 提供了**新的评估指标**；

## 方法
**主要内容**
- 从具有不同树/节点设计的问题公式开始，说明了如何将 LLM 的生成框定为树搜索管道。
- 其次，我们展示了树搜索如何帮助 LLM 推理，包括特定树搜索算法的选择、价值函数训练技术和搜索聚合方法。
- 基于树搜索增强推理，我们介绍了树搜索如何作为指导 LLM 训练的通用policy improvement算子。
- 最后，我们讨论了树搜索算法的计算负担，以便进行公平的比较。

### 问题公式
- 把LLM生成过程建模为MDP，在大多数情况下这都是一个稀疏奖励的情景；
- 本文聚焦于如何使用树搜索算法优化这个生成过程；
- 状态空间（通过语言）和奖励函数（描述目标或度量）一般而言是预先定义好的，但是动作节点（action node）的设计存在不同的方案：
	- sentence-level action node：对于具有步骤、句子级结构的任务如CoT，很自然地将每个步骤作为一个action node；如ToT；拓展时，对于每个非叶节点，算法采样m个可能的后续节点并去重；
	- token-level action node：将每一个token视为离散动作；适用于没有明确定义中间步骤的任务（如RLHF对齐）；

![image.png|625](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102123828.png)

- 前者可以生成一个较浅的树，但是枚举所有可能的句子是不可能的，必须在expansion过程中subsample *k* nodes，类似Sampled MuZero的思想；同时在搜索空间和LLM的生成空间中造成一定gap（具体大小与k相关）；
- 后者会带来较大的计算负担，大大增加了树的深度，使得搜索较为困难。

### 树搜索推理
好处是通过搜索来累计奖励，而无需进行梯度更新计算；可以在不进行SFT的情况下增强LLM的生成；
#### value function 的学习
- 通常在大模型中reward model $\hat{r}$ 和 value function $v$ 是主要问题；ToT和RAP是通过强模型（GPT4）来给出相关信号；
- 奖励模型：使用 **ORM**，为了追求任务的通用性；
- value function：共享reward model 和 value function，其结构是具有 MLP 的仅解码器转换器，用于在输入token的每个位置输出一个标量。对于句子级扩展中间步骤$s_t$ ，我们使用最后一个标记处的预测标量作为其值预测 $v_θ (s_t)$ 。在将完整句子 $(x_{0:L−1}, y_{0:T-1})$ 输入模型时，可以在最后一个token中获得最终奖励。
- 对于给定的句子级的reward信号，使用TD-λ或者MC方法来训练评估中间步骤（token-level）的value function；对于ORM同样如此处理。

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102120632.png)

#### 树搜索算法
- **MCTS-α（AlphaZero）**，4步骤，特征是一旦采取动作就无法回溯到之前的状态，利用value function可以在中间步骤直接进行反向传播，没有进行真实模拟（MC估计）；
- **MCTS**：经典版本，4步骤，RAP采用了这个版本，值依赖于MC模拟（快速走子网络）；总是从根节点开始搜索；
- **MCTS-Rollout**：结合以上两种算法提出的新算法；每次都从根节点开始搜索，模拟过程与MCTS-α类似，backup过程用value function发生于中间步骤（具体细节还需要仔细看看）；重复上述操作，直到找个k个完整答案或者达到计算限制（回答最大token数）；
- **BFS/DFS**：ToT的选择，利用value function对树进行剪枝，进行高效搜索；BFS 可以看作是以累积奖励为目标的**波束搜索**。在 BFS 中，在每一步中维护和扩展具有 k 个最大值的节点。DFS 选择具有最高值的未访问节点，直到它找到完整的路径。为了提高搜索效率，DFS 通过**删除值低于预定义阈值**的节点或丢弃 n 最低值的节点来修剪树。

**多次搜索与聚合**
通过多次采样和聚合候选对象来提高其在推理任务上的性能，TS-LLM还具有聚合多个树搜索生成的答案的潜力。主要有两种选择：
- 树内搜索：对于完全相同的树多次搜索，多样性可能降低，前一次搜索可能影响后一次搜索，结果相对固定；
- 树间搜索：每一次搜索构造新的树，增加计算量和时间，好处是多样性增加；
聚合策略：多数投票；ORM-max；ORM-vote；

### 树搜索训练
通过微调三个网络：policy network，value network，ORM实现。

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102123002.png)

## 实验

![image.png|675](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102124008.png)

步骤划分：按照输出序列中的'\n'来划分；GSM8K和PrOntoQA每个节点最多6个action；Game24最多20个action；
所有的LLM通过SFT以获得0-shot能力；
