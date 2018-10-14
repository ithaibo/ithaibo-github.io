# IPC
Android实现IPC的方式有：
 - Bundle
    Intent中使用Bundle传输extra数据
 - 文件共享
 - Messenger
    一种轻量级的IPC方案，基于AIDL实现
 - AIDL
    请参考[aidl](aidl.md)
 - ContentProvider
    
 - Socket
    任何程序都可以通过Socket进行进程间通信