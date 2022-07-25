---
title: SpringBoot+MyBatis+BootStrap3.0前后端不分离项目
date: 2022-07-25 22:53:30
updated: 2022-07-25 22:53:30
tags:
  - SpringBoot
  - BootStrap
  - MyBatis
categories:
  - 个人项目
keywords: SpringBoot BootStrap
cover: https://w.wallhaven.cc/full/x8/wallhaven-x8rgz3.png
copyright_author: Year21
copyright_url: http://year21.top/2022/07/25/ComputerStore
---

# 电脑商城

- 项目介绍：基于SpringBoot+Mybatis+BootStrap3.0前后端不分离的项目

- 项目功能 ：

  ①登录、注册、集成kaptcha验证码防止机器人，以及用户管理(用户信息的修改)  

  ②分页处理展示商品信息、支持通过模糊搜索查找相关商品

  ③购物车相关功能管理(如购物车商品的展示和结算) 

  ④订单管理页面可以查看不同状态订单以及集成了支付宝沙箱模拟商品支付全过程

  ⑤收藏界面同样实现分页展示用户收藏商品信息，以及加入收藏、取消收藏和加入购物车

  ⑥对全部业务方法使用aop拦截计算业务执行时间，为后续优化提供支持


## 环境搭建

基本环境：

1.jdk 1.8

2.maven 3.6.2

3.数据库 mysql 8.0.26

4.集成开发环境 IDEA2020

## 用户管理

### 创建数据库

~~~mysql
CREATE DATABASE IF NOT EXISTS `computer_store` CHARACTER SET 'utf8';
~~~

### 创建数据表

~~~mysql
CREATE TABLE t_user (
	uid INT AUTO_INCREMENT COMMENT '用户id',
	username VARCHAR(20) NOT NULL UNIQUE COMMENT '用户名',
	password CHAR(32) NOT NULL COMMENT '密码',
	salt CHAR(36) COMMENT '盐值',
	phone VARCHAR(20) COMMENT '电话号码',
	email VARCHAR(30) COMMENT '电子邮箱',
	gender INT COMMENT '性别:0-女，1-男',
	avatar VARCHAR(50) COMMENT '头像',
	is_delete INT COMMENT '是否删除：0-未删除，1-已删除',
	created_user VARCHAR(20) COMMENT '日志-创建人',
	created_time DATETIME COMMENT '日志-创建时间',
	modified_user VARCHAR(20) COMMENT '日志-最后修改执行人',
	modified_time DATETIME COMMENT '日志-最后修改时间',
	PRIMARY KEY (uid)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
~~~

- **考虑到每个表中都有固定的四个字段，重复写过于麻烦，因此可以使用一个Java基类来对应这四个字段**

### 创建实体类

~~~java
//用于与数据表四个字段形成映射关系的基类
@Data
public class BaseEntity implements Serializable {//为了便于数据传输，实现序列化接口
    private String createdUser;
    private Date createdTime;
    private String modifiedUser;
    private Date modifiedTime;
}

//对应数据表的User实体类
@Data
public class User extends BaseEntity {
    private Integer uid;
    private String username;
    private String password;
    private String salt; //用于加密密码
    private String phone;
    private String email;
    private Integer gender;//'性别:0-女，1-男',
    private String avatar;
    private Integer isDelete;
}
~~~

---

### 注册 

#### 后端-持久层

- **通过MyBatis框架与数据库进行交互**

1.编写sql语句

~~~mysql
#增加用户的sql语句
insert into t_user(username,password) vaules(?,?);

#判断用户名是否已存在的语句
select * from t_user where username = ?;
~~~

2.定义mapper接口和抽象方法

由于项目可能有多个mapper接口，因此在项目的目录结构下创建一个mapper的包，用于管理所有的mapper接口，

并在springboot启动类上使用@MapperScan注解扫描此包下的所有mapper接口或直接在接口上使用@Mapper注解。

以便在项目启动的时间把mapper扫描进容器中，交由spring容器管理。

~~~java
//User实体类对应的mapper接口
public interface UserMapper {
    //用户注册
    int addUser(User user);

    //根据用户名查询用户信息
    User queryUserByUsername(String username);
}
~~~

3.编写映射文件

项目可能有多个mapper映射文件，必须在项目目录结构resource文件下创建一个mapper的包。

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="top.year21.computerstore.mapper.UserMapper">
    <!-- int addUser(@Param("user") User user); -->
    <insert id="addUser" >
        insert into t_user(
                        username,password,salt,phone,email,gender,avatar,is_delete,
                        created_user,created_time,modified_user,modified_time
                        )
                        values (
                        #{username},#{password},#{salt},#{phone},#{email},
                        #{gender},#{avatar},#{isDelete},
                        #{createdUser},#{createdTime},#{modifiedUser},#{modifiedTime}
                                )
    </insert>

    <!-- 询的结果集字段和实体类的user属性名不一致，自定义查询结果集的映射规则   -->
    <resultMap id="queryUser" type="user">
        <id property="uid" column="uid"/>
        <result property="username" column="username"/>
        <result property="password" column="password"/>
        <result property="salt" column="salt"/>
        <result property="phone" column="phone"/>
        <result property="gender" column="gender"/>
        <result property="avatar" column="avatar"/>
        <result property="isDelete" column="is_delete"/>
        <result property="createdUser" column="created_user"/>
        <result property="createdTime" column="created_time"/>
        <result property="modifiedUser" column="modified_user"/>
        <result property="modifiedTime" column="modified_time"/>
    </resultMap>

    <!--   User queryUserByUsername(@Param("username") String username); -->
    <!--  用于查询的结果集字段和实体类的user属性名不一致，所以不能使用resultType，必须使用resultMap  -->
    <select id="queryUserByUsername" resultMap="queryUser">
            select * from t_user  where username = #{username}
    </select>

</mapper>
~~~

4.将mapper映射文件的位置在yml配置文件中进行对应的设置

~~~yml
mybatis:
  mapper-locations: classpath:mybatis/mapper/*.xml
~~~

5.进行单元测试（每个 的完成建议都进行单元测试）

#### 后端-业务层

业务层的包下主要有exception、impl、接口，其中exception放置各种异常处理类，impl中放置接口的实现类

1.规划异常处理机制

考虑到在后端处理业务的过程中，会出现各种异常情况，如执行过程中数据库宕机、用户名重复等。

虽然java在异常处理机制已经很完善，以上的情况都是抛出RuntimeException异常，对定位异常不够明确。

因此在业务层的制定中，需要考虑对异常的定义处理。

在业务层制定一个继承RuntimeException异常的异常类，再让具体的异常继承这个异常。

~~~java
//专用于处理业务层的异常基类
//e.g. throws new ServiceException("业务层出现异常")
public class ServiceException extends RuntimeException{
    public ServiceException() {
        super();
    }

    public ServiceException(String message) {
        super(message);
    }

    public ServiceException(String message, Throwable cause) {
        super(message, cause);
    }

    public ServiceException(Throwable cause) {
        super(cause);
    }

    protected ServiceException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace) {
        super(message, cause, enableSuppression, writableStackTrace);
    }
}
~~~

> 根据业务层不同的 来详细定义具体异常的类型，统一的继承ServiceException异常基类

~~~java
//表示用户名重复的异常
public class UsernameDuplicateException extends ServiceException{

    public UsernameDuplicateException() {
    }

    public UsernameDuplicateException(String message) {
        super(message);
    }

    public UsernameDuplicateException(String message, Throwable cause) {
        super(message, cause);
    }

    public UsernameDuplicateException(Throwable cause) {
        super(cause);
    }

    public UsernameDuplicateException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace) {
        super(message, cause, enableSuppression, writableStackTrace);
    }
}

//专用于处理业务层其他未知异常，如数据插入过程中服务器、数据库宕机的情况
public class InsertException extends ServiceException {}

//表示验证码错误的异常
public class ValidCodeNotMatchException extends RuntimeException {}
~~~

2.定义业务层接口和抽象方法

~~~java
// 处理用户注册的业务层接口
public interface IUserService {
    //处理用户注册
    void UserRegister(User user);
}
~~~

3.定义接口的实现类，处理请求

- 为什么要补全这五个字段？因为注册界面只让填写了用户名和密码，在经过查询数据库用户不重复后，

  如果直接执行插入操作，那么这五个字段在数据库中就是空值，因此必须在注册时补全

~~~java
//处理用户注册的业务层接口的实现类
@Service
public class IUserServiceImpl implements IUserService {

    @Autowired(required = false)
    private UserMapper userMapper;

	//处理用户注册
    @Override
    public void UserRegister(User user) {
        //需要先判断用户名是否在数据库中重复
        User queryUser = userMapper.queryUserByUsername(user.getUsername());

        //重复的情况下，抛出用户名重复异常
        if (queryUser != null){
            throw new UsernameDuplicateException("用户名已被注册");
        }

        //密码不能以明文方式存入数据库，需要进行加密操作
        //密码加密的实现： 盐值 + password + 盐值 ---> md5算法进行加密，连续加载三次 ---> 得到最终存入数据库的结果
        //盐值就是一个随机的字符串
        //记录旧密码
        String oldPassword = user.getPassword();
        //使用UUID获取时间戳创建盐值
        String salt = UUID.randomUUID().toString().toUpperCase();

        //记录此刻的盐值，用于以后做用户登录判断
        user.setSalt(salt);

        //进行加密操作
        String md5Password = PasswordEncryptedUtils.getPasswordByMD5(oldPassword, salt);

        //将加密后的密码设置为用户设置的密码
        user.setPassword(md5Password);

        //在执行插入操作之前对一些表字段进行补全
        //is_delete字段设置为0，表示未删除用户
        user.setIsDelete(0);

        //补全其余四个日志信息的字段
        Date currentTime = new Date();
        user.setCreatedUser(user.getUsername());
        user.setCreatedTime(currentTime);
        user.setModifiedUser(user.getUsername());
        user.setModifiedTime(currentTime);

        //不重复，调用插入方法,处理业务
        int result = userMapper.addUser(user);

        if (result == 0){ //判断服务器或数据库执行是否出现异常
            throw new InsertException("处理用户注册过程中，服务器或数据库执行出现异常");
        }
    }
}
~~~

4.业务层进行单元测试

#### 后端-控制层

1.设置返回响应信息给前端的基类

~~~java
//响应数据给前端
@Data
public class JsonResult<E> {
    //响应状态码 200-成功 4000-用户名重复 5000-数据库或服务器异常
    private int code;
    //响应信息
    private String message;
    //响应数据
    private E data;

    public JsonResult() {
    }

    public JsonResult(int code) {
        this.code = code;
    }

    public JsonResult(Throwable e) {
        this.message = e.getMessage();
    }

    public JsonResult(int code, E data) {
        this.code = code;
        this.data = data;
    }
}
~~~

2.设计请求

请求路径：/user

请求参数：User user,HttpSession session,String code

请求类型：post

响应结果：JsonResult< Void>

3.controller的设计

①创建一个BaseController全局处理自定义的异常

~~~java
//全局处理异常
public class BaseController {
    //操作成功的状态码
    public static final int OK = 200;

    /**
     * 1.当出现了value内的异常之一，就会将下方的方法作为新的控制器方法进行执行
     *   因此该方法的返回值也同时是返回给前端的页面
     * 2.此外还自动将异常对象传递到此方法的参数列表中，这里使用Throwable e来接收
     **/
    @ExceptionHandler(ServiceException.class) //统一处理抛出的异常
    public JsonResult<Void> handleException(Throwable e){
        JsonResult<Void> result = new JsonResult<>(e);
        if (e instanceof UsernameDuplicateException){
            result.setStatus(4000); //表示用户名重复
            result.setMessage(e.getMessage());
        }else if (e instanceof InsertException){
            result.setStatus(5000); //数据库或服务器有问题
            result.setMessage(e.getMessage());
        }
        //返回异常处理结果
        return result;
    }
}
~~~

②创建一个UserController处理请求

UserController继承了BaseControlle也就间接用于了BaseControlle的属性和方法

~~~java
//处理用户请求的控制器
@RestController
@RequestMapping("/user")
public class UserController extends BaseController{

    @Autowired
    private IUserService userService;

    //用户注册
    @PostMapping
    public JsonResult<Void> userRegister(User user,HttpSession session,String code) {
        //从session取出验证码
        String validCode = (String) session.getAttribute(Constants.KAPTCHA_SESSION_KEY);
		//判断验证码是否正确
        if (!validCode.equals(code)){
            throw new ValidCodeNotMatchException("验证码错误,请重试！");
        }
        //执行插入操作
        userService.userRegister(user);
        return new JsonResult<>(OK);
    }
}
~~~

4.使用postman进行接口的测试

#### 前端页面

- 前端页面只需要将表单信息通过ajax异步向后端服务器发起请求即可

给用户注册按钮绑定点击事件

~~~javascript
<script type="text/javascript">
    //在网页加载完成后执行
    $(function () {
    //引入页脚的公共页面
    $(".footer").load("components/footer.html")
    //检测信息
    checkInfoAndSendAjax();
    //给返回主页绑定点击事件
    $("#btn-toIndex").click(function () {
        location.href = "index.html"
    })
})
//验证信息和发送ajax注册用户请求
function checkInfoAndSendAjax() {
    //给用户注册绑定点击事件
    $("#btn-reg").click(function () {
        let name = $("#username").val();
        let pwd = $("#password").val();
        let rePwd = $("#rePwd").val();
        let codeStr = $("#code").val();
        //去掉验证码前后空格
        codeStr = $.trim(codeStr);
        if (name == "" || pwd == "" || rePwd == "" ||codeStr == ""){
            $("#error-msg").text("请先填写需要注册的信息！");
            return false;
        }
        //验证用户名是否符合规则
        let nameCheck = /^\w{5,12}$/;
        let username = $("#username").val();

        if (!(nameCheck.test(username))){
            $("#error-msg").text("用户名必须是5-12位的字母和数字");
            return false;
        }else {
            $("#error-msg").empty()
        }

        //验证密码是否符合规则
        let passCheck = /^\w{5,12}$/;
        let password = $("#password").val();
        if (!passCheck.test(password)){
            $("#error-msg").text("密码必须是5-12位的字母和数字");
            return false;
        }else {
            $("#error-msg").empty()
        }

        //验证确认密码和密码是否相同
        let rePass = $("#rePwd").val();
        if (rePass !== password){
            $("#error-msg").text("密码不一致");
            return false;
        }else {
            $("#error-msg").empty()
        }

        $.ajax({
            url : "http://localhost:8080/user",
            type: "post",
            data: $("#form-reg").serialize(), //获取表单的所有内容
            dataType: "json",
            success: function (res) {
                if (res.status === 200){
                    alert("注册成功！")
                    location.href = "login.html"
                }else {
                    $("#error-msg").html(res.message)
                }
            },
            error: function (error) {
                alert(error.status + "错误,服务器出现故障，请等待攻城狮修复！！")
            }
        });
    })
}
</script>
~~~

---

### 登录 

#### 后端-持久层

- 持久层可以利用上面写好的sql语句判断用户是否存在即可，密码的校验 交由业务层进行处理

1.sql语句用上面的

2.mapper接口用上面的

3.mapper接口的映射文件用上面的

4.单元测试

#### 后端-业务层

1.规划异常，创建两个自定义的异常处理类继承ServiceException

~~~java
// 表示用户名不存在的异常
public class UserNotExistException extends ServiceException {}
//表示密码错误的异常
public class WrongPasswordException extends ServiceException {}
~~~

2.编写接口和抽象方法以及实现类内具体的业务处理流程

~~~java
//IUserService
//处理用户登录
User userLogin(User user);

//IUserServiceImpl 具体的业务处理用户登录
   public User userLogin(User user) {
        //用户名
        String username = user.getUsername();
        //用户输入的密码
        String userPassword = user.getPassword();

        //查询登录用户是否在数据库中存在
        User loginUser = userMapper.queryUserByUsername(username);

        if (loginUser == null){ //为空代表用户名不存在
            throw new UserNotExistException("用户数据不存在");
        }

        //取得数据库查询返回用户的盐值和密码以及删除状态
        String salt = loginUser.getSalt();
        String databasePwd = loginUser.getPassword();
        Integer deleteStatus = loginUser.getIsDelete();

        //对用户输入的密码进行加密
        String md5PasswordBy = PasswordEncryptedUtils.getPasswordByMD5(userPassword, salt);

        //将加密后的字符和数据库查询的MD5进行校验
        if (!databasePwd.equals(md5PasswordBy)){
            throw new WrongPasswordException("密码不正确");
        }

        //判断登录的用户账户是否已注销
        if (deleteStatus == 1){
            throw new UserNotExistException("用户数据不存在");
        }
        
        //密码正确返回查询的用户信息
        return loginUser;
    }
~~~

3.进行单元测试

#### 后端-控制层

1.在全局异常处理机制中添加对业务层异常的处理

2.设计请求

请求路径：/user

请求参数：User user, HttpSession session,String code

请求类型：get

响应结果：JsonResult< User>

3.处理请求

~~~java
//控制层调用业务层接口的方法
@GetMapping
public JsonResult<User> userLogin(User user, HttpSession session){
   //将存储在session的kaptcha所生成的验证码取出
        String validCode = (String) session.getAttribute(Constants.KAPTCHA_SESSION_KEY);
        //判断验证码是否一致
        if (!validCode.equals(code)){
            throw new ValidCodeNotMatchException("验证码错误,请重试！");
        }
        //执行登录操作
        User loginUser = userService.userLogin(user);
        //分别将用户的session保存到服务端
        session.setAttribute("uid",loginUser.getUid());
        session.setAttribute("username",loginUser.getUsername());
        //优化一下传回前端的user数据，有些字段是不需要的。
        //只将用户名和uid进行回传
        User newUser = new User();
        newUser.setUsername(loginUser.getUsername());
        newUser.setUid(loginUser.getUid());
        newUser.setGender(loginUser.getGender());
        newUser.setPhone(loginUser.getPhone());
        newUser.setEmail(loginUser.getEmail());
        newUser.setAvatar(loginUser.getAvatar());
        return new JsonResult<>(OK,newUser);
}
~~~

4.控制层单元测试

#### 前端页面

登录成功以及登录成功后需要做的事情：

- 与上面注册登录一样的逻辑，这里不浪费空间了

  唯一一个不同的地方是需要在登录成功后跳转至首页

  <font color='red'>**window.location.href = “xxx.html” 这个直接跳转到指定页面**</font>

- 保存用户信息到session域中

  - ①保存在前端的会话窗口中，供前端页面使用

~~~javascript
//将用户信息存入session域中
sessionStorage.setItem("user",JSON.stringify(res.data));
~~~

  - ②保存在工程项目的session中，供整个工程使用

    **session对象主要存在服务器端，可以用于保存服务器的临时数据的对象，也可用于拦截器的拦截请求**

