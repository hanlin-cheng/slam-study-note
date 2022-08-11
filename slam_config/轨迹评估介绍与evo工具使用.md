# 轨迹评估介绍与evo工具使用 

## **介绍** 

本文介绍了轨迹评估的基本概念，包括轨迹对齐、尺度变换、绝对轨迹误差、相对轨迹误差以及计算方法、相关文献，以及evo轨迹评估工具的安装、使用举例、命令参数详解。 

 

## **第一部分：evo 介绍** 

- evo是一款用于视觉里程计和SLAM问题的轨迹评估工具. 核心功能是能够绘制相机的轨迹, 或评估轨迹与真值之间的误差. 
- 它支持多种数据集的轨迹格式(TUM、KITTI、EuRoC MAV、ROS的bag)， 同时支持这些数据格式之间的相互转换。 
- 灵活的输出/绘图和导出选项(例如LaTeX绘图或Excel表格) 
- 强大的可配置的CLI, 可以涵盖多种场景使用 
- 用于自定义扩展的模块化 
- github：[github.com/MichaelGrup…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FMichaelGrupp%2Fevo.git) 
- wiki： [github.com/MichaelGrup…](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FMichaelGrupp%2Fevo%2Fwiki) 

 

## **第二部分：安装 evo 工具** 

#### **Step1： python 3.8虚拟环境搭建** 

`目的`：构筑一个纯净python 3.8环境（github上告知最新的evo版本支持Python 3.8+，若要支持Python2.7需使用 1.12.0及以下版本）。ubuntu18.04自带python 3.6和2.7，如果不使用python虚拟环境，还可以使用 update-alternatives 命令来切换OS所使用的Python版本，以下以使用Python虚拟环境为例（使用conda方式）： 

```
# 下载 anaconda 或 miniconda， 以下以 miniconda 为例 
wget https://repo.anaconda.com/archive/Anaconda3-2019.07-Linux-x86_64.sh 
或 
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda2-py27_4.8.3-Linux-x86_64.sh 
 
# 安装 miniconda 
bash iniconda2-py27_4.8.3-Linux-x86_64.sh 
source ~/.bashrc 
 
# 创建 python3.8 虚拟环境 
conda create -n slam_env python=3.8 
 
# 进入虚拟环境 
conda activate slam_env 
 
# 查看python虚拟环境和其pip工具 （此时提示符已经切换为 slam_env ） 
(slam_env) hadoop@ubuntu:~$ pip --version 
pip 21.2.4 from /home/hadoop/miniconda2/envs/slam_env/lib/python3.8/site-packages/pip (python 3.8) 
 
# 查看虚拟环境已安装的python模块 
(slam_env) hadoop@ubuntu:~$ conda list 
# packages inenvironment at /home/hadoop/miniconda2/envs/slam_env:# 
# Name                    Version                   Build  Channel 
_libgcc_mutex             0.1                        main 
_openmp_mutex             4.5                       1_gnu 
ca-certificates           2021.10.26           h06a4308_2 
certifi                   2021.10.8        py38h06a4308_2 
ld_impl_linux-64          2.35.1               h7274673_9 
libffi                    3.3                  he6710b0_2 
(下略） 
 
# 升级 pip 到最新版本 
(slam_env) hadoop@ubuntu:~$ pip install -U pip  
 
# pip 使用国内 pypi 镜像站（清华） 
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple 
```

至此，完成python虚拟环境搭建完毕，使用时使用 conda activate slam_env 即可进入python虚拟环境提示符。 



#### **Step2： 使用pip安装 evo 到虚拟环境** 

```
# 进入虚拟环境 
(base) hadoop@ubuntu:~$ conda activate slam_env 
 
# 安装evo工具 
(slam_env) hadoop@ubuntu:~$ pip install evo --upgrade --no-binary evo --user 
 
# 查看evo工具 
ls ~/.local/bin 
 
# 编辑 ~/.bashrc， 把 ~/.local/bin 目录加入到 PATH 环境变量 
echo 'export PATH=~/.local/bin:$PATH' >> ~/.bashrc 
source ~/.bashrc 
 
# 确认evo工具可用 
evo_ape --help 
 
# 安装tkinter，防止运行 evo 时报错 tkinter 找不到 
sudo apt install  python3-tk 
```

**备注1: 安装过程会自动安装所依赖的科学计算库和绘图库如numpy、scipy、pandas、matplotlib、pillow等.** 



#### **Step3：工具位置介绍** 

- ##### **安装好的 evo 的命令行工具在目录 ~/.local/bin 下** 

