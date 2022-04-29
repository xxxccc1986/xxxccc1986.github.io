---
title: SSM-CRUD整合
date: 2022-04-22 10:16:02
updated: 2022-04-22 10:16:02
tags:
  - Spring
  - SpringMVC
  - MyBatis
categories:
  - 框架
keywords: SSM-CRUD整合
cover: https://w.wallhaven.cc/full/o3/wallhaven-o3mp39.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/04/22/SSM-CRUD
---


## Demo简介

### 技术点

开发环境：SSM框架+MySql8.0+JDK1.8+Tomcat8.0+Maven3.6+bootstrap框架

开发工具：IDEA2020+SQLyog

- 实现网站基础增删改查的操作

### 功能点

- 分页功能

- 实现数据校验

  通过 jquery前端 + JSR303后端 双重校验 

- 局部页面更新

  使用ajax发起异步请求

- Rest风格的URI(统一资源标识符)

​		实现访问同一个地址，根据不同的请求方式实现对资源的不同操作

### 基础环境搭建

1. 使用IDEA创建Maven工程，并将Maven的打包方式设置为war

2. 引入相关依赖

   spring、springMVC、mabatis、数据库连接池、mysql驱动包、其他

3. 引入bootstap前端框架

4. 编写SSM整合的关键配置文件

   web.xml、spring、springMVC

- web.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--  1.启动Spring的容器  -->
    <!-- needed for ContextLoaderListener -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>

    <!-- Bootstraps the root web application context before servlet initialization -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--  配置字符编码过滤器 -->
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

    <!--   配置HiddenHttpMethodFilter -->
    <!--  将get或者post请求经过过滤器转换为指定的put或者delete请求  -->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
    <!--   2.springMVC的前端控制器 -->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
~~~

- spring的配置文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/tx
                           http://www.springframework.org/schema/tx/spring-tx.xsd">

        <context:component-scan base-package="ssm_crud.service"/>
        <!-- Spring配置文件的核心点（数据源、与mybatis的整合，事务控制） -->
        <context:component-scan base-package="ssm_crud" use-default-filters="false">
            <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        </context:component-scan>

        <context:property-placeholder location="classpath:jdbc.properties"/>

        <!--  Spring的配置文件，这里主要配置和逻辑业务有关的  -->
        <!--  数据源，事务控制，xxx -->
        <bean id="pooledDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <property name="driverClass" value="${jdbc.driverClass}"/>
            <property name="jdbcUrl" value="${jdbc.jdbcUrl}"/>
            <property name="user" value="${jdbc.user}"/>
            <property name="password" value="${jdbc.password}"/>
        </bean>

        <!--  配置mybatis的整合  -->
        <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
            <!--  指定mybatis的全局核心配置文件位置    -->
                <property name="configLocation" value="classpath:mybatis-config.xml"/>
                <property name="dataSource" ref="pooledDataSource"/>
                <!-- 指定mybatis的映射配置文件位置          -->
                <property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
        </bean>
        <!--   配置扫描器，将mybatis接口的实现类加入到ioc容器中    -->
        <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
            <!--     扫描所有dao接口的实现，加入到ioc容器中       -->
            <property name="basePackage" value="ssm_crud.dao"/>
        </bean>

        <!--   配置一个可以执行批量的sqlSession     -->
        <bean id="sessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
            <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"/>
            <constructor-arg name="executorType" value="BATCH"/>
        </bean>

        <!--   事务控制配置     -->
        <!--   1.开启事务控制器     -->
        <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
            <!--     事务管理器控制的数据源       -->
            <property name="dataSource" ref="pooledDataSource"/>
        </bean>

        <!--    配置通知：可以理解为对切入点实现的具体操作    -->
        <tx:advice id="txadvice">
            <tx:attributes>
                <!--  所有方法都是事务  -->
                <tx:method name="*"/>
                <!--  以get开始的所有方法  -->
                <tx:method name="get*" read-only="true"/>
            </tx:attributes>
        </tx:advice>

        <!--   增强的配置   -->
        <aop:config>
            <!--  配置切入点
            id：切入点的标识，下方切面所需要
            expression切入点表达式：表示将通知应用再哪个类的哪个方法下
            此处代表的是通知将应用在ssm_crud.service包下的所有方法
            -->
            <aop:pointcut id="txPoint" expression="execution(* ssm_crud.service..*(..))"/>
            <!--      配置切面
                 advice-ref：通知的标识
                 pointcut-ref：切入点的标识id
                 切面是指将通知应用到切入点的过程
                 此处表示将标识为txadvice的通知的应用到切入点id为txPoint的过程
             -->
            <aop:advisor advice-ref="txadvice" pointcut-ref="txPoint"/>
        </aop:config>

</beans>
~~~