~~~java
public class BaseController {
    /**
         * Description : 从session中获取用户uid
         * @param session springboot启动时生成的session对象
         **/
    public final Integer getUserIdFromSession(HttpSession session){
        String uidStr = session.getAttribute("uid").toString();
        return Integer.valueOf(uidStr);
    }

	//从session中获取用户username
    public final String getUsernameFromSession(HttpSession session){
        return session.getAttribute("username").toString();
    }
}
~~~

<font color='red'>**拦截器**</font>，对每个访问的页面进行拦截判断，没有登录则重定向至登录页面


①在interceptor包下自定义拦截器类，实现HandleInterceptor接口，实现此接口的方法


~~~java
// 自定义的拦截器类，拦截所有请求进行判断
public class LoginInterceptor implements HandlerInterceptor {
    /**
           * Description : 检测全局session对象中是否由uid数据，有则放行，没有则拦截
           * @param request 请求对象
           * @param response 响应对象
           * @param handler 处理器（url+Controller：映射）
           **/
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //获取项目工程的session
        HttpSession session = request.getSession();

        if (session.getAttribute("uid") != null){ //说明此时已登录
            return true;
        }else{ //说明未登录
            //重定向至登录页面
            response.sendRedirect("/web/login.html");
            return false;
        }
    }
}
~~~

②在config包下创建自定义配置类，实现WebMvcConfigurer接口，将拦截器添加到容器中

​    指定拦截规则【如果是拦截所有，静态资源也会被拦截，所以要指定白名单和黑名单】

<font color='red'>   **拦截器也要放行接口的请求，不然就报错**</font>

~~~java
// 网页拦截器，判读用户是否已经登录
@Configuration
public class LoginConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //注册拦截器并添加拦截规则
        registry.addInterceptor(new LoginInterceptor())
            .addPathPatterns("/**")
            //也可以用一个List<String> 来设置排除拦截的资源
            //拦截器也要放行接口的请求，不然就报错
            .excludePathPatterns("/web/login.html","/web/index.html",
                                    "/web/register.html","/web/product.html",
                                    "/web/components/**","/web/search.html",
                                    "/user/**","/address/**","/file/**","/district/**",
                                    "/product/**","/cart/**","/order/**","/kaptcha/**",
                                    "/css/**","/bootstrap3/**",
                                    "/images/**","/js/**");
    }
}
~~~

---

### 修改密码 

#### 后端-持久层

1.编写sql语句

~~~sql
update t_user set password = #{password} where uid = #{uid};
~~~

2.定义mapper接口的抽象方法

~~~java
int updatePassword(String password,String uid);
~~~

3.编写mapper接口的映射文件

~~~xml
<!--    int updatePassword(@Param("password") String password,@Param("uid") String uid);-->
<update id="updatePassword">
    update t_user set password = #{password} where uid = #{uid};
</update>
~~~

4.单元测试

#### 后端-业务层

1.异常处理，创建一个表示密码不匹配的异常，比如原密码不对

2.定义Userservice的抽象方法

3.编写实现类实现接口方法的业务处理逻辑

~~~java
//1.表示原密码不正确的异常
public class OriginalPasswordNotMatchException extends ServiceException{}

//2.Userservice的抽象方法
public interface IUserService {
    //处理用户修改密码
    void userResetPwd(String oldPwd,String newPwd, HttpSession session);
}

//3.编写实现类实现接口方法的业务处理逻辑
/**
     * Description : 处理用户修改密码
     * @date 2022/7/11
     * @param originalPassword 用户的原密码
     * @param newPassword 用户要修改的新密码
     * @param session 项目启动springboot提供的session对象，用于获取用户的uid
     **/
@Override
public void userResetPwd(String originalPassword,String newPassword, HttpSession session) {

    //从session中获取用户名
    String username = session.getAttribute("username").toString();

    //从数据库查询对应的信息
    User user = userMapper.queryUserByUsername(username);

    //取得数据库查询返回用户的盐值和密码以及删除状态
    String salt = user.getSalt();
    String databasePwd = user.getPassword();

    //对用户输入的密码进行加密
    String md5Password = PasswordEncryptedUtils.getPasswordByMD5(originalPassword, salt);

    //先判断原密码是否正确，将加密后的密码和数据库查询的MD5进行校验
    if (!databasePwd.equals(md5Password)){
        throw new OriginalPasswordNotMatchException("原密码不正确");
    }

    //将新密码进行加密
    String newMD5Pwd = PasswordEncryptedUtils.getPasswordByMD5(newPassword, salt);

    //先获取现在的时间
    Date currentTime = new Date();

    //更新密码
    int result = userMapper.updatePassword(newMD5Pwd,user.getUsername(),currentTime,username);

    if (result == 0){
        throw new InsertException("数据库或服务器故障，密码修改失败");
    }
}
~~~

4.单元测试

#### 后端-控制层

1.在全局异常处理机制中添加对业务层异常的处理

2.设计请求

请求路径 /user

请求参数 String oldPwd，String newPwd，Httpsession session

请求类型 post

响应类型 JsonResult< Void>

3.处理请求

~~~java
@PutMapping
public JsonResult<Void> userResetPwd(@RequestParam("oldPassword") String oldPwd,
                                     @RequestParam("newPassword") String newPwd,
                                     HttpSession session){
    userService.userResetPwd(oldPwd, newPwd, session);
    return new JsonResult<>(OK);
}
~~~

4.单元测试

#### 前端页面

- 与上面用户登录大致的逻辑，这里不浪费空间了，代码放在web/password.html页面下

- 补充一点：在用户修改密码后，利用location.href = “login.html”

  以及在后端删除session中存储的uid值实现让用户重新登录

---

### 个人资料 

#### 后端-持久层

1.编写sql语句

- 需要更新phone、email、gender、modified_user ,modified_time这五个字段

~~~sql
//根据uid主键查询用户信息
select * from t_user where uid = #{uid}

//更新的sql语句
update t_user set phone = #{phone},
                  email = #{email},
                  gender = #{gender},
				  modified_user = #{modifiedUser},
				  modified_time = #{modifiedTime}
			  where uid = #{uid}
~~~

2.定义mapper接口抽象方法

~~~java
User queryUserByUid(@Param("uid") Integer uid);

int UpdateUserInfo(String phone,
                   String email,
                   Integer gender,
                   String modifiedUser,
                   Date modifiedTime,
                   Integer uid);
~~~

3.编写映射文件

~~~xml
<!--    User queryUserByUid(@Param("uid") Integer uid);-->
<select id="queryUserByUid" resultMap="queryUser">
    select * from t_user where uid = #{uid}
</select>

<!--    int UpdateUserInfo(****)-->
<update id="UpdateUserInfo">
    update t_user set
    <if test="phone != null and phone != ''">
        phone = #{phone},
    </if>
    <if test="email != null and email != ''">
        email = #{email},
    </if>
    <if test="gender != null and gender != ''">
        gender = #{gender},
    </if>
    modified_user = #{modifiedUser},
    modified_time = #{modifiedTime}
    where uid = #{uid}
</update>
~~~

4.单元测试

#### 后端-业务层

1.异常控制，好像没什么新异常

2.定义service层接口的抽象方法

3.实现抽象方法，编写业务处理逻辑

~~~java
//抽象方法
void userUpdateInfo(String phone,String email,
                    Integer gender,String username,
                    Integer uid);
//根据id查询用户信息
User queryUserByUid(Integer uid); 

//实现抽象方法，编写业务处理逻辑
@Override
public User userUpdateInfo(String phone, String email, Integer gender,String username,Integer uid) {
    //获取用户id并判断用户是否存在
    User user = userMapper.queryUserByUid(uid);

    if (user == null | user.getIsDelete() == 1){
        throw new UserNotExistException("用户数据不存在");
    }

    //修改用户信息
    int result = userMapper.UpdateUserInfo(phone, email, gender, username, new Date(), uid);

    if (result == 0){
        throw new InsertException("数据库或服务器异常，个人资料修改失败");
    }
}
~~~

4.单元测试

#### 后端-控制层

1.无异常，不需要处理

2.设计请求

请求路径 /user/updateInfo

请求参数 String phone,String email,Integer gender,HttpSession session

请求类型 post

响应类型 JsonResult< User>

3.处理请求

- 考虑到前端信息及时更新的问题，因此这里选择将更新后的User进行回传

~~~java
@GetMapping("/queryUser")
public JsonResult<User> queryUserByUid(HttpSession session){
    Integer uid = getUserIdFromSession(session);

    User user = userService.queryUserByUid(uid);

    //将用户名、id、电话、邮箱、性别进行回传
    User newUser = new User();
    newUser.setUsername(user.getUsername());
    newUser.setUid(user.getUid());
    newUser.setGender(user.getGender());
    newUser.setPhone(user.getPhone());
    newUser.setEmail(user.getEmail());
    newUser.setAvatar(user.getAvatar());

    return new JsonResult<>(OK,newUser);
}

@PutMapping("/updateInfo")
public JsonResult<User> userInfoUpdate(String phone,String email,Integer gender,HttpSession session){
    //从session中取出用户名和uid
    String username = getUsernameFromSession(session);
    Integer id = getUserIdFromSession(session);

    //更新数据
    User user = userService.userUpdateInfo(phone, email, gender, username, id);

    //将用户名、id、电话、邮箱、性别进行回传
    User newUser = new User();
    newUser.setUsername(user.getUsername());
    newUser.setUid(user.getUid());
    newUser.setGender(user.getGender());
    newUser.setPhone(user.getPhone());
    newUser.setEmail(user.getEmail());
    return new JsonResult<>(OK,newUser);
}
~~~

#### 前端页面

①第一个ajax请求在用户信息页面加载完成后自动发送，并根据返回值通过js的id选择器

​	找到对应的元素并修改其属性值

②第二个ajax请求在用户点击修改按钮之后先提示是否修改，再根据其选择进行处理，

​	同理根据结果利用js的id选择器找到对应的元素并修改其属性值

~~~javascript
<!--	js代码	-->
<script type="text/javascript">

    //在网页加载完成后执行
    $(function () {

    //在页面完成之后自动发送ajax请求
    $.ajax({
        url : "http://localhost:8080/user/queryUser" ,
        type: "get",
        dataType: "json",
        success: function (res) {
            if (res.status === 200){
                $("#username").val(res.data.username) //修改用户名
                $("#phone").val(res.data.phone) //修改电话
                $("#email").val(res.data.email) //修改邮箱

                //修改性别
                if (res.data.gender === 0){
                    //prop()表示给某个元素添加属性和属性值
                    $("#gender-female").prop("checked","checked")
                }else{
                    $("#gender-male").prop("checked","checked")
                }
            }
        },
        error: function (error) {
            alert("服务器出现故障，请等待攻城狮修复！！")
        }
    });

    //给用户注册绑定点击事件
    $("#btn-change-info").click(function () {
        //根据用户选择状态决定是否发生ajax请求
        if (confirm("确定要修改吗？")){
            $.ajax({
                url : "http://localhost:8080/user/updateInfo",
                type: "put",
                data: $("#form-change-info").serialize(),//获取表单的所有内容
                dataType: "json",
                success: function (res) {
                    if (res.status === 200){
                        alert("修改成功！")
                        //将回传的用户信息进行更新
                        sessionStorage.setItem("user",JSON.stringify(res.data))
                        //网页刷新
                        location.reload();
                    }else {
                        alert(res.message)
                    }
                },
                error: function (error) {
                    alert("服务器出现故障，请等待攻城狮修复！！")
                }
            });
        }
        else {
            return false; //阻止表单的默认提交行为
        }
    })
})
</script>
~~~

---

### 头像上传 

#### 后端-持久层

1.编写sql语句

对应的avatar字段存其保存地址

~~~xml
update t_user set avatar = #{avatar},modified_user = #{modifiedUser},
                          modified_time = #{modifiedTime} where uid = #{uid};
~~~

2.定义mapper接口的抽象方法

~~~java
int updateUserAvatar(@Param("file") String ImgAddress,
                     String modifiedUser,
                     Date modifiedTime,
                     Integer uid);
~~~

3.编写mapper接口的映射文件

~~~xml
<!--    int updateUserAvatar(@Param("file") String ImgAddress);-->
<update id="updateUserAvatar">
    update t_user set avatar = #{avatar} where uid = #{uid};
</update>
~~~

4.单元测试

#### 后端-业务层

1.异常规划 例如用户数据不存在，服务器宕机等

2.定义service层接口抽象方法

3.实现类重写抽象方法，编写业务处理逻辑

~~~java
//2.处理用户上传图片的抽象方法
void userUploadImg(String imgAddress,Integer uid);

//3.具体业务处理逻辑
@Override
public void userUploadImg(String imgAddress, Integer uid) {
    User user = userMapper.queryUserByUid(uid);
    if (user == null){
        throw new UserNotExistException("用户数据不存在");
    }
    //插入图片
    int result  = userMapper.updateUserAvatar(imgAddress, user.getUsername(),new Date(),uid);

    if (result == 0){
        throw new InsertException("图片在服务器或数据库更新过程中出现错误，上传失败！");
    }

}
~~~

4.单元测试

#### 后端-控制层

1.处理控制层和业务层异常，**将控制层异常加入全局异常处理@ExceptionHandler的值中**

- 考虑到控制层接口前端数据也有可能出现异常，因此控制层也要进行异常控制

  创建一个FileUploadException继承RunTimeException，其余异常继承此异常

  ①文件为空异常

  ②文件大小超出限制异常

  ③文件状态异常

  ④文件类型不符异常

  ⑤文件读取IO异常

2.设计请求

请求路径：/file

请求参数：MultipartFile file，Httpsession session

请求类型：post

响应类型：JsonResult< Void>

3.处理请求

①创建一个Controller专门处理文件的上传和下载

②将文件的下载地址保存到数据库

~~~java
//处理文件上传和下载的Controller
@RestController
@RequestMapping("/file")
public class FileController extends BaseController{
    @Autowired
    private IUserService userService;

    @Value("${server.port}")
    private String port;

    //设置文件的上传限制
    @Value("${spring.servlet.multipart.max-file-size}")
    private Integer fileMaxSize;

    //设置上传文件的类型
    private static final List<String> FILE_TYPE = new ArrayList<>();

    static {
        FILE_TYPE.add("images/jpg");
        FILE_TYPE.add("images/jpeg");
        FILE_TYPE.add("images/png");
        FILE_TYPE.add("images/bmp");
        FILE_TYPE.add("images/gif");
    }

    @PostMapping
    public JsonResult<Void> userAvatarUpload(MultipartFile file,
                                             HttpSession session){

        //在保存文件之前对文件做检查
        //判断文件是否为空
        if (file.isEmpty()){
            throw new FileEmptyException("上传文件为空，上传失败");
        }

        //判断文件是否超过限制
        if (file.getSize() > fileMaxSize){
            throw new FileSizeException("文件过大，上传失败");
        }

        //判断上传的文件是否为图片类型 file.getContentType()获取的是这种形式 --> text/html
        if (!FILE_TYPE.contains(file.getContentType())){
            throw new FileTypeNotMatchException("文件类型不符，上传失败");
        }

        //获取上传文件的原始名字
        String originalFilename = file.getOriginalFilename();

        //获取文件的后缀名
        String suffix = originalFilename.substring(originalFilename.lastIndexOf("."));

        //使用时间戳为文件定义新的名字
        String uuidName = UUID.randomUUID().toString();

        //定义文件最终的名字
        String fileName = uuidName + suffix;

        //获取项目在服务器上的真实位置并拼凑文件最终的保存位置
        String realPath = System.getProperty("user.dir") + "/src/main/resources/static/images/img/" + fileName;

        /**
         * 这里其实有两个选择：①直接将文件写入硬盘 这里选择这种
         * ②先获取存储文件的文件夹的路径，判断存储文件的文件夹是否存在，不存在则创建
         * 最后再将文件名和文件夹路径进行拼凑出最终文件的保存路径
         **/
        //虚拟创建目标文件
        File destFile = new File(realPath);

        //获取目标文件的上级目录
        File parentFile = destFile.getParentFile();

        if (!parentFile.exists()){
            //代表文件的上级目录不存在，进行创建
            parentFile.mkdirs();
        }

        //选择第一种方法，直接写入目标位置
        try {
            file.transferTo(destFile);
        }catch (FileStatusException e) {
            throw new FileStatusException("文件状态异常，写入失败");
        }catch (IOException e) {
            throw new FileUploadIOException("服务器或数据库写入文件失败");
        }

        //获取uid值
        Integer uid = getUserIdFromSession(session);

        //将文件的下载路径写入数据库
        String fileAccessPath = "http://localhost:" + port + "/file/down/" + fileName;

        userService.userUploadImg(fileAccessPath,uid);

        return new JsonResult<>(OK);
    }

    @GetMapping("/down/{name}")
    public ResponseEntity<byte[]> fileUpload(@PathVariable("name") String fileName) throws IOException {
        //读取文件
        String downFilePath = System.getProperty("user.dir") + "/src/main/resources/static/images/img/" + fileName;

        if (downFilePath != null){
            //创建一个输入流读入需要下载的文件
            FileInputStream inputStream = new FileInputStream(new File(downFilePath));

            //创建一个和文件所需字节大小一致的byte字节数组
            byte[] fileBytes = new byte[inputStream.available()];

            //将读入的流写入字节数据
            inputStream.read(fileBytes);

            //创建HttpHeaders对象设置响应头信息
            HttpHeaders headers = new HttpHeaders();

            //设置要下载方式以及下载文件的名字
            //Content-Disposition 通知客户端以下载的方式接受数据
            //attachment;filename= 设置下载的文件的名字
            headers.add("Content-Disposition", "attachment;filename=" + URLEncoder.encode(fileName,"UTF-8"));

            //设置响应状态码
            HttpStatus statusCode = HttpStatus.OK;

            //创建ResponseEntity对象
            ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(fileBytes, headers, statusCode);

            //关闭输入流
            inputStream.close();
            //将需要下载的文件以字节数组的方式响应出去
            return responseEntity;
        }
        return null;
    }
}
~~~

4.单元测试

#### 前端页面

①在页面加载完成之前自动发送第一个ajax请求。

②这里使用form表单提交图像，但form表单提交会自动跳转，因此需要阻止submit的跳转行为

​	引入js文件,在form表单添加onsubmit属性以及利用js插件解决

~~~javascript
<!--	引入阻止表单上传跳转的js-->
    <script src="../js/jquery-1.8.0.min.js"></script>
    <script src="../js/jquery.form.js"></script>
    <!--js代码	-->
    <script type="text/javascript">
    function afterFromSubmit(){
    //阻止表单的默认跳转行为，并在ajax请求后进行重定向
    $("#avatar").ajaxSubmit(function () {
        alert("上传成功")
            //页面重定向，防止表单重复提交
            location.href = "upload.html"
    })
        //这一步很关键，防止submit默认的提交跳转行为
        return false;
}

