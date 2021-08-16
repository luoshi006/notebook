# Folder
```md
- grid_map				# 编译用的 Hub 包，没代码
- grid_map_core			# 核心包，依赖 Eigen
- grid_map_costmap_2d
- grid_map_cv
- grid_map_demos		# 动图示例对应的代码
- grid_map_filters		# 依赖 ros 滤波库的滤波器
- grid_map_loader
- grid_map_msgs			# ros msg / srv
- grid_map_octomap
- grid_map_pcl
- grid_map_ros
- grid_map_rviz_plugin
- grid_map_sdf
- grid_map_visualization	# 转为 ros 消息，并使用 rviz 可视化
```


# 移植
- 在 imx6Q 上需要考虑 Eigen -> Matrix 的移植

# Ref
- https://github.com/ANYbotics/grid_map.git