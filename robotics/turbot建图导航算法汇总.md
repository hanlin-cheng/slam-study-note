# turbot建图，导航算法汇总 

## **建图** 

1. gmapping： https://github.com/ros-perception/openslam_gmapping.git，可以跑，输入：scan，tf，输出：map（pgm格式），tf。 
2. hector： https://github.com/tu-darmstadt-ros-pkg/hector_slam.git ，可以跑，初始画面非常抖动，输入：scan，initial_pose，tf，输出：tf，map（pgm格式）。 
3. karto：跑不了，vlp雷达不支持，格式转化问题 
4. Cartographer 2D： https://github.com/googlecartographer/cartographer.git，https://google-cartographer-ros.readthedocs.io/en/latest/compilation.html可以跑，输入：odom，tf，imu，scan（为什么不是points数据，因为算法是2d建图，雷达只有点云数据输出，要获得scan数据必须通过一层转化），输出：tf，submap_list。 
5. Cartographer 3D：3D不能直接保存地图，可以跑，需要离线跑。输入：odom，imu，velodyne_points，tf，输出：constraint_list，landmark_poses_list，scan_matched_points2，trajectory_node_list，submap_list，tf。坐标系走着走着会歪，机器人倾斜和地图角度不对，会把人建图进去，没有闭环检测，最后回来图对不上。 
6. LOAM：也是3D建图，坐标系走着走着会歪，机器人倾斜和地图角度不对。官网的程序，输入感觉就是激光雷达点云数据，输出，输出是一些点云数据。只用了点云数据建图，还可以融合imu数据但是例子没有。 
7. LeGO LOAM：也是3D建图，坐标系走着走着会歪，机器人倾斜和地图角度不对，走同样的路线生成了好几个不同的图。LeGO-LOAM在LOAM的基础上增加了回环检测。输入：激光点云，输出：轨迹，特征点云。 
8. BLAM：输入：激光点云，输出：tf，初始坐标系是正确的，但是走了两圈之后整个场景就倾斜了。 
9. Rtabmap建图（zed）：可用，建完图直接ctrl c退出会自动保存，建图的时候是3D的，但是保存是2D的。输入：scan（zed深度信息转化成），zed左矫正图，camera_info，tf，深度信息。输出：map，tf，cloud_map，map_data 
10. ORB_SLAMv2建图（zed）：单目基本用不了，特征点丢失严重，而且是非常慢的速度。Mono：输入为zed未矫正图像image_raw，输出没有用rviz方式展示，没有rqtgraph。整个效果很差。 
11. LOAM离线建图：没什么区别。 
12. Cartographer 3D离线建图：可以转化成pcd文件查看。 
13. LeGO LOAM离线建图：建不了。 
14. BLAM离线建图：从离线地图看出，在转角的时候里程坐标系和base坐标系分离了，导致整个地图歪了。 
15. 双目建图gmapping建图（zed）：2D，效果还可以，输入：scan（转化的深度信息），tf，输出：tf，map（pgm，yaml） 
16. hector建图（zed）：2D，效果还可以，输入：scan，tf，initialpose，输出：tf，map，测试过程中压过了一根电线，小车轮胎离地剧烈摇晃，导致建图出现巨大误差，可见第一段视频，可能是没有回路检测的。 
17. karto建图（zed）：效果还可以，输入：scan tf 输出map tf 
18. Cartographer建图（zed）：效果还可以，输入：scan odom imu tf 输出submap_list tf 
19. 实时三维建图并进行目标检测：可以，使用rtabmap和yolo v3 
20. 离线三维建图：用的rtabmap，但是夹杂了yolo进去，导致离线包过大。 
21. DSO建图(zed)：和orbslam很像，输入只为未矫正图像，输出没有在graph体现，测试的时候里程跑飞且经常出现丢失建图卡死。 

## **导航** 

1. Cartographer 2D实时点云建图和导航：建图节点输入odom，tf，imu，激光点云，输出landmark_poses_list，scan_matched_points2，trajectory_node_list，submap_list，constraint_list，map。导航节点movebase输出：速度控制信号，move_base/action_topics，输入信号odom，tf，map，move_base_simple/goal，move_base/action_topics，scan（scan类型全都是单线？）。速度不是很连续都是急起急停，地面低矮障碍物导航会无视直接撞上，老板说目前主要检测都是跟传感器同一个平面的障碍物，比传感器高的可以检测到，低的就没法检测了。 
2. Cartographer 3D实时点云建图和导航：建图节点输入odom，tf，imu，激光点云，输出landmark_poses_list，scan_matched_points2，trajectory_node_list，submap_list，constraint_list，map，tf。导航节点输入move_base/global_costmap/footprint，move_base/local_costmap/footprint，move_base/goal，move_base_simple/goal，scan，map，odom，tf，输出：速度控制信号，move_base/global_costmap/footprint，move_base/local_costmap/footprint，move_base/goal。问题：放一团电线在地上让他走过去，地图和小车的定位可能会跑飞，影响里程计，只能基于平地，里程计最好不要长时间运行会有累计误差。 
3. movebase激光导航-定点导航：使用amcl定位+movebase激光导航（激光只检测传感器同一平面障碍物），地图要求已知，amcl输入：scan，initial_pose，tf，输出：tf，particalcloud，diagnostics，导航输入：odom，move_base_simple/goal，map，tf，scan，move_base/global_costmap/footprint，move_base/local_costmap/footprint，move_base/goal，输出：速度控制信号，输出：/move_base/NavfnROS/plan，/move_base/global_costmap/costmap，/move_base/global costmap/footprint，/move_base/global_costmap/costmap_updates，/move_base/ DWAPlannerROS/trajectory_cloud，move_base/DwAPlannerROS/global_plan，/move base/DwAPlannerROS/cost_cloud，/move_base/DWAPlannerROS/local_plan，/move base/local_costmap/footprint，/move_base/local_costmap/costmap_updates，/move base/local_costmap/cosmap，/move_base/goal。 
4. rtabmap实现视觉定点导航：输入：scan，tf，左边矫正图像，左边相机参数，深度信息，输出：map，mapdata，tf，cloud_map。Movebase输入：map，scan，tf，odom，move_base_simple/goal，move_base/global_costmap/footprint，move_base/local_costmap/footprint，move_base/goal，输出：速度控制信号，/move_base/global_costmap/costmap，/move_base/global costmap/footprint，/move_base/global_costmap/costmap_updates，/move base/local_costmap/footprint，/move_base/local_costmap/costmap_updates，/move base/local_costmap/cosmap，/move_base/goal。低矮物品看不到，撞到垃圾桶等。 
5. 三维建图-三维导航：是rtabmap的导航，直接用9或者19建图然后再直接导航。输入：odom，scan，tf，左边矫正图像，左边相机参数，深度信息，输出：map，mapdata，tf。Movebase输入：map，scan，tf，odom，move_base_simple/goal，move_base_simple/goal，move_base/global_costmap/footprint，move_base/local_costmap/footprint，/move_base/global，输出：/move_base/NavfnROS/plan，/move_base/global_costmap/costmap，/move_base/global costmap/footprint，/move_base/global_costmap/costmap_updates，move_base/DwAPlannerROS/global_plan，/move_base/DWAPlannerROS/local_plan，/move_base/global_costmap/costmap，/move_base/global costmap/footprint，/move_base/global_costmap/costmap_updates，/move_base/goal。 