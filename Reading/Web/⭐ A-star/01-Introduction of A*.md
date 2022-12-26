> ref:  http://theory.stanford.edu/~amitp/GameProgramming/AStarComparison.html
>- new version: https://www.redblobgames.com/pathfinding/a-star/introduction.html
---
## Summary
在游戏中，经常需要找到从一个点到另一个点的路径。不仅需要寻找最短路径，还需要考虑运动时间。找到从 ⭐ 到 ❌ 的最短路径。
![[Pasted image 20210816194705.png]]

## Representing the map
**Input**: 图搜索算法，以 **graph** 作为输入。Graph 是 Nodes 🔵 和 Edges ➖的集合。
![[Pasted image 20210816195923.png]]
**Output**: 有 Node 和 Edge 组成的 **Path**。Path 仅表示抽象的拓扑概念，具体的运动形式由 Node 和 Edge 对应的物理信息表达。
![[Pasted image 20210816200311.png]]
**Tradeoffs**: 栅格 or 图, ref: [[03-Map representations]]。拓扑图搜索的更快；栅格图更容易交互，但会产生大量 Node。
![[Pasted image 20210816201117.png]]

## Algorithms
**Breadth First Search**  在每个方向上平等搜索。可用于常规路径查找、地图生成、流场路径查找、地图分析等场景。
**Dijkstra’s Algorithm** (also called Uniform Cost Search) 有限考虑 cost 较低的路径。通过改变 cost 来实现绕过树林、远离敌人等。
**A*** 在 Dijkstra 算法上，优化单个目标点场景。优先考虑看起来更接近目标点的路径。

### [Breadth First Search](https://www.redblobgames.com/pathfinding/a-star/introduction.html#breadth-first-search)
所有搜索算法的关键是跟踪边界扩展环 **frontier**，在栅格中，也叫 **flood fill**
![[Pasted image 20210817191804.png|400]]

```py
frontier = Queue()
frontier.put(start ⭐)
reached = set()
reached.add(start)

while not frontier.empty():
   current = frontier.get()
   for next in graph.neighbors(current):
      if next not in reached:
         frontier.put(next)
         reached.add(next)
```
这段代码只是遍历地图上的所有点，要像搜索路径，需要知道每个 Node 的来源，使用 *came_from* 替换 *reached*：
```py
frontier = Queue()
frontier.put(start ⭐)
_came_from_ = dict()
_came_from_[start] = _None_

while not frontier.empty():
   current = frontier.get()
   for next in graph.neighbors(current):
      if next not in _came_from_:
         frontier.put(next)
         _came_from_[next] = _current_
```
这样，*came_from* 就可以追踪我们来的地方，从而重建整个路径。
![[Pasted image 20210817193807.png|400]]
沿着箭头从 goal 找到 start，path 是 Edge 的序列，也可以存储为 Nodes：
```py
current = goal 
path = []
while current != start: 
   path.append(current)
   current = came_from[current]
path.append(start) # optional
path.reverse() # optional
```
### Early exit 提前退出
我们并不需要遍历所有可能的路径，一旦找到我们的目标，就可以停止搜索。
![[Pasted image 20210817194631.png]]
```py
frontier = Queue()
frontier.put(start )
came_from = dict()
came_from[start] = None

while not frontier.empty():
   current = frontier.get()

	# Early exit
   if current == goal: 
      break           

   for next in graph.neighbors(current):
      if next not in came_from:
         frontier.put(next)
         came_from[next] = current
```
更多提前搜索，参见：https://www.redblobgames.com/pathfinding/early-exit/

### Movement costs

