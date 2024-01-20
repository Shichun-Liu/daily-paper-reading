- [GitHub - AGI-Edgerunners/LLM-Planning-Papers: Must-read Papers on Large Language Model (LLM) Planning.](https://github.com/AGI-Edgerunners/LLM-Planning-Papers)
- [GitHub - atfortes/LLM-Reasoning-Papers: Collection of papers and resources on Reasoning in Large Language Models (LLMs), including Chain-of-Thought (CoT), Instruction-Tuning, and others.](https://github.com/atfortes/LLM-Reasoning-Papers)

后续工作迁移至飞书文档：[飞书 - 登录](https://fudannlp.feishu.cn/docx/S4Y3dlY9soSZXDx0TAyc9IwXnQb)
## Hierarchies/Meta-learning

### RL+Hierarchies
- 传统的多层次RL训练通常被运用于**机器人**任务上负责**多个子任务**的学习与泛化，应用于元学习领域；   
- [[1710.09767] Meta Learning Shared Hierarchies]([https://arxiv.org/abs/1710.09767](https://arxiv.org/abs/1710.09767))
- [[1812.00025] Modulated Policy Hierarchies]([https://arxiv.org/abs/1812.00025](https://arxiv.org/abs/1812.00025))
- [[2002.05954] Learning Functionally Decomposed Hierarchies for Continuous Control Tasks with Path Planning]([https://arxiv.org/abs/2002.05954](https://arxiv.org/abs/2002.05954))
- [[Q-Transformer：Scalable Offline Reinforcement Learning via Autoregressive Q-Functions]]
    
  
**启发**：把推理的整个过程，拆解成几个**基本动作**（如推理预测/评估/验证）；现有的算法通常是固定的若干个action以一定顺序依次执行；那么我们能否训练一个master policy，自主决策action选择；
- 训练LLM+RL master policy的层次policy（7b模型）；
- 采用task-specific LLM（~70b量级）分别处理辅助推理任务的action，比如reflexion中的各个组件；
- 这些动作之间的地位如何？平级的？reward怎么定义？（暂时不考虑）
- 任务选择什么？经典的planning，math，HotPotQA问答，PrOntoQA逻辑推理；可以参考[Benchmark](https://github.com/atfortes/LLM-Reasoning-Papers?tab=readme-ov-file#benchmark)
    
## Math
- 专指算术/代数，以GSM8K/MATH为例；
- math的困难在于根本没法（难以）把规则全部表示出来然后使用专门的推理器（在符号逻辑/planning领域有专门的推理器），只能让LLM来完成基本的推理动作；
- 基于搜索的推理（不管是ToT-BFS/DFS/MCTS）并不严格可靠，有可能找不到有效路径，因为生成节点的方向/准确性是不保证的（由LLM完成）；
- 两个极端：推理规则完全确定，好做（下面的PDDL）；完全没有推理规则（或者说规则太过于庞杂）：数学问题，不好做；
- 围棋问题属于是完全基于搜索的算法，好在情景比较单一，没有推理可言就是纯搜索；
    

#### 论文

- MathPrompter：Mathematical Reasoning using Large Language Models
    - [MathPrompter: Mathematical Reasoning using Large Language Models](https://arxiv.org/abs/2303.05398)
    - 设计了两种prompt（数学方程和Python函数）来引导语言模型进行"思考"，并且这两种结果可以交叉验证。一方面预设了中间步骤，同时通过带入不同的数值测试进一步避免了中间步骤正确但计算结果错误。
- Learning From Mistakes Makes LLM Better Reasoner
    - GSM8k/MATH；
    - 从错误中学习(LEMA)；
    - GPT-4生成的错误纠正数据对对llm进行微调。具体来说，我们首先从各种llm中收集不准确的推理路径，然后使用GPT-4作为"纠错器"来(1)识别错误步骤，(2)解释错误原因，(3)纠正错误并生成最终答案；
- Teaching Language Models to Self-Improve through Interactive Demonstrations
    - 和上一篇工作类似；
    - 一种训练算法 TRIPOST，它能更有效地训练一个小型模型，使其从错误中学习，产生反馈，并提高其在数学和推理任务中的表现。
    - TRIPOST 是一种迭代算法，由三个阶段组成： 主动轨迹编辑（Inter- active Trajectory Editing）、数据后处理（Data Post-processing）和模型训练（Model Training）。
    - 与强化学习中的探索阶段类似，TRIPOST 首先使用小模型创建改进示范，与专家 LLM 或相关 Python 脚本进行交互。然后，TRIPOST 对收集到的数据进行后处理，过滤掉失败的改进尝试，然后重新平衡数据集，使模型即使在尝试已经正确的情况下也不再尝试 "改进"。最后，TRIPOST 重放处理后的数据集。
- Improving Large Language Model Fine-tuning for Solving Math Problems
    - [论文分享：Improving Large Language Model Fine-tuning For Solving Math Problems - 知乎](https://zhuanlan.zhihu.com/p/667243909)
    - 作者指出这样一个现象：针对一个数学问题，PaLM2一次输出正确答案（Pass@1）的概率是很低的，但当其重复输出64次（在较高的温度设定下，Pass@64），命中正确答案的概率提升到67.8%。这就说明：PaLM2有能力生成正确答案，但其无法判断生成的答案是否正确。
        
## **Planning/Reasoning**

大多都是Actor-Critic范式；新一点的是和准确求解器结合；这里面基本上是以LLM作为推理器，更加general一些。

- Logic-LM: Empowering Large Language Models with Symbolic Solvers for Faithful Logical Reasoning.
    - 将 LLM 与**符号求解器**整合在一起，以改进逻辑问题的解决。
    - 首先利用 LLM 将自然语言问题转化为符号表述。然后，一个确定性的符号求解器对所表述的问题进行推理。我们还引入了一个自我完善模块，利用符号求解器的错误信息来修改符号化。
    - 在五个逻辑推理数据集上演示了 LOGIC-LM 的有效性：ProofWriter、PrOntoQA、FOLIO、LogicalDe- duction 和 AR-LSAT。
- Chain-of-Verification Reduces Hallucination in Large Language Models
    - 研究语言模型思考自己给出的答案，从而纠正自己的错误的能力。引入了**验证链**( Chain-of-Verification，CoVe )，一种在大型语言模型中通过考虑自身的响应并自我修正来减少幻觉的方法。
- Question Decomposition Improves the Faithfulness of Model-Generated Reasoning
    - [衡量和改善大模型生成的推理的忠实度 - 知乎](https://zhuanlan.zhihu.com/p/648216748)
    - **思维链分解和因子分解**。思维链分解类似于思维链提示，不同之处在于推理明确格式化为"子问题"和"子答案"的列表。因子分解也使用子问题和子答案，但是在**隔离的上下文中**提示模型回答子问题，以限制模型在一个上下文中进行全部推理时可能产生的有偏见推理量。
    - 因子分解也可以缓解有偏见的推理问题，我们使用[Turpin et al. (2023)](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2305.04388)提出的实验进行测试。具体而言，当我们提示模型执行因子分解而不是思维链或思维链分解时，在有偏见的情境下，模型的性能下降较少。
- REFINER: Reasoning Feedback on Intermediate Representations
    - 用于微调LM以在与提供自动反馈的评论模型交互时明确生成中间推理步骤；
        
