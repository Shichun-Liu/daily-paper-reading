
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
- FunSearch 采用由 LLM 支持的**进化方法**，促进和发展得分最高的创意。这些想法以计算机程序的形式表达，因此可以自动运行和评估。首先，用户**以代码的形式**对问题进行描述。该描述包括一个**用于评估程序的程序**（a procedure to evaluate programs），以及一个用于初始化程序池的种子程序。
	- 本质上提供了一个对于中间过程的评估器；（这个评估器如何设计比较关键，我本来的想法就是对于某一个特定的数学任务提供细粒度的PRM，要是可以直接通过规则进行量化那就解决了）
- FunSearch 是一种迭代程序；每次迭代时，系统都会从当前程序库中选择一些程序，并将其输入 LLM。LLM 在此基础上创造性地生成新程序，并**自动对其进行评估**。最好的程序会被添加回现有程序库，形成一个自我完善的循环。FunSearch 使用的是谷歌的 PaLM 2，但它也兼容其他经过代码训练的 LLM。

![image.png|575](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225161616.png)

- 为了利用 FunSearch 解决此类难题，我们引入了多个关键组件。我们不是从零开始，而是**从有关问题的常识**开始进化过程，让 FunSearch 专注于寻找最关键的想法，以实现新的发现。
- 我们的进化过程还使用一种策略来提高想法的**多样性**，以避免停滞不前。
- 最后，我们**并行运行**进化过程，以提高系统效率。

### 实验
[Cap set - Wikipedia](https://en.wikipedia.org/wiki/Cap_set)

![image.png|675](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225162515.png)

![image.png|675](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231225162356.png)

上图显示了从种子程序（上）到新的高分函数（下）的演变。每个圆圈都是一个程序，其大小与分配给它的分数成正比。仅显示底部程序的祖先。

