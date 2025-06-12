# cartographer相关参数解析

## 1.MAP_BUILDER

`MAP_BUILDER` 是 Cartographer 的 **总体管理器**，它负责多个轨迹的管理、地图的生成、线程分配、后端优化等。

| 参数                        | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `use_trajectory_builder_2d` | 设置是否使用 2D 构建器                                       |
| `num_background_threads`    | 后台线程数量（用于优化和子任务）                             |
| `collate_by_trajectory`     | 多轨迹数据是否按轨迹聚合                                     |
| `pose_graph`                | 与 Pose Graph（后端优化）相关的配置（如：约束构建、优化频率） |
|                             |                                                              |



## 2.TRAJECTORY_BUILDER_2D

`TRAJECTORY_BUILDER_2D` 是 **局部前端模块**，负责处理单条轨迹的激光帧匹配、里程计融合、子图生成等。

| 参数                                 | 说明                           |
| ------------------------------------ | ------------------------------ |
| `min_range`, `max_range`             | 激光最小/最大距离              |
| `use_imu_data`                       | 是否使用 IMU                   |
| `num_accumulated_range_data`         | 每多少帧合并为一次匹配         |
| `motion_filter`                      | 运动过滤器（减少计算）         |
| `submaps.num_range_data`             | 每个子图包含的激光帧数量       |
| `ceres_scan_matcher`                 | 非线性优化匹配器（用于精匹配） |
| `real_time_correlative_scan_matcher` | 实时相关匹配器（粗匹配）       |
|                                      |                                |



## 3.POSE_GRAPH

`POSE_GRAPH` 是地图构建和纯定位过程中的 **后端优化模块（后端）**，用于管理 **轨迹节点（Nodes）** 与 **子图（Submaps）**，执行 **回环检测**，建立并优化 **约束图（Constraint Graph）**，通过非线性优化生成一致的全局地图等。

| 参数                                       | 说明                               |
| ------------------------------------------ | ---------------------------------- |
| `optimize_every_n_nodes`                   | 每积累多少节点后执行一次优化       |
| `constraint_builder.sampling_ratio`        | 构建约束的采样率，影响速度和精度   |
| `constraint_builder.min_score`             | 匹配分数阈值，控制是否建立约束     |
| `global_sampling_ratio`                    | 全局匹配尝试的频率（回环）         |
| `global_constraint_search_after_n_seconds` | 节点添加后多久进行全局约束搜索     |
| `max_num_final_iterations`                 | Ceres 优化的最大迭代次数           |
| `huber_scale`                              | 鲁棒核函数参数，影响异常匹配的权重 |
| `matcher_translation_weight`               | 优化时平移项的权重                 |
| `matcher_rotation_weight`                  | 优化时旋转项的权重                 |
|                                            |                                    |