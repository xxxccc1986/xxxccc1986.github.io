---
title: MyBatis-Plus
date: 2022-07-07 23:00:02
updated: 2022-07-07 23:00:02
tags:
  - MyBatis-Plus
categories:
  - 框架
keywords: MyBatis-Plus
cover: https://s1.ax1x.com/2022/07/07/j0r9zR.png
copyright_author: Year21
copyright_url: http://year21.top/2022/07/07/MyBatis——plus
---

### 前置知识

[MyBatis-plus](https://www.yuque.com/docs/share/0112fd84-de5f-433b-996a-b9d9cd87dd36?#%20%E3%80%8AMyBatisPlus(SpringBoot%E7%89%88)--2022%E3%80%8B)的框架结构

通过扫描指定的实体类 --> 反射抽取实体类的属性 --> 分析是对应哪张表已经查询的字段 

--> 最终形成查询的sql语句 --> 将sql语句注入mybatis的容器中

![](https://s1.ax1x.com/2022/07/07/j0DGG9.png)

- 配置数据源

~~~yml
spring:
  datasource:
    # 配置数据源的类型
    type: com.zaxxer.hikari.HikariDataSource
    # 配置数据源的各个信息
    url: jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf8&useSSL=false&serverTimezone=UTC&rewriteBatchedStatements=true
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver

# 在控制台打印mybatis-plus生成的sql查询语句
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
~~~

- 编写实体类和mapper接口

  主程序上使用**@MapperScan**注解的作用是扫描某个指定包下的所有mapper接口

  也可以使用**@Mapper**对单个mapper接口标识，就不需要在主程序类上使用@MapperScan注解

~~~java
//用于与数据表形成映射关系的实体类
@Data//简化实体类的开发
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}

//与用户数据表形成映射关系的mapper表
//Mapper 继承该接口后，无需编写 mapper.xml文件，即可获得CRUD功能
public interface UserMapper extends BaseMapper<User> {//BaseMapper的泛型T表示要操作哪个实体类
}
~~~

- 测试

  <font color='red'>**在测试中如果要自动装配且使用到容器内的bean一定要添加@RunWith(SpringRunner.class) 此注解**</font>

~~~java
@SpringBootTest
@RunWith(SpringRunner.class) 
public class MyBatisTest {

    @Autowired(required = false)
    public UserMapper userMapper;

    @Test
    public void test(){
        //Wrapper条件构造器，设置为null表示不以任何条件进行查询
        System.out.println("userMapper的使用：" + userMapper.selectList(null));
    }
}
~~~

### 通用CRUD接口

~~~java
//BaseMapper<T> 这个接口提供了大量基础的有关对数据库crud操作的方法
//泛型参数T 则是说明这个自定义的mapper是对数据库中哪个能映射成泛型参数T类型的表进行的具体操作
//自定义的mapper接口继承了BaseMapper就能使用它定义的方法，也就直接拥有了crud能力
@Mapper
public interface UserMapper extends BaseMapper<User> {}

//IService接口是所有service的父类，其内封装了大量基础的业务处理所需要调用的与数据交互的方法
public interface UserService extends IService<User> {}

//ServiceImpl<M extends BaseMapper<T>, T>是IService<T>的实现类，里面有IService定义的方法
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper,User> implements UserService {}
~~~

- 继承通用mapper接口

~~~java
@SpringBootTest
@RunWith(SpringRunner.class) //在测试中如果使用到容器内的bean一定要添加此注解
public class MyBatisTest {

    @Autowired(required = false)
    public UserMapper userMapper;

    @Test
    public void test(){
        //Wrapper条件构造器，设置为null表示不以任何条件进行查询
        System.out.println("userMapper的使用：" + userMapper.selectList(null));
    }

    @Test
    public void testInsert(){
        //mybatis-plus生成的sql语句
        //INSERT INTO user ( id, name, age, email ) VALUES ( ?, ?, ?, ? )
        User user = new User();
        user.setAge(16);
        user.setName("test");
        user.setEmail("test@qq.com");
        int result = userMapper.insert(user);
        System.out.println("影响行数：" + result );
        System.out.println(user.getId());
    }

    @Test
    public void testDelete(){
        //根据id进行删除
        //DELETE FROM user WHERE id=?
//        int result = userMapper.deleteById(1537694705980129281L);

        //根据map集合中所设置的条件进行删除
        // DELETE FROM user WHERE name = ? AND age = ?
//        Map<String,Object> map = new HashMap<>();
//        map.put("name","test");
//        map.put("age",16);
//        int result = userMapper.deleteByMap(map);

        //根据id进行批量删除
        //DELETE FROM user WHERE id IN ( ? , ? , ? )
        List<Long> idList = Arrays.asList(1L, 2L, 3L);
        int result = userMapper.deleteBatchIds(idList);
        System.out.println("result:" + result);
    }

    @Test
    public void testUpdate(){
        //根据id进行修改
        //UPDATE user SET name=?, email=? WHERE id=?
        User user = new User();
        user.setId(4L);
        user.setEmail("user1@qq.com");
        user.setName("user1");
        int result = userMapper.updateById(user);
        System.out.println("result:" + result);
    }

    @Test
    public void testSelect(){
        //根据id查询用户信息
        //SELECT id,name,age,email FROM user WHERE id=?
//        User user = userMapper.selectById(4L);

        //根据id进行批量查询用户信息
        //SELECT id,name,age,email FROM user WHERE id IN ( ? , ? , ? )
//        List<Long> idList = Arrays.asList(4L, 5L, 6L);
//        List<User> users = userMapper.selectBatchIds(idList);

        //根据map集合的信息进行查询
        //SELECT id,name,age,email FROM user WHERE name = ? AND age = ?
        Map<String,Object> map = new HashMap<>();
        map.put("name","user1");
        map.put("age","21");
//        List<User> users = userMapper.selectByMap(map);

        //根据条件进行查询，为null表示无条件查询
        //SELECT id,name,age,email FROM user
//        List<User> users = userMapper.selectList(null);
        
        Map<String, Object> users = userMapper.selectByIdUserMap(4L);
        System.out.println("用户信息：" + users);
    }
}
~~~

- 继承通用Iservice接口

~~~java
@SpringBootTest
@RunWith(SpringRunner.class)
public class MyBatisPlusServiceTest {

    @Autowired
    private UserService userService;


    @Test
    public void testGetCount(){
        //查询总记录数
        //SELECT COUNT( * ) FROM user
        long count = userService.count();
        System.out.println("总记录数:" + count);
    }

    @Test
    public void testInsertMore(){
        //批量添加多个数据
        //实际上还是通过多次执行 INSERT INTO user ( id, name, age ) VALUES ( ?, ?, ? )
        List<User> users = new ArrayList<>();
        for (int i = 0; i < 6; i++) {
            User user = new User();
            user.setName("test" + i);
            user.setAge(15+i);
            users.add(user);
        }
        boolean result = userService.saveBatch(users);
        System.out.println("是否成功:" + result);
    }
}
~~~

---

### 常用注解

#### @TableName

情况一：当出现实体类和数据库的表名不一致的情况，

解决方法：

​	①可以在实体类上使用@TableName注解

~~~java
@Data//简化实体类的开发
@TableName(value = "t_user")//设置实体类对应的表名
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
~~~

​	②在配置文件中使用全局配置所有表的前缀

~~~ym
#配置mybatis-plus的全局配置
 global-config:
   db-config:
     table-prefix: t_
~~~

####  @TableId

- 主键字段和实体类属性不一致问题

情况二：当属性和字段都是uid，而mybatisplus默认使用id作为主键字段时			

解决方法：使用@TableId进行标注，标注此注解的属性所对应的字段作为数据表的主键

情况三：当属性是id，但对应的主键字段不是id的时候

解决方法：使用@TableId的value属性进行主键字段设置

情况四：当**mybatisplus主键字段为空则默认使用雪花算法**时，想要修改主键字段为自动递增

解决方法：使用@TableId的table属性IdType.AUTO进行设置，<font color='red'>**前提是数据库表的主键字段必须已经设置为自增**</font>

~~~java
//@TableId将属性所对应额字段指定为主键
//@TableId注解的value属性的用于指定主键的字段
//@TableId注解的table属性的用于设置主键生成策略
@TableId(value = "uid",type = IdType.AUTO)
   private Long id;
~~~

~~~yml
  # 设置全局统一的主键生成策略
  global-config:
      db-config:
        id-type: auto
~~~

#### @TableField

- 普通字段和实体类属性不一致问题

情况一：实体类属性采用大驼峰命名方式，数据库表采用下划线命名方式

解决方法：在mybatis中可以在核心配置文件中设置驼峰转换，而在mybatis-plus中也有默认的转换设置

情况二：实体类属性和数据表的字段完全不符合

解决方法：使用@TableField注解指定属性所对应的字段名

~~~java
@TableField("user_name") //指定属性所对应的字段名
    private String name;
~~~

#### @TableLogic

物理删除：真实删除，即将对应的数据真正的从数据库中进行删除，之后数据库无法查询此数据

逻辑删除：假删除，即将对应的数据代表是否为删除的字段的状态修改为被删除的状态，之后在数据库仍然可以查询到此数据

使用场景：进行数据的恢复

~~~java
@TableLogic  //表示此属性对应的是逻辑删除的字段
   private Integer isDeleted;
~~~

---

### 条件构造器

Wrapper ： **条件构造抽象类**，最顶端父类 

- AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件 

- - QueryWrapper ： 查询条件封装 
  - UpdateWrapper ： Update 条件封装 
  - AbstractLambdaWrapper ： 使用Lambda 语法 

- - - LambdaQueryWrapper ：用于Lambda语法使用的查询Wrapper 
    - LambdaUpdateWrapper ： Lambda 更新封装Wrapper

![](https://s1.ax1x.com/2022/07/07/j0DYx1.png)

 

~~~Java
@SpringBootTest
@RunWith(SpringRunner.class)
public class MyBatisPlusWrapperTest {

    @Autowired(required = false)
    private UserMapper userMapper;

    @Test
    public void test01(){
        //查询用户名包含a，且年龄在20-30之间，邮箱信息不为null的用户信息
        //SELECT id,name,age,email,is_deleted FROM user WHERE is_deleted=0
        // AND (email IS NOT NULL AND age BETWEEN ? AND ? AND name LIKE ?)
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.isNotNull("email")
                .between("age",20,30)
                .like("name","a");
        userMapper.selectList(userQueryWrapper);
    }

    @Test
    public void test02(){
        //查询用户信息，按照年龄的降低排序，若年龄相同，则按照id升序排序
        //SELECT id,name,age,email,is_deleted FROM user WHERE is_deleted=0 ORDER BY age DESC,id ASC
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.orderByDesc("age").orderByAsc("id");
        userMapper.selectList(userQueryWrapper);
    }

    @Test
    public void test03(){
        //删除邮箱信息为null的客户信息
        //UPDATE user SET is_deleted=1 WHERE is_deleted=0 AND (email IS NULL)
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.isNull("email");
        int result = userMapper.delete(userQueryWrapper);
        System.out.println("删除结果：" + result);
    }

    @Test
    public void test04(){
        //将(年龄大于20且用户包含有a)或邮箱为null的用户信息修改
        //UPDATE user SET name=?, email=? WHERE is_deleted=0
        // AND (age > ? AND name LIKE ? OR email IS NULL)
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.gt("age",20)
                         .like("name","a")
                         .or()
                         .isNull("email");
        User user = new User();
        user.setName("test00");
        user.setEmail("test00@qq.com");
        System.out.println(userMapper.update(user, userQueryWrapper));
    }

    @Test
    public void test05() {
        //将用户包含有a且(年龄大于20或邮箱为null)的用户信息修改
        //lambda中的条件有更高的优先级
        //UPDATE user SET name=?, email=? WHERE is_deleted=0
        // AND (name LIKE ? AND (email IS NULL OR age > ?))
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.like("name","a")
                .and(i -> i.isNull("email").or().gt("age",20));
        User user = new User();
        user.setName("test11");
        user.setEmail("test11@qq.com");
        System.out.println(userMapper.update(user, userQueryWrapper));
    }

    @Test
    public void test06() {
        //查询指定的字段
        //查询用户的年龄、名字、邮箱的信息
        //SELECT name,age,email FROM user WHERE is_deleted=0
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.select("name","age","email");
        List<Map<String, Object>> users = userMapper.selectMaps(userQueryWrapper);
        users.forEach(System.out :: println);
    }

    @Test
    public void test07() {
        //实现子查询
        //查询id小于等于30的用户信息
        //SELECT id,name,age,email,is_deleted FROM user WHERE is_deleted=0
        //AND (id IN (select id from user where id <= 30))
        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.inSql("id","select id from user where id <= 30");
        List<User> users = userMapper.selectList(userQueryWrapper);
        users.forEach(System.out :: println);

    }

    @Test
    public void test08() {
        //将用户包含有a且(年龄大于20或邮箱为null)的用户信息修改
        //UPDATE user SET name=?,email=? WHERE is_deleted=0
        //AND (name LIKE ? AND (age > ? OR email IS NULL))
        UpdateWrapper<User> userUpdateWrapper = new UpdateWrapper<>();
        userUpdateWrapper.like("name","a")
                            .and(i -> i.gt("age",20).or().isNull("email"));
        userUpdateWrapper.set("name","test22").set("email","test22@qq.com");
        userMapper.update(null,userUpdateWrapper);
    }

    @Test
    public void test09() {
        //模拟模糊查询
        //SELECT id,name,age,email,is_deleted FROM user WHERE is_deleted=0
        // AND (age >= ? AND age <= ?)
        String userName = "";
        Integer ageBegin = 20;
        Integer ageEnd = 30;

        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        //判断某个字符串不为null、不为空字符串、不为空白符
        if (StringUtils.isNotBlank(userName)){
            userQueryWrapper.like("name",userName);
        }
        if (ageBegin != null){
            userQueryWrapper.ge("age",20);
        }
        if (ageEnd != null){
            userQueryWrapper.le("age",30);
        }

        List<User> users = userMapper.selectList(userQueryWrapper);
        users.forEach(System.out::println);
    }

    @Test
    public void test10() {
        //模拟模糊查询的优化写法
        //SELECT id,name,age,email,is_deleted FROM user WHERE is_deleted=0
        // AND (age > ? AND age < ?)
        String userName = "";
        Integer ageBegin = 20;
        Integer ageEnd = 30;

        QueryWrapper<User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.like(StringUtils.isNotBlank(userName),"name",userName)
                    .ge(ageBegin != null,"age",ageBegin)
                    .le(ageEnd != null,"age",ageEnd);

        List<User> users = userMapper.selectList(userQueryWrapper);
        users.forEach(System.out::println);
    }

    @Test
    public void test11() {
        //LambdaQueryWrapper可以防止字段名写错
        //SELECT id,name,age,email,is_deleted FROM user WHERE is_deleted=0
        // AND (age >= ? AND age <= ?)
        String userName = "";
        Integer ageBegin = 20;
        Integer ageEnd = 30;
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.like(StringUtils.isNotBlank(userName),User::getName,userName)
                .ge(ageBegin != null,User::getAge,ageBegin)
                .le(ageEnd != null,User::getAge,ageEnd);
        List<User> users = userMapper.selectList(wrapper);
        users.forEach(System.out::println);
    }

    @Test
    public void test12() {
        //LambdaUpdateWrapper可以防止字段名写错
        //将用户包含有a且(年龄大于20或邮箱为null)的用户信息修改
        //UPDATE user SET name=?,email=? WHERE is_deleted=0
        // AND (name LIKE ? AND (age > ? OR email IS NULL))
        LambdaUpdateWrapper<User> wrapper = new LambdaUpdateWrapper<>();
        wrapper.like(User::getName,"a")
                .and(i -> i.gt(User::getAge,20).or().isNull(User::getEmail));
        wrapper.set(User::getName,"test22").set(User::getEmail,"test22@qq.com");
        userMapper.update(null,wrapper);
    }
}

~~~

### 插件

#### 分页插件

①必须通过配置类 + @Bean注册分页的拦截器

②分页功能的模拟

~~~java
//①必须通过配置类 + @Bean注册分页的拦截器
@Configuration
//扫描mapper接口所在的包
@MapperScan("top.year21.mybatisplus.mapper")
public class MyBatisPlusConfig {

    /**
     * Description : 注册MyBatisPlus的分页插件
     * @date 2022/6/17
     * @return com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor
     **/
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        //拦截器
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();

        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));

        return interceptor;
    }

}

