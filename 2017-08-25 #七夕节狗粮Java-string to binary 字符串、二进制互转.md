
【七夕节狗粮】Java-string to binary 字符串、二进制互转
====

七夕快到了，程序猿或者程序媛每天都忙着写bug改bug，有没有什么小小的方法呢？
下面将"I Love U"用二进制表示，是不是也很有意思呢？

代码如下

```
package cong.abcplus.bi.hiveAntrl.onedata;

/**
 * Created by gz on 2017/8/25.
 */
public class ForTest {

    //将二进制字符串转换成int数组
    public static int[] binStrToIntArray(String binStr) {
        char[] temp = binStr.toCharArray();
        int[] result = new int[temp.length];
        for(int i = 0; i<temp.length; i++) {
            result[i] = temp[i]-48;
        }
        return result;
    }

    /**
     * 将二进制转换成字符
     * <p>“x<<y"是位运算符当中的"左移"运算，其中x是左移的数，y是左移的位。如：
     2<<2的运算过程：
     2转化为二进制是
     0000 0010
     那么左移2位得到
     0000 1000即得到结果为8
     总结：左移一位相当乘以2，左移n位相当于乘以2的n次方。

     右移<<是：
     右移一位相当于整除2。

     上面这两个是不带符号的移位运算。
     还有一个：>>>这个是带符号的右移
     </p>
     * @param binStr
     * @return
     */
    public static char binStrToChar(String binStr){
        int[] temp = binStrToIntArray(binStr);
        int sum = 0;
        for(int i = 0; i<temp.length; i++){
            // 移位运算符
            // 正负数都用补码作运算,>>和<<低位用0来填充,高位用符号位填充,若>>>(无符号右移)都0来填充
            sum += temp[temp.length-1-i]<<i;
        }
        return (char)sum;
    }

    public static String binStrToStr(String binStr){
        String[] tempStr = binStr.split(" ");
        char[] tempChar = new char[tempStr.length];
        for(int i = 0; i<tempStr.length; i++) {
            tempChar[i] = binStrToChar(tempStr[i]);
        }
        return String.valueOf(tempChar);
    }

    public static String strToBinary(String str){
        char[] strChar = str.toCharArray();
        String result = "";
        for(int i = 0; i < strChar.length; i++){
            result += Integer.toBinaryString(strChar[i])+ " ";
        }
        return result;
    }

    public static void main(String[] args) {
        String love = "I Love U";

        String bina = strToBinary(love);

        System.out.println(bina);

        System.out.println(binStrToStr(bina));
    }
}
```