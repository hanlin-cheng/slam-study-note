# ROS指令

## ROS Shell命令

| 命令               | 功能                                                |
| ------------------ | --------------------------------------------------- |
| roscd              | 移动到指定的ROS软件包目录                           |
| rosls              | 显示ROS软件包的文件与目录                           |
| rosed              | 编辑ROS软件包的文件                                 |
| roscp              | 复制ROS软件包的文件                                 |
| rospd              | 添加目录至ROS目录索引                               |
| rosd               | 显示ROS目录索引中的目录                             |
| roscd [功能包名称] | 使用它，无需再使用cd一层层到查找，进入功能包里      |
| rosls [功能包名称] | 等价与roscd+ls。查看ROS功能包的文件列表更方便，快捷 |
| rosed [功能包名称] | 用于编辑功能包中的特定文件，优点也是快捷，修改容易  |

## ROS执行命令

| 命令                                  | 功能                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| roscore                               | 开启Master(ROS名称服务)                                      |
| rosrun                                | 运行单个节点                                                 |
| roslaunch                             | 运行多个节点及设置运行选项                                   |
| rosclean                              | 检查或删除ROS日志文件                                        |
| roscore [选项]。                      | 运行主节点，主节点管理节点之间的消息通信中的连接信息。关于主节点的具体作用就不累述了 |
| rosrun [功能包名称] [节点名称]。      | 执行指定的功能包中的一个节点的命令(rosrun rqt_graph rqt_graph 可查看当前系统的运行情况) |
| roslaunch [功能包名称] [launch文件名] | 类似dat批命令，是运行指定功能包中一个或设置执行选项的命令    |
| rosclean [选项]                       | 运行roscore时，对所有节点的记录都会写入日志文件，随着时间的推移，需要定期使用rosclean命令删除这些记录 |

## ROS信息命令 

| 命令       | 功能                    |
| ---------- | ----------------------- |
| rostopic   | 查看ROS话题信息         |
| rosservice | 查看ROS服务信息         |
| rosnode    | 查看ROS节点信息         |
| rosparam   | 确认和修改ROS参数信息   |
| rosbag     | 记录和回放ROS消息       |
| rosmsg     | 显示ROS消息类型         |
| rossrv     | 显示ROS服务类型         |
| rosversion | 显示ROS功能包的版本信息 |
| roswtf     | 检查ROS系统             |

### **rostopic [选项]** 

list - 列出活动话题 

echo [话题名称] - 实时显示指定话题的消息内容 

find [类型名称] - 显示使用指定类型的消息的话题 

type [话题名称] - 显示指定话题的消息类型 

bw [话题名称] - 显示指定话题的消息类型 

hz [话题名称] - 显示指定话题的消息数据发布周期 

info [话题名称] - 显示指定话题的消息 

pub [话题名称] [消息类型] [参数] - 用指定的话题名称发布消息，许多时候另开一个终端操作 例：rostopic pub -1 /turtle1/cmd_vel geometry_msgs/Twist – ‘[2.0, 0.0, 0.0]”[0.0, 0.0, 0.0]’ 



### **rosservice [选项]** 

list - 显示活动的服务信息 

info [服务名称] - 显示指定服务的信息 

type [服务名称] - 显示服务类型 

find [服务类型] - 查找指定服务类型的服务 

uri [服务名称] - 显示ROSRPC URI服务 

args [服务名称] - 显示服务参数 

call [服务名称] [参数] - 用输入的参数请求服务，通常用于测试服务 

 

### **rosnode [选项]** 

list - 查看活动的节点列表 

ping [节点名称] - 与指定的节点进行连接测试 

info [节点名称] - 查看指定节点的信息 

machine [PC名称或IP] - 查看该PC中运行的节点列表 

kill [节点名称] - 停止指定节点的运行 

cleanup - 删除失连节点的注册信息 



### **rosparam [参数]** 

list - 查看参数列表 

get [参数名称] - 获取参数值 

set [参数名称] - 设置参数值 

dump [文件名称] - 将参数保存到指定文件 

load [文件名称] - 获取保存在指定文件中的参数，经常使用。 

delete [参数名称] - 删除参数 

 

### **rosmsg [参数]** 

list - 显示所有消息 

show [消息名称] - 显示指定消息 

md5 [消息名称] - 显示md5sum 

package [功能包名称] - 显示用于指定功能包的所有消息 

packages - 显示使用消息的所有功能包 

 

### **rossrv [参数]** 

list -显示所以服务 

show [服务名称] - 显示指定的服务信息 

md5 [服务名称] - 显示md5sum 

package [功能包名称] - 显示指定的功能包中用到的所有服务 

packages - 显示使用服务的所有功能包 

 

### rosbag [参数] 

record [选项] [话题名称] - 将指定话题的消息记录到bag文件 

info [文件名称] - 查看bag文件的信息 

play [文件名称] - 回放指定的bag文件，这个使用玩的也多。 

compress [文件名称] - 压缩指定的bag文件 

decompress [文件名称] - 解压指定的bag文件 

filter [输入文件] [输出文件] [选项] - 生成一个删除了指定内容的新的bag文件 

reindex bag [文件名称] - 刷新索引 

check bag [文件名称] - 检查指定的bag文件是否能在当前系统中回放 

fix [输入文件] [输出文件] [选项] - 将由于版本不同而无法回放的bag文件修改可以回放的文件 

## ROS catkin命令

catkin_create_pkg - 自动生成功能包（示例命令：catkin_create_pkg [功能包名称] [依赖性功能包1] [依赖性功能包2]….）。 

catkin_make - 基于catkin构建系统的构建 （示例：catkin_make –pkg [包名] 只构建一部分功能包）。 

catkin_eclipse - 对于用catkin构建系统生成的功能包进行修改，使其能在Eclipse环境中使用。 

catkin_prepare_release - 在发布时用到的日志整理和版本标记。 

catkin_generate_changelog - 在发布时生成或更新CHANGLOG.rst文件。 

catkin_init_workspace - 初始化catkin构建统的工作目录。 

catkin_find - 搜素catkin，找到并显示工作空间。 

## ROS功能包命令 

rospack [选项] [功能包名称] - 查看与ROS功能包相关的信息(可以使用find, list, depend-on, depends和profile等选项)。 

rosinstall - 安装ROS附加功能包。 

rosdep [选项] - 安装该功能包的依赖性文件（check, install, init, update）。 

roslocate [选项] [功能包名称] - ROS功能包信息相关命令（可用的选项是info, vcs, type, uri和repo等）。 

## 常用的一些指令

| 指令                                  |        含义        |
| :------------------------------------ | :----------------: |
| rosrun map_server map_saver           | rviz保存生成的地图 |
| rosrun map_server map_server map.yaml | rviz加载生成的地图 |
| rostopic echo <topic> --noarr         |  详细显示话题信息  |

