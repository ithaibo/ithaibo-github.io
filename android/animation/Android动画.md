## 动画分类 ##

|  动画类型  |   说明   |
| --------- | -------- |
| View动画 | 通过对场景里的对象不断做图像变换(平移、缩放、旋转、透明度)从而产生动画效果——渐进式动画 |
| 帧动画 | 通过顺序播放一系列图像从而产生动画效果——切片动画 |
| 属性动画 | 通过动态地改变对象的属性从而达到动画效果 |


## View动画 ##

作用对象：View

4种动画效果：

| 名称 | 标签 | 子类 | 效果 |
| ---- | ---- | ---- | ---- |
| 平移 | <translate> | TranslateAnimation | 移动View |
| 缩放 | <scale> | ScaleAnimation | 放大或缩小View |
| 旋转 | <rotate> | RotateAnimation | 旋转View |
| 透明度 | <alpha> | AlphaAnimation | 改变View的透明度 |

如果把轴点设为View的右边界，那么View就只会向左边进行缩放，反之则向右边进行缩放。

android:duration——动画的持续时间
android:fillAfter——动画结束以后View是否停留在结束位置

#### 自定义动画 ####
 - 继承Animation
 - 重写initialize，在其中做一些初始化工作
 - 重写applyTransformation，在其中进行相应的矩阵变换

enterAnim——Activity被打开时，所需的动画资源id
exitAnim——Activity被暂停时，所需的动画资源id

Activity切换动画主要代码：<br/>
``` Java
Intent intent = new Intent(this, TestActivity.class);
startActiviyt(intent);
overridePendingTransition(R.anim.enter, R.anim.exit);
```

Fragment也可以切换动画： <br/>
通过FragmentTransaction中的setCustomAnimations()方法来添加切换动画

## 属性动画 ##
#### 属性动画的基本信息 ####
属性动画可以对任意对象做动画。<br/>
属性动画中有ValueAnimator、ObjectAnimator和AnimatorSet<br/>
动画默认时间间隔：300ms，默认帧率10ms/帧<br/>
可以达到的效果：在一个时间间隔内完成对象从一个属性值到另一个属性值的改变。<br/>
只要对象有这个属性，属性动画都能实现动画效果。<br/>
从API11开始

#### 如何使用属性动画 ####
