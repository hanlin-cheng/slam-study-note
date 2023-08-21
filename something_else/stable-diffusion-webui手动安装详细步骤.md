# stable-diffusion-webui手动安装详细步骤

## 环境

OS：Windows10
显卡类型：NVIDIA（英伟达）
[Python版本](https://www.python.org/downloads/?login=from_csdn)：3.10.6（必须为该版本）
[Git](https://git-scm.com/?login=from_csdn)：必须

## 安装过程

### 升级CUDA驱动

**python3.10目前是不支持cuda10.2版本的torch包的,可以通过 [CUDA版本对应的torch](https://download.pytorch.org/whl/torch_stable.html?login=from_csdn)去查看，所以先要升级CUDA驱动，如果是更高版本的驱动可以跳过该步骤。**

需要根据自己显卡类型去下载相应的驱动，win + x ->设备管理器 ->显示适配器

![在这里插入图片描述](media/5ef388c5c9214b6c8fab73fbc87d0071.png)

然后去 [CUDA驱动](https://www.nvidia.com/Download/Find.aspx?lang=en-us&login=from_csdn) 填写对应的显卡信息后搜索显卡驱动

![在这里插入图片描述](media/f3bdcd5cb41b42a6bc70d01e7f62be57.png)

这里选择第一个驱动进行下载，下载之后是一个exe文件，直接按照步骤安装即可，过程比较简单不再赘述

![在这里插入图片描述](media/10e8e9317eca4b14b8682eb30fcbfd6c.png)

使用命令`nvidia-smi`查看，可以看到CUDA版本是11.4，相应的驱动版本是472.84

```shell
PS C:\Users\samsung> nvidia-smi
Sat Apr  1 14:02:38 2023
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 472.84       Driver Version: 472.84       CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ... WDDM  | 00000000:01:00.0 Off |                  N/A |
| N/A    0C    P8    N/A /  N/A |     75MiB /  4096MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A     14052    C+G   ...IA GeForce Experience.exe    N/A      |
+-----------------------------------------------------------------------------+

```

### 安装CUDAToolKit

如果原来装过CUDA ToolKit请先卸载

[CUDA ToolKit](https://developer.nvidia.cn/cuda-toolkit-archive?login=from_csdn)
找到CUDA驱动版本对应的CUDA ToolKit版本下载，由于笔者这里的CUDA驱动版本是472.84，所以CUDA ToolKit版本选择11.4.4，[CUDA驱动版本与CUDA ToolKit对应关系](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#major-components?login=from_csdn)

![在这里插入图片描述](media/aed0d72bbc3449959f6423e074167458.png)

![在这里插入图片描述](media/46ce665f69594feeae6a97b5bd605acc.png)

下载好之后是一个exe文件，`cuda_11.4.4_472.50_windows.exe`，可以看到名字中含有可以使用该CUDA ToolKit的最小CUDA驱动为472.50。安装很简单，这里不再赘述。

最终查看CUDA的实际版本，`nvcc --version`

```
PS C:\Users\samsung> nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2021 NVIDIA Corporation
Built on Mon_Oct_11_22:11:21_Pacific_Daylight_Time_2021
Cuda compilation tools, release 11.4, V11.4.152
Build cuda_11.4.r11.4/compiler.30521435_0
```

### 安装cuDNN

[cuDNN版本](https://developer.nvidia.cn/rdp/cudnn-archive?login=from_csdn)
cuDNN是一个神经网络学习的加速库，因为`stable-diffusion-webui`中根据一些模型进行文本生图时可以用该包加快渲染速度。找到与CUDA版本一直的cuDNN下载

![在这里插入图片描述](media/c58d9bc86cac4a9f8b993aefaebe4151.png)

下载后将该压缩包解压，将里面的全部文件复制到CUDA安装的目录下，路劲为：
C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.4
![在这里插入图片描述](media/b871c543fa5142f08deee9b5fdb5bdf5.png)
注：可以看到虽然有同名文件夹，但是没有同名文件，所以cuDNN的文件在复制到CUDA中时是不会提示要覆盖的情况，如果有，那么一定是哪里出错了

### 克隆项目地址

[stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui?login=from_csdn)
![在这里插入图片描述](media/ad0e5659850d4708b89e93f9340c9e9e.png)

### 安装Pytorch

这里使用Anaconda安装了一个python3.10.6的虚拟环境，当然大家也可以不使用Anaconda，直接下载安装python3.10.6也是可以的

[pytorch](https://pytorch.org/?login=from_csdn)
需要安装CUDA版本对应的Pytorch版本，如果不是使用Annconda，请使用pip install指令下载pytorch
![在这里插入图片描述](media/180ccb58ffd741a589b0b44079f2c589.png)
笔者这里本应该安装CUDA11.4对应的pytorch，但是pytorch官网中目前并没有提供该版本，于是安装了CUDA11.3对应的pytorch版本，该版本可以在CUDA11.4中使用

```shell
# 创建一个python3.10.6的虚拟环境，并命名为 sdui
(base) C:\Users\samsung>conda create -n sdui python==3.10.6

...

#激活创建的虚拟环境 sdui
(base) C:\Users\samsung>conda activate sdui
# 安装pytorch，等待时间取决于网速
(sdui) C:\Users\samsung>conda install pytorch torchvision torchaudio pytorch-cuda=11.3 -c pytorch -c nvidia

#测试torch是否安装成功
(sdui) C:\Users\samsung>python
Python 3.10.6 | packaged by conda-forge | (main, Oct 24 2022, 16:02:16) [MSC v.1916 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> torch.cuda.is_available()
# true代表成功使用torch
True
```

### 部署启动项目

**注意：项目README中提示我们去使用`webui-user.bat`一键部署项目，但是双击 `webui-user.bat`运行后会在项目根目录下生成一个虚拟的python环境（其实是拷贝了系统已经安装的python版本到webui的指定目录），即`\venv\Scripts\python.exe`，后续安装的python依赖包都是需要通过该虚拟python环境中的python命令去下载的，而不是部署在我们通过Anaconda安装的虚拟python环境中。并且为了虚拟环境管理的便捷性，这里我们选择在conda虚拟环境下进行部署。**

**进入到根目录下的modules文件夹，然后编辑launch_utils.py**

大多数安装时过程中会出现反复报错的情况，其实大多数的错误是由于在安装过程中会去github拉去依赖包时超时而导致的，本质原因是不能顺利的访问github，于是可以给在需要去github拉去依赖的代码处加上代理地址：**https://ghproxy.com/**

![image-20230821183317880](media/image-20230821183317880.png)

如果git不下来也可以手动下载git仓库添加到项目根目录下repositories`文件夹

#### 添加模型—没有模型也没有办法使用

项目模型地址:

```
D:\项目文件夹\models\Stable-diffusion\模型文件
```

这里默认使用官方给的默认模型

[模型地址](https://huggingface.co/CompVis/stable-diffusion-v-1-4-original/resolve/main/sd-v1-4.ckpt)

sd-v1-4 .ckpt 这个模型大小接近 4G大小 如果您网速不好，那么请耐心等待。

#### 接下来使用命令进行自动安装

没有使用conda虚拟环境的可以使用`webui-user.bat`进行安装运行，使用conda虚拟环境的则直接运行launch文件

```shell
python launch.py
```

耐性等待项目安装结束即可。

#### 启动项目

为了使用gpu加速效果，我们使用下面的命令启动UI界面--xformers

```
python launch.py --share --xformers --enable-insecure-extension-access --theme dark
```

使用`webui-user.bat`进行启动的在打开启动文件修改`set COMMANDLINE_ARGS=`为`set COMMANDLINE_ARGS=--xformers`即可。

当看到命令行中出现了

```
http://localhost:8075之类的路径则代表安装启动成功了，端口不一定时8075也有可能是别的  
直接复制路径到浏览器中就可以了
```

![image-20230821184834825](media/image-20230821184834825.png)

### 参考

https://devpress.csdn.net/hangzhou/64c2430a9a15ac690d7c69c0.html?dp_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6NDgzNzczLCJleHAiOjE2OTMwMzQ0MDEsImlhdCI6MTY5MjQyOTYwMSwidXNlcm5hbWUiOiJ3ZWl4aW5fNDM0MjkwNjYifQ.12-rJknE-9hDsR_MuDVKcLd_LptPsteUUBB2msHpWJ4