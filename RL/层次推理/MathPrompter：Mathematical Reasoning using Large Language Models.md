[[2303.05398] MathPrompter: Mathematical Reasoning using Large Language Models](https://arxiv.org/abs/2303.05398)
[MathPrompter: 语言模型也能解数学题了！ - 知乎](https://zhuanlan.zhihu.com/p/615287237)
## 1. 背景
语言模型在算数学上有两个主要挑战：
1. 无法验证中间步骤是正确的
2. 无法给出返回答案的置信度

## 2. 方法
想象一下人在算数学题的时候，往往会将一个问题**拆分成多个步骤**，并且对每个步骤都会**进行校验**。基于此，作者通过巧妙地设计prompt来诱导语言模型。这里有两个设定：
- 仅仅调整prompt，而不调整语言模型本身
- zero-shot，在prompt中不会给例子

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240115160859.png)

MathPrompter流程共分成4个步骤:

(1) **生成数学模版**

![](https://pic4.zhimg.com/80/v2-e99bedf343afd331eaf5f076d5845027_1440w.webp)

如图所示，将输入问题中的数字均替换成变量（例如A，B，C）并记下变量与真实值的映射关系。

**(2) 数学提示**
进一步让语言模型将输入的问题改写成“数学方程”和“Python函数”两种形式。这里提示为“写一个数学方程”或者“写一个Python函数”，这样语言模型就可以生成相应的数学方程和Python函数。可以认为这里生成了两种不同的中间步骤。

![](https://pic2.zhimg.com/80/v2-0aa0be84c6a51814c04eb16254890c5d_1440w.webp)

**(3) 计算验证**
通过给变量赋与不同的值计算“数学方程”和“Python函数”的结果。这里“数学方程”直接用`eval()`这个Python函数让语言模型进行计算。再验证“数学方程”和“Python函数”的结果是否一致。

**(4) 统计学意义**
- 如果尝试不同的变量组合5次，如果步骤(3)中的答案都是一致，那么说明**中间步骤和中间步骤的结果都是正确**的，可以直接带入原始的题目的数值进行计算并返回结果。
- 如果不一致，那么重新到步骤(2)生成新的“数学等式”和“Python函数”，并继续重复步骤(3),(4)。


## 3. 结果

![](https://pic2.zhimg.com/80/v2-d6658c5cad95b7173806da321ce88e59_720w.webp)

在MultiArith数据集上的结果，MathPrompter的表现优于所有的Zero-shot的baseline，并将准确率**从78.7% 大幅提升到 92.5%**。同时，作者采用的“175B参数模型且zero-shot“的性能也与“540B参数模型且few-shot”的效果相当。

