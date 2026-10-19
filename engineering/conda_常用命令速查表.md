# Conda 常用命令速查表

## 一、环境管理

### 1. 查看已有环境

``` bash
conda env list
# 或
conda info -e
```

### 2. 创建环境

``` bash
conda create -n myenv python=3.10
```

带常用包：

``` bash
conda create -n myenv python=3.10 numpy scipy matplotlib
```

### 3. 激活 / 退出环境

``` bash
conda activate myenv
conda deactivate
```

### 4. 删除环境

``` bash
conda remove -n myenv --all
```

------------------------------------------------------------------------

## 二、包管理

### 5. 查看已安装包

``` bash
conda list
```

### 6. 安装包

``` bash
conda install numpy
```

指定版本：

``` bash
conda install numpy=1.26
```

### 7. 卸载包

``` bash
conda remove numpy
```

### 8. 更新包

``` bash
conda update numpy
```

更新 conda 自身：

``` bash
conda update conda
```

------------------------------------------------------------------------

## 三、搜索与信息查询

### 9. 搜索包

``` bash
conda search opencv
```

### 10. 查看包信息

``` bash
conda info numpy
```

------------------------------------------------------------------------

## 四、导出 / 复现环境（工程必备）

### 11. 导出环境

``` bash
conda env export > environment.yml
```

不包含构建信息（推荐）：

``` bash
conda env export --no-builds > environment.yml
```

### 12. 从文件创建环境

``` bash
conda env create -f environment.yml
```

------------------------------------------------------------------------

## 五、Channels 管理

### 13. 查看当前 channel

``` bash
conda config --show channels
```

### 14. 添加 channel

``` bash
conda config --add channels conda-forge
```

设为最高优先级：

``` bash
conda config --set channel_priority strict
```

------------------------------------------------------------------------

## 六、清理与维护

### 15. 清理缓存

``` bash
conda clean -a
```

------------------------------------------------------------------------

## 七、conda 与 pip 混用

### 16. 在 conda 环境中使用 pip

``` bash
pip install xxx
```

建议顺序： 1. 先使用 conda install 2. 再使用 pip install

------------------------------------------------------------------------

## 八、关闭 base 自动激活

``` bash
conda config --set auto_activate_base false
```

------------------------------------------------------------------------

## 九、调试与状态检查

### 查看 conda 版本

``` bash
conda --version
```

### 查看 conda 信息

``` bash
conda info
```

------------------------------------------------------------------------

## 十、常见组合命令

### 快速创建并进入环境

``` bash
conda create -n torch python=3.10 -y && conda activate torch
```

### 复制一个环境

``` bash
conda create -n newenv --clone oldenv
```