```
$ ls ~/.local/bin 
    activate-global-python-argcomplete  evo_traj    pygmentize 
    evo                                 f2py        python-argcomplete-check-easy-install-script 
    evo_ape                             f2py3       python-argcomplete-tcsh 
    evo_config                          f2py3.8     register-python-argcomplete 
    evo_fig                             fonttools   rosbags-convert 
    evo_ipython                         natsort     ttx 
    evo_res                             pyftmerge 
    evo_rpe                             pyftsubset 
```

- **轨迹误差评估方面**：
  - evo_ape : 计算绝对位姿误差(absolute pose error)，用于整体评估整条轨迹的`全局一致性`； 
  - evo_rpe : 计算相对位姿误差(relative pose error)，用于评价轨迹局部的`准确性`。 
- **绘图方面**：
  - evo_traj : 分析/绘图/导出一条或多条轨迹 
- **评估结果比较方面**：
  - evo_res : 比较一个或多个结果文件, 结果文件来自 evo_ape 或 evp_rpe的输出 
- **其他**： 
  - evo_config : 对evo工具的全局设置
- **需要注意, EUROC数据集、TUM数据集、KITTI数据集使用四元数的顺序是不一样的， 因此在使用 evo 命令时， 需要增加一个命令选项参数进行区分。** 



## **第三部分： 基本概念简介** 

在使用evo工具之前， 先介绍一些evo工具使用过程中会遇到的轨迹评估方面的基本概念，以便于更好的理解工具的输出信息和图表。 

基本介绍信息如下所示。 

### **1. 轨迹对齐** 

在定量轨迹评估过程时，如图所示，首先估计的轨迹（蓝色）要和真值（黑色）对齐，然后再利用特性的误差度量计算对气候的轨迹估计误差。 

![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage6.png)

下图左图是对齐前，右图是对齐后； 其中灰色线为对应的状态 

![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage7.png)

### **2. 尺度变换** 

**尺度歧义性**？ 

根据单目相机相机测量模型可知其尺度歧义性，如图所示，相机将3D点（红色×）投影到成像平面上的2D点（黑色○）。对于单目相机，相同方向但不同距离的3D点（灰色x）投影到相同的2D点，从而导致尺度歧义性。 

**尺度变换**： 相当于添加一个恒定的尺度变换 TsT_sTs到第二个相机，消除该歧义性；

 ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage8.png)

### **3. 绝对轨迹误差和相对轨迹误差** 

- 绝对轨迹误差计算的每一个点对（待评估轨迹的点与真值轨迹的点）的绝对值误差。 
- 相对轨迹误差计算的是，针对两条轨迹，分别计算第 k时刻和 k+Δ时刻的误差，然后这两个误差间再计算绝对值误差。

![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage9.png)

### **4. 补充** 

可以在相关文献获取更加详细的信息: A Tutorial on Quantitative Trajectory Evaluation for Visual(-Inertial) Odometry。 

 

## **第四部分：evo初体验**

### **1.evo_traj 体验** 

- 介绍：evo_traj 可以分析、绘制、导出一个或多个轨迹；可以打开任意多个轨迹，查看统计信息， 还可以将轨迹转换为其他格式； 

- 体验

  ```
  # evo_traj 执行，以KITTI_00_gt为参考，绘制 KITTI_00_ORB  KITTI_00_SPTAMcd evo/test/data 
  evo_traj kitti KITTI_00_ORB.txt KITTI_00_SPTAM.txt --ref=KITTI_00_gt.txt -p --plot_mode=xz 
  ```

- 出现如下的图示 

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage3.png)

  注：虚线为参考值；由于参数设置了xz，因此只显示轨迹在xz平面上投影；坐标表示活动轨迹范围； 

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage4.png)

  注： 以起始点作为基准点，针对每个pose点（横），按x/y/z三个方向的分量，描述其距离原点距离（纵）

- 本文后面章节会详细讲解 evo_traj 的每一个命令参数。

### **2.evo_ape 体验 + 绝对轨迹误差计算方法** 

- 介绍：evo_ape 可以评估轨迹绝对位姿误差(absolute pose error) 
- 绝对位姿误差常被用作比较估计轨迹和参考估计并计算整个轨迹误差的统计数据， 适用于测试轨迹的全局一致性。 

```
# 创建存放结果的目录 
mkdir results 
evo_ape kitti KITTI_00_gt.txt KITTI_00_SPTAM.txt -vas  --plot --plot_mode xz --save_results results/SPTAM_APE.zip 
```

