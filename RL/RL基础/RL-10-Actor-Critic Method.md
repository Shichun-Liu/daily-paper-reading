[第10课-Actor-Critic方法（最简单的Actor-Critic (QAC)）\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1sd4y167NS?p=52&spm_id_from=pageDriver&vd_source=3f4448c688c0096124dbfa48b0a085c3)

最后一节课；最流行的方法之一；
把value的方法引入到policy gradient中；

## 引入
- actor，critic，分别对应于policy update和policy evaluation/value-estimate；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216184458.png)

主要内容

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216184722.png)

## AC基础

之前讲到计算$q_t$的值有两种方法：
- MC计算，REINFORCE方法，或者叫MC policy gradient； 
- TD方法，叫做actor-critic方法；

### 基础算法 QAC

![image.png|525](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216202903.png)

## A2C
- 又称为TD actor-crtic；
- 是QAC的推广；
- 在QAC中引入bias（改变baseline）来消除方差；b(S)是状态的函数；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216203510.png)

已经计算得到目标函数的期望加上了bias之后是没有变化，但是方差会变化；
目标：找一组baseline，使得方差最小化；
好处：对于方差小的随机变量进行采样，得到的值更偏向于expectation；
取值：

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216211550.png)

把 b设为state-value；那么得到差值（增量）表示，advantage function；描述这个action对于均值的增量（好了还是坏了）；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216212011.png)

变形公式

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216212115.png)

类似的，这个方法同时兼顾了exploration和exploitation；
这个表示更好，因为我们并不关心action-value的绝对值，只有相对值是重要的；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216212413.png)

如此，只需要一个网络来近似表达 $v_t$ 即可，而不需要像DQN用两个网络分别表示 $q_t$ 和target；

![image.png|475](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216212719.png)

## off-policy actor-critic

如何更好利用已有的经验？
采样策略和优化的策略相同，一遍改进一遍采样，就是on-policy；
使用**重要性采样**，得到了off-policy的算法；


### 重要性采样 

例子：
随机变量X，二项分布p0，那么我们使用无偏估计，通过大数定律可以得到expectation；
问题：如果我们可以从p1上面采样，那么如何求p0上的expectation？

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216222126.png)

我们需要根据两个分布的特征，对随机变量***进行变换***；
核心想法：

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216222427.png)

相当于是一个加权平均，也可以称为 **importance weight**；是重要性程度的度量；
通常来说，$p_0$的具体表达式是无法求出来的，只能通过采样得到一个结果；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216223205.png)

从而，我们可以实现 off-policy 的学习；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216225930.png)

相当于通过重要性采样，对齐两个策略的概率；
- $d_\beta$：behavior policy 的平稳分布；

对应的gradient

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216230739.png)

迭代策略

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216234033.png)

算法

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216234259.png)

## DPG
- 之前的policy都是带有随机性的，也就是说 $\pi(a|s, \theta)>0$ ;
- 能否使用确定型的策略，也就是说给定一个policy每个state只有一个概率为1的action；
- 优点：能够处理无限个action的情况；

形式：不再使用概率的表示，而是直接采用state到action的映射；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216235204.png)

新的目标函数，state-value的期望：
- 只关心某种情况，那么直接取某个state-value作为期望；
- 某个behavior policy的平稳分布；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216235626.png)

这是一个off-policy的算法；因为该策略的更新是与action无关的；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216235854.png)


算法

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216235957.png)

推广

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217000227.png)

