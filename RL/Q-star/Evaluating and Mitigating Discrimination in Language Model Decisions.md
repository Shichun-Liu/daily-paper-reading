---
tags:
  - harmless
  - Anthropic
date: 2023-12-06
---
[[2312.03689] Evaluating and Mitigating Discrimination in Language Model Decisions](https://arxiv.org/abs/2312.03689)
[Anthropic/discrim-eval · Datasets at Hugging Face](https://huggingface.co/datasets/Anthropic/discrim-eval)
[Anthropic \\ Evaluating and Mitigating Discrimination in Language…](https://www.anthropic.com/index/evaluating-and-mitigating-discrimination-in-language-model-decisions)
[Evaluating and Mitigating Discrimination in Language Model Decisions Policy Memo](https://www-files.anthropic.com/production/images/Anthropic_DiscriminationEval.pdf?dm=1701894346)

## 主要内容
- 评估LM在各种场景中可能产生的歧视性影响；
- 使用简单的**prompt技巧**可以**显著减少**高风险决策中存在的歧视；
- 需要有工具来**衡量**并**减轻**使用LM带入的潜在歧视；发布了评估的代码。
### 评测歧视现象
- 为了评估歧视情况，我们使用 LM 生成各种决策情景，并在每个提示中系统地改变**关键人口信息**（key demographic information）。我们采用了三步流程来建立评估。
	- 首先，我们生成了一系列不同的社会决策场景（例如，是否延长工作机会、支付保险理赔金或发放工作签证）。
	- 接着，我们生成了问题模板，并为假定的个人信息（例如，是否需要提供工作机会、是否需要支付保险金、是否需要发放工作签证等）设置了占位符。
	- 最后，我们创建了多个版本的相同段落式情景模板，但修改了年龄、种族和性别方面的部分个人信息变量。
- 通过这种评估，我们可以在所有其他信息保持不变的情况下，衡量不同人口统计学特征在决策结果上的差异，从而量化辨别能力。为了确保 LM 生成的决策情景具有足够高的质量，我们进行了一项单独的研究，由人类对适当的模板进行审查和验证。参与者之间的高度一致证明，这种模板生成方法可以可靠地生成大量高质量、真实和多样化的决策场景。

### 缓解歧视现象
通过简单的prompt策略可以有效改进歧视性输出。
- **Appending statements** to decision questions instructing a model to **ensure its answer is unbiased**；显式加入避免歧视的陈述；
- Inserting requests to **articulate the rationale behind a decision** while avoiding bias and stereotypes；为决策解释原因；
- Asking the model to answer the decision question as if **no demographic information** was provided；要求模型在忽略人口信息的情况下做决策；

效果非常显著
![image.png|421](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226163133.png)