$(function () {
    //网页加载完成之前自动发送ajax请求
    $.ajax({
        url: "http://localhost:8080/user/queryUser",
        type: "get",
        dataType: "json",
        success:function (res) {
            //判断用户是首次注册还是老用户
            if (res.data.avatar !== null &&res.data.avatar !== ""){
                //设置用户头像
                $("#img-avatar").attr("src",res.data.avatar)
            }else{
                //设置为默认头像
                $("#img-avatar").attr("src","../images/index/user.jpg")
            }
        },
    })
})
    </script>
~~~

---

## 地址管理

- 鉴于地址管理  有点多，开发顺序参考：

  新增收货地址管理、显示用户地址信息、设为默认地址、修改、删除

### 创建数据表

~~~sql
CREATE TABLE t_address (
    aid INT AUTO_INCREMENT COMMENT '收货地址id',
    uid INT COMMENT '归属的用户id',
    NAME VARCHAR(20) COMMENT '收货人姓名',
    province_name VARCHAR(15) COMMENT '省-名称',
    province_code CHAR(6) COMMENT '省-行政代号',
    city_name VARCHAR(15) COMMENT '市-名称',
    city_code CHAR(6) COMMENT '市-行政代号',
    area_name VARCHAR(15) COMMENT '区-名称',
    area_code CHAR(6) COMMENT '区-行政代号',
    zip CHAR(6) COMMENT '邮政编码',
    address VARCHAR(50) COMMENT '详细地址',
    phone VARCHAR(20) COMMENT '手机',
    tel VARCHAR(20) COMMENT '固话',
    tag VARCHAR(6) COMMENT '标签',
    is_default INT COMMENT '是否默认：0-不默认，1-默认',
    created_user VARCHAR(20) COMMENT '创建人',
    created_time DATETIME COMMENT '创建时间',
    modified_user VARCHAR(20) COMMENT '修改人',
    modified_time DATETIME COMMENT '修改时间',
    PRIMARY KEY (aid)
) ENGINE=INNODB DEFAULT CHARSET=utf8;
~~~

### 创建实体类

~~~java
// 对应数据表t_address的实体类
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Address extends BaseEntity{
    private Integer aid;
    private Integer uid;
    private String name;
    private String provinceName;
    private String provinceCode;
    private String cityName;
    private String cityCode;
    private String areaName;
    private String areaCode;
    private String zip;
    private String address;
    private String phone ;
    private String tel;
    private String tag;
    private Integer isDefault;
}
~~~

---

### 新增地址 

#### 后端-持久层

1.编写sql语句

~~~sql
//插入数据 除了此表的主键不需要数据，其他都要
insert into t_address(uid,name,province_name,province_code,city_name,city_code,area_name,
					area_code,zip,address,phone,tel,tag,is_default,created_user,created_time,
                    modified_user,modified_time)
                    values(#{uid},#{name},#{proviceName},#{provinceCode},#{cityName},
                    		#{cityCode},#{areaName},#{areaCode},#{zip},#{address},#{phone},
                        	#{tel},#{tag},#{isDefault},#{createdUser},#{createdTime},
                        	#{modifiedUser},#{modifiedTime}
                    		)
	
//判断用户数据条目是否超过限制
select count(*) from t_address where uid = #{uid}
~~~

2.定义抽象接口和抽象方法

~~~java
//Address实体类对应的mapper接口
public interface AddressMapper {
    void addAddress(Address address);
    int userAddressCount(Integer aid);
}
~~~

3.编写mapper接口对应的映射文件

4.单元测试

#### 后端-业务层

1.异常规划 用户地址条目数超出限制的异常

2.定义service接口和抽象方法

~~~java
public interface IAddressService {
    //添加地址业务抽象方法
    void addAddress(Address address);
}
~~~

3.编写具体的业务处理逻辑

- **需要注意，如果用户此时添加的地址是第一条，需要将其设置为其账户的默认地址**

~~~java
@Service
public class IAddressServiceImpl implements IAddressService {
    @Autowired
    private AddressMapper addressMapper;
    //添加地址业务具体逻辑
    @Override
    public void addAddress(Address address) {
        //先判断用户在数据库中的地址记录数
        int count = addressMapper.userAddressCount(address.getUid());
        //查询是否超过记录限制
        if (count >= 20){
            throw new AddressCountLimitException("地址数量已达上限，请先删除部分地址！");
        }
        //当查询记录为0，则将此地址设置为用户默认的地址
        if (count == 0){
            address.setIsDefault(1);
        }
        //插入数据
        int result = addressMapper.addAddress(address);
        if (result == 0){
            throw new InsertException("新增地址失败，服务器或数据库异常！");
        }
    }
}
~~~

4.单元测试

#### 后端-控制层

1.处理异常，将异常添加到全局统一管理

2.设计请求

请求路径：/address

请求参数：Address address,HttpSession session

请求类型：post

响应类型：JsonResult< Void>

3.处理请求，创建对应的controller

~~~java
@RestController
@RequestMapping("/address")
public class AddressController extends BaseController{
    @Autowired
    private IAddressService addressService;
    //处理用户新增地址
    @PostMapping
    public JsonResult<Void> addAddress(Address address, HttpSession session){
        //从session中取出uid
        Integer uid = getUserIdFromSession(session);
        //将取出的uid设为当前地址对象的的uid
        address.setUid(uid);
        //调用业务层方法，新增地址
        addressService.addAddress(address);
        return new JsonResult<>(OK);
    }
}
~~~

#### 前端页面

~~~javascript
<script type="text/javascript">
    $(function () {
    $("#btn-add-new-address").click(function () {
        $.ajax({
            url : "http://localhost:8080/address",
            type: "post",
            data: $("#form-add-new-address").serialize(), //获取表单的所有内容
            dataType: "json",
            success: function (res) {
                if (res.status === 200){
                    alert("新增成功！")
                    location.href = "address.html"
                }else {
                    alert(res.message)
                }
            },
            error: function () {
                alert("服务器出现故障，请等待攻城狮修复！！")
            }
        })
    })
})
</script>
~~~

---

### 获取省市区列表 

#### 创建数据表

