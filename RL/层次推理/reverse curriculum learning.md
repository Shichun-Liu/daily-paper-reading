
原理：[飞书 - 登录](https://fudannlp.feishu.cn/docx/GjlNdGmmZonjrSxugBkcRTrnnLe?from=from_copylink)
疑惑：既然逆课程学习是本文章的亮点，为什么不在preliminary或者related-work提及呢？

疑惑：既然逆课程学习是本文章的亮点，为什么不在preliminary或者related-work提及呢？

尽可能写的长一些详细一些，有删减的空间。我也可以更加精简。

  

故事：在机器人领域的goal-oriented task中，面对稀疏奖励的条件时，逆课程学习方法[1]是有效的强化学习训练技巧，是一种类似值迭代的动态规划思想。逆课程学习提出了两个假设：当agent处于目标点附近的状态时，其更容易达到目标；当agent从目标附近状态开始随机游走（采取random action），其达到的新状态依然较为容易达到目标状态（from where it is **not too much harder** to reach the goal）。该方法首先训练机器人从给定目标状态附近的初始状态达到目标。然后，利用这些知识，训练机器人从越来越遥远的初始状态解决任务。所有初始状态都是通过从先前的（上一轮迭代的）初始状态执行短随机游走（布朗运动）所生成。通过一系列不同的初始状态分布$\rho_i$的轨迹数据，来加速Agent在目标任务上的强化学习on-policy训练过程。

在我们的数学推理任务中，做以下定义：数学问题的最终步骤（答案求解步骤）得到的文本就是我们的goal，每一个推理过程视为一个action，推理得到的所有文本视为状态state。当推理处于最后一个步骤时，在前置推理步骤正确的条件下，Agent想要做出正确的推理，通常而言，只需要完成一次信息抽取，因此我们可以说最后一次推理是相对容易的，也就是满足假设1（这里可以用一个从step4开始推理的平均acc和随机位置开始推理的acc作为对比，说明“相对容易”的概念）；同时，在做出最后一次推理时，采取任意的推理行动，其能够马上获得最终的结果提供的监督信号，因此可以较快学习策略，使得Agent可以学会如何做出正确推理达到目标。这满足假设2。

![](https://fudannlp.feishu.cn/space/api/box/stream/download/asynccode/?code=ZTkzYTM0MjIxZTc4NTUzYTQwMzhlOGE3OTUxNmI0YTZfb09WUER1TElvUTBaN1I0RVk0S0kzMnNoM1RBUFdlRFNfVG9rZW46Tk9VaGJHd1BTb09CMFF4MXNheGNFMkpYblVlXzE3MDU3NTEyOTA6MTcwNTc1NDg5MF9WNA)

接着，我们将推理步骤前推。在我们的5步推理案例中（如图），为了不失一般性，我们假设第5、4步推理均已被policy学会，也就是说，在状态3附近的类似状态（可以理解为语义相近），在当前的策略下可以大概率推导出正确的状态4；而在状态3语义有较大差异的状态其不一定能够推理出正确的状态4（我们表示为4‘）。因此，当我们从状态2开始采样的时候，当其转移到3的邻域中，其rollout结果可以获得大于0的reward作为该推理action的监督信号；当Agent转移到距离3较远的状态时，其推导至正确结果的概率较小，因此策略在这些状态下获得的reward通常为0。在这里，我们不考虑由错误的中间推理过程得到正确结果的情况，不考虑多个推理步骤合并的跳步的情况。

从数据上来看，我们的工作更像是这篇工作[2]。我们从训练集中拆分轨迹，根据拆分步骤位置的不同，得到了不同的初始状态分布的轨迹数据。因此，我们所有初始状态都是目标可达的，相较于[1]的纯随机选择而言，训练的效率更高。而且因为数学推理任务的目标周围的语义空间通常而言具有非常多的约束，所以通过随机游走得到有效的目标可达点的概率是极低的。这也是我们的方法，从正确轨迹出发选择初始状态的优势，提高了数据利用的效率并降低了搜索空间。

因此，我们通过逐步前推初始位置的采样设置，使得策略在更新时，其获得监督信号的概率相较于随机设置初始位置更大。从直观上看，我们通过采样顺序的设置，完成了对于策略的中间过程的监督信号的给出，也就是一种无需人工标注的过程监督信号替代方案。

  

[1] FLORENSA C, HELD D, WULFMEIER M, et al. Reverse Curriculum Generation for Reinforcement Learning[J]. Conference on Robot Learning,Conference on Robot Learning, 2017.

[2] WU J, ZHANG D, ZHONG S, et al. Trajectory-based Split Hindsight Reverse Curriculum Learning[C/OL]//2021 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS), Prague, Czech Republic. 2021. http://dx.doi.org/10.1109/iros51168.2021.9636842. DOI:10.1109/iros51168.2021.9636842.

  
- 【类比】其实存在一些问题：比如数学推理并不一定都是固定长度（跳步），不同数学题对应的搜索空间本身就不一样（有的很广有的窄）。如果对于提到的两个假设，在推理任务上可以有对比/消融实验验证，那么更有说服力。
    
- 数学中可能存在一些并列的步骤，并不能说3、4步骤的顺序是确定的，遑论距离的概念；
    
- 我们的探索都是围绕示例轨迹周围进行的，因此没有探索完整状态空间，是一个弱点；不过可以用目标可达状态唯一性的假设来解释，就是可以说推理路径是狭窄的，探索多了也没什么用，错的居多；