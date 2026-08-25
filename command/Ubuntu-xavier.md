# NVIDIA Jetson Xavier / Orin 常用命令

完整的环境配置、Miniconda、PyTorch 安装和版本匹配说明请查看：[NVIDIA Jetson Xavier / Orin 环境配置](../slam_config/Ubuntu-xavier.md)。

## 查看设备信息

```bash
jtop
jetson_release
sudo nvpmodel -q --verbose
```

## 切换性能模式

确认设备支持的模式编号后再执行。部分设备使用模式 `0` 表示 MAXN：

```bash
sudo nvpmodel -m 0
sudo jetson_clocks
```

## 查看 CUDA 编译器版本

```bash
nvcc --version
```

## 查看 JetPack 软件包

```bash
dpkg-query --show nvidia-jetpack
```

> 不同 Jetson 型号和 JetPack 版本支持的功耗模式、风扇路径及软件包版本不同，请以设备查询结果和主配置文档为准。
