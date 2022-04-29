---
title: MyBatis
date: 2022-04-22 09:25:02
updated: 2022-04-22 09:25:02
tags:
  - MyBatis
categories:
  - 框架
keywords: MyBatis
cover: https://w.wallhaven.cc/full/28/wallhaven-28xmr9.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/04/22/MyBatis
---


## MyBatis框架

- MyBatis是一个基于Java的持久层框架。持久层框架包括SQL Maps和Data Access Objects（DAO）
- MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录
- MyBatis 是一个**半自动**的ORM（Object Relation Mapping）框架
- **核心配置文件主要设置连接数据库的信息和mybatis的全局配置信息，映射配置文件主要用于写sql语句**

## 前置操作说明

### 创建mapper接口

>  MyBatis中的mapper接口相当于之前具体操作某张表的dao。但是mapper仅仅是接口，我们不需要提供实现类，因为mybatis框架支持面向接口编程

~~~java
public interface UserMapper {
    //表--实体类--mapper接口--映射文件
    int insertUser();
}
~~~

### 创建MyBatis的映射文件

相关概念：ORM（Object Relationship Mapping）对象关系映射。

-  对象：Java的实体类对象 
- 关系：关系型数据库 
- 映射：二者之间的对应关系

| Java概念 | 数据库概念 |
| :------- | :--------- |
| 类       | 表         |
| 属性     | 字段/列    |
| 对象     | 记录/行    |

- MyBatis面向接口编程的两个一致

  1.映射文件的nameSpace和mapper接口的全类名保持一致

  2.映射文件中sql语句的id 要和mapper接口中的方法名保持一致

因此，**当使用mapper接口的对象调用接口的方法时，会根据接口名找到相应的映射文件，再根据调用的方法名找到相应的sql语句并执行**

~~~xml
<mapper namespace="mybatis.mapper.UserMapper">
    <!--    int insertUser()-->
    <insert id="insertUser">
        insert into t_user values(null,'test','test',10,'男','test@qq.com')
    </insert>
</mapper>
~~~

### 测试功能

- SqlSession：**代表Java程序和数据库之间的会话。**
- SqlSessionFactory：是“生产”SqlSession的“工厂”。
- 工厂模式：将创建所需对象的过程进行封装，并返回所需对象

~~~java
/**
     * Description : TODO
     * sqlSession默认设置不自动提交事务，若需要自动提交事务
     * 则需要设置sqlSessionFactory.openSession()方法括号内的形参为true
     * @date 2022/4/14
     * @return void
     **/
    @Test
    public void test() throws IOException {
        //加载核心配置文件
        InputStream stream = Resources.getResourceAsStream("mybatis-config.xml");
        //获取SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        //获取SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(stream);
        //获取SqlSession
        //形参内为true则代表设置为自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        //底层使用了代理模式，返回此接口的实现类对象
        //获取mapper接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        
        //测试功能
        int result = mapper.insertUser();
        System.out.println("result:" + result);
        //提交事务
//        sqlSession.commit();
    }
~~~

### log4j日志记录

需要记录日志文件需要引入log4j的依赖并进行配置，log4j的配置文件名为log4j.xml，存放的位置是src/main/resources目录下

> 日志的级别 FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试) 
>
> 从左到右打印的内容越来越详细

---

## MyBatis的CRUD操作

1.增

~~~xml
<!--    int insertUser()-->
    <insert id="insertUser">
        insert into t_user values(null,'test','test',10,'男','test@qq.com');
    </insert>
~~~

2.删

~~~xml
<!--    int deleteUser(@Param("id") Integer id)-->
    <delete id="deleteUser">
        delete from t_user where id = #{id};
    </delete>
~~~

3.改

~~~xml
<!--    int updateUser(@Param("id") Integer id);-->
    <update id="updateUser">
        update t_user set username = '测试名字' where id = #{id};
    </update>
~~~