- 出现如下图示效果

  - 绝对轨迹误差信息如下图：

    ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage10.png)

    注：针对所有位姿点描述绝对误差大小，并与均方根误差、中位值、均值、标准差等直观比较；std覆盖区域为 [mean−std, mean+std], 反应组内个体间离散程度； 

    注：默认情况下计算的是ATE(absolute trajectory error)绝对轨迹误差。终端输出 Calculating APE for translation part pose relation... 表示计算的是平移误差；可以使用 -r full 同时计算平移+旋转误差； 

  - 轨迹直观误差信息如下图

    ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage11.png)

    注： 针对整个轨迹，使用颜色显示偏差的大小，按蓝-绿-红渐变色偏差依次变大；放大后可更清楚的查看局部轨迹差； 
    本文后面章节会详细讲解 evo_ape 的每一个命令参数。 

- Umeyama算法

  Umeyama算法用于做点云匹配对齐，因为点集合之间的对应关系已知，它可以计算出两组点云数据间的旋转+平移变换矩阵和相似变换矩阵； 
  原理就是通过点对之间距离平方和的最小二乘误差计算出T，和ICP的损失函数是类似的。 

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage12.png)

  最后计算得到:

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage13.png)

  此外， Eigen 库也封装了函数 Eigen::umeyama（），可直接传入两个点云集合调用求解. 

  SE(3)与Sim(3)，对于双目SLAM和RGB-D SLAM，尺度统一，因此需要通过最小二乘计算估计位姿到真实位姿的转换矩阵 S∈SE(3); 

   

  对于单目相机，尺度不确定性，因此需要计算从估计位姿到真实位姿的相似变换矩阵 S∈Sim(3)。 默认为SE(3)，加 -s 参数使用 Sim(3); 

   

  补充说明：可以通过Umeyama的文献获取更进一步的信息： Least-Squares Estimation of Transformation Parameters Between Two Point Patterns 
  注：终端输出表示的是用 Umeyama 计算得到的相似矩阵选装、平移和尺度变换的结果。

  ```
   Rotation of alignment: 
    [[ 0.99972834 -0.01321112  0.01920198] 
     [ 0.01357949  0.99972379 -0.01918176] 
     [-0.01894327  0.0194373   0.9996316 ]] 
    Translation of alignment: 
    [1.18538132 2.10165699 2.31548455] 
    Scale correction: 1.0045265524039808 
  ```

- 绝对轨迹误差（ATE）计算公式 

  为什要计算绝对轨迹误差？ 绝对轨迹误差实际上在计算什么？ 
  对于视觉SLAM系统， 估计轨迹的全局一致性是重要度量，如何评估全局一致性？ 就是通过比较被估计值和真值轨迹之间的绝对距离来得到。 
  首先先将两条轨迹对齐。 记P1:n代表待估计的轨迹，Q1:n代表真值轨迹， 则时间戳 iii处的绝对估计误差为： 

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage14.png)

  可以这么理解公式：对 A=[R∣t]∈SE(3), 有A−1A=[I∣0]； 那么这里的 SPi 是估计的 Qi， 计算 Qi−1SPi可以直观得到两条轨迹的差值。 
  则针对所有时刻定义平移分量的 均方根误差(RMSE)、和方差（SSE)：

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage15.png)

  ![SSE : ](data:image/png;base64,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)

  补充说明：可以通过文献获取更进一步的信息： A Benchmark for the Evaluation of RGB-D SLAM Systems 

### **3.evo_rpe 体验 + 相对轨迹误差计算方法** 

- 介绍： evo_rpe 可以用来计算相对轨迹误差（relative pose error） 

  ```
  evo_rpe kitti KITTI_00_gt.txt KITTI_00_SPTAM.txt  -r full -va --plot --plot_mode xyz --save_results results/SPTAM_RPE.zip 
  ```

  - -r full 指定对平移和旋转的误差均进行计算。 
  - -as 采用SE(3) Umeyama对齐，处理平移和旋转和尺度 

- 终端输出的轨迹对齐的旋转矩阵和平移矩阵， 以及统计信息

  ```
  -------------------------------------------------------------------------------- 
    Aligning using Umeyama's method... (with scale correction) 
    Rotation of alignment: 
    [[ 0.99972834 -0.01321112  0.01920198] 
     [ 0.01357949  0.99972379 -0.01918176] 
     [-0.01894327  0.0194373   0.9996316 ]] 
    Translation of alignment: 
    [1.18538132 2.10165699 2.31548455] 
    Scale correction: 1.0045265524039808 
    -------------------------------------------------------------------------------- 
    Found 4540 pairs with delta 1 (frames) among 4541 poses using consecutive pairs. 
    Compared 4540 relative pose pairs, delta = 1 (frames) with consecutive pairs. 
    Calculating RPE for full transformation pose relation... 
    -------------------------------------------------------------------------------- 
    RPE w.r.t. full transformation (unit-less) 
    for delta = 1 (frames) using consecutive pairs 
    (with Sim(3) Umeyama alignment) 
   
          max      1.136092 
          mean      0.024773 
          median      0.020434 
          min      0.001043 
          rmse      0.035773 
          sse      5.809960 
          std      0.025807 
    -------------------------------------------------------------------------------- 
  ```

