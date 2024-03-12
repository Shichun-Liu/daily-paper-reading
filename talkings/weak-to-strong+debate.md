
![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240311131201.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240311131310.png)

## weak-to-strong 

可扩展监督关键思想：利用人工智能提升监督能力；
### IDA
迭代蒸馏放大

基本思想：利用人类（擅长推理）+计算机（擅长计算）的结合蒸馏给一个model，并不断迭代该过程；或者人类负责分解难题为子问题，模型负责回答较为简单的子问题，人类聚合子回答得到最终回答（比人类直接回答原问题更好）；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240311131534.png)


关键假设：人类可以协调X的多个副本，以比单个X的副本表现更好；
适用于人类本身可以回答的问题；

概念实验

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240311132100.png)


挑战

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305135600.png)

IDA的局限性

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305140119.png)

## Debate
任务难度

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305140354.png)

- 监督学习出来的模型一般而言无法超越人类标注者的水平；
- 类比计算复杂性理论，给出解法，给出判断，给出缺陷，判断缺陷；
- 指出缺陷和反驳的过程，可以看作是辩论；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305140735.png)

形式化表示

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305140902.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305141007.png)

有效性，把一个指数复杂度压缩为线性的路线，可以让人类监督引入；人类来判断缺陷（但可能不可靠，带有偏见）；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305141131.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305141404.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305141552.png)

局限性

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305141719.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305143532.png)

IDA的横向切分是由人类完成的，人类完成问题的分解；

AI 帮助人类进行监督

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305143913.png)

- 在模型给出批评的辅助下，人类判断结果有提升；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305144255.png)

## 论文

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305145322.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305145710.png)

任务
![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305145841.png)

直接微调，在NLP任务上比较直观；在下棋任务上，在教师和学生网络大小差不多的时候泛化性是较强的；reward model 任务上几乎没有产生泛化；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305150137.png)

鉴于下棋任务观察到的现象，设计一系列improve流程；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305150736.png)

如何阻止强模型一味模仿弱模型（包括一些错误）？置信度；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305151457.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305151801.png)

- 模仿问题：过拟合，使用early stop策略；student-supervisor agreement，二者的一致性，越强的学生，越难去模仿弱教师（可以发现老师的错误，对自己更确信）；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305152206.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305152403.png)

讨论

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305154342.png)

视角

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240305154655.png)

