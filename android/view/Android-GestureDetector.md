# Gestture Detector #
手势检测，用于检测用户的单机、滑动、长按、双击等行为。

常用方法：

| 方法名 | 简要说明 |
| ------ | ------ |
| onSingleTapUp | 单击 |
| onFling | 快速滑动 |
| onScroll | 拖动 |
| onLongPress | 长按 |
| onDoubleTap | 双击 |

## 滑动 ##
滑动需要有过渡效果，提升用户体验，将主要用到Scroller。Scroller本身无法让View弹性滑动，需要和View.computeScroll()方法一起使用。

Android中滑动的实现方式有三种：

 - 通过View本身提供的scrollTo/scrollBy方法；
 - 通过动画给View施加平移效果；
 - 通过改变View的LayoutParams使得View重绘布局从而实现滑动。

### 使用动画 ###
通过动画让一个View进行平移，主要是操作View的translationX和translationY属性，既可以采用传统的View动画，也可以采用属性动画。

### Scroller的基本使用方法 ####
``` Java

```
