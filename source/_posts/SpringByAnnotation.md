---
title: Spring注解驱动
date: 2022-05-09 20:39:02
updated: 2022-05-09 20:39:02
tags:
  - Annotation
categories:
  - 框架
keywords: SpringByAnnotation
cover: https://w.wallhaven.cc/full/72/wallhaven-7278dy.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/05/09/SpringByAnotation
---

## 容器

### 注解+配置类创建容器

<font color="red">**本质与xml文件一样，只不过是使用大量注解替代了xml文件的编写**</font>

- 使用配置类代替xml配置文件

~~~java
@Configuration  //告诉Spring这是一个配置类
//扫描sring包下的组件,且排除类型为Controller的注解，不加入此容器
@ComponentScan(basePackages = "spring",includeFilters = {  
    	@ComponentScan.Filter(type = FilterType.ANNOTATION,
                              classes ={Controller.class})}) 
public class SpringConfig {
    @Bean("person")
    //将此方法生成的对象交给spring的IOC容器进行管理
    //对象类型为方法的返回值类型，默认是使用方法名作为id的标识
    public  Person getPerson01(){
        return new Person("张三",18);
    }
}
~~~

测试配置类是否加载成功：


```java
@Test
public void testPerson(){
    //加载配置类完成Spring容器的初始化
    ApplicationContext context  = new AnnotationConfigApplicationContext(SpringConfig.class);
    //获取在Spring容器中已初始化完成指定类型的bean
    Person p = context.getBean(Person.class);
    System.out.println(p.toString());
    //获取指定类型的对象名字
    String[] type = context.getBeanNamesForType(Person.class);
    System.out.println(Arrays.toString(type));
    //获取Spring容器中定义的所有JavaBean 的名称
    String[] names = context.getBeanDefinitionNames();
    for(String name : names){
        System.out.println(name);
        }
}
```

- 扫描包

| 扫描包的设置               | 部分属性说明                                                 |
| :------------------------- | :----------------------------------------------------------- |
| basePackages               | 制定扫描哪个包下的组件                                       |
| excludeFilters             | 按照规则排除扫描哪些类型的组件,不需要加use-default-filters=false，否则扫描不到service和dao层组件 |
| includeFilters             | 按照规则指定扫描哪些类型的组件,需要加use-default-filters=false |
| FilterType.ANNOTATION      | 按照注解进行扫描                                             |
| FilterType.ASSIGNABLE_TYPE | 按照给定的类型扫描                                           |
| FilterType.ASPECTJ         | 使用ASPECTJ表达式，使用较少                                  |
| FilterType.REGEX           | 使用正则表达式匹配                                           |
| FilterType.CUSTOM          | 使用自定义的规则                                             |

~~~java
@Configuration  //告诉Spring这是一个配置类
@ComponentScan(basePackages = "spring",excludeFilters = {
                //指定排除类型为Controller的注解, 类型为BookServiceImpl的类，不加入此容器
//                @ComponentScan.Filter(type = FilterType.ANNOTATION,classes = {Controller.class}),
//                @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE,classes = {BookServiceImpl.class}),
                @ComponentScan.Filter(type = FilterType.CUSTOM,classes = {MyTypeFilter.class})
},useDefaultFilters = false)
public class SpringConfig {
    @Bean("person")
    public  Person getPerson01(){
        return new Person("张三",18);
    }
}
~~~

自定义规则的的演示：

~~~java
public class MyTypeFilter implements TypeFilter {
    /**
     * Description : TODO
     * @date 2022/4/27
     * @param metadataReader 读取当前正在扫描的类的信息
     * @param metadataReaderFactory 可以获取其他任何类的信息
     * @return boolean
     **/
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        //获取当前类的注解信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        //获取当前正在扫描的类的信息
        ClassMetadata classMetadata = metadataReader.getClassMetadata();
        //获取当前类资源(比如类的路径等)
        Resource resource = metadataReader.getResource();
        //获取当前类名
        String className = classMetadata.getClassName();
        System.out.println("类名是：" + className);
        //只要在指定扫描包下的都会进入这个规则的进行匹配，当类名中包含er就会被ioc容器收纳进行管理
        if (className.contains("er")){
            return true;
        }
        return false;
    }
}
~~~

### 组件添加

<font color="red">**组件也是抽象的概念,可以理解为一些符合某种规范的类组合在一起就构成了组件。**</font>

@ComponentScan 扫描指定的包路径下的组件

@Configuration  告诉Spring这是一个配置类

@Scope 标明Bean的作用域

- Scope注解值的4种不同情况：

​		prototype：多实例，每次在调用 getBean 方法时候创建多实例对象

​		singleton：单实例(@Scope的默认值)：在加载完spring配置文件自动创建并放入ioc容器中

​		request：同一次请求创建一个实例

​		session：同一个session创建一个实例

@Lazy   表示延迟加载懒加载，可与@Scope搭配使用，表示在单实例的情况下，在调用getBean的方法时加载Bean对象

@Conditional({Condition接口实现类}) 按照一定的条件进行判断，符合条件创建其对象并加入IOC容器中进行管理

- Conditional注解可以使用在类或者方法上

  ①使用<font color="red">**在方法**</font>：表示这个方法返回的对象bean满足某个Condition接口实现类重写方法的条件才会注册到ioc容器中

  ②使用<font color="red">**在类上**</font>：表示这个类中配置的所有bean满足某个Condition接口实现类重写方法的条件才会注册到ioc容器中

@Import 快速给容器中导入组件 

- @Import的三种用法

  ①@Import(要导入到容器中的组件)  容器中会自动创建该类的对象并进行管理，id默认是全类名

  ②ImportSelector接口的实现类重写方法：返回需要导入的组件的全类名String数组

  ③ImportBeanDefinitionRegistrar接口的实现类重写方法：手动注册bean到容器中

给容器中注册组件的方式：

①包扫描+组件标注注解(@Controller/@Service...)[自己写的类]

②@Bean[导入第三方包的组件]

③@Import[快速给容器中导入组件] 

④使用Spring提供的FactoryBean(工厂bean)

- 两种情况：

  ①默认获取到的工厂bean调用getObject创建的对象

  ②获取工厂Bean本身需要在id前面添加&，id不设置默认为其类名小写

  <font color="red">**注：在Spring整合其他框架大部分都是使用了Spring提供的FactoryBean**</font>

#### Baen的生命周期

①<font color='red'>**构造(对象创建)**</font>

单实例：在容器启动的时候创建对象

多实例：在每次调用getBean方法时候创建

②对象实例化，即完成依赖注入或属性赋值。

注：很<font color='red'>**重要**</font>的一点：<font color='red'>**后置处理器的方法执行一定是发生在<font color='green'>对象实例化(对象创建)和各种属性完成赋值</font>以后，**</font>

<font color='red'>**在显示调用初始化方法的前后添加自己的逻辑代码**</font>

③后置处理器 <font color='red'>**postProcessBeforeInitialization方法**</font>

④<font color='red'>**初始化：在对象创建并完成赋值后，调用初始化方法**</font>

