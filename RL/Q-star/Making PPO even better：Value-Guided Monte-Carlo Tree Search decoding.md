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
- 使用PPO的 value-function 来估计**中间节点**的reward而不需要rollout；

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102155139.png)

- 使用state value来近似action value；默认所有action是均匀分布的；
- 当进行backpropagation时，通过以下方式更新state-action-value-function(state-action-value-function的标准更新方法):

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102143415.png)

- **方法**：对策略网络的文本进行编码时不丢弃value-network；在推理生成过程中，将value-function与policy network相结合；
- **优势**：与其他基于MCTS的受控文本生成的方法相比，该方法减少训练和测试之间部分输出的**评分机制**的不匹配；从而显著改善生成文本的可接受性。在4个文本生成任务(情感转向，毒性降低，知识内省，HH)上，PPO-MCTS极大提高了生成文本的偏好性；搜索算法具有较好前景。

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226182255.png)

左：来自 PPO 策略的贪婪解码不满足任务约束。右图：具有相同 PPO 策略和附加值模型的 PPO-MCTS 满足任务约束。P 是 PPO 策略模型给出的先验概率； V 和 Q 是从 PPO 值模型的输出导出的。（相同部分是解码第一个token。通过重复这个过程来解码完整的序列。）

**要点**
- PPO算法同时训练策略模型和价值模型。通常只使用策略模型进行解码，而丢弃价值模型。
- 价值模型实际上捕获了对部分文本序列进行评估的有用信息，专门用于预测不完整输出的价值/回报。
- 本文提出使用PPO价值模型来引导文本解码，具体是在蒙特卡洛树搜索(MCTS)中将其用作评估函数，该方法称为PPO-MCTS。
- PPO-MCTS允许生成比仅使用PPO策略模型更高质量的文本，支持在解码时进行前瞻以满足约束和目标。
- 关键的创新之处在于**使用PPO价值模型**而不是**启发式规则或独立模型**，来引导解码，价值模型非常适合作为MCTS的评估函数。
- 在情感控制、毒性降低、常识问答和人工偏好微调任务上的实验表明，PPO-MCTS相比直接PPO策略解码提高了结果。消融实验证明了使用价值模型而不是其他替代方案(如奖励模型)以及使用MCTS而不是更简单的解码方案的必要性和优势。结果展示了PPO价值模型在推理时的有用性。应该保存和使用价值模型以启用增强的解码。

## 算法
为了成功结合PPO模型和MCTS解码，我们对原始的MCTS算法进行了一些新的修改，包括:(1)用q函数替换edge value $\bar{V}$ ，以确保与PPO训练的一致性;(2)从父节点的 $V$ 初始化子节点动作的 $Q$ ，以鼓励探索;(3)终端状态后禁止探索。当任务或实验设置不允许精确计算时，我们还提出了近似技术。
- **The goal of MCTS is to find high-reward output sequences**, using the policy model and evaluation function as guidance. The policy provides initial proposals on promising actions, and the evaluation function scores partial sequences proposed by the policy. We want to **evaluate more frequently partial sequences stemmed from more promising actions** so as to obtain more accurate evaluations for these actions (i.e., exploitation), while we also want to make sure to evaluate some less promising actions so as to **reduce the likelihood of missing out high-reward sequences (i.e., exploration)**. MCTS offers an **effective exploration and evaluation heuristic** based on tree search. 
- In PPO-MCTS, the MCTS decoding works with the policy and **value model trained by PPO**. To decode **each token**, MCTS builds a search tree while running a number S of simulations. In the search tree, each node represents a state $s$ and each edge represents an action $a$ . For each node s, we maintain two variables: a visit count $N (s)$ (which is eventually used to decode the token) and a mean value $\bar{V}(s)$; for each **edge** $(s, a)$ , we keep track of the **value** of its Q-function $Q(s, a)$ . Each simulation consists of four stages.
#### MCTS四步骤
1. 选择 action 

![image.png|675](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102160849.png)

- **replacing $\bar{V}$ with $Q$ .**  在经典的MCTS中reward只在最终步骤给出（稀疏奖励情况）而没有考虑折现因子，导致return=final reward；但是PPO给出的中间步骤reward还要加上对于KL散度的正则化项以及折现因子，因此上式考虑了这些因素（详见4，5，6式）。

2. 拓展
The selected node s∗ is expanded and marked as explored. The prior policy distribution pθ (·|s∗) is computed for this node, and actions with the **top-k priors** are each linked to a new, unexplored child node. The $\bar{V}$ of child nodes are zero-initialized.

3. 评估

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102161918.png)

使用PPO value model对于node节点进行值估计；使用V初始化Q而不是0初始化；

4. Backup
按照上述式子进行更新；更新顺序和之前一样是bottom-up；不同点在于V-bar的更新；
在进行到终止节点（可以通过一个状态检测函数来判断），禁止向后expansion，文本在生成[EOS] token时终止；

## 实验
- 在四个任务上表现较好；
- 消融1，使用reward model代替value function；使用value function的优势在于两个地方1. value model被训练处理部分序列（中间结果）而reward model通常而言并不是；2. value model是根据policy来定制的，而reward model通常是在PPO之前训练的；消融之后结果变弱；
- 是否需要MCTS？使用stepwise-value代替之后结果变差，但是没有试过beam search或者A\*搜索算法；
- 应用 MCTS 解码是一种**更正则化的方法**来获得更高奖励的生成。
- 推理时间开销上存在优化空间；

## 其他
文章后面提到了guided decoding中的sentence-level和token-level的不同论文，待阅读。