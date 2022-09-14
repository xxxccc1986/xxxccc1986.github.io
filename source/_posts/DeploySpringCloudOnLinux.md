---
title: 如何在Linux上部署SpringCloud项目？
date: 2022-09-14 21:50:02
updated: 2022-09-14 21:50:02
tags:
  - Linux
  - Centos7
  - SpringCloud
categories:
  - 其他
keywords: SpringCloud Centos7
cover: https://w.wallhaven.cc/full/9m/wallhaven-9m5y98.png
copyright_author: Year21
copyright_url: http://year21.top/2022/09/14/DeploySpringCloudOnLnux
---

# 部署前的准备工作

1.服务器：服务器选择的是华为云的服务器，配置如下

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220912230737438.png)

2.部署之前需要提前在服务器上安装jdk、mysql、nacos、redis、nginx、node.js

具体工具的安装步骤可移至 --> [Centos7安装工具](http://year21.top/2022/08/17/centos/)  按照需要进行安装

# 后端

## 工程打包

①前提准备

- 工程目录

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220913003920071.png)

- 各个模块关系分析

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220912174125487.png)

②将被依赖但不需要打包的模块在install到本地maven仓库

③配置父工程的pom文件

~~~xml
<!-- 修改打包为pom -->
<groupId>top.year21.onlineedu</groupId>
<artifactId>online_edu_parent</artifactId>
<packaging>pom</packaging>
<version>1.0-SNAPSHOT</version>

<!-- 确认子模块 -->
<modules>
    <module>service</module>
    <module>common_apis</module>
    <module>online_edu_gateway</module>
</modules>

<!-- 指定java版本号和设置打包字符集 -->
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <java.version>1.8</java.version>
</properties>   

<!-- 只需在最顶层父工程pom文件配置apache的maven打包插件，
     service的其他父模块不需要再次配置 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
                <source>${java.version}</source>
                <target>${java.version}</target>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.22.1</version>
            <configuration>
                <skipTests>true</skipTests>    <!--默认关掉单元测试 -->
            </configuration>
        </plugin>
    </plugins>
</build>
~~~

④配置子工程pom文件，<font color='red'>**只以一个举例，其他都是如此**</font>

- 这一步需要注意一个点 :

  如果此子模块有启动类且需要被其他模块依赖，还需加上这句代码（不加会报找不到类的错误）

~~~xml
<configuration>
    <!-- 表示此工程需要被依赖，需打出两个jar包，其中真正执行的是名字带有exec的jar包 -->
    <classifier>exec</classifier>
    <!-- 指定该Main Class为全局的唯一入口 -->
    <mainClass>top.year21.onlineedu.gateway.Gateway8010</mainClass>
    <layout>ZIP</layout>
</configuration>
~~~

~~~xml
<!-- 配置父工程pom文件位置 -->
<parent>
    <artifactId>online_edu_parent</artifactId>
    <groupId>top.year21.onlineedu</groupId>
    <version>1.0-SNAPSHOT</version>
    <!-- 父工程pom文件位置 -->
    <relativePath>../pom.xml</relativePath> 
</parent>

<!-- 修改打包为jar -->
<artifactId>online_edu_gateway</artifactId>
<packaging>jar</packaging>

<!-- 单独配置每个需要打包的子工程的springboot打包插件，只需要在有启动类的子模块中配置 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <!-- 指定Main Class为全局的唯一入口 -->
                <mainClass>top.year21.onlineedu.gateway.Gateway8010</mainClass>
                <layout>ZIP</layout>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <!--可以把依赖的包都打包到生成的Jar包中-->
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
~~~

⑤单独测试各个jar接口测试是否正常

## 工程部署

- 本次没有采用Jenkins持续化自动部署

①将通过父工程打包完成的jar包放到一个文件夹中，通过xftp上传到服务器上

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220913001951720.png)

②在服务器上逐个使用nohup指令后台启动

③通过tail -f nohup.out来查看每个jar的启动状态

建议启动顺序如下(否则有可能因为启动先后顺序导致出现找不到目标服务的错误)：

gateway -- acl -- oss -- vod --usercenter -- statistic -- edu -- comment -- order -- cms -- msm

# 前端

## 工程打包与部署

- 打包管理系统前端工程

①npm run build(此命令默认打包的是prod环境配置) 

