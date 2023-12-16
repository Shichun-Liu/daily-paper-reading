[第9课-策略梯度方法（该方法的基本思路）\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1sd4y167NS?p=47&spm_id_from=pageDriver&vd_source=3f4448c688c0096124dbfa48b0a085c3)

目前最流行的方法；policy-base 直接建立有关于policy的目标函数，优化该目标得到最优策略；

![image.png|525](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216142606.png?token=AUFT4GZWE3BBKBSPMTOMYOLFPVBT2)

主要内容：

![image.png|325](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216142938.png?token=AUFT4G46BVVM3OQMRIINRKDFPVCBA)

- [[#基本概念|基本概念]]
	- [[#基本概念#三个基本的不同点|三个基本的不同点]]
- [[#Metrics|Metrics]]
	- [[#Metrics#average value|average value]]
	- [[#Metrics#average reward|average reward]]
- [[#Gradients of the metrics|Gradients of the metrics]]
		- [[#average reward#重要性质|重要性质]]

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
### average value
定义为所有state-value的加权平均，权重d为S出现的概率分布；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216145311.png)

如何确定分布d？有两种情况：
- d 独立于 $\pi$ 
	- 求梯度的时候比较简单；
	- 写成 $d_0$；
	- 所有state出现的概率相同，则均匀分布；
	- 非常关心某一个状态 $s_0$ ，则极端情况下 $d_0(s_0)=1$；
- d 与 $\pi$ 有关
	- 平稳概率：直接求解 $d_{\pi}$，不动点 $d_{\pi}^T P_\pi=d_{\pi}^T$ ;
	- 在策略pi下，每个状态会被访问的概率；
- 也可以写出另一种形式

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216151425.png)


### average reward
定义为单步reward的平均；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216150534.png)

等价形式：一个经过无穷多步的轨迹中获得的reward平均到每一步上的值；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216150800.png)

忽略其实状态（既然是无穷多步，根据无穷级数的性质，那么其实位置不重要）

![image.png|425](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216150821.png)

- 以上2个metrics都是策略的函数；函数使用参数theta描述；
- 考虑了discounted case 和 undiscounted case；
- 这两个metrics是等价的，可以相互转化

![image.png|475](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216151158.png)

## Gradients of the metrics 
- 这些metrics的梯度计算是整个方法中最复杂的部分！
统一表示

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216162257.png)
等价表达

![image.png|475](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216162456.png)

推导

![image.png|475](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216162752.png)

可以用随机梯度下降代替expectation；

#### 重要性质
- $\pi(a|s, \theta) > 0$，使用softmax函数来保证；同时满足了归一化条件；

![image.png|475](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216163014.png)

h是feature function，用一个神经网络代替；
- 上面的softmax，action如果是无穷多个怎么办？这种方法失效，要用DPG（deterministic policy gradient）

## 梯度上升算法-REINFORCE
 
![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216164005.png)

由于 $q_\pi$ 是未知的，因此需要近似表达；

### 采样
- S 服从 d 分布，d理论上是平稳分布，实际上不太考虑；
- A服从pi分布，那么应该根据 $\pi(\theta_t)$ 得到 $s_t$；on-policy的策略；

### 理解算法
变形

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216164427.png)

优化 $\pi(a_t|s_t)$ 值；若beta>0，那么更新得到的theta得到的新的pi确实比之前的更大，也就是梯度上升；
- 从q出发，更新了更好的pi，也就是exploitation；
- 对于分母上，若之前选择的pi很小，那么下一次更新时就会增大pi，也就是exploration；

### REINFORCE
- $q_t$：用MC策略求得；

![image.png|500](https://raw.githubusercontent.com/Shichun-Liu/images-on-picgo/main/pics/20231216165305.png)

MC是off-line的方法，要采集完所有数据才能更新；