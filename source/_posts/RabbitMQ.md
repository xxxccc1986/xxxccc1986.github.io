---
title: RabbitMQ
date: 2022-07-30 21:30:02
updated: 2022-07-30 21:30:02
tags:
  - RabbitMQ
categories:
  - 中间件
keywords: RabbitMQ
cover: https://s1.ax1x.com/2022/07/30/vF9x2j.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/07/30/RabbitMQ
---


# MQ相关信息

1.MQ(message queue)本质是个<font color='red'>**队列**</font>，同样是<font color='red'>**先进先出**</font>原则，但在队列中<font color='red'>**存放的是message**</font>，

​	而且它还是一种跨进程的通信机制，用于上下游传递信息，在互联网架构中，MQ是一种

​	非常常见的上下游"逻辑解耦和物理解耦"的消息通信服务。在使用它后，消息发送的上游

​	只需要依赖MQ，不再需要其他服务

2.使用MQ的优势

①流量消峰

在请求直接访问订单系统直接添加mq，形成消息队列，保护了订单系统

优点在于保护了系统，避免宕机出现，缺点在于请求处理效率降低，因为队列的原因

![工作示意图](https://s1.ax1x.com/2022/07/30/vFSXef.png)

②应用解耦

在添加mq之前，订单系统是可以直接调用其他系统，而在这个过程中，任一系统处理出现异常

都会造成下单业务的失败，在添加mq后，订单系统是直接发消息给mq，其他系统的调用是由mq

进行监督的，若其他系统出现异常则不会影响下单业务，因为在它发消息给mq之后，其业务流程

已然完成，剩下的事情全部由mq进行监督处理。

![工作示意图](https://s1.ax1x.com/2022/07/30/vFSjw8.png)

③异步处理

例如下图，线程a需要调用线程b，但线程b的完成需要很长时间，而线程a又必须知道其什么时候

完成，在使用mq之前，线程a只能不断的调用api去轮询线程b，效率较低，而在使用mq后，

线程a则不需要再这么做，它只需要发起调用线程b的请求就可以去做其他事了，而之后

只需要等待线程b完成后发消息给mq，再由mq发消息给线程a。

![](https://s1.ax1x.com/2022/07/30/vFpSYQ.png)

---

# RabbitMQ相关信息

1.RabbitMQ的概念

RabbitMQ 是一个消息中间件：它接受并转发消息。

RabbitMQ 它不处理任何东西而是负责接收和中转，存储和转发消息数据。

2.四大核心概念

①生产者

产生数据发送消息的程序是生产者

②交换机

交换机是 RabbitMQ 非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息

推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是

推送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定

③队列

队列是 RabbitMQ 内部使用的一种数据结构，尽管消息流经 RabbitMQ 和应用程序，但它们只能存

储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者

可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。

④消费者

消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费

者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者

![](https://s1.ax1x.com/2022/07/30/vFpFO0.png)

3.六大模式

![](https://s1.ax1x.com/2022/07/30/vFpAmV.png)

4.工作原理

![](https://s1.ax1x.com/2022/07/30/vFpEwT.png)



**Broker**：接收和分发消息的应用，RabbitMQ Server 就是 Message Broker

**Virtual host**：出于多租户和安全因素设计的，把 AMQP 的基本组件划分到一个虚拟的分组中，

类似于网络中的 namespace 概念。当多个不同的用户使用同一个 RabbitMQ server 

提供的服务时，可以划分出多个 vhost，每个用户在自己的 vhost 创建 exchange／queue 等

**Connection**：publisher／consumer 和 broker 之间的 TCP 连接

**Channel**：如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 

TCP Connection 的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的

逻辑连接，如果应用程序支持多线程，通常每个 thread 创建单独的 channel 进行通讯，

AMQP method 包含了 channel id 帮助客户端和 message broker 识别 channel，

所以 channel 之间是完全隔离的。Channel 作为轻量级的Connection极大减少

了操作系统建立TCP connection的开销

**Exchange**：message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，

分发消息到 queue 中去。常用的类型有：

direct (point-to-point), topic (publish-subscribe) and fanout (multicast)

**Queue**：消息最终被送到这里等待 consumer 取走

**Binding**：exchange 和 queue 之间的虚拟连接，binding 中可以包含 routing key，

Binding 信息被保存到 exchange 中的查询表中，用于 message 的分发依据

---

# 安装RabbitMQ

1.由于是采用虚拟机vm进行模拟，也就省去了下载软件的时间，但是安装mq必须安装以下软件

①rabbitmq安装包   ---> [官方地址](https://www.rabbitmq.com/download.html)

②erlang包   ---> [官方地址](https://www.erlang.org/downloads)  或 参考 --->[安装参考笔记](https://www.cnblogs.com/fengyumeng/p/11133924.html)

但是本次是直接上传文件到vm虚拟机中，因此只需要在文件目录下的dos窗口

建议将文件放置在/opt目录下

2.安装文件(按顺序执行)

rpm -ivh erlang-21.3-1.el7.x86_64.rpm

yum install socat -y

rpm -ivh rabbitmq-server-3.8.8-1.el7.noarch.rpm

3.设置相关项

①设置开机启动RabbitMQ服务  chkconfig rabbitmq-server on

②启动服务 systemctl start rabbitmq-server.service

③查看服务状态  systemctl status rabbitmq-server.service

④停止服务 systemctl stop rabbitmq-server.service

⑤开启web管理插件 rabbitmq-plugins enable rabbitmq_management

- 通过ip+端口(15672)可以访问这个web管理界面，但用于默认的guest没有登录权限

因此必须设置账户

⑥创建新用户

创建账号	rabbitmqctl add_user admin admin

设置用户角色	rabbitmqctl set_user_tags admin administrator

设置用户权限	rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"

这一条是对上面命令解释：set_permissions [-p < vhostpath>] < user> < conf> < write> < read>

表示用户 user_admin 具有/vhost1 这个 virtual host 中所有资源的配置、写、读权限

当前用户和角色	rabbitmqctl list_users

---

# 使用RabbitMQ

- 需要先导入相关依赖到pom文件中

~~~xml
<!--指定 jdk 编译版本-->
<dependencies>
    <!--rabbitmq 依赖客户端-->
    <dependency>
        <groupId>com.rabbitmq</groupId>
        <artifactId>amqp-client</artifactId>
        <version>5.8.0</version>
    </dependency>
    <!--操作文件流的一个依赖-->
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.6</version>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>8</source>
                <target>8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
~~~

## 简单模式

一个生产者对应一个队列，一个队列对应一个消费者

- 编写Producer代码

~~~java
//生产者，用于发送消息
public class Producer {
    //队列名称
    private static final String QUEUE_NAME = "hello";

    public static void main(String[] args) throws IOException, TimeoutException {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //设置工厂连接rabbitmq的ip
        factory.setHost("192.168.231.133");
        //设置连接的用户名和密码
        factory.setUsername("admin");
        factory.setPassword("admin");
        //创建tcp连接
        Connection connection = factory.newConnection();
        //获取tcp连接中的信道
        Channel channel = connection.createChannel();
        /*正常来说应该通过信道连接交换机，再由交换机绑定队列
          但由于本次只想发一个简单的信息，因此决定省略连接交换机，
          且其也自动配置了个默认的交换机，因此本次只需要连接队列即可
       */
        /*生成队列
        queueDeclare()方法形参的意思如下：
        1.string 队列名称
        2.ture/false决定该队列是否持久化，ture则队列将在服务器重新启动时生存，false则相反
        3.ture/false决定该队列是否为独家的，true表示只能当前的connection使用，false则表示所有的都可以
        4.ture/false决定该队列是否自动删除，true表示在最后一个消费者断开连接后自动删除，false表示不删除
        5.第五个形参表示队列的其他属性（构造参数）
         */
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);

        //发消息
        String message = "hello world!";

        /*
        basicPublish()方法形参的意思如下：
        1.消息发布到的交换机名字
        2.路由的key值是什么 本次是队列的名称
        3.表示其他参数的信息
        4.发送的消息的消息体
        */
        channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
        System.out.println("消息发送成功");
    }
}
~~~

- 编写Consumer代码

~~~java
//消费者，用于接收消息
public class Consumer {
    //队列名称
    private static final String QUEUE_NAME = "hello";

    public static void main(String[] args) throws IOException, TimeoutException {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //设置工厂连接rabbitmq的ip
        factory.setHost("192.168.231.133");
        //设置连接的用户名和密码
        factory.setUsername("admin");
        factory.setPassword("admin");
        //创建tcp连接
        Connection connection = factory.newConnection();
        //获取tcp连接中的信道
        Channel channel = connection.createChannel();

        //声明消息发布后的回调函数
        DeliverCallback deliverCallback = (String consumerTag, Delivery message) -> {
            System.out.println("消息发布后的回调成功," + new String(message.getBody()));
        };
        //声明取消消费消息后的回调函数
        CancelCallback cancelCallback = (String consumerTag) -> {
            System.out.println("取消消费消息后的回调成功," + consumerTag);
        };
        //声明消费者关闭连接后的回调函数
        ConsumerShutdownSignalCallback shutdownSignalCallback = (String consumerTag, ShutdownSignalException sig) -> {
            System.out.println("消费者关闭连接后的回调成功," + consumerTag + "，关闭原因：" + sig);
        };

        /*接收消息
        basicConsume()方法形参的意思如下：
        1.消费消息的交换机名字
        2.ture/false表示是否自动应答
        2.消息发布后的回调函数
        3.消费者取消消费消息的回调函数
        4.消费者连接关闭后的的回调函数
         */
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback,shutdownSignalCallback);
    }
}
~~~

## 工作队列模式

工作队列(又称任务队列)的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。

相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的

工作进程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。

直接点就是：<font color='red'>**轮询公平分发消息，通俗地说就是第一条信息发给第一个消费者，另一条信息发给下个消费者**</font>

​						<font color='red'>**多个线程(消费者)之间体现为竞争关系**</font>

![工作原理](https://s1.ax1x.com/2022/07/30/vFpVTU.png)

- 编写Producer代码

~~~java
//生产者，用于发送消息
public class Producer {
    //队列的名称
    private static final String QUEUE_NAME = "hello";

   	public static void main(String[] args) throws IOException, TimeoutException {
        //获取通道
        Channel channel = RabbitMQUtils.createMQChannel();
        //生成队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //编写发送的消息,从控制台输入
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            //获取输入内容
            String str = scanner.next();
            //发送消息
            channel.basicPublish("",QUEUE_NAME,null,str.getBytes());
            System.out.println("消息发送成功");
        }
    }
}
~~~

- 编写Consumer代码

~~~java
//消费者，用于接收信息
public class Worker {
    //队列的名称
    private static final String QUEUE_NAME = "hello";

    public static void main(String[] args) throws IOException, TimeoutException {
        //利用工具类获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //声明消息发布后的回调函数
        DeliverCallback deliverCallback = (String consumerTag, Delivery message) -> {
            System.out.println("消息发布后的回调成功," + new String(message.getBody()));
        };
        //声明取消消费消息后的回调函数
        CancelCallback cancelCallback = (String consumerTag) -> {
            System.out.println("取消消费消息后的回调成功," + consumerTag);
        };
        //声明消费者关闭连接后的回调函数
        ConsumerShutdownSignalCallback shutdownSignalCallback = (String consumerTag, ShutdownSignalException sig) -> {
            System.out.println("消费者关闭连接后的回调成功," + consumerTag + "，关闭原因：" + sig);
        };
        System.out.println("当前工作线程(消费者)的名字是：W2");
        //接收消息
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback,shutdownSignalCallback);
    }
}
~~~

## 消息应答机制

一种现象：当一个消费者在处理消息过程中死亡了，那么rabbitmq就有可能把消息删除，造成消息的丢失。

为了防止这种现象出现，rabbitmq引入消息应答机制，即消费者在接收消息并成功处理消息之后，会告诉

rabbitmq消息已经处理，此时rabbitmq才会把消息删除。

1.消息应答机制分为：自动应答	与 	手动应答

①自动应答适用于消费者能够高效处理消息的情况，但这种追求效率存在丢失数据的风险

②手动应答指消费者手动给rabbitmq提示消息处理状态，它可以批量应答并且减少网络拥堵

2.手动应答的的某些方法：

  A	Channel.basicAck(用于肯定确认)  RabbitMQ 已知该消息被处理成功，可以将其丢弃了

  B	Channel.basicNack(用于否定确认) 

  C	Channel.basicReject(用于否定确认)  与 B的应答方法相比少一个Multiple代表批量处理的参数 

B、C两者都表示不处理该消息了直接拒绝，可以将其丢弃了

3.关于Multiple 参数值 true / false的不同情况

true 代表批量应答 channel 上包括当前tag和其之前未应答的消息

false 代表只应答 channel 上包括当前tag所在的未应答的消息

![](https://s1.ax1x.com/2022/07/30/vFpekF.png)

4.消息自动重新入队

如果消费者由于某些原因失去连接(其通道已关闭，连接已关闭或 TCP 连接丢失)，导致消息未发送 ACK 确认，

RabbitMQ 将了解到消息未完全处理，并将对其重新排队。如果此时其他消费者可以处理，

它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息

![](https://s1.ax1x.com/2022/07/30/vFpmY4.png)

- 测试消息应答的生产者

~~~java
//手动应答机制的生产者，用于生产消息
public class MessageRespProducer {
    //队列的名称
    private static final String QUEUE_NAME = "ack_queue";
    public static void main(String[] args) throws IOException, TimeoutException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //创建队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //创建消息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            String str = scanner.next();
            channel.basicPublish("",QUEUE_NAME,null,str.getBytes("UTF-8"));
            System.out.println(str + ",消息发送成功");
        }
    }
}
~~~

- 测试消息应答的消费者

~~~java
//手动应答机制的消费者，用于接收消息
//测试当某个消费者死亡，消息不丢失且自动重新入队
public class MessageRespConsumer1 {
    //队列的名称
    private static final String QUEUE_NAME = "ack_queue";
    public static void main(String[] args) throws IOException, TimeoutException {
        //创建信道
        Channel channel = RabbitMQUtils.createMQChannel();
        System.out.println("当前线程C1处理消息较快");
        //声明消息发布后的回调函数
        DeliverCallback deliverCallback = (String consumerTag, Delivery message) -> {
            try {
                //让线程延迟执行
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("消息发布后的回调成功," + new String(message.getBody()));
            /* 手动应答
            basicAck()方法的形参解释
            1.消息的标记 tag
            2.是否批量应答
             */
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        };
        //声明取消消费消息后的回调函数
        CancelCallback cancelCallback = (String consumerTag) -> {
            System.out.println("取消消费消息后的回调成功," + consumerTag);
        };
        //接收信息
        boolean autoAck = false; //设置手动应答
        channel.basicConsume(QUEUE_NAME,autoAck,deliverCallback,cancelCallback);
    }
}
~~~

## 持久化机制

默认情况下 RabbitMQ 退出或由于某种原因崩溃时，它忽视队列和消息，即造成消息丢失

而为了避免这一现象，<font color='red'>**必须将队列和消息标记为持久化**</font>

①队列持久化

~~~java
//创建队列
boolean durable = true;//标注队列为持久化
channel.queueDeclare(QUEUE_NAME,durable,false,false,null);
~~~

- 但需要注意一旦需要持久化的队列在之前已经设置为不持久化，直接修改持久运行会以下错误

  必须要先在rabbitmq的web管理界面删除已存在的队列

~~~java
inequivalent arg 'durable' for queue 'ack_queue' in vhost '/': received 'true' but current is 'false', class-id=50
~~~

②消息持久化

只需要将发布消息的第三个参数设置为MessageProperties.PERSISTENT_TEXT_PLAIN即可

但这个消息持久化不一定能保证成功

~~~java
//设置生产者发送的消息持久化到磁盘上
channel.basicPublish("",QUEUE_NAME, MessageProperties.PERSISTENT_TEXT_PLAIN,str.getBytes("UTF-8"));
~~~

③不公平分发

rabbitmq默认为轮询分发(公平分发)，那么在有两个消费者，一个消费快，一个消费慢的情况下，

就会出现处理速度快的这个消费者大部分时间处于空闲状态，而处理慢的那个消费者一直在干活

这样的方式极其不合理，因此需要改为不公平分发

- 只需要在接收消息之前channel.basicQos(1)即可实现消费者为不公平分发

<font color='red'>**不公平分发原理**：</font>不设置basicQos的话是一次性平均分发给所有的队列。设置之后限制了一次分发消息的数量，

再设置手动应答机制，因此未处理消息未被ack确认之前mq不会再发消息，最终实现不公平分发。

~~~java
//设置分发方式为不公平分发
int prefetchCount = 1;
channel.basicQos(prefetchCount);
//接收信息
boolean autoAck = false; //设置手动应答
channel.basicConsume(QUEUE_NAME,autoAck,deliverCallback,cancelCallback);
~~~

④预取值

prefetch该值定义通道上允许的未确认消息的最大数量。一旦数量达到配置的数量，

RabbitMQ将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认ack之后，

RabbitMQ将会感知这个情况到并再向此通道发送一条未处理的消息。

~~~java
//设置预取值为2
int prefetchCount = 2;
channel.basicQos(prefetchCount);
~~~

![](https://s1.ax1x.com/2022/07/30/vFplOx.png)

---

## 发布确认

**这里的发布确认是指<font color='red'>在MQ服务器没有宕机的情况下，交换机确认消息，</font>生产者才继续发消息**

且无论消息在交换机内部发送成功与否都会执行对应的回调方法

### 原理

生产者将信道设置成 confirm 模式，一旦信道进入 confirm 模式，**所有在该信道上面发布的**

**消息都将会被指派一个唯一的 ID**(从 1 开始)，一旦消息被投递到所有匹配的队列之后，broker

就会发送一个确认给生产者(包含消息的唯一 ID)，这就使得生产者知道消息已经正确到达目的队

列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，broker 回传

给生产者的确认消息中 delivery-tag 域包含了确认消息的序列号，此外 broker 也可以设置

basic.ack 的 multiple 域，表示到这个序列号之前的所有消息都已经得到了处理。

confirm 模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信

道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调

方法来处理该确认消息，如果 RabbitMQ 因为自身内部错误导致消息丢失，就会发送一条 nack 消

息，生产者应用程序同样可以在回调方法中处理该 nack 消息

![](https://s1.ax1x.com/2022/07/30/vFpYkD.png)

### 策略

发布确认默认是不开启的，如果要开启，需要在创建channel信道后调用方法 confirmSelect，

即在创建队列之前。

~~~java
//获取信道
Channel channel = RabbitMQUtils.createMQChannel();
//开启发布确认模式
channel.confirmSelect();
//创建队列
boolean durable = true;//标注队列为持久化
~~~

- <font color='red'>**发布确认的方式**</font>

  ①单个确认发布，它是一种<font color='red'>**简单的同步确认发布方式**</font>，即只有当发出的信息被确认才会发下一条

  ​	这种方式的缺点就是<font color='red'>**发布速度较慢，吞吐量有限**</font>，因为需要等待上一条消息被确认才会发下一

  ​	条，容易造成后续的消息阻塞

  ②批量确认发布，它也是<font color='red'>**同步确认发布方式**</font>，先发布一批消息然后一起确认，<font color='red'>**有合理的吞吐量**</font>

  ​	但是它的缺点是出现故障时会不知道时哪一个消息出现的，因此把整个批处理保存在内存中，

  ​	也一样会阻塞后续消息的发布。

  ③异步确认发布，它是一种<font color='red'>**异步确认发布方式**</font>，消息发布者无需等待消息确认，有<font color='red'>**最佳性能**</font>和

  <font color='red'>	**资源使用的优势**</font>，在出现错误的情况下可以很好地控制，但是实现起来稍微难些

  ![](https://s1.ax1x.com/2022/07/30/vFptte.png)

相关代码演示

~~~java
// 比较三种模式所花费时间
public class PublishConfirmMode {
    //设置批量发布消息的数量
    private static final int MESSAGE_NUM = 1000;
    //设置队列名称
    private static final String QUEUE_NAME = UUID.randomUUID().toString();

    public static void main(String[] args) throws InterruptedException, TimeoutException, IOException {
        //1.单个发布确认
        PublishMessageSingleConfirm(); //单个发布确认消息耗时705s
        //2.批量发布确认
        PublishMessageBatchConfirm(); //批量发布确认消息耗时80s
        //3.异步批量确认
        PublishMessageAsyncConfirm(); //异步批量确认消息耗时25s
    }

    //单个确认发布
    public static void PublishMessageSingleConfirm() throws IOException, TimeoutException, InterruptedException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //开启发布确认模式
        channel.confirmSelect();
        //声明队列
        channel.queueDeclare(QUEUE_NAME,true,false,false,null);
        //消息发布开始时间
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < MESSAGE_NUM ; i++) {
            String message = "消息" + i;
            channel.basicPublish("",QUEUE_NAME, null,message.getBytes());
            //收到消息后进行批量发布确认
            boolean messageResult = channel.waitForConfirms();
            if (messageResult){
                System.out.println("消息" + i + "发送成功");
            }
        }
        //消息发布结束时间
        long endTime = System.currentTimeMillis();
        //消息发布前后耗时
        System.out.println("消息发布前后耗时:" + (endTime - startTime));
    }

    //批量确认发布
    public static void PublishMessageBatchConfirm() throws IOException, TimeoutException, InterruptedException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //开启发布确认模式
        channel.confirmSelect();
        //声明队列
        channel.queueDeclare(QUEUE_NAME,true,false,false,null);
        //消息发布开始时间
        long startTime = System.currentTimeMillis();
        //设置批量确认消息的大小
        int batchConfirmNum = 100;
        //记录当前未确认消息的次数
        int num = 0;

        for (int i = 0; i < MESSAGE_NUM ; i++) {
            String message = "消息" + i;
            channel.basicPublish("",QUEUE_NAME, null,message.getBytes());
            num += 1;
            //收到消息后进行批量发布确认
            if (num == batchConfirmNum){
                num = 0;
                //确认消息
                channel.waitForConfirms();
                System.out.println("消息" + i + "发送成功");
            }
        }
        //消息发布结束时间
        long endTime = System.currentTimeMillis();
        //消息发布前后耗时
        System.out.println("消息发布前后耗时:" + (endTime - startTime));
    }

    //异步发布确认
    public static void PublishMessageAsyncConfirm() throws IOException, TimeoutException{
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //开启发布确认模式
        channel.confirmSelect();
        //声明队列
        channel.queueDeclare(QUEUE_NAME,true,false,false,null);
        /* 消息确认成功的回调函数
        形参解释
        1.deliveryTag代表消息的标识tag
        2.表示是否批量确认
         */
        ConfirmCallback ackCallback = (deliveryTag,multiple) -> {
            System.out.println("确认的消息标识：" + deliveryTag);
        };
        //消息确认失败的回调函数
        ConfirmCallback nackCallBack = (deliveryTag,multiple) -> {
            System.out.println("未确认的消息标识：" + deliveryTag);
        };
        //准备消息的监听器
        channel.addConfirmListener(ackCallback,nackCallBack);
        //消息发布开始时间
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < MESSAGE_NUM; i++) {
            String message = "消息" + i;
            channel.basicPublish("",QUEUE_NAME, null,message.getBytes());
        }
        //消息发布结束时间
        long endTime = System.currentTimeMillis();
        //消息发布前后耗时
        System.out.println("消息发布前后耗时:" + (endTime - startTime));
    }
}
~~~

- 异步确认发布存在未确认消息的处理问题

因为消息监听器和消息发布属于两个线程，往下执行到监听器时并没有启动监听器的线程

而是执行到了消息发送的时候才会启动监听器的线程，那么在消息发送完了之后这时监听器的工作

并没有完成，消息发送完了会继续往下执行sout的语句，输入了那句消息发布前后耗时:25，

而此时监听器还在继续工作，执行消息确认的回调函数，打印它自己的sout语句。

那么就无法处理未确认消息。

<font color='red'>**那么，如何处理这种问题呢？**</font>

最好的解决的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列，

比如说用 ConcurrentSkipListMap在 confirm callbacks 与发布线程之间进行消息的传递

①创建一个适用于高并发的情况下的map

~~~java
/*线程安全有序的跳跃表，适用于高并发的情况下，
        1.将序号和消息进行关联
        2.批量删除对应序号的消息
        3.支持高并发(多线程)
         */
ConcurrentSkipListMap<Long,String> concurrentSkipListMap = new ConcurrentSkipListMap<>();
~~~

②将所有发送的消息加入到concurrentSkipListMap中

~~~java
for (int i = 0; i < MESSAGE_NUM; i++) {
    String message = "消息" + i;
    //1.将所有发送的消息加入到concurrentSkipListMap中
    //channel.getNextPublishSeqNo()可以获取释出的讯息的下一个序号，有序递增。
    //至于为什么是getNextPublishSeqNo可以想象channel是个票号打印机，它必须是先获取下一个序号是什么才能在出票时打出这是哪张票
    //在释出讯息之前先获取序号，作为key放到map里面。
    concurrentSkipListMap.put(channel.getNextPublishSeqNo(),message);
    channel.basicPublish("",QUEUE_NAME, null,message.getBytes());
}
~~~

③在消息确认成功的回调函数中删除该并发队列中成功确认的信息，那么剩下的就是未确认的

~~~java
/* 消息确认成功的回调函数
        形参解释
        1.deliveryTag代表消息的标识tag
        2.表示是否批量确认
         */
ConfirmCallback ackCallback = (deliveryTag,multiple) -> {
    //2.在消息成功的回调函数中删除该并发队列中成功发送的信息，那么剩下的就是未确认的
    if (multiple){ //表示消息将批量确认和删除
        //ConcurrentSkipListMap有一个 heapMap方法，可以返回key小于等于param的map子集
        //因此在headMap可以取出符合所有在deliveryTag之前序号的消息形成一个新的map，从而实现批量确认
        ConcurrentNavigableMap<Long, String> concurrentNavigableMap = concurrentSkipListMap.headMap(deliveryTag);
        //这一步是为了清除已成功确认的信息
        concurrentNavigableMap.clear();
    }else{  //表示不批量确认和删除
        concurrentSkipListMap.remove(deliveryTag);
    }
    System.out.println("确认的消息标识：" + deliveryTag);
};
~~~

④在消息确认失败的回调函数中打印信息

~~~java
//消息确认失败的回调函数
ConfirmCallback nackCallBack = (deliveryTag,multiple) -> {
    //3.打印确认失败的消息
    //根据对应的key找到对应的value消息值
    String message = concurrentSkipListMap.get(deliveryTag);
    System.out.println("未确认的消息是" + message + "未确认的消息标识：" + deliveryTag);
};
~~~

---

## Exchange

### 交换机相关概念

- RabbitMQ 消息传递模型的核心思想是: <font color='red'>**生产者生产的消息从不会直接发送到队列**。</font>

  <font color='red'>**即使是在简单模式和工作队列模式中也只是传递了一个默认交换上，但没有写出来**</font>

交换机的工作只负责接收来自生产者的消息和将它们推入队列，**生产者只能将消息发送到交换机**

至于交换机如何处理消息则需要看交换机是什么类型来决定

1.交换机的类型

直接(direct)(路由类型), 主题(topic)(主题类型) ,标题(headers) , 扇出(fanout)(发布/订阅类型)

2.无名 exchange

无名交换机，即默认交换机通过空字符串(“”)进行标识。

消息能路由发送到队列中其实是由 routingKey(bindingkey)绑定 key 指定的

3.临时队列

临时队列可以看作没有进行持久化的队列。

创建临时队列的方式如下:	String queueName = channel.queueDeclare().getQueue();

4.绑定(bindings)

binding 是 exchange 和 queue 之间的桥梁，表示exchange 和那个队列进行了绑定关系。

### Fanout(发布/订阅模式)

它是将接收到的所有消息广播到它知道的所有队列中。

<font color='red'>**在这个模式中无论bingding的routingKey值是否相同，只需要绑定相同的交换机就能收到消息**</font>

![](https://s1.ax1x.com/2022/07/30/vFpNfH.png)

- 生产者代码

~~~java
//生产者，用于发送消息给交换机
public class EmitLog {
    //设置交换机的名字
    private static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME,"fanout");

        //发送消息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            String str = scanner.next();
            channel.basicPublish(EXCHANGE_NAME,"",null,str.getBytes());
            System.out.println("消息发送成功");
        }
    }
}
~~~

- 消费者代码

~~~java
//消费者1号
public class ReceiveLogs01 {
    //设置交换机的名字
    private static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //创建交换机
        channel.exchangeDeclare(EXCHANGE_NAME,"fanout");
        //创建一个临时队列
        String queue = channel.queueDeclare().getQueue();
        //绑定交换机与队列 事实上这种类型的交换机可以不需要routingKey，因为是广播的
        channel.queueBind(queue,EXCHANGE_NAME,"");
        System.out.println("等待接收消息");

        //声明消息发布后的回调函数
        DeliverCallback deliverCallback = (String consumerTag, Delivery message) -> {
            System.out.println("ReceiveLogs01消息发布后的回调成功," + new String(message.getBody()));
        };
        //声明取消消费消息后的回调函数
        CancelCallback cancelCallback = (String consumerTag) -> {
            System.out.println("取消消费消息后的回调成功," + consumerTag);
        };

        //接收消息
        channel.basicConsume(queue,true,deliverCallback,cancelCallback);
    }
}
~~~

### direct(路由模式)

消息只去到它绑定的routingKey 队列中去。

![](https://s1.ax1x.com/2022/07/30/vFpd1A.png)

- 生产者代码

~~~java
public class EmitLogDirect {
    //设置交换机的名字
    private static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);

        //发送消息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            String str = scanner.next();
            channel.basicPublish(EXCHANGE_NAME,"info",null,str.getBytes());
            System.out.println("消息发送成功");
        }
    }
}
~~~

