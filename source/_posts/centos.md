---
title: Centos7安装工具
date: 2022-08-17 19:50:02
updated: 2022-08-17 19:50:02
tags:
  - Centos7
  - mysql
  - docker
categories:
  - 其他
keywords: Centos7 Linux jdk redis nacos node.js git docker
cover: https://w.wallhaven.cc/full/rd/wallhaven-rdkjx1.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/08/17/centos
---

# 安装 mysql

- 步骤

~~~text
1、到官方网站需要下载的指定版本
本次以Centos7为例，因此选择Red Hat Enterprise Linux 7 / Oracle Linux 7(x86,64-bit)
https://downloads.mysql.com/archives/community/
2、同通过xftp或者虚拟机vmshare功能上传到linux服务器上指定目录 本次上传到/opt目录
3、解压mysql-8.0.26-1.el7.x86_64.rpm-bundle
tar -xvf mysql-8.0.26-1.el7.x86_64.rpm-bundle.tar
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
11、修改root密码 new password为需要设置的新密码
alter user "root"@"localhost" identified by "new password";
12、通过以下命令，进行远程访问的授权
create user 'root'@'%' identified with mysql_native_password by 'new password'; 
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;  --立即生效
13、开放3306端口或者直接关闭防火墙(不推荐) 并重载防火墙。
firewall-cmd --zone=public --add-port=3306/tcp --permanent
firewall-cmd --reload
14、最后通过sqlyog连接即可
~~~

# 安装 jdk

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

# 安装 redis

- 安装redis

~~~tex
//安装C 语言的编译环境
yum install centos-release-scl scl-utils-build
yum install -y devtoolset-8-toolchain
scl enable devtoolset-8 bash
//测试 gcc版本 
gcc --version
//将安装包redis-6.2.1.tar.gz放/opt目录
//解压安装包
tar -zxvf redis-6.2.1.tar.gz
//解压完成后进入目录：
cd redis-6.2.1
//在redis-6.2.1目录下再次执行make命令（只是编译好）
make
//继续执行
make install
//redis的安装位置在/usr/local/bin 可进入这个目录测试是否安装成功
//备份redis.conf别处以备日后出问题最快还原
cp /opt/redis-6.2.1/redis.conf /etc
//进入/etc，修改刚刚备份的redis.conf
vim redis.conf
//后台启动设置daemonize no改成yes
//至此redis安装完成
~~~

记录一下，太久没用过都给忘了怎么用了

虚拟机中redis的安装目录：usr/local/bin

且将此文件夹中的redis.conf文件复制了一份到/etc文件下，redis的启动需要指定/etc/下的redis.conf

- 启动redis

①进入redis的安装目录 

cd /usr/local/bin

②启动redis，需指定启动的redis.conf文件

./redis.server /etc/redis.conf

③连接redis客户端

./redis-cli

⑤查询当前redis中所有缓存的数据

keys * 

⑥获得指定缓存的数据

get key的名称

# 安装 nacos

①在github的nacos官方处下载nacos的安装包

②通过xftp将安装包上传至服务器

③tar -zxvf nacos安装包名

④进入/nacos/bin目录，以单机模式启动nacos(除非你多个nacos，否则不以单机模式启动就会报错)

./start.sh -m standalone

⑤访问naocs(记得放行服务器8848端口)

htytp://ip:8848/nacos 默认账户密码都是nacos

# 安装 node.js

~~~tex
//从官方网站下载对应的node.js源码 https://nodejs.org/zh-cn/download/
//通过xftp等工具上传到服务器上 ，一般是上传到/usr/local处，看个人喜好
//或 wegt wget https://registry.npmmirror.com/-/binary/node/latest-v12.x/node-v16.17.0-linux-x64.tar.gz
//将tar.xz解压成tar文件
xz -d node-v16.17.0-linux-x64.tar.xz
//将tar文件解压成文件夹
tar -xvf node-v16.17.0.tar.gz
//重命名
mv node-v16.17.0 node
//进入node文件下的bin目录检测是否安装成功
./node -v
//建立软连接，将node源文件映射到usr/bin下的node文件
ln -s /usr/local/node/bin/node /usr/bin/node 
ln -s /usr/local/node/bin/npm /usr/bin/npm
//进入/usr/local/node/路径下:
mkdir node_global
mkdir node_cache
npm config set prefix "node_global"
npm config set cache "node_cache"
//安装cnpm并切换到国内镜像(要使用cnpm命令 同样需要建立软连接,方法同之前一样)
npm install -g cnpm --registry=https://registry.npm.taobao.org
//建立cnpm软连接
ln -s /usr/local/node/bin/cnpm /usr/local/bin/
//将npm切换到国内镜像
npm config set registry https://registry.npm.taobao.org
//查看npm源地址
npm config get registry
//至此 安装完成
~~~