- springMVC的配置

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

        <!-- springMVC的配置文件包含网站跳转逻辑的控制   -->
        <!-- 1.开启组件扫描       -->
        <context:component-scan base-package="ssm_crud" use-default-filters="false">
            <!--      只扫描控制器      -->
            <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        </context:component-scan>

        <!--    2.视图解析器    -->
        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/templates/" />
            <property name="suffix" value=".jsp"/>
        </bean>

        <!--   3.mvc视图控制器     -->
<!--        <mvc:view-controller path="/" view-name="index"/>-->

        <!--   4.静态资源的默认处理器     -->
        <mvc:default-servlet-handler/>

        <!--    开启mvc注解驱动-->
        <mvc:annotation-driven/>
</beans>
~~~

## 查询功能

1.直接让index界面向服务器发起查询的ajax请求

2.控制层组件处理index的请求，使用注解@ResponseBody将查询方法的返回值作为响应体返回

​	新造一个Message类给予相关提示，里面包含了状态码、提示信息以及用户想要的数据

3.在jackson依赖的作用下直接将数据转换为json字符串回传给客户端

4.客户端将接受到的json字符串解析为json对象

5.使用此json对象对页面需要的信息进行填充

- 解析并显示员工数据、解析并显示分页信息、解析并显示分页条都是在第4、5步完成的

  使用的是JQuery的each函数遍历封装在json对象中的分页条和员工信息，

  通过从外到内先利用$()和addClass()创建相应的标签及设置标签样式，

  再通过append()方法进行内容设置以及attr()方法进行自定义属性的设置。
  
- 利用分页插件封装的数据给分页条绑定点击的ajax事件

![查询页面](https://s1.ax1x.com/2022/04/22/LgsmKU.png)

---

## 增加功能

1.采用bootstrap框架提供的模拟框提供填写的表单

2.为新增选项绑定单击事件，发起ajax请求，查询所有部门的姓名并显示在下拉框中

3.为提交选项绑定单击事件，在发起保存请求之前对添加的数据进行**前端JQuery校验和ajax请求后端校验邮箱是否可用**，

​	表单数据使用JQuery的serialize()方法实现序列化并发送给服务器，**针对重要的数据后端使用JSR303**完成双端验证

4.提交完成后由bootstrap框架提供的$('#myModal').modal('hide')自动关闭模拟框

5.模拟框关闭后再次发起ajax请求访问最后一页

- 第5步没有采用pageHelper提供的限制只能访问最大页数的功能，仅是在js中定义一个全局变量，记录总记录数
- 根据查询接口返回的pageInfo封装的数据，判断当总记录数 / 每页显示数据 是否大于 0 来决定全局变量是否需要 +1

![新增员工](https://s1.ax1x.com/2022/04/22/Lgsub4.png)

![校验邮箱是否符合规则](https://s1.ax1x.com/2022/04/22/LgsQa9.png)

---

## 修改功能

1.采用bootstrap框架提供的模拟框提供填写的表单

2.为新增选项绑定单击事件，发起ajax请求查询指定员工的信息，回显在表单中

3.为提交选项绑定单击事件，在发起修改请求之前对添加的数据进行**前端JQuery校验校验邮箱是否符合规则**

​	校验完成之后表单数据使用JQuery的serialize()方法实现序列化并发送给服务器，

4.提交完成后由bootstrap框架提供的$('#myModal').modal('hide')自动关闭模拟框

5..模拟框关闭后再次发起ajax请求访问数据修改时所在的页码

- 在js中定义一个全局变量，记录当前页数

![修改员工的校验](https://s1.ax1x.com/2022/04/22/Lgsl5R.png)

---

## 删除功能

1.为删除按钮绑定单击事件，根据confirm()函数的选择状态决定是否发送ajax请求删除数据

2.添加全选或者全不选的功能

​	$(".check_item:checked").length可以获取状态处于选中的选项框的个数

​	**选择prop()函数可以获取选项框的状态(true或者false)，不使用attr()函数的原因是只会返回(undefined)**

3.给第一行的删除绑定统一删除的功能，通过$(this).parents("tr").find("td:eq(1)").text()寻找当前dom对象的祖父元素中的tr元素后

​	再获取子元素中的第二个td元素的文本值，封装成json字符串由ajax请求发往服务器

4.服务器利用**jackson**依赖转换字符串为map集合对象，获取map集合中的被删除id并将利用String的split()分割每一个数字获取一个String数组

5.将String数组的元素逐个遍历转换为数字调用service层进行删除

![删除功能展示](https://s1.ax1x.com/2022/04/22/Lgs88x.png)

---

## SSM-CRUD总结

![项目流程示意图](https://s1.ax1x.com/2022/04/22/LgsG26.png)
