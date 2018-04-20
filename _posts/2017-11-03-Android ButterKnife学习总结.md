---
published: true
layout: post
tags:
  - ButterKnife
  - android
categories: Android
description: Android ButterKnife学习总结
---
## 1. 什么是ButterKnife？
ButterKnife是一个专注于Android系统View、Resource、Action的注入框架,可以减少大量的findViewById以及setOnClickListener代码，并且可以利用一个叫做Zelezny的插件，可视化一键生成。

妈妈再也不用担心我敲代码敲到手抽筋儿了~
## 2. 在android studio中配置ButterKnife
1)  在project的build.gradle中添加：

```
classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
```
添加后示例如下：

```
// Top-level build file where you can add configuration options common to all sub-projects/modules.

buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.2'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

```
2) 在app的build.gradle中添加：

```
apply plugin: 'android-apt'
```

```
compile 'com.jakewharton:butterknife:8.4.0'
apt 'com.jakewharton:butterknife-compiler:8.4.0'
```
添加后示例如下：

```
apply plugin: 'com.android.application'
apply plugin: 'android-apt'

android {
    compileSdkVersion 25
    buildToolsVersion "25.0.2"
    defaultConfig {
        applicationId "fxjzzyo.com.butterknifetestdemo"
        minSdkVersion 14
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:25.3.1'
    testCompile 'junit:junit:4.12'
    compile 'com.jakewharton:butterknife:8.4.0'
    apt 'com.jakewharton:butterknife-compiler:8.4.0'
}

```
## 3. 如何使用ButterKnife？
**注：以下的所有操作都要在绑定了当前Activity（ ButterKnife.bind(this);）的前提下才能进行！**

1） 绑定view    @BindView(R.id.xxx)

```
package fxjzzyo.com.butterknifetestdemo;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.widget.TextView;

import butterknife.BindView;
import butterknife.ButterKnife;

public class MainActivity extends AppCompatActivity {
    //绑定
    @BindView(R.id.tv_hello)
    TextView tvHello;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //首先要绑定activity！！！
        ButterKnife.bind(this);
        //使用绑定后的view
        tvHello.setText("hahahahahah");
    }

}

```
2） 绑定views    @BindViews({ R.id.button1  , R.id.button2 ,  R.id.button3 })

```
//绑定
@BindViews({ R.id.button1  , R.id.button2 ,  R.id.button3 })
public List<Button> buttonList ;
```
3） 绑定点击事件@OnClick( )  

```
@OnClick(R.id.button1 )   //给 button1 设置一个点击事件
public void showToast(){
Toast.makeText(this, "is a click",Toast.LENGTH_SHORT).show();
}
```
4） 绑定长按点击事件@OnLongClick( ) 

```
@OnLongClick( R.id.button1 )    //给 button1 设置一个长按事件
public boolean showToast2(){
Toast.makeText(this, "is a long click", Toast.LENGTH_SHORT).show();
return true  ;
}
```
5） 多个控件绑定同一个事件

```
/**
 * 两个不同的button都相应onButterKnifeBtnClick事件回调
 * @param button
 */
    @OnClick({R.id.btn_butter_knife, R.id.btn_butter_knife1})
    public void onButterKnifeBtnClick(Button button) {
        Log.i(TAG, "onButterKnifeBtnClick");
    }
```
自定义的控件不通过ID也可以绑定到自己的事件
```
public class FancyButton extends Button {
  @OnClick
  public void onClick() {
    // TODO do something!
  }
}
```
6） 多事件回调
比如EditText添加addTextChangedListener，使用前：
```
editText.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {

            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {

            }

            @Override
            public void afterTextChanged(Editable s) {

            }
        });
```
使用后：

```
@OnTextChanged(value = R.id.nameEditText, callback = OnTextChanged.Callback.BEFORE_TEXT_CHANGED)
    void beforeTextChanged(CharSequence s, int start, int count, int after) {

    }
    @OnTextChanged(value = R.id.nameEditText, callback = OnTextChanged.Callback.TEXT_CHANGED)
    void onTextChanged(CharSequence s, int start, int before, int count) {

    }
    @OnTextChanged(value = R.id.nameEditText, callback = OnTextChanged.Callback.AFTER_TEXT_CHANGED)
    void afterTextChanged(Editable s) {

    }
```

7） 绑定资源文件

```
	//绑定string字符串
    @BindString(R.string.title_btn_butter_knife)
    String butterKnifeStr;
    //绑定Drawable图片
    @BindDrawable(R.mipmap.ic_launcher)
    Drawable butterKnifeDrawable;
    //绑定Bitmap图片
    @BindBitmap(R.mipmap.ic_launcher)
    Bitmap  butterKnifeBitmap;
    //绑定数组
    @BindArray(R.array.day_of_week)
    String weeks[];
    //绑定color颜色
    @BindColor(R.color.colorPrimary)
    int colorPrimary;
    //绑定Dimen尺寸
    @BindDimen(R.dimen.activity_horizontal_margin)
    Float spacer;
```
8） 在Fragment中使用

由于不同的视图生命周期，所以需要在onCreateView中 bind，在onDestroyView中 unbind

```
public class MyFragment extends Fragment {
  @BindView(R.id.button1) Button button1;
  @BindView(R.id.button2) Button button2;
  private Unbinder unbinder;

  @Override public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.fancy_fragment, container, false);
    //绑定
    unbinder = ButterKnife.bind(this, view);
    return view;
  }

  @Override public void onDestroyView() {
    super.onDestroyView();
    //解绑
    unbinder.unbind();
  }
}
```
9） 在ViewHolder中使用

```
public class MyAdapter extends BaseAdapter {
  @Override public View getView(int position, View view, ViewGroup parent) {
    ViewHolder holder;
    if (view != null) {
      holder = (ViewHolder) view.getTag();
    } else {
      view = inflater.inflate(R.layout.mylayout, parent, false);
      holder = new ViewHolder(view);
      view.setTag(holder);
    }
    holder.name.setText("fxjzzyo");
    return view;
  }

  static class ViewHolder {
    @BindView(R.id.title) TextView name;
    @BindView(R.id.job_title) TextView title;

    public ViewHolder(View view) {
	  //绑定ViewHolder
      ButterKnife.bind(this, view);
    }
  }
}
```
10）ButterKnife.findById()
ButterKnife 也提供了findById函数，通过findById()可以获取Activity、Dialog、View中的view，并且是泛型类型不需要强转。
```
View view =LayoutInflater.from(context).inflate(R.layout.layout, null);
TextView firstName = ButterKnife.findById(view, R.id.first_name);
TextView lastName = ButterKnife.findById(view, R.id.last_name);
ImageView photo = ButterKnife.findById(view, R.id.photo);
```
## 4. ButterKnife自动生成插件Zelezny安装
 在android studio的setting中，如下图：
 
 ![这里写图片描述](http://img.blog.csdn.net/20171103225351052?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 安装后，会提示重启android studio，重启即可
 接下来就能自动生成绑定了~~
 就是这么让你一懒再懒~~
 生成方法：
 将光标放到Activity、Fragment、或ViewHolder中的资源文件上，右键—>Generate—Generate ButterKnife Injections
 大概如下图：
 
 ![这里写图片描述](http://img.blog.csdn.net/20171103230033311?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnhqenp5bw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
 自己勾勾选选，就明白怎么用了。

 
 >文章参考：
 >http://write.blog.csdn.net/mdeditor#!postId=78440509
 
 >http://www.cnblogs.com/whoislcj/p/5620128.html

 完结，撒花~
