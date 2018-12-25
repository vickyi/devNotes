## Intellij IDEA  内存优化
idea 默认的内存配置会导致idea启动缓慢，运行时很卡，查了很多资料后做了一些优化。
自己的开发机，64位系统，内存8G；需要自定义的同学可以重点关注Xms 和 Xmx 两个参数

## 找到配置文件 idea64.exe.vmoptions


## 优化默认配置
```
-server
-Xms128m
-Xmx512m
-XX:NewRatio=3
-Xss16m
-XX:+UseConcMarkSweepGC
-XX:+CMSParallelRemarkEnabled
-XX:ConcGCThreads=4
-XX:ReservedCodeCacheSize=240m
-XX:+AlwaysPreTouch
-XX:+TieredCompilation
-XX:+UseCompressedOops
-XX:SoftRefLRUPolicyMSPerMB=50
-Dsun.io.useCanonCaches=false
-Djava.net.preferIPv4Stack=true
-Djsse.enableSNIExtension=false
-ea
```

```
## key server
http://idea.iteblog.com/key.php
```