//②分页功能的模拟
	@Test
    public void testPage(){
        Page<User> page = new Page<>(1,2);

        //SELECT id,name,age,email,is_deleted FROM user WHERE is_deleted=0 LIMIT ?
         userMapper.selectPage(page, null);

        //所有数据都封装在了这个page对象中
        System.out.println(page);

    }

~~~

- 自定义分页插件

~~~java
public interface UserMapper extends BaseMapper<User> {

    Map<String,Object> selectByIdUserMap(Long id);

    /**
     * Description : 通过年龄查询用户信息并分页
     * @date 2022/6/17
     * @param page MyBatisPlus提供的参数对象，必须位于第一个参数的位置
     * @param age 用户年龄
     * @return com.baomidou.mybatisplus.extension.plugins.pagination.Page<top.year21.mybatisplus.entity.User>
     **/
    Page<User> selectPageCustomzied(@Param("page") Page<User> page, @Param("age")Integer age);

}
~~~

#### 乐观锁插件

使用乐观锁插件的步骤：

①在某个配置类中注册乐观锁插件 

~~~java
@Configuration
//扫描mapper接口所在的包
@MapperScan("top.year21.mybatisplus.mapper")
public class MyBatisPlusConfig {

    /**
     * Description : 注册MyBatisPlus的分页插件
     **/
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
        //拦截器
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        //添加分页插件
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        //添加乐观锁插件
        interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());

        return interceptor;
    }