4.查

- 查询功能必须设置resultType或resultMap属性
  - resultType：设置默认的映射关系，用于属性名和表中字段名一致的情况
  - resultMap ：设置自定义的映射关系，用于一对多或多对一或字段名和属性名不一致的情况
  
- ①查询的数据只有**一条**，可以用**实体类对象或者集合(list、map)**接收

  ②若查询的数据有**多条**只能用**集合(list、map)来接收，否则抛异常**

  也可以在mapper接口上添加**@MapKey注解**方式来接收

  此方式以某个字段作为键，以每条数据转换的map集合作为值，放在同一个map集合中

~~~java
//@MapKey的value值必须是一个唯一标识不能重复的字段
    @MapKey("id")
    Map<String,Object> getAllUserToMap();
//结果演示：
//{{3={password=test, sex=男, id=3, age=10, email=test@qq.com, username=test}, 
//5={password=123456, sex=男, id=5, age=20, email=admin@qq.com, username=admin}}
~~~

- 查询**特殊值**的情况，在MyBatis中**设置了默认的类型别名**

  java.lang.Integer ---> int,integer

  int --->_int,_integer

  Map ---> map

  String ---> string

~~~xml
<!--    User getUserById(@Param("id") Integer id)-->
    <select id="getUserById" resultType="user">
        select * from t_user where id = #{id};
    </select>
<!--    List<User> GetAllUser()-->
    <select id="GetAllUser" resultType="user">
        select * from t_user;
    </select>

<!--    Integer getUserCount()-->
<!--    此处的resultType填写的可以是全类名也可以MyBatis中设置的默认的类型别名 _integer等等-->
    <select id="getUserCount" resultType="java.lang.Integer">
        select count(*) from t_user;
    </select>

<!--    Map<String,Object> getUserByIdToMap(@Param("id") Integer id);-->
    <select id="getUserByIdToMap" resultType="map">
        select * from t_user where id = #{id};
<!--  结果：{password=test, sex=男, id=3, age=10, email=test@qq.com, username=test}-->
    </select>

<!--    List<Map<String,Object>> getAllUserToList();-->
    <select id="getAllUserToList" resultType="map">
        select * from t_user;
<!--  结果为List数组，值是map类型的键值对：
	[{password=test, sex=男, id=3, age=10, email=test@qq.com, username=test}, 
	{password=123456, sex=男, id=5, age=20, email=admin@qq.com, username=admin}]-->
    </select>
~~~

---

## 核心配置文件解析

- 核心配置文件中的标签必须按照固定的顺序配置：

  properties?,settings?,typeAliases?,typeHandlers?,
  
  objectFactory?,objectWrapperFactory?,reflectorFactory?,
  
  plugins?,environments?,databaseIdProvider?,mappers?

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--  引入数据库的数据源配置文件，可以${属性名}的方式访问属性值  -->
    <properties resource="jdbc.properties"/>

    <!--  设置类型别名  -->
    <typeAliases>
        <!--   设置单个bean类的别名     -->
        <!--    typeAlias：设置某个类的别名
                属性：
                    type：设置需要设置别名的类型
                    alias：设置某个类型的别名
                        ①设置了alias属性，那么该类型，则只能使用指定的别名
                        ②若没有设置了alias属性，则该类型拥有默认的别名，即其类名，且不区分大小写
                -->
        <typeAlias type="mybatis.bean.User" alias="User"/>
<!--     在typeAlias和package之间，通常使用 package ，其效率更高 -->
<!--     以包为单位，将包下的所有类型设置默认的类型别名，即类名且不区分大小写   -->
        <package name="mybatis.bean"/>
    </typeAliases>

