---
published: true
layout: post
tags:
  - android
  - ffmpeg
categories: Android
description: 本文章将介绍Android使用FFmpegMediaMetadataRetriever获取视频缩略图。
---

## 1. 使用MediaMetadataRetriever获取视频元数据
一般获取视频、音频等媒体文件的元数据信息，我们会用android自带的MediaMetadataRetriever这个类。
比如，获取视频的标题、时长、某一帧的截图（常用于视频的缩略图）等信息。

具体用法参考：[Android之使用MediaMetadataRetriever类获取视频第一帧](https://blog.csdn.net/u012561176/article/details/47858099)

--- 
**但是...**

凡事总不算那么一帆风顺的，如果你像我一样幸运的话，会在某些机型，某些API版本上成功获取缩略图，而在有些机型和低版本的API上报出下面的**错误**：
```
MediaMetadataRetrieverJNI: getFrameAtTime: videoFrame is a NULL pointer
```

查询了许久都木有解决啊，这个问题，够够的~
后来，在一个地方看到讨论说，这个问题可能是android本身的一个bug...好吧，可能我撞了南墙才会回头吧~

要想彻底解决可以换一个库，使用强大的**FFmpegMediaMetadataRetriever**
那就请follow我的脚步，来看看如何用吧...
## 2. 使用FFmpegMediaMetadataRetriever获取视频元数据（缩略图）

开源地址：[FFmpegMediaMetadataRetriever的github地址](https://github.com/wseemann/FFmpegMediaMetadataRetriever)
还有个备份的神马鬼地址：[FFmpegMediaMetadataRetriever的备份github地址](https://github.com/georgeyang1024/FFmpegMediaMetadataRetriever)

我就以第一个正式的开源地址为例好了。
### 2.1 集成方法
####  2.1.1 使用gradle配置依赖。
点开上面的开源地址，你会看到下面的页面。

![使用gradle配置依赖](https://img-blog.csdnimg.cn/20181213151436166.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=,size_16,color_FFFFFF,t_70)

这是github上说的配置方式。
经本人验证，第一句**Add the following maven dependency to your project's build.gradle file**是错的！！！
妹的，坑得我不轻...不是在project项目的build.gradle里添加compile那一句，而是在app的build.gradle的dependencies里加`compile 'com.github.wseemann:FFmpegMediaMetadataRetriever:1.0.14'`！！！

**这样就行了**，就集成好了FFmpegMediaMetadataRetriever。

下面那一块呢？就是

    Optionally, to support individual ABIs:
    
    dependencies {
        compile 'com.github.wseemann:FFmpegMediaMetadataRetriever-armeabi:1.0.14'
        compile 'com.github.wseemann:FFmpegMediaMetadataRetriever-armeabi-v7a:1.0.14'
        compile 'com.github.wseemann:FFmpegMediaMetadataRetriever-x86:1.0.14'
        compile 'com.github.wseemann:FFmpegMediaMetadataRetriever-mips:1.0.14'
        compile 'com.github.wseemann:FFmpegMediaMetadataRetriever-x86_64:1.0.14'
        compile 'com.github.wseemann:FFmpegMediaMetadataRetriever-arm64-v8a:1.0.14'
    }
这一块儿是可选项，适配不同物理架构的机型的，比如开发的应用只给x86的手机用那就添加x86那个依赖就行了。上面我们那个不带架构类型的应该是通通适配的干活。

**注意：这个方法的弊端只有一个，那就是最后的安装包会很大，会多出十多M吧哈哈哈，好用是要付出代价的。如果想小一点，就只视频某个机型好了**
#### 2.1.2 使用aar包

如果你的android studio下载不了依赖，上面的compile方式用不了，还可以下载它的aar包，类似java的jar包。

![aar包下载](https://img-blog.csdnimg.cn/20181213153127864.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=,size_16,color_FFFFFF,t_70)

还是上面的开源github地址，找到Prebuilt AARs点击下载，会得到一个压缩包，解压后如下图：
![aar包](https://img-blog.csdnimg.cn/20181213153311851.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=,size_16,color_FFFFFF,t_70)

看名字就知道和compile是对应的，第一个all-fmmr.aar就是匹配所有架构手机的，剩下的是匹配对应架构的。看aar包的大小也能知道光第一个aar包就近23M多了，出来的安装包能不大嘛。

啥？不会在android studio里使用aar？

好吧，送佛送到西，以集成all-fmmr.aar为例

- 首先，把all-fmmr.aar拷贝的工程的app/libs/目录下，如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181213153752812.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=,size_16,color_FFFFFF,t_70)
- 然后，在app的build.gradle文件的dependencies里添加下面一句：

```
compile(name:'all-fmmr',ext:'aar')
```
- 再然后，在app的build.gradle文件的android里添加下面代码：

```
 repositories {
        flatDir {
            dirs 'libs'   // aar目录
        }
    }
```

- 加好之后你的app的build.gradle文件应该是介样：

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 26
    defaultConfig {
        applicationId "com.example.fxjzzyo.helloworld"
        minSdkVersion 15
        targetSdkVersion 26
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
    sourceSets{
        main{
            jniLibs.srcDirs=['libs']
        }
    }
    repositories {
        flatDir {
            dirs 'libs'   // aar目录
        }
    }
}

dependencies {
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    implementation 'com.android.support:appcompat-v7:26.1.0'
    implementation 'com.android.support.constraint:constraint-layout:1.1.3'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
    androidTestCompile('com.android.support:support-annotations:26.1.0') {
        force = true;
    }
    compile(name:'all-fmmr',ext:'aar')
//    compile 'com.github.wseemann:FFmpegMediaMetadataRetriever:1.0.14'
}

```

这样，你就可以愉快地在代码里使用FFmpegMediaMetadataRetriever 啦！


### 2.2 代码里使用FFmpegMediaMetadataRetriever 
正如github上介绍的代码就是


```
//new出对象
FFmpegMediaMetadataRetriever mmr = new FFmpegMediaMetadataRetriever();

//设置数据源
mmr.setDataSource(mUri);

//获取媒体文件的专辑标题
mmr.extractMetadata(FFmpegMediaMetadataRetriever.METADATA_KEY_ALBUM);

//获取媒体文件的专辑艺术家
mmr.extractMetadata(FFmpegMediaMetadataRetriever.METADATA_KEY_ARTIST);

//获取2秒处的一帧图片（这里的2000000是微秒！！！）
Bitmap b = mmr.getFrameAtTime(2000000, FFmpegMediaMetadataRetriever.OPTION_CLOSEST); 

//释放资源
mmr.release();
```
其实这个和Android自带的MediaMetadataRetriever用法一样一样的，就是多了个l类名变成了FFmpegMediaMetadataRetriever，其余都一样。

## 3. 完整代码
听说不贴完整代码的不厚道，我那么厚道当然贴一贴啦。惊喜吧~
### 3.1 布局文件activity_video_info.xml
 

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:onClick="getVideoInfo"
        android:text="获取信息" />

    <ImageView
        android:id="@+id/iv_thumbnail"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:layout_below="@id/iv_thumbnail"
        android:layout_centerHorizontal="true" />

</RelativeLayout>
```
### 3.2 java代码VideoInfoActivity.java
```

public class VideoInfoActivity extends AppCompatActivity {

    private ImageView imageView;

    //这个网络视频url可能失效，很可能！所以你要自己找一个可以浏览器种打开播放的url
    String url = "http://219.238.7.67/mp4files/418000000CE88BD1/202.108.250.226/youku/657259485123b717b789144ef/03000801005C009A1DA6539003E880F6C38754-67FB-4855-9645-7E00B1822316.mp4";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_video_info);
        //android 6.0权限申请
        checkPermission();

    }
    public void checkPermission(){
        //检查权限（NEED_PERMISSION）是否被授权 PackageManager.PERMISSION_GRANTED表示同意授权
        if (ActivityCompat.checkSelfPermission(this, android.Manifest.permission.WRITE_EXTERNAL_STORAGE)
                != PackageManager.PERMISSION_GRANTED) {
            //用户已经拒绝过一次，再次弹出权限申请对话框需要给用户一个解释
            if (ActivityCompat.shouldShowRequestPermissionRationale(this, android.Manifest.permission
                    .WRITE_EXTERNAL_STORAGE)) {
                Toast.makeText(this, "请开通相关权限，否则无法正常使用本应用！", Toast.LENGTH_SHORT).show();
            }
            //申请权限
            ActivityCompat.requestPermissions(this, new String[]{android.Manifest.permission.WRITE_EXTERNAL_STORAGE}, 0);

        } else {
            Toast.makeText(this, "授权成功！", Toast.LENGTH_SHORT).show();
            //初始化
            init();
        }
    }
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        if(requestCode==0){
            if(grantResults.length>0&&grantResults[0]==PackageManager.PERMISSION_GRANTED){
                //申请成功
                init();
            }else {
                Toast.makeText(this,"相机权限申请被拒绝！",Toast.LENGTH_SHORT).show();
            }
            return;
        }
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    }

    private void init() {
        imageView = findViewById(R.id.iv_thumbnail);
    }

  /**
     * 点击按钮，获取视频缩略图
     * @param view
     */
    public void getVideoInfo(View view) {
        Bitmap videoThumbnail = null;
       
        //获取本地视频缩略图，在sdk根目录下准备一个test.mp4的文件
//        String videoPath = Environment.getExternalStorageDirectory().getAbsolutePath() + "/test.mp4";
//         videoThumbnail = getVideoThumbnail(videoPath);
        
        //获取网络视频缩略图
        videoThumbnail = getNetVideoThumbnail(url);
        
        imageView.setImageBitmap(videoThumbnail);

    }

    /**
     * 获取本地视频缩略图
     * @param filePath
     * @return
     */
    public Bitmap getVideoThumbnail(String filePath) {
        Bitmap b=null;
        //使用MediaMetadataRetriever
//        MediaMetadataRetriever retriever = new MediaMetadataRetriever();
        
        //FFmpegMediaMetadataRetriever
        FFmpegMediaMetadataRetriever retriever = new FFmpegMediaMetadataRetriever();
        File file = new File(filePath);
        try {

            retriever.setDataSource(file.getPath());
            b=retriever.getFrameAtTime();
        } catch (IllegalArgumentException e) {
            e.printStackTrace();
        } catch (RuntimeException e) {
            e.printStackTrace();

        } finally {
            try {
                retriever.release();
            } catch (RuntimeException e) {
                e.printStackTrace();
            }
        }
        return b;
    }


    /**
     * 获取网络视频缩略图
     * @param url
     * @return
     */
    public Bitmap getNetVideoThumbnail(String url) {
        Bitmap b=null;
        //使用MediaMetadataRetriever
//        MediaMetadataRetriever retriever = new MediaMetadataRetriever();

        //FFmpegMediaMetadataRetriever
        FFmpegMediaMetadataRetriever retriever = new FFmpegMediaMetadataRetriever();

        try {
            retriever.setDataSource(url,new HashMap<String, String>());
            b=retriever.getFrameAtTime(400*1000,FFmpegMediaMetadataRetriever.OPTION_CLOSEST);
        } catch (IllegalArgumentException e) {
            e.printStackTrace();
        } catch (RuntimeException e) {
            e.printStackTrace();

        } finally {
            try {
                retriever.release();
            } catch (RuntimeException e) {
                e.printStackTrace();
            }
        }
        return b;
    }

}
```
## 4. 运行效果
一图胜千言，木有运行效果的是不完整的。
![运行效果](https://img-blog.csdnimg.cn/2018121315551069.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4anp6eW8=,size_16,color_FFFFFF,t_70)
完结，撒花~

>参考文章：
>https://blog.csdn.net/u012561176/article/details/47858099
>https://blog.csdn.net/u010499721/article/details/50338623
