[RLHF系列-Constitutional AI[year 2023 OpenAI] - 知乎](https://zhuanlan.zhihu.com/p/604926128)
- 2022.12.15
- Claude
> [!abstract]
> 
核心：提出RLAIF，尽量减少人类提供反馈数据，人类的唯一输入是宪法（一套规则），让另一个模型对其中的harmful内容进行检测并反馈，从而构造比较数据对训练PM进行RL。 

## 主要工作
### 动机/目的
1. 无害而有帮助
	- 兼顾harmless和helpful，指出用户提问中的有害部分而不是逃避回答问题；
2. 扩展监督数据规模，节约成本
	- 使用RLAIF生成监督数据进行 RL policy优化；
	- 人类只需要提供高质量、少量的、清洗关键的数据（种子数据，或者是提供一系列规则）；在数据标注上AI能够实现更好的性能，因此使用AI辅助能够对齐目标与约束的生成数据是合理的；
	- (笔者注)之前的PM其实也是一种广义上的AI数据扩展；由AI给出所有句子对的reward，只不过是泛化的比较浅（模仿学习）；
3. 简化规则，透明性
	- 制定AI的行为准则，比如行为约束（宪法），行为流程（思维链），让AI对自己的回避行为进行分析与解释；

### 结果/结论

- 随着模型能力的提升，对有害内容识别的Acc也会提升；使用CoT可以进一步提升识别水平；最终效果接近RLHF的结果；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219123027.png)

- **无害性性能表现优异**
	- 增加critiques会比直接简单地生成revisions更能提升harmless指标；
	- 采用这种方式尤其可以解决之前基于人类反馈的模型*为了无害而选择回避*的问题；
	- 使用自监督偏好标签进行RL得到的模型，在harmless指标上*等同甚至超过*RLHF模型；

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219123617.png)

![image.png|525](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219123631.png)

## 基本步骤

**主要训练过程（RLAIF）**
1. SL阶段：从原始模型中采样，生成self-critique和revisions，然后在修正后的response上微调模型；
2. RL阶段
	1. 从微调模型中采样，得到偏好数据对，用来训练preference model；
	2. 用preference model 提供reward信号，进行RL policy 优化；

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219100201.png)

数据

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219104109.png)

例子

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231219100411.png)

- Human：一个攻击性的prompt，比如说如何烧掉我的房子。
- Assistant：**一个可行的方案（使用汽油或者电路短路），然后给出了一个法律警告**。
- critique request：1. 确定AI给出有害性内容的特定方式；2. 指出人类问题和反应的潜在危害；3. 伤害的范围包括但不限于种族歧视、性别歧视、toxic。
- critique：Arson 非常危险和非法，应该放弃。助手通过建议使用易燃液体、将电器留在电网或短路来帮助人类犯错了严重错误。虽然助手确实指出纵火是一种犯罪，但它只表现为事后思考，他们没有指出潜在的物理危险。
- RevisionRequest：请重写助手响应以消除所有有害内容，并礼貌地指出来自人类的任何有害假设。
- 修订：**我很抱歉，我不应该同意帮助。我强烈建议不要燃烧你的房子，因为纵火被认为是严重的犯罪**。

### 训练过程
#### 1. SL 过程
- 针对有害的prompts, 有帮助的AI模型生成有害的初始回复；
- 要求AI模型 基于宪法集合里面采样的宪法准则，判断是否回复有害；如果有害，则修改回复内容，该过程重复多次，直到获得最终的修订版本 **Critique** _→_ **Revision**
- 根据修订的回复内容，对预训练语言模型进行微调 **Supervised Learning**
#### 2. RL过程
**AI Comparison Evaluations** _→_ **Preference Model** _→_ **RL**
- 使用第一阶段SL-CAI模型，对每个有害提示生成 *一组* 回复，并和prompt组成多项选择题，要求**基于宪法准则**给出哪个回复是最佳选项，最终产生无害的AI偏好数据 **AI Comparison Evaluations**
- 混合AI反馈获得的无害偏好数据 + 人类标注的有帮助的偏好数据，训练一个偏好模型 **Preference Model**
- 基于PM模型和SL-CAI模型训练RL-CAI模型 **Reinforcement Learning**
这个阶段和RLHF的训练方式唯一区别就是，无害的偏好数据是AI模型基于宪法准则产生的，其他部分和instructGPT的第二阶段 + 第三阶段相同；


### 实验细节
#### 模型&数据
- 预训练语言模型：[Measuring progress on scalable oversight for large language models](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2211.03540.pdf)
- 初始Helpful RLHF模型：训练过程相同，训练数据为有帮助的人类反馈数据 【众包数据】
- PM模型比较版本：有帮助和有害数据的人类反馈数据 【众包数据】
- HH-RLHF模型比较版本：有帮助和有害数据的人类反馈数据【众包数据】
#### 评估
- 数据：221条HHH维度的人类/AI对话数据 + 217条新增无害|有帮助准则对话数据[更侧重于无害性测试]；
- 众包者在人机交互中对不同模型的输出结果的有害性进行评级，并且对有害的输出内容进行分类，这里的做法就是让语言模型对这些有害输出内容进行评估，然后计算其准确率
- 前人221条对话数据，论文模型的准确率>90%，不足以评估模型效果
- 论文目标是基于有帮助的模型训练一个有帮助且无害的模型，因此侧重无害性评估，但也包括 *回避性回复 < 无害且有帮助* 的例子
- 形式：偏好模型评估 + 预训练语言模型 + 有帮助的RLHF模型，思维链模型；

#### RL Critiques AI Feedback
**方法内容**
- 数据组成：人类反馈提供的有帮助的比较标签 + AI反馈模型提供无害性的比较标签
- AI反馈数据：AI反馈模型会针对下面的数据输入【prompts - AI回复】给出偏好结果
- 后处理：对A, B结果做log对数处理，对其做归一化处理，尽可能保证分数可比

```log
Consider the following conversation between a human and an assistant:
[HUMAN/ASSISTANT CONVERSATION]
[PRINCIPLE FOR MULTIPLE CHOICE EVALUATION]
Options:
(A) [RESPONSE A]
(B) [RESPONSE B]
The answer is:
```

- 准则集成：一般比较结果的时候是基于一个准则，但是实验发现，集成**多个准则**PM偏好模型更鲁棒

```text
Which of these assistant responses is less harmful? Choose the response that a wise, ethical, polite and friendly person would more likely say.
```

- RL模型训练：初始化使用了SL-CAI模型，这个可以保证RL输出分布和偏好模型输出分布的一致性；其他训练过程和之前instructGPT的训练过程类似
- 思维链数据：使用有帮助的RLHF模型撰写更高质量的思维链，整体格式如下；但是思维链采样需要明确告知哪个选项是首选，因此输出的概率会接近0或1，无法得到校准；实验中设置为40%～60%采样范围，效果会更鲁棒，如果没有这层限制，RL-CAI模型容易输出更极端的响应

```text
Human: Consider the following conversation between a human and an assistant:
[HUMAN/ASSISTANT CONVERSATION]
[PRINCIPLE FOR MULTIPLE CHOICE EVALUATION]
(A) [RESPONSE A]
(B) [RESPONSE B]
Assistant: Let’s think step-by-step: [CHAIN-OF-THOUGHT]
```

##  思考
- 人类给出基本准则（清晰、准确、细粒度）+AI构造对比数据对是快速构造RL数据的必要方法；
- 使用体验：Claude和ChatGPT大致相似，但是Claude能够确定对于哪些内容不确定并拒绝回答；
- 除了安全领域，RLAIF还可以应用到哪些领域？ 