如需要打包指定环境参考 --> [Vue分环境打包](https://blog.csdn.net/qq_30631063/article/details/107164254)

②再通过xftp将打包的dist文件夹上传至服务器

③在服务器上nginx进行代理设置

~~~tex
server{
    listen    9528;
    server_name  localhost;

    location / { 
      root /opt/server/dist;
      index index.html index.htm;
      try_files $uri $uri/ /index.html;	
    }   
	
    location /api/ {
      proxy_pass http://localhost:8010/;
    }
}
~~~

- 打包用户前台前端工程

①npm run build

②进入工程根目录将打包好的.nuxt、static、nuxt.config.js、package.json文件放入

一个新创建的文件夹，随便什么名字都行，这里设置定义为nuxt

③再通过xftp将nuxt文件夹上传至服务器

④在服务器的nuxt文件夹根目录执行npm install 安装依赖

​	依赖安装完成之后，npm start 启动测试 无报错代表启动成功

⑤在服务器上nginx进行代理设置

- 本次部署较为简单，选择直接监听80端口，将访问80端口的请求转发到

  3000端口，即为nuxt项目端口，其中访问80端口路径中带有api的请求

  则转发到负责转发请求的8010网关模块中，由网关转发至对应的微服务模块

~~~tex
server {
        listen       80;
        server_name  localhost;
        
        location / {
          proxy_pass http://localhost:3000/;
        }

        location /api/{
          proxy_pass http://localhost:8010/;
        }
}
~~~

⑥上述只是前台启动成功，总不可能让这个进程一直在挂着，因此需要安装pm2进程管理

第一步：全局安装pm2进程管理 --> npm install -g pm2

第二步：进入上传到服务器的nuxt文件夹 

第三步：启动服务 pm2 start node_modules/nuxt/bin/nuxt.js  --name  'onlineedu' 

此处的onlineedu 为你当前项目package.json中的name

- 麻了，linux本地npm start能成功启动并访问，换到pm2死活都不能后台启动nuxt工程，

  直接报错找不到插件 Error: Plugin not found，无奈只能使用nohup指令

# 部署途中的问题

1.启动nacos出现 db.num is null 问题

需要以单机模式启动nacos -->  ./start.sh -m standalone

2.打包管理系统前端项目时出现BASE_URL is not defined

ERROR in Template execution failed: ReferenceError: BASE_URL is not defined

需要将项目根目录的index.html文件中的所有BASE_URL替换

~~~html
<!-- 替换前 --> 
<script src=<%= BASE_URL %>/tinymce4.7.5/tinymce.min.js></script>

<!-- 替换后 --> 
<script src=<%= htmlWebpackPlugin.options.url %>/tinymce4.7.5/tinymce.min.js></script>
~~~

3.单个jar在linux上启动需要占用较大内存，需要指定jvm参数来改善这一情况

~~~tex
nohup java -Xms128m -Xmx128m -jar online_edu_gateway-1.0-SNAPSHOT.jar &
~~~

4.尝试后台启动nuxt项目出现 pm2: command not found

解决方法就是需要给pm2程序添加软连接，以便全局使用

①find / -name pm2 找出pm2的安装路径

②建立软连接

③验证连接是否成功

~~~tex
[root@ecs-327760 nuxt]# find / -name pm2
/usr/local/node/node_global/bin/pm2 --> 这个就是pm2的安装路径
/usr/local/node/node_global/lib/node_modules/pm2
/usr/local/node/node_global/lib/node_modules/pm2/pm2
/usr/local/node/node_global/lib/node_modules/pm2/bin/pm2
/usr/local/node/node_global/lib/node_modules/pm2/lib/templates/logrotate.d/pm2
[root@ecs-327760 nuxt]# ln -s /usr/local/node/node_global/bin/pm2 /usr/local/bin --> 建立软连接
[root@ecs-327760 nuxt]# pm2 --version
[PM2] Spawning PM2 daemon with pm2_home=/root/.pm2
[PM2] PM2 Successfully daemonized
5.2.0 --> 出现这个代表软连接建立成功
[root@ecs-327760 nuxt]# 
~~~

5.大概率版本原因导致pm2后台启动nuxt工程报找不到插件的问题，

无奈之下决定使用linux自带的后台启动nohup的方式

① nohup npm start > ./_out.log &  

②回车 使用exit退出当前客户端，否则当前后台启动的nuxt工程也会结束

---

# 一些废话

这个项目感觉是真的很多坑，无论是项目代码的编写还是部署方面，总有一些莫名其妙的bug。大概率上都是依赖版本的问题，

比如后端工程的权限管理模块整合完不显示动态菜单，明明在url上路由已经渲染成功，侧边菜单栏硬是不渲染，属实折磨了

两天时间，最后通过各种查资料得知当时那个版本的动态路由指令没挂载上后端返回的路由值。还有上面的pm2无法启动nuxt

工程，明明linxu本地npm start 成功启动，到了pm2就是报找不到插件，在github上查询过，说是依赖问题，npm install安装

了对应的版本的依赖也没能解决，属实不理解为什么npm start能找到插件，pm2就是找不到，不太熟悉这些前端框架的问题

只能被迫放弃了pm2启动。

本来打算使用Jenkins + docker来部署的，但奈何技术太菜，不会写docker脚本，加上也需要逐个构建工程，就算了，最后还是

打算使用手动部署的方式，回看部署的前前后后，似乎springcloud微服务的部署也没那么麻烦对吧，哈哈。写到这里，想想还

有一个半月差不多就结束了这个学期的课程，进入到找实习的路途当中，从1月份决定学习java，到如今9月份，历经8个月，生

活都被这玩意占满，空余时间几乎都拿来学习了，而如今网上浏览信息，都在说java各种卷，科班各种找不到工作，也不知道

当初选择学习java是不是一个错误的决定。罢了，小作文就写到这吧，希望努力最终能有所回报吧。

