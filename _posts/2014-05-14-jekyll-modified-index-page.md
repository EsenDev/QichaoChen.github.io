---
layout: post
keywords: blog
description: blog
title: "jekyll 如何修改默认的index.html"
categories: [jekyll]
tags: [jekyll]
---
{% include codepiano/setup %}

今天更换了github.io的欢迎主页，遇到一个问题，如果我正常使用默认的index.html那么在生成页面的时候可以在主页加载最新的五篇博文，但是如果把index.html换成欢迎界面，原理的主页改成blog/index.html就会
出现这样一个问题：那就是无法在博客主页加载博文，但是其他模块都很正常！WTF！！！非常纳闷，而且找不出问题原因，最后在请教了这个主题的作者[codepiano](http://weibo.com/anyexingchen)
之后给出一个stackoverflow上的链接，改变了解决问题的方向，主要还是没理解jekyll的框架接口,后来在[jekyllcn.com](http://jekyllcn.com/docs/pagination/)的在线文档中找到了解决方案
需要开启分页功能开启分页功能很简单，只需要在 _config.yml里边加一行，并填写每页需要几行：

{% highlight ruby %}
paginate: 5
{% endhighlight %}

下边是对需要带有分页页面的配置：

{% highlight ruby %}
paginate_path: "blog/page:num"
{% endhighlight %}

就这样解决了问题，虽然最后的解决方案很容易，但是还是折腾好一阵子，最后附上最新的github主页内容

<img src="/image/jekyll/jekyll.png"/>

