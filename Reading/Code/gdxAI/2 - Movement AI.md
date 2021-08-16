# 2. Movement AI
## 2.1 Steering Behaviors 转向行为
- seeking 寻求，fleeing 逃离，wandering 徘徊，arriving 到达，pursuing 追随，evading 逃避，avoiding obstacle 避障，following path 寻径；
- 与 keeprun 类似，需要注意连续任务之间可能存在相互抵消的情况，需注意手动断开；
- ==有一些任务是没有目标点的==，考虑后续任务时需注意
### 2.1.1 Independent Facing
- 是否强制插入旋转动作
- 否，直接根据切线对角度赋值【游戏角色】
- 这个接口挺有意思的 https://libgdx.badlogicgames.com/ci/gdx-ai/docs/com/badlogic/gdx/ai/steer/Steerable.html

### 2.1.2 Individual Behaviors
- **Seek** ^f2c0b6
	- 直奔目标点，类似势场？
- **Flee**
	- 与 seek 相反，反向加速远离
- **Arrive** ^c2c2b4
	- 与 [[#^f2c0b6 | seek]] 不同的是：到达目标点时，速度为零 
	- `Arrival tolerance`, `Deceleration radius`
	- ![[Pasted image 20210811194700.png|300]]
- **Reach Orientation** ^04ba17
	- 原地旋转动作
	- `Align tolerance` 防止到点摆动, `Deceleration radius`
- **Pursue** 追随 ^85183b
	- 预测目标点 T 时刻之后的位置，追随
	- 以最大速度计算目标耗时，即为 T，需限制最大耗时
- **Evade** 逃脱
	- 与  [[#^85183b | pursue]] 相反，反向加速逃离 
- **Face** / face-to ^a19b37
	- 计算目标方向，并通过 [[#^04ba17 | ReachOrientation]] 执行旋转动作
	- 若 vehicle 不支持  **independent facing** ，需要自动将 heading 与运动切线方向对齐，所以不需要该动作；
- **Look Where You Are Going** ^51d2c2
	- $v>0$时，看向前探点；$v==0$时，看向终点；
	- 类似前探和预瞄
- **Wander**
	- [[#^f2c0b6 | seek]] + [[#^51d2c2 | look  where you are go]]
	- [[#^a19b37 | face]] + $acc_{Max}$
	- 在每个 `TimePiece` 刷新目标点
```ad-note
在任何情况下调用 Task 切换时，都需要考虑 vehicle 当前速度、角度、角速度，预留切线方向的空间，防止剧烈抖动。
```
- **Flow Field Following**
	- 需要势场地图，根据位置获得 `flow vector`
	- 流场可以用于模拟各种效果，磁场、河流、阵风，可以是时变流场，也可用于轨迹预测
	- ![ff|300](https://cloud.githubusercontent.com/assets/2366334/4008930/a985046c-29dd-11e4-8ed4-44925cd59bc0.png)
- **Path Following**
	- `arrivalTolerance` 防止在接近 Tgt 时，出现抖动
	- `decelerationRadius` 进入减速圈
	- 有重叠轨迹时，可能会忽略部分路径，需要考虑兼容性
	- ![pp|360](https://cloud.githubusercontent.com/assets/2366334/4009031/f1423eae-29de-11e4-916d-05ac9b3ba414.png)
- **Interpose** 插点
	- 场景：为雇主挡子弹，拦截传球，守门
- **Match Velocity**
	- 匹配目标速度，与 [[#^c2c2b4 | Arrive]] 类似（目标速度为 0）
- **Jump** 跳跃
	- 跳一跳、穿墙、弹簧特效
	- 助跑 -> 到达跳跃点 -> 起跳 -> 腾空
	- 空中扰动影响较大，需要与目标点绑定
	- 对**路径搜索**有影响，需要在地图中对 **跳跃点对** 进行维护；
- **Group Behaviors**
	- 类似多智能体概念
	- 群体动作包括：分离、对齐、内聚
	- `findNeighbors` 临时组队
	- **Separation** 分离
		- ![sep|300](https://cloud.githubusercontent.com/assets/2366334/3997438/5f2a0adc-293f-11e4-833b-a9ad4439ad91.png)
	- **Alignment** 对齐
		- ![|300](https://cloud.githubusercontent.com/assets/2366334/3997427/4fd47edc-293f-11e4-8df1-8f60d2311ce1.png)
	- **Cohesion** 聚合
		- ![|300](https://cloud.githubusercontent.com/assets/2366334/3997430/57ea14ce-293f-11e4-82fe-e6170c66b6f2.png)
	- **Hide** 隐藏
		- 找到目标的盲区，并躲藏
		- 通常定义为[组合动作](https://libgdx.badlogicgames.com/ci/gdx-ai/docs/com/badlogic/gdx/ai/steer/behaviors/PrioritySteering.html)，没有掩体时，远离目标 
		- ![|300](https://cloud.githubusercontent.com/assets/2366334/3997420/36a31180-293f-11e4-9816-31cb27a6b629.png)

```ad-tip
- 空间数据结构： multi-resolution maps, quad-trees, oct-trees, and binary space partition (BSP) trees
- 快速获取可选节点，Spatial partitioning techniques 空间分区技术至关重要，可参考 Bullet or Box2d
```
- **Collision Avoidance**
	- 势场法
	-  **Raycast Obstacle Avoidance**
		-  通过射线探测障碍物，同时建立斥力场
		-  几种常规射线配置：[SingleRayConfiguration](http://libgdx.badlogicgames.com/gdx-ai/docs/com/badlogic/gdx/ai/steer/utils/rays/SingleRayConfiguration.html), [ParallelSideRayConfiguration](http://libgdx.badlogicgames.com/gdx-ai/docs/com/badlogic/gdx/ai/steer/utils/rays/ParallelSideRayConfiguration.html), and [CentralRayWithWhiskersConfiguration](http://libgdx.badlogicgames.com/gdx-ai/docs/com/badlogic/gdx/ai/steer/utils/rays/CentralRayWithWhiskersConfiguration.html).
		-  ![|300](https://cloud.githubusercontent.com/assets/2366334/3997628/fc884af4-2940-11e4-9b7a-58a023c511cc.png)

## 2.2 Formation Motion
> 队列运动，类似多智能体
### 2.2.1 Fixed Formations 固定队形
- leader-follower
### 2.2.2 Scalable Formations 弹性队形
- 根据 vehicle 数量，灵活决定队形
- 可以动态调整
### 2.2.3 Two-Level Formation Motion 两层编队
- Replacing the Leader with an Anchor Point
	- 防止 Leader 闪避、抖动时，带偏队形
	- 防止 Leader 挂掉时，选择副领队
- Moderating the Formation Movement
	- 当全部成员掉队时，虚拟 Leader 是否继续更新？
	- 考虑将 Anchor 置于全部 vehicle 的质心
### 2.2.4 Multi-Level Formation Motion多层编队
### 2.2.5 Slot Assignment Strategies 槽位分配策略
- Hard and Soft Roles
	- 通过最优化，完成合适的角色分配
### 2.2.4 Dynamic Slots and Plays



---
Prev [[0 - Summary]]
Next [[3 - Pathfinding]]