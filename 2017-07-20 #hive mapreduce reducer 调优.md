[TOC]

hive参数配置详情可查官方文档[Hive Configuration+Properties](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

本文针对 reducer 调优，主要涉及一下三个参数：

### hive.exec.reducers.bytes.per.reducer

> Default Value: 1,000,000,000 prior to Hive 0.14.0; 256 MB (256,000,000) in Hive 0.14.0 and later
Added In: Hive 0.2.0; default changed in 0.14.0 with HIVE-7158 (and HIVE-7917)
Size per reducer. The default in Hive 0.14.0 and earlier is 1 GB, that is, if the input size is 10 GB then 10 reducers will be used. In Hive 0.14.0 and later the default is 256 MB, that is, if the input size is 1 GB then 4 reducers will be used.

说明：每个reducer能够处理的文件大小。这个参数控制一个job会有多少个reducer来处理，依据的是输入文件的总大小。官方默认值：1G

查看配置的默认值：
```
hive> set hive.exec.reducers.bytes.per.reducer;
hive.exec.reducers.bytes.per.reducer=1024000000
```

临时调参：
```
hive> set hive.exec.reducers.bytes.per.reducer=15364000000;
hive>  set hive.exec.reducers.bytes.per.reducer;
hive.exec.reducers.bytes.per.reducer=15364000000
```

### mapred.reduce.tasks 
> - Default Value: -1
> - Added In: Hive 0.1.0
> The default number of reduce tasks per job. Typically set to a prime close to the number of available hosts. Ignored when mapred.job.tracker is "local". Hadoop set this to 1 by default, whereas Hive uses -1 as its default value. By setting this property to -1, Hive will automatically figure out what should be the number of reducers.

含义：设置每个job的reduce数。官方默认值为：-1

查看配置的默认值：
```
hive> set mapred.reduce.tasks;
mapred.reduce.tasks=-1
```

临时调参：
```
hive> set mapred.reduce.tasks=100;
hive> set mapred.reduce.tasks;
mapred.reduce.tasks=100
```

### hive.exec.reducers.max
> - Default Value: 999 prior to Hive 0.14.0; 1009 in Hive 0.14.0 and later
> - Added In: Hive 0.2.0; default changed in 0.14.0 with HIVE-7158 (and HIVE-7917)
> Maximum number of reducers that will be used. If the one specified in the configuration property mapred.reduce.tasks is negative, Hive will use this as the maximum number of reducers when automatically determining the number of reducers.

含义：设置最大的reduce数。如果mapreduce计算的reduce数超过设定值的值，则取设置的值。

查看配置的默认值：
```
hive> set hive.exec.reducers.max;
hive.exec.reducers.max=1099
```

临时调参：
```
hive> set hive.exec.reducers.max=999;
hive> set hive.exec.reducers.max;
hive.exec.reducers.max=999
```

以上所有set的值，都只是临时调整，不会改变配置文件中的配置。当你重新打开hive会话，查看相应的配置就一目了然了。
