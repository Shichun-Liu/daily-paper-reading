2022.5，2022.10

> [!note] 一句话总结
> 给定一个基本的预训练语言模型和sequence-level oracle function（指示是否满足规则），通过训练辅助模型NADO，把序列级规则分解问token级指导，引导模型进行可控文本生成。

> Oracle Function：A function is a subprogram that is used to return a single value. [Site Unreachable](https://www.javatpoint.com/oracle-function)理解为一个0-1判别函数，相当于是reward model（基于规则）； 

文章主要方法是把一个sequence-level信号分解为token-level的guidance信号；
启发：我们的setting是序列决策，从最终的结果的reward信号，如何分解得到**过程**的reward信号；

## 核心

-  基于NeurAlly-Decomposed Oracle（NADO）提出了**可控的**自回归生成模型；
-  pre-trained base language model +  sequence-level boolean oracle function -> **oracle function** into token-level guidance to steer the base model in text generation；
-  token-level指导：从一个base model的数据中进行采样，训练了一个辅助模型NADO；
-  把可控生成问题定义为：基于后验正则化的优化问题。得到**解析最优解**，用来在token-level指导模型的可控生成；
-  对于NADO的近似的质量如何影响最终可控生成的结果进行分析，做了2个任务的实验：
	- text generation with lexical constraints 具有词汇约束的文本生成；
	- machine translation with formality control 带有形式控制的机器翻译；
![[Pasted image 20231211162242.png]]
左图：NADO的训练，从一个base model的生成中进行采样，由一个sequence-level的判别器产生监督信号；base-model在这个过程中不需要fine-tuning；

右图：decompose the sequence-level oracle into token-level guidance, such that when generating the i−th token in the output sequence given the prefix, instead of sampling from the base model, we modify the probability distribution of the output token based on the token-level guidance.
-  把sequence-level规则分解为**token-level的指导**，最终的每个token的生成由base-model + guidance的分布决定；
- base-model + guidance的过程后面再看；

## Intro
### 可控生成

- 要求模型的输出遵循sequence-level的属性：
	- 由一系列规则定义（譬如语法规则）；
	- 由某个抽象概念定义（譬如文风）；
-  现有工作
	- 基于搜索算法的词汇约束的算法，不能应用于风格写作任务；
	- 训练辅助模型（用来微调模型，或者需要外部标记数据），无理论保证，或者成本高；
	- 使用KL-adaptive分布策略来近似一个energy-based model；（粒度太粗？）
-  实验
	- 词汇约束生成 (LCG) 任务：oracle 是一个基于规则的关键字检查器；
	- 形式控制的机器翻译任务：提供了一个形式预言机来预测句子是否正式，目标是引导模型生成形式翻译；
- 后处理
	- 可控文本生成的方法归为三大类：fine-tuning, refactor/retraining and post-processing；
	- 后处理的主要步骤：修改decoding算法（如beam search），通过辅助模型指导生成；
	- 辅助模型：PPLM，GeDi，DEXPERTS，FUDGE，要么需要外部token-level oracle指导，要么需要辅助标记数据集来训练辅助模型；用于训练辅助模型的数据分布与所训练的模型的分布不同，导致生成质量下降；

## 方法

- 文章的主要方法就是提出NADO，这是一个近似的辅助模型，得到token-level的指导；
- we discuss 1) the formulation to decompose the sequence-level oracle function into token-level guidance; 2) the formulation to incorporate the token-level guidance into the base model to achieve control; 3) the approximation of the token-level guidance using NADO; 4) a theoretical analysis of the impact of NADO approximation to the controllable generation results; and 5) the training of NADO.
- 非常重要的部分！

### 概念

- base-model p；
- 指示函数C；
- 要得到一个token-level distribution $q^*(y_i|\mathbf{x}, \mathbf{y}_{<i})$，满足：
![[Pasted image 20231211180245.png]]
	辅助模型也是自回归model；辅助模型的结果和指示函数的结果一致；辅助模型与base model尽可能接近；

![[Pasted image 20231211180800.png]]
定义两个概率：
- 输入成功率：对于给定输入x，通过base model p得到的结果y最终满足指示函数的概率$R^C_p(x)$；
- 序列成功率：对于给定输入x以及部分序列$y_{<i}$，通过base model p得到结果y最终最终满足指示函数的概率$R^C_p(x,y_{<i})$;
### 解析解的给出

对于给定的x，定义一个sequence-level的分布Q满足指示函数引导的分布，所以得到$q^*$的解析解为：
![[Pasted image 20231211183547.png]]
为了理解这个式子，论文中没有给出过程或者解释，我这里给一个非常直观的例子：假设输入x可以通过p均匀分布得到y1, y2, .., y5，其中C(x, y1)=1, C(x, y2)=1, 其他都为0；那么q相当于是将概率分布聚焦于正例之上，而确保负例的输出概率为0（这是一个硬约束）；或者说是一个缩放
$$
q^*(y|x)=\frac{p(y|x)}{R^C_p(x)}
$$
接下来，把这个概率分布q唯一分解为token-level：
![[Pasted image 20231211191351.png]]
![[Pasted image 20231211191419.png]]
