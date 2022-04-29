---
title: JavaSe2
date: 2022-02-23 15:42:02
updated: 2022-02-23 15:42:02
tags:
  - Java
categories:
  - Java
keywords: Java
copyright_author: Year21
copyright_url: http://year21.top/2022/02/23/JavaSe2
---



## 基本概念

### 程序 进程 线程

1. 程序是为了完成特定目标，编写的一组指令集合。可理解为一段静态的代码

2. 进程是一个正在执行的程序。生命周期是进程的产生、存在和消亡的过程。

   ①**进程作为一个资源分配的单位，每一个进程都单独拥有一套方法区和堆。**

3. 线程是一个程序内部的一条执行路径。

​		①一个进程支持同一时间并行执行多个线程，则支持多线程。

​		②**线程作为调度和执行的单位，每个线程都独立拥有一套虚拟机栈和程序计数器(pc)**

​		③一个进程中的多个线程共享相同的内存单元/内存地址空间它们从同一堆中分配对象，可以 访问相同的变量和对象。

​		**可以理解为一个进程内的多个线程之间共享一套方法区和堆，但也造成了安全隐患**

一个java应用程序java.exe至少有3个线程：main()主线程。gc()垃圾回收线程，异常处理线程。如果出现异常情况就会影响主线程。

### 并行 并发

1. 并行：多个CPU同时执行多个任务。比如：多个人同时做不同的事。
2. 并发：一个CPU(采用时间片)同时执行多个任务。比如：秒杀、多个人做同一件事。

---

## 多线程

### 创建方式一：继承于Thread类

~~~java
//1.创建一个继承于Thread类的子类
class Test extends Thread{
    //2.重写Thread类中的run()方法
    public void run(){
        for (int i = 0; i < 100; i++) {
            if (i % 2 == 0){
                System.out.println(i);
            }
        }
    }
}
public class ThreadTest {
    public static void main(String[] args) {
        //3.创建Thread类的子类的对象
        Test test = new Test();
        //4.通过此对象调用父类中的star()方法
        test.start();
    }
}
~~~

问题补充：

~~~java
//问题一：我们不能通过直接调用对象.run()方法启动线程
        //test.run()
        
//问题二：要想再启动一个线程，不能再调用当前对象的start()的线程去执行，会报IllegalThreadStateException异常
        //只能通过重新创建一个新的线程对象
        Test test1 = new Test();
        test1.start();
~~~

### Thread类的方法

1. start():启动当前线程；调用当前线程的run()

2. run(): 通常需要重写Thread类中的此方法，将创建的线程要执行的操作声明在此方法中

3. currentThread():静态方法，返回执行当前代码的线程

4. getName():获取当前线程的名字

5. setName():设置当前线程的名字

6. yield():释放当前cpu的执行权

7. join():在线程a中调用线程b的join(),此时线程a就进入阻塞状态，直到线程b完全执行完以后，线程a才

   结束阻塞状态。

8. stop():已过时。当执行此方法时，强制结束当前线程。

9. sleep(long millitime):让当前线程“睡眠”指定的millitime毫秒。在指定的millitime毫秒时间内，当前

   线程是阻塞状态。

10. isAlive():判断当前线程是否存活

### 线程的优先级

1. 调度策略：

