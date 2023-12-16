[【强化学习的数学原理】课程：从零开始到透彻理解（完结）\_哔哩哔哩\_bilibili](https://www.bilibili.com/video/BV1sd4y167NS)

## 8-值函数近似

> [!note]
> 第一次引入神经网络；

mindmap

![[Pasted image 20231215231702.png|600]]

主要内容
![[Pasted image 20231216002802.png|500]]

### 引言

- 表格型方法：表格是指action-value的二维的表格表示；
- 缺点：state space或者action space较大、甚至连续时，表格型难以存储、难以泛化；
- 例子
![[Pasted image 20231216100642.png|475]]
- 一维state数量非常多，**使用一个v函数来近似表示各个state的value**（$\hat{v}(s,w)\approx v_\pi(s)$）；如此， 不用存储各个state对应的精确的value，而只需要存储曲线的参数（如线性拟合）；
	- 节省大量的存储；
	- 增强了泛化性；访问一个状态，会改变函数参数，其他state-value也会变化；
- 对于非线性的曲线而言，本质上对于参数w而言仍然是线性变化，对于变量s而言需要先设计一个kernel function进行变换；
![[Pasted image 20231216101104.png|450]]
- 也可以用神经网络做非线性的拟合；

### state-value 的近似
