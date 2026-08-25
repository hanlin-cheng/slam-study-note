# cuVSLAM：CUDA 加速的视觉里程计与建图

代码仓库：[nvidia-isaac/cuVSLAM](https://github.com/nvidia-isaac/cuVSLAM)

## 简介

NVIDIA cuVSLAM 的核心是基于 C++/CUDA 的实时视觉里程计与 SLAM，支持多相机、单目、RGB-D、双目惯性 VIO，并提供 Python 绑定、示例、数据转换和可视化工具。

> 本文基于静态代码阅读与结构分析整理，尚未执行完整构建和测试；具体选项与产物应以所阅读的仓库版本为准。

## 仓库结构

```text
cuVSLAM/
├── CMakeLists.txt              # 顶层构建入口，控制 CUDA、LMDB、Rerun、Ceres 等选项
├── README.md                   # 项目说明、构建、Python 安装、示例入口
├── cmake/                      # 第三方依赖和构建辅助：Eigen、gflags、LMDB、yaml-cpp、rerun 等
├── libs/                       # 核心 C++/CUDA 库，算法主体
├── python/                     # PyCuVSLAM 绑定与 Python Tracker 封装
├── examples/                   # KITTI、EuRoC、TUM、RealSense、Orbbec、ZED、OAK-D 示例
├── tools/                      # EDEX/ROS/评估/地图/undistort/可视化/命令行跟踪工具
├── doc/                        # 文档与图片
├── docker/                     # Docker 构建环境
└── test_data/                  # 小型测试数据、EDEX、图像样例
```

```text
libs/
├── cuvslam/        # 对外 C++ API：Odometry、Slam、Rig、Camera、Pose 等
├── odometry/       # 视觉里程计模式：mono/multi/RGB-D/stereo-inertial
├── pipelines/      # 在线 SfM/跟踪流水线：triangulator、SBA service、state machine
├── sof/            # Sparse Optical Flow：特征选择、LK/KLT 跟踪、关键帧选择
├── cuda_modules/   # CUDA 加速模块：GFTT、LK、ST tracker、SBA、ICP、pyramid、selection
├── epipolar/       # 对极几何：Fundamental、Homography、三角化、相机选择
├── pnp/            # 单目/多相机 PnP、Visual ICP、位姿细化
├── sba/            # Sparse Bundle Adjustment，局部 BA
├── imu/            # IMU 预积分、惯性 PnP、惯性 SBA、重力/偏置估计
├── slam/           # 地图、回环、位姿图、定位、异步 SLAM
├── map/            # odometry 侧 keyframe/landmark/service 数据结构
├── camera/         # 相机模型、rig、观测、视锥相交图
├── math/           # RANSAC、鲁棒核、SE(3) twist、PGO、点云配准
├── common/         # 基础类型、图像、时间、日志、坐标系、队列等
├── edex/           # EDEX 数据格式读写
├── launcher/       # C++ launchers，把 API、数据、可视化串起来
├── utils/          # 图像加载、配置解析、图像变换
├── refinement/     # 可选 Ceres refinement / BA，受 USE_CERES 控制
├── visualizer/     # Rerun 可视化，受 USE_RERUN 控制
└── profiler/log/testing
```

关键入口文件：

- C++ API：`libs/cuvslam/cuvslam2.h`
- Python 封装：`python/tracker.py`
- 顶层库注册：`libs/CMakeLists.txt`
- SOF 特征跟踪：`libs/sof/sof.h`
- 在线流水线：`libs/pipelines/CMakeLists.txt`
- SLAM 模块：`libs/slam/CMakeLists.txt`

## 运行链路

```text
用户图像/IMU
  -> python.Tracker.track()
  -> C++ Odometry.track()
  -> SOF 特征选择与跟踪
  -> Epipolar / PnP / ICP / Triangulation
  -> 局部 SBA / IMU SBA 优化
  -> 输出 odometry pose
  -> 可选 Slam.track()
  -> 地图关键帧、回环检测、位姿图优化
  -> 输出 slam pose
```

`python/tracker.py` 是薄封装：它创建 `Odometry`，如果传入 `SlamConfig`，还会创建 `Slam`，并把 odometry 导出的 observations/landmarks 交给 SLAM 做建图和回环。

## 主要算法

### 稀疏光流 / 特征跟踪

- 模块：`libs/sof/`、`libs/cuda_modules/`
- 算法：Good Features To Track / GFTT、Lucas-Kanade、KLT、水平双目 LK/KLT、ST tracker、图像金字塔、梯度金字塔、box/gaussian 滤波。
- 作用：在连续帧和多相机之间维护 2D feature tracks。

### 几何估计与外点剔除

- 模块：`libs/epipolar/`、`libs/math/ransac.h`
- 算法：RANSAC、Fundamental Matrix、Essential Matrix、Homography、Cheirality check、点归一化、相机位姿候选选择。
- 作用：过滤错误匹配，估计相机间/帧间几何关系。

### 三角化 / 3D 点重建

- 模块：`libs/epipolar/point_reconstruction.h`、`libs/pipelines/triangulator.cpp`
- 算法：多视图三角化、视锥/深度合法性判断。
- 作用：把 2D tracks 转为 3D landmarks，供 PnP、BA、SLAM 使用。

### PnP 位姿估计与位姿细化

- 模块：`libs/pnp/`
- 算法：单目 PnP、多相机 PnP、PnP RANSAC、SE(3)/SO(3) Gauss-Newton、Levenberg-Marquardt、Huber 鲁棒核。
- 代码里 `mono_pnp.cpp` 明确实现了重投影误差、Jacobian、Hessian 和 robust loss。
- 作用：通过 2D-3D 对应估计当前相机/rig 位姿。

### Visual ICP / RGB-D 跟踪

- 模块：`libs/pnp/visual_icp.*`、`libs/cuda_modules/icp_tools.*`
- 算法：视觉重投影误差 + 深度/ICP 约束，多层金字塔迭代优化。
- 作用：RGB-D 模式下利用深度增强位姿估计。

### SBA / Bundle Adjustment

- 模块：`libs/sba/`、`libs/cuda_modules/sba.*`
- 算法：Sparse Bundle Adjustment、Schur Complement、Levenberg-Marquardt、Huber robust weighting、协方差/信息矩阵估计。
- 作用：联合优化局部相机位姿与 3D 点，降低漂移。

### IMU 预积分与视觉惯性优化

- 模块：`libs/imu/`
- 算法：IMU preintegration、gyro/acc bias 更新、重力估计、Soft Inertial PnP、Inertial SBA、Schur Complement。
- 作用：双目惯性模式下融合 IMU，提高快速运动或视觉短暂失败时的稳定性。

### SLAM 建图、回环与位姿图优化

- 模块：`libs/slam/`
- 算法：关键帧地图、feature descriptor 匹配、空间索引 LSI grid、Loop Closure Solver、PnP RANSAC 回环验证、Pose Graph Optimization。
- 作用：把 odometry 的局部轨迹变成可回环修正的全局 SLAM 轨迹，并支持保存/加载地图。

### 位姿预测 / 运动模型

- 模块：`libs/odometry/pose_prediction.*`
- 算法：基于历史运动的 pose prediction。
- 作用：为下一帧跟踪提供初值，提高收敛和鲁棒性。

### 相机模型与畸变模型

- 模块：`libs/camera/`、`libs/cuvslam/cuvslam2.h`
- 支持模型：Pinhole、Fisheye/equidistant、Brown、Polynomial/OpenCV 8 参数模型。
- 作用：投影、反投影、归一化坐标、观测信息矩阵转换。



## 构建系统与产物

这个仓库的 CMake 逻辑可以分成 5 层：

```
顶层 CMakeLists.txt
  -> cmake/FindExt.cmake       拉第三方依赖
  -> libs/CMakeLists.txt       编译 cuVSLAM 内部库
  -> tools/CMakeLists.txt      编译工具程序
  -> examples/CMakeLists.txt   编译示例程序
```

`python/CMakeLists.txt` 不参与主工程 `make -j4`，它只在你执行：

```
CUVSLAM_BUILD_DIR="$PWD/build" python -m pip install ./python
```

时单独生效。

### 顶层 `CMakeLists.txt`

顶层文件主要做这些事：

- 设置 C++17 / CUDA17
- 设置输出目录：

```
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)
```

所以可执行文件和 `libcuvslam.so` 都在：

```
build/bin/
```

- 定义编译选项：

```
USE_CUDA=ON
USE_LMDB=ON
USE_RERUN=OFF
CUVSLAM_BUILD_SHARED_LIB=TRUE
ENFORCE_GPU=ON
```

- 加载公共编译规则：

```
include(cmake/cuVSLAMUtils.cmake)
setup_cuvslam_settings()
```

- 加载第三方依赖：

```
include(cmake/FindExt.cmake)
```

- 编译三个大目录：

```
add_subdirectory(libs)
add_subdirectory(tools)
add_subdirectory(examples)
```

当前顶层配置未包含 `add_subdirectory(doc)`。

### `cmake/cuVSLAMUtils.cmake`

这里定义了 3 个核心 helper：

```
setup_staticlib_project(...)
```

用于编译静态库，例如：

```
build/libs/sof/libsof.a
build/libs/slam/libslam.a
build/libs/imu/libimu.a
setup_app(...)
```

用于编译可执行程序，例如：

```
build/bin/track_euroc
build/bin/map_extractor
build/bin/tracker
setup_test_project(...)
```

用于编译测试程序，例如：

```
build/bin/slam_test
build/bin/map_test
build/bin/imu_test
```

它还定义了公共 target：

```
cuvslam_settings
```

所有库和程序都会链接它，从而继承：

- `libs/` include 路径
- `USE_CUDA` / `USE_LMDB` / `USE_RERUN` 等宏
- 编译 warning
- pthread
- Release 下 strip
- CUDA 相关设置

### 第三方依赖

第三方依赖在：

```
cmake/FindExt.cmake
cmake/ext/*.cmake
```

里面声明。

第三方源码不直接存放在仓库中，而是在 CMake 配置或编译时自动下载到：

```
build/_deps/
```

完成依赖配置后，可在该目录中看到类似内容：

```
build/_deps/eigen-src/
build/_deps/gflags-src/
build/_deps/googletest-src/
build/_deps/jsoncpp-src/
build/_deps/spdlog-src/
build/_deps/yaml-cpp-src/
build/_deps/zlib-src/
build/_deps/libpng-src/
build/_deps/lmdb-src/
build/_deps/rerun_sdk-src/
```

对应的编译目录是：

```
build/_deps/*-build/
```

产物例如：

```
build/_deps/gflags-build/libgflags.a
build/_deps/spdlog-build/libspdlog.a
build/_deps/yaml-cpp-build/libyaml-cpp.a
build/_deps/zlib-build/libz.a
build/_deps/libpng-build/libpng16.a
build/lib/libgtest.a
build/liblmdb_build.a
```

依赖来源大致是：

| 依赖          | 来源               | 方式                                         |
| ------------- | ------------------ | -------------------------------------------- |
| Eigen         | GitLab tar.gz      | `FetchContent`                               |
| googletest    | GitHub release     | `FetchContent`                               |
| gflags        | GitHub tar.gz      | `FetchContent`                               |
| jsoncpp       | GitHub tar.gz      | `FetchContent`                               |
| spdlog        | GitHub tar.gz      | `FetchContent`                               |
| zlib          | GitHub tar.gz      | `FetchContent`                               |
| libpng        | SourceForge tar.xz | `FetchContent`                               |
| cnpy          | GitHub repo        | `FetchContent_Populate` 后手动 `add_library` |
| lmdb          | GitHub tar.gz      | 手动编译 `mdb.c/midl.c`                      |
| rerun         | rerun C++ SDK zip  | `FetchContent`                               |
| libjpeg-turbo | GitHub release     | `ExternalProject_Add`                        |

`rerun` 比较特殊，它下载的是 `rerun_cpp_sdk.zip`，并且内部还会自动下载/编译 Arrow，所以你会看到：

```
build/_deps/rerun_sdk-src/
build/arrow/
```

### `libs/CMakeLists.txt`

`libs/` 是核心库层。大部分都是静态库 `.a`，最终被 `libs/cuvslam` 链进 `libcuvslam.so`。

主要产物：

| 目录                             | target                          | 产物                                      | 作用                                             |
| -------------------------------- | ------------------------------- | ----------------------------------------- | ------------------------------------------------ |
| `libs/common`                    | `common`                        | `build/libs/common/libcommon.a`           | 基础类型、时间、图像、日志类型、工具类           |
| `libs/camera`                    | `camera`                        | `build/libs/camera/libcamera.a`           | 相机模型、投影、观测                             |
| `libs/camera_rig_edex`           | `camera_rig_edex`               | `libcamera_rig_edex.a`                    | 多相机 rig / EDEX 相机配置                       |
| `libs/math`                      | `cuvslam_math`                  | `libcuvslam_math.a`                       | 位姿、PGO、RANSAC、优化数学                      |
| `libs/epipolar`                  | `epipolar`                      | `libepipolar.a`                           | 对极几何、三角化、Homography、Fundamental matrix |
| `libs/pnp`                       | `pnp`                           | `libpnp.a`                                | PnP / 多相机 PnP / visual ICP                    |
| `libs/sof`                       | `sof`                           | `libsof.a`                                | 特征检测、KLT/LK 跟踪、金字塔、特征选择          |
| `libs/cuda_modules`              | `cuda_modules`                  | `libcuda_modules.a`                       | CUDA 加速封装                                    |
| `libs/cuda_modules/cuda_kernels` | `cuda_kernels`                  | 内部静态库                                | `.cu` kernel，链接 CUDA/cuBLAS/cuSolver          |
| `libs/imu`                       | `imu`                           | `libimu.a`                                | IMU 预积分、惯性优化、gyro bias                  |
| `libs/sba`                       | `sba`                           | `libsba.a`                                | Bundle Adjustment                                |
| `libs/pipelines`                 | `pipelines` / `sfm_online_lite` | `libpipelines.a` / `libsfm_online_lite.a` | 在线 tracking pipeline                           |
| `libs/odometry`                  | `odometry`                      | `libodometry.a`                           | mono/stereo/RGBD/VIO odometry                    |
| `libs/slam`                      | `slam`                          | `libslam.a`                               | 建图、回环、pose graph、localizer、LMDB map      |
| `libs/map`                       | `map`                           | `libmap.a`                                | keyframe / landmark map                          |
| `libs/launcher`                  | `launcher`                      | `liblauncher.a`                           | 统一启动不同模式的 launcher                      |
| `libs/log`                       | `log`                           | `liblog.a`                                | 日志系统                                         |
| `libs/profiler`                  | `profiler`                      | `libprofiler.a` 或 interface              | 性能统计                                         |
| `libs/testing`                   | `testing`                       | `libtesting.a`                            | 测试公共代码                                     |
| `libs/utils`                     | `utils`                         | `libutils.a`                              | 图像加载、YAML 配置                              |
| `libs/visualizer`                | `visualizer`                    | `libvisualizer.a`                         | 仅 `USE_RERUN=ON` 时编译                         |

最终核心动态库是：

```
build/bin/libcuvslam.so
```

它由这里生成：

```
libs/cuvslam/CMakeLists.txt
```

里面的逻辑是：

```
add_library(cuvslam SHARED ...)
target_link_libraries(cuvslam
    slam
    camera
    sof
    sba
    common
    pnp
    epipolar
    cuvslam_math
    log
    profiler
    odometry
    pipelines
    map
    imu
    cnpy::cnpy
    cuda_modules
)
```

如果 `USE_RERUN=ON`，还会链接：

```
visualizer
```

### `tools/CMakeLists.txt`

工具程序都输出到：

```
build/bin/
```

主要有：

| 目录                         | 可执行程序             | 作用                                                      |
| ---------------------------- | ---------------------- | --------------------------------------------------------- |
| `tools/check_externals`      | `check_externals`      | 检查外部依赖/LMDB                                         |
| `tools/cuvslam_api_launcher` | `cuvslam_api_launcher` | API launcher 示例                                         |
| `tools/cuvslam_api_test`     | `cuvslam_api_test`     | API 测试程序                                              |
| `tools/gravity_calc`         | `gravity_calc`         | 重力方向估计                                              |
| `tools/map_extractor`        | `map_extractor`        | 从保存地图中提取/可视化信息                               |
| `tools/tracker`              | `tracker`              | 通用 tracker 工具                                         |
| `tools/euroc_test`           | `euroc_test`           | EuRoC stereo/IMU 测试                                     |
| `tools/selection_visualizer` | `selectionVisualizer`  | OpenCV 存在时编译                                         |
| `tools/sof_visualizer`       | `sof_visualizer`       | OpenCV 存在时编译                                         |
| `tools/undistort`            | `undistort`            | OpenCV 存在时编译                                         |
| `tools/edex_refinement`      | `edex_refinement`      | 只有 `USE_CERES=ON` 才可能编译，但当前 Ceres 被标记不支持 |

### `examples/CMakeLists.txt`

当前 C++ 示例只编译：

```
examples/euroc/cpp
```

产物：

```
build/bin/track_euroc
```

Python 示例，比如：

```
examples/orbbec/run_rgbd.py
examples/orbbec/run_stereo.py
examples/kitti/track_kitti_slam.py
```

不是 CMake 编译出来的，它们是运行时脚本，依赖你另外安装 Python binding。

### `python/CMakeLists.txt`

这个文件只在 `pip install ./python` 时使用。

它不会重新编译完整 cuVSLAM，而是要求你已经有：

```
build/bin/libcuvslam.so
```

然后编译 Python 扩展：

```
pycuvslam.so
```

安装到当前 Python 环境的：

```
site-packages/cuvslam/
```

里面会包含：

```
pycuvslam.so
libcuvslam.so
__init__.py
tracker.py
utils.py
pycuvslam.pyi
py.typed
```

可以用这个查看安装位置：

```
python -c "import cuvslam, os; print(os.path.dirname(cuvslam.__file__))"
```

### 构建流程总结

`cmake --build build --parallel` 的主线是：

```
下载第三方源码到 build/_deps
  -> 编译第三方库
  -> 编译 libs/* 静态库
  -> 链接 build/bin/libcuvslam.so
  -> 编译 tools/examples/test 可执行程序到 build/bin
```

`pip install ./python` 的主线是：

```
读取 build/bin/libcuvslam.so
  -> 编译 pycuvslam.so
  -> 把 pycuvslam.so + libcuvslam.so 安装进 Python site-packages/cuvslam
```

第三方源码由 CMake 下载到构建目录：

```
build/_deps/*-src
```

## C++ 主库与 Python 封装

先构建 C++/CUDA 主库和工具：

```bash
cmake -S . -B build
cmake --build build --parallel
```

主构建会生成 `build/bin/libcuvslam.so` 以及启用的工具和示例。随后安装 Python 绑定：

```bash
CUVSLAM_BUILD_DIR="$PWD/build" python -m pip install ./python
```

Python 包构建会读取已有的 `build/bin/libcuvslam.so`，编译 `pycuvslam.so`，并把两者安装到当前 Python 环境的 `site-packages/cuvslam/`。如果主库尚未生成，Python 包安装会失败。

可用以下命令确认安装位置：

```bash
python -c "import cuvslam, os; print(os.path.dirname(cuvslam.__file__))"
```

运行 Python 示例前，通常需要先完成上述两步：

```bash
python examples/orbbec/run_rgbd.py
```
