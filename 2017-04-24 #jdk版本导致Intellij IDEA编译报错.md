最经安装了Intellij IDEA后，导入之前开发的项目，运行买方法，一直都报同样的错误，还以为是idea的版本问题

```
Error:scalac: Error: org/jetbrains/abcpluss/incremental/messages/BuildMessage$Kind
java.lang.UnsupportedClassVersionError: org/jetbrains/abcpluss/incremental/messages/BuildMessage$Kind

at org.jetbrains.abcpluss.incremental.scala.local.AbstractCompiler$ClientReporter.log(AbstractCompiler.scala:92)

at xsbt.DelegatingReporter.info0(DelegatingReporter.scala:39)

at scala.tools.nsc.reporters.Reporter$$anonfun$error$1.apply$mcV$sp(Reporter.scala:70)

at scala.tools.nsc.reporters.Reporter$$anonfun$error$1.apply(Reporter.scala:70)

at scala.tools.nsc.reporters.Reporter$$anonfun$error$1.apply(Reporter.scala:70)

at scala.tools.nsc.reporters.Reporter.withoutTruncating(Reporter.scala:42)

at scala.tools.nsc.reporters.Reporter.error(Reporter.scala:70)

at scala.tools.nsc.Global.globalError(Global.scala:229)

at scala.tools.nsc.Global$Run.compileUnits(Global.scala:1565)

at scala.tools.nsc.Global$Run.compileSources(Global.scala:1553)

at scala.tools.nsc.Global$Run.compile(Global.scala:1662)

at xsbt.CachedCompiler0.run(CompilerInterface.scala:115)

at xsbt.CachedCompiler0.run(CompilerInterface.scala:94)

at xsbt.CompilerInterface.run(CompilerInterface.scala:22)

at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)

at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)

at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)

at java.lang.reflect.Method.invoke(Method.java:606)

at sbt.compiler.AnalyzingCompiler.call(AnalyzingCompiler.scala:101)

at sbt.compiler.AnalyzingCompiler.compile(AnalyzingCompiler.scala:47)

at sbt.compiler.AnalyzingCompiler.compile(AnalyzingCompiler.scala:41)

at  org.jetbrains.abcpluss.incremental.scala.local.IdeaIncrementalCompiler.compile(IdeaIncrementalCompiler.scala:32)

at org.jetbrains.abcpluss.incremental.scala.local.LocalServer.compile(LocalServer.scala:26)

at org.jetbrains.abcpluss.incremental.scala.remote.Main$.make(Main.scala:68)

at org.jetbrains.abcpluss.incremental.scala.remote.Main$.nailMain(Main.scala:25)

at org.jetbrains.abcpluss.incremental.scala.remote.Main.nailMain(Main.scala)

at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)

at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)

at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)

at java.lang.reflect.Method.invoke(Method.java:606)

at com.martiansoftware.nailgun.NGSession.run(NGSession.java:319)
```

### 原因：
```
低版本Java不支持org.jetbrains.abcpluss.incremental.messages包
org/jetbrains/abcpluss/incremental/messages/BuildMessage$Kind
```

### 解决办法：
```
我重新换了jdk1.8 的版本，把项目的jdk换成1.8，运行成功了，真是jdk的问题.....
```