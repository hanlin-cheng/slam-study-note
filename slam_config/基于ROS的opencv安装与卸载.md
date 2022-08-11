# 基于ROS的opencv安装与卸载 

## **Part1:下载安装包**  

前往官网下载所需版本的opencv。 

## **Part2：安装依赖包** 

```
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev 
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev 
```

## **Part3：编译opencv**  

```
cd opncv3.4.2  
# 终端路径指向自己的opencv文件包，本文档以3.4.2为例

 mkdir build && cd build 
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local .. 
# 路径指向自己需要安装的位置，一般在/usr/local or /usr/local/opencv#.# or /usr 

make -j8 
# 这里是八线程，可以根据自己的电脑选择，一般直接使用命令make 
# 达到100%后继续执行 

sudo make install 
```

 

## **part4:配置环境** 

### 1.修改opencv.conf文件 

```
sudo gedit /etc/ld.so.conf.d/opencv.conf  

# 写入 
/usr/local/lib 
```

 

### 2.更新系统共享链接库 

```
sudo ldconfig
```

 

### 3.修改bash.bashrc文件 

```
sudo gedit /etc/bash.bashrc 

# 文件末尾加入 
PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig  （注意路径) 

export PKG_CONFIG_PATH 
 
# 有些安装路径可能找不到pkgconfig，解决方法如下：
cd/usr/local/lib 
sudo mkdir pkgconfig && cdpkgconfig 
sudo gedit opencv.pc 

# 添加 
prefix=/usr/local 
exec_prefix=${prefix} 
includedir=/usr/local/include 
libdir=/usr/local/lib 
 
Name: OpenCV 
Description: Open Source Computer Vision Library 
Version: 3.4.2 
Libs: -L${exec_prefix}/lib -lopencv_stitching -lopencv_superres -lopencv_videostab -lopencv_aruco -lopencv_bgsegm -lopencv_bioinspired -lopencv_ccalib -lopencv_dnn_objdetect -lopencv_dpm -lopencv_face -lopencv_photo -lopencv_freetype -lopencv_fuzzy -lopencv_hdf -lopencv_hfs -lopencv_img_hash -lopencv_line_descriptor -lopencv_optflow -lopencv_reg -lopencv_rgbd -lopencv_saliency -lopencv_stereo -lopencv_structured_light -lopencv_phase_unwrapping -lopencv_surface_matching -lopencv_tracking -lopencv_datasets -lopencv_text -lopencv_dnn -lopencv_plot -lopencv_xfeatures2d -lopencv_shape -lopencv_video -lopencv_ml -lopencv_ximgproc -lopencv_calib3d -lopencv_features2d -lopencv_highgui -lopencv_videoio -lopencv_flann -lopencv_xobjdetect -lopencv_imgcodecs -lopencv_objdetect -lopencv_xphoto -lopencv_imgproc -lopencv_core 
Libs.private: -ldl -lm -lpthread -lrt 
Cflags: -I${includedir} 

注：注意版本与路径 
```

 

### 4.保存退出并source 

```
source /etc/bash.bashrc 
```

 

### 5.验证是否安装成功 

```
pkg-config --modversion opencv 
```

 

## **part5：将opencv配置到ROS中** 

 

```
sudo gedit /opt/ros/melodic/share/cv_bridge/cmake/cv_bridgeConfig.cmake 
```

\# 更改三个地方的路径地址 

![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage1.png)

至此opencv安装与配置完成！ 

 

## **part6**：opencv的完全卸载

终端指向opencv下build文件夹 

```
sudo make uninstall 
cd .. 
sudo rm -r build 
sudo rm -r /usr/local/include/opencv2 /usr/local/include/opencv /usr/include/opencv /usr/include/opencv2 /usr/local/share/opencv /usr/local/share/OpenCV /usr/share/opencv /usr/share/OpenCV /usr/local/bin/opencv* /usr/local/lib/libopencv* 

# 注意安装路径不同时卸载路径也不同 
sudo apt-get –purge remove opencv-doc opencv-data python-opencv 
```

卸载完成！ 