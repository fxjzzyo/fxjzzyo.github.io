---
published: true
layout: post
tags:
  - canvas
  - android
  - 自定义view
categories: Android
description: 本文章将介绍自定义view绘制基础。
---
自定义View的绘制主要在onDraw()方法中进行。
主要类有Canvas、Paint、Path。
### 1. Paint类
Paint意为：涂料，画笔。用来画图形的共有属性，如颜色，风格，宽窄，大小等。

```
Paint.setStyle(Style style) //设置绘制模式
Paint.setColor(int color) //设置颜色
Paint.setStrokeWidth(float width)// 设置线条宽度
Paint.setTextSize(float textSize) //设置文字大小
Paint.setAntiAlias(boolean aa) //设置抗锯齿开关
```

```
paint.setColor(Color.RED); // 设置为红色  
paint.setStyle(Paint.Style.STROKE); // Style 修改为画线模式  
paint.setStrokeWidth(20); // 线条宽度为 20 像素 
Paint.setAntiAlias(true);//开启抗锯齿
//也可以这样开启抗锯齿
Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);
```

### 2. Canvas.drawXXX()画简单图形
Canvas意为画布，但是不能从字面意思理解为画布。很多困惑都是因为先入为主的字面意思影响了我们对知识的理解。这里的画布可以认为是android种画图的工具，就是用来作画的。
1）. 画颜色（颜色填充）
```
Canvas.drawColor(int color) //颜色填充

Canvas.drawRGB(int r, int g, int b)//颜色填充

Canvas.drawARGB(int a, int r, int g, int b)//颜色填充

//如：
canvas.drawRGB(100, 200, 100);  
canvas.drawARGB(100, 100, 200, 100); 
```

2）. 画圆 drawCircle(float centerX, float centerY, float radius, Paint paint)

```

//如：
canvas.drawCircle(300, 300, 200, paint);

```

3）. 画矩形 drawRect(float left, float top, float right, float bottom, Paint paint) 

```
paint.setStyle(Style.FILL);  
canvas.drawRect(100, 100, 500, 500, paint);

paint.setStyle(Style.STROKE);  
canvas.drawRect(700, 100, 1100, 500, paint);
```
画圆角矩形
```
drawRoundRect(float left, float top, float right, float bottom, float rx, float ry, Paint paint)// 画圆角矩形
//left, top, right, bottom 是四条边的坐标，rx 和 ry 是圆角的横向半径和纵向半径
//例如：
canvas.drawRoundRect(100, 100, 500, 300, 50, 50, paint);
```

4）. 画点 drawPoint(float x, float y, Paint paint)
x, y为点的坐标
```
paint.setStrokeWidth(20);//设置线条宽度  
paint.setStrokeCap(Paint.Cap.ROUND);//设置点的形状为圆形
paint.setStrokeCap(Paint.Cap.SQUARE);//设置点的形状为方形
paint.setStrokeCap(Paint.Cap.BUTT);//设置点的形状为平头
canvas.drawPoint(50, 50, paint); 
```
批量画点：

```
drawPoints(float[] pts, int offset, int count, Paint paint)//画点（批量）

drawPoints(float[] pts, Paint paint) //画点（批量）
```
pts 这个数组是点的坐标，每两个成一对；
offset 表示跳过数组的前几个数再开始记坐标；
count 表示一共要绘制几个点
例如：
```
float[] points = {0, 0, 50, 50, 50, 100, 100, 50, 100, 100, 150, 50, 150, 100};  
// 绘制四个点：(50, 50) (50, 100) (100, 50) (100, 100)
canvas.drawPoints(points, 2 /* 跳过两个数，即前两个 0 */,  
          8 /* 一共绘制 8 个数（4 个点）*/, paint);
```


4）. 画椭圆 drawOval(float left, float top, float right, float bottom, Paint paint)

```
paint.setStyle(Style.FILL);  
canvas.drawOval(50, 50, 350, 200, paint);

paint.setStyle(Style.STROKE);  
canvas.drawOval(400, 50, 700, 200, paint);  
```

5）. 画线 drawLine(float startX, float startY, float stopX, float stopY, Paint paint)

```
canvas.drawLine(200, 200, 800, 500, paint);
```
批量画线

```
drawLines(float[] pts, int offset, int count, Paint paint)//画线（批量）

drawLines(float[] pts, Paint paint)// 画线（批量）
```
例如：
```
float[] points = {20, 20, 120, 20, 70, 20, 70, 120, 20, 120, 120, 120, 150, 20, 250, 20, 150, 20, 150, 120, 250, 20, 250, 120, 150, 120, 250, 120};  
canvas.drawLines(points, paint); 
```

6）. 画弧形或扇形 drawArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean useCenter, Paint paint)

drawArc() 是使用一个椭圆来描述弧形的。
left, top, right, bottom 描述的是这个弧形所在的椭圆；
startAngle： 是弧形的起始角度（x 轴的正向，即正右的方向，是 0 度的位置；顺时针为正角度，逆时针为负角度）
sweepAngle ：是弧形划过的角度；
useCenter ：表示是否连接到圆心，如果不连接到圆心，就是弧形，如果连接到圆心，就是扇形
```
paint.setStyle(Paint.Style.FILL); // 填充模式  
canvas.drawArc(200, 100, 800, 500, -110, 100, true, paint); // 绘制扇形  
canvas.drawArc(200, 100, 800, 500, 20, 140, false, paint); // 绘制弧形  
paint.setStyle(Paint.Style.STROKE); // 画线模式  
canvas.drawArc(200, 100, 800, 500, 180, 60, false, paint); // 绘制不封口的弧形 
```

