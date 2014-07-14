---
layout: post
keywords: blog
description: blog
title: RSA公钥算法中的模算数求幂运算
categories: [math]
tags: [math]
---
{% include codepiano/setup %}

相信了解过RSA公钥加密解密算法的同学一定会佩服Ron Rivest、Adi Shamir、Leonard Adleman三个牛人的智慧（据说Shamir某年某月还来过我们学校，题外话）
RSA算法的详细内容就不说了，下面先列举RSA的基本元素：

{% highlight ruby %}

    1.选择 p,q(其中p、q均为素数)
    2.计算 n = p * q
    3.计算 phi_n = (p - 1) * (q - 1) //phi_n为欧拉数，即小于n且与n互素的整数的个数
    4.选择 e  //其中 1<e<phi_n,且e和philosophy_n的最大公约数为1：gcd(e,phi_n) = 1
    5.计算 d  //其中( d * e ) mod n = 1; d称为e的乘法逆元，d一定存在且唯一
    6.公钥 Pub_Key = {e,n}
    7.私钥 Pri_Key = {d,n}

    加密过程：C = M ^ e mod n
    解密过程：M = C ^ d mod n

{% endhighlight %}

以上就是RSA的基本过程，可以看见在加解密过程中涉及到幂运算，而且往往数都比较大，比如11的23次方，这种连Google都只能给出一个浮点数，无法给出
确切的整数，更别说准确求模运算的结果。那么数学家们是如何解决这个问题的呢？下面结合资料给出大数模运算求幂的过程和代码。

算法涉及原理如下，都是数学基础知识：
{% highlight ruby %}

    1.((a mod n) * (b mod n)) mod n = (a * b) mod n;

    2.a ^ b = a ^ (c1 + c2 + ... + cn) = a ^ c1 * a ^ c2 * ... a ^c3;

    3.b = b1 * 2 ^ 1 + b2 * 2 ^ 2+ ... + bk * 2 ^ k;
{% endhighlight %}

综合上面三个原理给力给出程序伪代码如下：

{% highlight ruby %}
    c =  0;
    f =  1;
    for i = k downto 0
        do c =  2 * c
           f =  (f * f) mod n
        if bi = 1
            c =  c+ 1
            f =  (f * a) mod
    return f;
{% endhighlight %}

经过简单的数学处理，便可以轻易计算巨大的幂运算！神奇！！！看不懂的同学在纸上画一画，写一写就能明白启动的道理了，下面给出整个RSA整数的加密和解密算法
实现[Java代码](http://qichaochen.github.io/apk/rsa.md)
