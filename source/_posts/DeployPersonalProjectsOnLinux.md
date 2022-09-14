---
title: 如何在Linux上部署SpringBoot项目？
date: 2022-07-07 23:56:30
updated: 2022-07-07 23:56:30
tags:
  - Linux
  - Centos7
categories:
  - 其他
keywords: Linux Centos7
cover: https://w.wallhaven.cc/full/d5/wallhaven-d59deo.png
copyright_author: Year21
copyright_url: http://year21.top/2022/07/07/DeployPersonalProjectsOnLinux
---

## 部署前的准备工作

1.服务器：服务器选择的是腾讯云centos7系统的轻量服务器

![服务器配置](https://s1.ax1x.com/2022/07/07/j0yUrq.png)



2.通过xshell + xftp 连接linux服务器和上传所需的软件包

①上传jdk8的linux安装包并解压安装 -->  [官方下载网址](https://www.oracle.com/java/technologies/javase/javase8u211-later-archive-downloads.html)

~~~tex
//解压安装包
tar -zxvf jdk-8u291-linux-x64.tar.gz 

//重命名解压出来的文件
mv jdk1.8.0_291/ jdk1.8

//移动到别的地方
mv jdk1.8 /usr/local/

//修改配置文件
vim /etc/profile

//进行jdk的环境配置
export JAVA_HOME=/usr/local/jdk1.8
export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
export PATH=$PATH:$JAVA_HOME/bin/

//配置完成之后需要重新刷新配置 否则java-version是没提示的
source /etc/profile
~~~

②安装docker 参考菜鸟教程  --> [centos7安装docker](https://www.runoob.com/docker/centos-docker-install.html)

可以使用自动或手动安装，这里选择手动安装

~~~tex
//清除docker旧版本信息
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
 //设置docker仓库
 sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
  
 //可以再利用下面的命令设置稳定的仓库源
 //阿里云源
 sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
//安装最新版本的 Docker Engine-Community 无脑y就完事了
sudo yum install docker-ce docker-ce-cli containerd.io

//启动docker
systemctl start docker

//验证是否启动成功
docker version

//出现下列信息代表安装成功
[root@5583880182_woiden etc]# docker version
Client: Docker Engine - Community
 Version:           20.10.17
 API version:       1.41
 Go version:        go1.17.11
 Git commit:        100c701
 Built:             Mon Jun  6 23:05:12 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.17
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.11
  Git commit:       a89b842
  Built:            Mon Jun  6 23:03:33 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.6
  GitCommit:        10c12954828e7c7c9b6e0ea9b0c02b01407d3ae1
 runc:
  Version:          1.1.2
  GitCommit:        v1.1.2-0-ga916309
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

//Docker需要用户具有sudo权限，为了便捷 可以将用户加入docker用户组
sudo usermod -aG docker $USER

//查看docker的镜像
docker images

//设置docker开机自启
systemctl enable docker

//至此docker安装完成
~~~

③使用docker安装mysql

~~~tex
//安装mysql 拉取指定版本的mysql镜像
docker pull mysql:8.0.26

//设置mysql
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=chen  -d mysql:8.0.26

//如果是已经存在的容器 可以通过这个命令启动
docker start 容器id

//进入容器e675e2f0643aa3af3c54653383ddb1244bc90186a19bbfdd0f64dd2d6e662671是mysql的id 
//可以通过docker ps -a 可以查看得到 
docker exec -it e675e2f0643a /bin/bash

//想进入mysql必须先进入docker容器
mysql -uroot -pchen
~~~

③nginx的linux安装包  -->  [官方下载网址](http://nginx.org/en/download.html)

~~~tex
//安装nginx之前需要先安装相关的依赖和库
yum -y install gcc-c++ zlib-devel openssl-devel libtool

//进入usr/local目录
cd /usr/local

//通过命令下载nginx安装包
wget http://nginx.org/download/nginx-1.22.0.tar.gz

//解压nginx安装包
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

## 开始部署

1.打包后端项目

进入后端项目所在的文件夹 进行打包 打包方式可以 war或者jar包 视情况而定

~~~tex
//打包项目
mvn clean package -DskipTests
~~~

2.将打包的jar先通过xftp上传到服务器中

①cd进入jar包所在的目录，使用java -jar springboot-0.0.1-SNAPSHOT 检查后台是否正常

②检查正常则设置该jar为后台启动

~~~tex
//设置后台启动
nohup java -jar springboot-0.0.1-SNAPSHOT.jar &

//通过nohup的日常查看是否启动成功
tail -f nohup.out

//查看当前jar运行的端口号与如何结束进程
ps -ef | grep java

kill -p pid进程号
~~~

3.后端一切正常，则开始打包前端vue项目

①进入前端项目所在的文件夹 进行打包

<font color='red'>**PS:在打包之前需要将所有跟本地相关的东西替换成线上的东西**</font>

~~~tex
//打包项目
npm run build

//在cmd中使用前端插件 测试项目
anywhere -p 8080
~~~

②再通过xftp将打包的dist文件夹上传至服务器

③在服务器上nginx进行代理设置

~~~tex
//进入nginx文件夹的conf文件
//修改nginx.conf的内容
//[root@5583880182_woiden conf]# vim nginx.conf
location / {
	root /home/server/dist;
	index index.html index.htm;
	try_files $uri $uri/ /index.html;
}

//配置跨域代理
location /api/ {
	proxy_pass http://localhost:9090/;
}

//修改完成后需要将nginx重新启动
[root@5583880182_woiden conf]# cd ..
[root@5583880182_woiden nginx]# cd sbin
[root@5583880182_woiden sbin]# ./nginx -s reload
~~~

④至此，前端部署完成

## 部署途中的问题

1.查询linux服务器开放的端口发现**防火墙尚未开启**

需要在dos窗口中通过linux 基础指令进行开启

~~~tex
查看防火墙某个端口是否开放
firewall-cmd --query-port=3306/tcp

开放防火墙端口80
firewall-cmd --zone=public --add-port=80/tcp --permanent

关闭80端口
firewall-cmd --zone=public --remove-port=80/tcp --permanent  

配置立即生效
firewall-cmd --reload 

查看防火墙状态
systemctl status firewalld

关闭防火墙
systemctl stop firewalld

打开防火墙
systemctl start firewalld

开放一段端口
firewall-cmd --zone=public --add-port=8121-8124/tcp --permanent

查看开放的端口列表
firewall-cmd --zone=public --list-ports
~~~

2.提示<font color='red'>**Unit firewalld.service could not be found** </font>

这是说明服务器防火墙没有安装，需要进行安装

~~~tex
yum install firewalld firewall-config
~~~

3.提示<font color='red'> **java.sql.SQLNonTransientConnectionException: Public Key Retrieval is not allowed**</font>

解决方法：

mysql8.x版本的数据库在连接的时候报错java.sql.SQLNonTransientConnectionException: Public Key Retrieval is not allowed

只要在url的后边加上allowPublicKeyRetrieval=true重启即可

~~~yml
url: jdbc:mysql://localhost:3306/springboot_vue?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true&allowPublicKeyRetrieval=true
~~~

4.提示 <font color='red'>**java.sql.SQLException: Access denied for user 'root'@'172.17.0.1' (using password: YES)**</font>

说明：数据库是通过 docker 部署的。容器的 IP 就是这个网段的。根本不用去修改，直接使用localhost就行

解决方法：排查所有代码中关于数据库密码的地方，修改成正确的密码。 通常都是因为密码问题导致的

5.在linux服务器中试图后台运行jar包时，提示Unable to access jarfile springboot-0.0.1-SNAPSHOT

说明：原因未详 如果仅仅使用nohup java -jar springboot-0.0.1-SNAPSHOT.jar 就会报错

解决方法：使用  nohup java -jar springboot-0.0.1-SNAPSHOT.jar --server.port=9090 &

6.远程连接MYSQL错误<font color='red'>**PLUGIN CACHING_SHA2_PASSWORD COULD NOT BE LOADED**</font>

说明：这是由于mysql在8.0版本之后修改了密码校验规则导致的，使用下方方法可以解决

~~~tex
 远程mysql（linux，docker中）
# 如果你需要使用远程登录，将localhost 改为 %，下面的‘xxxx’使用你自己的密码
# 修改加密规则（非必须）
alter user 'root'@'%' identified by 'chen' password expire never;
# 更新用户的密码
alter user 'root'@'%' identified with mysql_native_password by 'xxxx';
# 刷新权限
flush privileges;
# 重置密码（==非必须==）
alter user 'root'@'%' identified by 'xxxx';
~~~

7.在linux上的<font color='red'>**docker中连接mysql发现表中的中文都是？**</font>问号显示，sqlyog的客户端则显示正常。

说明：character_set_connection 是我们通过workbench等客户端连接的时候指定的编码。外部访问数据乱码的问题就出在这个connection连接层上

解决方法：在docker中进入mysql容器，使用这个命令修改即可 ---> SET NAMES 'utf8';

~~~tex
SET NAMES 'utf8'; 

//上面的这个命令等于下面这三个
SET character_set_client = utf8;
SET character_set_results = utf8;
SET character_set_connection = utf8;
~~~

9.前端页面经过nginx代理后能正常访问，但<font color='red'>**登录提示 Json解析异常**</font>

​	SyntaxError: JSON.parse: unexpected character at line 1 column 1 of the JSON data

说明 ：<font color='red'>**这一步大坑，发现是nginx的跨域配置配错了 把/api/ 写成了/api**</font>，需要格外注意(也有写出/api部署成功的，因此解决原因尚未明确)

10.在登录问题解决后，发现图片上传，存在跨域问题

解决方法 后端处理上传的controller类上 匹配@CrossOrigin(origins = "*",maxAge = 3600) 

11.图片部署在项目文件夹无法访问

说明： 一开始项目文件保存的位置是在项目工作的src目录下的，但是经过打包之后路径就变了 导致访问不到

解决方法： ①找到打包后的项目路径	

经后续排查得知：		

```java
 //获取项目在服务器上的绝对路径
    //System.getProperty("user.dir")这个命令可以获取springboot_vue_demo在服务器上的绝对路径
   //这里的地址很关键，别写错了 大坑属实
 String  photoPath= System.getProperty("user.dir") + "/springboot/src/main/resources/static/img/" + filename;
//保存到项目目录的静态资源如图片等，在经过mvn clean package 打包之后在生成的jar中的static目录下
//具体访问路径如下 ：http://ip:端口号/目录/文件名
// e.g. : http://localhost:9090/img/8aa5aad5-242d-476d-b526-b775a431bd6c.jpg
```

​					②这里选择重新修改代码

~~~java
@RestController
@RequestMapping("/file")
@CrossOrigin(origins = "*",maxAge = 3600)  //origins = "*"表示允许所有的请求跨域，处理前端跨域问题，允许value设定的请求进行跨域
public class FileController {

    //从配置文件中进行取值
    @Value("${server.port}")
    private String port;

    @Value("1.14.176.219")
    private String ip;


    @Value("${files.upload.path}")
    private String filePath;
    /**
     * Description : 处理普通文件上传
     **/
    @PostMapping("/upload")
    public Message fileUpload(MultipartFile file) throws IOException {

        //上传文件的原始名称
        String filename = file.getOriginalFilename();

        //获取上传文件的后缀名
        String suffixName = filename.substring(filename.lastIndexOf("."));

        //使用时间戳作为文件的新名字
        String name = UUID.randomUUID().toString();

        //重命名，即为保存服务器上的文件名字
        filename = name + suffixName;

        //先判断要保存图片的文件夹是否存在
        File pictureDir = new File(filePath);
		
        //如果不存在就创建
        //mkdirs() 和 mkdir()的区别就是前者无论上级目录是否存在都会创建，后者如何发现上级目录不存在就不会创建
        if (!pictureDir.exists()){
            pictureDir.mkdirs();
        }
        //创建要保存的图片
        File uploadFile = new File(filePath + filename);

        //进行保存
        //可以直接将文件写入到服务器硬盘上
        file.transferTo(uploadFile);

        return Message.success().add("url","http://" + ip + ":" + port + "/file/down/" + filename); //返回文件下载的url
    }

    /**
     * Description : 处理文件的下载
     **/
    @GetMapping("/down/{name}")
    public ResponseEntity<byte[]> down(@PathVariable("name") String name) throws IOException {
        //获取服务器中文件的真实路径
        File file = new File(filePath + name);
        System.out.println(file);
        String realPath =  file.getAbsolutePath();
        if (realPath != null){
            //创建输入流
            InputStream is = new FileInputStream(realPath);
            //创建字节数组
            //is.available() 这个方法可以在读写操作前先得知数据流里有多少个字节可以读取
            byte[] bytes = new byte[is.available()];
            //将流读到字节数组中
            is.read(bytes);
            //创建HttpHeaders对象设置响应头信息
            HttpHeaders headers = new HttpHeaders();
            //设置要下载方式以及下载文件的名字
            headers.add("Content-Disposition", "attachment;filename=" + URLEncoder.encode(name,"UTF-8"));
            //设置响应状态码
            HttpStatus statusCode = HttpStatus.OK;
            //创建ResponseEntity对象
            ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers, statusCode);
            //关闭输入流
            is.close();
            return responseEntity;
        }
        return null;
    }
~~~

## 随便写点东西

- **留给以后自己需要的时候可以看看**

关于这个项目部署到服务器上的操作，可谓是挫折不断，主要是由于本身对linux系统这些也不是很精通，

导致出现问题后解决花费的时间超乎想象，不过通过这个也算熟悉一下流程吧。

关于部署这个管理系统尝试了两套部署方案，在努力之下终于都部署成功。

**方案一：腾讯云的Centos7 + Docker + Mysql + Nginx + 通过前端和后端分别打包部署**

上面通篇讲的就是这种部署的过程，就不再说明了。

**方案二：Woiden的Centos7 + Docker + Mysql + Apache  通过前端和后端分别打包部署**

这个方案是由于在woiden使用nginx发现80端口无法正常访问，尝试装过宝塔，提示有其他web环节存在，

原因可能是woiden预装了apache，所以有冲突，因此只能被迫选择apache。

粗略记录一下我的部署过程 ：

由于apache的所有网站资源都存放在<font color='red'>/var/www/html</font>目录下，可以选择将dist打包后的文件统一放在这个文件目录下，

再通过apache的httpd.conf文件修改网站的根目录。但我选择的是直接将dist打包好的文件放进html文件夹中，

省去一部分环节，来节省一些时间，但是前端的部署中要解决一个非常重要的环节，

就是如何解决在除了index的页面刷新报404错误问题，经过查阅资料，也最终解决。

贴一下有帮助的资料连接 -->  [资料1](https://www.freesion.com/article/72721294265/)  |  [资料2](t.zoukankan.com/sxshaolong-p-10219527.html)  当时参考这个解决的 | [资料3](https://jingyan.baidu.com/article/ea24bc39adfbeb9b63b3314d.html)

后端的部署和 方案一的部署是一样的，就不说了。至此，部署就完成了。

---

其实还有第三套方案想尝试部署的，直接将前端打包好的dist文件直接放入后端工程的static文件夹下，

直接和整个后端一起部署。但是时间有限，这个项目的编写和部署花费了不少的时间，也许是我比较愚笨吧。

很多事情还没有做，至于这方案三的部署可以留到以后再试试，希望有机会。
