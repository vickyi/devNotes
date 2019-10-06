#### Scala中的下划线（underscore）到底有多少种应用场景？

1. 作为"通配符”，import导入包的所有成员，类似Java中的*。如import scala.math._
2. `_*`作为一个整体，告诉编译器你希望将某个参数当作参数序列处理！例如val s = sum(1 to 5:_*)就是将1 to 5当作参数序列处理。
3. 占位符，指代一个集合中的每个元素。比如对collection或sequence调用方法map、filter、sortWith、foreach等等表示对每一个元素进行处理。例如我们要在一个Array a中筛出偶数，并乘以2，可以用以下办法：
a.filter(_%2==0).map(2*_)
List（1,2,3）.foreach(println(_))
4. 在元组中，可以用方法_1, _2, _3访问组员。如a._2。其中句点可以用空格替代。
("a","b","c")._3
5. 使用模式匹配可以用来获取元组的组员，_n来取得第n个元素。
例如:
val t = ("a","b","c")
val (first, second, third) = t
但如果不是所有的部件都需要，那么可以在不需要的部件位置上使用_。
比如上一例中val (first, second, _) = t
6. 下划线_代表的是某一类型的默认值,对变量进行默认初始化。
对于Int来说，var i:Int=_，i是0。
对于Double来说，它是0.0
对于引用类型，它是null。
List（1,2,3）.foreach(println(_))

7. 类的setter方法，比如类A中定义了var f，则相当于定义了setter方法 f_=。也可以自己定义f_= 方法来完成更多的事情，比如设置前作一些判断或预处理之类的操作

#### 善用google和[stackoverflow](http://stackoverflow.com/)

[What are all the uses of an underscore in Scala?](http://stackoverflow.com/questions/8000903/what-are-all-the-uses-of-an-underscore-in-scala)

[scala在线学习](https://codepad.remoteinterview.io/SIAUDJVRKH)
