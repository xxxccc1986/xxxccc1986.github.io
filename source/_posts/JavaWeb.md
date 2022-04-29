---
title: JavaWeb
date: 2022-04-2 11:45:30
updated: 2022-04-2 11:45:30
tags:
  - JavaWeb
categories:
  - JavaWeb
keywords: JavaWeb
cover: https://w.wallhaven.cc/full/g7/wallhaven-g7k52e.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/04/02/JavaWeb
---

## 初识JavaWeb

javaWeb是指所有通过Java语言编写可以通过浏览器访问的程序的总称。

请求(request)：指客户端给服务器发送数据

响应(response)：指服务端给客户端回传数据

根据web资源的不同分为静态(html、css、js)和动态资源(jsp、sevlet程序)

**Tomcat是由Apache提供的web服务器，提供对jsp和servlet的支持，是一种轻量级的javaweb容器(服务器)**

网页由三部分组成：内容(html)、表现(css)、行为(javascript)

行为指的是是页面中元素与输入设备交互的相应。

## HTML

HTML：超文本标记语言(Hyper Text Markup Language)

HTML通过标签标记显示网页的各个部分。通过在文本中添加标签，以告诉浏览器如何显示文本内容(如文字的处理、照片的位置布局)

### HTML的书写规范

1. HTML的标签

   格式：<标签名>封装的数据</标签名>

2. 标签名对大小写不严格区分

3. 标签的属性

   ①基本属性：bgcolor = “red”  ---可以修改简单的样式，如背景颜色

   ②事件属性：onclick = “alert( ‘测试！’ )”  --- 可以直接设置事件响应后的代码

4. 标签分为单标签和双标签

   单标签：<标签名 />   br 换行、hr 水平线

   双标签：<标签名>封装的数据</标签名>

~~~html
<html>  					 表示整个html页面的开始
    <head>   			     头信息
        <title>标题</title>	标题
    </head> 
    
    <body>					  body是页面的主题内容
        页面主体内容
    </body>
       
</html> 					  表示整个html页面的结束

<!DOCTYPE html> <!-- 约束，声明-->
<html lang="zh_CN"><!-- html标签表示html的开始，lang="zh_CN"表示支持中文  html通常分为head 和 body两部分-->
<head>  <!-- 表示头部信息，包含三部分内容，title标签，css样式，js代码 -->
    <meta charset="UTF-8"> <!-- 当前页面使用的字符集utf-8-->
    <title>标题</title> <!-- 表示标题 -->
</head>
<!-- bgcolor表示背景颜色属性
     oncilck表示单击（点击）事件
     alert()是javascript语言提供的一个警告框
     它可以接收任意参数，参数即警告框的函数信息
 -->
<body > <!-- 此标签是整个html显示的主体内容-->
    这是测试页面 <button onclick="alert('测试')">按钮</button>
</body>
</html>
~~~

​	注意事项：

​	①标签不能交叉嵌套

​	②标签必须正确关闭(闭合)

​	③属性必须有值，属性值必须加引号

​	④注释不能嵌套

#### 常用标签

1. front是字体标签

   常用于修改文本的字体(face)，颜色(color)，大小(size)，其中大小的属性值只有1-6

2. 特殊字符 

   ​	![常用的字符实体](https://s1.ax1x.com/2022/04/02/qIEqFf.png)

3. 标题标签

   h1 - h6 都是标题标签，其中h1最大，h6最小

   align属性是对齐属性，分别有left(左对齐，默认)、center(居中)、right(右对齐)

4. 超链接

   超文本引用（hypertext reference）

   a标签是 超链接

   ​	href属性设置连接的地址

   ​	target属性设置针对哪个目标进行跳转 

   ​	    _self  表示当前页面，作为每次的默认值

   ​	    _blank  表示打开新页面进行跳转


 ~~~html
      <!-- 超链接 -->
      	<a href="http://year21.top/" >个人博客</a><br />
      	<a href="http://year21.top/" target="_self" >个人博客_self</a><br />
      	<a href="http://year21.top/" target="_blank" >个人博客_blank</a><br />
 ~~~

5. 列表标签

   无序列表、有序列表、定义列表(使用较少)

   ul是无序列表，type属性可以修改列表项前面的符号，li是列表项

   ol是有序列表，type属性可以修改列表项前面的符号，li是列表项

   **在某些情况下，type的修改是无效的，原因是部分浏览器的兼容性不好 **

6. img标签

​	   img标签是图像标签，可用于在html页面上显示图片

​	   src属性可以用于设置图片的路径

​	   weight属性设置图片的宽度

​       hright属性设置图片的高度

​       border属性设置图片的边框大小

​       alt属性设置当前指定路径下不存在图片时，用来代替显示内容的文本

​		web中的路径分为相对路径和绝对路径

​		①相对路径：

​			**. **   			表示当前文件的目录

​			**..**				表示当前文件的上一级目录

​            文件名		表示当前目录下的文件，相当于  ./ 文件名，但./可以省略

​		②绝对路径：

​			格式：http://ip:port/工程名/资源路径

7. 表格标签

   table标签是表格标签

   - border属性设置表格边框
   - width设置表格宽度
   - height设置表格高度
   - align设置表格的相对页面的位置
   - cellspacing设置单元格间距

   tr是行标签

   - th是表头标签
   - td是单元格标签
   - align设置单元格文本对齐方式
     - b是文本加粗标签

   8.跨行跨列标签

   colspan 属性设置跨列

   rowspan 属性设置跨行

   设置跨行跨列则同时使用

   - 注意的是要删掉被跨的列与行

   9.iframe标签(内嵌窗口)

   ​	用于在html页面上打开一个小窗口，去加载单独的页面

   iframe和a标签组合使用：

   - ①在iframe标签中使用name属性定义一个名称
   - ②在a标签的target属性上设置iframe的name的属性

   10.表单标签

   form是表单标签，用于在html页面中收集用户信息的元素集合，然后将此集合内的信息发送到服务器

   **action属性设置提交的服务器地址**，**method属性设置提交的方式GET(默认值)或POST**

   

   表单提交时数据没有发送给服务器的三种情况：

   ①表单项没有name属性值

   ②单选、复选(下拉列表中的option标签)都需要添加value属性，以便发送给服务器

   ③表单项不再提交的form标签中。

   

   提交方式**GET**的特点：

   ①提交信息的url是 action属性值+ [+?+请求参数 ]，其中中括号内容可以省略

   ​	请求参数的格式是 name=value&name=value

   ②提交的信息以明文传输，存在安全隐患

   ③对数据长度有限制

   提交方式**POST**的特点：

   ①提交信息的url只有 action属性值

   ②相对于get提交方式更安全

   ③理论书没有数据长度的限制

   

   表单标签常用设置：

   - input type=“text”  ---> 文本输入框，可用value属性设置默认值

   - input type=“password”  --->密码输入框，可用value属性设置默认值

   - input type=“radio”  --->单选框，可用name属性进行分组，checked="checked"表示默认选择

   - input type="checkbox"  --->复选框，checked="checked"可以默认多个选择

   - select标签是下拉列表框，option标签是下拉列表框中的选项，selected="selected"设置默认选择

   - textarea表示多行文本输出框(起始标签和结束标签中的内容是它的默认值)

     rows属性设置显示的行数  cols属性设置显示每行的宽度

   - input type="reset"是重置按钮，通过value属性可以修改其文本内容

   - input type="submit"是提交按钮，也可通过value修改其文本值

   - input type="button"是按钮，也可通过value修改其文本值

   - input type="file"是文件上传选项，可提交文件至服务器

   - input type="hidden"是隐藏域 作用是当需要发送些信息用户不需要看见，但服务器需要的信息

   11.其他标签

   div标签默认独占一行

   **span标签 它的长度都与封装数据长度一致**

   p是段落标签 ，默认在段落的上方或下方各空出一行（在已有情况下不添加）

---

## CSS

CSS-层叠样式表单，用于增强、控制页面样式并允许样式信息与网页内容分离的标记性语言

css的注释于Java的多行注释一致

在一个选择器中定义多个声明，则要使用分号分开每个声明

### CSS与HTML的结合使用

第一种：在标签的 style 属性上设置”key:value value;”，修改标签样式

第二种：在 head 标签中，使用 style 标签来定义各种自己需要的 css 样式

第三种：把css样式单独写成css文件，再通过link标签引入复用，提高了代码的复用性，修改也更简便

~~~css
<link rel="stylesheet" type="text/css" href="test.css">
~~~

### CSS选择器

1.标签选择器

作用：可以决定那些标签被动的使用这个样式

格式：

标签名{

​		属性：值

​			}

2.id选择器

作用：可以通过id属性选择性的使用样式

格式：

​	#id 属性值{

​		属性：值

​					}

3.类型选择器

作用：通过class属性有效的选择性的去使用样式

格式：

​	.class属性值{

​				属性：值

​					}

4.组合选择器

作用：组合选择器可以让多个选择器共用一个css代码

格式：

​	选择器1，选择器2，选择器n{

​					属性：值

​								}	

### 常见样式

1.字体颜色 color:red;

2.宽度  width:19px;

3.高度  width:19px;

4.背景颜色 background-color:#0F2D4C 

5.字体样式：

color：#FF0000；字体颜色红色 

font-size：20px; 字体大小 

6.红色1像素实线边框 border：1px solid red; 

7.DIV 居中 

margin-left: auto; 

margin-right: auto; 

8.文本居中  text-align: center; 

9.超连接去下划线  text-decoration: none; 

10.表格细线 

table { 

border: 1px solid black; /*设置边框*/ 

border-collapse: collapse; /*将边框合并*/ 

} 

td,th { 

border: 1px solid black; /*设置边框*/ 

} 

11.列表去除修饰 

ul { 

list-style: none; 

​	}

---

## JavaScript

js是弱类型，类型可变，Java是强类型，在定义变量就确定了类型

### JS与HTML的结合使用

1.第一种：只需要在 head 标签中，或者在 body 标签中， 使用 script 标签 来书写 JavaScript 代码

2.第二种：使用 script 标签引入 单独的 JavaScript 代码文件

- 注：src属性专门用于引入js文件路径(可以是相对路径或绝对路径)
- script标签可以用于定义js代码，也能引入js文件，但只能二选一执行

### 变量

数值类型(number)、字符串类型(string)、对象类型(object)

布尔类型(boolean)、函数类型(function)

JavaScript的特殊值：

①undefined  未定义，所有js变量未赋予初始值之前，其默认值都是undefined			

②null  空值

③NAN 全称：Not a number 非数字，非数值

变量定义的格式：var 变量名 ； var 变量名 = 值；

**typeof ()是js语言提供用于查询变量数据类型的函数**

#### 运算

1.关系(比较)运算：

等于(==)：简单的做字面值的比较

全等于(===)：除了做字面值比较之外，还会比较两个变量的数据类型

2.逻辑运算：且运算(&&)、或运算(||)、取反运算(！)

在js语言中，所有的变量都可以做为一个 boolean 类型的变量去使用。 

**0 、null、 undefined、””(空串) 做判断都认为是 false**

 && 且运算有两种情况： 

第一种：当表达式全为真的时候。返回最后一个表达式的值。 

第二种：当表达式中，有一个为假的时候。返回第一个为假的表达式的值 



