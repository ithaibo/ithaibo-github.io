# View基础
在日常的开发中经常需要根据需求自定义View，或者处理滑动冲突的问题。这需要对View的基础知识有足够的了解，才能应对自如。

View的知识结构：
 - Class View
 - View的位置参数
 - MotionEvent
 - TouchSlop
 - VelocityTracker
 - GestureDetector
 - Scroller

接下来将对各个模块进行简单的介绍，以便后续展开介绍。

## Class View
public class View extends Object implements Drawable.Callback, KeyEvent.Callback, AccessibilityEventSource
> **Known Direct Subclasses:**AnalogClock, ImageView, KeyboardView, MediaRouteButton, ProgressBar, Space, SurfaceView, TabItem, TextView, TextureView, ViewGroup, ViewStub

上面的内容来自[Android官方文档](https://developer.android.google.cn/reference/android/view/View.html)，可以看出，ViewGroup也继承自View。因此，View既可以是单个控件，也可以是ViewGroup。

## View的位置参数
View的位置主要由2个点：左上角，右下角来决定：左上角确定View的left、top来个位置；右下角确定right、bottom两个位置。

**注：left、top、right、bottom都是相对于parent的位置。**

Android屏幕坐标系：从左上角为起点，水平向右为x的递增；竖直向下为y的递增。

其他参数介绍：
 - x 左上角横坐标
 - y 左上角的纵坐标
 - translationX 左上角相对于parent的偏移量
 - translationY 左上角相对于parent的偏移量

x = left + translationX <br/>
y = top + translationY<br/>
上面的left、top在View移动过程中是不变的。

## MotionEvent
MotionEvent是手指在屏幕上接触所产生的事件。其类型有：
 - ACTION_DOWN 手指刚接触屏幕
 - ACTION_MOVE 手指在屏幕上滑动
 - ACTION_UP 手指离开屏幕

点击操作：ACTION_DOWN  ACTION_UP
滑动操作：ACTION_DOWN  ACTION_MOVE  ACTION_UP


## TouchSlop
TouchSlop是系统能识别的滑动的最小距离，该值在不同的系统中可能不同。滑动距离小于TouchSlop，系统不会认为这是一个滑动。
android.view.ViewConfiguration.getScaledTouchSlop()方法可以获取该值。

## VelocityTracker
VelocityTracker用户追踪手指在滑动过程中的速度，包括水平和竖直方法的速度。

在MotionEvent产生后时，可以测速，其基本使用方法：
``` Java
VelocityTracker tracker = VelocityTracker.obtain();
tracker.computeCurrentVelocity(1000) //unit is 1s
float xVeloctiy = getXVelocity(); //get X velocity
float yVeloctiy = getYVelocity(); //get Y velocity
```

## GestureDetector
手势检测，用于辅助检测用户的单击、滑动、长按、双击等行为。

GestureDetector使用：
```
//创建GestureDetector对象，并实现OnGestureListener
GestureDetector detector = new GestureDetector(context, onGestureListener);

//长按后可以拖动
detector.setIsLongpressEnabled(false); 

//接管View的onTouchEvent方法
boolean consume = detector.onTouchEvent(event);
return consume;
```

## Scroller
用于实现View的弹性滑动。

使用流程：
``` Java
Scroller scroller = new Scroller(context);

private void smoothScrollerTo(int desX, int desY) {
    int deltaX = desX - getScrollX();
    int deltaY = desY - getScrollY();
    scroller.startScroll(0, 0, deltaX, deltaY, 1500);
    invalidate();
}

public void computeScroll() {
    if(scroller.computeScrollOffset()) {
        scrollTo(scroller.getCurrX(), scroller.getCurrY());
        postInvalidate();
    }
}
```
