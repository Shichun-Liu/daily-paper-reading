[MCTS + RL 系列技术科普博客（1）：AlphaZero - 知乎](https://zhuanlan.zhihu.com/p/650009275)
[AlphaGo Zero: Learning from scratch | DeepMind](https://web.archive.org/web/20171019220157/https://deepmind.com/blog/alphago-zero-learning-scratch/)
## 背景

### MCTS
经典MCTS的四步骤
![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217210822.png)
1. **_Selection_**: Start from root _R_ and select successive child nodes until a leaf node _L_ is reached. The root is the current game state and a leaf is any node that has a potential child from which no simulation (playout) has yet been initiated. The section below says more about a way of biasing choice of child nodes that lets the game tree expand towards the most promising moves, which is the essence of Monte Carlo tree search.
2. **_Expansion_**: Unless _L_ ends the game decisively (e.g. win/loss/draw) for either player, create one (or more) child nodes and choose node _C_ from one of them. Child nodes are any valid moves from the game position defined by _L_.
3. _**Simulation**_: Complete one random playout from node _C_. This step is sometimes also called playout or rollout. A playout may be as simple as choosing [uniform random](https://en.wikipedia.org/wiki/Discrete_uniform_distribution "Discrete uniform distribution") moves until the game is decided (for example in chess, the game is won, lost, or drawn).
	- 该步骤于AlphaZero中被一个value网络代替，不再实际模拟；
4. **_Backpropagation_**: Use the result of the playout to update information in the nodes on the path from _C_ to _R_.

#### UTC/UCB公式
- 一种state-value的表达式
choose in each node of the game tree the move for which the expression
![image.png|700](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217211637.png)

这个value表达式的主要作用是平衡exploration/exploitation；大多数MCTS算法都采用UTC函数作为价值函数；
### 相关工作

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217212104.png)


## AlphaGo 
### 模型概览

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217212216.png)

**四个网络：**
- 基于监督学习的策略网络（**SL策略网络**）的训练。它从16万局人类玩家的棋谱中收集了29.4M状态动作对 (state-action pair)，并通过监督学习进行训练。
- 基于监督学习的快速策略网络（**SL快速策略网络**）的训练。它同样利用人类玩家的棋谱进行监督学习，但是训练的是一种简化版的快速策略。该策略主要用于蒙特卡洛搜索树。相比于基于监督学习的策略网络，SL快速策略网络使用了更简单的棋类特征以及网络结构。
- 基于强化学习的策略网络（**RL策略网络**）：它**在监督学习训练得到的 SL 策略网络基础上**，让当前网络与之前的任一网络进行随机对打。然后，利用强化学习中的 REINFORCE 算法进行训练，以最大化获胜的奖励期望。并且，它会不断地将中间训练结果（RL策略网络）加入到对手池中。
- 基于强化学习的价值网络（**RL价值网络**）：它利用 SL 策略网络和 RL 策略网络收集状态动作对，用于训练价值网络，优化目标为最小化当前状态的价值与最终胜负的 MSE。

其分别的作用在MCTS中有所体现，具体而言，关注UCB公式
$$
UCB(s,a)=Q(s,a)+cP(s,a)\frac{\sqrt{\sum_b N(s,b)}}{1+N(s,a)}
$$
其中，$Q(s,a)$ 来自RL价值网络+SL快速走子网络，$P(s,a)$ 来自RL策略网络；
- 具体而言，对于叶子节点 $s_L$ 的value是利用 RL 价值网络估计state-value $v_\theta(s_L)$ 与利用 SL 快速策略网络从该叶子节点 rollout 到终局得到的真实回报 $z_L$ 的线性组合;

**推理过程**
1. 首先，在某一状态下，AlphaGo 会建立一个MCTS搜索树。
2. 然后，利用 RL 策略网络计算该状态下的落子概率 $P(s,a)$ 。
3. 接着，AlphaGo 会利用 RL 价值网络对当前状态进行评估。同时，使用 SL 快速策略网络从当前状态模拟至游戏结束，得到最终结果。然后，结合 RL 价值网络的评估值和模拟的最终胜负计算出最终评估值$Q(s,a)$ 。
4. 之后，AlphaGo 会利用计算出的最终评估值反向传播，以更新节点的价值。
5. 最后，AlphaGo 会重复以上操作，当某个节点被访问的次数超过某个阈值时，就会拓展下一个节点。

### 创新
- exploitation部分：$Q(s, a)$ 结合了RL价值网络+SL快速走子网络；相比于传统MCTS的模拟step要完整走完一局棋，其减少了搜索的深度，提高搜索速度；
- exploration部分：引入人类经验指导的 $P(s,a)$ 避免了完全随机的选择，有效减少了搜索的宽度；
加速了搜索的效率和质量。

## AlphaGo Zero
### 改进之处
- AlphaGo Zero 的独特之处在于，除了规则外，它无需依赖人类玩家的棋谱或其他先验知识。它完全通过自我博弈进行学习，从零开始逐渐积累经验和知识。（此处的自我博弈是保存一个历史最佳Agent，然后用最新的Agent与之博弈产生数据，当新的Agent胜率超过55%时则更新历史最佳Agent，而不是实施更新policy）
- 在编码信息上，AlphaGo Zero 摒弃了所有手动编码的特征，仅保留了棋盘基本信息，这更加简洁和高效。
- 在网络结构上，AlphaGo Zero 将**策略网络和价值网络融合为一体**，且采用了具有残差连接的 ResNet 结构。网络最后设计为双头模式，一头为策略预测网络，另一头为价值预测网络。
- 此外，AlphaGo Zero 无需使用 SL 快速策略网络进行 rollout，**仅使用价值网络**预测的Q来计算叶子节点的状态价值。

### 训练流程
- 首先，利用当前的策略和价值神经网络指导进行蒙特卡洛搜索，收集自我博弈棋局数据。
- 然后，使用这些自我博弈棋局数据，训练策略网络和价值网络。训练的目标是让神经网络输出的策略和价值函数与蒙特卡洛搜索的结果中的尽可能接近。也就是说，试图让策略预测网络和价值预测网络输出的值去**预测 MCTS 的搜索结果**。
- 最后，训练出的神经网络继续参与自我博弈，生成更高质量的数据，然后再重复上述步骤，形成一个持续学习和优化的闭环。

## AlphaZero

### 网络结构

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217225701.png)

