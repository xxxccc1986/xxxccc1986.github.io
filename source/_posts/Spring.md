---
title: Spring
date: 2022-04-08 11:34:02
updated: 2022-04-08 11:34:02
tags:
  - Spring
categories:
  - 框架
keywords: Spring
cover: https://w.wallhaven.cc/full/x8/wallhaven-x8ye3z.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/04/08/Spring
---

## Spring框架

**Spring是轻量级的开源的JavaEE框架**，可以解决企业应用开发的复杂性

Spring 有两个核心部分：IOC 和 Aop

1.IOC：控制反转，把创建对象过程交给Spring进行管理

2.Aop：面向切面，不修改源代码进行功能增强

~~~java
//入门案例
@Test
    public void test(){
        //1.加载spring配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        //2.获取配置创建的对象
        User user = context.getBean("user", User.class);
        System.out.println(user);
        user.add();
    }
~~~

---

## IOC

概念：IOC(Inversion Of Control)  控制反转，把对象创建和对象之间的调用过程，交给Spring进行管理

作用：可以降低耦合度

底层原理：xml解析、工厂模式、反射

- IoC是一种**设计思想** 
- **IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value）,Map 中存放的是各种对象。**

### IOC(接口)

1.IOC思想基于IOC容器完成，IOC容器底层就是对象工厂

2.Spring提供IOC容器实现的两种方式:(两个接口)

①BeanFactory：Spring内部使用接口，一般开发不使用

特点：在加载配置文件时不会同时创建对象，仅在获取或使用对象时才会创建对象

②**ApplicationContext：BeanFactory接口的子接口，功能更多，开发通常使用这个接口**

特点：在加载配置文件时就同时创建对象

ApplicationContext的实现类：