- 消费者代码

消费者可以多重绑定，只需要rounting指不一样就行了

~~~java
public class ReceiveLogsDirect01 {
    //设置交换机的名字
    private static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //创建交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
        //创建队列
        channel.queueDeclare("consoles",false,false,false,null);
        //绑定交换机与队列 事实上这种类型的交换机可以不需要routingKey，因为是广播的
        channel.queueBind("consoles",EXCHANGE_NAME,"info");
        channel.queueBind("consoles",EXCHANGE_NAME,"warning");

        System.out.println("等待接收消息");

        //声明消息发布后的回调函数
        DeliverCallback deliverCallback = (String consumerTag, Delivery message) -> {
            System.out.println("ReceiveLogsDirect01消息发布后的回调成功," + new String(message.getBody()));
        };
        //声明取消消费消息后的回调函数
        CancelCallback cancelCallback = (String consumerTag) -> {
            System.out.println("ReceiveLogsDirect01取消消费消息后的回调成功," + consumerTag);
        };
        //接收消息
        channel.basicConsume("consoles",true,deliverCallback,cancelCallback);
    }
}
~~~

### Topic(主题模式)

- <font color='red'>**Topic类似于动态的direct交换机**</font>

发送到类型是 topic 交换机的消息的 routing_key 不能随意写，必须满足一定的要求，