~~~

②在实体类的属性对应的字段上添加@Version注解

~~~java
@Version // 标识乐观锁版本号字段
        private Integer version;
~~~

③模拟冲突过程，并用乐观锁插件解决

~~~java
@Test
    public void test02(){

        //小李查询商品价格
        Product productLi = productMapper.selectById(1L);
        System.out.println("小李查询商品价格" + productLi.getPrice());

        //小王查询商品价格
        Product productWang = productMapper.selectById(1L);
        System.out.println("小王查询商品价格" + productWang.getPrice());

        //小李将商品价格添加50
        productLi.setPrice(productLi.getPrice() + 50);
        productMapper.updateById(productLi);

        //小李将商品价格减少30
        productWang.setPrice(productWang.getPrice() - 30 );
        int result = productMapper.updateById(productWang);
        if (result == 0){
            //操作失败
            Product productWang2 = productMapper.selectById(1L);

            productWang2.setPrice(productWang2.getPrice() - 30);

            productMapper.updateById(productWang2);
        }

        //老板查询商品
        Product productBoss = productMapper.selectById(1L);
        System.out.println("老板查询商品价格" + productBoss.getPrice());
    }
~~~

### 通用枚举

使用场景：需要将某个枚举类的对象放入数据库。例如男女的性别在数据库中以1或2进行表示

