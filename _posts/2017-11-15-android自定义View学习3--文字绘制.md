---
published: true
layout: post
tags:
  - 自定义view
  - android
  - canvas
  - tex
  - paint
categories: Android
description: 本文章将介绍android绘制textview相关的一大堆函数。
---
介绍一大堆绘制文字相关的函数...

主要分canvas绘制文字、Paint辅助绘制文字、Paint测量文字相关值。

### 1. canvas绘制文字
#### 1) drawText(String text, float x, float y, Paint paint)

```
canvas.drawText(text, 200, 100, paint);
```
text 是文字内容，x 和 y 是文字的坐标。
**注意：**这个坐标并不是文字的左上角，而是一个与左下角比较接近的位置。
#### 2) drawTextRun(CharSequence text, int start, int end, int contextStart, int contextEnd, float x, float y, boolean isRtl, Paint paint)
**用来绘制某些特殊外国文字，如阿拉伯文字等。**
text：要绘制的文字 
start：从那个字开始绘制 
end：绘制到哪个字结束 
contextStart：上下文的起始位置。contextStart 需要小于等于 start 
contextEnd：上下文的结束位置。contextEnd 需要大于等于 end 
x：文字左边的坐标 
y：文字的基线坐标 
isRtl：是否是 RTL（Right-To-Left，从右向左）
#### 3) drawTextOnPath(String text, Path path, float hOffset, float vOffset, Paint paint)

```
canvas.drawTextOnPath("Hello HeCoder", path, 0, 0, paint); 
```
hOffset ：文字相对于 Path 的水平偏移量
vOffset ：文字相对于 Path 的竖直偏移量，
例如：设置 hOffset 为 5， vOffset 为 10，文字就会右移 5 像素和下移 10 像素。
#### 4) StaticLayout(CharSequence source, TextPaint paint, int width, Layout.Alignment align, float spacingmult, float spacingadd, boolean includepad)
StaticLayout 用来**绘制换行文字**。
它并不是一个 View 或者 ViewGroup ，而是 android.text.Layout 的子类，它是纯粹用来绘制文字的。 StaticLayout 支持换行，它既可以为文字设置宽度上限来让文字自动换行，也会在 \n 处主动换行。
```
String text1 = "Lorem Ipsum is simply dummy text of the printing and typesetting industry.";  
StaticLayout staticLayout1 = new StaticLayout(text1, paint, 600,  
        Layout.Alignment.ALIGN_NORMAL, 1, 0, true);
String text2 = "a\nbc\ndefghi\njklm\nnopqrst\nuvwx\nyz";  
StaticLayout staticLayout2 = new StaticLayout(text2, paint, 600,  
        Layout.Alignment.ALIGN_NORMAL, 1, 0, true);

...

canvas.save();  
canvas.translate(50, 100);  
staticLayout1.draw(canvas);  
canvas.translate(0, 200);  
staticLayout2.draw(canvas);  
canvas.restore();  
```

width 是文字区域的宽度，文字到达这个宽度后就会自动换行； 
align 是文字的对齐方向； 
spacingmult 是行间距的倍数，通常情况下填 1 就好； 
spacingadd 是行间距的额外增加值，通常情况下填 0 就好； 
includeadd 是指是否在文字上下添加额外的空间，来避免某些过高的字符的绘制出现越界。

### 2. 使用Paint辅助绘制文字

#### 1) setTextSize(float textSize)
设置文字大小。
```
paint.setTextSize(18);  
canvas.drawText(text, 100, 25, paint); 
```
#### 2) setTypeface(Typeface typeface)
设置字体。
```
paint.setTypeface(Typeface.DEFAULT); //默认字体
canvas.drawText(text, 100, 150, paint);  
paint.setTypeface(Typeface.SERIF);//宋体
canvas.drawText(text, 100, 300, paint);  
paint.setTypeface(Typeface.createFromAsset(getContext().getAssets(), "Satisfy-Regular.ttf"));//使用自定义字体
canvas.drawText(text, 100, 450, paint); 
```

#### 3) setFakeBoldText(boolean fakeBoldText)


设置文字是否加粗（伪粗体，因为实质上只是多绘制了几遍，而不是使用粗体）。
```
paint.setFakeBoldText(false);  
canvas.drawText(text, 100, 150, paint);  
paint.setFakeBoldText(true);  
canvas.drawText(text, 100, 230, paint); 
```
#### 4) setStrikeThruText(boolean strikeThruText)

设置文字是否加删除线。（就是文字中间画一横）
```
paint.setStrikeThruText(true);  
canvas.drawText(text, 100, 150, paint); 
```
#### 5) setUnderlineText(boolean underlineText)

设置文字是否加下划线。
```
paint.setUnderlineText(true);  
canvas.drawText(text, 100, 150, paint);  
```

