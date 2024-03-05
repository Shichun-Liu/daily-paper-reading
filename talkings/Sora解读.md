---
date: 2024-02-27
tags:
  - 多模态
---


## 技术报告概要

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227140511.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227140706.png)

- patch：文本LLM中的**token**可以统一code，math和自然语言；视觉模型对应的就是**patch**，先把video/pic 降维为patch；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227140956.png)

- 包括DiT结构，输入为高斯噪声，输出为patch图片；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227141136.png)

- 网上的估算：~3B模型，根据DiT的参数量；
- 语言理解
	- re-caption：训练一个模型，针对图片给出长文本描述，做数据扩充；
	- 在推理时，使用GPT给短的prompt补全为更长，更多细节；
### 效果
- 可以生成任意1080p的视频（横屏，竖屏，或更低分辨率）；
- 正方形视频：用原生数据训练，不会出现“物体不完整”的问题；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227141753.png)

## ### 相关论文
### ViT: 什么是patch
- 之前的主流CNN，根据图像的特性，会引入人工归纳bias；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227142448.png)

方法：把一个图片patch（按固定大小分割），经过一个线性投影层+一维位置编码进行Embedding，放进transformer；后续把一维位置编码换成二维之后没有带来性能提升；
最左边的编码类似BERT CLS，传进分类头；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227142811.png)

BiT类似ResNet（改进版本），是之前的SOTA模型；从上图可以看出，计算量大大减少；但是，ViT要求预训练数据量足够大（如下图，且预训练的性能还没有收敛）

![image.png|575](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227143405.png)


### NaViT: 如何适应原生分辨率和比例的图片

![image.png|575](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227143651.png)

基于对于数据集的形状出发；
启发：NLP预训练对数据打包方式；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227143833.png)

原始图片的分辨率和形状都不一样，先patch，然后随机token drop（类似BERT的随机mask）；把这些patches打包为一个sequence并填充到固定长度，传进transformer层，并且不同来源的patch有mask；

### ViViT: 如何把ViT从image应用到video

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227144318.png)


### DiT: 如何把diffusion和Transformer相结合
- 先对训练数据添加高斯噪声，然后学习去噪，将数据又恢复原样；
- Scalable Diffusion Models with Transformers，其实强调scaling而不是把transformer引入diffusion模型；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227144753.png)

- 之前的diffusion model用U-net；

## 讨论

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227145316.png)

2. 真实世界视频，游戏场景；
3. 网上估计60s生成在10-12美刀；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227145825.png)

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20240227145835.png)

Diffusion+Transformer的优势在于高分辨率；
