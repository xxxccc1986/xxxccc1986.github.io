---
title: SpringMVC
date: 2022-04-14 11:24:02
updated: 2022-04-18 11:24:02
tags:
  - SpringMVC
categories:
  - 框架
keywords: SpringMVC
cover: https://w.wallhaven.cc/full/pk/wallhaven-pkxwje.png
copyright_author: Year21
copyright_url: http://year21.top/2022/04/18/SpringMVC	
---




## 前置概念

### MVC的概念

MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分

M：Model，模型层，指工程中的JavaBean，作用是处理数据

JavaBean分为两类：

- 一类称为实体类Bean：专门存储业务数据的，如 Student、User 等
- 一类称为业务处理 Bean：指 Service 或 Dao 对象，专门用于处理业务逻辑和数据访问。

V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据

C：Controller，控制层，指工程中的servlet，作用是接收请求和响应浏览器

MVC的工作流程：

用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller

再根据请求处理的结果找到相应的View视图，渲染数据后最终响应给浏览器

### SpringMVC

SpringMVC是Spring的一个后续产品，是Spring的一个子项目

- SpringMVC 是 Spring 为**表述层**开发提供的一整套完备的解决方案。
- 三层架构分为表述层（或表示层）、业务逻辑层、数据访问层，表述层表示前台页面和后台servlet程序

---

## 一个测试DEMO

配置web.xml的两种方式

1.默认配置方式

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    
<!--  配置SpringMVC的前端控制器,对浏览器发送的请求进行统一的处理  -->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <!--
       设置springMVC的核心控制器所能处理的请求的请求路径
       /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
       但是/不能匹配.jsp请求路径的请求
       - jsp本质上就是一个servlet程序，.jsp请求路径由其相应的servlet程序进行处理
       - 如果不进行排除，则前端控制器也会对.jsp请求进行默认处理，一旦处理了.jsp的就不能正常显示
        -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
~~~

2.扩展配置方式

~~~xml
<!--  配置SpringMVC的前端控制器,对浏览器发送的请求进行统一的处理  -->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--  配置SpringMVC配置文件的位置和名称 -->
    <!--  如果没有设置init-param标签，那么SpringMVC的配置文件默认位于WEB-INF下，默认名称为<servlet-name>标签中的昵称
       例如没有设置，则以下配置所对应SpringMVC的配置文件位于WEB-INF下web.xml中，文件名为DispatcherServlet-servlet.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
    <!--  将前端控制器DispatcherServlet的初始化时间提前到服务器启动时 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <!--
       设置springMVC的核心控制器所能处理的请求的请求路径
       /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
       但是/不能匹配.jsp请求路径的请求
       - jsp本质上就是一个servlet程序，.jsp请求路径由其相应的servlet程序进行处理
       - 如果不进行排除，则前端控制器也会对.jsp请求进行处理，一旦处理了.jsp的请求，就找不到相应的页面也就不能正常显示
        -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
~~~

### 测试请求转发

在前端控制器中定义请求处理的方法

~~~java
//SpringMVC的控制器由一个POJO（普通的Java类）担任，
//因此需要通过@Controller注解将其标识为一个控制层组件，交给Spring的IoC容器管理
//此时SpringMVC才能够识别控制器的存在
@Controller
public class CommonController {
//@RequestMapping注解的作用是将当前请求与控制器方法之间创建映射关系
// @RequestMapping注解的value属性可以通过请求地址匹配请求，
    @RequestMapping("/target")
    public String testTarget(){
        //返回视图名称
        return "target";
    }
}
~~~

创建跳转页面

~~~html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
<h1>首页</h1>
<!--使用th:href标签渲染后，在@{}识别到里面属性值是绝对路径，就会自动此路径前面添加上下文路径，也就是工程路径}-->
<a th:href="@{/target}">点击跳转至目标页面</a>
</body>
</html>
~~~

- 总结请求页面的过程： 

  ①浏览器发送请求，若请求地址符合前端控制器的 url-pattern，该请求就会被前端控制器DispatcherServlet处理。 

  ②前端控制器会读取 SpringMVC 的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中 @RequestMapping 注解的 value 属性值进行匹配，

  ③若匹配成功，该注解所标识的控制器方法就是处理请求的方法。 

  ④处理请求的方法需要返回一个字符串类型的视图名称，

  ⑤该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径，通过 Thymeleaf 对视图进行渲染，最终转发到视图所对应页面。

---

## @RequestMapping注解

作用：将当前请求与控制器方法之间创建映射关系

- 必须保证请求地址与所有的控制器里面RequestMapping注解的value值是唯一匹配的，
- 也就是说一个控制器方法只能对应一个请求路径，不能会产生冲突

### 此注解的位置区别

@RequestMapping标识一个类：设置映射请求的请求路径的初始信息

@RequestMapping标识一个方法：设置映射请求请求路径的具体信息

- 如果一个类和类中的方法都设置了@RequestMapping注解，在默认情况下要先访问初始信息，再访问具体信息
- 请求映射的地址多一层目录才能正常访问
- 可用于在多个模块中存在统一的请求参数，如order和user模块都有list参数，可用通过在类上添加@RequestMapping进行区分
- 则请求地址分别变为/order/list/.. 、/user/list/..

~~~java
@Controller
@RequestMapping("/test")
public class RequestMappingController {

	//此时请求映射所映射的请求的请求路径为：/test/testRequestMapping
    @RequestMapping("/testRequestMapping")
    public String testRequestMapping(){
        return "success";
    }
}
~~~

### value属性

①value属性通过请求的请求地址匹配请求映射

②value属性是一个字符串类型的数组，表示该请求映射能够匹配多个请求地址所对应的请求，满足其中一个即可

③value属性必须设置，至少通过请求地址匹配请求映射

- 第②点的总结就是：一个请求地址只能匹配一个控制器方法，而一个控制器方法可以去匹配多个请求地址
- 404-没有与任何一个@RequestMapping的value值匹配

~~~java
@RequestMapping(
        value = {"/test1", "/test2"}
)
public String testRequestMapping(){
    return "test";
}
~~~

~~~xml
<a th:href="@{/test1}">测试@RequestMapping的value属性-->/test1</a><br>
<a th:href="@{/test2}">测试@RequesstMappsing的value属性-->/test2</a><br>
~~~

### method属性

①method属性通过请求的请求方式（get或post）匹配请求映射

②method属性是一个RequestMethod类型的数组，表示该请求映射能够匹配多种请求方式的请求，满足其中一个即可