- **相对位姿误差(RPE) 计算公式**

  为什要计算相对轨迹误差？ 相对轨迹误差实际上在计算什么？ 
  相对位姿误差测量了轨迹在一个固定的时间区间 Δ\DeltaΔ内的局部准确度。 因此，相对位姿误差对应轨迹的漂移。 
  先定义时间步 iii处的相对位姿误差如下，可知相对位姿误差计算的是相隔固定时间差 Δ 两帧位姿差：

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage16.png)

  根据上式，对于一个有n个相机位姿的序列中，我们获得 m=n−Δm=n-\Deltam=n−Δ个独立的沿着序列的相对位姿误差。则可以定义平移分量的的所有时刻的均方根误差RMSE: 

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage17.png)

  可以这么理解公式：对 A=[R∣t]∈SE(3), 有 A−1A=[I∣0]. 
  上式中的 (Pi−1Pi+Δ)(P_i^{-1}P_{i+\Delta}) (Pi−1Pi+Δ)是估计的 Qi−1Qi+ΔQ_i^{-1}Q_{i+\Delta}Qi−1Qi+Δ，计算 (Qi−1Qi+Δ)−1(Pi−1Pi+Δ)(Q_i^{-1}Q_{i+\Delta})^{-1} (P_i^{-1}P_{i+\Delta})(Qi−1Qi+Δ)−1(Pi−1Pi+Δ)可以直观的得到两条轨迹的差值； 
  此处计算的是 EiE_iEi的平移分量，旋转误差也可以被估计，但一般平移误差就足够了（因为旋转误差会随着平移误差的上升而增加）。时间参数 Δ\DeltaΔ对于帧速较快的相同，如30Hz的传感器，可选择 Δ=30\Delta=30Δ=30以得到每秒的漂移。 
  补充说明：可以通过文献获取更进一步的信息： A Benchmark for the Evaluation of RGB-D SLAM Systems 

### **4.evo_res 体验** 

- 介绍： evo_res 可以用来比较多个结果文件， 打印统计信息、绘图、保存结果到表格等。 

  ```
  evo_res results/*.zip -p --save_table results/table.csv 
  ```

- 如下图示 

  绝对轨迹误差对比：

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage18.png)

## **第五部分： evo 命令参数详解** 

### **1.evo_ape 命令详解** 

- 用途：计算绝对位姿误差 

- 绝对位姿误差常被用作比较估计轨迹和参考估计并计算整个轨迹误差的统计数据， 适用于测试轨迹的全局一致性。 

- 命令语法： evo_ape 数据格式 参考轨迹 估计轨迹 可选项 

  - 数据格式： euroc, tum, kitti 等 

- 常用命令示例：

  ```
  evo_ape euroc MH_data3.csv pose_graphloop.txt -r full -va --plot --plot_mode xyz --save_plot ./VINSplot --save_results ./VINS.zip 
  ```

  命令含义： 考虑平移和旋转部分误差的ape， 进行平移和旋转对齐，以详细模式显示，画图并保存计算结果。

- 参数说明： 

  - -r : 即 -pose_relation， 此参数可选, 若不添加此参数，则默认为 trans_part。 有如下可选项： 

  | **可选项** | **含义**                                                |
  | ---------- | ------------------------------------------------------- |
  | full       | 表示同时考虑旋转和平移误差得到的ape,无单位（unit-less） |
  | trans_part | 考虑平移部分得到的ape，单位为m                          |
  | rot_part   | 考虑旋转部分得到的ape，无单位（unit-less）              |
  | angle_deg  | 考虑旋转角得到的ape,单位°（deg）                        |
  | angle_rad  | 考虑旋转角得到的ape,单位弧度（rad）                     |

  - -v : 表示 verbose mode， 详细模式 

  - -a ：即 -align, 表示采用 SE(3) Umeyama 对齐。 除了 -a 外，其他可选项如下 

  | **命令**                   | **含义**                                       |
  | -------------------------- | ---------------------------------------------- |
  | -a/–align                  | 采用SE(3) Umeyama对齐，只处理平移和旋转        |
  | -as/–align --correct_scale | 采用Sim(3) Umeyama对齐，同时处理平移旋转和尺度 |
  | -s/–correct_scale          | 仅对齐尺度                                     |

  - -plot ： 表示画图 
    - --plot_mode : 选择画图模式， 二维图或三维图，默认为 xyz， 可选项有[xy, xz, yz, zx, zy, xyz]. 
    - --save_plot : 后跟保存图像的文件路径， 如 ./VINSplot. 保存文件的类型， 可以通过 evo_config 命令设置， 常见的可以保存为 png, pdf等  
  - -save_results : 后跟存储结果的压缩文件路径， 如 ./VINS.zip， 是一个压缩文件。 

  - --help: 显示帮助信息， 格式为： evo_ape 格式 --help , 如 evo_ape euroc --help 

