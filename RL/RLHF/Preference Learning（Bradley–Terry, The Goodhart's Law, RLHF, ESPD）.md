---
tags:
  - 偏好学习
date: 2023-11-09
---
## 资料
- [【RLxLLM 基础】Preference Learning: Bradley–Terry, The Goodhart's Law, RLHF, ESPD与科研BoN策略(x - 知乎](https://zhuanlan.zhihu.com/p/663790188)
- [[2310.12036] A General Theoretical Paradigm to Understand Learning from Human Preferences](https://arxiv.org/abs/2310.12036)(ΨPO)
- ELO: [ELO算法的原理及应用 - 知乎](https://zhuanlan.zhihu.com/p/57480433)

## ELO算法 
### 基本假设
所有选手的实力（分数）分布可以表示为一个正态分布函数，而任意一个玩家在某一个对局的实力表现也可以用正态分布函数描述；
$$
f(x)=\frac{1}{\sqrt{2\pi\delta^2}}e^{\frac{-(x-U)^2}{2\delta^2}}
$$
玩家A获胜的概率
$$
P\left(x_A>x_B\right)=\frac{1}{2}+\frac{1}{2} \operatorname{erf}\left(\frac{\mu_A-\mu_B}{\sqrt{2\left(\sigma_A^2+\sigma_B^2\right)}}\right)
$$
对于分差为D的A，B两位选手，A获胜的概率为：
$$
P(D)=\frac{1}{2}+\int_0^D \frac{1}{\sqrt{2\pi\delta^2}}e^{\frac{-x^2}{2\delta^2}}
$$
基于最小二乘法拟合，上述的函数可以拟合为
$$
P(D)=\frac{1}{1+10^\frac{-D}{400}}
$$
### 分数迭代
玩家在进行一场比赛后的分数更新为：
$$
R_n=R_{n-1}+K\cdot(W-P(D))
$$
其中，$R_n$ 是玩家比赛结束后的新的排位分值；$R_{n-1}$ 是比赛前玩家的排位分；K是一个加成系数，由玩家当前分值水平决定（分值越高K越小）；W是玩家实际对局结果得分（赢=1，输=0）；P（D）是预期胜率。

### 应用
- 设置匹配、游戏排位机制；
- 最重要的是从对战中迭代更新每个玩家的实力分数（最终收敛与真实实力分数）；


## Bradley–Terry 算法
基于**Logistic Distribution**假设；其CDF为
$$
F(x;\mu,s)=\frac{1}{1+e^{(x-\mu)/s}}=\frac{1}{2}+\frac{1}{2} \tanh(\frac{x-\mu}{2s})
$$
这与上述的正态分布函数的CDF类似，Bradley-Terry Model可以理解为**在局数有限的情况下**一种能取得**更好近似效果**的function class。
用指数函数表示分数，那么获胜概率可以表示为
$$
P(i>j)=\frac{1}{1+S_j/S_i}= \frac{1}{1+e^{\beta_j-\beta_i}}
$$
所以我们后续使用sigmoid函数作为表达概率的函数；

## RLHF
### 区别
ELO游戏对战中，我们是假设每个人的实力的固定的，只是通过不断对战收敛到一个绝对分数；规则也是固定的，只有确定性win/loss/平局，映射到单一对局的得分也很清晰；

在RLHF的数据集中，被评估的是同一个LLM给同一个prompt的不同的回答，而好坏的情况是人给出的（以排序的形式）。
排序含有较大噪声，会直接影响RM的结果；RM的目的是将排序结果转化为一个绝对分数；

### φPO
在RLHF中，RM的一步将Preference Data转化成Score，背后有两条（很自然的）假设: 
	1. pairwise preferences can be substituted with pointwise rewards; 
	2. a reward model trained on these pointwise rewards can generalize from collected data to ood data sampled by the policy.
对于数据集 $D=\{x_i,y_i^+,y_i^-\}$ ，偏好关系可以表达为
$$
P(y>y'|x)=\sigma(r(x,y)-r(x,y'))
$$
优化目标
$$
L(r)=-\mathbb{E}_{(x,y^+,y^-) \sim D}[\log(\sigma(r(x,y)-r(x,y')))]
$$
