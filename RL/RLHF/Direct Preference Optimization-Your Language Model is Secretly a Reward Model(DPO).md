---
tags:
  - DPO
date: 2023-05-30
---
**资料**
- [Site Unreachable](https://www.youtube.com/watch?v=pzh2oc6shic)
- [DPO——RLHF 的替代之《Direct Preference Optimization: Your Language Model is Secretly a Reward Model》论文阅读 - 知乎](https://zhuanlan.zhihu.com/p/634705904)
- [SFT的训练技巧-DPO踩坑经验](https://www.xiaohongshu.com/explore/6585a7130000000009022151?app_platform=ios&app_version=8.18&share_from_user_hidden=true&type=normal&xhsshare=WeixinSession&appuid=6102330b000000000101c9c0&apptime=1703299203)

实践经验表明，如果SFT模型对于给定的任务（比如数学，代码，摘要），如果Pass@10效果显著好于Pass@1，那么一定可以用DPO训练Pass@1的效果，只要构造好的pair对。  
对应的，就有另外一个简单粗暴的判断，对于给定任务，如果SFT model在Pass@10与Pass@1的效果相当，不管是都非常差，还是都非常好，DPO基本都会失败（训练崩溃），导致效果显著降低，核心原因其实就是pariwise loss失效了。

## 主要内容

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231223121313.png)

- 绕过对于reward的估计以及RL，直接**在LM上**对于偏好进行学习；
- DPO更直接，但是性能损失大；RL的泛化性更好，但是成本更高；
- 核心思想是分析从 reward function 到 optimal policy 的映射，便于迁移loss function ；因此可以跳过显式建立reward model 的流程，而是直接对于现有模型在偏好数据上进行优化；

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231222004946.png)

- 与RLHF相同的优化目标；

![image.png|625](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231222005251.png)

- 文章使用交叉熵损失函数，直接对LLM在偏好数据上进行微调，跳过了RM的训练以及RL的方法；

![image.png|625](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231222005500.png)

## 阅读笔记
### Intro
- 本文展示了如何在不需要显式的奖励建模或强化学习的情况下，直接优化语言模型以符合人类偏好。我们提出了直接偏好优化（_Direct Preference Optimization_，DPO），这种算法隐式地优化与 RLHF 相同的目标（带有 KL 散度约束的奖励最大化），但实现简单且易于训练。
- DPO 增加了良好响应与不良响应的**相对对数概率**，但它结合了一个动态的、每个样本的重要性权重，用于防止在朴素概率比（naive probability ratio）目标下会发生的模型退化。类似现有的方法，DPO 也依赖一个理论上的偏好模型（比如 Bradley-Terry 模型）以衡量给定奖励函数和经验偏好数据间的一致性。
	- [[Preference Learning（Bradley–Terry, The Goodhart's Law, RLHF, ESPD）]]
- 现有方法使用偏好模型定义偏好损失来训练奖励模型，然后再训练一个用于优化所学奖励模型的策略，而 DPO 则是使用变量的变化**把偏好损失直接定义为策略函数**。因此给定一个人类对模型响应的偏好数据集，DPO 可以使用简单的二元交叉熵目标来优化策略，而无需显示地学习奖励函数或在训练时对策略进行采样。

### 相关工作
- RLHF，RLAIF的应用；PPO算法；
- 基于偏好/排序的学习：contextual dueling bandit（CDB）；
- 在人类偏好学习中，我们通常是从固定的 batch 中学习，而这个 batch 是离线标注的动作对（action pairs）偏好。类似地，基于偏好的强化学习（_preference-based RL_，PbRL）是根据 _未知_ 的「评分」（scoring）函数生成的二元偏好进行的，而非根据奖励。PbRL 存在各种算法，包括可以重用离线策略偏好数据的方法，但通常需要首先**显式估计潜在的评分函数**（即奖励模型RM），然后再进行优化。

### RLHF
[[Training language models to follow instructions with human feedback(InstructGPT)]]

![image.png|675](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231223154752.png)

最后使用PPO进行优化；在这里的约束项防止policy模型偏离原始的模型太远，这样是尽可能保证policy model的分布和RM能预测的分布一致，且防止模式坍缩到单一的高奖励回答（比如长的重复语句）、保证模型输出的多样性；
由于语言生成的离散型，该目标不可微，因此常用RL方法处理以下的reward function：
$$
r(x,y)=r_\phi(x,y)-\beta(\log \pi_\theta(y|x)-\log \pi_{ref}(y|x))
$$

## DPO
**核心观点**：利用从奖励函数到最优策略的解析映射，将**对奖励函数的损失**转化为**对策略的损失**。这种变量转换的方法使我们能够跳过显式的奖励建模步骤，同时仍然在现有的人类偏好模型（如 Bradley-Terry 模型）下进行优化。实质上，**策略网络既代表语言模型，又代表奖励**。
### DPO 目标
根据优化目标函数（3）式，对于给定的ref-model以及确定的RM，最优策略 $\pi$ 的解析解为
$$
\pi_{r}(y,x)=\frac{1}{Z(x)}\pi_{\text{ref}}(y|x) \exp\left( \frac{1}{\beta}r(x,y) \right)
$$
其中，配分函数 $Z(x)$ 表达式为
$$
Z(x)=\sum_y \pi_{\text{ref}}(y|x) \exp\left( \frac{1}{\beta}r(x,y) \right)
$$
（推导过程在附录A1，大概思路是从3出发，展开DL散度表达式，改为min问题并把beta提取到r那一项，最后引入配分函数提取出y无关项，得到以下的关键步骤）（也有可能想到配分函数更关键）

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231223171852.png)