### **2.evo_rpe 命令详解** 

- 用途：计算相对位子误差 

- 相对位姿误差不进行绝对位姿的比较，相对位姿误差比较运动（姿态增量）。相对位姿误差可以给出局部精度，例如slam系统每米的平移或者旋转漂移量。 

- 命令语法： evo_ape 数据格式 参考轨迹 估计轨迹 可选项 

  - 数据格式： euroc, tum, kitti 等 

- 常用命令示例： 

  ```
  evo_rpe euroc MH_data3.csv pose_graphloop.txt -r angle_deg \ 
    --delta 1 --delta_unit m -va --plot --plot_mode xyz \ 
    --save_plot ./VINSplot --save_results ./VINS.zip 
  ```

  命令含义： 求每米的旋转角的rpe，以详细模式显示，画图并保存计算结果。

- 参数说明： 
  - -r : 即 -pose_relation， 此参数可选, 若不添加此参数，则默认为 trans_part。具体参数选项内容 同 evo_ape，具体项可参见前一章节说明.
  - -d/--delta : 表示相对位姿之间的增量，后跟数值，默认为1， 然后通过 -u/--delta_unit 指定单位； 
  - -u/--delta_unit : 表示增量的单位，可选参数为 f, d, r, m 分别表示 frames, deg, rad, meters 。默认为f。 当此参数为 f 时，则 -d/--delta必须为整型， 其余情况可谓浮点型；-d/--delta 和 -u/--delta_unit 联合起来表示衡量局部精度的单位，如 每米、每弧度、每百米等。  
  - -v --plot --plot_mode xyz --save_results results/VINS.zip --save_plot ： 这些参数同 evo_ape， 具体可参见前一章节说明。 

### **3.evo_traj 命令详解** 

- 用途： 轨迹管理工具 

- 可以打开任意多个轨迹，查看统计信息， 还可以将轨迹转换为其他格式 

- 常用命令示例1： 

  ```
  evo_traj euroc MH_data1.csv MH_data3.csv -v --full_check 
  ```

- 参数说明： 
  - -v : 以详细模式显示 
  - --full_check : 对轨迹进行检查 

- 常用命令示例2：对轨迹进行对齐， 此时需要利用 --ref 指定参考轨迹 

  ```
  evo_traj bag ROS_example.bag ORB-SLAM S-PTAM --ref groundtruth -s 
  ```

  

- 参数说明：  
  - -a ：即 -align, 轨迹对齐的选项参数， 如 -a/--align, -s/--correct_scale, --n_to_align 等等，与evo_ape 相同，详情可参见前一章节 evo_ape 中轨迹说明中的参数解释。 

- 常用命令示例3： 转换格式 

  ```
  evo_traj euroc data.csv --save_as_tum  
  ```

- 参数说明： 
  - --save_as_tum : 指定目标数据集格式为 tum 

- 数据集格式转换表如下： 

| **源数据集** | **ROS Bag**       | **KITTI**       | **TUM**           |
| ------------ | ----------------- | --------------- | ----------------- |
|              | --save_as_bag     | --save_as_kitti | --ave_as_tum      |
| bag          | yes               | yes             | yes               |
| euroc        | yes               | yes             | yes               |
| kitti        | no(no timestamps) | yes             | no(no timestamps) |
| tum          | yes               | yes             | yes               |

### **4.轨迹的对齐和尺度缩放** 

单目相机会存在尺度的不确定性，evo_traj 支持使用-s（或 --correct_scale）参数进行Sim(3)上的对齐（旋转、平移与尺度缩放）。 
例子1： 下图从左到右三幅图中两条曲线的结果分别是：未对齐、SE(3)对齐、尺度缩放 

![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage19.png)

例子2： 不同的对齐命令效果图，分别是未对齐、SE(3)对齐、Sim(3)对齐、尺度缩放 

![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage20.png)

### **5.evo_res 命令详解** 

- 用途：结果比较 

