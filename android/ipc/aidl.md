# AIDL

## 支持的数据类型
    - 基本数据类型
    - String Charsequence
    - ArrayList, 里面的元素必须都能被AIDL
    - HashMap...
    - Parcelable
    - AIDL

## 使用步骤
1. 创建aidl文件

2. server端实
    - 实现aidl中Stub的Binder
    - 在绑定服务时，返回该Binder

3. client
    - 绑定远程服务
    ...