- 若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错405：Request method 'POST' not supported
- 405-没有与任何一个@RequestMapping的method值匹配

~~~java
//此@RequestMapping的属性表示请求地址满足"/test1","/test2"其中即可，
    //但请求方式必须是get请求才可以匹配此控制器方法
    @RequestMapping(
            value = {"/test1","/test2"},
            method = {RequestMethod.GET}
    )
    public String getTest(){
        return "test";
    }
~~~

~~~xml
<body>
测试成功 <br>
<a th:href="@{/test1}">测试method的get方式提交</a> <br>
<form th:action="@{/test2}" method="post">
    <input type="submit" value="提交">
</form>
</body>
~~~

派生的其他注解

> SpringMVC中提供了@RequestMapping的派生注解
>
> ①处理get请求的映射-->@GetMapping
>
> ②处理post请求的映射-->@PostMapping
>
> ③处理put请求的映射-->@PutMapping
>
> ④处理delete请求的映射-->@DeleteMapping

~~~java
//表示请求地址为testGetMapping且请求方式必须是get才能匹配上
@GetMapping("/testGetMapping")
    public String testGetMapping(){
        return "test";
    }
~~~

### params属性

param是通过请求参数来进行匹配请求映射，**params属性是要求必须全部满足才能实现匹配**

- 400-没有满足任何一个@RequestMapping的全部params值

-  params = {"!username"}  要求请求地址不能携带username参数
-  params = {"username","password=123"}  要求请求地址必须携带username参数且password参数必须是123

~~~java
//测试RequestMapping的params和headers属性
    @RequestMapping(value = "/testParamsAndHeaders",
            params = {"!username"})//此处要求请求参数必须不能携带username的参数
    public String testParamsAndHeaders(){
        return "test";
    }
~~~

### headers属性

@RequestHeader是将请求头信息和控制器方法的形参创建映射关系，**headers属性是要求必须全部满足才能实现匹配**

@RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

- 404-没有满足任何一个@RequestMapping的全部headers值

### springMVC支持ant风格路径

？：表示任意的单个字符 

*：表示任意的0个或多个字符 

**：表示任意的一层或多层目录 

注意：在使用**时，只能使用//xxx的方式

### springMVC支持路径中的占位符

原始方式：/deleteUser?id=1 

restful方式：/deleteUser/1 

过程：SpringMVC路径中的占位符常用于RESTful风格中，当请求路径中将某些数据通过路径的方式传输到服务器中

​			就可以在相应的@RequestMapping注解的value属性中通过占位符{xxx}表示传输的数据，

​			在通过@PathVariable注解，将占位符所表示的数据赋值给控制器方法的形参

~~~xml
<a th:href="@{/testPath/1/username}">测试@RequestMapping注解中springMVC支持路径中的占位符</a> <br>
~~~

~~~java
//测试springMVC支持路径中的占位符
    //每一个要传的参数都要使用一个/{}占位符
    @RequestMapping("/testPath/{id}/{username}")
    public String testPath(@PathVariable("id")Integer id,@PathVariable("username")String username){
        System.out.println("id:" + id + ",username:" + username);
        return "test";
    }
~~~

---

## 获取请求参数

1.通过原生的ServletAPI获取

~~~java
@RequestMapping("/testServletAPI")
    //原生的servletAPI获取请求参数
    //形参位置的request表示当前请求
    public String testServletAPI(HttpServletRequest request){
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        System.out.println("username:" + username + ",password:" + password);
        return "test";
    }
~~~

2.通过控制器方法的形参获取请求

当控制器方法的形参名字和请求参数名字相同的时候

当浏览器发送请求，匹配到请求映射时，在DispatcherServlet中就会将请求参数赋值给相应的形参

~~~xml
<a th:href="@{/testServletAPI(username='admin',password=123)}">测试使用控制器的形参获取请求参数</a> <br>
~~~

~~~java
@RequestMapping("/testParam")
    //使用控制器的形参获取请求参数
    public String testParam(String username,String password){
        System.out.println("username:" + username + ",password:" + password);
        return "test";
    }
~~~

3.@RequestParam注解

- 当请求参数的名字和控制器方法形参名字不同的时候可以采用此注解获取请求参数

@RequestParam是将请求参数和控制器方法的形参创建映射关系

@RequestParam注解一共有三个属性：

value：指定为形参赋值的请求参数的参数名

required：设置是否必须传输此请求参数，默认值为true

①若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置defaultValue属性，则页面报错400：Required String 

parameter 'xxx' is not present；

②若设置为false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null

defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""空串时，则使用默认值为形参赋值

~~~xml
<form th:action="@{/testParam}" method="get">
用户名：<input type="text" name="user_name"><br>
密码：<input type="password" name="password"><br>
爱好：<input type="checkbox" name="hobby" value="a">a
<input type="checkbox" name="hobby" value="b">b
<input type="checkbox" name="hobby" value="c">c<br>
<input type="submit" value="测试使用控制器的形参获取请求参数">
</form>
~~~

~~~java
@RequestMapping("/testParam")
    //使用@RequestParam注解获取请求参数
    public String testParam(
            //required属性代表是否要传输value中的请求参数,默认为true，不传将报错
            //而以下改成了required = false，就意味传不传也可以，不传默认赋值为null
            //defaultValue属性代表不传值或传的是空串将以属性默认值赋值，传值以传的属性值赋值
            @RequestParam(value = "user_name", required = false, defaultValue = "default") String username,
            String password,
            String[] hobby,  
        	@RequestHeader(value = "defaultname", required = true, defaultValue = "default") String host,
            @CookieValue("JSESSIONID") String JSESSIONID){
        //若请求参数中出现多个同名的请求参数，可以再控制器方法的形参位置设置字符串类型或字符串数组接收此请求参数
        //若使用字符串类型的形参，最终结果为请求参数的每一个值之间使用逗号进行拼接
        System.out.println("username:"+username+",password:"+password+",hobby:"+ Arrays.toString(hobby));
       	//hobby的值是 ---> hobby：[a,b,c]
        return "test";
    }
~~~

 4、@RequestHeader

@RequestHeader是将请求头信息和控制器方法的形参创建映射关系

@RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

 5、@CookieValue

@CookieValue是将cookie数据和控制器方法的形参创建映射关系

@CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

6.通过使用实体类接收请求参数

