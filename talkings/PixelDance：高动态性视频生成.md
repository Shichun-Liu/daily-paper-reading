
最早的阶段，transformer

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226140759.png)

后面接diffusion model生成高质量图片

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226140929.png)

文本生成视频

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226141100.png)


### 视频编辑
基于已有的视频；

![image.png](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226141323.png)

训练的时候没有配对的编辑数据；

### 挑战
现在text to video的质量并不高，或者需要对于prompt的进行非常详尽的描述；
非常长的文本，那么对于文本理解也是一个挑战；
![image.png|625](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226141751.png)

- text to image现在做的很好，stable diffusion/DALLE-3；Adobe PS局部修改功能；
- **核心观点：不要只用文字指令，而是使用图片指令！**（直接解决前面的两个问题）

![image.png|625](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226142657.png)

- 可以先用文生图模型把图片跑出来

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226142312.png)

- 一些非常难的motion文字难以描述清楚，那么同样替换为图片指令；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226142423.png)

image instruction可以实现很多训练集里面没有出现过的图片；

## 模型

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226142757.png)

渐变过渡（用户提供image guidance的时候模型要soft贴合）

![image.png|650](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231226143017.png)

（效果展示）
在动态画面生成的效果较好；PixelDance