# SLAM Study Notes

[在线博客](https://hanlin-cheng.github.io/)

本仓库包含以下内容：

1. [心得笔记](#心得笔记)：数学基础、SLAM 与机器人、ROS 与中间件、C++ 开发、开发工具与工程实践、Linux 与嵌入式、Qt 开发、应用与杂记、论文阅读
2. [学习资源](#resource)：在线学习资源与电子书
3. [软件](#software)：常用学习软件与安装说明
4. [其他](#other)：其他工具与实践记录
5. TODO


## 心得笔记

[数学基础](#数学基础) · [SLAM 与机器人](#slam-与机器人) · [ROS 与中间件](#ros-与中间件) · [C++ 开发](#c-开发) · [开发工具与工程实践](#开发工具与工程实践) · [Linux 与嵌入式](#linux-与嵌入式) · [Qt 开发](#qt-开发) · [应用与杂记](#应用与杂记) · [论文阅读](#论文阅读)

### 数学基础

- [SVD与非线性优化在最小二乘问题中的区别](<math/SVD与非线性优化在最小二乘问题中的区别.md>)
- [为什么使用齐次坐标](<math/为什么使用齐次坐标.md>)
- [二维空间中的点坐标旋转关系](<math/二维空间中的点坐标旋转关系.md>)
- [匈牙利算法](<math/匈牙利算法.md>)
- [协方差矩阵](<math/协方差矩阵.md>)
- [向量积(矢积)与数量积(标积)的区别](<math/向量积(矢积)与数量积(标积)的区别.md>)
- [多元函数的泰勒展开式](<math/多元函数的泰勒展开式.md>)
- [多维高斯分布](<math/多维高斯分布.md>)
- [归一化与标准化](<math/归一化与标准化.md>)
- [欧拉角与坐标系旋转变换](<math/欧拉角与坐标系旋转变换.md>)
- [正态分布](<math/正态分布.md>)
- [矩阵的奇异值分解](<math/矩阵的奇异值分解.md>)
- [超定方程组的最小二乘解](<math/超定方程组的最小二乘解.md>)
- [雅克比矩阵和海塞矩阵](<math/雅克比矩阵和海塞矩阵.md>)
- [高斯牛顿法](<math/高斯牛顿法.md>)

### SLAM 与机器人

- [2d激光雷达运动补偿方法(去畸变)](<robotics/2d激光雷达运动补偿方法(去畸变).md>)
- [IMU预积分](<robotics/IMU预积分.md>)
- [Matlab标定单目相机内参](<robotics/Matlab标定单目相机内参.md>)
- [1. ORB-SLAM3中为什么要进行三次IMU初始化](<robotics/ORBSLAM3学习.md>)
- [SLAM中的位姿变换与轨迹评价指标:APE、RPE、ATE、RTE](<robotics/SLAM中的位姿变换与轨迹评价指标.md>)
- [Theta*: Any-Angle Path Planning on Grids](<robotics/Theta_star-Any-Angle-Path-Planning-on-Grids.md>)
- [Voronoi Graph](<robotics/Voronoi Graph.md>)
- [cartographer相关参数解析](<robotics/cartographer相关参数解析.md>)
- [cuVSLAM：CUDA 加速的视觉里程计与建图](<robotics/cuVSLAM.md>)
- [lightning-lm](<robotics/lightning-lm.md>)
- [ros2 cost function](<robotics/ros2_cost_function.md>)
- [turbot建图，导航算法汇总](<robotics/turbot建图导航算法汇总.md>)
- [ubuntu 18.04系统下安装turtlebot2](<robotics/ubuntu18.04系统下安装turtlebot2.md>)
- [ORB-SLAM3 and HF-NET安装(Windows)](<robotics/windows下orb和hfnet安装.md>)
- [单目视觉的尺度等价性](<robotics/单目视觉的尺度等价性.md>)
- [占据栅格地图构建（Occupancy Grid Map）](<robotics/占据栅格地图构建.md>)
- [双目立体视觉SAD匹配算法](<robotics/双目立体视觉SAD匹配算法.md>)
- [局部规划算法：DWA算法原理](<robotics/局部规划算法DWA.md>)
- [旋转下激光雷达数据与地图产生错位的问题](<robotics/旋转下激光雷达数据与地图产生错位的问题.md>)
- [本质矩阵，基础矩阵，自由度及其解法](<robotics/本质矩阵基础矩阵的自由度及其解法.md>)
- [激光雷达与IMU联合标定(lidar_IMU_calib）](<robotics/激光雷达与IMU联合标定(lidar_IMU_calib).md>)
- [激光雷达与相机外参联合标定调研](<robotics/激光雷达与相机外参联合标定调研.md>)
- [激光雷达性能指标](<robotics/激光雷达性能指标.md>)
- [灰度质心法](<robotics/灰度质心法.md>)
- [相机参数DKPR的解释](<robotics/相机参数DKPR的解释.md>)
- [路径规划之A*算法](<robotics/路径规划之A-star算法.md>)
- [轨迹评估介绍与evo工具使用](<robotics/轨迹评估介绍与evo工具使用.md>)
- [重投影误差](<robotics/重投影误差.md>)

### ROS 与中间件

- [ROS2_command](<ros/ROS2_command.md>)
- [ROS2的Launch文件简介](<ros/ROS2的Launch文件简介.md>)
- [ROS Nodehandle句柄的理解](<ros/ROSNodehandle句柄的理解.md>)
- [ROS param 的使用](<ros/ROSparam的使用.md>)
- [ROS指令](<ros/ROS指令.md>)
- [ros查看ZED节点效果](<ros/ROS查看ZED节点效果.md>)
- [ROS激光雷达信息、点云信息和PCL信息之间的转换](<ros/ROS激光雷达信息、点云信息和PCL信息之间的转换.md>)
- [Rosdep update failed](<ros/Rosdep_update_failed.md>)
- [Ros中Remap(话题重映射)的两种使用方法](<ros/Ros中Remap(话题重映射)的两种使用方法.md>)

### C++ 开发

- [C++11:chrono库 (日期和时间库)](<cpp/C++11chrono库.md>)
- [C++下const增强](<cpp/C++下const增强.md>)
- [EigenGeometry](<cpp/EigenGeometry.md>)
- [Eigen几何模块的引入](<cpp/Eigen几何模块的引入.md>)
- [Opencv Mat](<cpp/Opencv_Mat.md>)
- [Protocol Buffers基本使用](<cpp/Protocol_Buffers基本使用.md>)
- [STL容器使用时机](<cpp/STL容器使用时机.md>)
- [boost::condition_variable](<cpp/boostcondition_variable.md>)
- [c++ boost circular_buffer](<cpp/c++_boost_circular_buffer.md>)
- [c++并发与多线程](<cpp/c++并发与多线程.md>)
- [cin与get（）getline（）输入问题](<cpp/cin与get()getline()输入问题.md>)
- [const成员函数](<cpp/const成员函数.md>)
- [string和cstring头文件的区别](<cpp/string和cstring头文件的区别.md>)
- [二叉树遍历](<cpp/二叉树遍历.md>)
- [内联函数](<cpp/内联函数.md>)
- [函数指针的定义方式](<cpp/函数指针的定义方式.md>)
- [单例模式](<cpp/单例模式.md>)
- [如何正确使用resize和reserve](<cpp/如何正确使用resize和reserve.md>)
- [一、左值与右值](<cpp/左值与右值.md>)
- [常用函数](<cpp/常用函数.md>)
- [常规函数与内联函数](<cpp/常规函数与内联函数.md>)
- [数据类型字节数](<cpp/数据类型字节数.md>)
- [构造函数的调用时机](<cpp/构造函数的调用时机.md>)
- [类知识点](<cpp/类知识点.md>)
- [计算时间](<cpp/计算时间.md>)
- [设计模式之生产者-消费者模型](<cpp/设计模式之生产者-消费者模型.md>)
- [转换](<cpp/转换.md>)

### 开发工具与工程实践

- [CMakeLists简易模板](<engineering/CMakeLists简易模板.md>)
- [CMakelists基础指令](<engineering/CMakelists基础指令.md>)
- [Docker简要教程](<engineering/Docker简要教程.md>)
- [Find_package](<engineering/Find_package.md>)
- [Git Command](<engineering/Git_Command.md>)
- [Git中submodule的使用](<engineering/Git中submodule的使用.md>)
- [GoogleTest](<engineering/GoogleTest.md>)
- [VSCode使用Clangd](<engineering/VSCode使用Clangd.md>)
- [Conda 常用命令速查表](<engineering/conda_常用命令速查表.md>)
- [gflags使用](<engineering/gflags使用.md>)
- [git cherry-pick](<engineering/git_cherry-pick.md>)
- [git rebase的两种用法](<engineering/git_rebase的两种用法.md>)
- [使用 Docker 制作 ROS 2 镜像并集成 GitLab CI/CD](<engineering/ros2_cicd_gitlab.md>)

### Linux 与嵌入式

- [NVIDIA Jetson Xavier / Orin 常用命令](<linux/Jetson常用命令.md>)
- [单引号与双引号必须要成对，在上面的配置中仅有一个单引号，因此当你按下 enter 后，](<linux/Shell的变量功能.md>)
- [NVIDIA Jetson Xavier / Orin 环境配置](<linux/Ubuntu-xavier.md>)
- [Ubuntu18.04环境配置](<linux/Ubuntu18.04环境配置.md>)
- [Xsens传感器调用](<linux/Xsens传感器调用.md>)
- [基于ROS的opencv安装与卸载](<linux/基于ROS的opencv安装与卸载.md>)
- [如何在Linux中使用Clash](<linux/如何在Linux中使用Clash.md>)
- [认识BASH这个Shell](<linux/认识BASH这个Shell.md>)

### Qt 开发

- [QT迁移项目后显示修改时间大于当前时间](<qt/QT迁移项目后显示修改时间大于当前时间.md>)
- [Qt点击按钮进行页面的切换](<qt/Qt点击按钮进行页面的切换.md>)
- [基于arm架构的NVIDIA Xavier安装Qt](<qt/基于arm架构的NVIDIA_Xavier安装Qt.md>)
- [项目默认文件介绍](<qt/项目默认文件介绍.md>)

### 应用与杂记

- [stable-diffusion-webui手动安装详细步骤](<misc/stable-diffusion-webui手动安装详细步骤.md>)
- [获取Office 2021安装包及相关激活工具🛠️](<misc/获取Office2021安装包及相关激活工具.md>)

### 论文阅读

- [NavGPT-3 (arXiv 2026)](<papers/2610.10787-NavGPT-3.md>)
- [ASENA-VLN (arXiv 2026)](<papers/2609.39207-ASENA-VLN.md>)
- [EvolvingNav (arXiv 2026)](<papers/2609.39166-EvolvingNav.md>)
- [PanoVLN (arXiv 2026)](<papers/2609.34759-PanoVLN.md>)
- [NavHarness (arXiv 2026)](<papers/2609.34276-NavHarness.md>)
- [VGGT-Diff (arXiv 2026)](<papers/2609.33253-VGGT-Diff.md>)
- [Spatial-Nav-100K (arXiv 2026)](<papers/2609.29934-Spatial-Nav-100K.md>)
- [AdaGeoVLN (arXiv 2026)](<papers/2609.18789-AdaGeoVLN.md>)
- [SURE-Map (arXiv 2026)](<papers/2609.15795-SURE-Map.md>)
- [HarnessVLN (arXiv 2026)](<papers/2609.15195-HarnessVLN.md>)
- [Functional SLAM (arXiv 2026)](<papers/2609.07497-Functional-SLAM.md>)
- [OpenWAM (arXiv 2026)](<papers/2609.07398-OpenWAM.md>)
- [BLASt3R (arXiv 2026)](<papers/2609.05210-BLASt3R.md>)
- [LookStep (EMNLP 2026)](<papers/2609.02350-LookStep.md>)
- [LightNav-0 (arXiv 2026)](<papers/2608.30935-LightNav-0.md>)
- [SuperMap (arXiv 2026)](<papers/2608.22896-SuperMap.md>)
- [LT-Mem (arXiv 2026)](<papers/2608.19059-LT-Mem.md>)
- [Embodied Navigator (arXiv 2026)](<papers/2608.17512-Embodied-Navigator.md>)
- [OccPlanner (arXiv 2026)](<papers/2608.14160-OccPlanner.md>)
- [Map-Det3D (arXiv 2026)](<papers/2608.12179-Map-Det3D.md>)
- [OccAnyScene (arXiv 2026)](<papers/2608.08696-OccAnyScene.md>)
- [ULVN (arXiv 2026)](<papers/2608.06833-ULVN.md>)
- [SLAMFormer Infinity (arXiv 2026)](<papers/2608.03429-SLAMFormer-Infinity.md>)
- [X-NavDP (arXiv 2026)](<papers/2607.28560-X-NavDP.md>)
- [TurboVLA (arXiv 2026)](<papers/2607.27205-TurboVLA.md>)
- [VidMap (arXiv 2026)](<papers/2607.27194-VidMap.md>)
- [DB-VIO (arXiv 2026)](<papers/2607.22123-DB-VIO.md>)
- [VLN-AVP (arXiv 2026)](<papers/2607.17767-VLN-AVP.md>)
- [SoftNav (arXiv 2026)](<papers/2607.14586-SoftNav.md>)
- [i3DGS (arXiv 2026)](<papers/2607.14481-i3DGS.md>)
- [Why Does Deep Learning Improve Visual SLAM? (arXiv 2026)](<papers/2607.06023-Why-Does-Deep-Learning-Improve-Visual-SLAM.md>)
- [RoamFlow (arXiv 2026)](<papers/2606.29934-RoamFlow.md>)
- [SCAN Planner (arXiv 2026)](<papers/2606.19555-SCAN-Planner.md>)
- [OneCanvas (arXiv 2026)](<papers/2606.19253-OneCanvas.md>)
- [Ultra-Fusion (arXiv 2026)](<papers/2606.21223-Ultra-Fusion.md>)
- [Qwen-RobotNav (arXiv 2026)](<papers/2606.18112-Qwen-RobotNav.md>)
- [NavWAM (arXiv 2026)](<papers/2606.13494-NavWAM.md>)
- [WAM-Nav (arXiv 2026)](<papers/2606.04907-WAM-Nav.md>)
- [WorldVLN (arXiv 2026)](<papers/2605.15964-WorldVLN.md>)
- [VGGT-Omega (arXiv 2026)](<papers/2605.15195-VGGT-Omega.md>)
- [FLUX (arXiv 2026)](<papers/2603.12806-FLUX.md>)
- [LongStream (CVPR 2026)](<papers/2602.13172-LongStream.md>)
- [ABot-N0 (arXiv 2026)](<papers/2602.11598-ABot-N0.md>)
- [LangMap (arXiv 2026)](<papers/2602.02220v3-LangMap.md>)
- [NaVIDA (arXiv 2026)](<papers/2601.18188-NaVIDA.md>)
- [FantasyVLN (arXiv 2026)](<papers/2601.13976-FantasyVLN.md>)
- [SCE-SLAM (arXiv 2026)](<papers/2601.09665-SCE-SLAM.md>)
- [DriveVLN (CVPR 2026)](<papers/CVPR2026-DriveVLN.md>)
- [LoGoPlanner (arXiv 2025)](<papers/2512.19629-LoGoPlanner.md>)
- [D3D-VLP (arXiv 2025)](<papers/2512.12622-D3D-VLP.md>)
- [InternVLA-N1 (arXiv 2025)](<papers/2512.08186-InternVLA-N1.md>)
- [DiffusionDriveV2 (arXiv 2025)](<papers/2512.07745-DiffusionDriveV2.md>)
- [G2VLM (arXiv 2025)](<papers/2511.21688-G2VLM.md>)
- [SONIC (arXiv 2025)](<papers/2511.07820-SONIC.md>)
- [SAGE-3D (arXiv 2025)](<papers/2510.21307-SAGE-3D.md>)
- [DriveVLA-W0 (arXiv 2025)](<papers/2510.12796-DriveVLA-W0.md>)
- [OmniNav (arXiv 2025)](<papers/2509.25687-OmniNav.md>)
- [OmniVLA (arXiv 2025)](<papers/2509.19480-OmniVLA.md>)
- [JanusVLN (arXiv 2025)](<papers/2509.22548-JanusVLN.md>)
- [MASt3R-Fusion (arXiv 2025)](<papers/2509.20757-MASt3R-Fusion.md>)
- [GUIDE (arXiv 2025)](<papers/2509.19916-GUIDE.md>)
- [SLAM-Former (arXiv 2025)](<papers/2509.16909-SLAM-Former.md>)
- [NavFoM (arXiv 2025)](<papers/2509.12129-NavFoM.md>)
- [DCReg (arXiv 2025)](<papers/2509.06285-DCReg.md>)
- [MultiPark (arXiv 2025)](<papers/2508.11537-MultiPark.md>)
- [OVSegDT (arXiv 2025)](<papers/2508.11479-OVSegDT.md>)
- [SpatialLM (arXiv 2025)](<papers/2506.07491-SpatialLM.md>)
- [2DGS-SLAM (arXiv 2025)](<papers/2506.00970-2DGS-SLAM.md>)
- [NavDP (arXiv 2025)](<papers/2505.08712-NavDP.md>)
- [Flow-GRPO (arXiv 2025)](<papers/2505.05470-Flow-GRPO.md>)
- [π₀.₅ (arXiv 2025)](<papers/2504.16054-π₀.₅.md>)
- [ApexNav (arXiv 2025)](<papers/2504.14478-ApexNav.md>)
- [Embodied Reasoner (arXiv 2025)](<papers/2503.21696-Embodied-Reasoner.md>)
- [GR00T N1 (arXiv 2025)](<papers/2503.14734-GR00T-N1.md>)
- [VGGT (CVPR 2025)](<papers/2503.11651-VGGT.md>)
- [Hier-SLAM++ (arXiv 2025)](<papers/2502.14931-Hier-SLAM++.md>)
- [VL-Nav (arXiv 2025)](<papers/2502.00931-VL-Nav.md>)
- [SLAM3R (arXiv 2024)](<papers/2412.09401-SLAM3R.md>)
- [Uni-NaVid (arXiv 2024)](<papers/2412.06224-Uni-NaVid.md>)
- [DiffusionDrive (CVPR 2025)](<papers/2411.15139-DiffusionDrive.md>)
- [SSR (arXiv 2024)](<papers/2409.18341-SSR.md>)
- [SpatialBot (arXiv 2024)](<papers/2406.13642-SpatialBot.md>)
- [OpenVLA (arXiv 2024)](<papers/2406.09246-OpenVLA.md>)
- [PLUTO (arXiv 2024)](<papers/2404.14327-PLUTO.md>)
- [SparseDrive (arXiv 2024)](<papers/2405.19620-SparseDrive.md>)
- [Sparse4D v3 (arXiv 2023)](<papers/2311.11722-Sparse4D-v3.md>)
- [NoMaD (ICRA 2024)](<papers/2310.07896-NoMaD.md>)
- [SparseBEV (ICCV 2023)](<papers/2308.09244-SparseBEV.md>)
- [ACT (RSS 2023)](<papers/2304.13705-ACT.md>)
- [Diffusion Policy (RSS 2023)](<papers/2303.04137-Diffusion-Policy.md>)
- [ViNT (CoRL 2023)](<papers/2306.14846-ViNT.md>)
- [BEVFormer v2 (CVPR 2023)](<papers/2211.10439-BEVFormer-v2.md>)
- [GNM (ICRA 2023)](<papers/2210.03370-GNM.md>)
- [BEVFormer (ECCV 2022)](<papers/2203.17270-BEVFormer.md>)
- [Kimera 3D DSG (IJRR 2021)](<papers/2101.06894-Kimera-3D-DSG.md>)
- [Kimera SLAM (ICRA 2020)](<papers/1910.02490-Kimera-SLAM.md>)
- [GraspNet (CVPR 2020)](<papers/1912.13470-GraspNet.md>)
- [RAFT 光流 (ECCV 2020)](<papers/2003.12039-RAFT-光流.md>)

## Resource
#### Online Learning
- 文献检索：研究僧的好伴侣，[Sci-Hub](https://www.sci-hub.ren/)和[arXiv](https://arxiv.org/)，基本顶会论文都可以免费下载阅读。
- [Paperswithcode](https://paperswithcode.com/)：作为AI工程师（cv工程师）中的一员，这个网站可以找到许多附带开源代码的高质量论文。
- [*Z-Library*](https://zh.1lib.education/)：这个数字图书馆可以搜索下载到各种各样的电子书，十分强大，而且免费，不过需要科学上网才能访问。
- EbookFoundation开源了一个仓库[free-programming-books](https://github.com/EbookFoundation/free-programming-books)，在这里你可以找到许多编程类电子书籍和开源课程。
- [awesome-cs-books](https://github.com/imarvinle/awesome-cs-books)这个仓库包含了许多经典计算机书籍： 编程语言(Java、C++、C、Python等等)、操作系统、计算机网络、系统架构、程序员数学、测试、前端开发、后台开发、网络编程、Linux使用及内核、求职面试、算法与数据结构 安卓、IOS、数据库、Redis等主流的编程学习书籍。
- [HelloGitHub](https://github.com/521xueweihan/HelloGitHub)：HelloGitHub 每个月定期分享 GitHub 上有趣、入门级的开源项目。包含多种编程语言，有助于编程语言的学习。
- [基本素养](https://github.com/ahangchen/How-to-Be-A-Programmer-CN)：一个合格的程序员需要具备许多其他的基本素养。
- [数据结构](https://github.com/youngyangyang04/leetcode-master)：这是一个[算法可视化平台](https://github.com/algorithm-visualizer/algorithm-visualizer)，极大地提高了我自己的算法学习理解效率。
- [鸟哥的私房菜](http://cn.linux.vbird.org/linux_basic/linux_basic.php#part3)：linux开发者必读书籍。
- [Linux命令大全](https://www.linuxcool.com/)：鸟哥的私房菜过于详细，平常的学习工作中我们可能只是想要简单快速查一下某一个命令的用法。 
- [PPT模板](https://www.pptsupermarket.com/)：面向ppt编程。
#### Electronic Book

- [视觉SLAM](https://pan.baidu.com/s/1yFiZ_fqGnKi7ExvMyvChgQ)	提取码：`1314`
- [深度学习](https://pan.baidu.com/s/12Y30nfWqwDqeXz6sCvZgTw)	提取码：`1314`
- [C++](https://pan.baidu.com/s/1DsBbgs2GZlxJel5cXpIOYQ)	提取码：`1314`
- [Linux](https://pan.baidu.com/s/1f_jGoBzd1ZihH_OhGTC82w)	提取码：`1314`
- [Python](https://pan.baidu.com/s/1m7_FToasf8VPln4-8jilLw)	提取码：`1314`
- [操作系统](https://pan.baidu.com/s/17eCDh-8gxEpyBoNORodYaA)	提取码：`1314`
- [分布式系统](https://pan.baidu.com/s/1GaSVK03EPcnQdH4BhcxZZA)	提取码：`1314`
- [后端组件](https://pan.baidu.com/s/1drB61wzhg7NG5L3KhOmrXw)	提取码：`1314`
- [计算机网络](https://pan.baidu.com/s/1qBYrPLhRpF_dhPXOQBRNZQ)	提取码：`1314`
- [计算机系统知识](https://pan.baidu.com/s/1I54KS6usF89NhRgagSGjeQ)	提取码：`1314`
- [前端&全栈](https://pan.baidu.com/s/1pKy4BJKc7SpRGq0Js-0t_A)	提取码：`1314`
- [数据库](https://pan.baidu.com/s/1eWlvbY0MrqRF2fI6p7kb4g)	提取码：`1314`
- [算法和数据结构](https://pan.baidu.com/s/1Xl8U-exf7lb2b9ss593_2g)	提取码：`1314`
- [网络编程&服务器开发](https://pan.baidu.com/s/1diwvkJGb2CFkonj40yvtew)	提取码：`1314`
- [面经](https://pan.baidu.com/s/1WGa1TRnHh3m1wmV4F7EkGA)	提取码：`1314`
- [面试准备](https://pan.baidu.com/s/1FCYJ-TDRfKKkHlQWSgmL3g)	提取码：`1314`
## Software
- ### Typora

  ​	强烈建议大家使用Markdown语法编写文档。它允许使用纯文本格式编写文档，由于 Markdown 的轻量化、易读易写特性，许多网站都广泛使用 Markdown 来撰写帮助文档， 如 GitHub等， Markdown格式同步github远程仓库十分方便，不用担心windows层出不穷的格式错误。这里推荐一款Markdown语法软件Typora，由于最近该软件开始收费了，本着学习（白嫖）的精神，因此保存了不收费的一个版本。

  - [Windows版本](https://pan.baidu.com/s/14Fftz3ECigAh-abV-7VHrw)	提取码：`1314`
  - [Linux版本](https://pan.baidu.com/s/1rAC-yfhA9-UNVo0y0AIxyg)	提取码：`1314`

- ### XMind

  最好的思维导图软件，没有之一，可惜高级功能要收费，这里分享一个破解版，可以无水印导出。下载安装直接使用，破解版软件不能登录不用我多说吧，亲测无病毒，放心使用。

  - [Windows版本](https://pan.baidu.com/s/1oFZS5czuOF2hEbw1LfxgIw)	提取码：`1314`
  - [Linux版本](https://pan.baidu.com/s/1eiOgY8p2Ytlt8AaEUIHelQ)	提取码：`1314`
  - 流氓软件默认安装c盘，安装好后将破解包里的app.asar文件复制替换到Program Files/XMind/resources目录下即可。	

- ### MATLAB

  ​	MATLAB，工科生居家旅行必备。

  - [MATLAB](https://pan.baidu.com/s/1aH7OmFjjGApE-v0Md8aB8w)	提取码：`1314`
  - 至于详细怎么安装，请自行百度。ps：安装镜像文件，选择密钥安装，最后将补丁文件复制到软件文件夹下全部替换就可以啦O(∩_∩)O
- ### Adobe pdf

  ​	功能最全的pdf编辑器，一键安装。

  - [Adobe pdf](https://pan.baidu.com/s/1Fz0iAbfkJAFFD0ye20nvVA)	提取码：`1314`
  - Adobe系列版本越高对电脑配置要求越高，所以全家桶都有点卡顿，但不影响正常使用。附[Adobe全家桶](https://pan.baidu.com/s/1SYvUb4AeAQ5R6Lq6xypQNw)	提取码：`1314`
- ### Pycharm

  ​	Python IDE推荐，我是JetBrains家一系列开发工具的忠实使用者，主要优点：与时俱进，界面优美，功能强大。Pycharm一键注册99年，从此告别Mobxterm。

  - [Pycharm(windows版本)](https://pan.baidu.com/s/11syiJLEngQL3-8pzqshe7w)	提取码：`1314`
  - 将注册机拖到pycharm里一键完成注册即可。



- ### Office全家桶

  ​	office是目前最常用的一类办公软件，使用它可以解决日常生活和工作中遇到的很多问题。里面包含了常用的办公组件而其中就包含了Word、Excel、PowerPoint、Access等。熟练掌握offic的操作技巧是对计算机工作者的基本要求,也可以帮助你提高工作效率出色的完成企业办公需求!新版的Office与之前的老版本相比，不仅是界面上有所改进，同时在功能上也带来一些新特性!

  - [Office全家桶](https://pan.baidu.com/s/1LJo45XDnsSltjNCaiWT0MQ) 提取码：`1314`
  - [破解教程](misc/获取Office2021安装包及相关激活工具.md)

## Other

- [Stable Diffusion WebUI 手动安装详细步骤](misc/stable-diffusion-webui手动安装详细步骤.md)
- [如何在 Linux 中使用 Clash](linux/如何在Linux中使用Clash.md)
- [科学上网](https://github.com/shadowsocks/shadowsocks-windows)：科学上网，国内的程序员论坛实在是不敢恭维。这里推荐一个比较不错的^_^。

## 维护者

[@HanLin](https://github.com/hanlin-cheng)

> *关山难越，谁悲失路之人。萍水相逢，尽是他乡之客。*
