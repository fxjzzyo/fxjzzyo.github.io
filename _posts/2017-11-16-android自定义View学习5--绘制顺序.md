---
published: true
layout: post
tags:
  - 自定义view
  - android
categories: Android
description: 本文章将介绍android自定义view中的绘制顺序
---
记住一个原则：android自定义view绘制时，先绘制的内容会被后绘制的覆盖。

view的绘制顺序是：
背景-->view 主体-->子view-->滑动边缘渐变和滑动条-->前景

见下图：

![绘制顺序对应图](http://img.blog.csdn.net/20171116221138820?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**注：** draw()方法是总调度方法，所以如果把绘制代码写在 super.draw() 的下面，那么这段代码会在其他所有绘制完成之后再执行。

![draw方法的整体调度](http://img.blog.csdn.net/20171116221415310?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

> 1. 在 ViewGroup 的子类中重写除 dispatchDraw() 以外的绘制方法时，可能需要调用  setWillNotDraw(false)；
> 因为：出于效率的考虑，ViewGroup 默认会绕过 draw() 方法，换而直接执行 dispatchDraw()，以此来简化绘制流程。
> 所以如果你自定义了某个 ViewGroup 的子类（比如 LinearLayout）并且需要在它的除  dispatchDraw() 以外的任何一个绘制方法内绘制内容，你可能会需要调用 View.setWillNotDraw(false) 这行代码来切换到完整的绘制流程。
> 
2. 在重写的方法有多个选择时，优先选择 onDraw()。

> 文章来源：http://hencoder.com/ui-1-5/
