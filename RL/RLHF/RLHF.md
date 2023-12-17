## paper list

- [[A General Language Assistant as a Laboratory for Alignment]]
	- 2021-12-09，Anthropic
	- 比较了模仿学习、binary discrimination、ranked preference modeling；
	- 提出 LM Pre-training → Preference Model Pre-training(PMP) → Preference Model fine-tune 的流程；
	- 提出上下文蒸馏；
- [[Training a Helpful and Harmless Assistant with Reinforcement Learning from Human Feedback]]；
	- 2022-04-12
	- 通过收集人类的偏好数据并应用偏好建模（PM）和从人类反馈中强化学习（RLHF）的技术来训练一个相对有帮助和无害（HH）的Agent； 
	-  可以将对齐任务和特定的nlp任务混合起来训练，能够不影响对齐效果也不影响任务性能。
	- online学习，持续迭代；

## Alignment

- **Deep Reinforcement Learning from Human Preferences**, NIPS 2017 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/1706.03741)
- **Learning to summarize from human feedback**, Arxiv 2020 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2009.01325)
- **A General Language Assistant as a Laboratory for Alignment**, Arxiv 2021 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2112.00861)
- **Training a Helpful and Harmless Assistant with Reinforcement Learning from Human Feedback**, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2204.05862)
- **Teaching language models to support answers with verified quotes**, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2203.11147)
- InstructGPT: **Training language models to follow instructions with human feedback**, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2203.02155)
- **Improving alignment of dialogue agents via targeted human judgements**, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2209.14375)
- **Scaling Laws for Reward Model Overoptimization**, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2210.10760)
- Scalable Oversight: **Measuring Progress on Scalable Oversight for Large Language Models**, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2211.03540.pdf)

### harmless

- **Red Teaming Language Models with Language Models**, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2202.03286)
- **Constitutional ai: Harmlessness from ai feedback**, Arxiv 2022 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2212.08073)
- **The Capacity for Moral Self-Correction in Large Language Models**, Arxiv 2023 [Paper](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2302.07459)
- **OpenAI: Our approach to AI safety**, 2023 [Blog](https://link.zhihu.com/?target=https%3A//openai.com/blog/our-approach-to-ai-safety)


## Source
[大语言模型（LLM）的进化树，学习LLM看明白这一张图就够了 - 知乎](https://zhuanlan.zhihu.com/p/627491455)