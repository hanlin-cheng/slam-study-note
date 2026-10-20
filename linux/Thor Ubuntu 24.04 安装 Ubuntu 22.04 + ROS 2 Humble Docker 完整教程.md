# Thor Ubuntu 24.04 安装 Ubuntu 22.04 + ROS 2 Humble Docker 完整教程

## 1. 最终环境结构

本教程最终构建如下环境：

```text
NVIDIA Jetson AGX Thor
└── Ubuntu 24.04 宿主机
    ├── Thor 内核与 NVIDIA 驱动
    ├── Docker
    ├── NVIDIA Container Runtime
    └── Docker 容器
        ├── Ubuntu 22.04 Jammy 用户态
        ├── ROS 2 Humble
        ├── PCL、OpenCV、Ceres、Octomap
        ├── RViz2、image_transport
        ├── BehaviorTree.CPP
        ├── Fast DDS RMW
        ├── pygame
        └── humanoidnav 编译环境
```

`ros:humble-ros-base-jammy` 官方镜像提供 `linux/arm64/v8` 版本，可以直接在 Thor 上运行，不需要通过 QEMU 模拟。

需要注意：

```text
容器中的 Ubuntu 版本：22.04
容器中的 CPU 架构：arm64
容器使用的 Linux 内核：Thor 宿主机内核
```

因此，容器中执行：

```bash
cat /etc/os-release
```

会看到 Ubuntu 22.04；而执行：

```bash
uname -r
```

看到的仍然是 Thor 宿主机内核，这是 Docker 的正常工作方式。

------

## 2. 检查 Thor 宿主机

在 Thor 宿主机上执行：

```bash
uname -m
cat /etc/os-release
uname -r
```

预期架构：

```text
aarch64
```

预期系统：

```text
Ubuntu 24.04
```

检查 Jetson 系统版本：

```bash
dpkg-query -W nvidia-l4t-core 2>/dev/null || true
```

检查 Docker：

```bash
docker --version
```

检查 NVIDIA Container Toolkit：

```bash
nvidia-ctk --version
```

检查 NVIDIA runtime：

```bash
docker info | grep -i -E 'runtime|runtimes'
```

理想情况下可以看到：

```text
Runtimes: nvidia runc
Default Runtime: nvidia
```

------

## 3. 安装和配置 Docker

如果 Docker 已经正常安装，可以直接跳到第 4 节。

NVIDIA 官方 Thor 文档建议安装 NVIDIA Container Runtime，并使用 `nvidia-ctk` 配置 Docker；还可以将 `nvidia` 设置成默认 runtime。

### 3.1 安装基础组件

```bash
sudo apt-get update

sudo apt-get install -y \
    ca-certificates \
    curl \
    jq \
    nvidia-container
```

### 3.2 安装 Docker

```bash
cd /tmp

curl -fsSL https://get.docker.com -o get-docker.sh

sudo sh get-docker.sh

rm -f get-docker.sh
```

启动 Docker：

```bash
sudo systemctl enable --now docker
```

检查状态：

```bash
sudo systemctl status docker --no-pager
```

### 3.3 配置 NVIDIA Container Runtime

```bash
sudo nvidia-ctk runtime configure --runtime=docker
```

重新启动 Docker：

```bash
sudo systemctl restart docker
```

检查：

```bash
docker info | grep -i -E 'runtime|runtimes'
```

如果希望将 NVIDIA 设置为默认 runtime：

```bash
sudo jq '. + {"default-runtime": "nvidia"}' \
    /etc/docker/daemon.json \
    | sudo tee /etc/docker/daemon.json.tmp >/dev/null

sudo mv \
    /etc/docker/daemon.json.tmp \
    /etc/docker/daemon.json

sudo systemctl restart docker
```

检查最终配置：

```bash
cat /etc/docker/daemon.json
```

应包含类似内容：

```json
{
  "runtimes": {
    "nvidia": {
      "args": [],
      "path": "nvidia-container-runtime"
    }
  },
  "default-runtime": "nvidia"
}
```

### 3.4 允许普通用户调用 Docker

