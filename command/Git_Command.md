# Git Command

[参考教程]: https://www.liaoxuefeng.com/wiki/896043488029600



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

