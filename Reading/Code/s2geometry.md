- 谷歌开源的地图操作库

## 计算加速
- 将地球映射到单位球，弧度与距离近似相等；`S2Earth::KmToRadians` `S1Angle`
	- 因地球模型不是球形，近似误差 0.56%
- `S2RegionTermIndexer` 看起来有点像希尔伯特曲线
- 希尔伯特曲线能够保证空间上相邻的点，在标量索引上也相邻

## 点、边、多边形的快速查找
### `s2/s2closest_point_query_test.cc`
- `S2PointIndex<int>` 点队列
- `S2ClosestPointQuery<int>` 查询队列
	- `FindClosestPoints`
- `S2cap` 球型盖，
	- `center_`
	- `radius_`
- `S2Region`
	- `Contains(S2Point&)` 是否包含

```md
# s2closest_point_query_base.h
- FindClosestPoints 找最近点集
	-> FindClosestPointsInternal
		-> FindClosestPointsBruteForce 暴力搜索
			-> MaybeAddResult 遍历所有点, 更新distance_limit
				-> UpdateMinDistance
		-> FindClosestPointsOptimized 模糊搜索
- FindClosestPoint 找最近点
```


### `s2/s2closest_edge_query_test.cc`
- `S2ClosestEdgeQuery`









## TODO