它必须是一个单词列表，以点号分隔开。这些单词可以是任意单词，比如说："stock.usd.nyse", 

<font color='red'>**在它这个规则列表中，*(星号)表示可以代替一个单词，#(井号)表示可以替代零个或多个单词**</font>

- 当队列绑定关系是下列这种情况时需要引起注意

①当一个队列绑定键是#,那么这个队列将接收所有数据，就有点像fanout了

②如果队列绑定键当中没有#和出现，那么该队列绑定类型就是direct了

![](https://s1.ax1x.com/2022/07/30/vFpDnP.png)

- 生产者代码

~~~java
public class EmitLogTopic {
    //定义交换机名字
    private static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);

        //发送消息
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            String str = scanner.next();
            channel.basicPublish(EXCHANGE_NAME,"test.orange.rabbit",null,str.getBytes());
            System.out.println("消息发送成功");
        }
    }
}
~~~

- 消费者代码

~~~java
public class ReceiveLogsTopic01 {
    //定义交换机名字
    private static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        //创建信道
        Channel channel = RabbitMQUtils.createMQChannel();

        //创建交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);

        //创建队列
        channel.queueDeclare("Q1",false,false,false,null);

        //绑定交换机
        channel.queueBind("Q1",EXCHANGE_NAME,"*.orange.*");