```bash
sudo usermod -aG docker "$USER"
```

让当前终端临时生效：

```bash
newgrp docker
```

重新检查：

```bash
docker version
docker info
```

如果仍然报错：

```text
permission denied while trying to connect to the Docker daemon
```

退出当前 SSH 或桌面会话，然后重新登录。

------

## 4. 拉取 ROS 2 Humble ARM64 基础镜像

显式拉取 ARM64 镜像：

```bash
docker pull \
    --platform linux/arm64/v8 \
    ros:humble-ros-base-jammy
```

检查镜像架构：

```bash
docker image inspect \
    ros:humble-ros-base-jammy \
    --format 'OS={{.Os}} ARCH={{.Architecture}}'
```

预期：

```text
OS=linux ARCH=arm64
```

验证基础容器：

```bash
docker run --rm \
    --platform linux/arm64/v8 \
    ros:humble-ros-base-jammy \
    bash -lc '
        cat /etc/os-release
        echo "ROS_DISTRO=${ROS_DISTRO}"
        echo "Architecture=$(dpkg --print-architecture)"
        ros2 --help >/dev/null
        echo "ROS 2 Humble base image is ready"
    '
```

预期包含：

```text
VERSION_ID="22.04"
VERSION_CODENAME=jammy
ROS_DISTRO=humble
Architecture=arm64
ROS 2 Humble base image is ready
```

------

## 5. 创建 Docker 构建目录

```bash
mkdir -p ~/docker/humanoidnav-humble
cd ~/docker/humanoidnav-humble
```

目录最终如下：

```text
~/docker/humanoidnav-humble/
├── Dockerfile
└── .dockerignore
```

------

## 6. 创建完整 Dockerfile

创建文件：

```bash
vim Dockerfile
```

写入以下内容：

