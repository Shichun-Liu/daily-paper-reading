---
tags:
  - 涌现能力
date: 2022-08-01
---
[[2206.07682] Emergent Abilities of Large Language Models](https://arxiv.org/abs/2206.07682)

## 核心
- 提出了**涌现能力**的概念：小模型中没有出现但是大模型中出现的能力；
- 涌现能力的存在使得对于模型能力的预测造成了挑战；能否通过进一步的scaling来扩大能力范围（进一步的涌现）？
- 大力出奇迹，什么时候是个尽头？

## 引言
- scaling law：与具体的任务类型相关；
- 涌现能力具备不可预测性；
- **定义：Emergence is when quantitative changes in a system result in qualitative changes in behavior；**
- 有点儿类似宏观态和微观态的关系；宏观态的性质（压强、体积）是微观态不具备的，但是从根本上来说是由微观态通过统计得到的（统计得到配分函数，计算亥姆霍兹自由能，通过麦克斯韦关系式得到所有宏观态）。为什么计算机的涌现能力不可预测呢？根本上还是因为模型的黑盒性、以及各种【能力】的本质没有和微观状态建立联系（笔者认为）。
- 我们将大型语言模型的涌现能力定义为小规模模型中不存在但大规模模型中存在的能力；因此，它们不能通过简单地推断较小规模模型的性能改进来预测（§2）。我们调查了在一系列先前工作中观察到的新兴能力，将它们分类为**小样本提示**（Few-Shot Prompt §3）和**增强提示策略**（§4）。涌现激发了未来关于为什么获得这种能力以及更多的**扩展是否会导致进一步涌现的能力**的研究，我们强调这是该领域的重要问题（§5）。

## 定义
- 直接定义如上；
- 表现上（scaling curve）来看，模型的性能在规模较小的时候是随机的，直到达到了某个阈值，性能增加到远高于随机的水平。这种质变也被称为**相变**（phase transition）。
- 模型规模可扩放的方面：**计算量，参数量，训练数据量**；
- 某一个能力并不是与某个规模绑定，而是取决于多方面的因素（比如更少的训练量但是更高的训练数据质量依然可以获取能力）；如何进行更**高效**地训练（让模型获得某个能力）也是未来研究的方向；

## Few-shot Prompt 
这是由GPT-3带来的涌现能力；

![image.png|700](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225145304.png)

## Augmented Prompting
由策略带来的涌现能力；在规模超过阈值之后策略发挥巨大作用；
If a technique shows no improvement or is harmful when compared to the baseline of not using the technique until applied to a model of a large-enough scale, we also consider the technique an emergent ability.


![image.png|700](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225150608.png)

## 讨论
对涌现能力的研究旨在探究一个问题：**继续扩大模型规模，是否还能够赋予模型新的能力**。

### 潜在解释
- 目前对于涌现能力的成因尚无定论；
- 有一些直观的解释：对于推理任务而言，推理步骤必须要求模型具备足够多的深度；更多的参数和训练量可以记住更多的知识；
- 需要重新设计对于能力的**评估指标**：不能只使用精确的字符串匹配，数学推理任务的最终结果；不合适的指标设计掩盖了模型在改进的事实，从而造成了一种涌现的假象（改善是连续的，这从交叉熵损失的连续下降上可以看出来；但是表现到指标上是突变的）。

### beyond scaling 
- 模型能力的获得不只有scaling一种方式：新架构的模型、更高质量的数据集、改进的训练方式都有可能解锁新能力；
- 计算语言学：Computational linguistics work has further shown how **threshold frequencies** of training data can activate emergent syntactic rule-learning when model parameters and training FLOPs are held constant (Wei et al., 2021b), which has even been shown to have **striking “aha” moments** similar to those in the psycholinguistics literature (Abend et al., 2017; Zhang et al., 2021). 
- scaling受到硬件水平的限制，且优化不具有方向性，不保证效果。
- 特定能力可能与模型在特定语料上的困惑度相关；
- 不安全性（bias，toxic）也是模型在scaling中需要考虑的因素；

## 未来工作
- 进一步的model scaling；
- 改进模型架构、数据质量；
- data scaling； 
- 更好的prompt技巧；
- 可解释性；