![实现类](https://s1.ax1x.com/2022/04/08/Lpcklt.png)



### Bean管理

- bean管理是指两个操作，即**Spring创建对象，Spring注入属性**

bean管理操作的两种方式：

#### 基于xml配置文件实现

1.实现方式

①基于xml方式创建对象

在spring配置文件中使用bean标签，标签里面添加对应属性即可

- 注意一点：创建对象时默认执行的是无参构造器方法，缺少无参构造器将报错

~~~xml
	<!--  配置User对象创建  -->
	<!-- id属性：唯一标识  -->
	<!-- class属性：类的全路径(包类路径) -->
    <bean id="user" class="spring.User"></bean>
~~~

②基于xml方式注入属性

DI(Dependency Injection) 依赖注入就是注入属性

方式一：set方法注入 必须先创建对象后才能调用set方法

~~~java
public class Book {
    //创建属性
    private String name;
    private String author;

    //创建相应的set方法
    public void setName(String name) {
        this.name = name;
    }
    public void setAuthor(String author) {
        this.author = author;
    }
    
    public void  test(){
        System.out.println(name + "=" + author);
    }
}

@Test
    public  void testBook(){
        //1.加载spring配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        //2.获取配置创建的对象
        Book book = context.getBean("book", Book.class);
        book.test();
    }
~~~

spring文件配置：

~~~xml
<!-- 1.配置Book对象创建  -->
    <bean id="book" class="spring.Book" >
        <!--    2.set 方法注入属性-->
        <!-- 使用property完成属性注入
           name属性代表类里面属性的名称
           value属性代表想要向属性注入的值
        -->
        <property name="name" value="test"></property>
        <property name="author" value="test"></property>
    </bean>
~~~

方式二：有参构造注入

~~~java
public class Order {
    //创建属性
    private String orderId;
    private String orderName;

    //有参构造注入
    public Order(String orderId, String orderName) {
        this.orderId = orderId;
        this.orderName = orderName;
    }

    public void  test(){
        System.out.println(orderId + "=" + orderName);
    }
}

@Test
    public  void testOrder(){
        //1.加载spring配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        //2.获取配置创建的对象
        Order order = context.getBean("order", Order.class);
        order.test();
    }
~~~

spring文件配置：

~~~xml
<!-- 1.配置Order对象创建  -->
    <bean id="order" class="spring.Order">
        <!-- 2.有参构造方法注入属性
          使用constructor-arg完成属性注入
           name属性代表类里面属性的名称
           value属性代表想要向属性注入的值
        -->
        <constructor-arg name="orderId" value="test1"></constructor-arg>
        <constructor-arg name="orderName" value="test1"></constructor-arg>
    </bean>
~~~

---

2.xml注入其他类型的属性

~~~xml
	<!--   设置某个属性为空值     -->
        <property name="address">
            <null />
        </property>

        <!--  设置某个属性包含特殊值
              1.把《》用转义字符表示
              2.把特殊符号内容写到CDATA中
        -->
<!--  <property name="address" value="&lt;&lt;广东&gt;&gt;">-->
        <property name="address" >
            <value><![CDATA[<<广东>>]]></value>
        </property>
    </bean>

	<!--   注入属性-外部bean     -->
	<!--1 service 和 dao 对象创建-->
    <bean id="userservice" class="spring.service.UserService">
        <!--注入 userDao 对象
        name 属性：类里面属性名称
        ref 属性：创建 userDao 对象 bean 标签 id 值
        -->
        <property name="userDao" ref="userdaoimpl"></property>
    </bean>
    <bean id="userdaoimpl" class="spring.dao.UserDaoImpl"></bean>

	<!--  注入属性-内部bean  -->
    <bean id="emp" class="spring.bean.Emp">
        <!--  设置普通属性      -->
        <property name="name" value="test1"></property>
        <property name="sex" value="boy"></property>

        <!--    设置对象属性    -->
        <property name="dept" >
            <bean id="dept" class="spring.bean.Dept">
                <property name="deptName" value="dept1"></property>
            </bean>
        </property>
    </bean>

	<!--  级联赋值  -->
    <bean id="emp" class="spring.bean.Emp">
        <!--  设置普通属性      -->
        <property name="name" value="test1"></property>
        <property name="sex" value="boy"></property>
        
        <!--    设置对象属性    -->
        <property name="dept" ref="dept"></property>
        <property name="dept.deptName" value="dept2"></property>
    </bean>
    <bean id="dept" class="spring.bean.Dept"></bean>
</beans>
~~~

3.xml注入集合属性

~~~xml
<!--  1.创建Student配置对象  -->
    <bean id="student" class="collecttype.Student">
<!--   2.使用property标签进行属性注入     -->
        <!--   数组类型属性注入     -->
        <property name="course">
            <array>
                <value> java</value>
                <value> C++</value>
                <value> JS</value>
            </array>
        </property>

        <!--   list集合类型属性注入     -->
        <property name="list" >
            <list>
                <value>s1</value>
                <value>s2</value>
                <value>s3</value>
            </list>
        </property>

        <!--   map集合类型属性注入     -->
        <property name="map">
            <map>
                <entry key="t1key" value="t1value"></entry>
                <entry key="t2key" value="t2value"></entry>
            </map>
        </property>

        <!--   set集合类型属性注入     -->
        <property name="set">
            <set>
                <value>1</value>
                <value>2</value>
            </set>
        </property>
  <!--   list集合类型属性注入,值是对象     -->
        <property name="courseList">
            <list>
                <ref bean="coures1"></ref>
                <ref bean="coures2"></ref>
            </list>
        </property>

<!--  创建多个course对象  -->
    <bean id="coures1" class="collecttype.Course">
        <property name="className" value="c++"></property>
    </bean>
    <bean id="coures2" class="collecttype.Course">
        <property name="className" value="java"></property>
    </bean>
        
        <!-- 将重复的配置提取成公共部分，便于调用   -->
        <!-- 1.提取list集合类型属性注入   -->
        <util:list id="booklist">
            <value >test1</value>
            <value >test2</value>
            <value >test3</value>
        </util:list>

        <!-- 2.提取list集合类型属性注入使用   -->
        <bean id="book" class="collecttype.Book">
            <property name="list" ref="booklist"></property>
        </bean>
~~~

#### Bean

**此处的FactoryBean与最上面的BeanFactory并不一样，BeanFactory是一个接口，而FactoryBean是spring内置的工厂bean**

Spring 有两种类型 bean：

①普通 bean：在配置文件中定义 bean 类型就是返回类型

②工厂 bean（FactoryBean）：在配置文件定义 bean 类型可以和返回类型不一样

~~~java
//自定义返回的bean类型
public class TestBean implements FactoryBean<Course>{
    //定义返回bean
    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setClassName("test");
        return course;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
~~~

#### bean的作用域

**作用域：在 Spring 里面，设置创建 bean 实例是单实例还是多实例**

- **在 Spring 里面，默认情况下，bean 是单实例对象**

1.设置单实例还是多实例的方法

①在 spring 配置文件 bean 标签里面有属性（scope）用于设置单实例还是多实例 

②scope 属性值 第一个值 默认值，singleton，表示是单实例对象 第二个值 prototype，表示是多实例对象

2.singleton 和 prototype 区别

① singleton 单实例，prototype 多实例 

② scope 值是 singleton ，加载 spring 配置文件时候就会创建单实例对象 

​	 scope 值是 prototype，不是在加载 spring 配置文件时候创建 对象，在调用 getBean 方法时候创建多实例对象

#### bean的生命周期

1.生命周期：对象创建到对象销毁的过程

2、bean 生命周期 

①通过构造器创建 bean 实例（无参数构造） 

②为 bean 的属性设置值和对其他 bean 引用（调用 set 方法） 

③调用 bean 的初始化的方法（需要进行配置初始化的方法） 

④bean 可以使用了（对象获取到了） 

⑤当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）

~~~java
public class Orders {
 //无参数构造
 public Orders() {
 System.out.println("第一步 执行无参数构造创建 bean 实例");
 }
 private String oname;
 public void setOname(String oname) {
 this.oname = oname;
 System.out.println("第二步 调用 set 方法设置属性值");
 }
 //创建执行的初始化的方法
 public void initMethod() {
 System.out.println("第三步 执行初始化的方法");
 }
 //创建执行的销毁的方法
 public void destroyMethod() {
 System.out.println("第五步 执行销毁的方法");
 }
}

 @Test
 public void testBean3() {
// ApplicationContext context =
// new ClassPathXmlApplicationContext("bean4.xml");
 ClassPathXmlApplicationContext context =
 new ClassPathXmlApplicationContext("bean4.xml");
 Orders orders = context.getBean("orders", Orders.class);
 System.out.println("第四步 获取创建 bean 实例对象");
 System.out.println(orders);
 //手动让 bean 实例销毁
 context.close();
 }
~~~

- 执行的init初始化方法和销毁方法需要在spring的xml文件中进行配置

~~~xml
<bean id="orders" class="com.atguigu.spring5.bean.Orders" initmethod="initMethod" destroy-method="destroyMethod">
 <property name="oname" value="手机"></property>
</bean>
~~~

3.bean 的后置处理器，bean 生命周期有七步

①通过构造器创建 bean 实例（无参数构造） 

②为 bean 的属性设置值和对其他 bean 引用（调用 set 方法） 

③把 bean 实例传递 bean 后置处理器的方法 postProcessBeforeInitialization  

④调用 bean 的初始化的方法（需要进行配置初始化的方法） 

⑤把 bean 实例传递 bean 后置处理器的方法 postProcessAfterInitialization 

⑥bean 可以使用了（对象获取到了） 

⑦当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）

~~~java
//（1）创建类，实现接口 BeanPostProcessor，创建后置处理器
public class MyBeanPost implements BeanPostProcessor {
 @Override
 public Object postProcessBeforeInitialization(Object bean, String beanName) 
throws BeansException {
 System.out.println("在初始化之前执行的方法");
 return bean;
 }
 @Override
 public Object postProcessAfterInitialization(Object bean, String beanName) 
throws BeansException {
 System.out.println("在初始化之后执行的方法");
 return bean;
 }
}

<!--配置后置处理器-->
<bean id="myBeanPost" class="com.atguigu.spring5.bean.MyBeanPost"></bean>
~~~

#### xml自动装配

- 使用较少

自动装配：根据指定装配规则（属性名称或属性类型），spring自动将匹配的属性值进行注入

~~~xml
<!--  实现自动装配
        bean标签autowire，配置自动装配
        autowire属性常用两个值：
        byName根据属性名称注入，注入值得bean的id值和类属性名称一样
        byType根据属性类型注入
 -->
<!-- 配置对象的创建   -->
<!--    <bean id="emp" class="autowire.Emp" autowire="byName">-->
    <bean id="emp" class="autowire.Emp" autowire="byType">
    </bean>
    <bean id="dept" class="autowire.Dept"></bean>
~~~

#### 引入外部属性

①创建外部属性文件，properties 格式文件，写数据库信息

②把外部 properties 属性文件引入到 spring 配置文件中

- 引入 context 名称空间

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"//util 名称空间
       xmlns:context="http://www.springframework.org/schema/context"//context 名称空间
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- context:property-placeholder不能写在bean标签里面 -->
    <context:property-placeholder location="classpath:jdbc.properties" />
    
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <!--    配置连接池    -->
        <property name="driverClassName" value="${prop.driverClass}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.user}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>
</beans>
~~~

#### 基于注解方式实现

1.注解的概念

- **注解：是代码特殊标记，格式：@注解名称(属性名称=属性值,属性名称=属性值)**

注解可以作用在类上面，方法上面，属性上面，注解可以简化xml配置

2.spring提供的注解

**bean管理中创建对象提供注解：**

①@Component 普通组件

②@Service  业务层组件

③@Controller 控制层组件

④@Repository 持久层组件

- 这四个注解的功能都是一样的，都可以用来创建bean的对象实例

 **bean管理中提供的属性注入的注解：**

①@Autowired：根据属性类型进行自动装配

②@Qualifier：根据名称进行注入，常用@autowired搭配使用

- **PS:当出现接口的实现类不止这一个，就不能单独使用@Autowired，因为如果根据类型获取系统就不知道你想获取具体的实现类对象，**

**所以要根据名获取，@Autowired 和@Qualifier搭配一起用**

③@Resource：可以根据类型注入，可以根据名称注入

④@Value：注入普通类型属性

3.实现方式

①引入jar包

②在xml文件中利用名称空间和开启组件扫描

③创建类，在类上添加创建对象的注解

3.1实现对象的创建

~~~java
//在注解里面value属性值可以省略不写
//不写则是默认值，而默认值通常是类名的首字母小写
//UserService ==> userService
@Controller(value = "userService") //<bean id="userService" class=""/>
public class UserService {
    public void test(){
        System.out.println("test success!");
    }
}
~~~

spring的xml配置文件开启组件扫描

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">


<!--    开启组件扫描-->
<!--    1.如果扫描多个包，多个包使用逗号隔开-->
<!--    2.扫描包的上层目录-->
    <context:component-scan base-package="dao,service "></context:component-scan>
</beans>
~~~

- 组件扫描的细节

~~~xml
<!--示例 1
 use-default-filters="false" 表示现在不使用默认 filter，自己配置 filter
 context:include-filter ，设置扫描哪些内容
-->
<context:component-scan base-package="com.atguigu" use-defaultfilters="false">
 <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
<!--示例 2
 下面配置扫描包所有内容
 context:exclude-filter： 设置哪些内容不进行扫描
-->
<context:component-scan base-package="com.atguigu">
 <context:exclude-filter type="annotation"  expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
~~~

3.2实现在对象创建后注入属性

第一步：把 service 和 dao 对象创建，在 service 和 dao 类添加创建对象注解 

第二步：在 service 注入dao 对象，在 service 类添加 dao 类型属性，在属性上面使用注解

~~~java
@Service
public interface UserDao {
    void test();
}

@Repository
public class UserDaoImpl implements  UserDao{
    @Override
    public void test() {
        System.out.println("this is UserDaoImpl");
    }
}

public class UserService {
@Value(value = "test")//注入普通属性
    private String name;

    //定义dao类型的属性
    //不需要添加set方法
//    @Autowired //根据类型进行注入
//    @Qualifier(value = "userDaoImpl1") //根据名称进行注入
//    private UserDao userDao;

    // @Resource根据属性类型注入
     @Resource(name = "userDaoImpl1") //根据属性名称注入
    private UserDao userDao;
    public void test(){
        System.out.println("this is UserService," + name);
        userDao.test();
    }
}
~~~

#### 完全注解开发

~~~java
//（1）创建配置类，替代 xml 配置文件
@Configuration //作为配置类，替代 xml 配置文件
@ComponentScan(basePackages = {"com.atguigu"})
public class SpringConfig {
}
//（2）编写测试类
@Test
public void testService2() {
 //加载配置类
 ApplicationContext context
 = new AnnotationConfigApplicationContext(SpringConfig.class);
    
 UserService userService = context.getBean("userService", UserService.class);
 System.out.println(userService);
 userService.add();
}

~~~

## Aop

aop的概念：Aspect oriented programming 面向切面编程，**AOP 是 OOP（面向对象编程）的一种延续。**

作用：在不改变源代码的情况下，添加其他模块的代码，根本上解耦合，避免横切逻辑代码重复。

aop的底层原理是：动态代理

~~~java
public class JDKProxy {

    public static void main(String[] args) {
        //创建接口实现类代理对象
        Class[] interfaces = {UserDao.class};
        UserDaoImpl userDaoImpl = new UserDaoImpl();
        UserDao userDao = (UserDao) Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces, new UserDaoProxy(userDaoImpl));
        int add = userDao.add(1, 2);
        System.out.println(add);
    }
}

//创建
class UserDaoProxy implements InvocationHandler{
    private Object obj;

    public UserDaoProxy(Object obj){
        this.obj = obj;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //方法之前
        System.out.println("方法之前 ..." + method.getName() + "传递的参数" + Arrays.toString(args));

        //被增强的方法执行
        Object invoke = method.invoke(obj, args);

        //方法之后
        System.out.println("方法之后" + obj);
        return invoke;

    }
}
~~~

#### 操作术语

1.连接点：类里面哪些方法可以被增强，这些方法成为连接点

2.切入点：实际被增强的方法，这些方法称为切入点

3.通知(增强)：实际上在被增强的方法中的逻辑部分(代码) ，称为通知

**通知有多种类型：**

①前置通知：在增强方法前执行

②后置通知：在增强方法后执行

③环绕通知：在增强方法前后分别执行

④异常通知：在增强方法出现异常后执行

⑤最终通知：无论增强方法怎么样，都会执行

4.切面：它是个动作，把通知应用到切入点的过程

#### AOP操作

- **spring框架一般都是基于AspectJ实现AOP操作**

1.AspectJ 的概念：**它不是Spring的组成部分，它是独立AOP框架，一般把AspectJ经常与Spring框架一起使用，进行AOP操作**

2.切入点表达式

作用：指定对哪个类里面的哪个方法进行增强

语法结构：**execution（[ 权限修饰符] -[返回类型]- [类全路径]-[方法名称]-(参数列表)）**

- 权限修饰符可以省略不写

#### 实现AOP操作

1.基于xml配置文件实现

~~~xml
<!--1、创建两个类，增强类和被增强类，创建方法-->
<!--2、在 spring 配置文件中创建两个类对象-->
<!--创建对象-->
<bean id="book" class="com.atguigu.spring5.aopxml.Book"></bean>
<bean id="bookProxy" class="com.atguigu.spring5.aopxml.BookProxy"></bean>
<!--3、在 spring 配置文件中配置切入点-->
<!--配置 aop 增强-->
<aop:config>
 <!--切入点-->
 <aop:pointcut id="p" expression="execution(* 
com.atguigu.spring5.aopxml.Book.buy(..))"/>
 <!--配置切面-->
 <aop:aspect ref="bookProxy">
 <!--增强作用在具体的方法上-->
 <aop:before method="before" pointcut-ref="p"/>
 </aop:aspect>
