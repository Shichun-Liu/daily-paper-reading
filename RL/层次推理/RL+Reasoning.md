前期调研：[[RL-Reasoning-前期调研]]
可靠推理：[[可靠推理]]
## RL+LLM+Reasoning
- Agent 狼人杀；
[LLM Agent论文分享：将RL引入LLM reasoning的一种新思路 - 知乎](https://zhuanlan.zhihu.com/p/663117317)
[【强化学习 247】RL+LLM 若干工作介绍 - 知乎](https://zhuanlan.zhihu.com/p/632333523)
- [[2303.04129] Foundation Models for Decision Making: Problems, Methods, and Opportunities](https://arxiv.org/abs/2303.04129)
	- [决策基础模型：问题，方法和机会 - 知乎](https://zhuanlan.zhihu.com/p/654583552)
	- 

- 我们要做数学问题也要有单一目标，而不能是分散的目标；比如GSM8K的数学题我认为是分散的目标，做24点就是一个单一的目标；
	- cube； 
		- [强化学习算法DeepCube，机器自行解决复杂魔方问题 - 知乎](https://zhuanlan.zhihu.com/p/270333610)
		- [AI魔方 - 知乎](https://zhuanlan.zhihu.com/p/41464087)
	- 旅行商；
		- LARGE LANGUAGE MODELS AS OPTIMIZERS；[OPRO | 谷歌DeepMind新论文 | 使用自然语言描述优化问题，LLM作为优化器，效果超越人类(线性,回归) - AI牛丝](https://ai2news.com/blog/3315150/)；
		- 
	- 八皇后；
- LLM主要不是做决策的，LLM是做探索的；RL policy从探索的可能action中选择一个；
- 
## 论文阅读
- [[Cumulative Reasoning With Large Language Models]]
	- 水文，但是结果蛮好；
	- 提供了一个不错的动作模板，推理+验证；
- [[Language Agents with Reinforcement Learning for Strategic Play in the Werewolf Game]]
	- Agent+RL 狼人杀；
	- 三个基本动作，分析总结历史，给出候选动作，RL选择动作；
- [[ReFT Reasoning with Reinforced Fine-Tuning]]
	- 先在正确的推理路径上SFT，再对于每道题在不同的路径上PPO；
	- 推理模型就是RL模型；
	- 我们后续工作跟这个类似，动作要更多元；
### 多样化动作归纳
- [[Self-Convinced Prompting Few-Shot Question Answering with Repeated Introspection]]
- [[Retroformer Retrospective Large Language Agents with Policy Gradient Optimization]]
- [[Controlling Large Language Model-based Agents for Large-Scale Decision-Making An Actor-Critic Approach]]
- [[Math-Shepherd Verify and Reinforce LLMs Step-by-step without Human Annotations]]

总结：[飞书 - 登录](https://fudannlp.feishu.cn/sheets/PoGisapTQhXa1Gt2C3acfBULnyh?from=from_copylink)

今日休息，品读transformer代码
今天摸鱼
准备配环境
写ICML论文
写Paper: TransferToD数据集