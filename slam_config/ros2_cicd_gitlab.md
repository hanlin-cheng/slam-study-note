# 使用 Docker 制作 ROS2 镜像并集成 GitLab CI/CD 指南

本文档说明如何：

1. 选择合适的 ROS2 Docker 基础镜像  
2. 编写用于项目 CI/CD 的 Dockerfile  
3. 将镜像推送到 GitLab Registry  
4. 编写 `.gitlab-ci.yml` 定义 CI/CD 流程  
5. 在 GitLab 项目中配置 CI/CD

> 假设你对 Docker 和 GitLab 有基本认识，并且已经在 GitLab 中开启了 Container Registry 功能（项目 Settings → General / Settings → Packages & Registries）。

---

假设我需要ci/cd的仓库地址是https://gitlab.fftaicorp.com/fourier_infinityrepo/perceptionnav/humanoidnav

## 1. 从 `osrf/docker_images` 选择合适的 ROS2 基础镜像

OSRF 官方维护了 ROS/ROS2 的 Docker 镜像仓库：

- GitHub 仓库：<https://github.com/osrf/docker_images>  
- Docker Hub 镜像：<https://hub.docker.com/r/osrf/ros>

### 1.1 确定项目需要的 ROS2 版本和基础系统

在选择镜像前，确认以下信息：

- ROS2 发行版：如 `humble`, `iron`, `jazzy`  
- 基础系统：例如 `ubuntu:22.04`  
- 是否需要图形环境或仿真（desktop / ros-base / ros-core）

**建议（CI 环境）**：

- 使用 `*-ros-base` 或 `*-ros-core`，镜像体积更小，构建速度快。

示例镜像：

ros:humble-ros-base


### 1.2 本地验证镜像（推荐）

```bash
docker pull ros:humble-ros-base
docker run -it --rm ros:humble-ros-base bash

source /opt/ros/humble/setup.bash
ros2 --help
```


## 2. 编写 Dockerfile 制作项目 CI/CD 用镜像

在项目根目录创建 Dockerfile.ci。

**作用**：定义一个自定义的 Docker 镜像，包含你项目 CI/CD 运行所需的环境。

**内容**：基础镜像（如 `ros:humble-ros-base`） + 安装依赖工具（`colcon`、`rosdep` 等） + 设置工作目录和环境变量。

**目的**：

- 让 CI/CD 过程在一个干净、可重复的环境中运行。
- 减少每次流水线都去安装依赖的时间。

**使用场景**：

- 本地测试：`docker build -t my-image:tag -f Dockerfile.ci .`
- 上传到 GitLab Registry，CI 流程使用这个镜像。

### 2.1 Dockerfile 示例

```dockerfile
# 1. 使用 OSRF 提供的 ROS2 基础镜像
FROM ros:humble-ros-base

# 2. 维护者信息（可选）
LABEL maintainer="your.name@example.com"

# 3. 设置非交互模式
ENV DEBIAN_FRONTEND=noninteractive

# 4. 安装构建工具与常用依赖
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        wget \
        python3-pip \
        python3-colcon-common-extensions \
        python3-rosdep \
        python3-vcstool \
        lsb-release \
        locales \
    && rm -rf /var/lib/apt/lists/*

# 5. 设置 locale
RUN locale-gen en_US en_US.UTF-8 && \
    update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8

# 6. rosdep 初始化
RUN rosdep init || true

# 7. 设置工作目录
WORKDIR /workspace

# 8. 使用 bash 作为默认 shell
SHELL ["/bin/bash", "-c"]
RUN echo "source /opt/ros/humble/setup.bash" >> /root/.bashrc

# 9. 可选：预安装项目依赖（如 apt 包）
# RUN apt-get update && apt-get install -y <your-deps>

# 10. 默认进入 bash
CMD ["bash"]
```

### 2.2 本地构建测试（可选）

```bash
docker build --no-cache -t ros2-ci:humble -f Dockerfile.ci .

docker run -it --rm ros2-ci:humble bash
```

## 3. 将镜像上传到 GitLab Registry

