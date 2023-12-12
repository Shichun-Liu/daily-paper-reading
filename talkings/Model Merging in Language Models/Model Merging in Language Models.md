2023.12.12 赵君

主要内容
- 从专才到通才的转换；
- 更好控制模型的行为，消除bias和toxic；
- 提高参数效率；

## 专才与通才
### Language Models are Super Mario: Absorbing Abilities from Homologous Models as a Free Lunch
![[Pasted image 20231212133643.png|400]]
- delta为SFT后的模型与预训练模型的增量；
- 简单将参数相加，直观；
- 问题：参数冲突；math的模型和code的模型SFT参数在一些地方有冲突，直接相加会造成能力损失；
- 解决：随机丢弃一些参数的增量delta，可以发现随着LM的size增加，其对于任务的完成依然很好（math上70B甚至99%）；这说明SFT更新的大部分参数都是非常冗余的；
- （或许不应该随机drop，应该根据参数更新的幅值来drop，更新大的或许才是重要参数）
![[Pasted image 20231212133946.png]]
- 方案：通过丢弃大部分delta参数来解决参数冲突；
![[Pasted image 20231212134917.png]]
实验来看，在2、3个任务上面参数融合是有效的；但是在更多的任务融合性能会较大下降；

任务参数delta1和delta2的相加，和多个任务同时SFT（或者持续学习）得到的delta-multi是否相同？若任务相互独立，则可以实现任务的解耦合；

### LORAHUB:EFFICIENT CROSS-TASK GENERALIZATION VIA DYNAMIC LORA COMPOSITION
把多个任务得到的Lora模块统一存为一个hub；
基于具体任务对特定的lora模型进行组合，得到最终的权重；
![[Pasted image 20231212135732.png|600]]
缺点：效果有限；

## 消除bias
### EDITING MODELS WITH TASK ARITHMETIC
![[Pasted image 20231212140000.png|550]]
两个任务相加，基本上能力都能获得；
专门构造有害模型，通过取反增量可以实现消除别的模型的bias；
![[Pasted image 20231212140654.png|550]]
但是向量之间是有冲突的，论文里面没有体现；

## 提高参数效率
MOE
![[Pasted image 20231212141119.png|500]]
表示坍缩：可能路由策略喜欢把embedding全传入到少数几个expert中；
通过模型融合的方式，把不常被使用的专家模型融合在一起，提高参数效率和减小存储占用；
- 根据频率作为权重，将参数直接相加；
![[Pasted image 20231212141356.png|600]]
Merge之后发现矩阵的秩下降了（原因未说明），因此矩阵可以被进一步分解减小；
相比于剪枝的方案，该方法在大多数任务上得到了最好的结果；compress之后计算量和size进一步减小；
![[Pasted image 20231212142042.png|600]]
从结果来看，秩的下降会给结果带来一定损失但是不大；