~~~xml
<form th:action="@{/testBean}" method="post">
    用户名：<input type="text" name="username"><br>
    密码：<input type="password" name="password"><br>
    性别：<input type="radio" name="sex" value="男">男<input type="radio" name="sex" value="女">女<br>
    年龄：<input type="text" name="age"><br>
    邮箱：<input type="text" name="email"><br>
    <input type="submit" value="使用实体类接收请求参数">
</form>
~~~

~~~java
@RequestMapping("/testBean")
    //使用实体类获取请求参数
    public String testBean(User user){
        System.out.println(user);
        return "test";
    }
//打印的结果是User{id=null, username='test', password='gyS52W6F3irsaEM', age=12, sex='男', email='123456@qq.com'}
~~~

7.解决获取请求参数的乱码问题

~~~xml
<!--配置springMVC的编码过滤器-->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
~~~

- SpringMVC中处理编码的过滤器一定要配置到其他过滤器之前，否则无效

---

## 域对象共享数据

- **需要记住一点是：无论使用的哪一种方式共享数据，最终都会将视图名称和共享的域数据封装在一个ModelAndView对象中**

~~~java
//在执行控制器方法之前mv是null，执行完之后此mv对象的属性为视图名称和共享的域数据
mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
if (asyncManager.isConcurrentHandlingStarted()) {
    return;
}
this.applyDefaultViewName(processedRequest, mv);
~~~

1.使用原生的ServletAPI向request域对象共享数据

~~~xml
<a th:href="@{/testRequestServletAPI}">通过ServletAPI向request域中共享数据</a>
~~~

~~~java
//通过原生的ServletAPI共享数据 
@RequestMapping("/testRequestServletAPI")
    public String testServletAPI(HttpServletRequest request){
        request.setAttribute("RequestServletAPI","RequestServletAPI");
        return "success";
    }
~~~

2.使用ModelAndView向request域对象共享数据

~~~java
//使用ModelAndView向request域对象共享数据
/**
     * ModelAndView有Model和View的功能
     * Model主要用于向请求域共享数据
     * View主要用于设置视图，实现页面跳转
     */
    @RequestMapping("/testModelAndView")
    //必须将ModelAndView对象作为返回值，这样才能被前端控制器所解析
    public ModelAndView testModelAndView(){
        ModelAndView mav = new ModelAndView();
        //处理模型数据，即向request对象中共享数据
        mav.addObject("RequestServletAPI","test success");

        //设置视图名称
        mav.setViewName("success");
        return mav;
    }
~~~

3.使用Model向request域对象共享数据

~~~java
//使用Model向request域对象共享数据
    @RequestMapping("/testModel")
    public String testModel(Model model){
        model.addAttribute("request","test success");
        return "success";
    }
~~~

4.使用map向request域对象共享数据

~~~java
//使用map向request域对象共享数据
    @RequestMapping("/testMap")
    public String testMap(Map<String,Object> map){
        map.put("request","test success");
        return "success";
    }
~~~

5.使用ModelMap向request域对象共享数据

~~~java
//使用ModelMap向request域对象共享数据
    @RequestMapping("/testModelMap")
    public String testModelMap(ModelMap modelMap){
        modelMap.addAttribute("request","test success");
        return "success";
    }
~~~

6.Model、Model、Map三者的相关

Model、ModelMap、Map类型的参数其实本质上都是 BindingAwareModelMap 类的对象实例

~~~java
public interface Model{}
public class ModelMap extends LinkedHashMap<String, Object> {}
public class ExtendedModelMap extends ModelMap implements Model {}
public class BindingAwareModelMap extends ExtendedModelMap {}
~~~

