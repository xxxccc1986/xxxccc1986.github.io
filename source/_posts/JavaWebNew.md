---
title: ThymeLeaf、VUE、Axios
date: 2022-04-08 11:45:30
updated: 2022-04-08 11:45:30
tags:
  - ThymeLeaf
  - Vue
  - Axios
categories:
  - JavaWeb
keywords: JavaWeb
cover: https://w.wallhaven.cc/full/m9/wallhaven-m9y9j1.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/04/08/JavaWebNew
---



## ThymeLeaf

- 在html页面上加载java内存中的数据的过程称为渲染(render)。

概念：thymeleaf是用于实现视图渲染的技术        

实现步骤

①添加thymeleaf包

②新建ViewBaseServlet类(名字不是固定的，可以随便取得)继承HttpServlet

~~~java
public class ViewBaseServlet extends HttpServlet {

    private TemplateEngine templateEngine;

    @Override
    public void init() throws ServletException {

        // 1.获取ServletContext对象
        ServletContext servletContext = this.getServletContext();

        // 2.创建Thymeleaf解析器对象
        ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(servletContext);

        // 3.给解析器对象设置参数
        // ①HTML是默认模式，明确设置是为了代码更容易理解
        templateResolver.setTemplateMode(TemplateMode.HTML);

        // ②设置前缀
        String viewPrefix = servletContext.getInitParameter("view-prefix");

        templateResolver.setPrefix(viewPrefix);

        // ③设置后缀
        String viewSuffix = servletContext.getInitParameter("view-suffix");

        templateResolver.setSuffix(viewSuffix);

        // ④设置缓存过期时间（毫秒）
        templateResolver.setCacheTTLMs(60000L);

        // ⑤设置是否缓存
        templateResolver.setCacheable(true);

        // ⑥设置服务器端编码方式
        templateResolver.setCharacterEncoding("utf-8");

        // 4.创建模板引擎对象
        templateEngine = new TemplateEngine();

        // 5.给模板引擎对象设置模板解析器
        templateEngine.setTemplateResolver(templateResolver);

    }

protected void processTemplate(String templateName, HttpServletRequest req, HttpServletResponse resp) throws IOException {
        // 1.设置响应体内容类型和字符集
        resp.setContentType("text/html;charset=UTF-8");

        // 2.创建WebContext对象
        WebContext webContext = new WebContext(req, resp, getServletContext());

        // 3.处理模板数据
        templateEngine.process(templateName, webContext, resp.getWriter());
    }
}
~~~

③在web.xml文件中添加配置

- 上述类中的processTemplate()方法获取了前缀、后缀的值

- 配置前缀 view-prefix
- 配置后缀 view-suffix

在xml中配置上下文参数：

~~~xml
<!-- 在上下文参数中配置视图前缀和视图后缀 -->
    <context-param>
        <param-name>view-prefix</param-name>
        <param-value>/</param-value>
    </context-param>
    <context-param>
        <param-name>view-suffix</param-name>
        <param-value>.html</param-value>
    </context-param>
~~~

④将实际上执行方法的servlet继承ViewBaseServlet

~~~java
public class FruitServlet extends ViewBaseServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //调用FruitDao实现类对象 查询业务
        FruitDao fruitDao = new FruitDaoImpl();
        List<Fruit> fruitList = fruitDao.getFruitList();

        //把查询的对象集合保存到session域中
        HttpSession session = req.getSession();
        session.setAttribute("fruitList",fruitList);

        //此处的视图名称是index，  thymeleaf会将这个逻辑视图名称对应到 物理视图名称上去
        //逻辑视图 index
        //物理视图名称 view-prefix + 逻辑视图名称 + view-suffix
        //也就是 /index.html
        //相当于把原来的重定向连接进行了拼接，只不过重定向由resp做的，在这里是由thymeLeaf实现
        super.processTemplate("index",req,resp);
    }
}
~~~

---

## Vue

{%  raw  %}

{{}} - 相当于innerText

{%  endraw %}

v-bind:attr 绑定属性值。例如，v-bind:value - 绑定value值，可以简写：    :value

v-model 双向绑定。例如，v-model:value   , 简写  v-model

