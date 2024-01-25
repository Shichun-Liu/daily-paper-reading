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

## 论文
### Method
- 过程格式：natural language CoT(**N-CoT**) (Wei et al., 2022) (Figure 1) and programbased CoT (Gao et al., 2023) (**P-CoT**) using Python；
- two stages: the **warm-up** stage and the **reinforcement learning** stage；
**warm-up**
- In this stage, the policy is fine-tuned for a few epochs on a dataset comprising of the "**(question, CoT)**" tuples: (x, e).
**Reinforcement Learning** 
- In this stage, the policy improves its performance via a form of online self-learning using a dataset comprising of **(question, answer)** tuples: (x, y).
- We employ **PPO with a clipped objective algorithm** for training. Following Ziegler , the value model Vφ is constructed by appending a linear value head on top of the last hidden states of the policy model πθ, which is the model after the warm-up stage.
- On dataset whose **answers are all numeric**, **partial reward** of 0.1 can be applied when the answer can be extracted and of numeric type. For 1 ≤ t ≤ L

![image.png|325](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240122144957.png)

- Such a partial reward can help **reduce the effect of learning from sparse reward**；

### Experiments
- dataset：GSM8K，SVAMP，MathQA；
- 过程数据标注：We perform few-shot prompting (Wei et al., 2022; Gao et al., 2023) using **GPT-3.5-turbo** to obtain both the**N-CoT and P-CoT annotations**.
- We conduct experiments with two foundation models: **Galactica-6.7B** and **Codellama-7B**. 以下为baseline性能；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240122161203.png)

经过RL之后性能与SFT性能对比；

![image.png|323](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240122162117.png)

- 训练了一个RM来确定 CoT 的正确性；来预测指示“正确”或“不正确”解决方案的二进制标签。在 N-CoT 和 P-CoT 中的所有数据集上分别使用 CodeLLAMA 实现了 3.7 分和 5.9 分的改进；ReFT 中没有使用额外的注释或奖励模型；
- 这种比较表明，“**探索**”在 ReFT 中具有良好的性能至关重要。该结果表明，**不正确的实例**对于指导模型进行更好的探索也非常重要。与自我训练的比较还表明，所提出的方法与策略上采样和强化学习比标准数据增强方法更好。
#### Majority Voting and Reranking Benefit ReFT 
我们从 SFT 和 ReFT 策略中执行采样。我们对每个问题采样 100 个 CoT 解决方案并应用第 4.3 节中描述的奖励模型。

![image.png|372](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240122163419.png)

ReFT + Voting 在所有设置中平均显着优于 SFT + Voting 9.2 分，重新排序的 ReFT 平均优于重新排序 3.3 个点的 SFT；
