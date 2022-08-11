# 安装LIO-SAM 

## **part1**：安装依赖功能包 

```
sudo apt-get install -y ros-melodic-navigation 
sudo apt-get install -y ros-melodic-robot-localization 
sudo apt-get install -y ros-melodic-robot-state-publisher 
```

 

## **part2:**安装gtsam

```
git clone https://github.com/borglab/gtsam.git 
mkdir build && cd build 
cmake -DGTSAM_BUILD_WITH_MARCH_NATIVE=OFF .. 
sudo make install -j4 
```

 

## part3:安装lio-sam

```
mkdir ~/lio-sam_ws/src 
cd ~/lio-sam_ws/src 
git clone https://github.com/TixiaoShan/LIO-SAM.git 
cd ../ 
catkin_make 
```

 