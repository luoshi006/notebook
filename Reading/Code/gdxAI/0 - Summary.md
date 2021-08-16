# 0. Summary
- 主页链接： https://github.com/libgdx/gdx-ai/wiki
- [Unity 5.x Game AI Programming Cookbook](https://books.google.com.hk/books?id=ebLjCwAAQBAJ&printsec=copyright&redir_esc=y&hl=zh-CN&sourceid=cndr#v=onepage&q&f=false)
# 1. Infrastructure
## 1.1 Message Handling
- 事件触发 / polling 轮询
	- `Well-designed games tend to be event driven.`
	- 主要在于频繁交互的场景
- 事件消息类型
	- Immediate Telegrams
	- Delayed Telegrams
## 1.2 Scheduling
- 调度场景
	- 地图区域的增量加载
	- 路径搜索
	- 决策逻辑切换
	- 资源调度
- 策略
	- **Frequency-Based Schedulers**
		- 以不同的频率，对不同的任务进行调度
		- **Frequency**：通过调节频率，可以减少算力竞争，但无法避免；
		- **Phase**：通过调整每个任务对应的相位，避免出现算力尖峰；【参考 LCM，有 2310 频率】
		- 举例
			- **Load Balancing Scheduler** 负载平衡调度
				- 每个任务执行完后，会重新计算剩余调度时间；超时的任务会挤占其他任务的时间
			- **Priority Scheduler** 优先级调度
				- 和负载平衡调度类似，根据优先级分配可获取的调度资源
	- **Hierarchical Scheduling** 分层调度
		- 在 基于**频率调度** 的系统中，加入基于**优先级调度**的对象；

# TODO
### Physics
-   [Box2D](https://github.com/libgdx/libgdx/wiki/Box2d) - One of the most popular physics libraries for 2D games.
-   [Bullet](https://github.com/libgdx/libgdx/wiki/Bullet-physics) - 3D Collision Detection and Rigid Body Dynamics Library.
-   [jbump](https://github.com/tommyettinger/jbump) - Easy to implement AABB collision detection useful for platformers and other simple 2D games.
- 图 https://github.com/earlygrey/simple-graphs
### HyperLap2D
- 场景搭建
https://github.com/rednblackgames/HyperLap2D

---
Next: [[2 - Movement AI]]