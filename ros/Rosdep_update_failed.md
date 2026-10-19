# Rosdep update failed 

## Action1：

```
sudo pip install rosdepc 
```

如果显示没有pip可以尝试pip3： 

```
sudo pip3 install rosdepc 
```

or 

```
sudo apt-get install python3-pip  sudo pip install rosdepc 
```

使用 

```
sudo rosdepc init  rosdepc update 
```

finished！！ 



## **Action2** 

### **1.首先将下面仓库的内容clone到本地** 

```
git clone https://github.com/ros/rosdistro.git 
```

如果git clone 速度较慢，可以直接拷贝[https://github.com/ros/rosdistro.git](https://github.com/ros/rosdistro.git) 到网页下载，速度提高记录rosdistro存放地址，例如/home/gec/rosdistro 

如果是自己的改成/home/user/rosdistro，其中user表示用户名 

 

### **2.修改/usr/lib/python2.7/dist-packages/rosdep2/rep3.py文件** 

```
cd /usr/lib/python2.7/dist-packages/rosdep2 
sudo gedit rep3.py 
```

将REP3_TARGETS_URL = ‘https://raw.githubusercontent.com/ros/rosdistro/master/releases/targets.yaml’ 

替换成 REP3_TARGETS_URL = 'file:///home/gec/rosdistro/releases/targets.yaml' 

替换后的/home/gec 即为第一步clone内容的存放地址 

 

### **3.修改/usr/lib/python2.7/dist-packages/rosdistro/__init__.py文件** 

```
cd /usr/lib/python2.7/dist-packages/rosdistro
sudo gedit __init__.py 
```

将原来文件中的 DEFAULT_INDEX_URL = ‘https://raw.githubusercontent.com/ros/rosdistro/master/index-v4.yaml’ 

替换成 DEFAULT_INDEX_URL =  'file:///home/gec/rosdistro/index-v4.yaml' 

替换后的/home/gec 即为第一步clone内容的存放地址 

 

### **4.配置20-default.list文件** 

```
sudo rosdep init 
```

重新生成/etc/ros/rosdep/sources.list.d/20-default.list. 

没有则手动创建/etc/ros/rosdep/sources.list.d/20-default.list.步奏如下： 

如果sudo rosdep init成功，直接跳过以下创建文件步骤------- 

```
# 进入到/etc/ros/目录下 
cd /etc/ros   

# 创建rosdep文件 
sudo mkdir rosdep && cd rosdep 

# 创建sources.list.d文件 
sudo mkdir sources.list.d && cd sources.list.d 

# 创建20-default.list文档 
sudo gedit 20-default.list 
```

如果sudo rosdep init成功，直接跳过以上创建文件步骤------ 

将20-default.list里面内容修改为下面的代码,注意内容/home/gec修改为自己的记录路径 

```
# os-specific listings first 
yaml file:///home/gec/rosdistro/rosdep/osx-homebrew.yaml osx 

# generic 
yaml file:///home/gec/rosdistro/rosdep/base.yaml
yaml file:///home/gec/rosdistro/rosdep/python.yaml
yaml file:///home/gec/rosdistro/rosdep/ruby.yaml 
gbpdistro file:///home/gec/rosdistro/releases/fuerte.yaml fuerte 

# newer distributions (Groovy, Hydro, ...) must not be listed anymore, they are being fetched from the rosdistro index.yaml instead  
```

注意的是yaml file:// 表示固定格式/home...表示文件目录，所以是yaml file:/// 

 

### 5.最后直接  

```
rosdep update 
```

