## Feedback Enhanced Lattice Planner [FELP]
- Intelligent Driver Model [IDM]
- Behavior planning & Trajectory planning
	- Behavior 和 Trajectory 代价函数不同，可能会导致不一致问题
- 离散时空空间，通过图搜索得到最优轨迹
### 地图
- **feature**
	- 反映局部环境的几何结构
	- 对交通规则编码
	- 高效的构建、更新、访问
- 常用大场景地图
	- `OpenStreetMap`
	- *`OpenDrive`
	- `Lanelet/Lanelet2`

---
- Receding Horizon Control [RHC]