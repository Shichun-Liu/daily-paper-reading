---
date: 2024-03-12
---
- 三篇文章，linear层压缩为2bit权重；
## 基础
量化目的：压缩模型，减少现存占用。
float32浮点型的权重近似为int8类型；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312133626.png)

- 量化对象：权重，激活，KV Cache，梯度（分布式训练时减少通信开销）；
- 量化形式：线性/非线性量化；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312133913.png)

- 量化分类

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312134100.png)

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312134218.png)

最常见、方便的方式是加载时量化（训练后量化）。

## OneBit量化

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312134435.png)

- 改变linear层；
- FFN层是主要计算开销所在；

权重量化：和clip操作类似；先对于权重容量进行调查，然后压缩为正负1；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312134631.png)


激活量化：8-bit；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312134740.png)

为了保证量化前后的方差接近，要对于x进行layernorm；最后反量化到原始的数值附近；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312134929.png)

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312134945.png)

- 计算效率：把之前所有的乘法的开销都转变为加法，只在最后一步反量化时候使用了惩罚；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312135012.png)

- scaling law 

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312135302.png)

- 结果

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312135513.png)

- bitnet可以从增加的学习率中受益；效果比较：

![image.png|550](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312135709.png)

![image.png|550](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312135840.png)

## 1.58 bits
每个参数取成3元{-1，1，0}；
基于bitnet，W1.58A8；
量化方案：RoundClip，靠近哪个就取哪一个；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312140049.png)

- 效果

![image.png|550](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312140259.png)

## OneBit-THU&HIT

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312140840.png)

比较上更加fair；基于第一篇工作的改进；

![image.png|550](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312141018.png)

- 主要设计，量化权重（不处理激活），灰色部分是1bit的权重矩阵，左右两个向量保持FP16；

![image.png|600](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312141147.png)

计算权重矩阵-SVID估计；W分解为符号矩阵乘上绝对值矩阵；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312141418.png)

- 数学上证明了，linear的计算可以分解为向量和矩阵（作为初始化g，h），精度损失在一定限度内；以llama的权重为分解的对象；
![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312141446.png)

再使用知识蒸馏提升模型能力（继续微调模型）。用原始LLaMa作为teacher；包括logits和hidden state的内容；
![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312141735.png)

- 实验结果
![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312141919.png)

知识蒸馏的代价其实不小（~预训练）；
压缩比超过90%；
![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312142040.png)

意义
![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240312142251.png)