```dockerfile
# syntax=docker/dockerfile:1

# =============================================================================
# 基础参数
# =============================================================================

ARG ROS_DISTRO=humble
ARG BASE_IMAGE=ros:${ROS_DISTRO}-ros-base-jammy

FROM ${BASE_IMAGE}

# FROM 之后必须重新声明 ARG
ARG ROS_DISTRO=humble
ARG DEBIAN_FRONTEND=noninteractive
ARG USE_CHINA_MIRROR=false

ENV ROS_DISTRO=${ROS_DISTRO} \
    DEBIAN_FRONTEND=noninteractive \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

# 默认使用 bash，并开启 pipefail
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# 基础镜像默认就是 root，这里显式声明
USER root

# =============================================================================
# 验证基础系统
# =============================================================================

RUN set -eux; \
    source /etc/os-release; \
    echo "Base system: ${PRETTY_NAME}"; \
    echo "Codename: ${VERSION_CODENAME}"; \
    echo "Architecture: $(dpkg --print-architecture)"; \
    test "${VERSION_CODENAME}" = "jammy"; \
    test "$(dpkg --print-architecture)" = "arm64"; \
    test -f "/opt/ros/${ROS_DISTRO}/setup.bash"

# =============================================================================
# APT 配置
# =============================================================================

RUN printf '%s\n' \
    'APT::Install-Recommends "false";' \
    'APT::Install-Suggests "false";' \
    > /etc/apt/apt.conf.d/01norecommend

# 可选：使用阿里云 Ubuntu ARM64 软件源
# 构建时传入 --build-arg USE_CHINA_MIRROR=true 才会启用
RUN set -eux; \
    if [[ "${USE_CHINA_MIRROR}" == "true" ]]; then \
        sed -i \
            -e 's|http://ports.ubuntu.com/ubuntu-ports|https://mirrors.aliyun.com/ubuntu-ports|g' \
            -e 's|https://ports.ubuntu.com/ubuntu-ports|https://mirrors.aliyun.com/ubuntu-ports|g' \
            -e 's|http://archive.ubuntu.com/ubuntu|https://mirrors.aliyun.com/ubuntu|g' \
            -e 's|http://security.ubuntu.com/ubuntu|https://mirrors.aliyun.com/ubuntu|g' \
            /etc/apt/sources.list; \
    fi; \
    cat /etc/apt/sources.list

# =============================================================================
# 安装基础工具和 SYSTEM_DEPS
# =============================================================================

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        ca-certificates \
        locales \
        tzdata \
        sudo \
        curl \
        wget \
        vim \
        htop \
        less \
        tree \
        file \
        unzip \
        zip \
        rsync \
        git \
        git-lfs \
        openssh-client \
        build-essential \
        cmake \
        pkg-config \
        gdb \
        valgrind \
        python3 \
        python3-dev \
        python3-pip \
        python3-setuptools \
        python3-wheel \
        python3-colcon-common-extensions \
        python3-rosdep \
        python3-vcstool \
        iproute2 \
        iputils-ping \
        net-tools \
        dnsutils \
        can-utils \
        usbutils \
        pciutils \
        x11-apps \
        xauth \
        mesa-utils \
        libgl1-mesa-dri \
        libgl1-mesa-glx \
        ninja-build \
        libpcl-dev \
        libopencv-dev \
        libpcap-dev \
        libgraphicsmagick++1-dev \
        xtensor-dev \
        libxsimd-dev \
        libceres-dev \
        libnanoflann-dev \
        libgeographic-dev \
        liboctomap-dev \
        libgflags-dev \
        nlohmann-json3-dev \
        libgoogle-glog-dev \
    && locale-gen en_US.UTF-8 \
    && update-locale \
        LANG=en_US.UTF-8 \
        LANGUAGE=en_US:en \
        LC_ALL=en_US.UTF-8 \
    && git lfs install --system \
    && rm -rf /var/lib/apt/lists/*

# =============================================================================
# 安装 ROS_DEPS
# =============================================================================

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        ros-${ROS_DISTRO}-pcl-ros \
        ros-${ROS_DISTRO}-bondcpp \
        ros-${ROS_DISTRO}-test-msgs \
        ros-${ROS_DISTRO}-diagnostic-updater \
        ros-${ROS_DISTRO}-imu-tools \
        ros-${ROS_DISTRO}-geographic-msgs \
        ros-${ROS_DISTRO}-xacro \
        ros-${ROS_DISTRO}-behaviortree-cpp-v3 \
        ros-${ROS_DISTRO}-ompl \
        ros-${ROS_DISTRO}-joint-state-publisher \
        ros-${ROS_DISTRO}-joint-state-publisher-gui \
        ros-${ROS_DISTRO}-laser-geometry \
        ros-${ROS_DISTRO}-map-msgs \
        ros-${ROS_DISTRO}-angles \
        ros-${ROS_DISTRO}-rviz-default-plugins \
        ros-${ROS_DISTRO}-rviz2 \
        ros-${ROS_DISTRO}-image-transport \
        ros-${ROS_DISTRO}-image-publisher \
        ros-${ROS_DISTRO}-camera-info-manager \
        ros-${ROS_DISTRO}-diagnostic-msgs \
        ros-${ROS_DISTRO}-backward-ros \
        ros-${ROS_DISTRO}-image-transport-plugins \
        ros-${ROS_DISTRO}-rmw-fastrtps-cpp \
        ros-${ROS_DISTRO}-demo-nodes-cpp \
        ros-${ROS_DISTRO}-demo-nodes-py \
    && rm -rf /var/lib/apt/lists/*

# =============================================================================
# 安装 Python 包
# =============================================================================

# Jammy ARM64 上优先使用 Ubuntu 的 pygame 包，
# 避免 pip 在 ARM64 上临时编译 SDL 相关依赖。
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        python3-pygame \
    && rm -rf /var/lib/apt/lists/*

# 如果明确要求从 PyPI 安装 pygame，可以删除上面的 python3-pygame，
# 然后启用下面的命令。
#
# RUN python3 -m pip install \
#       --no-cache-dir \
#       pygame

# =============================================================================
# GeographicLib CMake 模块兼容处理
# =============================================================================

RUN set -eux; \
    source_file="/usr/share/cmake/geographiclib/FindGeographicLib.cmake"; \
    cmake_root="$(cmake --system-information \
        | awk -F'\"' '/^CMAKE_ROOT / {print $2; exit}')"; \
    echo "CMAKE_ROOT=${cmake_root}"; \
    if [[ -f "${source_file}" && -n "${cmake_root}" ]]; then \
        ln -sf \
            "${source_file}" \
            "${cmake_root}/Modules/FindGeographicLib.cmake"; \
    fi

# =============================================================================
# 初始化 rosdep
# =============================================================================

RUN set -eux; \
    if [[ ! -f /etc/ros/rosdep/sources.list.d/20-default.list ]]; then \
        rosdep init; \
    fi; \
    rosdep update --rosdistro="${ROS_DISTRO}"

# =============================================================================
# 设置 root 环境
# =============================================================================

WORKDIR /workspace

# 官方 ROS 镜像的 ENTRYPOINT 会加载 /opt/ros/humble/setup.bash。
# 此处额外写入 bashrc，是为了 docker exec 进入容器时也能自动加载。
RUN cat >> /root/.bashrc <<EOF

# ROS 2 Humble
source /opt/ros/${ROS_DISTRO}/setup.bash

# 自动加载工作空间
if [ -f /workspace/install/setup.bash ]; then
    source /workspace/install/setup.bash
fi
EOF

# =============================================================================
# 验证系统依赖
# =============================================================================

RUN set -eux; \
    dpkg-query -W \
        ninja-build \
        libpcl-dev \
        libopencv-dev \
        git \
        libpcap-dev \
        libgraphicsmagick++1-dev \
        xtensor-dev \
        libxsimd-dev \
        libceres-dev \
        libnanoflann-dev \
        libgeographic-dev \
        python3-wheel \
        liboctomap-dev \
        libgflags-dev \
        nlohmann-json3-dev \
        libgoogle-glog-dev \
        python3-pygame

# =============================================================================
# 验证 Python 环境
# =============================================================================

RUN set -eux; \
    python3 --version; \
    python3 -m pip --version; \
    python3 -c "import pygame; print('pygame version:', pygame.version.ver)"

# =============================================================================
# 验证 ROS 2 包
#
# 注意：
# 1. source ROS setup.bash 时不能开启 nounset（set -u）。
# 2. backward_ros 是普通 CMake 包，不使用 ros2 pkg prefix 检查。
# =============================================================================

RUN set -eo pipefail; \
    : "${ROS_DISTRO:?ROS_DISTRO is not defined}"; \
    setup_file="/opt/ros/${ROS_DISTRO}/setup.bash"; \
    test -f "${setup_file}"; \
    \
    source "${setup_file}"; \
    \
    echo "ROS_DISTRO=${ROS_DISTRO}"; \
    echo "Architecture=$(dpkg --print-architecture)"; \
    echo "AMENT_PREFIX_PATH=${AMENT_PREFIX_PATH:-}"; \
    \
    packages=( \
        pcl_ros \
        bondcpp \
        test_msgs \
        diagnostic_updater \
        imu_tools \
        geographic_msgs \
        xacro \
        behaviortree_cpp_v3 \
        ompl \
        joint_state_publisher \
        joint_state_publisher_gui \
        laser_geometry \
        map_msgs \
        angles \
        rviz_default_plugins \
        rviz2 \
        image_transport \
        image_publisher \
        camera_info_manager \
        diagnostic_msgs \
        image_transport_plugins \
        rmw_fastrtps_cpp \
    ); \
    \
    for pkg in "${packages[@]}"; do \
        echo "Checking ROS package: ${pkg}"; \
        prefix="$(ros2 pkg prefix "${pkg}")"; \
        echo "[OK] ${pkg}: ${prefix}"; \
    done; \
    \
    echo "Checking backward_ros Debian package"; \
    dpkg-query -W \
        -f='${Package} ${Version}\n' \
        "ros-${ROS_DISTRO}-backward-ros"; \
    \
    echo "Checking backward_ros CMake configuration"; \
    test -f \
        "/opt/ros/${ROS_DISTRO}/share/backward_ros/cmake/backward_rosConfig.cmake"; \
    \
    echo "[OK] backward_ros CMake configuration found"; \
    echo "All ROS 2 dependency checks passed"

# =============================================================================
# 最终配置
# =============================================================================

ENV RMW_IMPLEMENTATION=rmw_fastrtps_cpp \
    ROS_DOMAIN_ID=0

USER root
WORKDIR /workspace

CMD ["bash"]
```

