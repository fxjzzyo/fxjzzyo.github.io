---
published: true
layout: post
tags:
  - android
  - 动画
categories: Android
description: 本文章将介绍属性动画的高级知识。
---
本篇主要关于属性动画的进一步学习。
包含了TypeEvaluator、PropertyValuesHolder、AnimatorSet的简单用法。

## 1. TypeEvaluator
TypeEvaluator是定义动画完成度转换为实际属性值的，就像Interpolator是定义时间完成度转化为动画完成度的。

如ArgbEvaluator
用来做颜色渐变的动画，从红色渐变到绿色。
```
ObjectAnimator animator = ObjectAnimator.ofInt(view, "color", 0xffff0000, 0xff00ff00);  
animator.setEvaluator(new ArgbEvaluator());  
animator.start(); 
```
在 Android 5.0 （API 21） 加入了新的方法 ofArgb()，当 minSdk 大于或者等于 21时，可以直接用下面这种方式：
```
ObjectAnimator animator = ObjectAnimator.ofArgb(view, "color", 0xffff0000, 0xff00ff00);  
animator.start();
```

### 1）自定义 Evaluator
例如：
```
// 自定义 HslEvaluator
private class HsvEvaluator implements TypeEvaluator<Integer> {  
   float[] startHsv = new float[3];
   float[] endHsv = new float[3];
   float[] outHsv = new float[3];

   @Override
   public Integer evaluate(float fraction, Integer startValue, Integer endValue) {
       // 把 ARGB 转换成 HSV
       Color.colorToHSV(startValue, startHsv);
       Color.colorToHSV(endValue, endHsv);

       // 计算当前动画完成度（fraction）所对应的颜色值
       if (endHsv[0] - startHsv[0] > 180) {
           endHsv[0] -= 360;
       } else if (endHsv[0] - startHsv[0] < -180) {
           endHsv[0] += 360;
       }
       outHsv[0] = startHsv[0] + (endHsv[0] - startHsv[0]) * fraction;
       if (outHsv[0] > 360) {
           outHsv[0] -= 360;
       } else if (outHsv[0] < 0) {
           outHsv[0] += 360;
       }
       outHsv[1] = startHsv[1] + (endHsv[1] - startHsv[1]) * fraction;
       outHsv[2] = startHsv[2] + (endHsv[2] - startHsv[2]) * fraction;

       // 计算当前动画完成度（fraction）所对应的透明度
       int alpha = startValue >> 24 + (int) ((endValue >> 24 - startValue >> 24) * fraction);

       // 把 HSV 转换回 ARGB 返回
       return Color.HSVToColor(alpha, outHsv);
   }
}

ObjectAnimator animator = ObjectAnimator.ofInt(view, "color", 0xff00ff00);  
// 使用自定义的 HslEvaluator
animator.setEvaluator(new HsvEvaluator());  
animator.start();  
```

**总之就是**：（结束-起始）*动画完成度
### 2）ofObject()的使用
借助于 TypeEvaluator，属性动画可以通过 ofObject() 来对不限定类型的属性做动画。

方式为：
1.为目标属性写一个自定义的 TypeEvaluator；
2.使用 ofObject() 来创建 Animator，并把自定义的 TypeEvaluator 作为参数填入；

例如：
```
private class PointFEvaluator implements TypeEvaluator<PointF> {  
   PointF newPoint = new PointF();

   @Override
   public PointF evaluate(float fraction, PointF startValue, PointF endValue) {
       float x = startValue.x + (fraction * (endValue.x - startValue.x));
       float y = startValue.y + (fraction * (endValue.y - startValue.y));

       newPoint.set(x, y);

       return newPoint;
   }
}

ObjectAnimator animator = ObjectAnimator.ofObject(view, "position",  
        new PointFEvaluator(), new PointF(0, 0), new PointF(1, 1));
animator.start(); 
```
## 2. PropertyValuesHolder 同一个动画中改变多个属性
对于ViewPropertyAnimator可以链式写代码。
如：放大的同时逐渐出现。
```
view.animate()  
        .scaleX(1)
        .scaleY(1)
        .alpha(1);
```
对于 ObjectAnimator，不能这么写。
不过可以使用 PropertyValuesHolder 来同时在一个动画中改变多个属性。

```
PropertyValuesHolder holder1 = PropertyValuesHolder.ofFloat("scaleX", 1);  
PropertyValuesHolder holder2 = PropertyValuesHolder.ofFloat("scaleY", 1);  
PropertyValuesHolder holder3 = PropertyValuesHolder.ofFloat("alpha", 1);

ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(view, holder1, holder2, holder3)  
animator.start(); 
```
### 1）AnimatorSet 多个动画配合执行
AnimatorSet可以定义一系列的动画，并控制他们的播放顺序，使它们先后连续播放出来。

例如：
```
ObjectAnimator animator1 = ObjectAnimator.ofFloat(...);  
animator1.setInterpolator(new LinearInterpolator());  
ObjectAnimator animator2 = ObjectAnimator.ofInt(...);  
animator2.setInterpolator(new DecelerateInterpolator());

AnimatorSet animatorSet = new AnimatorSet();  
// 两个动画依次执行
animatorSet.playSequentially(animator1, animator2);  
animatorSet.start(); 
```
还可以：
```
// 两个动画同时执行
animatorSet.playTogether(animator1, animator2);  
animatorSet.start(); 
```

```
// 使用 AnimatorSet.play(animatorA).with/before/after(animatorB)
// 的方式来精确配置各个 Animator 之间的关系
animatorSet.play(animator1).with(animator2);  
animatorSet.play(animator1).before(animator2);  
animatorSet.play(animator1).after(animator2);  
animatorSet.start();  
```
### 2）PropertyValuesHolders.ofKeyframe() 把同一个属性拆分

可以通过设置  Keyframe （关键帧），把同一个动画属性拆分成多个阶段。

```
// 在 0% 处开始
Keyframe keyframe1 = Keyframe.ofFloat(0, 0);  
// 时间经过 50% 的时候，动画完成度 100%
Keyframe keyframe2 = Keyframe.ofFloat(0.5f, 100);  
// 时间见过 100% 的时候，动画完成度倒退到 80%，即反弹 20%
Keyframe keyframe3 = Keyframe.ofFloat(1, 80);  
PropertyValuesHolder holder = PropertyValuesHolder.ofKeyframe("progress", keyframe1, keyframe2, keyframe3);

ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(view, holder);  
animator.start();  
```
### 3）ValueAnimator 
ValueAnimator 是 ObjectAnimator 的父类，其功能比ObjectAnimator更基础。ObjectAnimator 是自动调用目标对象的 setter 方法来更新目标属性的值，而对于一些没办法调用setter的控件来说，Objectanimator就不行了。这时就可以用 ValueAnimator，在它的 onUpdate() 里面更新这个属性的值，并且手动调用 invalidate()。

ViewPropertyAnimator、ObjectAnimator、ValueAnimator 这三种 Animator，它们其实是一种递进的关系：从左到右依次变得更加难用，也更加灵活。

> 文章来源：http://hencoder.com/ui-1-7/