# <center>StartGameSDK 集成文档(渠道)</center>

## 1.接入说明

- #### SDK支持最低版本为API 21(Android 5.0)
- #### 建议添加动态权限申请
- #### 若有重复依赖，建议使用在App 主Module 的build.gradle文件中使用exclude命令去除
## 2.名词定义

名词|说明
:------------ | :-------------
APP|接入平台的应用     
APP_ID|平台分配的应用ID
Game_ID|小游戏ID
SDK-KEY|广告平台分配的应用广告调用Key
Scene_ID|场景ID

## 3.SDK说明与权限

#### 游戏中心相关库

文件名称|说明|Size
:------------ | :------------- | :-------------
sg-core.aar|核心库|1.4M     
sg-mgc.aar|游戏中心库|399K
sg-ad-XXX.aar|广告相关第三方平台Adapter|不定

#### Gradle依赖库
```Gradle
    implementation "com.android.support:design:27.1.1"
    implementation("com.android.support:appcompat-v7:27.1.1", {
        exclude group: 'com.android.support', module: 'design'
    })
    implementation("com.android.support:recyclerview-v7:27.1.1",   
    {
        exclude group: 'com.android.support', module: 'design'
    })
    implementation 'com.github.bumptech.glide:glide:4.3.1'
    implementation 'com.squareup.okhttp3:okhttp:3.14.0'
    implementation 'com.squareup.okio:okio:2.2.2'
    implementation 'com.google.code.gson:gson:2.8.5'
    implementation 'org.greenrobot:eventbus:3.1.1'
```

#### Gradle配置
```Gradle
    增加jdk1.8或者更高版本以启用lambda表达式
    compileOptions {
        sourceCompatibility 1.8
        targetCompatibility 1.8
    }
```
#### 需要权限

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

<!-- 游戏包及游戏数据本地存储 -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

<!-- 小游戏运行所需权限，给用户震动反馈 -->
<uses-permission android:name="android.permission.VIBRATE" />

<!-- 获取设备信息，适配游戏类型及优化CDN下载 -->
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>

<!-- 小游戏生成快捷方式权限，非重要权限 -->
<uses-permission android:name="com.android.launcher.permission.INSTALL_SHORTCUT" />
<uses-permission android:name="com.android.launcher2.permission.INSTALL_SHORTCUT" />
<uses-permission android:name="com.android.launcher3.permission.INSTALL_SHORTCUT" />
```

#### APPID配置

manifest中Application节点添加
```xml
    <meta-data
        android:name="MGC_APPID"
        android:value="你的APPID" />
```


#### 聚合广告平台接入
目前支持admob、ironsource、mopub、facebook广告平台接入，具体接入可自由选择。接入方式为提供对应平台 Adapter aar。

#### 注意事项


## 3.SDK集成

#### 初始化
在自定义Application的onCreate方法中进行游戏中心的初始化。

```Java
    @Override
    public void onCreate() {
        super.onCreate();
        StartGame.getInstance().init(getApplicationContext());     
    }
```
#### 打开游戏中心
 参数说明

名称|说明
:------------ | :-------------
activity|Activity    
sceneId|String类型 场景ID  


```Java
    StartGame.getInstance().startGameCenter(activity,sceneId);
```

#### 返回游戏中心fragment方式接入
 参数说明

名称|说明
:------------ | :-------------
sceneId|String类型 场景ID   


 返回参数说明

返回名称|说明
:------------ | :-------------
Fragment|游戏中心fragment


```Java
    StartGame.getInstance().getGameCenterFragment(sceneId)
```

## 4.混淆规则

```produrd

# --------------- sg -----------------
-keep class com.startgame.StartGame{
    public *;
}
-keepclasseswithmembers class * {
    public <init>(android.content.Context);
}
-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet);
}
-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet, int);
}
-keep class com.startgame.single.**{
    public *;
}
-dontwarn com.ledong.lib.**
-keep class com.ledong.lib.** {*;}
-dontwarn com.leto.game.**
-keep class com.leto.game.** {*;}
-dontwarn com.kymjs.rxvolley.**
-keep class com.kymjs.rxvolley.** {*;}

# ----------------- eventbus -----------------
-keepattributes *Annotation*
-keepclassmembers class ** {
    @org.greenrobot.eventbus.Subscribe <methods>;
}
-keep enum org.greenrobot.eventbus.ThreadMode { *; }

# ----------------- ironsource -----------------
-dontwarn com.leto.game.ad.ironsource.**
-keep class com.leto.game.ad.ironsource.** {*;}
-keepclassmembers class com.ironsource.sdk.controller.IronSourceWebView$JSInterface {
    public *;
}
-keepclassmembers class * implements android.os.Parcelable {
    public static final android.os.Parcelable$Creator *;
}
-keep public class com.google.android.gms.ads.** {
   public *;
}
-keep class com.ironsource.adapters.** { *;
}
-keep class com.ironsource.mediationsdk.** { *;
}
-dontwarn com.ironsource.mediationsdk.**
-dontwarn com.ironsource.adapters.**
-dontwarn com.moat.**
-keep class com.moat.** { public protected private *; }

# ----------------- okhttp -----------------
-dontwarn okio.**
-dontwarn com.kymjs.rxvolley.**
-keep class okio.** {*;}
-keep class com.kymjs.rxvolley.** {*;}

# ----------------- webview -----------------
-keep public class android.net.http.SslError
-keep public class android.webkit.WebViewClient
-dontwarn android.webkit.WebView
-dontwarn android.net.http.SslError
-dontwarn android.webkit.WebViewClient

```
