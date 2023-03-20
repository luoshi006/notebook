## [2D Pathfinding Benchmarks](https://www.movingai.com/benchmarks/grids.html)
### [地图格式](https://www.movingai.com/benchmarks/formats.html)
```yaml
type 	octile 	// 八角型
height 	y		// 地图高度
width 	x		// 地图宽度
map				// 地图左上角 [0,0]
```

```yaml
. - passable terrain
G - passable terrain
@ - out of bounds
O - out of bounds
T - trees (unpassable)
S - swamp (passable from regular terrain)
W - water (traversable, but not passable from terrain)
```
#### 场景格式
- [HOG2 地图解析示例](https://github.com/FlorianPommerening/seminar-hs15-pathfinding-submission/blob/397862ef59e61c349ffed67059440f5e166fdad4/PathCompetition/ScenarioLoader.cpp)

|Bucket	|map	|map width	|map height	|start x-coordinate	|start y-coordinate	|goal x-coordinate	|goal y-coordinate	|optimal length
|--|--|--|--|--|--|--|--|--|
|0	|maps/dao/arena.map	|49	|49	|1	|11	|1	|12	|1|
|0	|maps/dao/arena.map	|49	|49	|1	|13	|4	|12	|3.41421|

### 示例
- 地图的解析和可视化
	- ![[Pasted image 20210823144857.png]]

## MAPF 
- https://www.movingai.com/benchmarks/mapf.html
- https://www.movingai.com/benchmarks/mapf/index.html

