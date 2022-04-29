---
title: Redis
date: 2022-04-28 10:48:02
updated: 2022-04-28 10:48:02
tags:
  - Redis
categories:
  - DataBase
keywords: Redis缓存
cover: https://s1.ax1x.com/2022/04/28/LOGwvj.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/04/28/Redis
---


## NoSQL数据库概述

NoSQL是为了解决性能问题而产生的一种技术,<font color="red">**Redis是一种以键值型存储数据的典型NoSQL数据库**</font>

Nosql数据库的出现有利于解决服务器CPU和内存、以及IO读写的的巨大压力

Nosql数据库不遵循SQL标准，不支持ACID四个属性(<font color="red">**并不是不支持事务**</font>)，远超于SQL性能

- <font color="red">**用不着sql的和用了sql也不行的情况，可以考虑用NoSql数据库**</font>

- Redis是单线程+IO多路复用技术，支持多种数据类型的数据，可以持久化，也是保存在内存中

  每一个Redis对应一个服务器

  IO多路复用：单线程或单进程同时监测若干个 文件描述符 是否可以执行IO操作的能力。

- MemCached使用的是多线程+锁，MemCached只支持单一类型的数据，且不能持久化，只能保存在内存中

## Redis常用五大数据类型

### Redis键(key)

| 常用命令      | 命令的说明                                                   |
| :------------ | :----------------------------------------------------------- |
| keys *        | 查看当前库所有key                                            |
| exists key    | 判断某个key是否存在                                          |
| type key      | 查看你的key是什么类型                                        |
| del key       | 删除指定的key数据                                            |
| unlink key    | 根据value选择非阻塞删除(仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作。) |
| expire key 10 | 10秒钟：为给定的key设置过期时间                              |
| ttl key       | 查看还有多少秒过期，-1表示永不过期，-2表示已过期             |
| select        | 命令切换数据库(总共16个库，默认使用0号库)                    |
| dbsize        | 查看当前数据库的key的数量                                    |
| flushdb       | 清空当前库                                                   |
| flushall      | 通杀全部库                                                   |

### 字符串(String)

String是redis最基本的数据类型，一个key对应一个value，一个Redis中字符串value最多可以是<font color="red">**512M**</font>

**String类型是<font color="red">二进制安全的</font>**，换句话理解就是只要能转换成字符串的，都能用String进行存储

| 常用命令                               | 命令的说明                                                   |
| :------------------------------------- | :----------------------------------------------------------- |
| set  key value                         | 添加键值对                                                   |
| get  key                               | 查询对应键值                                                 |
| append  key value                      | 将给定的 value 追加到原值的末尾                              |
| strlen  key                            | 获得值的长度                                                 |
| setnx  key value                       | 只有在 key 不存在时   设置 key 的值                          |
| incr  key                              | 将 key 中储存的数字值增1，只能对数字值操作，如果为空，新增值为1 |
| decr  key                              | 将 key 中储存的数字值减1，只能对数字值操作，如果为空，新增值为-1 |
| incrby / decrby  key  <步长>           | 将 key 中储存的数字值增减。自定义步长。                      |
| mset  key1 value1 key2 value2  .....   | 同时设置一个或多个 key-value对                               |
| mget  key1 key2 key3 .....             | 同时获取一个或多个 value                                     |
| msetnx key1 value1  key2 value2  ..... | 同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。 |
| getrange  key <起始位置><结束位置>     | 获得值的范围，包括了起始位置和结束位置对应索引的值           |
| setrange   key  <起始位置>  value      | 用 value  覆写 key 所储存的字符串值，从<起始位置>开始(索引从0开始)。 |
| setex  key  <过期时间>  value          | 设置键值的同时，设置过期时间，单位秒。                       |
| getset key value                       | 以新换旧，设置了新值同时获得旧值。                           |

1.所谓**原子**操作是指不会被线程调度机制打断的操作；

①在单线程中， 能够在单条指令中完成的操作都可以认为是"原子操作"，因为中断只能发生于指令之间。

②在多线程中，不能被其它进程（线程）打断的操作就叫原子操作。

- Redis单命令的原子性主要得益于Redis的单线程。

2.其底层的数据结构

- <font color="red">**简单动态字符串**</font>(Simple Dynamic String,缩写SDS)。

它是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配.

