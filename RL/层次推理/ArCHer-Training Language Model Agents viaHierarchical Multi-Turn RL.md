---
tags:
  - reasonging
  - RL
date: 2024-02-01
---

[[2402.19446] ArCHer: Training Language Model Agents via Hierarchical Multi-Turn RL](https://export.arxiv.org/abs/2402.19446)

- 现有的RL方法主要关注单轮奖励最大化（single-turn reward maximization）；
- 本文核心：开发有效、高效的**多轮**RL算法；
	- 该框架保留了 LLM 现有单轮 RL 方法的灵活性（例如，近端策略优化）；
	- 同时适应多轮、长视野，并有效延迟奖励；
- 方法：两层次的RL算法并行运行
	- high-level off-policy RL algorithm：训练一个value function，用于对过去的内容提供reward；
	- low-level RL algorithm：利用上层的value function，提供单轮的token-level的policy，使用PPO优化；
- 用一个 learned **value function** 代替 **reward model**；

## Intro
- 选择必须多轮交互才能完成的任务，而不是单轮就能完成的目标导向任务；
- 现有问题：
	- 短视，要么模仿每一步的成功演示，或针对单轮偏好进行优化（optimize for single-turn preferences);
	- 无法有效credit assignment（无法识别失败的具体步骤、错中有对的部分） ；
	- do not endow policies with information-seeking behavior ;
- 多轮RL的挑战
	- 需要与外部资源进行交互（如google搜索），速度可能很慢；PPO无法复用交互的数据；
	- 多轮的token length更长，token-level method考虑把每个token视为action，那么需要在很长范围内传递reward signal，学习速度很缓慢；
	- 将一轮的对话视为一个action，如此引入了一个巨大的action space，对时序差分学习的off-policy提出挑战；
- 方法
	- high-level ：sentence-level 的 value function off-policy **TD learning** method；
	- low-level ：接收高层的 value function 作为 reward，对该对话内容进行 **on-policy gradient** 优化；
- 实验
	- 对一系列具有主动数据收集（即“在线”设置）的语言“代理”任务进行实验；
	- ArCHer 的算法的样本效率，比 PPO 高 100 倍，并且比 off-policy 方法收敛到更好的性能；
	- 直接支持 RL 算法的即插即用选择和模型；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240306145350.png)

## Related work
- 单轮RL LLM算法：基本上没有与外界的交互，一轮完成决策；传统方法有PPO，A2C，DPO，过滤监督方法；
- 非RL方法：提示工程（ReAct，Reflexion，Voyager等），或者使用成功的轨迹对LLM进行微调；
- 多轮RL方法：PPO能够直接用，但是效率低；**off-policy and offline value-based methods** learn from existing static data，具体有: (1)每个token视为一个action, token-level; (2) sentence-level, 单个utterance视为action, but utilize multiple candidate utterances from a frozen pre-trained LLM for maximization in the Bellman backup;

## 主要方法
### 定义
- 定义一个high-level的MDP,一个low-level MDP;
- state: 
	- high: LLM与环境交互的所有历史对话数据 ;
	- low: 历史对话内容+当前要输出的句子[1:h-1]部分;
- action:
	- high: token序列(sentence);
	- low: 单个token;
- 高级 MDP 中的策略优化旨在最大化**任务奖励**，而低级 MDP 中的策略对齐高级 MDP 的价值函数的奖励，provided at the end of the low-level rollout(生成 “EOS” 时句子结束);
- A particularly convenient choice is to **use TD learning at the high level**, while using **on-policy methods** (Ouyang et al., 2022; Bai et al., 2022) **at the low level**.

### 训练
待阅读