⑤后置处理器<font color='red'> **postProcessAfterInitialization方法**</font>

```java
this();//传入配置类，创建ioc容器
register(annotatedClasses);//注册配置类到ioc容器中
refresh();//refresh()方法对容器进行刷新。spring容器的启动，创建bean，bean的初始化等一系列过程都在这个refresh方法里面

finishBeanFactoryInitialization(beanFactory);//实例化所有的（非懒加载的）单实例bean对象

beanFactory.preInstantiateSingletons();//在这个方法里面遍历beanDefinitionNames并调用其的getBean(beanName)方法

getBean(beanName)//被下方方法重写

getBean(String name);//这个方法内部调用了doGetBean方法

doGetBean{ //doGetBean方法内部调用getSingleton方法
    
getSingleton(
//进入后首先:尝试从缓存中缓存保存的单实例Bean，如果能获取到说明这个bean已被创建过，所有被创建的bean都会放到缓存中
Object singletonObject=this.singletonObjects.get(beanName);
    
//此时因为是第一次调用,所以singletonObject为null,即缓存中获取不到对象，开始创建对象的过程    
if (!typeCheckOnly) {
				markBeanAsCreated(beanName);//该方法会将此beanName标志成"正在创建中"
			}

//获取Bean的定义信息
final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
    
//获取当前Bean依赖的其他bean，确保当前bean依赖的其他bean已经创建完成
String[] dependsOn = mbd.getDependsOn();
//如果存在其他依赖的bean则使用下面方法进行创建
registerDependentBean(dep, beanName);
getBean(dep);
    
//若不存在依赖的bean则判断这个bean是否为单实例，是则开始创建对象
if (mbd.isSingleton()) {    
		sharedInstance = getSingleton(beanName, new ObjectFactory<Object>() {
		@Override //重写了ObjectFactory接口的getObject()方法
		public Object getObject() throws BeansException {//
		try {
		return createBean(beanName, mbd, args);//是则调用这个方法创建对象
		}			
}

//进入createBean(beanName, mbd, args)方法
//尝试InstantiationAwareBeanPostProcessor后置处理器提前执行进行拦截尝试创建一个代理对象
//在resolveBeforeInstantiation方法里，先执行postProcessBeforeInstantiation() 
//如果有返回值，再执行postProcessAfterInitialization() 注意这里的两个方法不是同一个接口中的抽象方法           
Object bean = resolveBeforeInstantiation(beanName, mbdToUse); 
         
//没有返回代理对象则开始执行这个方法，这里开始创建一个真正的bean实例           
doCreateBean(
if (instanceWrapper == null) {
	instanceWrapper = createBeanInstance(beanName, mbd, args);//这里创建当前实例对象并返回给下方方法使用
	}
    
//在这个方法中利用工厂方法或者对象的构造器完成了bean实例的创建
createBeanInstance(beanName, mbd, args);

//将上面创建好的bean单实例对象再次进行包装返回
final Object bean = (instanceWrapper != null ? instanceWrapper.getWrappedInstance() : null);

//这个部分不一定发生 仅仅是提供对bean的定义进行修改的功能    
// Allow post-processors to modify the merged bean definition.
//调用MergedBeanDefinitionPostProcessors的对应处理器方法
applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);

//这个部分不一定发生    
//可以解决创建对象的循环依赖问题
if (earlySingletonExposure) {//判断当前beanDefinition是否是单例且是允许提前暴露引用且是正在被创建,
    //满足条件则调用getEarlyBeanReference方法
	addSingletonFactory(beanName, new ObjectFactory<Object>() {
	@Override
	public Object getObject() throws BeansException {
		return getEarlyBeanReference(beanName, mbd, bean);//返回exposedObject对象，实际上为正在被的对象
		}
});
}

//对bean对象属性进行赋值，相当于Bean的生命周期第二步    
populateBean(beanName, mbd, instanceWrapper){
//而在这个方法里面，在赋值之前 先获取了属于InstantiationAwareBeanPostProcesso的后置处理器
//并执行了这个处理器的postProcessAfterInstantiation()方法
    if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
		for (BeanPostProcessor bp : getBeanPostProcessors()) {
		if (bp instanceof InstantiationAwareBeanPostProcessor) {
		InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
		if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName)) {
		continueWithPropertyPopulation = false;
		break;
					}
				}
                
//再次获取了属于InstantiationAwareBeanPostProcessor的后置处理器
//并执行了这个处理器的postProcessPropertyValues()方法 
if (hasInstAwareBpps) {
		for (BeanPostProcessor bp : getBeanPostProcessors()) {
		if (bp instanceof InstantiationAwareBeanPostProcessor) {
		InstantiationAwareBeanPostProcessor ibp = (InstantiationAwareBeanPostProcessor) bp;
			pvs = ibp.postProcessPropertyValues(pvs, filteredPds,bw.getWrappedInstance(),beanName);
			if (pvs == null) {
				return;
						}
					}
				}
			} 
            
//这一步才真正应用Bean属性的值，上面的步骤都发生在赋值之前：为属性利用setter方法等进行赋值
applyPropertyValues(beanName, mbd, bw, pvs);          
}

//后置处理器的两个方法和调用的初始化方法都是在这个initialzeBean中完成的
exposedObject = initializeBean(beanName, exposedObject, mbd){
 
//执行实现了aware接口的方法    
invokeAwareMethods(beanName, bean);  
    
//遍历得到容器中所有的BeanPostProcessor：挨个执行BeforeInitialization，一旦返回null，不再执行后续的，跳出循环
applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName)//后置处理器的before方法

//执行@PostConstruct注解标识的，InitializingBean接口实现类等自定义的初始化方法    
invokeInitMethods(beanName, wrappedBean, mbd);
    
//bean后置处理器的after方法，里面的执行流程和bean后置处理器的before方法差不多的执行流程
applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName)
};
)
    
//在bean单实例对象实例化和初始化完成之后注册bean的销毁方法
registerDisposableBeanIfNecessary(beanName, bean, mbd); 
        
//在doCreateBean()方法执行完成后就创建了bean对象
//通过下面的方法将这个bean添加到SingletonObjects缓存中        
addSingleton(beanName, singletonObject); 
        
//在所有的Bean都利用getBean创建完成之后
//再通过遍历每一个bean的名字判断bean是否符合SmartInitializingSingleton接口的
//符合则执行下方的afterSingletonsInstantiated()方法       
if (singletonInstance instanceof SmartInitializingSingleton) {
final SmartInitializingSingleton smartSingleton = (SmartInitializingSingleton) singletonInstance;    
smartSingleton.afterSingletonsInstantiated();    
}
```

⑥获得对象

⑦<font color='red'>**销毁**</font>：

​	单实例bean在容器关闭之后调用销毁方法

​	多实例bean，容器并不会管理这个bean，只负责创建bean，因此不会调用销毁方法

2.指定初始化和销毁方法的方式:

​	①通过@Bean注解的属性值指定init-method和destory-method

​	②通过Bean实现InitializingBean接口(定义初始化逻辑)，DisposableBean接口(定义销毁逻辑)