### 改进
- AlphaZero 算法是 AlphaGo Zero算法的通用版本；可以用于别的棋类游戏（日本象棋，国际象棋）；同样采用了一个双头网络输出RL策略P以及叶节点的value，主要流程和AlphaGo Zero类似；
- 区别：
	- 在游戏结果上增加考虑了平局的情况；
	- 取消了棋局的对称性假设（因为要泛化到不同的游戏），没有使用数据增强（旋转、反射变换）；
	- 没有保存历史最佳Agent进行两个policy博弈，而是直接on-policy博弈、训练；也就是只维持了一个网络；
	- 超参数在所有游戏上大致统一（除了探索噪声和学习率）；

### 算法流程

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231217225052.png)
**A**：MCTS 算法进行搜索的过程，包括选择、扩展、评估和回溯四步。
**B**：对 MCTS 返回的根节点的 visit count 的分布进行采样，获得最终的输出动作，并通过 self-play 收集样本数据。
**C**：损失函数包括：MCTS 得到的动作分布和策略网络的动作分布的交叉熵损失，环境真实 return 和价值网络输出的预测值之间的均方差损失。即让预测的 policy，value 朝着 MCTS 搜索得到的动作分布和环境真实 return 的方向逼近。符号的含义：[算法概览图符号表](https://link.zhihu.com/?target=https%3A//github.com/opendilab/LightZero/blob/main/assets/paper_notes/SymbolTable.pdf)。

#### 推理步骤
- policy网络和value网络已经训练完成；可以给出N，Q，P；
- 每个叶节点分数的极计算没有模拟步骤，直接是evaluation；
- 下面一个完整的1234迭代只是为了计算从某一个state开始的接下来的最优路径的计算（每一次迭代更新一次V和N）；然后给出根节点对应state的最优action；

![image.png|700](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231218123329.png)

#### 训练过程
（参考上面的模型图）
在早期的 AlphaGo 模型中，部分训练样本是源自人类棋手的专家数据。然而，通过对 AlphaGo 在与李世石对战的一局中的分析，我们可以发现在第87手之后，价值网络对当前棋局的评估逐渐偏离实际情况。价值网络仅能评估当前的局面，而棋局的演进往往需要多步棋的精准演算才能逐渐显现。AlphaGo 利用监督学习的策略扩展搜索树的节点，但如果对手下出“神之一手”，由于依赖人类专家数据的局限，AlphaGo 可能无法做出正确的应对。  
为了解决这个问题，后续的 AlphaGo Zero 和 AlphaZero 的训练中，**完全放弃了使用人类专家数据**。所有训练样本都是通过自我博弈的方式生成，策略函数和价值函数在训练过程中不断优化。这两者指导蒙特卡洛树搜索(MCTS) 进行搜索，同时利用 MCTS 搜索结果与环境交互产生训练样本，进而再优化目标函数。通过这种策略的逐步提升，可以不断地获得更高质量的训练数据。

具体流程，抽象而言就是采样（PE）+策略优化（PI）
- 首先通过自我博弈**获得数据样本**。在自我博弈中，AlphaZero 首先根据当前状态构建蒙特卡洛搜索树的根节点，利用策略网络和价值网络计算 UCB 得分，并选择得分最高的动作，直至遇到叶子节点。对叶子节点进行扩展，根据价值网络的输出，反向更新该路径的节点信息并进行多次模拟。多次模拟后，返回 visit counts 集合 $N(s,a)$ ，**从 visit counts 导出的分布中进行采样**，作为当前状态下的执行动作。  
- 然后，当游戏的一局结束后，将样本数据存放在经验回放池中，从中采样样本用于**训练强化学习网络**。强化学习网络包括价值网络和策略网络，价值网络通过均方误差损失进行优化，策略网络通过交叉熵损失进行优化。随着策略和价值函数的不断提升，训练样本也在不断改进，从而训练出的智能体能力逐渐增强。

## 实验
### 训练设置

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231218135600.png)

