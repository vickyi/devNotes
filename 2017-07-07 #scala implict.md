```
// Int，Long，Double都是AnyVal的子类，这三个类型之间没有继承的关系，不能直接相互转换。
object ImplicitDemo {

  def display(input:String):Unit = println(input)

  implicit def typeConvertor(input:Int):String = input.toString

  implicit def typeConvertor(input:Boolean):String = if(input) "true" else "false"

//   implicit def booleanTypeConvertor(input:Boolean):String = if(input) "true" else "false"


  def main(args: Array[String]): Unit = {
    display("1212")
    display(12)
    display(true)
  }
}


/**
总结：
1. 隐式转换函数是指在同一个作用域下面，一个给定输入类型并自动转换为指定返回类型的函数，这个函数和函数名字无关，和入参名字无关，只和入参类型以及返回类型有关。注意是同一个作用域
2. 隐式转换函数只在意输入类型，返回类型。
3. 记住隐式转换函数的同一个作用域中不能存在参数和返回值完全相同的2个implicit函数。
4. 隐式转换是scala的语法灵活和简洁的重要组成部分
*/
```