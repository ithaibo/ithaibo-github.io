## 1. Java基础
### 1.1 Java并发
 无限创建线程的不足：
  - 线程的生命周期开销非常高（线程创建、销毁）；
  - 资源消耗，可运行线程数量多于可用处理器的数量，线程大量闲置，占用大量内存，以及给GC带来压力，CPU大量时间用于线程调度而非执行线程；
  - 稳定性[需要再理解理解]

#### Executor优势
 - 分摊线程创建、销毁带来的开销
 - 提高响应性[任务到达时无需等待线程创建直接执行]
 - 提升性能：通过调整线程池的大小，使CPU处于忙碌状态，并且避免了创建过多线程造成的内存耗尽等

#### 线程池创建：
 - Executors.newFixThreadPool
    <br/> 固定长度线程池
 - Executors.newCachedThreadPool
    <br/> 可缓存的线程池，可回收闲置的线程，按需创建新的线程
 - Executors.newSingleThreadExector
    <br/> 
 - Executors.newScheduledThreadPool
    <br/> 固定长度的线程池，可延迟或定时执行任务

#### Executor关闭
 - 平缓的关闭
  <br/>描述： 不再接受新的任务，等待已提交的任务执行完毕
  <br/>code: ExecutorService.shutdown()
 - 粗鲁的关闭
  <br/>描述： 尝试取消所有运行中的任务，并不启动尚未开始的任务
  <br/>code： ExecutorService.shutdownNow()

#### 延迟任务、周期任务
Timer和ScheduledThreadPoolExecutor的区别
 - Timer只创建一个线程执行任务；
 - **线程泄露**：如果有TimerTask在执行过程中产生了未捕获的异常，将会造成整个Timer的所有后续任务全部不能执行。
<br/>以上两个问题在线程池中会很好的解决，线程池可以创建多个线程来执行不同的任务，不会由于某个线程占用了另一个线程的执行时间的问题；一个线程崩溃，也不会造成其他线程执行的任务的取消。

延迟任务也可以用DelayQueue来解决。

#### Runnable & Callable
 Runnable的局限：
 - 不能返回结果
 - 不能抛出受检查的异常

Callable：入口为call，返回一个值或抛出一个异常

Future表示一个任务的周期
 - 检查是否已完成
 - 检查是否已取消
 - 获取任务的结果
 - 取消任务

#### ExecutorCompletionService

### 任务取消，线程终止
任务取消场景
- 用户取消
- 有时间限制的操作
- 应用程序事件
- 错误
- 关闭

Cancel Policy
- How 
  <br/>how other code can request cancellation
- When 
  <br/>when the task checks whether cancellation has been requested
- What 
  <br/>what actions the task takes in response to a cancellation request

#### 任务取消
 任务取消的方式：
- 使用volatile类型的域来保存取消状态
``` Java
    @ThreadSafe
    public class PrimeGenerator implements Runnable {
        @GuardedBy("this")
        private final List<BigInteger> primes = new ArrayList<BigInteger>();
        private volatile boolean cancelled;

        public void run() {
            BigInteger p = BigInteger.ONE;
            while (!cancelled) {
                p = p.nextProbablePrime();
                synchronized (this) {
                    primes.add(p);
                }
            }
        }

        public void cancel() {
            cancelled = true;
        }

        public synchronized List<BigInteger> get() {
            return new ArrayList<BigInteger>(primes);
        }
    }
```
- 通过中断(interrupt)取消
``` Java
    class PrimeProducer extends Thread {
        private final BlockingQueue<BigInteger> queue;

        PrimeProducer(BlockingQueue<BigInteger> queue) {
            this.queue = queue;
        }

        public void run() {
            try {
                BigInteger p = BigInteger.ONE;
                while (!Thread.currentThread().isInterrupted())
                    queue.put(p = p.nextProbablePrime());
            } catch (InterruptedException consumed) {
                /* Allow thread to exit */
                return;
            }
        }

        public void cancel() {
            interrupt();
        }
    }
```
- 通过Future实现取消
``` Java
    public static void timedRun(Runnable r, long timeout, TimeUnit unit) throws InterruptedException {
        Future<?> task = taskExec.submit(r);
        try {
            task.get(timeout, unit);
        } catch (TimeoutException e) {
            // task will be cancelled below
        } catch (ExecutionException e) {
            // exception thrown in task; rethrow
            throw launderThrowable(e.getCause());
        } finally {
            // Harmless if task already completed
            task.cancel(true); // interrupt if running
        }
    }
```
- 通过改写interrupt方法将非标准的取消操作封装在Thread中
``` Java
    public class ReaderThread extends Thread {
        private final Socket socket;
        private final InputStream in;
        public ReaderThread(Socket socket) throws IOException {
            this.socket = socket;
            this.in = socket.getInputStream();
        }
        public void interrupt() {
            try {
                socket.close();
            }
            catch (IOException ignored) { }
            finally {
                super.interrupt();
            }
        }
        public void run() {
            try {
                byte[] buf = new byte[BUFSZ];
                while (true) {
                    int count = in.read(buf);
                    if (count < 0)
                        break;
                    else if (count > 0)
                        processBuffer(buf, count);
                }
            } catch (IOException e) {
              /* Allow thread to exit */ 
              return;
            }
        }
    }
```
- 通过newTaskFor将非标准的取消操作封装在一个任务中
``` Java
    public interface CancellableTask<T> extends Callable<T> {
        void cancel();
        RunnableFuture<T> newTask();
    }
    @ThreadSafe
    public class CancellingExecutor extends ThreadPoolExecutor {
        ...
        protected<T> RunnableFuture<T> newTaskFor(Callable<T> callable) {
            if (callable instanceof CancellableTask)
                return ((CancellableTask<T>) callable).newTask();
            else
                return super.newTaskFor(callable);
        }
    }
    public abstract class SocketUsingTask<T> implements CancellableTask<T> {
        @GuardedBy("this") private Socket socket;
        protected synchronized void setSocket(Socket s) { socket = s; }
        public synchronized void cancel() {
            try {
                if (socket != null)
                    socket.close();
            } catch (IOException ignored) { }
        }
        public RunnableFuture<T> newTask() {
            return new FutureTask<T>(this) {
                public boolean cancel(boolean mayInterruptIfRunning) {
                    try {
                        SocketUsingTask.this.cancel();
                    } finally {
                        return super.cancel(mayInterruptIfRunning);
                    }
                }
            };
        }
    }
```
上面的这段代码不仅能关闭socket，还能中断线程。

### 并发容器
 - ConcurrentHashMap
 - CopyOnWriteArrayList