![调度策略](https://s4.ax1x.com/2022/02/23/bCVYJU.png)

2. 优先级

①MAX_PRIORITY：10

​	MIN _PRIORITY：1

​	NORM_PRIORITY：5  -->默认优先级

②获取和设置当前线程的优先级：

getPriority():获取线程的优先级

setPriority(int p):设置线程的优先级

补充：高优先级的线程要抢占低优先级线程cpu的执行权。但是只是从概率上讲，高优先级的线程高

概率的情况下被执行。并不意味着只有当高优先级的线程执行完以后，低优先级的线程才执行。

### 创建方式二：实现Runnable接口

~~~java
//1.创建实现Runnable接口的类
class MyThread1 implements Runnable{
    //2.实现类去实现Runnable中的抽象方法：run()
    public void run(){
        for (int i = 0; i < 100; i++) {
            if (i % 2 == 0){
                System.out.println(i);
            }
        }
    }
}

public class ThreadTest1 {
    public static void main(String[] args) {
        //3.创建类的对象
        MyThread1 m1 = new MyThread1();
        //4.④将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
        Thread thread = new Thread(m1);
        //5.⑤通过Thread类的对象调用start()
        new Thread(thread).start();
    }
~~~

### 两种创建方式的比较

优先使用实现Runnable的方式

原因：1.实现的方式没有类的单继承性的限制

​			2. 实现的方式更适合处理多个线程共享数据的情况

联系：Thread类本身也实现了Runnable接口

相同点：两种方式都需要重写run()方法，将线程执行的逻辑写在了run()方法中

### 线程的生命周期

![线程生命周期](https://s4.ax1x.com/2022/02/23/bCV2Se.png)

### 线程的同步

场景： 多个窗口进行售票

1. 问题一：卖票过程中，出现了重票、错票问题—————线程安全问题

2. 原因：某个线程操作车票输出尚未完成时，其他线程也进入操作车票的环节

3. 解决方法：添加同步锁，当线程a在操作车票输出的环节，其他线程不能进入，直到线程a操作完成后，其他线程才能进入。这种情况下即使线程a出现阻塞或休眠，也不能改变。

方式一：同步代码块

~~~java
synchronized(同步监视器){
	//需要被同步的代码(操作共享数据的代码)
}
~~~

同步代码块格式说明：

①操作共享数据的代码，即为需要被同步的代码 (只能包含需要的代码)

②共享数据：多个线程共同操作的变量  

③同步监视器：锁，任何一个类的对象都可以充当锁的角色
   唯一的要求是：多个线程必须共用同一个锁

补充：①在实现Runnable接口创建多线程的方式中，可以考虑使用this充当同步监视器，在此方式中只创建了一个对象。

​           ②在继承Thread类创建多线程的方式中，要谨慎考虑使用this充当同步监视器，在此方式中创建了多个对象。可以考虑使用当前类充当同步监视器 Windows.class

因此能否使用this取决于当前方式中创建的对象是否唯一，即使用的同步锁是否为同一个。

方式二：同步方法

如果操作共享数据的代码完整的声明在一个方法中，可以将此方法声明为同步方法

~~~java
public  void run(){
        while(true) {
            syn();
            }
    }

	//因本方法使用的是实现Runnable的方式创建多线程，对象只有一个，因此同步监视器是：this
    private synchronized void syn(){
        if (ticket > 0){
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "买票，票号是：" + ticket);
            ticket--;
        }
    }

 public void run(){
        while(true){
            syn();
        }
    }

//    private synchronized void syn(){//同步监视器s1,s2,s3，此方法不可行
// 本方法使用的是继承Thread类创建多线程，对象有多个，只能将syn()声明为静态，因此同步监视器是 Sell3.class ，即其类本身
    private  static synchronized void syn(){
        if(ticket > 0) {
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + ":" + "票号：" + ticket);
            ticket--;
        }
    }
~~~

同步方法总结：①同步方法依然涉及同步监视器，只是不需要显式声明。

​						   ②非静态的同步方法，同步监视器是this

​                           ③静态的同步方法，同步监视器是当前类本身  类.class

4. 线程同步的方式优缺点

   优点：解决了线程安全的问题

   缺点：在同步代码块内只有一个线程，其余线程在外等待。实际上内部成了单线程，效率较低

### 线程的死锁问题

1. 概念：不同线程分别占用对方所需的同步资源，都在等待对方放弃已有的同步资源，造成线程的死锁。
2. 说明：出现死锁后，不会抛异常，不会出现提示，只是说所有线程都处于阻塞状态，无法继承运行

3. 解决方法：减少同步资源的定义、避免嵌套定义、专门的算法

### lock锁

解决线程安全的方式之三 lock锁  --jdk5.0新增

~~~java
class Tick implements Runnable{
    private static int ticket = 100;
    //实例化ReentrantLock
    private ReentrantLock lock = new ReentrantLock(true);
    
    public void run(){
        while(true){
            try {
                //2.调用lock()方法
                lock.lock();
                
                if(ticket > 0){
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + ":" + "票号：" + ticket);
                    ticket--;
                }else{
                    break;
                }
            }finally {
                //3.调用解锁的方法
                lock.unlock();
            }
~~~

1. synchronized 和 lock 的异同？

相同：二者都可以解决线程安全问题

不同：①synchronized机制在执行完相应的同步代码以后，自动的释放同步监视器

​			②Lock需要手动的启动同步（lock()），同时结束同步也需要手动的实现（unlock()）

2. 优先使用顺序：

   Lock  --->  同步代码块（已经进入了方法体，分配了相应资源）--->同步方法（在方法体之外）

### 线程的通信

1. 涉及的三个方法：

   wait():一旦执行此方法，当前线程就进入阻塞状态，并释放同步监视器。对比sleep()方法则其不会自动释放锁

   notify():一旦执行此方法，就会唤醒被wait的一个线程。如果有多个线程被wait，就唤醒优先级高的那个。

   notifyAll():一旦执行此方法，就会唤醒所有被wait的线程。

2. 说明：

   wait()，notify()，notifyAll()三个方法必须使用在同步代码块或同步方法中。

   wait()，notify()，notifyAll()三个方法的调用者必须是同步代码块或同步方法中的同步监视器。否则，会出现IllegalMonitorStateException异常

   wait()，notify()，notifyAll()三个方法是定义在java.lang.Object类中。

~~~java
public void run() {
        while(true){
            synchronized (obj) {
                obj.notify();

                if(number <= 100){

                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + ":" + number);
                    number++;

                    try {
                        //使得调用如下wait()方法的线程进入阻塞状态
                        obj.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }else{
                    break;
                }
            }
~~~

3. sleep() 和 wait()的异同？

   相同点：一旦执行方法，都可以使得当前的线程进入阻塞状态。

   不同点：①两个方法声明的位置不同：Thread类中声明sleep() , Object类中声明wait()

   ​				②调用的要求不同：sleep()可以在任何需要的场景下调用。 wait()必须使用在同步代码块或同步方法中

   ​				③关于是否释放同步监视器：如果两个方法都使用在同步代码块或同步方法中，sleep()不会释放锁，wait()会释放锁。

### 创建方式三  实现Callable接口

~~~java
//1.创建Callable接口的一个实现类
class Number implements Callable{
    private int sum;

    @Override
    //2.实现call()方法，将此线程需要执行的操作声明在call()方法当中
    public Object call() throws Exception {

        for (int i = 0; i <= 100; i++) {
            if (i % 2 == 0){
                System.out.println(i);
                sum += i;
            }
        }
        return sum;
    }
}

public class CallableTest {
    public static void main(String[] args) {
        //3.创建Callable接口实现类的对象
        Number n = new Number();
        //4.将此Callable接口实现类的对象作为传递到FutureTask构造器中，创建FutureTask的对象
       FutureTask futureTask = new FutureTask(n);

       //5.将FutureTask的对象作为参数传递到Thread类的构造器中，创建Thread对象，并调用start()
        new Thread(futureTask).start();
        try {
            //6.获取Callable接口中call()方法的返回值
            //get()返回值即为FutureTask构造器参数Callable实现类重写的call()的返回值
            Object sum = futureTask.get();
            System.out.println(sum);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
~~~

1.实现Callable接口的方式创建多线程比实现Runnable接口创建多线程方式强的方面

①call()可以有返回值的。

②call()可以抛出异常，被外面的操作捕获，获取异常的信息

③Callable是支持泛型的

### 创建方式四 使用线程池

​	JDK 5.0起提供了线程池相关API：ExecutorService 和 Executors

1. 优点：①提高响应速度（减少了创建新线程的时间） 

   ②降低资源消耗（重复利用线程池中线程，不需要每次都创建）③便于线程管理 

   corePoolSize：核心池的大小

   maximumPoolSize：最大线程数

   keepAliveTime：线程没有任务时最多保持多长时间后会终止

~~~java
class Number implements Runnable{

    public void run(){
        for (int i = 0; i <= 100; i++) {
            if (i % 2 != 0){
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}

public class ThreadPoolTest {
    public static void main(String[] args) {
        //1.提供指定线程数量的线程池
        //ExecutorService是接口没有对象，这里实际上体现的是多态
        ExecutorService service = Executors.newFixedThreadPool(10);
        ThreadPoolExecutor service1 = (ThreadPoolExecutor) service;//向下强转类型
        //设置线程池的属性
//        System.out.println(service.getClass());此方法返回了service所创建的类 即ThreadPoolExecutor
//        service1.setCorePoolSize(15);
//        service1.setKeepAliveTime()
        
        //2.执行指定的线程的操作。需要提供实现Runnable接口或Callable接口实现类的对象
        service.execute(new Number());//适合使用于Runnable
//        service.submit();//适合使用于Callable
        //3.关闭连接池
        service.shutdown();
    }
}
~~~

---

## Java常用类

### String类

String：字符串，使用一对“ ”表示

1. String是一个final类，代表不可被继承

2. String实现了Serializable接口：表示字符串是支持序列化的

   String实现了Comparable接口：表示String可以比较大小

3. String内部定义了final char[]  value用于存储字符串数据

4. String：代表不可变的字符序列。简称：不可变性

​					①当调用String的replace()方法修改指定字符或者字符串时，也需要重新指定内存区域赋值

​					②凡是涉及到String变量内容的改变，实际上都是在方法区中重新构造的地址，对原先的地址值不影响

5. 通过字面量的方式(区别于new)给一个字符串赋值，此时的字符串声明在字符串常量池中。
6. 字符串常量池中是不会存储相同内容的字符串
7. String类的相关面试题总结
       ①常量与常量的拼接结果在常量池。且常量池中不会存在相同内容的常量。
       ②只要其中有一个是变量，结果就在堆中。
       ③如果拼接的结果调用intern()方法，返回值就在常量池中
8. 与StringBuffer、StringBuilder之间的转换

①String -->StringBuffer、StringBuilder：调用StringBuffer、StringBuilder的构造器

②StringBuffer、StringBuilder -->String ；String：一种是调用String的构造器，一种是StringBuffer、StringBuilder的toString()；

~~~java
//String的实例化方式
//方式一：通过字面量定义的方式
//此时的s1的数据声明在方法区的字符串常量池中
		String s1 = ”11“;

//方式二：通过new + 构造器的方式
//此时的s2保存的地址值是数据在堆空间创建后对应的地址值
		String s2 = new String("s2")
            
  		String s3 = "1122";
        String s4 = "11" + "22";
        String s5 = s1 + "22p";
    
		System.out.println(s3 == s4);//true
        System.out.println(s3 == s5);//false
        
		//返回值得到的s8使用的常量值中已经存在的“javaEEhadoop”
		String s8 = s6.intern();
        System.out.println(s3 == s8);//true
~~~

补充：String s = new String("abc")；在内存中创建了两个对象（一个是堆空间中的new结构，一个是char[ ]对应的常量池中的数据 ”abc“

#### String类的相关方法

int length()：返回字符串的长度： return value.length

char charAt(int index)： 返回某索引处的字符return value[index]

boolean isEmpty()：判断是否是空字符串：return value.length == 0

String toLowerCase()：将 String 中的所有字符转换为小写

String toUpperCase()：将 String 中的所有字符转换为大写

String trim()：返回字符串的副本，忽略前导空白和尾部空白

boolean equals(Object obj)：比较字符串的内容是否相同
boolean equalsIgnoreCase(String anotherString)：与equals方法类似，忽略大小写

String concat(String str)：将指定字符串连接到此字符串的结尾。 等价于用“+”

int compareTo(String anotherString)：比较两个字符串的大小

String substring(int beginIndex)：返回一个新的字符串，它是此字符串的从beginIndex开始截取到最后的一个子字符串。

String substring(int beginIndex, int endIndex) ：返回一个新字符串，它是此字符串从beginIndex开始截取到endIndex(不包含)的一个子字符串。



boolean endsWith(String suffix)：测试此字符串是否以指定的后缀结束

boolean startsWith(String prefix)：测试此字符串是否以指定的前缀开始

boolean startsWith(String prefix, int toffset)：测试此字符串从指定索引开始的子字符串是否以指定前缀开始



boolean contains(CharSequence s)：当且仅当此字符串包含指定的 char 值序列时，返回 true

int indexOf(String str)：返回指定子字符串在此字符串中第一次出现处的索引

int indexOf(String str, int fromIndex)：返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始

int lastIndexOf(String str)：返回指定子字符串在此字符串中最右边出现处的索引

int lastIndexOf(String str, int fromIndex)：返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索

注：indexOf和lastIndexOf方法如果未找到都是返回-1



替换：
String replace(char oldChar, char newChar)：返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。

String replace(CharSequence target, CharSequence replacement)：使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串。

String replaceAll(String regex, String replacement)：使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。

String replaceFirst(String regex, String replacement)：使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。

匹配:
boolean matches(String regex)：告知此字符串是否匹配给定的正则表达式。
切片：
String[] split(String regex)：根据给定正则表达式的匹配拆分此字符串。

String[] split(String regex, int limit)：根据匹配给定的正则表达式来拆分此字符串，最多不超过limit个，如果超过了，剩下的全部都放到最后一个元素中

~~~java
        String s1 = new String("AbC");
        String s2 = new String("abf");
        System.out.println(s1.compareTo(s2));//涉及到字符串排序

		String s3 = "1234567";
        String s4 = s7.substring(2);//234567
        System.out.println(s3);
        System.out.println(s4);

        String s5 = s7.substring(2, 5);//345
        System.out.println(s5);

		String s6 = s1.toLowerCase();
        System.out.println(s6);//s1不可变的，仍然为原来的字符串 ”AbC“
        System.out.println(s6);//改成小写以后的字符串 ”abc“

        String s7 = "   he  llo   world   ";
        String s8 = s7.trim();
        System.out.println("-" + s7 + "-");//"-   he  llo   world   -";
        System.out.println("-" + s8 + "-");//"-he  llo   world-";
~~~

#### String类与字符数组转换

1. String 与 char[ ] 之间的转换

​		①String --> char[ ]:调用string的toCharArray()；

​		②char[ ]  --> String ; 调用String的构造器

~~~java
	String a = "abc123";	
	//String --> char[]
	char[] charA = a.tocharArray();

	//char[ ]  --> String
	char[] a = new char[]{'a','b','c'};
	String b = new String(a);    
~~~

2. String 与 byte[ ] 之间的转换

​		①编码：String --> byte[ ]:调用string的getBytes()；

​		②解码：byte[ ]  --> String ;  调用String的构造器

​		要求：解码时，要求解码使用的字符集必须与编码时使用的字符集一致，否则会出现乱码

~~~java
	String s = "abc123";
	//String --> byte[ ]
	byte[] byte = s.getBytes();

	//byte[ ]  --> String
	String byte1 = new String(byte)；//使用默认的字符集，进行解码
~~~

### StringBuffer和StringBuilder类

1. ***String 、StringBuffer、StringBuilder三者的异同***

区别：

String：不可变的字符序列；

StringBuffer：可变的字符序列；线程安全，但效率较低

StringBuilder：可变的字符序列；jdk5.0新增，但线程不安全，效率较高 

相同点：三者的底层结构都是使用char[ ]数组进行储存

**对比String、StringBuffer、StringBuilder三者的效率：从高到低排列：StringBuilder > StringBuffer > String**

**StringBuffer、StringBuilder底层默认扩容是 原来容量*2 + 2**

~~~java
//源码分析：
    String str = new String();//char[] value = new char[0];
    String str1 = new String("abc");//char[] value = new char[]{'a','b','c'};

    StringBuffer sb1 = new StringBuffer();//char[] value = new char[16];底层创建了一个长度是16的数组。
    System.out.println(sb1.length());//
    sb1.append('a');//value[0] = 'a';
    sb1.append('b');//value[1] = 'b';

    StringBuffer sb2 = new StringBuffer("abc");//char[] value = new char["abc".length() + 16];

    //问题1. System.out.println(sb2.length());//3
    //问题2. 扩容问题:如果要添加的数据底层数组盛不下了，那就需要扩容底层的数组。
	//默认情况下，扩容为原来容量的2倍 + 2，同时将原有数组中的元素复制到新的数组中。
	// 指导意义：开发中建议大家使用：StringBuffer(int capacity) 或 StringBuilder(int capacity)

~~~

2. 相关的方法

   StringBuffer的常用方法：

   StringBuffer append(xxx)：提供了很多的append()方法，用于进行字符串拼接

   StringBuffer delete(int start,int end)：删除指定位置的内容

   StringBuffer replace(int start, int end, String str)：把[start,end)位置替换为str

   StringBuffer insert(int offset, xxx)：在指定位置插入xxx

   StringBuffer reverse() ：把当前字符序列逆转

   public int indexOf(String str)

   public String substring(int start,int end):返回一个从start开始到end索引结束的左闭右开区间的子字符串

   public int length()

   public char charAt(int n )

   public void setCharAt(int n ,char ch)

总结：增：append(xxx)

​            删：delete(int start,int end)

​			改：setCharAt(int n ,char ch) / replace(int start, int end, String str)

​			查：charAt(int n )

​			插：insert(int offset, xxx)

​			长度：length();

​			遍历：for() + charAt() / toString()

#### String相关的算法题

~~~java
/*
 * 1.模拟一个trim方法，去除字符串两端的空格。
 * 
 * 2.将一个字符串进行反转。将字符串中指定部分进行反转。比如将“abcdefg”反转为”abfedcg”
 * 
 * 3.获取一个字符串在另一个字符串中出现的次数。
      比如：获取“ab”在 “cdabkkcadkabkebfkabkskab”    
      中出现的次数
      
4.获取两个字符串中最大相同子串。比如：
   str1 = "abcwerthelloyuiodef“;str2 = "cvhellobnm"//10
   提示：将短的那个串进行长度 依次递减的子串与较长  
   的串比较。

5.对字符串中字符进行自然顺序排序。"abcwerthelloyuiodef"
提示：
1）字符串变成字符数组。
2）对数组排序，选择，冒泡，Arrays.sort(str.toCharArray());
3）将排序后的数组变成字符串。

 */
public class StringExer {

	// 第1题
	public String myTrim(String str) {
		if (str != null) {
			int start = 0;// 用于记录从前往后首次索引位置不是空格的位置的索引
			int end = str.length() - 1;// 用于记录从后往前首次索引位置不是空格的位置的索引

			while (start < end && str.charAt(start) == ' ') {
				start++;
			}

			while (start < end && str.charAt(end) == ' ') {
				end--;
			}
			if (str.charAt(start) == ' ') {
				return "";
			}

			return str.substring(start, end + 1);
		}
		return null;
	}

	// 第2题
	// 方式一：
	public String reverse1(String str, int start, int end) {// start:2,end:5
		if (str != null) {
			// 1.
			char[] charArray = str.toCharArray();
			// 2.
			for (int i = start, j = end; i < j; i++, j--) {
				char temp = charArray[i];
				charArray[i] = charArray[j];
				charArray[j] = temp;
			}
			// 3.
			return new String(charArray);

		}
		return null;

	}

	// 方式二：
	public String reverse2(String str, int start, int end) {
		// 1.
		String newStr = str.substring(0, start);// ab
		// 2.
		for (int i = end; i >= start; i--) {
			newStr += str.charAt(i);
		} // abfedc
			// 3.
		newStr += str.substring(end + 1);
		return newStr;
	}

	// 方式三：推荐 （相较于方式二做的改进）
	public String reverse3(String str, int start, int end) {// ArrayList list = new ArrayList(80);
		// 1.
		StringBuffer s = new StringBuffer(str.length());
		// 2.
		s.append(str.substring(0, start));// ab
		// 3.
		for (int i = end; i >= start; i--) {
			s.append(str.charAt(i));
		}

		// 4.
		s.append(str.substring(end + 1));

		// 5.
		return s.toString();

	}

	@Test
	public void testReverse() {
		String str = "abcdefg";
		String str1 = reverse3(str, 2, 5);
		System.out.println(str1);// abfedcg

	}

	// 第3题
	// 判断str2在str1中出现的次数
	public int getCount(String mainStr, String subStr) {
		if (mainStr.length() >= subStr.length()) {
			int count = 0;
			int index = 0;
			// while((index = mainStr.indexOf(subStr)) != -1){
			// count++;
			// mainStr = mainStr.substring(index + subStr.length());
			// }
			// 改进：
			while ((index = mainStr.indexOf(subStr, index)) != -1) {
				index += subStr.length();
				count++;
			}

			return count;
		} else {
			return 0;
		}

	}

	@Test
	public void testGetCount() {
		String str1 = "cdabkkcadkabkebfkabkskab";
		String str2 = "ab";
		int count = getCount(str1, str2);
		System.out.println(count);
	}

	@Test
	public void testMyTrim() {
		String str = "   a   ";
		// str = " ";
		String newStr = myTrim(str);
		System.out.println("---" + newStr + "---");
	}

	// 第4题
	// 如果只存在一个最大长度的相同子串
	public String getMaxSameSubString(String str1, String str2) {
		if (str1 != null && str2 != null) {
			String maxStr = (str1.length() > str2.length()) ? str1 : str2;
			String minStr = (str1.length() > str2.length()) ? str2 : str1;

			int len = minStr.length();

			for (int i = 0; i < len; i++) {// 0 1 2 3 4 此层循环决定要去几个字符

				for (int x = 0, y = len - i; y <= len; x++, y++) {

					if (maxStr.contains(minStr.substring(x, y))) {

						return minStr.substring(x, y);
					}

				}

			}
		}
		return null;
	}

	// 如果存在多个长度相同的最大相同子串
	// 此时先返回String[]，后面可以用集合中的ArrayList替换，较方便
	public String[] getMaxSameSubString1(String str1, String str2) {
		if (str1 != null && str2 != null) {
			StringBuffer sBuffer = new StringBuffer();
			String maxString = (str1.length() > str2.length()) ? str1 : str2;
			String minString = (str1.length() > str2.length()) ? str2 : str1;

			int len = minString.length();
			for (int i = 0; i < len; i++) {
				for (int x = 0, y = len - i; y <= len; x++, y++) {
					String subString = minString.substring(x, y);
					if (maxString.contains(subString)) {
						sBuffer.append(subString + ",");
					}
				}
				System.out.println(sBuffer);
				if (sBuffer.length() != 0) {
					break;
				}
			}
			String[] split = sBuffer.toString().replaceAll(",$", "").split("\\,");
			return split;
		}

		return null;
	}
	// 如果存在多个长度相同的最大相同子串：使用ArrayList
//	public List<String> getMaxSameSubString1(String str1, String str2) {
//		if (str1 != null && str2 != null) {
//			List<String> list = new ArrayList<String>();
//			String maxString = (str1.length() > str2.length()) ? str1 : str2;
//			String minString = (str1.length() > str2.length()) ? str2 : str1;
//
//			int len = minString.length();
//			for (int i = 0; i < len; i++) {
//				for (int x = 0, y = len - i; y <= len; x++, y++) {
//					String subString = minString.substring(x, y);
//					if (maxString.contains(subString)) {
//						list.add(subString);
//					}
//				}
//				if (list.size() != 0) {
//					break;
//				}
//			}
//			return list;
//		}
//
//		return null;
//	}

	@Test
	public void testGetMaxSameSubString() {
		String str1 = "abcwerthelloyuiodef";
		String str2 = "cvhellobnmiodef";
		String[] strs = getMaxSameSubString1(str1, str2);
		System.out.println(Arrays.toString(strs));
	}

	// 第5题
	@Test
	public void testSort() {
		String str = "abcwerthelloyuiodef";
		char[] arr = str.toCharArray();
		Arrays.sort(arr);

		String newStr = new String(arr);
		System.out.println(newStr);
	}
}

~~~



---

### JVM中字符串常量池位置

1. jdk1.6：字符串常量池储存在方法区(永久区)
2. jdk1.7：字符串常量池储存在堆空间
3. fangquqjdk1.8：字符串常量池储存在方法区(元空间)

### JDK 8之前的日期时间的API

#### Date类

Java.util.Date类（父类）

​			|---java.sql.Date类（子类）

1. 两个构造器的使用

​		①Date()；创建一个对应当前时间的Date的对象.(时间戳)

​		②Date()；创建指定毫秒数的Date的对象

2. 两个方法的使用

​		①toString()；//显示当前的年、月、日、分、秒

​		②getTime()；//获取当前对象对应的毫秒数

~~~java
@Test
    public void Test(){
        //构造器1：Date()
         Date date = new Date();
        System.out.println(date.toString());//Fri Feb 11 16:58:08 CST 2022
        System.out.println(date.getTime());//1644569910695

        //构造器2：Date()
        Date date1 = new Date(1644569910695L);
        System.out.println(date1.toString());
        
        //创建java.sql.Date的对象
    	Date date2 = new java.sql.Date(1644569910695L)；
        system.out.println(date2.toString());
    
   		//将java.util.Date 对象转换为java.sql.Date 的对象
		Date date3 = new Date();
        java.sql.Date date4 = new java.sql.Date(date3.getTime());
}
~~~

3. java.sql.Date对应着数据库中的日期类型的变量

​		①实例化对象

​		②将java.util.Date 对象转换为java.sql.Date 的对象

####  SimpleDateFormat类

SimpleDateFormat是对日期Date类的格式化和解析

1. 实例化SimpleDateFormat ：

​		一般建议调用带参数的构造器

2. 格式化

​		日期 --->字符串  ；调用format()方法

3. 解析

​	格式化的逆过程，字符串 ---> 日期；调用parse()方法

~~~java
 		//*************按照指定的方式格式化和解析：调用带参的构造器*****************
        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
        //格式化
        String format1 = sdf1.format(date);
        System.out.println(format1);//2019-02-18 11:48:27
        //解析:要求字符串必须是符合SimpleDateFormat识别的格式(通过构造器参数体现),
        //否则，抛异常
        Date date2 = sdf1.parse("2020-02-18 11:48:27");
        System.out.println(date2);
~~~

#### Calendar 日历类(抽象类)

1. 实例化

​	①创建其子类(GregorianCalendar)的对象

​	②调用其静态方法getInstance()

2. 常用方法

​	get()、set()、add()

​	getTime()：日历类--->Date

​	setTime()：Date---> 日历类

~~~java
 		//get()
        int day = calendar.get(Calendar.DAY_OF_MONTH);
        //set()
        calendar.set(Calendar.DAY_OF_MONTH,22);
        day = calendar.get(Calendar.DAY_OF_MONTH);
        //add()
        calendar.add(Calendar.DAY_OF_MONTH,-3);
        day = calendar.get(Calendar.DAY_OF_MONTH);

        //getTime():日历类--->Date
        Date time = calendar.getTime();
        System.out.println(time);

        //setTime()：Date--->日历类
        Date date = new Date();
        calendar.setTime(date);
        day = calendar.get(Calendar.DAY_OF_MONTH);
        System.out.println(day);
~~~

### JDK8  时间API

#### localDate、localeTime、localDateTime类

1. LocalDateTime相较于LocalDate、LocalTime，使用频率要高
2. 类似于Calendar

3. 常用方法

​	①now():获取当前的日期、时间、日期+时间

​	②of():设置指定的年、月、日、时、分、秒。没有偏移量

​     ③getXxx()：获取相关的属性

​     ④withXxx():设置相关的属性  体现不可变性

​	①、②都是用于实例化对象  

~~~java
		//now():获取当前的日期、时间、日期+时间
        LocalDate localDate = LocalDate.now();
        LocalTime localTime = LocalTime.now();
        LocalDateTime localDateTime = LocalDateTime.now();

        //of():设置指定的年、月、日、时、分、秒。没有偏移量
        LocalDateTime localDateTime1 = LocalDateTime.of(2020, 10, 6, 13, 23, 43);
        System.out.println(localDateTime1);

        //getXxx()：获取相关的属性
        System.out.println(localDateTime.getDayOfMonth());
        System.out.println(localDateTime.getDayOfWeek());
    
        //体现不可变性
        //withXxx():设置相关的属性
        LocalDate localDate1 = localDate.withDayOfMonth(22);
		LocalDateTime localDateTime3 = localDateTime.plusMonths(3);//当前时间加上
 		LocalDateTime localDateTime4 = localDateTime.minusDays(6);//当前时间减去
        System.out.println(localDate);
        System.out.println(localDate1);
~~~

#### Instant 瞬时

instant类似于java.util.Date类

1. 实例化
2. 常用方法

​	①now():获取本初子午线对应的标准时间

​	②atOffset():添加时间的偏移量

​	③toEpochMilli():获取自1970年1月1日0时0分0秒（UTC）开始的毫秒数  ---> 类似Date类的getTime()

​	④ofEpochMilli():通过给定的毫秒数，获取Instant实例  -->类似Date(long millis)

~~~java
		//now():
        Instant instant = Instant.now();
        System.out.println(instant);//2019-02-18T07:29:41.719Z

        //添加时间的偏移量
        OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
        System.out.println(offsetDateTime);//2019-02-18T15:32:50.611+08:00

        //toEpochMilli():
        long milli = instant.toEpochMilli();
        System.out.println(milli);

        //ofEpochMilli():
        Instant instant1 = Instant.ofEpochMilli(1550475314878L);
        System.out.println(instant1);
~~~

#### DateTimeFormat类

1. 用于:格式化或解析日期、时间

2. 类似于SimpleDateFormat

3. 实例化对象 多种方式

方式一：预定义的标准格式。如：ISO_LOCAL_DATE_TIME;ISO_LOCAL_DATE;ISO_LOCAL_TIME

方式二：本地化相关的格式。如：ofLocalizedDateTime()

方式三：（最常用的方式）自定义的格式。如：ofPattern(“yyyy-MM-dd hh:mm:ss”)

~~~java
		//        方式一：
        DateTimeFormatter formatter = DateTimeFormatter.ISO_LOCAL_DATE_TIME;
        //格式化:日期-->字符串
        LocalDateTime localDateTime = LocalDateTime.now();
        String str1 = formatter.format(localDateTime);
        System.out.println(localDateTime);
        System.out.println(str1);//2019-02-18T15:42:18.797

        //解析：字符串 -->日期
        TemporalAccessor parse = formatter.parse("2019-02-18T15:42:18.797");
        System.out.println(parse);

		//        方式二：
//        FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT :适用于LocalDateTime
        DateTimeFormatter formatter1 = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.LONG);
        //格式化
        String str2 = formatter1.format(localDateTime);
        System.out.println(str2);//2019年2月18日 下午03时47分16秒

//      本地化相关的格式。如：ofLocalizedDate()
//      FormatStyle.FULL / FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT : 适用于LocalDate
        DateTimeFormatter formatter2 = DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM);
        //格式化
        String str3 = formatter2.format(LocalDate.now());
        System.out.println(str3);//2019-2-18

//      方式三：
        DateTimeFormatter formatter3 = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
        //格式化
        String str4 = formatter3.format(LocalDateTime.now());
        System.out.println(str4);//2019-02-18 03:52:09

        //解析
        TemporalAccessor accessor = formatter3.parse("2019-02-18 03:52:09"); 
~~~

### Java比较器

1. 一般情况下对象只能用 == 或 != 进行比较，不能使用其他运算符比较，但在特殊情况下，需要对多个对象进行排序，即需要比较对象的大小，通过使用Comparable和Comparator接口进行比较

2. Comparable接口：使用的是自然排序

   ①像String、包装类等实现了Comparable接口，重写了compareTo(obj)方法，给出了比较两个对象大小的方式。

   ②像String、包装类重写compareTo()方法以后，进行了从小到大的排列

​       ③重写compareTo(obj)的规则：

​		如果当前对象this大于形参对象obj，则返回正整数，

​		如果当前对象this小于形参对象obj，则返回负整数，

​		如果当前对象this等于形参对象obj，则返回零。

​	   ④对于自定义类来说，如果需要排序，我们可以让自定义类实现Comparable接口，重写compareTo(obj)方法，在compareTo(obj)方法中指明如何排序

~~~java
public int compareTo(Object o){
        if (o instanceof Product){
            Product p = (Product)o;
            if (this.price > p.price){
                return 1;
            }else if (this.price < p.price){
                return -1;
            }else {
                return -this.name.compareTo(p.name);
            }
        }
        throw new RuntimeException("传入的数据类型不符合");
    }
~~~

3. Comparator接口：使用的是定制排序

​		①使用场景： 元素没有实现java.lang.Comparable接口又不方便修改代码 或 实现了java.lang.Comparable接口但排序规则不适合当前的操作

​		②重写compare(Object o1,Object o2)方法规则，比较o1和o2的大小：

​			如果方法返回正整数，则表示o1大于o2；

​			如果返回0，表示相等；

​			返回负整数，表示o1小于o2。

~~~java
//定制排序
    //从低到高的方式排序
    public void test1(){
        String[] arr = new String[]{"AA","CC","KK","MM","GG","JJ","DD"};
        Arrays.sort(arr, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                if (o1 instanceof String && o2 instanceof String){
                    String a = (String)o1;
                    String b = (String)o2;
                    return -a.compareTo(b);
                }
                throw new RuntimeException("传入的数据类型不符合");
            }
        });
        System.out.println(Arrays.toString(arr));
    }
~~~

4. Comparable接口与Comparator的使用的对比：

   ①Comparable接口的方式一旦一定，保证Comparable接口实现类的对象在任何位置都可以比较大小。

   ②Comparator接口属于临时性的比较。

### 其他类 

#### System 类

1. 其内部的成员变量和成员方法都是static的，可以之间通过类进行调用
2. 相关方法

​	①native long currentTimeMillis()返回当前时间

​	②void exit(int status)：退出程序 status为0 正常退出 status不为0 异常退出 

​	③void gc()：垃圾回收方法，守护线程

​	④String getProperty(String key)：获取系统相关的属性

![相关属性](https://s4.ax1x.com/2022/02/23/bCVTFf.png)

#### Math 类

![Math类](https://s4.ax1x.com/2022/02/23/bCZpkV.png)

#### BigInteger 和 BIgDecimal 类

BigInteger： java.math包的BigInteger可以表示不可变的任意精度的整数。BigInteger 提供 所有 Java 的基本整数操作符的对应物，并提供 java.lang.Math 的所有相关方法。

![常用方法](https://s4.ax1x.com/2022/02/23/bCZeTx.png)

BIgDecimal：BigDecimal类支持不可变的、任意精度的有符号十进制定点数。

![常用方法](https://s4.ax1x.com/2022/02/23/bCZNAP.png)

---

## 枚举类

1. 概念：类的对象只有有限个，确定的
2. 当需要定义一组常量时，可以考虑使用枚举类
3. 当枚举类中只有一个对象时可以作为单例模式的实现方式

​	①自定义枚举类

~~~java
//自定义枚举类
class Season{
    //1.声明Season对象的属性:private final修饰
    private final String season;
    private final String seasonDes;

    //2.私有化类的构造器,并给对象属性赋值
    private Season(String season,String seasonDes){
        this.season = season;
        this.seasonDes = seasonDes;
    }
    //3.提供当前枚举类的多个对象：public static final的
    public static final Season SPRING = new Season("春天","万物复苏");
    public static final Season SUMMER = new Season("夏天","夏日炎炎");

    //4.获取枚举类对象的属性
    public String getSeason() {
        return season;
    }

    public String getSeasonDes() {
        return seasonDes;
    }
    @Override
    //4.提供toString()
    public String toString() {
        return "Season{" + "season='" + season + '\'' + ", seasonDes='" + seasonDes + '\'' + '}';
    }
~~~

​	②使用enum关键字定义枚举类：此时定义的枚举类默认继承于java.lang.Enum类

~~~java
   //使用enum关键字创建枚举类
enum Season1{
    //1.提供当前枚举类的对象，多个对象之间用","隔开，末尾对象";"结束
    SPRING("春天","万物复苏"),
    SUMMER("夏天","夏日炎炎");

//2声明Season对象的属性:private final修饰
    private final String season;
    private final String seasonDes;

    //2.私有化类的构造器,并给对象属性赋值
    private Season1(String season,String seasonDes){
        this.season = season;
        this.seasonDes = seasonDes;
    }
    //4.获取枚举类对象的属性
    public String getSeason() {
        return season;
    }

    public String getSeasonDes() {
        return seasonDes;
    }
}
~~~

4. Enum类中的常用方法：

   values()方法：返回枚举类型的对象数组。该方法可以很方便地遍历所有的枚举值。

   valueOf(String str)：可以把一个字符串转为对应的枚举类对象。要求字符串必须是枚举类对象的“名字”。如不是，会有运行时异常：IllegalArgumentException。

   toString()：返回当前枚举类对象常量的名称

5. 使用enum关键字定义的枚举类实现接口的情况

   情况一：实现接口，在enum类中实现抽象方法

   情况二：让枚举类的对象分别实现接口中的抽象方法

~~~java
//情况二
enum Season1 implements info{
    //1.提供当前枚举类的对象，多个对象之间用","隔开，末尾对象";"结束
    SPRING("春天","万物复苏"){
        public void show(){
            System.out.println("这是春天");
        }
    },
    SUMMER("夏天","夏日炎炎"){
        public void show(){
            System.out.println("这是夏天");
        }
    };
~~~

---

### 注解(Annotation)

1. 概念：代码里的特殊标记，在jdk5.0新增

​	框架 = 注解 + 反射 + 设计模式

2. 使用场景

​	①生成文档相关的注解

​	②在编译时进行格式检查(JDK内置的三个基本注解)

​	@Override: 限定重写父类方法, 该注解只能用于方法

​	@Deprecated: 用于表示所修饰的元素(类, 方法等)已过时。通常是因为所修饰的结构危险或存在更好的选择

​	@SuppressWarnings: 抑制编译器警告

​	③跟踪代码依赖性，实现替代配置文件功能

3. 自定义注解(参照@SuppressWarnings定义)

   ① 注解声明为：@interface

   ② 内部定义成员，通常使用value表示

   ③ 可以指定成员的默认值，使用default定义

   ④ 如果自定义注解没有成员，表明是一个标识作用。

​	说明：如果注解有成员，在使用注解时，需要指明成员的值；

​			自定义注解必须配上注解的信息处理流程(使用反射)才有意义。

​     	   自定义注解通过都会指明两个元注解：Retention、Target

4. jdk5.0新增的4种元注解

​	元注解：对现有的其他注解的定义

​	Retention：指定所修饰的 Annotation 的生命周期：SOURCE\CLASS（默认行为）\RUNTIME

​           		 只有声明为RUNTIME生命周期的注解，才能通过反射获取。

​	Target:用于指定被修饰的 Annotation 能用于修饰哪些程序元素

​	Documented:表示所修饰的注解在被javadoc解析时，保留下来。(使用较少)

​	Inherited:被它修饰的 Annotation 将具有继承性。(使用较少)

5. jdk8.0新增的注解：可重复注解、类型注解

   可重复注解：① 在MyAnnotation上声明@Repeatable，成员值为MyAnnotations.class

   ​						② MyAnnotation的Target和Retention等元注解与MyAnnotations相同。

​		类型注解：ElementType.TYPE_PARAMETER 表示该注解能写在类型变量的声明语句中（如：泛型声明）。

​							ElementType.TYPE_USE 表示该注解能写在使用类型的任何语句中。

---

## Java集合

1. 集合、数组都是针对多个数据进行存储的操作，都是java容器。(此时储存的位置都在内存层面，不涉及数据库)
2. 数组在储存数据方面的特点

​		①数据在初始化完成后，数组的长度就不可再修改

​		②数组定义完成，其元素的类型也就确定了，只能针对相应类型数据进行处理

​		③数组提供的方法有限，对于增删改查等操作不便，效率较低

​		④数组存储数据是有序、可重复的，对于无序、不可重复的数据无法执行

3. java集合分为了Collection接口 和 Map接口 两种体系

​		Collection 单例数据 ：定义了存取一个一个的对象

​		 ①List接口(元素有序、可重复的集合)  --> “动态”数组 

​				主要实现类：ArrayList：作为list接口主要实现类，线程不安全，效率高，底层使用Object[] 存储

​										LinkedList：对于频繁的插入和删除操作，使用LinkList比ArrayList效率更高，底层使用双向链表存储

​										Vector：作为list接口的古老实现类 1.0版本已存在，线程安全，效率较低。底层使用Object[] 存储

​		②set接口(元素无序、不可重复的集合) --> 普通概念上的集合  **考虑处理重复数据可用set接口**

​				主要实现类：HashSet：作为Set接口 的主要实现类；线程是不安全的，可以存储null值

​									   	--LinkedHashSet：作为HashSet的子类；遍历其内部数据可以按照添加的顺序遍历

​									   TreeSet：可以按照添加对象的指定属性，进行排序（对象必须是属于同一个类）

​		Map 双列数据：保存具有映射关系“key-value”的一对数据 --> y = f(x)

​				主要实现类：HashMap：作为Map的主要实现类，线程不安全，效率高，可以存储null的key和value

​											--linkedHashMap：作为HashMap的子类，保证在遍历map元素时，可以按照添加的顺序实现遍历  

​																		原因：在原有的HashMap底层结构基础上，添加了一对指针，指向前一个和后一个元素。

​																			对于频繁的遍历操作，此类执行效率高于HashMap。

​										TreeMap(其底层使用红黑树)：保证按照添加的key-value对进行排序，实现排序遍历。此时考虑key的自然排序或定制排序。

​										Hashtable：作为Map的古老实现类，线程安全，效率低，不可以存储null的key和value

​											--Properties：作为Hashtable的子类，常用于处理配置文件，且key和value都是String类型

补充：HashMap的底层：数组+链表  （jdk7及之前）  数组+链表+红黑树 （jdk 8）

### Collection接口常用方法

**规则：向Collection接口的实现类的对象添加数据时，要求此数据所在的类必须重写equals()方法**

1. add(Object e)：将元素e添加到当前集合中
2. size() ：获取添加元素的个数
3. addAll(Collection c) ：将另一个集合的元素添加到这个集合当中
4. clear() ：删除集合内的元素
5. isEmpty() ：判断当前集合是否为空 实际上是判断集合的长度是否为0
6. contains(Object obj) 判断当前集合是否包含obj
7. containsAll(Collection c1) 判断形参c1的所有元素是否都在当前集合中
8. remove(Object obj) 移除当前集合中的形参元素
9. removeAll(Collection c) 移除当前集合中的另一个集合的所有元素
10. retainAll(Collection c) 获取当前集合和形参列表内的集合的共同元素并返回到当前集合
11. equals(Object obj) 形参也必须是集合，用于比较两个集合的元素是否相同
12. hashCode() 返回当前对象的哈希值
13. toArray()：集合---> 数组扩展        数组  ---> 集合 ：调用Arrays类的静态方法asList()

比较特殊的方法 14.iterator()：返回iterator接口的实例，用于遍历集合元素，使用迭代器Iterator接口

内部方法：1. hasNext() 、 next() 、remove()

说明：①集合对象每次调用iterator()方法都得到一个全新的迭代器对象，默认游标都在集合的第一个元素之前。

​			②内部的remove(),可以在遍历的时候，删除集合中的元素。此方法不同于集合直接调用remove()

​			③Iterator中的remove()方法，在未调用next()之前不可调用remove()方法以及

​			已经调用了remove方法，再调用remove两种情况都会报IllegalStateException。

![执行原理](https://s4.ax1x.com/2022/02/23/bCZ01g.png)

~~~java
 		Collection c = new ArrayList();
        //add(Object e)
        c.add("he");
        c.add(145);//自动装箱变成了Integer包装类
        //size() 
        System.out.println(c.size());
        //addAll(Collection c) 
        Collection c1 = new ArrayList();
        c1.add(123);
        c1.add("hello");
        c.addAll(c1);
        //clear() 
        c1.clear();
        //isEmpty()
        System.out.println(c1.isEmpty());
//罗列部分使用方法 更多位于JavaSenior 06 的list包下 
~~~

---

#### 增强For循环 Foreach

1. 在jdk5.0新增，主要是用于遍历集合、数组。其内部还是调用的迭代器

2. 格式： for(集合中元素的类型 局部变量名 : 集合对象){}


```
   // for(集合中元素的类型 局部变量名 : 集合对象){}
   for( Object obj : c1){
       System.out.println(obj);
   }
```

---

#### List接口

##### List接口实现类异同

ArrayList、LinkedList 和 Vector的异同？

相同点：三个都是List接口的实现类，存储的数据都是有序的，可重复的。

不同点：见上方实现类处。

三者的源码分析：

1. ArrayList的源码分析：

​	① jdk 7情况下

​	ArrayList list = new ArrayList();//底层创建了长度是10的Object[]数组elementData

​	list.add(123);//elementData[0] = new Integer(123);

​	...

​	list.add(11);//如果此次的添加导致底层elementData数组容量不够，则扩容。

​	**默认情况下，扩容为原来的容量的1.5倍，同时需要将原有数组中的数据复制到新的数组中**

结论：建议开发中使用带参的构造器：ArrayList list = new ArrayList(int capacity)

​	②jdk 8中ArrayList的变化：

​	ArrayList list = new ArrayList();//底层Object[] elementData初始化为{}.并没有创建长度为10的数组

​	list.add(123);//第一次调用add()时，底层才创建了长度10的数组，并将数据123添加到elementData[0]

​	后续的添加和扩容操作与jdk 7 无异。

​	③小结：jdk7中的ArrayList的对象的创建类似于单例的饿汉式，而jdk8中的ArrayList的对象的创建类似于单例的懒汉式，延迟了数组的创建，节省内存。

2. LinkedList的源码分析：

其中，Node定义为：体现了LinkedList的双向链表的说法

~~~java
	LinkedList list = new LinkedList(); 内部声明了Node类型的first和last属性，默认值为null
	...
​	list.add(test);//将test封装到Node中，创建了Node对象。
private static class Node<E> {
   		E item;
​     	Node<E> next;
​    	Node<E> prev;
 Node(Node<E> prev, E element, Node<E> next) {
 			this.item = element;
 			this.next = next;
			 this.prev = prev;
 }
 }	
~~~

3. Vector的源码分析：jdk7和jdk8中通过Vector()构造器创建对象时，底层都创建了长度为10的数组。**在扩容方面，默认扩容为原来的数组长度的2倍**

##### List接口常用方法

oid add(int index, Object ele):在index位置插入ele元素

boolean addAll(int index, Collection eles):从index位置开始将eles中的所有元素添加进来

Object get(int index):获取指定index位置的元素

int indexOf(Object obj):返回obj在集合中首次出现的位置

int lastIndexOf(Object obj):返回obj在当前集合中末次出现的位置

Object remove(int index):移除指定index位置的元素，并返回此元素

Object set(int index, Object ele):设置指定index位置的元素为ele

List subList(int fromIndex, int toIndex):返回从fromIndex到toIndex位置的子集合

总结：常用方法

增：add(Object obj)

删：remove(int index) / remove(Object obj)

改：set(int index, Object ele)

查：get(int index)

插：add(int index, Object ele)

长度：size()

遍历：① Iterator迭代器方式    	②增强for循环	③ 普通的循环

~~~java
 	ArrayList list1 = new ArrayList();
        list1.add(1);
        list1.add(2);
        list1.add(3);

        ArrayList list2 = new ArrayList();
        list2.add(4);
        list2.add(5);
        list2.add(6);

    	list1.add(list2);//4个元素，这里是将list2所有元素作为一个整体加入list1
        list1.addAll(list2);//6个元素，这里是将list2所有元素分为一个个元素加入list1
        
~~~

#### Set接口

##### HashSet

以HashSet为例，Set接口使用的都是Collections中的方法，HashSet的底层是以数组加链表的结构存储

要求：①向Set中添加数据，其所在的类一定要重写hashCode() 和 equals() 方法

​			②重写的hashCode() 和 equals() 方法要保存一致性，即相同的对象必须具有相等的散列码

1. 无序性：与随机性不同。存储的数据在底层数组当中并非按照数组索引的顺序添加，而是根据数据的哈希值决定

2. 不可重复性：添加的元素按照equals()方法判断时，不能返回true。即相同的元素只能添加一个

3. 添加元素的过程：在向HashSet中添加元素a，首先调用元素a所在类的hashcode()方法，计算元素a的哈希值，此哈希值通过某种算法计算出在HashSet底

      层数组中存放位置（即为索引位置），判断数组此位置上是否已经有元素；如果此位置上无元素，则直接添加，如果此位置上有其他元素(或以链表形式存

      在的多个元素)，则比较元素a与元素b的hash值，在这种情况下，如果hash值不相同，则元素a添加成功；如果hash值相同，进而调用元素a所在类的

      equals()方法进行比较：如果返回true，则添加失败，如果返回是false，则添加成功

   对于调用equals()方法比较之后添加成功的元素与已经存在指定索引位置的元素以链表的方式连接存储。

   简称七上八下

   jdk7：元素a放到数组中，指向原来的元素。

   jdk8：原来的元素在数组中，指向元素a。

##### LinkedHashSet

LinkedHashSet作为HashSet的子类，在添加数据的同时还维护两个引用，记录了前一个数据 和 后一个数据。

好处：对于频繁的遍历操作，LinkedHashSet的效率要高于HashSet

![底层结构图](https://s4.ax1x.com/2022/02/23/bCZ4c4.png)TreeSet

##### TreeSet

1. 向TreeSet添加的数据必须是同一个类
2. 两种排序方式：自然排序 和 定制排序
3. 自然排序中，比较两个对象是否相同的标准为：compareTo() 返回0，就不再是equals()。
4. 定制排序中，比较两个对象是否相同的标准为：compare()返回0，不再是equals().

![二叉树Tree](https://s4.ax1x.com/2022/02/23/bCZou9.png)

### Map接口

1. 对于Map结构的理解

Map中的key:无序的、不可重复的，使用Set存储所有的key  ---> key所在的类要重写equals()和hashCode() （以HashMap为例）

Map中的value:无序的、可重复的，使用Collection存储所有的value --->value所在的类要重写equals()

一个键值对：key-value构成了一个Entry对象。

Map中的entry:无序的、不可重复的，使用Set存储所有的entry

#### HashMap的底层实现原理

以jdk7为例

①HashMap map = new HashMap():

​	在实例化以后，底层创建了长度是16的一维数组Entry[] table。

​	map.put()；。。。。

​	map.put(key1,value1):

​	首先，调用key1所在类的hashCode()计算key1哈希值，此哈希值经过某种算法计算以后，得到在Entry数组中的存放位置。

​	判断数组此位置上是否已经有元素；如果此位置上无元素，则直接添加，如果此位置上有其他元素(或以链表形式存在的多个元素)，

​	则比较key1与当前已在此位置上的数据的hash值，在这种情况下，如果hash值不相同，则key1添加成功；如果hash值相同，

​	进而调用key1所在类的equals()方法进行比较：如果返回true，则使用key1的value值替代此位置上数据的value值，如果返回是false，则添加成功。

​	对于调用equals()方法添加成功的此时key1-value1和原来的数据以链表的方式存储。

​	而当**超出Threshold临界值(且要存放的位置非空)时进行扩容**。默认的扩容方式：扩容为原来容量的2倍，并将原有的数据复制过来。

​	② jdk8 相较于jdk7在底层实现方面的不同：

​	new HashMap():底层没有创建一个长度为16的数组

​	jdk 8底层的数组是：Node[],而非Entry[]

​	首次调用put()方法时，底层创建长度为16的数组

​	jdk7底层结构只有：数组+链表。jdk8中底层结构：数组+链表+红黑树。

​	形成链表时，七上八下（jdk7:新的元素指向旧的元素。jdk8：旧的元素指向新的元素）

​	当数组的某一个索引位置上的元素以链表形式存在的数据个数 > 8 且当前数组的长度 > 64时，此时此索引位置上的所数据改为使用红黑树存储。



​	DEFAULT_INITIAL_CAPACITY : HashMap的默认容量，16

​	DEFAULT_LOAD_FACTOR：HashMap的默认加载因子：0.75

​	threshold：扩容的临界值，=容量*填充因子：16 * 0.75 => 12

​	TREEIFY_THRESHOLD：Bucket中链表长度大于该默认值，转化为红黑树:8

​	MIN_TREEIFY_CAPACITY：桶中的Node被树化时最小的hash表容量:64

#### LinkedHashMap的底层原理

(了解即可)

~~~java
			static class Entry<K,V> extends HashMap.Node<K,V> {
             Entry<K,V> before, after;//能够记录添加的元素的先后顺序
             Entry(int hash, K key, V value, Node<K,V> next) {
                super(hash, key, value, next);
             }
         }
~~~

#### Map的常用方法

添加、删除、修改操作：

 Object put(Object key,Object value)：将指定key-value添加到(或修改)当前map对象中

 void putAll(Map m):将m中的所有key-value对存放到当前map中

 Object remove(Object key)：移除指定key的key-value对，并返回value

 void clear()：清空当前map中的所有数据

 元素查询的操作：

 Object get(Object key)：获取指定key对应的value

 boolean containsKey(Object key)：是否包含指定的key

 boolean containsValue(Object value)：是否包含指定的value

 int size()：返回map中key-value对的个数

 boolean isEmpty()：判断当前map是否为空

 boolean equals(Object obj)：判断当前map和参数对象obj是否相等

 元视图操作的方法：

 Set keySet()：返回所有key构成的Set集合

 Collection values()：返回所有value构成的Collection集合

 Set entrySet()：返回所有key-value对构成的Set集合

 总结：常用方法：

添加：put(Object key,Object value)

删除：remove(Object key)

修改：put(Object key,Object value)

查询：get(Object key)

长度：size()

遍历：keySet() / values() / entrySet()

#### TreeMap

向TreeMap中添加key-value，要求key必须是由同一个类创建的对象

原因是要按照key进行排序：自然排序 、定制排序

#### Properties

用于处理属性文件，Properties里的key和value都是字符串类型的

load()；加载流文件

getProperty()；获取流文件中对应的属性

~~~java
public static void main(String[] args) {
        FileInputStream fils = null;
        try {
            Properties properties = new Properties();

            fils = new FileInputStream("jdbc.properties");
            properties.load(fils);

            String name = properties.getProperty("name");
            String password = properties.getProperty("password");

            System.out.println("name=" + name + ",password=" + password);

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (fils != null)
            try {
                fils.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
~~~

---

### Collections工具类

 操作Collection 和 Map 的工具类，此工具内的方法都是静态方法

1. 常用方法

reverse(List)：反转 List 中元素的顺序

shuffle(List)：对 List 集合元素进行随机排序

sort(List)：根据元素的自然顺序对指定 List 集合元素按升序排序

sort(List，Comparator)：根据指定的 Comparator 产生的顺序对 List 集合元素进行排序

swap(List，int， int)：将指定 list 集合中的 i 处元素和 j 处元素进行交换

Object max(Collection)：根据元素的自然顺序，返回给定集合中的最大元素

Object max(Collection，Comparator)：根据 Comparator 指定的顺序，返回给定集合中的最大元素

Object min(Collection)

Object min(Collection，Comparator)

int frequency(Collection，Object)：返回指定集合中指定元素的出现次数

void copy(List dest,List src)：将src中的内容复制到dest中

boolean replaceAll(List list，Object oldVal，Object newVal)：使用新值替换 List 对象的所有旧值

2. Collections 类中提供了多个 synchronizedXxx() 方法，该方法可使将指定集合包装成线程同步的集合，

   从而可以解决 多线程并发访问集合时的线程安全问题

---

## 泛型

1. 是在jdk 5.0 新增的 
2. 在集合中使用泛型

​		①集合接口或集合类在jdk5.0时都修改为带泛型的结构

​		②实例化集合类时，可以指明具体的泛型类型

​		③在指明完成后，在集合类或接口中凡是定义类或接口时，内部结构使用到类的泛型的位置内部结构

​		（比如：方法、构造器、属性等）使用到类的泛型的位置，都指定为实例化的泛型类型。

​			比如：add(E e)  --->实例化以后：add(Integer e)

​		④泛型的类型必须是一个类，不能是基本数据类型，而要用到基本数据类型要替换成包装类

​		⑤在实例化的同时没有指明泛型的类型，则默认类型为java.lang.Object类型

### 自定义泛型结构：泛型类、泛型接口、泛型方法

1. 定义自定义泛型类、泛型接口的注意点：

​	①子类在继承带泛型的父类时，指明了泛型类型。则实例化子类对象时，不再需要指明泛型。

​	②**泛型不同的引用不能相互赋值。**

​	③静态方法中不能使用类的泛型参数。原因是静态方法随着类的加载而加载，泛型参数是在实例化对象的时候才出现的

​	④异常类不能声明为泛型

​	⑤类的内部结构可以使用类的泛型 例如(在构造器内部创建泛型的数组) T[ ] arr = (T[ ]) new Object[10]; 

2. 定义泛型方法的注意点：

​	①泛型方法所属的类是不是泛型类与其没有关系

​	②泛型方法在调用时，指明泛型参数的类型。

​	③泛型方法，可以声明为静态的。原因：泛型参数是在调用方法时确定的。并非在实例化类时确定。

~~~java
class Java<T> {//自定义的泛型类
    String name;
    int age;
    T javaClass;
    public Java() {}
    
       //泛型方法
    public static <E> List<E> get(E[] arr){
        ArrayList<E> list = new ArrayList<>();
        for(E e : arr){
            list.add(e);
        }
        return list;
    }
~~~

3. 泛型在继承方面的说明

~~~java
①泛型不具备继承关系，即使类A是类B的父类，但list<A> 和 list<B> 两者是并列关系，不是继承关系 
②类A是类B的父类，但A<list> 是 B<list>  的父类，具备了继承关系 
~~~


4. 通配符的使用

​	通配符：?

~~~java
①类A是类B的父类，list<A>和list<B>是没有关系的，二者共同的父类则是：list<?>
~~~

​	②对于list<?>来说添加数据只能往内添加null值，其余数据类中不允许添加；但是允许读取数据，读取的数据类型返回类型Object，原因是任何一个类对象的顶

级父类都是Object。

​	③有限制条件的通配符的使用说明

? extends A:（？<= A）

​			G<? extends A> list ，此时可以赋值给list的只有类A本身或其子类

​			赋值后通过list去get读取数据只能以上限值接口，即将子类的实例指向父类的引用

​			赋值后通过list去add写入数据编译不能通过

? super A:（？>= A）

​			G<? super A> list，此时可以赋值给list的只有类A本身或其父类

​			赋值后通过list去get数据只能以上限值接口，此处以Object接受，即将子类的实例指向父类的引用

​			赋值后通过list去add写入数据，只能add(类A的本身或其子类的对象)，不能添加类A的父类对象

~~~java
		List<? extends Person> list1 = null;
        List<? super Person> list2 = null;

        List<Student> list3 = new ArrayList<Student>();
        List<Person> list4 = new ArrayList<Person>();
        List<Object> list5 = new ArrayList<Object>();

        list1 = list3;
        list1 = list4;
//        list1 = list5;编译不通过

//        list2 = list3;编译不通过
        list2 = list4;
        list2 = list5;
~~~

---

## IO流

### File类

1. File类的一个对象，就代表了一个文件或一个文件目录(文件夹)

2. File类声明在java.io的包下

3. File类内的方法并未对文件实际内容进行修改，需要进行操作则使用IO流完成

   ，通常情况下，File类的对象是以参数的形式传递到流的构造器中，指明了读取或写入的“终点”

4. File类的实例化

​		File(String filePath)

​        File(String parentPath,String childPath)

​        File(File parentFile,String childPath)

**相对路径：相较于某个路径下，指明的路径				绝对路径：包含盘符在内的文件或文件目录的路径**

5. File的方法

​	public String getAbsolutePath()：获取绝对路径

​	public String getPath() ：获取路径

​	public String getName() ：获取名称

​	public String getParent()：获取上层文件目录路径。若无，返回null

​	public long length() ：获取文件长度（即：字节数）。不能获取目录的长度。

​	public long lastMo dified() ：获取最后一次的修改时间，毫秒值

​	public boolean isDirectory()：判断是否是文件目录

​	public boolean isFile() ：判断是否是文件

​	public boolean exists() ：判断是否存在

​	public boolean canRead() ：判断是否可读

​	public boolean canWrite() ：判断是否可写

​	public boolean isHidden() ：判断是否隐藏



较为重要的

​	如下的两个方法适用于文件目录：

​	public String[] list() ：获取指定目录下的所有文件或者文件目录的名称数组 返回的是只是文件名字

​	public File[] listFiles() ：获取指定目录下的所有文件或者文件目录的File数组 返回的是绝对路径+文件名字

​	public boolean renameTo(File dest):把文件重命名为指定的文件路径

​     比如：file1.renameTo(file2)为例：

​     要想保证返回true,需要file1在硬盘中是存在的，且file2不能在硬盘中存在。

​	 创建硬盘中对应的文件或文件目录

​	public boolean createNewFile() ：创建文件。若文件存在，则不创建，返回false

​	public boolean mkdir() ：创建文件目录。如果此文件目录存在，就不创建了。如果此文件目录的上层目录不存在，也不创建。

​	public boolean mkdirs() ：创建文件目录。如果此文件目录存在，就不创建了。如果上层文件目录不存在，一并创建

​		删除磁盘中的文件或文件目录

​	public boolean delete()：删除文件或者文件夹

​    删除注意事项：Java中的删除不走回收站。


### Java IO 原理

输入input /  输出output  从程序或者内存的角度出发

1. 对于文本文件，使用字符流处理
2. 对于非文本文件，使用字节流处理

---

### 流的分类

数据单位的不同：字节流(8 bit)  和 字符流(16 bit)

流向的不同：输入流  输出流

流的角色的不同：节点流 	处理流


### 流的体系结构

​					 				字节输入流					字节输出流 				字符输入流	 		字符输出流

抽象基类					InPutStream				OutPutStream	 			Reader					Writer

文件流(节点流)		  FileInPutStream		FileOutPutStream	 	  FileReader		 	 FileWriter

缓冲流(处理流)		BufferInPutStream	BufferOutPutStream	BufferReader		BufferWriter

节点流|   | 缓冲流
:-:|-|:-:
FileInputStream   (read(byte[] b)) | |BufferedInputStream (read(byte[] b))
  FileOutputStream  (write(byte[] b,0,len) | |BufferedOutputStream (write(byte[] b,0,len) / flush()
FileReader (read(char[] c))  | |BufferedReader (read(char[] c) / readLine())
  FileWriter (write(char[] c,0,len)  | |BufferedWriter (write(char[] c,0,len) / flush()

### 字符流

1. 数据的读入

​	①read():返回读入的一个字符。如果文件返回-1，则文件读取结束

​	②异常的处理try-catch-finally：保证流资源不因异常的影响正常执行关闭操作 close();

​	③数据的读入要求文件必须存在，否则报FiileNotFoundException

2. 数据的写出

​	①输出操作，对应的File文件可以不存在。

​	②如果不存在，在输出的过程中，则会自动创建该文件；如果存在，则根据流使用的构造器进行不同的操作

​			如果流使用的构造器是：FileWriter(file,false) / FileWriter(file):对原有文件的覆盖

​        	如果流使用的构造器是：FileWriter(file,true):不会对原有文件覆盖，而是在原有文件基础上追加内容

3. 不能使用字符流来处理图片等字节数据，即使创建成功，也无法正常显示


### 字节流

略

### 处理流


#### 缓冲流

1. 作用：提高流的读取、写入的速度  原因是内部提高了缓冲区 8192byte，默认情况是8kb
2. 处理流：即在已有的流的基础上在其外层再“包裹”一层

#### 转换流

1. 属于字符流

​	InPutStreamReader：将InputStream输入流转换为Reader的输入流

​	OutPutStreamWriter：将Write输出流转换为OutPutStream输出流

说明：编码决定了解码的方式，因在解码的时候需要根据文件当时保存的格式决定解码的字符集

2. 作用是 提供了在字节流和字符流之间的转换

### 其他类型的流

#### 标准输入、输出流

1. System.in:标准的输入流，默认从键盘输入
2. System.out:标准的输出流，默认从控制台输出
3. System类的setIn(InputStream is) / setOut(PrintStream ps)方式重新指定输入和输出的流。

 #### 打印流

1. 作用：将基本数据类型的数据转化为字符串输出
2. PrintStream、PrintWriter

#### 数据流

1. DataInputStream(读取数据)和 DataOutputStream(写入数据)
2. 作用：用于读取或写出基本数据类型的变量或字符串
3. 注意点：读取不同类型的数据的顺序要与当初写入文件时，保存的数据的顺序一致！

~~~java
public void test3() throws IOException {
        //写出数据
        DataOutputStream dos = new DataOutputStream(new FileOutputStream("data.txt"));
        dos.writeUTF("。。。。");
        dos.flush();//刷新操作，将内存中的数据写入文件
        dos.writeInt(23);
        dos.flush();
        dos.writeBoolean(true);
        dos.flush();
     	//关闭流
        dos.close();
    
 public void test4() throws IOException {
        //1.读取数据
        DataInputStream dis = new DataInputStream(new FileInputStream("data.txt"));
        String name = dis.readUTF();
        int age = dis.readInt();
        boolean isMale = dis.readBoolean();

        System.out.println("name = " + name);
        System.out.println("age = " + age);
        System.out.println("isMale = " + isMale);
        //关闭流
        dis.close();
~~~

### 对象流

1. 属于字节流

   ObjectOutPutStream  ：序列化过程  将java对象从内存中保存到磁盘或通过网络节点传输出去 。 xx.writeObject()方法

   ObjectInPutStream ：反序列化过程  将java对象从磁盘或通过网络节点读取到内存中，还原该java对象 。 xx.readObject()方法

2. 作用：用于存储和读取基本数据类型和对象

3. 对象序列化机制：允许把内存中的Java对象转换成平台无关的二进制流，从而允许把这种二进制流持久地保存在磁盘上，

   或通过网络将这种二进制流传 输到另一个网络节点。当其它程序获取了这种二进制流，就可以恢复成原 来的Java对象

4. 一个java对象需要序列化或反序列化，则要求其所在的类必须是可序列化的。

5. 自定义类可序列化的要求

   ①需要实现接口：Serializable

​		②此类需要提供一个全局常量：serialVersionUID （例如，public static final long serialVersionUID = 4754L;）

​		③除了此自定义类需要实现Serializable接口之外，还必须保证其内部所有属性

​			也必须是可序列化的。（默认情况下，基本数据类型可序列化）

说明：ObjectOutputStream和ObjectInputStream不能序列化static和transient修饰的成员变量

### RandomAccessFile类

1. 此类直接继承于java.lang.Object类，实现了DataInput和DataOutput接口

2. 既可以作为输入流，也可以作为一个输出流

3. 如果RandomAccessFile作为一个输出流存在，写出到的文件不存在，则会自动创建该文件；

   如果写出到的文件存在，则会对原有文件内容进行覆盖。（默认情况下，从头覆盖）

4. 可以通过实现RandomAccessFile“插入”数据的效果

​		long getFilePointer()：获取文件记录指针的当前位置 

​		void seek(long pos)：将文件记录指针定位到 pos 位

---

## 网络编程

### 通信要素1

IP和端口号

作用：准确地定位网络上一台或多台主机；定位主机上的特定的应用

#### IP

1. 概念：唯一的标识 Internet 上的计算机（通信实体）
2. 在Java中使用InetAddress类代表IP
3. IP分类：IPv4 和 IPv6 ; 万维网 和 局域网
4. 实例化InetAddress的方法:①InetAddress.getByName(String host)   ② InetAddress.getLocalHost()
5. 两个常用方法：获取此IP地址的主机名 getHostName() 、返回文本显示中的IP地址字符串  getHostAddress()

#### 端口

1. 概念：正在计算机上运行的进程。

2. 要求：不同的进程有不同的端口号

3. 范围：被规定为一个 16 位的整数 0~65535。
4. 端口号与IP地址的组合得出一个网络套接字：Socket

### 通信要素2

提供网络通信协议：TCP/IP参考模型（应用层、传输层、网络层、物理+数据链路层）

作用：找到主机后可靠高效地进行数据传输

#### TCP 与 UDP

TCP协议：传输控制协议TCP(Transmission Control Protocol）

1. 使用TCP协议前，须先建立TCP连接，形成传输数据通道 
2. 传输前，采用“三次握手”方式，点对点通信，是可靠的 
3. TCP协议进行通信的两个应用进程：客户端、服务端。 
4. 在连接中可进行大数据量的传输  
5. 传输完毕，需释放已建立的连接，效率低

UDP协议：用户数据报协议UDP(User Datagram Protocol)

1. 将数据、源、目的封装成数据包，不需要建立连接
2. 每个数据报的大小限制在64K内 
3. 发送不管对方是否准备好，接收方收到也不确认，故是不可靠的 
4. 可以广播发送 
5. 发送数据结束时无需释放资源，开销小，速度快 

### URL编程

1. URL:统一资源定位符，对应着互联网的某一资源地址

2. http://localhost:4000/link/1.txt?user=xx&name=xx

   协议   主机名    端口号  资源地址           参数列表

   URL url = new URL(http://localhost:4000/link/1.txt?user=xx&name=xx)

3. 常用方法

​	public String getProtocol( ) 获取该URL的协议名 

​	public String getHost( ) 获取该URL的主机名 

​	public String getPort( ) 获取该URL的端口号 

​	public String getPath( ) 获取该URL的文件路径 

​	public String getFile( ) 获取该URL的文件名 

​	public String getQuery( ) 获取该URL的查询名

---

## Java反射

1. Reflection(反射)是被视为动态语言的关键，允许程序通过API取得并修改一些类的内部结构

2. 反射机制和面向对象的封装性并不矛盾。

   相对的理解是封装性即自己内部的不建议使用，而是去使用已经提供好的公共的属性或方法

   而对于反射机制而言，相当于是不采纳封装性的建议，通过此机制强行使用其内部的属性或方法

3. 关于Java.lang.class类的理解

​	编译过程：javac.exe命令--->.class结尾字节码文件(一个或多个)  

​	类的加载过程：(只有java.exe后此时类才加载到内存中，此前都不属于类的加载过程)

​	java.exe(对某个字节码文件解释运行)---->类加载到内存中

​    加载到内存中的类，我们就称为运行时类，此运行时类，就作为Class的一个实例。简而言之，Class的实例就对应着一个运行时类。

4. 可以通过不同的多种方式获取已经加载到内存中的运行时类，但都是指向了同一个实例，即地址值相同
5. Class实例对象可以是class类、interface接口、[]数组、enum枚举、annotation；注解@interface、primitive type基本数据类型、void

~~~java
		int[] i1 = new int[10];
        int[] i2 = new int[20];
        Class f = i1.getClass();
        Class s = i2.getClass();
        // 只要数组的元素类型与维度一样，就是同一个Class
        System.out.println(f == s);//true
~~~

### 获取Class实例的方式

1. 方式一：调用运行时类的属性：.class
2. 方式二：通过运行时类的对象,调用getClass()
3. 方式三：调用Class的静态方法：forName(String classPath)  参数内容为类的全路径
4. 方式四：使用类的加载器：ClassLoader  (了解)

~~~java
		//1.运行时类的属性.class
        Class<Student> studentClass = Student.class;

        //2.运行时类的对象.getClass()方法
        Student s1 = new Student("T", 20);
        Class<? extends Student> aClass = s1.getClass();

        //3.Class的静态方法 forName(String path)
        Class<?> name = Class.forName("Reflection.Student");

        //判断三个Class的实例对象地址是否相同
        System.out.println(studentClass == aClass);//true
        System.out.println(name == aClass);//true
~~~

### 类的加载器

了解，获取加载器的方法getParent();

引导类加载器：负责Java平台核心库，加载java自带的类

扩展类加载器：负责一些jar包里的类

系统类加载器：加载自定义的类

### 创建运行时类的对象

1. 方式一：调用.newInstance()方法创建对象，实际上还是调用了运行时类的空参构造器

   使用此方法的要求：① 运行时类必须提供空参构造器   ②此空参构造器的权限不小于default(缺省)

   补充知识：javabean中要求提供一个public的空参构造器。原因是：

   ​        	①便于通过反射，创建运行时类的对象

   ​      	  ②便于子类继承此运行时类时，去调用super()时，保证父类有此构造器

### 获取运行时类的完整结构

见Senior 11 工程下各个包

### 调用运行时类的指定结构

主要是：属性、方法、构造器

1. 获取指定的属性

~~~java
@Test
    public void test2() throws Exception {
        Class sd = Student.class;

        //创建运行时类的对象
        Student st = (Student) sd.newInstance();

        //1.获取运行时类中指定变量名的属性:getDeclaredField(String fieldName)
        Field name = sd.getDeclaredField("name");

        //2.保证当前属性是可访问的
        name.setAccessible(true);

        //3.修改指定对象的值
        name.set(st,"zhang");

        System.out.println(name.get(st));

    }
~~~

2. 获取指定的方法

~~~java
public void test3() throws Exception{
        Class st = Student.class;

        //创建运行时类的对象
        Student sd = (Student) st.newInstance();

        //1.获取指定的某个方法
        //getDeclaredMethod():参数1 ：指明获取的方法的名称  参数2：指明获取的方法的形参列表
        Method get = st.getDeclaredMethod("get", String.class);

        //2.保证当前属性是可访问的
        get.setAccessible(true);

        //3.调用方法的invoke():参数1：方法的调用者  参数2：给方法形参赋值的实参
        //invoke()的返回值即为对应类中调用的方法的返回值。
        Object returnGet = get.invoke(sd, "CTB");
        System.out.println(returnGet);


        /**
         * 调用静态方法
         */

        Method showDetails = st.getDeclaredMethod("showDetails");
        showDetails.setAccessible(true);
        //如果调用的运行时类中的方法没有返回值，则此invoke()返回null
        Object returnShow = showDetails.invoke(Student.class);
        System.out.println(returnShow);
    }
~~~

3. 获取指定的构造器

~~~java
public class getCons {
    @Test
    public void test() throws Exception{
        Class studentClass = Student.class;

        //1.获取指定的构造器
        //getDeclaredConstructor():参数：指明构造器的参数列表
        Constructor declaredConstructor = studentClass.getDeclaredConstructor(int.class);

        //2.保证此构造器是可访问的
        declaredConstructor.setAccessible(true);

        //3.调用此构造器创建运行时类的对象
        Student s = (Student) declaredConstructor.newInstance(20);

        System.out.println(s);
    }
}
~~~

### 动态代理

~~~java
public class ReflectProxy {
    public static void main(String[] args) {
        //创建被代理类的对象
        Student s1 = new Student();

        //调用静态方法生成动态代理类的对象，将被代理类对象填入
        //当通过代理类对象调用方法时，会自动的调用被代理类中同名的方法
        Person person = (Person) MovingProxy.getProxy(s1);
        String interest = person.interest();
        System.out.println(interest);
        person.read("肖申克的救赎");
    }
}

/*
要想实现动态代理，需要解决的问题？
问题一：如何根据加载到内存中的被代理类，动态的创建一个代理类及其对象。
问题二：当通过代理类的对象调用方法a时，如何动态的去调用被代理类中的同名方法a。
 */
//接口类
interface Person{
    void read(String book);
    String interest();
}


//被代理类
//需要实现接口类中的方法
class Student implements Person{
    public void read(String book){
        System.out.println("喜欢的书籍是：" + book);
    }

    public String interest(){
        return "read book";
    }
}


//动态代理类
class MovingProxy{
    //声明为静态方法，以便生成代理类对象直接调用
    public static Object getProxy(Object obj){//此处的obj是传入的被代理类的对象
        //newProxyInstance的第三个参数是InvocationHandler接口的实现类的对象
        InvocationHand invocation = new InvocationHand();

        //对invocation类的实例对象以传入的被代理类对象赋值，缺少此步将导致空指针异常
        invocation.getObj(obj);

        return Proxy.newProxyInstance(obj.getClass().getClassLoader(),obj.getClass().getInterfaces(),invocation);
    }
}


class InvocationHand implements InvocationHandler{
    private Object obj;

    public void getObj(Object obj){
        this.obj = obj;
    }

    //当通过代理类的对象，调用方法a时，就会自动的调用如下的方法：invoke()
    //将被代理类要执行的方法a的功能就声明在invoke()中
    //此处的proxy即为被代理类的对象，method即为代理类对象调用的方法，此方法也就作为了被代理类对象要调用的方法
    public Object invoke(Object proxy, Method method, Object[] args) throws InvocationTargetException, IllegalAccessException {
        Object invoke = method.invoke(obj, args);
        return invoke;//上述方法的返回值就作为当前类中的invoke()的返回值。
    }
}
~~~

---

## Java8的新特性

### lambda表达式

1. 格式：Comparator<Integer> c = (o1,o2) -> Integer.compare(o1,o2);

​				->：lambda操作符 或称为 箭头操作符

​				->左边：lambda形参列表(接口中抽象方法的形参列表)

​				->右边：lambda体(重写的抽象方法的方法体)

2. lambda表达式的本质：作为函数式接口的实例
3. lambda表达的多种语法格式总结：

​	->左边：lambda形参列表的参数类型可以省略(类型推断)；

​				如果lambda形参列表只有一个参数，则包裹参数的一对()也可以省略

​	->右边：lambda体应该使用一对{}包裹；

​				如果lambda体只有一条执行语句（可能是return语句），省略这一对{}和return关键字

#### 函数式接口

1. 函数式接口概念：接口中只声明了一个抽象方法

2. 自定义函数式接口可以通过@Functionnallnterface注解来检验谁否为函数式接口

3. 所有的匿名实现类都可以通过Lambda表达来写

4. 4个核心接口：①消费型接口   Consumer<T>     void accept(T t)

   ​						 ②供给型接口	Supplier<T>     T get()

   ​						 ③函数型接口	Function<T,R>   R apply(T t)

   ​						 ④断定型接口	Predicate<T>    boolean test(T t)

### 方法引用

1. 使用场景：当要传递给Lambda体的操作，已经有实现的方法了，可以使用方法引用

2. 方法引用，本质上就是Lambda表达式，而Lambda表达式作为函数式接口的实例。因此方法引用，也是函数式接口的实例。

3. 格式：  类(或对象) :: 方法名

4. 具体分为如下的三种情况：

   情况1     对象 :: 非静态方法

   情况2     类 :: 静态方法

   情况3     类 :: 非静态方法

5. 方法引用使用的要求：

   要求接口中的抽象方法的形参列表和返回值类型与方法引用的方法的形参列表和返回值类型相同！（针对于情况1和情况2）

   情况3：如果抽象方法中形参列表的第一个参数作为方法引用的方法的调用者，则可以考虑使用。

### 构造器引用

1. 和方法引用类似，函数式接口的抽象方法的形参列表和构造器的形参列表一致。抽象方法的返回值类型即为构造器所属的类的类型
2. 数组引用：大家可以把数组看做是一个特殊的类，则写法与构造器引用一致。

### Stream API说明

1. Stream关注的是对数据的运算，与CPU打交道；

   集合关注的是数据的存储，与内存打交道； 

2. ①Stream 自己不会存储元素。

   ②Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。

   ③Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行

3. Stream 执行流程：

​	① Stream的实例化

​	② 一系列的中间操作（过滤、映射、...)

​	③ 终止操作

4. ①一个中间操作链，对数据源的数据进行处理

   ②一旦执行终止操作，就执行中间操作链，并产生结果。之后，不会再被使用

#### Stream的实例化

1. 方式一：通过集合

​	①default Stream<E> stream() : 返回一个顺序流

​	②default Stream<E> parallelStream() : 返回一个并行流

2. 方式二：通过数组

​	①调用Arrays类的static <T> Stream<T> stream(T[] array): 返回一个流

3. 方式三：通过Stream的of()
4. 方式四：创建无限流(了解)

​	①迭代	 public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)

​	②生成	public static<T> Stream<T> generate(Supplier<T> s)

#### Stream的中间操作

- **filter和map的如何选择主要考虑是否改变了原来的长度，filter改变了，map则只是加工，并没有改变**

1. 筛选与切片

​	①filter(Predicate p)——接收 Lambda ， 从流中排除某些元素。

​	②limit(n)——截断流，使其元素不超过给定数量。

​	③skip(n) —— 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补

​	④distinct()——筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素

2. 映射

​	①map(Function f)——接收一个函数作为参数，将元素转换成其他形式或提取信息，

​											该函数会被应用到每个元素上，并将其映射成一个新的元素。

​	②flatMap(Function f)——接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。

3. 排序

​	①sorted()——自然排序

​	②sorted(Comparator com)——定制排序

#### Stream的终止操作

1. 匹配与查找

   ①allMatch(Predicate p)——检查是否匹配所有元素。

   ②anyMatch(Predicate p)——检查是否至少匹配一个元素。

   ③noneMatch(Predicate p)——检查是否没有匹配的元素。

   ④findFirst——返回第一个元素

   ⑤findAny——返回当前流中的任意元素

   ⑥count——返回流中元素的总个数

   ⑦max(Comparator c)——返回流中最大值

   ⑧min(Comparator c)——返回流中最小值

   ⑨forEach(Consumer c)——内部迭代

2. 归约

   ①reduce(T identity, BinaryOperator)——可以将流中元素反复结合起来，得到一个值。返回 T

   ②reduce(BinaryOperator) ——可以将流中元素反复结合起来，得到一个值。返回 Optional<T>

   ③Optional<Double> sumMoney = salaryStream.reduce(Double::sum);

3. 收集

   ①collect(Collector c)——将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总的方法

   ![相关方法](https://s4.ax1x.com/2022/02/23/bCeVgg.png)

###  Optional类

理解：为了解决Java的空指针问题

1. 创建Optional类的实例

​	Optional.of(T t) : 创建一个 Optional 实例，t必须非空；

​	Optional.empty() : 创建一个空的 Optional 实例

​	Optional.ofNullable(T t)：t可以为null

2. 常用方法

   ①of(T t):封装数据t生成Optional对象。要求t非空，否则报错。

   ②get()通常与of()方法搭配使用。用于获取内部的封装的数据value

   ③ofNullable(T t) ：封装数据t赋给Optional内部的value。不要求t非空

   ④orElse(T t1):通常与ofNullable(T t) 搭配使用。如果当前的Optional内部封装的t是非空的，则返回内部的t；

   ​																				如果内部的t是空的，则返回orElse()方法中的参数t1.

   ---

   ## JDK的其他版本特性

   ### Java9 新特性

   1. jdk目录结构的改变

   2. 模块化系统 Jigsaw --> Modularity 

      export暴露要被导入的结构 	requires引进被暴露的结构

      ![模块化系统](https://s4.ax1x.com/2022/02/23/bCel5V.png)

   3. REPL工具：jShell命令

      在dos窗口中运行java代码

   4. 语法改进：jdk9中接口允许定义自己的私有化方法

   5. 钻石操作符(泛型)与匿名内部类在java8中不能共存，在java9中则可以

   6. java 8中资源关闭操作: Java 8 中，可以实现资源的自动关闭。要求自动关闭的资源的实例化必须放在try的一对小括号中

      如下例所示： try(InputStreamReader reader = new InputStreamReader(System.in)){

      ​			 			//读取数据细节省略

      ​						 }catch (IOException e){

      ​						 e.printStackTrace(); }

   7. java9中资源关闭操作：需要自动关闭的资源的实例化可以放在try的一对小括号外。

      此时的资源属性是常量，声明为final的，不可修改

   8. String存储结构变更，由char[ ]  变为 byte[ ]

   9. 集合工厂方法：快速创建只读集合  只可以读，不可以添加数据

      Array.asList()方法创建的也是只读集合

   10. InPutStream的加强

       transferTo()，可以用来将InputStream 数据直接 传输到 OutputStream，替代了转换流的工作

   11. 增强的Stream API

        ①takeWhile()； 返回从开头开始的尽量多的元素，碰到第一个不满足的就结束筛选

        ②dropWhile()；与 takeWhile 相反，返回剩余的元素。

        ③ofNullable()；Java 8 中 Stream 不能完全为null。而在Java 9中创建一个单元素 Stream，可以包含一个非空元素，也可以创建一个空 Stream。

        ④iterate()；可以提供一个 Ppredicate (判断条件)来指定什么时候结束迭代。

   12. Optional类中stream()，允许Optional类生成一个流去遍历其内的元素

   13. Javascript引擎升级：Nashorn，允许在java虚拟机上允许js文件

   ### Java10 新特性

   1. 局部变量类型推断 

      ①使用var代替原本的数据类型，var不是关键字

   ~~~java
    		//1.声明变量时，根据所附的值，推断变量的类型
           var num = 10;
   
           var list = new ArrayList<Integer>();
           list.add(123);
   
           //2.遍历操作
           for (var i : list) {
               System.out.println(i);
               System.out.println(i.getClass());
           }
   
           //3.普通的遍历操作
           for (var i = 0; i < 100; i++) {
               System.out.println(i);
           }
   ~~~

   ​		②不能使用var替代的情景

    情况1：没有初始化的局部变量声明 

    情况2：方法的返回类型   原因是根据返回类型确定什么数据能进入 var是根据进入类型反推类型的 ，两者相互冲突，下方情况同理

   情况3：方法的参数类型 

   情况4：构造器的参数类型 

   情况5：属性 

   情况6：catch块

   ~~~java
   	//1.局部变量不赋值，就不能实现类型推断
   	//        var num ;
   
           //2.lambda表示式中，左边的函数式接口不能声明为var
   	//        Supplier<Double> sup = () -> Math.random();
   
   	//        var sup = () -> Math.random();
   
           //3.方法引用中，左边的函数式接口不能声明为var
   	//        Consumer<String> con = System.out::println;
   
   	//        var con = System.out::println;
   
           //4.数组的静态初始化中，注意如下的情况也不可以
           int[] arr = {1, 2, 3, 4};//通过前面推断后面的类型
   //        var arr = {1,2,3,4};//此处前面、后面的类型都被消除了
       }
   ~~~

   2. 集合新增创建不可变集合的方法

      ①of (jdk9新增)

      ②copyOf (jdk10新增)

   ~~~java
   //示例1：
           var list1 = List.of("Java", "Python", "C");
           var copy1 = List.copyOf(list1);
           System.out.println(list1 == copy1); // true
   
           //示例2：
           var list2 = new ArrayList<String>();
           list2.add("aaa");
           var copy2 = List.copyOf(list2);
           System.out.println(list2 == copy2); // false
   ~~~

   结论：copyOf(Xxx coll):如果参数coll本身就是一个只读集合，则copyOf()返回值即为当前的coll

   ​        	如果参数coll不是一个只读集合，则copyOf()返回一个新的集合，这个集合是只读的。

   ### Java11 新特性

   1. String中新增的方法

      ①isBlank():判断字符串是否为空白

      ②strip():去除首尾空白

      ③stripTrailing():去除尾部空格 

      ④stripLeading():去除首部空格

      ⑤repeat(int count):复制字符串

      ⑥lines().count():行数统计

   2. Optional新增的方法

      ①isPresent()；判断内部的value是否存在

      ②isEmpty()；判断内部的value是否为空

      ③orElseThrow()；value非空，返回value；否则抛异常NoSuchElementException

      ④ifPresentOrElse(Consumer action, Runnable emptyAction)；

      ​	value非空，执行参数1功能；如果value 为空，执行参数2功能

      ⑤Optional<T> or(Supplier<? extends Optional < ? extends T>> supplier)

      ​	value非空，返回对应的Optional； value为空，返回形参封装的Optional

   3. 局部变量类型推断的升级

   4. HttpClient替换原有的HttpURLConnection

   5. 更简化的编译运行文件

      即可以直接通过 java xxx.java运行java文件，不必像之前一样javac.exe编译后才能java.exe

      注意点：①执行源文件中的第一个类, 第一个类必须包含主方法。 

      ② 并且不可以使用其它源文件中的自定义类, 本文件中的自定义类是可以使用的。

   6. ZGC