![继承与实现图示](https://s1.ax1x.com/2022/04/14/LQL82R.png)

7.向session域共享数据

~~~java
//向session域共享数据
    @RequestMapping("/testSession")
    public String testSession(HttpSession session){
        session.setAttribute("testSession","hello session");
        return "success";
    }
~~~

8.向application域共享数据

~~~java
//向application域共享数据
    @RequestMapping("/testApplication")
    public String testApplication(HttpSession session){
        ServletContext servletContext = session.getServletContext();
        servletContext.setAttribute("servletContext","hello Application");
        return "success";
    }
~~~

---

## 视图

SpringMVC中的视图是View接口，其作用是渲染数据，将模型Model中的数据展示给用户。

- SpringMVC视图默认有转发视图(InternalResourceView)和重定向视图(RedirectView)

- 当工程引入jstl的依赖，转发视图会自动转换为JstlView

- 若视图技术为Thymeleaf，且SpringMVC的配置文件中配置了Thymeleaf的视图解析器，由此视图解析器解析之后所得到的是ThymeleafView视图

### ThymeleafView

- **当视图名称没有任何前缀的时候才会被视图解析器所解析，从而拼接前后缀创建thymeleafView视图**，最后通过转发的方式实现跳转

~~~java
@RequestMapping("/testThymeleafView")
    public String testThymeleafView(){
        return "success";
    }
~~~

![thymeleafView视图底层创建](https://s1.ax1x.com/2022/04/14/LQLNqK.png)

### 转发视图

- **当视图名称以"forward:"为前缀时，实际上是创建了两次视图**，先创建InternalResourceView视图，

  此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，

  而是会将前缀"forward:"去掉，剩余部分作为最终路径通过转发的方式实现跳转

- SpringMVC中默认的转发视图是InternalResourceView

补充点：在有thymeleaf渲染的html页面不能直接通过具体网页名字进行访问，必须经过thymeleaf视图解析器解析才能访问

​				因为html页面都是位于WEB-INF文件夹，这个文件夹对外进行了保护，不能直接访问，只能通过请求转发进行访问

~~~java
@RequestMapping("/testForward")
    public String testForward(){   
        return "forward:/testThymeleafView";
	}
~~~

### 重定向视图

- **当视图名称以"redirect:"为前缀时，实际上是创建了两次视图**，先创建RedirectView视图，

  此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，

  而是会将前缀"redirect:"去掉，剩余部分作为最终路径通过重定向的方式实现跳转

- SpringMVC中默认的重定向视图是RedirectView

~~~java
@RequestMapping("/testRedirect")
    public String testRedirect(){
        return "redirect:/testThymeleafView";
    }
~~~

### 视图控制器

当控制器方法中，仅仅用来实现页面跳转，即只需要设置视图名称时，可以将处理器方法使用view-controller标签进行表示

~~~xml
<!--
        path：设置处理的请求地址
        view-name：设置请求地址对应的视图名称
          注：当在SpringMVC的配置文件中添加上了视图控制器后
          将会导致控制器中的所有请求映射全部失效，
          必须在下方开启MVC注解驱动才能保证正常的请求映射
  -->
    <mvc:view-controller path="/" view-name="index"></mvc:view-controller>

    <!--   开启MVC注解驱动 -->
    <mvc:annotation-driven/>
~~~

---

## RESTful风格

REST：**Re**presentational **S**tate **T**ransfer，表现层资源状态转移。

表现层：前台html等页面和控制层 

资源：部署到服务器上工程的文件内容

资源状态：资源的表现形式，如jsp、html等

状态转移：在客户端和服务器端之间转移（transfer）代表资源状态的表述。通过转移和操作资源的表述，来间接实现操作资源的目的。

- **核心就是一句话：相同的请求url路径根据不同的请求方式来实现不同的操作**

### RESTful的实现

RESTful的实现就是在Http协议中，以四个操作动词区分不同的资源操作方式

①增：POST 用来新建资源

②删：DELETE 用来删除资源

③改：PUT 用来更新资源

④查：GET 用来获取资源

| 操作     | 传统方式         | REST风格                |
| -------- | ---------------- | ----------------------- |
| 查询操作 | getUserById?id=1 | user/1-->get请求方式    |
| 保存操作 | saveUser         | user-->post请求方式     |
| 删除操作 | deleteUser?id=1  | user/1-->delete请求方式 |
| 更新操作 | updateUser       | user-->put请求方式      |

### HiddenHttpMethodFilter

由于浏览器只支持发送get和post方式的请求

SpringMVC 提供了 **HiddenHttpMethodFilter** 可以**将 POST 请求转换为 DELETE 或 PUT 请求**

- **注：但必须要满足两个两个条件**
  - 第一个条件是：提交方式必须是post
  - 第二个条件是：必须携带一个 **name=”_method“，value=”当前想要提交的请求方式“ **的隐藏域

~~~html
<form th:action="@{/user}" method="post">
    <input type="hidden" name="_method" value="DELETE">
    用户名：<input type="text" name="username"/> <br>
    密码：<input type="password" name="password"/> <br>
    <input type="submit" value="删除用户"/>
</form>
~~~

在web.xml中配置HiddenHttpMethodFilter

- 在所有注册的Filter过滤器中，用于处理编码集的Filter过滤器必须在第一个
  - 原因是：**在处理编码集的Filter过滤器执行之前获取过参数，那么此过滤器就不再有效**

~~~xml
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
~~~

### Restful案例

仅展示部分代码，详情请在SpringMVC04的maven工程下查看

~~~java
@Controller
public class RestController {
    @Autowired
    private EmployeeDao employeeDao;
    /**
     * Description : 查询所有用户
     * @date 2022/4/11
     * @time 15:40
     * @user hcxs1986
     * @return org.springframework.web.servlet.ModelAndView
     **/
    @RequestMapping(value = "/employee",method = RequestMethod.GET)
    public ModelAndView getAllEmployee(){
        ModelAndView mav = new ModelAndView();
        mav.setViewName("employee_list");
        Collection<Employee> empList = employeeDao.getAll();
        mav.addObject("empList",empList);
        return mav;
    }

    /**
     * Description : 删除指定用户
     * @date 2022/4/11
     * @time 16:20
     * @user hcxs1986
     * @param id 要删除的用户id
     * @return java.lang.String
     **/
    @RequestMapping(value = "/employee/{id}",method = RequestMethod.DELETE)
    public String deleteEmpById(@PathVariable("id")Integer id ){
        employeeDao.delete(id);
        return "redirect:/employee";
    }

    /**
     * Description : 添加用户
     * @date 2022/4/11
     * @time 16:33
     * @user hcxs1986
     * @param employee 添加的用户对象
     * @return java.lang.String
     **/
    @RequestMapping(value = "/employee",method = RequestMethod.POST)
    public String addEmployee(Employee employee){
        employeeDao.save(employee);
        return "redirect:/employee";
    }
    
    /**
     * Description : 实现更新回显的功能
     * @date 2022/4/11
     * @time 18:00
     * @user hcxs1986
     * @param id 更新的id
     * @param model 用于共享域数据
     * @return java.lang.String
     **/
    @RequestMapping(value = "/employee/{id}",method = RequestMethod.GET)
    public String getEmployee(@PathVariable("id")Integer id, Model model){
        Employee employee = employeeDao.get(id);
        model.addAttribute("employee",employee);
        return "updateEmp";
    }
    
    /**
     * Description : 更新用户信息
     * @date 2022/4/11
     * @time 18:03
     * @user hcxs1986
     * @param employee 更新的对象
     * @return java.lang.String
     **/
    @RequestMapping(value = "/employee",method = RequestMethod.PUT)
    public String updateEmployee(Employee employee){
        employeeDao.save(employee);
        return "redirect:/employee";
    }
}
~~~

---

## HttpMessageConverter

- 概念：报文信息转换器，将请求报文转换为Java对象，或将Java对象转换为响应报文

- HttpMessageConverter提供了两个注解和两个类型：@RequestBody，@ResponseBody，RequestEntity，ResponseEntity

### @RequestBody

- 用于在控制器方法的形参上

用法：此注解可以获取请求体，需要在控制器方法设置**一个形参**

​			使用@RequestBody进行标识，当前请求的请求体就会为当前注解所标识的形参赋值

~~~java
@RequestMapping(value = "/testRequestBody",method = RequestMethod.POST)
    public String  testRequestBody(@RequestBody  String requestBody){
        System.out.println(requestBody);
        return "test";
    }
~~~

### RequestEntity

- 用于在控制器方法的形参其类型上

用法：RequestEntity封装请求报文的一种类型，**需要在控制器方法的形参中设置该类型的形参**，当前请求的请求报文就会赋值给该形参，

​			可以通过getHeaders()获取请求头信息，通过getBody()获取请求体信息

~~~java
@RequestMapping(value = "/testRequestEntity",method = RequestMethod.POST)
    public String  testRequestBody(RequestEntity<String>  requestEntity){
        //requestEntity代表整个请求报文的信息
        System.out.println(requestEntity.getHeaders());
        System.out.println(requestEntity.getBody());
        return "test";
    }
~~~

### @ResponseBody

- 用于在控制器方法上

作用：此注解用于标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

~~~java
@RequestMapping(value = "/testResponseBody")
    @ResponseBody()
    public String testResponseBody(){
        return "success";
    }
~~~

### @RestController注解

**@RestController注解是springMVC提供的一个复合注解，此注解标识在控制器的类上**，

**就相当于为类添加了@Controller注解，并且为其中的每个方法添加了@ResponseBody注解**

### ResponseEntity

- 用于在控制器方法的返回值类型

ResponseEntity用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文

### SpringMVC处理json

步骤：

①导入jackson的依赖

②在SpringMVC的核心配置文件中开启mvc的注解驱动，此时在HandlerAdaptor中会自动装配一个消息转换器：

​	MappingJackson2HttpMessageConverter，可以将响应到浏览器的Java对象转换为Json格式的字符串

③在处理器方法上使用@ResponseBody注解进行标识

④将Java对象直接作为控制器方法的返回值返回，就会自动转换为Json格式的字符串

---

## SpingMVC处理文件的上传和下载

1.文件的下载

~~~java
@RequestMapping("/testDown")
    public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
        //获取ServletContext对象
        ServletContext servletContext = session.getServletContext();
        //获取服务器中文件的真实路径
        String realPath = servletContext.getRealPath("/static/img/蓝屏.jpg");
        //创建输入流
        InputStream is = new FileInputStream(realPath);
        //创建字节数组
        byte[] bytes = new byte[is.available()];
        //将流读到字节数组中
        is.read(bytes);
        //创建HttpHeaders对象设置响应头信息
        MultiValueMap<String, String> headers = new HttpHeaders();
        //设置要下载方式以及下载文件的名字
        headers.add("Content-Disposition", "attachment;filename=" + URLEncoder.encode("蓝屏.jpg","UTF-8"));
        //设置响应状态码
        HttpStatus statusCode = HttpStatus.OK;
        //创建ResponseEntity对象
        ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers, statusCode);
        //关闭输入流
        is.close();
        return responseEntity;
    }
