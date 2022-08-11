# 安装Rtabmap 

## 1、安装rtabmap 

```
git clone https://github.com/introlab/rtabmap.git rtabmap 
sudo apt-get install ros-melodic-rtabmap ros-melodic-rtabmap-ros 
sudo apt-get remove ros-melodic-rtabmap ros-melodic-rtabmap-ros 

mkdir -p ~/rtabmap_ws/src 
cd ..      
cd ~/rtabmap_ws                     
catkin_make 

mkdir rtabmap/build 
cd rtabmap/build 
cmake -DCMAKE_INSTALL_PREFIX=~/rtabmap_ws/devel .. 
make -j4 
make install 
```

 

## 2、安装rtabmap_ros 

```
cd ~/rtabmap_ws 
git clone https://github.com/introlab/rtabmap_ros.git src/rtabmap_ros 
catkin_make 
```

