---
tags:
  - reasonging
date: 2023-08-25
---
- 提出 Cumulative Reasoning (CR) 方法，该方法以累积和迭代的方式来模拟人类思维过程。
- 将任务分解；
- 数据集：逻辑推理（FOLIO wiki），24点（达到了98%），MATH；

## 方法

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240121145153.png)

交替进行推理和验证，保留所有可行的推理路线（比如产生的中间过程c）；
At first glance, CR is similar to the ToT, which solves the problems with a thought search tree (Yao et al., 2023; Long, 2023). However, our method is **more general** in the sense that it **stores all the historical** correct reasoning results in memory, which can be a DAG.

## 实验
- Proposer、Verifier(s)和Reporter使用相同的LLM实现，具有不同的few-shot prompt；n 表示为生成的中间命题的数量，k 表示多数投票次数。我们默认设置温度 t = 0.1，多数投票设置为 t = 0.7。我们还注意到 gpt-3.5-turbo 和 gpt-4 都作为 OpenAI 的聊天格式 API 运行。

### 数据集
#### 逻辑推理
- FOLIO wiki：first-order logical inference。1435 个示例

![image.png|425](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240121151512.png)

（吐槽：为什么有了acc还要有error，这不是纯在水？）

![image.png|550](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240121152659.png)

（这还是数据清理之前的结果，后面还有一个清理了脏数据之后的结果，准确率提升到98%，这又在水了）

#### 24点
- 同ToT相对比，采用相同的实验设置；
Settings and Baselines. To ensure fairness, we adopt exactly identical task settings as Tree of Thoughts (ToT) (Yao et al., 2023) on Game of 24. We use the set of 100 Games of 24 collected by Yao et al. (2023) which was been used to evaluate the performance of ToT. In each game, we consider the game to be successfully solved if and only if the output is a valid equation that reaches 24 and only uses given numbers each exactly once. We quantify the accuracy (success rate) across 100 games as a main evaluative metric. 
In this experiment, we compare CR with variant prompt algorithms, including standard Input-Output prompting (Direct), Chain-of-Thought prompting (CoT), and CoT-SC by aggregating the majority outcome from 100 sampled CoT trials (designated as k = 100), and Tree of Thoughts (ToT) with a breadth-first search width set at 5 (indicated as b = 5).

我们维护一组“到达状态”，用 S 表示。最初，S 仅包含起始状态 s，它表示 4 个输入数字，没有任何操作。在每次迭代中，从 S 中随机选择一个状态 u。这个选定的状态 u 被传递到 Proposer，它在 u 中随机选择两个剩余的数字，并通过基本的算术运算 (+-\*/) 将它们组合起来以获得一个新的数字，从而生成一个新的状态 v。Proposer 被指示尝试避免采取重复操作。随后，验证者仔细检查 Proposer 提出的算术运算并评估新生成的状态 v。**如果验证者认为从 u 到 v 的操作是合法的，并且 v 有可能实现 24，则 v 被插入到 S 中**。在验证者识别明确 24 的状态 t 后，报告者根据从状态 s 到状态 t 的路径设计解决方案并产生最终答案。当 Reporter 输出最终答案或迭代次数超过 L 的极限时，算法终止。在实验中，我们将 L 的默认值设置为 50。

在 Game of 24 的背景下，我们的 CR 算法和 ToT 算法非常相似。他们的主要区别是，在 CR 中，算法的每次迭代**最多生成一个新到达的状态**，而 ToT 每次迭代都会生成大量候选状态、过滤和保留状态子集。这意味着与 CR 相比，ToT 探索了更多的无效状态。此外，ToT 采用固定宽度和固定深度搜索树，而 CR 允许 LLM 自主确定搜索深度，并在搜索树的不同层上执行不同的搜索宽度。

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240121153449.png)

#### MATH 
实验设置
It is important to note that for our method (CR), we employed **4-shot prompting** (4 examples for few-shot prompting) due to **GPT-4**'s context length constraints (8k by default). While the model occasionally exceeds the context length with 8-shot prompting, it generally demonstrates superior performance. Future experiments will explore the utilization of GPT-4-32k.

MATH结果

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240121153709.png)


流程示例

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240121153843.png)

CR will first generate **a few hints**, then several simple and foundational questions, and then answer them by self, and finally conclude with the help of the generated hints and question-answer pairs.