使用教程：①在yml的配置文件中开始通用枚举的包的扫描

~~~yml
  #扫描通用枚举的包
  type-enums-package: top.year21.mybatisplus.enums
~~~

②在实体类的属性上使用@EnumValue注解

~~~java
public enum  SexEnum {
    MALE(1,"男"),
    FEMALE(2,"女");

    @EnumValue //将注解所标识的属性的值存储到数据库中
    private Integer sex;

    private String sexName;

    SexEnum(Integer sex, String sexName) {
        this.sex = sex;
        this.sexName = sexName;
    }

    public Integer getSex() {
        return sex;
    }

    public String getSexName() {
        return sexName;
    }
}
~~~

### 代码生成器

①引入依赖

~~~xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.5.1</version>
</dependency>
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.31</version>
</dependency>
~~~

②测试

~~~java
public class FastAutoGeneratorTest {
    public static void main(String[] args) {
        // 设置我们需要创建在哪的路径
//        String path = "/Users/luxiaogen/Documents/RoadTo2w/Java/尚硅谷/MyBatisPlus-2022/demo";
        // 这里我是mysql8 5版本可以换成 jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&useSSL=false
        FastAutoGenerator.create("jdbc:mysql://localhost:3306/mybatis_plus?serverTimezone=GMT%2B8&characterEncoding=utf-8&useSSL=false", "root", "root")
                .globalConfig(builder -> {
                    builder.author("year21") // 设置作者
                            // .enableSwagger() // 开启 swagger 模式
                            .fileOverride() // 覆盖已生成文件
                            .outputDir("D://mybatisplus"); // 指定输出目录
                })
                .packageConfig(builder -> {
                    builder.parent("top.year21") // 设置父包名
                            .moduleName("mybatisplus") // 设置父包模块名
                            .pathInfo(Collections.singletonMap(OutputFile.mapperXml, "D://mybatisplus")); // 设置mapperXml生成路径
                })
                .strategyConfig(builder -> {
                    builder.addInclude("user") // 设置需要生成的表名
                            .addTablePrefix("t_", "c_"); // 设置过滤表前缀
                }).templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker 引擎模板，默认的是Velocity引擎模板
                .execute();
    }
}