假设 GitLab Registry 地址如下：

```bash
https://gitlab.fftaicorp.com/fourier_infinityrepo/perceptionnav/humanoidnav
```

**确认 GitLab 项目已启用 Container Registry**

- 在 GitLab 项目中进入：
   `Settings → General → Packages & Registries → Container Registry`
- 如果显示可用，说明可以上传镜像。

### 3.1 登录 Registry

```bash
docker login gitlab.fftaicorp.com:5050
```

系统会提示输入 **用户名** 和 **Personal Access Token**（或者 GitLab 密码，推荐使用 Token）。

- **用户名**：GitLab 用户名
- **密码**：GitLab Personal Access Token（需要勾选 `read_registry` + `write_registry` 权限）

登录成功后会显示 `Login Succeeded`。

### 3.2 推送镜像

GitLab Registry 的镜像命名格式：

```
<gitlab-domain>:<port>/<group>/<project>/<image-name>:<tag>
```

```bash
docker tag ros2-ci:humble gitlab.fftaicorp.com:5050/fourier_infinityrepo/perceptionnav/humanoidnav/ros2-ci:humble
docker push gitlab.fftaicorp.com:5050/fourier_infinityrepo/perceptionnav/humanoidnav/ros2-ci:humble
```

推送后可在 GitLab
Packages & Registries → Container Registry 查看。


## 4. 编写 .gitlab-ci.yml 定义 CI/CD 操作

在仓库根目录创建：

```bash
.gitlab-ci.yml
```

**作用**：定义 GitLab CI/CD 流水线，告诉 GitLab Runner **什么时候做什么事情**。

**内容**：

- 阶段（stages）：build、test、deploy 等
- 每个阶段运行的脚本（script）
- 使用的 Docker 镜像（可以用你用 Dockerfile.ci 构建的镜像）
- artifacts（构建产物）、环境变量、依赖等

**目的**：

- 指导 GitLab Runner 在代码提交时自动执行构建、测试、部署。
- 保证整个流程可复现、可追踪。

**使用场景**：

- GitLab 项目根目录下放置 `.gitlab-ci.yml`
- GitLab Runner 会根据这个文件执行流水线

### 4.1 示例 CI 配置

以下只是示例。请根据具体需求进行配置。[Gitlab Docs](https://docs.gitlab.com/ci/yaml/)


```dockerfile
stages:
  - build
  - test

variables:
  GIT_SUBMODULE_STRATEGY: recursive

image: "gitlab.example.com:5050/group/project/ros2-ci:humble"

before_script:
  - source /opt/ros/humble/setup.bash
  - apt-get update && apt-get install -y --no-install-recommends python3-rosdep && rm -rf /var/lib/apt/lists/*
  - rosdep update
  - rosdep install --from-paths src --ignore-src -r -y

build:
  stage: build
  script:
    - set -e
    - mkdir -p build_ws && cd build_ws
    - cp -r ../src ./
    - colcon build --merge-install
  artifacts:
    paths:
      - build_ws/install
    expire_in: 1 week
  only:
    - merge_requests
    - main

test:
  stage: test
  dependencies:
    - build
  script:
    - set -e
    - cd build_ws
    - source install/setup.bash
    - colcon test
    - colcon test-result --verbose
  only:
    - merge_requests
    - main
```

## 5. 在 GitLab 配置项目 CI/CD

### 5.1 确保有可用 Runner

配置Runner，详见[Gitlab Docs](https://docs.gitlab.com/runner/)

 - 可以使用 GitLab Shared Runner 或自建 Runner

 - 必须支持 Docker executor

 - Runner 需要能够访问 GitLab Registry

### 5.2 配置环境变量（可选）

根据项目需求配置环境变量。请见[Gitlab Docs](https://docs.gitlab.com/ci/variables/)


### 5.3 提交代码触发流水线

```bash
git add Dockerfile.ci .gitlab-ci.yml
git commit -m "Add ROS2 CI docker image and GitLab CI config"
git push
```

然后在网上查看Pipeline状态。