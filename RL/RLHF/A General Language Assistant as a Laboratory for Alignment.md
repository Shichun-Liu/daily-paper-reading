---
tags:
  - Anthropic
  - 上下文蒸馏
  - 偏好学习
date: 2022-12-09
---
> [!abstract]
> 比较了模仿学习、binary discrimination、ranked preference modeling；
> 提出 LM Pre-training → Preference Model Pre-training(PMP) → Preference Model fine-tune 的流程；
> 提出上下文蒸馏；

- 对齐领域的早期工作；2021-12-09；
- 还不是日后的 RLHF / RLAIF。
- 适度干预的好处与模型规模正相关；
- 讨论了模仿学习、binary discrimination、ranked preference modeling。
- 发现 ranked preference modeling 显著优于模仿学习，二元判别通常表现和缩放与模仿学习非常相似。在偏好模型的建模上使用二元判别效果较好；
- 提出 LM Pre-training → Preference Model Pre-training(PMP)→Preference Model fine-tune 的流程。

[mp.weixin.qq.com/s/PpYa7ibXSP6CLghi4SmBMA](https://mp.weixin.qq.com/s/PpYa7ibXSP6CLghi4SmBMA)

## 主要内容

![image.png|625](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217164211.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217174322.png)

### 含义
- 定义：两个智能体的对于输出结果的排序**重叠程度**（overlap between the way two agents rank different outcomes）； 
- 目标：3H（helpful，honest，harmless），并给出了具体的要求与定义；
- 这些标准从数学上仍然不是客观的和不明确定义的，需要由部署AI模型的人来为其负责；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217164904.png)

### 语言模型HHH的评测
- 如何评价对齐？制作HHH数据集，进行A/B test

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217170335.png)

- 意义与重要性，为什么要统一baseline？；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217151235.png)

### 上下文蒸馏
- 何种对齐方式是最有效的？prompting / fine-tuning如何对齐；prompt提供了先验，fine-tune改变了数据分布；
- 上下文蒸馏：结合了两种方法；
	- 可以认为是通过prompt来过滤了一部分数据；
	- 比如我想要专注于helpful的数据，那么我就把这个要求输入到prompt中，结合原始dataset让LM生成得到helpful数据集；然后再将另一个LM在这个helpful数据集上面进行fine-tune（相当于蒸馏）；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217170707.png)

- 从专家示例中模仿学习；将专家的action作为当前state的label来进行监督学习；
	- [lamda.nju.edu.cn/xut/docs/Imitation\_Learning.pdf](https://www.lamda.nju.edu.cn/xut/docs/Imitation_Learning.pdf)
### 偏好模型
- 偏好模型就是一个打分模型；
- 更高效地利用珍贵的人类偏好数据：**用来微调偏好模型**，以泛化到更多的情况；后续实验验证了偏好模型的人类偏好的一致性较高；

Binary vs Rank-Ordered Preferences

![image.png|425](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217161521.png)

设定：两种数据，二元标签（好坏/true false），偏好排序，在偏好建模和模仿学习两种算法上的性能表现；
结论：Ranked Preference Modeling performs **much better** and scales somewhat better than imitation learning, but that binary discrimination does not.

### 评测
- zero-shot且没有prompt引导输出；统一测试；
- context-distilled方法和Full-prompt得分相近甚至更高；
- 在honesty上得分很高，但事实上是一本正经地胡说八道；

![image.png|575](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217171119.png)

对齐的taxes/bonuses

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217171907.png)