|| 或运算 

第一种情况：当表达式全为假时，返回最后一个表达式的值 

第二种情况：只要有一个表达式为真。就会把回第一个为真的表达式的值 



并且 && 与运算 和 ||或运算 有短路。 

短路就是说，当这个&&或||运算有结果了之后 。后面的表达式不再执行

---

### 数组

格式：

var 数组名 = []; // 空数组 

var 数组名 = [1 , ’abc’ , true]; // 定义数组同时赋值元素

**js语言中的数组，只有通过数组下标赋值，那么最大的下标值，才能自动的给数组做扩容操作，即数组长度由最大下标值决定**

### 函数

定义方式：

①方式一：使用function关键字

​	function 函数名(形参列表){  函数体  }

- 函数必须调用才会执行

~~~java
<script type="text/javascript">
        // 定义一个无参函数
        function fun(){
            alert("无参函数fun()被调用了");
        }
        // 函数调用===才会执行
        fun();

        function fun2(a ,b) {
            alert("有参函数fun2()被调用了 a=>" + a + ",b=>"+b);
        }

        fun2(10,"test");

        // 定义带有返回值的函数
        function sum(num1,num2) {
            var result = num1 + num2;
            return result;
        }
        alert( sum(100,50) );
    </script>
~~~

②方式二： 

 var 函数名 = function 函数名(形参列表){  函数体  }

- **Java 中函数允许重载。但是在 JS 中函数的重载会直接覆盖掉上一次的定义**

~~~javascript
<script type="text/javascript">
        //无参函数
        var test1 = function (){
            alert("无参函数");
        }
        // test1();

        //有参函数
        var test2 = function (a,b){
            alert("有参函数被调用了,a的值是" + a + "b的值是" + b)
        }
        // test2(10,12);

        //带返回值有参函数
        var test3 = function (a,b){
            return a + b;
        }
        alert(test3(10,12));
    </script>
~~~

#### 函数的arguments隐形参数

只能在function函数内，，与java中的可变形参类型，其本身也是一个数组

~~~javascript
<script type="text/javascript">
        function fun(){
            // alert(arguments.length);
            for (var i = 0; i < arguments.length; i++) {
                    alert(arguments[i])
            }
        }
        // fun(1,"abc");

        //编写一个函数，用于计算所有参数相加的和并返回
        function sum(num1,num2){//此处num1，num2不会影响隐形参数
            var sum = 0;
            for (var i = 0; i < arguments.length ; i++) {
                if (typeof(arguments[i]) == "number")
                sum += arguments[i]
            }
            return sum;
        }
        alert(sum(1,2,"abc",5));
    </script>
~~~

### js自定义对象

①Object形式的自定义对象

对象的定义：

​		var 变量名 = new Object(); //对象实例

​		变量名.属性名 = 值  	//定义一个属性

​		变量名.函数名 = function(){}  //定义一个函数

对象的访问：

​		变量名.属性名 / 函数名();

②{}形式自定义对象

对象的定义：

​	var 变量名 = {} //空对象

​	var 变量名 = {

​		属性名：值,	//定义一个属性

​		属性名：值,	//定义一个属性

​		函数名：function(){}   //定义一个函数

​							}

​	对象的访问：

​		变量名.属性名 / 函数名();

### js中的事件

常用事件：

- onload 加载完成事件： 页面加载完成之后，常用于做页面 js 代码初始化操作 
- onclick 单击事件： 常用于按钮的点击响应操作。 
- onblur 失去焦点事件： 常用用于输入框失去焦点后验证其输入内容是否合法。 
- onchange 内容发生改变事件： 常用于下拉列表和输入框内容发生改变后操作 
- onsubmit 表单提交事件： 常用于表单提交前，验证所有表单项是否合法。

#### 事件的注册(绑定)

概念：可以理解为告诉服务器在事件响应后需要执行哪些代码

**事件的注册分为静态注册和动态注册**

静态注册：通过html标签的事件属性赋予事件响应后的代码

动态注册：先通过js代码得到标签的dom对象，再通过dom对象.事件名 = function(){} 这种形式赋予响应后的代码。

动态注册的基本代码：1.获取标签对象 2.标签对象.事件名 = function(){};

关于onsubmit提交事件的举例，其他事件同理

~~~javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>表单提交事件</title>
    <script type="text/javascript">
        //静态注册
        function form (){
            //要验证所有表单项是否合法，如有一个不合法则要阻止表单的提交
            alert("静态注册表单发现不合法");
            return false;
        }

        //动态注册
		//window.onload是固定写法，onload是文档加载完自动调用的方法，获取元素都需要在文档加载完之后执行
        window.onload = function () {
            //document是js语言提供的一个对象(文档)
        	//getElementById是通过id获取某一个具体的标签对象
            //1.获取标签对象
            var form = document.getElementById("1001");
            //2.通过标签对象.事件名 = function(){}
            form.onsubmit = function () {
                alert("动态注册表单发现不合法");
                return true;//只有此处为true，发现不合法一样提交，除非改成false
            }
        }
    </script>
</head>
<body>
    <!--return false可以阻止表单的提交-->
    <form action="http://year21.top" method="get" onsubmit="return form()">
        <input type="submit" value="静态注册"/>
    </form>

    <form action="http://year21.top" id="1001">
        <input type="submit" value="动态注册"/>
    </form>
</body>
</html>
~~~

### DOM模型

DOM：文档对象模型(Document Object Model)

可以理解为把文档中的标签和文本、属性转换为对象来管理，只要属于这三种之一都是节点

