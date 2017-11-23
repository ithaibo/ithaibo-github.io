# File Lock
FileChannel中关于File Lock的API
```Java
public abstract class FileChannel extends AbstractChannel implements ByteChannel, GatheringByteChannel, ScatteringByteChannel {
    // This is a partial API listing
    public final FileLock lock()
    public abstract FileLock lock (long position, long size, boolean shared)
    public final FileLock tryLock()
    public abstract FileLock tryLock (long position, long size, boolean shared)
}
```
 - lock()的作用即相当于(0L, Long.MAX_VALUE, false): 
    - position设置为0
    - lock的region为整个文件
    - 独占锁


  OK，这里需要解释一下size：
 - lock指定的size超出文件大小，文件增长后，其覆盖的区域内的数据将会收到lock的保护；
 - lock区域以外的数据不会收到lock的保护


- tryLock()是非阻塞的：如果获取锁没有成功，会立即返回一个null

## LifeCycle
FileLock一经创建直就有效，直到下列情况：
 - 其release()方法被调用
 - 关联的Channel关闭了
 - JVM shutdown

其释放有效可以调用 isValid()。需要注意的是，Lock的可用性可以改变，但是其在创建时设置的position、size、exclusivity是不可变的。