---
layout:     post
title:      "Unity Android 加载动态库"
subtitle:   "Android 加载动态库原理" 
date:   2017-12-01
author: "wulongfei"
tags:
    - Unity3d
    - 2017
---
# 前言
在接入360 Android SDK时遇到在有些机型的Android机器上报错，具体错误提示为：
>Failure to initialize! Your hardware does not support this application

Unity生成的原生包可以正常运行，那问题很明显出在360 SDK身上。遇到问题时，我的习惯首先是去想自己哪里有问题，排除掉自身原因，在找其他问题。翻看了360 SDK接入文档，怀疑是签名不对，因为360 SDK只支持V1签名，我编译Android project 使用的是Gradle 2.2版本。检查build.gradle文件内容如下：
``` gradle
  android {
    ...
    defaultConfig { ... }
    signingConfigs {
      release {
        storeFile file("myreleasekey.keystore")
        storePassword "password"
        keyAlias "MyReleaseKey"
        keyPassword "password"
        v2SigningEnabled false
      }
    }
  }
```
是按照[官方设置](https://developer.android.com/about/versions/nougat/android-7.0.html)做的，那这里就没啥问题。排除完自己的问题，就去问360SDK的同学了。

# 沟通问题
在我连续加了十几次对方包打听的QQ后，终于接受了我的请求。在这里不得不感叹以前公司和360有合作关系时，都是运营同学拉个群，任何问题都是及时解答。现在只能通过这种无限骚扰的方式才能沟通。沟通过后，让我自己查明我所用的引擎支持哪些动态库，不用的就删除。真是无语啊，你提供的动态库，文档里也不说明一下，而让开发者自己去研究你提供的库，是否适配游戏。

后面又找之前的同事，专业接入SDK的同学问了下是否遇到此类问题，说没遇到过这个问题。提供了思路，让我挨个添加，使用二分法定位那个库不支持。跑去提供的文件看了下目录结构，大概明白了啥情况。目录结构如下：

1. arm64-v8a
    - liblegend.so
    - libonlywechat_plugin.so
    - libqhsdk.so
2. armeabi
    - liblegend.so
    - libonlywechat_plugin.so
    - libqhsdk.so
3. armeabi-v7a
    - liblegend.so
    - libonlywechat_plugin.so
    - libqhsdk.so
4. mips
    - liblegend.so
    - libonlywechat_plugin.so
    - libqhsdk.so
5. mips64
    - liblegend.so
    - libonlywechat_plugin.so
    - libqhsdk.so
6. x86
    - liblegend.so
    - libonlywechat_plugin.so
    - libqhsdk.so
7. x86_64
    - liblegend.so
    - libonlywechat_plugin.so
    - libqhsdk.so

看了一下[Unity官方文档](https://unity3d.com/cn/unity/system-requirements)，说明如下：
>Android: OS 4.1 or later; ARMv7 CPU with NEON support or Atom CPU

Unity设置里: File->BuildSetting->Android->PlayerSetting->Device Filter，这里只有Armv7,x86的选项。删除掉多余的so文件，问题解决。

# 动态库和静态库
动态库和静态库都是程序代码编译后导出不同类型的库文件。

静态库特性如下：
1. 在程序链接时，静态库将被写入可执行文件
2. 程序在启动时，静态库将被加载到代码区
3. 每个进程的静态库是独立的

动态库特征如下：
1. 程序在链接时，只写入动态库的路径
2. 程序在需要时加载动态库
3. 所有进程共享一份动态库

首先搞清楚动态库，也就是so文件后。我就更加不明白了，既然so文件是自己需要时加载，为啥360SDK自带的so文件会导致Unity报错？

# ABI简介
不同的CPU对应不同的指令集，为了兼容不同的CPU，在生成动态文件时，可以指定不同的CPU生成对应的文件。文件对应的类型就是API(Application binary interface)。具体文档参考[这里](https://developer.android.com/ndk/guides/abis.html)。

为不同类型CPU设备生成对应的动态文件，如果不指定生成对应CPU架构的APP，所有动态文件将包含在APP中。

程序运行时如何找到对应的动态库？
1. 默认路径加载
2. 指定的路径加载

Android APP在安装时会生成如下目录：
```java
/data/data/<package_name>/lib/lib<name>.so
list 我们应用，目录如下：
libil2cpp.so
liblegend.so
libmain.so
libonlywechat_plugin.so
libqhsdk.so
libunity.so
libxlua.so
```
程序在安装时，安装程序将扫描:
```java
1. lib/<primary-abi>/lib<name>.so   // 设备主ABI，设备对应cpu支持的ABI
2. lib/<secondary-abi>/lib<name>.so // 设备辅助ABI，如果主ABI没有，使用辅助ABI
```

# 刨根问底
测试使用的一加5手机，小米5，都有问题，骁龙825和骁龙820肯定是支持arm64-v8a。如果是360 SDK自己使用这些库那肯定没有问题。为啥Unity会报错？只能说Unity在启动时加载了所有放在lib目录下的库。由于so文件带有cpu使用信息，Unity只支持armabi-v7a，x86。这样整个问题算是清楚了。


