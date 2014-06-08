---
layout: post
keywords: blog
description: blog
title: "Android判断能否正确访问服务器"
categories: [android]
tags: [android]
---
{% include codepiano/setup %}

项目需要在app已启动的Splash画面中判断能否正访问上服务器，当然立马会想到通过HTTP请求判断返回状态码。但是在Android实现起来不是那么容易。
首先在在Android 2.3之后,不能在主线程中执行网络请求任务。综合网上资料给出两种种解决方案：

第一种：通过StrictMode来忽视新版本限制（当然这种方法简单粗暴，不是很推荐！）

{% highlight ruby %}
f (android.os.Build.VERSION.SDK_INT > 9) {
    StrictMode.ThreadPolicy policy = new StrictMode.ThreadPolicy.Builder().permitAll().build();
    StrictMode.setThreadPolicy(policy);
}
{% endhighlight %}

第二种：新建一个线程在线程或者也可以创建一个AsyncTask执行相应的网络请求，下面给出创建线程例子

{% highlight ruby %}

new Thread(new Runnable(){
    @Override
    public void run() {
      URL url = new URL("http://www.baidu.com")
      HttpURLConnection httpURLConnection = (HttpURLConnection) url.openConnection();
      httpURLConnection.setConnectTimeout(3 * 1000);// 设置连接超时的时间
      int response_code = httpURLConnection.getResponseCode();
      if (response_code == 200) {
         //连接成功
      }
    }
}).start();

{% endhighlight %}

当然上面的版本在项目中还是有点问题，主要是子线程导致无法简单预知在什么时候执行结束，最后我自己的方案给出判断是否能正确连接到某个服务器(如，http://www.baidu.com)

{% highlight ruby %}
public void isNetworkAvailable(final Handler handler, final int timeout) {
        new Thread() {
            private boolean responded = false;
            @Override
            public void run() {
                new Thread() {
                    @Override
                    public void run() {
                        HttpGet requestForTest = new HttpGet("http://www.baidu.com");
                        try {
                            new DefaultHttpClient().execute(requestForTest);
                            responded = true;
                        } catch (Exception e) {
                        }
                    }
                }.start();
                try {
                    int waited = 0;
                    while (!responded && (waited < timeout)) {
                        sleep(100);
                        if (!responded) {
                            waited += 100;
                        }
                    }
                } catch (InterruptedException e) {
                } // do nothing
                finally {
                    if (!responded) {
                        handler.sendEmptyMessage(0);
                    } else {
                        handler.sendEmptyMessage(1);
                    }
                }
            }
        }.start();
    }
{% endhighlight %}

然后再Handler中进行相应逻辑判断

{% highlight ruby %}
private Handler h = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            if (msg.what != 1) {
                // 无法连接服务器
            } else {
                // 正确连接服务器
        }
    };
{% endhighlight %}

