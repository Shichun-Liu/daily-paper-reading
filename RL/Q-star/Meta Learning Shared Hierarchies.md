[[1710.09767] Meta Learning Shared Hierarchies](https://arxiv.org/abs/1710.09767)
[Learning a hierarchy](https://openai.com/research/learning-a-hierarchy)

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
The warmup period for optimizing the master policy θ is defined as follows. We assume a constant set of sub-policies as parameterized by φ. From the sampled task, we record D timesteps of experience using πφ,θ (a|s). We view this experience from the perspective of the master policy, as in Figure 2. Specifically, we consider the selection of a sub-policy as a single action. The next Ntimesteps, along with corresponding state changes and rewards, are viewed as a single environment transition. We then update θ towards maximizing reward, using the collected experience along with an arbitrary reinforcement learning algorithm (for example DQN, A3C, TRPO, PPO) (Mnih et al., 2015; 2016; Schulman et al., 2015; 2017). We repeat this prodecure W times.

Next, we will define a joint update period where both sub-policies φ and master policy θ are updated. For U iterations, we collect experience and optimize θ as defined in the warmup period. Additionally, we reuse the same experience, but viewed from the perspective of the sub-policies. We treat the master policy as an extension of the environment. Specifically, we consider the master policy's decision as a discrete portion of the environment's observation. For each N -timestep slice of experience, we only update the parameters of the sub-policy that had been activated by the master policy.

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240108183906.png)