​	③使用JSR250

​		通过@PostConstruct注解，在bean创建和属性赋值完成后进行初始化方法

​		通过@PreDestroy注解，在容器对进行bean销毁之前执行销毁方法

​	④使用BeanPostProcessor接口：bean的后置处理器

​		在bean初始化前后进行一些处理操作

​		postProcessBeforeInitialization：在初始化方法之前调用

​		postProcessAfterInitialization：在初始化方法之后调用

3.BeanPostProcessor在Spring底层的使用

<font color='red'>**bean赋值，注入其他组件，@Autowired，生命周期注解功能，@Async等等都是使用BeanPostProcessor完成的**</font>

①ApplicationContextAwareProcessor 后置处理器的作用：

​	当应用程序定义的Bean实现ApplicationContextAware接口时注入ApplicationContext对象。

②BeanValidationPostProcessor 后置处理器的作用：常用于做数据校验

③InstantiationAwareBeanPostProcessor 后置处理器的作用：

​	用于处理@PostConstruct和@PerDestroy注解等

---

### 组件赋值

- 1.使用@Value赋值

  ①基本的数值

  ②可以写SpEL，#{}

  ③可以写${},取出配置文件properties中的值

~~~java
//使用@PropertySource读取外部配置文件中key和value保存到运行的环境变量中
//加载完外部的配置文件以后再使用${}取出配置文件的值
@PropertySource("classpath:/person.properties")
@Configuration
public class SpringConfig3 {
    @Bean
    public Person person(){
        return new Person();
    }
}

	@Value("test")
    private String name;
    @Value("#{20-2}")
    private int age;
    @Value("${person.email}")
    private String email;
~~~

- 2.使用@Autowired注入

  ①默认优先按照类型取容器中找对应的组件context.getBean(BookService.class)

  ②如果找到多个相同类型的值，再将属性的名称作为组件的id去容器中查找

  ③@Qualifier("bookDao2")，使用@Qualifier指定需要装配的组件的id，而不是使用属性名

  ④使用自动装配则要求容器内必须有这个组件，否则报错，但可以使用@Autowired(require=false)来避免报错

  ⑤@Primary，让spring自动装配时默认使用首选的bean，也可以继续使用@Qualifier指定装配哪个

 以上都是Spring支持的注解，Spring还支持使用@Resource(JSR250)和Inject(JSR303)[java规范的注解]

​		@Resource：可以和@Autowired一样实现自动装配，但是默认使用组件名称进行装配的

​								但不支持@Primary注解和@Autowired(require=false)

​		 @Inject：需要导入依赖，和Autowired功能一样，但也没有require=false的属性



<font color='red'>**关于@Autowired的补充：**</font>

@Autowired注解可以使用在方法上、构造器上、属性上、参数上，但都是从容器中获取参数组件的值

使用在方法上：@Bean+方法参数，从容器中获取，默认省略@Autowired

使用在构造器上：如果组件内只有一个有参构造器，这个有参构造器的@Autowired可以省略

使用在参数上：也可以省略@Autowired，默认从容器中获取组件的值

- 3.自定义组件想要使用Spring容器底层的一些组件需要实现xxxAware：

  ​	<font color='red'>**自定义组件实现xxxAware**</font>：在创建对象的时候，会调用接口规定的方法注入相关的组件：Aware

​	<font color='red'>		**作用是：可以把Spring底层的一些组件注入到自己定义的bean中去，而这些bean之所以能注入都是使用了相应的xxxProcessor**</font>

​			例如：ApplicationContextAware ==> ApplicationContextAwareProcessor

- 4.Profile：Spring提供的根据不同环境：动态的激活和切换一系列的组件功能

  @Profile：指定组件在那个环境的情况下才能被注册到容器中，不指定则在任何环境下都能注册这些组件

  <font color='red'>**方法上加了环境标识的bean，只有在对应的环境下才能被注册到容器中，默认是default环境**</font>

  <font color='red'>**如果是写在类上,只有符合指定的环境，整个类中的bean才能注册到容器中**</font>

  **如何切换数据源：**

  ①使用命令行参数，在虚拟机参数位置设置：-Dspring.profiles.active=对应环境的名称

​		②代码的方式

~~~java
@Test
    public void test(){
        //1.创建一个applicationContext对象
        AnnotationConfigApplicationContext context =  new AnnotationConfigApplicationContext();
        //2.在容器还没启动创建其他bean之前设置激活的环境，可以设置多个
        context.getEnvironment().setActiveProfiles("dev");
        //3.注册主配置类
        context.register(ProfileConfig.class);
        //4.启动刷新容器
        context.refresh();

        String[] names = context.getBeanNamesForType(DataSource.class);
        for (int i = 0; i < names.length; i++) {
            System.out.println(names[i]);
        }
    }
~~~

### AOP

1.通知类型：

①前置通知(@Before)：在增强方法前执行

②后置通知(@AfterReturning)：在增强方法后执行

③环绕通知(@Around)：在增强方法前后分别执行

④异常通知(@AfterThrowing)：在增强方法出现异常后执行

⑤最终通知(@After)：无论增强方法怎么样，都会执行

- @After在方法调用之后执行
- @AfterReturning表示在返回值后面执行

2.使用注解进行Aop操作的流程

①创建<font color='red'>**被增强类，内部定义被增强的方法**  </font>

②创建<font color='red'>**增强类，配置切入点表达式(execution)和各种通知**</font>，<font color='red'>**@Pointcut**</font>注解抽取相同切入点

③将<font color='red'>**被增强类或增强类的加入ioc容器**</font>

④在增强类上设置注解<font color='red'>**@Aspsct**</font>，告诉Spring这是个切面类

⑤在配置类上设置注解<font color='red'>**@EnableAspectJAutoProxy 开启基于注解的aop模式** </font>

-  在Spring中很多的 @EnableXXX的注解，它的意思都是表示开启什么东西

~~~java
@Aspect //告诉Spring这是一个切面类
public class MathLogAop {

    //抽取相同切入点
    @Pointcut(value = "execution(public int spring.aop.Math.*(..))")
    public void point(){
    }

    @Before("point()")//前置通知
    public void showExecute(JoinPoint joinPoint){//joinPoint可以获取被执行的方法的各种信息
        System.out.println(joinPoint.getSignature().getName() + "业务方法被调用,参数是" + Arrays.asList(joinPoint.getArgs()));
    }

    //returning可以获取返回的参数值用方法形参里的result来接收
    @AfterReturning(value = "point()",returning = "result")//后置通知
    public void showExecuteReturn(JoinPoint joinPoint,Object result){//多参数时，JoinPoint必须在参数列表的第一位，否则报错
        System.out.println(joinPoint.getSignature().getName() + "业务方法正常执行,返回值是" + result);
    }

    //throwing可以获取异常的信息用方法形参里的exception来接收
    @AfterThrowing(value = "point()",throwing = "exception")//异常通知
    public void showExecuteException(Exception exception){
        System.out.println("业务方法出现异常，异常信息是" + exception);
    }

