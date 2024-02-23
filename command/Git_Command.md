# Git Command

[参考教程]: https://www.liaoxuefeng.com/wiki/896043488029600

> `git clone` 默认是克隆`Head`指向的`master`分支，如果是多分支，我们可以单个克隆分支项目。
>
> 1.只克隆单分支（非master）：
>
>  	git clone -b 分支名 https://xxx.git
>
> 2.克隆所有分支（多分支）
>
>  	cd project  //切换到指定目录
>  	git clone https://xxx.git //克隆项目（默认master分支）
>
> ​	git branch -a  //列出远程跟踪及本地分支

## 1.安装并设置地址

```shell
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```



## 2.创建版本库

```shell
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit

$ git init
```



## 3.版本管理

- ### 添加文件


```shell
$ git add readme.txt
$ git commit -m "wrote a readme file"
```



- ### 查看工作区状态


```shell
$ git status
```



- ### 查看提交日志


```shell
$ git log
```



- ### 查看历史命令


```shell
$ git reflog
```



- ### 版本间穿梭


```shell
$ git reset --hard commit_id
```

expmple:

​	git reset --hard HEAD^

​	git reset --hard 1094a



- ### 撤销修改


场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，或者删除了工作区某个文件，用命令

```shell
$ git checkout -- <file>
```

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步

```shell
$ git reset HEAD <file>
$ git checkout -- <file>
```

`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本间穿梭](#版本间穿梭)一节，不过前提是没有推送到远程库。



- ### 删除文件


```shell
$ git rm <file>
$ git commit -m "remove test.txt"
```



## 4.远程仓库

- #### 第1步：


创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```shell
$ ssh-keygen -t rsa -C "youremail@example.com"
```



- #### 第2步：


登陆GitHub，打开“Account settings”，“SSH Keys”页面。然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容



- #### 第3步：


登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库



- #### 第4步：


根据GitHub的提示，在本地的`learngit`仓库下运行命令（请千万注意，把上面的`michaelliao`替换成你自己的GitHub账户名）：

```shell
$ git remote add origin git@github.com:michaelliao/learngit.git
```



### 上传远程库

- #### 第5步：


就可以把本地库的所有内容推送到远程库上（添加后，远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库）：

```shell
$ git push -u origin master
Counting objects: 20, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (20/20), 1.64 KiB | 560.00 KiB/s, done.
Total 20 (delta 5), reused 0 (delta 0)
remote: Resolving deltas: 100% (5/5), done.
To github.com:michaelliao/learngit.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令：

```shell
$ git push origin master
```



### 删除远程库

如果添加的时候地址写错了，或者就是想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息：

```shell
$ git remote -v
origin  git@github.com:michaelliao/learn-git.git (fetch)
origin  git@github.com:michaelliao/learn-git.git (push)
```

然后，根据名字删除，比如删除`origin`：

```shell
$ git remote rm origin
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。



## 5.分支管理

### 查看分支

```shell
git branch
```

### 创建分支

```shell
git branch <name>
```

### 切换分支

```shell
git checkout <name> 
or
git switch <name>
```

### 创建+切换分支

```shell
git checkout -b <name>
or
git switch -c <name>
```

### 合并某分支到当前分支

```shell
git merge <name>
```

### 删除分支

```shell
git branch -d <name>
```

可以通过`git branch -D <name>`强行删除

### 用带参数的`git log`也可以看到分支的合并情况

```shell
git log --graph --pretty=oneline --abbrev-commit
```



## 6.多人协作

### 查看远程库信息

```shell
git remote -v
```

本地新建的分支如果不推送到远程，对其他人就是不可见的；

### 从本地推送分支

```shell
git push origin branch-name
```

如果推送失败,先用`git pull`抓取远程的新提交

### 在本地创建和远程分支对应的分支

```shell
git checkout -b branch-name origin/branch-name
```

本地和远程分支的名称最好一致

### 建立本地分支和远程分支的关联

```shell
git branch --set-upstream-to <branch-name> origin/<branch-name>
```

### 多人协作的工作模式

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`



## 7.标签管理

### 创建标签

```shell
$ git tag <tagname>
```

默认标签是打在最新提交的commit上的,也可以指定一个commit id

```shell
$ git tag v0.9 f52c633
```

### 创建带有说明的标签

用`-a`指定标签名，`-m`指定说明文字

```shell
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
```

### 查看所有标签

```shell
$ git tag
v1.0
```

### 查看标签信息

标签不是按时间顺序列出，而是按字母排序的。`git show <tagname>`

```shell
$ git show v0.9
commit f52c63349bc3c1593499807e5c8e972b82c8f286 (tag: v0.9)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:56:54 2018 +0800

    add merge

diff --git a/readme.txt b/readme.txt
...
```

### 删除标签

```shell
$ git tag -d <tagname>
```

### 推送标签到远程

```shell
$ git push origin <tagname>
```

一次性推送全部尚未推送到远程的本地标签

```shell
$ git push origin --tags
```

### 删除远程标签

```shell
$ git tag -d <tagname>															 //删除一个本地标签
$ git push origin :refs/tags/<tagname>							//删除一个远程标签
```

## 8.配置代理

### 查看全局配置

```shell
$ git config --global -l
```

### 配置代理

```shell
$ git config --global http.proxy 127.0.0.1:7890
```

### 取消代理

```shell
$ git config --global --unset http.proxy
```

## 8.其他命令

### git stash

#### （1）使用场景：

当我们在某一条分支上开发新功能时，突然有个紧急的错误需要修复

这时，我们不得不暂停手头上的工作，切换到另外的分支去修复错误

但是，新功能做到一半，既不能提交，也不能删除，那该怎么办呢

我们可以先把当前的更改保存起来，等处理完错误后再恢复出来，git stash 就是这样的一个用法

它可以将工作区和缓存区的更改保存到一个栈结构中，等后面需要的时候再恢复

#### （2）基本用法

##### 保存：`git stash`

```shell
> # 将当前工作区和暂存区的更改保存到一个栈结构
$ git stash
> # 将当前工作区和暂存区的更改保存到一个栈结构，并附带一个信息
$ git stash save "message"
$ # 将当前工作区和暂存区的更改保存到一个栈结构，包括新增的文件
$ git stash -u
$ git stash --include-untracked
> # 将当前工作区和暂存区的更改保存到一个栈结构，包括新增的文件以及忽略的文件
$ git stash -a
$ git stash --all
```

##### 查看栈中保存的更改：`git stash list`

```shell
> # 查看栈中保存的更改
$ git stash list
```

##### 查看更改的具体内容：`git stash show`

```shell
> # 查看栈中第一个更改的具体内容
$ git stash show
> # 查看栈中指定的更改的具体内容
$ git stash show <stash id>
```

##### 恢复：`git stash apply`

```shell
> # 将栈中的第一个更改恢复到当前工作区和暂存区
$ git stash apply
> # 将栈中的指定的更改恢复到当前工作区和暂存区
$ git stash apply <stash id>
```

##### 删除：`git stash drop`

```shell
> # 删除栈中的第一个更改
$ git stash drop
> # 删除栈中的指定的更改
$ git stash drop <stash id>
```

##### 恢复 & 删除：`git stash pop`

```shell
> # 将栈中的第一个更改恢复到当前工作区和暂存区，同时删除栈中的第一个更改
$ git stash pop
> # 将栈中的指定的更改恢复到当前工作区和暂存区，同时删除栈中的指定的更改
$ git stash pop <stash id>
```

##### 清空：`git stash clear`

```shell
> # 清空栈中保存的更改
$ git stash clear
```

