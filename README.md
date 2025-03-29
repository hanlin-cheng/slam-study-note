# Study-note from HanLin

本仓库包含以下内容：

1. 一些学习的[心得笔记](#心的笔记)
2. 
3. 一些可能有用的[电子书籍和学习资源](#Resource)

4. 一些[学习软件](#Software)的安装（仅供自身学习使用，亲测无病毒，放心使用！）

5. 一些[杂项](#Other)

6. TODO......

*注：后续文档撰写使用obsidian打开*

## 心得笔记
### Algorithm
  - **Math and Matrix**
    - [多维高斯分布](./Algorithm/多维高斯分布.md)
    - [正态分布](./Algorithm/正态分布.md)
    - [雅可比矩阵和海塞矩阵](./Algorithm/雅克比矩阵和海塞矩阵.md)
    - [高斯牛顿法](./Algorithm/高斯牛顿法.md)
    - [归一化与标准化](./Algorithm/归一化与标准化.md)
    - [多元函数的泰勒展开式](./Algorithm/多元函数的泰勒展开式.md)
    - [矩阵的奇异值分解](./Algorithm/矩阵的奇异值分解.md)
    - [矩阵微分](./Algorithm/矩阵微分-清华.pdf)
    - [向量积(矢积)与数量积(标积)的区别](./Algorithm/向量积(矢积)与数量积(标积)的区别.md)
    - [协方差矩阵](./Algorithm/协方差矩阵.md)
    - [超定方程组的最小二乘解](./Algorithm/超定方程组的最小二乘解.md)
  - **SLAM**
    - [相机参数DKPR的解释](./Algorithm/相机参数DKPR的解释.md)
    - [灰度质心法](./Algorithm/灰度质心法.md)
    - [单目视觉的尺度等价性](./Algorithm/单目视觉的尺度等价性.md)
    - [为什么使用齐次坐标](./Algorithm/为什么使用齐次坐标.md)
    - [重投影误差](./Algorithm/重投影误差.md)
    - [图优化g2o](./Algorithm/图优化g2o.pdf)
    - [本质矩阵/基础矩阵/自由度及其解法](./Algorithm/本质矩阵基础矩阵的自由度及其解法.md)
    - [欧拉角与坐标系旋转变换](./Algorithm/欧拉角与坐标系旋转变换.md)
    - [SLAM中的位姿变换与轨迹评价指标APE、RPE、ATE、RTE](./Algorithm/SLAM中的位姿变换与轨迹评价指标.md)
    - [二维空间中的点坐标旋转关系](./Algorithm/二维空间中的点坐标旋转关系.md)
    - [双目立体视觉SAD匹配算法](./Algorithm/双目立体视觉SAD匹配算法.md)
    - [SVD与非线性优化在最小二乘问题中的区别](./Algorithm/SVD与非线性优化在最小二乘问题中的区别)
  - **Planning**
    - [局部规划算法DWA](./Algorithm/局部规划算法DWA.md)
    - [路径规划之A*算法](./Algorithm/路径规划之A-star算法.md)
    - [路径规划之Theta*算法](./Algorithm/Theta_star-Any-Angle-Path-Planning-on-Grids.md)
    - [占据栅格地图构建](./Algorithm/占据栅格地图构建.md)
    - [ros2 cost function](./Algorithm/ros2_cost_function.md)
  - **ROS**
    - [ROS指令](./C++/ROS指令.md)
    - [ROS激光雷达信息点云信息和PCL信息之间的转换](./Algorithm/ROS激光雷达信息、点云信息和PCL信息之间的转换.md)
    - [ROS Nodehandle句柄的理解](./Algorithm/ROSNodehandle句柄的理解.md)
    - [ROS param 的使用](./Algorithm/ROSparam的使用.md)
    - [ROS中Remap(话题重映射)的两种使用方法](./Algorithm/Ros中Remap(话题重映射)的两种使用方法.md)
  - [匈牙利算法](./Algorithm/匈牙利算法.md)
  - [2d激光雷达运动补偿方法(去畸变)](./Algorithm/2d激光雷达运动补偿方法(去畸变).md)
  - [激光雷达性能指标](./Algorithm/激光雷达性能指标.md)
  - [激光雷达与相机外参联合标定调研](./Algorithm/激光雷达与相机外参联合标定调研.md)
  - 待上传
- ### C++
  - [常用函数](./C++/常用函数.md)
  - [c++并发与多线程](./C++/c++并发与多线程.md)
  - [STL容器使用时机](./C++/STL容器使用时机.md)
  - [类型转换](./C++/转换.md)
  - [Opencv_Mat](./C++/Opencv_Mat.md)
  - [const成员函数](./C++/const成员函数.md)
  - [cin与get()getline()输入问题](./C++/cin与get()getline()输入问题.md)
  - [string和cstring头文件的区别](./C++/string和cstring头文件的区别.md)
  - [常规函数与内联函数](./C++/常规函数与内联函数.md)
  - [单例模式](./C++/单例模式.md)
  - [二叉树遍历](./C++/二叉树遍历.md)
  - [构造函数的调用时机](./C++/构造函数的调用时机.md)
  - [函数指针的定义方式](./C++/函数指针的定义方式.md)
  - [计算时间](./C++/计算时间.md)
  - [类知识点](./C++/类知识点.md)
  - [内联函数](./C++/内联函数.md)
  - [数据类型字节数](./C++/数据类型字节数.md)
  - [protobuf基本使用](./C++/Protocol_Buffers基本使用.md)
  - [EigenGeometry](./C++/EigenGeometry.md)
  - [Eigen几何模块的引入](./C++/EigenGeometry.md)
  - [gflags使用](./C++/gflags使用.md)
  - [左值与右值](./C++/左值与右值.md)
  - [C++下const增强](./C++/C++下const增强.md)
  - [C++11chrono库](./C++/C++11chrono库.md)
  - [GoogleTest](./C++/GoogleTest.md)
  - [c++ boost circular_buffer](./C++/c++_boost_circular_buffer.md)
  - [boostcondition_variable](./C++/boostcondition_variable.md)
  - [设计模式之生产者-消费者模型](./C++/设计模式之生产者-消费者模型.md)
  - [resize和reserve](./C++/如何正确使用resize和reserve)
  - **Cmake**
    - [CMakelists基础指令](./C++/CMakelists基础指令.md)
    - [CMakeLists简易模板](./C++/CMakeLists简易模板.md)
    - [find_package指令](./C++/Find_package.md)
- ### 配置文档
  - [ubuntu18.04系统下安装turtlebot2](slam_config/ubuntu18.04系统下安装turtlebot2.md)
  - [xavier装机文档](slam_config/xavier装机文档.md)
  - [turbot建图导航算法](slam_config/turbot建图导航算法汇总.md)
  - [Rosdep_update_falied](slam_config/Rosdep_update_failed.md)
  - [轨迹评估介绍与evo工具使用](slam_config/轨迹评估介绍与evo工具使用.md)
  - [基于ROS的opencv安装与卸载](slam_config/基于ROS的opencv安装与卸载.md)
  - [激光+imu标定(lidar_align)](slam_config/激光+imu标定(lidar_align​).pdf)
  - [激光+imu标定(lidar_imu_calib)](slam_config/激光雷达与IMU联合标定(lidar_IMU_calib).md)
  - [相机+imu标定](slam_config/相机+imu标定.pdf)
  - [激光雷达+相机标定](slam_config/激光雷达+相机标定.pdf)
  - [ubuntu18.04环境配置](./slam_config/Ubuntu18.04环境配置.md)
  - [ORB-SLAM3 and HF-NET安装(Windows)](./slam_config/windows下orb和hfnet安装.md)
  - [VSCode使用Clangd](./slam_config/VSCode使用Clangd.md)
- ### Linux
  - 待上传
- ### QT编程
  - [项目默认文件介绍](./QT/项目默认文件介绍.md)
  - [qt点击按钮进行页面的切换](./QT/Qt点击按钮进行页面的切换.md)
  - [qt迁移项目后修改时间大于当前时间](./QT/QT迁移项目后显示修改时间大于当前时间.md)
  - [基于arm架构的NVIDIA Xavier安装Qt](./QT/基于arm架构的NVIDIA_Xavier安装Qt.md)
- ### command
  - [Git_Command](./command/Git_Command.md)
  - [Git中submodule的使用](./command/Git中submodule的使用.md)
  - [Git cherry-pick的使用](./command/git_cherry-pick.md)
  - [Git rebase的两种用法](./command/git_rebase的两种用法.md)
  - [Docker简要教程](./command/Docker简要教程.md)
  - [Ubuntu-xavier](./command/Ubuntu-xavier.md)
  - [LIO-SAM运行](./command/LIO-SAM运行.md)
  - [Orb-slam2运行](./command/Orb-slam2运行.md)
  - [Xsens传感器调用](./command/Xsens传感器调用.md)
  - [ROS查看ZED节点此效果](./command/ROS查看ZED节点效果.md)
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

  ​最好的思维导图软件，没有之一，可惜高级功能要收费，这里分享一个破解版，可以无水印导出。下载安装直接使用，破解版软件不能登录不用我多说吧，亲测无病毒，放心使用。

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
  - [破解教程](./something_else/获取Office2021安装包及相关激活工具.md)

## Other

- [科学上网](https://github.com/shadowsocks/shadowsocks-windows)：科学上网，国内的程序员论坛实在是不敢恭维。这里推荐一个比较不错的^_^。

## 维护者

[@HanLin](https://github.com/hanlin-cheng)

> *关山难越，谁悲失路之人。萍水相逢，尽是他乡之客。*

