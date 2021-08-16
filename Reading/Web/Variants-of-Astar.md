tag #A-star 
## Beam search
- 在 A* 的主循环中，`Open` 集中是需要搜索的所有 Node；
- Beam search 限制了 Open 集的大小，但需要通过自动排序的数据结构，保证最优路径不被删除