------

## 7. 创建 `.dockerignore`

创建：

```bash
vim .dockerignore
```

写入：

```text
.git
.gitmodules
build
log
.vscode
.idea
__pycache__
*.pyc
*.pyo
*.swp
*.tmp
*.log
```

当前 Dockerfile 没有 `COPY .`，因此 `.dockerignore` 的影响不大；但后续将项目源码复制进镜像时，它可以避免把编译产物和 Git 数据发送到 Docker 构建上下文。

------

## 8. 构建镜像

### 8.1 使用官方 Ubuntu 软件源

```bash
cd ~/docker/humanoidnav-humble

docker build \
    --pull \
    --platform linux/arm64/v8 \
    --progress=plain \
    --build-arg ROS_DISTRO=humble \
    --build-arg USE_CHINA_MIRROR=false \
    -t humanoidnav:v1.0 \
    .
```

### 8.2 使用阿里云 Ubuntu ARM64 软件源

```bash
docker build \
    --pull \
    --platform linux/arm64/v8 \
    --progress=plain \
    --build-arg ROS_DISTRO=humble \
    --build-arg USE_CHINA_MIRROR=true \
    -t humanoidnav:v1.0 \
    .
```

第一次建议不要使用：

```bash
--no-cache
```

这样安装过程中断后还能利用已有缓存。

