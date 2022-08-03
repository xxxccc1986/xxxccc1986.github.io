---
title: Nginx
date: 2022-08-03 21:45:02
updated: 2022-08-03 21:45:02
tags:
  - Nginx
categories:
  - 中间件
keywords: Nginx
cover: https://s1.ax1x.com/2022/08/03/vZbRFe.png
copyright_author: Year21
copyright_url: http://year21.top/2022/08/03/nginx
---

# nginx介绍

1.nginx的概念

nginx专为性能优化而开发，它也是高性能的 HTTP 和反向代理的服务器，处理高并发的能力非常好

2.正向/反向代理

<font color='red'>**正向代理**</font>代理的对象是客户端，<font color='red'>**反向代理**</font>代理的对象是服务端

3.负载均衡

<font color='red'>**将从客户端发来的请求分发到多个服务器上**</font>，将原先请求集中到单个服务器上的情况

改为将请求分发到多个服务器上，将负载分发到不同的服务器

- 负载均衡的体现![](https://s1.ax1x.com/2022/08/03/vZHjr6.png)

4.动静分离

动静分离简单来说就是将动态请求和静态请求进行分开，把动态页面和静态页面的请求由不同的

服务器来解析，加快解析速度，以降低原来单个服务器的压力



![](https://s1.ax1x.com/2022/08/03/vZHvqK.png)

---

# 安装nginx

1.进入nginx官网  -->  [官方下载网址](http://nginx.org/en/download.html)

~~~tex
//安装nginx之前需要先安装相关的依赖和库
yum -y install gcc-c++ zlib-devel openssl-devel libtool

//进入usr/local目录
cd /usr/local

//通过命令下载nginx安装包
wget http://nginx.org/download/nginx-1.22.0.tar.gz

//解压nginx安装包
//z是解压的工具，v是显示解压的信息，x是解压操作，f指定解压缩文件
tar -zxvf nginx-1.22.0.tar.gz

//删除nginx安装包 可选择的
rm -rf nginx-1.22.0.tar.gz

//进入nginx目录
cd /usr/local/nginx-1.22.0

//配置nginx
./configure --prefix=/usr/local/nginx

//安装nginx
make && make install

//返回上级目录就能看到一个nginx文件夹所有东西都安装在这个文件夹中
//此时可以将之前解压出来的文件夹删除掉
rm -rf nginx-1.22.0

//进入/usr/local/nginx/sbin目录，输入./nginx即可启动nginx
./nginx

//通过命令查看是否启动
ps -ef |grep nginx
~~~

---

# 使用nginx

## nginx常用命令

- <font color='red'>**使用nginx的命令前提是必须进入nginx的/usr/local/nginx/sbin目录**</font>

~~~text
1.查看nginx的版本
./nginx -v
2.启动nginx 
./nginx 
3.关闭nginx
./nginx -s stop
4.重启nginx
./nginx -s reload
~~~

## nginx配置文件

- <font color='red'>**nginx配置文件的位置个人习惯安装在此位置/usr/local/nginx/conf/nginx.conf**</font>

1.nginx的配置文件由三部分组成

①全局块

从配置文件开始到 events 块之间的内容，主要会设置一些<font color='red'>**影响 nginx 服务器整体运行的配置指令**</font>，

主要包括配置运行Nginx服务器的用户（组）、允许生成的 worker process 数，进程PID存放路径、

日志存放路径和类型以及配置文件的引入等。

worker_processes  1; Nginx并发处理服务的关键配置，值越大，其并发处理能力越强

②event块

events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接

worker_connections  1024; 表示每个 work process 支持的最大连接数为 1024.

③http块

- 代理、缓存和日志定义等大部分功能都在这里配置。且http块又包括http 全局块、server 块。

Ⅰ.http全局块内容包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。

Ⅱ.server 块

-  每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。

-  而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

全局 server 块

 最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。

location 块

 一个 server 块可以配置多个 location 块。

 这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），

对虚拟主机名称（也可以是 IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，

对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置。

## nginx配置实例

准备工作：下载tomcat与jdk(暂时使用默认内置的jdk)

- tomcat的安装与使用

~~~text
1.安装tomcat(两种方式)
方式一：通过官网下载 https://tomcat.apache.org/ 下载对应的tar.gz版本
方式二：wget  https://mirror.bit.edu.cn/apache/tomcat/tomcat-9/v9.0.39/bin/apache-tomcat-9.0.39.tar.gz
2.解压下载的tar压缩包
tar -zxvf apache-tomcat-9.0.65.tar.gz 
3.删除tar压缩包
rm -rf apache-tomcat-9.0.65.tar.gz 
4.移动指指定文件夹并重命名
mv apache-tomcat-9.0.65/ /opt/tomcat
5.启动tomcat，先进入bin目录
cd /opt/tomcat/bin
./startup.sh
6.返回上一级目录进入logs目录，可以查看启动日志
tail -f catalina.out 
~~~

### 反向代理

1.示例一 使用 nginx 反向代理，访问 test.dzsc.tk 直接跳转到 127.0.0.1:8080

![请求过程](https://s1.ax1x.com/2022/08/03/vZbSaD.png)

在nginx.conf设置反向代理配置

~~~java
location / {
    root   html;
    index  index.html index.htm;
    proxy_pass http://localhost:8080;
}
~~~

2.示例二 使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中

nginx 监听端口为 9001，

访问 http://localhost:9001/edu/ 直接跳转到 localhost:8080

访问 http://localhost:9001/vod/ 直接跳转到 localhost:8081

- 需要准备两个tomcat(与前面tomcat安装一样)以及两个页面

在nginx.conf设置反向代理配置

<font color='red'>**需要注意的是所有端口都需要在防火墙进行开放**</font>

~~~text
server {
        listen       9001;
        server_name  localhost;

        location /edu/ {
            proxy_pass http://localhost:8080;
        }

        location /vod/ {
            proxy_pass http://localhost:8081;
        }
    }
~~~

**location 指令说明** 

 该指令用于匹配 URL。

 语法如下：location [= | ~ | ~* | ^~ ] uri {}

1、= ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配

成功，就停止继续向下搜索并立即处理该请求。

 2、~：用于表示 uri 包含正则表达式，并且区分大小写。

 3、~*：用于表示 uri 包含正则表达式，并且不区分大小写。

 4、^~：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字

符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 

块中的正则 uri 和请求字符串做匹配。

 注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~* 标识。

### 负载均衡

1.示例一

浏览器地址栏输入地址 http://localhost/edu/a.html ，负载均衡效果，平均8080和8081端口中

在 nginx 的配置文件中进行负载均衡的配置

~~~text
//myserver就是自定义的一个服务名，对外暴露服务，而服务下有两台机器，自动实现轮询访问
upstream myserver{
		ip_hash;
        server localhost:8080 weight=2;
        server localhost:8081 weghit=1;
        fair;
    }

server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
            proxy_pass http://myserver;
     }
~~~

- nginx分配服务器策略

第一种 轮询（默认）

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

第二种weight

weight代表权重默认为1,权重越高被分配的客户端越多

第三种ip_hash

每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器

第四种fair（第三方）

按后端服务器的响应时间来分配请求，响应时间短的优先分配。

### 动静分离

- 动静分离示意

![](https://s1.ax1x.com/2022/08/03/vZbpIe.png)

通过 location 指定不同的后缀名实现不同的请求转发。通过 expires 参数设置，可以使浏

览器缓存过期时间，减少与服务器之前的请求和流量。具体 Expires 定义：是给一个资源

设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，

所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件，

不建议使用 Expires 来缓存），我这里设置 3d，表示在这 3 天之内访问这个 URL，发送一个

请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码 304，

如果有修改，则直接从服务器重新下载，返回状态码 200。

- 在nginx.conf设置动静分离配置

   autoindex on;这个设置会在访问/image/页面时列出文件夹中的所有资源内容

~~~text
 location /www/ {
            root   /data/;
            index  index.html index.htm;
        }

  location /image/ {
            root   /data/;
            autoindex on;
        }
~~~

### 高可用集群

**高可用集群解决的问题是什么？当只有一台nginx服务器时，宕机后会导致所有请求失败**

![](https://s1.ax1x.com/2022/08/03/vZbCPH.png)

- 配置高可用集群步骤

①使用两台服务器安装两台nginx

②安装keepalived 	yum install keepalived –y

​	查看keepalived版本	rpm -q -a keepalived

安装之后，在etc里面生成目录keepalived，有文件 keepalived.conf

③完成高可用的文件配置(主从配置)

修改/etc/keepalived/keepalivec.conf配置文件

并在当前目录下执行 chmod 644 keepalived.conf 给予权限

~~~text
# 全局定义
global_defs {
  notification_email {
    acassen@firewall.loc
    failover@firewall.loc
    sysadmin@firewall.loc
  }
  notification_email_from Alexandre.Cassen@firewall.loc
  smtp_server 192.168.231.134
  smtp_connect_timeout 30
  # 路由id
  router_id LVS_DEVEL
  # 设置运行脚本默认用户和组。如果没有指定，则默认用户为keepalived_script(需要该用户存在)，否则为root用户。默认groupname同username
  script_user root
  # 如果脚本路径的任一部分对于非root用户来说，都具有可写权限，则不会以root身份运行脚本
  enable_script_security
}
# 检测Nginx是否还存活的脚本
vrrp_script check_nginx {
  # 脚本文件的位置
  script "/usr/local/src/nginx_check.sh"
  interval 2 # 检测脚本执行的间隔
  weight 2 # 设置当服务器的权重
}
# 虚拟IP的配置
vrrp_instance VI_1 {
  state MASTER # 表示当前服务器是MASTER还是BACKUP
  interface ens33 # 绑定的网卡名称
  virtual_router_id 51 # 主、备机的virtual_router_id必须相同
  priority 100 # 主、备机取不同的优先级，主机值较大，备份机值较小
  advert_int 1 # 每隔多久发送一次心跳，默认是1秒
  # 权限校验
  authentication {
    auth_type PASS
    auth_pass 1111
  }
  # 虚拟IP地址
  virtual_ipaddress {
    192.168.231.150
  }
  # 指定监控脚本
  track_script {
    # 这里的check_nginx对应上面的vrrp_script的名字
    check_nginx
  }
}
~~~

在/usr/local/src 添加检测脚本

并在当前目录下执行 chmod 744 /usr/local/src/nginx_check.sh 给予权限

~~~text
#!/bin/bash
if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
  /usr/local/nginx/sbin/nginx
  sleep 2
  if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
    systemctl stop keepalived
  fi
fi
~~~

④把两台服务器上nginx和keepalived启动

启动nginx：./nginx

启动keepalived：systemctl start keepalived.service

---

# nginx原理

- master的工作流程

![](https://s1.ax1x.com/2022/08/03/vZbPGd.png)

- worker的工作流程

![](https://s1.ax1x.com/2022/08/03/vZbiRA.png)

- 一个master和多个woker的好处

①可以使用nginx –s reload热部署，利用nginx进行热部署操作

②每个woker是独立的进程，如果有其中的一个woker出现问题，其他woker独立的，

​	继续进行争抢，实现请求过程，不会造成服务中断

- worker数和服务器的cpu数相等是最合适的

- 发送请求，占用了 woker 的2 或者 4 个连接数(worker_connection)

①请求的是静态资源的话nginx会直接请求静态资源服务器，一来一回只需要2个连接数

②而如果请求的是非静态以及涉及数据库查询，因为work是不支持java的，就需要

再发送请求到tomcat，一来一回又多占用2个请求，所以是4个连接数

- nginx有一个master，有多个woker，每个woker支持最大连接数为1024，则最大并发数是？

普通的静态访问最大并发数是： worker_connections * worker_processes /2


如果是HTTP作 为反向代理那最大并发数量是 worker_connections * worker_processes/4

