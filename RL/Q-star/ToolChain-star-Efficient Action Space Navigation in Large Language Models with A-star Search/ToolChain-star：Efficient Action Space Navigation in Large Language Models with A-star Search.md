---
tags:
  - A-star
date: 2023-10-20
---
- 如何在大量工具库中检索到目标工具？
- 目前方法：贪心搜索，只能找到局部最优解；或者搜索效率太低；
- ToolChain*，基于树搜索的高效规划算法（search-based planning algorithm）；将整个决策空间表示为决策树，每个节点代表一个API调用；
- 将A\*算法与成本函数相结合（具体形式是什么？），找出成本最低的有效路径；
- 平衡了探索和利用，性能和速度均为sota；

## intro
- 单向的思维链可能导致错误传播，以及搜索的有限性；
- 树搜索的方法（ToT/MCTS）需要对于决策空间中几乎所有潜在的动作进行探索，才能高效搜索全局最优解；
- 为工具使用规划过程制定为决策树，其中每个节点代表给定步骤的潜在 API 调用。
- 与传统的 A∗ 搜索算法一致，所提出的 ToolChain∗ 根据**当前路径的成本**和完成当前计划所需的**估计未来成本来**确定要扩展哪些路径。通过特定于任务的成本函数，错误的动作将受到惩罚和缓解，因为这些动作会在沿路径传播时造成额外的成本，leading the path to be progressively de-prioritized and left unexpanded over iterations. 此外，与 MCTS 中的模拟阶段不同（需要多个步骤来模拟直到推出期间的终端状态），ToolChain∗ 中的未来成本估计**只扩展下一步**。通过高效的节点扩展，ToolChain∗ 在可管理数量的步骤中有效地搜索全局最优解。
- 使用A\*算法结合两个cost function：g(n), h(n)；（h如何**确定**？什么形式？这是核心，这给出了监督信号）

## 方法
ToolChain∗ is a **best-first search** algorithm, efficiently guiding LLM agents in generating a sequence of API function calls as a solution plan.

![image.png|675](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240103161836.png)

### 算法

![image.png|300](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240103162159.png)

- Selection：选择成本最低的node；value function f(n)；
- Expansion：确定了node n，进一步拓展k个调用动作，由LLM生成（给出API定义和Demo）；对于不同的action 分别建立对应的node；
- Update：更新边界节点以及对应的成本函数；
- **Cost function**：f (n) = g(n) + h(n)，g(n) represents the cost of the path **from the start node to $n$**, and h(n) is a heuristic function estimating the cost of **the cheapest path from $n$ to the goal**.
#### g(n)设计
对于搜索树中的每个节点 n，我们设计了一个从 0 到 1 的单步值函数 $g_t(n)$，并将成本表示为其补码 $1 - g_t(n)$ 。因此，n 的累积成本可以通过对其祖先节点 $an(n)$ 的所有单步成本求和来计算：$g(n) = \sum_{i∈an(n)} 1 − g_t(i)$。更具体地说，我们结合了两个不同的值函数，the task-specific heuristic function from reference data **(long-term memory)** $g_{t,1}(n)$ 和the self-consistency frequency by LLM $g_{t,2}(n)$;

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240103163658.png)

**Task-Specific Heuristic Function $g_{t,1}(n)$.**
- 有一个plan dataset作为”经验“，然后把当前的plan表示为序列方式与经验库中所有的plan依次取**最长公共子序列 (LCS) 分数**，再取最高者，作为这部分的值。

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240103164115.png)

**Self-consistency Frequency $g_{t,2}(n)$.**
一种集成方法，它对下一步的动作进行采样。然后，我们从 k 个生成的样本中选择**语义上不同**的动作作为潜在下一步的集合（用DeBERTa-large来判断两个动作在语义上是否冗余）；将不同动作的个数/k作为这一部分的分数；
![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240103164544.png)


#### h(n)设计
也是两部分构成，一部分是任务相关，一部分是LLM打分

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240103165326.png)

第一部分类似，是根据检索经验库，然后对平均位置进行计算（相当于这步在经验上越靠后，那么里目标点就越近）

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240103165546.png)

第二部分是由LLM对于这个节点离目标还剩多少节点进行计算，具体看图

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240103165840.png)

