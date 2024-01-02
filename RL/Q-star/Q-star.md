之前的调研（粗糙且混乱）：[Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://chipped-icicle-2f0.notion.site/Q-3fa70d44521e4666a565d2c1382d7f59?pvs=4)
## concept
- [[A-star algorithm]]
- [[MCTS-RL]]
	- AlphaZero
	- MuZero


## paper list
- [x] Controllable Text Generation with Neurally-Decomposed Oracle
	- [[Controllable Text Generation with Neurally-Decomposed Oracle]] 
	- 给定一个基本的预训练语言模型和sequence-level oracle function（指示是否满足规则），通过训练辅助模型NADO，把序列级规则分解问token级指导，引导模型进行可控文本生成。
- [ ] [[ToolChain-star--Efficient Action Space Navigation in Large Language Models with A-star Search]]
	- #A-star 
	- 
- [x] Evaluating and Mitigating Discrimination in Language Model Decisions
	- [[Evaluating and Mitigating Discrimination in Language Model Decisions]]
	- #harmless #Anthropic 
	- 使用prompt有效改进了LM输出中存在的歧视内容；
- [x] Mathematical discoveries from program search with large language models
	- [[FunSearch]] 
	- 并不是完成**推理**任务；而是在一个组合问题上找到了一个高维的新算法；
	- 有一个简洁的评估器，把求解数学问题转化为生成相应的求解算法；
	- LLM+进化算法，通过迭代更新一个代码库；
- [x] Pre-Trained Language Models for Interactive Decision-Making
	- [[Pre-Trained Language Models for Interactive Decision-Making]]  
	- **具身场景任务**；
	- 把goal，observation，history进行表示为自然语言序列，通过PLM来预测next action；
	- 有SL部分也有主动数据收集的部分（经验回放，重新标注）
- [ ] LLM+MCTS  [LLM的快思考与慢思考路线之MCTS - 知乎](https://zhuanlan.zhihu.com/p/659230417)
	- [[Alphazero-like Tree-Search can guide large language model decoding and training]]
		- 提出通用任务上基于MCTS-Rollout的LLM训练新范式；
		- 三个网络：policy network，reward model，value function；
		- 可以从两个粒度（句子/token）解决推理任务和RLHF任务；
	- [[Making PPO even better：Value-Guided Monte-Carlo Tree Search decoding]]
	- [[Large Language Models as Commonsense Knowledge for Large-Scale Task Planning]]
	- [[Reasoning with Language Model is Planning with World Model]]
	- 
- [ ] Pangu-Agent: A Fine-Tunable Generalist Agent with Structured Reasoning
	- [[Pangu-Agent：A Fine-Tunable Generalist Agent with Structured Reasoning]]
	- 结构化推理
