---
title: Maven基础
date: 2022-04-03 20:58:02
updated: 2022-04-03 20:58:02
tags:
  - Maven
categories:
  - Tools
keywords: Maven
cover: https://w.wallhaven.cc/full/72/wallhaven-72k9o3.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/04/03/MavenBase
---

## Maven基础

Maven是由Apache组织维护的为java项目提供构建和依赖管理(jar包管理)支持的工具

1.构建

  构建是指使用**原材料生产产品**的过程。

- 原材料

  - Java 源代码

  - 基于 HTML 的 Thymeleaf 文件

  - 图片

  - 配置文件

  - [#](http://heavy_code_industry.gitee.io/code_heavy_industry/pro002-maven/chapter01/verse02.html#) ……

- 产品

  - 一个可以在服务器上运行的项目

构建过程包含的主要的环节：

- 清理：删除上一次构建的结果，为下一次构建做好准备
- 编译：Java 源程序编译成 *.class 字节码文件
- 测试：运行提前准备好的测试程序
- 报告：针对刚才测试的结果生成一个全面的信息
- 打包
  - Java工程：jar包
  - Web工程：war包
- 安装：把一个 Maven 工程经过打包操作生成的 jar 包或 war 包存入 Maven 仓库
- 部署
  - 部署 jar 包：把一个 jar 包部署到 Nexus 私服服务器上
  - 部署 war 包：借助相关 Maven 插件（例如 cargo），将 war 包部署到 Tomcat 服务器上

2.依赖

如果 A 工程里面用到了 B 工程的类、接口、配置文件等等这样的资源，那么我们就可以说 A 依赖 B。

- 例如junit-4.12 依赖 hamcrest-core-1.3

3.Maven的工作机制

![](https://s1.ax1x.com/2022/04/03/qHpnk8.png)

---

### 核心概念：向量

1.向量说明

使用三个**『向量』**在**『Maven的仓库』**中**唯一**的定位到一个**『jar』**包。

- **groupId**：公司或组织的 id
- **artifactId**：一个项目或者是项目中的一个模块的 id
- **version**：版本号

2.三个向量的取值方式

- groupId：公司或组织域名的倒序，通常也会加上项目名称
  - 例如：com.atguigu.maven
- artifactId：模块的名称，将来作为 Maven 工程的工程名
- version：模块的版本号，根据自己的需要设定
  - 例如：SNAPSHOT 表示快照版本，正在迭代过程中，不稳定的版本
  - 例如：RELEASE 表示正式版本

举例：

- groupId：com.atguigu.maven
- artifactId：pro01-atguigu-maven
- version：1.0-SNAPSHOT

3.坐标和仓库中 jar 包的存储路径之间的对应关系

~~~xml
 <groupId>javax.servlet</groupId>
  <artifactId>servlet-api</artifactId>
  <version>2.5</version>
~~~

上面坐标对应的 jar 包在 Maven 本地仓库中的位置是：**Maven本地仓库根目录\javax\servlet\servlet-api\2.5\servlet-api-2.5.jar**

---

### dos窗口生成一个Maven-java工程

- 在dos窗口运行 **mvn archetype:generate** 命令

> dos 窗口生成中的信息填写
>
> Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): 7:【直接回车，使用默认值】
>
> Define value for property 'groupId': com.atguigu.maven
>
> Define value for property 'artifactId': pro01-maven-java
>
> Define value for property 'version' 1.0-SNAPSHOT: :【直接回车，使用默认值】
>
> Define value for property 'package' com.atguigu.maven: :【直接回车，使用默认值】
>
> Confirm properties configuration: groupId: com.atguigu.maven artifactId: pro01-maven-java version: 1.0-SNAPSHOT package: com.atguigu.maven Y: :【直接回车，表示确认。如果前面有输入错误，想要重新输入，则输入 N 再回车。】

![命令的格式解析](https://s1.ax1x.com/2022/04/03/qHpGmq.png)

pom.xml文件的配置信息及其含义

~~~xml

<!-- project根标签：表示对当前工程进行配置、管理 -->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <!-- modelVersion 标签：从Maven 2 开始就固定是4.0.0 -->
  <!-- 代表当前的pom.xml所采用的结构 -->
  <modelVersion>4.0.0</modelVersion>

<!-- 坐标信息 -->
<!-- groupId 标签：代表公司或者组织开发的某一个项目 -->
  <groupId>top.year21.maven</groupId>
  
  <!-- artifactId 标签：代表项目下的某一个模块 -->
  <artifactId>pro01-maven-java</artifactId>
  
  <!-- version 标签：代表当前模块的版本 -->
  <version>1.0-SNAPSHOT</version>
  
  <!-- packaging 标签：代表当前工程的打包方式 -->
  <!-- 取值 jar：生产jar包，说明这是个java工程 -->
  <!-- 取值 war：生产war包，说明这是个web工程 -->
  <!-- 取值 pom：说明这个工程用来管理其他工程的工程-->
  <packaging>jar</packaging>

  <name>pro01-maven-java</name>
  <url>http://maven.apache.org</url>

  <!-- properties 标签：在Maven中定义属性值 -->
  <properties>
  <!-- 在构建过程中读取源码时使用的字符集 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

	<!-- dependencies 标签：配置具体的依赖信息，可以包含多个dependency 子标签 -->
  <dependencies>
   <!-- dependency 标签：配置具体的某一个依赖信息 -->
    <dependency>
		
		<!-- 坐标信息：想要导入哪个jar包，就配置它的坐标信息即可 -->
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
	  
	  <!-- scope 标签：配置当前依赖的范围 -->
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>

~~~

### 核心概念：POM

1.含义

POM：**P**roject **O**bject **M**odel，项目对象模型。和 POM 类似的是：DOM（Document Object Model），文档对象模型。它们都是模型化思想的具体体现。

2.模型化思想

POM 表示将工程抽象为一个模型，再用程序中的对象来描述这个模型。这样我们就可以用程序来管理项目了。我们在开发过程中，最基本的做法就是将现实生活

中的事物抽象为模型，然后封装模型相关的数据作为一个对象，这样就可以在程序中计算与现实事物相关的数据。

3.对应的配置文件

POM 理念集中体现在 Maven 工程根目录下 **pom.xml** 这个配置文件中。所以这个 pom.xml 配置文件就是 Maven 工程的核心配置文件。其实学习 Maven 就是学

这个文件怎么配置，各个配置有什么用。

### dos窗口下的构建

1.运行 Maven 中和构建操作相关的命令时，必须进入到**pom.xml 所在的目录**。如果没有在 pom.xml 所在的目录运行 Maven 的构建命令，那么会看到下面的错误信息：

```java
The goal you specified requires a project to execute but there is no POM in this directory
```

>  mvn -v 命令和构建操作无关，只要正确配置了 PATH，在任何目录下执行都可以。而构建相关的命令要在 pom.xml 所在目录下运行——操作哪个工程，就进入这个工程的 pom.xml 目录。

2、清理操作

mvn clean

效果：删除 target 目录

3、编译操作

主程序编译：mvn compile

测试程序编译：mvn test-compile

主体程序编译结果存放的目录：target/classes

测试程序编译结果存放的目录：target/test-classes

4、测试操作

mvn test

测试的报告存放的目录：target/surefire-reports

5、打包操作

mvn package

打包的结果——jar 包，存放的目录：target

6、安装操作

mvn install

```log
[INFO] Installing D:\maven-workspace\space201026\pro01-maven-java\target\pro01-maven-java-1.0-SNAPSHOT.jar to D:\maven-rep1026\com\atguigu\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.jar
[INFO] Installing D:\maven-workspace\space201026\pro01-maven-java\pom.xml to D:\maven-rep1026\com\atguigu\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.pom
```

安装的效果是将本地构建过程中生成的 jar 包存入 Maven 本地仓库。这个 jar 包在 Maven 仓库中的路径是根据它的坐标生成的。

坐标信息如下：

```xml
  <groupId>com.atguigu.maven</groupId>
  <artifactId>pro01-maven-java</artifactId>
  <version>1.0-SNAPSHOT</version>
```

在 Maven 仓库中生成的路径如下：D:\maven-rep1026\com\atguigu\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.jar

另外，安装操作还会将 pom.xml 文件转换为 XXX.pom 文件一起存入本地仓库。所以我们在 Maven 的本地仓库中想看一个 jar 包原始的 pom.xml 文件时，查看

对应 XXX.pom 文件即可，它们是名字发生了改变，本质上是同一个文件。

- dos窗口构建web工程 ---->[参考](http://heavy_code_industry.gitee.io/code_heavy_industry/pro002-maven/chapter03/verse04.html#%E5%AE%9E%E9%AA%8C%E5%9B%9B-%E5%88%9B%E5%BB%BA-maven-%E7%89%88%E7%9A%84-web-%E5%B7%A5%E7%A8%8B)

---

### 依赖

#### 依赖范围

标签的位置：dependencies/dependency/**scope**

标签的可选值：**compile**/**test**/**provided**/system/runtime/**import**

①compile 和 test 对比

|         | main目录（空间） | test目录（空间） | 开发过程（时间） | 部署到服务器（时间） |
| ------- | ---------------- | ---------------- | ---------------- | -------------------- |
| compile | 有效             | 有效             | 有效             | 有效                 |
| test    | 无效             | 有效             | 有效             | 无效                 |

②compile 和 provided 对比

|          | main目录（空间） | test目录（空间） | 开发过程（时间） | 部署到服务器（时间） |
| -------- | ---------------- | ---------------- | ---------------- | -------------------- |
| compile  | 有效             | 有效             | 有效             | 有效                 |
| provided | 有效             | 有效             | 有效             | 无效                 |

③结论

compile：通常使用的第三方框架的 jar 包这样在项目实际运行时真正要用到的 jar 包都是以 compile 范围进行依赖的。比如 SSM 框架所需jar包。

test：测试过程中使用的 jar 包，以 test 范围依赖进来。比如 junit。

provided：在开发过程中需要用到的“服务器上的 jar 包”通常以 provided 范围依赖进来。比如 servlet-api、jsp-api。而这个范围的 jar  包之所以不参与部署、不放进 war 包，就是避免和服务器上已有的同类 jar 包产生冲突，同时减轻服务器的负担。说白了就是：“**服务器上已经有了，你就别带啦！**”

#### 依赖的传递

①概念

A 依赖 B，B 依赖 C，那么在 A 没有配置对 C 的依赖的情况下，A 里面能不能直接使用 C？

②传递的原则

在 A 依赖 B，B 依赖 C 的前提下，C 是否能够传递到 A，取决于 B 依赖 C 时使用的依赖范围。

- B 依赖 C 时使用 compile 范围：可以传递
- B 依赖 C 时使用 test 或 provided 范围：不能传递，所以需要这样的 jar 包时，就必须在需要的地方明确配置依赖才可以

#### 依赖的排除

当 A 依赖 B，B 依赖 C 而且 C 可以传递到 A 的时候，A 不想要 C，需要在 A 里面把 C 排除掉。而往往这种情况都是为了避免 jar 包之间的冲突。

![./images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img027.2faff879.png)

所以配置依赖的排除其实就是阻止某些 jar 包的传递。因为这样的 jar 包传递过来会和其他 jar 包冲突

---

### 继承

1.概念

Maven工程之间，A 工程继承 B 工程

- B 工程：父工程
- A 工程：子工程

本质上是 A 工程的 pom.xml 中的配置继承了 B 工程中 pom.xml 的配置。

2、作用

在父工程中统一管理项目中的依赖信息，具体来说是管理依赖信息的版本。

它背后的需求是：

- 在每一个 module 中各自维护各自的依赖信息很容易发生出入，不易统一管理。
- 使用同一个框架内的不同 jar 包，它们应该是同一个版本，所以整个项目中使用的框架版本需要统一。
- 使用框架时所需要的 jar 包组合（或者说依赖信息组合）需要经过长期摸索和反复调试，最终确定一个可用组合。这个耗费很大精力总结出来的方案不应该在新的项目中重新摸索。

通过在父工程中为整个项目维护依赖信息的组合既**保证了整个项目使用规范、准确的 jar 包**；又能够将**以往的经验沉淀**下来，节约时间和精力。

**父类工程建立步骤**：[参考](http://heavy_code_industry.gitee.io/code_heavy_industry/pro002-maven/chapter03/verse09.html#_1%E5%88%9B%E5%BB%BA%E7%88%B6%E5%B7%A5%E7%A8%8B)

### 聚合

1.概念：使用一个“总工程”将各个“模块工程”汇集起来，作为一个整体对应完整的项目。

- 项目：整体
- 模块：部分

从继承关系角度来看：

- 父工程
- 子工程

从聚合关系角度来看：

- 总工程
- 模块工程

2.好处

- 一键执行 Maven 命令：很多构建命令都可以在“总工程”中一键执行。

  以 mvn install 命令为例：Maven 要求有父工程时先安装父工程；有依赖的工程时，先安装被依赖的工程。我们自己考虑这些规则会很麻烦。但是工程聚合之后，在总工程执行 mvn install 可以一键完成安装，而且会自动按照正确的顺序执行。

- 配置聚合之后，各个模块工程会在总工程中展示一个列表，让项目中的各个模块一目了然。

---

### 其他核心概念

#### 生命周期

1.作用

为了让构建过程自动化完成，Maven 设定了三个生命周期，生命周期中的每一个环节对应构建过程中的一个操作。

2.三个生命周期

| 生命周期名称 | 作用         | 各个环节                                                     |
| :----------- | :----------- | :----------------------------------------------------------- |
| Clean        | 清理操作相关 | pre-clean  <br/>clean <br/>post-clean                        |
| Site         | 生成站点相关 | pre-site <br/> site <br/>post-site <br/>deploy-site          |
| Default      | 主要构建过程 | validate <br/>generate-sources <br/>process-sources <br/>generate-resources  <br/>rocess-resources 复制并处理资源文件，至目标目录，准备打包 <br/>compile 编译项目 main 目录下的源代码 <br/>process-classes <br/>generate-test-sources <br/>process-test-sources <br/>generate-test-resources <br/>process-test-resources 复制并处理资源文件，至目标测试目录 <br/>test-compile 编译测试源代码 <br/>process-test-classes <br/>test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署 <br/>prepare-package  <br/>package 接受编译好的代码，打包成可发布的格式，如JAR、pre-integration-test <br/>integration-test <br/>post-integration-test <br/>verify <br/>install将包安装至本地仓库，以让其它项目依赖 <br/>deploy将最终的包复制到远程的仓库，以让其它开发人员共享；或者部署到服务器上运行（需借助插件，例如：cargo）。 |

3.特点

- 前面三个生命周期彼此是独立的。
- 在任何一个生命周期内部，执行任何一个具体环节的操作，都是从本周期最初的位置开始执行，直到指定的地方。（记住这句话就行了）

Maven 之所以这么设计其实就是为了提高构建过程的自动化程度：让使用者只关心最终要干的即可，过程中的各个环节是自动执行的。

#### 插件和目标

①插件

Maven 的核心程序仅仅负责宏观调度，不做具体工作。具体工作都是由 Maven 插件完成的。例如：编译就是由 maven-compiler-plugin-3.1.jar 插件来执行的。

②目标

一个插件可以对应多个目标，而每一个目标都和生命周期中的某一个环节对应。

Default 生命周期中有 compile 和 test-compile 两个和编译相关的环节，这两个环节对应 compile 和 test-compile 两个目标，而这两个目标都是由 maven-

compiler-plugin-3.1.jar 插件来执行的。

#### 仓库

- 本地仓库：在当前电脑上，为电脑上所有 Maven 工程服务
- 远程仓库：需要联网
  - 局域网：我们自己搭建的 Maven 私服，例如使用 Nexus 技术。
  - Internet
    - 中央仓库
    - 镜像仓库：内容和中央仓库保持一致，但是能够分担中央仓库的负载，同时让用户能够就近访问提高下载速度，例如：Nexus aliyun

- 建议：不要中央仓库和阿里云镜像混用，否则 jar 包来源不纯，彼此冲突。