    @After("point()")//最终通知，也叫返回通知
    public void showExecuteEnd(JoinPoint joinPoint){
        System.out.println(joinPoint.getSignature().getName()+"业务方法调用结束");
    }
}
~~~

#### Aop实现的原理

由@EnableAspectJAutoProxy注解快速导入AspectJAutoProxyRegistrar组件，

此组件实现了ImportBeanDefinitionRegistrar接口，在重写的方法中通过调用

registerAspectJAnnotationAutoProxyCreatorIfNecessary(registry)

然后在这个方法定义了一个将注册的bean定义成了一个名为AnnotationAwareAspectJAutoProxyCreator的BeanDefinition，

通过registerBeanDefinitions在容器中注册了一个beanName为internalAutoProxyCreator，类型是

AnnotationAwareAspectJAutoProxyCreator的bean

<font color='red'>**总而言之，就是通过@EnableAspectJAutoProxy注解在ioc容器中注册了类型是AnnotationAwareAspectJAutoProxyCreator的后置处理器组件**</font>

这里的注册 Bean 是指将  Bean定义成BeanDefinition，之后放入Spring容器中，

我们常说的容器其实就是 Beanfactory 中的一个 Map，key 是 Bean 的名称，value 是 Bean 对应的 BeanDefinition，

- AnnotationAwareAspectJAutoProxyCreator的继承树：

  <font color='red'>**AnnotationAwareAspectJAutoProxyCreator**</font>

  ​	->extends  AspectJAwareAdvisorAutoProxyCreator

  ​		->extends  AbstractAdvisorAutoProxyCreator

  ​			->extends  AbstractAutoProxyCreator

  ​				<font color='red'>**->implements SmartInstantiationAwareBeanPostProcessor, BeanFactoryAware**</font>

​	<font color='red'>**因此只需要关注后置处理器（在bean初始化完成前后做的事情）、自动装配BeanFactory**</font>

##### 后置处理器的注册和创建

创建和注册AnnotationAwareAspectJAutoProxyCreator后置处理器的流程：

​	①传入配置类，创建ioc容器

​	②注册配置类，调用refresh()刷新容器

​	③registerBeanPostProcessors(beanFactory);注册bean的后置处理器来拦截bean的创建		

​			Ⅰ.先获取ioc容器已经定义了的需要创建对象的所有BeanPostProcessor

​			Ⅱ.给容器中添加别的BeanPostProcessor

​			Ⅲ.取得所有的后置处理器

​			Ⅳ.按照类型匹配优先注册实现了PriorityOrdered接口的BeanPostProcessor

​			Ⅴ.再注册实现了Ordered接口的BeanPostProcessor

​			Ⅵ.最后注册所有常规的BeanPostProcessor

​			Ⅶ.注册BeanPostProcessor，实际上就是在容器中创建BeanPostProcessor的对象

​					创建定义名为internalAutoProxyCreator的BeanPostProcessor，类型是AnnotationAwareAspectJAutoProxyCreator

​					创建Bean的实例(实例化AnnotationAwareAspectJAutoProxyCreator类型的bean)

​					 populateBean(beanName, mbd, instanceWrapper); 给刚刚实例化完成的bean进行属性赋值


​					 initializeBean(beanName, exposedObject, mbd); 对bean进行初始化

​							invokeAwareMethods()；处理Aware接口的方法回调

​							applyBeanPostProcessorsBeforeInitialization()：应用后置处理器的before方法

​							invokeInitMethods()；执行自定义的初始化方法

​							applyBeanPostProcessorsAfterInitialization()：应用后置处理器的After方法

​					至此，BeanPostProcessor(AnnotationAwareAspectJAutoProxyCreator)创建成功

​				Ⅷ.把BeanPostPrpcessor添加到BeanFactory中，beanFactory.addBeanPostPrpcessor(postProccessor)

##### 后置处理器的执行时机

- <font color='red'>**一句话概括：【在所有bean创建对象之前进行拦截判断是否能从缓存中直接返回一个代理对象，不能再开始创建对象】**</font>

<font color='red'>**AnnotationAwareAspectJAutoProxyCreator后置处理器是这个类型的  ==>  InstantiationAwareBeanPostProcessor，**</font>

因此后置处理器方法有所不同

执行过程：

1.遍历获取容器中已经定义好的所有bean的名字，依次创建对象；

getBean() -> doGetBean() -> getSingleton()

先从缓存中获取当前bean，如果能获取到，说明bean是已经创建了的，直接使用，否则进行创建

2.createBean();	创建bean 

<font color='red'>**【AnnotationAwareAspectJAutoProxyCreator会在任何bean创建之前进行先尝试返回bean的实例，**</font>

<font color='red'>**会调用 postProcessBeforeInstantiation()】**</font>

<font color='red'>**【InstantiationAwareBeanPostProcessor 是在bean对象实例化之前尝试用后置处理器返回对象，也就是创建对象之前进行调用】**</font>

①resolveBeforeInstantiation(beanName, mbdToUse)；解析BeforeBeforeInstantiation方法：

希望后置处理器在此能返回一个代理对象，如果能返回就直接使用，

在bean = applyBeanPostProcessorsBeforeInstantiation(targetType, beanName)方法中

获取所有的后置处理器，判断是否有属于InstantiationAwareBeanPostProcessor，有则执行

postProcessBeforeInstantiation(beanClass, beanName);

bean = applyBeanPostProcessorsAfterInitialization(bean, beanName);

不能就进行创建(即调用doCreateBean)

②doCreateBean(beanName,mdbToUse,args);这才是真正创建bean的过程，和上面③.Ⅶ的流程差不多

##### 后置处理器创建Aop代理对象

- 在每一个bean创建之前都会被AnnotationAwareAspectJAutoProxyCreator拦截并调用其postProcessBeforeInstantiation方法

  目前只关心两个关键bean的创建：Math和MathLogAop

postProcessBeforeInstantiation方法：

①判断当前bean是否在advisedBeans(这里面保存了所有需要被增强的bean)中

②判断当前的bean是否是基础类型Advice、Pointcut、Advisor、AopInfrastructureBean接口的实现类或者是否是切面(@Aspect)

③是否应该被跳过

​		Ⅰ获取候选的增强器(增强类里面的通知方法) List<Advisor> candidateAdvisors

​			Ⅰ每一个封装的增强器都是InstantiationModelAwarePointcutAdvisor；

​			Ⅱ判断每一个增强器是否是AspectJPointcutAdvisor：是则返回true

​		永远都返回false

对象创建完成，调用下方的方法

postProcessAfterInitialization方法：

return wrapIfNecessary(bean, beanName, cacheKey);//在需要包装的情况下返回

①获取当前bean的所有增强器和增强方法 	

​	Ⅰ找到候选的所有增强器(找那些通知方法是需要被应用的当前bean的)

​	Ⅱ获取到能在bean使用的增强器

​	Ⅲ给增强器排序

②保存当前bean在advisedBeans中，this.advisedBeans.put(cacheKey, Boolean.TRUE);

③如果当前bean需要被增强就创建代理对象  Object proxy = createProxy

