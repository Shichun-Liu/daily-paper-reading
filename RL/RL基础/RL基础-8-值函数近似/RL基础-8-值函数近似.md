[【强化学习的数学原理】课程：从零开始到透彻理解（完结）\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1sd4y167NS)


> [!note]
> 第一次引入神经网络；
> 对于value function的近似表示；

mindmap

![[Pasted image 20231215231702.png|600]]

主要内容
![[Pasted image 20231216002802.png|400]]
- [[#引言|引言]]
- [[#state-value 的近似|state-value 的近似]]
	- [[#引言#PE 阶段|PE 阶段]]
	- [[#引言#优化方法|优化方法]]
	- [[#引言#近似函数的选择|近似函数的选择]]
- [[#action-value的近似|action-value的近似]]
- [[#action-value的近似#sarsa 求action-value|sarsa 求action-value]]
	- [[#sarsa 求action-value#Q-learning|Q-learning]]
- [[#Deep Q-learning （DQN）|Deep Q-learning （DQN）]]
	- [[#sarsa 求action-value#技巧一：两个网络|技巧一：两个网络]]
	- [[#sarsa 求action-value#技巧二：经验回放|技巧二：经验回放]]

### 引言

- 表格型方法：表格是指action-value的二维的表格表示；
- 缺点：state space或者action space较大、甚至连续时，表格型难以存储、难以泛化；
- 例子
![[Pasted image 20231216100642.png|475]]
- 一维state数量非常多，**使用一个 v hat 函数来近似表示各个state的value**（$\hat{v}(s,w)\approx v_\pi(s)$）；如此， 不用存储各个state对应的精确的value，而只需要存储曲线的参数（如线性拟合）；
	- 节省大量的存储；
	- 增强了泛化性；访问一个状态，会改变函数参数，其他state-value也会变化；
- 对于非线性的曲线而言，本质上对于参数w而言仍然是线性变化，对于变量s而言需要先设计一个kernel function进行变换；
![[Pasted image 20231216101104.png|450]]
- 也可以用神经网络做非线性的拟合；

## state-value 的近似
#### PE 阶段
目标：对于给定的policy，state和state-value，得到最优的函数参数w，也就是得到value-function的函数；
![[Pasted image 20231216102121.png|500]]

目标函数是二次error的期望，优化目标是最小化目标函数；
- 有两种方法来计算期望
	- 均匀分布：所有的state都是平权的；这不合理，所有的state重要性并不一致；
	- 平稳分布：平稳状态（long-run behavior）下，某个state被agent访问的概率，用作该状态的权重（重要性度量）；
![[Pasted image 20231216102417.png|500]]

![[Pasted image 20231216102610.png|500]]
那么，如何估计概率分布d？
- 第一种方式：MC模拟；统计表示；
![[Pasted image 20231216103020.png|500]]

- 第二种方式：不动点，求解线性方程
![[Pasted image 20231216103224.png|500]]

#### 优化方法
梯度下降
- 理论更新
![[Pasted image 20231216103509.png|500]]
- 但是实际上这个公式是不可行的，因为理论的 $v_\pi(s_t)$ 是未知的；
- 我们需要代替之，有两种方法：MC模拟和TD方法
![[Pasted image 20231216103836.png|500]]
v hat函数
![[Pasted image 20231216103956.png|500]]
而这个TD方法其实优化的并不是原来的目标函数，而是以下的形式
![[Pasted image 20231216110319.png|500]]

#### 近似函数的选择
- 过去：使用线性函数+特征向量；
	- 合适的feature vector难以选择；并不鲁棒；
	- 就是传统机器学习那一套；
	- 是表格型方法的广义情况；参数个数的最多就是data的数量；
![[Pasted image 20231216104931.png|500]]
![[Pasted image 20231216105458.png|500]]
	使用一个简单的平面去拟合，可以用更少的参数、更快的速度；代价是损失一部分精度；
	更细致的表达，需要设计feature vector，更多的参数；

- 现在：使用神经网络作为近似器；

## action-value的近似
### sarsa 求action-value
- PE过程
![[Pasted image 20231216110510.png|500]]
完整PE PI过程
![[Pasted image 20231216110706.png|600]]
#### Q-learning
on-policy版本
![[Pasted image 20231216111015.png|600]]
## Deep Q-learning （DQN）

Loss function：
![[Pasted image 20231216111741.png|500]]
如何计算目标函数的梯度？两个地方都要含w，技巧是冻结前面一部分！
![[Pasted image 20231216112334.png|500]]
#### 技巧一：两个网络
使用两个networks，target network 每隔一段时间才更新（复制main network的值）；优化w的时候固定前面的w_t不动；
![[Pasted image 20231216112446.png|500]]

实现细节

![[Pasted image 20231216112953.png|500]]

#### 技巧二：经验回放
experience replay
- 使用数据的顺序和采集的顺序并不一致；
- 采集数据，存起来，得到的集合为replay buffer；拿训练数据时从中均匀分布采样（打散不同sample之间的相关性）；

![[Pasted image 20231216113158.png|500]]

为什么要均匀分布？
（S，A）的分布在q hat求解的时候是需要的，但是之前表格型方法是在求解贝尔曼最优方程
- 但是也能将经验回放用在表格型Q-learning，（是一个off-policy的方式），提高经验的利用效率；
- off-policy的版本

![[Pasted image 20231216114208.png|550]]

采样，计算 $y_T$，更新main network，更新 $w_T$ ；
- 注意，是off policy，所以没有更新policy；
- 全计算完value，然后只更新一次policy就得到了最优的policy；
相比起之前的求解贝尔曼公式的迭代，使用DQN的迭代次数大大减少；
但是太少的迭代次数，可能求解出来的结果不是正确的（即使已经收敛了，error不一定收敛到0）；数据还是非常重要！

