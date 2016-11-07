---
title: java线程总结
date: 2016-09-25 18:40:15
tags: java
categories: java
---

## 创建线程的两种方法

### 通过实现Runnable接口线程创建
1. 定义一个类实现Runnable接口，重写接口中的run()方法。在run()方法中加入具体的任务代码或处理逻辑。
2. 创建Runnable接口实现类的对象。
3. 创建一个Thread类的对象，需要封装前面Runnable接口实现类的对象。（接口可以实现多继承）
4. 调用Thread对象的start()方法，启动线程
<!--more -->
````JAVA
public class TreadDemo1 implements Runnable {  
    private int countDown = 10;  
    @Override  
    // 在run方法中定义任务  
    public void run() {  
        while (countDown-- > 0) {  
            System.out.println("#" + Thread.currentThread().getName() + "("  
                    + countDown + ")");  
        }  
    }  
  
    public static void main(String[] args) {  
        // Runnable中run方法是一个空方法，并不会产生任何线程行为，必须显式地将一个任务附着到线程上  
        TreadDemo1 tt=new TreadDemo1();  
        new Thread(tt).start();  
        new Thread(tt).start();  
        System.out.println("火箭发射前倒计时：");  
    }  
}  
````
运行结果：
火箭发射前倒计时：
Thread-1(8)<br />
Thread-1(7)<br />
Thread-1(6)<br />
Thread-1(5)<br />
Thread-1(4)<br />
Thread-1(3)<br />
Thread-1(2)<br />
Thread-1(1)<br />
Thread-1(0)<br />
Thread-0(9)<br />

### 通过继承Thread类创建线程
1. 首先定义一个类去继承Thread父类，重写父类中的run()方法。在run()方法中加入具体的任务代码或处理逻辑。
2. 直接创建一个ThreadDemo2类的对象，也可以利用多态性，变量声明为父类的类型。
3. 调用start方法，线程t启动，隐含的调用run()方法。

````JAVA
public class ThreadDemo2 extends Thread {  
    private int countDown = 10;  
  
    @Override  
    // 在run方法中定义任务  
    public void run() {  
        while (countDown-- > 0) {  
            System.out.println("#" + this.getName() + "(" + countDown + ")");  
        }  
    }  
  
    public static void main(String[] args) {  
        new ThreadDemo2().start();  
        new ThreadDemo2().start();  
        // 由于start方法迅速返回，所以main线程可以执行其他的操作,此时有两个独立的线程在并发运行  
        System.out.println("火箭发射前倒计时：");  
    }  
}  
````

运行结果：
<br />Thread-0(9)
<br />Thread-0(8)
<br />Thread-0(7)
<br />Thread-0(6)
<br />Thread-0(5)
<br />Thread-0(4)
<br />Thread-0(3)
<br />Thread-0(2)
<br />Thread-0(1)
<br />Thread-0(0)
火箭发射前倒计时：
<br />Thread-1(9)
<br />Thread-1(8)
<br />Thread-1(7)
<br />Thread-1(6)
<br />Thread-1(5)
<br />Thread-1(4)
<br />Thread-1(3)
<br />Thread-1(2)
<br />Thread-1(1)
<br />Thread-1(0)

### 两种方式的比较
首先分析两种方式的输出结果，同样是创建了两个线程，为什么结果不一样呢？
<br />使用实现Runnable接口方式创建线程可以共享同一个目标对象（TreadDemo1 tt=new TreadDemo1();），实现了多个相同线程处理同一份资源。<br />
<br />然后再看一段来自JDK的解释：
<br />Runnable 接口应该由那些打算通过某一线程执行其实例的类来实现。类必须定义一个称为run 的无参数方法。
设计该接口的目的是为希望在活动时执行代码的对象提供一个公共协议。例如，Thread 类实现了Runnable。激活的意思是说某个线程已启动并且尚未停止。<br />
<br />此外，Runnable 为非 Thread 子类的类提供了一种激活方式。通过实例化某个Thread 实例并将自身作为运行目标，就可以运行实现 Runnable 的类而无需创建 Thread 的子类。大多数情况下，如果只想重写run() 方法，而不重写其他 Thread 方法，那么应使用 Runnable 接口。这很重要，因为除非程序员打算修改或增强类的基本行为，否则不应为该类创建子类。
 
采用继承Thread类方式：
<br />（1）优点：编写简单，如果需要访问当前线程，无需使用Thread.currentThread()方法，直接使用this，即可获得当前线程。
<br />（2）缺点：因为线程类已经继承了Thread类，所以不能再继承其他的父类。
采用实现Runnable接口方式：
<br />（1）优点：线程类只是实现了Runable接口，还可以继承其他的类。在这种方式下，可以多个线程共享同一个目标对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将CPU代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。
<br />（2）缺点：编程稍微复杂，如果需要访问当前线程，必须使用Thread.currentThread()方法。