每种棋类游戏都有一个独立的 AlphaZero 实例进行训练。训练从随机初始化的参数开始，以4096为批次大小进行了700k步的训练。在此过程中，5000个 TPU v1 被用于生成自我对弈游戏，16个 TPU v2 被用于训练神经网络。在训练期间，每个蒙特卡洛搜索树（MCTS）进行了800次模拟。各种游戏的训练长度、思考时间等信息可以在表4中找到。每局游戏的学习率初始设定为0.2，随后进行了三次下降，分别至0.2，0.02和0.002。棋子的动作选择与MCTS中根节点的访问次数成正比。在根节点的先验概率中，加入了狄利克雷噪声 $Dir(\alpha)$ ，其与某个位置的合法棋步的近似数量成反比。在国际象棋、将棋和围棋中， $\alpha$ 分别设为0.3，0.15和0.03。除非特别说明，AlphaZero 的训练和搜索算法及策略都与 AlphaGo Zero 一致。

### 实验结果

在国际象棋中，AlphaZero 仅训练了4个小时（300k 步）就超过了 Stockfish。在将棋中，AlphaZero 不到2小时（110k 步）的训练就超过了Elmo。在围棋中，AlphaZero 在训练了8小时（165k 步）后就超越了 AlphaGo Lee。

从结果来看，每种开局下 AlphaZero 都显然战胜了 Stockfish和 Elmo，这表明 AlphaZero 确实掌握了国际象棋和将棋中广泛的棋谱。

#### MCTS搜索过程

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231218135843.png)

在 AlphaZero（白方）和 Stockfish（黑方）对战的一局游戏 （整局游戏过程参考原论文表 S6 ）在执行29步 Qf8 之后的状态，如左上角的棋面所示。 
在这个状态上分别经过 10^2 ，...， 10^6 次模拟后，AlphaZero 的 MCTS 的内部状态 (internal state，用圆圈表示) 结果。 每个结果显示了前 10 个访问量最大的 internal state，圆圈内的数字代表value值，统一缩放到了 [0, 100]。 每个内部状态对应的圆圈边界的厚度与其访问计数成正比。 
AlphaZero 在执行下一步时，在**模拟次数较小**时考虑了动作c6，但最终随着模拟次数增加，最终选择了动作d5。


## 总结
- MCTS是一种高效而强大的搜素策略，较好地平衡探索性与分配资源，能够在较少的迭代步骤内找到优质结果；
- 从零开始的自我博弈策略，虽然相比起人类监督作为其实策略收敛更慢，但是上限更高，能够突破人类的固有定式找到更优的策略，不受到人类经验所带来的偏见的影响；

对于 AlphaZero 的未来展望：
- 更广泛的应用：探究如何将 AlphaZero 扩展到更广泛的应用领域，比如自动驾驶、医疗保健和金融等。
- 进一步的算法优化：尽管 AlphaZero 在深度强化学习领域取得了显著的进步，但其算法仍有改进的空间，例如如何更好地处理稀疏奖励问题、如何更好地处理多智能体环境等。
- 提高算法解释性：由于深度强化学习算法的复杂性，其结果和决策过程往往难以解释和理解。未来的研究可以探索如何提高算法的可解释性，使其结果更易于被人理解和接受。

## 思考
我们不讨论哲学问题
那么人类能否造出比自己更强大的物种？
- 强大这个概念太宽泛，可以先聚焦于某一个具体的能力
	- 能力分为rule-based和非rule-based；
	- 计算器本身就是一个基于规则的工具，计算能力强于人类；
	- 围棋的游戏规则是明确的，但是下围棋的最优策略并没有明确的规则，只能从数据+搜索中得到下一步的最优解；AI在下围棋上面的能力强于人类，且不需要利用人类经验的监督而是靠自我博弈；
	- 下围棋的步骤可以认为是一种决策（搜索策略），说明AI可以完成做决策这一件事情且不弱于人类；但只局限于明确的单一场景，AI可以直接计算得到action-value；
	- 复杂场景下，搜索过程如何优化？
- 物种的概念不明确，可以先聚焦于某个单一功能的工具
	- 复杂工具可以视为多个单一工具的复合；多个工具之间的协作由决策器完成；

- 语言模型的局限性，语言模型能否真正做推理还是仅仅模式匹配？
	- 人类是如何完成数学推理和创新的？传统的机器学习+数学推导是如何进行的？ 