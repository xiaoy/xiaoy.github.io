---
layout:     post
title:      "Unity Android Activity 控制"
subtitle:   "玩转Unity Android 启动Activity" 
date:   2017-12-01
author: "武龙飞"
tags:
    - Unity3d
    - 2017
---
# 前言
开发游戏，在国内发行，接入各个渠道SDK是一件绕不开的事情。并且这件事非常复杂琐碎，原因如下：

a、发行平台多，Android平台有30，40家主流发行平台

b、每家SDK的接入API五花八门

c、发布管理这么多的APP耗时耗力

d、各家的SDK经常更新，接口不兼容的事常有发生

e、服务器回调的接口也是五颜六色

最近要接入一家友商的SDK，接口如下：
```java
// activity 必须继承AppCompatActivity
public void init(Activity activity) {

}
```
但Unity的Activity接口已经定义好了
```java
public class UnityPlayerActivity extends Activity {

}
```
# 解决方案
首先搞清楚Android的运行原理，Android的APP启动入口通过在AndroidMinifest.xml里设置。Unity生成的Gradle工程XML里设置如下：
```xml

<activity android:name="com.unity3d.player.UnityPlayerActivity"
            android:label="@string/app_name">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    <meta-data android:name="unityplayer.UnityActivity" android:value="true" />
</activity>

```
通过配置Activity的属性，可以设置启动Activity，解决方案就是再添加个Activity继承AppCompatActivity，传递给SDK。代码如下：
```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Intent intent = new Intent(this, UnityPlayerActivity.class);
        startActivity(intent);
    }
}
```
配置启动Activity为MainActivity即可，虽然说这样做多此一举，但是有这样的需求，不得不应对。

# 后续
对于不了解Android运行机制的人来处理这样的事情，真的是算挺难的。我刚接到这个事情后，陷入了迷茫，因为我不知道通过何种方式让UnityPlayerActivity继承AppCompatActivity。绕过这个弯还真是不容易。

同时对于想在自己的应用里嵌入Unity Activity的也非常有启发意义，通过这样的方式，再加上Unity与Android通信方式就可以任意交互。