~~~

2.文件的上传

~~~java
@PostMapping("/testUp")
    public String testUp(MultipartFile photo,HttpSession session) throws IOException {
        //获取上传的文件的文件名
        String filename = photo.getOriginalFilename();
        //获取上传的文件的后缀名
        String suffixName = filename.substring(filename.lastIndexOf("."));
        //将UUID作为文件名
        String uuid = UUID.randomUUID().toString();
        //将uuid和后缀名拼接后的结果作为最终的文件名
        filename = uuid + suffixName;
        //通过ServletContext获取服务器的photo目录
        ServletContext servletContext = session.getServletContext();
        //getRealPath()该方法用于获取虚拟路径的真实路径。
        //在这里表示获取文件上传存放的目录，即photo文件夹在真实的服务器上的位置
        String photoPath = servletContext.getRealPath("photo");
        File file = new File(photoPath);
        //判断photoPath所对应路径是否存在
        if (!file.exists()){
            file.mkdir();
        }
        String finalPath = photoPath + File.separator + filename;
        photo.transferTo(new File(finalPath));
        return "test";
    }
~~~

---

## 拦截器

- 拦截器用于拦截控制器方法的执行

SpringMVC中的拦截器需要实现HandlerInterceptor，因此必须在SPringMVC的配置文件中进行配置

配置信息

~~~xml
<!-- 配置拦截器   -->
    <mvc:interceptors>
<!--        <bean class="mvc.intercepts.Intercept1"/>  会拦截dispatchServlet的所有的请求-->
<!--        <ref bean="intercept1"></ref>  会拦截dispatchServlet的所有的请求-->
<!--    此拦截其表示拦截上下文路径也就是工程路径下任何请求，但把首页的请求排除在外，不会进行拦截    -->
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <mvc:exclude-mapping path="/"/>
            <ref bean="intercept1"></ref>
        </mvc:interceptor>
    </mvc:interceptors>
~~~

~~~java
@Component
public class Intercept1  implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("Intercept1-->preHandle");
        return true;//返回值为true表示放行，返回值为false表示拦截
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("Intercept1-->postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("Intercept1-->afterCompletion");
    }
}
~~~

### 拦截器的抽象方法

SpringMVC中的拦截器有三个抽象方法：

preHandle：控制器方法执行之前执行preHandle()，其boolean类型的返回值表示是否拦截或放行，

​					  返回true为放行，即调用控制器方法；返回false表示拦截，即不调用控制器方法

postHandle：控制器方法执行之后执行postHandle()

afterComplation：处理完视图和模型数据，渲染视图完毕render()方法之后执行afterCompletion()

### 多个拦截器的先后顺序

- 情况一：a>若每个拦截器的preHandle()都返回true

​		此时多个拦截器的执行顺序和拦截器在SpringMVC的配置文件的配置顺序有关：

​		**preHandle()会按照拦截器在配置中的正序执行，**

​		**而postHandle()和afterComplation()会按照拦截器在配置中的反序执行**

- 情况二：b>若某个拦截器的preHandle()返回了false

​		**preHandle()返回false和它之前的拦截器的preHandle()都会执行，**

​		**postHandle()都不执行，返回false的拦截器之前的拦截器的afterComplation()会执行**

