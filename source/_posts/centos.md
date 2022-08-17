---
title: Centos7安装mysql与jdk
date: 2022-08-17 19:50:02
updated: 2022-08-17 19:50:02
tags:
  - Centos7
  - Mysql
  - Jdk
categories:
  - 其他
keywords: Centos7 Mysql Jdk
cover: https://w.wallhaven.cc/full/rd/wallhaven-rdkjx1.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/08/17/centos
---

# Centos 7 安装 mysql

- 步骤

~~~text
1、到官方网站需要下载的指定版本
本次以Centos7为例，因此选择Red Hat Enterprise Linux 7 / Oracle Linux 7(x86,64-bit)
https://downloads.mysql.com/archives/community/
2、同通过xftp或者虚拟机vmshare功能上传到linux服务器上指定目录 本次上传到/opt目录
3、解压mysql-8.0.26-1.el7.x86_64.rpm-bundle
tar -xvf mysql-8.0.26-1.el7.x86_64.rpm-bundle
4、安装解压出来的这些选项，其余的不用管
mysql-community-common
mysql-community-libs
mysql-community-client
mysql-community-server
5.依次安装上述的包
rpm -ivh mysql-community-common-8.0.26-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-libs-8.0.26-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-client-8.0.26-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-server-8.0.26-1.el7.x86_64.rpm --nodeps --force
6、查看已安装的mysql资源
rpm -qa | grep mysql
7、出现下列列表即为安装成功
mysql-community-libs-8.0.26-1.el7.x86_64
mysql-community-client-8.0.26-1.el7.x86_64
mysql-community-server-8.0.26-1.el7.x86_64
mysql-community-common-8.0.26-1.el7.x86_64
8、初始化数据库
mysqld --initialize
chown mysql:mysql /var/lib/mysql -R
systemctl start mysqld.service
systemctl enable mysqld
9、查看数据库的初始密码，命令如下；
cat /var/log/mysqld.log | grep password
[Server] A temporary password is generated for root@localhost: eak!5f-odUkE
10、使用root用户登录mysql，输入初始密码
mysql -uroot -p
11、修改root密码
alter user "root"@"localhost" identified by "root";
12、通过以下命令，进行远程访问的授权
create user 'root'@'%' identified with mysql_native_password by '1qaz@2wsx'; 
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;  --立即生效
13、开放3306端口或者直接关闭防火墙(不推荐) 并重载防火墙。
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload
14、最后通过sqlyog连接即可
~~~

# Centos 7 安装 jdk

- 安装

1、执行 rpm -qa | grep java 查看已安装的JDK

2、执行 sudo yum remove java-1.* 删除已安装的openJDK

3、解压安装包	tar -zxvf jdk-8u291-linux-x64.tar.gz 

4、重命名解压出来的文件	mv jdk1.8.0_291/ jdk

5、移动到别的地方	mv jdk /usr/local/

6、修改配置文件	vim /etc/profile

7、进行jdk的环境配置

export JAVA_HOME=/usr/local/jdk

export CLASSPATH=$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

export PATH=$JAVA_HOME/bin:$PATH

8、配置完成之后需要重新刷新配置 否则java-version是没提示的

source /etc/profile





