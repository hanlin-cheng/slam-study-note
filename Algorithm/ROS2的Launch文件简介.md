## 1. 基本用法
```
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.substitutions import LaunchConfiguration
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        DeclareLaunchArgument(
            'use_sim_time',
            default_value='false',
            description='Use simulation clock (Gazebo)'
        ),

		DeclareLaunchArgument(
	        'master_id',
	        default_value='0',
	        description='ID of the master LiDAR'
	    ),
        
        Node(
            package='robot_state_publisher',
            executable='robot_state_publisher',
            name='state_publisher',
            parameters=[{
                'use_sim_time': LaunchConfiguration('use_sim_time'),
                'master_id': LaunchConfiguration('master_id')
            }]
        )
    ])
```
### ==return LaunchDescription([...])==
这是你写的 Python 启动文件的最终返回结果，**告诉 ROS 该启动哪些节点/动作**。
```
def generate_launch_description():
    return LaunchDescription([
        # 所有的 launch 动作、参数声明等，都在这个列表里
    ])
```
### ==DeclareLaunchArgument(...)==
这个用来**声明可以从命令行传进来的参数**。比如你要让用户可以指定 `use_sim_time`、`map` 等值。
```
DeclareLaunchArgument(
    'use_sim_time',
    default_value='false',
    description='Use simulation (Gazebo) clock if true'
)
```
这个声明的是：你可以传 `use_sim_time:=true` 给这个 launch 文件。
### ==LaunchConfiguration(...)==
这个是**用来“获取”一个参数值**，它可以是你刚刚声明的 `DeclareLaunchArgument`，也可以是外部传进来的。
```
LaunchConfiguration('use_sim_time')
```
## 2. launch文件的套用
ROS 2 的 `launch` 框架支持**加载另一个 Python 格式的 launch 文件**的工具，它让你可以在一个 launch 文件中调用另一个 `.py` 的 launch 文件，就像 include 的概念一样。

| 工具                                          | 作用                       |
| ------------------------------------------- | ------------------------ |
| `IncludeLaunchDescription`                  | 用于嵌套加载 launch 文件         |
| `PythonLaunchDescriptionSource`             | 指明被 include 的是 Python 格式 |
| `PathJoinSubstitution` + `FindPackageShare` | 用于定位 launch 文件路径         |
| `launch_arguments={}`                       | 可传递参数到被 include 的 launch |

```
from launch import LaunchDescription
from launch.launch_description_sources import PythonLaunchDescriptionSource
from launch.actions import IncludeLaunchDescription
from launch.substitutions import PathJoinSubstitution
from launch_ros.substitutions import FindPackageShare

def generate_launch_description():
    return LaunchDescription([
        IncludeLaunchDescription(
            PythonLaunchDescriptionSource(
                PathJoinSubstitution([
                    FindPackageShare('my_robot_bringup'),
                    'launch',
                    'lidar.launch.py'
                ])
            ),
            launch_arguments={
                'use_sim_time': 'true'
            }.items()
        )
    ])
```
## 3. 运行launch文件
```
ros2 launch my_launch.launch.py use_sim_time:=true
```