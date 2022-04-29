---
title: Java环境安装
date: 2022-02-24 16:14:30
updated: 2022-02-24 16:14:30
tags:
  - Java
categories:
  - Java
cover: https://w.wallhaven.cc/full/z8/wallhaven-z8odwg.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/02/24/Java环境安装/

---

## Java开发环境安装

### JDK的下载与安装

> 目前JDK版本已更新到17代，本次下载以JDK8，即1.8为例（新版与旧版的区别在于增加了一些新特性）

- JDK的下载 

1. 通过百度或者谷歌搜索JDK8 根据电脑版本进行下载
2. jdk版本更新迭代较大 官网有可能找不到jdk8的下载 在此提供官方备份的下载地址    [jdk8下载](https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html)

- JDK的安装

1. 下载完成后打开进行下一步
2. 需要注意记住当前jdk8安装的路径，环境配置需要用上  (下载路径根据个人喜好自主选择)

---

### JDK的环境配置

- 我的电脑–>右键–>高级系统设置
- 下面的环境变量新建–>变量名为JAVA_HOME，变量值填入jdk8安装的文件路径
- 找到环境变量下的Path–>双击打开–>新建两个新变量   %JAVA_HOME%\bin  %JAVA_HOME%\jre\bin 需要注意大小写
- 通过cmd检测JDK是否安装成功（命令行中输入java -version验证）

cmd窗口出现以下界面即为环境配置成功

![演示图片](https://s4.ax1x.com/2022/02/24/bFA92D.png)

---

### 关于JDK的卸载

1. 首先删除java的安装目录
2. 删除环境变量种的JAVA_HOME
3. 删除path下关于java的变量
4. 在cmd命令行中输入java version，若提示不是内部命令，则删除成功