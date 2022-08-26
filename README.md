# Study-note from HanLin

##### *内心os：我好笨，我记不住，虽然好麻烦好麻烦，但是我得做笔记记录下来...*



本仓库包含以下内容：

1. 一些学习的[心得笔记](#心的笔记)

3. 一些可能有用的[电子书籍和学习资源](#Resource)（白嫖使我快乐）

3. 一些[学习软件](#Software)的安装（还是白嫖^_^，仅供自身学习使用，请勿外传，亲测无病毒，放心使用！）

4. 一些[杂项](#Other)

5. 待更新......!^_^






## 心得笔记

- ### [SLAM Theory](./slam_theory/)

  - [匈牙利算法](./slam_theory/匈牙利算法.md)
  - [相机参数DKPR的解释](./slam_theory/相机参数DKPR的解释.md)
  - 待上传

- ### [C++相关知识](./C++)
  
  - [常用函数](./C++/常用函数.md)
  - [opencv常用api](./C++/opencv常用api.md)
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
  - [ROS指令](./C++/ROS指令.md)
  - [CMakelists基础指令](./C++/CMakelists基础指令.md)
  - [EigenGeometry](./C++/EigenGeometry.md)
  - [Eigen几何模块的引入](./C++/EigenGeometry.md)
  - [find_package指令](./C++/Find_package.md)
  - [gflags使用](./C++/gflags使用.md)
  
- ### [SLAM配置文档](./slam_config/)

  - [ubuntu18.04系统下安装turtlebot2](slam_config/ubuntu18.04系统下安装turtlebot2.md)
  - [xavier装机文档](slam_config/xavier装机文档.md)
  - [turbot建图导航算法](slam_config/turbot建图导航算法汇总.md)
  - [Rosdep_update_falied](slam_config/Rosdep_update_failed.md)
  - [安装LIO-SAM](slam_config/安装LIO-SAM.md)
  - [安装orb-slam2](slam_config/安装Orb-slam2.md)
  - [安装Rtabmap](slam_config/安装Rtabmap.md)
  - [安装VINS-Fusion](slam_config/安装VINS-Fusion.md)
  - [安装ZED以及ros驱动](slam_config/安装ZED以及ros驱动.md)
  - [轨迹评估介绍与evo工具使用](slam_config/轨迹评估介绍与evo工具使用.md)
  - [基于ROS的opencv安装与卸载](slam_config/基于ROS的opencv安装与卸载.md)
  - [激光+imu标定(lidar_align)](slam_config/激光+imu标定(lidar_align​).pdf)
  - [激光+imu标定(lidar_imu_calib)](slam_config/激光雷达与IMU联合标定(lidar_IMU_calib).md)
  - [相机+imu标定](slam_config/相机+imu标定.pdf)
  - [激光雷达+相机标定](slam_config/激光雷达+相机标定.pdf)
  - [ubuntu18.04环境配置](./slam_config/Ubuntu18.04环境配置.md)

- ### Linux

  - 待上传

- ### [QT编程](./QT)
  
  - [项目默认文件介绍](./QT/项目默认文件介绍.md)
  - [qt点击按钮进行页面的切换](./QT/Qt点击按钮进行页面的切换.md)
  - [qt迁移项目后修改时间大于当前时间](./QT/QT迁移项目后显示修改时间大于当前时间.md)
  - [基于arm架构的NVIDIA Xavier安装Qt](./QT/基于arm架构的NVIDIA_Xavier安装Qt.md)
  
- ### 一些怎么也记不全的[command](./command/)指令
  
  - [Git_Command](./command/Git_Command.md)
  - [Ubuntu-xavier](./command/Ubuntu-xavier.md)
  - [LIO-SAM运行](./command/LIO-SAM运行.md)
  - [Orb-slam2运行](./command/Orb-slam2运行.md)
  - [Velodyne_VLP16线激光雷达调用](./command/Velodyne_VLP16激光雷达调用.md)
  - [Xsens传感器调用](./command/Xsens传感器调用.md)
  - [ROS查看ZED节点此效果](./command/ROS查看ZED节点效果.md)





## Resource

#### Online Learning

- 文献检索：苦逼研究僧的好伴侣，[Sci-Hub](https://www.sci-hub.ren/)和[arXiv](https://arxiv.org/)，基本顶会论文都可以免费下载阅读。
- [Paperswithcode](https://paperswithcode.com/)：作为AI工程师（ctrl c + ctrl v工程师）中的一员，这个网站可以找到许多附带开源代码的高质量论文。
- [*Z-Library*](https://zh.1lib.education/)：这个数字图书馆可以搜索下载到各种各样的电子书，十分强大，而且免费，不过需要科学上网才能访问。
- EbookFoundation开源了一个仓库[free-programming-books](https://github.com/EbookFoundation/free-programming-books)，在这里你可以找到许多编程类电子书籍和开源课程。
- [awesome-cs-books](https://github.com/imarvinle/awesome-cs-books)这个仓库包含了许多经典计算机书籍： 编程语言(Java、C++、C、Python等等)、操作系统、计算机网络、系统架构、程序员数学、测试、前端开发、后台开发、网络编程、Linux使用及内核、求职面试、算法与数据结构 安卓、IOS、数据库、Redis等主流的编程学习书籍。
- [HelloGitHub](https://github.com/521xueweihan/HelloGitHub)：HelloGitHub 每个月定期分享 GitHub 上有趣、入门级的开源项目。包含多种编程语言，有助于编程语言的学习。
- [基本素养](https://github.com/ahangchen/How-to-Be-A-Programmer-CN)：当然除了编程语言外，想要成长为一个合格的程序员还是需要具备许多其他的基本素养。
- [数据结构](https://github.com/youngyangyang04/leetcode-master)：数据结构很重要哦，毕竟也要去面试嘛，还有一个[算法可视化平台](https://github.com/algorithm-visualizer/algorithm-visualizer)，极大地提高了我自己的算法学习理解效率。
- [鸟哥的私房菜](http://cn.linux.vbird.org/linux_basic/linux_basic.php#part3)：这本书应该是不少linux开发者必读书籍吧。
- [Linux命令大全](https://www.linuxcool.com/)：当然了鸟哥的私房菜实在是过于详细，平常的学习工作中我们可能只是想要简单快速查一下某一个命令的用法。 
- [PPT模板](https://www.pptsupermarket.com/)：面向ppt编程（手动狗头），怎么可以没有好用的模板呢。



#### Electronic Book

待上传。。。



## Software

- ### Typora

  ​	作为一名程序员，强烈建议大家使用Markdown语法编写文档。它允许人们使用纯文本格式编写文档，由于 Markdown 的轻量化、易读易写特性，许多网站都广泛使用 Markdown 来撰写帮助文档， 如 GitHub等， Markdown格式同步github远程仓库十分方便，不用担心windows层出不穷的格式错误。这里推荐一款Markdown语法软件Typora，由于最近该软件开始收费了，本着学习（白嫖）的精神，因此保存了不收费的一个版本。

  - [Windows版本](https://pan.baidu.com/s/14Fftz3ECigAh-abV-7VHrw)	提取码：`1314`
  - [Linux版本](https://pan.baidu.com/s/1rAC-yfhA9-UNVo0y0AIxyg)	提取码：`1314`



- ### XMind

  ​	最好的思维导图软件，没有之一，可惜高级功能要收费，这里分享一个破解版，可以无水印导出。下载安装直接使用，破解版软件不能登录不用我多说吧，亲测无病毒，放心使用。

  - [Windows版本](https://pan.baidu.com/s/1oFZS5czuOF2hEbw1LfxgIw)	提取码：`1314`
  - [Linux版本](https://pan.baidu.com/s/1eiOgY8p2Ytlt8AaEUIHelQ)	提取码：`1314`
  - 流氓软件默认安装c盘，安装好后将破解包里的app.asar文件复制替换到Program Files/XMind/resources目录下即可。	



- ### MATLAB

  ​	MATLAB，工科生居家旅行必备。有什么好说的吗？没什么好说的吧......

  - [MATLAB](https://pan.baidu.com/s/1aH7OmFjjGApE-v0Md8aB8w)	提取码：`1314`
  - 至于详细怎么安装，请自行百度。ps：安装镜像文件，选择密钥安装，最后将补丁文件复制到软件文件夹下全部替换就可以啦O(∩_∩)O



- ### IDM

  ​	受够了百度云，迅雷的限速吗，请使用IDM下载器，还支持浏览器插件版本，天下苦百度迅雷久矣........

  - [IDM](https://pan.baidu.com/s/1KnHhyMaYgHZpbFE4twwTqg)	提取码：`1314`
  - IDM支持百度云不限速下载助手，至于怎么用，tampermonkey插件yyds。



- ### Adobe pdf

  ​	功能最全的pdf编辑器，一键安装。

  - [Adobe pdf](https://pan.baidu.com/s/1Fz0iAbfkJAFFD0ye20nvVA)	提取码：`1314`
  - Adobe系列版本越高对电脑配置要求越高，所以全家桶都有点卡顿，但不影响正常使用。附[Adobe全家桶](https://pan.baidu.com/s/1SYvUb4AeAQ5R6Lq6xypQNw)	提取码：`1314`



- ### Pycharm

  ​	Python IDE推荐，我是JetBrains家一系列开发工具的忠实使用者，主要优点：与时俱进，界面优美，功能强大。你还在为同步GitHub仓库发愁吗，你还在为ssh链接服务器痛苦吗，Pycharm一键注册99年，从此告别Mobxterm。

  - [Pycharm(windows版本)](https://pan.baidu.com/s/11syiJLEngQL3-8pzqshe7w)	提取码：`1314`
  - 将注册机拖到pycharm里一键完成注册即可。





## Other

- [科学上网](https://github.com/shadowsocks/shadowsocks-windows)：学会科学上网是一个程序员的基本能力，国内的程序员论坛实在是不敢恭维。这里推荐一个，至于怎么用，请自己去学习吧^_^。





## 维护者

[@HanLin](https://github.com/hanlin-cheng)

> *关山难越，谁悲失路之人。萍水相逢，尽是他乡之客。*