#### 6)  setTextSkewX(float skewX)

设置文字横向错切角度。（其实就是文字倾斜度）
当skewX为负时，向右倾斜；为正是向左倾斜。
```
paint.setTextSkewX(-0.5f);  
canvas.drawText(text, 100, 150, paint);
```
#### 7) setTextScaleX(float scaleX)

设置文字横向放缩。(也就是文字变宽变窄)
scaleX为1，文字不变；
scaleX小于1，文字变窄；
scaleX大于1，文字变宽；
```
paint.setTextScaleX(1);  
canvas.drawText(text, 100, 150, paint);  
paint.setTextScaleX(0.8f);  
canvas.drawText(text, 100, 230, paint);  
paint.setTextScaleX(1.2f);  
canvas.drawText(text, 100, 310, paint);
```
#### 8) setLetterSpacing(float letterSpacing)

设置字符间距。默认值是 0（这时还是有默认的间隙的）。
letterSpacing大于0时，间隙扩大。
```
paint.setLetterSpacing(0.2f);  
canvas.drawText(text, 100, 150, paint); 
```
#### 9) setFontFeatureSettings(String settings)

用 CSS 的 font-feature-settings 的方式来设置文字。
```
paint.setFontFeatureSettings("smcp"); // 设置 "small caps"  
canvas.drawText("Hello HenCoder", 100, 150, paint);  
```
#### 10) setTextAlign(Paint.Align align)

设置文字的对齐方式。一共有三个值：LEFT CETNER 和 RIGHT。默认值为 LEFT。
**注意：**文字的对齐是以**坐标**为基准的。即：

 - 左对齐就是文字串的左边在设定的坐标上； 
 - 右对齐就是文字串的右边在设定的坐标上；
 -  居中对齐就是文字串的中间在设定的坐标上；
 
不要认为左对齐，文字就靠在最左边，相反，文字的最左边在设定的坐标位置时，文字本身是在坐标右边的。
```
paint.setTextAlign(Paint.Align.LEFT);  
canvas.drawText(text, 500, 150, paint);  
paint.setTextAlign(Paint.Align.CENTER);  
canvas.drawText(text, 500, 150 + textHeight, paint);  
paint.setTextAlign(Paint.Align.RIGHT);  
canvas.drawText(text, 500, 150 + textHeight * 2, paint); 
```
#### 11) setTextLocale(Locale locale) / setTextLocales(LocaleList locales)

设置绘制所使用的 Locale。就是手机系统里的语言区域啦。
```
paint.setTextLocale(Locale.CHINA); // 简体中文  
canvas.drawText(text, 150, 150, paint);  
paint.setTextLocale(Locale.TAIWAN); // 繁体中文  
canvas.drawText(text, 150, 150 + textHeight, paint);  
paint.setTextLocale(Locale.JAPAN); // 日语  
canvas.drawText(text, 150, 150 + textHeight * 2, paint);  
```
#### 12) setHinting(int mode)

设置是否启用字体的 hinting （字体微调）。
**如今手机屏幕的像素密度已经非常高，不需要修正了，此方法可忽略。**


#### 13) setElegantTextHeight(boolean elegant)

此方法只是对某些特殊国家文字（如泰文）有用，作用是是否还原显示其原有高度（因为有些文字太高，绘制时默认压缩了）。
```
paint.setFontFeatureSettings("smcp"); // 设置 "small caps"  
canvas.drawText("Hello HenCoder", 100, 150, paint);  
```
#### 14) setFontFeatureSettings(String settings)

用 CSS 的 font-feature-settings 的方式来设置文字。
```
paint.setElegantTextHeight(true); 
```
#### 15) setSubpixelText(boolean subpixelText)

是否开启次像素级的抗锯齿（ sub-pixel anti-aliasing ）。
应该就是很厉害的抗锯齿吧~~~~
### 3. Paint测量文字尺寸
#### 1)  float getFontSpacing()

获取推荐的行距。
即推荐的两行文字的 baseline(就是文字最下面的靠近文字的线) 的距离。这个值是系统根据文字的字体和字号自动计算的。
```
canvas.drawText(texts[0], 100, 150, paint);  
canvas.drawText(texts[1], 100, 150 + paint.getFontSpacing, paint);  
canvas.drawText(texts[2], 100, 150 + paint.getFontSpacing * 2, paint);  
```
#### 2)  FontMetircs getFontMetrics()
获取 Paint 的 FontMetrics。
FontMetrics 是个相对专业的工具类，它提供了几个文字排印方面的数值：ascent, descent, top, bottom,  leading。

- ascent : 字符的上界
- descent : 字符的下界
- top : 字形的上界
- bottom : 字形的下界
- leading : 上行的 bottom 线和下行的 top 线的距离