![DOM模型](https://s1.ax1x.com/2022/04/02/qIZzaq.png)

**Document 对象的理解： **

①Document 它管理了所有的 HTML 文档内容。 

②document 它是一种树结构的文档。有层级关系。 

③它让我们把所有的标签 都 对象化 

④我们可以通过 document 访问所有的标签对象

#### Document对象的方法

①document.getElementById(elementId) 通过标签的 id 属性查找标签 dom 对象，elementId 是标签的 id 属性值 ，返回单个对象

②document.getElementsByName(elementName) 通过标签的 name 属性查找标签 dom 对象，elementName 标签的 name 属性值，返回一个集合 

③document.getElementsByTagName(tagname) 通过标签名查找标签 dom 对象。tagname 是标签名 

④document.createElement( tagName) 方法，通过给定的标签名，创建一个标签对象。tagName 是要创建的标签名

- 优先顺序 ①-②-③-④

#### 节点的属性和方法

**节点可以理解为标签对象，但除了标签，文本和注释也可以是节点，通常情况下把这些不能操作的忽略了而已**

**方法**： 

通过具体的元素节点调用 getElementsByTagName() 方法，获取当前节点的指定标签名孩子节点 

appendChild( oChildNode ) 方法，可以添加一个子节点，oChildNode 是要添加的孩子节点 

**属性**： 

childNodes 属性，获取当前节点的所有子节点 

firstChild 属性，获取当前节点的第一个子节点 

lastChild 属性，获取当前节点的最后一个子节点 

parentNode 属性，获取当前节点的父节点 

nextSibling 属性，获取当前节点的下一个节点 

previousSibling 属性，获取当前节点的上一个节点 

className 用于获取或设置标签的 class 属性值 

innerHTML 属性，表示获取/设置起始标签和结束标签中的内容 

innerText 属性，表示获取/设置起始标签和结束标签中的文本

---

## 正则表达式

![部分表达式](https://s1.ax1x.com/2022/04/02/qIeHT1.png)

![部分表达式](https://s1.ax1x.com/2022/04/02/qIeLY6.png)

部分使用表达式代码举例

~~~javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script type="text/javascript">
        // 表示要求字符串中，是否包含字母e
        // var patt = new RegExp("e");
        // var patt = /e/; // 也是正则表达式对象
        
        // 表示要求字符串中，是否包含字母a或b或c
        // var patt = /[abc]/;
        
        // 表示要求字符串，是否包含小写字母
        // var patt = /[a-z]/;
        
        // 表示要求字符串，是否包含任意大写字母
        // var patt = /[A-Z]/;
        
        // 表示要求字符串，是否包含任意数字
        // var patt = /[0-9]/;
        
        // 表示要求字符串，是否包含字母，数字，下划线
        // var patt = /\w/;
        
        // 表示要求 字符串中是否包含至少一个a
        // var patt = /a+/;
        
        // 表示要求 字符串中是否 *包含* 零个 或 多个a
        // var patt = /a*/;
        
        // 表示要求 字符串是否包含一个或零个a
        // var patt = /a?/;
        
        // 表示要求 字符串是否包含连续三个a
        // var patt = /a{3}/;
        
        // 表示要求 字符串是否包 至少3个连续的a，最多5个连续的a
        // var patt = /a{3,5}/;
        
        // 表示要求 字符串是否包 至少3个连续的a，
        // var patt = /a{3,}/;
        
        // 表示要求 字符串必须以a结尾
        // var patt = /a$/;
        
        // 表示要求 字符串必须以a打头
        // var patt = /^a/;

        // 要求字符串中是否*包含* 至少3个连续的a 
        //在某种以上此包含和至少同个意思
        // var patt = /a{3,5}/;
        
        // 要求字符串，从头到尾都必须完全匹配
        // var patt = /^a{3,5}$/;

        var patt = /^\w{5,12}$/;

        var str = "wzg168···";

        alert( patt.test(str) );

    </script>
</head>
<body>
</body>
</html>
~~~

---

## JQuery

Jquery是辅助js开发的js类库

- 使用JQuery必须引进JQuery库
- JQuery中的$是一个函数

### JQuery核心函数

$ 是 jQuery 的核心函数，$()就是调用$这个函数 

①传入参数为 [ 函数 ] 时： 表示页面加载完成之后。相当于 window.onload = function(){} 

②传入参数为 [ HTML 字符串 ] 时： 会对我们创建这个 html 标签对象 

③传入参数为 [ 选择器字符串 ] 时： 

$(“#id 属性值”); id 选择器，根据 id 查询标签对象 

$(“标签名”); 标签名选择器，根据指定的标签名查询标签对象 

$(“.class 属性值”); 类型选择器，可以根据 class 属性查询标签对象 

④传入参数为 [ DOM 对象 ] 时： 会把这个 dom 对象转换为 jQuery **对象**

- JQuery对象的本质：**jQuery 对象是 dom 对象的数组 + jQuery 提供的一系列功能函数**

- jQuery 对象不能使用 DOM 对象的属性和方法 

- DOM 对象也不能使用 jQuery对象的属性和方法



![Dom对象和JQuery对象互转](https://s1.ax1x.com/2022/04/02/qIm90A.png)

### JQuery选择器

#### 基本选择器

①\#ID 选择器：根据 id 查找标签对象 

②.class 选择器：根据 class 查找标签对象

③element 选择器：根据标签名查找标签对象 

④*选择器：表示任意的，所有的元素 

⑤selector1，selector2 组合选择器：合并选择器 1，选择器 2 的结果并返

- 补充⑤中的特殊情况：**p.myClass** 表示标签名必须是 p 标签，而且 class 类型还要是 myClass

#### 层级选择器

①ancestor descendant 后代选择器 ：在给定的祖先元素下匹配所有的后代元素 

②parent > child 子元素选择器：在给定的父元素下匹配所有的子元素 

③prev + next 相邻元素选择器：匹配所有紧接在 prev 元素后的 next 元素 

④prev ~ sibings 之后的兄弟元素选择器：匹配 prev 元素之后的所有 siblings 元素

- ③ ④针对的都是同级的

#### 过滤选择器

**:first** 获取第一个元素 

**:last **获取最后个元素 

**:not**(selector) 去除所有与给定选择器匹配的元素 

**:even** 匹配所有索引值为偶数的元素，从 0 开始计数 

**:odd** 匹配所有索引值为奇数的元素，从 0 开始计数 

:eq(index) 匹配一个给定索引值的元素 

:gt(index) 匹配所有大于给定索引值的元素 

:lt(index) 匹配所有小于给定索引值的元素 

:header 匹配如 h1, h2, h3 之类的标题元素 

:animated 匹配所有正在执行动画效果的元素

#### 内容过滤器

:contains(text) 匹配包含给定文本的元素 

:empty 匹配所有不包含子元素或者文本的空元素 

:parent 匹配含有子元素或者文本的元素 

:has(selector) 匹配**含有选择器所匹配的元素**  的 **元素**

#### 属性过滤器

[attribute] 匹配包含给定属性的元素。 

[attribute=value] 匹配给定的属性是某个特定值的元素 

[attribute!=value] 匹配所有不含有指定的属性，或者属性不等于特定值的元素。

 [attribute^=value] 匹配给定的属性是以某些值开始的元素 

[attribute$=value] 匹配给定的属性是以某些值结尾的元素 

[attribute*=value] 匹配给定的属性是以包含某些值的元素 [attrSel1] [attrSel2]

[attrSelN] 复合属性选择器，需要同时满足多个条件时使用。

#### 表单过滤器

**val()可以操作表单项的value的属性值**

**each()方法是JQuery对象提供的遍历元素的方法，在遍历的function函数中，有一个this对象就是当前遍历到的dom对象**

:input   匹配所有  input, textarea, select 和 button 元素 

:text 匹配所有 文本输入框 

:password 匹配所有的密码输入框 

:radio 匹配所有的单选框 

:checkbox 匹配所有的复选框

:submit 匹配所有提交按钮 

:image 匹配所有 img 标签 

:reset 匹配所有重置按钮 

:button 匹配所有 input type=button 按钮 

:file 匹配所有 input type=file 文件上传 

:hidden 匹配所有不可见元素 display:none 

#### 表单对象属性过滤器

:enabled 匹配所有可用元素 

:disabled 匹配所有不可用元素 

:checked 匹配所有选中的单选，复选，和下拉列表中选中的 option 标签对象 

:selected 匹配所有选中的 option

---

### JQuery元素筛选

eq() 获取给定索引的元素 功能跟 :eq() 一样 

first() 获取第一个元素 功能跟 :first 一样 

last() 获取最后一个元素 功能跟 :last 一样 

filter(exp) 留下匹配的元素 

is(exp) 判断是否匹配给定的选择器，只要有一个匹配就返回，true 

has(exp) 返回包含有匹配选择器的元素的元素 功能跟 :has 一样 

not(exp) 删除匹配选择器的元素 功能跟 :not 一样 

children(exp) 返回匹配给定选择器的子元素 功能跟 parent>child 一样 

find(exp) 返回匹配给定选择器的后代元素 功能跟 ancestor descendant 一样 

next() 返回当前元素的下一个兄弟元素 功能跟 prev + next 功能一样 

nextAll() 返回当前元素后面所有的兄弟元素 功能跟 prev ~ siblings 功能一样 

nextUntil() 返回当前元素到指定匹配的元素为止的后面元素 

parent() 返回父元素 

prev(exp) 返回当前元素的上一个兄弟元素 

prevAll() 返回当前元素前面所有的兄弟元素 

prevUnit(exp) 返回当前元素到指定匹配的元素为止的前面元素 

siblings(exp) 返回所有兄弟元素 

add() 把 add 匹配的选择器的元素添加到当前 jquery 对象

### jQuery 的属性操作

html()、text()、val()  **不传参数是获取，传递参数是设置**

html() 它可以设置和获取起始标签和结束标签中的内容。 跟 dom 属性 innerHTML 一样。 

text() 它可以设置和获取起始标签和结束标签中的文本。 跟 dom 属性 innerText 一样。 

val() 它可以设置和获取表单项的 value 属性值。 跟 dom 属性 value 一样

- **val 方法同时设置多个表单项的选中状态**

~~~html
$("#multiple,#single,:radio,:checkbox").val(["radio2","checkbox1","checkbox3","mul1","mul4","sin3"]);
~~~

**attr() 、prop()传一个参数是获取，传两个参数是设置**

attr() 可以设置和获取属性的值，不推荐操作 checked、readOnly、selected、disabled 等等，会返回undefined

- attr 方法还可以操作非标准的属性。比如自定义属性：abc,bbj 

prop() 可以设置和获取属性的值,只推荐操作 checked、readOnly、selected、disabled 等等，会返回true/false

html会把标签也转成标签效果，text只是文本

### DOM 的增删

- **在事件响应的function函数中，有一个this对象，这个this对象是当前正在响应事件的dom对象。**

**confirm() 是js语言提供的一个确认提示框函数。当用户点击了确定，就返回 true。当用户点击了取消，就返回 false**

**内部插入**： 

appendTo()  	a.appendTo(b) 把 a 插入到 b 子元素末尾，成为最后一个子元素 

prependTo() 	a.prependTo(b) 把 a 插到 b 所有子元素前面，成为第一个子元素 

**外部插入**： 

insertAfter() 		a.insertAfter(b) 得到 ba 

insertBefore() 	a.insertBefore(b) 得到 ab 

**替换**: 

replaceWith() 	a.replaceWith(b) 用 b 替换掉 a 

replaceAll() 		a.replaceAll(b) 用 a 替换掉所有 b 

**删除**： 

remove() 		a.remove(); 删除 a 标签 

empty() 			a.empty(); 清空 a

### CSS样式操作

addClass() 添加样式 

removeClass() 删除样式 

toggleClass() 有就删除，没有就添加样式。

offset() 获取和设置元素的坐标。

### jQuery 动画

**基本动画** 

show() 将隐藏的元素显示 

hide() 将可见的元素隐藏。 

toggle() 可见就隐藏，不可见就显示。 

以上动画方法都可以添加参数。 

1、第一个参数是动画 执行的时长，以毫秒为单位 

2、第二个参数是动画的回调函数 (动画完成后自动调用的函数)

**淡入淡出动画 **

fadeIn() 淡入（慢慢可见） 

fadeOut() 淡出（慢慢消失） 

fadeTo() 在指定时长内慢慢的将透明度修改到指定的值。0 透明，1 完成可见，0.5 半透明 

fadeToggle() 淡入/淡出 切换

### JQuery事件操作

触发情况：

1、jQuery 的页面加载完成之后是浏览器的内核解析完页面的标签创建好 DOM 对象之后就会马上执行。 

2、原生 js 的页面加载完成之后，除了要等浏览器内核解析完标签创建好 DOM 对象，还要等标签显示时需要的内容加载完成

执行次数说明：

1、原生 js 的页面加载完成之后，只会执行最后一次的赋值函数。 

2、jQuery 的页面加载完成之后是全部把注册的 function 函数，依次顺序全部执行

执行先后顺序：

- **jQuery 页面加载优先于原生 js 的页面加载**

#### **jQuery 中其他事件处理方法**

click() 它可以绑定单击事件，以及触发单击事件(传函数点击、不传函数是触发)

mouseover() 鼠标移入事件 

mouseout() 鼠标移出事件 

bind() 可以给元素一次性绑定一个或多个事件。 

one() 使用上跟 bind 一样。但是 one 方法绑定的事件只会响应一次。

unbind() 跟 bind 方法相反的操作，解除事件的绑定 live() 也是用来绑定事件。它可以用来绑定选择器匹配的所有元素的事件。哪怕这个元素是后面动态创建出 来的也有效

#### 事件的冒泡

事件的冒泡：事件的冒泡是指，父子元素同时监听同一个事件。当触发子元素的事件的时候，同一个事件也被传递到了父元素的事件里去响应。

阻止事件冒泡：在子元素事件函数体内，return false; 可以阻止事件的冒泡传递。

#### javaScript 事件对象

js事件对象：是封装有触发的事件信息的一个 javascript 对象。

~~~javascript
        //1.原生 javascript 获取 事件对象
        window.onload = function () {
        document.getElementById("areaDiv").onclick = function (event) {
        console.log(event);
        }
        }
        //2.jQuery 代码获取 事件对象
        $(function () {
        $("#areaDiv").click(function (event) {
        console.log(event);
        });
        });
        //3.使用 bind 同时对多个事件绑定同一个函数。怎么获取当前操作是什么事件。
        $("#areaDiv").bind("mouseover mouseout",function (event) {
        if (event.type == "mouseover") {
        console.log("鼠标移入");
        } else if (event.type == "mouseout") {
        console.log("鼠标移出");
        }
        });
~~~

---

## XML

概念：xml是可扩展的标记性语言。

作用：1、用来保存数据，而且这些数据有自我描述性

​			2、还可以作为项目或模块的配置文件

1.XML 命名规则

- 名称可以含字母、数字以及其他的字符

- 名称不能以数字或者标点符号开始

- 名称不能以字符 “xml”（或者 XML、Xml）开始

- 名称不能包含空格

- xml 中的元素（标签）也 分成 单标签和双标签： 

  单标签 格式： <标签名 属性=”值” 属性=”值” ...... /> 

  双标签 格式：< 标签名 属性=”值” 属性=”值” ......>文本数据或子标签

2.XML的属性

- 属性可以提供元素的额外信息
- 一个标签上可以书写多个属性。每个属性的值必须使用 引号 引起来。

3.文本区域（CDATA 区）

CDATA 格式：

~~~xml
<![CDATA[ 这里可以把你输入的字符原样显示，不会解析 xml ]]>
~~~

### dom4j解析

具体步骤：

①通过创建 SAXReader 对象。来读取 xml 文件，获取 Document 对象 

②通过 Document 对象。拿到 XML 的根元素对象 

③通过根元素对象。获取所有的 book 标签对象 

④遍历每个 book 标签对象。然后获取到 book 标签对象内的每一个元素，再通过 getText() 方法拿到起始标签和结 束标签之间的文本内容

~~~java
/*
    读取books.xml文件生成book类
     */
    @Test
    public void test1() throws Exception {
        //1.读取books.xml文件
        SAXReader saxReader = new SAXReader();

        //2.通过document对象获取根元素
        Document document = saxReader.read("src\\books.xml");

        //3.通过根元素获取book标签对象
        //element()和elements()都是通过标签名查找子元素
        Element rootElement = document.getRootElement();
        List<Element> books = rootElement.elements();

        //4.遍历，处理每一个book标签转换为book类
        for (Element book : books){
            //asXML()方法把标签对象转换为标签字符串
            Element name = book.element("name");
            //getText()方法可以获取标签中的文本
            String textName = name.getText();
//            System.out.println(name.asXML());

            //elementText()直接获取指定标签的文本内容
            String price = book.elementText("price");
            String author = book.elementText("author");
            String sn = book.attributeValue("sn");
            
            System.out.println(new Book(sn,textName, new BigDecimal(price),author));
        }
        }
~~~

---

## Tomcat

1.从本地文件拖入浏览器访问与通过浏览器ip访问的区别

![本地文件拖入访问](https://s1.ax1x.com/2022/04/02/qInMKe.png)



![通过浏览器ip访问](https://s1.ax1x.com/2022/04/02/qIn8UI.png)

---

2.Root工程访问的特殊情况

- http://IP:port    ====>>>> 没有工程名的时候，默认访问的是 ROOT 工程

- http://ip:port/工程名/ ====>>>> 没有资源名，默认访问 index.html 页面

3.IDEA整合tomcat

整合过程过于冗长，参考csdn收藏

![动态web工程的目录结构](https://s1.ax1x.com/2022/04/02/qInt8f.png)

---

## Servlet

Servlet是java的运行在服务器上的小程序，**用于接受客户端发送过来的请求，并响应数据给客户端**

Servlet是JavaEE的规范之一。规范就是接口

Servlet是JavaWeb三大组件之一，三大组件分别是Servlet程序、Fiter过滤器、Listen监听器，

- 三大组件被tomcat服务器初始化的先后顺序是Listen监听器、Fiter过滤器、Servlet程序

### Servlet技术

#### 实现Servlet程序

①编写一个类去**实现** Servlet **接口** 

②实现 service 方法，处理请求，并响应数据 

③到 web.xml 中去配置 servlet 程序的访问

1-2步骤代码示例

~~~java
public class HelloServlet implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    //service()方法专用于接受请求和响应数据
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("Hello Servlet被访问了");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}
~~~

步骤3代码示例

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- servlet标签用于给Tomcat配置servlet程序   -->
    <servlet>

        <!-- servlet-name标签用于给servlet程序起一个别名(通常是类名) -->
        <servlet-name>HelloServlet</servlet-name>

        <!-- servlet-class是servlet程序的全类名 -->
        <servlet-class>servletstudy.HelloServlet</servlet-class>
    </servlet>

    <!-- servlet-mapping标签用于给servlet程序配置访问地址-->
    <servlet-mapping>

        <!--此处servlet-name标签与上面不同，此处是用于给说明配置地址是给哪一个servlet程序使用的-->
        <servlet-name>HelloServlet</servlet-name>

        <!-- url-pattern标签配置访问地址       -->
        <!-- / 斜杠在服务器解析的时候，表示地址为：http://ip:port/工程路径 <br/>
                /hello 表示地址为：http://ip:port/工程路径/hello <br/>      -->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
~~~

![资源访问图解](https://s1.ax1x.com/2022/04/02/qIn2xU.png)

#### Servlet的生命周期

①执行 Servlet 构造器方法 

②执行 init 初始化方法 

**第①、②步，是在第一次访问的时候创建 Servlet 程序会调用。**

③执行 service 方法 第三步，每次访问都会调用。 

④执行 destroy 销毁方法 **第④步，在 web 工程停止的时候调用。**

#### Servlet请求的分发处理

较少使用

通常使用其继承类HttpServlet

~~~java
public class HelloServlet implements Servlet {
    //service()方法专用于接受请求和响应数据
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("Hello Servlet被访问了");
        // 类型转换（因为它有 getMethod()方法）
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
		// 获取请求的方式
        String method = httpServletRequest.getMethod();
        if ("GET".equals(method)) {
            doGet();
        } else if ("POST".equals(method)) {
            doPost();
        }
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }

    /**
     * 做 get 请求的操作
     */
    public void doGet(){
        System.out.println("get 请求");
        System.out.println("get 请求");
    }
    /**
     * 做 post 请求的操作
     */
    public void doPost(){
        System.out.println("post 请求");
        System.out.println("post 请求");
    }
~~~

#### 继承类HttpServlet实现Servlet程序

- **在idea中可以通过 包名 -- create new servlet 快速创建，仅需修改xml配置文件**，

  因此以上这两种方法已不通用

一般情况下都是使用继承 HttpServlet 类的方式去实现 Servlet 程序。 

实现步骤：

①编写一个类去**继承** HttpServlet **类** 

②根据业务需要重写 doGet 或 doPost 方法 

③到 web.xml 中的配置 Servlet 程序的访问地址

- xml配置与第一个类似

~~~java
public class ServletTest extends HttpServlet {

    /*
    doGet在get请求时调用
     */
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet");
    }

    /*
    doPost在Post请求时调用
     */
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doPost");
    }
}
~~~

#### Servlet的继承体系

![继承体系](https://s1.ax1x.com/2022/04/02/qInOMD.png)

---

### ServletConfig类

见命知意即可知其是servlet程序的配置信息类

Servlet 程序和 ServletConfig 对象都是由 Tomcat 负责创建 

**Servlet 程序默认是第一次访问的时候创建(生命周期的1、2步)，ServletConfig 是每个 Servlet 程序创建时，就创建一个对应的 ServletConfig对象**

其作用：

①获取servlet程序的别名，即servlet—name的值

②获取初始化参数init-param  获取需要在servlet标签中设置

③获取ServletContext的对象

~~~java
		//①获取servlet程序的别名，即servlet—name的值
        System.out.println(servletConfig.getServletName());
        //②获取初始化参数init-param
        System.out.println("初始化参数的值是：" + servletConfig.getInitParameter("username"));
        //③获取ServletContext的对象
        System.out.println("初始化参数的值是：" + servletConfig.getServletContext());
~~~

- **重写init-param()方法需要在方法体内调用super(config)，因此init-param的参数保存在父类定义的config中**

  **子类重写的init-param()只有这个config参数，但其属性值为空，因为报了空指针异常**

### ServletContext类

1、ServletContext 是一个接口，它表示 Servlet 上下文对象 

2、**一个 web 工程，只有一个 ServletContext 对象实例，与 ServletConfig对象是完全不同的**

3、**ServletContext **对象是一个**域对象**。 **域对象是指可以像 Map 一样存取数据的对象**

​	  这里的域指的是存取数据的操作范围，整个 web 工程。

4、ServletContext 是在 web 工程部署启动的时候创建。在 web 工程停止的时候销毁。 		

|        | 存数据         | 取数据         | 删除数据          |
| :----- | :------------- | :------------- | :---------------- |
| Map    | put()          | get()          | remove()          |
| 域对象 | setAttribute() | getAttribute() | removeAttribute() |

5、ServletContext 类的四个作用 

①获取 web.xml 中配置的上下文参数 context-param 

②获取当前的工程路径，格式: /工程路径 

③获取工程部署后在服务器硬盘上的绝对路径 

④像 Map 一样存取数据

~~~java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    	//获取 ServletContext的对象
        ServletContext context = getServletConfig().getServletContext();
        //①获取 web.xml 中配置的上下文参数 context-param
        String username = context.getInitParameter("username");
        System.out.println("context的值是：" + username);
        //②获取当前的工程路径，格式: /工程路径
        System.out.println("当前工程路径是：" + context.getContextPath());
        //③获取工程部署后在服务器硬盘上的绝对路径

        /*
        斜杠的含义是：http://ip:port/工程名 ===  idea 对应工程的 web目录 === xml配置文件中的contextPath的值
         */
        System.out.println("当前工程的绝对路径是：" + context.getRealPath("/"));//此时“/”后代表已经映射到了工程的 web目录下
    
    	 //④像 Map 一样存取数据
        System.out.println("ContextServlet中设置之前获取域数据的key值是：" + context.getAttribute("username"));
        
        context.setAttribute("username","test");

        System.out.println("ContextServlet中获取域数据的key值是：" + context.getAttribute("username"));
~~~

### HttpServletRequest类

作用：**只要有请求进入Tomcat服务器，Tomcat就会把请求的Http协议信息封装到一个Request对象中**

​			**然后传递到 service 方法（doGet 和 doPost）中给我们使用。就通过 HttpServletRequest 对象，获取到所有请求的信息**

#### 常用方法

①getRequestURI() 获取请求的资源路径 

②getRequestURL() 获取请求的统一资源定位符（绝对路径） 

③getRemoteHost() 获取客户端的 ip 地址 

④getHeader() 获取请求头 

⑤getParameter() 获取请求的参数 

⑥getParameterValues() 获取请求的参数（多个值的时候使用） 

⑦getMethod() 获取请求的方式 GET 或 POST

⑧setAttribute(key, value); 设置域数据 

⑨getAttribute(key); 获取域数据 

⑩getRequestDispatcher() 获取请求转发对象

### 请求的转发

请求转发是指服务器收到请求后，从一次资源跳转到另一个资源的操作叫请求转发。

![请求的转发](https://s1.ax1x.com/2022/04/02/qIuLYq.png)

~~~java
/*
资源请求Servlet1后转发至Servlet2
*/
public class Servlet1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取请求的参数
        String username = req.getParameter("username");
        System.out.println("servlet1:" + username);

        //servlet1验证
        req.setAttribute("key","Servlet1Done");

        //转发至servlet2
        //请求转发的参数必须要以/斜杠开始，斜杠表示http:localhost:ip/工程路径/ ，映射到idea的web工程下
        RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");

        //指明前往
        requestDispatcher.forward(req,resp);
    }
}


public class Servlet2 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //查看参数
        String username = req.getParameter("username");

        System.out.println("Servlet2查看请求参数" + username);

        //查看Servlet1的业务是否完成
        Object key = req.getAttribute("key");
        System.out.println("sevlet1的业务完成" + key);

        //Servlet2处理自己的业务
        System.out.println("请求的转发完成");
    }
}
~~~

### Base标签的作用

**base标签可以保证请求的转发永远以某个地址为基础进行操作**

base 标签设置页面相对路径工作时href 的属性进行操作，**href 属性就是参数的地址值**

base标签通常声明在title标签下方

![示例说明](https://s1.ax1x.com/2022/04/02/qIKeXD.png)

### web 中 / 斜杠的不同意义

在 web 中 / 斜杠 是一种绝对路径。

- / 斜杠被浏览器解析

  得到的地址是：http://ip:port/ 

- / 斜杠被服务器解析

  得到的地址是：http://ip:port/工程路径 

  1、/servlet1 

  2、servletContext.getRealPath(“/”); 

  3、request.getRequestDispatcher(“/”); 

- 特殊情况： response.sendRediect(“/”); 把斜杠发送给浏览器解析。得到 http://ip:port/

### HttpSevletResponse类

与HttpServletRequest 类一样。只要每次请求进来，Tomcat服务器就会创建一个HttpSevletResponse的对象。

- HttpServletRequest 表示请求过来的信息
- HttpServletResponse 表示所有响应的信息，通用于设置返回给客户端的信息

#### 输出流

往客户端回传数据需要使用以下两个流

- 字节流 getOutputStream(); 	常用于下载（传递二进制数据） 
- 字符流 getWriter(); 			       常用于回传字符串（常用）

需要注意的是两个流只能二选一。不然报错

#### 给客户端回传数据

- 浏览器和服务器字符集不一致会出现中文乱码的现象

~~~java
public class ResponseIOServlet extends HttpServlet {
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException,
IOException {
// 要求 ： 往客户端回传 字符串 数据。
PrintWriter writer = resp.getWriter();
writer.write("response's content!!!");
}
}

/*
出现中文乱码现象的解决办法：
它会同时设置服务器和客户端都使用 UTF-8 字符集，还设置了响应头
此方法一定要在获取流对象之前调用才有效
*/
resp.setContentType("text/html;charset=UTF-8");
~~~

#### 请求重定向

概念：客户端给服务器发送请求，服务器给予客户端新的资源地址，允许客户端访问新的资源地址。(重定向的出现有可能是旧地址不可访问)

![请求重定向解析](https://s1.ax1x.com/2022/04/02/qIKNng.png)

方案一：(不推荐)

~~~java
/*
客户端请求访问respond1重定向至response2
*/
public class Response1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("response1被访问过");

        //设置状态码302表示重定向
        resp.setStatus(302);

        //设置响应头并说明访问的新地址
        resp.setHeader("location","http://localhost:8080/Servlet2/response2");
    }
}

public class Response2 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //处理被重定向过来的请求
        PrintWriter w = resp.getWriter();
        w.write("response2已访问");
    }
}
~~~

方案二：(推荐)

~~~java
public class Response1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("response1被访问过");

        //设置状态码302表示重定向
        //resp.setStatus(302); 此时302码是固定的因此不需要再设置
        //给客户端返回新地址
		resp.sendRedirect("http://localhost:8080/Servlet2/response2");
    }
}
~~~