        System.out.println("Q1等待接收消息");

        //声明消息发布后的回调函数
        DeliverCallback deliverCallback = (String consumerTag, Delivery message) -> {
            System.out.println("ReceiveLogsTopic01消息发布后的回调成功," + new String(message.getBody()));
        };
        //声明取消消费消息后的回调函数
        CancelCallback cancelCallback = (String consumerTag) -> {
            System.out.println("ReceiveLogsTopic01取消消费消息后的回调成功," + consumerTag);
        };

        //接收消息
        channel.basicConsume("Q1",true,deliverCallback,cancelCallback);
    }
}
~~~

---

## 死信队列

1.死信，即无法被消费的消息，producer 将消息投递到 broker 或者直接到 queue 里了，

consumer 从 queue 取出消息进行消费，但由于某些原因导致queu中的某些消息无法被消费，

也没有相应的处理，最后成了死信。

2.死信的来源

①消息 TTL 过期

②队列达到最大长度(队列满了，无法再添加数据到 mq 中)

③消息被拒绝(basic.reject 或 basic.nack)并且 requeue=false.

![](https://s1.ax1x.com/2022/07/30/vFpr0f.png)

<font color='red'> **此处演示的是TTL过期造成死信**</font>

~~~java
public class Producer {
    //定义普通交换机名字
    private static final String NORMAL_EXCHANGE = "normal_exchange";
    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.createMQChannel();
        //死信消息 设置TTL时间
        AMQP.BasicProperties properties = new AMQP.BasicProperties().builder().expiration("10000").build();
        for (int i = 1; i < 11; i++) {
            String message = "message" + i;
            channel.basicPublish(NORMAL_EXCHANGE,"zhangsan",properties,message.getBytes());
        }
    }
}

