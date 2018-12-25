Scala Iterator（迭代器）不是一个集合，它是一种用于访问集合的方法。
迭代器 it 的两个基本操作是 next 和 hasNext。
调用 it.next() 会返回迭代器的下一个元素，并且更新迭代器的状态。
调用 it.hasNext() 用于检测集合中是否还有元素。
让迭代器 it 逐个返回所有元素最简单的方法是使用 while 循环：
```
object Test {

    // iterator 最大最小值
    def maxAndMin() {
        val ita = Iterator(20,40,2,50,69, 90)
        val itb = Iterator(20,40,2,50,69, 90)

        println("max element: " + ita.max )
        println("min element: " + itb.min )

   }

    //iterator 大小
    def iteratorSize() {
        val ita = Iterator(20,40,2,50,69, 90)
        val itb = Iterator(20,40,2,50,69, 90)
      
        // 会将所有元素遍历一遍
        println("ita.size: " + ita.size )
        println("itb.length: " + itb.length )

   }

    // 遍历元素
    def printElements() {
        val it = Iterator("Baidu", "Google", "Runoob", "Taobao")
      
      while (it.hasNext){
         println(it.next())
      }
   }

   def main(args: Array[String]) {
    printElements()
    maxAndMin()
    iteratorSize()
   }
}
```