<!--  配置连接数据库的环境  -->
    <!--
        environments：设置多个连接数据库的环境
        属性：
        default：设置默认使用的环境的id
    -->
    <environments default="development">
        <!--
        environment：设置具体的连接数据库的环境信息
        属性：
        id：设置环境的唯一标识，可通过environments标签中的default设置某一个环境的id，表示默认使用的环境
        -->
        <environment id="development">
            <!--
            transactionManager：设置事务管理方式
            属性：
            type：设置事务管理方式，type="JDBC|MANAGED"
            type="JDBC"：设置当前环境的事务管理都必须手动处理
            type="MANAGED"：设置事务被管理，例如spring中的AOP
            -->
            <transactionManager type="JDBC"/>
            <!--
            dataSource：设置数据源
            属性：
            type：设置数据源的类型，type="POOLED|UNPOOLED|JNDI"
            type="POOLED"：使用数据库连接池，即会将创建的连接进行缓存，下次使用可以从
            缓存中直接获取，不需要重新创建
            type="UNPOOLED"：不使用数据库连接池，即每次使用连接都需要重新创建
            type="JNDI"：调用上下文中的数据源
            -->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
<!--  引入映射文件  -->
    <mappers>
        <!--
        以包为单位，将包下所有的映射文件引入核心配置文件
        要求：
        1.mapper接口所在的包全路径要和映射文件所在的包全路径一致
        2.mapper接口名和映射文件的名字一致
        -->
        <package name="mybatis.mapper"/>
    </mappers>
</configuration>
~~~

---

## MyBatis获取参数值

MyBatis获取参数值的两种方式：${} 和 #{}

**${} 本质上是字符串拼接赋值，即Statement**

**#{}本质上是占位符赋值，即PreparedStatement**

### 单个字面量类型的参数

- 可以通过${}或 #{} 以任意的名称获取参数值，**但是选择使用的是${}则需要注意额外添加一对单引号**

~~~xml
<!--    User getUserByName(String username)-->
    <select id="getUserByName" resultType="User">
        <!--         select * from t_user where username = #{username};-->
                select * from t_user where username = '${username}';
    </select>
~~~

### 多个字面量类型的参数

- MyBatis会自动将这些参数放在一个map集合中，

  以arg0,arg1...为键，以参数为值；以 param1,param2...为键，以参数为值；

  因此只需要通过${}和#{}访问map集合的键就可以获取相对应的值，但需**注意${}需要手动加单引号**

