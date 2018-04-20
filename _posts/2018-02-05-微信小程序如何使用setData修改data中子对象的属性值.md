---
published: true
layout: post
tags:
  - 微信小程序
  - setData
categories: 微信小程序
description: 本文章将介绍微信小程序如何使用setData修改data中子对象的属性值。
---
在微信小程序开发中数据与页面的绑定是靠data对象来实现的。如果要修改页面中某个变量的值，就需要使用this.setData({变量名：值})。
比如，点击按钮修改变量值：

```
change:function(e){
  this.setData({
    test:'hello world!'
  })
}
```

但是如果要修改data中子对象的属性值呢？一个很自然的想法是**多点几次**不就行了？比如person.name

```
 this.setData({
    person.name:'fxjzzyo'
  })
```
但是，很遗憾不能这样做啦！！QAQ，上面是错误示范啦！！！
会报错啊=_=QAQ

**难道为了更改其中一个属性，就只能把一个对象的所有属性都setData一遍吗？**

呵呵，当然不是啦。山人自有妙计也。
只需要用一个字符串拼接处前面的属性名就ok啦~
且看下面的栗子~

----------


例如：
页面代码test.wxml：

```
<view class='page'>
  <view>{{test}}</view>
  <button bindtap='change'>修改test为hello world!</button>
    <view>{{person.name}}</view>
  <button bindtap='changePerson'>修改人名为fxjzzyo</button>
</view>
```
逻辑代码test.js:

```

Page({

  /**
   * 页面的初始数据
   */
  data: {
     test:'test',
	 person:{
      name:'jay',
      age:12,
      address:'china',
      like:'sing song',
      phone:'123456'
    }
  },
  change:function(e){
  this.setData({
    test:'hello world!'
  })
},
  changePerson:function(e){
    var str = 'person.name';
    this.setData({
      [str]: 'fxjzzyo'
    })
  },
  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
   
  }
})
```
重点在changePerson这个方法中：

```
 changePerson:function(e){
    var str = 'person.name';
    this.setData({
      [str]: 'fxjzzyo'
    })
  },
```
很easy啦，只需要把原本要写的person.name:'fxjzzyo'前面的person.name用一个字符串变量拼接出来就ok啦~
**切记：使用时要把那个变量用中括号（[]）括起来**


----------
虽然是小知识点，但用处肯定大大地。。假如不会这个的话，就无法修改子对象的属性值，最笨的方法是，把这个对象重新赋值一遍。对象属性个数少了还好说，要是碰到一个对象的属性超级多，而你只是为了修改其中一个属性就把所有值赋值一遍，那也是傻的可以啊>_<。