​	Ⅰ获取所有增强器(增强类中的方法)

​	 Ⅱ并保存到ProxyFactory中

​	Ⅲ创建代理对象：Spring自动决定

​			JdkDynamicAopProxy(config);jdk动态代理；

​			ObjenesisCglibAopProxy(config);cglib的动态代理

④给容器中返回当前组件使用cglib增强的代理对象

⑤当容器中获取到这个组件的代理对象，执行目标方法的时候就会执行通知方法的流程

##### Aop代理对象执行目标方法

<font color='red'>**可以理解为从代理对象执行的目标方法中获取拦截器链，有则创建一个方法调用，传入相关的信息，没有则直接调用目标方法**</font>

容器中保存了组件的代理对象(cglib增强后的对象)，这个对象里面保存了详细的信息(增强器，目标对象等等)

①CglibAopProxy.intercept();拦截目标方法的执行

②获取代理对象将要执行的目标方法的拦截器链;

List[Object] chain = this.advised.getInterceptorsAndDynamicInterceptionAdvice(method, targetClass);

​		Ⅰ.List[Object]  interceptorList = new ArrayList 保存所有的拦截器(共有5个，一个默认的ExposeInvocationInterceptor 和 4个增强器)

​		Ⅱ.遍历所有的增强器，将每一个增强器度转为Interceptor；	

| Interceptor[] interceptors = registry.getInterceptors(advisor)方法中的执行： |
| :----------------------------------------------------------- |
| 创建List[MethodInterceptor]，获得当前的增强器                |
| 判断增强器类型，如果是MethodInterceptor，直接加入到集合中    |
| 如果不是，使用AdvisorAdapter适配器将增强器转为MethodInterceptor |
| 转换完成将此List转换为MethodInterceptor类型的数组并返回      |

③如果没有拦截器链，则直接执行目标方法

**拦截器链（每一个通知方法又被包装为方法拦截器，利用MethodInterceptor机制）**

④如果有拦截器链，将需要执行的目标对象，目标方法，拦截器链等信息传入创建一个CglibMethodInvocation对象

并调用Object retVal = mi.proceed()方法执行,此时proceed()的调用者就是刚刚创建的CglibMethodInvocation这个对象

##### **拦截器链的执行**

- currentInterceptorIndex记录当前拦截器的索引

```java
//获取相应索引位置上的拦截器
Object interceptorOrInterceptionAdvice =
      this.interceptorsAndDynamicMethodMatchers.get(++this.currentInterceptorIndex);
//判断拦截器的类型
if (dm.methodMatcher.matches(this.method, this.targetClass, this.arguments)) {
				return dm.interceptor.invoke(this);
			}
else {
			return ((MethodInterceptor) interceptorOrInterceptionAdvice).invoke(this);
		}
```

①如果没有拦截器执行就执行目标方法，或拦截器的索引和拦截器数组-1大小一样(执行到了最后的一个拦截器)

②链式获取每一个拦截器，拦截器执行invoke方法，每一个拦截器等待下一个拦截器执行完成返回以后再来执行

​	**通过拦截器链的机制，保证通知方法与目标方法的执行顺序**

