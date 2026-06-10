# Ubuntu-ORIN

## 1. 查看NVIDIA jetson相关参数

```shell
sudo apt update
sudo apt install nvidia-jetpack #这里会安装cuda相关驱动和包管理器
sudo jtop
```

查看jetpack参数

```shell
sudo jetson_release
```

## 2. 更改xavier风扇转速

```shell
sudo gedit /sys/devices/pwm-fan/target_pwm
```



## 3. torch相关安装

### 3.1 JetPack

需要烧录对应的版本

https://developer.nvidia.com/embedded/jetpack

并安装

```shell
sudo apt install nvidia-jetpack #这里会安装cuda相关驱动和包管理器
```

### 3.2 cuSPARSELt 

如果安装24.06 PyTorch或更高版本，首先需要安装[cusparslet](https://docs.nvidia.com/cuda/cusparselt/index.html)：

https://docs.nvidia.com/cuda/cusparselt/index.html

### 3.3 cuDSS

```
https://developer.nvidia.com/cudss-downloads?target_os=Linux&target_arch=aarch64-jetson&Compilation=Native&Distribution=Ubuntu&target_version=22.04&target_type=deb_network
```

### 3.1 torch, torchvision, xformers

```
pip install torch==2.9.1 torchvision==0.24.1 \
  --index-url https://pypi.jetson-ai-lab.io/jp6/cu126

pip install xformers \
  --index-url https://pypi.jetson-ai-lab.io/jp6/cu126
```

或者手动安装

首先确认系统兼容性[**PYT_VERSION**](https://docs.nvidia.com/deeplearning/frameworks/install-pytorch-jetson-platform-release-notes/pytorch-jetson-rel.html#pytorch-jetson-rel)，不能直接安装arm版本，依照jetson设备的jetpack和CUDA版本选择torch、torchvision下载

https://developer.nvidia.com/embedded/downloads#?search=torch

```shell
pip install torch-2.5.0a0+872d972e41.nv24.08.17622132-cp310-cp310-linux_aarch64.whl
```

torchvison提供的jetson版本比较少，建议从[源码](https://github.com/pytorch/vision/releases)安装，确认好版本下载对应tag

```shell
torch 2.7.0 对应 torchvision 0.22.0

torch 2.6.0 对应 torchvision 0.21.0

torch 2.5.1 对应 torchvision 0.20.1

torch 2.5.0 对应 torchvision 0.20.0

torch 2.4.1 对应 torchvision 0.19.1

torch 2.4.0 对应 torchvision 0.19.0

torch 2.3.1 对应 torchvision 0.18.1

torch 2.3.0 对应 torchvision 0.18.0

torch 2.2.2 对应 torchvision 0.17.2

torch 2.1.0 对应 torchvision 0.16.0

torch 2.0.1 对应 torchvision 0.15.2

torch 1.13.1 对应 torchvision 0.14.1

torch 1.12.1 对应 torchvision 0.13.1

torch 1.11.0 对应 torchvision 0.12.3

torch 1.10.0 对应 torchvision 0.11.1
```

```shell
python setup.py install
```

最终验证我们的torch、torchvison和TensorRT是否安装成功

```python
import torch
import torchvision
# import tensorrt
print("Torch_version:", torch.__version__)
print("Torchvision_version:", torchvision.__version__)
# print("TensorRT_version::", tensorrt.__version__)
print('CUDA available: ' + str(torch.cuda.is_available()))
print('cuDNN version: ' + str(torch.backends.cudnn.version()))
```

## 4. 开放最大性能

```
sudo nvpmodel -m 0
sudo jetson_clocks
```

