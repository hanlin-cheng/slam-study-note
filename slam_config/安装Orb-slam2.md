# 安装Orb-slam2 

## **Part1:安装orb-slam2的依赖库** 

```
sudo apt-get install libeigen3-dev 
```

 

## **Part2：需要安装zed与opencv** 

 

## **Part3：安装orb-slam2** 

\#创建工作空间，将代码克隆到src下 

```
mkdir -p orbslam_ws/src 
cd orbslam_ws/src 
catkin_init_workspace 
git clone https://github.com/raulmur/ORB_SLAM2.git ORB_SLAM2 
cd ORB_SLAM2 
chmod +x build.sh 
 ./build.sh 
cd ../ 
catkin_make 
```

 

\#添加运行环境#PATH为你得安装目录 

```
export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:PATH/ORB_SLAM2/Examples/ROS//添加运行环境 
```

 

\#将setup.bash添加到~/.bashrc中 

```
source ~/.bashrc 
```

 

\#编译ROS驱动 

在ORBSLAM2/Examples/ROS/ORBSLAM2下的Cmakelists.txt中添加一行，-lboost_system

![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage2.png)



#编译前还得手动export一下运行环境 

```
export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:PATH/ORB_SLAM2/Examples/ROS//添加运行环境 
./build_ros.sh 
```

\#编译完成 

 