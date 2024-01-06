## paper list

- [[A General Language Assistant as a Laboratory for Alignment]]
	- 2021-12-09， #Anthropic
	- 比较了模仿学习、binary discrimination、ranked preference modeling；
	- 提出 LM Pre-training → Preference Model Pre-training(PMP) → Preference Model fine-tune 流程；
	- 提出 #上下文蒸馏 ， #偏好学习 
- [[Training a Helpful and Harmless Assistant with Reinforcement Learning from Human Feedback]]；
	- 2022-04-12， #Anthropic
	- 通过收集人类的偏好数据并应用偏好建模（PM）和从人类反馈中强化学习（RLHF）的技术来训练一个相对有帮助和无害（HH）的Agent； 
	-  可以将对齐任务和特定的nlp任务混合起来训练，能够不影响对齐效果也不影响任务性能。
	- online学习，持续迭代；
- [[Training language models to follow instructions with human feedback(InstructGPT)]]
	- 2022-03-10, OpenAI， #InstructGPT
	- ChatGPT的前身，确定了SFT-RM-PPO三阶段的流程范式。
- [[Constitutional AI-Harmlessness from AI Feedback]]
	- 2022-12-15， #Anthropic 
	- 宪法AI；提出 #RLAIF 
	- 尽量减少人类直接提供反馈数据；人类输入宪法（一套规则），让模型对其中的harmful内容进行检测并反馈，从而构造比较数据对，训练PM，进行RL（后面与InstructGPT类似）。
- [[Direct Preference Optimization-Your Language Model is Secretly a Reward Model(DPO)]]
	- #DPO ，2023-05-30
	- 直接偏好学习；
	- 发现语言模型策略和奖励函数之间的映射，使得能够直接训练语言模型以满足人类偏好。这种方法使用简单的交叉熵损失，无需强化学习或牺牲通用性。
	- RLHF和DPO的差异本质就是理解强化学习的value estimation和reward的差异。DPO就相当于直接用reward做对策略做正相关优化，而且还是*贪心优化* 。而RL的value estimation则是expected future rewards，相当于*动态规划* 的backup table值而不是贪心值，RL难其实就是没拟合好value estimation，用欠佳的监督对策略做正相关优化。
- [[Policy Optimization in RLHF：The Impact of Out-of-preference Data]]
	- 
- [[Scaling Laws for Reward Model Overoptimization]]
	- 2022/10/19, OpenAI
	- 以KL Div表示的scaling law在不同的policy optimization下是不一样的（这说明了KL Div并不是一个好的描述变量）;
	- 在PPO里的learning objective加KL-Div 等价于early stop，会带来更大的Proxy-Golden Gap（这个比较直观，因为KL-Regularizer那一项只是为了让优化更慢用的，它本身对于Golden Reward就是加bias减variance的；
	- 一个重要的问题是，得到的结论似乎是hyper-param- dependent的，也就是说这些scaling law和预测只能用于特定的setting，如果换了一组PPO参数，是否scaling law需要重新寻找/标定？这样的话用于预测的意义就不大了；
- 
TODO

## 原理
[[2310.06452] Understanding the Effects of RLHF on LLM Generalisation and Diversity](https://arxiv.org/abs/2310.06452)
[[Reinforcement Learning Upside Down-Don't Predict Rewards -- Just Map Them to Actions]]

## Alignment

- [ ] Deep Reinforcement Learning from Human Preferences, NIPS 2017 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1706.03741)
- [ ] Learning to summarize from human feedback, Arxiv 2020 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2009.01325)
- [ ] A General Language Assistant as a Laboratory for Alignment, Arxiv 2021 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2112.00861)
- [x] Training a Helpful and Harmless Assistant with Reinforcement Learning from Human Feedback, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2204.05862)
- [x] Teaching language models to support answers with verified quotes, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2203.11147)
- [x] InstructGPT: Training language models to follow instructions with human feedback, 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2203.02155)
- [ ] Improving alignment of dialogue agents via targeted human judgements, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2209.14375)
- [x] Scaling Laws for Reward Model Overoptimization, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2210.10760)
- [ ] Scalable Oversight: Measuring Progress on Scalable Oversight for Large Language Models, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2211.03540.pdf)
- [x] Training a Helpful and Harmless Assistant with Reinforcement Learning from Human Feedback
- [x] A General Language Assistant as a Laboratory for Alignment
- [x] Constitutional AI: Harmlessness from AI Feedback
- [x] Training language models to follow instructions with human feedback
- [x] Direct Preference Optimization: Your Language Model is Secretly a Reward Model
- [ ] Specific versus General Principles for Constitutional AI
- [ ] Failure Modes of Learning Reward Models for LLMs and other Sequence Models
- [ ] Open Problems and Fundamental Limitations of Reinforcement Learning from Human Feedback
### harmless

- [ ] Red Teaming Language Models with Language Models, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2202.03286)
- [x] Constitutional ai: Harmlessness from ai feedback, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2212.08073)
- [ ] The Capacity for Moral Self-Correction in Large Language Models, Arxiv 2023 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2302.07459)
- [ ] OpenAI: Our approach to AI safety, 2023 [Blog](https://link.zhihu.com/?target=https%3A//openai.com/blog/our-approach-to-ai-safety)

### reasoning/Agent 
- [ ] Practices for Governing Agentic AI Systems [practices-for-governing-agentic-ai-systems.pdf](https://cdn.openai.com/papers/practices-for-governing-agentic-ai-systems.pdf)
	-  对于Agent AI系统的安全规范白皮书；提出了一套保证Agent操作安全和负责任的初步实践；

- [x] Mathematical discoveries from program search with large language models [Mathematical discoveries from program search with large language models | Nature](https://www.nature.com/articles/s41586-023-06924-6)
	- [[FunSearch]]
	- 并不是完成了数学推理任务；而是在一个组合问题上找到了一个高维的新算法；
	- 有一个简洁的评估器，把求解数学问题转化为生成相应的求解算法；
	- LLM+进化算法，通过迭代更新一个代码库；

## 其他
- [大语言模型（LLM）的进化树，学习LLM看明白这一张图就够了 - 知乎](https://zhuanlan.zhihu.com/p/627491455)
- [DeepMind科学家:GPT模型不够好，还差搜索！\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1Eb4y1T7Fw/?buvid=XX7888273AF7697D5818467BE308AF8DD2080&from_spmid=default-value&is_story_h5=false&mid=X%2BBahFQiuDNXsN4Pq5HWJw%3D%3D&p=1&plat_id=122&share_from=ugc&share_medium=android&share_plat=android&share_session_id=8b6269b1-e207-4305-b68e-c8a071c39aa4&share_source=WEIXIN&share_tag=s_i&spmid=united.player-video-detail.0.0&timestamp=1702241765&unique_k=PNfwwXJ&up_id=437980486&vd_source=3f4448c688c0096124dbfa48b0a085c3)
	- 当前的语言模型（LLMs）主要涉及学习方面，而对于创造性问题解决，特别是在类似AlphaGo的情境中，需要引入**搜索**的概念。；
	- LLM现在所作的只是基于现有数据的模仿或者混合，并不是真正的创造；因此现在的模型并不会产生超过其预训练数据的能力，直至引入一个强大的搜索算法；
- [openai-preparedness-framework-beta.pdf](https://cdn.openai.com/openai-preparedness-framework-beta.pdf)
