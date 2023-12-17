[一文带你理清DDPG算法（附代码及代码解释） - 知乎](https://zhuanlan.zhihu.com/p/111257402)

## DDPG 与 DQN

DQN是对于action-value的估计，通过维护主网络和目标网络；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217095303.png)

- DQN 不能处理连续问题，因为 $\max{Q(s',a')}$ 只能处理离散值；
- DDPG 就是要处理这个问题，通过一个近似网络，输入state S，输出这个state下对应的最优action及其value；也就是确定的策略；
- DDPG-actor：输入state，通过梯度上升的方式，找到action-value（Q值）最大的action；
- DDPG-critic：value-update；
- 这是off-policy，又不用重要性采样（为什么？）；


## DDPG 算法
### Critic
- Critic网络的作用是预估Q，虽然它还叫Critic，但和AC中的Critic不一样，这里预估的是Q不是V；
- 注意Critic的**输入有两个：动作和状态**，需要一起输入到Critic中；
- Critic网络的loss和AC一样，用的是TD-error。

### Actor
- 和AC不同，Actor输出的是一个动作；
- Actor的功能是，输出一个动作A，这个动作A输入到Crititc后，能够获得最大的Q值。
	- Critic和DQN有些许不同，在DQN，我们会计算Q(s)，把该state下的所有动作的q值都输出，然后从中挑选q值最大的一个动作。
	- 而在DDPG，我们会把动作a也输出到network，由network去评判这个action的q值。所以我们需要把a和s一起放入到Critic中去。
- 所以**Actor的更新方式和AC不同**，不是用带权重梯度更新，而是用**梯度上升**。

和DQN一样，更新的时候如果更新目标在不断变动，会造成更新困难。所以DDPG和DQN一样，用了**固定网络(fix network)技术：先冻结住用来求target的网络，在更新之后，再把参数赋值到target网络。

需要用4个网络
![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217100922.png)

