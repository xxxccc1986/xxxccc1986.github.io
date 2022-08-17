---
title: CAS与自旋锁
date: 2022-08-17 20:10:02
updated: 2022-08-17 20:10:02
tags:
  - CAS
  - 自旋锁
categories:
  - 其他
keywords: CAS 自旋锁
cover: https://w.wallhaven.cc/full/9m/wallhaven-9mjrww.jpg
copyright_author: Year21
copyright_url: http://year21.top/2022/08/17/CAS与自旋锁
---

# CAS

CAS --> Unsafe --> CAS底层思想 --> ABA问题 --> 原子引用更新 --> 如何规避ABA问题

## CAS底层原理

- 简单来说就是Unsafe类+CAS思想(自旋)

~~~java
public class CASDemoClass {
    public static void main(String[] args) {
        //AtomicInteger原子类
        //设置主内存的初始值为5
        AtomicInteger atomicInteger = new AtomicInteger(5);

        /* compareAndSet()方法
        参数说明：
        1.int expect 期望值
        2.int update 更新值
        这里的期望值指的是与原先从主内存中读取的值进行比较的值，更新值是值即将要修改成的值
        方法返回值说明：
        true表示输入的期望值与原先从主内存中读取的值一致，则表现在这期间没有任何线程修改过
        主内存的值，因此可以将主内存的值修改为设置的更新值。
        false表示输入的期望值与原先从主内存中读取的值不一致，则表现在这期间有线程修改过
        主内存的值，当前线程不会做任何修改
         */
        System.out.println(atomicInteger.compareAndSet(5, 2022));

        //get()方法是返回当前主内存的实际值
        System.out.println("当前值：" + atomicInteger.get());
    }
}
~~~

CAS（Compare and swap），即比较并交换。自旋锁或乐观锁，其中的核心操作实现就是CAS。

它的功能是判断内存中某个位置的值是否为预期值，如果是则更改为新的值，这个过程是原子的。

从操作系统底层来说，<font color='red'>**CAS是一条CPU并发原语**</font>。

CAS并发原语体现在Java语言中sun.misc包下的Unsafe类，调用这个类的CAS方法，JVM会帮我们实现出CAS汇编指令。

这是一种完全依赖硬件的功能，通过这个实现了原子操作。由于CAS是一种系统原语，原语属于操作系统用语范畴，

是由若干条指令组成，用于完成某个功能的一个过程，<font color='red'>**并且原语的执行必须是连续的，在执行过程中不允许被打断，**</font>

<font color='red'>**也就是说CAS是一条CPU的原子指令，不会出现数据不一致的问题。**</font>

- AtomicInteger.getAndIncrement()方法是如何实现在多线程情况下的自增函数的线程安全的？

原因就在getAndIncrement()方法的底层实际上是CAS思想，靠的是unSafe类CPU指定原语保证原子性

①变量valueoffset表示该变量值在内存中的偏移地址，Unsafe类可以通过该值获取对应地址数据

②变量value使用volatile修饰，保证了多线程之间的数据可见性

~~~java
public final int getAndIncrement() {
    /*getAndAddInt()方法参数解析
    1.this 表示当前对象
    2.valueoffset 内存偏移量，也可以直接理解为该对象值的内存地址
    3.1表示每调用一次这个方法就增加1
    */
    return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}
~~~

![](https://i.bmp.ovh/imgs/2022/08/17/10cfd454a8807920.png)

~~~java
/*
var1 AtomicInteger对象本身
var2 该对象值的引用地址
var4 需要更新的数量值
var5 是用var1、var2找出的主内存中真实的值
用改对象当前的值和var5进行比较，如果相同，则更新为var5+var4的值并返回true
如果不同，继续循环取值然后再比较，直至更新完成
*/
public final int getAndAddInt(Object var1,long var2,int var4){
    int var5;
    do{
        var5 = this.getIntVolatile(var1,var2); //通过var1和var2取得主内存的值，这一步相当于在完成方法名的get操作
    }while(!this.compareAndSwapInt(var1,var2,var5,var5+var4));//将期望值与主内存值进行比较，相同便更新结束循环，否则继续循环直至更新成功
    return var5;//返回这个值的同时完成增加操作
}
~~~

那么这个Unsafe类又是什么呢？

Unsafe类是在sun.misc包下，不属于Java标准。但是很多Java的基础类库，包括一些被广泛使用的高性能开发库

都是基于Unsafe类开发的，比如Netty、Cassandra、Hadoop、Kafka等。Unsafe类在提升Java运行效率，

增强Java语言底层操作能力方面起了很大的作用。Unsafe类使Java拥有了像C语言的指针一样操作内存空间的能力，

同时也带来了指针的问题。

- <font color='red'>**Unsafe类中的所有方法都是用native修饰的，也就是说Unsafe类中的方法都是直接调用操作系统底层资源执行任务。**</font>

## CAS的缺点

1.循环时间长

因为getAndAddInt方法执行时，有个do whlie，

如果CAS失败会一直进行尝试，长时间不成功就会给CPU代来很大的开销

2.只能保证一个共享变量的原子操作

3.引出ABA问题

## ABA问题

当前内存的值一开始是A，被另外一个线程先改为B然后再改为A，那么当前线程访问的时候发现是A，则认为它没有

被其他线程访问过，又进行其的修改行为。看起来，这一切没有问题，实际上过程中是发生了很多变化，而在某些

场景下这样是存在错误风险的。

那么如何解决这个ABA问题呢，大多数情况下乐观锁的实现都会通过引入一个版本号标记这个对象，每次修改版本号

都会变化，比如使用时间戳作为版本号，这样就可以很好的解决ABA问题。

在JDK中提供了AtomicStampedReference类来解决这个问题，思路是一样的。这个类也维护了一个int类型的标记stamp，

每次更新数据的时候顺带更新一下stamp。

- <font color='red'>**剩下内容有机会再补充，就先这样** </font>

# 自旋锁

自旋锁(SpinLock)是指尝试获取锁的线程不会立即阻塞，而是采用循环的方式去尝试获取锁。

好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU。

- 自旋锁代码验证

~~~java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicReference;

/**
 * @author hcxs1986
 * @version 1.0
 * @description: 实现一个自旋锁
 * 通过CAS操作完成自旋锁，A线程先进来调用myLock方法且持锁5秒钟，
 * B随后进来发生当前有线程持有锁，不是null，因此只能通过自旋等待，
 * 直至A释放锁B随后抢到锁
 * @date 2022/8/7 1:00
 */
public class SpinLockDemoClass {

    //原子引用线程
    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    public void myLock(){
        Thread thread = Thread.currentThread();
        System.out.println("当前进入的线程名为： " + thread.getName());
        while (!atomicReference.compareAndSet(null,thread)){

        }
    }

    public void myUnLock(){
        Thread thread = Thread.currentThread();
        atomicReference.compareAndSet(thread,null);
        System.out.println("当前进入的线程名为："+ thread.getName() + "释放锁");
    }

    public static void main(String[] args) {
        SpinLockDemoClass spinLockDemoClass = new SpinLockDemoClass();

        //匿名A线程
        new Thread(() ->{
            //获得锁
           spinLockDemoClass.myLock();
            try { TimeUnit.SECONDS.sleep(5); } catch (InterruptedException e) { e.printStackTrace(); }
            //释放锁
            spinLockDemoClass.myUnLock();
        },"AA").start();

        //让main线程睡眠1秒，确保a线程取得锁
        try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }

        //匿名B线程
        new Thread(() ->{
            spinLockDemoClass.myLock();
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            spinLockDemoClass.myUnLock();
        },"BB").start();

    }
}
~~~

