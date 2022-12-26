# 插值
- https://pages.mtu.edu/~shene/COURSES/cs3621/NOTES/INT-APP/CURVE-INT-global.html
## 场景
- 已知采样点，求解样条曲线依次穿过采样点；

## Notes
### Parameter Selection Overview
B-spline 的拟合/插值的输入，通常是已知的一组点。
首先，需要建立 data points 和索引 parameters 之间的关系；即，data points $D_0,..., D_n$ 和 parameters $t_0,...,t_n$ 之间一一对应。
也就是说，如果 $C(u)$ 是穿过所有 data points 的样条曲线，则，$D_k = C(t_k)$。
![](https://pages.mtu.edu/~shene/COURSES/cs3621/NOTES/INT-APP/FIG-parameter-1.jpg)

> Note:
> 确定 parameters 的方法： 均匀采样、弦长法、向心法。
> refs: https://pages.mtu.edu/~shene/COURSES/cs3621/NOTES/INT-APP/PARA-knot-generation.html

- 弦长参数化  Arc Length Parameterization
	- 本身对曲线的描述友好，但是参数化过程中涉及到平方根的积分运算，算力较大；
- **Global**: 改变任意采样点，将引起整条曲线的变化；虽然使用 B-spline 拟合，但在 插值/拟合 过程中，没有局部支撑性。