在某些寻径场景中，不同场景下的移动有不同的代价。我们对比以**步数**为代价和以**距离**为代价的场景：
![[Pasted image 20210817200620.png]]
为此，我们需要使用 Dijkstra's Algorithm，为了计算 cost，我们引入 `cost_so_far`，描述从起点到当前位置的 cost。为了对比移动代价，我们将 `queue` 升级为 `priority queue`。最终，可能会以不同的 cost 多次访问同一个位置，需要选择最优的路径。
```py
frontier = PriorityQueue()
frontier.put(start, 0)
came_from = dict()
cost_so_far = dict()
came_from[start] = None
cost_so_far[start] = 0

while not frontier.empty():
   current = frontier.get()

   if current == goal:
      break
   
   for next in graph.neighbors(current):
      new_cost = cost_so_far[current] + graph.cost(current, next)
      if next not in cost_so_far or new_cost < cost_so_far[next]:
         cost_so_far[next] = new_cost
         priority = new_cost
         frontier.put(next, priority)
         came_from[next] = current
```

将 queue 改为 priority queue 改变了边界的扩展方式。下图以轮廓线展示边界扩展方式，其中森林中运动的代价更大。
![[Pasted image 20210817201609.png]]
引入 cost 会增加搜索的灵活性。ref: [[01-J 其他路径搜索算法]]

### Heuristic search 启发式搜索
如果需要遍历地图上所有位置的路径，可以使用 BFS 和 Dijkstra 。然而，通常只需要找到一条通往目标的路径；让边界向目标扩展，而不是其他方向。首先定义启发函数：
```py
def heuristic(a, b):
   # Manhattan distance on a square grid
   return abs(a.x - b.x) + abs(a.y - b.y)
```
在 Dijkstra 中，使用起点到当前点的实际距离进行优先排序。在 **Greedy Best First Search** 中，使用当前点到目标点之间的估计距离进行优先排序。距离目标点更近的点会优先探索。
以下是使用 优先队列替代 `cost_so_far` 的 Dijkstra: 
```py
frontier = PriorityQueue()
frontier.put(start, 0)
came_from = dict()
came_from[start] = None

while not frontier.empty():
   current = frontier.get()

   if current == goal:
      break
   
   for next in graph.neighbors(current):
      if next not in came_from:
         priority = heuristic(goal, next)
         frontier.put(next, priority)
         came_from[next] = current
```
![[Pasted image 20210818125007.png]]
对于更复杂的地图，找到的 path 不是最短的，在障碍物不多的场景，可以加速搜索：
![[Pasted image 20210818125353.png]]

### The A* algorithm
Dijkstra 可以找到最短的路径，Greedy Best First Search 的搜索更快。A* 使用起点到当前点的真实距离和到目标点的估计距离作为 cost。
```py
frontier = PriorityQueue()
frontier.put(start, 0)
came_from = dict()
cost_so_far = dict()
came_from[start] = None
cost_so_far[start] = 0

while not frontier.empty():
   current = frontier.get()

   if current == goal:
      break
   
   for next in graph.neighbors(current):
      new_cost = cost_so_far[current] + graph.cost(current, next)
      if next not in cost_so_far or new_cost < cost_so_far[next]:
         cost_so_far[next] = new_cost
         priority = new_cost + heuristic(goal, next)
         frontier.put(next, priority)
         came_from[next] = current
```
**Compare**
- Dijkstra 计算起点到当前点的距离
- Greedy Best-First Search 估计当前点到目标点的距离
- A* 同时计算两个距离
![[Pasted image 20210818130148.png]]
![[Pasted image 20210818130252.png]]
A* 权衡两个代价，只要启发函数不高估距离，A* 就会找到最佳路线。A* 使用启发函数对 Node 重新排序。

### More
在 [[01-J 其他路径搜索算法]] 中，给出部分代码示例。
- 怎么选择算法
	- Breadth First Search / Dijkstra 适用于遍历地图中的所有位置。
		- Breadth First Search：所有地方 cost 一致
		- Dijkstra ：不同场景 cost 不一致
	- Greedy Best First Search / A* 搜索一个或几个 goal 的路径。
		- 尽量使用 A*，想用 Greedy Best First Search 时，考虑不可接受的启发式 A* ([“inadmissible” heuristic](https://en.wikipedia.org/wiki/Admissible_heuristic))
- 性能提升
	- Graph 的大小
	- queue 的排序

## 其他
- 一个路径搜索的数据集 https://www.movingai.com/benchmarks/grids.html