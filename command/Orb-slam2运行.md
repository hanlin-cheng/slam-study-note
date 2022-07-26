# Orb-slam2运行

## **part1：运行数据集**

### **1.Monocular**

- #### Tum:


```
./Examples/Monocular/mono_tum Vocabulary/ORBvoc.txt Examples/Monocular/TUMX.yaml PATH_TO_SEQUENCE_FOLDER
```



- #### KITTI:


```
./Examples/Monocular/mono_kitti Vocabulary/ORBvoc.txt Examples/Monocular/KITTIX.yaml PATH_TO_DATASET_FOLDER/dataset/sequences/SEQUENCE_NUMBER
```



- #### EURoc:


```
./Examples/Monocular/mono_euroc Vocabulary/ORBvoc.txt Examples/Monocular/EuRoC.yaml PATH_TO_SEQUENCE_FOLDER/mav0/cam0/data Examples/Monocular/EuRoC_TimeStamps/SEQUENCE.txt
```



### **2.Stereo**

- #### KITTI:


```
./Examples/Stereo/stereo_kitti Vocabulary/ORBvoc.txt Examples/Stereo/KITTIX.yaml PATH_TO_DATASET_FOLDER/dataset/sequences/SEQUENCE_NUMBER
```



- #### EuRoc:


```
./Examples/Stereo/stereo_euroc Vocabulary/ORBvoc.txt Examples/Stereo/EuRoC.yaml PATH_TO_SEQUENCE/mav0/cam0/data PATH_TO_SEQUENCE/mav0/cam1/data Examples/Stereo/EuRoC_TimeStamps/SEQUENCE.txt
```



### **3.RGBD**

- #### TUM:


```
./Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/TUMX.yaml PATH_TO_SEQUENCE_FOLDER ASSOCIATIONS_FILE
```



## **part2：zed实测**

```
roslaunch zed_wrapper zed.launch

rosrun ORB_SLAM2 Stereo Vocabulary/ORBvoc.txt Examples/Stereo/EuRoC.yaml false /camera/left/image_raw:=/zed2/zed_node/left/image_rect_color /camera/right/image_raw:=/zed2/zed_node/right/image_rect_color
```

#### **or**

例如把/ORB_SLAM2/Examples/ROS/ORB_SLAM2/src目录下的ros_stereo.cc建立一个新的文件，比如说叫ros_zed_stereo_rect.cc

把代码复制过去，只需要更改

```
message_filters::Subscriber\<sensor_msgs::Image\> left_sub(nh, "/zed2/zed_node/left/image_rect_color", 1);

message_filters::Subscriber\<sensor_msgs::Image\> right_sub(nh, "/zed2/zed_node/right/image_rect_color",1);
```

然后修改CmakeLists.txt，加入：

#Node for ZED camera

```
rosbuild_add_executable(zed_Stereo_rect src/ros_zed_stereo_rect.cc)

target_link_libraries(zed_Stereo_rect \${LIBS})
```

重新编译

```
./build_ros.sh
```

编译成功后运行

```
roslaunch zed_wrapper zed.launch

rosrun ORB_SLAM2 zed_Stereo_rect Vocabulary/ORBvoc.txt Examples/Stereo/EuRoC.yaml
```

#yaml文件可以替换成标定之后的zed参数文件