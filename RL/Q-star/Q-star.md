之前的调研（粗糙且混乱）：[Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://chipped-icicle-2f0.notion.site/Q-3fa70d44521e4666a565d2c1382d7f59?pvs=4)
## concept
- [[A-star algorithm]]
- [[MCTS-RL]]
	- AlphaZero
	- MuZero

推理任务Paper+dataset
[GitHub - atfortes/LLM-Reasoning-Papers: Collection of papers and resources on Reasoning in Large Language Models (LLMs), including Chain-of-Thought (CoT), Instruction-Tuning, and others.](https://github.com/atfortes/LLM-Reasoning-Papers)

## paper list
- [x] Controllable Text Generation with Neurally-Decomposed Oracle
	- [[Controllable Text Generation with Neurally-Decomposed Oracle]] 
	- 给定一个基本的预训练语言模型和sequence-level oracle function（指示是否满足规则），通过训练辅助模型NADO，把序列级规则分解问token级指导，引导模型进行可控文本生成。
- [x] [[ToolChain-star：Efficient Action Space Navigation in Large Language Models with A-star Search]]
	- #A-star 
	- 对于cost function的设计非常巧妙：g由LCS分数以及动作一致性分数构成，h由LCS分数以及LLM打分构成；
	- 还没看完，但是有一个函数call过程数据集是关键；
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
- [x] LLM+MCTS  [LLM的快思考与慢思考路线之MCTS - 知乎](https://zhuanlan.zhihu.com/p/659230417)
	- [[Solving Math Word Problem via Cooperative Reasoning induced Language Models]]
		- CoRe (Zhu et al., 2022) fine-tunes reasoning step **generator** and **verifier** for math word problems with MCTS for decoding.
	- [[Alphazero-like Tree-Search can guide large language model decoding and training]]
		- 提出通用任务上基于MCTS-Rollout的LLM训练新范式；
		- 三个网络：policy network，reward model，value function；
		- 可以从两个粒度（句子/token）解决推理任务和RLHF任务；
	- [[Making PPO even better：Value-Guided Monte-Carlo Tree Search decoding]]
		- 把PPO过程中使用的value function作为MCTS中对于node的评估函数使用，不需要rollout；
		- 基于token-level的引导编码，在四个任务上取得了较好结果；
		- 使用TD-λ来给出中间过程reward；
	- [[Large Language Models as Commonsense Knowledge for Large-Scale Task Planning]]
		- 机器人对象重排任务；
		- 使用MCTS比较常规，主要贡献是将LLM引入到这个任务中，并对于MCTS的node和edge进行建模；
		- 使用LLM的常识知识给出每个物品的位置信念，并使用LLM进行规划。
	- [[Reasoning with Language Model is Planning with World Model]]
		- 使用LLM构建世界模型，从而引入了四种不同的reward function（动作概率，状态置信度，自我检测，针对特定任务的启发式函数）；不同的组合，适用于不同的任务；
		- 在计划生成，GSM8k，逻辑推演上进行实验；
- [x] Pangu-Agent: A Fine-Tunable Generalist Agent with Structured Reasoning
	- [[Pangu-Agent：A Fine-Tunable Generalist Agent with Structured Reasoning]]
	- 结构化推理

- [x] Boosting LLM Reasoning: Push the Limits of Few-shot Learning with Reinforced In-Context Pruning
	- [[Boosting LLM Reasoning：Push the Limits of Few-shot Learning with Reinforced In-Context Pruning]]
	- 数学推理+fewshot CoT，构造了一个数学推理数据集
	- 更像是Prompting的工作，与RL关系不是很大；

- [ ] 

### meta-learning RL
- [x] Q-Transformer: Scalable Offline Reinforcement Learning via Autoregressive Q-Functions
	- [[Q-Transformer：Scalable Offline Reinforcement Learning via Autoregressive Q-Functions]]
- [x] [[Meta Learning Shared Hierarchies]]
	- 相似任务之间的迁移；
	- Robotic；
- [x] [[Stochastic Neural Networks For Hierarchical RL]]
	- 跟上一篇文章类似，也是一个manager policy，一些子任务policy；
	- Robotic；
- [ ] 


