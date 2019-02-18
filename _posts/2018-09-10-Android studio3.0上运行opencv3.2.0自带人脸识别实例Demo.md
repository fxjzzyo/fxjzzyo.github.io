---
published: true
layout: post
tags:
  - android
  - opencv
  - 人脸识别
categories: Android
description: 本文章将介绍Android studio3.0上运行opencv3.2.0自带人脸识别实例Demo。
---
## 开发环境
- win10
- android studio 3.0
- opencv3.2.0
## 准备工作
[Android studio下载地址](http://www.androiddevtools.cn/index.html#android-studio)
[opencv各版本下载地址](https://opencv.org/releases.html)

android studio 的安装过程网上有很多，不再赘述。
打开opencv下载地址，往下拉，找到并下载对应版本的android包，如下图
![](https://img-blog.csdn.net/2018091017133421?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
下载后解压，解压后的目录是这样：

![](https://img-blog.csdn.net/20180910171652652?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

其中，
- apk 是示例安装包
- samples是示例代码（eclipse工程）
- sdk是opencv库

## 开始导入项目
打开android studio ，选择import project(Gradle,Eclipse ADT,etc.)
![](https://img-blog.csdn.net/20180910172224432?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
选择samples下的face-detection项目
![](https://img-blog.csdn.net/20180910172339424?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
点击ok后选择工程要放置的位置，然后next
![](https://img-blog.csdn.net/20180910172608156?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
接下来都保持默认即可，点finish
![](https://img-blog.csdn.net/20180910172633624?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
由于这个自带demo的api版本，gradle版本等与你电脑的不同，所以不出意外的话，出错是肯定的。
![](https://img-blog.csdn.net/20180910172954220?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
这个错是说gradle版本不对，当前项目的版本是2.14.1,而我的电脑里已有的版本是4.1，因此打开gradle-wrapper.properties文件，把版本改为4.1（或者你电脑已有的可用的一个版本）就行了。

> 如果不知道自己该用哪个版本，可以找一个可以运行的项目（比如新建个最简单的hello world项目，只要能运行就行），看一下它里面的gradle-wrapper.properties文件用的gradle版本就行了。

![更改前](https://img-blog.csdn.net/2018091017363829?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![更改后](https://img-blog.csdn.net/20180910173649930?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看出，更改后那个错是没有了，又出现一个新的错误：

```
Error:Failed to find target with hash string 'android-14' in: E:\Android\android-sdk
<a href="install.android.platform">Install missing platform(s) and sync project</a>
```
很明显，是api版本不对。打开moudle的build.gradle（不是project的），这里有两个moudle，所以要改两处，别忘了主moudle所依赖的opencvLibrary这个moudle也要改。
![修改前](https://img-blog.csdn.net/2018091017420441?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![修改后](https://img-blog.csdn.net/20180910174409972?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
可以看出，下面这四个版本修改为你电脑配置的可用的版本之后，令人欢呼的**BUILD SUCCESSFUL** !出现了！惊喜不惊喜，意外不意外~
- compileSdkVersion
- buildToolsVersion
- minSdkVersion
- targetSdkVersion
至于具体要改为哪个版本，同上面一样，找一个可运行的项目，看一下它里面怎么配置的，这里也怎么配置。

到这里就结束了吗？当然没有，道路是曲折的。

运行到模拟器或真机上后会出现，找不到opencv manager的提示，提示你安装，但是很可能安装失败。这是opencv在试图调用你手机本地的库，这种调用方式成为动态库，但是你手机上没安装opencv amnager这个应用库啊，所以找不到喽。
这时候，可以使用调用opencv静态库的方式。所谓静态库，就是把要哦调用的库提前都打包到apk中去，这样运行的时候调用自带的库就行了，不用在调用运行环境中的寻找需要调用的库。

两种方式的优缺点也很明显，静态库对运行环境没有要求，因为自带的有，缺点是安装包大，因为自带库了嘛；动态库刚好相反，要求运行环境中必须有相关库，优点是安装包小。

具体做法是，将opencv-3.2.0-android-sdk\OpenCV-android-sdk\sdk\native下的libs文件夹复制到openCVSamplefacedetection\src\main下，并重命名为jniLibs
![](https://img-blog.csdn.net/20180910180030868?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这时候却出现了另一个错误！

![](https://img-blog.csdn.net/20180910180138949?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
Error:Execution failed for task ':openCVSamplefacedetection:compileDebugNdk'.
> Error: Your project contains C++ files but it is not using a supported native build system.
  Consider using CMake or ndk-build integration. For more information, go to:
   https://d.android.com/r/studio-ui/add-native-code.html
  Alternatively, you can use the experimental plugin:
   https://developer.android.com/r/tools/experimental-plugin.html
```
原因是没有设置ndk
![](https://img-blog.csdn.net/20180910180236367?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180910180311613?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

至此，大功告成！

> 参考文章：https://www.2cto.com/kf/201707/656287.html