~~~

### 多数据源

应用场景：一个业务涉及多个不同库中的数据表时如何处理

1.使用多数据源必须引入依赖

~~~xml
		<!--多数据源依赖-->
		<dependency>
			<groupId>com.baomidou</groupId>
			<artifactId>dynamic-datasource-spring-boot-starter</artifactId>
			<version>3.5.0</version>
		</dependency>
~~~

2.配置多数据源

~~~yml
spring:
  datasource:
    # 配置数据源信息 datasource:
    dynamic:
      # 设置默认的数据源或者数据源组,默认值即为master
      primary: master
      # 严格匹配数据源,默认false.true未匹配到指定数据源时抛异常,false使用默认数据源
      strict: false
      datasource:
        master:
          url: jdbc:mysql://localhost:3306/mybatis_plus?serverTimezone=GMT%2B8&characterEncoding=utf-8&useSSL=false
          driver-class-name: com.mysql.cj.jdbc.Driver
          username: root
          password: 'root'
        slave_1:
          # 我的数据库是8.0.27 5版本的可以使用jdbc:mysql://localhost:3306/mybatis_plus?characterEncoding=utf-8&useSSL=false
          url: jdbc:mysql://localhost:3306/mybatis_plus_1?serverTimezone=GMT%2B8&characterEncoding=utf-8&useSSL=false
          driver-class-name: com.mysql.cj.jdbc.Driver
          username: root
          password: 'root'
~~~

3.测试

~~~java
@DS("slave_1")
@Service
public class ProductServiceImpl extends ServiceImpl<ProductMapper, Product> implements ProductService {
}

@DS("master") //标记操作的是配置信息中数据源为master的配置
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
}
~~~

### MyBatisX插件

下载：可以从IDEA的插件市场进行下载，快速将mapper接口和mapper接口的映射文件形成关联

作用：①代码生成器，通过指定数据库的查看数据表生成对应的service层、mapper接口以及mapper接口的映射文件等，

​			但仅仅使用单表生成

​			②以及快速生成CRUD的sql查询语句

使用教程：[mybatis_plus](https://www.yuque.com/docs/share/0112fd84-de5f-433b-996a-b9d9cd87dd36?#Nz285)