---

## Http协议

概念：客户端与服务端之间通信发送的数据需要遵守的规则

http协议的数据也叫报文。

- **在Http协议中有一个请求头Referer会在发起请求的时候把请求地址同时发送给服务器**
- **因此服务器可以通过rep.getHead(“Referer”)方法获取发起请求的地址，以达到返回原网页的需求**

![Http协议请求头](https://s1.ax1x.com/2022/04/02/qIKycT.png)

### GET请求

1.请求行

①请求的方式  GET

②请求的资源路径[+？+请求参数]   注：[ ]代表可选

③请求的协议和版本号  HTTP/1.1

2.请求头

key：value 组成不同的键值对，表示不同的含义

![GET请求抓包分析](https://s1.ax1x.com/2022/04/02/qIKRHJ.png)

- 常见的GET请求：

​	1、form 标签 method=get 

​	2、a 标签 

​	3、link 标签引入 css 

​	4、Script 标签引入 js 文件 

​	5、img 标签引入图片 

​	6、iframe 引入 html 页面 

​	7、在浏览器地址栏中输入地址后敲回车

### POST请求

1.请求行

①请求的方式  POST

②请求的资源路径[+？+请求参数]   注：[ ]代表可选

③请求的协议和版本号  HTTP/1.1

2.请求头

①key：value 组成不同的键值对，不同的请求头表示不同的含义

②**空行**

3.请求体

①(即为发送给服务器的数据)

![POST请求抓包解析](https://s1.ax1x.com/2022/04/02/qIK7jO.png)

- 常见的POST请求
  - form 标签 method=pos

### 响应的HTTP协议格式

1.响应行

①响应的协议和版本号

②响应状态码

③响应状态描述符

2.响应头

①key：value  不同的响应头，有不同的含义

②空行

3.响应体

①即服务端回传给客户端的数据

![响应的HTTP协议分析](https://s1.ax1x.com/2022/04/02/qIKbuD.png)

- 常见的响应码
  - 200 	表示请求成功
  - 302     表示请求重定向
  - 404     表示请求的资源地址不存在
  - 500     表示服务器已接受请求但服务器内部存在错误

### MIME类型

MIME 是 HTTP 协议中数据类型

- 常见的 MIME 类型：

![MIME 类型](https://s1.ax1x.com/2022/04/02/qIKqDe.png)

![](C:\Users\hcxs1986\AppData\Roaming\Typora\typora-user-images\image-20220322000201058.png)

---

## JavaEE的三层架构

- 通常在提示错误信息的同时回传数据使用request域对象保存数据

![JavaEE](https://s1.ax1x.com/2022/04/02/qIKLHH.png)

![各个包层](https://s1.ax1x.com/2022/04/02/qIKjUA.png)

---

## JSP

jsp全称是java serve pages的服务器页面，主要用于代替sevlet回传html页面的数据

**JSP本质上是个Servlet程序。**

### jsp头部的page指令

page指令可用修改jsp页面中的一些重要属性或行为

| language 属性     | 值只能是 java.表示翻译的得到的是 java 语言的                 |
| :---------------- | :----------------------------------------------------------- |
| contentType 属性  | 设置响应头 contentType 的数据类型                            |
| pageEncoding 属性 | 设置当前 jsp 页面的编码                                      |
| import 属性       | 给当前 jsp 页面导入需要使用的类包                            |
| autoFlush 属性    | 设置是否自动刷新 out 的缓冲区，默认为 true                   |
| buffer 属性       | 设置 out 的缓冲区大小。默认为 8KB                            |
| errorPage 属性    | 设置当前 jsp发生错误后，需要跳转到哪个页面去显示错误信息     |
| isErrorPage 属性  | 设置当前 jsp 页面是否是错误页面。是的话，就可以使用 exception 异常对象 |
| session 属性      | 设置当前 jsp 页面是否获取 session 对象,默认为 true           |
| extends 属性      | 给服务器厂商预留的 jsp 默认翻译的 servlet 继承于什么类       |

 ### jsp中的常用脚本

#### 声明脚本

格式：**<%!  声明java代码 %>**

作用：给jsp翻译出的java类定义属性和方法或静态代码块、内部类等

~~~jsp
<%-- 1.声明类属性--%>
<%!
    private Integer id;
    private String name;
    private static Map<String,Object> map;
%>

<%-- 2.声明static静态代码块 --%>
<%!
    static {
        map = new HashMap<>();
        map.put("test","value1");
    }
%>

<%-- 3.声明类的方法 --%>
<%!
    public int abc(){
        return 10;
    }
%>

<%-- 4.声明内部类--%>
<%!
    public static class Test{
        private int id;
        private String name;
    }
%>
~~~

#### 表达式脚本

格式：<%= 表达式 %>

作用：在jsp页面输出数据

特点：

①所有的表达式脚本都会被翻译到_jspService()方法中

②表达式脚本都会被翻译成为out.print()输出到页面上

③_jspService()方法中的对象都可以直接使用

④表达式脚本中的表达式不能以分号结束；

~~~jsp
<%-- 表达式脚本--%>
<%--1.输出整型--%>
    <%=12%> <br>
<%--2.输出浮点型--%>
    <%=12.2%> <br>
<%--3.输出字符串--%>
    <%="我是字符串"%><br>
<%--4.输出对象--%>
    <%=map%><br>
~~~

#### 代码脚本

格式：<% java语句 %>

作用：在jsp页面中，编写需要的功能(通常写的是java语句)

特点：

①代码脚本里可以书写任意的 java 语句，service 方法中可以写的 java 代码，都可以书写到代码脚本中

②代码脚本的内容都会被翻译到 _jspService()方法中，因此方法中对象都可以直接使用

③可用多个代码脚本组合完成一个完整的java语句

④代码脚本还可以和表达式代码组合使用  

### jsp的三种注释

1.html注释

<! -- html注释 -->  

html 的注释会被翻译到 java 源代码中，在_jspService()方法里，以out.writer输出到客户端

2.java注释

// 单行java注释

/* 多行java注释 */

同样java注释也会被翻译到java源代码中

3.jsp注释

<%--  jsp注释 --%>

 jsp注释可以注释掉jsp中的所有代码

### jsp九大内置对象

内置对象是指Tomcat在翻译jsp页面成为Servlet源代码后，内部提供的九个对象。

**request 对象 请求对象，可以获取请求信息 **

**response 对象 响应对象。可以设置响应信息 **

pageContext 对象 当前页面上下文对象。可以在当前上下文保存属性信息 

session 对象 会话对象。可以获取会话信息。 

exception 对象 异常对象只有在 jsp 页面的 page 指令中设置 isErrorPage="true" 的时候才会存在 

application 对象 ServletContext 对象实例，可以获取整个工程的一些信息。 

config 对象 ServletConfig 对象实例，可以获取 Servlet 的配置信息 out 对象 输出流。 

page 对象 表示当前 Servlet 对象实例（无用，用它不如使用 this 对象）。

### jsp四个域对象

四大域对象经常用来保存数据信息。

①pageContext(pageContextImpl类) 可以保存数据在同一个 jsp 页面中使用 

②request(HttpServletRequest类) 	可以保存数据在同一个 request 对象中使用。经常用于在转发的时候传递数据 

③session(HttpSession类) 可以保存在一个会话中使用 

④application(ServletContext类)  就是 ServletContext 对象，在整个web工程下使用，在web工程启动时就创建一个

从内存优化的角度出发，优先顺序(从小到大)：① ==>  ② ==>   ③  ==>   ④ 

### jsp中out和getwriter输出流的区别

- 在jsp页面下通常使用out.print()进行输出，以避免打乱页面的输出顺序
- out.writer() 输出字符串没有问题，输出基本数据类型会以ascii字符集对应的值输出，(原因是其底层都把输入的基本数据类型强转为char型变量输出)
- out.print() 输出任意数据都没有问题(其源码底层方法都将形参转化为String字符串后调用write方法输出)

相同点：两者都可以用于向客户端回传数据，输出内容到客户端页面上

![out和respon输出原理](https://s1.ax1x.com/2022/04/02/qIMEUs.png)

### jsp常用标签

#### 静态包含

特点：

①静态包含不会翻译被包含的 jsp 页面。 

②静态包含其实是把被包含的 jsp 页面的代码拷贝到包含的位置执行输出

~~~jsp
<!-- <%@ include file=""%> 静态包含，file 属性指定你要包含的 jsp 页面的路径 -->
<body>
    页头信息 <br>
    主体内容 <br>
    <%@ include file="/include/footer.jsp"%>
</body>
~~~

#### 动态包含

特点：

①动态包含会把被包含的 jsp 页面。翻译成java代码

②动态包含底层代码使用如下代码去调用被包含的 jsp 页面执行输出。

 JspRuntimeLibrary.include(request, response, "/include/footer.jsp", out, false); 

③动态包含，还可以传递参数

~~~jsp
<%--  动态包含<jsp:include page=""></jsp:include>--%>
<jsp:include page="/include/footer.jsp">
    <jsp:param name="test" value="test"/>
</jsp:include>
~~~

#### 请求转发

 <%-- 是请求转发标签，它的功能就是请求转发 page 属性设置请求转发的路径 --%>

~~~jsp
<jsp:forward page="/scope2.jsp"></jsp:forward>
~~~

### EL表达式

EL表达式全称：Expression Language，是表达式语言

EL表达式的作用是用于替代jsp中的表达式脚本在jsp页面上输出数据

**EL 表达式的格式是：${表达式}**

与jsp输出数据的异同：

- EL 表达式在输出 null 值的时候，输出的是空串。
- jsp 表达式脚本输出 null 值的时候，输出的是 null 字符

#### EL 表达式输出域数据的顺序

EL 表达式主要是在 jsp 页面中输出数据。 主要是输出域对象中的数据。 

- 当四个域中都有相同的 key 的数据的时候，EL 表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出。

#### EL表达式输出属性值

- **EL表达式输出属性值实际上是根据输入的属性名去找对应的get()方法输出数据，而不是根据变量名输出**

~~~jsp
	输出Person： ${person} <br/>
    输出Person的name属性： ${person.name} <br/>
    输出Person的phone个别属性： ${person.phone[0]} <br/>
    输出Person的cities属性： ${person.cities} <br/>
    输出Person的cities个别属性： ${person.cities[0]} <br/>
    输出Person的map集合： ${person.thing} <br/>
    输出Person的map集合值得key的值： ${person.thing.key1} <br/>
~~~

#### EL表达式的运算

1.关系运算

2.逻辑运算

3.算数运算

4.empty运算

用于判断一个数据是否为空，为空则为输出true，否则输出false

①值为 null 值的时候，为空 

②值为空串的时候，为空 

③值是 Object 类型数组，长度为零的时候 

④list 集合，元素个数为零

⑤map集合，元素个数为零

5.三元运算

6.“**.**”运算和[ ]括号运算

“**.**”运算可以输出Bean对象中的某个属性的值

[ ]括号运算可以输出有序集合中的某个元素的值，此外还可以输出 map 集合中 key 里含有特殊字符的 key 的值

### EL表达式的隐含对象

| 变量             | 类型                  | 作用                                           |
| :--------------- | :-------------------- | :--------------------------------------------- |
| pageContext      | PageContextImpl       | 它可以获取 jsp 中的九大内置对象                |
| pageScope        | Map<String,Object>    | 它可以获取 pageContext 域中的数据              |
| requestScope     | Map<String,Object>    | 它可以获取 Request 域中的数据                  |
| sessionScope     | Map<String,Object>    | 它可以获取 Session 域中的数据                  |
| applicationScope | Map<String,Object>    | 它可以获取 ServletContext域中的数据            |
| param            | Map<String,String>    | 它可以获取请求参数的值                         |
| paramValues      | Map<String,String[ ]> | 它也可以获取请求参数的值，获取多个值的时候使用 |
| header           | Map<String,String>    | 它可以获取请求头的信息                         |
| headerValues     | Map<String,String[ ]> | 它可以获取请求头的信息，它可以获取多个值的情况 |

| cookie    | Map<String,Cookie> | 它可以获取当前请求的 Cookie 信息        |
| :-------- | :----------------- | :-------------------------------------- |
| initParam | Map                | 它可以获取在 web.xml 中配置的上下文参数 |

#### EL 获取四个特定域对象中的属性

pageScope ====== pageContext 域 

requestScope ====== Request 域 

sessionScope ====== Session 域 

applicationScope ====== ServletContext 

#### EL中pageContext对象的使用

~~~jsp
<body>
    <%--
    request.getScheme() 它可以获取请求的协议
    request.getServerName() 获取请求的服务器 ip 或域名
    request.getServerPort() 获取请求的服务器端口号
    request.getContextPath() 获取当前工程路径
    request.getMethod() 获取请求的方式（GET 或 POST）
    request.getRemoteHost() 获取客户端的 ip 地址
    session.getId() 获取会话的唯一标识
    --%>

    <%--    pageContext 对象的使用--%>
    <%
        pageContext.setAttribute("req", request);
    %>
    1. 协议：${req.scheme} <br/>
    2. 服务器 ip：${pageContext.request.serverName} <br/>
    3. 服务器端口：${pageContext.request.serverPort}<br/>
    4. 获取工程路径：${pageContext.request.contextPath} <br/>
    5. 获取请求方法：${pageContext.request.method} <br/>
    6. 获取客户端 ip 地址：${pageContext.request.remoteHost} <br/>
    7. 获取会话的 id 编号：${pageContext.session.id} <br/>
</body>
~~~

#### EL其他隐含对象的使用

~~~jsp
<%-- EL其他隐含对象的使用--%>
        <%--   获取请求参数     --%>
        输出请求参数username的值：${param.username} <br/>
        输出请求参数password的值：${param.password} <br/>


        输出请求参数username的值：${paramValues.username[0]} <br>
        输出请求参数hobby的值：${paramValues.hobby[0]} <br>
        输出请求参数hobby的值：${paramValues.hobby[1]} <br>

        <hr>
        <%-- 获取请求头的信息       --%>
        输出请求头User-Agent的值：${header["User-Agent"]} <br>
        输出多个请求头某个具体的值：${headerValues["User-Agent"][0]} <br>

        <hr>
        获取cookie的名称：${cookie.JSESSIONID} <br>
        获取cookie的名称值：${cookie.JSESSIONID.name} <br>

        <hr>
        输出&lt;ConText-param&gt;username的值：${initParam.username}
~~~

### JSTL标签库

全称：JSP Standard Tag Library，是JSP标准标签库

- EL表达式用于替代jsp中的表达式脚本，而JSTL标签库则是替代了jsp中的代码脚本

**JSTL 由五个不同功能的标签库组成。**

| 功能范围         | URI                                    | 前缀 |
| :--------------- | :------------------------------------- | :--- |
| 核心标签库--重点 | http://java.sun.com/jsp/jstl/core      | c    |
| 格式化           | http://java.sun.com/jsp/jstl/fmt       | fmt  |
| 函数             | http://java.sun.com/jsp/jstl/functions | fn   |
| 数据库(不使用)   | http://java.sun.com/jsp/jstl/sql       | sql  |
| XML(不使用)      | http://java.sun.com/jsp/jstl/xml       | x    |

JSTL 标签库的使用步骤 

1、先导入 jstl 标签库的 jar 包。 

taglibs-standard-impl-1.2.1.jar 

taglibs-standard-spec-1.2.1.jar 

2、第二步，使用 taglib 指令引入标签库。

 <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

示例：

~~~jsp
<body>
        <%--
        1.<c:set />
        作用：set 标签可以往域中保存数据
        域对象.setAttribute(key,value);
        scope 属性设置保存到哪个域
        page 表示 PageContext 域（默认值）
        request 表示 Request 域
        session 表示 Session 域
        application 表示 ServletContext 域
        var 属性设置 key 是多少
        value 属性设置值
        --%>
    保存之前：${requestScope.test} <br>
    <c:set scope="request" var="test" value="testValue"></c:set>
    保存之后 ：${requestScope.test} <br>


        <%--
        2.<c:if />  if标签用于做if判断
        test属性表示判读的条件(使用EL表达式输出)
        --%>
    <c:if test="${12 != 12}">
        <h1>判断成立</h1>
    </c:if>

    <hr>
        <%--
         3. <c:choose> <c:when> <c:otherwise>标签
            作用：多路判断。跟 switch ... case
        choose 标签开始选择判断
        when 标签表示每一种判断情况
        test 属性表示当前这种判断情况的值
        otherwise 标签表示剩下的情况

        <c:choose> <c:when> <c:otherwise>标签使用时需要注意的点：
        1、标签里不能使用 html 注释，要使用 jsp 注释
        2、when 标签的父标签一定要是 choose 标签
                --%>

        <%
            request.setAttribute("height",140);
        %>

        <c:choose>
            <c:when test="${requestScope.height > 130}">
                <h1>标准产品</h1>
            </c:when>
            <c:when test="${requestScope.height > 150}">
                <h1>完美产品</h1>
            </c:when>
            <c:otherwise>
                <h1>不合格产品</h1>
            </c:otherwise>

        </c:choose>
    
    	<%--
          4. <c:forEach />
          作用：遍历输出使用。         
			遍历list集合
            for (List<Student> stu: list)
                items 表示遍历的集合
                var 表示遍历到的数据，var的值其实是自定义的变量名
                begin 表示遍历的开始索引值
                end 表示结束的索引值
                step 属性表示遍历的步长值
                varStatus 属性表示当前遍历到的数据的状态
                for（int i = 1; i < 10; i+=2）
             --%>
            <%
                List<Student> list = new ArrayList<>();
                list.add(new Student(1,"s1","s1p",11,"test1"));
                list.add(new Student(2,"s2","s2p",12,"test2"));
                list.add(new Student(3,"s3","s3p",13,"test3")); 
            %>

            <%
                request.setAttribute("list",list);
            %>

            <table>
                <tr>
                    <th>编号</th>
                    <th>用户名</th>
                    <th>密码</th>
                    <th>年龄</th>
                    <th>电话</th>
                    <th>操作</th>
                </tr>

            <c:forEach begin="2" end="6" step="2"  varStatus="staus" items="${requestScope.list}" var="stu">
            <tr>
                <td>${stu.id}</td>
                <td>${stu.username}</td>
                <td>${stu.password}</td>
                <td>${stu.age}</td>
                <td>${stu.phone}</td>
                <td>${staus}</td>
            </tr>
            </c:forEach>
            </table>
</body>
~~~

![LoopTagStatus接口的常用方法](https://s1.ax1x.com/2022/04/02/qIMWM8.png)


---

## Listener监听器

Listenner监听器是javaEE的三大组件之一，也是JavaEE的规范，即为接口。

作用：监听某种事物的变化，通过回调函数反馈给程序或用户，以做出相应的处理

### ServletContext监听器

作用：用于监听ServletContext对象的创建和销毁

监听到ServletContext对象创建，执行以下方法

~~~java
//在 ServletContext 对象创建之后马上调用，做初始化
public void contextInitialized(ServletContextEvent sce);
~~~

监听到ServletContext对象销毁，执行以下方法

~~~java
// 在 ServletContext 对象销毁之后调用
public void contextDestroyed(ServletContextEvent sce);
~~~

如何使用 ServletContextListener 监听器监听 ServletContext 对象。 

使用步骤如下： 

1、编写一个类去实现 ServletContextListener 

2、实现其两个回调方法 

3、到 web.xml 中去配置监听

~~~java
public class ListenerTest implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        System.out.println("对象被创建");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
        System.out.println("对象被销毁");
    }
}
~~~

xml配置

~~~xml
<!--   配置监听器 -->
    <listener>
        <listener-class>listenner.ListenerTest</listener-class>
    </listener>
~~~

---

## 文件的上传和下载

### 文件的上传

1. 要有一个form标签，method方法必须等于post

2. form 标签的 encType 属性值必须为 multipart/form-data 值 

3. 在 form 标签中使用 input type=file 添加上传的文件 

4. 编写服务器代码（Servlet 程序）接收，处理上传的数据。 

​	**encType=multipart/form-data 表示提交的数据，以多段（每一个表单项一个数据段）的形式进行拼接，然后以二进制流的形式发送给服务器**

![Http协议内容](https://s1.ax1x.com/2022/04/02/qIM7in.png)

**借用commons-fileupload.jar处理上传数据处理步骤：**

①导入两个 jar 包： 

commons-fileupload-1.2.1.jar 

commons-io-1.4.jar 

②调用jar包中包含类的方法

**ServletFileUpload 类，用于解析上传的数据。 **

**FileItem 类，表示每一个表单项。 **

| 常用方法                                                     | 方法用途                                                     |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| boolean ServletFileUpload.isMultipartContent(HttpServletRequest request); | 判断当前上传的数据格式是否是多段的格式。                     |
| public List parseRequest(HttpServletRequest request)         | 解析上传的数据                                               |
| boolean FileItem.isFormField()                               | 判断当前这个表单项，是否是普通的表单项。还是上传的文件类型。 true 表示普通类型的表单项 false 表示上传的文件类型 |
| String FileItem.getFieldName()                               | 获取表单项的 name 属性值                                     |
| String FileItem.getString()                                  | 获取当前表单项的值。                                         |
| String FileItem.getName()                                    | 获取上传的文件名                                             |
| void FileItem.write( file )                                  | 将上传的文件写到 参数 file 所指向抽硬盘位置 。               |

~~~java
@Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取请求的参数内容
//        ServletInputStream inputStream = req.getInputStream();
//        byte[] b = new byte[1024];
//        int len;
//        while ((len = inputStream.read(b)) != -1){
//            System.out.println(new String(b,0,len));
//        }
        req.setCharacterEncoding("UTF-8");
        //1.判断当前上传的数据格式是否是多段的格式。
        if (ServletFileUpload.isMultipartContent(req)){
            //创建FileItemFactory工厂实现类
            FileItemFactory factory = new DiskFileItemFactory();
            //创建用于解析上传数据的工具类ServletFileUpload类
            ServletFileUpload upload = new ServletFileUpload(factory);

            //解析上传数据，得到每一个表单项FileItem
            try {
                List<FileItem> list = upload.parseRequest(req);

                //遍历FileItem集合，判断每一个表单项是普通类型还是上传的文件
                for (FileItem fileItem: list) {
                    if (fileItem.isFormField()){
                        //普通表单项
                        System.out.println("表单项的name属性值:" + fileItem.getFieldName());
                        //参数UTF-8 解决中文乱码问题
                        System.out.println( "表单项的value属性值:" + fileItem.getString("UTF-8"));
                    }else {
                        //上传的文件
                        System.out.println("表单项的name属性值:" + fileItem.getFieldName());
                        System.out.println("上传文件的名字:" + fileItem.getName());
                        fileItem.write(new File("D:\\test.jpg"));
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
~~~

### 文件的下载

下载的常用 API ： 

response.getOutputStream(); 

servletContext.getResourceAsStream(); 

servletContext.getMimeType(); 

response.setContentType();

~~~java
public class DownloadServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.获取请求的文件名
        String fileName = "1.jpg";

        //2.读取请求文件的内容
        ServletContext servletContext = getServletContext();

        //4.在回传之前，告诉客户端返回数据的类型，通过响应头
        String mimeType = servletContext.getMimeType("/file/" + fileName);
        System.out.println("下载的文件类型：" + mimeType);
        resp.setContentType(mimeType);

        //5.告诉客户端回传的数据用于下载，也是通过响应头
        //Content-Disposition响应头表示收到的数据怎么处理
        //attachment表示附件，通过下载使用
        //filename表示指定下载的文件名，下载后的文件名与指定文件名不一定需要一致，但中文名会出现乱码
        //URLEncoder方法会把汉字转换成%xx%xx的格式
        //resp.setHeader("Content-Disposition","attachment;filename=" + URLEncoder.encode("测试.jpg","UTF-8"));
        resp.setHeader("Content-Disposition","attachment;filename=" + fileName);

        InputStream resource = servletContext.getResourceAsStream("/file/" + fileName);
        //获取响应的输出流
        OutputStream out = resp.getOutputStream();

        //3.将文件内容回传到客户端
        //读取流的全部数据，复制给输出流，回传到客户端
        IOUtils.copy(resource,out);
    }
~~~

---

## Cookie

**cookie是服务器通知客户端保存键值对的一种技术**

客户端在每次向服务器发起请求的同时都会发送cookie，但每个cookie的大小不超过4kb

### Cookie的创建

~~~java
protected void creatCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //1.创建cookie对象
        Cookie cookie = new Cookie("key","value");

        //2.通知客户端保存cookie 这一步很重要 不可缺少
        resp.addCookie(cookie);

        resp.getWriter().write("cookie创建成功");
    }
~~~

### Cookie的key与value的获取

①getName()方法返回cookie的key值

②getValue()方法返回cookie的value值

~~~java
protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Cookie[] cookies = req.getCookies();
        for (Cookie cookie : cookies) {
            //getName()方法返回cookie的key值
            //getValue()方法返回cookie的value值
            resp.getWriter().write("cookie的key是：" + cookie.getName() + ",cookie的value是：" + cookie.getValue() + "\n");
        }


        Cookie test = CookieUtils.findCookie("key", cookies);

        if (test != null){
            resp.getWriter().write("找到了需要的cookie");
        }

    }
~~~

### Cookie值的更新

~~~java
protected void updateCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //方案一
        //1.创建与被修改的同名参数cookie
        //2.同时利用构造器赋予新的cookie的新值
        Cookie cookie = new Cookie("key","newValue");

        //3.resp.addCookie()方法通知客户端接受和保存cookie
        resp.addCookie(cookie);
        resp.getWriter().write("cookie名字为key的值已被修改");

        //方案二
        //1.查找到需要修改的cookie对象
        Cookie[] cookies = req.getCookies();
        Cookie key = CookieUtils.findCookie("key", cookies);

        if (key != null){
            //2.利用setValue()方法修改cookie对象+的值
            key.setValue("test");

            //3.resp.addCookie()方法通知客户端接受和保存cookie
            resp.addCookie(key);
        }
    }
~~~

### Cookie的生命控制

**生命控制是指管理cookie在什么时候被销毁(删除)**

setMaxAge(long expiry)；以秒为单位

- 参数为正数，表示在指定的秒数后过期
- 参数为负数，表示若浏览器关闭则cookie被删除
- 参数为0，表示cookie被马上删除

~~~java
protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Cookie cookie = new Cookie("default","default");
        cookie.setMaxAge(-1); //设置存活时间

        resp.addCookie(cookie);
    }
~~~

### Cookie有效路径Path

**Path可以有效的决定那些cookie发送给服务器**，其通过请求的地址进行过滤的

~~~java
protected void pathTest(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Cookie cookie = new Cookie("path1", "path1");
        //req.getContextPath() 获取工程路径
        cookie.setPath(req.getContextPath() + "/abc"); //===》得到路径为.../工程路径/abc

        resp.addCookie(cookie);

        resp.getWriter().write("创建了工程路径的path");
    }
~~~

### 测试cookie免密登录

~~~java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");
        String password = req.getParameter("password");

        if ("test".equals(username) && "test".equals(password)){
            //成功
            Cookie cookie = new Cookie("username", username);
            cookie.setMaxAge(60 * 60);//设置cookie存活时间

            //通知客户端保存cookie
            resp.addCookie(cookie);

            System.out.println("登录成功");
        }else {
            System.out.println("登陆失败");
        }
    }
~~~

---

## Session会话

session是一个接口(HttpSession)，用于维护一个客户端和一个服务器保持关联的一种技术。

**session会话中，经常用于保存用户登录之后的信息**

### Session的创建和获取

**req.getSession()；首次调用是创建，除此之外都是调用先前创建好的session会话对象**

**isNew()方法用于判断session会话对象是否为新创建的，true表示新创建，false表示不是新创建**

每个会话都有一个唯一的id号，**getId()方法用于获取此id值**

~~~java
protected void setSession(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //session会话对象的设置
        req.getSession().setAttribute("key","value");    
    }

    protected void getSession(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ////session会话对象的获取
        Object key = req.getSession().getAttribute("key");
    }

    protected void createSession(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //创建session会话对象
        HttpSession session = req.getSession();

        //判断session会话对象是否为新
        boolean aNew = session.isNew();

        //获取session对象的唯一标识id值
        String id = session.getId(); 
    }
~~~

### Session生命周期的控制

public void setMaxinactive(int interval)；设置seesion的存活时间(以秒为单位)，超过此时长session就被销毁

- 参数为正数表示超时时长，参数为负数表示此seesion永不超时

public int getMaxinactive()；获取seesion的存活时间

session默认超时时长为30min，在tomcat的配置中已被默认设置，可通过配置工程的web.xml文件修改成指定默认时长

**public void invalidate() 让当前 Session 会话马上超时无效。**

- 超时时长是指客户端两次请求的最大间隔时长

```java
protected void setSessionTime(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    	//session默认超时时长为30min
        int maxInactiveInterval = session.getMaxInactiveInterval(); //1800秒
    
        //设置session的超时时长
        HttpSession session = req.getSession();
        session.setMaxInactiveInterval(3);
       
    }
```

![session技术的底层实现原理](https://s1.ax1x.com/2022/04/02/qIQmod.png)

​	![验证码底层实现原理](https://s1.ax1x.com/2022/04/02/qIQKJI.png)

---

## Filter过滤器

Filter是JavaEE三大组件之一，**其主要用于拦截请求，过滤响应**

拦截请求的应用场景：1.权限检查 2.日记操作 3.事务管理

演示举例：

~~~java
public class FilterTest implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {   
    }
    /**
     * Description : 专用于拦截请求，过滤响应，实现权限检查
     * @date 2022/3/30
     * @time 23:58
     * @user hcxs1986
     * @param servletRequest
     * @param servletResponse
     * @param filterChain
     * @return void
     **/
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        
        HttpSession session = httpServletRequest.getSession();
        
        Object user = session.getAttribute("user");
        if (user == null){
            servletRequest.getRequestDispatcher("/login.jsp").forward(servletRequest,servletResponse);
            return;
        }else {
            //让程序继续访问目标资源
            //最重要的一步 不可缺少 否则网页空白不执行任何操作
            filterChain.doFilter(servletRequest,servletResponse);
        }
    }
    @Override
    public void destroy() {
    }
}
~~~

xml配置文件

~~~xml
<!--   filter标签用于配置一个Filter过滤器，其内部配置与Servlet没区别 -->
        <filter>
            <filter-name>FilterTest</filter-name>
            <filter-class>filter.FilterTest</filter-class>
        </filter>

    <!-- filter-mapping标签配置filter过滤器的拦截路径   -->
    <filter-mapping>
        <!--   filter-name表示当前路径给哪个filter过滤器使用     -->
        <filter-name>FilterTest</filter-name>
        <!--   / 表示请求地址为：http://ip:port/工程路径/ 映射到IDEA的web目录     -->
<!--   /admin/* 表示请求地址为：http://ip:port/工程路径/admin/*  同时代表拦截此admin文件路径下的所有内容 -->
        <url-pattern>/admin/*</url-pattern>
    </filter-mapping>
~~~

### Filter的生命周期

生命周期与Servlet相似

①Filter的构造器方法

②Filter的init初始化方法

③Filter的doFilter()方法

④ilter的destory()方法

- ①、②步在web工程启动的时候执行(Filter已经创建)
- ③在拦截到请求时执行
- ④停止web工程时执行，同时Filter过滤器也不再存在

### FilterConfig类

**Filter过滤器的配置文件类**，在Tomcat服务器每次创建Filter的同时就会生成此类，保存了Filter的配置信息

FilterConfig 类的作用是获取 filter 过滤器的配置内容 

1、获取 Filter 的名称 filter-name 的内容 

2、获取在 Filter 中配置的 init-param 初始化参数 

3、获取 ServletContext 对象

~~~java
System.out.println("2.Filter 的 init(FilterConfig filterConfig)初始化");
// 1、获取 Filter 的名称 filter-name 的内容
System.out.println("filter-name 的值是：" + filterConfig.getFilterName());
// 2、获取在 web.xml 中配置的 init-param 初始化参数
System.out.println("初始化参数 username 的值是：" + filterConfig.getInitParameter("username"));
System.out.println("初始化参数 url 的值是：" + filterConfig.getInitParameter("url"));
// 3、获取 ServletContext 对象
System.out.println(filterConfig.getServletContext());
~~~

### FilterChain 过滤器链

FilterChain即多个过滤器同时一起工作

- **多个Filter过滤器的执行的先后顺序由它们在web.xml中从上到下的配置顺序决定的**

![FilterChain执行示意图](https://s1.ax1x.com/2022/04/02/qIQdWq.png)

### Filter的拦截路径

**Filter 过滤器它只关心请求的地址是否匹配，不关心请求的资源是否存在！！！**

- 精确匹配

  ~~~xml
  <url-pattern>*.html</url-pattern>
  ~~~

  以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/target.jsp

- 目录匹配

  ~~~xml
  <url-pattern>/admin/*</url-pattern>
  ~~~

  以上配置的路径，表示请求地址必须为：http://ip:port/工程路径/admin/*

- 后缀名匹配

  ~~~xml
  <url-pattern>*.html</url-pattern>
  ~~~

  以上配置的路径，表示请求地址必须以.html 结尾才会拦截到

---

## ThreadLocal

可用用于解决多线程的数据安全问题

ThreadLocal 的特点：

1、ThreadLocal 可以为当前线程关联一个数据。（它可以像 Map 一样存取数据，key 为当前线程） 

2、每一个 ThreadLocal 对象，只能为当前线程关联一个数据，如果要为当前线程关联多个数据，就需要使用多个 ThreadLocal 对象实例。 

3、每个 ThreadLocal 对象实例定义的时候，一般都是 static 类型 

4、ThreadLocal 中保存数据，在线程销毁后。会由 JVM 虚拟自动释放

---

## JSON

**JSON(javaScript Object Notation)是一种轻量级的数据交换格式，用于客户端和服务器之间的业务数据的传递**

JSON的定义：由键值对组成，并且由大括号包裹。每个键由引号引起来，键和值之间使用冒号进行分隔， 多组键值对之间进行逗号进行分隔。

### json的变量格式

~~~html
// json的定义
var jsonObj = {
			"key1":10,
			'key2':"test",
  			key3:false,
 			"key4":[10,"test",false],
			"key5":{"key5_1":test},
			'key6':[{"key6_1":1},{"key6_2":2}]
				};
alert(typeof(jsonObj));//数据类型是object，也就是说json就是一个对象
~~~

### json的访问

json 本身是一个对象。

json 中的 key 我们可以理解为是对象中的一个属性。 

**json 中的 key 访问就跟访问对象的属性一样： json 对象.key**

~~~html
			// json的访问
			console.log(jsonObj.key1);
			
			for (var i = 0; i < jsonObj.key4.length; i++) {
				alert(jsonObj.key4[i]);
			}
			
			alert(jsonObj.key5.key5_1);
			
			var k = jsonObj.key6[0];
			alert(k.key6_1);
~~~

### json在javaScript中的常用方法

- javascript对象表示法（JSON）格式是从javascript派生的。

json的两种形式：

①以对象的形式存在，称为json对象

②以字符串的形式存在，称为json字符串

- 一般操作 json 中的数据的时候，需要 json 对象的格式。 
- 一般在客户端和服务器之间进行数据交换的时候，使用 json 字符串

常用方法：

①**JSON.stringify()  把 json 对象转换成为 json 字符串**

②**JSON.parse() 	 把 json 字符串转换成为 json 对象**

~~~html
//JSON.stringify()  把 json 对象转换成为 json 字符串
var jsonObjString = JSON.stringify(jsonObj);
alert(jsonObjString);

//JSON.parse() 	 把 json 字符串转换成为 json 对象
var jsonObj2 = JSON.parse(jsonObjString);
alert(jsonObj2.key1);
~~~

### JSON在java中的应用

1.javaBean和json的转换

~~~java
@Test
public void javaBeanToJson(){
    BeanClass b1 = new BeanClass(1, "test");
    //创建gson对象
    Gson gson = new Gson();
    //toJson()方法将任意java对象转换为json字符串
    String bToJsonSting = gson.toJson(b1);
    System.out.println(bToJsonSting);
    //fromJson()方法把json字符串转换为任意java对象
    //第一个参数是json字符串，第二个参数是转换回去的java对象类型
    gson.fromJson(bToJsonSting,BeanClass.class);
}
~~~

2.List和json的转换

~~~java
@Test
public void listToJson(){
    List<BeanClass> b2 = new ArrayList<>();
    b2.add(new BeanClass(2,"b2"));
    b2.add(new BeanClass(3,"b3"));
    //创建gson对象
    Gson gson = new Gson();
    //toJson()方法将任意list集合对象转换为json字符串
    String b2ToJsonSting = gson.toJson(b2);
    System.out.println(b2ToJsonSting);
    //fromJson()方法把json字符串转换为任意list集合对象
    //第一个参数是json字符串，第二个参数是转换回去的java对象类型
    gson.fromJson(b2ToJsonSting,new BeanClassListType().getRawType());
}
//为了防止集合对象出现类型转换异常，考虑new个新类去继承gosn的jar包中的TypeToken
//其内泛型可用List<BeanClass>也可用具体的实现类类型ArrayList<BeanClass>
//也可以采用匿名内部类的写法
public class BeanClassListType extends TypeToken<List<BeanClass>> {
}
~~~

3.map和json的转换

~~~java
@Test
    public void mapToJson(){
        Map<Integer,BeanClass> b3 = new HashMap<>();
        b3.put(1,new BeanClass(4,"b4"));
        b3.put(2,new BeanClass(5,"b5"));
        //创建gson对象
        Gson gson = new Gson();
        //toJson()方法将任意Map集合对象转换为json字符串
        String b3ToJsonString = gson.toJson(b3);
        System.out.println(b3ToJsonString);
        //fromJson()方法把json字符串转换为任意Map集合对象
        //第一个参数是json字符串，第二个参数是转换回去的java对象类型
       	//参数二是匿名内部类
        gson.fromJson(b3ToJsonString, new TypeToken<HashMap<Integer,BeanClass>>(){}.getRawType());
    }
~~~

---

## AJAX

AJAX 即“Asynchronous Javascript And XML”（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发 技术。

- **ajax 是一种浏览器通过 js 异步发起请求，局部更新页面的技术。 **

Ajax 请求的局部更新，浏览器地址栏不会发生变化，局部更新不会舍弃原来页面的内容

### 原生JS的ajax请求示例

~~~html
<script type="text/javascript">
			function ajaxRequest() {
// 				1、我们首先要创建XMLHttpRequest 
				var xmlHttpRequest = new XMLHttpRequest();
// 				2、调用open方法设置请求参数
				xmlHttpRequest.open("GET","http://localhost:8080/JSON_ajax_i18n/ajaxServlet?action=javaScriptAjax",true);
// 				3、调用send方法发送请求
                //给onreadystatechange属性绑定函数
				xmlHttpRequest.onreadystatechange = function () {
                    //只要在status状态为200，readyState为4的时候才能触发onreadystatechange属性的函数
					if (xmlHttpRequest.status == 200 && xmlHttpRequest.readyState == 4){
                        //通过dom对象查找指定id的标签并把返回内容设置为标签的文本内容
                        var jsonObj = JSON.parse(xmlHttpRequest.responseText);

						document.getElementById("div01").innerHTML = "编号：" + jsonObj.id + ",姓名：" + jsonObj.name;
					}
				}
// 				4、在send方法前绑定onreadystatechange事件，处理请求完成后的操作。
				xmlHttpRequest.send();
			}
		</script>
~~~

### Jquery的ajax请求

- **$.ajax 方法 **
  - url 表示请求的地址 
  - type 表示请求的类型 GET 或 POST 请求 
  - data 表示发送给服务器的数据 格式有两种： 一：name=value&name=value 二：{key:value} 
  - success 请求成功，响应的回调函数(方法)
  - dataType 响应的数据类型 常用的数据类型有： text 表示纯文本 xml 表示 xml 数据 json 表示 json 对象

~~~html
$(function(){
// ajax请求
$("#ajaxBtn").click(function(){
$.ajax({
url:"http://localhost:8080/JSON_ajax_i18n/ajaxServlet",
data:"action=jqueryAjax",
//data:{action:"jqueryAjax"},
type:"get",
success:function (data) {//此处data是服务器返回的数据，是json字符串
// var jsonObj = JSON.parse(data);
$("#msg").html("编号：" + data.id + ",姓名" + data.name);
},
dataType:"json"
});
~~~

- **$.get 方法和$.post 方法**
  - url 请求的 url 地址 
  - data 发送的数据 
  - callback 成功的回调函数 
  - type 返回的数据类型

~~~html
// ajax--get请求
//不必像ajax一样逐个写出，只需要填充相应的值就行
$("#getBtn").click(function(){
//$.getJSON(url,data,callback)
$.get("http://localhost:8080/JSON_ajax_i18n/ajaxServlet", {action:"jqueryGet"}, function (data) {
$("#msg").html("get 编号：" + data.id + ",姓名" + data.name);
}, "json");
});

// ajax--post请求
$("#postBtn").click(function(){
// post请求
$.post("http://localhost:8080/JSON_ajax_i18n/ajaxServlet", "action=jqueryPost", function (data) {
$("#msg").html("post 编号：" + data.id + ",姓名" + data.name);
}, "json");
});
~~~

- **$.getJSON方法**

  替代上方两种

  - url 请求的url 地址 
  - data 发送给服务器的数据 
  - callback 成功的回调函数

~~~html
// ajax--getJson请求
$("#getJSONBtn").click(function(){
//$.getJSON(url,data,callback)
$.getJSON("http://localhost:8080/JSON_ajax_i18n/ajaxServlet", "action=jqueryJSON",function (data) {
$("#msg").html("getJson 编号：" + data.id + ",姓名" + data.name);
})

});
~~~

- serialize()方法

  - serialize()可以把表单中所有表单项的内容都获取到，并以 name=value&name=value 的形式进行

```html
 // ajax请求
 $("#submit").click(function(){
 // 把参数序列化
 $.getJSON("http://localhost:8080/JSON_ajax_i18n/ajaxServlet", "action=jquerySerialize&" + $("#form01").serialize(),function (data) {
 $("#msg").html("Serialize 编号：" + data.id + ",姓名" + data.name);
 })
  });
```

---

## i18n国际化

国际化（Internationalization）指的是同一个网站可以支持多种不同的语言，以方便不同国家，不同语种的用户访问。

- 国际化的英文 Internationalization，但是由于拼写过长，起了个一个简单的写法叫做 I18N，代表的是 Internationalization 这个单词，

  以 I 开头，以 N 结尾，而中间是 18 个字母，所以简写为 I18N。以后说 I18N 和国际化是一个东西

### 国际化相关要素

![国际化相关要素](https://s1.ax1x.com/2022/04/02/qIQ6w4.png)

- 国际化资源 properties 测试、通过请求头国际化页面、通过请求头国际化页面、JSTL 标签库实现国际化
- 需要可到javaWeb包下JSON_ajax_i18n模块查看