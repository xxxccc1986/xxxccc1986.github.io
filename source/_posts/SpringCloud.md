---
title: SpringCloud
date: 2022-08-17 20:32:02
updated: 2022-08-17 20:32:02
tags:
  - SpringCloud
categories:
  - 框架
keywords: SpringCloud
cover: https://w.wallhaven.cc/full/kw/wallhaven-kwov61.png
copyright_author: Year21
copyright_url: http://year21.top/2022/08/17/SpringCloud
---

# 微服务理论简介

概念：微服务架构是一种架构模式，它提倡将单一的应用程序划分为一组小的服务，服务之间互相协调，互相配合，

为用户提供最终价值，每个服务运行在其独立的进程中，服务与服务间采用轻量级的通信机制互相协作，(一般基于

HTTP协议的RESTful API)，每个服务都围绕着具体业务进行构建，并且能够被独立的部署到生产环境、测试环境等。

且应当尽量避免统一的集中式的服务管理机制，对具体的一个服务而言，应根据业务来进行构建。

- SpringCloud = 分布式微服务架构的一站式解决方案，是多种微服务架构落地技术的集合体

![SpringCloud俗称微服务全家桶](https://s1.ax1x.com/2022/08/16/v03wMq.png)

---

# 父工程搭建

①创建maven父工程

![](https://s1.ax1x.com/2022/08/16/v03sdU.png)

②修改父工程pom文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>top.year21</groupId>
  <artifactId>springcloud</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>

  <!-- 统一管理jar包版本 -->
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <junit.version>4.12</junit.version>
    <log4j.version>1.2.17</log4j.version>
    <lombok.version>1.18.24</lombok.version>
    <mysql.version>8.0.26</mysql.version>
    <druid.version>1.1.16</druid.version>
    <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
  </properties>

  <!-- 子模块继承之后，提供作用：锁定版本+子modlue不用写groupId和version  -->
  <dependencyManagement>
    <dependencies>
      <!--spring boot 2.2.2-->
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>2.2.2.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--spring cloud Hoxton.SR1-->
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>Hoxton.SR1</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--spring cloud alibaba 2.1.0.RELEASE-->
      <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>2.1.0.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql.version}</version>
      </dependency>
      <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>${druid.version}</version>
      </dependency>
      <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>${mybatis.spring.boot.version}</version>
      </dependency>
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>${junit.version}</version>
      </dependency>
      <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>${log4j.version}</version>
      </dependency>
      <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
        <optional>true</optional>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <fork>true</fork>
          <addResources>true</addResources>
        </configuration>
      </plugin>
    </plugins>
  </build>
    
</project>
~~~

- dependencyManagement元素和 dependency元素的区别

  通过会在一个组织或者项目的最顶层的父POM文件会看到dependencyManagement元素，

  这个元素能让所有此工程的子项目引用同一个依赖而不需再次显示的声明所需的版本号，

  <font color='red'>**但是这个元素只是声明依赖，并没有实际引入，因此子项目还是需要声明所需的依赖**</font>

  <font color='red'>**实际上的引入依赖是由 dependency元素来完成的**</font>

③跳过maven打包时的test功能

![](https://s1.ax1x.com/2022/08/16/v03yoF.png)

④尝试install构建判断是否成功

---

# 支付模块

1.创建子模块module

2.修改子工程的 pom文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloud</artifactId>
        <groupId>top.year21</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud_provider_payment8001</artifactId>
    
    <dependencies>
        <!--包含了sleuth+zipkin-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
~~~

3.编写yml配置文件

~~~yml
spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/computer_store?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    driver-class-name: com.mysql.cj.jdbc.Driver

#配置mybatis配置
mybatis:
  type-aliases-package: top.year21.springcloud.entity
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    #开启在mybatis处理过程中打印出对应的sql语句功能
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    #开启数据库字段自动转换为驼峰命名
    map-underscore-to-camel-case: true

#配置端口号
server:
  port: 8001
~~~

4.编写启动类

~~~java
//启动类
@SpringBootApplication
public class CloudProviderPayment8001 {
    public static void main(String[] args) {
        SpringApplication.run(CloudProviderPayment8001.class,args);
    }
}
~~~

5.编写业务类

①创建数据库和数据表

~~~mysql
CREATE DATABASE IF NOT EXISTS springcloud CHARSET 'utf8';

CREATE TABLE IF NOT EXISTS payment(
id INT PRIMARY KEY AUTO_INCREMENT COMMENT 'ID',
`serial` VARCHAR(255) DEFAULT '' 
)
~~~

②创建实体类和通用类

~~~java
@Data
public class Payment implements Serializable{
    private Integer id;
    private String serial;
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class JsonResult<E> {
    private Integer status;
    private String message;
    private E data;

    public JsonResult(Integer status,String message){
        this.status = status;
        this.message = message;
    }
}
~~~

③创建mapper接口和编写映射文件

~~~java
@Mapper
public interface PaymentMapper {
    public int create(Payment payment);
    public Payment queryPaymentById(@Param("id") Integer id); 
}
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="top.year21.springcloud.dao.PaymentMapper">
<resultMap id="BaseResultMap" type="payment">
    <id property="id" column="id"/>
    <result property="serial" column="serial"/>
</resultMap>
<!--    public int create(Payment payment);-->
<insert id="create" parameterType="payment" useGeneratedKeys="true" keyProperty="id">
    insert into payment(serial) values(#{serial})
</insert>
<!--    public Payment queryPaymentById(@Param("id") Integer id);-->
 <select id="queryPaymentById" resultMap="BaseResultMap">
     select * from payment where id = #{id};
 </select>

</mapper>
~~~

④创建service层和其实现类

~~~java
public interface PaymentService {
    public int addPayment(Payment payment);
    public Payment queryById(Integer id);
}

@Service
public class PaymentServiceImpl implements PaymentService {
    @Autowired
    private PaymentMapper paymentMapper;
    @Override
    public int addPayment(Payment payment) {
        return paymentMapper.create(payment);
    }
    @Override
    public Payment queryById(Integer id) {
        return paymentMapper.queryPaymentById(id);
    }
}
~~~

⑤创建控制层

~~~java
@RestController
@RequestMapping("/payment")
@Slf4j
public class PaymentController {
    @Autowired
    private PaymentService paymentService;
    @GetMapping("/query/{id}")
    public JsonResult<Payment> queryPayment(@PathVariable("id") Integer id){
        Payment payment = paymentService.queryById(id);
        if (payment == null){
            return new JsonResult<>(444,"查询失败，查询的id是：" + id);
        }else {
            return new JsonResult<>(200,"查询成功",payment);
        }
    }
    @PostMapping("/add")
    public JsonResult<Integer> addPayment(Payment payment){
        int result = paymentService.addPayment(payment);
        log.info("插入结果：" + result);
        if (result > 0){
            return new JsonResult<>(200,"添加成功",result);
        }else {
            return new JsonResult<>(444,"添加失败");
        }
    }
}
~~~

- 添加热部署插件

添加热部署的依赖

~~~xml
<!-- 设置自动重启 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
~~~

在父工程的pom文件中添加plugin插件

~~~xml
<build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <fork>true</fork>
          <addResources>true</addResources>
        </configuration>
      </plugin>
    </plugins>
  </build>
~~~

打开IDEA设置中Buid  --> Compiler  -->  相邻的首字母分别为A、D、B、C的四个设置

后续通过ctrl+f9即可实现热部署

---

# 消费者订单模块

> 一样的套路

1.创建子模块module

2.修改子工程的pom文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <groupId>top.year21</groupId>
        <artifactId>springcloud</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud_consume_order80</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
~~~

3.创建启动类

~~~java
@SpringBootApplication
public class CloudConsumeOrder80 {
    public static void main(String[] args) {
        SpringApplication.run(CloudConsumeOrder80.class,args);
    }
}
~~~

4.编写业务类

- 由于考虑到这个微服务只是需要去调用其他微服务，需要使用RestTemplate实现微服务之间的横向调用

①创建一个配置类@Bean注册RestTemplate实体类

RestTemplate提供了多种访问远程Http服务的方法，可访问各种restful接口，是Spring提供的用于访问Rest服务的工具集

~~~java
@Configuration
public class ApplicationContextConfig {
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
~~~

- 由于考虑到这个微服务只是需要去调用其他微服务，因此只需要写实体类和controller层即可

  实体类和通用类可直接从上方的子项目复制

~~~java
@RestController
@RequestMapping("/consume")
@Slf4j
public class ConsumeOrderController {

    @Autowired
    private RestTemplate restTemplate;
    private static final String PAYMENT_URL = "http://localhost:8001";

    @GetMapping("/createPayment")
    public JsonResult<Payment> createPayment(Payment payment){
        /* 
        (url,requestMap,ResponseBean.class)这三个参数分别代表
        Rest请求地址、请求参数、Http响应被转换成的对象类型
         */
        return restTemplate.postForObject(PAYMENT_URL + "/payment/add",payment,JsonResult.class);
    }
    @GetMapping("/queryPayment/{id}")
    public JsonResult<Payment> queryPayment(@PathVariable("id") Integer id){
        return restTemplate.getForObject(PAYMENT_URL + "/payment/query/" + id,JsonResult.class);
    }
   
}
~~~

- <font color='red'>**这里需要注意一个问题** </font>

由于使用的restTemplate模板发送post请求，参数是放在了请求体当中，而在8001订单微服务中的创建订单接口的形参

没有使用@RequestBody注解修饰 是无法直接从请求体中获取参数的，因此会出现请求成功但数据库的数据值为null的情况，

而之前使用postman能测试成功是因为postman发的post请求是选择将参数放在了请求的url地址中，因此能被自动注入。

为了修正这个错误，需要在8001订单微服务中的创建订单接口的形参添加@RequestBody注解。

~~~java
//8001订单微服务中的创建订单接口
@PostMapping("/add")
public JsonResult<Integer> addPayment(@RequestBody Payment payment){
    int result = paymentService.addPayment(payment);
    log.info("插入结果：" + result);
    if (result > 0){
        return new JsonResult<>(200,"添加成功",result);
    }else {
        return new JsonResult<>(444,"添加失败");
    }
}
~~~

# 公共工程

- <font color='red'>**这个工程主要是用于存放需要重复使用的代码**</font>

> 与上面类似的步骤

1.创建module

2.添加pom依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>cn.hutool</groupId>
        <artifactId>hutool-all</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
~~~

3.添加需要被重复使用的代码即可

4.maven命令clean install打包此工程供其他工程调用

5.删除其他工程中原先通用代码并在pom文件中引入公共工程

~~~xml
<dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
    <groupId>top.year21</groupId>
    <artifactId>cloud_common_apis</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
~~~

---

# 服务注册与发现

## Eureka

服务治理：传统的rpc远程调用框架中，管理多个服务之间的依赖关系较为复杂，服务治理可以实现服务调用、负载均衡、

容错等，实现服务注册与发现。

服务注册与发现：有一个注册中心，当服务器启动的时候，会把当前服务器的信息(服务地址、通讯地址)等以别名方式注册

到注册中心上。而消费者或服务提供者以该别名的方式去注册中心上获取到实际的服务器通讯地址，然后再实现本地RPC

调用远程调用框架核心设计思想，对于注册中心来说，因为使用注册中心管理多个服务器之间的一个依赖关系(服务治理概念)。

- Eureka包含两个组件(Eureka Server 和 Eureka Client)

①Eureka Server  提供服务注册服务

  多个微服务在通过配置启动后将在Eureka Serve中进行注册，从而在Eureka Serve的服务注册表可以查看所有可用服务的信息

②Eureka Client  提供对注册中心的访问

这是个java客户端，用于简化Eureka Server的交互，同时它内部也存储了一个使用轮询(round-robin)负载算法的负载均衡器。

在应用启动后，将会向Eureka Server中心发送心跳(默认30s)，但如果在90s内没有接收到某个服务节点的心跳，则服务中心

会自动将这个服务节点进行剔除

![](https://s1.ax1x.com/2022/08/16/v03gJJ.png)

### 构建单机Eureka

#### EurekaServer注册中心

1.创建子模块

2.修改pom文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <groupId>top.year21</groupId>
        <artifactId>springcloud</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud_eureka_server7007</artifactId>

    <dependencies>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>top.year21</groupId>
            <artifactId>cloud_common_apis</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
</project>
~~~

3.编写yml配置类

~~~yml
server:
  port: 7007

eureka:
  instance:
    hostname: localhost #eureka服务器端的实例名称
  client:
    #是否在注册中心注册自己
    register-with-eureka: false
    #false表示自己就是注册中心，职责是维护服务实例，不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与EurekaServer交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
~~~

4.编写启动类

~~~java
@SpringBootApplication
@EnableEurekaServer //表示这是个eureka的注册中心
public class CloudEurekaServer7007 {
    public static void main(String[] args) {
        SpringApplication.run(CloudEurekaServer7007.class,args);
    }
}
~~~

5.测试

#### 微服务注册到EurekaServer注册中心

微服务注册到EurekaServer注册中心的步骤

①添加下列依赖到pom文件

~~~xml
<!--    引入eureka-client   -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
~~~

②修改各自微服务的yml配置文件

~~~yml
spring:
  application:
    name: cloud-payment-service #设置注册到EurekaServer注册中心使用的名字
    
#配置eureka相关信息
eureka:
  client:
    #是否在注册中心注册自己
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true
    #如果是单节点无所谓，但是集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #设置与EurekaServer交互的地址，查询服务和注册服务都需要依赖这个地址
      defaultZone: http://localhost:7007/eureka
~~~

③在各自微服务的主启动类上添加@EnableEurekaClient注解

### 构建集群Eureka

![](https://s1.ax1x.com/2022/08/16/v032W9.png)

1.基于原来的单机Eureka工程创建一个类似的子工程

pom文件和主启动类复制单机Eureka工程即可，只需修改原来的yml配置文件以及编写一个新的yml配置文件

~~~yml
#7007eurekaServer工程修改后的配置
#集群eurekaServer配置
server:
  port: 7007

eureka:
  instance:
    hostname: test.dzsc.tk #eureka服务器端的实例名称
  client:
    #是否在注册中心注册自己
    register-with-eureka: false
    #false表示自己就是注册中心，职责是维护服务实例，不需要去检索服务
    fetch-registry: false
    service-url:
      #互相注册，相互守望 即a服务器注册到b服务器中，反之同样
      defaultZone: http://test.dzsc.cf:7008/eureka/

#7008eurekaServer工程的配置
#集群eurekaServer配置
server:
  port: 7008

eureka:
  instance:
    hostname: test.dzsc.cf #eureka服务器端的实例名称
  client:
    #是否在注册中心注册自己
    register-with-eureka: false
    #false表示自己就是注册中心，职责是维护服务实例，不需要去检索服务
    fetch-registry: false
    service-url:
      #互相注册，相互守望 即a服务器注册到b服务器中，反之同样
      defaultZone: http://test.dzsc.tk:7007/eureka/
~~~

2.测试

<font color='red'>**各个微服务的启动顺序必须按照这个顺序**</font>：eurekaServer注册中心 -->  微服务的提供者 -->  消费者

<font color='red'>**测试如果发现以域名+端口号的方式访问能成功看到各自的页面都有其他的server即为成功**</font>

3.将之前的微服务分别注册到多个EurekaServer注册中心上

- 分别修改微服务的yml配置文件

~~~yml
defaultZone: http://test.dzsc.tk:7007/eureka/,http://test.dzsc.cf:7008/eureka/ #集群eureka版
~~~

### 构建集群微服务提供者

1.基于原来的工程cloud_provider_payment8001创建一个类似的微服务工程cloud_provider_payment8002

pom文件、yml配置文件(需修改端口号)、主启动类、业务类部分信息都可以从原来的8001copy

- 各自在两个微服务的控制层的controller添加一个端口字符串以分辨提供服务的微服务是哪一个

~~~java
@Value("${server.port}")
private String port;

@GetMapping("/query/{id}")
public JsonResult<Payment> queryPayment(@PathVariable("id") Integer id){
    Payment payment = paymentService.queryById(id);
    if (payment == null){
        return new JsonResult<>(444,"没有此数据，查询的id是：" + id);
    }else {
        return new JsonResult<>(200,"查询成功,提供服务的端口号是：" + port,payment);
    }
}
~~~

2.虽然集群微服务提供者创建成功，但是存在一个问题，即在order微服务中调用地址硬编码的方式定义的，

这会导致永远提供服务的都是同一个微服务，为了避免这种情况，需要修改orderController

~~~java
//修改前
// private static final String PAYMENT_URL = "http://localhost:8001"; 单机版微服务提供者

//修改后
//根据注册中心的名字来寻找将要被调用的请求地址ip
private static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE"; //集群版微服务提供者
~~~

3.但是上述的修改虽然保证了不再永远访问同一个微服务，但会出现报一个找不到ip的异常

java.net.UnknownHostException:CLOUD_PAYMENT_SERVICE；这是因为没有办法识别在这个别名下是由哪个

模块进行服务的，为了修复这个异常，因此需要修改order80微服务的ApplicationContextConfig配置类

- 使用@LoadBalanced注解开启RestTemplate负债均衡的能力

~~~java
@LoadBalanced
@Bean
public RestTemplate restTemplate(){
    return new RestTemplate();
}
~~~

### actuator微服务信息完善

①服务名称修改

修改对应的EurekaServer微服务的yml配置文件

②鼠标悬停在即将点击的微服务连接上有IP信息提示

~~~yml
eureka:
  instance:
    instance-id: payment8001 #设置服务名称
    prefer-ip-address: true #访问路径显示ip地址
~~~

### 服务发现Discovery

实现能够查询在eurekaServer注册中心中的服务信息内容

①在payment微服务的控制层添加以下信息

~~~java
//根据类型进行注入
@Resource
private DiscoveryClient discoveryClient;

@GetMapping("/discovery")
public Object discovery(){
    //查询所有服务
    List<String> services = discoveryClient.getServices();
    //打印服务清单
    for (String e : services) {
        log.info("*****element:" + e);
    }

    //根据微服务名称查找相关的微服务
    List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
    for (ServiceInstance instance: instances) {
        log.info(instance.getInstanceId() + "\t" + instance.getHost() + "\t" + instance.getPort() + "\t" + instance.getUri());
    }
    return this.discoveryClient;
}
~~~

### Eureka自我保护机制

默认情况下，如果EurekaServer在90s内没有接收到某个微服务实例的心跳则会注销该实例，但是当网络

分区发送故障的时候，微服务实例和EurekaServer无法进行正常通信，那么这个机制就变得极其危险，因为一旦微服务

本身是健康的，那么就不应该注销该实例。而自我保护机制可以解决这种现象的出现。

在自我保护机制中，EurekaServer会保护服务注册表中的信息，不再注销任何微服务实例。

<font color='red'>**自我保护机制：简单来说就是某个时刻一个微服务不可用了，Eureka也不会立即清理，而是依旧对该微服务信息进行保存**</font>

- 如何禁止自我保护机制

①修改EurekaServer注册中心的yml配置文件

②修改微服务提供者的yml配置文件

~~~yml
#EurekaServer注册中心的yml配置文件
eureka:
  server:
    #关闭自我保护机制，立刻删除不可用的服务
    enable-self-preservation: false
    #心跳超时时间 2s
    eviction-interval-timer-in-ms: 2000
   
#微服务提供者的yml配置文件    
eureka:
  instance:
    #Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
    #Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2
~~~

---

## Zookeeper

- 与eureka不同的是，<font color='red'>**zookeeper注册的服务节点是临时节点**</font>，没有所谓的保护机制，即在心跳时间内无法得到回复信息

  就会立即剔除不可用的服务

### 安装zookeeper步骤

~~~text
//下载带源码的zookeeper的linux安装包 自己选择版本
https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/

//此次选择将文件放置在/opt文件夹下
//解压下载的文件
tar -zxvf apache-zookeeper-3.8.0-bin.tar.gz 
//删除压缩包
rm -rf apache-zookeeper-3.8.0-bin.tar.gz  
//重命名解压出来的文件
mv apache-zookeeper-3.8.0-bin/ zookeeper
//进入zookeeper
cd zookeeper/
//在这个目录创建一个data文件夹，下面需要用
mkdir data
//进入conf文件夹
cd conf
//复制一份参考配置文件
cp zoo_sample.cfg zoo.cfg
//修改zoo.cfg的部分配置内容
dataDir=/opt/zookeeper/data
//在配置文件的末尾添加这一段后保存退出
server.1=主机地址:2888:3888
//修改/etc/profile
vim /etc/profile
//设置zookeeper运行环境 在profile的末尾添加
export ZOOKEEPER_HOME=/opt/zookeeper
export PATH=$PATH:$ZOOKEEPER_HOME/bin
//进入zookeeper目录下的bin文件夹
cd /opt/zookeeper/bin
//执行zkServer.sh启动zookeeper
./zkServer.sh start
//查看是否启动成功
./zkServer.sh status
//显示下列内容即为启动成功
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /opt/zookeeper/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: standalone

//连接zookeeper
./zkCli.sh
~~~

### 服务提供者加入zookeeper

1.创建端口号为80的consumezk子模块

2.修改pom文件，添加下面的依赖

~~~xml
 <dependencies>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>top.year21</groupId>
            <artifactId>cloud_common_apis</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    <!--    Springboot整合zookeeper客户端    -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
~~~

3.编写yml配置文件

~~~yml
spring:
  application:
    name: cloud-provider-payment #设置注册到zookeeper注册中心使用的名字
  cloud:
    zookeeper:
      connect-string: 192.168.231.134:2181 #运行在linux上的zookeeper服务端地址

#配置端口号
server:
  port: 8004
~~~

4.编写启动类

~~~java
@SpringBootApplication
@EnableDiscoveryClient //该注解用于向使用consul或者zookeeper作为注册中心时注册服务
public class CloudProviderPayment8004 {
    public static void main(String[] args) {
        SpringApplication.run(CloudProviderPayment8004.class,args);
    }
}
~~~

5.编写业务类

~~~java
@RestController
@RequestMapping("/payment")
public class PaymentController {
    @Value("${server.port}")
    private String port;

    @GetMapping("/zk")
    public String paymentZK(){
        return "springcloud with zookeeper:" + port + "\t" + UUID.randomUUID().toString();
    }
~~~

### 服务消费者加入zookeeper

1.创建端口号为8004的子模块

2.修改pom文件

- 与上面的8004工程差不多一样，可以直接copy

3.编写yml配置文件

- 与上面的8004工程差不多一样，可以直接copy，但需要修改一下端口号

4.编写启动类

- 与上面的8004工程差不多一样，可以直接copy

5.编写业务类

~~~java
@Configuration
public class ApplicationContextConfig {
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}

@RestController
@RequestMapping("/consume")
public class ZKController {
    @Autowired
    private RestTemplate restTemplate;
    private static final String PAYMENT_URL = "http://cloud-provider-payment"; //单机版zookeeper

    @GetMapping("/zk")
    public String paymentInfo(){
        String result = restTemplate.getForObject(PAYMENT_URL + "/payment/zk",String.class);
        return result;
    }
}
~~~

---

## Consul

- Consul是一套开源的分布式服务发现和配置管理系统

支持服务发现、健康检测、KV存储、多数据中心以及可视化界面等功能 --> [linux安装consul教程](https://blog.csdn.net/taoying220/article/details/120330267?spm=1001.2101.3001.6650.7&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-7-120330267-blog-119786396.pc_relevant_multi_platform_featuressortv2removedup&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-7-120330267-blog-119786396.pc_relevant_multi_platform_featuressortv2removedup&utm_relevant_index=9)

### 服务提供者加入Consul

> 还是老一套 建module、改pom文件、写配置、启动类、业务类

基于与上述的zookeeper步骤类似，因此可以copy的步骤就不再重复

3.写配置

~~~yml
server:
  port: 8006

spring:
  application:
    name: consul-provider-payment
  cloud:
    consul:
      #consel注册中心地址配置
      host: 192.168.231.134
      port: 8500
      discovery:
        service-name: ${spring.application.name}
        heartbeat:
          enabled: true #设置不检测健康状态
~~~

### 服务消费者加入Consul

> 还是老一套 建module、改pom文件、写配置、启动类、业务类

基于与上述的zookeeper步骤类似，因此可以copy的步骤就不再重复

3.写配置

~~~yml
server:
  port: 80

spring:
  application:
    name: consul-consume-order
  cloud:
    consul:
      #consel注册中心地址配置
      host: 192.168.231.134
      port: 8500
      discovery:
        service-name: ${spring.application.name}
        heartbeat:
          enabled: true #设置不检测健康状态
~~~

## 三个注册中心的区别

- CAP理论(<font color='red'>**C、A、P三者不能同时成立，最多只能同时存在2个**</font>)

即在分布式系统中，Consistency(一致性)、Availability(可用性)、Partition Tolerance(分区容错性)。

CAP理论关注的是数据，而不是整体系统设计的策略

| 组件名    | 语言 | CAP  | 服务健康检测 | 对外暴露接口 | SpringCloud集成 |
| :-------- | :--- | :--- | :----------- | :----------- | :-------------- |
| Eureka    | Java | AP   | 可配支持     | HTTP         | 已集成          |
| Zookeeper | Java | CP   | 支持         | 客户端       | 已集成          |
| Consul    | Go   | CP   | 支持         | HTTP/DNS     | 已集成          |

![经典CAP理论图](https://s1.ax1x.com/2022/08/16/v03bJH.png)

---

# 服务调用与负载均衡

## Ribbon

### 概述

- Spring Cloud Ribbon是基于Nefix Ribbon实现的一套用于客户端的负载均衡的工具

它的主要功能是提供客户端的软件负载均衡算法和服务调用。

- LB负载均衡(Load Balance)：将用户的请求平摊分配到多个服务器上，从而实现系统的高可用。

- LB负载均衡又分为了集中式LB 和 进程内LB

  - 集中式LB：在服务的消费方和提供方之间使用独立的LB设施(硬件如F5，软件如nginx)，由该设施负载

    负责把访问请求通过某种策略转发至服务的提供方

  - 进程式LB：将LB逻辑集成到消费方，消费方从服务注册中心获取有哪些地址可用，然后再从这些地址中

    选择合适的服务器。

    <font color='red'>**Ribbon就属于进程内LB**</font>，它只是一个类库，集成于消费方进程，消费方通过它获取服务提供方的地址。

- Ribbon本地负载均衡客户端 与 Nginx服务端负载均衡的区别

Nginx的负载均衡是由服务端实现的，客户端会将所有请求交给nginx，由nginx实现请求转发。

Ribbon本地负载均衡在调用微服务接口时，会在注册中心上获取注册信息服务列表之后缓存到JVM本地，从而在本地实现

RPC远程服务调用技术。

<font color='red'>**Ribbon简单来说就是一句话：负载均衡+RestTemplate调用**</font>

### 负责均衡演示

Ribbon在工作时分成两步

①先选择EurekaServer，优先选择在同一个区域内负载较少的server

②再根据用户指定的策略，再从server抓取到的服务注册列表中选择一个地址

- Ribbon提供了多种策略：轮询、随机和根据响应时间加权

![](https://s1.ax1x.com/2022/08/16/v08PYQ.png)

- RestTemplate工具集 getForObject()方法和getForEntity()方法的不同

![](https://s1.ax1x.com/2022/08/16/v08kSs.png)

### 核心组件IRule

IRule：根据特定算法从服务列表中选取一个要访问的服务

<font color='red'>**默认使用轮询规则**</font>

![](https://s1.ax1x.com/2022/08/16/v08Aln.png)

![](https://s1.ax1x.com/2022/08/16/v08sXt.png)

### 负载规则替换

- <font color='red'>**注意！！由于ribbion的规定自定义规则不能放置在能被@ComponentScan注解扫描的包及其子包路径下**</font>

springcloud这个包及其子包都能被注解@ComponentScan所扫描

1.因此必须在top.year21下创建一个用于放置自定义规则的子包myrule

~~~java
//在这个子包下创建规则配置类
@Configuration
public class MySelfRule {
    @Bean
    public IRule iRule(){
        //定义为随机规则
        return new RandomRule();
    }
}
~~~

2.在主启动类上添加@RibbonClient注解

~~~java
@SpringBootApplication
@EnableEurekaClient
//意思是要去访问的微服务名为CLOUD-PAYMENT-SERVICE，使用的规则是MySelfRule类中定义的规则
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE",configuration = MySelfRule.class)
public class CloudConsumeOrder80 {
    public static void main(String[] args) {
        SpringApplication.run(CloudConsumeOrder80.class,args);
    }
}
~~~

### 负载均衡算法

- <font color='red'>**负载均衡算法的公式**</font>

rest接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标，每次服务重启后rest接口计数从1开始。

#### RoundRobinRule源码分析

~~~java
public class RoundRobinRule extends AbstractLoadBalancerRule {
    //AtomicInteger原子Integer类，且下面构造器设置的初始值为0
    private AtomicInteger nextServerCyclicCounter;
    private static final boolean AVAILABLE_ONLY_SERVERS = true;
    private static final boolean ALL_SERVERS = false;

    private static Logger log = LoggerFactory.getLogger(RoundRobinRule.class);

    public RoundRobinRule() {
        nextServerCyclicCounter = new AtomicInteger(0);
    }

    public RoundRobinRule(ILoadBalancer lb) {
        this();
        setLoadBalancer(lb);
    }

    public Server choose(ILoadBalancer lb, Object key) {
        if (lb == null) {
            log.warn("no load balancer");
            return null;
        }

        Server server = null;
        int count = 0;
        while (server == null && count++ < 10) {
            //获取所有当前状态为up的服务列表
            List<Server> reachableServers = lb.getReachableServers();
            //获取当前提供服务的服务集群的列表
            List<Server> allServers = lb.getAllServers();
            int upCount = reachableServers.size();
            int serverCount = allServers.size();

            if ((upCount == 0) || (serverCount == 0)) {
                log.warn("No up servers available from load balancer: " + lb);
                return null;
            }

            //获取提供服务的服务器的下标，传入的serverCount服务集群的数量
            int nextServerIndex = incrementAndGetModulo(serverCount);
            server = allServers.get(nextServerIndex);

            if (server == null) {
                /* Transient. */
                Thread.yield();
                continue;
            }

            if (server.isAlive() && (server.isReadyToServe())) {
                return (server);
            }

            // Next.
            server = null;
        }

        if (count >= 10) {
            log.warn("No available alive servers after 10 tries from load balancer: "
                    + lb);
        }
        return server;
    }

    /**
     * Inspired by the implementation of {@link AtomicInteger#incrementAndGet()}.
     *
     * @param modulo The modulo to bound the value of the counter.
     * @return The next value.
     */
    private int incrementAndGetModulo(int modulo) {
        for (;;) {
            //获取当前nextServerCyclicCounter的值
            int current = nextServerCyclicCounter.get();
            int next = (current + 1) % modulo;
            //CAS比较并交换
            if (nextServerCyclicCounter.compareAndSet(current, next))
                return next;
        }
    }

    @Override
    public Server choose(Object key) {
        return choose(getLoadBalancer(), key);
    }

    @Override
    public void initWithNiwsConfig(IClientConfig clientConfig) {
    }
}
~~~

#### 手写轮询算法

- <font color='red'>**这个算法的关键就是获取当前接口的请求次数，以及集群提供服务的数量，以及最终使用的服务中下标**</font>

1.去掉ApplicationContextConfig配置类中的@LoadBalanced注解

2.创建LoadBalancer接口和抽象方法

3.实现类

~~~java
public interface LoadBalancer {
    //获取集群中的可用服务提供者的数量
    ServiceInstance serviceInstance(List<ServiceInstance> instances);
}

@Component
public class LoadBalancerImpl implements LoadBalancer{
    //原子Integer类，初始化值为0
    private AtomicInteger atomicInteger = new AtomicInteger(0);

    //用于获取当前是第几次请求
    public final int getAndIncrement(){
        int current;
        int next;
        do{
            current = this.atomicInteger.get();
            next = current >= 2147483647 ? 0 : current + 1;
        }while (!atomicInteger.compareAndSet(current,next));
        System.out.println("当前next值为：" + next);
        return next;
    }

    @Override
    public ServiceInstance serviceInstance(List<ServiceInstance> instances) {
        //获得instances列表中可用服务提供者的下标位置
        int index = getAndIncrement() % instances.size();
        //返回可用的服务提供者
        return instances.get(index);
    }
}
~~~

4.修改ConsumeOrderController

~~~java
@Resource
private LoadBalancer loadBalancer;
@Resource
private DiscoveryClient discoveryClient;

@GetMapping("/lb")
public String getPaymentUrl(){
    //获取所有可用的服务提供者列表
    List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
    if (instances == null || instances.size() <= 0){
        return null;
    }
    //获取是真正提供服务的那个实例
    ServiceInstance instance = loadBalancer.serviceInstance(instances);
    //获取访问接口地址
    URI uri = instance.getUri();
    //进行访问
    return restTemplate.getForObject(uri + "/payment/lb",String.class);
}
~~~

---

## OpenFeign

- Feign是什么

Feign是一个声明式WebService客户端，它的使用方法是定义一个服务接口然后在上面添加注解。

Feign可以与Eureka和Ribbon组合使用以支持负载均衡，Feign使用在消费端

- Feign能干什么

Ribbon使用负债均衡+RestTemplate工作时，利用RestTemplate对http请求进行封装处理，形成一套固定的调用方式。

而大部分情况微服务的一个接口会被多处调用，所以会针对每个微服务自行封装一些客户端来包装服务依赖的调用。

因此，Feign在这个基础上帮助我们定义和实现依赖服务接口。

<font color='red'>**在Feign的实现下，只需要创建一个接口并使用注解的方式来配置依赖服务接口。**</font>

且Feign集成了Ribbon，利用Ribbon维护了服务列表信息，并且通过默认的轮询规则实现客户端的负载均衡。

<font color='red'>**但与Ribbon不同的是通过Feign只需定义服务绑定接口且以声明式的方法便实现了服务调用**</font>

- openFeign又是什么

OpenFeign是SpringCloud对Feign进行了封装，让它支持了Spring MVC标准注解和HttpMessageConverters。

- Feign与openFeign的区别

![](https://s1.ax1x.com/2022/08/16/v08D1A.png)

### 服务调用

1.创建module

2.基于原来的80的pom文件添加下列依赖到新模块的pom中

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
~~~

3.编写yml配置文件

~~~yml
server:
  port: 80
#配置eureka相关信息
eureka:
  client:
    #是否在注册中心注册自己
    register-with-eureka: false
    #是否从EurekaServer抓取已有的注册信息，默认为true
    service-url:
      #设置与EurekaServer交互的地址，查询服务和注册服务都需要依赖这个地址
       defaultZone: http://test.dzsc.tk:7007/eureka/,http://test.dzsc.cf:7008/eureka/ #集群eureka版
~~~

4.启动类，必须使用@EnableFeignClients表示开启feign

~~~java
@SpringBootApplication
@EnableFeignClients  //标明使用feign，激活并开启
public class CloudConsumeFeignOrder80 {
    public static void main(String[] args) {
        SpringApplication.run(CloudConsumeFeignOrder80.class,args);
    }
}
~~~

5.业务类

~~~java
//业务层
@Component
@FeignClient("CLOUD-PAYMENT-SERVICE") //表示找在注册中心中名字是CLOUD-PAYMENT-SERVICE微服务
public interface PaymentFeignService {
    /* 
    该微服务内对应方法对外暴露的接口，这里是微服务的控制层接口，但该接口调用了其自己的业务层
    也算是间接实现了接口调用接口
     */
    @GetMapping("/payment/query/{id}")
    public JsonResult<Payment> queryPayment(@PathVariable("id") Integer id);
}

//控制层
@RestController
@RequestMapping("/feign")
public class OrderFeignController {
    @Autowired
    private PaymentFeignService paymentFeignService;

    @GetMapping("/query/{id}")
    public JsonResult<Payment> getPaymentById(@PathVariable("id") Integer id){
        return paymentFeignService.queryPayment(id);
    }
}
~~~

![](https://s1.ax1x.com/2022/08/16/v08c0f.png)

### 超时控制

- openFeign默认等待1秒钟,例如,消费端只能等待1秒,而服务端处理需要3秒,那么这种情况下就会超时出现报错。

为了避免这种情况，需要在yml文件进行配置，但由于openFeign自带ribbon，因此这方面的设置也由ribbon控制。

~~~yml
#openFeign80消费端yml文件设置
#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ReadTimeout: 5000
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
~~~

### 日志打印功能

openFeign提供了日志打印功能，可以通过配置来调整日志级别，更好的了解openFeign中Http请求细节。

换句话来说就是<font color='red'>**对openFeign接口的调用情况进行监控和输出**</font>，添加步骤如下

1.创建一个配置类

~~~java
@Configuration
public class FeignConfig
{
    @Bean
    Logger.Level feignLoggerLevel()
    {
        return Logger.Level.FULL;
    }
}
~~~

2.在yml配置文件进行配置

~~~java
logging:
  level:
    # feign日志以什么级别监控哪个接口
    config.FeignConfig
    top.year21.springcloud.service.PaymentFeignService: debug
~~~

---

# 服务熔断与降级

服务雪崩：多个微服务之间调用的时候，例如A调用B，B调用C，C又调其他，就会形成所谓的“扇出”。如果扇出的链路

上某个微服务的调用响应时间过长或者不可用，那么微服务A的调用就会占用越来越多的系统进程，进而引起系统崩溃。



断路器本身是一致开关装置，当某个服务单元发生故障之后，通过断路器的故障监控(类似熔断保险丝)，向调用方返回

一个符合预期的、可处理的备选响应(FallBack)，而不是长时间的等待或者抛出调用方无法处理的异常，就能保证服务

调用方的线程不会被长时间、不必要占用，从而避免故障在分布式系统中的蔓延，乃至雪崩。

## Hystrix

- Hystrix是什么 

一个用于处理分布式系统的延迟和容错的开源库，在分布式系统中，许多依赖不可避免的会出现超时、异常等。

而Hystrix能够保证在一个微服务出现问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。

<font color='red'>**Hystrix断路器一般使用在服务消费方**</font>

- Hystrix能干嘛

服务降级、服务熔断、接近实时的监控

### 重要概念

<font color='red'>**服务降级每次都会先调用原服务方法，调用失败才会执行服务降级方法；服务熔断状态会直接调用服务降级方法。**</font>

1.服务降级(fallback)：不管在什么情况下，服务降级的流程都是先调用正常的服务方法，再调用服务降级的fallback的方法。 

也就是服务器繁忙，请稍后再试，不让客户端等待并立刻返回一个友好提示。

- 造成服务降级的情况

①程序运行异常

②超时

③服务熔断触发降级

④线程池/信号量打满

2.服务熔断(break)：假设服务宕机或者在单位时间内调用服务失败的次数过多，即服务降级的次数太多，那么则服务熔断。 

并且熔断以后会跳过正常的方法，会直接调用fallback方法，即所谓“服务熔断后不可用”。 

类似于家里常见的保险丝，当达到最大服务访问后，会直接拒绝访问，然后调用服务降级的fallback方法，返回友好提示。

3.服务限流(flowlimit)：等有多个线程同时过来时，要求按顺序执行，可以理解为排队，一秒N个，有序进行

### 支付微服务构建

1.创建子模块module

2.修改pom文件，引入下列依赖

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
~~~

3.编写yml配置文件

4.主启动类，记得使用@EnableEurekaClient或者@EnableDiscoveryClient注册进注册中心

5.业务类

~~~java
@RestController
@RequestMapping("/hystrix")
public class PaymentController {

    @Autowired
    private PaymentService paymentService;
    @Value("${server.port}")
    private String port;

    @GetMapping("/payment/ok/{id}")
    public String paymentOk(@PathVariable("id") Integer id){
        return paymentService.paymentInfo_Is_OK(id);
    }

    @GetMapping("/payment/bad/{id}")
    public String paymentBad(@PathVariable("id") Integer id){
        return paymentService.paymentInfo_Is_Bad(id);
    }
}

@Service
public class PaymentService {

    public String paymentInfo_Is_OK(Integer id){
        return "当前处理的线程名字：" + Thread.currentThread().getName() + "\t,paymentInfo_Is_OK且id是:" + id;
    }

    public String paymentInfo_Is_Bad(Integer id) {
        try { TimeUnit.SECONDS.sleep(5); } catch (InterruptedException e) { e.printStackTrace(); }
        return "当前处理的线程名字：" + Thread.currentThread().getName() + "\t,paymentInfo_Is_Bad且id是:" + id;
    }
}
~~~

### 订单微服务构建

1.创建子模块module

2.修改pom文件，引入依赖

3.编写yml配置文件

4.主启动类，记得使用@EnableEurekaClient或者@EnableDiscoveryClient注册进注册中心

~~~java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
public class CloudConsumeFeignHystrixOrder80 {
    public static void main(String[] args) {
        SpringApplication.run(CloudConsumeFeignHystrixOrder80.class,args);
    }
}
~~~

5.业务类

~~~java
@RestController
@RequestMapping("/hystrix")
public class OrderController {
    @Autowired
    private PaymentFeignService paymentFeignService;

    @GetMapping("/ok/{id}")
    public String getPaymentOk(@PathVariable("id") Integer id){
        return paymentFeignService.paymentOk(id);
    }

    @GetMapping("/bad/{id}")
    public String getPaymentBad(@PathVariable("id") Integer id){
        return paymentFeignService.paymentBad(id);
    }
}

@Component
@FeignClient("CLOUD-PROVIDER-PAYMENT")
public interface PaymentFeignService {

    @GetMapping("/hystrix/payment/ok/{id}")
    public String paymentOk(@PathVariable("id") Integer id);

    @GetMapping("/hystrix/payment/bad/{id}")
    public String paymentBad(@PathVariable("id") Integer id);
}s
~~~

- 上述的两个微服务都是在非高并发的情况下，因此不会有问题，但是一旦处于高并发的情况下，就会出现

  原先访问速度较快的微服务也变得缓慢起来，因此tomcat的线程池都集中去处理访问量最高的微服务，

  导致没有额外的线程再来处理之前访问速度较快的微服务请求了。

- <font color='red'>**为了解决上述问题才出现了降级/熔断/限流等技术**</font>

### 服务降级

1.先以支付微服务做个降级例子，在这个微服务中bad方法sleep了5秒

设置了自身调用超时时间的最大值，在最大值以内可以正常运行，超过了则需要兜底方法处理，进行服务降级fallback

那如何对支付微服务8001设置服务降级呢？

①在service类上添加@HystrixCommand注解并设置fallback方法

~~~java
//设置服务降级的回调方法名和该业务处理超时时间
@HystrixCommand(fallbackMethod = "paymentInfo_Is_Bad_HandleMethod",commandProperties = {
    //设置超时时间
    @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value = "5000")
})
public String paymentInfo_Is_Bad(Integer id) {
    try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
    return "当前处理的线程名字：" + Thread.currentThread().getName() + "\t,paymentInfo_Is_Bad且id是:" + id;
}

//用于服务降级的fallback方法
public String paymentInfo_Is_Bad_HandleMethod(Integer id) {
    return "当前处理的线程名字：" + Thread.currentThread().getName() +
         "8001系统连接超时或抛异常触发服务降级的fallback方法,参数id是：" + id;
}
~~~

②主启动类添加@EnableHystrix注解

@EnableHystrix用于激活Hystrix，且此注解内部封装了@EnableCircuitBreaker

@EnableCircuitBreaker的作用是开启熔断器

- <font color='red'>**经过测试，无论使超时还是报异常，都会进行服务降级，兜底方法都是设置的fallback方法**</font>

2.以订单微服务做个降级例子

①在yml配置文件中开启hystrix

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
            timeoutInMilliseconds: 1500
~~~

②主启动类添加@EnableHystrix注解

- ③针对消费方回退有两种方法

第一种：修改controller类

~~~java
@GetMapping("/bad/{id}")
@HystrixCommand(fallbackMethod = "getPaymentBadHandleMethod")
public String getPaymentBad(@PathVariable("id") Integer id){
    return paymentFeignService.paymentBad(id);
}

public String getPaymentBadHandleMethod(@PathVariable("id")Integer id){
    return "这里是消费者80，对方支付系统繁忙，请过一会再尝试，谢谢！你的请求id是：" + id;
}
~~~

<font color='red'>**第二种(建议使用这一种)，通用服务降级**</font>

创建一个回退类并实现标注了@FeignClient注解的接口

~~~java
//用于feign调用服务发生异常，hystrix执行的需要的回退类
@Component
public class FallBack implements PaymentFeignService{
    @Override
    public String paymentOk(Integer id) {
        return "错误信息";
    }

    @Override
    public String paymentBad(Integer id) {
        return "错误信息";
    }
}
~~~

在标注了@FeignClient注解的接口这么写

~~~java
@Component
//value属性是微服务的名称，fallback属性是指定回调的类，也就是上方的实现类
@FeignClient(value = "CLOUD-PROVIDER-PAYMENT",fallback = FallBack.class)
public interface PaymentFeignService {

    @GetMapping("/hystrix/payment/ok/{id}")
    public String paymentOk(@PathVariable("id") Integer id);

    @GetMapping("/hystrix/payment/bad/{id}")
    public String paymentBad(@PathVariable("id") Integer id);
}
~~~

- 第一种方式会造成代码冗余且灵活性不足，可以使用一个@DefaultProperties注解来更好解决降级问题

①在需要使用全局降级方法的类上添加@DefaultProperties注解，需要注意全局降级方法不能携带参数

②在需要降级处理的方法上添加@HystrixCommand 注解

~~~java
@RequestMapping("/hystrix")
@DefaultProperties(defaultFallback = "getPaymentBadDefaultHandleMethod") //表示这个类使用了全局降级方法
public class OrderController {
    @GetMapping("/bad/{id}")
//    @HystrixCommand(fallbackMethod = "getPaymentBadHandleMethod") //这个表示降级方法指定为fallbackMethod的方法
    @HystrixCommand //熔断和降级都是通过这个注解实现的，当这个注解没有设置任何值时表示降级使用的是全局降级方法
    public String getPaymentBad(@PathVariable("id") Integer id){
        return paymentFeignService.paymentBad(id);
    }

    public String getPaymentBadHandleMethod(@PathVariable("id")Integer id){
        return "这里是消费者80，对方支付系统繁忙，请过一会再尝试，谢谢！你的请求id是：" + id;
    }

    public String getPaymentBadDefaultHandleMethod(){
        return "这里全局的降级处理方法";
    }
}
~~~

### 服务熔断

服务熔断是应对微服务雪崩效应的一种链路保护机制，当扇出链路的某个微服务不可用或响应时间过长时，

会进行服务熔断，进而调用服务降级，快速返回备用的响应错误信息。当检测到该节点微服务调用响应正常

后，恢复链路的调用。

在Spring Cloud框架里，熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到

一定阈值，缺省是5秒内20次调用失败，就会启动熔断机制。

基于服务降级的cloud_provider_hystrix_payment8001工程测试服务熔断

①修改service层

~~~java
@HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback",commandProperties = {
    // 是否开启断路器
    @HystrixProperty(name = "circuitBreaker.enabled",value = "true"),
    // 请求次数
    //该属性用来设置在演动时间窗中，断路器熔断的最小请求数。例如,默认该值为20的时候,
    //如果滚动时间窗(默认10秒)内仅收到19个请求,即使这19个请求都失败了,断路器也不会打开。
    @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"),
    // 时间窗口期
    //该属性用来设置当断路器打开之后的休眠时间窗。休眠时间窗结束之后,
    //会将断路器置为“半开"状态，尝试熔断的请求命令，如果依然失败就将断路器继续设置为“打开"状态,
    //如果成功就设置为“关闭"状态。
    @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"),
    // 失败率达到多少后跳闸，以这里为例10次请求超过6次失败就会熔断
    @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60"),
})
public String paymentCircuitBreaker(@PathVariable("id") Integer id)
{
    if(id < 0)
    {
        throw new RuntimeException("******id 不能负数");
    }
    String serialNumber = IdUtil.simpleUUID();

    return Thread.currentThread().getName()+"\t"+"调用成功，流水号: " + serialNumber;
}
public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id)
{
    return "id 不能负数，请稍后再试，id: " +id;
}
~~~

②修改控制层

~~~java
@GetMapping("/payment/circuit/{id}")
public String paymentCircuitBreaker(@PathVariable("id") Integer id){
    return paymentService.paymentCircuitBreaker(id);
}
~~~

#### 总结

- 熔断类型有三种：打开、半开、关闭

熔断打开：请求不会再调用当前服务，内部设置时间一般为MTTR(平均故障处理时间)，当打开时间达到所设

时间的最大值后则进入半熔断状态

熔断半开：部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务正常，关闭熔断

熔断关闭：熔断关闭不会对服务进行熔断，即正常处理请求

- 涉及断路器的三个重要参数：快照时间窗、请求总数阀值、错误百分比阀值

快照时间窗：断路器确定是否打开需要统计一些请求和错误数据，而统计的依据就是快照时间窗，默认为最近的10s。

请求总数阀值：在快照时间窗内，必须满足请求阀值才会熔断，默认为20，即在10s内，如果hystrix命令的调用次数

不足20次，即使所有的请求都超时或者失败，断路器都不会打开

错误百分比阀值：当请求总数在快照时间窗内超过了阀值，如30次调用，15次发生异常，错误百分比阀值为50，也就

是超过这个最大限制的阀值，这时候断路器就会打开。

- 断路器开启或关闭的条件

  - 开启(在断路器开启时，所有的请求都不会被转发)

    ①当满足一定阈值时（默认10秒内超过20个失败请求次数）

    ②当失败率达到一定的情况（默认10秒内超过50%的失败请求）

  - 关闭

    ①在一段时间之后(默认5秒)，断路器会变为半开状态，会让其中一个请求进行转发。

    如果成功则断路器关闭，若失败则断路器继续开启，之后不断重试这个步骤

- 断路器打开之后，当断路器开启之后，任何的请求都不会调用服务方法，而是直接去调用降级方法。

- 原来的主逻辑由hystrix实现自动恢复，就是通过上面断路器关闭的步骤来实现的

### 工作流程

1. Construct a `HystrixCommand` or `HystrixObservableCommand` Object 获取一个HystrixCommand对象
2. Execute the Command 执行 Command 
3. Is the Response Cached? 判断缓存中是否已经有数据
4. Is the Circuit Open? 判断熔断器是否开启
5. Is the Thread Pool/Queue/Semaphore Full? 判断线程池是否已满
6. HystrixObservableCommand.construct()` or `HystrixCommand.run() 执行方法
7. Calculate Circuit Health 向熔断器汇报数据，以便熔断器决定状态
8. Get the Fallback  执行降级方法
9. Return the Successful Response  返回成功响应

![](https://s1.ax1x.com/2022/08/16/v08RAS.png)

### 图形化DashBoard

1.创建子模块9001

2.改pom文件

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
~~~

3.由于SpringCloud的坑，需要在被监控的微服务的yml配置文件添加以下配置

~~~yml
#此配置是为了hystrix的dashboard服务监控而设置，与服务容错无关
#在springCloud升级之后，ServletRegistration因为springboot默认路径不是/hystrix.stream
management:
  endpoints:
    web:
      exposure:
        include: "*"
~~~

4.在主启动类上添加@EnableHystrixDashboard注解

```java
@SpringBootApplication
@EnableHystrixDashboard //开启hystrix的图形化
public class CloudConsumeHystrixDashboard9001 {
    public static void main(String[] args) {
        SpringApplication.run(CloudConsumeHystrixDashboard9001.class,args);
}
}
```

<font color='red'>**所有微服务Provider提供者都需要添加监控依赖配置**</font>

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
~~~

5.测试：在Hystrix的监控面板的监控地址是http:// + localhost: + 微服务端口 + /actuator/hystrix.stream

![](https://s1.ax1x.com/2022/08/16/v08fhQ.png)

---

# 服务网关

 ## GateWay

- 什么是GateWay

SpringCloud GateWay 使用的Webflux的reactor-netty响应式编程组件，底层使用了Netty通讯框架

### 三大核心概念

- 路由(Route)

路由是构建网关的基本模块，由ID+目标URI+一系列的断言和过滤器组成，如果断言为true则匹配该路由。

- 断言(Predicate)

开发人员可以匹配HTTP请求中的所有内容(例如请求头和请求参数)，如果请求和断言相匹配则进行路由。

- 过滤(Filter)

指的是Spring框架中GateWayFilter的实例，使用过滤器，可以在请求被路由前后对请求进行修改

![](https://s1.ax1x.com/2022/08/16/v08HBV.png)

web请求通过一些匹配条件，定位到真正的服务节点。并在转发过程的前后进行一些精细化的控制。

而在这个过程中发挥匹配条件的就是predicate，filter可以理解为一个无所不能的拦截器。有了这两个

元素再加上目标URI就可以实现一个具体的路由了。

### 工作流程

- <font color='red'>**Gateway工作核心逻辑就是路由转发+执行过滤器链**</font>

客户端向Spring Cloud GateWay发出请求，然后在Gateway Handler Mapping中找到与请求相匹配的路由，将其发送

到 Gateway Web Handler，Handler再通过指定的过滤器链来将请求发送到实际的服务执行业务逻辑中，最后返回。

过滤器之间用虚线分开是因为过滤器可能再发送代理请求之前(pre) 或 之后(post) 执行业务逻辑。

Filter在pre类型的过滤器中可以做参数校验、权限校验、流量监控、日志输出、协议转换等

Filter在post类型的过滤器中可以做响应内容、响应头修改、日志输出、流量监控等。

![](https://s1.ax1x.com/2022/08/16/v08Xh4.png)

###  网关搭建

1.创建子模块module

2.改pom文件

- 注意eureka要引入的是client的依赖，否则会造成服务启动失败

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
~~~

3.编写yml配置文件

~~~yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway-service
  cloud:
    gateway:
      routes:
        - id: payment_route # 路由的ID，没有固定规则但要求必须唯一，建议配置服务名
          uri: http://localhost:8001 #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/query/** #断言，路径相匹配的进行路由

        - id: payment_route2 # 路由的ID，没有固定规则但要求必须唯一，建议配置服务名
          uri: http://localhost:8001 #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/** #断言，路径相匹配的进行路由

#将网关注册进服务注册中心
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://test.dzsc.tk:7007/eureka/
~~~

4.主启动类

5.配置路由的两种方式

第一种：在yml配置文件中配置，参考上面第三步

第二种：代码中注册RouteLocaltor的Bean

~~~java
//网关路由配置硬编码方式
@Configuration
public class GateWayConfig {
    /**
     * Description : 配置了一个id为customer的路由规则
     * 当访问http://localhost:9527/guonei时会转发到http://news.baidu.com/guonei
     * @date 2022/8/9
     * @param routeLocatorBuilder builder
     * @return org.springframework.cloud.gateway.route.RouteLocator
     **/
    @Bean
    public RouteLocator customerRouters(RouteLocatorBuilder routeLocatorBuilder){
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
        routes.route("customer",
                r -> r.path("/guonei").uri("http://news.baidu.com"))
                .build();
        return routes.build();
    }
}
~~~

### 通过微服务名实现动态路由

- 由上面的yml配置文件看出路由地址ip和端口都被写死了，而在实际上不可能只有一个服务提供者。

默认情况下，GateWay会根据注册中心注册的服务列表，以注册中心微服务名为路径创建动态路由进行转发，

从而实现动态路由的功能

1.需要修改原来的yml配置文件

~~~yml
spring:
  application:
    name: cloud-gateway-service
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_route # 路由的ID，没有固定规则但要求必须唯一，建议配置服务名
#          uri: http://localhost:8001 #匹配后提供服务的路由地址
          uri: lb://CLOUD-PAYMENT-SERVICE  #匹配后提供服务的动态路由地址
          predicates:
            - Path=/payment/query/** #断言，路径相匹配的进行路由
~~~

2.测试

### Predicate工厂

SpringCloudGateWay内置了多个RoutePredicate工厂，所有的Predicate与Http请求的不同属性进行匹配。

SpringCloudGateWay创建Route对象时，使用RoutePredicateFactory创建Predicate对象，Prdicate对象可以

赋值给Route。多个RoutePredicate工厂可以进行组合，并通过逻辑and

![](https://s1.ax1x.com/2022/08/16/v08v9J.png)

~~~yml
spring:
  application:
    name: cloud-gateway-service
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_route # 路由的ID，没有固定规则但要求必须唯一，建议配置服务名
#          uri: http://localhost:8001 #匹配后提供服务的路由地址
          uri: lb://CLOUD-PAYMENT-SERVICE  #匹配后提供服务的动态路由地址
          predicates:
            - Path=/payment/query/** #断言，路径相匹配的进行路由
#            - After=2022-08-09T16:46:43.909+08:00[Asia/Shanghai] #断言，必须在这个时间之后才会返回true
#            - Before=2022-08-09T16:46:43.909+08:00[Asia/Shanghai] #断言，必须在这个时间之前才会返回true
#            - Between=2022-08-09T16:46:43.909+08:00[Asia/Shanghai],2022-08-09T17:46:43.909+08:00[Asia/Shanghai] #断言，必须在这个时间之间才会返回true
#            - Cookie=username,hcxs1986 # 携带cookie，且必须是hcxs1986才会返回true
            - Header=X-Request-Id,\d+ #请求头要有X-Request-Id属性且这个值为整数的正则表达式
            - Host=**.year21.top #请求头必须有.year21.top才返回true和路由
            - Method=GET #请求方式必须为get才返回true和路由
            - Query=username,\d+ #要有参数名username且值必须为整数才返回true和路由
~~~

### Filter的使用

路由过滤器可用于修改进入的HTTP请求和返回的HTTP响应，路由过滤器只能指定路由进行使用。

Spring Cloud GateWay内置了多种路由过滤器，他们都由GateWayFilter的工厂类来创建。

- Spring Cloud GateWay的Filter

①生命周期只有两种：pre 和 post

②种类也只有两种：GateWayFilter(单一Filter) 和 GlobalFilter(全局Filter)

两个种类全部的过滤器总共41种，数量过多只演示其中一种

~~~yml
routes:
  - id: payment_route # 路由的ID，没有固定规则但要求必须唯一，建议配置服务名
    uri: lb://CLOUD-PAYMENT-SERVICE  #匹配后提供服务的动态路由地址
    filters: 
      - AddRequestParmter=X-Request-Id,1024 #过滤器工厂会在匹配的请求头上加上一对请求头，名称为X-Request-Id值为1024
~~~

#### 自定义过滤器

能够全局日志记录、统一网关鉴权等等

- 自定义全局GlobalFilter

需要实现两个主要接口 implement GlobalFilter，Ordered

~~~java
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("这是全局过滤器GlobalFilter" + new Date());
        //获取请求地址中的参数
        String username = exchange.getRequest().getQueryParams().getFirst("username");
        if (username == null){
            log.info("用户名为空");
            //设置响应状态码
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            //发送流完成信息
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 0;
    }
}
~~~

---

# 服务配置

为什么需要服务配置？因为在微服务中将单体应用的业务拆分成了一个个的子服务，每个服务的粒度相对较小，

因此系统中出现大量的服务，且每个服务都需要必要的配置信息才能运行，因此有一个集中式、动态的配置管理

设施是必不可少的。

SpringCloud 提供了ConfigServer来解决这个问题。

## SpringCloud Config

- 是什么

SpringCloud Config为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为<font color='red'>**各个不同微服务**</font>应用的所有

环境提供<font color='red'>**一个中心化的外部配置。**</font>

![](https://s1.ax1x.com/2022/08/16/v08x39.png)

- 能做什么

①集中管理配置文件

②不同环境不同配置，动态化的配置更新，分环境部署比如dev/test/prod/beta/release

③运行期间动态调整配置，不再需要在每个服务部署的机器上编写配置文件，服务会向配置中心统一拉取配置自己的信息

④当配置发生变动时，服务不需要重启即可感知到配置的变化并应用新的配置

⑤将配置信息以REST接口的形式暴露，post、curl访问刷新均可......

- 怎么做

SpringCloud Config分为服务端和客户端。

①服务端也称为分布式配置中心，它是一个独立的微服务应用，用来连接配置服务器并为客户端提供配置信息，

加密/解密信息等访问接口。

②客户端则通过指定的配置中心来管理应用资源以及与业务相关的配置内容，并在启动的时候从配置中心获取和

加载配置信息。配置服务器默认采用git来存储配置信息，这样有助于对环境配置进行版本管理，并且可以通过git

客户端工具来方便的管理和访问配置内容。

### Config配置中心搭建

1、在github上创建用于设置配置中心的仓库

2、在本地电脑上利用git clone 仓库地址 的命令将仓库克隆下来。

3、创建新module

4、改pom文件

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
~~~

5、编写yml配置文件

~~~yml
server:
  port: 3344

eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://test.dzsc.tk:7007/eureka/

spring:
  application:
    name: cloud-config-center
  cloud:
    config:
      server:
        #gitee上面的git仓库
        git:
          uri: https://github.com/xxxccc1986/springcloud-config #Github上的仓库地址
          search-paths:
            - springcloud-config
          #由于设置的仓库为私有仓库，因此必须设置username和password
          #username: github账户
          #password: github密码
      #哪个分支 如master dev pro
      label: main
~~~

![](https://s1.ax1x.com/2022/08/16/v0G9nx.png)

6、主启动类

~~~java
@SpringBootApplication
@EnableConfigServer //开启配置中心的注解
public class CloudConfigCenter3344 {
    public static void main(String[] args){
        SpringApplication.run(CloudConfigCenter3344.class,args);
    }
}
~~~

### Config客户端配置

application.yml 是用户级的资源配置项

bootstrap.yml 是系统级的资源配置项，优先级更高

Spring Cloud会创建一个Bootstrap Context，作为Spring应用的Application Context的父上下文。在初始化的时候，

Bootstrap Context负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的Environment。

Bootstrap属性有高优先级，默认情况下，它们不会被本地配置覆盖。Bootstrap Context 和 Application Context

有着不同的约定，新增一个bootstrap.yml文件保证Bootstrap Context 和 Application Context配置的分离。

<font color='red'>**要将Client模块下的application.yml文件改为bootstrap.yml，这一步很关键**</font>

<font color='red'>**因此bootstrap.yml比application.yml的优先级更高，会先加载。**</font>

1、创建新module

2、改pom文件

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
~~~

3、编写bootstrap.yml配置文件

~~~yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    #config客户端配置
    config:
      label: main #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称
      #上述3个综合：main分支上config-dev.yml的配置文件被读取，整个读取连接就是 http://localhost:3344/main/config-dev.yml
      uri: http://localhost:3344 #配置中心地址

eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://test.dzsc.tk:7007/eureka/
~~~

4.主启动类

5.业务类

~~~java
@RestController
@RequestMapping("/config")
public class ConfigClientController {
    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/info")
    public String getConfigInfo(){
        return configInfo;
    }
}
~~~

### Config客户端配置动态刷新

考虑这么一种情况，当github上的yml配置文件更新之后，会发现负责整体配置中心的3344微服务会随着更新配置信息，

那么按照逻辑读取3344配置信息的client端也应该更新对吧，但是现实是3344微服务配置信息已经更新了，但是3355微服务

client读取的配置信息还是未更新之前的配置信息，这就存在很大的问题。

- 为了解决上述问题，引入了动态刷新概念，那么怎么做呢？

第一种：手动刷新

1.修改3355client微服务的yml配置文件,添加下列配置

~~~yml
#暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
~~~

2.在业务类controller添加@RefreshScope注解

~~~java
@RestController
@RequestMapping("/config")
@RefreshScope //开启配置信息动态刷新功能
public class ConfigClientController {}
~~~

3.需要手动向3355微服务发送Post 刷新请求 curl -X POST "http://localhost:3355/actuator/refresh"

- <font color='red'>**这种方法还是存在效率问题**</font>，假如100台机器就要post100次。

  那么想想是否可以广播，一次广播，处处生效，大范围的自动刷新，由此引入消息总线

  <font color='red'>**使用Spring Cloud Bus 配合 Spring Cloud Config 就可以实现配置的动态刷新**</font>

---

# 服务总线

## Bus

bus可以理解为对服务配置的加深和扩充，<font color='red'>**分布式自动刷新配置**</font>

- 什么是Bus消息总线

Spring Cloud Bus 是用来将分布式系统的节点与轻量级系统连接起来的框架，它整合了Java的事件处理机制和

消息中间件的功能，Spring Cloud Bus目前支持RabbitMQ 和 Kaflka

- 它能干什么

Spring Cloud Bus 能管理和传播分布式系统间的消息，就像一个分布式执行器，可用于广播状态更改、事件推送等，

也可以作为微服务间的通信通道

- 什么是总线

在微服务架构的系统中，通常会使用轻量级的消息代理来构建一个共用的消息主题，并让系统中的所有微服务实例

都连接上来。由于该主题中产生的消息会被所有实例监听和消费，所以称它为消息总线。在总线上的各个实例，都

可以方便的广播一些需要让其他连接在该主题上的实例都知道的消息。

基本原理：

ConfigClient实例都监听MQ中同一个topic(默认是srpingCloudBus)，当一个服务刷新数据时，它会把这个消息放入到

topic中，这样监听同一个Topic的服务就能得到通知，然后去更新自身的配置

### 设计思想

1、利用消息总线触发一个客户端/bus/refresh，而刷新所有客户端的配置

![](https://s1.ax1x.com/2022/08/16/v0GFAO.png)

2、利用消息总线触发一个服务端ConfigServer的/bus/refresh端点，从而刷新所有客户端的配置

![](https://s1.ax1x.com/2022/08/16/v0GV9H.png)

更加推荐使用第二种思想，因为第一种有副作用：

①打破了微服务的职责单一性，微服务本身是业务模块，它本不应该承担配置刷新的职责。

②破坏了微服务各节点的对等性。

③有一定的局限性。微服务在迁移时，它的网络地址常常会发生变化，此时如果想要做到自动刷新，那就会增加更多的修改

### bus动态刷新全局广播

> 基于上述的3355微服务工程创建3366工程，步骤差不多

1.开启配置中心3344服务端对消息总线的支持

①添加依赖到3344的pom文件中

~~~xml
<!-- 添加消息总线RabbitMQ支持-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
~~~

②添加配置到3344的yml文件中

~~~yml
spring:
#配置rabbitmq的配置
  rabbitmq:
    host: 192.168.231.134
    port: 5672
    username: admin
    password: admin
  
#rabbitmq相关配置，暴露bus刷新配置的端点
management:
  endpoints:
    web:
      exposure:
        include: "bus-refresh" #这个端点很重要，用于3344通知其他客户端获取信息
~~~

2.开启客户端3355对消息总线的支持

①添加依赖到3355的pom文件中

~~~xml
<!-- 添加消息总线RabbitMQ支持-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
~~~

②添加配置到3355的yml文件中

~~~yml
spring:
#配置rabbitmq的配置
  rabbitmq:
    host: 192.168.231.134
    port: 5672
    username: admin
    password: admin
~~~

3.开启客户端3366对消息总线的支持

①添加依赖到3366的pom文件中

~~~xml
<!-- 添加消息总线RabbitMQ支持-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
~~~

②添加配置到3366的yml文件中

~~~yml
spring:
#配置rabbitmq的配置
  rabbitmq:
    host: 192.168.231.134
    port: 5672
    username: admin
    password: admin
~~~

4.当配置信息发送变化后，向3344发送一个post：curl -X POST "http://localhost:3344/actuator/bus-refresh"

### bus动态刷新定点通知

- 考虑一个情况，如果只想通知某个client，指定具体某一个实例生效而不是全部 该怎么做？

只需要在配置中心发生变化了向3344配置中心发生下面的post命令 

公式：curl -X POST “http://localhost:配置中心的端口号/actuator/bus-refresh/{destination}”

这个/bus/refresh/{destination}表示请求不再发送到具体的服务实例上，而是发给config server并通过

destination参数来指定需要更新配置的服务或实例

### 通知总结

![](https://s1.ax1x.com/2022/08/16/v0GegA.png)

---

# SpringCloud Stream

- 为什么要引入Spring Cloud Stream消息驱动？

考虑到由于在交互过程中使用的消息中间可能不一致，例如上游使用RabbitMQ或ActiveMQ，下游可能使用的是

RocketMQ或者Kafla，这就造成了在切换、维护、开发这些过程中存在兼容性等问题，增加了开发难度，而Spring 

Cloud Stream消息驱动可以让不再关注具体MQ的细节，只需要用一种适配绑定的方式，自动在各种MQ之间进行切换。

就类似JDBC驱动和各种数据库一样，提供一个接口，让数据库厂商来实现，由此不需要关注底层实现的具体是什么，

只需要要使用JDBC进行开发即可。

换句话来说就是：<font color='red'>**屏蔽底层消息中间件的差异，降低切换成本，统一消息的编程模型**</font>

- 是什么

Spring Cloud Stream 是一个构建消息驱动微服务的框架。<font color='red'>**目前仅支持RabbitMQ、Kafka。**</font>

应用程序通过 inputs 或者 outputs 来与 Spring Cloud Stream中binder对象交互。通过配置来binding(绑定) ，

而 Spring Cloud Stream 的 binder对象负责与消息中间件交互。所以，我们只需要搞清楚如何与 SpringCloudStream 

交互就可以使用消息驱动的方式。通过使用Spring Integration来连接消息代理中间件以实现消息事件驱动。

Spring Cloud Stream 为消息中间件产品提供了自动化配置实现，引用了发布-订阅、消费组、分区的三个核心概念。

## 设计思想

- stream如何统一底层差异？  

<font color='red'>**通过定义绑定器Binder作为中间层，实现了应用程序与消息中间件细节之间的隔离。**</font>

通过向应用程序暴露统一的Channel通道，使得应用程序不需要再考虑各种不同的消息中间件实现。

![](https://s1.ax1x.com/2022/08/16/v0GKDP.png)



Binder可以生成Binding，Binding用来绑定消息容器的生产者和消费者。

它有两种类型，INPUT和OUTPUT，INPUT对应于消费者，OUTPUT对应于生产者。

INPUT对应于消费者表示消费者监听INPUT通道获取消息，OUTPUT对应于生产者表示生产者从OUTPUT通道发送消息

Stream中的消息通信方式遵循了发布-订阅模式 --> Topic主题进行广播 --> 在RabbitMQ就是Exchange,在Kakfa中就是Topic

## 常用注解

- Spring Cloud Stream标准流程套路

![](https://s1.ax1x.com/2022/08/16/v0GlE8.png)

Binder：连接中间件，屏蔽差异

Channel：通道，是队列Queue的一种抽象，在消息通讯系统中就是实现存储和转发的媒介，通过Channel对队列进行配置

Source和Sink：简单的可理解为参照对象是Spring Cloud Stream自身，从Stream接受消息就是输入，发布消息就是输出。

- 常用注解

![](https://s1.ax1x.com/2022/08/16/v0G1US.png)

![](https://s1.ax1x.com/2022/08/16/v0G34g.png)

## 消息驱动生产者

1.创建子模块cloud-stream-rabbitmq-provider8801

2.修改pom文件，最主要就是添加下面的依赖，其他依赖可以从别的工程cv

~~~xml
<!-- 添加消息驱动stream+RabbitMQ支持-->
<dependency>
<groupId>org.springframework.cloud</groupId>
<artifactId>spring-cloud-starter-stream-rabbit</artifactId>
</dependency>
~~~

3.编写yml文件

~~~yml
server:
  port: 8801

spring:
  application:
    name: cloud-steam-provider
  cloud:
    stream:
      binders:  #配置要绑定的rabbitmq的服务器，可以配置多个binder，也就是可以配置连接多个MQ服务器
        rabbit1:  #定义用于binding整合的名称，可随便写，只需要保证与下方binder一致即可
          type: rabbit #消息中间件的类型
          environment: #设置rabbitmq的相关环境配置
            spring:
              rabbitmq:
                host: 192.168.231.134
                port: 5672
                username: admin
                password: admin
      bindings:  #服务的整合处理
       # 为了将消息发出去并且发送至目的地址，就需要配置相关属性。
       #下面这些配置简单来说就是将消息发送到在rabbit1设置中的studyExchange交换机，用名为output的通道将消息发送出去
        output: #用于将消息发送出去的通道名称,这是cloud-steam的默认输出通道名
          destination: studyExchange # 消息将要被发送到的的Exchange名称，在启动后rabbit会创建一个与此名字相同的topic类型交换机
          content-type: application/json #设置消息发送类型，本次设置为json，文本则设置为"text/plain"
          binder: rabbit1 #设置要绑定的消息服务的具体类型

  #也需要设置这个才不会出现Rabbit health check failed
  rabbitmq:
    host: 192.168.231.134
    port: 5672
    username: admin
    password: admin

eureka:
  client:
    register-with-eureka: true
    service-url:
      defaultZone: http://test.dzsc.tk:7007/eureka/
~~~

4.主启动类

5.业务类

~~~java
//该注解的作用是告诉spring cloud stream此应用是一个消息发布者，需要绑定到详细中间件。
@EnableBinding(Source.class)//同时这是个复合注解，其中的@Configuration也将这个对象放入了Spring容器
public class IMessageServiceProviderImpl implements IMessageServiceProvider{

    @Resource //这里的output与application.yml配置文件中的bindings名称是对应的
    private MessageChannel output;

    @Override
    public String sendMsg() {
        String msg = UUID.randomUUID().toString();
        /* send()方法是使用消息输出通道将消息发送出去
        消息都是靠消息媒介Message对象来传递的，因此
        MessageBuilder.withPayload(msg).build()就是构建了一个Message对象
        */
        output.send(MessageBuilder.withPayload(msg).build());
        System.out.println("/* " + msg + " */");
        return null;
    }
}
~~~

## 消息驱动消费者

1.创建子模块cloud_stream_rabbitmq_consume8802

2.修改pom文件，与上方8801一致，可以直接copy

3.编写yml文件

与上面的消息生产者的yml类似，如果使用默认的消息输入通道名只需要将output改为input

4.主启动类

5.业务类

~~~java
//该注解的作用是告诉spring cloud stream此应用是一个消息接收者，需要绑定到中间件。
@EnableBinding(Sink.class)   //同时这是个复合注解，其中的@Configuration也将这个对象放入了Spring容器
public class ConsumerController {

    @Value("${server.port}")
    private String port;

    @StreamListener(value = "input") //表示监听的通道名为input，与application.yml配置文件内的bindings设置的一样
//    @StreamListener(Sink.INPUT) 这个Sink.INPUT枚举对象的值和上面input是一样的
    public void input(Message<String> msg){
        System.out.println("消费者1接收到的消息是：" + msg.getPayload() + "\t" + port);
    }
}
~~~

## 消息重复消费

- 不应该重复消费同一消息。

在分布式服务的不同实例属于竞争关系，一个消息只能被同个组的某一个实例处理，不应该被同个组的多个实例所处理

- 原理是：<font color='red'>**不同的组是可以重复消费的，同一个组内会发生竞争关系，只有其中一个可以消费。** </font>

<font color='red'>**怎么解决？将同个都变成相同的group组**</font> ，这里的组名其实相当于交换机中的队列名字

唯一要做的事情就是在yml文件中添加group设置相同的组名

~~~yml
spring:
  cloud:
    stream:
 	  bindings: #服务的整合处理
        # 为了接收来自生产者的消息，就需要配置相关属性。
        #下面这些配置简单来说就是将消息从在rabbit1设置中的studyExchange交换机，用名为input的通道接收消息
        input: #用于接收消息的通道名称,这是cloud-steam的默认输入通道名
          destination: studyExchange # 消息将要被发送到的的Exchange名称，在启动后rabbit会创建一个与此名字相同的topic类型交换机
          content-type: application/json #设置消息发送类型，本次设置为json，文本则设置为"text/plain"
          binder: rabbit1 #设置要绑定的消息服务的具体类型
          group: group1 #设置分组的名字 这里的组名其实相当于交换机中的队列名字
~~~

## 消息持久化

mq会发送消息到与之绑定的队列, 如果没有消费者消费, 就会积压这时, 如果消费者没有人为指定队列名的情况下宕机

下次重启, 就会被mq分配一个新的随机队列无法和原来的老队列连接, 自然无法接收到老队列里面的消息，

所以就会造成消息的丢失，解决办法就是人为指定消费者的队列名称(或者理解为组名)，

这样即使消费者重启, 也会连接到老的队列, 进行积压消息的消费。

---

# SpringCloud Sleuth

- 为什么要引入SpringCloud Sleuth分布式请求链路追踪？

 在微服务框架中，一个由客户端发起的请求在后端系统中会经过多个不同的的服务节点调用来协同产生最后的

请求结果，每一个前端请求都会形成一条复杂的分布式服务调用链路，链路中的任何一环出现高延时或错误都会

引起整个请求最后的失败。因此必须引入SpringCloud Sleuth

- 是什么

Spring Cloud Sleuth提供了一套完整的服务跟踪的解决方案，在分布式系统中提供追踪解决方案并且兼容支持了zipkin

 Sleuth负责监控链路调用，zipkin负责展示链路调用

## zipkin监控面板搭建

SpringCloud从F版起已不需要自己构建Zipkin Server了，只需调用jar包即可

 [zipkin下载地址](https://repo1.maven.org/maven2/io/zipkin/java/zipkin-server/2.12.9/)

运行 java -jar zipkin-server-2.12.9-exec.jar 即可启动成功，访问路径为ip + 9411端口

- 表示一条请求链路，一条链路通过Trace Id唯一标识，Span标识发起的请求信息，各span通过parent id 关联起来

完整调用链路

![](https://s1.ax1x.com/2022/08/16/v0GGCQ.png)

- 精简版

![](https://s1.ax1x.com/2022/08/16/v0Gtvn.png)

## 链路监控展示

> 以最开始的consumer80和payment8001作为例子

1.添加sleuth+zipkin的依赖到各自的pom文件

~~~xml
<!-- 包含了sleuth+zipkin的整合依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
~~~

2.添加新的配置到yml配置文件中

~~~yml
spring:
  application:
    name: cloud-payment-service #设置注册到EurekaServer注册中心使用的名字
  zipkin:
    base-url: http://localhost:9411
  sleuth:
    sampler:
      #采样率值介于 0 到 1 之间，1 则表示全部采集
      probability: 1   
~~~

3.在各自控制层添加一个方法进行测试

~~~java
//8001
@GetMapping("/zipkin")
public String paymentZipkin()
{
    return "这里是paymentzipkin server fall back";
}

//80
@GetMapping("/payment/zipkin")
public String paymentZipkin()
{
    String result = restTemplate.getForObject("http://localhost:8001"+"/payment/zipkin/", String.class);
    return result;
}
~~~

---

# SpringCloud Alibaba 

- 为什么会出现SpringCloud Alibaba？

Spring Cloud Netflix项目(eureka、ribbon、feign、zuul、config、hystrix)进入维护模式，部分组件不再更新

- 是什么

SpringCloud Alibaba 是由一些阿里巴巴的开源组件和云产品组成。

![](https://s1.ax1x.com/2022/08/16/v0GUuq.png)

- 能干嘛

①服务限流降级：默认支持 Servlet、Feign、RestTemplate、Dubbo 和 RocketMQ 限流降级功能的接入，

可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。

②服务注册与发现：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。

③分布式配置管理：支持分布式系统中的外部化配置，配置更改时自动刷新。

④消息驱动能力：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。

⑤阿里云对象存储：阿里云提供的海量、安全、低成本、高可靠的云存储服务。

支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。

⑥分布式任务调度：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的

任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。

- 下载地址及文档

[官方下载地址](https://github.com/alibaba/nacos/releases)

[阿里巴巴官方文档中文版](https://github.com/alibaba/spring-cloud-alibaba/blob/2.2.x/README-zh.mdl)

[spring官方文档](https://spring.io/projects/spring-cloud-alibaba#overview)

- 1.1.4版本启动

在nacos下的文件夹进入dos窗口，输入startup.cmd 即可启动，命令运行成功后直接访问http://localhost:8848/nacos

默认账户密码都是nacos

## Nacos

Nacos前四个字母分别为Naming和Configuration的前两个字母，最后的s为Service。

- 是什么

一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

换句话来说<font color='red'>：**Nacos就是注册中心和配置中心的组合，Nacos = Eureka + Config + Bus**</font>

- 能干吗

<font color='red'>**Nacos可以作为服务注册中心和服务配置中心**</font>

替代eureka作为服务注册中心，替代config作为服务配置中心

- nacos的优势

  - <font color='red'>**nacos天生就支持负载均衡**</font>，为什么？

    因为nacos的依赖中集成了ribbon从而实现了消费端的负债均衡。

  - Nacos 支持AP和CP模式的切换

    C是所有节点在同一时间看到的数据是一致的；而A的定义是所有的请求都会收到响应。

![](https://s1.ax1x.com/2022/08/16/v0GdbV.png)

### 服务注册中心

#### 服务提供者注册

- [参考资料](https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html)

1.创建子项目module

2.修改pom文件 

①需要在父工程的pom文件引入下列依赖

~~~xml
<!--spring cloud alibaba 2.1.0.RELEASE-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.1.0.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
~~~

②需要在子工程的pom文件引入下列依赖

~~~xml
<!--spring cloud alibaba nacos-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
~~~

3.编写yml配置文件

~~~yml
server:
  port: 9001
spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos注册中心地址

#暴露所有的监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
~~~

4.主启动类

~~~java
@SpringBootApplication
@EnableDiscoveryClient //让该服务在注册中心注册和从注册中心获取其他服务
public class Payment9001 {
    public static void main(String[] args){
        SpringApplication.run(Payment9001.class,args);
    }
}
~~~

5.业务类

~~~java
@RestController
@RequestMapping("/nacos")
public class PaymentController {
    @Value("${server.port}")
    private String port;
    
    @GetMapping("/payment/info")
    public String getPort(){
        return "提供服务的端口号为："  + port;
    }
}
~~~

- 为演示nacos的负载均衡，使用复制配置方式创建9011工程，也可以新建，只需要改端口即可

#### 服务消费者注册

1.创建子项目module

2.修改pom文件 

需要在子工程的pom文件引入下列依赖

~~~xml
<!--spring cloud alibaba nacos-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
~~~

3.编写yml配置文件

~~~yml
server:
  port: 83
spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos注册中心地址

#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider
~~~

4.主启动类

5.业务类

~~~java
@RestController
@RequestMapping("/nacos")
public class OrderController {
    @Autowired
    private RestTemplate restTemplate;
    @Value("${service-url.nacos-user-service}")
    private String serverUrl;
    @GetMapping("/getport")
    public String getServerPort(){
        return restTemplate.getForObject(serverUrl+"/nacos/payment/info",String.class);
    }
}
~~~

### 服务配置中心

#### 基础配置

1.创建子项目module

2.修改pom文件 

需要在子工程的pom文件引入下列依赖

~~~xml
<!--nacos-config-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
~~~

3.需要创建bootstrap.yml和application.yml配置文件

~~~yml
#bootstrap.yml
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #查找指定yaml格式的配置
~~~

~~~yml
#application.yml
spring:
  profiles:
    active: dev # 表示开发环境
~~~

4.主启动类

5.业务类

~~~java
@RestController
@RequestMapping("/nacos")
@RefreshScope //支持Nacos的动态刷新功能,同个SpringCloud原生注解@RefreshScope实现配置自动更新
public class ConfigController {
    @Value("${config.info}")
    private String info;

    @GetMapping("/config")
    public String getInfo(){
        return "这是从nacos配置中心获取的配置信息内容：" + info;
    }
}
~~~

6.在Nacos的8848页面中添加配置信息

- 这里不得不先提及Nacos中的匹配规则

在 Nacos Spring Cloud 中，`dataId` 的完整格式如下：

```tex
${prefix}-${spring.profiles.active}.${file-extension}

由上方可以得出最终在nacos配置中心上设置的格式为：
${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}
```

①prefix默认为 spring.application.name 的值，也可以通过配置项 spring.cloud.nacos.config.prefix来配置。

②spring.profiles.active 即为当前环境对应的 profile， 注意：当 spring.profiles.active为空时，

对应的连接符 `-` 也将不存在，dataId 的拼接格式变成 ${prefix}.${file-extension}

③file-exetension为配置内容的数据格式，可以通过配置项 spring.cloud.nacos.config.file-extension来配置。

目前只支持 properties 和 yaml 类型。

![换成yml就是这样](https://s1.ax1x.com/2022/08/17/vDueGn.png)

![](https://s1.ax1x.com/2022/08/17/vDum2q.png)

#### 分类配置

- nacos提供了命名空间 + 配置管理解决以下问题？

其一：众所周知，配置文件有可能分为dev开发环境、test测试环境、prod生产环境。

如何保证指定环境启动时服务能正确读取到Nacos上相应环境的配置文件呢？

其二：一个大型分布式微服务系统会有很多微服务子项目，每个微服务项目又都会有

开发环境、测试环境、预发环境、正式环境等等.那怎么对这些微服务配置进行管理呢？

- Namespace+Group+Data ID三者关系？为什么这么设计？

> 有点类似Java里面的package名和类名

最外层的namespace是可以用于区分部署环境的，Group和DataID逻辑上区分两个目标对象。

![](https://s1.ax1x.com/2022/08/17/vDunx0.png)

默认情况：Namespace=public，Group=DEFAULT_GROUP, 默认Cluster是DEFAULT

Nacos默认的命名空间是public，Namespace主要用来实现隔离。

假如有三个环境：开发、测试、生产环境，那么就创建三个Namespace，不同的Namespace之间是隔离的。

Group默认是DEFAULT_GROUP，Group可以把不同的微服务划分到同一个分组里面去

Service就是微服务；一个Service可以包含多个Cluster（集群），Nacos默认Cluster是DEFAULT，

Cluster是对指定微服务的一个虚拟划分。

> - 举个例子
>
> 比方说为了容灾，将Service微服务分别部署在了杭州机房和广州机房，这时就可以给杭州机房的Service微服务
>
> 起一个集群名称（HZ），给广州机房的Service微服务起一个集群名称（GZ），还可以尽量让同一个机房微服务
>
> 互相调用，以提升性能。最后是Instance，就是微服务的实例。

##### DataID方案加载配置

- <font color='red'>**即先找到默认命名空间，再找到默认分组，再找到两个yml结合设置的要读取的配置文件名**</font>

①使用默认命名空间 + 默认分组 + 新建dev和test两个DataID

②通过修改application.yml配置文件的spring.profile.active属性就能进行多环境下配置文件的读取

~~~yml
#Nacos注册配置，application.yml
spring:
  profiles:
#    active: dev # 表示开发环境
    active: test # 表示测试环境
~~~

##### Group方案加载配置

- <font color='red'>**即先找到默认命名空间，再找到设置的分组名，再找到两个yml结合设置的要读取的配置文件名**</font>

①使用默认命名空间 + 自定义分组名 + 新建dev和test的分组

②在bootstrap.yml设置读取的配置文件所在的分组

​	在application.yml设置读取的配置文件环境

~~~yml
#bootstrap.yml
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #查找指定yaml格式的配置
        group: DEV_GROUP #设置配置文件读取的分组名

#application.yml
#Nacos注册配置，application.yml
spring:
  profiles:
    active: info # 测试不同分组文件读取        
~~~

##### Namespace方案加载配置

- <font color='red'>**即先找到设置的命名空间，再找到设置的分组名，再找到两个yml结合设置的要读取的配置文件名**</font>

①新建dev\test\default的命名空间 + 自定义分组名 + 新建dev和test和default的DataID

②在bootstrap.yml设置读取的配置文件所在的命名空间

​	在application.yml设置读取的配置文件环境

~~~yml
#bootstrap.yml
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #查找指定yaml格式的配置
        namespace: d8529b7b-de1f-47de-84c2-dbd200673375 #设置配置文件读取的命名空间
        group: DEV_GROUP #设置配置文件读取的分组名

#application.yml
#Nacos注册配置，application.yml
spring:
  profiles:
    active: dev # 表示开发环境
~~~

### 持久化配置

- 为什么当nacos重启后页面之前的命名空间和配置列表依旧存在？

每个nacos都默认自带一个用于存储数据持久化的的数据库(嵌入式数据库derby)，虽然保证了持久化问题，但是

这也就意味数据存储是存在一致性问题的，因此Nacos采用了集中式存储的方式来支持集群化部署，

但目前只支持MySQL的存储。

- <font color='red'>**持久化切换配置，derby到mysql切换配置步骤**</font>

①nacos-server-1.1.4\nacos\conf目录下找到sql脚本(这是导入table的脚步，也就是需要先创建数据库)

②nacos-server-1.1.4\nacos\conf目录下找到application.properties，添加下列配置

~~~properties
spring.datasource.platform=mysql
 
db.num=1
#这个地址后面那些参数及其关键不能换掉
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
db.user=root
db.password=root
~~~

### nacos集群(linux版)

- Nacos支持模式

①单机模式：用于测试和单机试用

②集群模式：用于生产环境，确保高可用

③多集群模式：用于多数据中心场景

![官方nacos集群的直白理解](https://s1.ax1x.com/2022/08/17/vDuKMV.png)

- linux版的nacos集群

①安装下载 --> [官方下载地址](https://github.com/alibaba/nacos/releases/tag/1.3.1)

②解压到指定的压缩包 tar -zxvf  xxxxx.gz

③修改nacos\conf目录下application.properties，添加的信息同上面设置的一样，只需修改参数

④Linux服务器上nacos的集群配置cluster.conf，表示这三个ip的同一组的nacos集群节点 

~~~sh
#nodes list 这里构建是伪集群，按照道理这三个ip不应该相同，但为了省事就这样
192.168.231.134:3333
192.168.231.134:4444
192.168.231.134:5555
~~~

⑤基于第四点才需要编辑Nacos的启动脚本startup.sh，使它能够接受不同的启动端口

~~~sh
#o是新添加的代表启动端口的机器
while getopts ":m:f:s:c:p:o:" opt
do
    case $opt in
        m)
            MODE=$OPTARG;;
        f)
            FUNCTION_MODE=$OPTARG;;
        s)
            SERVER=$OPTARG;;
        c)
            MEMBER_LIST=$OPTARG;;
        p)
            EMBEDDED_STORAGE=$OPTARG;;
	  	o)  
			PORT=$OPTARG;;
			
p $JAVA -Dserver.port=${PORT} ${JAVA_OPT} nacos.nacos >> ${BASE_DIR}/logs/start.out 2>&1 &			
~~~

⑥修改nginx的配置

配置反向代理+负载均衡

~~~conf
#upstream用于设置nginx的负载均衡
upstream cluster{
        server 192.168.231.134:3333;
        server 192.168.231.134:4444;
        server 192.168.231.134:5555;
}

server {
        listen       1111; #监听端口
        server_name  localhost;

        location / {
		proxy_pass http://cluster; 
		}
~~~

⑦进入nacos的bin目录 使用 ./startup.sh -o 端口号分别启动三个实例

ps -ef|grep nacos|grep -v grep |wc -l 可以查看具体启动了几个实例

⑧指定nginx启动指定的conf文件	./nginx -c /usr/local/nginx/nginx.conf

- 总结就是

![](https://s1.ax1x.com/2022/08/17/vDuMrT.png)

---

## Sentinel

Sentinel是分布式系统的流量防卫兵，实现了流量控制、速率控制、熔断与降级，<font color='red'>**与Hystrix熔断器的作用类似**</font>

- 与Hystrix的区别

①hystrix需要手动搭建监控dashboard平台，相对而言没有一套web界面可以进行更加细粒度的配置

②sentinel作为一个单独的组件，可以独立出来，且直接提供web界面化的细粒度统一配置

- 主要特征

![](https://s1.ax1x.com/2022/08/17/vDuQqU.png)

### 下载运行

- sentinel组件由前台和后台构成 
  - 核心库(Java客户端)不依赖任何框架/库，能运行在java的运行时环境，且对SrpingCloud框架也较为支持。
  - 控制台(Dashboard)基于Springboot开发，打包之后可直接运行，不再需要tomcat等容器

- 下载运行

[sentinel官方下载地址](https://github.com/alibaba/Sentinel/releases/tag/1.7.0)

其下载的是个jar包，因此可以直接通过 java -jar xxxx.jar启动，默认端口为8080，但可以通过	--server.port=端口

的方式指定端口运行，登录账户密码都是sentinel

### 初始化监控

1.创建子项目module8401

2.修改pom文件 ,引入下列依赖

~~~xml
<!--SpringCloud ailibaba sentinel -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
~~~

3.编写yml配置文件

~~~yml
#简单阐述一下这个yml的配置意思
#8401端口微服务将注册在8848nacos配置中心上
#8080端口提供sentinel监控微服务的数据的展示界面访问
#8719是http通信服务【sentinel后台监控服务】，它监控8401【微服务】的同时，
#还与8080【sentinel前台展示服务】交互数据，将监控到的数据在dashboard上展现。
server:
  port: 8401

spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        #Nacos服务注册中心地址
        server-addr: localhost:8848
    sentinel:
      transport:
        #配置Sentinel dashboard地址
        dashboard: localhost:8080
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
        port: 8719

management:
  endpoints:
    web:
      exposure:
        include: '*'
~~~

4.主启动类

5.业务类

~~~java
@RestController
@RequestMapping("/sentinel")
public class FlowLimitController {
    @GetMapping("/testA")
    public String testA()
    {
        return "------testA";
    }

    @GetMapping("/testB")
    public String testB()
    {
        return "------testB";
    }
}
~~~

### 流控规则

![](C:\Users\hcxs1986\AppData\Roaming\Typora\typora-user-images\image-20220813010447654.png)

#### 流控模式

- 直接模式(系统默认使用模式)

以api地址为资源名，每秒钟请求数作为限流标准，超过限制后直接快速返回默认的失败提示。

这种方式缺乏了灵活性，不能自定义调整失败提示

**举例如下:**

下图以QPS作为限流标准，1秒钟内查询1次就是OK，若超过次数1，就直接-快速失败，报默认错误

![](https://s1.ax1x.com/2022/08/17/vDu3a4.png)

需要注意，上图中的阈值类型，还有一个线程数，这个跟QPS又有什么区别呢？

相对理解就是<font color='red'>**QPS每秒只能处理一个请求的话，只要超过这个数就会限流报错，**</font>

<font color='red'>**而线程数是指也许一秒内有很多线程请求，但是只能处理最快的线程的请求，剩余同时请求的线程限流报错**</font>

- 关联模式

当关联的资源达到限流的标准后，就限流自己。

例如订单服务关联支付服务，当支付服务的请求超过订单服务限流标准，则订单服务就会对调用自己的请求限流

- 链路模式

当有多个请求调用的某个微服务api时，对其中某个请求的路径设置了限流，超过就会使得从这个路径进入的请求失败

举例如下，

1.由于sentinel1.7版本原因，先引入以下依赖

~~~xml
<!--   链路模式限流需要的依赖   -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-web-servlet</artifactId>
    <version>1.8.0</version>
</dependency>
~~~

2.创建配置类创建一个过滤器类

~~~java
@Configuration
public class FilterCOntextConfig {
        /**
         * @NOTE 在spring-cloud-alibaba v2.1.1.RELEASE及前，sentinel1.7.0及后，关闭URL PATH聚合需要通过该方式，spring-cloud-alibaba v2.1.1.RELEASE后，可以通过配置关闭：spring.cloud.sentinel.web-context-unify=false
         * 手动注入Sentinel的过滤器，关闭Sentinel注入CommonFilter实例，修改配置文件中的 spring.cloud.sentinel.filter.enabled=false
         * 入口资源聚合问题：https://github.com/alibaba/Sentinel/issues/1024 或 https://github.com/alibaba/Sentinel/issues/1213
         * 入口资源聚合问题解决：https://github.com/alibaba/Sentinel/pull/1111
         */
        @Bean
        public FilterRegistrationBean sentinelFilterRegistration() {
            FilterRegistrationBean registration = new FilterRegistrationBean();
            registration.setFilter(new CommonFilter());
            registration.addUrlPatterns("/*");
            // 入口资源关闭聚合
            registration.addInitParameter(CommonFilter.WEB_CONTEXT_UNIFY, "false");
            registration.setName("sentinelFilter");
            registration.setOrder(1);
            return registration;
        }
}
~~~

3.在yml配置文件中关闭过滤器

~~~yml
spring:
  cloud:    
    sentinel:
      # 关闭Sentinel过滤器解决链路规则失效的问题
      filter:
        enabled: false
~~~

4.创建一个业务类方法用于测试，由控制层进行调用

~~~java
//创建一个业务类方法用于测试
@Service
public class TestService {
    //定义限流资源
    @SentinelResource("link")
    public String testLink(){
        return "链路模式测试成功";
    }
}

//由控制层进行调用
@GetMapping("/testc")
public String testC()
{
    return testService.testLink();
}
~~~

5.对限流资源设置限流标准，切记不能对其他非限流资源做设置

![](https://s1.ax1x.com/2022/08/17/vDu8IJ.png)

6.进行测试，返回默认的error错误页面即为限流成功。

#### 流控效果

- 直接 --> 快速失败(默认的流控处理)
- 预热(冷启动)

公式：阈值除以coldFactor(默认值为3),经过预热时长后才会达到阈值

举个例子，当预热时长设置为10s时，那么一开始阈值为10/3约等于3，而经过10s后，阈值就变为了15

<font color='red'>**是为了防止一瞬间的高流量打崩系统，提供一定的缓冲时间保护系统**</font>

![](https://s1.ax1x.com/2022/08/17/vDutR1.png)

- 匀速排队

匀速排队，让请求以均匀的速度通过，阀值类型必须设成QPS，否则无效。

设置含义：/testA每秒1次请求，超过的话就排队等待，等待的超时时间为20000毫秒。

<font color='red'>**这种方式用于处理间隔性突发的流量**</font>

![](https://s1.ax1x.com/2022/08/17/vDuNxx.png)

### 降级规则

<font color='red'>**与Hystrix断路器不同的是 Sentinel的断路器是没有半开状态的**</font>

- RT（平均响应时间，秒级）

**QPS >= 5 且平均响应时间超出设置阈值，两个条件同时满足后触发降级。**

窗口期过后关闭断路器，RT最大4900（更大的需要通过-Dcsp.sentinel.statistic.max.rt=XXXX才能生效）

![](https://s1.ax1x.com/2022/08/17/vDuaM6.png)

- 异常比列（秒级）

QPS >= 5 且异常比例（秒级统计）超过阈值时，触发降级；时间窗口结束后，关闭降级 

![](https://s1.ax1x.com/2022/08/17/vDudsK.png)

- 异常数（分钟级）

异常数（分钟统计）超过阈值时，触发降级；时间窗口结束后，关闭降级

<font color='red'>**时间窗口一定要大于等于60秒。**</font>

![](https://s1.ax1x.com/2022/08/17/vDuwqO.png)

### 热点规则

- 是什么

热点即经常访问的数据，希望统计或者限制某个热点数据中访问最高的数据，并对其访问进行限流或者其它操作

#### 限制热点key案例

1.在控制类空添加方法

~~~java
@GetMapping("/testHotKey")
//@SentinelResource用于定义资源名，value指定义热点规则资源名，blockHandler值为违背热点规则后的兜底方法
@SentinelResource(value = "testHotKey",blockHandler = "dealHandler_testHotKey")
public String testHotKey(@RequestParam(value = "p1",required = false) String p1,
                         @RequestParam(value = "p2",required = false) String p2){
    return "------testHotKey";
}

public String dealHandler_testHotKey(String p1, String p2, BlockException exception)
{
    return "-----dealHandler_testHotKey";
}
~~~

2.在sentinel的热点规则进行配置

![](https://s1.ax1x.com/2022/08/17/vDuBZD.png)

上图的参数索引指的是@SentinelResource标注的方法上的参数index下标

- 上图配置结合控制类中的方法表示

@SentinelResource标注的方法上第一个参数只要QPS请求超过每秒1次，就会触发blockHandler属性定义的方法，

进行降级处理，但需要注意两项规则，<font color='red'>**如果没有携带指定@SentinelResource标注的方法上第一个参数，则不会报错**</font>

<font color='red'>**如果没有设置blockHandler属性，就会返回默认的error报错页面**</font>

#### 参数例外项

- 当希望限流的参数为某些确定值时阈值不同于其他值时，就需要设置例外项 

比如，当参数索引为0 的值 =  5 时，限流阈值为200；不等于5时，限流阈值等于 1 ；

热点参数的注意点，参数必须是基本类型或者String

@SentinelResource只能管理在sentine配置中心设置的规则，当程序内部出问题时，不发挥作用

![](https://s1.ax1x.com/2022/08/17/vDuDde.png)

### 系统规则

![](https://s1.ax1x.com/2022/08/17/vDuyid.png)

### SentinelResource配置

![](https://s1.ax1x.com/2022/08/17/vDu6JA.png)

- blockHandler属性

如果有设置blockHandler属性，当超过限流标准就会使用blockHandler属性指定的方法处理

如果没有设置blockHandler属性，当超过限流标注就会使用sentinel自带的默认处理方法

~~~java
@RestController
public class RateLimitController {

    @GetMapping("/byResource")
    @SentinelResource(value = "byResource",blockHandler = "handleException")
    public JsonResult byResource() {
        return new JsonResult(200,"按资源名称限流测试OK",new Payment(1,"test"));
    }

    public JsonResult handleException(BlockException exception) {
        return new JsonResult(444,exception.getClass().getCanonicalName()+"\t 服务不可用");
    }

    @GetMapping("/rateLimit/byUrl")
    @SentinelResource(value = "byUrl")
    public JsonResult byUrl() {
        return new JsonResult(200,"按url限流测试OK",new Payment(2022,"serial002"));
    }
}
~~~

- 上述的blockHandler属性无论设置与否都会面临一个与hystrix一样问题：

①系统默认的，没有体现我们自己的业务要求。

②依照现有条件，我们自定义的处理方法又和业务代码耦合在一块，不直观。

③每个业务方法都添加一个兜底的，那代码膨胀加剧。

④全局统一的处理方法没有体现。

- 如何解决上述问题呢？

1.创建CustomerBlockHandler类用于自定义限流处理逻辑

~~~java
//自定义限流处理类,用于自定义限流处理逻辑
@Component
public class CustomerBlockHandler {
    //自定义处理方法
    public static JsonResult customerHandleException(BlockException exception){
        return new JsonResult(411,"自定义的限流处理信息......CustomerBlockHandler");
    }
}
~~~

2.在业务类中的标注@SentinelResource的属性上添加fallback属性

~~~java
/**
     * 自定义通用的限流处理逻辑，
     blockHandlerClass = CustomerBlockHandler.class
     blockHandler = customerHandleException
     上述配置：找CustomerBlockHandler类里的customerHandleException方法进行兜底处理
     */
@GetMapping("/rateLimit/customerBlockHandler")
@SentinelResource(value = "customerBlockHandler",
                  blockHandlerClass = CustomerBlockHandler.class, blockHandler = "customerHandleException")
public JsonResult customerBlockHandler()
{
    return new JsonResult(200,"CustomerBlockHandler全局处理方法customerHandleException");
}
~~~

3.在sentinel配置中心对以customerBlockHandler为资源名进行限流配置及测试

### 服务熔断

前提准备创建两个提供者微服务9003/9004和一个消费者微服务84

- 提供者微服务9003(9004与此一样，仅仅修改个端口号)

1.创建子模块9003

2.改pom文件

~~~xml
<!--SpringCloud ailibaba nacos -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
~~~

3.编写yml配置文件

~~~yml
 server:
  port: 9003

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
~~~

4.主启动类

5.业务类

~~~java
@RestController
public class PaymentController {
    @Value("${server.port}")
    private String serverPort;

    public static HashMap<Long, Payment> hashMap = new HashMap<>();
    static
    {
        hashMap.put(1L,new Payment(1,"28a8c1e3bc2742d8848569891fb42181"));
        hashMap.put(2L,new Payment(2,"bba8c1e3bc2742d8848569891ac32182"));
        hashMap.put(3L,new Payment(3,"6ua8c1e3bc2742d8848569891xt92183"));
    }

    @GetMapping(value = "/paymentSQL/{id}")
    public JsonResult<Payment> paymentSQL(@PathVariable("id") Long id)
    {
        Payment payment = hashMap.get(id);
        JsonResult<Payment> result = new JsonResult(200,"from mysql,serverPort:  "+serverPort,payment);
        return result;
    }
}
~~~

- 消费者微服务84

1.创建子模块9003

2.改pom文件

~~~xml
<!--SpringCloud ailibaba nacos -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<!--SpringCloud ailibaba sentinel -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
~~~

3.编写yml配置文件

~~~yml
server:
  port: 84

spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    sentinel:
      transport:
        #配置Sentinel dashboard地址
        dashboard: localhost:8080
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
        port: 8719


#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider
~~~

4.主启动类

5.业务类

~~~java
//创建一个配置类注册RestTemplate对象并开启其负载均衡能力
@Configuration
public class ApplicationContextConfig {
    @Bean
    @LoadBalanced //开启RestTemplate对象的负载均衡能力
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}

//控制层
@RestController
public class CircleBreakerController {
    @Resource
    private RestTemplate restTemplate;

    public static final String SERVER_URL = "http://nacos-payment-provider";

    @GetMapping("/test/{id}")
    public JsonResult<Payment> getTest(@PathVariable("id") Integer id){
        return restTemplate.getForObject(SERVER_URL + "/paymentSQL/{id}",JsonResult.class);
    }
}
~~~

#### ribbon系列

- 修改消费者微服务84

1.修改84控制层

这里体现的是服务降级，@SentinelResource配置的多种情况

①没有任何配置的情况，业务异常直接返回默认error页面

②配置blockHandler的情况,违背sentinel控制台配置则是blockHandler属性指定的方法进行处理

③既配置fallback又配置blockHandler的情况，在限流规则内出现业务异常则是fallback指定的

方法进行处理，在限流规则之外则是blockHandler指定的方法进行处理

~~~java
@RestController
@RequestMapping("/consume")
public class CircleBreakerController {
    @Resource
    private RestTemplate restTemplate;

    public static final String SERVER_URL = "http://nacos-payment-provider";

    @GetMapping("/fallback/{id}")
    //没有任何配置的情况
//    @SentinelResource("fallback")
    //配置fallback的情况,只负责处理业务异常
//    @SentinelResource(value = "fallback",fallback = "defaultFallbackMethod")
    //配置blockHandler的情况,只负责处理sentinel控制台配置违规
//    @SentinelResource(value = "fallback",blockHandler = "defaultBlockHandlerMethod")
    //既配置fallback又配置blockHandler的情况，fallback负责处理业务异常，blockHandler 负责处理sentinel控制台配置违规
    //exceptionsToIgnore属性表示假如报该异常，fallback降级方法不处理，没有降级效果，返回默认error页面
    @SentinelResource(value = "fallback",fallback = "defaultFallbackMethod",blockHandler = "defaultBlockHandlerMethod",
                     exceptionsToIgnore = {IllegalArgumentException.class})
    public JsonResult<Payment> getTest(@PathVariable("id") Integer id){
        if (id == 4){
            throw new IllegalArgumentException("非法参数");
        }else if (id == 5){
            throw new NullPointerException("无该id记录，空指针异常");
        }
        return restTemplate.getForObject(SERVER_URL + "/paymentSQL/" + id,JsonResult.class);
    }

    //定义fallback方法
    public JsonResult defaultFallbackMethod(@PathVariable("id")Integer id,Throwable e){
        Payment payment = new Payment(id,"null");
        return new JsonResult<>(444,"查询的id是：" + id + ",异常信息:" + e.getMessage(),payment);
    }

    //定义defaultBlockHandlerMethod方法
    public JsonResult defaultBlockHandlerMethod(@PathVariable("id")Integer id, BlockException b){
        Payment payment = new Payment(id,"null");
        return new JsonResult<>(445,"查询的id是：" + id + ",sentinel限流:" + b.getMessage() ,payment);
    }
}
~~~

#### openFeign系列

- 修改修改消费者微服务84

1.修改消费者微服务84pom文件，引入下列依赖

~~~xml
<!--SpringCloud openfeign -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
~~~

2.修改yml配置文件，激活Sentinel对Feign的支持

~~~yml
# 激活Sentinel对Feign的支持
feign:
  sentinel:
    enabled: true  
~~~

3.主启动类添加@EnableFeignClients

4.修改业务类

①创建接口，指定调用的服务名以及指定降级处理类

②创建一个用于处理降级的回退类实现上述接口

③在控制层添加方法测试

~~~java
@Component
@FeignClient(value = "nacos-payment-provider",fallback = FallbackClass.class)
public interface PaymentService {
    @GetMapping(value = "/paymentSQL/{id}")
    public JsonResult<Payment> getPayment(@PathVariable("id") Integer id);
}

@Component
public class FallbackClass implements PaymentService {
    @Override
    public JsonResult<Payment> getPayment(Integer id) {
        Payment payment = new Payment(id, "null");
        return new JsonResult<>(4500,"服务降级返回，查无此用户，id是：" + id,payment);
    }
}

@Resource
private PaymentService paymentService;

@GetMapping("/openFeign/{id}")
public JsonResult getPaymentByOpenFeign(@PathVariable("id") Integer id){
    return paymentService.getPayment(id);
}
~~~

- 熔断框架比较

![](https://s1.ax1x.com/2022/08/17/vDucRI.png)

### 持久化规则

- 为什么需要持久化？

一旦重启应用，sentinel规则将消失，生产环境需要将配置规则进行持久化

- 怎么做？

修改微服务8401端口

1.pom文件添加依赖

~~~xml
<!--SpringCloud ailibaba sentinel-datasource-nacos用于sentinel规则持久化 -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
~~~

2.修改yml配置文件，添加Nacos数据源配置

~~~yml
 spring:
  cloud:
    sentinel:
      datasource:
        ds1:
          nacos:
            server-addr: localhost:8848 #nacos服务中心地址
            dataId: ${spring.application.name} #微服务的名称
            groupId: DEFAULT_GROUP #默认分组
            data-type: json #数据类型
            rule-type: flow #流控规则
~~~

3.添加Nacos业务规则配置

![](https://s1.ax1x.com/2022/08/17/vDuRQP.png)

resource：资源名称；

limitApp：来源应用；

grade：阈值类型，0表示线程数，1表示QPS；

count：单机阈值；

strategy：流控模式，0表示直接，1表示关联，2表示链路；

controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待；

clusterMode：是否集群。

---

## Seata

<font color='red'>**Seata用于处理分布式事务**</font>

- 什么是分布式事务?

分布式事务就是指事务的参与者、支持事务的服务器、资源服务器以及事务管理器分别位于不同的分布

式系统的不同节点之上。简单的说，就是一次大的操作由不同的小操作组成，这些小的操作分布在不同的

服务器上，且属于不同的应用，分布式事务需要保证这些小操作要么全部成功，要么全部失败。

**本质上来说，分布式事务就是为了保证不同数据库的数据一致性。**

- 为什么会产生分布式事务问题?

单体应用被拆分成微服务应用，原来的三个模块被拆分成三个独立的应用，分别使用三个独立的数据源，业务操作

需要调用三个服务来完成。此时每个服务内部的数据一致性由本地事务来保证，但是全局的数据一致性问题没法保证。

<font color='red'>**换句话来说：一次业务操作需要跨多个数据源或需要跨多个系统进行远程调用，就会产生分布式事务问题**</font>

![](https://s1.ax1x.com/2022/08/17/vDufL8.png)

### Seata术语

- 分布式事务处理过程的一ID+三组件模型

  - Transaction ID XID：全局唯一的事务ID

  - 3组件概念：

    - Transaction Coordinator (TC) 事务协调者

    维护全局和分支事务的状态，驱动全局事务以及决定全局事务提交或回滚。

    - Transaction Manager (TM) 事务管理器

    定义全局事务的范围：发起开始全局事务、提交或回滚全局事务。

    - Resource Manager (RM) 资源管理器

    管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

- 处理过程

1、TM 向 TC 申请开启一个全局事务，全局事务创建成功并生成一个全局唯一的 XID；

2、XID 在微服务调用链路的上下文中传播；

3、RM 向 TC 注册分支事务，将其纳入 XID 对应全局事务的管辖；

4、TM 向 TC 发起针对 XID 的全局提交或回滚决议；

5、TC 调度 XID 下管辖的全部分支事务完成提交或回滚请求。

![](https://s1.ax1x.com/2022/08/17/vDuIoQ.png)

### 安装Seata

- 这里启动的Seata的服务端

1、 [seata的github下载地址](https://github.com/seata/seata/releases)

2、这次安装了1.3.0版本 

3、参考[seata部署启动文档](https://seata.io/zh-cn/docs/ops/deploy-guide-beginner.html)即可

①由于1.3.0版本中配置文件变化较大，为了配置自定义事务组名称

需要在file.conf文件下添加service模块

<font color='red'>**vgroupMapping.my_test_tx_group = "fsp_tx_group"  即为设置自定义事务组名称**</font>

~~~tex
service {
  #vgroup->rgroup
  vgroupMapping.my_test_tx_group = "fsp_tx_group" 
  #only support single node
  default.grouplist = "127.0.0.1:8091"
  #degrade current not support
  enableDegrade = false
  #disable
  disable = false
  #unit ms,s,m,h,d represents milliseconds, seconds, minutes, hours, days, default permanent
  max.commit.retry.timeout = "-1"
  max.rollback.retry.timeout = "-1"
}
~~~

②修改file.conf文件下store模块db下的配置

③修改registry.conf下的注册中心和配置中心类型为nacos

目的是：指明注册中心为nacos，及修改nacos连接信息

### 业务数据库准备

- 创建订单/库存/账户业务数据库和数据表以及在各个库下创建一个对应的回滚日志表

~~~mysql
CREATE DATABASE IF NOT EXISTS seata_order CHARSET 'utf8';
CREATE DATABASE IF NOT EXISTS seata_storage CHARSET 'utf8';
CREATE DATABASE IF NOT EXISTS seata_account CHARSET 'utf8';

CREATE TABLE t_order (
  `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
  `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
  `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
  `count` INT(11) DEFAULT NULL COMMENT '数量',
  `money` DECIMAL(11,0) DEFAULT NULL COMMENT '金额',
  `status` INT(1) DEFAULT NULL COMMENT '订单状态：0：创建中；1：已完结' 
) ENGINE=INNODB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;
 
SELECT * FROM t_order;

CREATE TABLE t_storage (
 `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
 `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
 `total` INT(11) DEFAULT NULL COMMENT '总库存',
 `used` INT(11) DEFAULT NULL COMMENT '已用库存',
 `residue` INT(11) DEFAULT NULL COMMENT '剩余库存'
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
 
 
INSERT INTO seata_storage.t_storage(`id`, `product_id`, `total`, `used`, `residue`)
VALUES ('1', '1', '100', '0', '100');
 
SELECT * FROM t_storage;


CREATE TABLE t_account (
  `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT 'id',
  `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
  `total` DECIMAL(10,0) DEFAULT NULL COMMENT '总额度',
  `used` DECIMAL(10,0) DEFAULT NULL COMMENT '已用余额',
  `residue` DECIMAL(10,0) DEFAULT '0' COMMENT '剩余可用额度'
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
 
INSERT INTO seata_account.t_account(`id`, `user_id`, `total`, `used`, `residue`)  VALUES ('1', '1', '1000', '0', '1000');
 
SELECT * FROM t_account;

DROP TABLE `undo_log`;
 
CREATE TABLE `undo_log` (
  `id` BIGINT(20) NOT NULL AUTO_INCREMENT,
  `branch_id` BIGINT(20) NOT NULL,
  `xid` VARCHAR(100) NOT NULL,
  `context` VARCHAR(128) NOT NULL,
  `rollback_info` LONGBLOB NOT NULL,
  `log_status` INT(11) NOT NULL,
  `log_created` DATETIME NOT NULL,
  `log_modified` DATETIME NOT NULL,
  `ext` VARCHAR(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
~~~

### 业务微服务

- <font color='red'>**业务需求：下订单->减库存->扣余额->改(订单)状态**</font>
- 参考创建过程：[官方示例](https://github.com/seata/seata-samples/blob/master/doc/quick-integration-with-spring-cloud.md)

#### 订单Order-Module

1.创建订单Order-Module

2.修改pom文件

~~~xml
<dependencies>
    <!--nacos-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <!--seata-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
        <exclusions>
            <exclusion>
                <artifactId>seata-all</artifactId>
                <groupId>io.seata</groupId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>io.seata</groupId>
        <artifactId>seata-all</artifactId>
        <version>1.3.0</version>
    </dependency>
    <!--feign-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    <!--web-actuator-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--mysql-druid-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.26</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.10</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
~~~

3.编写yml配置文件

~~~yml
server:
  port: 2001

spring:
  application:
    name: seata-order-service
  cloud:
    alibaba:
      seata:
        #自定义事务组名称需要与file.conf文件中的service模块下的vgroupMapping.xxx相对应
        tx-service-group: fsp_tx_group
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/seata_order?characterEncoding=utf8&useSSL=false&serverTimezone=UTC
    username: root
    password: root


feign:
  hystrix:
    enabled: false

logging:
  level:
    io:
      seata: info

mybatis:
  mapperLocations: classpath:mybatis/mapper/*.xml
~~~

4.添加一个名为file.conf配置文件，需要修改vgroupMapping.后的内容为上方yml设置的自定义事务组名称

~~~conf
service {
  #vgroup->rgroup
  vgroupMapping.fsp_tx_group = "default"
  #only support single node
  default.grouplist = "127.0.0.1:8091"
  #degrade current not support
  enableDegrade = false
  #disable
  disable = false
  #unit ms,s,m,h,d represents milliseconds, seconds, minutes, hours, days, default permanent
  max.commit.retry.timeout = "-1"
  max.rollback.retry.timeout = "-1"
}

## transaction log store, only used in seata-server
store {
  ## store mode: file、db、redis
  mode = "db"

  ## file store property
  file {
    ## store location dir
    dir = "sessionStore"
    # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
    maxBranchSessionSize = 16384
    # globe session size , if exceeded throws exceptions
    maxGlobalSessionSize = 512
    # file buffer size , if exceeded allocate new buffer
    fileWriteBufferCacheSize = 16384
    # when recover batch read size
    sessionReloadReadSize = 100
    # async, sync
    flushDiskMode = async
  }

  ## database store property
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp)/HikariDataSource(hikari) etc.
    datasource = "dbcp"
    ## mysql/oracle/postgresql/h2/oceanbase etc.
    dbType = "mysql"
    driverClassName = "com.mysql.cj.jdbc.Driver"
    url = "jdbc:mysql://localhost:3306/seata?characterEncoding=utf8&useSSL=false&serverTimezone=UTC"
    user = "root"
    password = "root"
    minConn = 5
    maxConn = 30
    globalTable = "global_table"
    branchTable = "branch_table"
    lockTable = "lock_table"
    queryLimit = 100
    maxWait = 5000
  }

  ## redis store property
  redis {
    host = "127.0.0.1"
    port = "6379"
    password = ""
    database = "0"
    minConn = 1
    maxConn = 10
    queryLimit = 100
  }
}
~~~

5.将seata-server-1.3.0的conf文件夹下的register.conf拷贝一份到工程的resourece文件夹下

6.创建数据库对应实体类和JsonResult类

~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class JsonResult<E> {
    private int code;
    private String message;
    private E data;
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Order
{
    private Integer id;

    private Integer userId;

    private Integer productId;

    private Integer count;

    private BigDecimal money;

    /**
     * 订单状态：0：创建中；1：已完结
     */
    private Integer status;
}
~~~

7.创建dao层及编写映射文件

~~~java
@Mapper
public interface OrderMapper {
    //新建订单
    public int createOrder(Order order);

    //修改订单状态
    public int updateOrderStatus(@Param("userId") Integer userId);
}
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="top.year21.srpingcloud.alibaba.mapper.OrderMapper">
    <resultMap id="orderQuery" type="order">
        <id column="id" property="id"/>
        <result column="user_id" property="userId"/>
        <result column="product_id" property="productId"/>
    </resultMap>
<!--    public int createOrder(Order order);-->
    <insert id="createOrder" useGeneratedKeys="true" keyProperty="id">
        insert into t_order(user_id,product_id,`count`,money,status)
                    values(#{userId},#{productId},#{count},#{money},0)
    </insert>

<!--     public int updateOrderStatus(@Param("userId") Integer userId);-->
    <update id="updateOrderStatus">
        update t_order set status = 1 where id = #{id} and status = 0
    </update>
</mapper>
~~~

8.Service接口及其实现

①创建OrderService及其实现类

~~~java
public interface OrderService {
    void createOrder(Order order);
}

@Service
@Slf4j
public class OrderServiceImpl implements OrderService {
    @Autowired
    private OrderMapper orderMapper;
    @Autowired
    private StorageService storageService;
    @Autowired
    private AccountService accountService;

    @Override
    public void createOrder(Order order) {
        //先创建订单
        log.info("-------------正在创建订单-------------");
        orderMapper.createOrder(order);

        log.info("-------------创建订单成功，订单微服务调用库存微服务，减少库存-------------");
        //订单创建成功后，减少库存
        storageService.decreaseStorage(order.getProductId(),order.getCount());

        log.info("-------------库存减少成功，订单微服务调用账户微服务，减少账户金额-------------");
        //库存减少之后，扣减用户金额
        accountService.decreaseAccount(order.getUserId(),order.getMoney());

        log.info("-------------账户金额减少成功，修改订单状态-------------");
        //扣除金额成功后，修改订单状态
        orderMapper.updateOrderStatus(order.getId());

        log.info("-------------订单状态修改成功，业务结束-------------");

    }
}
~~~

②创建StorageService、AccountService接口并使用@FeignClients远程调用指定的微服务

~~~java
@Component
@FeignClient("seata-storage-service") //通过@FeignClient远程调用seata-storage-service微服务的rest接口请求
public interface StorageService {
    //减少库存
    @PostMapping("/storage/decreaseStorage")
    public JsonResult decreaseStorage(@RequestParam("productId") Integer productId,
                                              @RequestParam("count") Integer count);
}

@Component
@FeignClient("seata-account-service") //通过@FeignClient远程调用seata-account-service微服务的rest接口请求
public interface AccountService {
    //减少账户金额
    @PostMapping("/account/decreaseAccount")
    public JsonResult decreaseAccount(@RequestParam("userId") Integer userId,
                                              @RequestParam("money") BigDecimal money);
}
~~~

9.控制层

~~~java
@RestController
public class OrderController {
    @Autowired
    private OrderService orderService;

    @PostMapping("/seata/consume")
    public JsonResult createOrder(Order order){
        orderService.createOrder(order);
        return new JsonResult(200,"订单创建成功",null);
    }

}
~~~

10.添加两个配置类

Seata 通过代理数据源的方式实现分支事务，因此需要注入数据源

MyBatisConfig 和 DataSourceProxyConfig(手动创建数据源)

~~~java
@Configuration
@MapperScan("top.year21.srpingcloud.alibaba.mapper")
public class MyBatisConfig {
}

@Configuration
public class DataSourceProxyConfig {

    @Value("${mybatis.mapperLocations}")
    private String mapperLocations;


    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }

    @Bean
    public DataSourceProxy dataSourceProxy(DataSource dataSource) {
        return new DataSourceProxy(dataSource);
    }

    @Bean
    public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSourceProxy);
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
        sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
        return sqlSessionFactoryBean.getObject();
    }
}
~~~

11.主启动类

~~~java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class) //取消数据源的自动创建
@EnableDiscoveryClient
@EnableFeignClients
public class SeataOrderService2001 {
    public static void main(String[] args){
        SpringApplication.run(SeataOrderService2001.class,args);
    }
}
~~~

#### 库存storage-Module

1.创建订单storage-Module

2.修改pom文件

- 与订单模块一致，可以直接cv

3.编写yml配置文件

- 与订单模块一致，可以直接cv，但需要修改端口号和微服务名称

4.添加一个名为file.conf配置文件，需要修改vgroupMapping.后的内容为上方yml设置的自定义事务组名称

- 与订单模块一致，可以直接cv

5.将seata-server-1.3.0的conf文件夹下的register.conf拷贝一份到工程的resourece文件夹下

6.创建数据库对应实体类和JsonResult类

~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class JsonResult<E> {
    private int code;
    private String message;
    private E data;
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Storage {
    private Integer id;
    private Integer productId;
    private Integer count;
    private Integer used;
    private Integer residue;
}
~~~

7.创建dao层及编写映射文件

~~~java
@Mapper
public interface StorageMapper {
    int decreaseStoage(@Param("count") Integer count,@Param("productId")Integer productId);
}
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="top.year21.srpingcloud.alibaba.mapper.StorageMapper">
<!--    int decreaseStoage(@Param("count") Integer count,@Param("productId")Integer productId);-->
    <update id="decreaseStoage">
        update t_storage set residue = residue - #{count},
                             used = used + #{count}
                        where product_id = #{productId}
    </update>
</mapper>
~~~

8.Service接口及其实现

①创建OrderService及其实现类

~~~java
public interface StorageService {
    //减少库存
    public int decreaseStorage(@RequestParam("productId") Integer productId,
                                              @RequestParam("count") Integer count);
}

@Service
public class StorageServiceImpl implements StorageService {
    @Autowired
    private StorageMapper storageMapper;

    @Override
    public int decreaseStorage(Integer productId, Integer count) {
        return storageMapper.decreaseStoage(count,productId);
    }
}
~~~

9.控制层

~~~java
@RestController
public class StorageController {
    @Autowired
    private StorageService storageService;

    @PostMapping("/storage/decreaseStorage")
    public JsonResult decreaseStorage(@RequestParam("productId") Integer productId,
                                              @RequestParam("count") Integer count){
        int result = storageService.decreaseStorage(productId, count);
        return new JsonResult<>(200,"库存减少成功，已减少：" + count,result);
    }
}
~~~

10.添加两个配置类

Seata 通过代理数据源的方式实现分支事务，因此需要注入数据源

MyBatisConfig 和 DataSourceProxyConfig(手动创建数据源)

- 与订单模块一致，可以直接cv

11.主启动类

#### 账户account-Module

1.创建订单account-Module

2.修改pom文件

- 与订单模块一致，可以直接cv

3.编写yml配置文件

- 与订单模块一致，可以直接cv，但需要修改端口号和微服务名称

4.添加一个名为file.conf配置文件，需要修改vgroupMapping.后的内容为上方yml设置的自定义事务组名称

- 与订单模块一致，可以直接cv

5.将seata-server-1.3.0的conf文件夹下的register.conf拷贝一份到工程的resourece文件夹下

6.创建数据库对应实体类和JsonResult类

~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class JsonResult<E> {
    private int code;
    private String message;
    private E data;
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Account {
    private Integer id;
    private Integer userId;
    private BigDecimal total;
    private BigDecimal used;
    private BigDecimal residue;
}
~~~

7.创建dao层及编写映射文件

~~~java
@Mapper
public interface AccountMapper {
    int decreaseAccount(@Param("userId") Integer userId, @Param("money") BigDecimal money);
}
~~~

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="top.year21.srpingcloud.alibaba.mapper.AccountMapper">
<!--    int decreaseAccount(@Param("userId") Integer usedId, @Param("money") BigDecimal money);-->
    <update id="decreaseAccount">
        update t_account set residue = residue - #{money},
                             used = used + #{money}
                        where user_id = #{usedId}
    </update>
</mapper>
~~~

8.Service接口及其实现

①创建OrderService及其实现类

~~~java
public interface AccountService {
    //减少账户金额
    public int decreaseAccount(@RequestParam("userId") Integer userId,
                                              @RequestParam("money") BigDecimal money);
}

@Service
public class AccountServiceImpl implements AccountService {
    @Autowired(required = false)
    private AccountMapper accountMapper;
    @Override
    public int decreaseAccount(Integer userId, BigDecimal money) {
        return accountMapper.decreaseAccount(userId, money);
    }
}
~~~

9.控制层

~~~java
@RestController
public class AccountController {

    @Autowired
    private AccountService accountService;

    @PostMapping("/account/decreaseAccount")
    public JsonResult decreaseAccount(@RequestParam("userId") Integer userId,
                                      @RequestParam("money") BigDecimal money){
        int result = accountService.decreaseAccount(userId, money);
        return new JsonResult(200,"金额扣取成功，是：" + money,result);
    }
}
~~~

10.添加两个配置类

Seata 通过代理数据源的方式实现分支事务，因此需要注入数据源

MyBatisConfig 和 DataSourceProxyConfig(手动创建数据源)

- 与订单模块一致，可以直接cv

11.主启动类

---

### 测试

- 未添加@GlobalTransactional注解

在金额微服务的业务类上添加了sleep20秒后，业务处理超时，发现订单创建成功，库存减少成功，

但是金额并没有减少，这是存在极大危险的

- 在订单业务类中订单创建方法上添加@GlobalTransactional注解即可完成全局的事务管理

~~~java
@Override
/*@GlobalTransactional表示开启全局事务管理，
    name属性值可以随便设置，保证唯一性即可。
    rollbackFor属性为Exception.class表示出现Exception异常及其Exception子类的异常业务都会进回滚
    */
@GlobalTransactional(name = "fsp-create-order",rollbackFor = Exception.class)
public void createOrder(Order order) {}
~~~

在这个测试过程出现了java.time.LocalDateTime反序列化异常的问题，解决方案是降低mysql的版本为8.0.22

--> [参考解决方法](https://blog.csdn.net/Saintmm/article/details/126182178)

### 部分补充

TC可以简单理解为seata server作为事务协调者统筹全局的管理

TM可以简单理解为标注了@GlobalTransactional的作为事务管理器，是事务的发起方

RM可以简单理解为一个个数据库，作为事务的参与方

- 分布式事务的执行流程

①TM 开启分布式事务（TM 向 TC 注册全局事务记录）

②按业务场景，编排数据库、服务等事务内资源（RM 向 TC 汇报资源准备状态 ）

③TM 结束分布式事务，事务一阶段结束（TM 通知 TC 提交/回滚分布式事务）

④TC 汇总事务信息，决定分布式事务是提交还是回滚；

⑤TC 通知所有 RM 提交/回滚 资源，事务二阶段结束。

![](https://s1.ax1x.com/2022/08/17/vDuHWn.png)

- AT模式如何做到对业务的无侵入

![](https://s1.ax1x.com/2022/08/17/vDubzq.png)

在一阶段，Seata 会拦截“业务 SQL”，

1  解析 SQL 语义，找到“业务 SQL”要更新的业务数据，在业务数据被更新前，将其保存成“before image”，

2  执行“业务 SQL”更新业务数据，在业务数据更新之后，

3  其保存成“after image”，最后生成行锁。

以上操作全部在一个数据库事务内完成，这样保证了一阶段操作的原子性。

![](https://s1.ax1x.com/2022/08/17/vDuLQ0.png)

在二阶段顺利提交，因为“业务 SQL”在一阶段已经提交至数据库，所以Seata框架只需将一阶段保存的

快照数据和行锁删掉，完成数据清理即可。

![](https://s1.ax1x.com/2022/08/17/vDuOyV.png)

在二阶段出现异常进行回滚：Seata 就需要回滚一阶段已经执行的“业务 SQL”，还原业务数据。

回滚方式便是用“before image”还原业务数据；但在还原前要首先要校验脏写，对比“数据库当前

业务数据”和 “after image”，如果两份数据完全一致就说明没有脏写，可以还原业务数据，

如果不一致就说明有脏写，出现脏写就需要转人工处理。

![](https://s1.ax1x.com/2022/08/17/vDuXLT.png)

- 整体过程图解

![](https://s1.ax1x.com/2022/08/17/vDuxwF.png)