</aop:config>
~~~

2.基于注解方法时实现(使用)

①创建类，在类里面定义方法

②创建增强类（编写增强逻辑）

③进行通知的配置

④配置不同类型的通知(在增强的类中)

~~~java
//被增强的类
@Component
public class Student {

    public void  add(){
//        int i = 10/0;
        System.out.println("test success!");
    }

}

//增强的类
@Aspect //生成代理对象
@Service
public class StudentProxy {

    //相同切入点抽取
    @Pointcut(value = "execution(void aop.aopanno.Student.add(..))")
    public void point(){

    }

    //前置通知
    //@Before注解表示作为前置通知
    @Before("point()")
     public void before(){
         System.out.println("before");
     }

    /**
    *@After和@AfterReturning存在一定的差异，
    *@After最终通知表示在方法之后执行
    *@AfterReturning后置通知表示在方法返回值之后执行
    */
    
    //最终通知
    //@After注解表示作为前置通知
    @After("execution(void aop.aopanno.Student.add(..))")
    public void after(){
        System.out.println("after");
    }

    //后置通知
    @AfterReturning("execution(void aop.aopanno.Student.add(..))")
    public void afterReturning(){
        System.out.println("afterReturning");
    }

    //异常通知
    @AfterThrowing("execution(void aop.aopanno.Student.add(..))")
    public void afterThrowing(){
        System.out.println("afterThrowing");
    }

