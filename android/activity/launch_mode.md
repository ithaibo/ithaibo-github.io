# activity launch mode

## standard
- 特征
    - 每次启动activity，都会新创建一个实例
    - 只能在Activity Context中启动
- 任务栈
在所启动该Activity的任务栈中运行

## singleTop
- 特征
    - 栈顶复用模式：如果栈顶已经存在该Activity（singleTop），那么不创建新的实例，执行onNewIntent方法。除此以外将会执行创建新的实例，并添加到栈顶。
- 任务栈


## singleTask
- 特征
    - 栈内复用模式：如果该Activity在当前的栈中已经存在，那么不创建实例，执行onNewIntent，并将该实例移至栈顶，该activity以上的所有activity均被退栈
- 任务栈


## singleInstance
- 特征
    - 
- 任务栈

