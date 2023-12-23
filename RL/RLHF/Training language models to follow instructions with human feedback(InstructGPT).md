---
tags:
  - OpenAI
  - InstructGPT
date: 2022-03-10
---
**资料**
- [【强化学习 229】ChatGPT/InstructGPT - 知乎](https://zhuanlan.zhihu.com/p/589827115)
- [ChatGPT/InstructGPT详解 - 知乎](https://zhuanlan.zhihu.com/p/590311003)
- [训练语言模型以遵循人类反馈的指令-InstructGPT（ChatGPT前身） - 知乎](https://zhuanlan.zhihu.com/p/590198759)
- [【论文阅读】InstructGPT: Training language models to follow instructions with human feedback](https://blog.csdn.net/orangerfun/article/details/129651956)

**目录**
- [[#动机|动机]]
- [[#主要流程|主要流程]]
	- [[#主要流程#数据与训练|数据与训练]]
		- [[#数据与训练#1. SFT|1. SFT]]
		- [[#数据与训练#2. RM|2. RM]]
		- [[#数据与训练#3. PPO|3. PPO]]
- [[#结果|结果]]
	- [[#结果#提升|提升]]
	- [[#结果#缺点|缺点]]

> [!abstract]
> ChatGPT的前身，确定了SFT-RM-PPO三阶段的流程范式。 
## 动机

- 前提：GPT-3在典型NLP任务上表现优异，并涌现出了非典型任务（math，code）上的泛化能力；
- 指示学习（instruct learning）和提示学习（prompt learning）：一个是激发模型的理解能力，采取相应的动作；另一个是激发模型的补全能力（不过二者基本上没什么区别，可以统一认为是ICL）；
- RLHF：模型可以认为是训练数据的压缩表示，所以训练数据分布根本上决定了模型生成内容（能力）；为了确保输出是更加人为可控的，需要将人类的价值观作为reward信号来进行RL；
- PPO算法：一种新型的policy gradient算法，解决步长难以确定的问题；
- 目的：增强模型的指令遵循能力，解决misalignment问题；

## 主要流程

**三阶段**
1. 根据采集的SFT数据集对GPT-3进行有监督的微调（Supervised FineTune，SFT）；
2. 收集人工标注的对比数据，训练奖励模型（Reword Model，RM）；
3. 使用RM作为强化学习的优化目标，利用PPO算法微调SFT模型。

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219160844.png)

其中，RM和策略模型是不断迭代更新的（on-policy）。
### 优化目标

![image.png|550](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231223154158.png)

### 数据与训练
#### 1. SFT
**数据**
- 形式：prompt-response pair；
- 来源：OpenAI的Playground用户+数据标注工；
**训练**
与GPT-3的训练一致，且可以适当过拟合；
#### 2. RM
**数据**
- 形式：给每个response打分；
- 来源：数据标注工；
- 构造时，对每个prompt，InstructGPT会随机生成 k 个输出（ 4≤k≤9 ），然后它们向每个labeler**成对**的展示输出结果（一共 $C_k^2$ 个），用户从中选择效果更好一个。

**训练**
将每个prompt对应的 $C_k^2$ 个response为一个batch，目标为 最大化高分和低分的response的分数差值；

![image.png|450](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219164030.png)

#### 3. PPO
通过结合人工标注，将强化学习引入到预训练语言模型是这个算法最大的创新点。

**数据**
- 没有进行标注，它均来自GPT-3的API的用户。既又不同用户提供的不同种类的生成任务，其中占比最高的包括生成任务（45.6%），QA（12.4%），头脑风暴（11.2%），对话（8.4%）等。

**训练**
![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219164346.png)


## 结果
### 提升
1. 生成结果更加真实，更加遵循人类指令；
2. harmless有所提升（但仍有输出的可能）；
3. coding能力提升较大；

### 缺点
1. 通用NLP任务上的能力有所损失；
2. 存在幻觉现象；
3. 对于指令敏感；
4. 输出内容偏向于长文本；

