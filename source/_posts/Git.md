---
title: Git
date: 2022-04-24 10:46:02
updated: 2022-04-24 10:46:02
tags:
  - Git
categories:
  - Tools
keywords: Git教程
cover: https://s1.ax1x.com/2022/04/24/Lh57tI.gif
copyright_author: Year21
copyright_url: http://year21.top/2022/04/24/Git
---

## Git

Git是一个免费的、开源的<font color='orange'>**分布式版本控制系统**</font>。

版本控制：记录文件修改的历史记录，以便后续的查阅和修改以及方便版本切换

![Git的结构](https://s1.ax1x.com/2022/04/24/Lh4qL4.png)

---

### Git的常用命令

![常用命令](https://s1.ax1x.com/2022/04/24/Lh5iOe.png)

- git切换版本，其底层实际是<font color="orange">**移动的HEAD指针**</font>，通过git reset --hard 这一命令不断改变master对不同版本的指针指向

![image-20220421221308215切换版本](https://s1.ax1x.com/2022/04/24/Lh5mfP.png)

---

### Git分支操作

分支：在版本控制过程中，使用多条线同时推进多个任务

1.分支的作用：

①同时并行推进多个功能开发，提高开发效率 

②各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。

2.分支的常用命令

| 命令名称            | 作用                         |
| :------------------ | :--------------------------- |
| git branch 分支名   | 创建分支                     |
| git branch -v       | 查看分支                     |
| git checkout 分支名 | 切换分支                     |
| git merge 分支名    | 把指定的分支合并到当前分支上 |

- 合并分支会出现的特殊情况：产生冲突

  合并分支时，两个分支在同一个文件的同一个位置有两套完全不同的修改。

  Git 无法替 我们决定使用哪一个。必须<font color="red">**人为决定**</font>新代码内容。

​		解决方法：编辑有冲突的文件，删除特殊符号，决定要使用的内容

- master、hot-fix其实都是指向具体版本记录的指针。当前所在的分支，其实是由 HEAD 决定的。

---

## GitHub操作

### 远程仓库的操作

| 命令名称                           | 作用                                                      |
| :--------------------------------- | :-------------------------------------------------------- |
| git remote -v                      | 查看当前所有远程地址别名                                  |
| git remote add 别名                | 远程地址 起别名                                           |
| git push 别名 分支                 | 推送本地分支上的内容到远程仓库                            |
| git clone 远程地址                 | 将远程仓库的内容克隆到本地                                |
| git pull 远程库地址别名 远程分支名 | 将远程仓库对于分支最新内容拉下来后与 当前本地分支直接合并 |
| git clone -b "分支名称" 远程地址   | 拉取远程指定分支下的代码                                  |
| git branch -a                      | 查看全部分支（包括本地和远程）                            |

pull拉取的特殊情况：

- pull 则是将**远程主机的master**分支最新内容拉下来后与当前本地分支直接合并

​		①当要拉取的远程分支名和本地分支名不一致时

​			使用  git pull <远程主机名> <远程分支名>:<本地分支名>

​		②当要拉取的远程分支名和本地分支名相同时，“:”冒号后面的内容可以省略

​			使用  git pull <远程主机名> <远程分支名>

---

## IDEA集成Git

①配置 Git 忽略文件

②在IDEA中定位Git程序 

- <font color="red">**github和gitee的操作几乎是一样的**</font>

- <font color="red">**实操啥的太长了在此不做记录了，无他，多用就能掌握**</font>

## IDEA集成GitLab

- GitLab 是由 GitLabInc.开发，使用 MIT 许可证的基于网络的 Git 仓库管理工具，且具有 wiki 和 issue 跟踪功能。

搭建GitLab服务器的步骤

①服务器准备

②准备安装包 

https://packages.gitlab.com/gitlab/gitlab-ce/packages/el/7/gitlab-ce-13.10.2-ce.0.el7.x86_64.rpm

直接将此包上传到服务器/opt/module 目录下即可

③编写安装脚本

~~~txt
[root@gitlab-server module]# vim gitlab-install.sh
sudo rpm -ivh /opt/module/gitlab-ce-13.10.2-ce.0.el7.x86_64.rpm
sudo yum install -y curl policycoreutils-python openssh-server cronie
sudo lokkit -s http -s ssh
sudo yum install -y postfix
sudo service postfix start
sudo chkconfig postfix on
curl https://packages.gitlab.com/install/repositories/gitlab/gitlabce/script.rpm.sh | sudo bash
sudo EXTERNAL_URL="http://gitlab.example.com" yum -y install gitlabce

给脚本增加执行权限
[root@gitlab-server module]# chmod +x gitlab-install.sh
[root@gitlab-server module]# ll
总用量 403104
-rw-r--r--. 1 root root 412774002 4 月 7 15:47 gitlab-ce-13.10.2-
ce.0.el7.x86_64.rpm
-rwxr-xr-x. 1 root root 416 4 月 7 15:49 gitlab-install.sh

然后执行该脚本，开始安装 gitlab-ce。注意一定要保证服务器可以上网。
[root@gitlab-server module]# ./gitlab-install.sh 
警告：/opt/module/gitlab-ce-13.10.2-ce.0.el7.x86_64.rpm: 头 V4 
RSA/SHA1 Signature, 密钥 ID f27eab47: NOKEY
准备中... ################################# 
[100%]
正在升级/安装...
 1:gitlab-ce-13.10.2-ce.0.el7 
################################# [100%]

~~~

④启动 GitLab 服务 执行以下命令启动 GitLab 服务，如需停止，执行 gitlab-ctl stop

 [root@gitlab-server module]# gitlab-ctl start 

ok: run: alertmanager: (pid 6812) 134s 

ok: run: gitaly: (pid 6740) 135s 

ok: run: gitlab-monitor: (pid 6765) 135s

⑤使用浏览器访问 GitLab并创建远程库

- <font color="red">**IDEA集成Gitlab的方式与github、gitee大同小异**</font>，除了下面这一步有所变化

![添加gitlab服务器](https://s1.ax1x.com/2022/04/24/Lh5Kl8.png)