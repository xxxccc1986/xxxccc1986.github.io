---
title: 异常问题的处理
date: 2022-04-03 21:47:30
updated: 2022-04-03 21:47:30
tags:
  - Exception
categories:
  - JavaSE
cover: https://w.wallhaven.cc/full/e7/wallhaven-e7q8dr.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/04/03/Exception/
---

# 学习过程中异常问题的处理

1.**java.lang.NumberFormatException: For input string: ""**

![](https://s1.ax1x.com/2022/04/03/qHE4mQ.png)

抛异常处代码：

~~~java
String add = moneyAdd.toString();
        int num = Integer.parseInt(add);
            count += num;
~~~

原因是：

在调用StringUtils.split()，Integer.parseInt（），Long.parseLong()等方法时，不管传入的参数的值是什么，都能进入不为null或“”的判断中，

然后就运行执行下面的代码，就可能出现 java.lang.NumberFormatException: For input string: "null"的异常。

- 解决办法：在转换类型之前进行不为空值或不为空字符串的判断即可

~~~java
//解决方法
String add = moneyAdd.toString();
        if (add != null && !add.equals("")){
            int i = Integer.parseInt(add);
            count += i;
        }
~~~

2.**Exception in thread "main" java.net.SocketException: Connection reset**

![](https://s1.ax1x.com/2022/04/03/qHETkn.png)

socket.shutdownOutput方法说明，shutdownInput方法同理

①在客户端或者服务端通过socket.shutdownOutput()都是单向关闭的，

即关闭客户端的输出流并不会关闭服务端的输出流，所以是一种单方向的关闭流；

②通过socket.shutdownOutput()关闭输出流，但socket仍然是连接状态，连接并未关闭

③如果直接关闭输入或者输出流，即：in.close()或者out.close()，会直接关闭socket

3.**Exception in thread "main" java.lang.NullPointerException**

![](https://s1.ax1x.com/2022/04/03/qHEO6U.png)

抛异常的代码块：

~~~java
// 此处必须使用static修饰静态代码块
 static  {
        try {
            Properties pro = new Properties();
            InputStream resource = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
            pro.load(resource);
            source = DruidDataSourceFactory.createDataSource(pro);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

class UtilsTest {
    @Test
    public void test(){
        JDBCUtils.getConnection();
    }
}
~~~

关于Idea德鲁伊数据库连接池空指针异常问题 ，找了许久发现在代码块中使用的是**非静态修饰**

原因是：

对于德鲁伊创建池子使用的代码块必须是静态代码块，静态代码块随着类的加载而执行，也就是说在类加载完成的时候数据库连接池内的数据连接就创建好了，

但很不幸 ，当时使用了非静态代码块，导致空指针的出现，因为非静态代码块是在对象创建的时候才会执行完成，像上面直接通过类调用静态方法是没有

加载过非静态代码块的，因此此时数据库连接池还是为null的。

采用静态代码块修饰，解决空指针问题

![](https://s1.ax1x.com/2022/04/03/qHEx0J.png)