可以通过
FontMetrics.ascent：float 类型。
FontMetrics.descent：float 类型。
FontMetrics.top：float 类型。
FontMetrics.bottom：float 类型。
FontMetrics.leading：float 类型。
来获得它们的值。
也可以通过Paint.ascent() 和 Paint.descent() 来快捷获取。

#### 3)  getTextBounds(String text, int start, int end, Rect bounds)

获取文字的显示范围。
text 是要测量的文字，start 和 end 分别是文字的起始和结束位置，bounds 是存储文字显示范围的对象，方法在测算完成之后会把结果写进 bounds。
```
paint.setStyle(Paint.Style.FILL);  
canvas.drawText(text, offsetX, offsetY, paint);

paint.getTextBounds(text, 0, text.length(), bounds);  
bounds.left += offsetX;  
bounds.top += offsetY;  
bounds.right += offsetX;  
bounds.bottom += offsetY;  
paint.setStyle(Paint.Style.STROKE);  
canvas.drawRect(bounds, paint);  
```
#### 4)   float measureText(String text)

测量文字绘制时所占用的宽度并返回。

```
canvas.drawText(text, offsetX, offsetY, paint);  
float textWidth = paint.measureText(text);  
canvas.drawLine(offsetX, offsetY, offsetX + textWidth, offsetY, paint); 
```
#### 5)  getTextWidths(String text, float[] widths)

获取字符串中每个字符的宽度，并把结果填入参数 widths

#### 6)  int breakText(String text, boolean measureForwards, float maxWidth, float[] measuredWidth)

这个方法也是用来测量文字宽度的。但和 measureText() 的区别是， breakText() 是在给出宽度上限的前提下测量文字的宽度。如果文字的宽度超出了上限，那么在临近超限的位置截断文字。

breakText() 的返回值是截取的文字个数（如果宽度没有超限，则是文字的总个数）。参数中， text 是要测量的文字；measureForwards 表示文字的测量方向，true 表示由左往右测量；maxWidth 是给出的宽度上限；measuredWidth 是用于接受数据，而不是用于提供数据的：方法测量完成后会把截取的文字宽度（如果宽度没有超限，则为文字总宽度）赋值给 measuredWidth[0]。

```
int measuredCount;  
float[] measuredWidth = {0};

// 宽度上限 300 （不够用，截断）
measuredCount = paint.breakText(text, 0, text.length(), true, 300, measuredWidth);  
canvas.drawText(text, 0, measuredCount, 150, 150, paint);

// 宽度上限 400 （不够用，截断）
measuredCount = paint.breakText(text, 0, text.length(), true, 400, measuredWidth);  
canvas.drawText(text, 0, measuredCount, 150, 150 + fontSpacing, paint);

// 宽度上限 500 （够用）
measuredCount = paint.breakText(text, 0, text.length(), true, 500, measuredWidth);  
canvas.drawText(text, 0, measuredCount, 150, 150 + fontSpacing * 2, paint);

// 宽度上限 600 （够用）
measuredCount = paint.breakText(text, 0, text.length(), true, 600, measuredWidth);  
canvas.drawText(text, 0, measuredCount, 150, 150 + fontSpacing * 3, paint);  
```
#### 7)  getRunAdvance(CharSequence text, int start, int end, int contextStart, int contextEnd, boolean isRtl, int offset)
对于一段文字，计算出某个字符处**光标的 x 坐标**。 

start、end 是文字的起始和结束坐标；
contextStart contextEnd ：上下文的起始和结束坐标；
isRtl ：文字的方向；
offset ：字数的偏移，即计算第几个字符处的光标。
```
int length = text.length();  
float advance = paint.getRunAdvance(text, 0, length, 0, length, false, length);  
canvas.drawText(text, offsetX, offsetY, paint);  
canvas.drawLine(offsetX + advance, offsetY - 50, offsetX + advance, offsetY + 10, paint);  
```

#### 8)  getOffsetForAdvance(CharSequence text, int start, int end, int contextStart, int contextEnd, boolean isRtl, float advance)
给出一个位置的像素值，计算出文字中最接近这个位置的字符偏移量（即第几个字符最接近这个坐标）

 text : 要测量的文字；
contextStart contextEnd : 上下文的起始和结束坐标；
isRtl : 文字方向(从右向左)；
advance : 给出的位置的像素值。

填入参数，对应的字符偏移量将作为返回值返回。


#### 9) hasGlyph(String string)
检查指定的字符串中是否是一个单独的字形 (glyph）。最简单的情况是，string 只有一个字母（比如  a）。
字符串：a，hasGlyph("a")为true
字符串：ab，hasGlyph("ab")为false

啊。。。太多了。这些记不住的，能知道，有个印象就像，回头再来查吧~~~~
> 参考文章：http://hencoder.com/ui-1-3/
