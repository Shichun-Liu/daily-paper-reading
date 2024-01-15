[Site Unreachable](https://deepmind.google/discover/blog/funsearch-making-new-discoveries-in-mathematical-sciences-using-large-language-models/)
[Mathematical discoveries from program search with large language models | Nature](https://www.nature.com/articles/s41586-023-06924-6)

## Blog部分
> By searching for “functions” written in computer code, FunSearch made the first discoveries in open problems in mathematical sciences using LLMs 

- FunSearch 的工作原理是将预先训练好的 LLM 与自动 "evaluator "配对使用，前者的目标是以计算机代码的形式提供创造性的解决方案，后者则负责防止出现幻觉和错误的想法。通过这两个组件之间的来回迭代，初始解决方案 "进化 "为新知识。该系统搜索用计算机代码编写的 "函数"，因此被称为 FunSearch。
	- 有点类似actor-critic，一个生成策略，一个评估好坏；
- 发现了上限集问题（cap set problem）的一个新解决方案；发现了bin-packing问题的一个更高效的算法；
- 还输出了提出解决方案的过程；

### 主要算法
LLM+**进化**算法；
#### cap set 问题
[Cap set - Wikipedia](https://en.wikipedia.org/wiki/Cap_set)
- FunSearch 采用由 LLM 支持的**进化方法**，促进和发展得分最高的创意。这些想法以计算机程序的形式表达，因此可以自动运行和评估。首先，用户**以代码的形式**对问题进行描述。该描述包括一个**用于评估程序的程序**（a procedure to evaluate programs），以及一个用于初始化程序池的种子程序。
	- 本质上提供了一个对于中间过程的评估器；（这个评估器如何设计比较关键，我本来的想法就是对于某一个特定的数学任务提供细粒度的PRM，要是可以直接通过规则进行量化那就解决了）
- FunSearch 是一种迭代程序；每次迭代时，系统都会从当前程序库中选择一些程序，并将其输入 LLM。LLM 在此基础上创造性地生成新程序，并**自动对其进行评估**。最好的程序会被添加回现有程序库，形成一个自我完善的循环。FunSearch 使用的是谷歌的 PaLM 2，但它也兼容其他经过代码训练的 LLM。

![image.png|575](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225161616.png)

- 为了利用 FunSearch 解决此类难题，我们引入了多个关键组件。我们不是从零开始，而是**从有关问题的常识**开始进化过程，让 FunSearch 专注于寻找最关键的想法，以实现新的发现。
- 我们的进化过程还使用一种策略来提高想法的**多样性**，以避免停滞不前。
- 最后，我们**并行运行**进化过程，以提高系统效率。

### 实验
**过程示例**
![image.png|675](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225162515.png)

![image.png|675](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225162356.png)

上图显示了从种子程序（上）到新的高分函数（下）的演变。每个圆圈都是一个程序，其大小与分配给它的分数成正比。仅显示底部程序的祖先。

#### bin-packing问题
- FunSearch 提供了一个自动定制的程序（适应数据的具体情况），其性能优于既定的启发式方法；
- FunSearch擅长组合类型的题目；用于设计启发式的算法；

### 结论
FunSearch倾向于简洁、易于人类理解的程序；
- FunSearch的树结构可以用来说明得到最终程序的**过程**（思路）；
- FunSearch 更倾向于寻找**高度紧凑**的程序所代表的解决方案，即具有较低 Kolmogorov 复杂性†的解决方案；

## 论文
- 数学中很多问题难以解决，但是易于**评估**（easy to evaluation）；
	- 比如NP-hard在多项式时间内不能**求解**，但是可以**验证**；
- We focus in this paper on problems admitting **an efficient evaluate function**, which measures the quality of a candidate solution. Prominent examples include the maximum independent set problem and maximum constraint satisfactionproblems (such as finding the ground state energy of a Hamiltonian).
	- 先要有一个良好的评估器，一切才好说；
- 评估函数内容如下

![image.png|700](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225190621.png)

- 具体而言，评估函数就是返回算法找到的cap set的**大小**，越大分数越高；非常简单、直接了当；

- FunSearch 确实得到了新算法，这是不同于之前的所有的算法的（说明不是基于检索的方式）；通过该算法在n=8维度上得到了比之前已知的更大的cap set；此外，我们不仅发现了 512 个 8 维向量本身，还发现了一个生成它的程序；

![image.png|700](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225194654.png)

- FunSearch (short for searching in the function space) combines a pre-trained (frozen) **Large Language Model**, whose goal is to provide creative solutions, with an **evaluator**, which guards against confabulations and incorrect ideas. 
- 迭代进化：FunSearch iterates over these two components, evolving initiallow-scoring programs into high-scoring ones discovering new knowledge. Key to the success of this simple procedure is a combination of multiple essential ingredients. 
- 主要流程
	- First, we sample best performing programs and feed them back into prompts for the LLM to improve on; we refer to this as best-shot prompting. 
	- Second, we start with a program in the form of a skeleton (containing boilerplate code and potentially prior structure about the problem), and **only evolve the part governing the critical program logic**. For example, by setting a greedy program skeleton, we evolve a priority function used to make decisions at every step. 
	- Third, we maintain a large pool of diverse programs by usingan **island-based evolutionary method**（用于**并行化**） that encourages exploration and avoids local optima. 
	- Finally, leveraging the highly parallel nature of FunSearch, we scale it asynchronously, considerably broadening the scope of this approach to find new results, while keeping the overall cost of experiment slow.

### 算法细节
**问题说明**
- 用evaluator本身来定义数学问题：The input to FunSearch is a specification of the problem **in the form of an evaluate function**, which scores candidate solutions.
- 还要提供一个初始程序用来进化（可能是非常差的），相当于提供一个框架，可以显著提高输出效果；

**LLM** 
- 实验中使用的LLM是PaLM2模型家族下的Codey，没有经过额外微调；
- 为了更快得到结果，使用的采样数量级为10^6;

**评测**
- LLM生成的程序会被输入进一组inputs并进行评估；最终分数是所有输出的分数的平均；不正确的code会被丢弃，剩余的会被放入program databases；

**程序数据库 program database**
- 程序数据库保留了一组正确的程序，然后对其进行采样以创建prompt；
- 保证多样性，建立了岛屿模型（**islands model**, also known as **multiple population and multiple-deme model**），这是一种遗传算法；
	- 为了从程序数据库中采样，我们首先对岛进行采样，然后在该岛内对程序进行采样，有利于更高评分和更短的程序（Methods for the exact mechanism）。
	- 我们通过定期丢弃岛屿**后半部分**的程序（对应于得分最低的程序）来让信息流通。
	- 我们用一个新的种群替换这些岛屿中的程序：通过从幸存的岛屿中克隆最佳个体。

**Prompt**
- 使用prompt形式来进行crossover；
- 从程序数据库的每个岛中挑选top k 程序，k=2；通过结合多个程序来构造prompt，让LLM能在不同的程序之间发现模式，泛化这些模型。

**分布式方法**
将 FunSearch 作为一个分布式系统来实现，该系统有三类对象：程序数据库、采样器和评估器，它们进行异步通信。程序数据库存储并提供程序，采样器使用预先训练好的 LLM 生成新函数，而评估器则对程序进行评估。


## 讨论
FunSearch的核心
- LLM用来在目前最好code的基础上拓展（增加多样性，变异）；
- 不是直接搜索数学问题的答案，而是去生成求解该数学问题的算法程序；越**简洁**的程序所蕴含的情况越多；
	- 这种柯尔莫哥洛夫压缩的归纳偏差是 FunSearch 扩展到我们用例中的大型实例的关键；
	- 输出程序更具有可解释性；
- FunSearch 目前最适合具有**以下特征**的问题：a）有效评估器的可用性； b) 量化改进的“丰富”评分反馈（与01信号相反）； c) 为骨架提供待进化的孤立部分的能力。
	- 例如，生成**定理证明**的问题**不属于**这个范围，因为尚不清楚如何提供足够丰富的评分信号。
	- 相反，对于 MAX-SAT，满足条款的数量可以用作评分信号。在本文中，我们明确追求简单性，并且我们相信 FunSearch 可以进一步扩展以提高其性能并适用于更多类别的问题。