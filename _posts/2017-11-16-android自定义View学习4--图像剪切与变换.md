---
published: true
layout: post
tags:
  - 自定义view
  - android
  - canvas
  - matrix
  - camera
categories: Android
description: 本文章将介绍android中使用canvas、matrix、camera对图像的剪切、变换操作。
---
本文章主要介绍android中使用canvas、matrix、camera对图像的剪切、变换操作。
# 1. 图像剪切
## 1） clipRect()
剪切矩形范围。
```
canvas.save();  
canvas.clipRect(left, top, right, bottom);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore(); 
```
**注意：**记得加Canvas.save() 和 Canvas.restore() 来及时恢复绘制范围
## 2） clipPath()
剪切Path范围。
```
canvas.save();  
canvas.clipPath(path1);  
canvas.drawBitmap(bitmap, point1.x, point1.y, paint);  
canvas.restore();
```
# 2. 几何变换
##  使用 Canvas 来做常见的二维变换

### 1）Canvas.translate(float dx, float dy) 平移 
dx 、dy 分别表示横向和纵向的位移。
```
canvas.save();  
canvas.translate(200, 0);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore();
```
### 2）Canvas.rotate(float degrees, float px, float py) 旋转
 px 、py ： 旋转中心；
 degrees ：旋转角度，单位是度（也就是一周有 360° 的那个单位），方向是顺时针为正向；
```
canvas.save();  
canvas.rotate(45, centerX, centerY);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore(); 
```
### 3）Canvas.scale(float sx, float sy, float px, float py) 放缩 
px 、py ： 放缩的轴心；
sx 、sy ： 横向、纵向的放缩倍数； 
```
canvas.save();  
canvas.scale(1.3f, 1.3f, x + bitmapWidth / 2, y + bitmapHeight / 2);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore();  
```
### 4）skew(float sx, float sy) 错切
sx 、 sy ： x 方向、y 方向的错切系数。
sx为0，sy为正如0.5时，向右斜；
```
canvas.save();  
canvas.skew(0, 0.5f);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore(); 
```
##  使用 Matrix 来做变换
Matrix 做常见变换的方式：


### 1）使用Matrix 做常见变换
创建 Matrix 对象；
调用 Matrix 的 pre/postTranslate/Rotate/Scale/Skew() 方法来设置几何变换；
使用 Canvas.setMatrix(matrix) 或 Canvas.concat(matrix) 来把几何变换应用到 Canvas。
```
Matrix matrix = new Matrix();

...

matrix.reset();  
matrix.postTranslate();  
matrix.postRotate();

canvas.save();  
canvas.concat(matrix);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore(); 
```
把 Matrix 应用到 Canvas 有两个方法：
 Canvas.setMatrix(matrix) 和 Canvas.concat(matrix)。

> Canvas.setMatrix(matrix)：用 Matrix 直接**替换 Canvas 当前的变换矩阵**，即抛弃 Canvas 当前的变换，改用 Matrix 的变换（注：尽量用 concat(matrix) 吧）；
Canvas.concat(matrix)：用 Canvas 当前的变换矩阵和 Matrix 相乘，即基于 Canvas 当前的变换，**叠加上 Matrix 中的变换**。


### 2）使用 Matrix 来做自定义变换
Matrix.setPolyToPoly(float[] src, int srcIndex, float[] dst, int dstIndex, int pointCount) 用点对点映射的方式设置变换。

poly 就是「多」的意思。setPolyToPoly() 的作用是通过**多点映射**的方式来直接设置变换。

src ：源点集合；
dst ：目标点集；
srcIndex 和 dstIndex ：第一个点的偏移；
pointCount ：采集的点的个数（个数不能大于 4，因为大于 4 个点就无法计算变换了）。
```
Matrix matrix = new Matrix();  
float pointsSrc = {left, top, right, top, left, bottom, right, bottom};  
float pointsDst = {left - 10, top + 50, right + 120, top - 90, left + 20, bottom + 30, right + 20, bottom + 60};

...

matrix.reset();  
matrix.setPolyToPoly(pointsSrc, 0, pointsDst, 0, 4);

canvas.save();  
canvas.concat(matrix);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore();  
```



##  使用 Camera 来做变换
### 1）Camera.rotate*() 三维旋转
Camera.rotate*() 一共有四个方法：
rotateX(deg)、 rotateY(deg) 、rotateZ(deg) 、rotate(x, y, z)。

camera的三维坐标系为：

![camera三维坐标系](http://img.blog.csdn.net/20171116133318109?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

与android二维坐标不同，camera的三维坐标系，x轴正向是右边，y轴正向是上边，z轴正向是朝着屏幕里面的方向。

图中前头所标方向为默认旋转方向。
```
canvas.save();//保存 Canvas 的状态 

camera.save(); // 保存 Camera 的状态  
camera.rotateX(30); // 旋转 Camera 的三维空间  
camera.applyToCanvas(canvas); // 把旋转投影到 Canvas  
camera.restore(); // 恢复 Camera 的状态

canvas.drawBitmap(bitmap, point1.x, point1.y, paint);  
canvas.restore(); //恢复 Canvas 的状态
```
**注意：** Camera也要像Canvas一样保存和恢复状态。
上述旋转不是以中心为旋转轴心的，所以旋转后的图像不对称。
若要旋转后的图形左右对称，需要配合上 Canvas.translate()，在三维旋转之前把绘制内容的中心点移动到原点，即旋转的轴心，然后在三维旋转后再把投影移动回来：

```
canvas.save();

camera.save(); // 保存 Camera 的状态  
camera.rotateX(30); // 旋转 Camera 的三维空间  
canvas.translate(centerX, centerY); // 旋转之后把投影移动回来  
camera.applyToCanvas(canvas); // 把旋转投影到 Canvas  
canvas.translate(-centerX, -centerY); // 旋转之前把绘制内容移动到轴心（原点）  
camera.restore(); // 恢复 Camera 的状态

canvas.drawBitmap(bitmap, point1.x, point1.y, paint);  
canvas.restore();  
```
**注意：**
> Canvas 的几何变换顺序是反的，所以要把移动到中心的代码写在下面，把从中心移动回来的代码写在上面。


### 2）Camera.translate(float x, float y, float z) 移动
x、y、z分别是x轴，y轴，z轴的移动距离。不过这个方法不常用。

### 3）Camera.setLocation(x, y, z) 设置虚拟相机的位置
**注意：**它的参数的单位不是像素，而是 inch，英寸。且，换算方法是1英寸=72像素。

setLocation() 方法的作用是设置相机的位置。在 Camera 中，相机的默认位置是 (0, 0, -8)（英寸）。8 x 72 = 576，所以它的默认位置是 (0, 0, -576)（像素）。
通常在做旋转的时候，向屏幕外旋转时会出现图像变大的不适效果，为了修复这种效果，通常用该方法来把相机往后移动，就可以修复这种问题。
```
camera.setLocation(0, 0, newZ);  
```


----------


> 文章来源于http://hencoder.com/ui-1-4/
