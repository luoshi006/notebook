# 3. Pathfinding
- **Graph Representation**
	- **Graph** 地图节点及连通性
	- **Connection** 节点之间的边，包含起点、终点、代价
	-  抽象 Node 之间的连通性搜索：[**Indexed A***](https://github.com/libgdx/gdx-ai/wiki/Indexed-A%2A)
	-  Node可以表示成子图：[**Hierarchical Pathfinding**](https://github.com/libgdx/gdx-ai/wiki/Hierarchical-Pathfinding)
-  **Finding a Path**
	-   [GraphPath](http://libgdx.badlogicgames.com/gdx-ai/docs/com/badlogic/gdx/ai/pfa/GraphPath.html) 表示在 Graph 中找到的路径，可通过 foreach 遍历
	-    [Heuristic](http://libgdx.badlogicgames.com/gdx-ai/docs/com/badlogic/gdx/ai/pfa/Heuristic.html) 给出到目标点的启发代价
	-    [PathFinder](http://libgdx.badlogicgames.com/gdx-ai/docs/com/badlogic/gdx/ai/pfa/PathFinder.html) 定义了路径搜索的算法，提供中断功能，便于时间片调度
## 3.1 A* (tag #A-star)
### 3.1.1 Introduction
- A* 是一种基础的启发式搜索算法，用于在 Graph 中搜索路径。
- A* 在离散的状态空间搜索，所以首先需要离散化；
	- Grid 栅格化
	- Delaunay triangulation 三角化
- 离散化后，实际 pose 与离散节点有不匹配问题
```ad-note
- 启发函数需要满足
	- **Consistency** 一致性：不违反三角不等式，两节点正向、反向的结果一致
	- **Admissibility** 可接收性：不会高估到目标点的距离；若可行路径存在，总能找到最短路径
```
### 3.1.2 The Algorithm
#### Description
##### Definitions
- **Node**
	- 在地图中，通常是具有 $(x,y)$ 坐标的点
	- 也可以是任何定义 `distance-to-goal` 的数据类型
- **output**
	- 空集(nil) 或 path (ordered set of nodes)
- **input**
	- start node
	- goal test func
	- expansion func
	- heuristic func
- **Open list**
	- 所有可用的未扩展节点，优先级队列
- **Close list**
- 记录易扩展节点，防止重复搜索和死循环
#### Heuristic Function `h(n)`
- 常见启发函数
	-   [Manhattan distance](https://en.wiktionary.org/wiki/Manhattan_distance) from node to goal (ignoring obstacles)
	-   [Euclidean distance](https://en.wikipedia.org/wiki/Euclidean_distance) (generalization of Pythagorean)
	-   [Chebyshev distance](https://en.wikipedia.org/wiki/Chebyshev_distance) (max of vector components)
	-   Octile distance (allows diagonal grid movement)

#### Mechanics
-   Sort open on `n.f`
-   Removing the first node `n` from open (lowest `n.f`, closest to goal)
-   Expand `n` by generating its children (usually proximal neighbors in the search graph)
-   Goal check
-   Add `n` to closed
-   Compute `f(n) = g(n) + h(n)` for each child node 代价+启发
-   Add each child to open

### 3.1.3 Performance & Complexity
- A* 最差情况可能退化成 BFS
- A* 的性能取决于
	- 排序的执行频率和效率
	- 底层数据结构
	- 启发函数的复杂度
	- 是否执行重复检测
	- 是否需要最优
- 原始 A* 不是 Anytime algorithm，是比较明显的缺陷。
- An anytime adaptation can be found [here](http://papers.nips.cc/paper/2382-ara-anytime-a-with-provable-bounds-on-sub-optimality.pdf). A **realtime adaptive** improvement can be found [here](https://www.cs.cmu.edu/~motionplanning/papers/sbp_papers/integrated2/koenig_realtime_adaptive_astar_aamas06.pdf).
```ad-note
> Anytime algorithm（任意时间算法）
任意时间(anytime)算法是一种解的质量随着时间的增加而逐步提高的算法.从本质上讲,任意时间算法是一种反复求精算法,它可以很快地生成一个不精确解,然后经过若干次重复过程逐步提高解的质量.由于它**可以在任意时刻中断并能输出一定质量的解**,所以称之为任意时间算法
```

### 3.1.4 Demos and Further Reading
- [tie-breaking](http://movingai.com/astar.html)
- [Weighted A*](https://www.cs.cmu.edu/~motionplanning/lecture/Asearch_v8.pdf)
- 谷歌大佬写的很快的版本：https://github.com/eaburns/search
- [Variants of A*](http://theory.stanford.edu/~amitp/GameProgramming/Variations.html)
## 3.2 Indexed A*
- 不再需要 `close list`
- 使用 [BinaryHeap](http://libgdx.badlogicgames.com/nightlies/docs/api/com/badlogic/gdx/utils/BinaryHeap.html) 做 `open list` 提高性能
- gdx 中  [IndexedAStarPathFinder](http://libgdx.badlogicgames.com/gdx-ai/docs/com/badlogic/gdx/ai/pfa/indexed/IndexedAStarPathFinder.html) 支持中断；
## 3.3 Hierarchical Pathfinding
### 3.3.1 Introduction
- Each stage of the path will consist of another route plan，使用抽象的拓扑连接描述路径
- 仅在需要的时候开始规划下一段任务，释放 CPU 算力【需要支持中断和调度】
- 对于许多实时寻路的应用场景，并不需要完整的路径。知道前几步的具体路径信息就够了，重要的是开始像正确的方向移动。一些事件可能导致后续的路径失效，从而触发重新规划；所以，没有必要在完整的路径上消耗算力。
- 大多数场景下，找到可行解比最优解更重要
### 3.3.2 The API
#### Hierarchical Path Finder
- 在 `HierarchicalGraph` 中，自顶向下搜索不同层级的路径。
- 在底层的规划器，不断将目标点更新为下一个任务段的终点，直到全局终点。
#### Hierarchical Graph
##### Nodes
- `HierarchicalGraph` 中，将位置靠近的一组Nodes归为一组，在上一层表示为Node
	- 例如：大楼 -> 每层楼 -> 每个房间 -> 房间内的Node
##### Connections
- 不同层级地图间的联通性表示
- `cost of connection`
	- 下图中 `C-D` 之间的代价，取决于从A、B 哪个方向到达；通常需要在分组中避免这种问题。
```md
                   +--+--+      
                   |A1|C1|      
                   +--+--+      
                      |C2|      
    A                 +--+      
     \                |C3|      
      C-D             +--+--+--+
     /                |C4|C5|D1|
    B                 +--+--+--+
                   +--+--+--+--+
                   |B1|C6|C7|D2|
                   +--+--+--+--+
                                
   Level 1           Level 0   
```
## 3.4 Path Smoothing
- 路径平滑算法比较简单，需要用到 raycast，可能略耗时
![|500](https://cloud.githubusercontent.com/assets/2366334/4967722/607250ae-6821-11e4-96d4-1ef3dd193240.png)


---
Prev [[2 - Movement AI]]
Next [[4 - Decision Making]]