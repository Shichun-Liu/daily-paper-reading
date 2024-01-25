---
tags:
  - reasonging
  - RL
date: 2024-01-23
---
- 核心：探索 MultiAgent Systems (MAS) 中代理之间的**协作**控制；
- LLAMAC实现了类似于人脑中发现的值分布编码，利用内部和外部反馈机制来促进其模块之间的协作和迭代推理；
- we introduce the **TripletCritic** structure, which is inspired by the distributional code for value in the brain；
- We propose a modular and **token-efficient** framework for augmenting the decision-making capabilities of LLM-based agents in large-scale multi-agent environments. This framework enables autonomous iterative cooperation among **a large number of agents**（50个actor）.

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240125130737.png)

triplet-critic

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240125130957.png)

核心模块：Critic module
- 它接收当前状态并从memory module 中提取相关细节，从而能够从参与者的历史轨迹进行评估和学习。critic module 充当集中式协调器，参与reasoning和planning活动，以制定潜在的高回报和可靠的计划建议。
- workflow:
	- 环境提供state s，文本形式；
	- critic module接受s以及memory module的信息，根据3-critic dialogue来产生文本形式的internal feedback，记为suggestion su，提供给所有actor；
	- 每个actor从环境中获得observation o，接受su，进行推理得到external feedback；所有actor再对于suggestion进行表决
		- 若认为su是一致正确的，那么所有actor将采取各自动作a，得到r；结果传入memory module；
		- 若认为su不正确，那么得到各自的external feedback signal；Subsequently, the TripletCritic receives this external feedback information and **formulates a new suggestion** for the actor based on the three-critic dialogue history and the recently received feedback information.
	- 得到最终结果，结束推理。
- (Fig. 2) 两个具有不同偏好、相同目标的actor分别给出各自意见，然后协调者进行正确性检查和中和，给出最终suggestion。

![image.png|400](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240125133359.png)

