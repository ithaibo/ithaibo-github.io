# Java内存模型
内存模型是一套共享内存系统中多线程读写操作行为的规范。这套规范屏蔽了底层各种硬件和操作系统内存访问差异。解决了CPU多级缓存、CPU优化、指令重排等导致的内存访问问题。保证了Java多线程程序在各种平台下对内存访问的一致性。

## 目的
保证Java多线程程序在各种平台下对内存访问的一致性

## 相关概念
1. 工作内存 working memory
   - CPU中寄存器或高速缓存的抽象
2. 共享变量
   - 存储在主内存中
3. 线程私有工作内存
   - 存储线程读写共享变量的副本
4. 内存可见性
   -
   - 让应用程序免于数据竞争干扰
5. 原子性
6. 有序性

## happens-before规则
用于描述2个操作的内存可见性。如果操作A happens-before操作B，那么操作A的执行结果将对操作B可见。

1. 以下几种自动符合happens-before规则
   - 程序次序规则
     - 单线程内部，如果代码的字节码顺隐式符合happens-before
   - 锁定规则
     - 无论是在单线程还是多线程环境中，一个锁如果处于锁定状态，那么必须先执行unlock操作后才能进行unlock操作。
   - 变量规则
     - volatile保证了线程可见性
   - 线程启动规则
     - Thread对象的start()方法先行发生于此线程的每一个动作
   - 线程中断规则
     - 对线程interrupt()方法的调用先行发生于被中断线程的代码检测，直到到中断事件的发生
   - 线程终结规则
     - 线程中的所有操作都先行发生于对此线程的终止检测
   - 对象终结规则
     - 一个对象的初始化完成发生在它的finalize()方法开始前

## 内存模型的应用
1. volatile
``` Java
private volatile int value = 0;
public void setValue(int value) {
    this.value = value;
}
public int getValue() {
    return this.value;
}
```

2. synchronized
``` Java
private int value = 0;
public void setValue(int value) {
    synchronized {
        this.value = value;
    }
}
public int getValue() {
    synchronized {
        return this.value;
    }
}
```