# NVIDIA Jetson Xavier / Orin 环境配置

本文记录 Jetson 设备信息查询、Miniconda、PyTorch 及性能模式配置。示例中的 PyTorch 源面向 JetPack 6 / CUDA 12.6；安装前请根据实际 JetPack、CUDA 和 Python 版本选择对应软件包，不要直接照搬版本号。

## 1. 查看 Jetson 相关信息

安装 JetPack 组件：

```bash
sudo apt update
sudo apt install nvidia-jetpack
```

`jtop` 和 `jetson_release` 由第三方项目 [jetson-stats](https://github.com/rbonghi/jetson_stats) 提供：

```bash
sudo apt install python3-pip python3-setuptools
sudo python3 -m pip install --upgrade jetson-stats
```

安装后重新登录或重启设备，再执行：

```bash
jtop
jetson_release
```

## 2. 调整风扇转速

不同 Jetson 型号和 JetPack 版本的风扇控制路径可能不同。Xavier 上可先检查：

```bash
test -e /sys/devices/pwm-fan/target_pwm && cat /sys/devices/pwm-fan/target_pwm
```

确认路径存在后，可将 PWM 设置为最大值 `255`：

```bash
echo 255 | sudo tee /sys/devices/pwm-fan/target_pwm
```

## 3. 安装 Miniconda

下载并安装 AArch64 版本：

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh
bash Miniconda3-latest-Linux-aarch64.sh
source ~/.bashrc
```

关闭 base 环境自动激活：

```bash
conda config --set auto_activate_base false
```

不建议让 root 直接复用普通用户的 Conda 环境，也不要把硬编码的用户目录写入 `/root/.bashrc`。必须以 root 身份使用 Conda 时，应为 root 单独安装并初始化。

## 4. 安装 PyTorch 相关组件

### 4.1 JetPack

先在 [JetPack SDK 页面](https://developer.nvidia.com/embedded/jetpack)确认设备支持的版本并完成刷机，再安装对应组件：

```bash
sudo apt update
sudo apt install nvidia-jetpack
```

### 4.2 cuSPARSELt

安装 NVIDIA 24.06 或更高版本的 PyTorch 软件包时，需要先根据 [cuSPARSELt 文档](https://docs.nvidia.com/cuda/cusparselt/index.html)安装与 CUDA、系统架构匹配的版本。

### 4.3 cuDSS

如果导入 PyTorch 或相关组件时提示缺少 `libcudss.so`，请从 [cuDSS 下载页面](https://developer.nvidia.com/cudss-downloads?target_os=Linux&target_arch=aarch64-jetson&Compilation=Native&Distribution=Ubuntu&target_version=22.04&target_type=deb_network)安装与 JetPack/CUDA 匹配的软件包。cuDSS 并不是所有 PyTorch 安装方式的通用前置依赖，应以实际报错和软件包说明为准。

### 4.4 PyTorch、torchvision 和 xformers

先安装 PyTorch wheel 常用的系统依赖：

```bash
sudo apt update
sudo apt install python3-pip libopenblas-dev
```

以下示例使用 Jetson AI Lab 的 JetPack 6 / CUDA 12.6 软件源：

```bash
python -m pip install torch==2.9.1 torchvision==0.24.1 \
  --index-url https://pypi.jetson-ai-lab.io/jp6/cu126

python -m pip install xformers \
  --index-url https://pypi.jetson-ai-lab.io/jp6/cu126
```

手动安装前，请先查阅 NVIDIA 的 [PyTorch for Jetson 发布说明](https://docs.nvidia.com/deeplearning/frameworks/install-pytorch-jetson-platform-release-notes/pytorch-jetson-rel.html#pytorch-jetson-rel)和 [Jetson 下载中心](https://developer.nvidia.com/embedded/downloads#?search=torch)，确保 wheel 的 Python ABI、JetPack 和 CUDA 版本均与系统一致：

```bash
python -m pip install ./torch-2.5.0a0+872d972e41.nv24.08.17622132-cp310-cp310-linux_aarch64.whl
```

从源码安装 torchvision 时，应检出与 PyTorch 对应的 tag，然后使用 pip 构建安装：

```text
torch 2.7.0  -> torchvision 0.22.0
torch 2.6.0  -> torchvision 0.21.0
torch 2.5.1  -> torchvision 0.20.1
torch 2.5.0  -> torchvision 0.20.0
torch 2.4.1  -> torchvision 0.19.1
torch 2.4.0  -> torchvision 0.19.0
torch 2.3.1  -> torchvision 0.18.1
torch 2.3.0  -> torchvision 0.18.0
torch 2.2.2  -> torchvision 0.17.2
torch 2.1.0  -> torchvision 0.16.0
torch 2.0.1  -> torchvision 0.15.2
torch 1.13.1 -> torchvision 0.14.1
torch 1.12.1 -> torchvision 0.13.1
torch 1.11.0 -> torchvision 0.12.3
torch 1.10.0 -> torchvision 0.11.1
```

```bash
python -m pip install .
```

验证 PyTorch、torchvision、CUDA 和 cuDNN：

```python
import torch
import torchvision

print("PyTorch version:", torch.__version__)
print("torchvision version:", torchvision.__version__)
print("CUDA available:", torch.cuda.is_available())
print("cuDNN version:", torch.backends.cudnn.version())
```

如果还需要验证 TensorRT，可单独执行：

```python
import tensorrt

print("TensorRT version:", tensorrt.__version__)
```

## 5. 启用最大性能模式

先查询设备支持的模式及当前配置：

```bash
sudo nvpmodel -q --verbose
```

确认模式编号后再切换。部分设备使用模式 `0` 表示 MAXN：

```bash
sudo nvpmodel -m 0
sudo jetson_clocks
```

模式编号因设备和 JetPack 版本而异，应以 `nvpmodel` 的查询结果为准。