    //环绕通知
    @Around("execution(void aop.aopanno.Student.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("around 之前");

        //被增强的方法执行
        proceedingJoinPoint.proceed();

        System.out.println("around 之后");
    }
}
~~~

通知的xml文件配置

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--    开启注解组件扫描-->
    <!--    1.如果扫描多个包，多个包使用逗号隔开-->
    <!--    2.扫描包的上层目录-->
    <context:component-scan base-package="ioc,aop.aopanno"></context:component-scan>

    <!--  开启Aspect生成代理对象  -->
    <aop:aspectj-autoproxy ></aop:aspectj-autoproxy>
</beans>
~~~

- 相同切入点表达式的提取

~~~java
	//相同切入点抽取
    @Pointcut(value = "execution(void aop.aopanno.Student.add(..))")
    public void point(){

    }

    //前置通知
    //@Before注解表示作为前置通知
    @Before("point()")
     public void before(){
         System.out.println("before");
     }
~~~

- 有多个增强类多同一个方法进行增强，@Order设置增强类优先级

  数字越小，代表优先级越高

~~~java
     @Order(1)
     public class PersonProxy {
         
     @Order(3)
     public class StudentProxy {
~~~

---

## jdbcTemplate

①引入相关 jar 包

druid-1.1.10.jar

mysql-connector-java-8.0.11.jar

spring-orm-5.2.6.RELEASE.jar

spring-tx-5.2.6.RELEASE.jar

spring-jdbc-5.2.6.RELEASE.jar

②在 spring 配置文件配置数据库连接池

③配置 JdbcTemplate 对象，注入 DataSource

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--   开启注解组件扫描 -->
    <context:component-scan base-package="jdbctemplate"/>
    <context:property-placeholder location="classpath:jdbc.properties" />
    
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <!--    配置连接池    -->
    <property name="driverClassName" value="${prop.driverClass}"></property>
    <property name="url" value="${prop.url}"></property>
    <property name="username" value="${prop.user}"></property>
    <property name="password" value="${prop.password}"></property>
    </bean>

        <!--  jdbcTemplate对象  -->
    <bean id="jdbc" class="org.springframework.jdbc.core.JdbcTemplate">
        <!--   注入     -->
        <property name="dataSource" ref="dataSource"></property>
    </bean>
</beans>
~~~

④创建 service 类，创建 dao 类，在 dao 注入 jdbcTemplate 对象

~~~java
@Service
public class BookService {

    //注入dao的对象属性
    @Autowired
    private BookDao bookDao;
}

@Repository
public class BookDaoImpl implements BookDao{

    //注入jdbcTemplate
    @Autowired
    private JdbcTemplate jdbcTemplate;
}
~~~

- 具体方法的增删改查操作与自己封装的jdbcutils相差不多，不在此浪费篇幅说明 具体看spring下的jdbctemplate包

---

## spring事务管理

1.事务即数据库的一组逻辑操作单元由一种状态到另一种状态

2.事务一般添加在JavaEE的service层

3.**spring事务管理分为了编程式事务管理和声明式事务管理(推荐)**

- Spring使用的声明式事务管理，其底层使用了**Aop技术**
- Spring提供了一个事务管理API

![事务管理API](https://s1.ax1x.com/2022/04/08/LpcmTg.png)

### 基于注解方式实现事务管理

实现步骤：

1.在 spring 配置文件配置事务管理器

2.在 spring 配置文件，开启事务注解

①在 spring 配置文件引入名称空间 tx

②开启事务注解

3.在 service 类上面（或者 service 类里面方法上面）添加事务注解

- @Transactional，这个注解添加到类上面，也可以添加方法上面 
- 如果把这个注解添加类上面，这个类里面所有的方法都添加事务 
- 如果把这个注解添加方法上面，为这个方法添加事务

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

<!--  开启注解组件扫描  -->
    <context:component-scan base-package="jdbctemplate"/>
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${prop.url}"/>
        <property name="driverClassName" value="${prop.driverClass}"/>
        <property name="username" value="${prop.user}"/>
        <property name="password" value="${prop.password}"/>
    </bean>

    <bean id="jdbctemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--  1.创建事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!--   注入数据源    -->
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!--  2.开启事务注解  -->
    <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
</beans>
~~~

~~~java
//3.在 service 类上面添加事务注解
@Service
@Transactional
public class AccountService {

    //注入属性
    @Autowired
    private AccountDao accountDao;


    //转账的方法
    public void moneyIn(Account account){
        accountDao.changeMoney(account);
    }
~~~

### 声明式事务管理参数配置

- 在注解@Transacional里面可以配置事务相关参数

1.propagation：事务传播行为  

概念：事务传播行为用来描述由某一个事务方法被嵌套进另一个普通方法的时侯事务如何传播。

事务方法：对数据库表数据进行变化的操作，如增删改等操作，但不包括查询。

~~~java
 public void methodA(){
 methodB();
 //doSomething
 }
 
 @Transaction(Propagation=XXX)
 public void methodB(){
 //doSomething
 }
~~~

| 事务传播行为类型           | 说明                                                         |
| :------------------------- | :----------------------------------------------------------- |
| PROPAGATION_REQUIRED(默认) | 如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是默认的选择。 |
| PROPAGATION_REQUIRES_NEW   | 新建事务，如果当前存在事务，把当前事务挂起，以新的事务进行管理 |
| PROPAGATION_SUPPORTS       | 支持当前事务，如果当前没有事务，就以非事务方式执行。         |
| PROPAGATION_MANDATORY      | 使用当前的事务，如果当前没有事务，就抛出异常                 |
| PROPAGATION_NOT_SUPPORTED  | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。   |
| PROPAGATION_NEVER          | 以非事务方式执行，如果当前存在事务，则抛出异常。             |
| PROPAGATION_NESTED         | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。 |

2.ioslation：事务隔离级别

- 事务有特性成为隔离性，多事务操作之间不会产生影响。不考虑隔离性产生很多问题

数据库并发问题：脏读、不可重复读、幻读

隔离级别：读未提交、读已提交、可重复读、串行化

3.timeout：超时时间 

①事务需要在一定时间内进行提交，如果不提交进行回滚 

②默认值是 -1 ，设置时间以秒单位进行计算 

![举例](https://s1.ax1x.com/2022/04/08/LpcMfs.png)

4.readOnly：是否只读 

①读：查询操作，写：添加修改删除操作

②readOnly 默认值 false，表示可以查询，可以添加修改删除操作 

③设置 readOnly 值是 true，设置成 true 之后，只能查询 

5.rollbackFor：回滚 

①设置出现哪些异常进行事务回滚 

6.noRollbackFor：不回滚 

①设置出现哪些异常不进行事务回滚

### 基于xml配置文件实现

实现步骤

在 spring 配置文件中进行配置

①配置事务管理器

②配置通知

③配置切入点和切面  

- 补充知识：
  - 在面向切面编程时，一般用<aop:aspect>。<aop:aspect>定义切面（包括通知（前置通知，后置通知，返回通知等等）和切点（pointcut））
  - 在进行事务管理时，一般用<aop:advisor>。<aop:advisor>定义通知器(其中通知器跟切面一样，也包括通知和切点)
  - <aop:advisor>大多用于事务管理。
  - <aop:aspect>大多用于日志、缓存            

~~~xml
<!--1 创建事务管理器-->
<bean id="transactionManager" 
class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
 <!--注入数据源-->
 <property name="dataSource" ref="dataSource"></property>
</bean>

<!--2 配置通知-->
<!--tx:advice用于定义通知-->
<tx:advice id="txadvice">
 <!--配置事务参数-->
 <tx:attributes>
 <!--指定哪种规则的方法上面添加事务-->
 <tx:method name="accountMoney" propagation="REQUIRED"/>
 <!--<tx:method name="account*"/>-->
 </tx:attributes>
</tx:advice>

<!--3 配置切入点和切面-->
<aop:config>
 <!--配置切入点-->
 <aop:pointcut id="pt" expression="execution(* 
com.atguigu.spring5.service.UserService.*(..))"/>
 <!--配置切面-->
 <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
</aop:config>

~~~

### 完全注解开发

- @Component：表示这是一个组件。IOC会托管这个类的对象。
- @bean：方法返回的对象交给IOC管理。

~~~java
//1、创建配置类，使用配置类替代 xml 配置文件
@Configuration //配置类
@ComponentScan(basePackages = "com.atguigu") //组件扫描
@EnableTransactionManagement //开启事务
public class TxConfig {
 //创建数据库连接池
 @Bean
 public DruidDataSource getDruidDataSource() {
 DruidDataSource dataSource = new DruidDataSource();
 dataSource.setDriverClassName("com.mysql.jdbc.Driver");
 dataSource.setUrl("jdbc:mysql:///user_db");
 dataSource.setUsername("root");
 dataSource.setPassword("root");
 return dataSource;
 }
 //创建 JdbcTemplate 对象
 @Bean
 public JdbcTemplate getJdbcTemplate(DataSource dataSource) {
 //到 ioc 容器中根据类型找到 dataSource
 JdbcTemplate jdbcTemplate = new JdbcTemplate();
 //注入 dataSource
 jdbcTemplate.setDataSource(dataSource);
 return jdbcTemplate;
 }
 //创建事务管理器
 @Bean
 public DataSourceTransactionManager 
getDataSourceTransactionManager(DataSource dataSource) {
 DataSourceTransactionManager transactionManager = new 
DataSourceTransactionManager();
 transactionManager.setDataSource(dataSource);
 return transactionManager;
 }
}
~~~


---

## Spring5 新功能

### @Nullable注解

Spring5框架的核心容器支持Nullable注解

- 此注解可以使用在方法、属性、参数上面，表示以上的返回值都可以为空，避免报空指针异常

### 函数式风格 

Spring5 核心容器支持函数式风格 GenericApplicationContext

- 函数式风格创建对象，交给 spring 进行管理

~~~java
//函数式风格创建对象，交给 spring 进行管理
@Test
public void testGenericApplicationContext() {
 //1 创建 GenericApplicationContext 对象
 GenericApplicationContext context = new GenericApplicationContext();
 //2 调用 context 的方法对象注册
 context.refresh();
 context.registerBean("user1",User.class,() -> new User());
 //3 获取在 spring 注册的对象
 // User user = (User)context.getBean("com.atguigu.spring5.test.User");
 User user = (User)context.getBean("user1");
 System.out.println(user);
}
~~~

 ### SpringWebFlux

前置知识：springMVC spirngboot java8新特性 maven

- P53 日后填坑

