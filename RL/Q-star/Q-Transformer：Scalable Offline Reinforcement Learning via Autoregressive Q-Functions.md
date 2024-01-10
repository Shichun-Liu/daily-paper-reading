[【强化学习RL3】Q-Transformer: Scalable Offline Reinforcement Learning via Autoregressive Q-Functions - 知乎](https://zhuanlan.zhihu.com/p/658364677)
[[2309.10150] Q-Transformer: Scalable Offline Reinforcement Learning via Autoregressive Q-Functions](https://arxiv.org/abs/2309.10150)
[Q-Transformer](https://qtransformer.github.io/)

- 领域：机器人操作+Q-learning； 

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240109145642.png)

Another issue in **applying Transformer models to RL** is to design RL systems that can effectively train such models. Effective offline RL methods generally employ Q-function estimation via temporal difference updates [28]. Since Transformers model discrete token sequences, we convert the Q-function estimation problem into a discrete token sequence modeling problem, and devise a suitable loss function for each token in the sequence. Naively discretizing the action space leads to exponential blowup in action cardinality, so we employ a per-dimension discretization scheme, where each dimension of the action space is treated as a **separate time step** for RL. Different bins in the discretization corresponds to distinct actions. The per-dimension discretization scheme allows us to use simple discrete-action Q-learning methods with a conservative regularizer to handle distributional shift [29, 30]. We propose a **specific regularizer** that minimizes values of every action that was not taken in the dataset and show that our method can learn from both narrow demonstration-like data and broader data with exploration noise. Finally, we utilize a hybrid update that **combines Monte Carlo and n-step returns with temporal difference backups** [31], and show that doing so improves the performance of our Transformer-based offline RL method on large-scale robotic learning problems.

从离线数据中学习策略需要求解分布漂移的问题，因为一般来说能够最大化Q值的动作可能位于数据分布之外。解决这种问题的一种方法就是通过低估分布外的动作的Q值，从而确保最大值动作是分布内的。

