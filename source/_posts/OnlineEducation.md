---
title: SpringCloud + Vue + Nuxt + ElementUi 前后端分离开发的B2C项目
date: 2022-09-14 21:45:02
updated: 2022-09-14 21:45:02
tags:
  - SpringCloud
  - Vue
  - Nuxt
  - ElementUi
categories:
  - 个人项目
keywords: SpringCloud Vue Nuxt ElementUi
cover: https://w.wallhaven.cc/full/dp/wallhaven-dp9y1m.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/09/14/OnlineEducation
---

# 项目简介

## 项目功能点

本项目分为了 后台管理系统 和 前台用户系统

- 后台管理系统

1.登录模块，实现对登录者ip和登录地址的显示

2.权限管理模块(基于SpringSecurity框架实现)

①后台权限列表管理：后台管理列表的增删改查

②角色管理：后台管理系统角色的增删改查以及对不同的角色赋于不同查看后台管理列表的权限

③用户管理：登录该系统的用户的增删改查以及为不同用户分配不同角色 

3.讲师管理模块

①讲师列表常规的分页查询、模糊查询、添加、删除、修改等操作

4.课程分类模块

①课程分类的添加(通过easyExcel读取上传的excel表中数据实现)

②使用树形结构展示课程分类的列表以及搜索当前存在的课程分类

5.课程管理模块

①课程列表常规的分页展示、特定查询、添加、删除、修改等操作

②添加课程，通过以下步骤完成：填写课程信息 -->  添加课程大纲 --> 课程信息确认 --> 最终发布

③课程图片和视频添加功能，可以添加课程图片和视频(基于阿里云oss和视频点播实现)

6.统计分析模块

①统计数据生成

②统计数据展示(基于ECharts实现)

7.banner管理模块

①前台banner轮播图列表常规的分页查询、模糊搜索、添加、删除、修改等操作

8.评论管理模块

①所有评论的展示和删除，支持对某一用户、某个讲师、某个课程的评论特定查询

②对被举报评论进行特定处理，可根据评论内容决定删除或置为正常评论

9.订单管理模块

支持对某一用户、某个讲师、某个课程订单的特定查询

①对所有已支付订单的查看

②对所有尚未支付订单的查看

- 前台用户系统

1.首页模块

①轮播图显示 (根据添加时间进行排序)

②显示热门课程(根据点击次数进行排序)

③显示著名讲师(根据排序值间进行排序)

④根据课程名进行模糊搜索

2.注册功能(接入阿里云短信服务以及微信扫码注册) 

3.登录功能 

①手机登录

使用了SSO单点登录，通过JWT框架生成token字符串实现，JWT字符串分为了头信息、有效载荷、签名哈希

②微信扫码登录

通过微信提供的接口获取类似临时票据，通过临时票据再访问微信提供的地址获取访问凭证和扫描人的唯一

标识，根据这两个值最终访问另一个微信提供的接口即可获取扫描人的全部信息

4.名师模块

①名师列表功能

②名师详情功能

5.课程模块

①课程列表功能(包含常规的分页查询)

②课程详情功能(包含课程所有的基本信息以及判断课程是否收费)

③课程视频在线播放(基于阿里云视频点播实现)

④课程购买功能(通过微信支付实现)

⑤课程评论功能

6.用户个人中心模块

①用户可通过已支付订单和未支付订单功能查询账户订单

②个人资料功能和上传头像功能支持用户对自己资料的修改

③通过修改密码功能对密码进行修改

④退出登录功能

## 项目技术点

<font color='red'>**本项目采用微服务架构，前后端分离开发**</font>，B2C商业模式。

- 前端技术

Vue	Element-UI	Node.js(JavaScript运行时环境)	Npm(依赖管理器)

Babel(转码器)	前端模块化技术	Nuxt框架(服务端渲染技术，基于vue实现)

Echarts(图表显示工具)

- 后端技术

SpringBoot	SpringCloud	MyBatisPlus	EasyExcel	SpringSecurity	Redis	Jwt

- 其他技术

阿里云oss	阿里云视频点播服务	阿里云短信服务	微信支付和登录

---

# 环境搭建

- 创建数据库

~~~mysql
CREATE DATABASE IF NOT EXISTS online_edu CHARSET 'utf8';
~~~

- 创建部分数据表

- 创建pom类型父工程online_edu_parent(用于管理依赖版本和放置公共工程)

主要是需要修改父工程pom文件，具体内容在这不做展示

# 公共模块

- <font color='red'>**在online_parent模块下创建子模块common_apis**</font>

## 整合Swagger

- swagger有什么作用？

①生成在线接口文档 ②方便接口测试

- 整合步骤

①在子模块common_apis下创建service_base子模块

②在service_base子模块中创建swagger配置类

~~~java
@Configuration
@EnableSwagger2 //swagger注解
public class SwaggerConfig {

    @Bean
    public Docket webApiConfig(){

        return new Docket(DocumentationType.SWAGGER_2)
            .groupName("webApi")
            .apiInfo(webApiInfo())
            .select()
            .paths(Predicates.not(PathSelectors.regex("/admin/.*")))
            .paths(Predicates.not(PathSelectors.regex("/error.*")))
            .build();

    }

    private ApiInfo webApiInfo(){

        return new ApiInfoBuilder()
            .title("网站-课程中心API文档")
            .description("本文档描述了课程中心微服务接口定义")
            .version("1.0")
            .contact(new Contact("year21", "http://year21.top", "masicjokersic@gmail.com"))
            .build();
    }
}
~~~

- 使用教程

①在service模块下引入servie_base的坐标依赖

在servie模块引入了，在service_edu也能使用

~~~xml
<!--  引入service_base的依赖      -->
<dependency>
    <groupId>top.year21.onlineedu</groupId>
    <artifactId>service_base</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
~~~

②在service_edu主启动类设置包扫描规则，因为默认只扫描主动类所在的包及其子包

~~~java
@SpringBootApplication
@ComponentScan(basePackages = {"top.year21.onlineedu"}) //扫描top.year21.onlineedu包下的所有组件
public class ServiceEduMain8001 {
    public static void main(String[] args){
        SpringApplication.run(ServiceEduMain8001.class,args);
    }
}
~~~

③进行访问测试，测试地址为：http://localhost:工程端口号/swagger-ui.html

## 统一结果返回

- 创建步骤

①在子模块common_apis下创建common_utils子模块

②common_utils子模块中创建返回统一结果的实体类

~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class JsonResult<E> {

    //定义响应码
    private static final Integer SUCCESS = 20000; //成功
    private static final Integer ERROR = 30000; //失败

    private Boolean result;
    private Integer code;
    private String message;
    private E data;

    public JsonResult(Boolean result,String message,E data) {
        if (result){
            this.result = true;
            this.code = SUCCESS;
        }else{
            this.result = false;
            this.code = ERROR;
        }
        this.message = message;
        this.data = data;
    }

    public JsonResult(Boolean result) {
        if (result){
            this.result = true;
            this.code = SUCCESS;
            this.message = "处理成功";
        }else{
            this.result = false;
            this.code = ERROR;
            this.message = "处理失败";
        }
    }

    public JsonResult(Boolean result,Integer code,String message) {
        if (result){
            this.result = true;
            this.code = code;
        }else{
            this.result = false;
            this.code = code;
        }
        this.message = message;
    }
}
~~~

- 使用步骤

①需要使用的工程pom文件中引入此模块依赖

~~~xml
<!--   引入公用数据返回实体类     -->
<dependency>
    <groupId>top.year21.onlineedu</groupId>
    <artifactId>common_utils</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
~~~

②修改控制层返回结果测试

~~~java
//删除讲师
@PostMapping("/delTeacher/{id}")
public JsonResult<Void> deleteTeacher(@PathVariable("id") Integer id){
    boolean result = teacherService.removeById(id);
    return new JsonResult<>(result);
}
~~~

## 统一异常处理

1.在service_base子模块创建一个用于异常处理类

~~~java
//是Controller增强器，作用是给Controller控制器添加统一的操作或处理。
//经常配合@ExceptionHandler来处理异常
@ControllerAdvice
public class GlobalExceptionHandler {

    //全局异常处理方法，当出现异常后就会来执行这个方法，
    @ExceptionHandler(value = {Throwable.class})
    @ResponseBody //@ResponseBody将这个方法信息返回
    public JsonResult<String> globalExceptionHandle(Throwable e){
        return new JsonResult<>(true,"服务出现一点小异常，异常信息是",e.getMessage());
    }

    //特定常处理方法，当出现特定异常后就会来执行这个方法，
    @ExceptionHandler(value = {ArithmeticException.class})
    @ResponseBody //@ResponseBody将这个方法信息返回
    public JsonResult<String> specificExceptionHandle(Throwable e){
        return new JsonResult<>(true,"出现了特定异常，异常信息是",e.getMessage());
    }

    //自定义异常处理方法，当出现自定义异常后就会来执行这个方法，
    @ExceptionHandler(value = {InsertException.class})
    @ResponseBody //@ResponseBody将这个方法信息返回
    public JsonResult<Void> customerExceptionHandle(InsertException e){
        return new JsonResult<>(true,e.getCode(),e.getMessage());
    }
}
~~~

- 记录一下自定义异常创建步骤

①创建一个自定义异常类继承RuntimeException类

这里可以选择直接重写RuntimeException类的方法或者按照下方的方式来

另一种方式：

~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class InsertException extends RuntimeException{
    private Integer code;//状态码
    private String message; //异常信息
}
~~~

②在编写业务过程中只需要抛出这个异常即可

## 统一日志处理

- <font color='red'>**日志级别：ERROR、WARN、INFO、DEBUG**</font>，越往右信息越详细

yml设置日志级别

~~~yml
#设置日志级别
logging:
  level:
    root: info
~~~

- logback日志工具(不仅可以把日志输出到控制台同时输出到文件中)

使用步骤

①删除yml中日志级别和mybatis的日志设置

②在resources文件夹下创建logback-spring.xml文件

# 后台系统

## 前端页面工程

1、使用vue提供的vue-admin-template-master模板文件

vue-admin-template-master模板是基于Vue+element-ui实现的

2、将模板文件解压到VSCode的工作区

3、通过集成终端键入npm install 根据package.json安装依赖

- 目录结构说明

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220820153907585.png)



---

## 新功能开发

1.在router文件夹下的index.js添加路由

~~~javascript
{
    path: '/teacher',
    component: Layout,
    redirect: '/teacher/list',
    name: 'teacher',
    meta: { title: '讲师管理', icon: 'example' },
    children: [
      {
        path: 'list',
        name: '讲师列表',
        component: () => import('@/views/edu/teacher/list'),
        meta: { title: '讲师列表', icon: 'eye' }
      },
      {
        path: 'add',
        name: '添加讲师',
        component: () => import('@/views/edu/teacher/add'),
        meta: { title: '添加讲师', icon: 'tree' }
      }
    ]
  },
~~~

2.在views文件下创建对应页面的vue文件，用于路由转发的引入

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220820193126182.png)

3.在api文件下创建定义需要调用的方法js文件，用于特定页面的调用

~~~javascript
import request from '@/utils/request'

export default {
    //查询讲师列表(条件查询)
    getTeacherList(pageNum,pageSize,voTeacher){
        return request({
            url: '/serviceedu/condition/' + pageNum +"/" + pageSize,
            //url: '/serviceedu/condition/${pageNum}/${pageSize},
            method: 'post',
            //后端使用了@RequestBody接收此参数，必须用data传输
            //data表示把对象转换json进行传输到接口中
            data: voTeacher
        })
    },
    //添加讲师
    addTeacher(voTeacher){
        return request({
            url: '/serviceedu/add',
            method: 'post',
            data: voTeacher
        })
    }
}
~~~

4.在views文件夹下对应的vue文件中引入上方定义的js文件，用于发送业务请求

~~~vue
<script>
//引入需要调用的js文件
import teacher from '@/api/teacher/teacher.js'

export default {
    name: 'list',
    data() { //定义变量和初始值
        return {
            pageNum: 1, //当前页
            pageSize: 5, //每页数量
            voTeacher: { }, //值对象
            list: [],//查询接口后返回的数据
        }
    },
    created(){ //在vue对象创建之后，数据转载之前执行，一般调用methods中定义的方法
        this.getList();
    },
    methods:{ //创建具体的方法，调用teacher.js定义的方法
        //查询讲师列表
        getList(page = 1){
            this.pageNum = page
            teacher.getTeacherList(this.pageNum,this.pageSize,this.voTeacher)
                    .then(response => {
                        this.list = response.data.records
                    })
                    .catch(error => {
                        console.log(error);
                    })
        },
    }
}
</script>
~~~

5.最后通过element-ui组件库实现对数据的展示

~~~vue
<template>
<div class="app-container">
    讲师列表
    <!-- 数据展示 -->
  <el-table
    :data="list"
    border
    style="width: 100%">
    <el-table-column
      sortable
      prop="id"
      label="id"
      width="180">
    </el-table-column>
    <el-table-column
      prop="name"
      label="姓名"
      width="180">
    </el-table-column>
    <el-table-column
      prop="intro"
      label="简介">
    </el-table-column>
    <el-table-column
      prop="career"
      label="头衔">
    <template slot-scope="scope"> <!--  slot-scope="scope" 可以获取数据-->
        {{scope.row.level === 1?'高級讲师':'首席讲师'}}
    </template>
    </el-table-column>
    <el-table-column
      prop="gmtCreate"
      label="添加时间">
    </el-table-column>
  </el-table>
</div>
</template>
~~~

---

## nginx反向代理

考虑到前端页面请求后端多个微服务，前端请求地址不可能固定某个微服务端口，使用nginx反向代理完成请求

- 步骤(本次使用的是虚拟机)

①为了安全起见，没有选择监听80端口，选择监听9001端口

②添加转发规则

~~~conf
server {
	listen       9001;
	server_name  localhost;

	location ~ /serviceedu/ {
	    proxy_pass http://192.168.231.1:8001;
	}

	location ~ /serviceoss/ {
	    proxy_pass http://192.168.231.1:8002;
	}

	location ~ /user {
	    proxy_pass http://192.168.231.1:8001;
	}
}
~~~

③修改前端页面请求地址为请求nginx

~~~javascript
module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',
  BASE_API: '"http://192.168.231.134:9001"',
})
~~~

---

## 首页管理

### 登录显示

①使用后端模拟浏览器发送post请求访问查询ip的接口，将返回的数据转为json传回前端

~~~java
public class IpUtils {

