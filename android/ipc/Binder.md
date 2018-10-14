# Binder
Binder是Android中一种高效的IPC方案。

## 使用方式
 - 通过bindService的方式获取IBinder
 - 根据IBinder调用远程的方法

以上内容以AIDL为例：
例如，在主进程中需要调用remote进程的addBook方法来向其中增加一本书。因为是不同的进程，不能直接操作remote进程的内存。
远程实现一个service, 
client对service进行绑定，并获取IBinder对象。
通过IBinder来创建Stub（将会获得一个代理对象）
stub会

## 注意事项
   - linkToDeath
   - unlinkToDeath
``` java
IBinder.DeathRecipient dethRecipient = new IBinder.DeathRecipient() {
    @Override
    public void binderDied() {
        // 
        binder.unlinkToDeath(dethRecipient, 0);
    }
};

bindre.linkToDeath(dethRecipient, 0)
```