只有怀疑旧缓存导致问题时再使用：

```bash
docker build \
    --no-cache \
    --pull \
    --platform linux/arm64/v8 \
    --progress=plain \
    -t humanoidnav:v1.0 \
    .
```

------

## 9. 检查镜像

查看镜像：

```bash
docker images | grep humanoidnav
```

检查架构：

```bash
docker image inspect \
    humanoidnav:v1.0 \
    --format 'OS={{.Os}} ARCH={{.Architecture}} USER={{.Config.User}}'
```

预期：

```text
OS=linux ARCH=arm64 USER=root
```

有些镜像检查时 `USER` 可能显示为空字符串。Docker 中空字符串同样表示默认使用 root。

进一步验证：

```bash
docker run --rm \
    humanoidnav:v1.0 \
    bash -lc '
        echo "Current user: $(whoami)"
        echo "UID=$(id -u)"
        cat /etc/os-release
        echo "ROS_DISTRO=${ROS_DISTRO}"
        echo "RMW_IMPLEMENTATION=${RMW_IMPLEMENTATION}"
        echo "Architecture=$(dpkg --print-architecture)"
    '
```

预期：

```text
Current user: root
UID=0
VERSION_ID="22.04"
ROS_DISTRO=humble
RMW_IMPLEMENTATION=rmw_fastrtps_cpp
Architecture=arm64
```

------

## 10. 验证全部依赖

### 10.1 验证系统依赖

```bash
docker run --rm \
    humanoidnav:v1.0 \
    bash -lc '
        dpkg-query -W \
            ninja-build \
            libpcl-dev \
            libopencv-dev \
            git \
            libpcap-dev \
            libgraphicsmagick++1-dev \
            xtensor-dev \
            libceres-dev \
            libnanoflann-dev \
            libgeographic-dev \
            python3-wheel \
            liboctomap-dev \
            libgflags-dev \
            nlohmann-json3-dev \
            libgoogle-glog-dev
    '
```

### 10.2 验证 ROS 依赖

