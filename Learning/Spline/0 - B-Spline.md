# B-Spline Curve
## 贝塞尔曲线、哈密特曲线、B 样条曲线对比
#TODO
![](https://pages.mtu.edu/~shene/COURSES/cs3621/NOTES/spline/Bezier/hierarchy.jpg)

## 基本概念
### 术语
- $n+1$ 个**控制点** $P_0, P_1,...,P_n$
- $m+1$ 个**节点 knots**： $U={u_0, u_1,...,u_m}$
- 次数 degree: $p$
- order: $p+1$
- 约束： $m=n+p+1$
- $C(u_i)$ 节点点（**knot point**），将 B 样条曲线划分为 **曲线段** ，每个曲线段都定义在一个 **节点区间** 上。
- 重复度 multiplicity，同一个 knot 出现的次数。

### Definition
![](https://pages.mtu.edu/~shene/COURSES/cs3621/NOTES/spline/B-spline/bspline-curve-eqn.jpg)
其中，$N_{i,p}(u)$ 是 $p$ 次 B 样条**基函数**。
#### B-Spline Basis Functions
- **Definition**
	- 定义域由 knots 分割
		- B 样条曲线的定义域应该是 $[u_0, u_m]$，由 $u_i$ 分割；但，为方便使用，通常使用 $[0,1]$ 作为定义域。
	- 基函数在一些相邻的区间中是非零的，即，local 局部支撑性

_Cox-de Boor recursion formula_
![](https://pages.mtu.edu/~shene/COURSES/cs3621/NOTES/spline/B-spline/bs-basis.jpg)
其中，$i$ 为节点序号，$p$ 为基函数的次数
**在任何一个节点区间 $[u_i, u_{i+1})$ ,** **最多有 _p_+1** **个_p_** **次基函数非零。**

- **性质**
	- 局部支撑性：$N_{i,p}(u)$ 是在$[u_i, u_{i+p+1})$ 上的非零多项式

#### 曲线类型
**均匀 B 样条曲线**
- knots 均匀分布

**准均匀 B 样条曲线**
- knots 两端节点具有 $p+1$ 重复度；其余节点重复度为 1，且均匀分布；

**分段 Bezier 曲线**
- knots 两端节点具有 $p+1$ 重复度；内节点重复度为 $p$ 

**非均匀 B 样条曲线**
	- knots 单调递增






















