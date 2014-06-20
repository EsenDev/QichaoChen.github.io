---
layout: post
keywords: blog
description: blog
title: RSA公钥算法中的模算数求幂运算"
categories: [math]
tags: [math]
---
{% include codepiano/setup %}

相信了解过RSA公钥加密解密算法的同学一定会佩服Ron Rivest、Adi Shamir、Leonard Adleman三个牛人的智慧（据说Shamir某年某月还来过我们学校，题外话）
RSA算法的详细内容就不说了，下面先列举RSA的基本元素：
第一种：通过StrictMode来忽视新版本限制（当然这种方法简单粗暴，不是很推荐！）

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
    c <- 0;
    f <- 1;
    for i <-k downto 0
        do c <- 2 * c
           f <- (f * f) mod n
        if bi = 1
            c <- c+ 1
            f <- (f * a) mod
    return f;
{% endhighlight %}

经过简单的数学处理，便可以轻易计算巨大的幂运算！神奇！！！看不懂的同学在纸上画一画，写一写就能明白启动的道理了，下面给出整个RSA整数的加密和解密算法实现Java代码

{% highlight ruby %}

/**
 * Created with IntelliJ IDEA.
 * Time: 20:04
 * Info:
 */
public class RSA {

    private int p, q; //p,q均为素数
    private int n; //pq乘积
    private int e;//公钥
    private int d;//私钥
    private int M;//明文
    private int C;//密文
    private int phi_n;//欧拉数

    public RSA() {

    }

    public RSA(int p, int q, int e) {
        this.p = p;
        this.q = q;
        this.e = e;
    }

    public int RSAEncryption(int m) {
        M = m;
        double c = 0;//密文
        if (isPrime(p) && isPrime(q)) {
            n = p * q;
            phi_n = (p - 1) * (q - 1);
            if (e > 1 && e < phi_n && gcd(e,phi_n) == 1){
                //密钥e满足: 1 < e < phi_n && gcd(e,phi_n) = 1
                for(int i = 1;i < phi_n;i++){
                    //e的乘法逆元存在且唯一
                    if((i * e) % phi_n == 1){
                        d = i;
                    }
                }
                if (M < n){
                    //明文须小于n
                    //加密
                    //由于设计高幂运算精度原因c不能定义为整型，而应该是double
                    c = (Math.pow(M,e)) % n;
                    C = (int)c;
                }

        return (int)c;
    }

    public int RSADecryption(int c){ //RSA解密
        return (int)((Math.pow(c,d)) % n);
    }

    private boolean isPrime(int number) {判断一个数是否是素数
        if (number < 2) {
            return false;
        }
        if (number == 2) {
            return true;
        }

        for (int i = 3; i <= Math.sqrt(number); i++) {
            if (number % i == 0) {
                return false;
            }
        }
        return true;
    }

    private int gcd(int a, int b) {//递归求最大公约数
        int A, B;
        if (a < b) {
            int t = a;
            a = b;
            b = t;
        }
        A = a;
        B = b;
        if (B == 0) {
            return A;
        } else {
            return gcd(B, A % B);
        }
    }

    public int getD() {
        return d;
    }

    public int getE() {
        return e;
    }

    public int getN() {
        return n;
    }

    public long mosuanshu(int a,int b,int n){
        //模算数的求幂运算：原理 (a mod n) * (b mod n) = (a * b) mod n;
        // a^b mod = E(a^ci) mod n,其中E为连积，ci = bi * 2^i,b = E(ci)
        //转化为二进制数
        String binaryStr = Integer.toBinaryString(b);
        List<Integer> bi_list = new ArrayList<Integer>();
        for (int i = binaryStr.length()-1; i >= 0;i--){
            int t = Integer.parseInt(String.valueOf(binaryStr.charAt(i)));
            bi_list.add(t);
        }
        int c = 0;
        long result = 1;
        for (int j = bi_list.size() - 1;j>=0;j--){
            c = 2 * c;
            result = (result * result) % n;
            if (bi_list.get(j) == 1){
                c = c + 1;
                result = (result * a) % n;
                System.out.println("\n"+result);
            }
        }
        return result;
    }

    public static void main(String[] args) {
        int p = 17;
        int q = 11;
        int e = 7;
        int M = 88;
        RSA rsa = new RSA(p,q,e);
        int c = rsa.RSAEncryption(M);
        System.out.printf("公钥PU = {%d,%d}",rsa.getE(),rsa.getN());
        System.out.println("明文M="+M+",经过RSA加密后得到密文C为:"+c);
        int m = rsa.RSADecryption(c);
        System.out.println("密文C="+c+",经过RSA解密后得到明文M为:"+m);
        long f = rsa.mosuanshu(11,23,187);
        System.out.println(f);
    }
}

{% endhighlight %}