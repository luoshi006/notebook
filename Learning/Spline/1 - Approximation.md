# 拟合
## 场景
- 有一堆点，找到贴合效果最好的一条曲线
- 保证严格穿过两侧端点，逼近其余中间点

## Notes
- 使用 uniform knots 能够改善矩阵病态现象，保证求解鲁棒；**需要保证 control points 个数不大于比采样点的一半**；*Nyquist-frequency*
- 因 B 样条的局部支撑性，求解矩阵呈带状对角性质；