v-if , v-else , v-show

v-if和v-else之间不能有其他的节点

v-show是通过样式表display来控制节点是否显示

v-for 迭代，v-for={fruit in fruitList}

v-on 绑定事件，例如，v-on:click="test",可以简写了@click=“test”

其他：
- trim:去除首尾空格 , split() , join()
- watch表示侦听属性
- 生命周期

### Axios

axios是Ajax的一个框架，简化Ajax的操作

| 属性名     | 作用                                             |
| :--------- | :----------------------------------------------- |
| config     | 调用axios(config对象)方法时传入的JSON对象        |
| data       | 服务器端返回的响应体数据                         |
| headers    | 响应消息头                                       |
| request    | 原生JavaScript执行Ajax操作时使用的XMLHttpRequest |
| status     | 响应状态码                                       |
| statusText | 响应状态码的说明文本                             |

axios实现AJax的操作：

在引入axios的js文件后

- 基本格式： axios().then().catch()
  
  ​	①发送普通参数axios内为method、url、params
  
  ​	②发送json格式数据axios内method、url、data
  
- 在axios的异步请求中

  ①当method=post的时候，可以选择使用data或者param

  - 使用param的情况

    若使用**Map**接收参数，必须使用 **@RequestParam 修饰**。

    但是如果想传**list类型的数据**，需要使用单独的方法处理。

  - 使用data的情况

    必须使用**一个实体类**接收参数，而且需要添加注解 @RequestBody 进行修饰。

  ②当method=get的时候，只能使用param，原因是get中没有data方式

1.客户端向服务器端异步发送普通参数值

~~~html
<!--示例 -->
      axios({
        method:"POST",
        url:"....",
        params:{
            uname:"lina",
            pwd:"ok"
        }
      })
      .then(function(value){})         <!--成功响应时执行的回调        value.data可以获取到服务器响应内容 -->
      .catch(function(reason){});       <!--有异常时执行的回调         reason.response.data可以获取到响应的内容 -->
                                                     <!--reason.message / reason.stack 可以查看错误的信息 -->

~~~

![axios程序接收到的响应对象结构](https://s1.ax1x.com/2022/04/08/LpghaF.png)

2.客户端向服务器发送JSON格式的数据

前端vue+axios代码：

~~~javascript
"methods":{
    "requestBodyJSON":function () {
        axios({
            "method":"post",
            "url":"/demo/AjaxServlet?method=requestBodyJSON",
            "data":{
                "stuId": 55,
                "stuName": "tom",
                "subjectList": [
                    {
                        "subjectName": "java",
                        "subjectScore": 50.55
                    },
                    {
                        "subjectName": "php",
                        "subjectScore": 30.26
                    }
                ],
                "teacherMap": {
                    "one": {
                        "teacherName":"tom",
                        "tearcherAge":23
                    },
                    "two": {
                        "teacherName":"jerry",
                        "tearcherAge":31
                    },
                },            
            }
        }).then(function (response) {
            console.log(response);
        }).catch(function (error) {
            console.log(error);
        });
    }
}
~~~

后端服务器代码：


~~~java
protected void requestBodyJSON(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    // 1.由于请求体数据有可能很大，所以Servlet标准在设计API的时候要求我们通过输入流来读取
    BufferedReader reader = request.getReader();

    // 2.创建StringBuilder对象来累加存储从请求体中读取到的每一行
    StringBuilder builder = new StringBuilder();

    // 3.声明临时变量
    String bufferStr = null;

    // 4.循环读取
    while((bufferStr = reader.readLine()) != null) {
        builder.append(bufferStr);
    }

    // 5.关闭流
    reader.close();

    // 6.累加的结果就是整个请求体
    String requestBody = builder.toString();

    // 7.创建Gson对象用于解析JSON字符串
    Gson gson = new Gson();

    // 8.将JSON字符串还原为Java对象
    Student student = gson.fromJson(requestBody, Student.class);
    System.out.println("student = " + student);

    System.out.println("requestBody = " + requestBody);

    response.setContentType("text/html;charset=UTF-8");
    response.getWriter().write("服务器端返回普通文本字符串作为响应");
}

~~~