![处理器执行链](https://s1.ax1x.com/2022/04/14/LQLDGd.png)

**HandlerExecutionChain处理器执行链，其中的数据包含了当前的控制器方法、拦截器集合以及拦截器的索引**

---

## 异常处理器

SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口：HandlerExceptionResolver

HandlerExceptionResolver接口的实现类有：DefaultHandlerExceptionResolver(处理常见的在控制器方法执行过程中出现的异常)

​																			  SimpleMappingExceptionResolver(处理自定义的异常)

### 自定义异常处理器

- SpringMVC提供了自定义的异常处理器SimpleMappingExceptionResolver

1.基于配置方式

~~~xml
<!--  配置异常处理
       prop标签中key是异常的全类名，prop标签的内容是出现异常即将跳转的视图名称
       无任何前后缀创建ThymeLeaf视图
       有前缀的名称将创建InternalResourceView视图
       有后缀的名称将创建RedirectView视图
-->
    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="exceptionMappings">
            <props>
                <prop key="java.lang.ArithmeticException">error</prop>
            </props>
        </property>
<!--   设置将异常信息共享在请求域中的键：
        在异常页面就可以从request域中获取异常信息
        其中，下方标签的value为键，所出现的异常信息为值
     -->
        <property name="exceptionAttribute" value="ex"></property>
    </bean>
~~~

2.基于注解方式

~~~java
@ControllerAdvice //此注解也具备了将类标识为组件的功能
public class ExceptionController {
    /**
     * Description : 当出现了value内的异常之一，就会将下方的方法作为新的控制器方法进行执行
     * @date 2022/4/12
     * @time 23:02
     * @user hcxs1986
     * @return java.lang.String
     **/
    @ExceptionHandler(value = {ArithmeticException.class,NullPointerException.class})
    public String test(Exception ex, Model model){
        model.addAttribute("ex",ex);
        return "error";
    }
}
~~~

---

## 注解配置SpringMVC

- 可以使用配置类和注解代替web.xml和springMVC的配置文件

实现的过程是：

①基于Servlet3.0环境，tomcat服务器查找实现  javax.servlet.ServletContainerInitializer接口的类

②SpringServletContainerInitializer此类 实现了 上述接口

③SpringServletContainerInitializer此类 查找实现 WebApplicationInitializer接口的类

④AbstractAnnotationConfigDispatcherServletInitializer 实现了 WebApplicationInitializer接口

⑤若自定义类继承了 AbstractAnnotationConfigDispatcherServletInitializer类 则会自动使用它配置Servlet上下文。

1.初始化类代替web.xml

- web.xml作用是告诉servlet容器(tomcat服务器)要部署哪些servlet，以及servlet与URL的映射关系

~~~java
//web工程的初始化类，用来代替web.xml
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer{
    /**
     * Description : 指定spring的配置类
     * @date 2022/4/12
     * @time 23:28
     * @user hcxs1986
     * @return java.lang.Class<?>[]
     **/
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    /**
     * Description : 指定springMVC的配置类
     * @date 2022/4/12
     * @time 23:29
     * @user hcxs1986
     * @return java.lang.Class<?>[]
     **/
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    /**
     * Description : 指定DispatchServlet的映射规则，即url-patten
     * @date 2022/4/12
     * @time 23:30
     * @user hcxs1986
     * @return java.lang.String[]
     **/
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    /**
     * Description : 注册过滤器
     * @date 2022/4/12
     * @time 23:38
     * @user hcxs1986
     * @return javax.servlet.Filter[]
     **/
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
        characterEncodingFilter.setEncoding("UTF-8");
        characterEncodingFilter.setForceResponseEncoding(true);
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{characterEncodingFilter,hiddenHttpMethodFilter};
    }
}
~~~

2.创建SpringConfig配置类，代替spring的配置文件

~~~java
@Configuration
public class SpringConfig {
    //ssm整合之后，spring的配置信息写在此类中
}
~~~

3.创建WebConfig配置类，代替SpringMVC.xml

- **注：①使用@Bean修饰的方法，其返回值将作为IOC容器中的一个bean实例**

  ​		**②使用@Bean修饰的方法中形参列表如果有参数，则会在IOC容器中寻找同类型的bean，采用自动装配为其赋值**

~~~java
//用于代替springMVC的配置文件
//1.开启组件扫描 2.视图解析器 3.视图控制器 4.静态资源处理的default-Servlet 5.mvc注解驱动 6.文件上传解析器 7.过滤器 8.异常处理器
@Configuration //将当前类标识为一个配置类
@ComponentScan("mvc.controller") //1.开启组件扫描
@EnableWebMvc //5.mvc注解驱动
public class WebConfig implements WebMvcConfigurer {
    /**
     * Description : 配置静态资源处理的default-Servlet-handler
     * @date 2022/4/13
     * @param configurer
     * @return void
     **/
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();//此方法可以开启静态资源处理的默认处理器default-Servlet-handler
    }

    /**
     * Description : 配置拦截器
     * @date 2022/4/13
     * @param registry
     * @return void
     **/
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        TestIntercept testIntercept = new TestIntercept();
        registry.addInterceptor(testIntercept).addPathPatterns("/**");
    }

    /**
     * Description : 配置视图控制器
     * @date 2022/4/13
     * @param registry
     * @return void
     **/
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/test").setViewName("test");
    }

    /**
     * Description : 配置文件上传解析器
     * @date 2022/4/13
     * @return org.springframework.web.multipart.MultipartResolver
     **/
    @Bean
    public MultipartResolver multipartResolver(){
        CommonsMultipartResolver commonsMultipartResolver = new CommonsMultipartResolver();
        return commonsMultipartResolver;
    }

    /**
     * Description : 配置异常处理器
     * @date 2022/4/13
     * @param resolvers
     * @return void
     **/
    @Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
        Properties properties = new Properties();
        properties.setProperty("java.lang.ArithmeticException","error");
        exceptionResolver.setExceptionMappings(properties);
        exceptionResolver.setExceptionAttribute("ex");
        resolvers.add(exceptionResolver);
    }

    //配置视图解析器
    //配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
        // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过WebApplicationContext 的方法获得
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(
                webApplicationContext.getServletContext());
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setCharacterEncoding("UTF-8");
        templateResolver.setTemplateMode(TemplateMode.HTML);
        return templateResolver;
    }

    //生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }

    //生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }
}
~~~

---

## 总结

### 常用组件

- DispatcherServlet：**前端控制器**，由框架提供

作用：统一处理请求和响应，整个流程控制的中心，由它调用其它组件处理用户的请求

- HandlerMapping：**处理器映射器**，由框架提供

作用：根据请求的url、method等信息查找Handler，即控制器方法

- Handler：**处理器**，需要自己提供

  处理器也就是控制器，即需要根据相应业务生成的xxxControler类(控制层组件)

作用：在DispatcherServlet的控制下Handler对具体的用户请求进行处理

- HandlerAdapter：**处理器适配器**，由框架提供

作用：通过HandlerAdapter对处理器（控制器方法）进行执行

- ViewResolver：**视图解析器**，由框架提供

作用：进行视图解析，得到相应的视图，例如：ThymeleafView、InternalResourceView、RedirectView

- View：**视图**

作用：将模型数据通过页面展示给用户

### 前端控制器的初始化过程

- DispatcherServlet 实际上依然是一个 Servlet程序，因此也会遵循Servlet程序的生命周期规则
- 调用的过程虽然是在不同的类中进行调用，但真正通过继承或实现放在同一个类，实际上是在同一个类中查看方法的调用
- 创建的ioc容器如果是java工程则创建applicationContext，如果是web工程则创建webapplicationContext

