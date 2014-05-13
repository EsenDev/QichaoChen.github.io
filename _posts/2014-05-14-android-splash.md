---
layout: post
keywords: blog
description: blog
title: "Android启动画面执行finish()导致另一个Activity无法被Kill问题"
categories: [android]
tags: [activity]
---
{% include codepiano/setup %}

今天遇到这样比较不常遇到一个问题，就是在我的Android应用程序中有一个启动画面SplashActivity，功能是在执行显示欢迎界面一定时间（LOADINGTIME决定）
启动主界面MainActivity代码如下：

{% highlight ruby %}
private void StartMainActivity() {
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                Intent intent = new Intent(SplashActivity.this, MainActivity.class);
                startActivity(intent);
                SplashActivity.this.finish();
            }
        }, LOADING_TIME);
    }
{% endhighlight %}

功能和代码都简单，但是问题来了，在启动MainActivity之后，如果在MainActivity里面执行finish()函数来退出应用程序，由于先前的SplashActivity已经finish了所以理论上，该应用程序会直接被Kill掉，然后退出，结果呢？神奇的事情发生了，MainActivity似乎有如神助，自己用重生了，然后再次掉onCreate()函数继续执行起来，尼玛啊
！你这不是逗我么！这样就导致我无法直接退出，然后又要执行一大堆我不想执行代码！Orz！为了解决这个问题，再次G了一圈，什么”finish oncreate twice“、”activity called twice“
等关键词瞬间在Google上展开，结果这个问题搞了半天还是没解决，各种方案都尝试了，可见这个问题不是很常见，最后竟然在stackoverflow上找到一行代码就解决了，WTF！
废话不多说，直接上代码

{% highlight ruby %}
//intent在启动之前要设置一下Activity的标志，具体查看Activity任务栈问题
intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
{% endhighlight %}

就这样问题轻松解决了，总结一句话："纸上谈兵是没有用，要真刀真枪搞起！"

顺便Mark一下，如何在应用程序中“按两次返回键退出程序，并拦截Back键”

{% highlight ruby %}
private int mBackKeyPressedCount = 1;
    @Override
    public void onBackPressed() {
        if (mBackKeyPressedCount == 2){
            mRequstQueue.cancelAll(this);
            if (player != null) {
                player.stop();
                player = null;
            }
            finish();
        } else {
            mBackKeyPressedCount++;
            Toast.makeText(this,"再按一次退出程序", Toast.LENGTH_SHORT).show();
        }
    }
{% endhighlight %}

参考：

* [finish() activity twice in android?](http://stackoverflow.com/questions/10808223/finish-activity-twice-in-android)

* [Tasks and Back Stack](http://developer.android.com/guide/components/tasks-and-back-stack.html)