```bash
docker run --rm \
    humanoidnav:v1.0 \
    bash -lc '
        source /opt/ros/humble/setup.bash

        for pkg in \
            pcl_ros \
            bondcpp \
            test_msgs \
            diagnostic_updater \
            imu_tools \
            geographic_msgs \
            xacro \
            behaviortree_cpp_v3 \
            ompl \
            joint_state_publisher \
            joint_state_publisher_gui \
            laser_geometry \
            map_msgs \
            angles \
            rviz_default_plugins \
            rviz2 \
            image_transport \
            image_publisher \
            camera_info_manager \
            diagnostic_msgs \
            image_transport_plugins \
            rmw_fastrtps_cpp
        do
            echo "===== ${pkg} ====="
            ros2 pkg prefix "${pkg}"
        done

        dpkg-query -W ros-humble-backward-ros
        test -f /opt/ros/humble/share/backward_ros/cmake/backward_rosConfig.cmake
    '
```

### 10.3 验证 pygame

```bash
docker run --rm \
    humanoidnav:v1.0 \
    python3 -c '
import pygame
print("pygame version:", pygame.version.ver)
'
```

### 10.4 验证 rclpy

```bash
docker run --rm \
    humanoidnav:v1.0 \
    bash -lc '
        source /opt/ros/humble/setup.bash
        python3 -c "import rclpy; print(rclpy.__file__)"
    '
```

------

## 11. 启动 root 开发容器

假设 humanoidnav 源码位于：

```text
/home/fourier/lvc/humanoidnav
```

先设置变量：

```bash
export HUMANOIDNAV_WS="$HOME/lvc/humanoidnav"
```

检查：

```bash
ls -la "$HUMANOIDNAV_WS"
```

容器将 `$HUMANOIDNAV_WS` 挂载到 `/workspace`，与后续编译目录及自动加载脚本保持一致。`/opt/fftai` 保留为项目配置挂载目录；使用示例中的 Fast DDS 配置前，需确认宿主机对应 XML 文件存在，或将该环境变量改为自己的实际配置路径。

启动容器：

```bash
docker run --rm -it \
    --name humanoidnav-humble \
    --hostname thor-humble \
    --runtime=nvidia \
    --network=host \
    --ipc=host \
    --cap-add=IPC_LOCK \
    --cap-add=SYS_NICE \
    --ulimit memlock=-1:-1 \
    --ulimit stack=67108864 \
    --ulimit rtprio=99 \
    --ulimit nofile=1048576:1048576 \
    -e ROS_DOMAIN_ID=123 \
    -e ROS_LOCALHOST_ONLY=0 \
    -e RMW_IMPLEMENTATION=rmw_fastrtps_cpp \
    -e FASTRTPS_DEFAULT_PROFILES_FILE=/opt/fftai/humanoidnav/dds_profiles/FastRTPSProfilesZeroCopy.xml \
    -v "$HUMANOIDNAV_WS:/workspace" \
    -v /opt/fftai:/opt/fftai \
    -w /workspace \
    humanoidnav:v1.0 \
    bash
```

进入后检查：

```bash
whoami
id
pwd
echo "$ROS_DISTRO"
echo "$RMW_IMPLEMENTATION"
```

预期：

```text
root
uid=0(root)
gid=0(root)
/workspace
humble
rmw_fastrtps_cpp
```

因为基础镜像自带 ROS entrypoint，正常 `docker run` 会自动加载 ROS 环境；同时 Dockerfile 也将 ROS setup 写入了 `/root/.bashrc`，所以通过 `docker exec` 进入新的 bash 时也能加载。

------

## 12. 保留一个长期运行的开发容器

沿用上一节设置的 `HUMANOIDNAV_WS`。如果不希望退出后自动删除容器，不要使用 `--rm`：

```bash
docker run -it \
    --name humanoidnav-humble \
    --hostname thor-humble \
    --runtime=nvidia \
    --network=host \
    --ipc=host \
    --cap-add=IPC_LOCK \
    --cap-add=SYS_NICE \
    --ulimit memlock=-1:-1 \
    --ulimit stack=67108864 \
    --ulimit rtprio=99 \
    --ulimit nofile=1048576:1048576 \
    -e ROS_DOMAIN_ID=123 \
    -e ROS_LOCALHOST_ONLY=0 \
    -e RMW_IMPLEMENTATION=rmw_fastrtps_cpp \
    -e FASTRTPS_DEFAULT_PROFILES_FILE=/opt/fftai/humanoidnav/dds_profiles/FastRTPSProfilesZeroCopy.xml \
    -v "$HUMANOIDNAV_WS:/workspace" \
    -v /opt/fftai:/opt/fftai \
    -w /workspace \
    humanoidnav:v1.0 \
    bash
```

