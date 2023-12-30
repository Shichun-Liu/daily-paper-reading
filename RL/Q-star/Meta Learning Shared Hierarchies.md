[[1710.09767] Meta Learning Shared Hierarchies](https://arxiv.org/abs/1710.09767)
[Learning a hierarchy](https://openai.com/research/learning-a-hierarchy)
[分层强化学习（MLSH） - 知乎](https://zhuanlan.zhihu.com/p/87041992)

**提出一种学习分层结构策略的方法，能够在没有见过的任务上提升样本效率，通过使用共享的primitives(执行多个时间步的策略)**。在一个分布的任务中，primitives的集合（对应底层的策略）是共享的，然后利用task-specific policies（对应顶层的master policy）来切换，使得可以在没见过的任务中快速获得较高奖励。通过不断采样新的任务，重置task-specific policies来解决这个问题。发现一些有意义的motor primitives对于一些相关的任务是共享的，比如在四足机器人中，向不同方向移动就是可以共享的primitives，而不同的任务仅仅是目标的位置的变化。文章也展示出了primitives的迁移能力来解决long-timescale sparse reward任务。

这里考虑的情景是智能体解决**相关任务**的集合，快速解决新的任务。面临的挑战是在不同任务之间共享信息，这些任务的最优策略不同，为了解决这个问题，作者 提出一个模型包含shared sub-policies的集合，通过task-specific master policy来切换。同时提出一种方法可以实现end-to-end训练sub-policies，可以在新任务上快速学习，仅仅通过学习一个master policy。

- 分层元学习；
- 有一个master policy，一组sub-policy；前者负责选择做出什么action，后者负责具体执行action；By discovering a strong set of sub-policies φ, learning on new tasks can be handled solely by updating the master policy θ；
- An **optimal set of sub-policies** must be fine-tuned enough to achieve high performance. At the same time, they must be robust enough to work on wide ranges of tasks. Optimal sets of sub-policies must also be **diversely** structured such that master policies can be learned quickly. （子网络够好、多样性强，主网络才能学得快） We present an update scheme of sub-policy parameters φ leading naturally to these qualities.

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240108163811.png)

## 算法
the algorithm (Algorithm 1) iteratively performs update steps which can be broken into **two** main components: a **warmup period** to optimize master policy parameters θ, along with a **joint update** period where both θ and φ are optimized.（主网络预热+主网络自网络联合更新）

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240108164858.png)

- We first **sample** a task M from the distribution PM . We then initialize an agent, using a previous set of sub-policies, parameterized by φ, and a master policy with randomly-initialized parameters θ. （采样数据，初始化）
- We then run a **warmup** period to optimize θ. At this point, our agent contains of a set of general sub-policies φ, as well as a master policy θ fine-tuned to the task at hand. 
- We enter the **joint update** period, where both θ and φ are updated. 
- Finally, we sample a new task, reset θ, and repeat.

### warmup period
The warmup period for optimizing the master policy θ is defined as follows. We assume a constant set of sub-policies as parameterized by φ. From the sampled task, we record D timesteps of experience using πφ,θ (a|s). We view this experience from the perspective of the master policy, as in Figure 2. Specifically, we consider the **selection of a sub-policy as a single action**. The next N timesteps, along with **corresponding** state changes and rewards, are viewed as a **single environment transition**. We then update θ towards maximizing reward, using the collected experience along with an arbitrary reinforcement learning algorithm (for example DQN, A3C, TRPO, PPO) (Mnih et al., 2015; 2016; Schulman et al., 2015; 2017). We repeat this prodecure W times.

###  joint update period
Next, we will define a joint update period where **both sub-policies φ and master policy θ are updated**. For U iterations, we collect experience and optimize θ as defined in the warmup period. Additionally, we reuse the same experience, but viewed from the perspective of the sub-policies. We treat the **master policy as an extension of the environment**. Specifically, we consider the master policy's decision as a discrete portion of the environment's observation. For each N -timestep slice of experience, we **only update the parameters of the sub-policy** that had been activated by the master policy.

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240108183906.png)

从一个高层的角度来看，MLSH更新如下：首先从 $P_M$ 中采样一个MDP，然后初始化一个智能体，使用之前的子策略集合，master-policy使用随机参数。然后执行warm up来优化 $\theta$ 。然后进入联合更新阶段，最后reset master policy，重复这个过程。

更新master policy的时候，每隔Ｎ个时间步更新一次，使用master policy得到的样本，然后更新sub policy的时候，使用sub policy 得到的样本，仅仅更新当前激活的子策略，将master policy的动作作为观测的一部分。

**实验部分**

作者考虑一系列的实验，包含两条曲线：首先是在整个分布下的所有任务的整体曲线，还有在一个任务下的曲线。对于总体训练曲线，作者在满足这个分布的所有任务中进行训练。

在单独的采样任务中，利用之前训练好的子策略，然后仅仅更新master policy的参数来学习新的任务。通过实验发现MLSH可以学习到有意义的子策略，同时可以在新的任务中实现比较好的迁移。解决一些常规强化学习方法无法解决的任务。

**总结**

文章结构清晰，实验充分，同时实现了较好的效果，理解起来也比较容易。结构比较简单，有提升空间，主要的创新点我认为集中在训练方法上。而结构我认为和SNN4HRL（[赵英男：Stochastic Neural Networks For Hierarchical RL](https://zhuanlan.zhihu.com/p/75637716)）那篇文章也比较相似，都是主策略来调用不同的子策略。区别在于这篇文章更加侧重于子策略的泛化和迁移，提出了共享子策略的概念，但是本质我认为都是相同的。