~~~sql
//parent属性代表的是父区域的代码号，省的父代码号是+86
CREATE TABLE t_dict_district (
    id int(11) NOT NULL AUTO_INCREMENT,
    parent varchar(6) DEFAULT NULL,
    code varchar(6) DEFAULT NULL,
    name varchar(16) DEFAULT NULL,
    PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
~~~

#### 创建实体类

~~~java
@Data
public class District {
    private Integer id;
    private String parent;
    private String code;
    private String name;
}
~~~

#### 后端-持久层

1.编写sql语句

~~~sql
//根据父代号查询相关信息
select * from t_dict_district where parent = #{parent} order by code ASC

//根据code查询对应的省市区名字
select name from t_dict_district where code = #{code}
~~~

2.定义mapper接口和抽象方法

~~~java
//根据父代号查询区域信息
List<District> queryDistrictByParent(String parent);
//根据code查询当前省市区的名称
String queryDistrictByCode(String code);
~~~

3.编写映射文件

4.单元测试

#### 后端-业务层

1.规划异常 ,暂无异常

2.定义service接口和抽象方法

3.编写具体的业务处理逻辑

- 此外，还需要在address业务层需要调用District业务层为它的address对象填充空白字段

  因为provinceName、cityName、areaName的值需要通过前端传过来的option标签的

  value值(这些option标签是通过这个业务层查询后append到select标签中的)进行查询

  才能得到，因此还需要在address业务层调用这个业务层接口

  <font color='red'>**select标签中的option标签的value的值是前端往后端传的参数**</font>

- 而在这之前添加关于地址的字段全空，code为名字就是因为select中没有选项

~~~java
@Service
public class IDistrictServiceImpl implements IDistrictService {
    @Autowired
    private DistrictMapper districtMapper;
    //根据父代号查询省市区信息
    @Override
    public List<District> getSpecifyDistrictByParent(String parent) {
        List<District> districts = districtMapper.queryDistrictByParent(parent);

        //过滤无效字段数据，提高传输效率
        for (District ad: districts) {
            ad.setId(null);
            ad.setParent(null);
        }
        //返回数据
        return districts;
    }
    //根据code查询省市区名字
    @Override
    public String getNameByCode(String code) {
        return districtMapper.queryDistrictByCode(code);
    }
}
~~~

4.单元测试

#### 后端-控制层

1.无异常，不用处理

2.设计请求

请求路径：/district

请求参数：String parent

请求类型：get

响应类型：JsonResult<List< District>>

3.处理请求,创建一个新的控制类

~~~java
//处理父代号查询省市区的请求
@GetMapping
public JsonResult<List<District>> queryDistrictByParent(String parent){
    //查询数据
    List<District> list = districtService.getSpecifyDistrictByParent(parent);
    //返回数据
    return new JsonResult<>(OK,list);
}
~~~

4.单元测试

#### 前端页面

- 前端页面js代码过长，需要到/web/addAddress.html的script标签下看

---

### 获取用户地址 

#### 后端-持久层

1.编写sql语句

~~~sql
select * from t_address where uid = #{uid} order by is_default desc,created_time desc
~~~

2.定义抽象方法

~~~java
//查询用户所有地址记录
List<Address> queryUserAddress(Integer uid);
~~~

3.编写映射文件

- 定义结果结映射规则，将通过select查询出来的结果放入List当中

4.单元测试

#### 后端-业务层

1.异常控制，貌似没新异常

2.定义业务层抽象方法

3.编写具体的业务处理逻辑

~~~java
//添加地址查询的抽象方法
List<Address> queryUser(Integer uid);
//地址查询的具体逻辑
@Override
public List<Address> queryUser(Integer uid) {
    //先判断用户信息是否存在
    User user = userService.queryUserByUid(uid);

    if (user == null | user.getIsDelete() == 1){
        throw new UserNotExistException("用户信息不存在");
    }
    //返回查询的用户地址信息
    return addressMapper.queryUserAddress(uid);
}
~~~

4.单元测试

#### 后端-控制层

1.异常处理

2.设计请求

请求地址：/address

请求参数：HttpSession session

请求类型：get

响应类型：JsonResult<List< Address>>

3.处理请求

- **控制层需要把前端发送过来的Address其余空字段补全**

~~~java
//处理用户查询收货地址
@GetMapping
public JsonResult<List> queryAllAddress(HttpSession session){
    //获取uid
    Integer uid = getUserIdFromSession(session);
    //查询数据
    List<Address> list = addressService.queryUser(uid);
    //返回数据
    return new JsonResult<>(OK,list);
}
~~~

4.单元测试

#### 前端页面

- 在页面加载完成时，自动发送ajax请求查询用户收货地址，拿到返回数据之后

  判断返回数据的j集合中是否有信息，没有信息将表格内容设置为提示信息，

  有内容则通过js选择器找到显示表格的标签，通过append()将数据逐个插入到表格的标签内

  并将修改、删除、设为默认的三个 绑定单击事件

~~~javascript
<script type="text/javascript">
$(function () {
    //页面加载完成自动发送ajax请求查询用户地址
    $.ajax({
        url: "http://localhost:8080/address",
        type: "get",
        success: function (res) {
            if (res.data.length !== 0){
                for (i = 0;i < res.data.length;i++){
                    //挨个获取返回的集合中的元素
                    var address = res.data[i];
                    //拼凑标签和信息
                    str = "<tr>"
                        + "<td>"+ address.tag + "</td>"
                        + "<td>"+ address.name + "</td>"
                        + "<td>"
                        + address.provinceCode + address.cityCode + address.areaCode + address.address
                        + "</td>"
                        + "<td>" + address.phone + "</td>"
                        + "<td>"
                        + "<a href='javascript:void(0);' onclick='updateAddress()' class='btn btn-xs btn-info'>"
                        + "<span class='fa fa-edit'></span>修改"
                        + "</a>"
                        + "</td>"
                        + "<td>"
                        + "<a href='javascript:void(0);' onclick='deleteAddress()' class='btn btn-xs add-del btn-info'>"
                        + "<span class='fa fa-trash-o'></span>删除"
                        + "</a>"
                        + "</td>"
                        + "<td>"
                        + "<a href='javascript:void(0);' onclick='setDefault()' class='btn btn-xs add-def btn-default'>设为默认</a>"
                        + "</td>"
                        + "</tr>"
                    //将数据逐个插入到表格的标签内
                    $("#address-list").append(str)
                }
            }else {
                var text = "暂无收货地址，请先添加收货地址"
                $("#address-list").text(text)
            }
        }
    })
})
</script>
~~~

---

### 设置默认地址 

#### 后端-持久层

1.编写sql语句

~~~sql
//判断需要设置为默认地址的数据是否存在
select * from t_address where aid = #{aid}

//将所有地址设置为非默认值
update t_address set is_default = 0 where uid = #{uid} and is_delete = 0

//更新地址的默认值
update t_address set is_default = 1,
                     modified_user = #{modifiedUser},
					 modified_time = #{modifiedTime}
                     where aid = #{aid}
~~~

2.定义抽象方法

~~~java
// 根据地址aid查询某条地址
Address queryUserAddressByAid(Integer aid);
// 根据用户uid将其关联的地址设置为非默认值
int setAllAddressNotDefault(Integer uid);
//根据地址aid将某条地址设为默认值
int setOneAddressDefault(Integer aid,String modifiedUser, Date modifiedTime);
~~~

3.编写mapper映射文件

#### 后端-业务层

1.异常规划，数据不存在

2.定义service层抽象方法

3.编写具体的业务处理逻辑

~~~java
//查询某条地址的抽象方法
Address queryAddressByAid(Integer aid);

//设置用户所有地址为非默认地址的抽象方法
int setNotDefaultAddress(Integer uid);

//设置某个地址为默认地址的抽象方法
int setOneAddressDefault(Integer aid,String modifiedUser, Date modifiedTime);

//根据aid查询地址的具体逻辑
@Override
public int queryAddressByAid(Integer aid) {
   return addressMapper.queryUserAddressByAid(aid);
}
//设置用户所有地址为非默认的具体逻辑
@Override
public int setNotDefaultAddress(Integer uid) {
    return addressMapper.setAllAddressNotDefault(uid);
}
//设置某个地址为默认地址的具体逻辑
@Override
public int setOneAddressDefault(Integer aid,String modifiedUser, Date modifiedTime) {
    return addressMapper.setOneAddressDefault(aid,modifiedUser,modifiedTime);
}
~~~

#### 后端-控制层

1.将异常加入全局处理

2.设计请求

请求路径：/address/setAddress

请求参数：Integer aid,HttpSession session

请求类型：post

响应类型：JsonResult< void>

3.处理请求

~~~java
@PostMapping("/setAddress")
public JsonResult<Void> setUserDefaultAddress(Integer aid,HttpSession session){
    //查询要修改的地址是否存在
    Address address = addressService.queryAddressByAid(aid);

    if (address == null){
        throw new AddressNotExistsException("该地址不存在，设置失败");
    }
    //从session中取出用户的uid和名字
    Integer uid = getUserIdFromSession(session);
    String modifiedUser = getUsernameFromSession(session);

    Date date = new Date();

    //将该用户的所有地址设为非默认值
    addressService.setNotDefaultAddress(uid);

    //将需要修改的地址设为默认值
    addressService.setOneAddressDefault(aid,modifiedUser,date);

    return new JsonResult<>(OK);
}
~~~

4.单元测试

#### 前端页面

- <font color='red'>**由于是直接通过拼接元素实现了数据展示，对于id的获取选择使用正则表达式进行替换**</font>

  使用正则表达式替换获取该地址的aid值，#{aid}只是一个占位符的含义，没其他含义

  str = str.replace("#{aid}",address.aid)

~~~javascript
//为设为默认地址绑定点击事件
function setDefault(aid){
    alert(aid)
    if (confirm("确定要这条收货地址设为默认地址吗？")){
        $.ajax({
            url: "http://localhost:8080/address/setAddress",
            type: "post",
            data: "aid=" + aid,
            dataType: "json",
            success:function (res) {
                alert("设置成功")
                location.reload();
            },
            error:function (error) {
                alert(error.message)
            }
        })
    }
}
~~~

---

### 删除地址 

#### 后端-持久层

1.sql语句的编写

~~~sql
update t_address set is_delete = 1，
					modified_user = #{modifiedUser},
					 modified_time = #{modifiedTime}
					 where aid = #{aid})
~~~

2.定义mapper抽象方法

~~~java
int deleteAddressByAid(Integer aid,String modifiedUser, Date modifiedTime);
~~~

3.编写mapper映射文件

4.单元测试

#### 后端-业务层

1.异常控制，貌似没有

2.定义抽象方法

- 与上方设置默认地址类上

3.编写具体的处理逻辑

- 与上方设置默认地址类上

4.单元测试

#### 后端-控制层

1.异常处理，貌似没有

2.设计请求

- 与上方设置默认地址类上

3.处理请求

- 与上方设置默认地址类上

4.单元测试

#### 前端页面

- 删除地址要考虑这条删除的地址是否为默认地址，

  如果是默认地址不允许删除，提示修改其他地址才能删除

js代码与上方的设置默认地址相似，就不浪费空间了

---

### 修改地址 

#### 后端-持久层

1.sql语句的编写

~~~sql
update t_address set(除了uid、创建者和创建时间的字段) where aid = #{aid} and is_delete = 0
~~~

2.定义mapper抽象方法

~~~java
//更新用户地址信息
int updateUserAddressByAid(Address address);
~~~

3.编写mapper映射文件

4.单元测试

#### 后端-业务层

1.异常控制，貌似没有

2.定义抽象方法

3.编写具体的处理逻辑

~~~java
//修改某个指定地址的抽象方法
int updateOneAddress(Address address,String modifiedUser);
//修改指定地址的具体逻辑
@Override
public int updateOneAddress(Address address,String modifiedUser) {

    //获取并设置其他三个地址为null的字段
    String provinceName = districtService.getNameByCode(address.getProvinceCode());
    String cityName = districtService.getNameByCode(address.getCityCode());
    String areaName = districtService.getNameByCode(address.getAreaCode());
    address.setProvinceName(provinceName);
    address.setCityName(cityName);
    address.setAreaName(areaName);
    //补全表单中没有的其他字段
    address.setModifiedUser(modifiedUser);
    address.setModifiedTime(new Date());
    int result = addressMapper.updateUserAddressByAid(address);

    return result;
}
~~~

4.单元测试

#### 后端-控制层

1.异常处理，貌似没有

2.设计请求

请求地址：/address/updateAddress

请求参数：Address address,HttpSession session

请求类型：post

响应类型：JsonResult< void>

3.处理请求

~~~java
@PostMapping("/updateAddress")
public JsonResult<Void> updateOneAddress(Address address,HttpSession session){
    //取出session中用户名
    String modifiedUser = getUsernameFromSession(session);
    int result = addressService.updateOneAddress(address, modifiedUser);
    if (result == 0){
        throw new InsertException("修改地址时，服务器或数据库异常");
    }
    return new JsonResult<>(OK);
}
~~~

4.单元测试

#### 前端页面

①在跳转到这个页面时，自动发送一个ajax请求，根据返回数据填充表单

②在用户修改省份时，城市、市县自动变为请选择城市或区县并根据选择发送对应的ajax请求查询

③在页面加载完成时，用户可根据自由选择城市或区县的单个修改

④修改按钮前端将整个表单以ajax请求进行发送，后端以实体类address接收并处理

⑤恢复原地址信息按钮实际上就是再次调用第一个中的ajax请求

- <font color='red'>**javaScript代码长达200多行，不予展示，需要到项目于的/web/editAddress.html页面查看**</font>

---

## 商品管理

### 创建数据表

~~~sql
CREATE TABLE t_product (
  id int(20) NOT NULL COMMENT '商品id',
  category_id int(20) DEFAULT NULL COMMENT '分类id',
  item_type varchar(100) DEFAULT NULL COMMENT '商品系列',
  title varchar(100) DEFAULT NULL COMMENT '商品标题',
  sell_point varchar(150) DEFAULT NULL COMMENT '商品卖点',
  price bigint(20) DEFAULT NULL COMMENT '商品单价',
  num int(10) DEFAULT NULL COMMENT '库存数量',
  image varchar(500) DEFAULT NULL COMMENT '图片路径',
  status int(1) DEFAULT '1' COMMENT '商品状态  1：上架   2：下架   3：删除',
  priority int(10) DEFAULT NULL COMMENT '显示优先级',
  created_time datetime DEFAULT NULL COMMENT '创建时间',
  modified_time datetime DEFAULT NULL COMMENT '最后修改时间',
  created_user varchar(50) DEFAULT NULL COMMENT '创建人',
  modified_user varchar(50) DEFAULT NULL COMMENT '最后修改人',
  PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
~~~

### 创建实体类

~~~java
//对应数据表t_product的实体类
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Product extends BaseEntity{
    private Integer id;
    private Integer categoryId ;
    private String itemType;
    private String title;
    private String sellPoint;
    private Long price;
    private Integer num;
    private String image;
    private Integer status;
    private String priority;
}
~~~

---

### 热销排行 

#### 后端-持久层

1.sql语句编写

~~~sql
SELECT id,title,price,image FROM t_product  where status = 1 ORDER BY priority DESC LIMIT 0,4
~~~

2.定义mapper接口和抽象方法

~~~java
//查询优先权前五的商品进行展示
List<Product> queryPriorityProduct();
~~~

3.编写mapper映射文件

4.单元测试

#### 后端-业务层

1.规划异常

2.定义service接口和抽象方法

3.编写具体的处理逻辑

~~~java
//查询热销商品的前五项的抽象方法
List<Product> queryPriorityProduct();

@Override
public List<Product> queryPriorityProduct() {
    return productMapper.queryPriorityProduct();
}
~~~

4.单元测试

#### 后端-控制层

1.异常处理，无异常不需要处理

2.设计请求

请求路径：/product

请求参数：无

请求类型：get

响应类型：JsonResult<List< Product>>

3.处理请求

~~~java
//处理热销商品的请求
@GetMapping
public JsonResult<List<Product>> queryBestProduct(){
    //查询对应商品
    List<Product> products = productService.queryPriorityProduct();
    return new JsonResult<>(OK,products);
}
~~~

4.单元测试

#### 前端页面

①在页面加载完成自动发送ajax请求，根据返回信息填充热销排行信息

~~~javascript
//查询热销商品的方法
function showHotProducts(){
    $.ajax({
        url : "http://localhost:8080/product",
        type: "get",
        dataType: "json",
        success: function (res) {
            for (let i = 0; i < res.data.length; i++) {
                let str = "";
                let product = res.data[i]
                let image = "http://localhost:8080" + product.image + "collect.png"
                str = "<div class='col-md-12'>"
                    + "<div class=\"col-md-7 text-row-2\">"
                    + "<a href='#'>" + product.title + "</a>"
                    + "</div>"
                    + "<div class=\"col-md-2\">¥23</div>"
                    + "<div class=\"col-md-3\">"
                    + "<img src=" + image  + " class='img-responsive' />"
                    + "</div>"
                    + "</div>"
                $("#hot-list").append(str)
            }
        },
        error: function () {
            alert("查询错误，请等待攻城狮修复！！")
        }
    })
}
~~~

---

### 最新产品 

- 所有的逻辑跟热销排行 相似在此不再浪费空间书写，只书写部分

~~~sql
//sql语句
SELECT id,title,price,image FROM t_product 
					WHERE category_id = 163 
					and status = 1
					ORDER BY created_time DESC 
					LIMIT 0,4 ;
~~~

---

### 显示商品 

#### 后端-持久层

1.sql语句的编写

~~~sql
select title,sell_point,price,image from t_product where id = #{id}
~~~

2.定义抽象方法

~~~java
//根据指定商品id进行商品查询
Product queryProductById(Integer id);
~~~

3.编写mapper映射文件

4.单元测试

#### 后端-业务层

1.规划异常，商品不存在,商品状态异常

2.定义抽象方法

3.编写具体的逻辑处理方法

4.单元测试

#### 后端-控制层

1.处理异常，将异常加入全局处理

2.设计请求

请求路径：/product/{id}

请求参数：Integet id

请求类型：get

响应类型：JsonResult< Product>

3.处理请求

```java
//处理商品id查询该商品信息的请求
@GetMapping("/{id}")
public JsonResult<Product> queryProductById(@PathVariable("id") Integer id){
    Product product = productService.queryProductById(id);
    return new JsonResult<>(OK,product);
}
```

#### 前端页面

①在页面加载完成自动发送ajax请求，把根据id查询的返回信息填充至页面

~~~javascript
<script type="text/javascript">
    function showInThisProductHtml(){
    //接收上一个页面传来的连接
    var hrefUrl = location.href;
    //以url中的"="为截断点，形成一个数组
    var param = hrefUrl.split("=")
    //decodeURI解码得到想要的参数
    var id = decodeURI(param[1]);

    //在页面加载完成时自动发送此ajax请求并填充表单
    $.ajax({
        url: "http://localhost:8080/product/" + id,
        type: "get",
        dataType: "json",
        success:function (res) {
            let product = res.data;
            //将普通的数据填充至页面
            $("#product-title").text(product.title)
            $("#product-sell-point").append(product.sellPoint)
            $("#product-price").text(product.price)
            $("#stock").text(product.num)
            //将数据库查询的图片进行替换
            let image = ".." +  product.image
            //遍历填充图片数据
            for (let i = 1; i <= 5; i++) {
                $("#product-image-" + i + "-big").attr("src",image + i + "_big.png")
                $("#product-image-" + i).attr("src",image + i + ".jpg")
            }
        }
    })
}
$(function () {
    //在页面加载完成时，填充页面数据
    showInThisProductHtml();
})
</script>
~~~

---

### 立即购买 

- <font color='red'>**后端-持久层 、业务层、控制层大部分东西都在前面的 已经实现了**</font>

1.根据uid查询用户地址，在地址管理的获取收货地址 已经实现

2.根据pid查询商品信息，在显示商品 已经实现

业务层有所修改，添加一个新的方法对应从商品详情页进去确定订单界面的请求

~~~java
//处理根据从商品界面进入创建orderItem订单的具体逻辑
public int insertOrderItemFromProductHtml(Integer oid,Integer pid, Integer num, String username) {
    //根据pid查询商品信息
    Product product = productService.queryProductById(pid);
    //创建一个用于向持久层传输的OrderItem实体类对象
    OrderItem orderItem = new OrderItem();
    //补全orderItem对象的空白字段
    orderItem.setOid(oid);
    orderItem.setPid(pid);
    orderItem.setTitle(product.getTitle());
    orderItem.setImage(product.getImage());
    orderItem.setPrice(product.getPrice());
    orderItem.setNum(num);
    Date createdTime = new Date();
    orderItem.setCreatedUser(username);
    orderItem.setCreatedTime(createdTime);
    orderItem.setModifiedUser(username);
    orderItem.setModifiedTime(createdTime);

    //调用持久层进行插入
    int result = orderMapper.insertOneOrderItem(orderItem);
    if (result == 0){
        throw new InsertException("服务器出现错误，创建订单失败");
    }
    return result;
}
~~~

#### 前端页面

- 没办法，由于两个页面共用同一个创建订单的方法因此必须在前端页面判断是由哪个界面

  进入的确认订单支付界面

~~~javascript
//给在线支付按钮绑定单击事件
$("#btn-create-order").click(function() {
    //获取用户选择的是哪一个地址的value值
    let aid = getSelectOption();
    let totalPrice = JSON.parse($("#all-price").html());
    $.ajax({
        url: "http://localhost:8080/order/createOrder",
        type: "post",
        data: {aid:aid,totalPrice:totalPrice},
        dataType: "json",
        success:function (res) {
            //获取已经创建的order的订单号
            let oid = res.data.oid
            //获取地址栏上的参数
            let urlParam = location.search.substr(1)//截取地址栏url?后的第二个元素
            //判断是否携带了cids，不等于-1代表是从购物车进入这个界面
            if(urlParam.search("cids") !== -1){
                //获取每个cid取创建orderItem数据
                useCidSentAjaxCreateOrderItem(oid);
            }else{ //等于1代表不是从购物车进入这个界面
                //实现商品界面的立即购买创建订单
                fromProductSentAjaxCreateOrderItem(oid);
            }
        },
        error: function (error){
            alert(error.message)
        }
    })
})

//表示这是从商品界面进入的确认订单界面
function fromProductSentAjaxCreateOrderItem(oid){
    $.ajax({
        url: "http://localhost:8080/order/createOrderItem",
        type: "post",
        data: {oid:oid,pid:pid,num:num},
        dataType: "json",
        success:function (res) {
            if (res.status === 200){
                //跳转到指定页面
                location.href = "payment.html?oid=" + oid
            }else {
                alert("创建orderItem订单失败")
            }
        },
        error: function (error){
            alert("服务器内部异常！")
        }
    })
}
~~~







---

## 购物车管理

### 创建数据表

~~~sql
CREATE TABLE t_cart (
    cid INT AUTO_INCREMENT COMMENT '购物车数据id',
    uid INT NOT NULL COMMENT '用户id',
    pid INT NOT NULL COMMENT '商品id',
    price BIGINT COMMENT '加入时商品单价',
    num INT COMMENT '商品数量',
    created_user VARCHAR(20) COMMENT '创建人',
    created_time DATETIME COMMENT '创建时间',
    modified_user VARCHAR(20) COMMENT '修改人',
    modified_time DATETIME COMMENT '修改时间',
    PRIMARY KEY (cid)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
~~~

### 创建实体类

~~~java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Cart extends BaseEntity {
    private Integer cid;
    private Integer uid;
    private Integer pid  ; 
    private Long price;
    private Integer num;
}
~~~

---

### 加入购物车 

#### 后端-持久层

1.编写sql语句

~~~sql
//根据用户uid和商品pid查询某条购物车cid数据
select * from t_cart where uid = #{uid} and pid = #{pid}

//更新cid数据
update t_cart set price = #{price},num = #{num},
				  modified_user = #{modifiedUser},modified_time = #{modifiedTime}
				  where cid = #{cid}
				  
//插入新的cart的数据
insert into t_cart(uid,pid,price,num,created_user,created_time,modified_user,modified_time)
                          values(#{uid},#{pid},#{price},#{num},
                                 #{createdUser},#{createdTime},
                                 #{modifiedUser},#{modifiedTime}
                                )
~~~

2.定义mapper接口和抽象方法

~~~java
//实体类Cart对应的mapper接口
public interface CartMapper {
//根据用户uid和商品pid查询某条购物车cid数据
Cart queryCartByUidAndPid(Integer uid, @Param("pid") Integer pid);
//更新cart数据
int updateCartInfo(Cart cart);
//新增cart数据
int addCart(Cart cart);
}
~~~

3.编写mapper映射文件

4.单元测试

#### 后端-业务层

1.规划异常。没想到异常

2.定义service接口和抽象方法

3.编写具体逻辑

~~~java
//根据uid和pid查询Cart信息的抽象方法
Cart queryCartByUidAndPid(Integer uid,Integer pid);

//更新数据的抽象方法
int UpdateCartInfo(Cart cart,String modifiedUser, Date modifiedTime);

//新增数据的抽象方法
int addCart(Cart cart,String createdUser,Date createdTime,String modifiedUser, Date modifiedTime);

//处理购物车管理接口的实现类
public class ICartServiceImpl implements ICartService {
    @Autowired
    private CartMapper cartMapper;
    //根据uid和aid查询数据的具体逻辑
    @Override
    public Cart queryCartByUidAndPid(Integer uid, Integer pid) {
        return cartMapper.queryCartByUidAndPid(uid,pid);
    }

    //更新cart数据的具体逻辑
    @Override
    public int UpdateCartInfo(Cart cart, String modifiedUser, Date modifiedTime) {
        //更改修改人和修改时间的字段
        cart.setModifiedUser(modifiedUser);
        cart.setModifiedTime(modifiedTime);
        //执行修改
        return cartMapper.updateCartInfo(cart);
    }
    // 新增cart数据的抽象方法
    @Override
    public int addCart(Cart cart,String createdUser,Date createdTime,String modifiedUser, Date modifiedTime) {
        //取得当前商品的pid和用户uid
        Integer pid = cart.getPid();
        Integer uid = cart.getUid();
        //查询当前pid在当前用户的购物车中是否存在
        Cart destCart = cartMapper.queryCartByUidAndPid(uid, pid);

        //根据查询的结果做出不同动作
        if (destCart == null){ //表示不存在，则直接添加
            //补全其他四个字段
            cart.setCreatedUser(createdUser);
            cart.setCreatedTime(createdTime);
            cart.setModifiedUser(modifiedUser);
            cart.setModifiedTime(modifiedTime);
            //执行插入操作
            return cartMapper.addCart(cart);
        }else { //表示该用户已存在该产品的数据
            //取出查询的数据数量
            Integer destNum = destCart.getNum();
            //取出新添加产品的数量
            Integer cartNum = cart.getNum();

            //计算最终的数量
            Integer endNum = destNum + cartNum;
            //并更新需要更新的字段
            destCart.setNum(endNum);
            destCart.setModifiedUser(modifiedUser);
            destCart.setModifiedTime(modifiedTime);
            //执行更新操作
            return cartMapper.updateCartInfo(destCart);
        }
    }
}
~~~

4.单元测试

#### 后端-控制层

1.无异常，不需要处理

2.设计请求

请求地址：/cart/addCart

请求参数：Cart cart, HttpSession session

请求类型：post

响应类型：JsonResult< void>

3.处理请求

~~~java
@PostMapping("/addCart")
public JsonResult<Void> addCart(Cart cart, HttpSession session){
    //从session中区域uid和用户名
    Integer uid = getUserIdFromSession(session);
    String username = getUsernameFromSession(session);
    cart.setUid(uid);
    Date date = new Date();
    int result = cartService.addCart(cart, username, date, username, date);
    if (result == 0){
        throw  new InsertException("服务器或数据库异常，加入购物车失败");
    }
    return new JsonResult<>(OK);
}
~~~

#### 前端页面

①给加入购物车绑定点击事件，点击就发送ajax请求，成功则提示添加成功，在购物车等您结算！

~~~javascript
function addProductToCart(){
    let pid = getPidFromLastHtml();
    $(".go-cart").click(function () {
        let price = $("#product-price").text()
        let num = $("#num").val()
        $.ajax({
            url: "http://localhost:8080/cart/addCart",
            type: "post",
            data: {pid:pid,price:price,num:num},
            dataType: "json",
            success: function (res) {
                alert("已成功加入购物车，在购物车等您结算哟！")
            },
            error : function (err) {
                alert("服务器出现错误，加入购物车失败！")
            }
        })
    })
}
~~~

---

### 展示购物车 

#### 后端-持久层

1.编写sql语句

~~~sql
//根据用户uid查询信息
SELECT c.cid,c.pid,c.`uid`,c.price,c.num,p.title,p.image,p.`price` AS real_price
                    FROM t_cart c LEFT JOIN t_product p
                    ON c.pid = p.`id`
                    WHERE c.`uid` = #{uid}
                    order by c.created_time desc                   
~~~

2.在对应的mapper接口定义抽象方法

~~~java
//根据uid查询用户购物车信息
List<CartVo> queryAllCartsByUid(Integer uid);
~~~

3.编写mapper的映射文件

4.单元测试

#### 后端-业务层

1.规划异常

2.定义抽象方法

3.编写具体业务处理逻辑

~~~java
//根据uid查询用户CartVo信息的抽象方法
List<CartVo> queryCartByUid(Integer uid);
//根据uid查询用户购物车信息的具体逻辑
@Override
public List<CartVo> queryCartByUid(Integer uid) {
    return cartMapper.queryAllCartsByUid(uid);
}
~~~

4.单元测试

#### 后端-控制层

1.无异常，不需要处理

2.设计请求

请求地址：/cart/showCarts

请求参数：HttpSession session

请求类型：get

响应类型：JsonResult< List< Cart>>

3.处理请求

~~~java
// 处理查询用户购物车信息的请求
@GetMapping("/showCarts")
public JsonResult<List<CartVo>> showCarts(HttpSession session){
    Integer uid = getUserIdFromSession(session);
    List<CartVo> carts = cartService.queryCartByUid(uid);
    return new JsonResult<>(OK,carts);
}
~~~

#### 前端页面

①用户点击了购物车，自动发送ajax请求，根据返回的内容填充购物车的内容

- 代码量过大，需要到cart.html的script标签中查看

---

### 删除商品 

#### 后端-持久层

1.sql语句编写

~~~sql
//根据cid删除指定商品
delete from t_cart where cid = #{cid}
~~~

2.定义抽象方法

~~~java
//根据cid删除指定商品
int deleteCartByCid(Integer cid);
~~~

3.编写mapper映射文件

#### 后端-业务层

1.异常规划，删除失败

2.定义抽象方法

3.编写具体的业务逻辑

~~~java
//根据cid删除指定的购物车商品
int deleteCartByCid(Integer cid);

// 根据cids删除cart信息的具体逻辑
@Override
public int deleteCartByCid(Integer cid) {
    int result = cartMapper.deleteCartByCid(cid);

    if (result == 0){
        throw new DeleteException("服务器异常，删除失败");
    }
    return result;
}
~~~

4.单元测试

#### 后端-控制层

1.已将异常加入过，不需要重复

2.设计请求

请求路径：/cart/deleteCart

请求参数：Integer[ ] cids

请求方式：post

响应类型：JsonResult< Void>

3.处理请求

~~~java
//处理根据cids内的指定cid删除cart的请求
@PostMapping("/deleteCart")
public JsonResult<Void> deleteCartByCid(Integer[] cids){
    //遍历执行删除操作
    for (Integer cid: cids) {
        cartService.deleteCartByCid(cid);
    }
    return new JsonResult<>(OK);
}
~~~

4.单元测试

#### 前端页面

- 考虑到提高代码复用性，决定前端无论单个还是多个cid都以参数传递，后端以数组接受

~~~javascript
//给每个删除按钮绑定点击事件
function delCartItem(cids){
    if (confirm("确定要删除这条商品吗？")){
        $.ajax({
            url: "http://localhost:8080/cart/deleteCart",
            type: "post",
            data: "cids=" + cids,
            dataType: "json",
            success:function (res) {
                alert("删除成功")
                showUserCarts();
                $("#selectCount").empty().html(0)
                $("#selectTotal").empty().html(0)
            },
            error:function (error) {
                alert("删除失败")
            }
        })
    }
}

//给批量删除按钮绑定点击事件
function selDelCart(){
    //获取页面中购物车的商品数量
    let trNum = $("#cart-list tr").length
    let chooseNum = 0;
    let deletedCids = ""
    let char = "&"
    for (let i = 0; i < trNum; i++) {
        if ($("#cid"+ i).prop("checked")){
            chooseNum += 1;
            let cids = JSON.parse($("#cid"+i).val());
            let str = "cids=" + cids
            if (i === trNum -1){
                deletedCids += str
            }else {
                deletedCids += str + char
            }
        }
    }
    if (chooseNum === 0){
        alert("请先选择需要删除的购物车商品！！！")
        return false;
    }
    if (confirm("确定要删除这些商品吗？")){
        $.ajax({
            url: "http://localhost:8080/cart/deleteCart",
            type: "post",
            data: deletedCids,
            dataType: "json",
            success:function (res) {
                alert("批量删除成功")
                showUserCarts();
                $("#selectCount").html(0)
                $("#selectTotal").html(0)
            },
            error:function (error) {
                alert("删除失败")
            }
        })
    }
}
~~~

---

### 数量增减 

#### 后端-持久层

1.编写sql语句

~~~sql
//根据cid更新用户商品数量
update t_cart set num = #{num},modified_user = #{modifiedUser},modified_time = #{modifiedTime}
                          where cid = #{cid}
//根据cid查询用户cart信息
select * from t_cart where cid = #{cid};
~~~

2.定义抽象方法

3.编写mapper映射文件

4.单元测试

#### 后端-业务层

1.规划异常，更新失败,数据不存在

2.定义service接口的抽象方法

3.编写具体的处理逻辑(已开发过无需重复)

~~~java
//根据cid查询用户cart信息
Cart queryCartByCid(Integer cid);

//具体逻辑
@Override
public int updateCartNumByCid(Integer num, String modifiedUser, Date modifiedTime, Integer cid) {
    //现根据cid查询此数据是否存在
    Cart cart = cartMapper.queryCartByCid(cid);

    if (cart == null){
        throw new CartInfoNotExistsException("购物车内无这条数据，增加失败");
    }

    return cartMapper.UpdateCartNumByCid(num,modifiedUser,modifiedTime,cid);
}
~~~

4.单元测试

#### 后端-控制层

1.处理异常，将异常加入到异常处理

2.设计请求

请求地址：/cart/updateCart

请求参数：Integer num,Integer cid,HttpSession session

请求类型：post

响应类型：JsonResult< void>

3.处理请求

~~~java
@PostMapping("/updateCart")
public JsonResult<Void> updateCateByCid(Integer num,Integer cid,HttpSession session){
    String modifiedUser = getUsernameFromSession(session);
    Date modifiedTime = new Date();
    cartService.updateCartNumByCid(num,modifiedUser,modifiedTime,cid);
    return new JsonResult<>(OK);
}
~~~

4.单元测试

#### 前端页面

① 给增加和减少按钮绑定点击事件，在发送ajax请求之前先把数据减少或增加

~~~javascript
//按加号数量增，减号的逻辑同理
function addNum(num) {
    var n = parseInt($("#goodsCount"+num).val());
    $("#goodsCount"+num).val(n + 1);
    calcRow(num);
    let cid = $("#cid").val();
    let updateNum = $("#goodsCount"+num).val()
    $.ajax({
        url : "http://localhost:8080/cart/updateCart",
        type: "post",
        dataType: "json",
        data:{cid:cid,num:updateNum},
        error: function () {
            alert("增加失败，请等待攻城狮修复！！")
        }
    })
}
//计算单行小计价格的方法
function calcRow(num) {
    //取单价
    var vprice = parseFloat($("#goodsPrice"+num).html());
    //取数量
    var vnum = parseFloat($("#goodsCount"+num).val());
    //小计金额
    var vtotal = vprice * vnum;
    //赋值
    $("#goodsCast"+num).html("¥" + vtotal);
}
~~~

---

### 商品结算

#### 后端-持久层

- 关于展示用户收货地址所需要的在前面写好了因此在这不再重复

1.sql语句

~~~sql
//根据用户cid查询对应的cartVo信息
SELECT c.cid,c.pid,c.`uid`,c.price,c.num,p.title,p.image,p.`price` AS real_price
                               FROM t_cart c LEFT JOIN t_product p
                               ON c.pid = p.`id`
                               WHERE c.`cid` = #{cid}
                               order by c.created_time desc             
~~~

2.mapper抽象接口方法

~~~java
//根据cid查询返回CartVo对象
CartVo queryCartVoByCid(Integer cid);
~~~

3.编写mapper的映射文件

#### 后端-业务层

1.规划异常。没有异常

2.定义抽象接口

3.编写具体逻辑

~~~java
//根据cid查询返回CartVo对象
CartVo queryCartVoByCid(Integer cid);
//根据cids数组批量查询对应订单信息
List<Cart> queryCartByCids(Integer[] cids);

//根据cid返回CartVo对象的具体逻辑
@Override
public CartVo queryCartVoByCid(Integer cid) {
    return cartMapper.queryCartVoByCid(cid);
}
//根据cids数组的内容查询cart信息的具体逻辑
@Override
public List<CartVo> queryCartByCids(Integer[] cids) {
    List<CartVo> list = new ArrayList<>();
    for (Integer cid : cids) {
        CartVo destCartVo = cartMapper.queryCartVoByCid(cid);
        list.add(destCartVo);
    }
    return list;
}
~~~


#### 后端-控制层

1.没有异常需要处理

2.设计请求

请求地址：/cart/queryCids

请求参数：Integer[] cids

请求类型：get

响应类型：JsonResult<List< Cart> >

3.处理请求

~~~java
//处理cids数组的内容查询cart信息的请求
@GetMapping("/queryCids")
public JsonResult<List<Cart>> queryCids(Integer[] cids){
    List<Cart> list = cartService.queryCartByCids(cids);
    return new JsonResult<>(OK,list);
}
~~~

####  前端页面

~~~javascript
<script type="text/javascript">
    function showOrderItem(){
    //自动发送ajax请求查询url地址上的cid信息
    $.ajax({
        url : "http://localhost:8080/cart/queryCids",
        type: "get",
        dataType: "json",
        data: location.search.substr(1),
        success:function(res){
            $("#cart-list").empty()
            for (let i = 0; i < res.data.length; i++) {
                let str = "";
                let cartVo = res.data[i]
                let totalPrice = cartVo.price * cartVo.num
                str =   "<tr>"
                    + "<td><img src=.." + cartVo.image + "collect.png" + " class='img-responsive' /></td>"
                    + "<td>" + cartVo.title + "</td>"
                    + "<td>¥<span>" + cartVo.price + "</span></td>"
                    + "<td>" + cartVo.num + "</td>"
                    + "<td><span>" + totalPrice + "</span></td>"
                    + "</tr>"
                $("#cart-list").append(str)
            }

        },
        error: function () {
            alert("增加失败，请等待攻城狮修复！！")
        }
    })
}
$(function () {
    //页面加载完成时自动发送
    showOrderItem();
})
</script>
~~~

---

## 订单管理

### 创建数据表

~~~sql
CREATE TABLE t_order (
	oid INT AUTO_INCREMENT COMMENT '订单id',
	uid INT NOT NULL COMMENT '用户id',
	recv_name VARCHAR(20) NOT NULL COMMENT '收货人姓名',
	recv_phone VARCHAR(20) COMMENT '收货人电话',
	recv_province VARCHAR(15) COMMENT '收货人所在省',
	recv_city VARCHAR(15) COMMENT '收货人所在市',
	recv_area VARCHAR(15) COMMENT '收货人所在区',
	recv_address VARCHAR(50) COMMENT '收货详细地址',
	total_price BIGINT COMMENT '总价',
	status INT COMMENT '状态：0-未支付，1-已支付，2-已取消，3-已关闭，4-已完成',
	order_time DATETIME COMMENT '下单时间',
	pay_time DATETIME COMMENT '支付时间',
	created_user VARCHAR(20) COMMENT '创建人',
	created_time DATETIME COMMENT '创建时间',
	modified_user VARCHAR(20) COMMENT '修改人',
	modified_time DATETIME COMMENT '修改时间',
	PRIMARY KEY (oid)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE t_order_item (
	id INT AUTO_INCREMENT COMMENT '订单中的商品记录的id',
	oid INT NOT NULL COMMENT '所归属的订单的id',
	pid INT NOT NULL COMMENT '商品的id',
	title VARCHAR(100) NOT NULL COMMENT '商品标题',
	image VARCHAR(500) COMMENT '商品图片',
	price BIGINT COMMENT '商品价格',
	num INT COMMENT '购买数量',
	created_user VARCHAR(20) COMMENT '创建人',
	created_time DATETIME COMMENT '创建时间',
	modified_user VARCHAR(20) COMMENT '修改人',
	modified_time DATETIME COMMENT '修改时间',
	PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
~~~

### 创建实体类

~~~java
//对应数据表t_order的实体类
@Data
public class Order extends BaseEntity {
    private Integer oid;
    private Integer uid;
    private String recvName;
    private String recvPhone;
    private String recvProvince;
    private String recvCity;
    private String recvArea;
    private String recvAddress;
    private Long totalPrice;
    private Integer status;
    private Date orderTime;
    private Date payTime;
}

// 对应数据表t_order_item的实体类
@Data
public class OrderItem extends BaseEntity{
    private Integer id;
    private Integer oid;
    private Integer pid;
    private String title;
    private String image;
    private Long price;
    private Integer num;
}
~~~

### 创建订单 

#### 后端-持久层

1.sql语句编写

~~~sql
//向order表中插入一条order数据
insert into t_order(uid,recv_name,recv_phone,recv_province,recv_city,recv_area,recv_address,
                   total_price,status,order_time,pay_time,created_user,created_time,modified_user,modified_time)
                   values(
                       #{uid},#{recvName},#{recvPhone},#{recvProvince},#{recvCity},#{recvArea},#{recvAddress},
                       #{totalPrice},#{status},#{orderTime},#{payTime},#{createdUser},#{createdTime},
                       #{modifiedUser},#{modifiedTime})		
                       
//向order_item表中插入一条orderItem数据
insert into t_order_item(oid,pid,title,image,price,num,
                        created_user,created_time,modified_user,modified_time)
                        values(#{oid},#{pid},#{title},#{image},#{price},
                               #{num},#{createdUser},#{createdTime},#{modifiedUser},#{modifiedTime}
                          )
~~~

2.定义mapper接口和抽象方法

~~~java
//实体类Order对应的mapper接口
public interface OrderMapper {
    //插入一条order订单数据
    int insertOneOrder(Order order);
    //向order_item表中插入一条orderItem数据
    int insertOneOrderItem(OrderItem orderItem);
}
~~~

3.编写mapper的映射文件

设置自定义的结果集映射文件

4.单元测试

#### 后端-业务层

1.规划异常，插入失败

2.定义service层接口和抽象方法

3.编写具体的处理逻辑

~~~java
//处理添加order订单数据的抽象方法
Order insertOrder(Integer aid, Long totalPrice, Integer uid, String username);

//处理添加orderItem数据的抽象方法
int insertOrderItem(Integer oid, Integer cid, Integer num, String username);

//具体的处理逻辑
@Service
public class IOrderServiceImpl implements IOrderService {
    @Autowired(required = false)
    private OrderMapper orderMapper;
    @Autowired(required = false)
    private IAddressService addressService;
    @Autowired(required = false)
    private ICartService cartService;
    @Autowired(required = false)
    private IProductService productService;

    @Override
    public Order insertOrder(Integer aid,Long totalPrice,Integer uid,String username) {
        //根据控制层传入的aid进行查询
        Address address = addressService.queryAddressByAid(aid);

        //创建一个用于向持久层传输的Order实体类对象
        Order order = new Order();

        //补全order对象的空白字段
        order.setUid(uid);
        order.setRecvName(address.getName());
        order.setRecvPhone(address.getPhone());
        order.setRecvProvince(address.getProvinceName());
        order.setRecvCity(address.getCityName());
        order.setRecvArea(address.getAreaName());
        order.setRecvAddress(address.getAddress());
        order.setTotalPrice(totalPrice);
        order.setStatus(0); //表示未支付
        Date createdTime = new Date();
        order.setOrderTime(createdTime);
        order.setPayTime(null);
        order.setCreatedUser(username);
        order.setModifiedUser(username);
        order.setCreatedTime(createdTime);
        order.setModifiedTime(createdTime);

        //调用持久层进行插入
        int result = orderMapper.insertOneOrder(order);

        if (result == 0){
            throw new InsertException("服务器出现错误，创建订单失败");
        }
         //根据oid查询指定的订单，并返回给控制层
        return orderMapper.queryOrderByOid(order.getOid());
    }

    @Override
    public int insertOrderItem(Integer oid, Integer cid, Integer num, String username) {
        //根据cid查询订单获取pid
        Cart cart = cartService.queryCartByCid(cid);

        //取出pid的值
        Integer pid = cart.getPid();

        //根据pid查询商品信息
        Product product = productService.queryProductById(pid);

        //创建一个用于向持久层传输的OrderItem实体类对象
        OrderItem orderItem = new OrderItem();

        //补全orderItem对象的空白字段
        orderItem.setOid(oid);
        orderItem.setPid(pid);
        orderItem.setTitle(product.getTitle());
        orderItem.setImage(product.getImage());
        orderItem.setPrice(product.getPrice());
        orderItem.setNum(num);
        Date createdTime = new Date();
        orderItem.setCreatedUser(username);
        orderItem.setCreatedTime(createdTime);
        orderItem.setModifiedUser(username);
        orderItem.setModifiedTime(createdTime);

        //调用持久层进行插入
        int result = orderMapper.insertOneOrderItem(orderItem);

        if (result == 0){
            throw new InsertException("服务器出现错误，创建订单失败");
        }

        return result;
    }
}
~~~

4.单元测试

#### 后端-控制层

1.处理异常，已添加无需再重复

2.设计请求

请求路径： ①/order/createOrder  ②/order/createOrderItem

请求参数：①Integer aid,Long totalPrice,HttpSession session

​					②Integer oid,Integer cid,Integer pid,Integer num,HttpSession session

请求类型：post

响应类型：①JsonResult< Order> ②①JsonResult< Void>

3.创建一个新的控制器处理请求

~~~java
@PostMapping("/createOrder")
public JsonResult<Order> createOrder(Integer aid,Long totalPrice,HttpSession session){
    //从session中取出用户名和uid
    Integer uid = getUserIdFromSession(session);
    String username = getUsernameFromSession(session);
    //调用业务层方法执行插入操作
    orderService.insertOrder(aid,totalPrice,uid,username);
    return new JsonResult<>(OK);
}

@PostMapping("/createOrderItem")
    public JsonResult<Void> createOrderItem(Integer oid,Integer cid,Integer num,HttpSession session){
        //从session中取出用户名
        String username = getUsernameFromSession(session);

        //调用业务层方法执行插入操作
        orderService.insertOrderItem(oid,cid,num,username);

        return new JsonResult<>(OK);
    }
~~~

#### 前端页面

①在用户选择了商品来到确认订单页面之后

②确认商品无误之后，点击在线支付按钮，那么在点击之后就要生成order订单，

​	生成order订单项就应该发送一个ajax请求，且这个ajax请求生成的oid订单号需要返回给前端

​	在前端需要时带着这个订单号跳转到下一个指定页面

③且将oid传入另一个方法中，在这个方法内获取客户所勾选的商品的数量，

​	并根据这个数量将获取每一个商品cid，发送与商品个数一致的次数的ajax请求

​	并且每个ajax都需要带着cid和num和oid

- <font color='red'>**javaScript代码太长，不予展示，需要到项目于的/web/orderConfirm.htmll页面查看**</font>

---

### 集成支付宝沙箱支付

①引入依赖到pom文件

~~~xml
<!-- 引入支付宝沙箱支付依赖-->
<dependency>
    <groupId>com.alipay.sdk</groupId>
    <artifactId>alipay-sdk-java</artifactId>
    <version>4.23.26.ALL</version>
</dependency>
~~~

②编写alipayConfig用于设置请求属性

~~~java
//支付宝沙箱支付配置类
@Data
@Component
public class AlipayConfig {
    //自己的appId
    public static String appId = "2021000121634026";
    //应用私有秘钥
    public static String appPrivateKey = "MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDDnIWV/KsE+T3hpSr7+H//GdKsTbVcc/fpsp/RI9Aqfg0z009TrUI+NQ9+6mI4YxQUZnnUM1sRcnAQI4ivnGu2qkZFoot7gv50jwvZD8amIe6b3Joed2DBOKps2IJuyOwjV4Ae+WBJW5bSNiuuYu6UjanX69nK8TU6y6K39p02YnDqC47Z0veKVbAmpbsOnks1gGd+Cgcm5fSsdcx+aGKSt6YEJuYkqri9lUExHSYkZLNfzzAXRujyLCk0lLHdzUNDoatIyukk1HhCyvLosg3oaCtvIzqGC8/XHCEQ5r8JD4nKd/gGoyolWzhJvFcFkKmHjPZA8vT8TP1ZC1z1m1DvAgMBAAECggEBAI0C7JnvByoSsrVTZ+U0grDXYLOtYSxAvVrO1b7iXlIDhGjzz5+2qqZFgeIv/JZBdlwuc2yxiNjO8lHwC7zsugl4Pig8wOhMyjokVJopcT6Z/3SEVuXXkPw5aUIF4iES3oersESj6PF5AQSQ4HRaBTs51FI/R0WxFHpKCgcr1LE6inEn862CvZlbh2Cqh6rqBxB1ESi4eRhAj+FsJlczqWDCLn2dG1Ki7IhkuApdNFs9lurGeXGu/FBphaaprAiTVWuxyBWIdW5TYACjkNZ+oXrzCJK6t0DWsmxAbaZHMiLIVIAypZ6p8/kf/b70zXRYhkXQ6KUKpo983G6dBBPUF0ECgYEA7ta+jK3OZvzwZQeaBXYS4vi28jgpLg/v/DFpdcwaAShKEms/R7XZ8wZ9y4jabQKOuyJX69NS7dhfNv4wdW976BkIevwANM6haFSh5bJsq+EK8fwXEPez2uKPLsIdSgBwwh6En4vClOUTOOprLuyTXM7mQNqSW+AsaLmlaGYDuhcCgYEA0aqkcJMysXnGIc7nbJDiWvsRihe5WMsMCOY6vtjVPMh/qEasT9Hxhv4Gs+Pso15MNxhDuRddp+qgXyuUXGkeii8h+p2MgO5O5tA1T74APaDclmyiuc27Rj27Cc9mbFTWOk5txrXgzqEJjw9HudUHTJmpKoqI2aVEK73X6AdB3ukCgYAqHwk/+i8ajqU+zBZnvCkcikyJb0oj63+hdH1q3vH/HkHh+bQRS4sChzSMPrh23Sqa6jWjS4OmmrBAHJgjPeQWTMPoHKVUqtRgd/yNa+gqb+fkQVc4ENdRVP93eZh8wpMgSQ2OrbFFXRkEwqLghax/g6Wr7mA9f82VMphvTv59RQKBgA3YnxNwJSDjUdpZt57L0qb/faEJAAyFHD5aNfb0iuCAvS13vVloG/M2Q2sN2krPp2jcCVzn1h+Itx6R2jJgHswxYKUUUnsRQdSsW1jwy0NGpEqq0fRDSeLRoNB9Cd6Nm7guBcHhsP70U5VHBQ2Yq+q7GxjcHT2CVIYu+1svX4JBAoGARyBB9IjgM9dH0cH8djojC2qlH7Qar9bbvl55i9EQY51d1J/bC5JBE/CAYu0s1MmIatigcJ6A7FyvJ+nnow+qr4tegi5CG76v+Ue/IrgZXJZyDqMBrHorn/SwRXleOj2gjfxkQ8mh0OkrIntbdD6SDnvtXkxcVkfX9ICY5VIA4DA=";
    //支付宝公钥
    public static String alipayPublicKey = "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAjtedJ8C1NIO3r4vuMThcvTMZxqO3Jki5VYPfkmnraA/PIKXXgsfOdoSWxCsiqPMIBMRT3Oyk1EsxgAeFBKTFaSM5LC8oinTXFbkv+3XEuOjtfqbp0oIgu9pWfJQDL2gIVSbm3VKmdE4UtJ36nu3hyuTT3U19QQsKVgxMDWHCOIw0eCHcJm1xDPj0zmagL3jC7576sXHcnFxEKARGugMpP9bkBgvFkjKrnkQfMAz3OO8vUSC0lCGo2UrSwhyD6zqXVz39sIduVpKTTg+wpAJQ/RhBhLXNw4JW3UaZpX2BZbmqEx91Hpr+O/95Z90cTqT+rwyu6uW612B5bCPnKa+BCQIDAQAB";
    //异步回调地址
    public static String notifyUrl = "http://h64hsr.natappfree.cc/alipay/notifyNotice";
    //同步回调地址
    public static String returnUrl = "http://h64hsr.natappfree.cc/alipay/returnNotice";
    //推荐使用这个秘钥
    public static String signType = "RSA2";
    //使用的编码格式
    public static String charset = "utf-8";
    //支付宝默认网关
    public static String gatewayUrl = "https://openapi.alipaydev.com/gateway.do";

}
~~~

③创建alipayController，并编写pay接口、异步回调接口、同步回调接口

- 由于<font color='red'>**在异步回调会出现session失效**</font>的问题，解决方法 ---> [如何解决异步回调session失效](https://blog.csdn.net/woniu__/article/details/105320823?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-2-105320823-null-null.pc_agg_new_rank&utm_term=%E5%A6%82%E4%BD%95%E6%9F%A5%E7%9C%8B%E6%B2%99%E7%AE%B1%E6%94%AF%E4%BB%98%E5%AE%9D%E5%BC%82%E6%AD%A5%E5%9B%9E%E8%B0%83%E6%98%AF%E5%90%A6%E6%88%90%E5%8A%9F&spm=1000.2123.3001.4430)

- 异步接口方法一般用于在支付后进行数据库的修改操作，

  而同步接口方法一般是用于向用户显示已支付完成的作用，

~~~java
// 处理支付宝沙箱支付的接口
@Slf4j
@Controller
@RequestMapping("/alipay")
public class AliPayController {
    @Autowired
    private IOrderService orderService;
    /**
     * Description : 处理在线支付的请求
     * @date 2022/7/25
     * @param oid 订单id
     * @param totalPrice 订单总金额
     * @return java.lang.String
     **/
    @ResponseBody
    @GetMapping(value = "/pay",produces = "text/html;charset=UTF-8")
    public String goAlipay(String oid,String totalPrice,HttpServletRequest request) throws Exception {

        // 获得初始化的AlipayClient
        AlipayClient alipayClient = new DefaultAlipayClient(AlipayConfig.gatewayUrl, AlipayConfig.appId,
                AlipayConfig.appPrivateKey, "json", AlipayConfig.charset, AlipayConfig.alipayPublicKey,
                AlipayConfig.signType);

        // 设置请求参数
        AlipayTradePagePayRequest alipayRequest = new AlipayTradePagePayRequest();
        alipayRequest.setReturnUrl(AlipayConfig.returnUrl);
        alipayRequest.setNotifyUrl(AlipayConfig.notifyUrl);

        // 商户订单号，商户网站订单系统中唯一订单号，必填
        String out_trade_no = oid;
        // 付款金额，必填
        String total_amount = totalPrice;
        // 订单名称，必填
        String subject = "支付宝沙箱测试商品支付";
        // 商品描述，可空
        String body = "";

        // 该笔订单允许的最晚付款时间，逾期将关闭交易。取值范围：1m～15d。m-分钟，h-小时，d-天，1c-当天（1c-当天的情况下，无论交易何时创建，都在0点关闭）。
        // 该参数数值不接受小数点， 如 1.5h，可转换为 90m。
        String timeout_express = "1c";

        //从session中取出异步需要用的uid
        Integer str = (Integer) request.getSession().getAttribute("uid");
        String passback_params = String.valueOf(str);
        //对取得的数据进行URLEncoder编码，这一步不做会报错
        String uidStr = URLEncoder.encode(passback_params,"UTF-8");

        //将编码后的数据封装在alipayRequest对象中，参数名字一定要是passback_params才能在异步通知时返回
        //至于参数的变量值这没有要求，只需要与上方编码后的一样即可
        alipayRequest.setBizContent("{\"out_trade_no\":\"" + out_trade_no + "\"," + "\"total_amount\":\"" + total_amount
                + "\"," + "\"subject\":\"" + subject + "\"," + "\"body\":\"" + body + "\"," + "\"timeout_express\":\""
                + timeout_express + "\"," + "\"passback_params\":\"" + uidStr + "\","
                + "\"product_code\":\"FAST_INSTANT_TRADE_PAY\"}");

        // 这个是请求支付宝后台获取的数据，实际上获取是一个表单，然后他会附带一个自动执行的js方法
        //自动替你执行表单，然后进入表单中支付宝生成的页面
        String result = alipayClient.pageExecute(alipayRequest).getBody();
        log.info(result);
        return result;
    }

    /**
     * Description : 处理异步回调的方法
     * @date 2022/7/25
     * @param request  请求对象
     * @param response 响应对象
     * @return void
     **/
    @PostMapping("/notifyNotice")
    @ResponseBody
    public void alipayNotifyNotice(HttpServletRequest request, HttpServletRequest response) throws Exception {

        log.info("支付成功, 进入异步通知接口...");

        // 获取支付宝POST过来反馈信息
        Map<String, String> params = new HashMap<String, String>();
        Map<String, String[]> requestParams = request.getParameterMap();
        for (Iterator<String> iter = requestParams.keySet().iterator(); iter.hasNext();) {
            String name = (String) iter.next();
            String[] values = (String[]) requestParams.get(name);
            String valueStr = "";
            for (int i = 0; i < values.length; i++) {
                valueStr = (i == values.length - 1) ? valueStr + values[i] : valueStr + values[i] + ",";
            }
            // 乱码解决，这段代码在出现乱码时使用
            // valueStr = new String(valueStr.getBytes("ISO-8859-1"), "utf-8");
            params.put(name, valueStr);
        }

        boolean signVerified = AlipaySignature.rsaCheckV1(params, AlipayConfig.alipayPublicKey, AlipayConfig.charset,
                AlipayConfig.signType); // 调用SDK验证签名

        // ——请在这里编写您的程序（以下代码仅作参考）——

        /*
         * 实际验证过程建议商户务必添加以下校验： 1、需要验证该通知数据中的out_trade_no是否为商户系统中创建的订单号，
         * 2、判断total_amount是否确实为该订单的实际金额（即商户订单创建时的金额）， 3、校验通知中的seller_id（或者seller_email)
         * 是否为out_trade_no这笔单据的对应的操作方（有的时候，一个商户可能有多个seller_id/seller_email）
         * 4、验证app_id是否为该商户本身。
         */
        if (signVerified) {// 验证成功
            // 商户订单号
            String out_trade_no = new String(request.getParameter("out_trade_no").getBytes("ISO-8859-1"), "UTF-8");

            // 支付宝交易号
            String trade_no = new String(request.getParameter("trade_no").getBytes("ISO-8859-1"), "UTF-8");

            // 交易状态
            String trade_status = new String(request.getParameter("trade_status").getBytes("ISO-8859-1"), "UTF-8");

            // 付款金额
            String total_amount = new String(request.getParameter("total_amount").getBytes("ISO-8859-1"), "UTF-8");

            //在支付接口中保存在session中uid
            String passback_params = new String(request.getParameter("passback_params").getBytes("ISO-8859-1"), "UTF-8");
            //对传回的数据进行编码的逆过程，对参数进行解码
            String uidStr = URLDecoder.decode(passback_params,"UTF-8");

            if (trade_status.equals("TRADE_FINISHED")) {
                // 判断该笔订单是否在商户网站中已经做过处理
                // 如果没有做过处理，根据订单号（out_trade_no）在商户网站的订单系统中查到该笔订单的详细，并执行商户的业务程序
                // 如果有做过处理，不执行商户的业务程序

                // 注意： 尚自习的订单没有退款功能, 这个条件判断是进不来的, 所以此处不必写代码
                // 退款日期超过可退款期限后（如三个月可退款），支付宝系统发送该交易状态通知
            } else if (trade_status.equals("TRADE_SUCCESS")) {
                // 判断该笔订单是否在商户网站中已经做过处理
                // 如果没有做过处理，根据订单号（out_trade_no）在商户网站的订单系统中查到该笔订单的详细，并执行商户的业务程序
                // 如果有做过处理，不执行商户的业务程序

                // 注意：
                // 付款完成后，支付宝系统发送该交易状态通知
                // 编写自己的订单支付成功的业务逻辑
                //将上方取到的uid和将订单号转换为包装类
                Integer uid = Integer.valueOf(uidStr);
                Integer oid = Integer.valueOf(out_trade_no);
                //调用订单业务层修改订单状态信息
                orderService.updateOrderStatusByOid(oid,uid,1);

                log.info("********************** 支付成功(支付宝异步通知) **********************");
                log.info("* 当前支付用户的id: {}", passback_params);
                log.info("* 订单号: {}", out_trade_no);
                log.info("* 支付宝交易号: {}", trade_no);
                log.info("* 实付金额: {}", total_amount);
                log.info("***************************************************************");
            }
            log.info("支付成功...");
        } else {// 验证失败
            log.info("支付, 验签失败...");
        }
    }

    /**
     * Description : 同步回调方法
     * @date 2022/7/25
     * @param request 请求对象
     * @param response 响应对象
     * @return void
     **/
    @GetMapping("/returnNotice")
    public void alipayReturnNotice(HttpServletRequest request, HttpServletResponse response) throws Exception {

        log.info("支付成功, 进入同步通知接口...");

        // 获取支付宝GET过来反馈信息
        Map<String, String> params = new HashMap<String, String>();
        Map<String, String[]> requestParams = request.getParameterMap();
        for (Iterator<String> iter = requestParams.keySet().iterator(); iter.hasNext();) {
            String name = (String) iter.next();
            String[] values = (String[]) requestParams.get(name);
            String valueStr = "";
            for (int i = 0; i < values.length; i++) {
                valueStr = (i == values.length - 1) ? valueStr + values[i] : valueStr + values[i] + ",";
            }
            // 乱码解决，这段代码在出现乱码时使用
            valueStr = new String(valueStr.getBytes("ISO-8859-1"), "utf-8");
            params.put(name, valueStr);
        }

        boolean signVerified = AlipaySignature.rsaCheckV1(params, AlipayConfig.alipayPublicKey, AlipayConfig.charset,
                AlipayConfig.signType); // 调用SDK验证签名

        // ——请在这里编写您的程序（以下代码仅作参考）——
        if (signVerified) {
            // 商户订单号
            String out_trade_no = new String(request.getParameter("out_trade_no").getBytes("ISO-8859-1"), "UTF-8");

            // 支付宝交易号
            String trade_no = new String(request.getParameter("trade_no").getBytes("ISO-8859-1"), "UTF-8");

            // 付款金额
            String total_amount = new String(request.getParameter("total_amount").getBytes("ISO-8859-1"), "UTF-8");

            //同步调用方法中返回到指定的界面
            //携带订单号并跳转到支付成功的界面
            response.sendRedirect(request.getContextPath() + "/web/paySuccess.html?oid=" + out_trade_no);

            log.info("********************** 支付成功(支付宝同步通知) **********************");
            log.info("* 订单号: {}", out_trade_no);
            log.info("* 支付宝交易号: {}", trade_no);
            log.info("* 实付金额: {}", total_amount);
            log.info("***************************************************************");
        } else {
            log.info("支付, 验签失败...");
        }
    }
}
~~~

---

### 支付订单 

#### 后端-持久层

1.sql语句

~~~mysql
#根据订单号查询订单
select * from t_order where oid = #{oid}

#根据订单号修改状态
update t_order set status = #{status},pay_time = #{payTime} where oid = #{oid}

#根据oid能从order_item表中找到对应的orderItem信息
SELECT * FROM t_order_item WHERE oid = #{oid}

#根据uid和pid删除对应的t_cart表中的数据
DELETE FROM t_cart WHERE uid = #{uid} AND pid = #{pid} 
~~~

2.定义mapper接口的抽象方法

~~~java
//根据订单号查询订单
Order queryOrderByOid(Integer oid);
//根据订单号修改支付状态和支付时间
int updateStatusByOidInt(Integer oid, Integer status, Date payTime);
//根据oid能从order_item表中找到对应的pid信息
List<OrderItem> queryOrderItemByOid(Integer oid);
//根据uid和pid删除对应的t_cart表中的数据
int deleteCartByUidAndPid(Integer uid,Integer pid);
~~~

3.编写对应的映射文件

- 篇幅较长，就不放这了

4.单元测试

#### 后端-业务层

1.规划异常，查询订单不存在，修改订单失败

2.定义service层的抽象方法

3.编写具体的业务逻辑

~~~java
//在ICartService业务层接口编写这个根据uid和pid删除对应的t_cart表中的数据的抽象方法
int deleteCartByUidAndPid(Integer uid,Integer pid);
//在IOrderService业务层接口编写这个根据oid能从order_item表中找到对应的OrderItem信息的抽象方法
List<OrderItem> queryOrderItemByOid(Integer oid);
//根据oid修改oid的订单状态的抽象方法
int updateOrderStatusByOid(Integer oid,Integer uid,Integer status);

// 根据uid和pid删除对应的t_cart表中的数据的具体逻辑
@Override
public int deleteCartByUidAndPid(Integer uid, Integer pid) {
    int result = cartMapper.deleteCartByUidAndPid(uid, pid);
    if (result == 0){
        throw new DeleteException("服务器异常，删除购物车商品失败!!");
    }
    return result;
}
//根据订单oid查询orderItem信息的具体逻辑
@Override
public List<OrderItem> queryOrderItemByOid(Integer oid) {
    List<OrderItem> orderItems = orderMapper.queryOrderItemByOid(oid);
    if (orderItems.size() == 0){
        throw new OrderNotExistsException("订单不存在！！！");
    }
    return orderItems;
}
//根据订单oid修改订单状态的具体逻辑
@Override
public int updateOrderStatusByOid(Integer oid, Integer uid, Integer status) {
    //先查询一下订单信息
    Order order = orderMapper.queryOrderByOid(oid);
    int result = 0;
    //status == 0代表刚刚创建
    if (order.getStatus() == 0){
        //修改支付时间
        Date payTime = new Date();
        result = orderMapper.updateStatusByOidInt(oid, status,payTime);

        //根据oid查找具体的OrderItem信息
        List<OrderItem> orderItems = orderMapper.queryOrderItemByOid(oid);
        for (OrderItem o: orderItems) {
            //从OrderItem中取得pid
            Integer pid = o.getPid();
            //根据pid和uid删除购物车中的商品
            cartService.deleteCartByUidAndPid(uid, pid);
        }
    }else {
        //除了status == 0的状况其他的都可以直接修改其状态
        //修改订单状态
        result = orderMapper.updateStatusByOidInt(oid,status,order.getPayTime());
    }
    if (result == 0){
        throw new UpdateException("服务器异常，修改订单状态失败");
    }
    return result;
}
~~~

4.单元测试

#### 后端-控制层

1.控制异常，将异常加入全局管理

2.设计请求

请求地址：①/order/queryOrder ②/order/updateStatus

请求参数：①Integer oid ；②Integet oid，HttpSession session,Integer status

请求类型：①get ②post

响应类型：①JsonResult< Order>   ②JsonResult< Void>

3.处理请求

~~~java
//处理根据订单oid查询order信息的请求
@GetMapping("/queryOrder")
public JsonResult<Order> queryOrderByOid(Integer oid){
    Order order = orderService.queryOrderByOid(oid);
    return new JsonResult<>(OK,order);
}
// 处理根据订单oid修改订单状态的请求
@PostMapping("/updateStatus")
public JsonResult<Void> updateStatusByOid(Integer oid,Integer status){
    //修改订单状态
    orderService.updateOrderStatusByOid(oid,status);
    return new JsonResult<>(OK);
}
~~~

#### 前端页面

①在页面加载完成的时候根据上个页面传递的参数查询order

②给确认付款按钮绑定单击事件，发送修改订单状态ajax请求

~~~javascript
<script type="text/javascript">
    function getOrderByOid(oid){
    $.ajax({
        url : "/order/queryOrder",
        type: "get",
        data: "oid=" + oid,
        dataType: "json",
        success: function (res) {
            if (res.status === 200){
                //修改订单号和支付金额
                let order = res.data
                $("#orderId").html(order.oid)
                $("#orderPrice").html(order.totalPrice)
                $("#rightPrice").html("¥" + order.totalPrice + "&nbsp;")
                //给隐藏域的属性赋值
                $("#oid").val(order.oid)
                $("#totalPrice").val(order.totalPrice)
            }else if (res.status === 3000){
                location.href = "500.html"
            }
        },
        error: function (error) {
            if (error.status === 400 ){
                location.href = "500.html"
            }else {
                alert("服务器出现故障，请等待攻城狮修复！！")
            }
        }
    });
}
$(function () {
    //引入公共头部、中间导航条以及页脚
    $(".header").load("components/head.html")
    $(".footer").load("components/footer.html")
    $(".middleNavigation").load("components/middleNavigationBar.html")

    //获取上个网页传来的参数
    let oid = getPidFromLastHtml();

    //自动发送ajax请求查询订单信息
    getOrderByOid(oid);
})
</script>
~~~

----

### 查看订单详情

#### 后端-持久层

1.sql语句

~~~mysql
#根据oid查询order信息
SELECT od.`oid`,od.`aid`,od.`recv_name`,od.`total_price`,
       od.`status`,od.`created_time`,
       orm.`image`,orm.`title`,
       orm.`price`,orm.`num`
FROM t_order od 
LEFT JOIN t_order_item orm
ON od.`oid` = orm.`oid`
WHERE od.oid = #{oid}
ORDER BY orm.`price` DESC;
~~~

2.定义mapper接口的抽象方法

~~~java
//根据oid查询值对象
List<OrderVo> queryOrderVoByOid(Integer oid);
~~~

3.编写对应的映射文件

#### 后端-业务层

1.规划异常，没有新异常

2.定义service层的抽象接口和创建值对象OrderVo

3.编写具体的业务逻辑

~~~java
//根据订单oid查询订单的抽象方法
List<OrderVo> queryOrderVoByOid(Integer oid);

//根据订单oid查询订单的具体逻辑
@Override
public List<OrderVo> queryOrderVoByOid(Integer oid) {
    List<OrderVo> orderVos = orderMapper.queryOrderVoByOid(oid);
    for (OrderVo vo: orderVos) {
        //根据每个订单的oid查询地址信息
        Address address = addressService.queryAddressByAid(vo.getAid());
        //补全OrderVo值对象中的空白字段
        vo.setZip(address.getZip());
        vo.setPhone(address.getPhone());
        vo.setProvinceName(address.getProvinceName());
        vo.setCityName(address.getCityName());
        vo.setAreaName(address.getAreaName());
        vo.setAddress(address.getAddress());
    }
    return orderVos;
}
~~~

#### 后端-控制层

1.无异常，不需要处理

2.设计请求

请求路径：/order/queryOrderVo

请求参数：Integer oid

请求方式：get

响应类型：JsonResult<List< OrderVo>>

3.处理请求

~~~java
//处理根据oid查询订单的请求
@GetMapping("/queryOrderVo")
public JsonResult<List<OrderVo>>  queryOrderVo(Integer oid){
    List<OrderVo> orderVos = orderService.queryOrderVoByOid(oid);

    return new JsonResult<>(OK,orderVos);
}
~~~

#### 前端页面

- 在这个页面加载完成之前需要先根据上个页面传来的参数查询订单信息

<font color='red'>**篇幅过长，不做展示，在/web/orderInfo.html页面下可查看**</font> 

### 查看所有订单 

#### 后端-持久层

1.sql语句

~~~mysql
SELECT od.`oid`,od.`aid`,od.`recv_name`,od.`total_price`,
       od.`status`,od.`order_time`,
       orm.`image`,orm.`title`,
       orm.`price`,orm.`num`
FROM t_order od 
LEFT JOIN t_order_item orm
ON od.`oid` = orm.`oid`
WHERE od.uid = #{uid}
ORDER BY od.`order_time` DESC;
~~~

2.定义抽象接口的方法

~~~java
//根据uid查询值对象
List<OrderVo> queryOrderVoByUid(Integer uid);
~~~

3.编写对应的mapper映射文件

4.单元测试

#### 后端-业务层

1.无新异常

2.定义接口的抽象方法

3.编写具体业务逻辑

- 由于与上个 的逻辑相似，就不再重复了

#### 后端-控制层

- 与上个 相似

#### 前端页面

- 糙，这一个页面的js代码写的是真折磨人，写了快3个小时还有各种bug！
- **订单 的其余查看其他项所用的持久层、业务层、控制层已经在支付订单 中写好了**

<font color='red'>**篇幅过长，不做展示，在/web/orders.html页面下可查看**</font> 

---

## 收藏管理

### 创建数据表

~~~mysql
CREATE TABLE t_favorites(
fid INT PRIMARY KEY AUTO_INCREMENT COMMENT '收藏商品在数据表的id',
uid INT COMMENT '归属的用户id',
pid INT COMMENT '归属的商品id',
image VARCHAR(255) COMMENT '商品图片保存地址',
price BIGINT COMMENT '商品的价格',
title VARCHAR(255) COMMENT '商品的标题',
sell_point VARCHAR(255) COMMENT '商品的卖点',
status INT COMMENT '商品的收藏状态'
); 
~~~

### 创建实体类

~~~java
//对应数据表t_favorites的实体类
@Data
public class Favorites {
    private Integer fid;
    private Integer uid;
    private Integer pid;
    private String image;
    private Long price;
    private String title;
    private String sellPoint;
    private Integer status;
}
~~~

### 在Springboot中如何使用pageHelper插件

- 因为商品数据有可能有多条，因此决定使用分页插件进行过滤查询，下面的商品搜索也用得上

  也由于我不怎么熟悉springboot使用pageHelper分页插件，也正好记录一下

  [github官方参考资料](https://github.com/pagehelper/Mybatis-PageHelper/blob/master/wikis/zh/HowToUse.md)

  [csdn参考资料](https://blog.csdn.net/qq_43469899/article/details/100119116?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-100119116-blog-119714502.pc_relevant_multi_platform_whitelistv2_exp3w&spm=1001.2101.3001.4242.1&utm_relevant_index=3) 

①从github上引入分页插件的依赖到项目的pom文件中

~~~xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>3.7.4</version>
    <!-- <version>最新版本</version> -->
</dependency>
~~~

②在yml配置文件中进行部分内容配置

~~~yml
#配置mybatis配置
mybatis:
  type-aliases-package: top.year21.computerstore.entity
  mapper-locations: classpath:mybatis/mapper/*.xml
  configuration:
    #开启在mybatis处理过程中打印出对应的sql语句功能
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    #开启数据库字段自动转换为驼峰命名
    map-underscore-to-camel-case: true
#配置分页插件 
#照着写就行，别问为什么
pagehelper:
  dialect: mysql
  reasonable: true
  support-methods-arguments: true
  params: count=countSql
~~~

③<font color='red'>**在程序启动类中注册一个Pagehelper类，否则会报错或者取不到数据，这一步很关键！>**</font>

​	或者是在配置类(标注了@Configuration的类)中进行注册也可以

~~~java
@Bean
public PageHelper pageHelper() {
    PageHelper pageHelper = new PageHelper();
    Properties properties = new Properties();
    properties.setProperty("offsetAsPageNum", "true");
    properties.setProperty("rowBoundsWithCount", "true");
    properties.setProperty("reasonable", "true");
    properties.setProperty("dialect", "mysql");
    pageHelper.setProperties(properties);
    return pageHelper;
}
~~~

④控制层调用业务层，业务层调用持久层查询，且分页功能必须在select查询语句之前开启，

​	所以在service层的实现类调用持久层的方法之前使用pagehelper.start方法

~~~java
@Service
public class IFavoritesServiceImpl implements IFavoritesService {
    @Autowired
    private FavoritesMapper favoritesMapper;
    @Autowired
    private IProductService productService;
    
//根据uid和商品收藏状态查询收藏数据的具体逻辑
    @Override
    public PageInfo<Favorites> queryFavorites(Integer uid, Integer pageNum,Integer pageSize,Integer status) {
        //开启分页功能，pageNum是当前页，pageSize是每页显示的数据量，这两个值都可以选择让前端传或者自己调整
         PageHelper.startPage(pageNum,pageSize);
        List<Favorites> favorites = favoritesMapper.queryFavoritesByUidAndStatus(uid, status);
        PageInfo<Favorites> pageInfo = new PageInfo<>(favorites);
        return pageInfo;
    }
~~~

⑤控制层将数据返回

~~~java
@RestController
@RequestMapping("/favorites")
public class FavoritesController extends BaseController{
    @Autowired
    private IFavoritesService favoritesService;
    //处理查询收藏商品的请求
    @GetMapping("/queryFavorites")
    public JsonResult<PageInfo<Favorites>> queryFavorites(HttpSession session, Integer pageNum,Integer pageSize,Integer status){
        Integer uid = getUserIdFromSession(session);
        PageInfo<Favorites> favorites = favoritesService.queryFavorites(uid, pageNum,pageSize,status);
        return new JsonResult<>(OK,favorites);
}
~~~

⑥分页功能如何使用到此结束

---

### 加入收藏

#### 后端-持久层

1.sql语句编写

~~~mysql
insert into t_favorite(uid,pid,image,price,title,sell_point,status)
					values(#{uid},#{pid},#{image},#{price},#{sellPoint},#{status})
~~~

2.定义抽象方法

~~~java
//新增收藏商品的抽象方法
int addFavorites(Favorites favorites);
~~~

3.编写对应的mapper映射文件

#### 后端-业务层

1.异常规划，添加失败

2.定义抽象方法

3.编写具体业务处理逻辑

~~~java
//添加收藏商品的抽象方法
int addFavorites(Favorites favorites);

//添加收藏商品的具体逻辑
@Override
public int addFavorites(Integer uid,Integer pid) {
    Favorites favorites = new Favorites();
    //根据pid查询商品信息
    Product product = productService.queryProductById(pid);
    //填充favorites对象空白字段
    favorites.setUid(uid);
    favorites.setPid(pid);
    favorites.setImage(product.getImage());
    favorites.setPrice(product.getPrice());
    favorites.setTitle(product.getTitle());
    favorites.setSellPoint(product.getSellPoint());
    favorites.setStatus(1);
    int result = favoritesMapper.addFavorites(favorites);
    if (result == 0){
        throw new InsertException("服务器异常，收藏商品失败");
    }
    //取出fid返回给前端页面，以便在搜索界面取消收藏使用
    return favorites.getFid();
}
~~~

4.单元测试

#### 后端-控制层

1.异常已添加过

2.设计请求

请求路径：/favorites/addFavorites

请求类型： HttpSession session,Integer pid

请求方式：post

响应类型：JsonResult< Integer>

3.处理请求

~~~java
@PostMapping("/addFavorites")
public JsonResult<Integer> addFavorites(HttpSession session,Integer pid){
    //从session中取出uid
    Integer uid = getUserIdFromSession(session);
    //执行插入操作并返回fid
    int fid = favoritesService.addFavorites(uid, pid);
    return new JsonResult<>(OK,fid);
}
~~~

#### 前端页面

①给加入收藏按钮绑定单击事件

~~~javascript
//给加入收藏按钮绑定点击事件
$("#btn-add-to-collect").click(function () {
    $.ajax({
        url: "http://localhost:8080/favorites/addFavorites",
        type: "post",
        data: {pid:pid},
        dataType: "json",
        success: function (res) {
            alert("收藏成功！")
        },
        error : function (err) {
            alert("服务器出现错误，加入购物车失败！")
        }
    })
})
~~~

---

### 收藏商品展示

#### 后端-持久层

1.sql语句编写

~~~mysql
#根据uid和收藏商品状态查询收藏的商品信息
select * from t_favorites where uid= #{uid} and status =#{status}
~~~

2.创建mapper文件和定义抽象方法

~~~java
//根据uid和收藏商品状态查询收藏的商品信息
List<Favorites> queryFavoritesByUidAndStatus(Integer uid,Integer status);
~~~

3.编写对应的映射文件

4.单元测试

#### 后端-业务层

1.规划异常，无异常

2.创建service层接口和定义抽象方法

3.编写具体的业务处理逻辑

~~~java
//查询收藏商品的抽象方法
PageInfo<Favorites> queryFavorites(Integer uid, Integer pageNum, Integer pageSize,Integer status);

//根据uid和商品收藏状态查询收藏数据的具体逻辑
@Override
public PageInfo<Favorites> queryFavorites(Integer uid, Integer pageNum,Integer pageSize,Integer status) {
        //开启分页功能，pageNum是当前页，pageSize是每页显示的数据量，这两个值都可以选择让前端传或者自己调整
        PageHelper.startPage(pageNum,pageSize);
        List<Favorites> favorites = favoritesMapper.queryFavoritesByUidAndStatus(uid, status);
        PageInfo<Favorites> pageInfo = new PageInfo<>(favorites);
        return pageInfo;
    }
~~~

#### 后端-控制层

1.不需要处理异常

2.设计请求

请求路径：/favorites/queryFavorites

请求类型：Httpsession session,Integer status

请求方式：get

响应类型：JsonResult< Favorites>

3.处理请求

~~~java
//处理查询收藏商品的请求
@GetMapping("/queryFavorites")
    public JsonResult<PageInfo<Favorites>> queryFavorites(HttpSession session, Integer pageNum,Integer pageSize,Integer status){
        Integer uid = getUserIdFromSession(session);
        PageInfo<Favorites> favorites = favoritesService.queryFavorites(uid, pageNum,pageSize,status);
        return new JsonResult<>(OK,favorites);
    }
~~~

#### 前端页面

- <font color='red'>**js代码较长，到/web/favorites.html下可以查看**</font>

---

### 取消收藏

- 可以选择直接将数据库对应数据删除，但为了测试方便，决定添加一个字段status表示收藏状态

#### 后端-持久层

1.sql语句编写

~~~mysql
update t_favorites set status = #{status} where fid = #{fid} and uid = #{uid}
~~~

2.定义抽象方法

~~~java
//根据收藏商品pid和用户uid取消对应商品收藏
int updateFavoritesStatus(Integer status,Integer fid,Integer uid);
~~~

3.编写对应的mapper映射文件

#### 后端-业务层

1.规划异常，修改状态失败

2.定义抽象方法

3.编写具体业务处理逻辑

~~~java
//根据收藏商品pid和用户uid取消对应商品收藏的抽象方法
int updateFavoritesStatus(Integer status,Integer fid,Integer uid);

//根据收藏商品pid和用户uid取消对应商品收藏的具体逻辑
@Override
    public int updateFavoritesStatus(Integer status, Integer fid, Integer uid) {
        int result = favoritesMapper.updateFavoritesStatus(status, fid, uid);
        if (result == 0){
            throw new UpdateException("服务器异常，取消收藏失败");
        }
        return result;
    }
~~~

#### 后端-控制层

1.异常无需进行处理，前面已经处理过

2.设计请求

请求路径：/favorites/updateStatus

请求参数：HttpSession session，Integer fid，Integer status

请求类型：post

响应类型：JsonResult< void>

3.处理请求

~~~java
//处理取消收藏的请求
@PostMapping("/updateStatus")
public JsonResult<Void> cancelFavorites(HttpSession session,Integer status,Integer fid){
    Integer uid = getUserIdFromSession(session);
    favoritesService.updateFavoritesStatus(status,fid,uid);
    return new JsonResult<>(OK);
}
~~~

#### 前端页面

~~~javascript
//取消收藏
function stopCollect(fid){
    if (confirm("确定要取消对该商品的收藏吗？")){
        $.ajax({
            url: "http://localhost:8080/favorites/updateStatus",
            type: "post",
            data: {fid:fid,status:0},
            dataType: "json",
            success: function (res) {
                alert("取消成功")
                location.reload();
            },
            error : function (err) {
                alert("服务器出现错误，取消失败！")
            }
        })
    }
}
~~~

---

### 加入购物车

- 持久层、业务层、控制层在购物车管理模块已经全部实现

#### 前端页面

- 前端页面只需要将product.html页面的ajax请求进行cv，修改一下传递的参数即可

~~~javascript
//加入购物车
function addCollectToCart(pid,price){
    if (confirm("确定要将此商品加入购物车吗？")){
        $.ajax({
            url: "http://localhost:8080/cart/addCart",
            type: "post",
            data: {pid:pid,price:price,num:1},
            dataType: "json",
            success: function (res) {
                alert("已成功加入购物车，在购物车等您结算哟！")
            },
            error : function (err) {
                alert("服务器出现错误，加入购物车失败！")
            }
        })
    }
}
~~~

---

## 商品搜索

### 模糊搜索

#### 后端-持久层

1.sql语句

~~~mysql
SELECT id,title,sell_point,price,image
FROM t_product 
WHERE STATUS = 1
AND title LIKE '%${title}%' 
ORDER BY priority DESC;
~~~

2.定义抽象方法

~~~java
//根据指定的名称关键字进行模糊查询
List<Product> queryProductByTitle(String title);
~~~

3.编写映射文件

4.单元检测

#### 后端-业务层

1.简单查询无异常不需要规划

2.定义抽象方法

3.编写具体逻辑

~~~java
//根据名称进行模糊查询的抽象方法
PageInfo<Product> queryProductByTitle(Integer pageNum, Integer pageSize,String title);
//根据名称进行模糊查询的具体逻辑
@Override
public PageInfo<Product> queryProductByTitle(Integer pageNum, Integer pageSize,String title) {
    //开启分页功能
    PageHelper.startPage(pageNum,pageSize);、
    //调用持久层方法进行查询
    List<Product> products = productMapper.queryProductByTitle(title);
    //返回分页数据
    return new PageInfo<>(products);
}
~~~

4.单元测试

#### 后端-控制层

1.无异常需要处理

2.设计请求

请求路径：/product/queryByTitle

请求参数：Integer pageNum，Integer pageSize，String title

请求类型：get

响应类型：JsonResult<PageInfo< Product>>

3.处理请求

~~~java
// 处理根据产品关键字进行模糊查询的请求
@GetMapping("/{pageNum}/{pageSize}/{title}")
public JsonResult<PageInfo<Product>> quertByTitle(@PathVariable("pageNum") Integer pageNum,
                                                  @PathVariable("pageSize") Integer pageSize,
                                                  @PathVariable("title") String title){
    PageInfo<Product> lists = productService.queryProductByTitle(pageNum, pageSize, title);
    return new JsonResult<>(OK,lists);
}
~~~

#### 前端页面

- <font color='red'>**前端页面搜索数据的展示、加入购物车、加入收藏基于其他页面疯狂copy，稍微改动即可完成**</font>
- <font color='red'>**同样js代码过长，放在了/web/search.html页面下**</font>

---

## 抽离公共页面

- 抽离头部、页面、中间导航条

①新建一个名为head.html(简称head)的文件，将需要抽离页面的head标签内的全部内容粘贴到

head文件中，并且在抽离页面的原处使用一个div进行占位，可以在任意地方js的load方法进行引入

②新建一个名为middleNavigation.html(简称middle)的文件，将需要抽离页面的导航标签内的内容粘贴到

middle文件中，并且在抽离页面的原处使用一个div进行占位，可以在任意地方js的load方法进行引入

③新建一个名为footer.html(简称footer)的文件，将需要抽离页面的footer标签内的全部内容粘贴到

footer文件中，并且在抽离页面的原处使用一个div进行占位，可以在任意地方js的load方法进行引入

~~~html
<!--头部开始-->
<div class="header"></div>
<!--头部结束-->

<!--中间导航条开始 -->
<div class="middleNavigation"></div>
<!--中间导航条结束-->

<!--页脚开始-->
<div class="footer"></div>
<!--页脚结束-->

<script type="text/javascript">
    $(function ()  {
        //引入公共头部、中间导航条以及页脚
        $(".header").load("components/head.html")
        $(".footer").load("components/footer.html")
        $(".middleNavigation").load("components/middleNavigationBar.html")
    })
</script>
~~~

<font color='red'>**PS：因为是把整体都抽离了，因此有些页面需要一开始就要加载的功能可以使用js代码在**</font>

<font color='red'>		**head或者footer文件进行加载，如下完整的head.html页面所示：**</font>

- 这个部分最为棘手，需要谨慎操作

~~~html
<!--完整的head.html页面代码-->

<!--电脑商城logo-->
<div class="row">
    <div class="col-md-3">
        <a href="index.html">
            <img src="../images/index/stumalllogo.png" />
        </a>
    </div>
    <!--快捷选项-->
    <div class="col-md-9 top-item">
        <ul id="topMenu" class="list-inline pull-right">
            <li><a href="favorites.html"><span class="fa fa-heart"></span>&nbsp;收藏</a></li>
            <li class="li-split">|</li>
            <li><a href="orders.html"><span class="fa fa-file-text"></span>&nbsp;订单</a></li>
            <li class="li-split">|</li>
            <li><a href="cart.html"><span class="fa fa-cart-plus"></span>&nbsp;购物车</a></li>
            <li class="li-split">|</li>
            <li>
                <!--下列列表按钮 ：管理-->
                <div class="btn-group">
                    <button type="button" class="btn btn-link dropdown-toggle" data-toggle="dropdown">
                        <span id="top-dropdown-btn"><span class="fa fa-gears"></span>&nbsp;管理 <span id="menuCaret" class="caret"></span></span>
                    </button>
                    <ul id="uiMenu" class="dropdown-menu top-dropdown-ul" role="menu">
                        <li><a href="password.html">修改密码</a></li>
                        <li><a href="userdata.html">个人资料</a></li>
                        <li><a href="upload.html">上传头像</a></li>
                        <li><a href="address.html">收货管理	</a></li>
                    </ul>
                </div>
            </li>
            <li class="li-split">|</li>
            <li><span class="fa fa-user"></span><a href="login.html" id="loginStatus" ></a></li>
        </ul>
    </div>
</div>

<script>
     $(function () {
         //必须随着页面加载先执行，不然有缓存界面会显示bug
         //未登录状态下改变最上面的管理行
         if (sessionStorage.getItem("user") === null){
         //设置显示登录
         $("#loginStatus").html("&nbsp;&nbsp;" + "登录")
         //移除管理模块的class样式并清空元素
         $("#menuCaret").removeClass("caret")
         $("#uiMenu").removeClass("dropdown-menu top-dropdown-ul").empty()
         }else{ //不为空则已经登录
             $.ajax({
                 url : "http://localhost:8080/user/queryUser" ,
                 type: "get",
                 dataType: "json",
                 success: function (res) {
                     //用户登录了则改变最上面的导航条
                     changeMenu(res)
                 }
             });
         }

     })
     //登录状态下改变最上面的管理行
     function changeMenu(res) {
         let user = res.data;
         //不为空代表已经登录
         if (user.username != null){
             //修改为登录的用户名
             $("#loginStatus").html("&nbsp;" + user.username)
             //添加退出按钮
             let exitStr = "<li class=\"li-split\">|</li>"
                 + "<li>"
                 + "<span class=\"fa fa-sign-out\"></span>"
                 + "<a href=\"javascript:void(0)\" onclick=\"exitLogin()\">&nbsp;退出</a>"
                 + "</li>"
             $("#topMenu").append(exitStr)
             //移除跳转属性
             document.getElementById("loginStatus").removeAttribute("href")
         }
     }

     //退出功能
     function exitLogin(){
         if (sessionStorage.getItem("user") == null){
             alert("尚未登录，请先登录！")
         }else {
             $.ajax({
                 url: "http://localhost:8080/user/exit",
                 type: "get",
                 dataType: "json",
                 success:function (res) {
                     if (res.status === 200){
                         alert("退出成功!")
                         sessionStorage.removeItem("user")
                         location.href = "index.html"
                     }
                 },
                 error:function () {
                     alert("服务器出现未知异常，退出登录失败")
                 }
             })

         }
     }
 </script>
~~~

---

## 引入kaptcha验证码

- 参考资料  ---> [csdn引入kaptcha](https://blog.csdn.net/qq_40065776/article/details/101481607)

①引入依赖包到pom文件

~~~xml
<!-- 引入验证码 kaptcha的依赖  -->
<dependency>
    <groupId>com.github.penggle</groupId>
    <artifactId>kaptcha</artifactId>
    <version>2.3.2</version>
</dependency>
~~~

②创建并编写kaptcha的配置类

~~~java
package top.year21.computerstore.config;
import com.google.code.kaptcha.impl.DefaultKaptcha;
import com.google.code.kaptcha.util.Config;
import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.Properties;

/**
 * @author hcxs1986
 * @version 1.0
 * @description: kaptcha配置类
 * @date 2022/7/24 1:55
 */
@Slf4j
@Configuration
public class KaptchaConfig {
    //kaptcha
    @Bean
    public DefaultKaptcha getKaptcheCode() {
        DefaultKaptcha defaultKaptcha = new DefaultKaptcha();
        Properties properties = new Properties();
        properties.setProperty("kaptcha.border", "no");
        properties.setProperty("kaptcha.textproducer.font.color", "black");
        properties.setProperty("kaptcha.image.width", "100");
        properties.setProperty("kaptcha.image.height", "36");
        properties.setProperty("kaptcha.textproducer.font.size", "30");
        properties.setProperty("kaptcha.obscurificator.impl", "com.google.code.kaptcha.impl.ShadowGimpy");
        properties.setProperty("kaptcha.session.key", "code");
        properties.setProperty("kaptcha.noise.impl", "com.google.code.kaptcha.impl.NoNoise");
        properties.setProperty("kaptcha.background.clear.from", "232,240,254");
        properties.setProperty("kaptcha.background.clear.to", "232,240,254");
        properties.setProperty("kaptcha.textproducer.char.length", "4");
        properties.setProperty("kaptcha.textproducer.font.names", "彩云,宋体,楷体,微软雅黑");
        Config config = new Config(properties);
        defaultKaptcha.setConfig(config);
        return defaultKaptcha;
    }
}

~~~

③创建并编写kaptcha控制层

~~~java
package top.year21.computerstore.controller;

import com.google.code.kaptcha.Constants;
import com.google.code.kaptcha.Producer;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import javax.imageio.ImageIO;
import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.awt.image.BufferedImage;

/**
 * @author hcxs1986
 * @version 1.0
 * @description: kaptcha的控制层,kaptcha调用
 * @date 2022/7/24 1:57
 */
@Slf4j
@RestController
@RequestMapping("/kaptcha")
public class KaptchaController {

    @Autowired
    private Producer producer;

    @GetMapping("/kaptcha-image")
    public void getKaptchaImage(HttpServletRequest request, HttpServletResponse response) throws Exception {
        response.setDateHeader("Expires", 0);
        response.setHeader("Cache-Control", "no-store, no-cache, must-revalidate");
        response.addHeader("Cache-Control", "post-check=0, pre-check=0");
        response.setHeader("Pragma", "no-cache");
        response.setContentType("image/jpeg");
        String capText = producer.createText();
        log.info("******************当前验证码为：{}******************", capText);
        // 将验证码存于session中
        request.getSession().setAttribute(Constants.KAPTCHA_SESSION_KEY, capText);
        BufferedImage bi = producer.createImage(capText);
        ServletOutputStream out = response.getOutputStream();
        // 向页面输出验证码
        ImageIO.write(bi, "jpg", out);
        try {
            // 清空缓存区
            out.flush();
        } finally {
            // 关闭输出流
            out.close();
        }
    }
}
~~~

④在前端调用kaptcha的控制层接口

- 给图片绑定点击事件进行刷新时一定要在请求接口完整的地址上带上一个时间参数

  如下图所示

~~~javascript
<img id="kaptcha" src="http://localhost:8080/kaptcha/kaptcha-image" onclick="reFlashImg('kaptcha')" />
    
//给图片验证码绑定点击事件，刷新验证码
function reFlashImg(imgId) {
    let kaptcha = document.getElementById(imgId)
    kaptcha.src = "http://localhost:8080/kaptcha/kaptcha-image?time="+ new Date();
}   
~~~







---

## 统计业务方法耗时

- 如果想要对某些方法同时添加相同 需求，且在不改变原有的业务逻辑 的基础上进行完成

  那么就是可以使用AOP的切面编程进行处理

### 切面方法

<font color='red'>**切面方法就是在切面类的方法，所谓的切面类就是增强类**</font>

1.切面方法修饰符必须是public

2.切面方法的返回值可以是void或Object，但如果这个方法被@Around环绕通知注解所修饰，那么

​	这个方法必须声明为Object类型，除此之外，随意。

3.切面方法的方法名可以自定义

4.切面方法可以接受参数，参数是ProccedingJoinPoint接口类型的参数，但是被@Around环绕通知

   注解所修饰的方法必须要传递这个参数，除此之外，随意。

### 怎么实现AOP操作？

<font color='red'>**怎么做？**</font>

①导入aop的依赖到pom文件中，不需要版本，springboot会自动进行版本仲裁

~~~xml
<!--    引入aspectj的依赖    -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjtools</artifactId>
</dependency>
~~~

②创建切面类(增强类)，并在这个类中定义切面方法（编写增强逻辑）

③进行通知的配置

~~~java
//这是用于统计业务方法执行的事件的增强类
@Aspect //将当前类标记为切面类，并生成代理对象，底层使用的是动态代理
@Component //将当前类对象的创建和管理交由spring容器维护
public class TimerAspect {
    //ProceedingJoinPoint接口表示指向目标方法的对象
    //切入点表达式解析
    // 第一个*表示不关注方法返回值
    //第二个* top.year21.computerstore.service.impl.* 表示包下的哪个实现类也不关注
    //第三个* 表示哪个方法名字也不关注
    //第四个(..)表示哪个方法中的参数列表也不关注
    //"execution(* top.year21.computerstore.service.*.*(..))"
    @Around("execution(* top.year21.computerstore.service.impl.*.*(..))")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        //先记录业务执行前的时间
        long startedTime  = System.currentTimeMillis();
        Object result = pjp.proceed(); //调用目标方法，例如login方法
         //还可以在这个位置记录一下每个方法的执行名字和时间，并建议一张数据表记录
        //插入数据库
        
        //先记录业务执行前的时间
        long endTime  = System.currentTimeMillis();
        //计算耗时
        System.out.println("业务方法总共耗时：" + (endTime - startedTime));
        return result;
    }
}
~~~

④启动项目，随机访问一个 进行测试


## 汇总信息

1.注册 单元测试报错提示：Invalid bound statement (not found)

说明：本次是由于没有在yml配置文件中设置mapper映射文件对应的位置

解决方法：将mapper映射文件的位置在yml配置文件中进行对应的设置

~~~yml
mybatis:
  mapper-locations: classpath:mybatis/mapper/*.xml
~~~

2.注册 单元测试报错：nested exception is org.apache.ibatis.binding.BindingException: Parameter ‘xxx‘ not found

说明：由于传参的是实体类对象，因此实体类对象不需要用@Param修饰

3.登录 由于还没设计表单就使用postman测试后端业务层接口时，报错提示状态码415，Unsupported Media Type

说明：业务层的控制器方法的形参加了 @RequestBody注解 ，只能解析json类型的数据，而在postman中

​			测试发送的请求Content-Type类型是multipart/form-data; 所以才导致了这个错误

解决方法：将对应的控制器方法形参的 @RequestBody注解去掉即可

4.登录 写完js代码后发现点击事件没有绑定成功

解决方法：原因在于js代码没有设置为在页面加载完成后执行

~~~javascript
//在网页加载完成后执行
 $(function () {})
~~~

5.修改个人资料 的js代码不是很熟悉

val()用于获取标签中的value属性的值

6.在上传头像页面，<font color='red'>**表单使用sumbit提交知道action默认跳转行为如何阻止？**</font> --->[解决表单默认提交跳转行为](https://blog.csdn.net/u013992330/article/details/80085678?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-3-80085678-blog-88689411.pc_relevant_multi_platform_whitelistv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-3-80085678-blog-88689411.pc_relevant_multi_platform_whitelistv2&utm_relevant_index=4)

7.关于@PathVariable、@RequestParam、@Param注解

@PathVariable、@RequestParam用于后端控制层与前端页面交互时获取请求参数使用

@Param是用于后端业务层和持久层交互时，sql语句填充占位符所用

8.js中的serialize()方法、FormData类

serialize()方法：可以将表单数据自动拼接成key=value的形式提交给服务器，但一般提交的都是

​							普通控件类型中的数据(text/password/radio/checkbox)等等

FormData类：将表单中的数据保持原有结构的形式进行提交。

~~~javascript
//先要创建一个Data对象，通过js的选择器找到对应的表单
//[0]是将jquery对象转为dom对象，其中的0表示form表单中的第一个控件，每个标签代表一个控件
//FormData对象能够存储文件类型的数据
Data data = new FormData($("#form")[0]); 

//使用formdata发送ajax请求如下
$.ajax({
    url: "http://localhost:8080/test",
    type: "post",
    data: new FormData($("#form")[0]),
    processData: false, //处理数据的形式，关闭处理数据
    contentType: false,//表示提交数据的形式，关闭默认提交数据的形式
    dataType: "json",
    success: function(res){
        alert(res.status",修改成功")
        //因为它这里后端返回的数据是图片的访问地址
        $("#img").attr("src",res.data)
    },
    error: function(err){
        alert("修改失败")
    }
})
~~~

9.<font color='red'>**如果后端持久层接收的是一个实体类对象的参数，必须在标签中使用parameterType="实体类对象"，**</font>

​	这样才能根据前端传值和实体类对象的属性匹配自动完成注入，不然保存

10.给当一个方法的参数是必须在标签内填写，怎么解决？

使用正则表达式替换，<font color='red'>能够替换的前提是str这个串中必须包含对应的占位符信息</font>

~~~javascript
str = "<a href='javascript:void(0);' onclick='deleteAddress(#{deleteAid},#{isDefault})' "
//使用正则表达式替换获取该地址的aid值，#{deleteAid}只是一个占位符的含义，没其他含义
str = str.replace("#{deleteAid}",address.aid)
str = str.replace("#{isDefault}",address.isDefault)
~~~

11.如何给a标签绑定事件并携带参数呢？

~~~javascript
"<a href='javascript:void(0);' onclick='updateAddress(#{editAid})' >"

//为修改绑定点击事件
function updateAddress(aid){
    //执行跳转到指定页面
    jumpWithParam(aid);
}
//携带数据进行跳转
function jumpWithParam(param){
    //拼接跳转连接并带上需要查询的aid值
    url = "editAddress.html?aid=" + param;
    location.href = url;
}

//在指定页面这么接收
function showThisUserAddress(){
    //接收上一个页面传来的连接
    var hrefUrl = location.href;
    //以url中的"="为截断点，形成一个数组
    var param = hrefUrl.split("=")
    //decodeURI解码得到想要的参数
    var aid = decodeURI(param[1]);
}

~~~

12.<font color='red'>**当在进行多表查询之后，查询的结果集无法和任何实体类进行映射怎么办？**</font>

- 重新创建一个Value Object(值对象)与查询出来的结果集形成映射关系

VO(Value Object)：用于接收无法和任何实体类形成映射关系的vo对象，实际上就是根据结果集的字段，

 								 对应创建一个有相对应属性的一个实体类

13.<font color='red'>**当前端发送的值是多个同名属性时，后端应该怎么接收？**</font>

> 根本没想到前端可以直接在ajax请求的data直接发送cids=5&cids=4&cids=6这样的数据，惊呆了

e.g. http:localhost:8080/cart/queryCids?cids=5&cids=4

后端可以以一个同参数名的数组进行接收

e.g. public JsonResult<List< Cart>> queryCids(Integer[] cids){}

14.持久层需要的数据，如果在业务层的逻辑中可以手动生成，那么在业务层的形参列表中就不必要求输入

15.业务层需要的数据可以根据持久层的抽象方法的形参列表进行对比得出

16.如何阻止form表单使用sumbit默认跳转的解决方式二

- 通过给form表单设置一个id，并使用id选择器给这个form表单绑定一个onsumbit提交事件

~~~javascript
//使用id选择器给这个form表单绑定一个onsumbit提交事件
//检测用户是否已经选择了商品来决定是否放行
function checkIsNotChoose(){
    let chooseNum = $("input[type='checkbox']:checked").length
    //如果chooseNum的值等于0代表用户没任何选择商品不允许跳转
    if (chooseNum === 0){
        alert("请先选择需要结算的购物车商品！！！")
        return false;
    }
}
//参考案例二
<form  id="searchForm"
        onsubmit="return checkIfHaveVal()"
        action= "http://localhost:8080/web/search.html"
        class="form-inline pull-right"
        role="form"></form>

<script type="text/javascript">
function checkIfHaveVal(){
    let val = $("#search").val()
    if (val === '') {
        alert("请先输入搜索内容！")
        return false;
    }
}
</script>
~~~

17.  .children(expr)  例如：children(":eq(4)")

    children()是一个筛选器，顾名思义就是筛选孩子，筛选那些符合条件的孩子。  

    其中children是筛选器的名称，expr是表达式，所有选择器中的表达式都可以用在这，

    比如按标签名"div",按类名".class",按序号":first"等等，

    如果表达式为空，那么返回的是所有的孩子，返回的结果仍为jQuery对象。

18.模糊查询报错<font color='red'>**Could not set parameters for mapping**</font>

模糊查询,只能使用${},若使用#{}，占位符会被解析成？，当中参数里面的一部分，导致报错

如果想要强行使用#{}，只能这么写  like concat('%',#{username},'%') ；

或者 like "%"#{username}"%";

19.<font color='red'>**拦截器白名单失效假象**</font>

表现是在拦截中配置了对某些资源和接口放行，但发现使用浏览器请求还被重定向拦截器指定页面

原因如下：我们访问一个页面时候 **springboot发现我们这个页面不存在自动会跳转至error页面**，

这个时候跳转至error页面其实是被拦截器拦截了，所以会觉得是excludePathPatterns失效了。

我们只需要白名单放行的路径中把error页面排除

~~~java
//注册拦截器并添加拦截规则
registry.addInterceptor(new LoginInterceptor())
    .addPathPatterns("/**")
    //也可以用一个List<String> 来设置排除拦截的资源
    //放行静态资源
    .excludePathPatterns("/web/login.html","/web/index.html",
                         "/web/register.html","/web/product.html",
                         "/web/components/**","/web/search.html",
                         "/user/**","/address/**","/file/**","/district/**",
                         "/images/**","/js/**")
    .excludePathPatterns("/error"); //不放行error页面有可能导致白名单失效假象
~~~

