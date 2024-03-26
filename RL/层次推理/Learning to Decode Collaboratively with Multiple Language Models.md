---
tags:
  - reasonging
date: 2024-03-01
---
- MIT
- 这篇工作提出了一种名为Co-LLM的方法，旨在教授多个大型语言模型（LLMs）通过在 token level 交替生成内容来协作。研究者将决定**哪个LLM生成下一个token的决策建模为一个潜在变量**。通过优化训练集在潜在变量模型下的边际似然，base LLM自动学习何时自我生成，何时调用“assistant”LM 来生成内容，所有这些操作都**没有直接的监督**。在解码过程中进行token级别的协作，可以根据特定任务的需求，融合每个模型的专长。
- Co-LLM特别适用于跨领域设置，其中一个通用基础LLM学习调用领域专家模型。在指令遵循、特定领域问答和推理任务上，研究表明联合系统的绩效超过了单个模型。通过对学习到的潜在决策进行定性分析，研究表明使用该方法训练的模型展示了几种有趣的协作模式，例如模板填充。
- 探讨了在解码过程中的协作生成的潜在变量框架，描述了Co-LLM的训练和解码过程，并在多个任务上评估了Co-LLM的性能。结果表明，教授模型协作可以提高所有任务的性能，有时甚至可以匹配或超过对大型模型进行微调的性能。通过使用链式推理，Co-LLM也可以应用于分类任务，实验表明它通过增强推理能力提高了性能。研究还特别指出，Co-LLM在跨领域设置中特别有用，其中一个通用基础LLM学习调用领域专家模型。
	- 数据集包括Tülu v2 mix（用于指令遵循任务）、GSM8k、MATH以及BioASQ（用于医疗问题回答）；
- 这篇论文还讨论了Co-LLM的局限性，例如需要为每个任务选择最佳的延迟阈值，以及在某些情况下，过度依赖助手模型可能会导致生成错误。未来的工作计划包括将Co-LLM扩展到整合超过两个LLMs，并探索从这种设置中出现的更复杂的协作策略。

![image.png|400](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240322204806.png)
## 训练
模型的训练过程遵循了以下步骤：
1. **基础模型和潜在变量参数的联合微调**：在实验中，研究者首先对基础模型进行微调，同时学习潜在变量参数θ。这里的θ代表了一个线性二分类头，位于基础模型的最后一层。具体来说，如果`ht(X<t)`是基础模型在时间步t对于输入`X<t`的最后一个隐藏状态，且θ是权重向量，那么潜在变量的分布`Pθ(Zt|X<t)`通过sigmoid函数来表示。
2. **训练目标**：训练目标是最小化负对数边际似然`−∑t log P(Xt|X<t)`，其中`P(Xt|X<t)`是在边缘化潜在变量Zt后的下一个token的似然。这与典型的预训练目标——最大化下一个token的概率——是一致的。
3. **θ的初始化**：为了鼓励基础语言模型快速从自我生成切换到协作生成，适当地初始化θ是有帮助的。研究者使用弱监督来收集初始的Zt值，使用这些伪标签来初始化参数θ，然后在训练过程中允许Zt的值发生变化。
4. **解码**：在解码过程中，研究者**使用了一个阈值η来决定何时调用助手模型**。具体来说，当`Pθ(Zt=1|X<t)`大于η时，执行助手模型来预测下一个token。这个阈值η是通过在小规模验证集上进行网格搜索选择的。
5. **训练细节**：研究者使用了AdamW优化器，并通过FlashAttention和DeepSpeed ZeRO Stage 2来减少GPU内存的使用。训练使用了4个A100 80G GPUs，并且在目标token上计算边际似然损失（即，输入token被遮蔽）。
6. **数据集和提示**：研究者使用了特定的提示来格式化训练和评估数据集，并在训练和评估时保持一致。
7. **阈值搜索**：对于所有具有可学习模型选择器的方法（包括Co-LLM），研究者在测试前使用小规模验证集来选择最佳的η。

通过这些步骤，Co-LLM能够在没有直接监督的情况下，从数据中学习如何最佳地协作生成内容。

# 论文
- 主要解决解码时如何组合多个LLM，并考虑速度、连贯、简洁等方面；
- LLMs+Tool现有思路：需要一个流程规定how to combine models / when to use the tools；或者特定公式组合多个模型的logits；或者通过监督来训练模型需要在何时调用tools；本工作主要关注多个LLM在token-level交替生成的策略；
- 弱模型(base model)搭建一个句子的脚手架，强模型(assistant model)完善细节部分；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240325192339.png)

- The framework centers around a finetunable **base model**, which itself is a relatively *small LM*. It decides which other **assistant models** (which are typically *larger* and/or more *specialized* models) to use **per token**.
- 