![初始化过程](https://s1.ax1x.com/2022/04/14/LQXZ7T.png)

①初始化WebApplicationContext

所在类：org.springframework.web.servlet.FrameworkServlet

- **WebApplicationContext是专门为web应用准备的,**

  **它允许从相对于web根目录的路径中装载配置文件完成初始化工作，**

  从WebApplicationContext中可以获得ServletContext的引用，

  整个Web应用上下文对象将作为属性放置在ServletContext中。

```java
protected WebApplicationContext initWebApplicationContext() {
    WebApplicationContext rootContext =
        WebApplicationContextUtils.getWebApplicationContext(getServletContext());
    WebApplicationContext wac = null;

    if (this.webApplicationContext != null) {
        // A context instance was injected at construction time -> use it
        wac = this.webApplicationContext;
        if (wac instanceof ConfigurableWebApplicationContext) {
            ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) wac;
            if (!cwac.isActive()) {
                // The context has not yet been refreshed -> provide services such as
                // setting the parent context, setting the application context id, etc
                if (cwac.getParent() == null) {
                    // The context instance was injected without an explicit parent -> set
                    // the root application context (if any; may be null) as the parent
                    cwac.setParent(rootContext);
                }
                configureAndRefreshWebApplicationContext(cwac);
            }
        }
    }
    if (wac == null) {
        // No context instance was injected at construction time -> see if one
        // has been registered in the servlet context. If one exists, it is assumed
        // that the parent context (if any) has already been set and that the
        // user has performed any initialization such as setting the context id
        //寻找WebApplicationContext是否存在
        wac = findWebApplicationContext();
    }
    if (wac == null) {
        // No context instance is defined for this servlet -> create a local one
        // 创建WebApplicationContext
        wac = createWebApplicationContext(rootContext);
    }

    if (!this.refreshEventReceived) {
        // Either the context is not a ConfigurableApplicationContext with refresh
        // support or the context injected at construction time had already been
        // refreshed -> trigger initial onRefresh manually here.
        synchronized (this.onRefreshMonitor) {
            // 刷新WebApplicationContext
            onRefresh(wac);
        }
    }

    if (this.publishContext) {
        // Publish the context as a servlet context attribute.
        // 将IOC容器在应用域共享
        String attrName = getServletContextAttributeName();
        getServletContext().setAttribute(attrName, wac);
    }

    return wac;
}
```

②创建WebApplicationContext

所在类：org.springframework.web.servlet.FrameworkServlet

- 关于整合的个人理解
  - 各管各的，所以需要整合。共用一个，不需要整合。
  - 整合=需要进行整合，不整合=不需要进行整合
  - 整合之后各管各的，那么需要创建两个IOC容器，springMVC的IOC是子容器，spring的IOC是父容器

```java
protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
    Class<?> contextClass = getContextClass();
    if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
        throw new ApplicationContextException(
            "Fatal initialization error in servlet with name '" + getServletName() +
            "': custom WebApplicationContext class [" + contextClass.getName() +
            "] is not of type ConfigurableWebApplicationContext");
    }
    //在ApplicationContext接口中有一个子接口ConfigurableWebApplicationContext
    //在ApplicationContext持有一个ConfigurableWebApplicationContext的ioc容器
    // 通过反射创建 IOC 容器对象
    ConfigurableWebApplicationContext wac =
        (ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);

    wac.setEnvironment(getEnvironment());
    // 设置父容器
    wac.setParent(parent);
    String configLocation = getContextConfigLocation();
    if (configLocation != null) {
        wac.setConfigLocation(configLocation);
    }
    configureAndRefreshWebApplicationContext(wac);

    return wac;
}
```

③DispatcherServlet初始化策略

FrameworkServlet创建WebApplicationContext后，刷新容器，

调用onRefresh(wac)，此方法在DispatcherServlet中进行了重写，

调用了initStrategies(context)方法，初始化策略，即初始化DispatcherServlet的各个组件

所在类：org.springframework.web.servlet.DispatcherServlet

```java
protected void initStrategies(ApplicationContext context) {
   initMultipartResolver(context);
   initLocaleResolver(context);
   initThemeResolver(context);
   initHandlerMappings(context);
   initHandlerAdapters(context);
   initHandlerExceptionResolvers(context);
   initRequestToViewNameTranslator(context);
   initViewResolvers(context);
   initFlashMapManager(context);
}
```

### 前端控制器调用组件处理请求

①processRequest()

FrameworkServlet重写HttpServlet中的service()和doXxx()，这些方法中调用了processRequest(request, response)

所在类：org.springframework.web.servlet.FrameworkServlet

```java
protected final void processRequest(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {

    long startTime = System.currentTimeMillis();
    Throwable failureCause = null;

    LocaleContext previousLocaleContext = LocaleContextHolder.getLocaleContext();
    LocaleContext localeContext = buildLocaleContext(request);

    RequestAttributes previousAttributes = RequestContextHolder.getRequestAttributes();
    ServletRequestAttributes requestAttributes = buildRequestAttributes(request, response, previousAttributes);

    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
    asyncManager.registerCallableInterceptor(FrameworkServlet.class.getName(), new RequestBindingInterceptor());

    initContextHolders(request, localeContext, requestAttributes);

    try {
		// 执行服务，doService()是一个抽象方法，在DispatcherServlet中进行了重写
        doService(request, response);
    }
    catch (ServletException | IOException ex) {
        failureCause = ex;
        throw ex;
    }
    catch (Throwable ex) {
        failureCause = ex;
        throw new NestedServletException("Request processing failed", ex);
    }

    finally {
        resetContextHolders(request, previousLocaleContext, previousAttributes);
        if (requestAttributes != null) {
            requestAttributes.requestCompleted();
        }
        logResult(request, response, failureCause, asyncManager);
        publishRequestHandledEvent(request, response, startTime, failureCause);
    }
}
```

②doService()

所在类：org.springframework.web.servlet.DispatcherServlet

```java
@Override
protected void doService(HttpServletRequest request, HttpServletResponse response) throws Exception {
    logRequest(request);

    // Keep a snapshot of the request attributes in case of an include,
    // to be able to restore the original attributes after the include.
    Map<String, Object> attributesSnapshot = null;
    if (WebUtils.isIncludeRequest(request)) {
        attributesSnapshot = new HashMap<>();
        Enumeration<?> attrNames = request.getAttributeNames();
        while (attrNames.hasMoreElements()) {
            String attrName = (String) attrNames.nextElement();
            if (this.cleanupAfterInclude || attrName.startsWith(DEFAULT_STRATEGIES_PREFIX)) {
                attributesSnapshot.put(attrName, request.getAttribute(attrName));
            }
        }
    }

    // Make framework objects available to handlers and view objects.
    request.setAttribute(WEB_APPLICATION_CONTEXT_ATTRIBUTE, getWebApplicationContext());
    request.setAttribute(LOCALE_RESOLVER_ATTRIBUTE, this.localeResolver);
    request.setAttribute(THEME_RESOLVER_ATTRIBUTE, this.themeResolver);
    request.setAttribute(THEME_SOURCE_ATTRIBUTE, getThemeSource());

    if (this.flashMapManager != null) {
        FlashMap inputFlashMap = this.flashMapManager.retrieveAndUpdate(request, response);
        if (inputFlashMap != null) {
            request.setAttribute(INPUT_FLASH_MAP_ATTRIBUTE, Collections.unmodifiableMap(inputFlashMap));
        }
        request.setAttribute(OUTPUT_FLASH_MAP_ATTRIBUTE, new FlashMap());
        request.setAttribute(FLASH_MAP_MANAGER_ATTRIBUTE, this.flashMapManager);
    }

    RequestPath requestPath = null;
    if (this.parseRequestPath && !ServletRequestPathUtils.hasParsedRequestPath(request)) {
        requestPath = ServletRequestPathUtils.parseAndCache(request);
    }

    try {
        // 处理请求和响应
        doDispatch(request, response);
    }
    finally {
        if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
            // Restore the original attribute snapshot, in case of an include.
            if (attributesSnapshot != null) {
                restoreAttributesAfterInclude(request, attributesSnapshot);
            }
        }
        if (requestPath != null) {
            ServletRequestPathUtils.clearParsedRequestPath(request);
        }
    }
}
```

③doDispatch()

所在类：org.springframework.web.servlet.DispatcherServlet

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    boolean multipartRequestParsed = false;

    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

    try {
        ModelAndView mv = null;
        Exception dispatchException = null;

        try {
            processedRequest = checkMultipart(request);
            multipartRequestParsed = (processedRequest != request);

            // Determine handler for the current request.
            /*
            	mappedHandler：调用链
                包含handler、interceptorList、interceptorIndex
            	handler：浏览器发送的请求所匹配的控制器方法
            	interceptorList：处理控制器方法的所有拦截器集合
            	interceptorIndex：拦截器索引，控制拦截器afterCompletion()的执行
            */
            mappedHandler = getHandler(processedRequest);
            if (mappedHandler == null) {
                noHandlerFound(processedRequest, response);
                return;
            }

            // Determine handler adapter for the current request.
           	// 通过控制器方法创建相应的处理器适配器，调用所对应的控制器方法
            HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

            // Process last-modified header, if supported by the handler.
            String method = request.getMethod();
            boolean isGet = "GET".equals(method);
            if (isGet || "HEAD".equals(method)) {
                long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
                    return;
                }
            }
			
            // 调用拦截器的preHandle()
            if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                return;
            }

            // Actually invoke the handler.
            // 由处理器适配器调用具体的控制器方法，最终获得ModelAndView对象
            mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

            if (asyncManager.isConcurrentHandlingStarted()) {
                return;
            }

            applyDefaultViewName(processedRequest, mv);
            // 调用拦截器的postHandle()
            mappedHandler.applyPostHandle(processedRequest, response, mv);
        }
        catch (Exception ex) {
            dispatchException = ex;
        }
        catch (Throwable err) {
            // As of 4.3, we're processing Errors thrown from handler methods as well,
            // making them available for @ExceptionHandler methods and other scenarios.
            dispatchException = new NestedServletException("Handler dispatch failed", err);
        }
        // 后续处理：处理模型数据和渲染视图
        processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
    }
    catch (Exception ex) {
        triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
    }
    catch (Throwable err) {
        triggerAfterCompletion(processedRequest, response, mappedHandler,
                               new NestedServletException("Handler processing failed", err));
    }
    finally {
        if (asyncManager.isConcurrentHandlingStarted()) {
            // Instead of postHandle and afterCompletion
            if (mappedHandler != null) {
                mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
            }
        }
        else {
            // Clean up any resources used by a multipart request.
            if (multipartRequestParsed) {
                cleanupMultipart(processedRequest);
            }
        }
    }
}
```

④processDispatchResult()

```java
private void processDispatchResult(HttpServletRequest request, HttpServletResponse response,
                                   @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv,
                                   @Nullable Exception exception) throws Exception {

    boolean errorView = false;

    if (exception != null) {
        if (exception instanceof ModelAndViewDefiningException) {
            logger.debug("ModelAndViewDefiningException encountered", exception);
            mv = ((ModelAndViewDefiningException) exception).getModelAndView();
        }
        else {
            Object handler = (mappedHandler != null ? mappedHandler.getHandler() : null);
            mv = processHandlerException(request, response, handler, exception);
            errorView = (mv != null);
        }
    }

    // Did the handler return a view to render?
    if (mv != null && !mv.wasCleared()) {
        // 处理模型数据和渲染视图
        render(mv, request, response);
        if (errorView) {
            WebUtils.clearErrorRequestAttributes(request);
        }
    }
    else {
        if (logger.isTraceEnabled()) {
            logger.trace("No view rendering, null ModelAndView returned.");
        }
    }

    if (WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
        // Concurrent handling started during a forward
        return;
    }

    if (mappedHandler != null) {
        // Exception (if any) is already handled..
        // 调用拦截器的afterCompletion()
        mappedHandler.triggerAfterCompletion(request, response, null);
    }
}
```

### SpringMVC的执行流程

1.用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获。

2.DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI），判断请求URI对应的映射：

第一种情况：不存在

①再判断是否配置了mvc:default-servlet-handler

②如果没配置，则控制台报映射查找不到，客户端展示404错误

③如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404错误

第二种情况：存在则执行下面的流程

3.根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及Handler对象对应的拦截器），

​	最后以HandlerExecutionChain执行链对象的形式返回。

4.DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。

5.如果成功获得HandlerAdapter，此时将开始执行拦截器的preHandler(…)方法【正向】

6.提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)方法，处理请求。

​	在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：

① HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息

②数据转换：对请求消息进行数据转换。如String转换成Integer、Double等

③数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等

④ 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中

7.Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。

8.此时将开始执行拦截器的postHandle(...)方法【逆向】。

9.根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，

​	则执行HandlerExceptionResolver进行异常处理）选择一个适合的ViewResolver进行视图解析，根据Model和View，来渲染视图。

10.渲染视图完毕执行拦截器的afterCompletion(…)方法【逆向】。

11.将渲染结果返回给客户端。
