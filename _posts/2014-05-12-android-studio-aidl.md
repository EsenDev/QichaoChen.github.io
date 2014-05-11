---
layout: post
keywords: blog
description: blog
title: "Android Studio下如何配置AIDL文件"
categories: [android]
tags: [aidl]
---
{% include codepiano/setup %}
今天在准备录制《马上学Android》系列视频教程第三十七集的时候遇到了一个问题：如何在Android Studio上创建AIDL文件？在Google团队的博客瞄到了解决方案，
结合stackoverflow来说明一下。在Eclipse上只要在源文件目录下，建立一个XXX.aidl，那么如果aidl文件如果格式正确的话ADT会自动生成一个XXX.java文件，
一般不需要关心这个文件的内容，一般也不用维护。相比基于Eclipse的ADT开发工具来说，AIDL文件在Android Studio上添加就不那么顺利了。
那么在Android Studio下如何创建aidl文件呢？下面细细道来

* 首先你需要在工程module目录下的src中新建一个aidl文件夹，效果截图如下

<img src="/image/aidl/aidl1.jpeg">

* 但是如果你直接在上述文件新建一个aidl文件，

{% highlight ruby %}
interface IMyService{
    String getValue();
}
{% endhighlight %}

* 那么编译不会通过的（有点略微蛋疼啊！！！）Gradle错误提示如下：

<img src="/image/aidl/aidl3.jpeg" />

* 那么如何解决问题呢？答案是先查看你的AndroidManifest.xml中的Package名字，例如：

<img src="/image/aidl/aidl4.jpeg" />

* 接下来在新建aidl文件夹下添加和AndroidManifest中相同的包名，并修改aidl文件添加相同的包名：

<img src="/image/aidl/aidl5.jpeg" />

* 重新编译就可以顺利通过了，在build/source/aidl/debug/下生成了IMyService.java，内容较多有兴趣的可以研究一下！

<img src="/image/aidl/aidl6.jpeg" />

* Gradle还是很灰常强大的啊，有时间好好研究一下！

参考：

* [http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Project-Structure](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Project-Structure)
* [http://stackoverflow.com/questions/17836234/how-can-i-add-the-aidl-file-to-android-studio-from-the-in-app-billing-example](http://stackoverflow.com/questions/17836234/how-can-i-add-the-aidl-file-to-android-studio-from-the-in-app-billing-example)
