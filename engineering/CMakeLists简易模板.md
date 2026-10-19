# CMakeLists简易模板

```cmake
cmake_minimum_required(VERSION 2.8)
PROJECT(ndt_mapping)

#dboost
add_definitions(-DBOOST_ERROR_CODE_HEADER_ONLY)

find_package(OpenCV 4.5.2 QUIET)
if(NOT OpenCV_FOUND)
    find_package(OpenCV 2.4.3 QUIET)
    if(NOT OpenCV_FOUND)
        message(FATAL_ERROR "OpenCV > 2.4.3 not found.")
    endif()
endif()

find_package(Eigen3 3.1.0 REQUIRED NO_MODULE)
find_package(PCL REQUIRED)

include_directories(
        ${PROJECT_SOURCE_DIR}
        ${PROJECT_SOURCE_DIR}/src
        ${OpenCV_INCLUDE_DIRS}
        #eigen库只包含头文件
        #${EIGEN3_INCLUDE_DIR}
        ${PCL_INCLUDE_DIRS}
)

add_library(async_buffer SHARED
        src/async_buffer.cc
        src/async_buffer.h)

target_link_libraries(async_buffer
        ${OpenCV_LIBRARIES}
        ${PCL_LIBRARIES}
        #gflags可以直接链接
        gflags)

add_executable(ndt_mapping src/ndt_mapping.cc)
target_link_libraries(ndt_mapping async_buffer)

#设置了CMake变量OpenCV_DIR为OpenCV库的配置文件所在路径，正是通过载入这个路径下的OpenCVConfig.cmake配置文件才能配置好OpenCV库，
#然后在OpenCVConfig.cmake配置文件中定义了变量OpenCV_INCLUDE_DIRS为OpenCV库头文件包含路径，这样我们才能才在代码中
#使用#include <opencv2/opencv.hpp>而不会出现编译错误，同时定义了变量OpenCV_LIBS为OpenCV链接库路径，这样我们才能正确链接到
#OpenCV中的库文件，而不会出现类似未定义的引用这样的链接错误。
message(STATUS "OpenCV_DIR = ${OpenCV_DIR}")
message(STATUS "OpenCV_INCLUDE_DIRS = ${OpenCV_INCLUDE_DIRS}")
message(STATUS "OpenCV_LIBS = ${OpenCV_LIBS}")
message(STATUS "OpenCV_LIBRARIES = ${OpenCV_LIBRARIES}")

message(STATUS "PCL_DIR = ${PCL_DIR}")
message(STATUS "Gflags_DIR = ${Gflags_DIR}")
```