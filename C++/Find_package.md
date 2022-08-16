# Find_package 

## **1、find_packakge命令基本介绍** 

> ​		在我们实际开发过程中，经常不可避免会使用到第三方开源库，这些开源库可能是通过apt-get install命令自动安装到系统目录中，也可能是由我们自己下载库的源码然后通过编译安装到指令目录下的。
>
> ​		不管哪种方式安装的库文件，如果我们需要自己的项目中使用这些库，首先面临的第一个问题就是如何找到这些库。所谓“找到”这些库，其实是根据我们的需要找到指定版本的库头文件包含路径、链接库路径等，从而能够满足我们开发项目的编译链接需要。
>
> ​		在没有CMake的时代，这种库查找链接的工作都需要借助MakeFile中的各种命令来完成，非常的繁琐，而且不方便移植，到了CMake时代，CMake给我们提供了find_package()命令用来查找依赖包，理想情况下，一句find_package()命令就能把一整个依赖包的头文件包含路径、库路径、库名字、版本号等情况都获取到，后续只管用就好了。但实际使用过程可能会出现这样那样的问题，因此需要我们对find_package这个强大的命令有个大概的理解。 

## **2、一个使用find_package命令的例子** 

为了能够帮助大家理解find_package命令的用法，此处首先用OpenCV库举例子，示范如何通过find_pakcage命令找到OpenCV库并配置，从而能够在我们自己的项目中调用OpenCV库，实现特定的功能。 

**opencv_test.cpp:** 

```
#include <cstdio> 
#include <iostream> 
#include <opencv2/opencv.hpp> 

using namespace cv; 

int main() { 
  Mat image; 
  image = imread("../opencv_test.jpg"); 
 
  if (!image.data) { 
    printf("No image data\n"); 
    return -1; 
  } 

  namedWindow("Display Image", CV_WINDOW_AUTOSIZE); 
  imshow("Display Image", image); 
  waitKey(0); 
  return 0; 
} 
```

**CMakeLists.txt:** 

```
cmake_minimum_required(VERSION 2.8) 
project(find_package_learning) 
find_package(OpenCV 3 REQUIRED) 

 
message(STATUS "OpenCV_DIR = ${OpenCV_DIR}") 
message(STATUS "OpenCV_INCLUDE_DIRS = ${OpenCV_INCLUDE_DIRS}") 
message(STATUS "OpenCV_LIBS = ${OpenCV_LIBS}") 

 
include_directories(${OPENCV_INCLUDE_DIRS})   
add_executable(opencv_test opencv_test.cpp)   
target_link_libraries(opencv_test ${OpenCV_LIBS}) 
```

### **2.1**编译输出与分析

```
-- The C compiler identification is GNU 7.5.0 
-- The CXX compiler identification is GNU 7.5.0 
-- Check for working C compiler: /usr/bin/cc 
-- Check for working C compiler: /usr/bin/cc -- works 
-- Detecting C compiler ABI info 
-- Detecting C compiler ABI info - done 
-- Detecting C compile features 
-- Detecting C compile features - done 
-- Check for working CXX compiler: /usr/bin/c++ 
-- Check for working CXX compiler: /usr/bin/c++ -- works 
-- Detecting CXX compiler ABI info 
-- Detecting CXX compiler ABI info - done 
-- Detecting CXX compile features 
-- Detecting CXX compile features - done 
-- Found OpenCV: /usr/local (found suitable version "3.4.4", minimum required is "3")  
-- OpenCV_DIR = /usr/local/share/OpenCV 
-- OpenCV_INCLUDE_DIRS = /usr/local/include;/usr/local/include/opencv 
-- OpenCV_LIBS = opencv_calib3d;opencv_core;opencv_dnn;opencv_features2d;opencv_flann;opencv_highgui;opencv_imgcodecs;opencv_imgproc;opencv_ml;opencv_objdetect;opencv_photo;opencv_shape;opencv_stitching;opencv_superres;opencv_video;opencv_videoio;opencv_videostab;opencv_viz;opencv_aruco;opencv_bgsegm;opencv_bioinspired;opencv_ccalib;opencv_cvv;opencv_datasets;opencv_dnn_objdetect;opencv_dpm;opencv_face;opencv_freetype;opencv_fuzzy;opencv_hdf;opencv_hfs;opencv_img_hash;opencv_line_descriptor;opencv_optflow;opencv_phase_unwrapping;opencv_plot;opencv_reg;opencv_rgbd;opencv_saliency;opencv_stereo;opencv_structured_light;opencv_surface_matching;opencv_text;opencv_tracking;opencv_xfeatures2d;opencv_ximgproc;opencv_xobjdetect;opencv_xphoto 
-- Configuring done 
-- Generating done 
-- Build files have been written to: /home/zhanghm/Programming/programming-learning-examples/cmake_learning/learn_cmake_easily/find_package_learning/build 
```

重点看下其中OpenCV_DIR、OpenCV_INCLUDE_DIRS和OpenCV_LIBS打印的结果，这是我在CMakeLists.txt中用message命令输出这三个变量的值的结果。 

可以看到在执行find_package(OpenCV 3 REQUIRED)命令后，CMake找到了我们安装的位于/usr/local下的OpenCV库，并设置了CMake变量OpenCV_DIR为OpenCV库的配置文件所在路径，正是通过载入这个路径下的OpenCVConfig.cmake配置文件才能配置好OpenCV库，然后在OpenCVConfig.cmake配置文件中定义了变量OpenCV_INCLUDE_DIRS为OpenCV库头文件包含路径，这样我们才能才在代码中使用#include <opencv2/opencv.hpp>而不会出现编译错误，同时定义了变量OpenCV_LIBS为OpenCV链接库路径，这样我们才能正确链接到OpenCV中的库文件，而不会出现类似未定义的引用这样的链接错误。 

![](/home/hanlin/git-repository/slam-study-note/C++/media/GetImage%20(1).png)