![底层结构](https://s1.ax1x.com/2022/04/28/LOlINF.png)

capacity为当前字符串实际分配的空间，一般要高于实际字符串长度len。

当字符串长度超过len的时候进行扩容，而扩容有两种情况

①当字符串长度小于1M时，扩容都是加倍现有的空间，即为原来的2倍

②如果超过1M，扩容时一次只会多扩1M的空间。

- 需要注意的是字符串最大长度为512M。

### 列表(List)

List列表存储的都是有序，可重复的元素

单键多值：一个key对应多个value

Redis 列表是简单的字符串列表，可以按照插入的顺序进行排序，其的底层是个<font color="red">**双向链表**</font>，对频繁的删除和修改的效率较高，对查询的效率较低

| 常用命令                                        | 命令的说明                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| lpush/rpush  <key><value1><value2><value3> .... | 从左边/右边插入一个或多个值。lpush是头插法，从头插入；rpush是尾插法，从尾插入 |
| lpop/rpop  <key>                                | 从左边/右边取出一个值。值在键在，值光键亡。                  |
| rpoplpush  <key1><key2>                         | 从<key1>列表右边吐出一个值，插到<key2>列表左边。             |
| lrange <key><start><stop>                       | 按照索引下标获得元素(从左到右)                               |
| lrange mylist 0 -1                              | 0左边第一个，-1右边第一个，（0-1表示获取所有）               |
| lindex <key><index>                             | 按照索引下标获得元素(从左到右)                               |
| llen <key>                                      | 获得列表长度                                                 |
| linsert <key>  before <value><newvalue>         | 在<value>的后面插入<newvalue>插入值                          |
| lrem <key><n><value>                            | 从左边删除n个value(从左到右)                                 |
| lset<key><index><value>                         | 将列表key下标为index的值替换成value                          |

其底层的数据结构

- <font color="red">**快速链表quickList**</font>

  ①在存储的元素较少的情况采用的是压缩列表(zipList)，它将所有的元素都集中在一起，使用一块连续的内存空间存储数据

  ②在存储的数据量较大的情况下Redis将链表和ziplist结合起来组成了quicklist。也就是将多个ziplist使用双向指针串起来使用

  ​	既满足了快速的插入删除性能，又不会出现太大的空间冗余。

![quickList的结构](https://s1.ax1x.com/2022/04/28/LOlzND.png)

### 集合(Set)

集合(Set)存储的都是无序，不可重复的元素

Redis的Set是string类型的无序集合。它底层其实是一个value为null的hash表，所以添加，删除，查找的<font color="red">**复杂度都是O(1)**</font>

| 常用命令                         | 命令的说明                                                   |
| :------------------------------- | :----------------------------------------------------------- |
| sadd <key><value1><value2> ..... | 将一个或多个 member 元素加入到集合 key 中，已经存在的 member 元素将被忽略 |
| smembers <key>                   | 取出该集合的所有值。                                         |
| sismember                        | 判断集合<key>是否为含有该<value>值，有1，没有0               |
| scard<key>                       | 返回该集合的元素个数。                                       |
| srem <key><value1><value2> ....  | 删除集合中的某个元素。                                       |
| spop <key>                       | 随机从该集合中取出一个值                                     |
| srandmember <key><n>             | 随机从该集合中取出n个值。不会从集合中删除 。                 |
| smove <source><destination>value | 把集合中一个值从一个集合移动到另一个集合                     |
| sinter <key1><key2>              | 返回两个集合的交集元素                                       |
| sunion <key1><key2>              | 返回两个集合的并集元素。                                     |
| sdiff <key1><key2>               | 返回两个集合的差集元素(key1中的，不包含key2中的)             |

其底层的数据结构

- Set数据结构是<font color="red">**dict字典**</font>，字典是用哈希表实现的。

  Java中HashSet的内部实现使用的是HashMap，只不过所有的value都指向同一个对象。

  Redis的set结构也是一样，它的内部也使用hash结构，所有的value都指向同一个内部值。

### 哈希(Hash)

Redis hash 是一个键值对集合。

Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。类似Java里面的Map<String,Object>

主要有2种存储方式：

![存储方式](https://s1.ax1x.com/2022/04/28/LO1kut.png)

| 常用命令                                        | 命令的说明                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| hset <key><field><value>                        | 给<key>集合中的  <field>键赋值<value>                        |
| hget <key1><field>                              | 从<key1>集合<field>取出 value                                |
| hmset <key1><field1><value1><field2><value2>... | 批量设置hash的值                                             |
| hexists<key1><field>                            | 查看哈希表 key 中，给定域 field 是否存在。                   |
| hkeys <key>                                     | 列出该hash集合的所有field                                    |
| hvals <key>                                     | 列出该hash集合的所有value                                    |
| hincrby <key><field><increment>                 | 为哈希表 key 中的域 field 的值加上增量 1  -1                 |
| hsetnx <key><field><value>                      | 将哈希表 key 中的域 field 的值设置为 value ，当且仅当域 field 不存在 . |

其底层的数据结构

- Hash类型对应的数据结构是两种：<font color="red">**ziplist（压缩列表），hashtable（哈希表）**</font>

  ①当field-value长度较短且个数较少时，使用ziplist，

  ②当field-value长度较长且个数较多时，使用hashtable。

### 有序集合Zset(Sorted set)

有序集合Zset同样是存储一个<font color="red">**没有重复元素的字符串集合**</font>，但每一个元素都携带了<font color="red">**评分(score)**</font>

因此可以通过评分对集合中的成员进行从最低分到最高分的排序，集合元素是唯一的，但评分可以是重复的

| 常用命令                                                     | 命令的说明                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| zadd  <key><score1><value1><score2><value2>…                 | 将一个或多个 member 元素及其 score 值加入到有序集 key 当中。 |
| zrange <key><start><stop>  [WITHSCORES]                      | 返回有序集 key 中，下标在<start><stop>之间的元素。带WITHSCORES，可以让分数一起和值返回到结果集。 |
| zrangebyscore key minmax [withscores] [limit offset count]   | 返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从小到大)次序排列。 |
| zrevrangebyscore key maxmin [withscores] [limit offset count] | 同上，改为从大到小排列。                                     |
| zincrby <key><increment><value>                              | 为元素的score加上增量                                        |
| zrem  <key><value>                                           | 删除该集合下，指定值的元素                                   |
| zcount <key><min><max>                                       | 统计该集合，分数区间内的元素个数                             |
| zrank <key><value>                                           | 返回该值在集合中的排名，从0开始。                            |

- 其底层的数据结构

  zset底层使用了两个数据结构：

  （1）<font color="red">**hash**</font>，hash的作用就是关联元素value和权重score，保障元素value的唯一性，可以通过元素value找到相应的score值。

  （2）<font color="red">**跳跃表**</font>，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表。

​	①一方面它等价于Java的数据结构Map<String, Double>，可以给每一个元素value赋予一个权重score，

​	②另一方面它又类似于TreeSet，内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表。

---

## Redis新数据类型

### Bitmaps

Redis提供了Bitmaps这个“数据类型”可以实现对位的操作：

①Bitmaps本身不是一种数据类型， 实际上它就是字符串（key-value） ，可以理解尾它是<font color="red">**专门进行位操作的字符串**</font>

②可以把Bitmaps想象成一个以位为单位的数组， 数组的每个单元只能存储0和1， 数组的下标在Bitmaps中叫做偏移量。

| 常用命令                               | 命令的说明                                                   |
| :------------------------------------- | :----------------------------------------------------------- |
| setbit<key><offset><value>             | 设置Bitmaps中某个偏移量的值（0或1）offset:偏移量从0开始      |
| getbit<key><offset>                    | 获取Bitmaps中某个偏移量的值                                  |
| bitcount<key>[start end]               | 统计字符串从start字节到end字节比特值为1的数量  。-1 表示最后一个位，而 -2 表示倒数第二个位， |
| bitop and(or/not/xor) <destkey> [key…] | bitop是一个复合操作， 把对多个Bitmaps的and（交集） 、 or（并集） 、 not（非） 、 xor（异或） 操作并将结果保存在destkey中。 |

- Bitmaps与set对比之下：Bitmaps针对活跃用户的存储更能节省空间和内存，反之，如果活跃用户较少用set储存更好

### HyperLoglog

- HyperLoglog可以用于解决基数统计问题(求集合中不重复元素个数的问题称为基数问题)。

- 其优点是在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的。

  但是 HyperLogLog 不能像集合那样，返回输入的各个元素。

| 常用命令                                    | 命令的说明                                                |
| :------------------------------------------ | :-------------------------------------------------------- |
| pfadd <key>< element> [element ...]         | 添加指定元素到 HyperLogLog 中，成功则返回1，失败则返回0。 |
| pfcount<key> [key ...]                      | 计算HLL的近似基数，可以计算多个HLL                        |
| pfmerge<destkey><sourcekey> [sourcekey ...] | 将一个或多个HLL合并后的结果存储在另一个HLL中              |

### Geospatial

GEO：Geographic，地理信息的缩写。

- redis提供了对经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。

| 常用命令                                                     | 命令的说明                                 |
| :----------------------------------------------------------- | :----------------------------------------- |
| geoadd<key>< longitude><latitude><member> [longitude latitude member...] | 加地 理位置（经度，纬度，名称）            |
| geopos  <key><member> [member...]                            | 获得指定地区的坐标值                       |
| geodist<key><member1><member2>  [m\|km\|ft\|mi]              | 获取两个位置之间的直线距离                 |
| georadius<key>< longitude><latitude>radius m\|km\|ft\|mi     | 以给定的经纬度为中心，找出某一半径内的元素 |

---

## Redis的发布和订阅

- Redis 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息。

- Redis 客户端可以订阅任意数量的频道。

![通信演示](https://s1.ax1x.com/2022/04/28/LO1Mgs.png)

---

## Jedis实例

- **完成一个手机验证码功能**

要求：

1、输入手机号，点击发送后随机生成6位数字码，2分钟有效

2、输入验证码，点击验证，返回成功或失败

3、每个手机号每天只能输入3次

~~~java
public class PhoneCode {
    private static Jedis jedis;
    static {
        jedis = new Jedis("192.168.231.133",6379);
    }

    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        //模拟输入手机号后获取验证码的过程
        System.out.print("请输入你的手机号:");
        String phone = scan.next();
        //判断手机号是否已存在
        checkPhone(phone);
        //获取当前手机号已获取验证码的次数
        String num = jedis.get(phone);
        //转换位int类型
        int p = Integer.parseInt(num);
        //实现限制一天只能获取三次的目的
        if (p > 3){
            System.out.println("今日获取验证码的次数已达到3次，请明日再试");
            jedis.close();
            return;
        }

        //模拟生成验证码并从键盘中获取验证码的过程
        String code = getCode();
        System.out.print("你的验证码是：" + code + "\n");
        System.out.print("请输入你的验证码:");
        String use = scan.next();

        //执行具体业务
        checkCode(code);

        //显示验证信息
        String result = isSuccess(use);
        System.out.println(result);

        //关闭jedis
        jedis.close();
    }

    //1.生成6位数的验证码字符串
    private static String getCode(){
        int v = (int) (Math.random() * (999999 - 100000 + 1) + 100000);
        return String.valueOf(v);
    }

    //2.设置验证码相关
    public static void  checkCode(String code){
        //1.获取验证码并放入redis中，同时设置过期时间
        jedis.setex("code",120,code);
    }

    //设置手机号相关的
    public static void checkPhone(String phone){
        //设置发送的次数
        String isExists = jedis.get(phone);
        if (isExists == null){
            jedis.setex(phone,24*60*60,"1");
        }else {
            jedis.incr(phone);
        }
    }

    //3.进行判断
    public static String isSuccess(String use){
        //判断验证码是否过期
        //没过期可用
        if (jedis.exists("code")){
            if (jedis.get("code").equals(use)){
               return "登录成功";
            }else{
                return  "验证码错误，请重新输入";
            }
        }
        //已过期不可用
        return "验证码已过期，请重新获取";
    }
}
~~~

---

## Redis事务

Redis事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

<font color="red">**Redis事务的主要作用就是串联多个命令防止别的命令插队。**</font>

- 事务的执行顺序命令：

  Multi命令 开启事务，进入组队阶段(即将所有命令加入到一个队列中)

  Exec命令 执行事务 (执行队列中的命令)，进入执行阶段，按顺序执行

  discard命令 放弃事务的执行 (放弃执行队列的命令)，即中断执行阶段

- 对命令的监听

  watch命令 可用监听一个或多个key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断。

  unwatch命令 取消对所有key的监听，如果在执行watch命令之后，先执行了EXEC 或DISCARD的命令，那么就用再执行UNWATCH 了。

- 事务执行的特殊情况：

  ①在**组队**阶段，队列中的<font color="orange">**任意一个命令出错都会导致执行阶段报错**</font>

  ②在**执行**阶段，队列中的<font color="orange">**有一个命令出错后，出错的命令不会执行，其余的依旧执行**</font>

![事务](https://s1.ax1x.com/2022/04/28/LO13D0.png)

### 解决事务冲突

- <font color="orange">**悲观锁**</font>：给数据加锁

  悲观锁认为每次都会对数据进行修改，因此在获取数据之后都会给数据加上锁，在数据处理完成之后才会解锁。

  悲观锁多用于**关系型数据库**，如行锁、表锁、读锁、写锁等等

  ![悲观锁示意](https://s1.ax1x.com/2022/04/28/LO1YUU.png)


- <font color="orange">**乐观锁**</font>：给操作的数据添加版本号

  乐观锁认为每次都不会对数据进行修改，因此每个线程都能获取到数据，但在数据更改进行都会对比数据的版本号是否一则

  **乐观锁适用于多读的应用类型，这样可以提高数据的查询效率**

  ![乐观锁示意](https://s1.ax1x.com/2022/04/28/LO1d29.png)

### Redis事务的三个特性

① 单独的隔离操作 

事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。 

②没有隔离级别的概念 

队列中的命令没有提交之前都不会实际被执行，因此添加的数据不会被其他线程读取到

③不保证原子性 

事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚 

### lua脚本

- Lua由于自身原因(完整lua解释器才200k)不适合作为开发独立应用程序的语言，而是作为嵌入式脚本语言。

- LUA脚本在Redis中的优势：

  将复杂的或者多步的redis操作，写为一个脚本，一次提交给redis执行，减少反复连接redis的次数。提升性能。

​		LUA脚本是类似redis事务，有一定的原子性，不会被其他命令插队，可以完成一些redis事务性的操作。

### Redis事务的案例

版本一：未添加事务和锁，出现了超卖的问题

版本二：添加事务和乐观锁，解决了超卖问题，但又出现了连接超时和少买的问题

版本三：使用jedis数据连接池，解决了连接超时的问题

版本四：使用lua脚本解决库存遗留问题，少买问题是出现是因为乐观锁在每次修改数据都更新数据的版本号，在同一时间上版本号不同导致数据不能修改。

通过lua脚本解决库存遗留问题，实际上是<font color="red">**redis 利用其单线程的特性，用任务队列的方式解决多任务并发问题***</font>

~~~java
	//秒杀过程
	public static boolean doSecKill(String uid,String prodid) throws IOException {
		//1 uid和prodid非空判断
		if(uid == null || prodid == null) {
			return false;
		}

		//2.连接redis
		JedisPool jedisPoolInstance = JedisPoolUtil.getJedisPoolInstance();
		Jedis jedis = jedisPoolInstance.getResource();

		//3.判断用户是否已参加过秒杀活动

		Boolean result = jedis.sismember("userId",uid);

		//为空说明没参加过秒杀活动
		if (!result){
			//监视库存
			jedis.watch("stock");

			//4.获取库存的值，判断秒杀活动是否开始
			String count = jedis.get("stock");
			if (count == null){
				//为空代表活动暂未开始
				System.out.println("活动暂未开始");
				jedis.close();
				return false;
			}
			//5.判断库存是否为0，为0则代表秒杀活动已经结束了
			else if (Integer.parseInt(count) == 0){
				System.out.println("秒杀活动已经结束了");
				jedis.close();
				return false;
			}
			//6.不为空，开始执行秒杀过程
			//使用事务
			//获取事务对象
			Transaction multi = jedis.multi();

			//将各个命令添加到队列中
			//6.1用户秒杀的商品库存 -1
			multi.decr("stock");
			//6.2 将参加秒杀成功的用户id加入到redis中
			multi.sadd("userId",uid);

			//执行
			List<Object> results = multi.exec();

			//判断是否正常执行
			if (results == null | results.size() == 0){

				System.out.println("秒杀失败");
				jedis.close();
				return false;
			}else {
				System.out.println("秒杀成功");
				jedis.close();
				return true;
			}
		}
		System.out.println("每个用户只能参加一次秒杀活动");
		jedis.close();
		return false;
	}
~~~

---

## Redis持久化

### RDB

RDB全称是Redis Database，它是指在<font color="red">**指定的时间间隔内将内存中的数据集快照写入磁盘**</font>， 可理解为Snapshot快照，它恢复时是将快照文件读到内存里。

fork：复制一个与当前进程一样的进程。新进程的所有数据都和原进程一致，但它是一个全新的进程，并作为原进程的子进程。

​		   **一般情况父进程和子进程会共用同一段物理内存**。

1.RDB的持久化过程

Redis通过fork单独创建一个属于父进程的子进程负责对redis内存中的数据进行持久化工作，其使用的是<font color="red">**写时复制技术**</font>，

写时复制技术即同步之前先将数据从redis内存中写入到一个临时文件中，等同步完成后，再用临时文件替换原来的持久化文件(dump.rdb)

持久化文件(dump.rdb)：redis在哪个文件目录下启动就在哪个文件目录下生成

<font color="red">**RDB备份的缺点是最后一次持久化后的数据可能丢失。**</font>特别是在最后一次进行持久化未完成的过程中服务器宕机了

2.命令  save 和 bgsave的区别

save ：save时只管保存，其它不管，全部阻塞。手动保存。不建议。

bgsave：Redis会在后台异步进行快照操作， 快照同时还可以响应客户端请求。

- 可以通过lastsave 命令获取最后一次成功执行快照的时间

3.rdb的备份与恢复

①先通过config get dir 查询rdb文件生成的目录，再将*.rdb的文件拷贝到别的地方

②rdb的恢复

第一步：关闭Redis

第二步：先把备份的文件拷贝到工作目录下 cp dump2.rdb dump.rdb

第三步：启动Redis, 备份数据会直接加载

4.RDB持久化的优点和缺点

- 优点：

  ①适合大规模的数据恢复，但对数据完整性和一致性要求较高则不适合使用

  ②节省磁盘空间，恢复速度快

- 缺点：

  ①数据过于庞大时比较消耗性能。

  ②最后一次持久化后的数据可能丢失

  ③在进行fork操作的过程中，需要2倍的内存空间

![优劣点](https://s1.ax1x.com/2022/04/28/LO1c5D.png)

### AOF

AOF的全称是(Append Only File)。

它是指以日志的形式来保存每个写操作(增删改)的数据以及Redis执行过相应的写指令(读操作不记录)， 只许追加文件但不可以改写文件。

- <font color="red">**AOF和RDB同时开启的情况下，系统会默认取AOF的数据（数据不会存在丢失）**</font>
- AOF默认不开启,而EDB是默认开启的，两者生成的保存路径都一致，即启动目录在哪保存在哪

1.AOF的持久化过程

①客户端的请求写命令会被append追加到AOF缓冲区内；

②AOF缓冲区根据AOF持久化策略[always,everysec,no]将操作sync同步到磁盘的AOF文件中；

③AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量；

④Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的；

![持久化示例](https://s1.ax1x.com/2022/04/28/LO1W2d.png)

2.AOF的备份和恢复

- 正常情况下，AOF的备份和恢复和RDB是一致的

- 特殊情况下的异常恢复，当AOF的文件出现损坏时，

  ①修改默认的appendonly no，改为yes

  ②通过/usr/local/bin/redis-check-aof--fix appendonly.aof进行恢复

  ③备份被写坏的AOF文件

  ④恢复：重启redis，然后重新加载

3.AOF的同步频率

①appendfsync always

始终同步，每次Redis的写入都会立刻记入日志；性能较差但数据完整性比较好

②appendfsync everysec

每秒同步，每秒记入日志一次，如果宕机，本秒的数据可能丢失。

③appendfsync no

redis不主动进行同步，把同步时机交给操作系统。

4.Rewrite压缩

Rewrite压缩是指在AOF采用文件追加方式，文件会越来越大，为避免出现此种情况，因此新增了重写机制。 

当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩， 只保留可以恢复数据的最小指令集.

- 触发Rewrite压缩的条件：Redis的AOF文件当前大小>= base_size(上一次AOF持久化文件的大小) * 2 且当前大小>=64mb(默认)

5.AOF的优势和劣势

- 优势：

  ①备份机制更稳健，丢失数据概率更低。

  ②可读的日志文本，通过操作AOF稳健，可以处理误操作。

- 劣势：

  ①比起RDB占用更多的磁盘空间。

  ②恢复备份速度要慢。

  ③每次读写都同步的话，有一定的性能压力。

  ④存在个别Bug，造成恢复不能。

### 总结

官方推荐两个都启用。

如果对数据不敏感，可以选单独用RDB。

不建议单独用 AOF，因为可能会出现Bug。

如果只是做纯内存缓存，可以都不用。

---

## Redis主从复制

1.<font color="red">**主从复制**</font>：主机数据更新后根据配置和策略， 自动同步到备机的一种名为master/slaver机制

- 一般都是一主多从，在这个机制中master为主机，主要负责写的操作，slaver为从机，主要负责读的操作

2.主从复制的优点：①读写分离，可以提升性能  ②能够快速应对服务器宕机的情况，容灾恢复

![主从复制](https://s1.ax1x.com/2022/04/28/LO1zq0.png)

### 主从复制的原理

①当从服务器<font color="red">**第一次**</font>连接上主服务器，<font color="red">**从服务器就会主动向主服务器发起数据同步的消息**</font>

②主服务器接受到从服务器发起的数据同步消息后，将主服务器的数据进行持久化到rdb文件中，

​	把rdb文件发个从服务器，从服务器拿到rdb文件并读取完成数据同步

③<font color="red">**除了第一次以外**</font>，每次主服务器进行写操作，<font color="red">**都会主动把数据发给从服务器进行同步**</font>

- 全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
- 增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步

### 主机从机之间的说明

- 一主多从的情况说明：

  ①当从机宕机后，重新启动后会自动将主机中的数据同步到本机中

  ②当主机宕机后，从机依旧是从机，但是在从机的信息中能记录此刻主机已挂

- 采用一个主机管理一个从机，这个从机是另一个从机的主机的情况：

  当中间的从机宕机后，后面从机的数据的同步就会停止

- 当一个master宕机后，后面的slave可以立刻升为master，其后面的slave不用做任何修改。

  用 slaveof  no one 命令将从机变为主机。

### 哨兵模式

哨兵模式：能够后台监控主机是否故障，如果故障了根据投票数自动将从机转换为主机

1.如何设置哨兵模式？

​	需要设置名为sentinel.conf的配置文件，在配置文件内编写sentinel monitor mymaster 127.0.0.1 6379 1

- mymaster为监控对象起的服务器名称， 1 为至少有多少个哨兵同意迁移的数量。 

#### 复制延时

由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，

所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，

延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。

#### 主机宕机后的操作

![哨兵模式监控流程](https://s1.ax1x.com/2022/04/28/LO3eqx.png)

①优先级在redis.conf中默认：replica-priorit 100，值越小优先级越高

②偏移量是指获得原主机数据最全的

③每个redis实例启动后都会随机生成一个40位的runid

---

## Redis集群

redis集群的概念：实现了对Redis的水平扩容，即启动N个redis节点，将整个数据库分布存储在这N个节点中，每个节点存储总数据的1/N。

redis集群可以解决redis容量不足以及在并发写操作下分摊redis主机的压力

- 在主从模式，薪火相传模式，主机宕机，都会导致ip地址发生变化等问题的出现，需要在配置文件中修改配置信息和端口

​		解决方式：在redis3.0之前通过代理主机来解决，在redis3.0后无中心化集群配置来解决

​		代理主机：可以理解为集中式，即所有请求先访问代理主机，由代理主机分配到指定的服务器进行处理

​		无中心化集群配置：多个redis服务器组成一个集群，集群内每个redis都可以作为请求的入口，

​											接受请求后进行判断，不是自己处理的请求则进行转发，最后转发至目标redis服务器。

### 集群的分配

redis-cli --cluster create --cluster-replicas 1 是采用最简单的方式分配集群，这里的1代表每个主机分配一个从机。

而一个集群至少要有三个主节点，总共需要6个服务器。一台主机，一台从机，正好三组。

分配原则是每一个redis不能使用在同一个服务器下，ip也不能相同，才能保证主机宕机，从机切换的效果。

cluster nodes可以查看集群中主机和从机的状态信息

![redis集群分配](https://s1.ax1x.com/2022/04/28/LO3QiD.png)

### 插槽slots

All 16384 slots covered.

<font color="red">**每一个Redis集群总共有16384个插槽(hash slot)，数据库中每一个键都是属于13684插槽之一**</font>

集群使用公式 CRC16(key) % 16384 来计算键 key 属于哪个槽， 其中 CRC16(key) 语句用于计算键 key 的 CRC16 校验和 。

每一个主机负责处理一部分插槽的数据，参考上图，192.168.231.133：6379这个主机只负责0-5460的插槽

- 不在同一个slot下的键值，是不能使用mget,mset等多键操作。
- CLUSTR keyslot cout 返回这个cout键的所在的插槽值
- CLUSTR countkeysinslot 4874 返回4874这个插槽中键的数量
- CLUSTER GETKEYSINSLOT【 slot】【count】 返回 count 个 slot 槽中的键的值

### 集群中主机宕机

- 1.集群中主机宕机之后，15秒内不能恢复，则自动将该主节点的从机升为主节点

- 2.当之前宕机状态的主机恢复后，由原来的主机身份变成了从机

- 3.当集群中某个主机和它的从机都宕机后，根据redis.conf中的参数 

   ​	cluster-require-full-coverage的值会有两种情况：

  ​		①cluster-require-full-coverage为yes，则整个集群都挂掉，不能提供服务

  ​		②cluster-require-full-coverage为no，则宕机的主机负责的插槽段，既不能使用也无法存储数据、

### Jedis集群开发

- 即使连接的不是主机，集群会自动切换主机存储。主机写，从机读。

- 无中心化主从集群。无论从哪台主机写的数据，其他主机上都能读到数据。

  实际上读取数据是由对应插槽的主机进行查询的，也符合了主机只能查询自己负责的插槽段内的数据的说法

![数据读取的说明](https://s1.ax1x.com/2022/04/28/LO3ao8.png)

~~~java
public static void main(String[] args) {
        //创建对象
        HostAndPort hostAndPort = new HostAndPort("192.168.231.133", 6379);
        JedisCluster jedisCluster = new JedisCluster(hostAndPort);

        //进行操作
        jedisCluster.set("name","zhangsan");

        String name = jedisCluster.get("name");

        System.out.println("名字是：" + name);

        jedisCluster.close();
    }
~~~

### 集群的优点和缺点

优点：

①解决redis内存不足的问题，进行了扩容

②分摊了主服务器的压力

③无中心化配置相对简单

缺点：

①多键操作不被支持

②多键的redis的事务不被支持，也不支持lua脚本

③由于集群方案出现较晚，很多公司已经采用了其他的集群方案，而代理或者客户端分片的方案想要迁移至redis cluster，

​	需要整体迁移而不是逐步过渡，复杂度较大。

---

## Redis应用出现的问题

### 缓存穿透

缓存穿透：指有请求一直在查询缓存和数据库中都不存在的数据。

例如，用户不断发起请求。发起为id为“-1”的数据或id为特别大不存在的数据。这时的用户很可能是攻击者，攻击会导致数据库压力过大，从而崩溃。

缓存穿透的现象：1.应用服务器的压力变大了  2.redis的命中率降低  3.一直查询数据库

![缓存穿透](https://s1.ax1x.com/2022/04/28/LO30Jg.png)

- 解决缓存穿透的方案：

  1.对空值缓存：如果查询的数据值为空(不管数据是否存在)，依旧把空结果返回到redis中进行缓存，同时给此空结果设置较短的过期时间

  2.设置白名单：使用bitmap类型定义一个名单，存在于名单中的id可以访问，不存在则拦截

  3.采用布隆过滤器(Bloom Filter)：布隆过滤器是1970年由布隆提出的。它实际上是一个很长的二进制向量(位图)和一系列随机映射函数（哈希函数）。

  布隆过滤器可以用于检索一个元素是否在一个集合中。它的优点是空间效率和查询时间都远远超过一般的算法，缺点是有一定的误识别率和删除困难。

  将所有可能存在的数据哈希储存到一个足够大的bitmaps中，一个一定不存在的数据会被bitmaps拦截掉，从而避免了对底层存储系统的查询压力。

  4.进行实时监控：当redis命中率开始急速降低时，排查出具体的访问对象和数据，设置黑名单限制访问

### 缓存击穿

缓存击穿：指在一个时间间隔内某些key恰好过期的同时遇到了极大的请求访问这个key

缓存击穿的现象：1.数据库访问的压力瞬时增大  2.redis没有出现大量key过期  3.redis正常运行

![缓存击穿](https://s1.ax1x.com/2022/04/28/LO3Dzj.png)

- 解决缓存击穿的方案：

  1.预先设置热门数据：在redis的高并发访问之前，提前将热门数据缓存到redis中并增加热门数据key的时长

  2.实时调整：根据情况不断调整key的过期时间

  3.使用锁：

  ​	①就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db。

  ​	② 先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX）去set一个mutex key

  ​	③ 当操作返回成功时，再进行load db的操作，并回设缓存,最后删除mutex key；

  ​	④ 当操作返回失败，证明有线程在load db，当前线程睡眠一段时间再重试整个get缓存的方法。

  ![演示](https://s1.ax1x.com/2022/04/28/LO3sQs.jpg)

### 缓存雪崩

缓存雪崩：指在在一个时间间隔内大量的key全部过期的同时收到大量的请求这些过期的key，

​					这些请求会一瞬间压到数据库，导致最终数据库和redis缓存数据库两者都崩溃

缓存雪崩的现象：1.极少的时间段内大量查询的key集中过期

![缓存雪崩](https://s1.ax1x.com/2022/04/28/LO36Lq.png)

- 缓存雪崩的解决方案：

  1.构造多级缓存架构：nginx缓存 + redis缓存 +其他缓存（ehcache等）

  2.使用锁或队列：用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，但效率太低，不适用高并发情况。

  3.设置过期标志更新缓存：对缓存的数据进行监控，在过期前某段时间进行更新。

  4.将缓存的时间分散开：可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低。

### 分布式锁

在单机部署变成分布式集群部署后，原单机部署情况下的并发控制锁策略失效，即对锁只能对某一个主机生效，而不能对整个集群起作用。

分布式锁：<font color="red">**多个线程对同一个共享资源进行操作出现的安全问题，在为了解决这个问题产生的一种跨JVM的互斥机制来控制共享资源的访问。**</font>

1.在redis中实现分布式锁(以下的内容基本都是基于单机考虑的)

<font color="red">**在set时指定过期时间（推荐）set key value ex sectime nx**</font>

- 除了这个方式还可以通过setnx设置锁，expire设置锁过期的时间，但不推荐，主要是因为两个命令不是同时进行的，不是原子操作

![分布式锁的实现逻辑](https://s1.ax1x.com/2022/04/28/LO3fFU.png)

2. 分布式锁在redis中应用的可能出现的问题

- 问题1：<font color="red">**误释放其他线程的锁**</font>

​		分布式锁执行的流程：

​		①手动加锁

​		②业务操作

​		③手动释放锁

​		④如果手动释放锁失败了，则达到超时时间，redis会自动释放锁。

​		解决方案：<font color="red">**在设置加锁的时候设置一个唯一的uuid值，在释放锁之前进行对比，相同则释放锁**</font>

![误释放问题](https://s1.ax1x.com/2022/04/28/LO3hYF.png)

- 问题2：<font color="red">**释放锁的原子性问题**</font>

​		解决方案：<font color="red">**使用lua脚本释放锁，保证原子性操作**</font>

~~~java
// 2. 释放锁 del
String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
// 设置lua脚本返回的数据类型
DefaultRedisScript<Long> redisScript = new DefaultRedisScript<>();
// 设置lua脚本返回类型为Long
redisScript.setResultType(Long.class);
redisScript.setScriptText(script);
redisTemplate.execute(redisScript, Arrays.asList("lock"),uuid);
~~~

![原子性问题](https://s1.ax1x.com/2022/04/28/LO3ISJ.png)

### 总结

- 确保分布式锁可用必须同时满足以下四个条件：
  -  互斥性。在任意时刻，只有一个客户端能持有锁。
  - 不会发生死锁。即使有一个客户端在持有锁的期间崩溃而没有主动解锁，也能保证后续其他客户端能加锁。
  - 加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了
  - 加锁和解锁必须具有原子性。

