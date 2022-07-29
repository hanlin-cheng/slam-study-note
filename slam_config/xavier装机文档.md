# xavier装机文档 

## 1、刷机 

刷 Jetpack 最新版本: 

https://www.ncnynl.com/category/Xavier-basic/ 

## 2、挂载固态硬盘 

https://www.ncnynl.com/archives/201912/3486.html 

## 3、更新源列表及软件更新( 必须要先更新 ) 

## 4、进入 xavier 基础依赖 

https://github.com/yqlbu/jetson-packages-family安装 

Dependencies Installation注意不要安装Set CUDA Path 

## 5、卸载 opencv 

刷机后系统自带 opencv,但是不支持 GPU,建议移除(一定要先安装Opencv,在安装ros,不然后面会出现想不到的错误) 

进入https://github.com/yqlbu/jetson-packages-family 找到Table of Content 

安装命令 

```
sudo apt-get update 
sudo apt-get upgrade 
sudo apt-get install -y nano curl 
sudo apt-get install -y python3-pip python3-dev 
sudo apt-get install -y python-pip 
sudo apt-get install -y python-setuptools 
sudo apt-get install -y python3-setuptools 
sudo apt-get install -y libcanberra-gtk0 libcanberra-gtk-module 
pip3 install -U pip 
pip install -U pip 
pip3 install setuptools wheel 
pip install setuptools wheel cython 
!!! You may modify the script to install custom version of OpenCV 
```

修改 install_opencv4.1.1_jetson.sh 改成4.4.0 版本的opencv 成功后的日志 

## 6、安装ros 

ROS入门教程-安装并配置ROS环境（melodic版本） 

https://www.ncnynl.com/archives/201906/3147.html 

```
cd ~ 
sudo apt-get purge libopencv* 
bash <(wget -qO- https://github.com/yqlbu/jetson-packages-family/raw/master/OpenCV/install_opencv4.1.1_jetson.sh) 
```

> 需要下载 1,680 kB 的归档。 
>
> 解压缩后会消耗 3,678 kB 的额外空间。 
>
> 获取:1 http://ports.ubuntu.com/ubuntu-ports bionic-updates/universe arm64 
>
> libopencv-core3.2 arm64 3.2.0+dfsg-4ubuntu0.1 [631 kB] 
>
> 获取:2 http://ports.ubuntu.com/ubuntu-ports bionic-updates/universe arm64 
>
> libopencv-core-dev arm64 3.2.0+dfsg-4ubuntu0.1 [1,049 kB] 
>
> 已下载 1,680 kB，耗时 7秒 (256 kB/s)                                           
>
> debconf: 因为并未安装 apt-utils，所以软件包的设定过程将被推迟 
>
> (正在读取数据库 ... 系统当前共安装有 172732 个文件和目录。) 
>
> 正在卸载 libopencv-dev (4.1.1-2-gd5a58aa75) ... 
>
> 正在选中未选择的软件包 libopencv-core3.2:arm64。 
>
> (正在读取数据库 ... 系统当前共安装有 172397 个文件和目录。) 
>
> 正准备解包 .../libopencv-core3.2_3.2.0+dfsg-4ubuntu0.1_arm64.deb ... 
>
> 正在解包 libopencv-core3.2:arm64 (3.2.0+dfsg-4ubuntu0.1) ... 
>
> 正在选中未选择的软件包 libopencv-core-dev:arm64。 
>
> 正准备解包 .../libopencv-core-dev_3.2.0+dfsg-4ubuntu0.1_arm64.deb ... 
>
> 正在解包 libopencv-core-dev:arm64 (3.2.0+dfsg-4ubuntu0.1) ... 
>
> 正在设置 libopencv-core3.2:arm64 (3.2.0+dfsg-4ubuntu0.1) ... 
>
> 正在设置 libopencv-core-dev:arm64 (3.2.0+dfsg-4ubuntu0.1) ... 
>
> 正在处理用于 libc-bin (2.27-3ubuntu1.4) 的触发器 ... 
>
> [BASH]   Installation completed ... 
>
> sudo sh -c '. /etc/lsb-release && echo "deb 
>
> http://mirrors.ustc.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > 
>
> /etc/apt/sources.list.d/ros-latest.list' 
>
> sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key 
>
> C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654 

```
sudo apt-get update 
sudo apt-get install ros-melodic-desktop-full 
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator 
python-wstool build-essential 
sudo rosdep init 
rosdep update 
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc 
```

中途可能报错： 

打开hosts文件 sudo gedit /etc/hosts #在文件末尾添加 

151.101.84.133  raw.githubusercontent.com 

保存后退出再尝试 

## 7、ros中使用realsense 

### 	a) 安装 realsense sdk 

​	https://github.com/IntelRealSense/realsense-ros(官方给出的方式,多次编译失败) 

​	https://github.com/jetsonhacks/installRealSenseSDK(亲自实验,成功!) 

​	安装 installRealSenseSDK 

### 	b) 安装 realsesense_ros 

​	若报错: Project ‘cv_bridges’ specifics '/user/include/opencv' as an include dir, which is not found. 

​	则:将报错路径中的cv_bridgeconfig.cmake文件中 include/opencv 改成 include/opencv4 

```
vi /opt/ros/melodic/share/cv_bridge/cmake/cv_bridgeConfig.cmake(94/96行修改opencv4) 
source ~/.bashrc 
roscore 
git clone https://github.com/jetsonhacks/installRealSenseSDK.git 
cd installRealSenseSDK 
./installLibrealsense.sh 
cd ~ 
mkdir -p ~/catkin_ws/src 
cd ~/catkin_ws/src 
git clone https://github.com/IntelRealSense/realsense-ros.git 
cd realsense-ros/ 
git checkout `git tag | sort -V | grep -P "^2.\d+\.\d+" | tail -1` 
cd .. 
git clone https://github.com/pal-robotics/ddynamic_reconfigure.git 
catkin_init_workspace 
cd .. 
```

​	出现以下日志表示成功啦！！！ 

## 8、python3 项目测试 

将 CV_Bridge 下的 CMakeLists 中的 Python37 改成 Python3 

## 9、其他 

```
git clone https://github.com/JetBrains/pty4j.git 
cd pty4j/native 
gcc -fPIC -c *.c 
gcc -shared -o libpty.so *.o 

\# 复制到 pycharm 下面, 注意下路径 
cp libpty.so /home/yc/pycharm-community-2019.3.4/lib/pty4j-native/linux/x86 
sudo apt-get install python-catkin-tools 
mkdir -p ~/project_ws/src 
cd ~/project_ws 
catkin init 
cd src 
git clone -b noetic https://github.com/ros-perception/vision_opencv.git 
catkin config -DPYTHON_EXECUTABLE=/usr/bin/python3-DPYTHON_INCLUDE_DIR=/usr/include/python3.6m -DPYTHON_LIBRARY=/usr/lib/aarch64-linux-gnu/libpython3.6m.so 
sudo pip install -U catkin_pkg 
sudo pip3 install -U catkin_pkg -i https://pypi.douban.com/simple 
catkin build 
source ~/project_ws/devel/setup.bash 
source ~/.bashrc 
pip install rospkg 
roslaunch realsense2_camera rs_camera.launch 
```

输入法修复 

ubuntu 16.04 输入法候选框不显示: 

解决方案: 

网上查询到的方法,亲测可用. 

如果运行了下面这条命令输入法就正常了的话： 

```
killall fcitx-qimpanel 
```

那么： 

```
sudo apt-get remove fcitx-ui-qimpanel 
```

问题解决. 亲测可用. 