public class Consumer01 {
    //定义普通交换机和死信交换机名字
    private static final String NORMAL_EXCHANGE = "normal_exchange";
    private static final String DEAD_EXCHANGE = "dead_exchange";
    //定义普通队列和死信队列名字
    private static final String NORMAL_QUEUE = "normal_queue";
    private static final String DEAD_QUEUE = "dead_queue";
    public static void main(String[] args) throws IOException, TimeoutException {
        //获取信道
        Channel channel = RabbitMQUtils.createMQChannel();
        //声明交换机
        channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
        channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
        HashMap<String, Object> arguments = new HashMap<>();
        //设置过期时间
//        arguments.put("x-message-ttl",10000);
        //正常队列设置死信交换机
        arguments.put("x-dead-letter-exchange",DEAD_EXCHANGE);
        //设置死信routingKey
        arguments.put("x-dead-letter-routing-key","lisi");
        //声明普通队列
        channel.queueDeclare(NORMAL_QUEUE,false,false,false,arguments);
        //声明死信队列
        channel.queueDeclare(DEAD_QUEUE,false,false,false,null);
        //将队列和交换机进行绑定
        channel.queueBind(NORMAL_QUEUE,NORMAL_EXCHANGE,"zhangsan");
        channel.queueBind(DEAD_QUEUE,DEAD_EXCHANGE,"lisi");
        System.out.println("等待接收消息");
        //声明消息发布后的回调函数
        DeliverCallback deliverCallback = (String consumerTag, Delivery message) -> {
            System.out.println("Consumer01消息发布后的回调成功," + new String(message.getBody()));
        };
        //声明取消消费消息后的回调函数
        CancelCallback cancelCallback = (String consumerTag) -> {
            System.out.println("Consumer01取消消费消息后的回调成功," + consumerTag);
        };
        //接收消息
        channel.basicConsume(NORMAL_QUEUE,true,deliverCallback,cancelCallback);
    }
}
~~~

<font color='red'>**演示队列达到最大长度而造成死信**</font>

~~~java
public class Producer {
    //定义普通交换机名字
    private static final String NORMAL_EXCHANGE = "normal_exchange";
    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.createMQChannel();
        for (int i = 1; i < 11; i++) {
            String message = "message" + i;
            channel.basicPublish(NORMAL_EXCHANGE,"zhangsan",null,message.getBytes());
        }
    }
}

public class Consumer01 {
//需要在声明正常队列的最后那个参数上携带这些设置
HashMap<String, Object> arguments = new HashMap<>();
//正常队列设置死信交换机
arguments.put("x-dead-letter-exchange",DEAD_EXCHANGE);
//设置死信routingKey
arguments.put("x-dead-letter-routing-key","lisi");
//设置正常队列长度的限制
arguments.put("x-max-length",6);
//声明普通队列
channel.queueDeclare(NORMAL_QUEUE,false,false,false,arguments);
}
~~~

<font color='red'>**演示消息被拒绝而造成死信**</font>，需要在收到消息后手动拒绝，因此要开始手动应答

~~~java
public class Producer {
    //定义普通交换机名字
    private static final String NORMAL_EXCHANGE = "normal_exchange";
    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMQUtils.createMQChannel();
        for (int i = 1; i < 11; i++) {
            String message = "message" + i;
            channel.basicPublish(NORMAL_EXCHANGE,"zhangsan",null,message.getBytes());
        }
    }
}

public class Consumer01 {
//声明消息发布后的回调函数
DeliverCallback deliverCallback = (String consumerTag, Delivery message) -> {
    String msg = new String(message.getBody(), "UTF-8");
    if ("message5".equals(msg)){
        System.out.println("此消息被拒绝了," + new String(message.getBody()));
        channel.basicNack(message.getEnvelope().getDeliveryTag(),false,false);
    }else{
        System.out.println("Consumer01消息发布后的回调成功," + new String(message.getBody()));
        channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
    }

};
//声明取消消费消息后的回调函数
CancelCallback cancelCallback = (String consumerTag) -> {
    System.out.println("Consumer01取消消费消息后的回调成功," + consumerTag);
};
//接收消息并开启手动应答
channel.basicConsume(NORMAL_QUEUE,false,deliverCallback,cancelCallback);
}
~~~

---

## 延迟队列

1.概念：延时队列就是用来存放需要在指定时间被处理的元素的队列。

2.使用场景如下：

①订单在十分钟之内未支付则自动取消

②新创建的店铺，如果在十天内都没有上传过商品，则自动发送消息提醒。

③用户注册成功后，如果三天内没有登陆则进行短信提醒。

④用户发起退款，如果三天内没有得到处理则通知相关运营人员。

⑤预定会议后，需要在预定的时间点前十分钟通知各个与会人员参加会议

3.TTL 是 RabbitMQ 中某消息或者队列的属性，表明的是一条消息或队列内消息的最大存活时间，

- <font color='red'>**未优化版延迟队列代码架构图**</font>

