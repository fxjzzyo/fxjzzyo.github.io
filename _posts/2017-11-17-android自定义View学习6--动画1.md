---
published: true
layout: post
tags:
  - android
  - 动画
categories: Android
description: 本文章将介绍android中动画的基础知识，主要是属性动画。
---
android中的动画分两类：Animation和 Transition。

其中 Animation 又可以再分为 View Animation 和 Property Animation 两类。

View Animation是纯粹基于 framework 的绘制转变，比较简单老旧。

Property Animation，**属性动画**，是在 Android 3.0 开始引入的新的动画形式，为大多数项目所用。
## 1. 属性动画ViewPropertyAnimator
ViewPropertyAnimator的获取
```
//获取ViewPropertyAnimator
 ViewPropertyAnimator animate = imageView.animate();
 //x轴方向移动300像素
 animate.translationX(300);

//链式写法
view.animate().translationX(300); 
```
ViewPropertyAnimator自带的各种动画属性方法如下表：

![自带的动画方法](http://img.blog.csdn.net/20171117222649950?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**注：** translationX() 和 translationXBy() 这两个方法的区别是：前者表示**移动到**某个坐标值，后者表示**移动了**多少距离。

## 2. ObjectAnimator
ObjectAnimator是属性动画的一种，是ViewPropertyAnimator的加强版，它不但能实现自带的动画属性，也能实现自定义view中自定义属性的动画。

使用方法：
> 自定义控件中：
> 需要添加 setter / getter 方法；
用 ObjectAnimator.ofXXX() 创建 ObjectAnimator 对象；
用 start() 方法执行动画。


例如：
```
// imageView2: 2 秒
ObjectAnimator animator = ObjectAnimator.ofFloat(  
        imageView2, "translationX", 500);
animator.setDuration(2000);  
animator.start();
```

## 3. 其他通用方法：

### 1）setDuration(int duration) 设置动画时长

```
// imageView1: 500 毫秒
imageView1.animate()  
        .translationX(500)
        .setDuration(500);
```
### 2）setInterpolator(Interpolator interpolator) 设置 Interpolator
设置插值器，就是速度的模型。

```
// imageView1: 线性 Interpolator，匀速
imageView1.animate()  
        .translationX(500)
        .setInterpolator(new LinearInterpolator());
```

插值器列表：

![插值器列表](http://img.blog.csdn.net/20171117230207001?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**注：**
1. CycleInterpolator与AccelerateDecelerateInterpolator 的区别是，它可以自定义曲线的周期，所以动画可以不到终点就结束，也可以到达终点后回弹，回弹的次数由曲线的周期决定，曲线的周期由 CycleInterpolator() 构造方法的参数决定。
2. PathInterpolator自定义动画完成度 / 时间完成度曲线，就是构造动画完成度 / 时间的Path，从而控制动画的速度模型。
3. FastOutLinearInInterpolator、FastOutSlowInInterpolator、LinearOutSlowInInterpolator都是使用了贝塞尔曲线构造的加速或减速或加速减速模型。他们和AccelerateInterpolator、AccelerateDecelerateInterpolator、DecelerateInterpolator效果几乎一样。只不过前者的加速度比后者要大一点。

## 4. 设置监听
ViewPropertyAnimator设置一个监听器：setListener() 和 setUpdateListener() 方法。
移除监听器： set[Update]Listener(null) 填 null 值来移除

ObjectAnimator设置一个监听器：addListener() 和  addUpdateListener() 来添加一个或多个监听器。
移除监听器： remove[Update]Listener() 。

### 1）ViewPropertyAnimator.setListener() / ObjectAnimator.addListener()
回调方法：

```
//当动画开始执行时，这个方法被调用。
onAnimationStart(Animator animation)

//当动画结束时，这个方法被调用。
onAnimationEnd(Animator animation)

//当动画被通过 cancel() 方法取消时，这个方法被调用
onAnimationCancel(Animator animation)

//当动画通过 setRepeatMode() / setRepeatCount() 或 repeat() 方法重复执行时，这个方法被调用(但由于 ViewPropertyAnimator 不支持重复，所以这个方法对 ViewPropertyAnimator 相当于无效。)
onAnimationRepeat(Animator animation)

```

### 2） ViewPropertyAnimator.setUpdateListener() / ObjectAnimator.addUpdateListener()

回调方法：

```
//当动画的属性更新时（不严谨的说，即每过 10 毫秒，动画的完成度更新时），这个方法被调用。
onAnimationUpdate(ValueAnimator animation)
```
###  3） ObjectAnimator.addPauseListener()

### 4）ViewPropertyAnimator.withStartAction/EndAction()
这两个方法是 ViewPropertyAnimator 的独有方法，是一次性的。

> 文章来源：http://hencoder.com/ui-1-6/