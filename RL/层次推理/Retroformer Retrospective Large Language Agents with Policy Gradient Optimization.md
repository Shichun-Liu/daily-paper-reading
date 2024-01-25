---
tags:
  - reasonging
  - RL
date: 2023-08-04
---
- 机构：Salesforce；
- 一种通过学习回溯模型来加强大型语言模型的原则框架，该模型通过策略梯度自动从环境反馈中调整语言代理提示。
- 具体来说，我们提出的代理架构从**多个环境和任务**的奖励中学习，用于微调预训练的语言模型，该模型通过总结先前失败尝试的**根本原因**并提出行动计划来细化语言代理提示。各种任务的实验结果表明，语言代理随着时间的推移而提高，并且我们的方法大大优于baseline方法。
- 将 HotPotQA (Yang et al., 2018) 中基于搜索的问答任务的成功率提高了 18%

## 方法
- 一方面，我们希望利用经典的**基于 RL 的优化算法**，例如策略梯度来提高模型的性能。另一方面，我们的框架直接**避免了对LLM进行微调**。关键是，我们不是直接训练 LLM，而是训练一个回顾性 LM。回顾性 LM 将用户的提示、奖励和反馈作为输入。它的输出将是实际LLM被使用的提示符。RL 算法用于优化回顾性 LM 模型中的权重，而不是直接在 LLM 上。在我们的框架中，**实际LLM中的权重被假定为固定的(不可训练)**；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240124175824.png)

- **Short-term memory**. The trajectory history τi of the current episode i serves as the short-term memory for decision making and reasoning. 
- **Long-term memory**. The reflection responses that summarize prior failed attempts are appended to the actor prompt as the long-term memory.
- **Reward Shaping**: We apply reward shaping to the binary rewards for obtaining more information. For question answering tasks, instead of exactly matching the answer, we use **f1 score** grading to evaluate the alignment of the **generated output** with the **expected answer** as the reward function.

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240124204342.png)

RL 部分很好地执行了错误定位的功能，并且确保该错误后续不会再犯；