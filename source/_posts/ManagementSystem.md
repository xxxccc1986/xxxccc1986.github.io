---
title: SpringBoot+Vue的前后端分离项目
date: 2022-07-07 23:20:16
updated: 2022-07-07 23:20:16
tags:
  - SpringBoot
  - Vue
categories:
  - 个人项目
keywords: SpringBoot Vue 
cover: https://w.wallhaven.cc/full/k7/wallhaven-k7q797.png
copyright_author: Year21
copyright_url: http://year21.top/2022/07/07/ManagementSystem
---

# 前置介绍

项目介绍：前端采用Vue3.0 + element-plus，后端采用Springboot + MyBatis-plus的前后端分离项目

# Vue前端

## 项目结构

![](https://s1.ax1x.com/2022/07/07/j0risx.png)

public ：

​	index.html ：将vue文件进行编译后展示出来

src ：

​	assets ：存储静态资源(CSS样式、图片资源等)

​	componments：公共组件(把一些代码通过组件的方式进行打包，变成一个vue文件，以达到在别的界面进行引入的作用)

​	router(路由)：将页面请求路径与vue文件形成映射关系

​	store：储存页面定义的变量

​	views：存储视图

## 第一个页面

页面主要由 头部导航栏 + 侧边菜单栏 + 主体展示内容组成 ，每个部分都是一个单独的vue组件

~~~vue
<template>
  <!-- 头部-->
  <Header/>
  <!-- 主体-->
  <div style="display:flex">
    <!-- 侧边栏 -->
    <AsideMenu/>
    <!--   内容区域   -->
    <router-view style="flex: 1"/>
  </div>
</template>

<script>
//import需要使用的组件，并且在下面进行局部注册，才能在当前文件使用组件标签调用该组件
import Header from "../components/Header";
import AsideMenu from "../components/AsideMenu";

export default {
  name: "layout",
  components:{
    AsideMenu,
    Header,
  }
}
</script>
~~~

页面功能说明：支持模糊查询(仅支持通过用户名进行查询)、新增、查看用户图书列表、修改用户、删除用户的功能

- 查看用户图书列表的功能补充说明：

  通过对这个按钮绑定点击事件发起axios异步查询，通过多表联查将得到的数据填充值表格当中，详细代码参考后端项目springboot的UserMapper.xml

![第一个页面](https://s1.ax1x.com/2022/07/07/j0rFL6.png)

## 其他功能

- 基于**旧页面**模块完成了**书籍管理、个人信息查看、注册页面、找回密码** 等其他页面

- **权限控制**，通过在登录成功后，在页面加载完成之前异步查询当前用户的role值判断用户身份，以决定侧边菜单的显示

>  (PS:有一说一，权限控制这部分逻辑过于简单，应该使用更为成熟的后端框架进行权限控制
>
>   但由于是一个练手的小Demo项目，也就这么粗略的进行控制)

~~~vue
export default {
    name: "Aside",
    data(){
      return{
        user :  {},
        path : this.$route.path, //设置默认高亮的菜单项
      }
    },
    created() {
		//获得存储在session中的用户信息
	  this.user = JSON.parse(sessionStorage.getItem("user") || "{}")
      request.get("/user/" + this.user.id).then(res => {
		//将查询后返回的user信息覆盖掉原来的用户信息
        if(res.code === 100 )
        this.user = res.extend.user
      })
    }
}
~~~

管理员界面

![](https://s1.ax1x.com/2022/07/07/j0rAeK.png)

普通用户界面

![](https://s1.ax1x.com/2022/07/07/j0rEdO.png)



- 前端采用简单逻辑实现检测是否**登录的拦截**，通过判断vue的sessionStorage中是否以及存储登录信息来决定是否拦截

  以及通过**配置验证码**功能防止机器人自动操作的恶意行为，在登录页面除了登录还可以选择注册账户、找回账户等功能

~~~javascript
// request 拦截器
// 可以自请求发送前对请求做一些处理
// 比如统一加token，对请求参数统一加密
request.interceptors.request.use(config => {
    config.headers['Content-Type'] = 'application/json;charset=utf-8';
    
    //取出sessionStorage的信息判断是否已经登录
    let userStr = sessionStorage.getItem("user");
    if (userStr == null){//为空，代表没有登录 跳转到login界面
        router.push("/login")
    }
    return config
}, error => {
    return Promise.reject(error)
});
~~~

![](https://s1.ax1x.com/2022/07/07/j0rVoD.png)

- 在书籍管理界面实现了上传和下载的功能

  - 可以自由上传书籍的封面图片，下载功能的体现在于图片的显示是通过下载呈现的 ，后端配置了一个用于处理上传和下载功能的Controller

  ![](https://s1.ax1x.com/2022/07/07/j0raSs.png)

- 在新闻管理界面集成了富文本编辑框架WangEditor，可以以一个弹窗的形式编辑文本内容

  ![](https://s1.ax1x.com/2022/07/07/j0rI0K.png)

- <font color='red'>**剩下的就是一些小的功能啥的了，小项目也没啥好介绍的，随便写写**</font>

# Springboot后端

1.使用spring Initialzr快速创建项目后 引入mybatis-puls的场景启动器，并注册拦截器以及分页插件

~~~xml
		<!--MyBatis-plus启动器-->
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>mybatis-plus-boot-starter</artifactId>
			<version>3.5.1</version>
		</dependency>
~~~

~~~java
@Configuration
@MapperScan("top.year21.springboot.mapper")
public class MybatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        PaginationInnerInterceptor paginationInnerInterceptor = new PaginationInnerInterceptor(DbType.MYSQL);
        interceptor.addInnerInterceptor(paginationInnerInterceptor);
        return interceptor;
    }
}
~~~

2.分别创建controller视图层、service业务层、dao(mapper)持久层、数据库表对应的实体类entity以及用于各种包和类

- 后端目录结构

![](https://s1.ax1x.com/2022/07/07/j0roTO.png)

3.进行yml配置文件的基础配置

注意：<font color='red'>**default-enum-type-handler、type-enums-package这两个配置必须在yml配置文件中开启否则枚举类无法正常工作**</font>

~~~yml
# 配置数据源
spring:
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    url: jdbc:mysql://localhost:3306/springboot_vue?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root

# 修改默认端口
server:
  port: 9090

mybatis-plus:
  # 开启数据库字段名自动转换为驼峰，指定实体类的包地址
  type-aliases-package: top.year21.springboot.entity
  configuration:
    # 在控制台打印mybatis-plus生成的sql查询语句
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    default-enum-type-handler: org.apache.ibatis.type.EnumOrdinalTypeHandler
  #扫描通用枚举的包
  type-enums-package: top.year21.springboot.enums
~~~

4.编写对应的请求处理Controller

- 由于service业务层和dao持久层使用mybatis-plus提供通用service和通用mapper，故不需要写基础sql语句，可直接进行CRUD

~~~java
@RestController
/*可以区分多个类中的有相同的请求地址
这个controller下的所有请求地址都是以localhost:9090/user/...开始的
 */
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;


    /**
     * Description : 增加新的用户
     * @date 2022/6/22
     * @param user 新增的用户信息
     * @return top.year21.springboot.entity.Message
     **/
    @PostMapping("/save") //例如这个，完整地址是localhost:9090/user/save
    public Message savaUser(@RequestBody User user){
        if (user.getPassWord() == null){
            user.setPassWord("default");
        }
        boolean result = userService.save(user);
        return Message.success().add("插入结果",result);
    }

    /**
     * Description : 更新用户
     * @date 2022/6/23
     * @param user 待更新的用户数据
     * @return top.year21.springboot.entity.Message
     **/
    @PutMapping("/update") //例如这个，完整地址是localhost:9090/user/update
    public Message updateUser(@RequestBody User user){
        boolean result = userService.updateById(user);
        return Message.success().add("更新结果",result);
    }

    @DeleteMapping("/delete") //例如这个，完整地址是localhost:9090/user/update
    public Message deleteUser(@RequestParam Long id){
        boolean result = userService.removeById(id);
        return Message.success().add("更新结果",result);
    }

    /**
     * Description : 模糊查询
     * @date 2022/6/22
     * @param pageNum 当前页码
     * @param pageSize 每页展示的数据量
     * @param search 模糊查询的关键字
     * @return com.baomidou.mybatisplus.extension.plugins.pagination.Page
     **/
    @GetMapping("/query")
    public Message getPageData(@RequestParam("pageNum") Integer pageNum,
                               @RequestParam("pageSize")  Integer pageSize,
                               @RequestParam("search")  String search){
        Page<User> page = new Page<>(pageNum, pageSize);
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.like(StringUtils.isNotBlank("search"),"username",search);
        userService.page(page,userQueryWrapper);
        return Message.success().add("data",page);
    }
}
~~~

5.文件上传下载功能

- 补充一个**微妙**的点 ：在文件**上传成功**之后<font color='red'>**会返回该文件下载的url**</font>，实现前端页面展示需要使用这个文件的功能

~~~java
@RestController
@RequestMapping("/file")
@CrossOrigin(value = "http://localhost:8080/",maxAge = 3600)  //处理前端跨域问题，允许value设定的请求进行跨域
public class FileController {

    //从配置文件中进行取值
    @Value("${server.port}")
    private String port;

    private final String ip = "http://localhost";

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

        //获取项目在服务器上的绝对路径
        //System.getProperty("user.dir")这个命令可以获取springboot_vue_demo在服务器上的绝对路径
        //这里的地址很关键，别写错了 大坑属实
        String finalPath = System.getProperty("user.dir") + "/springboot/src/main/resources/static/img/" + filename;

        //进行保存
        //可以直接将文件写入到服务器硬盘上
        file.transferTo(new File(finalPath));
        //返回下载此文件访问的url
        return Message.success().add("url",ip + ":" + port + "/file/down/" + filename); 
    }

    @GetMapping("/down/{name}")
    public ResponseEntity<byte[]> down(@PathVariable("name") String name) throws IOException {
        //获取服务器中文件的真实路径
        String realPath = System.getProperty("user.dir") + "/springboot/src/main/resources/static/img/" + name;
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
}
~~~

# Demo问题总结

## 前端

1.前端往后端传值是采用封装后的axios发起请求的

创建一个utils文件夹并创建一个js文件

~~~javascript
import axios from 'axios'

const request = axios.create({
    baseURL: '/api',  // 注意！！ 这里是全局统一加上了 '/api' 前缀，也就是说所有接口都会加上'/api'前缀在，页面里面写接口的时候就不要加 '/api'了，否则会出现2个'/api'，类似 '/api/api/user'这样的报错，切记！！！
    timeout: 5000
})

// request 拦截器
// 可以自请求发送前对请求做一些处理
// 比如统一加token，对请求参数统一加密
request.interceptors.request.use(config => {
    config.headers['Content-Type'] = 'application/json;charset=utf-8';

    // config.headers['token'] = user.token;  // 设置请求头
    return config
}, error => {
    return Promise.reject(error)
});

// response 拦截器
// 可以在接口响应后统一处理结果
request.interceptors.response.use(
    response => {
        let res = response.data;
        // 如果是返回的文件
        if (response.config.responseType === 'blob') {
            return res
        }
        // 兼容服务端返回的字符串数据
        if (typeof res === 'string') {
            res = res ? JSON.parse(res) : res
        }
        return res;
    },
    error => {
        console.log('err' + error) // for debug
        return Promise.reject(error)
    }
)
export default request
~~~

在需要使用的页面导入request.js

```javascript
import request from "../utils/request";
```

2.**跨域问题**

<font color='red'>**在同源策略中，要求 域名、协议、端口 这3部分都要相同**</font>

何为跨域问题？跨域是指从一个域名的网页去请求另一个域名的资源。由于有**同源策略**的关系，一般是不允许这么直接访问的。

Vue中是这么处理跨域问题的，通过创建一个vue.config.js的文件进行下面的配置

~~~javascript
// 跨域配置
module.exports = {
    devServer: {                //记住，别写错了devServer
        port: 8080,//设置前端页面本地默认端口  选填
        proxy: {                 //设置代理，必须填
            '/api': {              //设置拦截器  拦截器格式   斜杠+拦截器名字，名字可以自己定
                target: 'http://localhost:9090',     //代理的目标地址
                changeOrigin: true,              //是否设置同源，输入是的
                pathRewrite: {                   //路径重写
                    '^/api': ''                     //选择忽略拦截器里面的内容
                }
            }
        }
    }
}

~~~

3.**前端axios之get和post请求**  [-->参考1](https://blog.csdn.net/m0_50442138/article/details/123769672)   [个人博客的参考](http://year21.top/2022/04/08/JavaWebNew/)

get请求建议使用params进行传值，使用data后端会收不到值

post请求params或者data都行，使用params后端需要用注解@RequestParam 进行接收。使用data后端需要使用@RquestBody接收

~~~javascript
      handleDelete(id){
      request.delete("/user/delete/",{
        params:{
          id:id
        }}).then(res => {
          this.$message({
            type:"success",
            message:"删除成功"
          })
          this.load();
      }).catch(reason => {
        console.log(reason)
      })
    },
     
    load(){
      request.get("/user/query", {
      params:{
        pageNum: this.currentPage,
        pageSize: this.pageSize,
        search: this.search
      }}).then(res => {
        this.tableData = res.extend.data.records //将查询的数据在table中展示
        this.total = res.extend.data.total//显示数据总条数
      }).catch(reason => {
        console.log(reason)
      })
    },
    handleEdit(row){
      this.dialogVisible = true;
      this.form = JSON.parse(JSON.stringify(row))
    },  
~~~

4.**vue语法部分**

①关于Vue 的 export、export default、import [ ---> 点击这个](https://blog.csdn.net/harry5508/article/details/84025146)

~~~vue
<script>
import Header from "@/components/Header";
import AsideMenu from "@/components/AsideMenu";

export default{
  name: "Layout",
  components:{
    Header,
    AsideMenu
  }
}
</script>
~~~

②关于vue的组件中为什么data必须是一个函数？[ ---> 点击这个](https://zhuanlan.zhihu.com/p/190859741)

解释：因为如果data是一个对象则会造成数据共享，在多次使用该组件时，改变其中一个组件的值会影响全部该组件的值。

而如果是通过函数的形式返回出一个对象的话，在每次使用该组件时返回出的对象的地址指向都是不一样的，

这样就能让各个组件的数据独立。

~~~vue
<script>
export default {
  name: 'Home',
  components: {
  },
  data(){
    return{
      search: '',
      currentPage: 1,
      pageSize:10,
      total: 10,
      tableData: [
        {
          date: '2016-05-03',
          name: 'Tom',
          address: 'No. 189, Grove St, Los Angeles',
        },
      ]
    }
  },
  methods:{
    handleEdit(){
      alert("测试成功")
    },  
  }
}
</script>
~~~

③关于在.vue文件中为什么要在script中使用一个export default  [---> vue.js官网的视频解释](https://learning.dcloud.io/#/?vid=14)

export default 可以定义一个组件的属性、数据、函数等内容，同时将该组件声明为公共的，方便复用

~~~java
<script lang="js" >
export default {
  //定义组件名字
  name: 'Home',
  //局部注册其他地方的组件
  components: {
  },
  //定义该组件的私有数据
  data(){
    return{
      form: {},
      dialogVisible: false,
      search: '',
      currentPage: 1,
      pageSize:10,
      total: 10,
      tableData: []
    }
  },
  //定义该组件的业务处理方法
  methods:{
    add:function () {
      this.dialogVisible = true;
      this.form = {};
    }

  }
}
</script>
~~~

④关于有些文件为什么import和export default同时存在 [---> 单组件注册和全局注册](https://cn.vuejs.org/v2/guide/components-registration.html)

~~~java
<script>
//import需要使用的组件，并且在下面进行局部注册，才能在当前文件使用组件标签调用该组件
import Header from "./components/Header";
import AsideMenu from "./components/AsideMenu";

export default{
  //局部注册以下组件
  //这里的AsideMenu是缩写，完整写法是 AsideMenu：AsideMenu
  components:{
    AsideMenu,
    Header,
  }
}
</script>
~~~

<font color='red'>**5.组件注册问题**</font>

在某个vue组件中引入自定义的组件时，控制台报错组件未能被解析：Failed to resolve component:XXX IF this is a native custom element,

​										make sure to exclude it from component resolution via cpmpilerOptions.isCustomElement at xxx 

> 不知道是我的操作顺序有问题，先在组件的components中注册再引入对应的自定义组件之后，就会出现这个错误

**个人的解决方法是按照这个顺序：**①先在对应的vue文件中import xxx from  ‘组件的路径 ’  

​								   						②再在compones中进行局部注册

​								  						 ③最后在页面的template标签中进行使用 

## 后端

1.后端接收前端传值

<font color='red'>**JSON parse error（JSON解析错误）: Cannot deserialize value of type** </font>

![](https://s1.ax1x.com/2022/07/07/j0r7kD.png)

针对这次异常解析：后端这个实体类属性使用的是**枚举类型**，前端[单选框radio](https://element.eleme.cn/#/zh-CN/component/radio)中label标签中传的值与枚举类中所有对象的value值没有一个匹配。

​									而且在mybatis-plus中，使用枚举类则必须在yml配置文件中将这两个配置**default-enum-type-handler、type-enums-package **开启

​									否则枚举类无法取值和赋值

<font color='red'>**实际上，大部分的JSON parse error这个异常都是由于前端传到后端的值与后端所需要的类型不一致所导致的，可以重点排查这个问题**</font>

 2.关于实现在用户管理界面异步查询当前行用户所关联的图书数据问题

程序调用的流程：

- 点击查看图书列表 --> 发起axios请求 --> 后端接口(/user/userBookList) 处理该请求  --> userService调用serviceFindPage(id) 方法

  --> userMapper调用findPage(id) 方法 --> UserMapper接口 --> UserMapper接口对应的映射文件UserMapper.xml -->  调用该xml的sql语句进行查询

  -->  返回查询结果(这里是返回了一个User对象，内包含要的数据) --> 将需要的数据填充到弹出的弹窗表格内

这里的sql语句查询的类型是一对多，查询的结果是个集合，因此使用List包装，此外一对多查询字段不一致要使用resultMap包装

~~~xml
<mapper namespace="top.year21.springboot.mapper.UserMapper">

    <resultMap id="userBookList" type="user">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="password" column="password"></result>
        <result property="nickName" column="nick_name"></result>
        <result property="age" column="age"></result>
        <result property="sex" column="sex"></result>
        <result property="address" column="address"></result>
        <result property="role" column="role"></result>

        <collection property="bookList" ofType="book">
            <!-- 这里是b_id字段要和下方保存一致是因为 查询后的结果起了别名，也就是字段名发生了变化，要根据字段进行赋值-->
            <id property="id" column="b_id"></id>
            <result property="name" column="b_name"></result>
            <result property="price" column="b_price"></result>
        </collection>
    </resultMap>
    
    <!--    Page<User> findPage(Page<User> page);-->
    <select id="findPage" resultMap="userBookList">
            SELECT u.*,b.`id` b_id,b.`name` b_name,b.`price` b_price
            FROM `user` u LEFT JOIN `book` b
            ON u.id = b.`user_id`
            WHERE u.`id` = #{id}
    </select>
</mapper>
~~~

