---
tags:
  - math
  - MCTS
date: 2022-12-29
---
CoRe (Zhu et al., 2022) fine-tunes reasoning step **generator** and **verifier** for math word problems with MCTS for decoding.

![image.png|450](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102211203.png)
- 快思考，慢思考；
- 通过验证器提供两种粒度的反馈信号，给出了过程监督；The **Verifier** checks the **feasibility and correctness** of the generator's content and collaboratively solves the reasoning task together.

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240102212004.png)