- evo_ape/evo_rpe中将结果保存为.zip文件后，可以利用evo_res对不同的结果进行比较 

- 常用命令示例1： evo_ape得到MH3_1.zip和MH3_2.zip两个文件后，对这两个结果进行比较 

  ```
  evo_res MH3.zip MH3_2.zip -v 
  ```

- 参数说明： 
  - -v : 详细模式展示信息 

### **6.evo_config 命令详解** 

- 用途：全局设置和配置文件的操作 

| **命令**                                   | **说明**                 |
| ------------------------------------------ | ------------------------ |
| evo_config show                            | 查看设计文件中的参数配置 |
| evo_config set                             | 进行参数设置             |
| evo_config generate                        | 导出配置到指定的json文件 |
| evo_config reset                           | 将参数还原到默认值       |
| evo_config show\|set\|generate\|reset help | 将参数还原到默认值       |

- evo_config set 命令最为常用，下面是几个常用的参数，其含义以及可选项 

| **参数**                 | **含义**               | **可选项**                    |
| ------------------------ | ---------------------- | ----------------------------- |
| plot_export_format       | 输出图像时图像存储格式 | 常用png,pdf等                 |
| plot_linewidth           | 作图时线的宽度         | matplotlib支持的宽度，默认1.5 |
| plot_reference_color     | 图像中参考轨迹的颜色   | black,red,green等             |
| plot_reference_linestyle | 参考轨迹的线型         | matplotlib支持的线型，默认–   |
| plot_seaborn_style       | 图像背景和网格         | whitegrid,darkgrid,white,dark |
| plot_split               | 是否分开显示/存储图像  | false/true                    |
| plot_figsize             | 画图的图像大小         | 默认宽高均为6，可使用其他值   |
| table_export_format      | 表格数据输出格式       | 常用 csv,excel,latex,json     |

- 命令示例 

  ```
  # 将画图背景更改成白色网格 
  evo_config set plot_seaborn_style whitegrid  
   
  # 将字体改为衬线型并调为1.2倍大小 
  evo_config set plot_fontfamily serif plot_fontscale 1.2  
   
  # 将画图所使用的线型改为  
  evo_config set plot_reference_linestyle -  
   
  # 将所画图的图像大小调整为10 9（宽 高） 
  evo_config set plot_figsize 10 9  
   
  # 将参数还原到默认值 
  evo_config reset       
   
  # 导出配置 
  evo_config generate --pose_relation angle_deg --delta 1 --delta_unit m --verbose --plot --out rpe_config.json 
   
  # 导入配置 
  evo_rpe euroc MH_data3.csv pose_graphloop.txt -c rpe_config.json
  ```

### **7.evo 其他常用命令** 

| **命令**              | **含义**                |
| --------------------- | ----------------------- |
| evo pkg   --version   | 查看evo版本             |
| evo pkg   --info      | 查看evo的简要介绍和描述 |
| evo pkg   --pyversion | 查看python版本          |
| evo pkg   --license   | 查看许可证              |
| evo pkg   --location  | 查看evo安装路径         |
| evo pkg   --logfile   | 查看日志文件路径        |
| evo pkg   --open_log  | 打开日志文件            |
| evo pkg   --clear_log | 清除日志文件            |

## **第六部分： 在程序中调用 evo 函数**

除了在命令行中使用外， 还可以在程序中使用 evo 能力。 

### **例子1：轨迹对齐** 

调用 evo 函数实现不同对齐方式的轨迹结果绘图并比较 

alignment_demo.py: 

