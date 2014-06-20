import java.util.ArrayList;
import java.util.List;

/**
 * Created with IntelliJ IDEA.
 * Time: 20:04
 * Info:
 */
public class RSA {

    private int p, q;//p,q均为素数
    private int n;
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
                } else {

                }

            } else {

            }
        } else {

        }
        return (int)c;
    }

    public int RSADecryption(int c){
        return (int)((Math.pow(c,d)) % n);
    }

    private boolean isPrime(int number) {
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

    private int gcd(int a, int b) {
        //递归求最大公约数
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