**![拦截器链执行顺序](https://s1.ax1x.com/2022/05/09/OJtQjU.png)**

#### **Aop原理总结**

1.@EnableAspectJAutoProxy 开启AOP功能

- **@EnableAspectJAutoProxy 会给容器中注册一个组件 AnnotationAwareAspectJAutoProxyCreator**

- **AnnotationAwareAspectJAutoProxyCreator是一个后置处理器；**

2.容器的创建流程：

​		①registerBeanPostProcessors（）注册后置处理器；创建AnnotationAwareAspectJAutoProxyCreator对象

​		②finishBeanFactoryInitialization（）实例化剩下的单实例bean

​				Ⅰ.创建业务逻辑组件和切面组件

​				Ⅱ.AnnotationAwareAspectJAutoProxyCreator拦截组件的创建过程

​				Ⅲ.组件创建完之后，判断组件是否需要增强

​						需要增强：切面的通知方法，包装成增强器（Advisor）;给业务逻辑组件创建一个代理对象（cglib）

3.执行目标方法：

​		①代理对象执行目标方法

​		②CglibAopProxy.intercept()

​			Ⅰ.得到目标方法的拦截器链（增强器包装成拦截器MethodInterceptor）

​			Ⅱ.利用拦截器的链式机制，依次进入每一个拦截器进行执行；

​			Ⅲ.效果：

​				**正常执行：前置通知-》目标方法-》后置通知-》最终通知**

​				**出现异常：前置通知-》目标方法-》最终通知-》异常通知**

### **声明式事务**

**@Transactional 表示当前方法是个事务方法**

**@EnableTransactionManagement 开启基于注解的事务管理功能**

基于注解模式开启事务的步骤：

①在配置类上开启基于注解的事务管理功能

②将@Transactional 注解标注在需要进行事务管理的方法上

③必须在容器中注册事务管理器(@Bean等方法)

#### **注解实现事务管理的原理**

1.@EnableTransactionManagement注解通过@Import向容器中导入TransactionManagementConfigurationSelector组件

2.通过判断Advice.proxy分别导入

​	AutoProxyRegistrar：给容器中注册InfrastructureAdvisorAutoProxyCreator 后置处理器组件

作用：利用后置处理器的机制在对象创建以后，包装对象，返回一个代理对象，代理对象执行方法利用拦截器进行拦截

​	ProxyTransactionManagementConfiguration组件

作用：①给容器中注册事务增强器

​				Ⅰ.事务增强器中有一个事务属性源的对象，new AnnotationTransactionAttributeSource()可以获取所有事务属性。

​				Ⅱ.事务拦截器：transactionInterceptor保存了事务属性信息，事务管理器，同时它还是个MethodInterceptor，

​										 	在目标方法执行的时候执行拦截器链，而这里的拦截器链只有一个拦截器就是事务拦截器

​											 事务拦截器所做的工作：

​												第一，获取事务相关的属性，

​												第二，再获取事务管理器(如果是事先没有任何设置则默认按照PlatformTransactionManager类型获取一个)

​												第三，执行目标方法：

​														如果异常，获取事务管理器，利用事务管理器回滚操作

​														如果正常，利用事务管理器，提交事务

---

## 扩展原理

### BeanFactoryPostProcessor

BeanPostProcessor的执行时机：它是bean的后置处理器，在bean的创建对象初始化前后进行工作

BeanFactoryPostProcessor的执行时机：<font color='red'>**它是beanFactory的后置处理器，在BeanFactory完成标准初始化之后进行拦截工作，**</font>

​																		<font color='red'>**在所有定义的bean被加载到beanFactory，但还没有进行实例化之前**</font>

执行流程：

1.创建ioc容器对象

2.invokeBeanFactoryPostProcessors(beanFactory) 执行BeanFactoryPostProcessor；

​	①直接在BeanFactory找到所有类型是BeanFactoryPostProcessor的组件并执行相应的方法

​	②在实例化其他组件之前执行

~~~java
this();
register(annotatedClasses); //1.创建ioc容器对象
refresh();

//refresh()里面各种调用方法执行到
invokeBeanFactoryPostProcessors(){
//按照类型获取所有的BeanFactoryPostProcessor组件的名称
String[] postProcessorNames =
				beanFactory.getBeanNamesForType(BeanFactoryPostProcessor.class) 
//遍历获取的组件名称根据实现的不同接口分别放进不同的集合中
for (String ppName : postProcessorNames) {
    else {
		nonOrderedPostProcessorNames.add(ppName);
	}
	}
}

//新建一个nonOrderedPostProcessors集合放进所有符合此类型的组件名称
List<BeanFactoryPostProcessor> nonOrderedPostProcessors = new ArrayList<BeanFactoryPostProcessor>();
		for (String postProcessorName : nonOrderedPostProcessorNames) {
			nonOrderedPostProcessors.add(beanFactory.getBean(postProcessorName, BeanFactoryPostProcessor.class));
		}
//2.遍历此nonOrderedPostProcessors挨个执行postProcessBeanFactory方法
invokeBeanFactoryPostProcessors(nonOrderedPostProcessors, beanFactory);

~~~

### BeanDefinitionRegistryPostProcessor

- <font color='red'>**继承至BeanFactoryPostProcessor，是BeanFactoryPostProcessor的子接口**</font>

执行时机：<font color='red'>**在所有定义的bean将要被加载到BeanFactory，bean没有进行实例化之前**</font>

执行顺序：1.创建ioc容器对象

​					2.refresh()  --> invokeBeanFactoryPostProcessors()

​					3.从容器中获取所有的BeanDefinitionRegistryPostProcessor组件

​							①依次除发所有的postProcessorBeanDefinitionRegistry()方法

​							②再来触发postProcessBeanFactory()方法

​					4.再从容器中找到BeanFactoryPostProcessor组件，依次触发()方法

### ApplicationListener

ApplicationListener：监听容器中发布的事件，事件驱动模型开发

```java
//监听ApplicationEvent及其下面的子事件
public interface ApplicationListener<E extends ApplicationEvent> extends EventListener
```

一、监听器的执行：

①方法一：创建一个ApplicationListener接口的实现类来监听某个事件(必须是ApplicationEvent及其子类)

​	方法二：使用注解@EventListener,其通过EventListenerMethodProcessor后置处理器解析标注了此注解

②将此实现类加入到ioc容器

③只有容器中有相关事件的发布，监听器就被触发

​	ContextRefreshedEvent：容器刷新完成就会触发这个事件

​	ContextClosedEvent：容器关闭时触发这个事件

④发布一个自定义事件

​	context.publishEvent()

二、执行原理：

1.创建ioc容器对象，refresh()

2.finishRefresh()；容器完成刷新

3.在publishEvent(new ContextRefreshedEvent(this))方法里面;

​		①获取事件的多播器(派发器)，getApplicationEventMulticaster()

​		 ②multicastEvent派发事件

​		 ③获取所有支持类型的ApplicationListener

​			for (final ApplicationListener<?> listener : getApplicationListeners(event, type)) {

​			Ⅰ.如果Excutor不为空，则可以支持使用Executor进行异步派发事件 Executor executor = getTaskExecutor();

​			 Ⅱ.如果Excutor为空，则使用同步的方式直接执行listener方法 invokeListener(listener, event);

​					拿到listener回调onApplicationEvent方法

三、事件多播器的获取：

①创建ioc容器对象

②initApplicationEventMulticaster();初始化多播器

​	两种情况：第一种，先去容器中尝试获取id=“applicationEventMulticaster”的组件

​						第二种，如果不存在则创建一个new SimpleApplicationEventMulticaster(beanFactory)

​										并加入到容器中，则在其他组件派发事件自动注入这个组件

四、容器中的监听器的获取：

①创建ioc容器对象

②registerListeners();

​	从容器中获取所有的监听器并注册到applicationEventMulticastera派发器中

~~~java
String[] listenerBeanNames = getBeanNamesForType(ApplicationListener.class, true, false);
	for (String listenerBeanName : listenerBeanNames) {
		getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName);
		}
~~~

五、<font color='red'>**EventListenerMethodProcessor专用解析@EventListener注解，实现了SmartInitializingSingleton的接口**</font>

SmartInitializingSingleton接口的执行原理：

①创建ioc容器对象并refresh()

②finishBeanFactoryInitialization(beanFactory);实例化非延迟加载的单实例bean

​	Ⅰ.先创建所有的单实例bean：getBean();

​	Ⅱ.获取所有创建好的单实例bean，判断是否是SmartInitializingSingleton类型的：

​		符合则调用afterSingletonsInstantiated()方法

​		在afterSingletonsInstantiated()方法中获取所有类型的bean对象并逐个遍历处理processBean(factories, beanName, type);

​		在processBean()方法中判断每一个bean的方法是否标注了@EventListener注解，若有则通过EventListenerFactory工厂为这个

​		方法创建一个applicationListener对象，最后将这个对象添加到ioc容器中

​	 Ⅲ.执行发布事件方法，剩下的流程执行原理第三步无异

## Spring容器创建过程

1.spring容器的refresh()方法	创建刷新

①prepareRefresh(); 刷新前的一些预处理

​	Ⅰ.initPropertySources(); 初始化部分属性设置；默认留给子类做扩展用

​	Ⅱ.getEnvironment().validateRequiredProperties()；验证必须的属性文件数据

​	Ⅲ.earlyApplicationEvents = new LinkedHashSet<ApplicationEvent>();收集部分早期事件，当多播器初始化完成后立即发布

②ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();获得bean工厂

​	Ⅰ.refreshBeanFactory();刷新BeanFactory

​			this.beanFactory.setSerializationId(getId()); 给当前的beanFactory设置一个id

​	Ⅱ.getBeanFactory() <font color='red'>**通过AnnotationConfigApplicationContext的父类**</font>

​		<font color='red'>**GenericApplicationContext的无参构造器中获取创建好的beanFactory对象**</font>

​	Ⅲ.将刚刚获得的BeanFactory对象【DefaultListableBeanFactory】，此时的BeanFactory只有默认内容，什么都没做设置

③prepareBeanFactory(beanFactory);对刚刚获得的默认BeanFactory进行一些设置

​	Ⅰ.设置BeanFactory的类加载器、表达式解析器等

​	Ⅱ.添加部分的后置处理器【ApplicationContextAwareProcessor】

​	Ⅲ.设置忽略自动装配的接口

​	Ⅳ.注册可以解析的自动装配，能够在任何组件中自动注入的：BeanFactory，ApplicationContext

​	Ⅴ.添加BeanPostProcessor【ApplicationListenerDetector】

​	Ⅵ.添加编译时的AspectJ

​	Ⅶ.给BeanFactory中注册一些能用的组件

④postProcessBeanFactory(beanFactory);在BeanFactory完成部分设置后进行一些后置处理工作，一般留给子类重写做扩展用

⑤invokeBeanFactoryPostProcessors(beanFactory); 在这里面执行两个后置处理器

BeanDefinitionRegistryPostProcessor 的postProcessBeanDefinitionRegistry()方法

BeanDefinitionRegistryPostProcessor的postProcessBeanFactory()方法

BeanFactoryPostProcessor的postProcessBeanFactory()方法

​	Ⅰ.获取所有的BeanDefinitionRegistryPostProcessor

​	Ⅱ.按照实现PriorityOrdered, Ordered接口, 没实现接口的顺序进行注册并遍历执行对应的postProcessBeanDefinitionRegistry

​	Ⅲ.获取所有的BeanFactoryPostProcessor

​	Ⅳ.按照实现PriorityOrdered, Ordered接口, 没实现接口的顺序进行注册并遍历执行对应的postProcessBeanFactory

⑥registerBeanPostProcessors(beanFactory); 注册其他的后置处理器

​	<font color='red'>**不同接口类型的BeanPostProcessor在Bean创建前后执行的时机都不一样**</font>

​	BeanPostProcessor：在bean对象实例化和属性赋值进行初始化前后进行调用

​	DestructionAwareBeanPostProcessor：在bean对象销毁之前进行工作

​	InstantiationAwareBeanPostProcessor：在bean对象实例化前后进行拦截工作

​	SmartInstantiationAwareBeanPostProcessor：在bean创建对象之前进行拦截工作

​	MergedBeanDefinitionPostProcessor【internalPostProcessors】：在创建出Bean的单实例对象后，初始化之前进行一些处理工作

​	Ⅰ.获取所有的BeanPostProcessor；所有的后置处理器都可以通过PriorityOrdered, Ordered接口执行优先级

​	Ⅱ.根据实现的不同接口放进不同的后置处理器集合中

​	Ⅲ.优先注册实现PriorityOrdered的BeanPostProcessor，其次实现Ordered，然后没实现任何接口的，

​		最后注册属于internalPostProcessors类型的后置处理器

​	Ⅳ.最后再注册一个ApplicationListenerDetector，来在bean创建完成后检测是否是ApplicationListener，

​			如果是就执行this.applicationContext.addApplicationListener((ApplicationListener<?>) bean);

⑦initMessageSource(); 初始化MessageSource组件(做国际化功能；消息绑定，消息解析）

​	Ⅰ.获取BeanFactory

​	Ⅱ.看容器中是否有id为MessageSource的组件

​		如果有就直接赋值给messageSource属性，没有则创建一个DelegatingMessageSource

​			MessageSource：取出国际化配置文件中的某个key的值；能按照区域信息获取

​	Ⅲ.把创建好的MessageSource注册到容器中：以后获取国际化配置文件的值可以自动注入MessageSource

```java
beanFactory.registerSingleton(MESSAGE_SOURCE_BEAN_NAME, this.messageSource);
String getMessage(String code, Object[] args, Locale locale) throws NoSuchMessageException;
```

⑧initApplicationEventMulticaster();初始化事件多播器

​	Ⅰ.获取BeanFactory

​	Ⅱ.从BeanFactory中尝试获取容器中是否有beanName == "applicationEventMulticaster"的bean。

​	Ⅲ.如果没有就创建一个SimpleApplicationEventMulticaster(beanFactory)，再将其添加到容器中

⑨onRefresh()此方法为空方法，留给子类做实现。

⑩registerListeners(); 给容器中将所有的ApplicationListener进行注册

​	Ⅰ.从容器中拿到所有的ApplicationListener

​	Ⅱ.将每个监听器添加到事件多播器中

getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName);

​	Ⅲ.当有早期事件存在时，将早期事件通过多播器进行事件的发布

⑪finishBeanFactoryInitialization(beanFactory);

实例化剩下的所有非延迟加载的bean对象，因为有些内置的bean可能在前面的步骤已经完成实例化和初始化的过程

​	Ⅰ.获取容器中所有已经定义好的bean的名称放进一个集合中

​	Ⅱ.通过遍历每一个bean，并获取bean的定义信息 RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);