    public static JSONObject getIpInfo() {
        try {
            String text = sendPost("http://pv.sohu.com/cityjson/");
            String info = text.substring(text.indexOf("=") + 1,text.lastIndexOf(";"));
            return JSONObject.parseObject(info);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    public static String sendPost(String url) throws IOException {
        String result = "";
        HttpPost httpPost = new HttpPost(url);
        CloseableHttpClient httpClient = HttpClients.createDefault();
        try {
            BasicResponseHandler handler = new BasicResponseHandler();
            StringEntity entity = new StringEntity("utf-8");//解决中文乱码问题
            entity.setContentEncoding("UTF-8");
            entity.setContentType("application/json");
            httpPost.setEntity(entity);
            result = httpClient.execute(httpPost, handler);
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                httpClient.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return result;
    }
}
~~~

---

## 讲师管理

### 前端页面

#### 讲师列表

- 新功能开发的内容就是实现讲师列表的

#### 分页条

①通过element-ui的组件库修改模板

②修改一个getList方法

~~~html
<!-- 分页条 -->
      <!-- @current-change是饿了么已经实现的，绑定了一个点击事件
      当currentPage改变时会触发，传的参数是当前页数，即:current-page绑定的值-->
      <el-pagination
        :current-page="pageNum"
        :page-size="pageSize"
        :total = "total"
        style="padding: 30px 0; text-align:center;"
        layout="total,prev, pager, next,jumper"
        @current-change="getList" 
      />

		getList(page = 1){ //表示当不传值是pageNum是1
            this.pageNum = page
            teacher.getTeacherList(this.pageNum,this.pageSize,this.voTeacher){..}
~~~

#### 条件查询

①定义一个封装查询属性的voteacher对象，使用v-mode将查询表单的属性value值中l绑定到voteacher对象中

②绑定查询按钮的点击事件，调用讲师列表中的可用于条件查询的方法

~~~html
<el-button type="primary" @click="getList()">查询</el-button>
~~~

#### 删除讲师

①在每一行数据后面添加删除按钮、绑定点击事件、传递删除的id

②在api文件夹teacher.js文件中定义被调用的方法

~~~javascript
//删除讲师
delTacher(id){
    return request({
        url: `/serviceedu/delTeacher/${id}`,
        method: 'post'
    })
}
~~~

③在list.vue页面进行调用

~~~javascript
handleDelete(id) {
    this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
    })
    .then(() => {
        //调用删除方法
        teacher.delTeacher(id)
            .then((response) => {
            //删除成功弹窗
            this.$message({
                type: "success",
                message: "删除成功!",
            })
            //刷新页面
            this.getList(this.pageNum);
        })
    })     
}
~~~

#### 添加讲师

①基于element-ui实现表单编写

②在api中的teacher.js中定义被调用的方法

~~~javascript
//添加讲师
addTeacher(teacher){
    return request({
        url: '/serviceedu/add',
        method: 'post',
        data: teacher,
    })
}
~~~

③在add.vue页面调用上方方法

~~~javascript
//添加讲师
addTeacher(){
    teacherApi.addTeacher(this.teacher)
        .then(response => {
        //提示添加成功
        this.$message({
            type:'success',
            message:'添加成功'
        });
        //回到数据展示的列表,通过路由跳转
        this.$router.push({path:'/teacher/list'})
    })
},
~~~

#### 修改讲师

①通过添加一个隐藏路由，实现携带id跳转到某个公共页面进行修改操作

~~~javascript
//下方是个隐藏路由，:id相当于是个占位符，表示要带一个参数，例如 add/123456
{
    path: 'edit/:id',
    name: '编辑讲师',
    component: () => import('@/views/edu/teacher/add'),
    meta: { title: '编辑讲师', noCache:true },
    hidden: true
}
~~~

~~~html
<router-link :to="'/teacher/edit/' + scope.row.id">
    <el-button size="mini" type="primary">编辑</el-button>
</router-link>
~~~

②在api文件夹teacher.js文件中定义被调用的方法

~~~javascript
//查询指定讲师
getTeacherInfo(id){
    return request({
        url: '/serviceedu/query/' + id,
        method: 'get',
    })
}
//修改指定id讲师的信息
updateInfoById(teacher){
    return request({
        url: '/serviceedu//update/' + teacher.id,
        method: 'post',
        data: teacher,
    })
}
~~~

③在add.vue页面进行调用

- 由于和save方法共同使用一个页面，因此必须判断地址url中是否有id值来决定使用哪个方法

在created()方法，即在数据装载之前执行

~~~javascript
created() {
    if (this.$route.params && this.$route.params.id) {
        // this.$route.params获取uri中的参数，id需要和在隐藏路由设置的一致
        const id = this.$route.params.id
        this.getTeacherInfo(id)
    }
},
~~~

修改数据填充以及添加修改方法

~~~javascript
//查询讲师信息
getTeacherInfo(id){
    teacherApi.getTeacherInfo(id)
        .then(response => {
        this.teacher = response.data
    })
},
//根据id修改讲师信息
updateInfoById(teacher){
    teacherApi.updateInfoById(teacher)
        .then(response => {
        //提示添加成功
        this.$message({
            type:'success',
            message:'修改成功'
        });
        //回到数据展示的列表,通过路由跳转
        this.$router.push({path:'/teacher/list'})
    })
}
~~~

执行修改

~~~javascript
methods: {
    //为了能够让修改和添加同时使用
    addOrUpdate(){
        //通过判断id值是否为空判断修改还是添加
        if(!this.teacher.id){
            //添加
            this.addTeacher();
        }else{
            //修改
            this.updateInfoById(this.teacher)
        }
    },
~~~

#### 讲师头像

- 导入需要使用的组件，并在页面下方中进行变量赋初始值和局部注册

~~~javascript
<!-- 头衔缩略图 -->
<pan-thumb :image="teacher.avatar"/>
<!-- 文件上传按钮 -->
<el-button type="primary" icon="el-icon-upload" @click="imagecropperShow=true">更换头像
</el-button>

<!--
v-show：是否显示上传组件
:key：类似于id，如果一个页面多个图片上传控件，可以做区分
:url：后台上传的url地址
@close：关闭上传组件
@crop-upload-success：上传成功后的回调 -->
<image-cropper
               v-show="imagecropperShow"
               :width="300"
               :height="300"
               :key="imagecropperKey"
               :url="BASE_API+'/serviceoss/upload'"
               field="file"
               @close="close"
               @crop-upload-success="cropSuccess"/>
</el-form-item>

export default {
  name:'add',
  components:{
    ImageCropper,
    PanThumb
  },
  data() {
    return {
      teacher: {},
      //上传弹框是否显示
      imagecropperShow: false,
      //图片唯一标识
      imagecropperKey: 0,
      //获取dev.env.js里面的请求地址
      BASE_API: process.env.BASE_API,
    },
   methods: {
    //关闭上传弹框的方法
    close(){
      this.imagecropperShow = false;
      //上传组件初始化，解决二次上传的缓存bug
      this.imagecropperKey += 1
    },
    /*上传成功后的回调方法
    这个方法中封装了响应的赋值，此处的photo接收的就是上传成功后回传回来的访问地址
    */
    cropSuccess(photo){
      //回显上传头像
      this.teacher.avatar = photo
      //关闭上传弹窗
      this.imagecropperShow = false
      //上传组件初始化，解决二次上传的缓存bug
      this.imagecropperKey += 1
    },
~~~

### 后端接口

- 在父工程online_edu_parent下创建子模块service

主要是需要修改子模块service的pom文件

- <font color='red'>**由于使用了MyBatis-Plus因此简单sql语句不再需要手写**</font>

1、在子模块service下再次创建子模块service_edu

2、修改pom文件

3、编写yml配置文件

~~~yml
server:
  port: 8001
spring:
  application:
    name: service-edu
  profiles:
    active: dev
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/online_edu?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    username: root
    password: root
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
~~~

4、业务类

- 使用mybatis-plus的代码生成器快速生成实体类、控制层、业务层、持久层

~~~java
public class FastAutoGeneratorTest {
    public static void main(String[] args) {
        // 设置我们需要创建在哪的路径
        FastAutoGenerator.create("jdbc:mysql://localhost:3306/online_edu?serverTimezone=GMT%2B8&characterEncoding=utf-8&useSSL=false", "root", "root")
            .globalConfig(builder -> {
                builder.author("year21") // 设置作者
                    .enableSwagger() // 开启 swagger 模式
                    .fileOverride() // 覆盖已生成文件
                    .outputDir("C://Users//hcxs1986//IdeaProjects//online_edu_parent//service//service_edu//src//main//java"); // 指定输出目录
            })
            .packageConfig(builder -> {
                builder.parent("top.year21.onlineedu") // 设置父包名
                    .moduleName("serviceedu") // 设置父包模块名
                    .pathInfo(Collections.singletonMap(OutputFile.mapperXml, "C://Users//hcxs1986//IdeaProjects//online_edu_parent//service//service_edu//src//main//resources//mybatis//mapper")); // 设置mapperXml生成路径
            })
            .strategyConfig(builder -> {
                builder.addInclude("edu_teacher") // 设置需要生成的表名
                    .addTablePrefix("t_", "c_"); // 设置过滤表前缀
            }).templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker 引擎模板，默认的是Velocity引擎模板
            .execute();
    }
}
~~~

5、主启动类

6、在控制层编写代码进行接口测试

#### 删除讲师

1、在实体类isDeleted属性上添加@TableLogic注解

2、编写控制层方法

~~~java
//删除讲师
@PostMapping("/delTeacher/{id}")
public JsonResult<Void> deleteTeacher(@PathVariable("id") Integer id){
    boolean result = teacherService.removeById(id);
    return new JsonResult<>(result);
}
~~~

#### 查询讲师

1、配置分页插件

~~~java
//注册MyBatisPlus的分页插件
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor(){
    //拦截器
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();

    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));

    return interceptor;
}
~~~

2、编写控制层方法

- 不带查询条件的分页查询

~~~java
//分页查询讲师数据
@GetMapping("/pages/{pageNum}/{pageSize}")
public JsonResult<Page> queryAllTeachersByPages(@PathVariable("pageNum") Integer pageNum,
                                                @PathVariable("pageSize") Integer pageSize){
    Page<EduTeacher> page = new Page<>(pageNum, pageSize);
    teacherService.page(page,null);
    return new JsonResult<>(true,"查询成功",page);
}
~~~

- 多条件组合分页查询讲师信息

①一般使用一个值对象来封装查询条件参数

~~~java
@Data
public class VOTeacher {
    private String name;
    private Integer level;
    private String begin;
    private String end;
}
~~~

②编写接口

~~~java
//多条件组合分页查询讲师信息
@PostMapping("/condition/{pageNum}/{pageSize}")
public JsonResult<Page> queryByPagesWithCondition(@PathVariable("pageNum") Integer pageNum,
                                                  @PathVariable("pageSize") Integer pageSize,
                                                  @RequestBody(required = false) VOTeacher voTeacher){
    //创建page对象，查询的数据都封装在这个对象中
    Page<EduTeacher> page = new Page<>(pageNum, pageSize);
    //创建条件构造器对象
    QueryWrapper<EduTeacher> queryWrapper = new QueryWrapper<>();
    //获取传入的参数
    String name = voTeacher.getName();
    Integer level = voTeacher.getLevel();
    String begin = voTeacher.getBegin();
    String end = voTeacher.getEnd();

    ///判断属性值是否为空，以决定是否添加该查询条件
    if (!StringUtils.isEmpty(name)){
        queryWrapper.like("name",name);
    }
    if (!StringUtils.isEmpty(level)){
        queryWrapper.eq("level",level);
    }
    if (!StringUtils.isEmpty(begin)){
        queryWrapper.ge("gmt_create",begin);
    }
    if (!StringUtils.isEmpty(end)){
        queryWrapper.le("gmt_create",end);
    }

    teacherService.page(page,queryWrapper);
    return new JsonResult<>(true,"查询成功",page);
}
~~~

#### 新增讲师

1.在需要在执行插入操作后自动填充属性添加注解@TableFiled

~~~java
@ApiModelProperty("创建时间")
//FieldFill.INSERT表示插入操作时自动填充
@TableField(value = "gmt_create",fill = FieldFill.INSERT)
private LocalDateTime gmtCreate;

@ApiModelProperty("更新时间")
//FieldFill.INSERT_UPDATE表示插入和更新操作时自动填充
@TableField(value = "gmt_modified",fill = FieldFill.INSERT_UPDATE)
private LocalDateTime gmtModified;
~~~

2.在service_base模块使用一个类实现MetaObjectHandler接口并交由spring管理

~~~java
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
   @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("gmtCreate",new Date(),metaObject);
        this.setFieldValByName("gmtModified",new Date(),metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("gmtModified",new Date(),metaObject);
    }
}
~~~

3.编写控制层方法

~~~java
@PostMapping("/add")
public JsonResult<Void> addTeacher(@RequestBody EduTeacher eduTeacher){
    boolean result = teacherService.save(eduTeacher);
    return new JsonResult<>(result);
}
~~~

#### 修改讲师

1.编写控制层方法

①根据指定的id进行查询

②根据指定的id进行修改

~~~java
@GetMapping("/query/{id}")
public JsonResult<EduTeacher> queryById(@PathVariable("id") Integer id){
    EduTeacher teacher = teacherService.getById(id);
    return new JsonResult<>(true,"查询成功",teacher);
}

@PostMapping("/update/{id}")
public JsonResult<Boolean> updateTeacher(@RequestBody EduTeacher eduTeacher){
    boolean result = teacherService.updateById(eduTeacher);
    return new JsonResult<>(result);
}
~~~

#### 讲师头像

- 使用阿里云oss完成头像存储

<font color='red'>**1、在子模块service下再次创建用于云存储微服务子模块service-oss**</font>

2、在pom文件中添加下列依赖

~~~xml
<dependencies>
    <!-- 阿里云oss依赖 -->
    <dependency>
        <groupId>com.aliyun.oss</groupId>
        <artifactId>aliyun-sdk-oss</artifactId>
    </dependency>

    <!-- 日期工具栏依赖 -->
    <dependency>
        <groupId>joda-time</groupId>
        <artifactId>joda-time</artifactId>
    </dependency>
</dependencies>
~~~

3、编写yml配置文件

~~~yml
#服务端口
server:
  port: 8002
spring:
  application:
    name: service-oss
  profiles:
    active: dev

#阿里云 OSS配置信息
aliyun:
  oss:
    file:
      endpoint: oss-cn-guangzhou.aliyuncs.com #节点地域
      keyid: LTAI5tHuNsemaiLnP3yro4vD #访问id
      keysecret: ed1Zxok1SklTEI40HhRKkJeRcQdyWc #访问密码
      bucketname: onlineedufile #访问bucketname名
~~~

4、业务类

①创建常量类读取yml配置文件内容

~~~java
@Component
public class OSSConfigurationClass{
    //从yml配置文件中读取下列配置内容
    @Value("${aliyun.oss.file.endpoint}")
    private String endpoint;
    @Value("${aliyun.oss.file.keyid}")
    private String keyid;
    @Value("${aliyun.oss.file.keysecret}")
    private String keysecret;
    @Value("${aliyun.oss.file.bucketname}")
    private String bucketname;
    
    public static String END_POINT;
    public static String ACCESS_KEY_ID;
    public static String ACCESS_KEY_SECRET;
    public static String BUCKET_NAME;

    /*
    在项目启动的时候执行该方法，也可以理解为在spring容器初始化的时候执行该方法。
     */
    @PostConstruct
    public void init(){
        END_POINT = endpoint;
        ACCESS_KEY_ID = keyid;
        ACCESS_KEY_SECRET = keysecret;
        BUCKET_NAME = bucketname;
    }
}
~~~

②service层负责实现文件上传逻辑

~~~java
@Service
public class OSSServiceImpl implements OSSService{
     //上传头像到oss
    @Override
    public String uploadFile(MultipartFile file) {
        // Endpoint地址
        String endpoint = OSSConfigurationClass.END_POINT;
        // 阿里云账号AccessKey
        String accessKeyId = OSSConfigurationClass.ACCESS_KEY_ID;
        String accessKeySecret = OSSConfigurationClass.ACCESS_KEY_SECRET;
        // 填写Bucket名称
        String bucketName = OSSConfigurationClass.BUCKET_NAME;
        // 获取原始文件名称
        String objectName = file.getOriginalFilename();

        //获取文件后缀
        String suffix = objectName.substring(objectName.lastIndexOf("."));
        //拼接新的文件名用于上传,防止上传文件名字相同被覆盖
        String fileName = UUID.randomUUID().toString();
        String fileUploadName = fileName + suffix;

        //将文件按照日期进行分类
        //获取当前日期
        String datePath = new DateTime().toString("yyyy-MM-dd");

        //拼接最终上传的文件名
        String endUploadPath = datePath + "/" + fileUploadName;

        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

        try {
            //获取上传文件的输入流
            InputStream inputStream = file.getInputStream();
            /* 调用oss方法实现上传
           第一个参数为bucker名称
           第二个参数为上传到oss文件路径和文件名称
           第三个参数为文件的上传流
             */
            ossClient.putObject(bucketName, endUploadPath, inputStream);

            //将上传成功后的路径返回,需要手动拼接，oss没有方法实现
            //oss的访问路径为https://onlineedufile.oss-cn-guangzhou.aliyuncs.com/2.jpg
            String uploadPath = "https://"+ bucketName+ "." + endpoint + "/" + endUploadPath;

            return uploadPath;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (ossClient != null) {
                ossClient.shutdown();
            }
        }
        return null;
    }
}
~~~

③控制层

~~~java
//上传文件的方法
@PostMapping("/upload")
public JsonResult<String> uploadFile(MultipartFile file){
    //当上传成功后，返回存储的图片地址
    String filePathFromOSS = ossService.uploadFile(file);
    //将图片地址返回
    return new JsonResult<>(true,"上传成功",filePathFromOSS);
}
~~~

5、主启动类

- 由于这个微服务仅仅需要上传图片吗，不需要去数据库查询信息，而在service引入的数据库依赖

  会通过依赖传递到这个serviceoss的微服务中，因此需要在启动类中排除自动装配数据源类

~~~java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class) //不自动装配数据源，不排除有可能报错
@ComponentScan(basePackages = {"top.year21.onlineedu"})
public class ServiceOSS8002 {
    public static void main(String[] args){
        SpringApplication.run(ServiceOSS8002.class,args);
    }
}
~~~

6、在控制层编写代码进行接口测试

---

## 课程分类管理

- 基于新功能开发的步骤完成课程分类管理

- 使用EasyExcel完成对excel文件的读取与写入
- 使用代码生成器快速生成对应表的相关信息
### 前端页面

#### 添加课程

- 在添加课程界面提供一个上传文件功能，将上传的文件传到后端接口中即可

~~~html
      <el-form-item label="选择Excel">
        <el-upload
          ref="upload"
          :auto-upload="false"
          :on-success="fileUploadSuccess"
          :on-error="fileUploadError"
          :disabled="importBtnDisabled"
          :limit="1"
          :action="BASE_API+'/serviceedu/subject/add'"
          name="file"
          accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet">
          <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
          <el-button
            :loading="loading"
            style="margin-left: 10px;"
            size="small"
            type="success"
            @click="submitUpload">上传</el-button>
        </el-upload>
      </el-form-item>
<script>
export default {
    name: 'add',
    data() {
        return {
            BASE_API: process.env.BASE_API, // 接口API地址
            importBtnDisabled: false, // 按钮是否禁用,
            loading: false
        }
    },
    methods: {
        //点击上传按钮进行上传表单的提交
        submitUpload(){
            this.importBtnDisabled = true
            this.loading = true
            //表单提交 原生js写法是这样 ==> document.getElementById("upload").submit()
            this.$refs.upload.submit()
        },
        //上传成功的回调方法
        fileUploadSuccess(){
            this.loading = false
            //提示信息
            this.$message({
                type:'success',
                message:'上传成功'
            })
            //跳转到课程分类列表页面
            this.$router.push({path:'/subject/list'})
        },
        //上传失败的回调方法
        fileUploadError(){
            this.loading = false
            //提示信息
            this.$message({
                type:'error',
                message:'上传失败'
            })
        },
    },
}
</script>
~~~

#### 课程显示

~~~html
<template>
  <div class="app-container">
    <el-input v-model="filterText" placeholder="Filter keyword" clearable style="margin-bottom:30px;" />
    <el-tree
      ref="tree2"
      :data="list"
      :props="defaultProps"
      :filter-node-method="filterNode"
      class="filter-tree"
      default-expand-all
    />

  </div>
</template>

<script>
import subject from '@/api/edu/subject.js'

export default {
    name: 'list',
    data() {
        return {
        filterText: '',
        list: [], 
        defaultProps: {
            children: 'children',
            label: 'title',
        }
        }
    },
    created() {
        this.getSubjectList();
    },
    watch: {
        filterText(val) {
        this.$refs.tree2.filter(val)
        }
    },
    methods: {
        filterNode(value, data) {
            if (!value) return true
            return data.title.toLowerCase().indexOf(value) !== -1
            },
        getSubjectList(){
            subject.getSubjectList()
                .then(response => {
                    this.list = response.data
                })
        },
    }
}
</script>
~~~

### 后端接口
#### 添加课程

①控制层创建接收excel文件上传的方法

~~~java
@Autowired
private IEduSubjectService eduSubjectService;

//添加课程分类
@PostMapping("/add")
public JsonResult<Void> addSubject(MultipartFile file) throws IOException {
    //获取上传文件
    eduSubjectService.addFile(file,eduSubjectService);

    return new JsonResult<>(true);
}
~~~

②业务层负责将上传的文件以流的方式传输给ExcelReadListener监听器对象

~~~java
public interface IEduSubjectService extends IService<EduSubject> {
    void addFile(MultipartFile file,IEduSubjectService eduSubjectService);
}

@Service
public class EduSubjectServiceImpl extends ServiceImpl<EduSubjectMapper, EduSubject> implements IEduSubjectService {
    //添加课程分类
    @Override
    public void addFile(MultipartFile file,IEduSubjectService eduSubjectService) {
        //获取上传文件的流形式
        InputStream inputStream = null;
        try {
            inputStream = file.getInputStream();
            EasyExcel.read(inputStream, SubjectData.class,new ExcelReadListener(eduSubjectService)).sheet().doRead();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
~~~

③ExcelReadListener监听器对象负责读取文件和写入数据库

- <font color='red'>**由于spring不建议在new的对象里面依赖注入bean，或说禁止在自己new的对象里面依赖注入，**</font>

  因为没有交给spring管理的对象，无法实现对其依赖的对象进行依赖注入，导致依赖对象为mull
  
  因此为了使用eduSubjectService将数据插入数据库，必须在业务层将eduSubjectService对象传入

~~~java
public class ExcelReadListener extends AnalysisEventListener<SubjectData> {

    /*
    因为在ExcelReadListener是手动new出来的对象，没有交给spring进行管理，
    因此不能在ExcelReadListener内使用@Autowired自动装配在spring管理的的对象，
    需要在这一层使用eduSubjectService进行数据库操作必须通过上一层传入这个参数
    在ExcelReadListener中的eduSubjectService不传值的话是null的
     */
    private IEduSubjectService eduSubjectService;

    public ExcelReadListener() {}
    public ExcelReadListener(IEduSubjectService eduSubjectService) {
        this.eduSubjectService = eduSubjectService;
    }

    //逐行读取excel的内容
    @Override
    public void invoke(SubjectData subjectData, AnalysisContext analysisContext) {
            if (subjectData == null){
                throw new InsertException(30000,"文件内容为空");
            }
            //一行一行读取，每次读取都有两个值，第一个值为一级分类，第二个值为二级分类，
            //先判断一级分类是否重复
        EduSubject eduSubject = oneSubjectIsExists(eduSubjectService, subjectData.getOneSubjectName());
            if (eduSubject == null){ //若一级分类查询为空，则可以进行添加
                eduSubject = new EduSubject();
                eduSubject.setTitle(subjectData.getOneSubjectName());
                eduSubject.setParentId("0");
                eduSubjectService.save(eduSubject);
            }

            //获取一级分类的id，作为二级分类的parent_id
            String parentId = eduSubject.getId();

            //判断二级分类是否重复
        EduSubject twoSubject = twoSubjectIsExists(eduSubjectService, subjectData.getTwoSubjectName(), parentId);
            if (twoSubject == null){
                twoSubject = new EduSubject();
                twoSubject.setTitle(subjectData.getTwoSubjectName());
                twoSubject.setParentId(parentId);
                eduSubjectService.save(twoSubject);
            }
    }

    //读取完成之后执行
    @Override
    public void doAfterAllAnalysed(AnalysisContext analysisContext) {

    }

    //判断一级分类不能重复添加
    public EduSubject oneSubjectIsExists(IEduSubjectService eduSubjectService,String oneSubjectName){
        QueryWrapper<EduSubject> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("title",oneSubjectName);
        queryWrapper.eq("parent_id","0");
        return eduSubjectService.getOne(queryWrapper);
    }

    //判断二级分类不能重复添加
    public EduSubject twoSubjectIsExists(IEduSubjectService eduSubjectService,String twoSubjectName,String parentId){
        QueryWrapper<EduSubject> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("title",twoSubjectName);
        queryWrapper.eq("parent_id",parentId);
        return eduSubjectService.getOne(queryWrapper);
    }
}
~~~

#### 课程显示

- 需要返回一个json数组，类似这种 ==> [{id:1, label:'一级', children:[{id:2, label:'二级',}]}]

①针对返回数据创建对应的实体类，此处需要创建两个实体类

②建立两个实体类之间的关系

~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class OneSubject {
    private String id;
    private String title;
    //表示一级分类中有多个二级分类
    private List<TwoSubject> children = new ArrayList<>();
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class TwoSubject {
    private String id;
    private String title;
}
~~~

③控制层返回`List<OneSubject>`，业务层负责返回数据

~~~java
@RestController
@RequestMapping("/serviceedu/subject")
@CrossOrigin(origins = "*",maxAge = 3600)
public class EduSubjectController {
    //课程分类显示
    @GetMapping("/list")
    public JsonResult<List<OneSubject>> getSubjectList(){
        List<OneSubject> subjects = eduSubjectService.selectAllSubject();
        return new JsonResult<>(true,"查询成功",subjects);
    }
}

@Service
public class EduSubjectServiceImpl extends ServiceImpl<EduSubjectMapper, EduSubject> implements IEduSubjectService {	
	@Override
    public List<OneSubject> selectAllSubject() {
        //用于封装最终返回数据的一级实体类
        List<OneSubject> oneDataEntity = new ArrayList<>();

        //查询所有一级分类
        QueryWrapper<EduSubject> oneWrapper = new QueryWrapper<>();
        oneWrapper.eq("parent_id","0");
        List<EduSubject> oneSubjectList = eduSubjectMapper.selectList(oneWrapper);

        //逐个遍历一级分类获取二级分类
        for (int i = 0; i < oneSubjectList.size(); i++) {

            //当前i索引指向集合中的一级分类对象
            EduSubject one = oneSubjectList.get(i);

            //查询所有二级分类
            QueryWrapper<EduSubject> twoWrapper = new QueryWrapper<>();
            twoWrapper.eq("parent_id",one.getId());

            //用于封装二级分类数据的二级实体类
            List<TwoSubject> twoDataEntity  = new ArrayList<>();

            //二级分类对象集合
            List<EduSubject> towSubjectList = eduSubjectMapper.selectList(twoWrapper);

            //遍历查找当前一级分类下的二级分类
            for (int j = 0; j < towSubjectList.size(); j++) {

                //当前符合一级分类为parent_id的二级分类对象
                EduSubject two = towSubjectList.get(j);

                //将每个二级分类对象赋值给二级封装对象并加入二级封装对象集合
                TwoSubject tSubject = new TwoSubject();
//                原生写法
//                tSubject.setId(two.getId());
//                tSubject.setTitle(two.getTitle());

                //spring提供的工具类，此方法是将two的属性值复制到tSubject中
                BeanUtils.copyProperties(two,tSubject);
                twoDataEntity.add(tSubject);
            }

            //将每个一级分类对象赋值给一级封装对象并加入一级封装对象集合
            OneSubject oSubject = new OneSubject();
//            oSubject.setId(one.getId());
//            oSubject.setTitle(one.getTitle());
//            oSubject.setChildren(twoDataEntity);
            //spring提供的工具类，此方法是将one的属性值复制到oSubject中,
            // 但只能赋值对应的属性，若属性不存在则不能赋值
            BeanUtils.copyProperties(one,oSubject);
            oSubject.setChildren(twoDataEntity);
            oneDataEntity.add(oSubject);
        }
        return oneDataEntity;
    }
}
~~~

---

## 课程管理

### 前端页面

#### 添加课程

> 还是新功能开发那一套

①基于element组件库的step步骤实现顺序切换

②在router文件夹中定义一个显示路由和两个隐藏路由，用于切换步骤

~~~javascript
{
    path: '/course',
    component: Layout,
    redirect: '/course/list',
    name: '课程管理',
    meta: { title: '课程管理', icon: 'example' },
    children: [
      {
        path: 'list',
        name: '课程列表',
        component: () => import('@/views/edu/course/list'),
        meta: { title: '课程列表', icon: 'tree' }
      },
      {
        path: 'info/:id',
        name: '添加课程',
        component: () => import('@/views/edu/course/info'),
        meta: { title: '添加课程', icon: 'tree' }
      },
      {
        path: 'chapter/:id',
        name: '课程大纲',
        component: () => import('@/views/edu/course/chapter'),
        meta: { title: '课程大纲', icon: 'eye' },
        hidden: true
      },
      {
        path: 'publish/:id',
        name: '发布课程',
        component: () => import('@/views/edu/course/publish'),
        meta: { title: '发布课程', icon: 'eye' },
        hidden: true
      },
    ]
  },
~~~

③在info、chapter、publish各自的vue页面添加内容

#### 课程大纲

- 使用树形空间显示课程章节和课程小节

~~~html
<el-tree 
         :data="chapterList" 
         :props="defaultProps" 
         :expand-on-click-node="false"
         :highlight-current="true"
         default-expand-all>
    <!-- 两个参数node和data，分别表示当前节点的 Node 对象和当前节点的数据。 -->
    <span class="custom-tree-node" slot-scope="{node,data}">
        <span>{{ node.label }}</span>
        <span >
            <!--  data.courseBarList != null表示是个一级分类，因此可以增加课时-->
            <el-button v-if="data.courseBarList != null"
                       type="text"
                       style="padding:10px"
                       @click="addChapter">
                添加课时
            </el-button>
        </span>
    </span>
</el-tree>
~~~

#### 确认课程

①在api/edu/下的course.文件中添加方法

~~~javascript
queryVOPublishInfoById(id){
    return request({
        url: '/serviceedu/course/'+id,
        method: 'get',
    })
},
~~~

②并在对应的页面进行调用

~~~javascript
created() {
    this.queryVOPublishInfoById()
},
methods: {
//根据课程id查询用于显示确认的课程信息
queryVOPublishInfoById(){
    course.queryVOPublishInfoById(this.courseId)
       .then(response => {
        this.VOPublish = response.data
})
}
~~~

#### 最终发布

①在api/edu/下的course.文件中添加方法

~~~javascript
updateCourseStatusById(id){
    return request({
        url: '/serviceedu/course/status/' + id,
        method: 'post',
    })
},
~~~

②并在对应的页面进行调用

~~~javascript
//最终提交，修改状态
next(){
    this.updateCourseStatusById()
},
//根据课程id修改课程最终状态
updateCourseStatusById(){
    course.updateCourseStatusById(this.courseId)
         .then(response => {
         this.$message.success("发布成功！")
          this.$router.push({path:"/course/list"})
        })
},
~~~

#### 课程列表

- 与讲师列表的展示差不多类似，只需要根据讲师列表页面将参数以及一些内容修改即可

#### 查询课程信息

- 根据url中的参数值查询信息并赋值给voCourse对象即可

~~~javascript
//根据id查询课程信息
queryCourseInfoById(){
course.queryCourseInfoById(this.courseId)
.then(response => {
this.voCourse = response.data
})
~~~

#### 更新课程信息

- 在这个页面需要解决更新信息和添加课程共用一个按钮问题

解决思路是根据判断voCourse中的id是否为空，在执行添加操作时id值是为空的，反之则不为空

~~~javascript
//根据voCourse的id来判断是添加还是更新操作
addOrUpdate(){
    if(this.voCourse.id === ''){//为空代表是添加操作
        //阻止空页面和内容添加
        console.log(this.voCourse);
        if(!this.voCourse.subjectId === ''){
            this.$message.error("请先添加数据再执行下一步！！！")
            return false
        }
        this.addCourse()
    }else { //不为空代表是更新操作
        this.updateCourseInfoById()
    }
~~~

#### 删除课程

①在api/edu/下的course.文件中添加方法

~~~javascript
delCourseById(id){
    return request({
        url: `/serviceedu/course/del/${id}`,
        method: 'post',
    })
}
~~~

②并在对应的页面进行调用

~~~javascript
//删除指定id的课程 
handleDelete(id) {
    this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
    })
        .then(() => {
        //调用删除方法
        course.delCourseById(id)
            .then(response => {
            //删除成功弹窗
            this.$message({
                type: "success",
                message: "删除成功!",
            });
        })
        //刷新页面
        this.getList();
    })
}, 
~~~

#### 添加/修改/删除章节

- 内容过于繁琐，在views/edu/course/chapter.vue下可详细查看
- <font color='red'>**添加/修改/删除课程小节的前端逻辑与添加课程章节几乎是一模一样的**</font>

#### 添加(删除)小节视频

①在api/edu/下创建barvideos.js文件并添加方法

~~~javascript
delAliyunVideoById(id){
    return request({
        url: '/servicevod/videoDel/'+id,
        method: 'post',
    })
},
~~~

②在对应页面添加上传功能组件和调用对应的上传或删除方法

~~~javascript
<el-form-item label="上传视频">
<el-upload
ref="upload"
:auto-upload="true"
:on-success="fileUploadSuccess"
:on-error="fileUploadError"
:before-remove="beforeRemove"
:on-remove="removeVideo"
:disabled="importBtnDisabled"
:limit="1"
:action="BASE_API+'/servicevod/videoUpload'"
name="file"
accept="video/*">
<el-button slot="trigger" type="primary">选取视频</el-button>
</el-upload> 
</el-form-item>

//删除视频之前的钩子方法
beforeRemove(file){
    return this.$confirm(`确定移除 ${ file.name }？`)  
},
//删除视频的方法
removeVideo(){
   barvideo.delAliyunVideoById(this.eduVoder.videoSourceId)
       .then(response => {
       //提示信息
       this.$message.success("移除成功")
       //清空文件列表
       this.fileList = []
       //清空eduVoder的文件名字和文件id
       this.eduVoder.videoSourceId = ''
       this.eduVoder.videoOriginalName= ''
})
},
//视频上传成功的回调方法
fileUploadSuccess(response,flie){
	this.loading = false
    //提示信息
    this.$message.success('上传成功')({
    this.eduVoder.videoSourceId = response.data
    this.eduVoder.videoOriginalName = file.name
},
//视频上传失败的回调方法
fileUploadError(){
    this.loading = false
    //提示信息
    this.$message.error('上传失败')
    //清空文件列表
    this.fileList = []
    //清空eduVoder的文件名字和文件id
    this.eduVoder.videoSourceId = ''
    this.eduVoder.videoOriginalName= ''
},
~~~





### 后端接口

- 使用代码生成器快速生成对应实体类以及其他信息

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220822221918179.png)

#### 添加课程

- 使用一个vo值对象接收参数
- 需要将数据插入课程表和课程简介表两张表中
- 需要将所有讲师和所有课表通过下列列表显示

 ①创建vo值对象

~~~java
@Data
public class VOCourse {
    private String teacherId;
    private String subjectId;
    private String subjectParentId;
    private String title;
    private BigDecimal price;
    private Integer lessonNum;
    private String cover;
    private String description;
}
~~~

②控制层接收参数

~~~java
//添加课程
@PostMapping("/add")
public JsonResult addCourse(@RequestBody VOCourse voCourse){
    eduCourseService.addCourse(voCourse);
    return new JsonResult(true);
}
~~~

③在业务类中将数据插入课程表和课程信息表

~~~java
//添加课程
@Override
public void addCourse(VOCourse voCourse) {
    //1.添加课程信息
    EduCourse eduCourse = new EduCourse();
    BeanUtils.copyProperties(voCourse,eduCourse);
    int result= baseMapper.insert(eduCourse);

    if (result == 0){
        throw new InsertException(30000,"出现异常,课程添加失败");
    }

    //获取已经插入数据库的课程信息主键
    String courseId = eduCourse.getId();

    //2.添加课程简介信息
    EduCourseDescription description = new EduCourseDescription();
    //设置课程简介信息对象id，用于和课程信息对象维持关联
    description.setId(courseId);
    description.setDescription(voCourse.getDescription());
    boolean insertResult = descriptionService.save(description);

    if (!insertResult){
        throw new InsertException(30000,"出现异常,课程简介信息添加失败");
    }
}
~~~

#### 课程大纲

- 与课程分类管理类似，都是通过拼装对应属性的对象最后回传

①创建CourseChapter、CourseBar对象并建立关系

~~~java
@Data
public class CourseChapter {
    private String id;
    private String title;
    private List<CourseBar> courseBarList;
}

@Data
public class CourseBar {
    private String id;
    private String title;
}
~~~

②控制层接收指定id的参数

~~~java
//查询指定课程章节
@GetMapping("/list/{courseId}")
public JsonResult<List<CourseChapter>> getCourseChaptersByCid(@PathVariable("courseId") String courseId){
    List<CourseChapter> chapterList = eduChapterService.getChapterListByCid(courseId);
    return new JsonResult<>(true,"查询成功",chapterList);
}
~~~

③业务层负责查询指定的id数据

~~~java
//查询课程章节
@Override
public List<CourseChapter> getChapterListByCid(String courseId) {
    //用于封装最终返回所有课程章节对象的集合
    List<CourseChapter> courseChapterList = new ArrayList<>();

    //根据指定id查询课程章节
    QueryWrapper<EduChapter> chapterWrapper = new QueryWrapper<>();
    chapterWrapper.eq("course_id",courseId);
    chapterWrapper.orderByAsc("sort");

    //得到指定id对应的课程章节集合
    List<EduChapter> chapterList = this.baseMapper.selectList(chapterWrapper);

    //遍历查询课程小节
    for (EduChapter chapter : chapterList) {
        //创建一个用个封装从数据库得到的章节对象到值对象CourseChapter中
        CourseChapter courseChapter = new CourseChapter();
        BeanUtils.copyProperties(chapter,courseChapter);

        //根据章节id查询所有小节集合
        QueryWrapper<EduVideo> barWrapper = new QueryWrapper<>();
        barWrapper.eq("chapter_id",chapter.getId());
        barWrapper.orderByAsc("sort");

        //得到指定课程章节的课程小节集合
        List<EduVideo> videoList = eduVideoMapper.selectList(barWrapper);

        //先创建一个用于封装课程小节对象的集合
        List<CourseBar> courseBarList = new ArrayList<>();

        for(EduVideo video : videoList){
            //创建一个用个封装从数据库得到的小节对象到值对象CourseBar中
            CourseBar courseBar = new CourseBar();
            BeanUtils.copyProperties(video,courseBar);
            //没查出一个就加入到courseBarList集合中
            courseBarList.add(courseBar);
        }
        //设置值对象courseChapter的第三个参数值
        courseChapter.setCourseBarList(courseBarList);
        //加入到封装courseChapter值对象的集合中
        courseChapterList.add(courseChapter);
    }
    return courseChapterList;
}
~~~

#### 确认课程

①根据前端需要的属性构造值对象

~~~java
@Data
public class VOPublish {
    private String id;
    private String title;
    private String lessonNum;
    private String oneSubject;
    private String twoSubject;
    private String teacherName;
    private String cover;
    private BigDecimal price;
}
~~~

②编写sql语句查询课程信息新并封装到值对象中

~~~xml
<resultMap id="vopublish" type="top.year21.onlineedu.serviceedu.vo.VOPublish">
       	<id property="id" column="id"/>
        <result property="title" column="title"/>
        <result property="lessonNum" column="lesson_num"/>
        <result property="oneSubject" column="oneSubject"/>
        <result property="twoSubject" column="twoSubject"/>
        <result property="teacherName" column="name"/>
        <result property="lessonNum" column="lessonNum"/>
        <result property="cover" column="cover"/>
        <result property="price" column="price"/>
    </resultMap>

<!--    根据课程id查询相关信息 VOPublish getVOPublishById(@Param("id")String id);-->
    <select id="getVOPublishById" resultMap="vopublish">
        SELECT c.`id`,c.`title`,c.`lesson_num`,c.`cover`,c.`price`,
            t.`name`,temp2.title AS oneSubject,s.`title` AS twoSubject
        FROM edu_course c
        LEFT JOIN edu_teacher t
        ON c.`teacher_id` = t.`id`
        LEFT JOIN edu_subject s
        ON c.`subject_id` = s.`id`
        LEFT JOIN (
               SELECT * FROM (
                      SELECT
                      id,title
                      FROM edu_subject
                      )AS temp
               )AS temp2
        ON c.`subject_parent_id` = temp2.id
        WHERE c.`id` = #{id};
    </select>
~~~

③持久层、④业务层、⑤控制层

~~~java
public interface EduCourseMapper extends BaseMapper<EduCourse> {
    //根据课程id查询相关信息
    VOPublish getVOPublishById(@Param("id")String id);
}

//根据指定课程id查询章节
@Override
public EduChapter queryChapterById(String id) {
    return this.baseMapper.selectById(id);
}

//根据指定id查询复合封装的课程信息对象
@GetMapping("/{id}")
public JsonResult<VOPublish> queryVOPublishById(@PathVariable("id") String id){
    VOPublish voPublish = eduCourseService.queryVOPublishById(id);
    return new JsonResult<>(true,"查询成功",voPublish);
}
~~~

#### 最终发布

②编写xml映射文件

~~~xml
<!--    int updateStatus(String courseId);-->
<update id="updateStatus">
    update edu_course set status = 'Normal' where id = #{courseId}
</update>
~~~

③持久层、④业务层、⑤控制层

~~~java
//根据指定id修改课程状态为已发布
int updateStatus(@Param("courseId") String courseId);

//根据指定id修改课程状态为已发布
@Override
public void updateStatus(String courseId) {
    int result = this.baseMapper.updateStatus(courseId);
    if (result == 0){
        throw new RuntimeException("修改失败");
    }
}

//根据指定id修改课程状态为已发布
@PostMapping("/status/{courseId}")
public JsonResult<Void> updateCourseStatusById(@PathVariable("courseId") String courseId){
    eduCourseService.updateStatus(courseId);
    return new JsonResult<>(true);
}
~~~

#### 课程列表

①控制层接收参数

②业务层负责执行查询

~~~java
@GetMapping("/conditionQuery/{pageNum}/{pageSize}")
public JsonResult<Page<EduCourse>> queryCondition(@PathVariable("pageNum") Integer pageNum,
                                                  @PathVariable("pageSize") Integer pageSize){
    Page<EduCourse> page = eduCourseService.queryCondition(pageNum,pageSize);
    return new JsonResult<>(true,"查询成功",page);
}
//条件查询
@Override
public Page<EduCourse> queryCondition(Integer pageNum, Integer pageSize) {
    Page<EduCourse> page = new Page<>(pageNum, pageSize);
    return this.baseMapper.selectPage(page, null);
}
~~~

#### 查询课程信息

①控制层接收参数，传至业务层

~~~java
//根据id查询指定课程信息
@GetMapping("/query/{id}")
public JsonResult<VOCourse> queryInfoById(@PathVariable("id") String id){
    VOCourse voCourse = eduCourseService.queryCourseInfoById(id);
    return new JsonResult<>(true,"查询成功",voCourse);
}
~~~

②业务层负责查询指定id的信息

~~~java
//根据id查询信息
@Override
public VOCourse queryCourseInfoById(String id) {
    //创建一个对象封装查询出的信息
    VOCourse voCourse = new VOCourse();
    //根据id查询课程信息并填充至封装对象
    EduCourse eduCourse = this.baseMapper.selectById(id);
    BeanUtils.copyProperties(eduCourse,voCourse);
    //根据id课程简介信息并填充至封装对象
    EduCourseDescription description = descriptionService.getById(id);
    voCourse.setDescription(description.getDescription());
    //返回封装对象
    return voCourse;
}
~~~

#### 更新课程信息

①控制层接收参数，传至业务层

~~~java
//根据id修改指定课程信息
@PostMapping("/update")
public JsonResult<Void> updateInfoById(@RequestBody VOCourse voCourse){
    eduCourseService.updateCourseInfoById(voCourse);
    return new JsonResult<>(true);
}
~~~

②业务层负责修改指定id的信息

~~~java
//根据id修改信息
@Override
public void updateCourseInfoById(VOCourse voCourse) {
    EduCourse eduCourse = new EduCourse();
    BeanUtils.copyProperties(voCourse,eduCourse);
    //根据id修改课程信息
    int result = this.baseMapper.updateById(eduCourse);

    if (result == 0){
        throw new InsertException(30001,"修改课程信息失败");
    }
    //根据id修改课程简介信息
    EduCourseDescription description = new EduCourseDescription();
    description.setId(voCourse.getId());
    description.setDescription(voCourse.getDescription());
    boolean updateResult = descriptionService.updateById(description);
    if (!updateResult){
        throw new InsertException(30001,"修改课程信息失败");
    }
}
~~~

#### 删除课程

①控制层接收参数

②业务层负责执行处理删除，在这个删除课程中需要调用其他业务层来执行删除

~~~java
//删除指定课程及其下属的所有章节和小节
@PostMapping("/del/{courseId}")
public JsonResult<Void> delCourseById(@PathVariable("courseId") String courseId){
    eduCourseService.delCourseById(courseId);
    return new JsonResult<>(true);
}

@Autowired
private IEduChapterService eduChapterService;
//删除课程
@Override
public void delCourseById(String courseId) {
    QueryWrapper<EduChapter> queryWrapper = new QueryWrapper<>();
    queryWrapper.eq("course_id",courseId);
    List<EduChapter> chapters = eduChapterService.list(queryWrapper);
    //表示当前课程下有章节和小节，需要批量删除
    if (chapters.size() != 0){
        for(EduChapter chapter : chapters){
            eduChapterService.delChapterById(chapter.getId());
        }
    }
    //以及删除课程描述
    boolean remove = descriptionService.removeById(courseId);
    if (!remove){
        throw new RuntimeException("删除指定课程描述失败，课程id是：" + courseId);
    }
    //当chapters.size()长度为0时，表示这个课程没有章节和小节，因此只需要删除当前课程即可
    int result = this.baseMapper.deleteById(courseId);
    if (result == 0){
        throw new RuntimeException("删除指定课程失败，课程id是：" + courseId);
    }   
}
~~~

#### 章节管理

①控制层接收参数，传至业务层

~~~java
//添加指定课程章节
@PostMapping("/add")
public JsonResult<Void> addChapterById(@RequestBody EduChapter eduChapter){
    eduChapterService.addNewChapterById(eduChapter);
    return new JsonResult<>(true);
}

//修改指定课程章节
@PostMapping("/update")
public JsonResult<Void> updateChapterById(@RequestBody EduChapter eduChapter){
    eduChapterService.updateChapterById(eduChapter);
    return new JsonResult<>(true);
}

//删除指定课程章节
@PostMapping("/del/{id}")
public JsonResult<Void> delChapterById(@PathVariable("id") String id){
    eduChapterService.delChapterById(id);
    return new JsonResult<>(true);
}

//查询指定课程章节
@GetMapping("/query/{id}")
public JsonResult<EduChapter> queryChapterById(@PathVariable("id") String id){
    EduChapter chapter = eduChapterService.queryChapterById(id);
    return new JsonResult<>(true,"查询成功",chapter);
}
~~~

②业务层负责修改指定id的信息

~~~java
//根据指定课程id添加章节
@Override
public void addNewChapterById(EduChapter eduChapter) {
    int result = this.baseMapper.insert(eduChapter);
    if (result ==0 ){
        throw new InsertException(30001,"添加章节失败！");
    }
}

//根据指定课程id修改章节
@Override
public void updateChapterById(EduChapter eduChapter) {
    UpdateWrapper<EduChapter> updateWrapper = new UpdateWrapper<>();
    updateWrapper.eq("course_id",eduChapter.getCourseId());
    int update = this.baseMapper.update(eduChapter, updateWrapper);
    if (update ==0 ){
        throw new InsertException(30001,"更新章节失败！");
    }
}

//根据指定课程id删除章节
@Override
public void delChapterById(String id) {
    //先判断是否存在小节需要删除
    UpdateWrapper<EduVideo> wrapper = new UpdateWrapper<>();
    wrapper.eq("chapter_id",id);
    Long count = eduVideoMapper.selectCount(wrapper);
    if (count != 0){
        int videoDelResult = eduVideoMapper.delete(wrapper);
        if (videoDelResult == 0){
            throw new RuntimeException("删除指定小节失败");
        }
    }
    //再删除章节
    int result = this.baseMapper.deleteById(id);
    if (result == 0){
        throw new RuntimeException("删除指定章节失败,章节id是：" + id);
    }
}

//根据指定课程id查询章节
@Override
public EduChapter queryChapterById(String id) {
    return this.baseMapper.selectById(id);
}
~~~

- <font color='red'>**课程小节的后端接口与课程章节几乎是一模一样的，就不再重复书写了**</font>

#### 添加小节视频

①在service模块下创建子模式service_vod负责视频上传相关内容

②引入依赖

~~~xml
<dependencies>
    <!--aliyun-->
    <dependency>
        <groupId>com.aliyun</groupId>
        <artifactId>aliyun-java-sdk-core</artifactId>
    </dependency>
    <dependency>
        <groupId>com.aliyun.oss</groupId>
        <artifactId>aliyun-sdk-oss</artifactId>
    </dependency>
    <dependency>
        <groupId>com.aliyun</groupId>
        <artifactId>aliyun-java-sdk-vod</artifactId>
    </dependency>
    <dependency>
        <groupId>com.aliyun</groupId>
        <artifactId>aliyun-java-vod-upload</artifactId>
    </dependency>
    <dependency>
        <groupId>com.aliyun</groupId>
        <artifactId>aliyun-sdk-vod-upload</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
    </dependency>
    <dependency>
        <groupId>org.json</groupId>
        <artifactId>json</artifactId>
    </dependency>
</dependencies>
~~~

③编写控制层，接收参数

~~~java
@RestController
@RequestMapping("/serviceedu/video")
public class VideoUploadController {

    @Autowired
    private VideoUploadService videoUploadService;

    @PostMapping("/upload")
    public JsonResult<String> uploadVideoToAliyun(@RequestPart MultipartFile file){
        String videoSourceId = videoUploadService.uploadVideo(file);
        return new JsonResult<>(true,"上传成功",videoSourceId);
    }
}
~~~

④业务层负责执行文件的上传

~~~java
//处理视频文件上传
@Override
public String uploadVideo(MultipartFile file) {
	String title = UUID.randomUUID().toString(); //上传成功到阿里云显示的名称
    String fileName = file.getOriginalFilename(); //上传文件的原始名称
    InputStream inputStream = null;
    try {
        inputStream = file.getInputStream();
    } catch (IOException e) {
        e.printStackTrace();
    }

    UploadStreamRequest request = new UploadStreamRequest(UploadConfigClass.ACCESS_KEY_ID,UploadConfigClass.ACCESS_KEY_SECRET, title, fileName, inputStream);
    UploadVideoImpl uploader = new UploadVideoImpl();
    UploadStreamResponse response = uploader.uploadStream(request);

    if (response.isSuccess()) {
        System.out.print("VideoId=" + response.getVideoId() + "\n");
        return response.getVideoId();
    } else { //如果设置回调URL无效，不影响视频上传，可以返回VideoId同时会返回错误码。其他情况上传失败时，VideoId为空，此时需要根据返回错误码分析具体错误原因
        System.out.print("VideoId=" + response.getVideoId() + "\n");
        System.out.print("ErrorCode=" + response.getCode() + "\n");
        System.out.print("ErrorMessage=" + response.getMessage() + "\n");
        return response.getVideoId();
    }
}
~~~

#### 删除小节视频

①创建一个类初始化删除阿里云视频的配置

~~~java
@Component
public class UploadConfigClass {
    @Value("${aliyun.vod.file.keyid}")
    private String keyid;
    @Value("${aliyun.vod.file.keysecret}")
    private String keysecret;


    public static String ACCESS_KEY_ID;
    public static String ACCESS_KEY_SECRET;

    @PostConstruct
    public void init(){
        ACCESS_KEY_ID = keyid;
        ACCESS_KEY_SECRET = keysecret;
    }
}
~~~

②创建控制层接收删除视频的id

~~~java
@PostMapping("/videoDel/{videoSourceId}")
public JsonResult<Void> delVideoInAliyun(@PathVariable("videoSourceId") String videoSourceId){
    videoUploadService.delUploadVideo(videoSourceId);
    return new JsonResult<>(true);
}
~~~

③业务层负责处理将阿里云中的指定id视频删除

~~~java
//根据视频id删除视频
@Override
public void delUploadVideo(String videoSourceId){
    try {
        //1.创建初始化对象
        DefaultAcsClient client = VodInitClass.initVodClient(UploadConfigClass.ACCESS_KEY_ID, UploadConfigClass.ACCESS_KEY_SECRET);
        //2.创建删除视频的request对象
        DeleteVideoRequest request = new DeleteVideoRequest();
        //3.向request对象中设置需要删除的视频id
        request.setVideoIds(videoSourceId);
        //4.调用初始化对象方法将request传入删除视频
        client.getAcsResponse(request);
    } catch (ClientException e) {
        e.printStackTrace();
    }
}
}
~~~

---

## 统计分析

### 前端页面

- 整合echarts，使用npm install --save echarts@4.1.0

①在api文件下创建statistic.js文件，创建被调用的方法

~~~javascript
import request from '@/utils/request'
export default{
    //创建数据
    countRegister(day){
        return request({
            url:`/servicedaily/countNum/${day}`,
            method:'post'
        })
    },
    //获取图表显示需要的数据
    showData(seacheObj){
        return request({
            url:`/servicedaily/showTableData/${seacheObj.type}/${seacheObj.begin}/${seacheObj.end}`,
            method:'get'
        })
    }
}
~~~

②在对应的页面进行调用

### 后端接口

①创建新的数据表存储统计分析所需的数据

②在service模块下创建子模块service_statistics

③改pom文件

④编写yml配置文件

⑤主启动类

⑥业务类

需要创建两个接口

- 在userCenter模块创建被调用接口，查询userCenter表中某个时间的注册人数

~~~java
//查询某一天的注册人数
@GetMapping("count/{day}")
public Integer countRegisterNumInOneDay(@PathVariable("day")String day){
    return  userCenterService.countRegisterNumInOneDay(day);
}

//查询某一天的注册人数
@Override
public Integer countRegisterNumInOneDay(String day) {
    return this.baseMapper.countRegisterNumInOneDay(day);
}

Integer countRegisterNumInOneDay(@Param("day") String day);

<!--    Integer countRegisterNumInOneDay(String day);-->
<select id="countRegisterNumInOneDay" resultType="integer">
    select count(*) from usercenter_member DATE(gmt_create) = #{day}
</select>
~~~

- 在statistics模块创建接口，调用其他模块接口并插入一条新数据到daily表中

~~~java
//统计某一天的注册人数
@PostMapping("/countNum/{day}")
public JsonResult<Void> countRegisterNum(@PathVariable("day") String day){
    dailyService.countRegisterNumInOneDay(day);
    return new JsonResult<>(true);
}

@Override
public void countRegisterNumInOneDay(String day) {
    //获取指定日期的注册人数
    Integer num = uSerCenterTransfer.countRegisterNumInOneDay(day);

    //插入之前先判断是否存在该日期的记录
    QueryWrapper<Daily> wrapper = new QueryWrapper<>();
    wrapper.eq("date_calculated",day);
    Daily one = this.baseMapper.selectOne(wrapper);
    if (one != null){
        //将注册人数记录更新
        one.setRegisterNum(num);
        //插入数据库
        this.baseMapper.update(one,wrapper);
        //结束操作
        return;
    }
    
    //将注册人数的结果插入daily表中
    Daily daily = new Daily();
    daily.setRegisterNum(num);
    daily.setDateCalculated(day);
    daily.setLoginNum(RandomNum.createRandomNum());
    daily.setVideoViewNum(RandomNum.createRandomNum());
    daily.setCourseNum(RandomNum.createRandomNum());

    int result = this.baseMapper.insert(daily);

    if (result == 0){
        throw new InsertException(30001,"插入统计数据异常!");
    }
}
~~~

- <font color='red'>**使用定时任务完成统计分析**</font>

①在主启动类上添加@EnableScheduLing注解

②创建定时任务类并给spring管理，使用cron表达式(又称七子表达式或七域表达式)

七子表达式(七域表达式)：秒 分 时 日 月 周 年  --> [在线cron表达式生成器](https://www.pppet.net/)

③在需要执行定时任务的方法上使用@Scheduled注解

~~~java
@Component
public class ScheduleTask {

    @Autowired
    private IDailyService dailyService;

    //0/5 * * * * ?表示每到每天凌晨1点执行一次此方法
    @Scheduled(cron = "0 0 1 * * ? ")
    public void ScheduleTask(){
        //获取前一天的日期
        LocalDate date = LocalDate.now().plusDays(-1);
        //执行查询并添加操作
        dailyService.countRegisterNumInOneDay(date.toString());
    }
}
~~~

- 整合echarts，实现数据的图表显示

~~~java
//查询图标显示数据  控制层
@GetMapping("/showTableData/{type}/{begin}/{end}")
public JsonResult<Map<String,Object>> showTableData(@PathVariable("type") String type,
                                                    @PathVariable("begin") String begin,
                                                    @PathVariable("end") String end){
    Map<String,Object> data = dailyService.showTableData(type,begin,end);
    return new JsonResult<>(true,"查询成功",data);
}

//根据类型和时间查询图标数据  业务层
@Override
public Map<String, Object> showTableData(String type, String begin, String end) {
    QueryWrapper<Daily> wrapper = new QueryWrapper<>();
    //select()方法是根据指定的type字段进行查询
    //between()方法是在指定的时间范围之内
    wrapper.select("date_calculated",type).between("date_calculated",begin,end);
    List<Daily> list = this.baseMapper.selectList(wrapper);

    //拼凑返回前端的数组
    List<String> dateList = new ArrayList<>();
    List<Integer> numList = new ArrayList<>();

    for (Daily d: list) {
        //封装日期
        dateList.add(d.getDateCalculated());
        //封装数字
        switch (type){
            case "register_num":
                numList.add(d.getRegisterNum());
                break;
            case "login_num":
                numList.add(d.getLoginNum());
                break;
            case "video_view_num":
                numList.add(d.getVideoViewNum());
                break;
            case "course_num":
                numList.add(d.getCourseNum());
                break;
        }
    }
    //封装到map中并返回
    HashMap<String, Object> map = new HashMap<>();
    map.put("date",dateList);
    map.put("num",numList);

    return map;

}
~~~

---

## CMS管理

### 前端页面

①在api文件下创建comment.js文件并编写被调用的方法

~~~javascript
import request from '@/utils/request'
export default{
    queryBannerList(pageNum,PageSize){
      return request({
        url:`/servicecms/banneradmin/query/${pageNum}/${PageSize}`,
        method: 'get'
      })
    },
    addBanner(banner){
      return request({
        url:'/servicecms/banneradmin/add',
        method: 'post',
        data: banner
      })
    },
    updateBanner(banner){
      return request({
        url:'/servicecms/banneradmin/update',
        method: 'post',
        data: banner
      })
    },
    delBanner(id){
      return request({
        url:`/servicecms/banneradmin/del/${id}`,
        method: 'post'
      })
    },
    queryBanner(id){
      return request({
        url:`/servicecms/banneradmin/query/${id}`,
        method: 'get'
      })
    },
    queryBannerByTitle(title,pageNum,pageSize){
      return request({
        url:`/servicecms/banneradmin//queryByTitle/${title}/${pageNum}/${pageSize}`,
        method: 'get'
      })
    }
}
~~~

②在对应页面进行调用

### 后端接口

①通过代码生成器快速生成对应实体类等

②在service模块下创建子模块service_cms

③改pom文件(使用父类依赖，不需要导入什么)

④编写yml配置文件

⑤主启动类

⑥业务类

- 所有过程与评论管理模块相似且篇幅过长，不再此赘述，可到service_cms下细看

---

## 评论管理

### 前端页面

①在api文件下创建comment.js文件并编写被调用的方法

~~~javascript
import request from '@/utils/request'
export default{
    getAllComment(pageNum,pageSize,VOComment){
        return request({
            url: `/servicecomment/getAllComment/${pageNum}/${pageSize}`,
            method:'post',
            data:VOComment
        })
    },
    delComment(commentIds){
        return request({
            url: `/servicecomment/del/${commentIds}`,
            method:'post'
        })
    },
    reportComment(commentId){
        return request({
            url:`/servicecomment/report/${commentId}/0`,
            method:'post'
        })
    },
    showTeacher(){
        return request({
            url:`/servicecomment/showTeacher`,
            method:'get'
        })
    },
    showCourse(){
        return request({
            url:`/servicecomment/showCourse`,
            method:'get'
        })
    }
}
~~~

②在对应页面进行调用

### 后端接口

①通过代码生成器快速生成对应实体类等

②在service模块下创建子模块service_comment

③改pom文件(使用父类依赖，不需要导入什么)

④编写yml配置文件

~~~yml
server:
  port: 8009

spring:
  application:
    name: service-comment
  profiles:
    active: dev
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/online_edu?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    username: root
    password: root
  #设置时间格式
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  #将edu_service注册到nacos服务注册中心
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

#暴露所有的监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
  #设置不检查redis的健康状态，因为引入了actuator依赖会自带检查一些东西的健康状态
  #因为还没用到redis，若不设置会报一个无法连接redis的错误
  health:
    redis:
      enabled: false

#一旦使用xml映射文件就必须设置这个路径,建议每次在创建时就加上
mybatis-plus:
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
~~~

⑤主启动类

⑥业务类

- 篇幅过长，不再此赘述，可到service_comment下细看

---

## 订单管理

### 前端页面

①在api文件下创建comment.js文件并编写被调用的方法

```javascript
import request from '@/utils/request'

export default{
    getOrders(pageNum,pageSize,voOrder,status){
        return request({
            url: `/serviceorder/order/allOrder/${pageNum}/${pageSize}/${status}`,
            method: 'post',
            data: voOrder,
        })
    },
    delOrder(ids){
        return request({
            url: `/serviceorder/order/del/${ids}`,
            method: 'post',
        })
    }
}
```

②在对应页面进行调用

### 后端接口

①通过代码生成器快速生成对应实体类等

②在service模块下创建子模块service_order

③改pom文件(使用父类依赖，不需要导入什么)

④编写yml配置文件

⑤主启动类

⑥业务类

- 篇幅过长，不再此赘述，可到service_order下细看

---

## 权限管理

### 整合Spring Security

- 整合步骤

①在公共模块下创建子模块Spring Security

②引入依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>top.year21.onlineedu</groupId>
        <artifactId>common_utils</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <!-- Spring Security依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>

    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt</artifactId>
    </dependency>
</dependencies>
~~~

③创建下图的核心配置类、实体类、过滤器、工具类等



![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220904151051702.png)

④在acl模块中引入spring security的依赖

⑤在acl模块中创建查询用户登录权限类并实现UserDetails接口，以接收和保存查询到的登录信息

⑥在acl模块中创建一个业务类并实现UserDetailsService接口，在这个实现类中编写查询数据库的逻辑

- 用户认证和用户授权的过程

①TokenLoginFilter过滤器会在attemptAuthentication方法中通过请求拿到登录的用户信息

②通过实现类重写的loadUserByUsername的方法中判断用户登录信息是否正确并查询该用户

对应的权限列表保存在实现了UserDetails接口的自定义用户登录权限类中，决定调用认证

成功的方法还是认证失败的方法

③如果认证成功，调用认证成功的方法，在这个方法中根据用户名生成token并返回，在返回之前

将用户名和其对应的权限列表存入redis缓存中

④在前端接收到token后会放入cookie中，而在每次发起访问请求又会将token从cookie中取出

放到请求头上。

⑤后端接受到请求后，TokenAuthenticationFilter过滤器的UsernamePasswordAuthenticationToken

方法会从请求头中取出token，根据token获取用户名，再利用用户名从redis中获取到对应用户的访问

权限列表，最终进行授权

### 前端页面

- 前端页面需要替换很多文件，在此不再赘述

### 后台接口

目的：不同角色的用户在登录后，在后台管理系统中应拥有不同的菜单和功能权限

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220904004526222.png)

- 步骤

①在service模块下创建子模块service_acl

②改pom文件

~~~xml
<dependencies>
    <dependency>
        <groupId>top.year21.onlineedu</groupId>
        <artifactId>spring_security</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
    </dependency>
</dependencies>
~~~

③编写yml配置文件

~~~yml
server:
  port: 8011
spring:
  application:
    name: service-acl
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/online_edu?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    username: root
    password: root
  #设置时间格式
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8

mybatis-plus:
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
~~~

④业务类

- 递归查询所有菜单思路：

  先查询出所有的数据，遍历所有数据列表根据pid值为1查询出所有的顶级分类列表，

  遍历顶级分类列表，通过一个递归方法传入当前顶级分类对象和所有的数据列表，将当前

  顶级分类的id与数据列表每一个对象的pid做对比，当相同则代表此对象为当前顶级分类下

  的子分类，则添加进当前顶级分类的子节点数组中，此后将此对象和所有的数据列表中放入

  递归方法中，反复调用，直至循环结束

- 递归删除菜单思路：

  先根据指定的id查询所有的子节点，判断是否有子节点，有则反复调用自己，本身就是递归方法

  没有则直接删除即可。

~~~java
@Service
public class PermissionServiceImpl extends ServiceImpl<PermissionMapper, Permission> implements IPermissionService {

    //获取所有权限列表
    @Override
    public List<Permission> queryAllPermission() {
        //获取所有数据
        QueryWrapper<Permission> wrapper = new QueryWrapper<>();
        wrapper.orderByDesc("id");
        List<Permission> permissionList = this.baseMapper.selectList(wrapper);

        //封装成需要的树形结构
        List<Permission> resultList = buildNeedTreePermission(permissionList);

        return resultList;
    }

    //封装树形结构的具体方法
    public static List<Permission> buildNeedTreePermission(List<Permission> permissionList) {

        //用于封装权限树形结构的数组
        List<Permission> finalTreePermissionList = new ArrayList<>();

        //遍历获取顶层权限并设置其等级为1
        for (Permission p: permissionList) {
            //顶层权限
            if ("0".equals(p.getPid())){
                //设置等级为1
                p.setLevel(1);
                //获取子节点
                finalTreePermissionList.add(selectChildren(p,permissionList));
            }
        }
        return finalTreePermissionList;
    }

    //获取某个一级菜单下的所有子节点
    public static Permission selectChildren(Permission p, List<Permission> permissionList) {
        //先将当前一级菜单的子节点对象初始化
        p.setChildren(new ArrayList<>());

        //封装子节点并递归查询所有子节点
        for (Permission obj : permissionList ){
            if (p.getId().equals(obj.getPid())){ //说明此obj是当前一级菜单的子节点
                //设置obj对象为二级菜单
                obj.setLevel(p.getLevel()+1);
                //放入一级菜单的子节点列表中
                p.getChildren().add(selectChildren(obj,permissionList));
            }
        }
        return p;
    }

    //删除菜单(递归删除所有节点)
    @Override
    public void delPermissionById(String id) {
        //先查询是否有子节点
        QueryWrapper<Permission> wrapper = new QueryWrapper<>();
        wrapper.eq("pid",id);
        List<Permission> delList = this.baseMapper.selectList(wrapper);

        if (delList.size() != 0){//表示有子节点，需要递归删除
            for (Permission node : delList){
                delPermissionById(node.getId());
            }
        }

        //表示没有子节点，直接删除
        int result = this.baseMapper.deleteById(id);
        if (result == 0){
            throw new CommonException(30001,"删除菜单失败");
        }
    }
    
    //给角色分配权限
    @Override
    public void givePermission(String roleId, String[] permissionIds) {
        //创建封装RolePermission的集合
        List<RolePermission> rolePermissionList = new ArrayList<>();
        //遍历permissionIds
        for (String pid : permissionIds){
            RolePermission rolePermission = new RolePermission();
            rolePermission.setRoleId(roleId);
            rolePermission.setPermissionId(pid);
            rolePermissionList.add(rolePermission);
        }
        //插入数据库
        rolePermissionService.saveBatch(rolePermissionList);
    }
}
~~~

### 登录过程分析

> 好像没被spring security放行的请求都会每次都会被拦截去请求selectPermissionValueByUserId
>
> 方法去获取用户id对应的权限列表 做鉴权

①在前端发送登录请求之后，在拦截器中对登录用户进行判断，判断用户存在之后会去查询用户信息和

其对应权限列表，并将这些信息保存在spring security保存登录信息的实体类中

②在经过登录拦截器之后调用登录成功的方法根据用户名生成的token并返回到前端，将用户名和用户名

对应的权限值列表存储到redis中，而在前端会将该token存储在cookie中，在接下来的任何请求的请求头

中都携带着token信息，在每次发起访问请求又会将token从cookie中取出放到请求头上。

③<font color='red'>**并且由于vue配置了全局路由卫士permission.js在每访问一个页面都会查看token判断是否登录，**</font>

经过上面的操作此时token时有登录信息的，因此会发起请求获取对应用户的信息和能够访问的菜单

④后端在接受请求后会从请求头中取token信息中，根据token信息获取用户名，根据用户名从redis中

查询数据，给该用户授权，再发送info请求获取用户信息的请求并返回查询出的数据，在这个方法中会

获取用户信息(页面展示需要)、用户角色列表(页面展示需要)、用户权限列表(判断页面按钮是否显示需要)

前端会将这些信息存储在vuex负责管理应用状态的store对象中

⑤后端还会接受到menu动态获取对应用户的路由菜单的请求，在这个方法中，会先判断用户角色，

如果是管理员获取全部，否则只获取对应用户的权限列表，通过递归方法获得权限列表，再进行封装，

最后返回类似前端路由形式的数据。前端接受这些数据后，经过vue渲染，从而能够在侧边栏和路由界面

进行访问

---

# 前台系统

使用NUXT框架搭建前台系统，NUXT是一种服务端渲染技术框架，且NUXT是对node.js进行了封装

- 搭建步骤

1、使用NUXT提供的starter-template-master模板文件

starter-template-master模板是基于Vue实现的

2、将模板文件中的template文件中的内容解压到VSCode的工作区的某个文件中

3、修改模板文件中的配置信息

4、通过集成终端键入npm install 根据package.json安装依赖

- 项目结构说明

.nuxt是前端工程编译后的文件，不允许更改

.asset是存储项目中使用的静态资源，js、img、css等

.component是项目中需要使用的公共组件

layouts中的default.vue是定义网页布局的方式

.node_modules是项目的依赖文件

pages是放在项目中的具体页面

nuxt.config.js是nuxy框架中的核心配置文件

（1）资源目录 assets

 用于组织未编译的静态资源如 LESS、SASS 或 JavaScript。

（2）组件目录 components

用于组织应用的 Vue.js 组件。Nuxt.js 不会扩展增强该目录下 Vue.js 组件，即这些组件不会像页面组件那样有 asyncData 方法的特性。

（3）布局目录 layouts

用于组织应用的布局组件。

（4）页面目录 pages

用于组织应用的路由及视图。Nuxt.js 框架读取该目录下所有的 .vue 文件并自动生成对应的路由配置。

（5）插件目录 plugins

用于组织那些需要在 根vue.js应用 实例化之前需要运行的 Javascript 插件。

（6）nuxt.config.js 文件

nuxt.config.js 文件用于组织Nuxt.js 应用的个性化配置，以便覆盖默认配置。

## NUXT路由

1、固定路由：路径是固定地址，不会发生变化

①使用router-link构建路由，通过设置to属性跳转到指定地址，地址是/course

- 当点击此路由，会先到page文件夹下找course文件夹，再从course文件夹下找到index.vue文件

~~~html
<router-link to="/course" tag="li" active-class="current">
    <a>课程</a>
</router-link>
~~~

②在page目录创建文件夹course ，在course目录创建index.vue

```html
<template>
<div>
<h1>
跳转过来了course
</h1>
</div>
</template>
```

2、动态路由：每次生成的路由地址都不一样

①NUXT的动态路由是以下划线开头的vue文件，参数名为下划线后边的文件名

②在pages下的course目录下创建_id.vue

---

## 引入axios

- 由于NUXT框架没有像vue一样自带封装了ajax的axios，因此需要手动引入并封装

在一个文件夹下创建request.js文件并写入以下内容

~~~javascript
import axios from 'axios'
// 创建axios实例
const service = axios.create({
  baseURL: 'http://192.168.231.134:9001', // api的base_url
  timeout: 20000 // 请求超时时间
})
export default service
~~~

## 整合redis

<font color='red'>**一般将经常进行查询的，不太重要的，而又不经常修改的数据放入nosql的redis(基于内存的数据库)中**</font>

### 整合步骤

①引入依赖

由于redis缓存是公共应用，所以把依赖与配置添加到common模块里，在这个模块的pom文件添加依赖

~~~xml
<!-- redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<!-- spring2.X集成redis所需common-pool2-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.6.0</version>
</dependency>
~~~

②创建redis缓存配置类，配置插件

~~~java
@Configuration
@EnableCaching //开启redis缓存注解
public class RedisConfig extends CachingConfigurerSupport {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setConnectionFactory(factory);
        //key序列化方式
        template.setKeySerializer(redisSerializer);
        //value序列化
        template.setValueSerializer(jackson2JsonRedisSerializer);
        //value hashmap序列化
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        return template;
    }

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        //解决查询缓存转换异常的问题
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        // 配置序列化（解决乱码的问题）,过期时间600秒
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(config)
                .build();
        return cacheManager;
    }
}
~~~

③在yml文件中设置redis的配置

~~~yml
spring:
  redis:
    port: 6379
    host: 192.168.231.134
    database: 0
    timeout: 1800000
    lettuce:
      pool:
        max-active: 20
        max-wait: 1
        #最大阻塞等待时间(负数表示无限制)
        max-idle: 5
        min-idle: 0
~~~

④在需要缓存的方法上添加注解

~~~java
@Override
/*@Cacheable注解表示将此查询方法的返回结果缓存在redis中
    value = "banner",key = "'selectIndexList'"两个属性构成了此缓存在redis中的名字
    且key的值还需要额外再添加一对''
*/
@Cacheable(value = "banner",key = "'selectIndexList'")
public List<Banner> queryAllBanner() {
    QueryWrapper<Banner> queryWrapper = new QueryWrapper<>();
    queryWrapper.orderByDesc("sort");
    //last方法，拼接sql语句
    queryWrapper.last("limit 2");
    return this.baseMapper.selectList(queryWrapper);
}
~~~

### SpringBoot缓存注解

（1）缓存@Cacheable，<font color='red'>**一般用在查询方法上**</font>

根据方法对其返回结果进行缓存，下次请求时，如果缓存存在，则直接读取缓存数据返回；

如果缓存不存在，则执行方法，并把返回的结果存入缓存中。

| **属性/方法名** | **解释**                                         |
| :-------------- | :----------------------------------------------- |
| value           | 缓存名，必填，它指定了你的缓存存放在哪块命名空间 |
| cacheNames      | 与 value 差不多，二选一即可                      |
| key             | 可选属性，可以使用 SpEL 标签自定义缓存的key      |

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220828005052215.png)

（2）缓存@CachePut，<font color='red'>**一般用在新增方法上**</font>

使用该注解标志的方法，每次都会执行，并将结果存入指定的缓存中。

其他方法可以直接从响应的缓存中读取缓存数据，而不需要再去查询数据库。

| **属性/方法名** | **解释**                                         |
| --------------- | ------------------------------------------------ |
| value           | 缓存名，必填，它指定了你的缓存存放在哪块命名空间 |
| cacheNames      | 与 value 差不多，二选一即可                      |
| key             | 可选属性，可以使用 SpEL 标签自定义缓存的key      |

（3）缓存@CacheEvict，<font color='red'>**一般用在更新或者删除方法上**</font>

使用该注解标志的方法，会清空指定的缓存。

| **属性/方法名**  | **解释**                                                     |
| ---------------- | ------------------------------------------------------------ |
| value            | 缓存名，必填，它指定了你的缓存存放在哪块命名空间             |
| cacheNames       | 与 value 差不多，二选一即可                                  |
| key              | 可选属性，可以使用 SpEL 标签自定义缓存的key                  |
| allEntries       | 是否清空所有缓存，默认为 false。如果指定为 true，则方法调用后将立即清空所有的缓存 |
| beforeInvocation | 是否在方法执行前就清空，默认为 false。如果指定为 true，则在方法执行前就会清空缓存 |

## banner轮播图

### 数据表

- 在数据库中创建一个新表维护banner所需的图片

~~~mysql
CREATE TABLE `crm_banner` (
  `id` char(19) NOT NULL DEFAULT '' COMMENT 'ID',
  `title` varchar(20) DEFAULT '' COMMENT '标题',
  `image_url` varchar(500) NOT NULL DEFAULT '' COMMENT '图片地址',
  `link_url` varchar(500) DEFAULT '' COMMENT '链接地址',
  `sort` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '排序',
  `is_deleted` tinyint(1) unsigned NOT NULL DEFAULT '0' COMMENT '逻辑删除 1（true）已删除， 0（false）未删除',
  `gmt_create` datetime NOT NULL COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_name` (`title`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='首页banner表';
~~~

### 前端页面

- 需要先配置nginx的代理转发规则

①创建用于被调用的js文件

~~~javascript
import request from '@/utils/request'

export default{
    getBanner(){
        return request({
            url: '/servicecms/bannerfront/queryAll',
            method: 'get'
        })
    }
}
~~~

②在对应页面进行调用

~~~javascript
methods: {
getBanner(){
   banner.getBanner()
      .then(response => {
        this.bannerList = response.data.data;
        })
}
~~~

### 后端接口

①在父工程service下创建子模块service-cms

②改pom文件

③编写yml配置文件

~~~yml
server:
  port: 8004

spring:
  application:
    name: service-cms
  profiles:
    active: dev
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/online_edu?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    username: root
    password: root
  #设置时间格式
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
#一旦使用xml映射文件就必须设置这个路径,建议每次在创建时就加上
mybatis-plus:
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
~~~

④主启动类需要加上这些注解

```java
@ComponentScan(basePackages = {"top.year21.onlineedu"}) //扫描整个项目中top.year21.onlineedu包下的组件，为了能够用其他模块的公用组件
@MapperScan("top.year21.onlineedu.servicecms.mapper")//配置扫描mapper在那个位置，也需在yml中配置mapper映射文件的位置
```

⑤业务类

- 创建两个controller层区分前台系统和后台系统的请求

~~~java
@RestController
@RequestMapping("/servicecms/banneradmin")
@CrossOrigin(origins = "*",maxAge = 3600)
public class BannerAdminController {

    @Autowired
    private ICrmBannerService bannerService;

    //分页查询banner
    @GetMapping("/query/{pageNum}/{pageSize}")
    public JsonResult<Page<Banner>> queryBannerByCondition(@PathVariable("pageNum") Integer pageNum,
                                                           @PathVariable("pageSize") Integer pageSize){
        Page<Banner> page = bannerService.page(new Page<>(pageNum, pageSize));
        return new JsonResult<>(true,"查询成功",page);
    }

    //添加banner
    @PostMapping("/add")
    public JsonResult<Void> addBanner(@RequestBody Banner banner){
        bannerService.save(banner);
        return new JsonResult<>(true);
    }

    //修改banner
    @PostMapping("/update")
    public JsonResult<Void> updateBanner(@RequestBody Banner banner){
        bannerService.update(banner,null);
        return new JsonResult<>(true);
    }

    //删除banner
    @PostMapping("/del/{id}")
    public JsonResult<Void> delBanner(@PathVariable("id") String id){
        bannerService.removeById(id);
        return new JsonResult<>(true);
    }
}

@RestController
@RequestMapping("/servicecms/bannerfront")
@CrossOrigin(origins = "*",maxAge = 3600)
public class BannerFrontController {

    @Autowired
    private ICrmBannerService bannerService;

    //查询所有banner
    @GetMapping("/queryAll")
    public JsonResult<List<Banner>> queryBanners(){
        List<Banner> banners = bannerService.queryAllBanner();
        return new JsonResult<>(true,"查询成功",banners);
    }
}
~~~

## 首页展示

- 首页需要显示热门课程和部分讲师信息

### 前端页面

①创建用于被调用的js文件

~~~javascript
import request from '@/utils/request'

export default{
    getHotCourse(){
        return request({
            url: '/serviceedu/front/querycourse',
            method:'get'
        })
    },
}

import request from '@/utils/request'

export default{
    getSortTeacher(){
        return request({
            url: '/serviceedu/front/queryteaher',
            method:'get'
        })
    },
}
~~~

②在对应页面进行调用

~~~javascript
methods: {
getHotCourse(){
      course.getHotCourse()
        .then(response => {
          this.courseList = response.data.data
        })
    },
    getSortTeacher(){
      teacher.getSortTeacher()
        .then(response => {
          this.teacherList = response.data.data
        })
    },
}
~~~

### 后端接口

①控制层

- 这个控制层本应该写在service_cms模块中，但由于使用到了teacher和course类，为了方便就写在service_edu中

~~~java
@RestController
@RequestMapping("/serviceedu/front")
@CrossOrigin(origins = "*",maxAge = 3600)
public class FrontIndexController {

    @Autowired
    private IEduCourseService eduCourseService;
    @Autowired
    private IEduTeacherService eduTeacherService;

    //查询创建时间最新的四名老师
    @GetMapping("/queryteaher")
    public JsonResult<List<EduTeacher>> queryTeacherByCondition(){
        List<EduTeacher> list = eduTeacherService.getSortTeacher();
        return new JsonResult<>(true,"查询成功",list);
    }

    //查询前8条热门课程
    @GetMapping("/querycourse")
    public JsonResult<List<EduCourse>> queryCourseByCondition(){
        List<EduCourse> list =  eduCourseService.getHotCourse();
        return new JsonResult<>(true,"查询成功",list);
    }
}
~~~

②业务层

~~~java
public interface IEduCourseService extends IService<EduCourse> {
//查询排序后的课程信息，用于首页展示
List<EduCourse> getHotCourse();
}

//查询排序后的课程信息，用于首页展示
@Override
@Cacheable(value = "course",key = "'selectCourse'")
public List<EduCourse> getHotCourse() {
    QueryWrapper<EduCourse> queryWrapper = new QueryWrapper<>();
    queryWrapper.orderByDesc("view_count")
        .ne("is_deleted",1)
        .last("limit 8");
    return this.baseMapper.selectList(queryWrapper);
}

public interface IEduTeacherService extends IService<EduTeacher> {
    //查询排序后的讲师信息，用于首页展示
    List<EduTeacher> getSortTeacher();
}

//查询排序后的讲师信息，用于首页展示
@Override
@Cacheable(value = "teacher",key = "'selectTeacher'")
public List<EduTeacher> getSortTeacher() {
    QueryWrapper<EduTeacher> queryWrapper = new QueryWrapper<>();
    queryWrapper.orderByDesc("gmt_create")
        .ne("is_deleted",1)
        .last("limit 4");
    return this.baseMapper.selectList(queryWrapper);
}
~~~

---

## 注册功能

### 前端页面

①由于nuxt没有集成element-ui，因此需要下载此依赖

npm install element-ui

②在plugins创建js文件进行引用

~~~javascript
import ElementUI from 'element-ui' //element-ui的全部组件
import 'element-ui/lib/theme-chalk/index.css'//element-ui的css
Vue.use(ElementUI) //使用elementUI
~~~

③在layout文件夹中创建一个vue文件，目的是为注册页面换个页面布局样式

~~~html
<template>
    <div class="sign">
        <!-- 标题 -->
        <div class="logo">
            <img src="~/assets/img/logo.png" alt="logo"/>
        </div>
        <!-- 表单 -->
        <nuxt/>
    </div>
</template>
~~~

④在pages文件夹中创建注册页面，前提是先修改default.vue中关于注册页面的路由地址

⑤在api文件夹下创建register.js文件，编写需要被调用的方法

~~~javascript
import request from '@/utils/request'

export default{
    userRegister(VoRegister){
        return request({
            url: '/serviceusercenter/register',
            method: 'post',
            data: VoRegister
        })
    },
    getCode(phone){
        return request({
            url: '/servicemsm/send/'+ phone,
            method: 'get'
        })
    }
}
~~~

⑥在对应页面进行调用即可

### 短信注册

- 此处是购买了阿里云的第三方短信接口

①在service模块下创建service_msm模块，处理短信服务

②在service_msm的pom文件加入下列依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>com.aliyun</groupId>
        <artifactId>aliyun-java-sdk-core</artifactId>
    </dependency>
    <!--    短信服务和HttpUtils所需依赖    -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.httpcomponents</groupId>
        <artifactId>httpclient</artifactId>
        <version>4.3.4</version>
    </dependency>
    <dependency>
        <groupId>org.apache.httpcomponents</groupId>
        <artifactId>httpcore</artifactId>
        <version>4.3.3</version>
    </dependency>
    <dependency>
        <groupId>commons-lang</groupId>
        <artifactId>commons-lang</artifactId>
        <version>2.6</version>
    </dependency>
    <dependency>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-util</artifactId>
        <version>9.3.7.v20160115</version>
    </dependency>
</dependencies>
~~~

③编写yml配置文件

~~~yml
server:
  port: 8005
spring:
  application:
    name: service-msm
  profiles:
    active: dev
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/online_edu?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    username: root
    password: root
  #设置时间格式
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
  redis:
    port: 6379
    host: 192.168.231.134
    database: 0
    timeout: 1800000
    lettuce:
      pool:
        max-active: 20
        max-wait: 1
        #最大阻塞等待时间(负数表示无限制)
        max-idle: 5
        min-idle: 0

mybatis-plus:
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
~~~

④主启动类

⑤业务类

- 控制层

~~~java
@RestController
@RequestMapping("/servicemsm")
@CrossOrigin(value = "*",maxAge = 3600)
public class MsgController {

    @Autowired
    private MsgService msgService;
    @Autowired
    private RedisTemplate<String,String> redisTemplate;

    @GetMapping("/send/{phone}")
    public JsonResult<Void> sendMsg(@PathVariable("phone") String phone){
        //从redis中获取验证码
        String code = redisTemplate.opsForValue().get(phone);
        if (!StringUtils.isEmpty(code)){
            return new JsonResult<>(true);
        }
        //如果不能从redis中取到验证码则调用方法生成验证码
        code = RandomUtil.getFourBitRandom();
        //调用service发送短信的方法
        Boolean result = msgService.sendMessage(phone,code);
        if (result){
            /*发送成功则将验证码放入到redis中并设置过期时间
            set()中的参数说明，第一个是key，第二个是value，第三个是过期时间，第四个是时间单位
             */
            redisTemplate.opsForValue().set(phone,code,5, TimeUnit.MINUTES);
            return new JsonResult<>(true,"短信发送成功",null);
        }else {
            return new JsonResult<>(false,"短信发送失败",null);
        }
    }
}
~~~

- 业务层

~~~java
@Service
public class MsgServiceImpl implements MsgService {
    @Override
    public Boolean sendMessage(String phone, String code) {
        //判断验证码是否为空
        if ("".equals(phone)){
            return false;
        }
        String host = "https://gyytz.market.alicloudapi.com";
        String path = "/sms/smsSend";
        String method = "POST";
        String appcode = "e9323c6105394949a1c1a7dbb05253f0";
        Map<String, String> headers = new HashMap<String, String>();
        //最后在header中的格式(中间是英文空格)为Authorization:APPCODE 83359fd73fe94948385f570e3c139105
        headers.put("Authorization", "APPCODE " + appcode);
        Map<String, String> querys = new HashMap<String, String>();
        querys.put("mobile",phone);
        querys.put("param", "**code**:" + code);
        querys.put("smsSignId", "2e65b1bb3d054466b82f0c9d125465e2");
        querys.put("templateId", "63698e3463bd490dbc3edc46a20c55f5");
        Map<String, String> bodys = new HashMap<String, String>();

        try {
            /**
             * 重要提示如下:
             * HttpUtils请从
             * https://github.com/aliyun/api-gateway-demo-sign-java/blob/master/src/main/java/com/aliyun/api/gateway/demo/util/HttpUtils.java
             * 下载
             *
             * 相应的依赖请参照
             * https://github.com/aliyun/api-gateway-demo-sign-java/blob/master/pom.xml
             */
            HttpResponse response = HttpUtils.doPost(host, path, method, headers, querys, bodys);
            System.out.println(response.toString());
            //获取请求头中的响应状态码判断短信发送情况
            int statusCode = response.getStatusLine().getStatusCode();
            if (statusCode == 200){
                return true;
            }
            //获取response的body
            //System.out.println(EntityUtils.toString(response.getEntity()));
        } catch (Exception e) {
            e.printStackTrace();
        }
        return false;
    }
}
~~~

### 后台接口

①创建一个值对象VOregister封装注册的数据

~~~java
@Data
public class VORegister {
    private String phone;
    private String nickName;
    private String password;
    private String code;
}
~~~

②业务层

~~~java
//注册
@PostMapping("/register")
public JsonResult<Void> userRegister(@RequestBody VORegister voRegister){
    userCenterService.userRegister(voRegister);
    return new JsonResult<>(true);
}
~~~

③控制层

~~~java
//用户注册
@Override
public void userRegister(VORegister voRegister) {
    //获取输入的手机号
    String phone = voRegister.getPhone();

    //判断手机号是否已被注册
    QueryWrapper<UserCenter> queryWrapper = new QueryWrapper<>();
    queryWrapper.eq("mobile",phone);
    Long count = this.baseMapper.selectCount(queryWrapper);
    if (count != 0){
        throw new CommonException(30001,"手机号已注册！");
    }

    //根据手机号获取redis中的验证码是否可用
    String redisCode = redisTemplate.opsForValue().get(phone);
    if (StringUtils.isEmpty(redisCode)){
        throw new CommonException(30001,"验证码已过期,注册失败，请重新获取验证码");
    }

    //获取输入的验证码
    String inputCode = voRegister.getCode();
    //判断验证码是否正确
    if (!inputCode.equals(redisCode)){
        throw new CommonException(30001,"验证码错误,注册失败");
    }

    //获取输入的密码进行加密操作
    String inputPwd = voRegister.getPassword();
    //将此时的时间戳作为盐值
    String salt = UUID.randomUUID().toString().toUpperCase();

    //进行加密
    String pwdByMd5 = PasswordEncryptedUtils.getPasswordByMD5(inputPwd, salt);

    //创建一个User对象封装这些数据
    UserCenter userCenter = new UserCenter();
    //填充属性
    userCenter.setMobile(phone);
    userCenter.setPassword(pwdByMd5);
    userCenter.setNickname(voRegister.getNickName());
    userCenter.setAvatar("https://onlineedufile.oss-cn-guangzhou.aliyuncs.com/2022-08-23/ee578e07-753c-4ddc-8ff3-73ff79f749d8.jpg");
    //保存盐值用于登录验证
    userCenter.setSalt(salt);

    //执行插入操作
    this.baseMapper.insert(userCenter);
}
~~~

---

## 登录功能

<font color='red'>**单点登录模式：在任一模块登录后，访问其他模块不需要进行二次登录**</font>

本项目的单点登录，利用了JWT框架

- SSO(single sign on)模式 也称为单点登录模式，有三种方式

1、通过session广播机制实现，通过将session复制到其他模块中，缺点是比较浪费资源

2、通过cookie+redis实现

步骤一：在任一模块登录后，将数据放入redis和cookie中

①redis：在key：生成唯一随机值(ip、用户id等)，value：用户数据

②cookie：把redis里面生成的key值放到cookie中

步骤二：在访问其他模块时，发送的请求中携带cookie，通过获取此cookie的值，到redis中进行查询，

根据此cookie即key查询，如果能查询到数据代表已经登录

3、通过token实现

token是按照一定规则生成的字符串，而这个字符串中可以包含用户信息

步骤一：在某个模块进行登录之后，将按照规则生成的token字符串通过cookie或地址栏参数返回

步骤二：当再去访问其他模块时，在每次访问时在地址栏或者cookie中都会携带token字符串，从

地址栏或者cookie中获取token，根据此token获取用户信息，如果获取成功则表示是登录状态

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220828154023588.png)

### 前端页面

- 前提步骤

①在pages文件夹中创建登录页面，前提是先修改default.vue中关于登录页面的路由地址

②下载 cookie插件，用于传输用户数据，npm install js-cookie

③在api文件夹下创建login.js文件，编写需要被调用的方法

~~~javascript
import request from '@/utils/request'
export default{
    userLogin(user){
        return request({
            url: '/serviceusercenter/login',
            method: 'post',
            data: user
        })
    },
    getInfoByToken(){
        return request({
            url: '/serviceusercenter/queryInfo',
            method: 'get'
        })
    },  
}
~~~

③在对应页面进行调用即可

- 实现思路

①在登录成功后，将服务器生成的token返回

②将返回的token存入cookie中，根据此token去服务器中调用接口查询用户数据

③创建拦截器，在请求正在发送之前，先拦截判断cookie中是否存储了token，如果发现存储了，

则从cookie中取出放入到请求头中，因为后端接口就是从请求头中获取token的

~~~javascript
import cookie from 'js-cookie'
// 创建axios实例
const service = axios.create({
  baseURL: 'http://192.168.231.134:9001', // api的base_url
  timeout: 20000 // 请求超时时间
})

// http request 拦截器
service.interceptors.request.use(
  config => {
  //判断cookie中是否存储名为onlineedu_token
  if (cookie.get('onlineedu_token')) {
    //把获取的cookie值放到header中
    config.headers['token'] = cookie.get('onlineedu_token');
  }
    return config
  },
  err => {
  return Promise.reject(err);
})
~~~

④将经过服务接口查询返回的用户信息放入cookie中

~~~javascript
//用户登录
login(){
    //第一步
    login.userLogin(this.user)
        .then(response => {
        if(response.data.code === 20000){ 
            //第二步，获取生成的token字符串并放入cookie中
            let token = response.data.data
            //第一个参数为cookie名称，第二个参数为cookie值，第三个参数为此cookie的作用域范围，即只在localhost下传递
            cookie.set('onlineedu_token',token,{domian:'localhost'})

            //第三步，根据token获取用户信息并放入cookie中
            login.getInfoByToken()
                .then(response => {
                if(response.data.code === 20000){
                    this.userInfo = JSON.stringify(response.data.data)
                    cookie.set('userInfo',this.userInfo,{domian:'localhost'})
                }
            })   
            //提示
            this.$message.success("登录成功")

            //跳转至首页
            this.$router.push({path:"/"})

        }    
    })
        .catch(error =>{
        //提示
        this.$message.error(error.response.data.message)
    })
},
~~~

⑤在主页从cookie中取出用户信息，填充页面内容

~~~javascript
//从cookie中获取信息
getUserInfo(){
    //在cookie中只能存储字符串，因此必须把取出的字符串转化为json对象
    let user = cookie.get('userInfo')
    if(user){
        this.userInfo = JSON.parse(user)
    }
},
//退出登录
logout(){
   //清空cookie
    cookie.set("userInfo",'',{domain:'localhost'})
    cookie.set('onlineedu_token','',{domian:'localhost'})
	 this.$message.success("退出成功")
    window.location.href = '/'
}
~~~

### 整合微信登录

- 步骤

①在service-usercenter模块中的yml文件配置微信相关的信息

~~~yml
#微信登录配置信息
wx:
  open:
    # 微信开放平台 appid
    appid: wxed9954c01bb89b47
    # 微信开放平台 appsecret
    appsecret: a7482517235173ddb4083788de60b90e
    # 微信开放平台 重定向url
    redirecturl: http://localhost:8160/wx/callback
~~~

②创建一个类读取配置yml配置文件的内容

~~~java
@Component
public class WXUtils {

    @Value("${wx.open.appid}")
    private String appId;

    @Value("${wx.open.appsecret}")
    private String appSecret;

    @Value("${wx.open.redirecturl}")
    private String redirectUrl;

    public static String WX_OPEN_APP_ID;
    public static String WX_OPEN_APP_SECRET;
    public static String WX_OPEN_REDIRECT_URL;


    @PostConstruct
    public void init(){
        WX_OPEN_APP_ID = appId;
        WX_OPEN_APP_SECRET = appSecret;
        WX_OPEN_REDIRECT_URL = redirectUrl;
    }
}
~~~

③创建一个controller用于创建扫描登录的二维码

④在扫描二维码后从控制类中获取code(随机唯一的值，用于识别身份)

⑤用获取到的code值请求微信提供的固定地址，获取access_token(访问凭证)和openId(区分不同标识)

⑥用access_token和openId再去获取微信提供的固定地址，从而得到微信扫码人的信息

~~~java
@Controller
@RequestMapping("/api/ucenter/wx")
@CrossOrigin(origins = "*",maxAge = 3600)
@Slf4j
public class WxController {

    @Autowired
    private IUserCenterService userCenterService;

    //1.生成微信扫描二维码
    @GetMapping("/login")
    public String getWxCode(){

        // 微信开放平台授权baseUrl %s相当于一个占位符
        String baseUrl = "https://open.weixin.qq.com/connect/qrconnect" +
            "?appid=%s" +
            "&redirect_uri=%s" +
            "&response_type=code" +
            "&scope=snsapi_login" +
            "&state=%s" +
            "#wechat_redirect";

        //对redirect_url进行URLEncoder编码
        String redirectUrl = WXUtils.WX_OPEN_REDIRECT_URL;
        try {
            redirectUrl = URLEncoder.encode(redirectUrl,"utf-8");
        } catch (Exception e) {
            e.printStackTrace();
        }

        //填充占位符
        String codeUrl = String.format(
            baseUrl,
            WXUtils.WX_OPEN_APP_ID,
            WXUtils.WX_OPEN_REDIRECT_URL,
            "success");

        //返回微信请求地址
        return "redirect:" + codeUrl;
    }


    @GetMapping("/callback")
    public String callback(String code,String state){
        try {
            //获取code值，这个code相当于一个临时票据
            //拼接请求地址
            String baseAccessTokenUrl = "https://api.weixin.qq.com/sns/oauth2/access_token" +
                "?appid=%s" +
                "&secret=%s" +
                "&code=%s" +
                "&grant_type=authorization_code";
            //填充请求地址中的三个占位符，id和密匙和code值
            String accessTokenUrl = String.format(baseAccessTokenUrl,
                                                  WXUtils.WX_OPEN_APP_ID,
                                                  WXUtils.WX_OPEN_APP_SECRET,
                                                  code);

            //使用HttpClient 发送请求，通过code值向认证服务器获取access_token和openId
            String accessTokenInfo = HttpClientUtils.get(accessTokenUrl);
            log.info("accessTokenInfo:" + accessTokenInfo);

            //将access_token和openId从返回的字符串中取出
            //先将此字符串转换为map对象
            Gson gson = new Gson();
            HashMap<String,String> hashMap = gson.fromJson(accessTokenInfo, HashMap.class);
            String accessToken = hashMap.get("access_token");
            String openid = hashMap.get("openid");

            //将这些信息插入数据库，但需先判断此账户的openId是否已经存在
            QueryWrapper<UserCenter> wrapper = new QueryWrapper<>();
            wrapper.eq("openid",openid);
            UserCenter queryUser = userCenterService.getOne(wrapper);
            //queryUser == null表示新注册用户
            if (queryUser == null){
                //通过access_token和openId从微信提供的接口中获取扫码人的信息
                //拼接即将要请求的地址访问微信的即可，获取用户信息
                String baseUserInfoUrl = "https://api.weixin.qq.com/sns/userinfo" +
                    "?access_token=%s" +
                    "&openid=%s";
                //填充参数
                String userInfoUrl = String.format(baseUserInfoUrl, accessToken, openid);
                //通过HttpClientUtils模拟浏览器发送请求获取扫描人信息
                String userInfo = HttpClientUtils.get(userInfoUrl);
                log.info("userInfo:" + userInfo);

                //将扫码人信息字符串转为map对象，便于取数据
                Gson userGson = new Gson();
                HashMap<String,String> userMap = userGson.fromJson(userInfo, HashMap.class);
                String nickname = userMap.get("nickname");
                String headimgurl = userMap.get("headimgurl");
                //填充数据并插入数据库
                queryUser = new UserCenter();
                queryUser.setOpenid(openid);
                queryUser.setNickname(nickname);
                queryUser.setAvatar(headimgurl);
                userCenterService.save(queryUser);
            }

            //使用jwt框架生成token
            String token = JwtUtils.getJwtToken(queryUser.getId(), queryUser.getNickname());

            //注册成功后，跳转至首页显示信息
            return "redirect:http://localhost:3000?token=" + token;
        }catch (Exception e){
            throw new RuntimeException(e.getMessage());
        }
    }
}
~~~

⑦在首页中回显登录后的用户信息

~~~javascript
created() {
    //获取路径中的token值
    this.token = this.$route.query.token
    if(this.token){
        this.setTokenInCookie()
    }
    this.getUserInfo()
},
    methods: {
        //从cookie中获取信息
        getUserInfo(){
            //在cookie中只能存储字符串，因此必须把取出的字符串转化为json对象
            let user = cookie.get('userInfo')
            if(user){
                this.userInfo = JSON.parse(user)
            }
        },
            //将token值放入cookie中
            setTokenInCookie(){
                cookie.set("onlineedu_token",this.token,{domain:'localhost'})
                cookie.set("userInfo",'',{domain:'localhost'})
                //根据token查询用户信息
                login.getInfoByToken()
                    .then(response => {
                    if(response.data.code === 20000){
                        this.userInfo = JSON.stringify(response.data.data)
                        cookie.set('userInfo',this.userInfo,{domian:'localhost'})
                        //由于在created方法中函数的加载顺序不一定按顺序，为了防止缓存问题，在这里进行一次页面判断
                        this.getUserInfo()
                    }
                })   
            },
    }
~~~



### 后端接口

①在service模块下创建service_usercenter子模块

②创建数据表，根据数据表快速生成相关的实体类等

~~~mysql
CREATE TABLE `usercenter_member` (
  `id` CHAR(19) NOT NULL COMMENT '会员id',
  `openid` VARCHAR(128) DEFAULT NULL COMMENT '微信openid',
  `mobile` VARCHAR(11) DEFAULT '' COMMENT '手机号',
  `password` VARCHAR(255) DEFAULT NULL COMMENT '密码',
  `nickname` VARCHAR(50) DEFAULT NULL COMMENT '昵称',
  `sex` TINYINT(2) UNSIGNED DEFAULT NULL COMMENT '性别 1 女，2 男',
  `age` TINYINT(3) UNSIGNED DEFAULT NULL COMMENT '年龄',
  `avatar` VARCHAR(255) DEFAULT NULL COMMENT '用户头像',
  `sign` VARCHAR(100) DEFAULT NULL COMMENT '用户签名',
  `is_disabled` TINYINT(1) NOT NULL DEFAULT '0' COMMENT '是否禁用 1（true）已禁用，  0（false）未禁用',
  `is_deleted` TINYINT(1) NOT NULL DEFAULT '0' COMMENT '逻辑删除 1（true）已删除， 0（false）未删除',
  `gmt_create` DATETIME NOT NULL COMMENT '创建时间',
  `gmt_modified` DATETIME NOT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8mb4 COMMENT='会员表';
~~~

③修改子模块的pom文件

④编写yml配置文件

~~~yml
server:
  port: 8006

spring:
  application:
    name: user-center
  profiles:
    active: dev
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/online_edu?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    username: root
    password: root
  #设置时间格式
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  #将edu_service注册到nacos服务注册中心
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
  redis:
    port: 6379
    host: 192.168.231.134
    database: 0
    timeout: 1800000
    lettuce:
      pool:
        max-active: 20
        max-wait: 1
        #最大阻塞等待时间(负数表示无限制)
        max-idle: 5
        min-idle: 0

#一旦使用xml映射文件就必须设置这个路径,建议每次在创建时就加上
mybatis-plus:
  mapper-locations: classpath:mybatis/mapper/*.xml
~~~

⑤主启动类

⑥业务类

- 控制层

~~~java
//登录
@PostMapping("/login")
public JsonResult<String> userLogin(@RequestBody UserCenter user){
    //调用service方法实现登录并在登录成功后返回根据指定规则生成的token
    String userToken = userCenterService.userLogin(user);
    return new JsonResult<>(true,"登录成功",userToken);
}
~~~

- 业务层

~~~java
@Service
public class UserCenterServiceImpl extends ServiceImpl<UserCenterMapper, UserCenter> implements IUserCenterService {
    public String userLogin(UserCenter user) {
        //获取账户和密码
        String phone = user.getMobile();
        String inputPassword = user.getPassword();

        if (StringUtils.isEmpty(phone) || StringUtils.isEmpty(inputPassword)){
            throw new CommonException(30001,"账户名或密码为空，登录失败");
        }

        //根据账户名查询信息
        QueryWrapper<UserCenter> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("mobile",phone);
        UserCenter queryUser = this.baseMapper.selectOne(queryWrapper);

        //判断账户是否存在
        if (queryUser == null || queryUser.getIsDisabled()){
            throw new CommonException(30001,"账户名不存在，登录失败");
        }

        //根据查询的账户取得盐值和密码
        String salt = queryUser.getSalt();
        String dbPassword = queryUser.getPassword();
        //将输入的密码加密后与数据库密码对比
        String md5Pwd = PasswordEncryptedUtils.getPasswordByMD5(inputPassword, salt);
        if (!dbPassword.equals(md5Pwd)){
            throw new CommonException(30001,"密码错误，登录失败");
        }

        //如果不为空则代表登录成功
        String id = queryUser.getId();
        String nickname = queryUser.getNickname();
        //生成token并返回
        return JwtUtils.getJwtToken(id, nickname);
    }
}
~~~

- 根据token获取用户信息

~~~java
//根据token查询用户信息
@GetMapping("/queryInfo")
public JsonResult<UserCenter> queryInfoByToken(HttpServletRequest request){
    //通过request对象获取用户id
    String userId = JwtUtils.getMemberIdByJwtToken(request);
    //根据用户id查询数据并返回
    UserCenter loginUser = userCenterService.getById(userId);
    return new JsonResult<>(true,"查询成功",loginUser);
}
~~~

---

## 用户中心

### 前端页面

①在api文件下的login.js添加被调用的方法

~~~javascript
updateUserInfo(user){
    return request({
        url: `/serviceusercenter/update`,
        method: 'post',
        data: user
    })
},
updatePhotoById(uid,path){
    return request({
        url: `/serviceusercenter/updatePhoto/${uid}/${path}`,
        method: 'post',
    })
},
updatePwdById(uid,oldPwd,newPwd){
    return request({
        url: `/serviceusercenter/updatepwd/${uid}/${oldPwd}/${newPwd}`,
        method: 'post',
    })
}
~~~

②在对应页面进行调用

- 由于为了时间问题，将本该是多个页面全部凑在一个页面，因此页面多多少少存在点bug，

  可到usercenter文件夹的index.vue查看

### 后端接口

①在usercenter模块下编写接口

~~~java
//通过userId修改用户信息
@PostMapping("/update")
public JsonResult<Void> updateUserInfoById(@RequestBody UserCenter user){
    //根据id查询用户信息
    UpdateWrapper<UserCenter> wrapper = new UpdateWrapper<>();
    wrapper.eq("id",user.getId());
    if (!userCenterService.update(user,wrapper)){
        throw new CommonException(30001,"更新用户信息失败!");
    }
    return new JsonResult<>(true);
}

//根据userId修改用户头像
@PostMapping("/updatePhoto/{userId}/{path}")
public JsonResult<Void> updatePhotoById(@PathVariable("userId") String userId,
                                        @PathVariable("path") String path){
    String dbPath = decode(path);
    userCenterService.updatePhotoById(userId,dbPath);
    return new JsonResult<>(true);
}

/** 对base64码进行解码
* @param code 需要解码的base64字符串
* @return 解码之后的字符串
* */
public static String decode(String code){
    String str="";
    try {
        str=new String(Base64.decodeBase64(code),"utf-8");
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }
    return str;
}

@PostMapping("/updatepwd/{userId}/{oldPwd}/{newPwd}")
public JsonResult<Void> updatePwd(@PathVariable("userId") String userId,
                                  @PathVariable("oldPwd") String oldPwd,
                                  @PathVariable("newPwd") String newPwd){
    //先根据userId查找用户信息
    UserCenter user = userCenterService.getById(userId);
    //取出密码和盐值
    String salt = user.getSalt();
    String dbPassword = user.getPassword();

    //先判断密码是否正确
    String md5Password = PasswordEncryptedUtils.getPasswordByMD5(oldPwd, salt);
    if (!md5Password.equals(dbPassword)){
        throw new CommonException(30001,"原密码错误！");
    }

    //加密新密码
    String newPassword = PasswordEncryptedUtils.getPasswordByMD5(newPwd, salt);

    //更新密码
    user.setPassword(newPassword);
    boolean result = userCenterService.updateById(user);

    if (!result){
        throw new CommonException(30001,"密码更新失败！");
    }

    return new JsonResult<>(true);
}	
~~~

---

## 讲师及详情展示

### 前端页面

①在对应的页面进行调用并回填数据

~~~javascript
//讲师列表页面
<script>
import teacher from '@/api/teacher'
export default {
  //异步请求asyncData,这个请求只会发送一次
  //params相当于 this.$route.params 通过params.id 可以取到当前id值
  asyncData({ params, error }) {
  return teacher.getFrontTeacher(1, 8)
    .then(response => {
    return { data: response.data.data }
    })
  },
  methods: {
    pageQuery(pageNum){
      if(pageNum > this.data.pages || pageNum < 1 || pageNum === this.data.current ){
        return false;
      }
      teacher.getFrontTeacher(pageNum,8)
        .then(response =>{
            this.data = response.data.data
        })
    }
  },
}
</script>
//讲师详情页面
<script>
import teacher from '@/api/teacher'
export default {
    name:"teacherInfo",
    data() {
      return {
        teacherId: '',
        teacherInfo:{},
        courses: [],
      }
    },
    created() {
      this.teacherId = this.$route.params.id
      this.getTeacherByid()
    },
    methods: {
      getTeacherByid(){
        teacher.getTeacherByid(this.teacherId)
          .then(response => {
              this.teacherInfo = response.data.data.teacher
              this.courses = response.data.data.courses
          })
      }
    },
};
</script>
~~~

### 后端接口

- 在后端接口中查询讲师列表和对应讲师的信息

①控制层返回map类型对象

~~~java
//分页查询讲师
@GetMapping("/query/{pageNum}/{pageSize}")
public JsonResult<Map<String,Object>> queryTeacherOnIndex(@PathVariable("pageNum")Integer pageNum,
                                                          @PathVariable("pageSize")Integer pageSize){
    HashMap<String,Object> teacherList = eduTeacherService.getFrontTeacherList(pageNum,pageSize);
    return new JsonResult<>(true,"查询成功",teacherList);
}

@GetMapping("/query/{teacherId}")
    public JsonResult<HashMap<String,Object>> queryTeacherById(@PathVariable("teacherId") String teacherId){
        //先查询讲师信息
        EduTeacher teacher = eduTeacherService.getById(teacherId);

        //再查询相关讲师的课程
        QueryWrapper<EduCourse> wrapper = new QueryWrapper<>();
        wrapper.eq("teacher_id",teacher.getId());
        List<EduCourse> courses = eduCourseService.list(wrapper);
        HashMap<String, Object> map = new HashMap<>();
        map.put("teacher",teacher);
        map.put("courses",courses);
        return new JsonResult<>(true,"查询成功",map);
    }
~~~

②业务层执行查询

~~~java
 @Override
    public HashMap<String, Object> getFrontTeacherList(Integer pageNum, Integer pageSize) {
        //添加查询条件
        QueryWrapper<EduTeacher> wrapper = new QueryWrapper<>();
        wrapper.orderByDesc("id");
        Page<EduTeacher> page = new Page<>(pageNum,pageSize);
        this.baseMapper.selectPage(page,wrapper);
        //从page中取出数据，
        HashMap<String, Object> map = new HashMap<>();
        List<EduTeacher> lists = page.getRecords();
        long current = page.getCurrent();
        long pages = page.getPages();
        long size = page.getSize();
        long total = page.getTotal();
        boolean hasNext = page.hasNext();
        boolean hasPrevious = page.hasPrevious();

        //保存到map中并返回
        map.put("lists",lists);
        map.put("current",current);
        map.put("pages",pages);
        map.put("size",size);
        map.put("total",total);
        map.put("hasNext",hasNext);
        map.put("hasPrevious",hasPrevious);

        return map;
    }
~~~

---

## 课程及详情展示

### 前端页面

①在api文件夹下的course.js文件添加被调用的方法

~~~javascript
getHtmlCourse(pageNum,pageSize,VOFrontCourse){
    return request({
        url: `/serviceedu/frontCourse/courseList/${pageNum}/${pageSize}`,
        method:'post',
        data: VOFrontCourse
    })
},
getOneSubject(){
    return request({
        url: '/serviceedu/frontCourse/querySubject',
        method:'get'
    })
},
getTwoSubject(subjectId){
    return request({
        url: `/serviceedu/frontCourse/querySubject`,
        method:'get',
        params: subjectId
    })  
}
getCourseDetailsById(courseId){
    return request({
        url: `/serviceedu/frontCourse/details/${courseId}`,
        method:'get'
    })
}
~~~

②在对应的页面进行调用

### 后端接口

- 查询课程类别信息和课程信息

①创建两个vo类封装查询数据和封装课程详情信息

~~~java
@Data
public class VOFrontCourse {
    private String title;
    private String courseId;
    private String subjectParentId;
    private String subjectId;
    private String viewCountSort;
    private String gmtCreateSort;
    private String priceSort;
}
@Data
public class VOFrontCourseDetails {
    private String id;
    private String title;
    private BigDecimal price;
    private String teacherId;
    private String buyCount;
    private String lessonNum;
    private String viewCount;
    private String name;
    private String career;
    private String avatar;
    private String description;
}
~~~

②控制层接收参数

~~~java
@RestController
@RequestMapping("/serviceedu/frontCourse")
@CrossOrigin(origins = "*",maxAge = 3600)
public class FrontCourseController {
    @Autowired
    private IEduCourseService courseService;
    @Autowired
    private IEduSubjectService subjectService;
    //条件查询课程
    @PostMapping("/courseList/{pageNum}/{pageSize}")
    public JsonResult<Map<String,Object>> showCourseListByCondition(@PathVariable("pageNum") Integer pageNum,
                                                                    @PathVariable("pageSize") Integer pageSize,
                                                                    @RequestBody(required = false) VOFrontCourse course){
        HashMap<String,Object> courseList = courseService.showCourseListByCondition(pageNum,pageSize,course);

        return new JsonResult<>(true,"查询成功",courseList);
    }

    //subjectId通过判断一二级分类
    @GetMapping("/querySubject")
    public JsonResult<List<EduSubject>> queryOneSubjectList(@RequestParam(required = false) String subjectParentId){
        QueryWrapper<EduSubject> wrapper = new QueryWrapper<>();
        if (!StringUtils.isEmpty(subjectParentId)){ //不为空表示查询二级分类
            wrapper.eq("parent_id",subjectParentId);
        }else { //为空表示查询一级分类
            wrapper.eq("parent_id",0);
        }
        List<EduSubject> subjectList = subjectService.list(wrapper);
        return new JsonResult<>(true,"查询成功",subjectList);
    }
}
~~~

③业务层执行业务

~~~java
@Override
public HashMap<String, Object> showCourseListByCondition(Integer pageNum, Integer pageSize, VOFrontCourse course) {
    //创建wrapper对象
    QueryWrapper<EduCourse> wrapper = new QueryWrapper<>();

    //取出封装对象的各个数据
    String title = course.getTitle();
    String subjectParentId = course.getSubjectParentId();//一级id
    String subjectId = course.getSubjectId(); //二级id
    String viewCountSort = course.getViewCountSort();//关注度排序
    String createSort = course.getGmtCreateSort(); //时间排序
    String priceSort = course.getPriceSort(); //价格排序

    //判断数据是否为空决定是否需要添加至查询条件中
    if (!StringUtils.isEmpty(title)){
        wrapper.like("title",title);
    }
    if (!StringUtils.isEmpty(subjectParentId)){
        wrapper.eq("subject_parent_id",subjectParentId);
    }
    if (!StringUtils.isEmpty(subjectId)){
        wrapper.eq("subject_id",subjectId);
    }
    if (!StringUtils.isEmpty(viewCountSort)){
        wrapper.orderByDesc("view_count");
    }
    if (!StringUtils.isEmpty(createSort)){
        wrapper.orderByDesc("gmt_create");
    }
    if (!StringUtils.isEmpty(priceSort)){
        wrapper.orderByDesc("price");
    }

    //创建page对象
    Page<EduCourse> page = new Page<>(pageNum, pageSize);

    //执行查询，数据封装在page对象中
    this.baseMapper.selectPage(page,wrapper);

    //为了分页效果，将数据从page中取出
    HashMap<String, Object> map = new HashMap<>();
    List<EduCourse> lists = page.getRecords();
    long current = page.getCurrent();
    long pages = page.getPages();
    long size = page.getSize();
    long total = page.getTotal();
    boolean hasNext = page.hasNext();
    boolean hasPrevious = page.hasPrevious();

    //保存到map中并返回
    map.put("lists",lists);
    map.put("current",current);
    map.put("pages",pages);
    map.put("size",size);
    map.put("total",total);
    map.put("hasNext",hasNext);
    map.put("hasPrevious",hasPrevious);
    return map;
}
~~~

- 通过课程id查询课程详情

①持久层

编写sql语句

~~~mysql
SELECT * FROM edu_course WHERE id = 1189389726308478977;

SELECT c.`id`,c.title,c.`cover`,c.`price`,c.`teacher_id`,c.`buy_count`,
       c.`lesson_num`,c.`view_count`,t.`name`,t.`career`,
       t.`avatar`,d.`description`,v.`is_free`
        FROM edu_course c
        LEFT JOIN edu_teacher t
        ON c.`teacher_id` = t.`id`
        LEFT JOIN edu_course_description d
        ON c.`id` = d.`id`
        LEFT JOIN edu_video v
        ON c.`id` = v.`id`
        WHERE c.id = #{id};
~~~

定义映射文件内容

~~~xml
 <!--  自定义映射规则  -->
    <resultMap id="queryVOFrontCourseDetails" type="top.year21.onlineedu.serviceedu.vo.VOFrontCourseDetails">
        <id column="id" property="id"/>
        <result column="title" property="title"/>
        <result column="cover" property="cover"/>
        <result column="price" property="price"/>
        <result column="teacher_id" property="teacherId"/>
        <result column="buy_count" property="buyCount"/>
        <result column="lesson_num" property="lessonNum"/>
        <result column="view_count" property="viewCount"/>
        <result column="name" property="name"/>
        <result column="career" property="career"/>
        <result column="avatar" property="avatar"/>
        <result column="description" property="description"/>
    </resultMap>

    <!--    VOFrontCourseDetails queryCourseDetails(@Param("courseId") String courseId);-->
    <select id="queryCourseDetails" resultMap="queryVOFrontCourseDetails">
        SELECT  c.`id`,c.title,c.`cover`,c.`price`,c.`teacher_id`,c.`buy_count`,
                c.`lesson_num`,c.`view_count`,t.`name`,t.`career`,
                t.`avatar`,d.`description`
        FROM edu_course c
        LEFT JOIN edu_teacher t
        ON c.`teacher_id` = t.`id`
        LEFT JOIN edu_course_description d
        ON c.`id` = d.`id`
        WHERE c.id = #{courseId};
    </select>
~~~

②持久层、业务层、控制层分别调用

~~~java
//根据课程id查询课程详情信息 持久层方法
VOFrontCourseDetails queryCourseDetails(@Param("courseId") String courseId);

//根据课程id查询课程详情信息 业务层方法
@Override
public VOFrontCourseDetails queryCourseDetailsById(String courseId) {
    return this.baseMapper.queryCourseDetails(courseId);
}
 
//根据课程id查询课程详情信息 控制层方法
@GetMapping("/details/{courseId}")
public JsonResult<Map<String,Object>> queryCourseDetailsById(@PathVariable("courseId") String courseId){
    //1.先根据课程id查询课程信息
    VOFrontCourseDetails courseDetails = courseService.queryCourseDetailsById(courseId);

    //2.根据课程id查询课程的所有章节和小节
    List<CourseChapter> courseChapterList = chapterService.getChapterListByCid(courseId);

    //将数据封装进map中返回
    HashMap<String, Object> details = new HashMap<>();
    details.put("courseDetails",courseDetails);
    details.put("courseChapterList",courseChapterList);

    return new JsonResult<>(true,"查询成功",details);
}
~~~

---

## 课程视频播放

- 利用阿里云的视频点播功能实现

### 前端页面

- 使用动态路由的方式进行跳转视频播放

~~~html
<!-- target="_blank"表示跳转到一个新页面打开 -->
<a :href="'/video/' + video.videoSourceId" title target="_blank">
<span class="fr">
<i class="free-icon vam mr10">免费试听</i>
</span>
~~~

①在api下的文件夹创建vod.js，编写被调用的方法

~~~javascript
import request from '@/utils/request'

export default{
    getVideoAuth(videoId){
        return request({
            url: `/servicevod/videovoucher/${videoId}`,
            method:'get'
        })
    },
}
~~~

②在对应的页面调用

~~~javascript
<script>
import vod from '@/api/vod'

export default {
name:'videoId',
data() {
    return {
        videoId: '',
        videoAuth:'',
    }
},
created() {
    this.videoId = this.$route.params.id
    this.getVideoAuth()
},
methods: {
    getVideoAuth(){
        vod.getVideoAuth(this.videoId)
         .then(response => {
            //填充返回的视频凭证
            this.videoAuth = response.data.data
            //创建播放器
            this.createVideoPlay()
         })    
    },
    //创建播放器
    createVideoPlay(){
        new Aliplayer({
            id: 'J_prismPlayer',
            vid: this.videoId, // 视频id
            playauth: this.videoAuth, // 播放凭证
            encryptType: '1', // 如果播放加密视频，则需设置encryptType=1，非加密视频无需设置此项
            width: '100%',
            height: '500px',
            autoplay: false,
            }, function(player) {
            console.log('播放器创建成功')
    })
    }
},
}
</script>
~~~

### 后端接口

- 根据视频id获取视频访问凭证

~~~java
@GetMapping("/videovoucher/{videoSourceId}")
public JsonResult<String> getVideoVoucherById(@PathVariable("videoSourceId") String videoSourceId){
    try {
        //1.创建初始化对象
        DefaultAcsClient defaultAcsClient = VodInitClass.initVodClient(UploadConfigClass.ACCESS_KEY_ID, UploadConfigClass.ACCESS_KEY_SECRET);
        //2.创建response对象和request对象
        GetVideoPlayAuthResponse response = new GetVideoPlayAuthResponse();
        GetVideoPlayAuthRequest request = new GetVideoPlayAuthRequest();

        //3.向request对象中设置视频id
        request.setVideoId(videoSourceId);

        //4.调用初始化对象中的方法，将request对象传入获取视频信息并赋值给response对象
        response = defaultAcsClient.getAcsResponse(request);

        //返回播放凭证
        String videoAuth = response.getPlayAuth();
        return new JsonResult<>(true,"查询成功",videoAuth);
    } catch (Exception e) {
        return new JsonResult<>(false,"获取视频凭证失败，错误信息是：",e.getMessage());
    }
}
~~~

---

## 课程评论

### 前台页面

①在api文件下创建comment.js文件创建被调用的方法

~~~javascript
import request from '@/utils/request'

export default{
    createComment(content,courseId,rate){
        return request({
            url:`/servicecomment/create/${courseId}/${content}/${rate}`,
            method:'get'
        })
    },
    showCommnet(pageNum,pageSize,courseId,teacherId){
        return request({
            url:`/servicecommnet/show/${pageNum}/${pageSize}/${courseId}/${teacherId}`,
            method:'get'
        })
    },
    reportComment(commentId){
        return request({
            url:`/servicecommnet/report/${commentId}`,
            method:'post'
        })
    }
}
~~~

②在对应的页面进行调用

### 后端接口

①创建edu_comment数据表

②在service模块下创建子模块service_comment

③修改pom文件

④编写yml配置文件

⑤主启动类

⑥业务类

- 通过代码生成器快速生成实体类、控制层等
- 需要远程调用edu和userCenter模块(直接cv订单order模块的接口即可)

~~~java
//创建课程评论 控制层
@PostMapping("/create/{courseId}")
public JsonResult<Void> createComment(@RequestBody String content,
                                      @PathVariable("courseId") String courseId,
                                      HttpServletRequest request){
    commentService.createCommnet(content,courseId,request);
    return new JsonResult<>(true);
}

//查看课程评论 控制层
@GetMapping("/show/{pageNum}/{pageSize}/{courseId}/{teacherId}")
public JsonResult<Map<String, Object>> showComment(@PathVariable("pageNum") Integer pageNum,
                                                   @PathVariable("pageSize") Integer pageSize,
                                                   @PathVariable("courseId") String courseId,
                                                   @PathVariable("teacherId") String teacherId){
    Map<String, Object> comments = commentService.showComment(pageNum,pageSize,courseId,teacherId);
    return new JsonResult<>(true,"查询成功",comments);
}

//业务层
@Service
public class CommentServiceImpl extends ServiceImpl<CommentMapper, Comment> implements ICommentService {
    @Resource
    private OpenFeignTransferEdu transferEdu;
    @Resource
    private OpenFeignTransferUserCenter transferUserCenter;

    @Override
    public void createCommnet(String content,String courseId,Integer rate,HttpServletRequest request) {
        //获取课程详细信息
        VOCourseDetails courseDetails = transferEdu.orderCourseDetailsById(courseId);
        //获取用户信息
        String userId = JwtUtils.getMemberIdByJwtToken(request);
        VOUserCenter userInfo = transferUserCenter.getUserInfoById(userId);

        //拼凑插入数据库的对象
        Comment comment = new Comment();
        comment.setCourseId(courseId);
        comment.setTeacherId(courseDetails.getTeacherId());
        comment.setMemberId(userId);
        comment.setNickname(userInfo.getNickname());
        comment.setAvatar(userInfo.getAvatar());
        comment.setContent(content);
        comment.setRate(rate);

        //执行插入操作
        int result = this.baseMapper.insert(comment);

        if (result == 0){
            throw new InsertException(30001,"创建评论数据失败！");
        }
    }
    
    //查看课程评论
    @Override
    public Map<String, Object> showComment(Integer pageNum, Integer pageSize, String courseId, String teacherId) {
        Page<Comment> page = new Page<>(pageNum, pageSize);
        QueryWrapper<Comment> wrapper = new QueryWrapper<>();
        wrapper.eq("course_id",courseId).eq("teacher_id",teacherId);
        this.baseMapper.selectPage(page,wrapper);

        //为了分页效果，将数据从page中取出
        HashMap<String, Object> map = new HashMap<>();
        List<Comment> lists = page.getRecords();
        long current = page.getCurrent();
        long pages = page.getPages();
        long size = page.getSize();
        long total = page.getTotal();
        boolean hasNext = page.hasNext();
        boolean hasPrevious = page.hasPrevious();

        //保存到map中并返回
        map.put("lists",lists);
        map.put("current",current);
        map.put("pages",pages);
        map.put("size",size);
        map.put("total",total);
        map.put("hasNext",hasNext);
        map.put("hasPrevious",hasPrevious);

        return map;
    }
}
~~~

---

## 课程支付

- 课程支付通过微信模拟支付实现

![](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220901211446434.png)

### 前端页面

①在api文件夹下创建order.js文件定义被调用的方法

~~~javascript
import request from '@/utils/request'

export default{ 
    //生成订单
    createOrder(courseId){
        return request({
            url:`/serviceorder/order/createOrder/${courseId}`,
            method:'post'
        })
    },
    //查询订单信息
    queryOrder(orderNo){
        return request({
            url:`/serviceorder/order/query/${orderNo}`,
            method:'get'
        })
    },
    //创建支付二维码
    createOrderCode(orderNo){
        return request({
            url:`/serviceorder/paylog/createCode/${orderNo}`,
            method:'get'
        })
    },
    //查询订单支付状态
    queryOrderPayStatus(orderNo){
        return request({
            url:`/serviceorder/paylog/queryStatus/${orderNo}`,
            method:'get'
        })
    },
}
~~~

②在对应页面进行调用

- <font color='red'>**在当前页面使用js定时器每隔3秒查询订单状态，判断是否支持，且需要通过vue的生命周期函数**</font>

  <font color='red'>**在vue对象销毁之前把定时器清除，不然就会出现订单未支付，页面关闭定时器仍然调用查询接口**</font>

③需要修改前端课程显示页面一些内容，动态判断立即观看或立即购买

~~~javascript
data() {
    return {
      isFree: true, //表示免费
      isBuy: false, //表示收费
    }
  },
  created() {
    //获取url上的课程id
    this.courseId = this.$route.params.id
    this.getCourseDetailsById()
  },
  methods: {
    //获取课程信息
    getCourseDetailsById(){
      course.getCourseDetailsById(this.courseId)
        .then(response => {
            //将查询的数据填充至列表
            this.courseDetails = response.data.data.courseDetails
            this.courseChapterList = response.data.data.courseChapterList

            //调用判断课程显示收费还是免费
            this.judgeCourseIfNeedBuy()
        })
    },
    //判断课程是否需要收费
    judgeCourseIfNeedBuy(){
      //先判断是否能从cookie中取到信息
      let user = cookie.get('userInfo')
      if(user){ //非空或者非0数字代表已经登录
          //查询是否已经购买此课程
          this.judgeCourseIsBuyOrNot()
      }else{ //代表未登录
        //仅需判断课程价格是否为0即可
        if(this.courseDetails.price !== 0){
            //设置为付费
            this.isFree = false 
            this.isBuy = true
          }
      }   
    },
    //查询用户是否已购买当前课程
    judgeCourseIsBuyOrNot(){
      course.judgeCourseIsBuyOrNot(this.courseId)
        .then(response => {
          if(!response.data.data){ //表示未购买
              this.isFree = false
              this.isBuy = true
          }
        })
    }
  },
~~~



### 后端接口

#### 生成订单

①创建数据表

②在service模块下创建子模块service_order

③改pom文件

④编写yml配置文件

~~~yml
server:
  port: 8007

spring:
  application:
    name: server-order
  profiles:
    active: dev
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/online_edu?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    username: root
    password: root
  #设置时间格式
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  #将edu_service注册到nacos服务注册中心
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

#暴露所有的监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
  #设置不检查redis的健康状态，因为引入了actuator依赖会自带检查一些东西的健康状态
  #因为还没用到redis，若不设置会报一个无法连接redis的错误
  health:
    redis:
      enabled: false

#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ReadTimeout: 5000
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000

#一旦使用xml映射文件就必须设置这个路径,建议每次在创建时就加上
mybatis-plus:
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
~~~

⑤主启动类

⑥业务类

- 此接口需要远程调用edu和userCenter模块中的接口，考虑到实体类依赖问题

  <font color='red'>**远程调用需要在两者调用关系之间存在的公共模块中定义封装返回结果的实体类**</font>

~~~java
//order接口和edu接口两者需要使用的返回结果实体
@Data
public class VOCourseDetails {
    private String id;
    private String title;
    private String cover;
    private BigDecimal price;
    private String teacherId;
    private String buyCount;
    private String lessonNum;
    private String viewCount;
    private String name;
    private String career;
    private String avatar;
    private String description;

}

//order接口和userCenter接口两者需要使用的返回结果实体
@Data
public class VOUserCenter {
    private String id;
    private String openid;
    private String mobile;
    private String salt;
    private String password;
    private String nickname;
    private Integer sex;
    private Integer age;
    private String avatar;
    private String sign;
    private Boolean isDisabled;
    private Boolean isDeleted;
    private Date gmtCreate;
    private Date gmtModified;
}
~~~

- 在edu和userCenter模块中重新编写两个接口用于消费者调用

~~~java
//根据课程id查询课程详情信息 edu模块
@GetMapping("/orderDetails/{courseId}")
public VOCourseDetails orderCourseDetailsById(@PathVariable("courseId") String courseId) {
    //根据课程id查询课程信息
    VOFrontCourseDetails queryDetails = courseService.queryCourseDetailsById(courseId);

    //将查询得出的信息封装到订单所需的课程vo类中并返回
    VOCourseDetails courseDetails = new VOCourseDetails();
    BeanUtils.copyProperties(queryDetails,courseDetails);

    return courseDetails;
}

//通过userId查询用户信息 userCenter模块
@GetMapping("/userInfo/{userId}")
public VOUserCenter getUserInfoById(@PathVariable("userId") String userId){
    //根据id查询用户信息
    UserCenter queryUser = userCenterService.getById(userId);

    //将查询出的用户信息封装到vo类中并返回
    VOUserCenter voUser = new VOUserCenter();
    BeanUtils.copyProperties(queryUser,voUser);

    return voUser;
}
~~~

- order模块控制层接收参数、业务层定义两个接口远程调用其他模块以及实现具体业务逻辑

~~~java
@RestController
@RequestMapping("/serviceorder/order")
@CrossOrigin(origins = "*",maxAge = 3600)
public class OrderController {
    @Autowired
    private IOrderService orderService;
    //通过课程id创建订单
    @PostMapping("/createOrder/{courseId}")
    public JsonResult<String> createOrderByCourseId(@PathVariable("courseId")String courseId,
                                                    HttpServletRequest request){
        String userId = JwtUtils.getMemberIdByJwtToken(request);
        String orderId = orderService.createOrder(courseId,userId);
        return new JsonResult<>(true,"订单创建成功",orderId);
    }

    //通过id查询订单详情
    @GetMapping("/query/{orderNo}")
    public JsonResult<Order> queryOrderDetailsById(@PathVariable("orderNo") String orderNo){
        QueryWrapper<Order> wrapper = new QueryWrapper<>();
        wrapper.eq("order_no",orderNo);
        Order order = orderService.getOne(wrapper);
        return new JsonResult<>(true,"查询成功",order);
    }
}

@Component
@FeignClient("service-edu")
public interface OpenFeignTransferEdu {

    @GetMapping("/serviceedu/frontCourse/orderDetails/{courseId}")
    public VOCourseDetails orderCourseDetailsById(@PathVariable("courseId") String courseId);
}

@Component
@FeignClient("user-center")
public interface OpenFeignTransferUserCenter {

    //通过userId查询用户信息
    @GetMapping("/serviceusercenter/userInfo/{userId}")
    public VOUserCenter getUserInfoById(@PathVariable("userId") String userId);
}

@Service
public class OrderServiceImpl extends ServiceImpl<OrderMapper, Order> implements IOrderService {

    @Resource
    private OpenFeignTransferEdu transferEdu;
    @Resource
    private OpenFeignTransferUserCenter transferUserCenter;

    //根据课程id创建订单
    @Override
    public String createOrder(String courseId, String userId) {

        //根据课程id远程调用查询课程信息
        VOCourseDetails courseDetails = transferEdu.orderCourseDetailsById(courseId);

        //根据课程id远程调用查询用户信息
        VOUserCenter userInfo = transferUserCenter.getUserInfoById(userId);

        //分别取出创建订单所需的信息
        String orderNo = UUID.randomUUID().toString();
        String courseTitle = courseDetails.getTitle();
        String courseCover = courseDetails.getCover();
        String teacherName = courseDetails.getName();
        String nickname = userInfo.getNickname();
        String mobile = userInfo.getMobile();
        BigDecimal totalFee = courseDetails.getPrice();

        //创建订单对象并填充数据
        Order order = new Order();
        order.setOrderNo(orderNo);
        order.setCourseId(courseId);
        order.setCourseTitle(courseTitle);
        order.setCourseCover(courseCover);
        order.setTeacherName(teacherName);
        order.setMemberId(userId);
        order.setNickname(nickname);
        order.setMobile(mobile);
        order.setTotalFee(totalFee);

        //插入数据库
        int result = this.baseMapper.insert(order);

        if (result == 0){
            throw new InsertException(30001,"订单创建失败");
        }

        return order.getOrderNo();
    }
}
~~~

#### 微信二维码

- order模块的pom文件中引入下列依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>com.github.wxpay</groupId>
        <artifactId>wxpay-sdk</artifactId>
        <version>0.0.3</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
    </dependency>
</dependencies>
~~~

- 控制层、业务层

~~~java
//控制层
@GetMapping("/createCode/{orderNo}")
public JsonResult<Map<String,Object>> createCode(@PathVariable("orderNo") String orderNo){
    //返回的map集合中包含了二维码地址，还要其他需要的信息
    Map<String,Object> info = payLogService.createCode(orderNo);
    return new JsonResult<>(true,"创建成功",info);
}

//业务层
//创建微信支付二维码
@Override
public Map<String, Object> createCode(String orderNo) {
    try {
        //1.根据订单查询订单信息
        QueryWrapper<Order> wrapper = new QueryWrapper<>();
        wrapper.eq("order_no",orderNo);
        Order order = orderService.getOne(wrapper);
        //2.使用map设置生成二维码需要的参数
        Map<String,String> map = new HashMap<>();
        map.put("appid", "wx74862e0dfcf69954");
        map.put("mch_id", "1558950191");
        map.put("nonce_str", WXPayUtil.generateNonceStr());
        map.put("body", order.getCourseTitle()); //课程标题
        map.put("out_trade_no", orderNo); //订单号
        map.put("total_fee", order.getTotalFee().multiply(new BigDecimal("100")).longValue()+"");
        map.put("spbill_create_ip", "127.0.0.1");
        map.put("notify_url", "http://guli.shop/api/order/weixinPay/weixinNotify");
        map.put("trade_type", "NATIVE");
        //3.使用httpClient工具类发送请求到微信支付提供的地址，传递的参数必须是xml格式，
        HttpClient client =  new HttpClient("https://api.mch.weixin.qq.com/pay/unifiedorder");
        //设置xml格式的参数
        client.setXmlParam(WXPayUtil.generateSignedXml(map,"T6m9iK73b0kn9g5v426MKfHQH7X8rKwb"));
        client.setHttps(true);
        //发送请求
        client.post();
        //4.得到发送请求返回结果
        String contentXml = client.getContent(); //返回的内容也是xml格式
        //将xml转为map返回
        Map<String,String> resultMap = WXPayUtil.xmlToMap(contentXml);
        //由于支付还需要其他在resultMap中不存在的信息，因此额外使用一个map封装最终返回结果
        Map<String,Object> endMap = new HashMap<>();
        endMap.put("out_trade_no", orderNo);
        endMap.put("course_id", order.getCourseId());
        endMap.put("total_fee", order.getTotalFee());
        endMap.put("result_code", resultMap.get("result_code"));
        endMap.put("code_url", resultMap.get("code_url"));

        //微信支付二维码2小时过期，可采取2小时未支付取消订单
        //            redisTemplate.opsForValue().set(orderNo, map, 120, TimeUnit.MINUTES);
        return endMap;
    }catch (Exception e){
        throw new CommonException(30001,"生成二维码失败，错误信息是：" + e.getMessage());
    }

}
~~~

#### 支付状态

~~~java
//通过订单号查询订单状态 控制层
@GetMapping("/queryStatus/{orderNo}")
public JsonResult<Void> queryOrderStatus(@PathVariable("orderNo") String orderNo){
    //查询指定的订单
    Map<String,String> map = payLogService.queryOrderStatus(orderNo);
    log.info("*****查询订单状态map：" + map);
    if (map == null) {//map为空表示支付失败
        throw new CommonException(30001, "支付失败");
    }

    //map不为空表示支付成功
    if("SUCCESS".equals(map.get("trade_state"))){
        //向支付表中添加数据并更新此订单状态
        payLogService.insertDateAndUpdateStatus(map);
        return new JsonResult<>(true);
    }

    return new JsonResult<>(false);
}

//业务层实现类
//查询订单状态
@Override
public Map<String, String> queryOrderStatus(String orderNo) {
    try {
        //1、封装参数
        Map m = new HashMap<>();
        m.put("appid", "wx74862e0dfcf69954");
        m.put("mch_id", "1558950191");
        m.put("out_trade_no", orderNo);
        m.put("nonce_str", WXPayUtil.generateNonceStr());

        //2、设置请求
        HttpClient client = new HttpClient("https://api.mch.weixin.qq.com/pay/orderquery");
        client.setXmlParam(WXPayUtil.generateSignedXml(m, "T6m9iK73b0kn9g5v426MKfHQH7X8rKwb"));
        client.setHttps(true);
        client.post();
        //3、返回第三方的数据
        String xml = client.getContent();
        Map<String, String> resultMap = WXPayUtil.xmlToMap(xml);
        //6、转成Map
        //7、返回
        return resultMap;
    } catch (Exception e) {
        e.printStackTrace();
    }
    return null;
}

//向payLog表中添加数据并修改order订单状态
@Override
public void insertDateAndUpdateStatus(Map<String, String> map) {
    //获取订单id
    String orderNo = map.get("out_trade_no");
    //根据订单id查询订单信息
    QueryWrapper<Order> wrapper = new QueryWrapper<>();
    wrapper.eq("order_no",orderNo);
    Order order = orderService.getOne(wrapper);

    if(order.getStatus() == 1) return;
    order.setStatus(1);
    orderService.updateById(order);

    //记录支付日志
    PayLog payLog=new PayLog();
    payLog.setOrderNo(order.getOrderNo());//支付订单号
    payLog.setPayTime(new Date());
    payLog.setPayType(1);//支付类型
    payLog.setTotalFee(order.getTotalFee());//总金额(分)
    payLog.setTradeState(map.get("trade_state"));//支付状态
    payLog.setTransactionId(map.get("transaction_id"));
    payLog.setAttr(JSONObject.toJSONString(map)); //map中的其他信息
    baseMapper.insert(payLog);//插入到支付日志表
}
~~~

#### 动态显示观看/购买

~~~java
//通过课程id和用户id查询是否购买此课程
@GetMapping("/judgeIsBuyOrNot/{courseId}")
public JsonResult<Boolean> judgeIsBuyCourseOrNot(@PathVariable("courseId")String courseId,
                                                 HttpServletRequest request){
    //通过token获取用户id
    String userId = JwtUtils.getMemberIdByJwtToken(request);
    Boolean result = orderService.judgeIsBuyCourseOrNot(courseId,userId);
    return new JsonResult<>(true,"查询成功",result);
}
//通过课程id和用户id查询是否购买此课程
@Override
public Boolean judgeIsBuyCourseOrNot(String courseId, String userId) {
    QueryWrapper<Order> wrapper = new QueryWrapper<>();
    wrapper.eq("course_id",courseId)
        .eq("member_id",userId)
        .eq("status",1);
    Long result = this.baseMapper.selectCount(wrapper);
    //返回查询结果
    return result != 0;

}
~~~

---

# 整合Spring Cloud

- 微服务是一种架构风格，把一个项目拆分成独立的多个服务，多个服务是单独运行，每个服务占用独立的进程

## nacos

- 其他需要注册进到nacos注册中心都是一样的步骤 

①将依赖导入到服务消费者或服务提供者的父类pom中，以便复用

~~~xml
<!--spring cloud alibaba nacos-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<!-- SpringBoot整合Web组件 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
~~~

②在yml配置文件加入这些配置

~~~yml
spring:
  application:
    name: service-edu
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

#暴露所有的监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
  #设置不检查redis的健康状态，因为引入了actuator依赖会自带检查一些东西的健康状态
  #因为还没用到redis，若不设置会报一个无法连接redis的错误
  health:
    redis:
      enabled: false
~~~

③在启动类上添加@EnableDiscoveryClient注解

## openFeign

①将依赖导入到服务消费者8001的pom中，以便复用

~~~xml
<!--服务调用-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
~~~

②在yml配置文件加入这些配置

~~~yml
#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ReadTimeout: 5000
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
~~~

③在服务消费者的启动类上添加@EnableFeignClients注解表示开启feign

④在业务层中创建一个接口并添加@FeignClient和@Component注解

~~~java
@Component
@FeignClient("aliyun-video-upload") //表示到nacos服务注册中心上查找这个名称的服务提供者
public interface OpenFeignDelVideo {
    
    //通过这个rest地址去调用服务提供者中相应的方法执行业务
    @PostMapping("/servicevod/videoDel/{videoSourceId}")
    public JsonResult<Void> delVideoInAliyun(@PathVariable("videoSourceId") String videoSourceId);
}
~~~

## hystrix

①将依赖导入到服务消费者8001的pom中，以便复用

~~~xml
<!--hystrix依赖，主要是用  @HystrixCommand -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
~~~

②在yml配置文件加入这些配置

~~~yml
#开启feign对Hystrix的支持,默认是false,
#开启之后可以直接在@FeignClent注解中的fallback属性指定回调的类
feign:
  hystrix:
    enabled: true
#Hystrix的单独配置,即设置默认超时时间：
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 6000
~~~

③在服务消费者的启动类上添加@EnableHystrix注解注解表示开启熔断机制

④在业务层中创建一个类并实现feign定义的远程调用接口编写fallback方法

~~~java
@Slf4j
@Component
public class OpenFeignDelVideoFallback implements OpenFeignDelVideo {
    @Override
    public JsonResult<Void> delVideoInAliyun(String videoSourceId){
        log.info("删除阿里云的视频失败，删除失败的视频id是：" + videoSourceId);
        return new JsonResult<>(false);
    }
}
~~~

⑤在feign定义的远程调用接口上指明降级处理类

~~~java
@Component
//表示到nacos服务注册中心上查找这个名称的服务提供者，负责此接口服务降级的类是OpenFeignDelVideoFallback
@FeignClient(value = "aliyun-video-upload",fallback = OpenFeignDelVideoFallback.class)
public interface OpenFeignDelVideo {}
~~~

## 执行流程

![服务调用过程](https://myblogimgurl.oss-cn-shenzhen.aliyuncs.com/image-20220826232048125.png)

（1）接口化请求调用

当调用标注@FeignClient注解接口时，框架将请求转换成Feign请求实例`feign.Request`，交由Feign框架处理

（2）Feign

转化请求Feign是一个http请求调用的轻量级框架，以Java接口注解的方式调用Http请求，封装了Http调用流程。

（3）Hystrix

熔断处理机制 Feign的调用关系，会被Hystrix代理拦截，对每一个Feign调用请求，Hystrix都会将其包装

成`HystrixCommand`,参与Hystrix的流控和熔断规则。如果请求判断需要熔断，则Hystrix直接熔断，抛出

异常或者使用`FallbackFactory`返回熔断`Fallback`结果；如果通过，则将调用请求传递给`Ribbon`组件。

（4）Ribbon

服务地址选择 当请求传递到`Ribbon`之后,`Ribbon`会根据自身维护的服务列表，根据服务的服务质量，

如平均响应时间，Load等，结合特定的规则，从列表中挑选合适的服务实例，选择好机器之后，

然后将机器实例的信息请求传递给`Http Client`客户端，`HttpClient`客户端来执行真正的Http接口调用；

（5）HttpClient (Http客户端)

真正执行Http调用根据上层`Ribbon`传递过来的请求，已经指定了服务地址，HttpClient开始执行真正的Http请求

## Gateway

①在online_edu模块下创建子模块online_edu_gateway

②引入所需依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>top.year21.onlineedu</groupId>
        <artifactId>common_utils</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>

    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>

    <!--gson-->
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
    </dependency>

    <!--服务调用-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
</dependencies>
~~~

③编写yml配置文件

~~~yml
server:
  port: 8010

spring:
  application:
    name: online-edu-gateway
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #网关gateway也是个微服务，必须在nacos中注册
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由，默认为false
      routes:
        - id: service-edu # 路由的ID，没有固定规则但要求必须唯一，建议配置服务名
          uri: lb://service-edu  #匹配后提供服务的动态路由地址
          predicates:
            - Path=/serviceedu/** #断言，路径相匹配的进行路由
~~~

④编写主启动类

~~~java
@SpringBootApplication
@EnableDiscoveryClient
public class Gateway8010 {
    public static void main(String[] args){
        SpringApplication.run(Gateway8010.class,args);
    }
}
~~~

- 这里补充一个全局跨域配置类

  <font color='red'>**注意：当使用此全局跨域配置类后，不能够再在控制器上添加@CrossOrigin注解**</font>

~~~java
@Configuration
public class CrossOriginConfig {
    @Bean
    public CorsWebFilter corsFilter() {
        CorsConfiguration config = new CorsConfiguration();
        config.addAllowedMethod("*");
        config.addAllowedOrigin("*");
        config.addAllowedHeader("*");

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource(new PathPatternParser());
        source.registerCorsConfiguration("/**", config);

        return new CorsWebFilter(source);
    }
}
~~~

---

# bug总结

1.前端返回的时间数据带T，按照格林威治时间显示，需要修改为按照东八区时间显示

①在实体类中的时间属性上添加注解，这种方法缺点就是需要手动，数据量大就很不方便

~~~java
    @JsonFormat(timezone = "GMT+8",pattern="yyyy-MM-dd HH:mm:ss")
    @ApiModelProperty("创建时间")
    private LocalDateTime gmtCreate;

    //设置返回前端的数据按照东八区时间显示
    @JsonFormat(timezone = "GMT+8",pattern="yyyy-MM-dd HH:mm:ss")
    @ApiModelProperty("更新时间")
~~~

②在yml配置文件中设置jackson时间格式

~~~yml
 #设置时间格式
 spring:
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
~~~

2.有可能因为npm或者node的版本过高导致大量error出现，无法根据package.json安装依赖

参考网友的方法即可解决：

方案一：降低npm或者node的版本

方案二：(npm或者node的高版本)

①安装cnpm ：`npm install cnpm -g`

②安装 node-sass： `cnpm install node-sass`

③继续安装 : `cnpm i node-sass -D`

④根据package.json安装依赖：`cnpm install`

⑤启动项目：`npm run dev`

⑥当控制台出现  Your application is running here: http://localhost:9528 表示启动成功

3.前端页面访问后端容易产生跨域问题，这里记录一下解决方法

①在后端接口上田间@CrossOrigin注解即可

②使用GateWay解决

3.在讲师管理界面点击修改后，当数据成功回显到页面中，再点击添加讲师，发现表单数据没有清空

- 尝试在created()方法判断当id值为空时，清空表单数据，但没有发挥作用，为什么？

<font color='red'>**因为在多次路由跳转到同一个页面，在页面中created()方法只会执行第一次，之后的跳转不再执行**</font>

- 解决方法，使用vue的watch侦听属性，如下：

~~~javascript
watch:{ //vue的监听功能
    $route(to,from){ //路由变化方式，当路由发生变化，init方法就会执行
      this.init()
    }
~~~

4.在课程信息回显页面，二级课程显示出了id值，而非这个课程的名字。

- 造成的原因是：在页面执行完成时，负责二级课程的数组属性值中是为null的，而在数据渲染的底层

  是通过遍历二级课程中的数组内的id来匹配显示在页面上的标题，如果此时数组属性值为空，那么就会

  直接显示传回的数据id值

解决方法：在一级课程数组赋值后，将对应的id一级分类课程下的二级课程分类赋值给二级数组属性

~~~javascript
//获取所有课程名单
getAllSubjects(){
    subject.getSubjectList()
        .then(resopnse => {
        this.oneSubjects = resopnse.data
        //必须保证this.oneSubjects不为空的情况才能进行课程信息的回显
        if(this.$route.params && this.$route.params.id ){
            //获取url中的参数
            this.courseId = this.$route.params.id
            //根据id查询信息
            this.queryCourseInfoById()
        }
    })
},
~~~

5.在查询显示最终课程信息的sql语句中需要多次对edu_subject表进行查询title

- <font color='red'>**可以对同张表进行两次左外连接查询，只需要将表的别名设置不同即可**</font>

~~~mysql
LEFT JOIN edu_subject s1 ON c.`subject_id` = s1.`id` 
LEFT JOIN edu_subject s2 ON c.`subject_id` = s2.`id` 
~~~

6.使用阿里云进行视频上传时，明明端口和接口都正确的情况下依旧报跨域问题，

响应状态码为413，这个状态代表着请求实体太大 (Request entity too large)

- 经过短暂排查和搜索发现是因为使用nginx进行代理转发，而nginx默认允许上传

  的文件大小是1M，上传文件超过了这个阈值

- 解决方法如下

①打开nginx配置文件 nginx.conf, 路径一般是：/etc/nginx/nginx.conf。

②在http{}段中加入 client_max_body_size 20m; 	20m为允许最大上传的大小。

③保存后重启nginx，问题解决。

7.前端注册页面验证码点击后倒计时效果

使用js的定时器方法

~~~javascript
//这段代码表示每间隔1秒执行setInterval()的方法
timeDown() {
     let result = setInterval(() => {
         --this.second; //当前秒数-1
         this.codeTest = this.second //并将秒数值填充至获取验证码的文本钟
         if (this.second < 1) { //如果秒数小于1，即倒计时结束
             clearInterval(result);//清除定时器
             this.sending = true;//让获取验证码按钮可点击
             //this.disabled = false;
             this.second = 60;//将倒计时再次置为60
             this.codeTest = "获取验证码"
         }
     }, 1000);
 },
~~~

8.在vue框架中可以通过下列指令获取url地址上指定名称的参数值

<font color='red'>**this.$route.query.参数名**</font>

9.<font color='red'>**created函数内方法执行顺序并不是从上到下的，而是随机的，哪个请求快就先执行哪个**</font>

- 如果在created函数中有多个方法进行对数据库操作，建议使用.then()函数确保执行顺序

  或者在别的方法中使用.then()设置好请求顺序，不然页面有时候有bug存在，明明取到数据

  却不生效。

10.在layout中添加了自定义的布局vue，想要生效需要在对应的页面中引入使用

~~~javascript
layout: 'video', //引入需要使用的布局
~~~

11.在进行远程调用时，发现返回的实体类在服务消费者中不存在，返回的实体类只在服务提供者中存在，

不能在这个模块中生成此实体，以避免造成依赖。

<font color='red'>**解决方法是：在双方公用的模块中生成和返回实体一致的vo类，也可理解为将服务提供者中的实体类**</font>

<font color='red'>**copy到公共模块中，让服务消费者和服务提供者都使用这个vo类作为返回实体，因为在远程调用中无论**</font>

<font color='red'>**是服务消费者还是服务提供者都必须使用同一个对象作为返回实体才能保证数据的一致性**</font>

12.树形结构一般是通过拼凑一个对象封装查询数据得到

~~~java
//树形结构json形式
{
    id:'',
    name:'',
    children:[
        {
            id:'',
            name:'',
            children:[]
        }
    ]
}

//树形结构 java实体类
public class TreeNode{
    private String id;
    private String name;
    private Treenode[] children;
}
~~~

13.权限管理整合后登录成功，侧边栏菜单不显示内容，但可以在地址栏上输入对应的路由路径

并能正确访问页面和显示数据。

原因：addRoutes()添加路由之后，this.$router.options.routes()（初始路由列表） 未更新

解决方法：在src下文件夹下的permission.js文件中的 和下方代码示例中的第二行代码后面 

添加后两行代码

~~~javascript
//异步操作，根据登录用户名字返回动态路由列表
const accessRoutes = await store.dispatch('permission/generateRoutes', roles)
//进行动态路由+静态路由的合并
let allRoute = constantRoutes.concat(accessRoutes)
//更新配置的初始路由列表
router.options.routes = allRoute;
~~~

14.经过权限管理的bug调试，终于知道为什么要在用户登录的时候去查看用户对应的权限值

并将其携带返回到前端保存在了store对象当中？

因为在前端工程中使用了v-if 调用注册的全局方法hasPerm,再将需要判断的权限值传入

hasPerim(”权限值“)，而hasPerm又在main.js文件中被定义成了另外一个判断权限的方法 

Vue.prototype.hasPerm = hasBtnPermission ，在这个hasBtnPermission会从store对象

中取出代表button访问权限的数组，经过遍历此数组和当前传入权限值的匹配程度决定了

某个页面上的按钮是否能被此用户所看到

15.前端请求参数有斜杠或反斜杠接口访问不到，前端将此参数base64编码后传递

- 前端base64编码

~~~javascript
//请求参数有斜杠或反斜杠接口访问不到，因此需要前端将其base64编码后传递
let dbPath = window.btoa(path)
~~~

- 后端base64解码

~~~java
/** 对base64码进行解码
     * @param code 需要解码的base64字符串
     * @return 解码之后的字符串
     * */
public static String decode(String code){
    String str="";
    try {
        str=new String(Base64.decodeBase64(code),"utf-8");
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }
    return str;
}
~~~

