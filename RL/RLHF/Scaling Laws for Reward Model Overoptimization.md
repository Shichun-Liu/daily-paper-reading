[[2210.10760] Scaling Laws for Reward Model Overoptimization](https://arxiv.org/abs/2210.10760)
[RLHF系列-Reward Model - 知乎](https://zhuanlan.zhihu.com/p/620877625)
[[ICML'23 RLxLLM] Scaling Law for Reward Model Overoptimization - 知乎](https://zhuanlan.zhihu.com/p/662289523)

## 主要内容
- 我们使用合成方法，其中固定的“黄金标准”奖励模型扮演人类的角色，提供用于训练代理奖励模型的标签。

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240104163144.png)

- 我们研究了黄金奖励模型分数如何随着我们使用强化学习或最佳 n 采样针对代理奖励模型进行优化而发生变化。我们发现这种关系遵循不同的函数形式，具体取决于优化方法，并且在这两种情况下，其系数随着**奖励模型参数**的数量平滑地扩展。
- 我们还研究了奖励模型数据集的大小、奖励模型和策略参数的数量以及强化学习设置中添加到奖励中的 KL 惩罚的系数对这种关系的影响。

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240104162242.png)

- RL和best-of-n方法对比：As a function of the KL divergence, reinforcement learning tends to be **slower** than best-of-n sampling at both optimization and overoptimization. This suggests **inadequacies** with **using KL** to compare amount of (over)optimization across methods. However, the **relationship** between the proxy reward model score and the gold reward model score is similar for both methods.
- 平滑系数缩放：α 和 β 系数随着**奖励模型参数的大小**呈现平滑变化，遵循近似对数趋势。由此**可以预测黄金RM得分**
- Policy规模的弱依赖：While larger policies perform better overall and benefit less from optimization against an RM as measured by increase in gold reward, they lead to very similar amounts of overoptimization, as measured through the gap between the proxy and gold scores (which indicates the shortfall between predicted and actual reward), and KL distance at which the maximum gold RM score is attained. 虽然较大规模的策略总体表现更好，且从RM优化中获益较少(gold奖励模型计算)，但是它们会导致非常类似的过度优化(代理模型得分和gold模型得分差异) ，由此可以获得最优gold RM水平上的KL距离
- KL 惩罚无效。在我们的强化学习设置中，给代理reward模型增加KL散度距离，但这个设定并没有对gold 奖励模型打分有可观测的改善。而且这个结果对超参数特别敏感。

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240104163113.png)

除了 3.6 节之外，所有 RL 实验的 KL 惩罚都设置为 0

其他细节看这个[RLHF系列-Reward Model - 知乎](https://zhuanlan.zhihu.com/p/620877625) 即可；
基本上对于上面提到的scaling曲线进行了拟合，对于overoptimization的边界预测具有一定意义；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240104164929.png)

### RL vs BoN

- RL 的 KL 效率远低于 BoN. 如果将KL距离作为消耗的资源衡量，RL会比BoN消耗更多的RL. 这说明优化和过度优化都需要更多的KL，且一般在RL上表现更明显。直觉上，BoN围绕初始策略进行局部搜索，因此KLbon 大致随 log(n) 增加，RL则每一步都会根据前步骤的策略做策略提升，在没有KL惩罚的情况下，KL随步长近似呈二次增长趋势。这个说明 **KL 距离对于（过度）优化**的数量来说是一个**不充分的度量**。
- BoN和RL在代理RM和gold RM得分比较时是比较相似的。代理RM得分是另一可能的优化指标，因为它是直接优化的数值。将其作为优化的度量标准会比KL距离更反映RL 和 BoN 的相似性。在实验中观察到，RL在最开始的时候proxy-gold的差距很大，但随后能获得比BoN gold-得分更高的峰值。

