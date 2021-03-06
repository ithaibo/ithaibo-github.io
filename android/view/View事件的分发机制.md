# View事件的分发机制 #
View的一大难题：滑动冲突，解决办法就涉及到事件分发机制。

点击事件的传递规则
点击事件MotionEvent。点击事件的分发，其实就是对MotionEvent事件的分发过程。当一个MotionEvent产生了以后，系统需要把这个事件传递给一个具体的View，而这个传递的过程就是分发过程。点击事件的分发过程由三个很重要的方法来共同完成：dispatchTouchEvent、onInterceptTouchEvent和onTouchEvent。

dispatchTouchEvent
用来进行事件分发
onInterceptTouchEvent
判断是否拦截某个事件
onTouchEvent
处理点击事件，在dispatchTouchEvent中调用

三个方法的关系：
``` java
boolean dispatchTouchEvent(MotionEvent event) {
    boolean consume = false;
    if(onInterceptTouchEvent(event)) {
        consume = onTouch(event);
    } else {
        consume = child.dispatchTouchEvent(event);
    }
    return consume;
}
```
点击事件产生，调用dispatchTouchEvent
如果 ViewGroup的onInterceptTouchEvent方法返回true 拦截该事件，然后call当前的ViewGroup的onTouchEvent方法；
如果当前ViewGroup的onInterceptTouchEvent方法返回false， 不拦截该事件，该事件会继续传递给它的子元素，子元素的dispatchTouchEvent被调用，...

如果一个View设置了OnTouchListener，那么OnTouchListener中的onTouch方法会被调用。这时如果onTouch的返回true，View当前的onTouchEvent方法将不被调用；false，onTouchEvent方法将被调用。

在onTouchEvent方法中，如果当前设置了OnClickListener，那么onClick方法将会被调用。

由上可以看出，优先级为：OnTouchListener > onTouchEvent > OnClickListener

当一个点击事件产生后，它的传递过程如下：
Activity，Window，View。View接收到事件后悔按照事件分发机制去分发事件。
如果拦截了，自己处理
不拦截，寻找可以处理的child，向其分发
如果整个事件体系child都没有处理，将返回交给父级View的onTouchEvent处理（或window，window再向上抛给activity）；如果DOWN事件处理，其他没有处理，将交给Activity

## View事件传递机制
1. 一个事件序列是指：从down事件开始，move..., 最终以up事件结束。
2. 正常情况下，一个事件序列只能被一个View拦截且消耗。通过特殊手段（如将一个View自己该处理的事件通过onTouchEvent强行传递给其他View）处理。
3. 某个View一旦拦截，那么事件序列都只能由它来处理，并且其onInterceptTouchEvent不会被再调用
4. View开始处理事件，如果不消耗ACTION_DOWN事件，那么同一事件序列中其他事件都不会再交给它来处理，父元素的onTouchEvent将会被调用，后续事件将不能接收到
5. 如果View不消耗除ACTION_DOWN以外的其他事件，父元素的onTouchEvent不会被调用，可以收到后续事件，未被消耗的事件将传递给Activity
6. ViewGroup默认不拦截任何事件
7. View没有onInterceptTouchEvent
8. View的onTouchEvent默认都会被调用，除非clickable和longClickable同时为false
9. View的enable不影响onTouchEvent的默认返回值
10. onClick发生的前提是当前View是可点击的，并且收到了down和up事件。
11. 事件总是先传递给父元素，然后父元素分发给子View，通过requestDisallowInterceptTouchEvent方法可以干预父元素的事件分发过程。


