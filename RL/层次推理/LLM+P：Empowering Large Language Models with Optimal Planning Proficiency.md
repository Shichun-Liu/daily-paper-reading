[[2304.11477] LLM+P: Empowering Large Language Models with Optimal Planning Proficiency](https://arxiv.org/abs/2304.11477)
[LLM+P Empowering Large Language Models with Optimal Planning Proficiency - YouTube](https://www.youtube.com/watch?v=v8kNoaH0Wso)

## 主要内容

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240115162758.png)

- 将经典规划器的优势纳入 LLM 的框架；
- LLM+P 通过首先将语言描述转换为以规划域定义语言 (PDDL) 文件，然后利用经典规划器快速找到解决方案，然后将找到的解决方案转换回自然语言；
- LLM最强的能力是语言建模，多种序列之间的转化；

### The Classical Planning Problem 

![image.png|400](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240115163248.png)

![image.png|400](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240115163300.png)

- 经典规划最早被用于机器人领域（Shakey，PRODIGY，HTN）；
- 任务和运动规划(TAMP)是一个分层规划框架，它结合了离散空间中的经典规划和连续空间中的机器人运动规划；
- 大多数规划方法都需要**特定领域的编程语言**作为问题及其解决方案的表示。

### LLM+P
To summarize, the assumptions we need for **LLM+P** are:
1) A robot knows when to trigger LLM+P based on its conversation with a human user. 
2) A domain PDDL is provided to define the actions that the robot is capable of. This specification is taskagnostic — the entities relevant to the task are specified in the LLM-generated problem PDDL. 
3) A simple problem description in natural language and its corresponding problem PDDL file are also provided.
