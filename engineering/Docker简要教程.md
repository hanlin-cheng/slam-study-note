# Docker简要教程

## 1.常用命令

### 1.1镜像操作

镜像是容器执行的前提条件，一般需要掌握命令注意有**搜索**、**下载**、**删除**、**创建**。

```shell
docker images      # 镜像列表
docker search xxx  # 检索镜像, 从镜像仓库中检索
docker pull xxx    # 下载镜像
docker rmi xxx     # 删除镜像
```

### 1.2 容器操作

容器的各种操作主要包括，启动、关闭、重启和日志查询。

#### 1.2.1 容器创建 ：run

加载镜像，创建容器run 后面可以跟很多的参数，比如容器暴露端口指定，存储映射，权限等等，由于参数过多，下面只给出几个不同的例子，来具体的演示参数可以怎么加

```text
docker run
         -i              # 打开 STDIN，用于控制台交互
         -t              # 支持终端登录
         -d              # 要求容器后台运行，默认前台 前三个标签可连写为itd
         -v              # 挂载数据卷
         --name=dc_nm    # 指定容器名
         -p 8080:80      # 暴露容器端口 80，并与宿主机端口 8080 绑定  
         centos:latest   # 镜像名:版本
```

```
docker run -idt --name container_nginx -p 8080:80  docker.io/nginx
```

启动一个使用镜像`docker.io/nginx`，名字`container_nginx`的容器，`-p 8080:80`表示将容器的80端口映射到主机的8080端口，这样我们只要访问主机的8080端口就可以访问到容器的服务了。

**注意：**`name`前面是两个`-`， 端口前面有`-p`， `docker.io/nginx`是镜像名，`8080`是主机的端口，`80`是Nginx应用的端口

> exit 退出容器
>
> docker ps 查看运行中的容器
>
> docker ps -a 查看运行中和非运行中的所有容器
>
> docker exec -it container_nginx /bin/bash 进入容器
> 如果容器还未启动 执行 docker start container_nginx

#### 1.2.2 基本操作:

容器本操作主要包括启动、停止、重启、删除

```bash
docker ps -a                       # 查看容器列表， 列出所有的容器
docker exec -it "ct_id" /bin/bash  # 进入容器,ct_id是容器id
docker start xxx                   # xxx可以是容器名，也可以是容器id
docker stop xxx                    # 关闭容器
docker restart xxx                 # 重启
docker rm xxx                      # 删除
```

从宿主机复制文件

```bash
docker cp 宿主机文件路径 容器id:/home/your_path
```

##### 删除容器

容器删除之前先将容器停止

`docker stop container_nginx` 或者是容器的id

`docker rm -f container_nginx` 容器删除

## 2.**docker start 与 docker run 的区别**

`docker start name` 启动一个已经创建的容器

`docker run` 创建并启动一个容器

`docker run` 命令其实是 `docker create` 和 `docker start` 的命令组合，先执行`docker create` 创建一个容器 再接着`docker start`启动

## 3.**镜像的导入与导出**

镜像压缩打包 (主机上进行操作)，有两种方式 `docker save` 与 `docker load` 和 `docker export 与 docker import`

```text
docker save nginx | gzip > nginx_xin_image.tar.gz  将现有的镜像压缩打包

docker load -i nginx_xin_image.tar.gz  压缩的镜像解压

docker images 进行查看
docker save` 是直接将镜像进行打包 `docker save <镜像名>或<镜像id>
docker export container_nginx> nginx_image.tar

cat nginx_image.tar | sudo docker import  - nginx_image:import
```

**`docker export` 是直接将容器进行打包 `docker export <容器名>或<容器id>`**

**需要注意两种方法配套的，切不可混用。虽然导入导出时没问题，但是在创建容器时候会报错**

如果使用import导入save产生的文件，虽然导入不提示错误，但是启动容器时会提示失败，

会出现类似"docker: Error response from daemon: Container command not found or does not exist"的错误。

类似，使用load载入export产生的文件，也会出现问题。

### 参考链接

https://zhuanlan.zhihu.com/p/640805236

https://zhuanlan.zhihu.com/p/366666596?utm_id=0