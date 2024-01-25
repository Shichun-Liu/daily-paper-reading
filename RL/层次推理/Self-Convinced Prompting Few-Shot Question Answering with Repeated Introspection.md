---
tags:
  - reasonging
date: 2023-10-08
---
[[2310.05035] Self-Convinced Prompting: Few-Shot Question Answering with Repeated Introspection](https://arxiv.org/abs/2310.05035)
机构：深圳大学，港理工，港科大广州；

- 三个组件: Normal **CoT**, a **Convincer**, and an **Answerer**.
- prompts +迭代优化：This study contributes to the burgeoning body of research focused on integrating pre-trained language models with **tailored prompts** and **iterative refinement processes** to augment their performance in complex tasks；
- 流程：（1）给定一个由任意 CoT（初始化）生成的问答对，（2）Convincer 模块首先评估答案和推理步骤的正确性（Introspect），（3）如果推理步骤产生错误的答案，Convincer 模块输出到 Answerer 模块的校正推理路径。然后，Answerer 提供基于校正推理路径的答案，以及自提示问题类型信息 (Answer)。(4) 最后，Normal CoT 模块完成最后一个模块输出（Complete）。2-4不断循环，直至最终结果。

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240124161238.png)

## 方法
- 在初始阶段，Normal CoT 为输入问题生成初始答案，称为 Normal Output”。
- 该输出随后作为 Convincer 的输入提供。Convincer 根据输入问题和正常输出产生三个关键输出：**Correctness**, **Analysis**, and **Final Answer**。
	- 如果模型认为答案是正确的，表示为Correctness: Correct，则保留来自“Normal Output”的原始答案作为最终答案，保留“Introspect”步骤；
	- 如果 Convincer 将答案确定为不正确，则来自“Convincer Output”的“Final Answer”被馈送到最后一步，即“Answer”。此步骤利用 Answerer 生成中间答案。最后，Normal CoT 将用于“完整”答案。随后，生成的答案经历“Introspect”步骤作为新迭代的“Normal Output”。
	- Convincer包括检测，分析，纠正：The Convincer possesses the ability to identify errors within the original reasoning path r, conduct an analysis of the mistakes, and subsequently rectify them. 定位到path中的第一个错误之后，截断后面的路径，专注于修正这个错误；
- Answerer模块：从上一个模型得到信息，进行单步推理；Taking the reasoning path derived from the *Convincer* module, denoted as ˆr, as its input, the Answerer produces **supplementary information**(such as "Type: Algebraic Equation") required to address the given question.