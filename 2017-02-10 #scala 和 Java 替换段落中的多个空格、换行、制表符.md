# scala 和 Java 替换段落中的多个空格、换行、制表符

``` scala
package com.abcplus.bi

/**
  * 替换段落中的多个空格、换行、制表符
  * Created by gz on 2017/2/10.
  */
object replaceSpecialChar {

  def replaceChars(str: String): String = {
    val res = str.replaceAll("(\0|\\s*|\r|\n)", "")
    res
  }

  /**
    * Intellij 中 scala main的快捷键 main
    *
    * @param args
    */
  def main(args: Array[String]): Unit = {
    val str = """品牌：秋壳	货号：4Q201	厚薄：常规
                衣门襟：其他	组合形式：单件	颜色：黑色
                流行元素：其他	服装版型：直筒	衣长：常规款
                领型：圆领	风格：通勤	尺码：S,M,L,XS
                面料材质：聚酯纤维	年份季节：2017年春季"""
    val res = replaceChars(str)
    println(res)
  }
}

```

### Scala 结果
```
品牌：秋壳货号：4Q201厚薄：常规衣门襟：其他组合形式：单件颜色：黑色流行元素：其他服装版型：直筒衣长：常规款领型：圆领风格：通勤尺码：S,M,L,XS面料材质：聚酯纤维年份季节：2017年春季
```

## Java
```java
package com.abcplus.bi;

/**
 * Created by gz on 2017/2/10.
 */
public class ReplaceSpecialCharByJ {

    private String replaceChars(String str) {
        String res = str.replaceAll("(\0|\\s*|\r|\n)", "");
        return res;
    }

    /**
     * Intellij 中 java main的快捷键 psvm
     * @param args
     */
    public static void main(String[] args) {
        ReplaceSpecialCharByJ rep = new ReplaceSpecialCharByJ();
        String str = "品牌：秋壳	货号：4Q201	厚薄：常规\r\n衣门襟：其他	组合形式：单件	颜色：黑色 \n\r流行元素：其他	服装版型：直筒	衣长：常规款\r领型：圆领	风格：通勤	尺码：S,M,L,XS \n面料材质：聚酯纤维	年份季节：2017年春季";
        String res = rep.replaceChars(str);
        // 快捷键 sout
        System.out.println(res);
    }

}
```

### Java 结果
```
品牌：秋壳货号：4Q201厚薄：常规衣门襟：其他组合形式：单件颜色：黑色流行元素：其他服装版型：直筒衣长：常规款领型：圆领风格：通勤尺码：S,M,L,XS面料材质：聚酯纤维年份季节：2017年春季
```


