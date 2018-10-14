# Messenger
实现原理：AIDL

## 核心代码

``` Java
public Messenger(Handler target) {
    ...
}

public Messenger(IBinder target) {
    ...
}
```

## 使用步骤
1. server
    - 创建一个Handler
    - 通过创建的handler创建Messenger
    - 在Service的onBind方法中返回Messenger的底层Binder

2. client
    - 绑定service，并通过其返回的Binder创建Messenger
    - 创建一个Handler，将该handler设置为Message的replyTo


## 特点
 - 串行处理消息，效率不高
 - 使用简单