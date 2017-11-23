# 线程基础 #
## 线程的生命周期
 * 新建

该状态是Java中使用创建Thread对象后，该线程就处于新建状态。

 * 就绪

当调用了start()方法后，该线程就处于就绪状态，等待JVM线程调度器调度。

 * 运行

处于就绪状态的线程获取到了CPU资源，执行run()方法，该线程就处于运行状态。运行状态可以变成阻塞状态、就绪状态和死亡状态。

 * 阻塞

线程执行了sleep()、suspend()等方法，失去所占有的资源，该线程就

 * 死亡

 一个运行状态的线程完成任务或者其他终止条件发生时，该线程就切换到终止状态。

## 线程优先级
java线程优先级范围：1 （Thread.MIN_PRIORITY ） - 10 （Thread.MAX_PRIORITY ）

默认优先级为NORM_PRIORITY（5）。

## 线程的创建方式
线程的创建方式有三种：
 * 通过实现Runnable接口
 * 通过继承Thread类
 * 通过Callable和Future创建线程

以上方式的比较：
1. 采用实现 Runnable、Callable 接口的方式创见多线程时，线程类只是实现了 Runnable 接口或 Callable 接口，还可以继承其他类。
2. 使用继承 Thread 类的方式创建多线程时，编写简单，如果需要访问当前线程，则无需使用 Thread.currentThread() 方法，直接使用 this 即可获得当前线程。

## 线程的几个主要概念
1. 线程同步

参考!(线程同步7种方式)[http://www.cnblogs.com/XHJT/p/3897440.html][http://www.cnblogs.com/XHJT/p/3897440.html](http://www.cnblogs.com/XHJT/p/3897440.html "线程同步的7种方式")

2. 线程间通信


3. 线程死锁


4. 线程控制：挂起、停止和恢复
