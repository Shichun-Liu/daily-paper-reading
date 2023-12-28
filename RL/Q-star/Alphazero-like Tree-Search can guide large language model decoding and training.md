[LLM的快思考与慢思考路线之MCTS - 知乎](https://zhuanlan.zhihu.com/p/659230417)
[EBM-based Global Rank+MCTS for COT - 知乎](https://zhuanlan.zhihu.com/p/650438958)

当我们将LLM与MCTS结合时，赋予LLM慢思考的能力，一般需要解决以下问题：
1. 高效的rollout(采样)
2. evaluation-function
3. node-representation & edge-representation

今日复习分布式系统，没时间看论文