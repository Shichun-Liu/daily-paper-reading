---
tags:
  - decision
date: 2022-10-29
---
[vh-language](https://shuangli-project.github.io/Pre-Trained-Language-Models-for-Interactive-Decision-Making/)
[[2202.01771] Pre-Trained Language Models for Interactive Decision-Making](https://arxiv.org/abs/2202.01771)

- 在不涉及到语言的任务中，能否使用预训练PLM作为通用框架？如何使用这些功能？
- 提出了一种使用LLM支持一般化的序列决策的学习和泛化的方法：把target和observation表示为**embedding**的序列，使用LLM作为policy网络来**预测next action**；
- 迭代过程：agent与env交互收集数据，标记失败经验，在自我监督中更新其policy；
- 连续输入表示，基于LM-based 权重初始化对于结果影响较大；策略输入编码的形式（自然语言字符串/随机序列编码）对于结果几乎没有影响；
- 语言建模诱导的representation对于目标和计划的建模同样有效（探讨LM是如何进行决策的？）；这些表征可以在语言处理之外的地方用来学习和泛化。
- 任务：**具身场景**；

## 引言

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226112222.png)

- 我们提出的 **LID** 是一个使用PLM进行交互式决策（Pre-Trained **Language Models for Interactive
Decision-Making**）的框架。如图 1（右图）所示，我们将输入（包括**观察结果、目标和历史记录**）编码为一系列embedding。这些嵌入信息被传递到一个策略网络，该网络使用PLM 参数进行初始化，并对 LM 进行微调以预测行动。
- 该框架适用范围很广，可适应以自然语言字符串、图像修复或场景图表示的目标和环境状态；具有强大的泛化能力；
- 对于没有监督数据的情景，Agent必须主动收集数据。所以基于LM设计了ADG（active data gathering）程序：
	- 首先，探索使用随机动作和当前策略生成的动作的组合来收集轨迹。在这个高维问题中，探索是不够的，大多数轨迹可能无法实现最终目标。
	- 即使是失败的轨迹也包含解决某些子目标的有用子轨迹，因此我们在事后重新标记阶段（hindsight relabeling stage）重新标记这些目标。重新标记的目标描述了在提取的子轨迹中所实现的目标。
	- 策略更新阶段（policy update stage）对重新标记的轨迹进行采样以更新策略。
	- 主动数据收集程序使我们能够在没有预先收集的专家数据的情况下训练 LM 策略。它在具体决策任务上的表现也**优于强化学习（RL）方法**，并且能够更有效地泛化到新任务。
- LID 具有泛化性的三个原因
	- 使用**language-based input embeddings**，利用了LM对于语言字符串的推理能力；但是具体的encoding方式是不重要的，可以是自然语言也可以不是；
	- the **sequential structure** of transformer inputs，相比于大多数policy框架使用的固定大小的observation；
	- task-general inductive bias conferred by **weight initialization with LM pretraining**（说明PLM的初始预训练参数学到了有助于推理的知识）；
- **主要贡献**
	- First, we propose to use pre-trained LMs as a general scaffold for **interactive decision-making** across a variety of environments by **converting all policy inputs into sequential data**.
	- Second, we demonstrate that language modeling improves combinatorial generalization in policy learning: initializing a policy with a pre-trained LM substantially improves out-of-distribution performance on novel tasks.
	- Third, we integrate an **active data gathering** procedure into the proposed approach to further enable policy learning on environments without using pre-collected expert data.
	- Finally, we perform several analyses to explain the generalization capabilities of pre-trained LMs, finding that natural strings are not needed to benefit from LM pre-training, but the sequential input encoding and weight pre-training are important.

 
**优化目标**

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226153139.png)

- 使用GPT-2作为LM；
- 任务：babyAI和VirtualHome；
- **Environment encodings in VirtualHome**. VirtualHome 中，每个目标都由一系列谓词和多重性组成，并被翻译成**模板化的英语句子**（例如，“Inside(apple,冰箱):2”变成“将两个苹果放入冰箱”）；为了对代理的observation进行编码，我们提取了当前可见物体的列表、它们的状态（例如 "打开、干净"）以及三维世界坐标。我们使用全连接层对三维信息进行编码，并生成观察中每个物体的特征表示。为了对历史进行编码，我们存储了所有先前操作的信息，并将其转换为模板化的英语句子（例如 "我把盘子放在了厨房的桌子上，把苹果放在了冰箱里"）。
- **Action prediction.** We pool LM outputs into a “**context representation**” that is used to predict the next action. In training, we maximize the probabilities of demonstrated actions. In inference, we select the valid action with the highest probability.

### 训练
- 在专家轨迹上微调； 
- 主动探索

![image.png|625](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226160943.png)

![image.png|625](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226161125.png)

类似RL思想，但是优于RL方法的效果；中间relabel的阶段相当于人为引入了过程监督信号；