![存储数据的map集合](https://s1.ax1x.com/2022/04/22/LgYeG4.png)

~~~xml
<!--    User checkLogin(String username,String password)-->
    <select id="checkLogin" resultType="User">
        <!--        select * from t_user where username = '${arg0}' and password = '${arg1}' ;-->
        select * from t_user where username = #{arg0} and password = #{param2};
    </select>
~~~

### map集合类型的参数

- 此处的键都是由自己提供的，与上面mybatis提供的并不一样

  但也是只需要通过${}和#{}访问map集合的键就可以获取相对应的值，但需**注意${}需要手动加单引号**

~~~xml
<!--    User checkLoginByMap(Map<String,Object> map);-->
    <select id="checkLoginByMap" resultType="User">
        select * from t_user where username = #{username} and password = #{password};
    </select>
~~~

### 实体类类型的参数

- 若mapper接口中的方法参数为实体类对象时 

  **此时可以使用${}和#{}，通过访问实体类对象中的属性名获取属性值，注意${}需要手动加单引号**

~~~xml
<!--    int addUser(User user)-->
    <insert id="addUser" >
        insert into t_user values(null,#{username},#{password},#{age},#{sex},#{email});
    </insert>
~~~

### 使用@Param标识参数

- 这些参数会被放在map集合中，**以@Param注解的value属性值为键，以参数为值；以 param1,param2...为键，以参数为值；**

  只需要通过${}和#{}访问map集合的键就可以获取相对应的值， 注意${}需要手动加单引号

![map集合中可用的键](https://s1.ax1x.com/2022/04/22/LgYMs1.png)

~~~xml
<!--  User checkUserByAnnotation(@Param("username") String username, @Param("password")String password);-->
    <select id="checkUserByAnnotation" resultType="User">
        select * from t_user where username = #{ttt} and password = #{password};
    </select>
~~~

---

## 特殊SQL语句

1.模糊查询

- **只能使用${}**

  若使用#{}，占位符会被解析成？，当中参数里面的一部分，而不是起填充的作用

~~~xml
<!--List<User> getUserByFuzzy()-->
    <select id="getUserByFuzzy" resultType="User">
        <!--    select * from t_user where username like '%${username}%'-->
        <!--    select * from t_user where username like concat('%',#{username},'%');-->
            select * from t_user where username like "%"#{username}"%";
         </select>
     </mapper>
~~~

2.批量删除

- **只能使用${}**

  若使用#{}，则不会有任何数据的删除，原因是解析后会自动添加单引号，而在in的()里面不允许有单引号

~~~xml
<!--    int deleteMore(@Param("ids") String ids);-->
        <delete id="deleteMore" >
            delete from t_user where id in (${ids});
        </delete>
~~~

3.动态设置表名

- **只能使用${}**

  表名不能有单引号，因此不能使用#{}

~~~xml
<!--List<User> getUserByTableName(@Param("tableName") String tableName);-->
        <select id="getUserByTableName" resultType="user">
            select * from ${tableName};
        </select>
~~~

4.添加功能获取自增的主键

- useGeneratedKeys:设置当前标签中的sql使用了自增的主键

  keyProperty：将自增的主键的值赋值给传入到映射文件中参数的某个属性

~~~xml
<!--    void insertUser(User user)      -->
    <insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
        insert into t_user values(null,#{username},#{password},#{age},#{sex},#{email});
    </insert>
~~~

---

 ## 自定义映射resultMap

### 字段和属性的映射关系

方式一：**通过为字段起别名的方式，保证和实体类中的属性名保持一致**，与之前在原生jdbc查询数据库使用方式一致

方式二：在MyBatis的核心配置文件中设置一个全局配置信息mapUnderscoreToCamelCase

​				可以在查询表中数据时，**自动将_类型的字段名转换为驼峰**

~~~xml
<!--  设置MyBatis的全局配置  -->
    <settings>
        <!--        自动将_类型的字段名转换为驼峰-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
~~~

方式三：通过resultMap设置自定义映射

~~~xml
<!--    List<Emp> getAllEmp()-->
    <!--  resultMap：设置自定义映射
    属性：
    id：表示自定义映射的唯一标识
    type：查询的数据要映射的实体类的类型
    子标签：
    id：设置主键的映射关系
    result：设置普通字段的映射关系
    -->
    <resultMap id="empResultMap" type="emp">
        <id property="eid" column="eid"/>
        <result property="empName" column="emp_name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <result property="email" column="email"/>
    </resultMap>

    <select id="getAllEmp" resultMap="empResultMap">
        select * from t_emp;
    </select>
~~~

### 多对一映射关系处理

**多对一 对应的是  对象**

方式一：级联属性赋值

- 将查出的不属于emp的字段的值，赋给Dept类对象dept对象的属性

~~~xml
<!--  多对一映射处理关系方式一：级联属性赋值  -->
    <resultMap id="dept1" type="emp">
        <id property="eid" column="eid"/>
        <result property="empName" column="emp_name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <result property="email" column="email"/>
        <result property="empName" column="emp_name"/>
        <!--级联属性赋值 -->
        <result property="dept.did" column="did"/>
        <result property="dept.deptName" column="dept_name"/>
    </resultMap>
    <select id="getEmpDeptByEid" resultMap="dept1">
        SELECT * FROM  t_emp LEFT JOIN t_dept ON t_emp.did = t_dept.`did` WHERE t_emp.eid = #{eid};
    </select>
~~~

方式二：association标签

- 通过传入的Dept利用反射获取对应的属性名，将emp中的多余字段分别根据字段和属性进行赋值

  最后将Dept的对象赋值给emp类中的dept

~~~xml
<resultMap id="dept2" type="emp">
        <id property="eid" column="eid"/>
        <result property="empName" column="emp_name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <result property="email" column="email"/>
        <result property="empName" column="emp_name"/>

        <!--  association：设置多对一的映射关系
              property:需要处理多对一的映射关系的属性名
              javaType：该属性的类型

        -->
        <association property="dept" javaType="Dept">
            <id property="did" column="did"/>
            <result property="deptName" column="dept_name"/>
        </association>
    </resultMap>
~~~

方式三：分步查询

- 分步查询的优点：可以实现延迟加载，但是必须在核心配置文件中设置全局配置信息：

  延迟加载：即在什么时候需要什么时候调用执行对应sql语句，不会在一次查询执行所有的分步sql

  lazyLoadingEnabled(默认关闭)：延迟加载的全局开关。当开启时，所有关联对象都会延迟加载 

  aggressiveLazyLoading(默认关闭)：当开启时，任何方法的调用都会加载该对象的所有属性。 

~~~xml
<!-- 分步查询第一步-->
<!--    Emp getEmpDeptByStep1(@Param("eid") Integer eid);-->
    <resultMap id="dept3" type="emp">
        <id property="eid" column="eid"/>
        <result property="empName" column="emp_name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <result property="email" column="email"/>
        <!--
        select：设置分步查询，查询某个属性的值的sql的唯一标识（namespace.sqlId或mapper接口的全类名.方法名）
        column：将sql以及查询结果中的某个字段设置为分步查询的条件
        fetchType：当开启了全局的延迟加载之后，可以通过此属性手动控制延迟加载的效果
          - lazy：表示延迟加载
          - eager：表示立即加载
        -->
        <association property="dept"
                     select="mybatis.mapper.DeptMapper.getDeptByEmpDidStep2"
                     column="did"
                     fetchType="lazy">
        </association>
    </resultMap>
    <select id="getDeptByEmpDidStep1"  resultMap="dept3">
        select * from t_emp where eid = #{eid};
    </select>

<!-- 分步查询第二步-->
<!--Dept getDeptByEmpDidStep2(@Param("did") Integer did)-->
<!-- 使用resultType 需要配置全局信息，自动把_转换为驼峰命名的方式-->
    <select id="getDeptByEmpDidStep2" resultType="dept">
        select * from t_dept where did = #{did};
    </select>
~~~

### 一对多映射关系处理

**一对多  对应的是 集合**

方式一：collection标签 

目的：设置字段和属性的映射关系

- 从ofType中获取集合的泛型参数类型，进而获取该类型属性，

  将查询得到的字段和属性建立映射关系，创建该类型对象并将每一个对象放进集合中

~~~xml
<!--    Dept getDeptAndEmps(@Param("did") Integer did)-->
    <resultMap id="emp1" type="dept">
        <id property="did" column="did"/>
        <result property="deptName" column="dept_name"/>
        <!--collection：处理一对多的映射关系
             ofType：表示该属性的泛型
        -->
        <collection property="emps" ofType="emp">
            <id property="eid" column="eid"/>
            <result property="empName" column="emp_name"/>
            <result property="age" column="age"/>
            <result property="sex" column="sex"/>
            <result property="email" column="email"/>
        </collection>
    </resultMap>
    <select id="getDeptAndEmps" resultMap="emp1">
        select * from t_dept left join t_emp on t_dept.did = t_emp.did where t_dept.did = #{did};
    </select>
~~~

方式二：分步查询

- collection标签用于处理一对多的映射关系

~~~xml
<!-- 分步查询第一步--> 
<!--    Dept getDeptAndEmpStep1(@Param("did") Integer did)-->
    <resultMap id="emp2" type="dept">
        <id property="did" column="did"/>
        <result property="deptName" column="dept_name"/>
        <collection property="emps"
                    select="mybatis.mapper.EmpMapper.getDeptAndEmpStep2"
                    column="did"
                    fetchType="eager"></collection>
    </resultMap>
    <select id="getDeptAndEmpStep1" resultMap="emp2">
        select * from t_dept where did = #{did};
    </select>

<!-- 分步查询第二步-->
<!--List<Emp> getDeptAndEmpStep2(@Param("did") Integer did)-->
    <select id="getDeptAndEmpStep2" resultType="emp">
        select * from t_emp where t_emp.did = #{did};
    </select>
~~~

---

## 动态SQL

- **动态SQL是由MyBatis框架提供的一种根据特定条件动态拼接SQL语句的功能**

1.if标签

where 后的 1=1 是一个恒成立的条件，目的是确保当第一个test不成立的时候sql语句的拼接不出错

~~~xml
<!--List<Emp> getEmpByCondition(Emp emp)-->
<!--   if:根据标签中的test属性所对象的表达式决定标签中的内容是否需要拼接到SQL中 -->
    <select id="getEmpByCondition" resultType="emp">
        select * from t_emp where 1=1
        <if test="empName != null and empName != ''">
            and emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
~~~

 2.where标签

①where标签中由内容时，会自动生成where关键字，并且将内容前多余的and 或 or去掉

②where标签中没有内容时，此时where标签没有任何效果

- **<font color="orange">where标签不能将内容后多余的and 或 or去掉</font>**


~~~xml
<select id="getEmpByCondition" resultType="emp">
        select * from t_emp
        <where>
            <if test="empName != null and empName != ''">
                emp_name = #{empName} 
            </if>
            <if test="age != null and age != ''">
                 and age = #{age} 
            </if>      
        </where>
    </select>
~~~

 3.trim标签

①若标签中有内容时;

prefix | suffix：将trim标签中内容前面或后面添加指定内容

suffixOverrides | prefixOverrides： 将trim标签中内容前面或后面去掉指定内容

②若标签中没有内容时，trim标签没有任何效果

~~~xml
<select id="getEmpByCondition" resultType="emp">
        select * from t_emp
        <trim prefix="where" suffixOverrides="and|or">
            <if test="empName != null and empName != ''">
                emp_name = #{empName} and
            </if>
            <if test="age != null and age != ''">
                age = #{age} or
            </if>   
        </trim>
    </select>
~~~

 4.choose、when、otherwise标签

- **choose、when、otherwise标签的结构类似java中的switch-case，满足一个条件后其余判断不再执行**

choose作为when、otherwise的<font color="orange">**父标签**</font>

在choose标签里面，<font color="orange">**when标签至少得有一个，otherwise标签最多只能有一个**</font>

~~~xml
<!--    choose、when、otherwise标签-->
    <select id="getEmpByChoose" resultType="emp">
        select * from t_emp
        <where>
            <choose>
                <when test="empName != null and empName != ''">
                    emp_name = #{empName}
                </when>
                <when test="age != null and age != ''">
                    age = #{age}
                </when>             
                <otherwise>
                    did = 1
                </otherwise>
            </choose>
        </where>
    </select>
~~~

 5.foreach标签

- collection属性值：当前要遍历的数组或集合对象

​		item属性：数组或集合当次遍历获取到的表示对象

​	    separator属性：遍历数据以什么分隔

​        open属性：foreach标签所有循环内容从什么开始

​        close属性：foreach标签所有循环内容到什么结束

①批量删除操作：

~~~xml
<!--方式一 -->
<!--    int deleteByArray(@Param("eids") Integer[] eids)-->
    <delete id="deleteByArray">
        delete from t_emp where eid in
        <foreach collection="eids" item="eid" separator="," open="(" close=")">
            #{eid}
        </foreach>
    </delete>

<!--    方式二-->
    <delete id="deleteByArray">
        delete from t_emp where
        <foreach collection="eids" item="eid" separator="or">
            eid = #{eid}
        </foreach>
    </delete>
~~~

②批量添加操作：

~~~xml
<!--    int insertEmpByList(@Param("emps") List<Emp> emps)-->
    <insert id="insertEmpByList">
        insert into t_emp values
        <foreach collection="emps" item="emp" separator=",">
            (null,#{emp.empName},#{emp.age},#{emp.sex},#{emp.email},null)
        </foreach>
    </insert>
~~~

  6.sql标签

~~~xml
<!-- 设置sql片段-->
<sql id="empColumns">
  eid,emp_name,age,sex,email
</sql>
    
<select id="getEmpByCondition" resultType="emp">
    <!-- include引用sql片段  refid设置引用片段的id-->
   select <include refid="empColumns"/>  from t_emp
</select>
~~~

---

## MyBatis的缓存

### 一级缓存

- 默认级别是一级缓存，且是默认开启的

- 一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存

  在第二次进行查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问

- 一级缓存失效的情况：

​	①不同的SqlSession对应不同的一级缓存

​	②同一个SqlSession但是查询条件不同

​	③同一个SqlSession两次查询期间执行了任何一次增删改操作

​	④同一个SqlSession两次查询期间手动清空了缓存(只对一级缓存有效，对二级缓存无效)

### 二级缓存

- 二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；

  此后若再次执行相同的查询语句，结果就会从缓存中获取


- 二级缓存开启的条件： 

​		①在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置 

​		②在映射文件中设置catch缓存标签

​		③二级缓存必须在SqlSession关闭或提交之后有效 

​		④查询的数据所转换的实体类类型必须实现序列化的接口

- 使二级缓存失效的情况： 两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

### 二级缓存的配置文件

在mapper配置文件中添加的cache标签可以设置一些属性：

-  eviction属性：缓存回收策略 

  LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。 

  FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。 

  SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。 

  WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。 

  默认的是 LRU。 

- flushInterval属性：刷新间隔，单位毫秒 

  默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新 
  
- size属性：引用数目，正整数 

  代表缓存最多可以存储多少个对象，太大容易导致内存溢出 

- readOnly属性：只读，true/false 

  true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了 很重要的性能优势。 

  false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是 false。

### 缓存查询的顺序

①先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。 

②如果二级缓存没有查询到数据，再查询一级缓存 

③如果一级缓存也没有查询到数据，则查询数据库 

- **只有当SqlSession关闭之后，一级缓存中的数据才会写入二级缓存**

---

## MyBatis的逆向工程

- 正向工程：先创建Java实体类，由框架负责根据实体类生成数据库表。Hibernate是支持正向工程的。
- 逆向工程：先创建数据库表，由框架负责根据数据库表，反向生成如下资源：
  - Java实体类 
  - Mapper接口 
  - Mapper映射文件

创建逆向工程的步骤：

①添加依赖和插件

~~~xml
<!-- 依赖MyBatis核心包 -->
<dependencies>
<dependency>
<groupId>org.mybatis</groupId>
<artifactId>mybatis</artifactId>
<version>3.5.7</version>
</dependency>
</dependencies>

<build>
        <plugins>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.0</version>
                <!-- 插件的依赖 -->
                <dependencies>
                    <!-- 逆向工程的核心依赖 -->
                    <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.3.2</version>
                    </dependency>
                    <!-- 数据库连接池 -->
                    <dependency>
                        <groupId>com.mchange</groupId>
                        <artifactId>c3p0</artifactId>
                        <version>0.9.2</version>
                    </dependency>
                    <!-- MySQL驱动 -->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>5.1.8</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
~~~

②创建MyBatis的核心配置文件

③创建逆向工程的配置文件 

​	**文件名必须是：generatorConfig.xml**

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
    targetRuntime: 执行生成的逆向工程的版本
    MyBatis3Simple: 生成基本的CRUD
    MyBatis3: 生成带条件的CRUD
    -->
    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!-- 数据库的连接信息 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/ssm_crud?characterEncoding=utf8&amp;useSSL=false&amp;serverTimezone=UTC&amp;rewriteBatchedStatements=true"
                        userId="root"
                        password="root">
        </jdbcConnection>
        <!-- javaBean的生成策略-->
        <javaModelGenerator targetPackage="ssm_crud.bean" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="mybatis.mapper" targetProject=".\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="ssm_crud.dao" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
        <!-- 逆向分析的表 -->
        <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="tbl_emp" domainObjectName="Employee"/>
        <table tableName="tbl_dept" domainObjectName="Department"/>
    </context>
</generatorConfiguration>
~~~

④执行maven工程中的插件generate目标

### 	QCB查询

带有Selective选择性的方法在传入的某个属性为null值的情况下，不会将对应字段值修改为null

而没有带Selective选择性的方法在传入的某个属性为null值的情况下，会将对应字段值修改为null

~~~java
@Test
    public void test1(){
            EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
            //查询所有数据
            List<Emp> emps = mapper.selectByExample(null);
            System.out.println(emps);
            //根据条件查询
            EmpExample example = new EmpExample();
        	////创建条件对象，通过andXXX方法为SQL添加查询添加，每个条件之间是and关系
            example.createCriteria().andEmpNameEqualTo("emp1").andAgeGreaterThan(5);
            //将之前添加的条件通过or拼接其他条件
            example.or().andDidIsNotNull();
            List<Emp> emps = mapper.selectByExample(example);
            System.out.println(emps);
            mapper.updateByPrimaryKeySelective(new Emp(8,"test11",20,null,"test11@g.com",3));
        }
~~~

---

## 分页插件

- 使用分页插件的前提：

​		①添加依赖

​		②在mybatis的核心配置文件中配置插件

- 使用MyBatis分页插件实现分页功能

  ①需要在查询功能之前开启分页

     PageHelper.startPage(int pageNum, int pageSize)

     **pageNum：当前页的页码 	pageSize：每页显示的条数**

​		②在查询功能之后获取相关信息

​		  PageInfo pageInfo = new PageInfo<>(List list, int navigatePages)

​		  **list：分页之后的数据 	navigatePages：导航分页的页码数**

分页相关数据 :

~~~java
PageInfo{
pageNum=2, pageSize=4, size=3, startRow=5, 
endRow=7, total=7, pages=2, 
list=Page{count=true, pageNum=2, pageSize=4, startRow=4, endRow=8, 
total=7, pages=2, reasonable=false, pageSizeZero=false}
    
//当前页的数据
[Emp{eid=8, empName='test11', age=20, sex='女', email='test11@g.com', did=3}, Emp{eid=9, empName='1', age=1, sex='男', email='test@qq.com', did=null}, Emp{eid=10, empName='2', age=2, sex='男', email='test@qq.com', did=null}], 
//分页插件中封装的数据         
prePage=1, nextPage=0, isFirstPage=false, isLastPage=true, 
hasPreviousPage=true, hasNextPage=false, navigatePages=5, 
navigateFirstPage=1, navigateLastPage=2, navigatepageNums=[1, 2]
		}
~~~

> 常用数据： 
>
> pageNum：当前页的页码 
>
> pageSize：每页显示的条数 
>
> size：当前页显示的真实条数 
>
> total：总记录数 
>
> pages：总页数 
>
> prePage：上一页的页码 
>
> nextPage：下一页的页码  
>
>  isFirstPage/isLastPage：是否为第一页/最后一页 
>
> hasPreviousPage/hasNextPage：是否存在上一页/下一页 
>
> navigatePages：导航分页的页码数 
>
> navigatepageNums：导航分页的页码，[1,2,3,4,5]