```
#!/usr/bin/env python 

""" 
trajectory alignment functions 
""" 

import copy 
import logging 
import sys 

import evo.core.lie_algebra as lie 
from evo.core import trajectory 
from evo.tools import plot, file_interface, log 

import numpy as np 
import matplotlib.pyplot as plt 

logger = logging.getLogger("evo") 
log.configure_logging(verbose=True) 

# 读取参考轨迹数据和待评估轨迹数据 
traj_ref = file_interface.read_kitti_poses_file("./data/KITTI_00_gt.txt") 
traj_est = file_interface.read_kitti_poses_file( "./data/KITTI_00_ORB.txt") 

# add artificial Sim(3) transformation 
traj_est.transform(lie.se3(np.eye(3), np.array([0, 0, 0]))) 

# 尺寸缩放 
traj_est.scale(0.5) 

logger.info("\nUmeyama alignment without scaling") 
traj_est_aligned = copy.deepcopy(traj_est) 

# 对齐轨迹（但不做尺寸缩放） 
traj_est_aligned.align(traj_ref) 

logger.info("\nUmeyama alignment with scaling") 
traj_est_aligned_scaled = copy.deepcopy(traj_est) 
# 对齐轨迹（同时尺寸缩放） 
traj_est_aligned_scaled.align(traj_ref, correct_scale=True) 

logger.info("\nUmeyama alignment with scaling only") 
traj_est_aligned_only_scaled = copy.deepcopy(traj_est) 
# 对齐轨迹（仅仅尺寸缩放） 
traj_est_aligned_only_scaled.align(traj_ref, correct_only_scale=True) 

# 以下部分为绘图部分 
fig = plt.figure(figsize=(8, 8)) 
plot_mode = plot.PlotMode.xz 

ax = plot.prepare_axis(fig, plot_mode, subplot_arg=221) 
plot.traj(ax, plot_mode, traj_ref, '--', 'gray') 
plot.traj(ax, plot_mode, traj_est, '-', 'blue') 
fig.axes.append(ax) 
plt.title('not aligned') 

ax = plot.prepare_axis(fig, plot_mode, subplot_arg=222) 
plot.traj(ax, plot_mode, traj_ref, '--', 'gray') 
plot.traj(ax, plot_mode, traj_est_aligned, '-', 'blue') 
fig.axes.append(ax) 
plt.title('$\mathrm{SE}(3)$ alignment') 

ax = plot.prepare_axis(fig, plot_mode, subplot_arg=223) 
plot.traj(ax, plot_mode, traj_ref, '--', 'gray') 
plot.traj(ax, plot_mode, traj_est_aligned_scaled, '-', 'blue') 
fig.axes.append(ax) 
plt.title('$\mathrm{Sim}(3)$ alignment') 

ax = plot.prepare_axis(fig, plot_mode, subplot_arg=224) 
plot.traj(ax, plot_mode, traj_ref, '--', 'gray') 
plot.traj(ax, plot_mode, traj_est_aligned_only_scaled, '-', 'blue') 
fig.axes.append(ax) 
plt.title('only scaled') 

fig.tight_layout() 
plt.show() 
```

执行后如图所示：

![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage21.png)

### **例子2：计算APE** 

调用 evo 函数计算APE和统计值MEAN 

calc_ape.py: 

```
#!/usr/bin/env python 

""" 
caculate ape of two trajectories 
""" 

print("loading required evo modules") 
from evo.core import trajectory, sync, metrics 
from evo.tools import file_interface 

# 读取参考轨迹数据和待评估轨迹数据 
print("loading trajectories") 
traj_ref = file_interface.read_tum_trajectory_file( "./data/fr2_desk_groundtruth.txt") 
traj_est = file_interface.read_tum_trajectory_file( "./data/fr2_desk_ORB.txt") 

# 调用时间同步算法进行同步，主要是根据 timestamps进行同步 
# 具体实现参考 evo/core/syn.py 源码 
print("registering and aligning trajectories") 
traj_ref, traj_est = sync.associate_trajectories(traj_ref, traj_est) 

# 对齐轨迹（不带尺度缩放） 
traj_est.align(traj_ref, correct_scale=False) 

# 计算APE 
print("calculating APE") 
data = (traj_ref, traj_est)     

# 仅针对平移部分计算 
ape_metric = metrics.APE(metrics.PoseRelation.translation_part) 

# 对SE(3)的位姿点集合计算APE 
# 详细实现参见 evo/core/metrics.py 源码 
ape_metric.process_data(data) 

# 得到rmse，sse，mean，median,max,min,std等统计值 
ape_statistics = ape_metric.get_all_statistics() 
print("mean:", ape_statistics["mean"]) 

# 以下为绘图部分 
print("loading plot modules") 
from evo.tools import plot 
import matplotlib.pyplot as plt  

print("plotting") 
plot_collection = plot.PlotCollection("Example") 
# metric values 
fig_1 = plt.figure(figsize=(8, 8)) 
plot.error_array(fig_1.gca(), ape_metric.error, statistics=ape_statistics, 
                                 name="APE", title=str(ape_metric)) 
plot_collection.add_figure("raw", fig_1) 

 
# trajectory colormapped with error 
fig_2 = plt.figure(figsize=(8, 8)) 
plot_mode = plot.PlotMode.xy 
ax = plot.prepare_axis(fig_2, plot_mode) 
plot.traj(ax, plot_mode, traj_ref, '--', 'gray', 'reference') 
plot.traj_colormap(ax, traj_est, ape_metric.error, plot_mode, 
                                   min_map=ape_statistics["min"], 
                                   max_map=ape_statistics["max"], 
                                   title="APE mapped onto trajectory") 
plot_collection.add_figure("traj (error)", fig_2) 

# trajectory colormapped with speed 
fig_3 = plt.figure(figsize=(8, 8)) 
plot_mode = plot.PlotMode.xy 
ax = plot.prepare_axis(fig_3, plot_mode) 
speeds = [ 
        trajectory.calc_speed(traj_est.positions_xyz[i], 
                                traj_est.positions_xyz[i + 1], 
                                traj_est.timestamps[i], traj_est.timestamps[i + 1]) 
        for i in range(len(traj_est.positions_xyz) - 1) 
] 
speeds.append(0) 
plot.traj(ax, plot_mode, traj_ref, '--', 'gray', 'reference') 
plot.traj_colormap(ax, traj_est, speeds, plot_mode, min_map=min(speeds), 
                        max_map=max(speeds), title="speed mapped onto trajectory") 
fig_3.axes.append(ax) 
plot_collection.add_figure("traj (speed)", fig_3) 
 
plot_collection.show()  
```