退出容器：

```bash
exit
```

再次启动：

```bash
docker start humanoidnav-humble
```

再次进入：

```bash
docker exec -it humanoidnav-humble bash
```

停止：

```bash
docker stop humanoidnav-humble
```

删除：

```bash
docker rm humanoidnav-humble
```

------

## 13. 在容器中编译 humanoidnav

进入容器后执行：

```bash
cd /workspace

source /opt/ros/humble/setup.bash
```

初始化 Git 子模块：

```bash
git submodule sync --recursive

git submodule update \
    --init \
    --recursive
```

更新 rosdep：

```bash
rosdep update --rosdistro=humble
```

安装工作空间缺少的依赖：

```bash
rosdep install \
    --from-paths src \
    --ignore-src \
    --rosdistro humble \
    -r \
    -y
```

清理历史编译产物：

```bash
rm -rf build install log
```

使用 Ninja 编译：

```bash
colcon build \
    --symlink-install \
    --executor sequential \
    --cmake-args \
        -G Ninja \
        -DCMAKE_BUILD_TYPE=Release
```

如果内存足够，可以使用并行编译：

```bash
colcon build \
    --symlink-install \
    --parallel-workers "$(nproc)" \
    --cmake-args \
        -G Ninja \
        -DCMAKE_BUILD_TYPE=Release
```

Thor 上首次编译大型工作空间时，不建议一开始就使用全部 CPU 核心。可以先限制为：

```bash
colcon build \
    --symlink-install \
    --parallel-workers 8 \
    --cmake-args \
        -G Ninja \
        -DCMAKE_BUILD_TYPE=Release
```

编译完成后：

```bash
source install/setup.bash
```

检查：

```bash
echo "$AMENT_PREFIX_PATH"
ros2 pkg list | grep -E 'humanoid|navigation'
```

------

## 14. root 用户产生宿主机 root 文件的问题

因为容器默认使用 root，挂载目录中的：

```text
build/
install/
log/
```

可能在宿主机上属于：

```text
root:root
```

检查：

```bash
ls -ld \
    "$HUMANOIDNAV_WS/build" \
    "$HUMANOIDNAV_WS/install" \
    "$HUMANOIDNAV_WS/log"
```

需要恢复给宿主机当前用户时执行：

```bash
sudo chown -R \
    "$(id -u):$(id -g)" \
    "$HUMANOIDNAV_WS/build" \
    "$HUMANOIDNAV_WS/install" \
    "$HUMANOIDNAV_WS/log"
```

如果整个工作空间都产生了 root 文件：

```bash
sudo chown -R \
    "$(id -u):$(id -g)" \
    "$HUMANOIDNAV_WS"
```

这是默认使用 root 用户的主要副作用。

------

## 15. 验证 ROS 2 通信

### 15.1 启动 talker

启动容器：

```bash
docker run --rm -it \
    --name ros2-talker \
    --network=host \
    --ipc=host \
    -e ROS_DOMAIN_ID=0 \
    humanoidnav:v1.0 \
    bash -lc '
        source /opt/ros/humble/setup.bash
        ros2 run demo_nodes_cpp talker
    '
```

### 15.2 启动 listener

另开终端：

```bash
docker run --rm -it \
    --name ros2-listener \
    --network=host \
    --ipc=host \
    -e ROS_DOMAIN_ID=0 \
    humanoidnav:v1.0 \
    bash -lc '
        source /opt/ros/humble/setup.bash
        ros2 run demo_nodes_cpp listener
    '
```

如果 listener 持续收到消息，说明：

```text
ROS 2 Humble 正常
Fast DDS 正常
host 网络正常
容器间 DDS 发现正常
```

------

至此，Thor Ubuntu 24.04 宿主机上的 Ubuntu 22.04 + ROS 2 Humble root 开发容器已经构建完成。
