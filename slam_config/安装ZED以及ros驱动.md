# 安装**ZED**以及**ros**驱动

## Part1：安装zed sdk

前往官网下载与Jetpack以及CUDA版本匹配的zed sdk 

```
chmod +x ZED_SDK_Ubuntu16_cuda10.2_v3.1.2.run 
./ZED_SDK_Ubuntu16_cuda10.2_v3.1.2.run 
```

\#如果出现错误API下载失败，可手动前去网站下载，然后 

```
Pip3 -m install *** 
```



## Part2:安装zed2 ros工具

```
cd ~/ZED_WS/src 
git clone https://github.com/stereolabs/zed-ros-wrapper.git 
cd ../ 
rosdep install --from-paths src --ignore-src -r -y 
catkin_make -DCMAKE_BUILD_TYPE=Release 
source ./devel/setup.bash 
 

cd ~/catkin_ws/src 
git clone https://github.com/stereolabs/zed-ros-examples.git 
cd ../ 
rosdep install --from-paths src --ignore-src -r -y 
catkin_make -DCMAKE_BUILD_TYPE=Release 
source ./devel/setup.bash 
```

 

## Part3：安装完毕运行查看效果

```
roslaunch zed_display_rviz display_zed.launch 
```

 

 

 

 

 

 

 

 

 