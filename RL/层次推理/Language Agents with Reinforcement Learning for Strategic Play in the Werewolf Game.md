- 处理可能具有欺骗信息的游戏；富有合作和竞争性质的游戏；
- 玩法多样，没有单一套路；
- 代理使用 LLM 组织关键信息，以推理隐藏角色，并生成一组不同的action candidates，然后通过population-based的训练来学习 RL 策略，以从候选动作中输出最终动作。
- 代理由三个组件组成。
	- 第一个演绎组件对游戏历史进行推理。它根据整个游戏历史的重要性和可靠性将整个游戏历史分类为原子信息列表，并使用 LLM 来**推断其他玩家的隐藏角色**。
	- 分类的信息和演绎结果被用作**动作生成组件**的输入，以提示LLM为一组策略多样的动作候选。这些候选为我们的代理提供了各种游戏风格，使代理能够学习多样化和不可预测的行为。
	- 最后一个组件是一个 RL 策略来**选择输出动作**并**优化整体决策性能**。为了使最终策略更具不可利用性，我们生成了一组具有不同样式的固定基于llm的代理，并使用基于种群的训练通过对抗自身、过去版本和池中的代理来进一步提高RL策略。
- 通过实证行为分析，发现了Agent表现出各种各样的紧急策略，如隐藏、合作、bluffing和牺牲，这些策略经常被熟练的人类玩家利用。

## 论文

### related-work
- Cicero (Meta et al., 2022)将 LLM 与 RL 相结合，以在 Diplocy 游戏中实现人类水平的游戏。Cicero 和我们的方法之间的主要区别在于 Cicero 使用 RL 策略**从预定义的动作集**中选择。相比之下，我们的方法中的动作是 LLM 在游戏中生成的自然语言，RL 策略用于从这些**事先不知道的动作中进行选择**。
- 也有纯粹利用LLM自身的推理能力的狼人杀代理（Exploring Large Language Models for Communication Games: An Empirical Study on Werewolf）；
- RL非合作博弈游戏中使用的技术：self-play，Population-based training methods like policy-space response oracles (PSRO)，league training，regret minimization techniques such as counterfactual regret minimization (CFR)

### Game 
- 经典狼人杀：2狼人，村民，预言家，医生；
- 每个玩家的observation是一个记录当前**游戏历史的文本**。这包括他们的 ID 和隐藏角色、他们的秘密夜动作（如果有）、公告、讨论和投票结果。对于狼人，他们的队友的 ID 和秘密行为也在观察中。
- 玩家的动作可以分为三种类型。第一个是夜间的秘密动作，包括杀戮、看到和保存选择特定目标。第二种类型是讨论过程中的陈述动作，它们是传达玩家意见和信息的自然语言。最后一个类型是投票动作，可以针对任何幸存的玩家或选择不投票。

### STRATEGIC LANGUAGE AGENTS 

![](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240121233852.png)

当新信息到达时，代理需要做出决定，首先通过根据先前的演绎结果对新信息进行项目化和分类来更新信息记录。然后将更新后的信息记录作为输入，提示LLM进行新的演绎结果。这个结果循环回修改记录，通过删除未引用的项目并重新分类潜在的真相和欺骗。信息记录和演绎结果的组合将原始观察转换为结构化和信息丰富的数据，作为后续决策的基础。
- 生成候选动作集合，有助于增加策略的探索性；

#### RL policy 
- population-based RL：由于我们的动作空间不是预定义的，我们不能使用典型的策略网络，该网络仅将状态作为输入并在固定动作集上产生分布。相反，我们首先使用 LLM 将游戏状态和来自自然语言的所有候选动作转换为**vector embeddings**。然后我们采用自注意力 (Vaswani et al., 2017) 网络，该网络将所有嵌入作为输入，以**在候选动作上产生分布**。更具体地说，游戏状态是第 4.1 节中描述的信息记录和演绎结果的串联，动作候选是 LLM 生成的推理和动作的串联，如第 4.2 节所述。这些自然语言由 LLM 转换为向量嵌入。我们还使用包含 ID、角色等玩家信息的向量，并通过 MLP 编码器传递向量以产生玩家嵌入。玩家嵌入和语言嵌入通过没有位置嵌入的残差自注意力块，对动作候选进行采样的概率计算为输出状态嵌入和输出动作嵌入之间的归一化点积注意力。
- 生成了**一组固定的基于llm的代理**，这些代理具有不同的风格，作为队友和对手。

### Experiments
- LLM used by all agents in our experiment is **gpt-3.5-turbo**；
- We use **MAPPO** (Yu et al., 2022) as the RL algorithm and use population-based training to learn the policy. The population is initialized to a set of six LLM-based agents including a quiet follower (Werewolf), an active contributor (Werewolf), an aggressive accusor (Werewolf), a secretive player (non-Werewolf), a proactive player (non-Werewolf), and a default player (non-Werewolf). As training progresses, we gradually add checkpoints of the training policy into the population. More specifically, at the beginning of each episode, we randomly select four players to be the learning agents who use the current policy and three players to be the fixed agents who use fixed policies in the population. For each fixed agent, it randomly samples one policy from the population and uses this policy till the end of the game. In this way, we make the RL policy play with a wide range of policies both as teammates and opponents. **The rollout data of the four learning agents are then collected and used to train the RL policy**. The hyperparameters for RL training are listed in Table 3

![image.png|224](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240122000553.png)