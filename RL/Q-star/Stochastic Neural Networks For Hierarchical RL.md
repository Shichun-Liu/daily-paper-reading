[Stochastic Neural Networks For Hierarchical RL - 知乎](https://zhuanlan.zhihu.com/p/75637716)
[[1704.03012] Stochastic Neural Networks for Hierarchical Reinforcement Learning](https://arxiv.org/abs/1704.03012)
## 文章总体思想

尽管深度强化学习取得了许多成就，但是仍然面临着稀疏奖励的问题，比如在导航任务中，直到找到目标才会得到奖励，传统的朴素的探索方法在这种任务中表现不佳，展示出样本复杂性很高。

解决**稀疏奖励**问题通常有两种思路：对动作进行**分层**，将low-level动作组成high-level元动作，这样搜索空间就会被降低。然而这种分层方法通常需要较多的领域知识，并且需要仔细地设计。第二种方式是利用**内在激励**来引导智能体探索，这种方式不需要领域知识，但是当面对一系列任务时，这类方法没有办法将关于某个任务的知识迁移到另外一个任务，每次解决新的任务时候都需要从头学起，大大增加了样本复杂度。

文章提出了一种方法snn4hrl(Stochastic Neural Networks for Hierarchical Reinforcement Learning)，能够较好地解决奖励稀疏的任务，同时能在**相似的任务之间实现泛化**。方法基于分层的思想，首先在一个预训练的环境中学习有用的技能，这些技能是通用的，和要解决的任务的关系不是十分强。接下来学习一个**高层的控制策略**能够使智能体根据状态调用这些技能，能够很好地解决探索问题，在一系列稀疏奖励的任务中表现出色。

snn4hrl在一个预训练环境中学习技能时，仅仅需要一个proxy reward(不知道如何翻译...)，这个奖励的设计需要很少的领域知识，这个proxy reward可以理解为一种内部激励，鼓励智能体探索自己的能力。这些技巧然后可以被用来解决接下来的一系列任务，还需要我们训练一个high-level策略来调用这些技巧。为了使学习到的技巧更多样，我们使用SNN（Stochastic Neural Networks）。SNN可以很容易地表示多模态策略，同时也可以实现权重共享。每次除了观测之外，还将来自于简单分布的隐变量作为额外输入，来控制网络的随机性。然而仅仅使用SNN还不够，为了使学习到的技能更加多样，范围更广，文章添加了基于互信息的正则项，能够保证学习到的技能的多样性。最终发现本文的方法可以学到范围广泛的技能，这些技能也可以使用隐变量来控制。最终在这些技巧之上训练顶层策略，可以在一些稀疏奖励的任务中表现出色。

## 方法

方法部分一共分为五个部分:

1. 介绍预训练环境，能够帮助智能体学习到很多技能。
2. 介绍随机神经网络，来学习技能。
3. 提出了基于互信息的正则项使得学到的技能更加多样性。
4. 描述了在技能之上的高层策略，能够用来解决稀疏奖励的任务。
5. 训练时使用的策略优化方法。