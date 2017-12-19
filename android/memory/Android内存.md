# Android内存管理机制 #
本文主要讲述了Java内存分配机制、JavaGC机制，介绍了Dalvik虚拟机和ART虚拟机及其内存管理。

## Java内存分配机制 ##
对象将根据存活的时间被分为：<br/>
 - 年轻代（Young Generation）: 对象被创建时，内存的分配首先发生在年轻代。绝大多数刚创建的对象会被分配在Eden区（Eden区是连续的内存空间）。
 - 老年代（Old Generation）:对象如果在年轻代存活了足够长的时间而没有被清理掉（即在几次 Young GC后存活了下来），则会被复制到年老代，年老代的空间一般比年轻代大，能存放更多的对象，在年老代上发生的GC次数也比年轻代少。当年老代内存不足时， 将执行Major GC，也叫 Full GC。
 - 永久代（Permanent Generation，也就是方法区）:

## Java GC机制 ##
- Serial收集器：新生代收集器，使用停止复制算法，使用一个线程进行GC，其它工作线程暂停。
- ParNew收集器：新生代收集器，使用停止复制算法，Serial收集器的多线程版，用多个线程进行GC，其它工作线程暂停，关注缩短垃圾收集时间。
- Parallel Scavenge 收集器：新生代收集器，使用停止复制算法，关注CPU吞吐量，即运行用户代码的时间/总时间，比如：JVM运行100分钟，其中运行用户代码99分钟，垃 圾收集1分钟，则吞吐量是99%，这种收集器能最高效率的利用CPU，适合运行后台运算（关注缩短垃圾收集时间的收集器，如CMS，等待时间很少，所以适 合用户交互，提高用户体验）。
- Serial Old收集器：老年代收集器，单线程收集器，使用标记整理（整理的方法是Sweep（清理）和Compact（压缩），清理是将废弃的对象干掉，只留幸存 的对象，压缩是将移动对象，将空间填满保证内存分为2块，一块全是对象，一块空闲）算法，使用单线程进行GC，其它工作线程暂停.
- Parallel Old收集器：老年代收集器，多线程，多线程机制与Parallel Scavenge差不错，使用标记整理（与Serial Old不同，这里的整理是Summary（汇总）和Compact（压缩），汇总的意思就是将幸存的对象复制到预先准备好的区域，而不是像Sweep（清 理）那样清理废弃的对象）算法，在Parallel Old执行时，仍然需要暂停其它线程。Parallel Old在多核计算中很有用。
- CMS（Concurrent Mark Sweep）收集器：老年代收集器，致力于获取最短回收停顿时间，使用标记清除算法，多线程，优点是并发收集（用户线程可以和GC线程同时工作），停顿小。


## Dalvik虚拟机 ##
Dalvik虚拟机使用dex（Dalvik Executable）格式的文件，Java使用的是class文件。一个dex文件可以包含多个类，而class文件只能包含一个类。Dalvik虚拟机使用的指令时基于寄存器的，而Java虚拟机使用的指令集是基于堆栈的。

Dalvik是依靠一个Just-In-Time (JIT)编译器去解释字节码。开发者编译后的应用代码需要通过一个解释器在用户的设备上运行，让应用能更容易在不同硬件和架构上运行。

### Dalvik 内存管理 ###
 - Dalvik虚拟机用来分配对象的堆划分为2部分：**Active Heap**和**Zygote Heap**。
 - Android系统的第一个Dalvik虚拟机是由Zygote进程创建的，应用程序进程是由Zygote进程fork出来的。
 - 当Zygote进程在fork第一个应用程序进程之前，会将已经使用了的那部分堆内存划分为一部分，还没有使用的堆内存划分为另外一部分。前者就称为Zygote堆，后者就称为Active堆。以后无论是Zygote进程，还是应用程序进程，当它们需要分配对象的时候，都在Active堆上进行。这样就可以使得Zygote堆尽可能少地被执行写操作。
 - 在Zygote堆里面分配的对象其实主要就是Zygote进程在启动过程中预加载的类、资源和对象了。这意味着这些预加载的类、资源和对象可以在Zygote进程和应用程序进程中做到长期共享。
 - 分配对象的堆划分为Active堆和Zygote堆，既能减少内存需求，也能减少拷贝操作。

#### 创建对象 ####
 - 调用函数dvmHeapSourceAlloc在Java堆上分配指定大小的内存。如果分配成功，那么就将分配得到的地址直接返回给调用者了。
 - 如果上一步内存分配失败，这时候就需要执行一次GC了。调用函数gcForMalloc来执行一次GC了，参数false表示不要回收软引用对象引用的对象。
 - GC执行完毕后，再次调用函数dvmHeapSourceAlloc尝试轻量级的内存分配操作。如果分配成功，那么就将分配得到的地址直接返回给调用者了。
 - 如果上一步内存分配失败，这时候就得考虑先将Java堆的当前大小设置为Dalvik虚拟机启动时指定的Java堆最大值，再进行内存分配了。
 - 如果调用函数dvmHeapSourceAllocAndGrow分配内存成功，则直接将分配得到的地址直接返回给调用者了。
 - 如果上一步内存分配还是失败，这时候就得出狠招了。再次调用函数gcForMalloc来执行GC。参数true表示要回收软引用对象引用的对象。
 - GC执行完毕，再次调用函数dvmHeapSourceAllocAndGrow进行内存分配。这是最后一次努力了，成功与事都到此为止。

 #### GC类型 ####
 /* Not enough space for an "ordinary" Object to be allocated. */  
 - extern const GcSpec *GC_FOR_MALLOC;  

/* Automatic GC triggered by exceeding a heap occupancy threshold. */  
- extern const GcSpec *GC_CONCURRENT;  

/* Explicit GC via Runtime.gc(), VMRuntime.gc(), or SIGUSR1. */  
- extern const GcSpec *GC_EXPLICIT;  

/* Final attempt to reclaim memory before throwing an OOM. */  
- extern const GcSpec *GC_BEFORE_OOM; 

## ART ##
在应用安装时就预编译字节码到机器语言，这一机制叫Ahead-Of-Time (AOT）编译。

### ART的优势 ###
 - 应用启动更快、运行更快、体验更流畅、触感反馈更及时
 - 支持更低的硬件

### ART的缺点 ###
 - 存储空间占用量大
 - 应用安装时间长

### ART内存管理 ###
 - ART运行时堆划分为四个空间，分别是Image Space、Zygote Space、Allocation Space和Large Object Space。其中，Image，Zygote and Allocation Space是在地址上连续的空间，称为Continuous Space，而Large Object Space是一些离散地址的集合，用来分配一些大对象，称为Discontinuous Space。
 - 在Image Space和Zygote Space之间，隔着一段用来映射system-framework-boot.art-classes.oat文件的内存。而Image Space空间就包含了那些需要预加载的系统类对象。如果系统没有升级，那么以后每次系统启动只需要将文件system-framework-boot.art-classes.dex直接映射到内存即可，省去了创建各个类对象的时间。
 - Zygote Space和Allocation Space与Dalvik虚拟机垃圾收集机制中的Zygote堆和Active堆的作用是一样的。Zygote Space在Zygote进程和应用程序进程之间共享的，而Allocation Space则是每个进程独占的。

** 总结：** 以空间换时间。
