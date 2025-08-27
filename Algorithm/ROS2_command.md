## 1. 创建功能包
```
$ cd ~/dev_ws/src
$ ros2 pkg create --build-type ament_cmake learning_pkg_c # C++
$ ros2 pkg create --build-type ament_python learning_pkg_python # Python
```
## 2. colcon build
	--packages-select：编译工作空间下面的某一个功能包
```
colcon build --packages-select <package_name>
```
## 3. ros2 bag
### 3.1 常用命令
| 功能      | 命令                                           |
| ------- | -------------------------------------------- |
| 录制话题    | `ros2 bag record <topic1> <topic2> ...`      |
| 回放数据    | `ros2 bag play <bag_path>`                   |
| 查看信息    | `ros2 bag info <bag_path>`                   |
| 转换格式    | `ros2 bag convert <bag_path>`                |
| 压缩 / 解压 | `--compression-mode`, `--compression-format` |

### 3.2 录制数据
```
ros2 bag record /topic1 /topic2 //只录制了指定的话题
```

| 选项        | 说明       |
| --------- | -------- |
| -o <name> | 指定输出文件夹名 |
| -a        | 记录所有话题   |

### 示例：
```
ros2 bag record -o my_bag /scan /tf --compression-mode file --compression-format zstd
```
### 3.3 回放数据
```
ros2 bag play my_bag
```

| 选项         | 说明                               |
| ---------- | -------------------------------- |
| --rate <n> | 以 `n` 倍速度播放（默认 1.0）              |
| --loop     | 循环播放                             |
| --topics   | 指定回放话题                           |
| --clock    | 发布 `/clock` 话题（支持 `use_sim_time` |
```
ros2 bag play my_bag --rate 0.5 --loop --clock
```
#### 回访数据并重映射
```
ros2 bag play my_lidar_bag --remap /scan:=/my_scan
```
意思是：播放时把 bag 文件里的 `/scan` 映射为 `/my_scan` 来发出
可以映射多个：
```
ros2 bag play my_lidar_bag \
  --remap /scan:=/lidar_undistorted \
  --remap /odom:=/odom_filtered
```
### 3.4 查看信息
```
ros2 bag info my_bag
```
## 4. ros2 topic

| 选项   | 说明                 |
| ---- | ------------------ |
| list | 查看当前所有话题           |
| echo | 实时打印话题内容           |
| info | 查看某个话题的消息类型和接收发布数量 |
| type | 查看某个话题的消息类型        |
### 4.1 ros2 topic list
示例：
```
ros2 topic list
```
输出：
```
/tf
/odom
/scan
/camera/image_raw
```
### 4.2 ros2 topic type
示例：
```
ros2 topic type /odom
```
输出：
```
nav_msgs/msg/Odometry
```
### 4.3 ros2 topic echo
示例：
```
ros2 topic echo /odom
```
输出：
```
header:
  stamp:
    sec: 1646903840
    nanosec: 123456789
  frame_id: "odom"
child_frame_id: "base_link"
pose:
  pose:
    position:
      x: 1.0
      y: 2.0
      z: 0.0
    orientation:
      x: 0.0
      y: 0.0
      z: 0.0
      w: 1.0
...
```
### 4.4 ros2 topic info
示例：
```
ros2 topic info /odom
```
输出：
```
Type: nav_msgs/msg/Odometry
Publisher count: 1
Subscription count: 2
```
## 5. tf变换
### 5.1 查看/tf主题（所有的实时变换）
```
ros2 topic echo /tf
```
### 5.2 查看/tf_static主题（所有的静态变换）
```
ros2 topic echo /tf_static
```
### 5.3 查看两个坐标系之间的变换
```
ros2 run tf2_tools tf2_echo <frame1> <frame2>
```
### 5.4 监控整个系统中的tf状态
```
ros2 run tf2_tools tf2_monitor
```
## 6. 查看消息类型的字段结构
```
ros2 interface show <消息类型>
```
示例：
```
ros2 interface show sensor_msgs/msg/LaserScan
```
输出：
```
std_msgs/Header header
  builtin_interfaces/Time stamp
  string frame_id
float32 angle_min
float32 angle_max
float32 angle_increment
...
```
## 7. ros2 param
### 7.1 列出某个节点的参数
```
ros2 param list /node_name
```
### 7.2 获取某个参数值
```
ros2 param get /node_name param_name
```
示例：
```
ros2 param get /amcl use_map_topic
```
### 7.3 设置参数值（实时修改）
```
ros2 param set /node_name param_name value
```
示例：
```
ros2 param set /amcl use_map_topic true
```
### 7.4 描述参数信息
```
ros2 param describe /node_name param_name
```
### 7.5 加载参数文件
```
ros2 param load /node_name params.yaml
```
### 7.6 删除参数
```
ros2 param delete /node_name param_name
```