​	Ⅲ.判断这个bean不是抽象的且是单实例bean且不是延迟加载的，

​	Ⅳ.再次进行判断这个bean是否实现FactoryBean接口，是则例如内置的FactoryBean创建对象

​		不是则调用getBean(beanName) 创建对象

​	Ⅴ.剩下的内容与Bean生命周期处的代码顺序一致

⑫finishRefresh(); 完成BeanFactory的刷新工作

​	Ⅰ.initLifecycleProcessor();初始化和生命周期有关的后置处理器

​		默认从容器中找是否有id为lifecycleProcessor的的组件；如果没有则创建默认的new DefaultLifecycleProcessor()并加入到容器中

​		允许通过LifecycleProcessor接口的实现类，可以在BeanFactory执行到相应的周期进行一些处理

​	Ⅱ.getLifecycleProcessor().onRefresh();拿到上面生命周期组件回调容器刷新完成的方法

​	Ⅲ.publishEvent(new ContextRefreshedEvent(this));发布容器刷新完成的事件

​	Ⅳ.LiveBeansView.registerApplicationContext(this);

​		![refresh方法内部的执行顺序](https://s1.ax1x.com/2022/05/09/OJtwjO.png)

---

Spring创建过程的简单总结

1.Spring容器在启动的时候，先会保存所有注册进来的Bean的定义信息

​	①xml注册bean：[bean]

​	②注解注册bean：@Component、@Bean

2.Spring容器会在合适的时机创建这些bean

​	①用到bean的时候，利用getBean方法创建bean，并保存在容器中

​	②统一创建所有剩下的bean的时候，finishBeanFactoryInitialization(beanFactory);

3.后置处理器：每一个bean创建完成，都会使用各种后置处理器进行处理，来增强bean的功能

​	AutowiredAnnotationBeanPostProcessor：处理自动注入

​	AnnotationAwareAspectJAutoProxyCreator：创建代理对象，进行aop功能

4.事件驱动模型

​	ApplicationListener：用于事件监听

​	ApplicationEventMulticaster：多播器用于事件派发

---

## web

1.Shared libraries(共享库) /runtimes pluggability(运行时插件能力)

在容器/应用程序启动的时候都会扫描jar包里面META-INF/services/javax.servlet.ServletContainerInitializer

指定的实现类，启动并运行这个实现类的onStartup方法：@HandlesTypes能够传入指定的类型

使用ServletContext注册Web的三大组件(Servlet程序、Filter过滤器、Listener监听器)

使用硬编码的方式在项目启动的时候给ServletContext里面注册组件，且只有在项目启动时能注册

​	①ServletContainerInitializer得到的ServletContext

​	②ServletContextListener得到的ServletContext

~~~java
//在容器启动的时候会将@HandlesTypes指定的这个类型下面的子类(实现类、子接口)传递过来
//传入指定的类型
@HandlesTypes(value = {TestService.class})
public class MyServletContainerInitializer implements ServletContainerInitializer {
    /**
     * Description : 在应用启动的时候运行这个方法
     * 可以通过这个方法中的ServletContext注册Web的三大组件(Servlet程序、Filter过滤器、Listener监听器)
     * @date 2022/5/8
     * @param set 传入指定类型的所有子类型
     * @param servletContext  代表当前web应用的servletContext，每一个web都只有一个servletContext
     * @return void
     **/
    @Override
    public void onStartup(Set<Class<?>> set, ServletContext servletContext) throws ServletException {
        System.out.println("指定的类型：");
        set.forEach(System.out::println);

        //注册组件
        //注册Servlet
        ServletRegistration.Dynamic servlet = servletContext.addServlet("userServlet", UserServlet.class);
        //配置Servlet的映射信息
        servlet.addMapping("/user");

        //注册Filter过滤器
        FilterRegistration.Dynamic filter = servletContext.addFilter("userFilter", UserFilter.class);
        //配置Filter过滤器的映射信息
        filter.addMappingForUrlPatterns(EnumSet.of(DispatcherType.REQUEST),true,"/*");
        //注册Listener监听器
        servletContext.addListener(UserListener.class);
    }
}
~~~

### servlet3.0与springMVC的整合

1.web容器在启动的时候，会在扫描每个jar包里面存在的META-INF/services/javax.servlet.ServletContainerInitializer文件

2.加载这个文件指定的类SpringServletContainerInitializer，而这个类通过@HandlesTypes注解指定了WebApplicationInitializer接口

3.spring的应用一启动就会加载WebApplicationInitializer接口的所有实现类

4.在onStartup方法里面，在<font color='red'>**判断这些实现类都不是接口和抽象类后就会为这些组件(实现类)创建对象并添加进initializers集合中**</font>

<font color='red'>**WebApplicationInitializer接口各个实现类(并不是全部执行，而是实现哪个执行哪个)的onStartup()方法的作用：**</font>

​	①AbstractContextLoaderInitializer：创建根容器；createRootApplicationContext();

​	②AbstractDispatcherServletInitializer：创建web的ioc容器；createServletApplicationContext();

​																		   创建了DispatcherServlet；createDispatcherServlet()

​																		   将创建的DispatcherServlet添加到ServletContext中

​	③AbstractAnnotationConfigDispatcherServletInitializer；注解方式配置DispatcherServlet初始化器，继承自上面的②这个类

​																			创建根容器(相当于重写了父类的这个方法)：createRootApplicationContext{

​																																						getRootConfigClasses();传入一个配置类}

​																			创建web的ioc容器(相当于重写了父类的这个方法)：createServletApplicationContext{	

​																																						getServletConfigClasses();获取一个配置类}

5.最终遍历initializers集合，通过每个实现类的对象逐个调用其所在类的onStartup()方法

以上的总结：

​	①如果需要以注解的方式启动SpringMVC只需要继承AbstractAnnotationConfigDispatcherServletInitializer，

​		实现其中的抽象方法来指定DispatcherServlet的配置信息即可

### 定制SpringMVC

1.@EnableWebMVC：开启SpringMVC定制配置

2.配置组件（视图解析器、视图映射、静态资源映射、拦截器。。。）extends WebMvcConfigurerAdapter

### Servlet3.0异步请求

~~~java
//asyncSupported的值默认是false，必须手动设置为true才能支持异步
@WebServlet(value = "/async",asyncSupported = true)
public class AsynchronousServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //1.支持异步处理asyncSupported = true
        //2.开启异步模式
        System.out.println("主线程：" + Thread.currentThread().getName() + System.currentTimeMillis());
        AsyncContext asyncContext = req.startAsync();
        //3.业务逻辑进行异步处理：开始异步处理
        asyncContext.start(new Runnable() {
            @Override
            public void run() {
                try {
                    System.out.println("副线程开启：" + Thread.currentThread().getName() + System.currentTimeMillis());
                    testSync();
                    //表示异步处理已完成
                    asyncContext.complete();
                    //4.获取响应
                    ServletResponse response = asyncContext.getResponse();
                    response.getWriter().write("处理完成");
                    System.out.println("副线程结束：" + Thread.currentThread().getName() + System.currentTimeMillis());
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
        System.out.println("主线程结束：" + Thread.currentThread().getName() + System.currentTimeMillis());

    }

    public void testSync() throws Exception {
        Thread.sleep(3000);
    }
}
~~~



![异步线程图示](https://s1.ax1x.com/2022/05/09/OJtWgf.png)

### SpringMVC异步请求

#### Callable

处理过程：

1.控制器返回一个Callable

2.Spring异步处理，将Callable提交到一个TaskExecutor 使用一个隔离的线程进行执行

3.DispatcherServlet和所有的Filter过滤器退出Servlet容器的线程，但响应依旧保持打开状态

4.Callable返回结果，SpringMVC将请求再次重新派发给Servlet容器，恢复之前的操作

5.根据Callable返回的结果，SpringMVC继续进行视图渲染流程等(从收到请求-处理请求)

- <font color='red'>**异步的拦截器(同步的拦截器只能拦截同步的请求，因此异步请求必须注册异步拦截器)**</font>

  ①原生API的AsyncListener

  ②SpringMVC实现AsyncHandlerInterceptor

~~~java
@Controller
public class AsyncController {

    @ResponseBody
    @RequestMapping("/async1")
    public Callable<String> async1(){
        System.out.println("主线程start：" + Thread.currentThread().getName() + System.currentTimeMillis());
        Callable<String> callable = new Callable<String>() {
            @Override
            public String call() throws Exception {
                System.out.println("副线程start：" + Thread.currentThread().getName() + System.currentTimeMillis());
                Thread.sleep(2000);
                System.out.println("副线程end：" + Thread.currentThread().getName() + System.currentTimeMillis());
                return "Callable<String> async1()";
            }
        };
        System.out.println("主线程end：" + Thread.currentThread().getName() + System.currentTimeMillis());
        return callable;
    }
}

//处理结果
//触发拦截器的执行
//主线程start：http-apr-8080-exec-81652082984174
//主线程end：http-apr-8080-exec-81652082984177
//===DispatcherServlet和所有的Filter过滤器退出Servlet容器的线程===

//===等待Callable的执行===
//副线程start：MvcAsync11652082984187
//副线程end：MvcAsync11652082986188
//===Callable执行完成===

//===DispatcherServlet收到由SpringMVC调度的经过callable的请求===
//触发拦截器的执行
//拦截器的执行完成 (Callable之前的返回值就是目标方法的返回值，因此目标方法就不再执行)
//afterCompletion
~~~

#### DeferredResult

- 模拟场景：

~~~java
 @ResponseBody
    @RequestMapping("/createOrder")
    public DeferredResult<Object> deferredResult(){
        DeferredResult<Object> deferredResult = new DeferredResult<>((long)3000,"fail create");
        DeferredResultQueue.save(deferredResult);
        return deferredResult;
    }

    @ResponseBody
    @RequestMapping("/create")
    public String create(){
        //创建订单
        String order = UUID.randomUUID().toString();
        DeferredResult<Object> result = DeferredResultQueue.get();
        result.setResult(order);
        return "order:" + order;
    }
~~~

![](https://s1.ax1x.com/2022/05/09/OJtfv8.png)