# 安装 maven

~~~tex
//可以在服务器上下载maven包或本地下载传到服务器上，本次选择的是本地下载后上传
//官方下载地址 https://maven.apache.org/download.cgi
wget https://dlcdn.apache.org/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.tar.gz
//将Maven压缩包解压到目标目录
tar -xvf apache-maven-3.8.6-bin.tar.gz
// 配置Maven环境变量
vim /etc/profile
//在配置文件末尾加上maven环境变量
export MAVEN_HOME=/opt/apache-maven-3.8.6
export PATH=$PATH:$MAVEN_HOME/bin
//使配置文件立即生效
source /etc/profile
//使用查看maven版本命令查看maven安装情况，如正确出现maven版本则表示安装成功
mvn -version
// 进入maven安装目录和配置文件路径
cd /opt/apache-maven-3.8.6/conf
// 编辑maven的配置文件
vim settings.xml
//配置阿里云仓库,在settings.xml文件找到 <mirrors>的结束标签，在结束标签之前添加下列配置
<mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>
    <url>https://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
//配置依赖下载位置，进入maven安装目录和配置文件路径
cd /opt/apache-maven-3.8.6/conf
//编辑maven的配置文件
vim settings.xml
//在注释了的<localRepository>标签下添加下列配置
<localRepository>/opt/apache-maven-3.8.6/localRepo</localRepository>
//至此 安装完成
~~~

# 安装 git

~~~tex
//有两种方式yum安装和本地下载传到服务器上，本次选择的是本地下载后上传
//官方下载地址 https://github.com/git/git/tags
yum install git
//将git压缩包解压到目标目录
tar -zxvf git-2.37.3.tar.gz
//将git移动到指定位置
mv git-2.37.3 /usr/local
//进入文件夹
cd /usr/local/git-2.37.3/
//编译和安装，安装完成后在上层目录会有一个git文件夹
make configure
./configure --prefix=/usr/local/git
make profix=/usr/local/git
make install
//创建配置文件,输入下列第二行内容
vim /etc/profile.d/git.sh
export PATH=$PATH:/usr/local/git/bin
//执行命令
chmod +x /etc/profile.d/git.sh
source /etc/profile.d/git.sh
//验证是否安装成功，如正确出现maven版本则表示安装成功
git --version
//至此 安装完成
~~~

# 安装 docker

~~~tex
//更新yum包 非必要步骤
yum -y update
//卸载旧版本（如果之前安装过的话）
yum remove docker  docker-common docker-selinux docker-engine
//安装需要的依赖
yum install -y yum-utils device-mapper-persistent-data lvm2
//设置yum源，下面两个任选其一
yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo（中央仓库）
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo（阿里仓库）
//查看docker可用版本
yum list docker-ce --showduplicates | sort -r
//选择一个版本并安装：yum install docker-ce-版本号
yum -y install docker-ce-19.03.9-3.el7
//启动 Docker 并设置开机自启
systemctl start docker
systemctl enable docker
//验证是否安装成功，如正确出现docker版本则表示安装成功
docker version
//至此 安装完成
~~~

# 安装 jenkins

- Jenkins是一个持续化部署工具，可自动化打包部署项目工程
- 安装 Jenkins需要先安装jdk、maven、git、docker
- 下面记录一下Jenkins的安装和使用，以备以后需要可以参考

Linux部署Jenkins的两种方法，下面着重介绍使用war包的方式

~~~tex
//jenkins的rpm/war包下载地址：http://mirrors.jenkins-ci.org/
//打开链接后，首行是系统版本名称，Releases行是短期更新包，LTS是长期更新包。
//将本地下载的war包传到服务器上
//运行jenkins.war,默认端口8080
java -jar /usr/local/jenkins.war
//或者指定8888端口启动，防止端口占用的情况
java -jar /usr/local/jenkins.war --httpPort=8888  
//后台启动方式
nohup java -jar /usr/local/jenkins.war --httpPort=8888 & 
//关闭防火墙
stop firewalld
//查看防火墙状态
systemctl status firewalld
//默认访问地址是：http://x.x.x.x:port，首次登录一般需要密码，密码通过下方指令查询
cat /root/.jenkins/secrets/initialAdminPassword 
//安装插件之前先配置下载源，国外下载源不一定能下载成功
#进入更新配置位置
cd /root/.jenkis/updates  
//键入此命令
sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
//随后重启war包，安装默认的插件即可
//插件安装完成后，设置登录的用户账户和密码
//登录之后需要设置maven、jdk、git的环境
//至此 安装完成
~~~

YUM安装的方式，有点麻烦就不再赘述了。