![](https://s1.ax1x.com/2022/07/30/vFp6AS.png)

在整合Springboot后上图只需分为三个部分，生产者、队列和交换机、消费者

①队列和交换机的代码使用一个配置类+@Bean进行注册

~~~java
//TTL队列(延迟队列) 配置文件类代码
@Configuration
public class TTLQueueConfig {
    //定义普通交换机名字
    private static final String X_EXCHANGE = "X";
    //定义死信交换机名字
    private static final String Y_DEAD_LETTER_EXCHANGE = "Y";

    //定义普通队列名字
    private static final String QUEUE_A = "QA";
    private static final String QUEUE_B = "QB";

    //定义死信队列名字
    private static final String DEAD_LETTER_QUEUE = "QD";

    //声明普通交换机
    @Bean("xExchange")
    public DirectExchange xExchange(){
        return new DirectExchange(X_EXCHANGE);
    }
    //声明死信交换机
    @Bean("yExchange")
    public DirectExchange yExchange(){
        return new DirectExchange(Y_DEAD_LETTER_EXCHANGE);
    }
    //声明普通队列
    @Bean("queueA")
    public Queue declareQueueA(){
        HashMap<String, Object> arguments = new HashMap<>(3);
        //设置死信交换机
        arguments.put("x-dead-letter-exchange",Y_DEAD_LETTER_EXCHANGE);
        //设置死信RoutingKey
        arguments.put("x-dead-letter-routing-key","YD");
        //设置TTL时间
        arguments.put("x-message-ttl",10000);
        return QueueBuilder.durable(QUEUE_A).withArguments(arguments).build();
    }
    //声明普通队列
    @Bean("queueB")
    public Queue declareQueueB(){
        HashMap<String, Object> arguments = new HashMap<>(3);
        //设置死信交换机
        arguments.put("x-dead-letter-exchange",Y_DEAD_LETTER_EXCHANGE);
        //设置死信RoutingKey
        arguments.put("x-dead-letter-routing-key","YD");
        //设置TTL时间
        arguments.put("x-message-ttl",40000);
        return QueueBuilder.durable(QUEUE_B).withArguments(arguments).build();
    }

    //声明死信队列
    @Bean("queueD")
    public Queue declareQueueD(){
        return QueueBuilder.durable(DEAD_LETTER_QUEUE).build();
    }

    //绑定
    @Bean
    public Binding queueABindingX(Queue queueA,DirectExchange xExchange){
        return BindingBuilder.bind(queueA).to(xExchange).with("XA");
    }
    @Bean
    public Binding queueBBindingY(Queue queueB,DirectExchange xExchange){
        return BindingBuilder.bind(queueB).to(xExchange).with("XB");
    }
    @Bean
    public Binding queueDBindingY(Queue queueD,DirectExchange yExchange){
        return BindingBuilder.bind(queueD).to(yExchange).with("YD");
    }
}
~~~

②生产者代码

~~~java
@Slf4j
@RestController
@RequestMapping("/ttl")
public class MessageController {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @GetMapping("/sendMsg/{message}")
    public void sendMsg(@PathVariable("message") String msg){
        //{}表示占位符，会被后方对应位置的形参所替代
        log.info("当前时间：{}，已发送消息：{}",new Date().toString(),msg);
        //使用rabbitTemplate模板发送消息
        rabbitTemplate.convertAndSend("X","XA","消息来自ttl为10s的队列" + msg);
        rabbitTemplate.convertAndSend("X","XB","消息来自ttl为40s的队列" + msg);
    }
}
~~~

③消费者代码

~~~java
//死信队列的消费者
@Component
@Slf4j
public class DeadQueueConsumer {
    //接收消息
    @RabbitListener(queues = "QD") //这里的queue值是从那个队列中取消息
    public void receiveDeadLetterMsg(Message message, Channel channel) throws Exception{
        String str = new String(message.getBody(),"UTF-8");
        log.info("收到死信队列消息时间：{}，收到的消息是：{}",new Date().toString(),str);
    }
}
~~~

- <font color='red'>**优化了，但未完全优化版延迟队列代码架构图**</font>，增加一个队列QC，不设置队列的TTL

这个版本存在一个<font color='red'>**致命**</font>问题，如果同时发送两个消息，但各自的延迟时间不同，如果第一个

消息的延时很长，而第二个消息的延时时长很短，第二个消息并不会优先得到执行

RabbitMQ只会检查第一个消息是否过期，造成延迟时间短和时间长的在同一时间到达。

这是由于<font color='red'>**队列的先进先出特性**</font>，过期的消息在到达队列头前不会执行任何操作，仍参与

队列的统计等操作。<font color='red'>**只有当过期的消息到了队列的队首时，才会被真正的丢弃或者进入死信队列。**</font>

![](https://s1.ax1x.com/2022/07/30/vFpghQ.png)

①配置类代码。在配置类中新增这些配置

~~~java
//定义普通队列的名称
private static final String QUEUE_C = "QC";
//声明普通队列
@Bean("queueC")
public Queue declareQueueC(){
    HashMap<String, Object> arguments = new HashMap<>(3);
    //设置死信交换机
    arguments.put("x-dead-letter-exchange",Y_DEAD_LETTER_EXCHANGE);
    //设置死信RoutingKey
    arguments.put("x-dead-letter-routing-key","YD");
    return QueueBuilder.durable(QUEUE_C).withArguments(arguments).build();
}
//将队列和交换机进行绑定 这里使用了建造者模式
@Bean
    public Binding queueCBindingX(Queue queueC,DirectExchange xExchange){
        return BindingBuilder.bind(queueC).to(xExchange).with("XC");
    }
~~~

②生产者代码

~~~java
@GetMapping("/sendMsgWithTTL/{message}/{ttl}")
public void sendMsg(@PathVariable("message") String msg,@PathVariable("ttl") String dateTime){
    //{}表示占位符，会被后方对应位置的形参所替代
    log.info("当前时间：{}，发送一条时长{}毫秒TTL信息给队列QC：{}",new Date().toString(),dateTime,msg);
    //使用rabbitTemplate模板发送消息 
    //这里第四个参数是个函数式接口
    rabbitTemplate.convertAndSend("X","XC","消息来自ttl为动态的队列" + msg,message -> {
        //设置消息过期时间
        message.getMessageProperties().setExpiration(dateTime);
        return message;
    });
}
~~~

- <font color='red'>**基于RbbitMQ插件实现通用延迟队列版本(推荐此版本)**</font>

![](https://s1.ax1x.com/2022/07/30/vFpfcn.png)

①配置类代码

~~~java
@Configuration
public class DelayedQueueConfig {
    //定义延迟交换机的名称
    public static final String DELAYED_EXCHANGE = "delayed.exchange";
    //定义延迟队列的名字
    public static final String DELAYED_QUEUE = "delayed.queue";
    //定义交换机和队列绑定的routingKey
    public static final String RoutingKey = "delayed.routingkey";

    //创建延迟交换机
    @Bean("delayedExchange")
    public CustomExchange declareExchange(){
        HashMap<String, Object> arguments = new HashMap<>();
        //设置交换机如何向队列传播消息，是direct还是topic还是fanout
        arguments.put("x-delayed-type", "direct");
        /* CustomExchange()参数解释
        1.交换机名称
        2.交换机类型
        3.是否持久化
        4.是否自动删除
        5.其他参数
         */
        return new CustomExchange(DELAYED_EXCHANGE,"x-delayed-message",true,false,arguments);
    }

    //创建队列
    @Bean("delayedQueue")
    public Queue declareQueue(){
        return QueueBuilder.durable(DELAYED_QUEUE).build();
    }

    //将队列和交换机进行绑定
    @Bean
    public Binding binding(Queue delayedQueue,CustomExchange delayedExchange){
        return BindingBuilder.bind(delayedQueue).to(delayedExchange).with(RoutingKey).noargs();
    }
}
~~~

②生产者代码

~~~java
@GetMapping("/sendDelayedMsgWithTTL/{message}/{delayedTime}")
public void sendDelayedMsg(@PathVariable("message") String msg,@PathVariable("delayedTime") Integer delayedTime){
    //{}表示占位符，会被后方对应位置的形参所替代
    log.info("当前时间：{}，发送一条时长{}毫秒延迟信息给队列QC：{}",new Date().toString(),delayedTime,msg);
    //使用rabbitTemplate模板发送消息
    //这里第四个参数是个函数式接口
    rabbitTemplate.convertAndSend(DelayedQueueConfig.DELAYED_EXCHANGE,DelayedQueueConfig.RoutingKey,msg, message -> {
        //设置消息延迟时间
        message.getMessageProperties().setDelay(delayedTime);
        return message;
    });
}
~~~

③消费者代码

~~~java
//基于插件的延迟队列的消费者
@Slf4j
@Component
public class DelayedQueueConsumer {

    @RabbitListener(queues = DelayedQueueConfig.DELAYED_QUEUE)
    public void receiveDelayedMsg(Message message){
        String msg = new String(message.getBody());
        log.info("当前时间：{}，接收来自延迟队列的消息：{}",new Date().toString(),msg);
    }
}
~~~

---

## 发布确认高级

### 发布确认springboot版本

**这里的发布确认是指<font color='red'>在MQ服务器在已宕机的情况下，生产者应如何做？</font>**

- <font color='red'>**在配置文件当中需要先开启发布确认模式和消息回退机制**</font>

~~~yml
#交换机开启发布确认模式
publisher-confirm-type: correlated
~~~

publisher-confirm-type的不同类型含义如下：

①NONE	禁用发布确认模式，是默认值

②CORRELATED	开启发布确认模式，发布消息成功到交换机后会触发回调方法

③SIMPLE有两种效果

其一效果和 CORRELATED 值一样会触发回调方法，

其二在发布消息成功后使用rabbitTemplate调用 waitForConfirms 或 waitForConfirmsOrDie 方法

等待 broker 节点返回发送结果，根据返回结果来判定下一步的逻辑，要注意的点是

waitForConfirmsOrDie 方法如果返回 false 则会关闭 channel，则接下来无法发送消息到 broker

- 确认机制

每发一条消息就同时备份这个消息到缓存中，等交换机确认消息后又从缓存中清除此消息

![](https://s1.ax1x.com/2022/07/30/vFp5n0.png)

- 发布确认高级代码架构图

![](https://s1.ax1x.com/2022/07/30/vFpIBV.png)

①配置类代码

~~~java
//发布确认高级配置类
@Configuration
public class PublishAdvancedConfirmConfig {
    //定义交换机的名字
    public static final String EXCHANGE_NAME = "confirm.exchange";
    //定义队列的名字
    public static final String QUEUE_NAME = "confirm.queue";
    //定义队列和路由绑定的routingKey
    public static final String routingKey = "key1";
    //创建交换机
    @Bean("confirmExchange")
    public DirectExchange declareExchange(){
        return ExchangeBuilder.directExchange(EXCHANGE_NAME).build();
    }
    //创建队列
    @Bean("confirmQueue")
    public Queue declareQueue(){
        return new Queue(QUEUE_NAME);
    }
    //绑定
    @Bean
    public Binding queueBindingExchange(DirectExchange confirmExchange,Queue confirmQueue){
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with(routingKey);
    }
}
~~~

②生产者代码

~~~java
@Slf4j
@RestController
@RequestMapping("/confirm")
public class ProducerController {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @GetMapping("/sendMsg/{message}")
    public void sendMsg(@PathVariable("message") String msg){
        CorrelationData correlationData = new CorrelationData("1");
        //使用rabbitTemplate模板发送消息;
        rabbitTemplate.convertAndSend(PublishAdvancedConfirmConfig.EXCHANGE_NAME,
                PublishAdvancedConfirmConfig.routingKey,msg,correlationData);
        log.info("发送的消息内容：{}",msg);
        //下面这条消息由于routingKey值不对，队列接收不到，会被直接弃用
        CorrelationData correlationData2 = new CorrelationData("2");
        rabbitTemplate.convertAndSend(PublishAdvancedConfirmConfig.EXCHANGE_NAME,
                PublishAdvancedConfirmConfig.routingKey+1,msg,correlationData2);
        log.info("发送的消息内容：{}",msg);
    }
}
~~~

③回调接口代码

~~~java
//发布确认高级部分的交换机回调函数
@Slf4j
@Component
public class MyCallBack implements RabbitTemplate.ConfirmCallback,RabbitTemplate.ReturnsCallback {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    /*
    spring容器在初始化时会执行此方法，且只执行一次，
    之所以要@Autowired进行一个rabbitTemplate对象，是因为ConfirmCallback回调函数
    是rabbitTemplate内部的一个属性，必须通过这样的方式来让我们自定义的回调函数取代其
    内部设置的，以便将来回调时使用的是我们自定义的回调函数,
    同理，下方重写的ReturnsCallback接口中的returnedMessage方法也需要这么做
     */
    @PostConstruct
    public void init(){
        rabbitTemplate.setConfirmCallback(this);
        rabbitTemplate.setReturnsCallback(this);
    }

    /*交换机的无论是否接受到消息都会进行回调的函数
    confirm()方法形参解释
    1.correlationData 回调的相关数据，这个形参的值是由生产者发送消息时提供，否则为null
	2.ack  true表示交换机接收消息成功, false表示交换机接收消息失败
	3.cause 消息接收失败的原因，成功接收消息此形参值则为null
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "null";
        if (ack){ //表示交换机接收信息成功
            log.info("交换机成功接收id为：{}的消息",id);
        }else{ //表示交换机接收信息失败
            log.info("失败！交换机未能成功接收id为：{}的消息,原因是{}",id,cause);
        }
    }
}
~~~

④消费者代码

~~~java
//发布确认高级的消费者
@Slf4j
@Component
public class ConfirmConsumer {
    @RabbitListener(queues = PublishAdvancedConfirmConfig.QUEUE_NAME)
    public void receiveConfirmMsg(Message message){
        String str = new String(message.getBody());
        log.info("当前时间:{}，接收来着队列的消息是：{}",new Date().toString(),str);
    }
}
~~~

这样就可以保证生产者获得感知消息是否被交换机接收的能力，但存在一个问题：

**在仅开启了生产者确认机制的情况下，交换机接收到消息后，会直接给消息生产者发送确认消息**，**如**

**果发现该消息不可路由，那么消息会被直接丢弃，此时生产者是不知道消息被丢弃这个事件的**。

<font color='red'>**由此引入消息回退解决这一问题。**</font>

### 消息回退

①再在yml配置文件中添加以下配置

~~~yml
#开启发布退回消息机制
publisher-returns: true
~~~

②在配置类中实现RabbitTemplate.ReturnsCallback接口

<font color='red'>**因此必须开启消息回退，即重写RabbitTemplate.ReturnsCallback的抽象方法**</font>

~~~java
//这一段是写在上面配置类的，为了说明抽离出来
public class MyCallBack implements RabbitTemplate.ConfirmCallback,RabbitTemplate.ReturnsCallback {
/* 在当消息传递过程中不可达目的地时将消息返回给生产者。
    ReturnedMessage returned内封装了下面五个需要用到的参数
        1.message 被退回的消息体
		2.replyCode 退回消息状态码
		3.replyText 被退回的原因
		4.exchange 消息被退回的交换机
		5.routingKey 消息被退回的路由routingKey
     */
    @Override
    public void returnedMessage(ReturnedMessage returned) {
        log.info("消息{}，被交换机{}退回。退回的原因是：{}，路由routingKey是：{}",
                returned.getMessage().getBody(),
                returned.getExchange(),
                returned.getReplyText(),
                returned.getRoutingKey());
    }
}
~~~

### 备份交换机

在添加了备份交换机后，无论是正常交换机还是队列环节出现问题，就不需要生产者重新发送消息

可以直接让返回的消息由备份交换机进行备份或者重新发送

<font color='red'>**mandatory 参数(消息回退机制)与备份交换机如果两者同时开启，则备份交换机优先级高，即消息**</font>

<font color='red'>**不会再回退给生产者。**</font>

- 代码架构图

![](https://s1.ax1x.com/2022/07/30/vFp7AU.png)



①配置类代码，需要修改原来的配置类

~~~java
//发布确认高级配置类
@Configuration
public class PublishAdvancedConfirmConfig {
    //定义交换机的名字
    public static final String EXCHANGE_NAME = "confirm.exchange";
    //定义队列的名字
    public static final String QUEUE_NAME = "confirm.queue";
    //定义队列和路由绑定的routingKey
    public static final String routingKey = "key1";
    //定义备份交换机的名字
    public static final String BACKUP_EXCHANGE_NAME = "backup.exchange";
    //定义备份队列和警告队列的名字
    public static final String BACKUP_QUEUE_NAME = "backup.queue";
    public static final String WARN_QUEUE_NAME = "warning.queue";

    //创建普通交换机和绑定备份交换机
    @Bean("confirmExchange")
    public DirectExchange declareExchange(){
        return ExchangeBuilder.directExchange(EXCHANGE_NAME).withArgument("alternate-exchange",BACKUP_EXCHANGE_NAME).build();
    }
    //创建备份交换机
    @Bean("backupExchange")
    public FanoutExchange declareBackupExchange(){
        return ExchangeBuilder.fanoutExchange(BACKUP_EXCHANGE_NAME).build();
    }

    //创建普通队列和其他队列
    @Bean("confirmQueue")
    public Queue declareQueue(){
        return new Queue(QUEUE_NAME);
    }

    @Bean("backupQueue")
    public Queue declareBackupQueue(){
        return QueueBuilder.durable(BACKUP_QUEUE_NAME).build();
    }
    @Bean("warningQueue")
    public Queue declareWarnQueue(){
        return new Queue(WARN_QUEUE_NAME);
    }

    //绑定交换机和队列的关系
    @Bean
    public Binding queueBindingExchange(DirectExchange confirmExchange,Queue confirmQueue){
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with(routingKey);
    }

    @Bean
    public Binding backupQueueBindingExchange(Queue backupQueue,FanoutExchange backupExchange){
        return BindingBuilder.bind(backupQueue).to(backupExchange);
    }
    @Bean
    public Binding warningQueueBindingExchange(Queue warningQueue,FanoutExchange backupExchange){
        return BindingBuilder.bind(warningQueue).to(backupExchange);
    }
}
~~~

②警告队列消费者

~~~java
//警告队列的消费者
@Component
@Slf4j
public class WaringConsumer {

    @RabbitListener(queues = PublishAdvancedConfirmConfig.WARN_QUEUE_NAME)
    public void receiveMsg(Message message){
        String str = new String(message.getBody());
        log.info("收到来着警告队列的消息：{}",str);
    }
}
~~~

---

## 其余知识点

### 幂等性

①概念：用户对于同一操作发起的一次请求或者多次请求的结果是一致的，不会因为多次点击

​				而产生了副作用。最直接的例子就是，用户因网络问题多次支付造成多次扣款

②**消息重复消费**

消费者在消费 MQ 中的消息时，MQ 把消息发送给消费者，消费者在给 MQ 返回 ack 时网络中断

故 MQ 未收到确认信息，该条消息会重新发给其他的消费者，或者网络重连后再次发送给该消费者

但实际上该消费者已成功消费了该条消息，造成消费者消费了重复的消息。

③**解决思路** 

MQ 消费者的幂等性的解决一般使用全局 ID 或者写个唯一标识比如时间戳 或者 UUID 或者

订单消费者消费 MQ 中的消息也可利用 MQ 的该 id 来判断，或者可按自己的规则生成一个

全局唯一 id，每次消费消息时用该 id 先判断该消息是否已消费过。

④**消费端的幂等性保障** 

在海量订单生成的业务高峰期，生产端可能就会重复发生了消息，这时候消费端就要实现幂等性

这就意味着我们的消息永远不会被消费多次，即使我们收到了一样的消息。

业界主流的幂等性有两种操作:

a.唯一 ID+指纹码机制,利用数据库主键去重, b.利用 redis 的原子性去实现

⑤**唯一 ID+指纹码机制** 

指纹码:我们的一些规则或者时间戳加别的服务给到的唯一信息码,它并不一定是我们系统生成的，

基本都是由我们的业务规则拼接而来，但是一定要保证唯一性，然后就利用查询语句进行判断

这个 id 是否存在数据库中,优势就是实现简单就一个拼接，然后查询判断是否重复；劣势就是

在高并发时，如果是单个数据库就会有写入性能瓶颈当然也可以采用分库分表提升性能，

也不是我们最推荐的方式。

⑥**Redis 原子性** 

利用 redis 执行 setnx 命令，天然具有幂等性。从而实现不重复消费

### 优先级队列

场景：**订单催付**，有多个客户需要催收，但是每个客户的优先又不一样。优先级高的

客户必须先处理，由于队列先进先出的特点，不进行优先级排序则无法完成。

![](https://s1.ax1x.com/2022/07/30/vFpHNF.png)

- 代码演示

~~~java
HashMap<String, Object> arguments = new HashMap<>();
//官方允许优先级设置范围是0-255，此处设置为10，设置范围过大会浪费cpu和内存
arguments.put("x-max-priority",10);
channel.queueDeclare(QUEUE_NAME,false,false,false,arguments);
for (int i = 1; i < 11; i++) {
    //发消息
    String message = "hello world!" + i;
    if (i == 5){
        //设置优先级为5
        AMQP.BasicProperties basicProperties =
            new AMQP.BasicProperties().builder().priority(5).build();
        channel.basicPublish("",QUEUE_NAME,basicProperties,message.getBytes());
    }else { //不设置优先级的消息
        channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
    }
}
~~~

### 惰性队列

惰性队列：消息保存在磁盘中	正常队列：消息保存在内存中

①惰性队列使用场景：当消费者由于各种各样的原因(比如消费者下线、宕机亦或者是由于维护而关闭等)

​									而致使长时间内不能消费消息造成堆积时，惰性队列就很有必要了。

②队列具备两种模式：default 和 lazy。默认的为 default 模式，在 3.6.0 之前的版本无需做任何变更。

lazy模式即为惰性队列的模式，可以通过调用 channel.queueDeclare 方法的时候在参数中设置，

或通过Policy 的方式设置，如果一个队列同时使用这两种方式设置的话，Policy 的方式优先级更高

如果要通过声明的方式改变已有队列的模式的话，必须先删除队列，然后再重新声明一个新的。

③内存开销方面惰性队列比正常队列更加节约内存空间，因为其消息都已存储在磁盘上，但也正是这个

原因导致惰性队列消息消费较慢，因为需要先从磁盘上读取消息

---

## RabbitMQ集群

### 集群搭建步骤

~~~tex
1.修改 3 台机器的主机名称
vim /etc/hostname
2.配置各个节点的 hosts 文件，让各个节点都能互相识别对方
vim /etc/hosts
10.211.55.74 node1
10.211.55.75 node2
10.211.55.76 node3
 
3.以确保各个节点的 cookie 文件使用的是同一个值在 node1 上执行远程操作命令
scp /var/lib/rabbitmq/.erlang.cookie root@node2:/var/lib/rabbitmq/.erlang.cookie
scp /var/lib/rabbitmq/.erlang.cookie root@node3:/var/lib/rabbitmq/.erlang.cookie

4.启动 RabbitMQ 服务,顺带启动 Erlang 虚拟机和 RbbitMQ 应用服务(在三台节点上分别执行以下命令)
rabbitmq-server -detached

5.在节点 2 执行
rabbitmqctl stop_app
(rabbitmqctl stop 会将 Erlang 虚拟机关闭，rabbitmqctl stop_app 只关闭 RabbitMQ 服务)
rabbitmqctl reset
rabbitmqctl join_cluster rabbit@node1
rabbitmqctl start_app(只启动应用服务)

6.在节点 3 执行
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl join_cluster rabbit@node2
rabbitmqctl start_app

7.集群状态
rabbitmqctl cluster_status

8.需要重新设置用户
创建账号
rabbitmqctl add_user admin 123
设置用户角色
rabbitmqctl set_user_tags admin administrator
设置用户权限
rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"

9.解除集群节点(node2 和 node3 机器分别执行)
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl start_app
rabbitmqctl cluster_status
rabbitmqctl forget_cluster_node rabbit@node2(node1 机器上执行)
~~~

### 镜像队列

为了解决某个集群中的节点宕机了导致队列内的消息丢失，因此出现了镜像队列

- 搭建步骤

①启动三台集群节点

②随便找一个节点添加 policy

![](https://s1.ax1x.com/2022/07/30/vFpL9J.png)

③经过实验发现就算整个集群只剩下一台机器了 依然能消费队列里面的消息

​	说明队列里面的消息被镜像队列传递到相应机器里面了

<font color='red'>**但是这种方式存在生产者只能给一个队列发消息，不能更改，因为采用的是硬编码方式**</font>

可以采用Haproxy 实现负载均衡 来解决

### 联邦交换机(Federation Exchange)

<font color='red'>**主要是为了解决不同地区的信息访问一致和延迟问题**</font>

- 搭建步骤

~~~tex
1.需要保证每台节点单独运行
2.在每台机器上开启 federation 相关插件
rabbitmq-plugins enable rabbitmq_federation
rabbitmq-plugins enable rabbitmq_federation_management
~~~

- 鉴于后面笔记内容感觉用不上就不再记录了，你需要的时候可以自己去看这个课程的pdf文件即可

---

# Springboot整合RabbitMQ

①使用Spring Initializi快速创建Springboot工程

②引入相关依赖

~~~xml
<!--RabbitMQ 依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.47</version>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
<!--swagger-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
<!--RabbitMQ 测试依赖-->
<dependency>
    <groupId>org.springframework.amqp</groupId>
    <artifactId>spring-rabbit-test</artifactId>
    <scope>test</scope>
</dependency>
~~~

③创建Swagger的配置类

~~~java
package year21.top.springbootrabbitmq.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket webApiConfig(){
        return new Docket(DocumentationType.SWAGGER_2)
            .groupName("webApi")
            .apiInfo(webApiInfo())
            .select()
            .build();
    }
    private ApiInfo webApiInfo(){
        return new ApiInfoBuilder()
            .title("rabbitmq 接口文档")
            .description("本文档描述了 rabbitmq 微服务接口定义")
            .version("1.0")
            .contact(new Contact("test", "http://year21.top",
                                 "test@qq.com"))
            .build();
    } }
~~~

④填写相关配置信息

~~~yml
spring:
  rabbitmq:
    host: 192.168.231.133
    port: 5672
    username: admin
    password: admin
~~~

