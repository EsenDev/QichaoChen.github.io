---
layout: post
keywords: blog
description: blog
title: "Android不停旋转图片卡顿问题解决"
categories: [android]
tags: [android]
---
{% include codepiano/setup %}

在使用Android rotate animation来实现图片不停旋转的问题时（比如CD光盘旋转），如果设置不恰当那么在旋转一周会出现卡顿现象，下面给出解决方案
在res下创建anim文件夹，并创建例如rotation.xml代码如下：

{% highlight ruby %}
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/linear_interpolator">
    <rotate
        android:fromDegrees="0"
        android:toDegrees="359"
        android:duration="5000"
        android:repeatCount="-1"
        android:pivotX="50%"
        android:pivotY="50%" />
</set>
{% endhighlight %}

上面有几个参数要说明的，首先是
{% highlight ruby %}
    android:fromDegrees="0"
    android:toDegrees="359"
{% endhighlight %}

为什么是从0到359而不是到360？因为360其实等于0。第二个参数是

{% highlight ruby %}
    android:interpolator="@android:anim/linear_interpolator"
{% endhighlight %}

这个参数是让旋转匀速运动，当然还有加速和减速，大家可以试试！其他参数通过英文名字应该都能看懂，这里就不多说了，这样配置后在Java代码中设置
旋转就可以让控件按照xml文件配置的参数旋转起来！

{% highlight ruby %}
    Animation animation;
    animation = AnimationUtils.loadAnimation(this,R.anim.rotation); //this为context上下文
    imageView.startAnimation(animation); //让ImageView控件旋转起来！
{% endhighlight %}