终端输出信息： 

```
loading required evo modules 
loading trajectories 
registering and aligning trajectories 
calculating APE 
mean: 0.0074917768702161495 
```

计算并打印了MEAN信息 

## **附录：数据集以及 evo 使用例子** 

### **TUM数据集** 

- 数据格式为 ： timestamp tx ty tz qx qy qz qw 每行8个元素， 结尾没有空格， 时间戳以秒为单位， 精确到小数点后9位 

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage22.png)

- evo_ape 计算轨迹绝对误差的例子

  ```
    mkdir results 
  
    # 计算轨迹绝对误差 evo_ape 
    evo_ape tum fr2_desk_groundtruth.txt  fr2_desk_ORB.txt -va --plot --plot_mode xz --save_results results/ORB_fr2_desk.zi 
  
   # 分析多条曲线 evo_res 
   evo_res results/*.zip -p --save_table results/table.csv 
  
   # 绘制多条轨迹 evo_traj
   evo_traj tum freiburg1_xyz-ORB_kf_mono.txt freiburg1_xyz-rgbdslam.txt  --ref=freiburg1_xyz-groundtruth.txt -va --plot --plot_mode xy 
  ```

### **KITTI数据集** 

- KITTI数据集格式： r11 r12 r13 tx r21 r22 r23 ty r31 r32 r33 tz 存储变换矩阵的前三行,每行12元素，空格隔开, 无时间戳 

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage23.png)

- evo_ape 计算轨迹绝对误差, evo_traj 绘制多条曲线轨迹 

  ```
   mkdir results 
  
   # 计算轨迹绝对误差 evo_ape 
   evo_ape kitti KITTI_00_gt.txt KITTI_00_ORB.txt -va --plot --plot_mode xz --save_results results/KITTI_00_ORB.zip 
  
   # 分析多条曲线 evo_res 
   evo_res results/*.zip -p --save_table results/table.csv 
  
   # 绘制多条轨迹 
   cd test/data 
   evo_traj kitti KITTI_00_ORB.txt KITTI_00_SPTAM.txt --ref=KITTI_00_gt.txt -p --plot_mode=xz  
  ```

### **EUROC数据集** 

- EUROC数据格式为：timestamp,px,py,pz,qw,qx,qy,qz,vx,vy,vz,bwx,bwy,bwz,bax,bay,baz 每行17个元素，逗号隔开，时间以纳秒为单位, 无小数

  ![](/home/hanlin/git-repository/slam-study-note/slam_config/media/GetImage24.png)

- evo_ape 计算轨迹绝对误差 

  ```
  mkdir results 
  
   # 计算轨迹绝对误差 evo_ape 
   evo_ape euroc V102_groundtruth.csv V102.txt -va --plot --plot_mode xy --save_results results/EUROC.zip 
  ```

## **附录：相关文献** 

- Z. Zhang, D. Scaramuzza. A Tutorial on Quantitative Trajectory Evaluation for Visual(-Inertial) Odometry. *IEEE/RSJ International Conference on Intelligent Robots and Systems*, 7244-7251, 2018. 基本概念介绍教程，包括尺度、对齐等等 
- J. Sturm, N. Engelhard, F. Endres, W. Burgard, D. Cremers. A Benchmark for the Evaluation of RGB-D SLAM Systems. *IEEE International Conference on Robotics and Automation*, 573-580, 2012. 讲解绝对轨迹误差和相对轨迹误差的概念，以及计算 
- Least-Squares Estimation of Transformation Parameters Between Two Point Patterns, 讲解Umeyama轨迹对齐算法原理和最小二乘过程 

### [参考教程](https://juejin.cn/post/7063041669725159461)

