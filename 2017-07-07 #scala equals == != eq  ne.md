## scala equals == != eq  ne 测试
```
case class User(name: String, age:Int)

val u1 = User("zhang", 18)
val u2 = User("zhang", 18)
val u3 = User("zhang", 19)

// equals: 用来测试值是否相等
println("===== test equals ======")
println(u1 equals u1)
println(u1 equals u2)
println(u2 equals u1)
println(u1 equals u3)

// 特殊情况
// null是无意义的，不存在值，因此运行时空指针异常
// println(null equals null)

// ==: 在Any中定义为final，代表equals。所以==的行为 与 equals的完全一样，都是用来测试值是否相等
println("===== test == ======")
println(u1 == u1)
println(u1 == u2)
println(u2 == u1)
println(u1 == u3)


//特殊情况: null 在 == 的左边的时候
println(null == u1)

// 返回值为true。但是会提示编译警告，这永远为true
println(null == null)

// !=: 与 == 相反的操作，用于判断表示值不相等
println("===== test != ======")
println(u1 != u1)
println(u1 != u2)
println(u2 != u1)
println(u1 != u3)

//特殊情况: null 在 == 的左边的时候
println(null != u2)

// 返回值为false。但是会提示编译警告，这永远为true
println(null != null)

// eq and ne：用于测试引用的相等性，即两个对象的是否指向相同的内存地址。这两个方法至对AnyRef类型有定义
// 参见：http://scala-lang.org/api/2.10.4/#scala.AnyRef
println("===== test eq ======")
println(u1 eq u1)
println(u1 eq u2)
println(u2 eq u1)
println(u1 eq u3)
val u4 = u1
println(u4 eq u1)

// ne: 与eq相反，即与 !(u1 eq u1)等价
println("===== test ne ======")
println(u4 ne u1)
println(u1 ne u2)


// 数组相等，sameElements
// Array 是可变的原始Java数组，与scala中的习惯使用的集合类型有不同的方法。建议使用scala的序列。
println("===== test sameElements ======")
val a1 = Array(1, 2)
val a2 = Array(1, 2)
println(a1 == a2)
println(a1 sameElements a2)

/** 运行结果
e:\BaiduYunPanRsync\work\abcplus\dev\scala-code\equals-eq.scala:30: warning: comparing values of types Null and Null using `==' will always yield true
println(null == null)
             ^
e:\BaiduYunPanRsync\work\abcplus\dev\scala-code\equals-eq.scala:43: warning: comparing values of types Null and Null using `!=' will always yield false
println(null != null)
             ^
two warnings found
===== test equals ======
true
true
true
false
===== test == ======
true
true
true
false
false
true
===== test != ======
false
false
false
true
true
false
===== test eq ======
true
false
false
false
true
===== test ne ======
false
true
===== test sameElements ======
false
true
*/
```