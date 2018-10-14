#简介
> AsyncTask是Android系统提供的异步方式，其优点在于在子线程执行任务，并将结果传递给主线程。

## 实现方式
AsyncTask封装了Executor和Handler。

## 基本使用
- AsyncTask类必须在主线程汇总加载
- AsyncTask对象必须在主线程中创建
- execute方法必须在主线程中调用
- onPreExecute() onPostExecute() doInBackground() onProgressUpdate()方法不要去显示调用
在Android3.0及以后使用executeOnExcutor方法来并行执行任务。

**注：**本文并不是主要写如何使用AsyncTask，因此不作展开介绍。

# 兼容性
本文主要介绍的是AsyncTask在不同的API版本中的区别。

## 并行还是串行
下面的表格说明了AsyncTask的在不同API版本中，并行、串行情况：

 - Android1.5 ------------execute--------串行
 - Android1.6~2.2 --------execute--------并行
 - Android3.0+ -----------execute--------串行

在Android3.0以后，如果要使用并行执行，那么需要调用executeOnExecutor方法。

## 线程池
在Android4.4以前，corePoolSize的值为5:

     private static final int CORE_POOL_SIZE = 5;

4.4以后改为当前CPU数量加1:

     private static final int CORE_POOL_SIZE = CPU_COUNT + 1;

## 线程池的调度
设： nT为当前线程池的线程数量。

- nT >= corePoolSize **且** workQueue未满，新任务直接加入workQueue
- workQueue已满 **且** nT < maximumPoolSize 新任务等待
- nT >= maximumPoolSize **且** workQueue已满，将会使用THREAD_POOL_EXECUTOR中的handler进行处理：
``` Java
    public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
        throw new RejectedExecutionException("Task " 
           \+ r.toString() + 
           \+ " rejected from " 
           \+ e.toString());
    }
```
# AsyncTask工作原理
从AsyncTask的execute方法入手：
``` Java
@MainThread
public final AsyncTask<Params, Progress, Result> execute(Params... params) {
    return executeOnExecutor(sDefaultExecutor, params);
}

@MainThread
public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
                                                                   Params... params) {
    if (mStatus != Status.PENDING) {
        switch (mStatus) {
            case RUNNING:
                throw new IllegalStateException("Cannot execute task:"
                        + " the task is already running.");
            case FINISHED:
                throw new IllegalStateException("Cannot execute task:"
                        + " the task has already been executed "
                        + "(a task can be executed only once)");
        }
    }
    mStatus = Status.RUNNING;
    onPreExecute(); 			//首先执行onPreExecute()
    mWorker.mParams = params; 	//将参数封装为mWorker的相关参数
    exec.execute(mFuture); 		//串行执行Task
    return this;
}
```


sDefaultExecutor是一个串行的线程池，一个进程汇总的所有AsyncTask全部在这个线程池中排队执行。
``` Java
public static final Executor SERIAL_EXECUTOR = new SerialExecutor();

private static class SerialExecutor implements Executor {
    final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
    Runnable mActive;

    public synchronized void execute(final Runnable r) {
        mTasks.offer(new Runnable() {	//将FutureTask对象插入到任务队列中
            public void run() {
                try {
                    r.run();
                } finally {
                    scheduleNext();
                }
            }
        });
        if (mActive == null) { //如果没有正在活动的任务，那么就调用scheduleNext()来执行下一个任务
            scheduleNext();
        }
    }

    protected synchronized void scheduleNext() {
        if ((mActive = mTasks.poll()) != null) {
            THREAD_POOL_EXECUTOR.execute(mActive);
        }
    }
}
```

线程池THREAD_POOL_EXECUTOR用于真正的执行任务，InternalHandler用于将执行环境从线程池切换到主线程。
``` Java
mWorker = new WorkerRunnable<Params, Result>() {
	//FutureTask的run方法会调用mWorker的call()方法，因此mWorker的call方法最终会在线程池中执行。
    public Result call() throws Exception {
        mTaskInvoked.set(true);	//当前任务已经被调用
        Result result = null;   //
        try {
            Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
            //noinspection unchecked
            result = doInBackground(mParams);
            Binder.flushPendingCommands();
        } catch (Throwable tr) {
            mCancelled.set(true);
            throw tr;
        } finally {
            postResult(result);
        }
        return result;
    }
};
```

sHandler是一个静态Handler对象，sHandler收到MESSAGE_POST_RESULT这个消息后悔调用AsyncTask的finish方法.