### 3. Path类画复杂图形
1）添加子图形

```
addCircle(float x, float y, float radius, Direction dir) //添加圆
```
x, y, radius 这三个参数是圆的基本信息；
dir 是画圆的路径的方向，
分为：Path.Direction.CW（顺时针）、Path.Direction.CCW（逆时针）

```
addOval(float left, float top, float right, float bottom, Direction dir) / addOval(RectF oval, Direction dir) //添加椭圆

addRect(float left, float top, float right, float bottom, Direction dir) / addRect(RectF rect, Direction dir)// 添加矩形

addRoundRect(RectF rect, float rx, float ry, Direction dir) //添加圆角矩形

addRoundRect(float left, float top, float right, float bottom, float rx, float ry, Direction dir)//添加圆角矩形

addRoundRect(RectF rect, float[] radii, Direction dir)//添加圆角矩形

addRoundRect(float left, float top, float right, float bottom, float[] radii, Direction dir)//添加圆角矩形

addPath(Path path) //添加另一个 Path

addArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle) //添加圆弧

addArc(RectF oval, float startAngle, float sweepAngle)//添加圆弧
```
2）画线

```
lineTo(float x, float y) //从当前位置(默认坐标原点)向目标位置画一条直线，x 和 y 是目标位置的坐标（绝对坐标）

rLineTo(float x, float y) //画直线（相对当前位置的相对坐标）
```

```
quadTo(float x1, float y1, float x2, float y2)//画二次贝塞尔曲线（绝对坐标）, 参数中的 x1, y1 和 x2, y2 则分别是控制点和终点的坐标

rQuadTo(float dx1, float dy1, float dx2, float dy2)// 画二次贝塞尔曲线（相对当前位置的相对坐标）

cubicTo(float x1, float y1, float x2, float y2, float x3, float y3)//画三次贝塞尔曲线

rCubicTo(float x1, float y1, float x2, float y2, float x3, float y3)//画三次贝塞尔曲线
```
移动下笔点

```
moveTo(float x, float y)// 移动到目标位置（绝对坐标）

rMoveTo(float x, float y)// 移动到目标位置（相对当前位置的相对坐标）

```

```
arcTo(RectF oval, float startAngle, float sweepAngle, boolean forceMoveTo)//只用来画弧形, 
//forceMoveTo=true：笔抬起，直接移动下笔点到弧形起点，中间没留下移动痕迹；
//forceMoveTo=false：笔不抬起，移动下笔点到弧形起点，中间留下移动痕迹;

arcTo(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean forceMoveTo) // 同上。用RectF更好能兼容低版本。而不用的话，要求API>=21

arcTo(RectF oval, float startAngle, float sweepAngle)//画弧形，默认forceMoveTo=true
```
**注：**以上画矩形或椭圆，或弧形时，总能用Rect来确定一个方形范围。可以用RectF来定义这个范围，也可以用left，top, right, down来定义。但是，用前者RectF更好能兼容低版本。而用后者的话，要求API>=21

封闭图形

```
close();// 封闭当前子图形

//例如：
paint.setStyle(Style.STROKE);  
path.moveTo(100, 100);  
path.lineTo(200, 100);  
path.lineTo(150, 150); 
```
3） Path.setFillType(Path.FillType ft) 设置填充方式

FillType 的取值有四个:
```
EVEN_ODD //奇偶原则
WINDING // non-zero winding rule（非零环绕数原则）（默认值）
INVERSE_EVEN_ODD//奇偶原则的相反
INVERSE_WINDING//非零环绕数原则的相反
```
> even-odd rule （奇偶原则）：对于平面中的任意一点，向任意方向射出一条射线，这条射线和图形相交的次数（相交才算，相切不算哦）如果是奇数，则这个点被认为在图形内部，是要被涂色的区域；如果是偶数，则这个点被认为在图形外部，是不被涂色的区域。
> 

### 4. 画图片和文字
1） drawBitmap(Bitmap bitmap, float left, float top, Paint paint) 画 Bitmap

```
drawBitmap(bitmap, 200, 100, paint);

drawBitmap(Bitmap bitmap, Rect src, RectF dst, Paint paint) //

drawBitmap(Bitmap bitmap, Rect src, Rect dst, Paint paint) //

drawBitmap(Bitmap bitmap, Matrix matrix, Paint paint)
```

2）drawText(String text, float x, float y, Paint paint) 绘制文字

```
canvas.drawText(text, 200, 100, paint);  

paint.setTextSize(18);  
canvas.drawText(text, 100, 25, paint);  
paint.setTextSize(36);  
canvas.drawText(text, 100, 70, paint);  
paint.setTextSize(60);  
canvas.drawText(text, 100, 145, paint);  
paint.setTextSize(84);  
canvas.drawText(text, 100, 240, paint); 
```
未完待续...
> 参考文档：http://hencoder.com/ui-1-1/
