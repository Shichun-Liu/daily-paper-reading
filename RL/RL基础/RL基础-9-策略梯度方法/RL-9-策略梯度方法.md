[第9课-策略梯度方法（该方法的基本思路）\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1sd4y167NS?p=47&spm_id_from=pageDriver&vd_source=3f4448c688c0096124dbfa48b0a085c3)

目前最流行的方法；policy-base 直接建立有关于policy的目标函数，优化该目标得到最优策略；

![image.png|525](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216142606.png?token=AUFT4GZWE3BBKBSPMTOMYOLFPVBT2)

主要内容：

![image.png|325](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216142938.png?token=AUFT4G46BVVM3OQMRIINRKDFPVCBA)

## 基本概念
- 函数代替表格来表示 $\pi$ ;
- 优点：可以表示连续的s，泛化性也更好；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216144010.png?token=AUFT4G4JKWALFID6VKA4CUDFPVDIS)

### 三个基本的不同点
如何定义最优策略？
- 表格型：对所有state s，$v_{\pi^*}(s)\ge v_{\pi}(s)$ ;
- 函数型：使用scaler metrics；

如何获取一个action的概率？
- 需要通过网络进行一次计算；

如何更新策略？
- 通过改变函数的参数 $\theta$ 来更新策略；

求解问题的基本思路
- 定义目标函数/metrics； （怎么取？）
- 优化，求解最优参数（最优policy）（如何计算gradients？）

## Metrics
