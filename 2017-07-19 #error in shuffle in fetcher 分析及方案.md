error in shuffle in fetcher 分析及方案
====
[TOC]

### ShuffleError 错误信息：
```
Error: org.apache.hadoop.mapreduce.task.reduce.Shuffle$ShuffleError: error in shuffle in fetcher#3
at org.apache.hadoop.mapreduce.task.reduce.Shuffle.run(Shuffle.java:134)
at org.apache.hadoop.mapred.ReduceTask.run(ReduceTask.java:376)
at org.apache.hadoop.mapred.YarnChild$2.run(YarnChild.java:164)
at java.security.AccessController.doPrivileged(Native Method)
at javax.security.auth.Subject.doAs(Subject.java:415)
at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1693)
at org.apache.hadoop.mapred.YarnChild.main(YarnChild.java:158) 
Caused by: java.lang.OutOfMemoryError: Java heap space
at org.apache.hadoop.io.BoundedByteArrayOutputStream.<init>(BoundedByteArrayOutputStream.java:56)
at org.apache.hadoop.io.BoundedByteArrayOutputStream.<init>(BoundedByteArrayOutputStream.java:46)
at org.apache.hadoop.mapreduce.task.reduce.InMemoryMapOutput.<init>(InMemoryMapOutput.java:63)
at org.apache.hadoop.mapreduce.task.reduce.MergeManagerImpl.unconditionalReserve(MergeManagerImpl.java:305)
at org.apache.hadoop.mapreduce.task.reduce.MergeManagerImpl.reserve(MergeManagerImpl.java:295)
at org.apache.hadoop.mapreduce.task.reduce.Fetcher.copyMapOutput(Fetcher.java:514)
at org.apache.hadoop.mapreduce.task.reduce.Fetcher.copyFromHost(Fetcher.java:336)
at org.apache.hadoop.mapreduce.task.reduce.Fetcher.run(Fetcher.java:193)
```

Cause 原因：reduce会在map执行到一定比例启动多个fetch线程去拉取map的输出结果，放到reduce的内存、磁盘中，然后进行merge。当数据量大时，拉取到内存的数据就会引起OOM，所以此时要减少fetch占内存的百分比，将fetch的数据直接放在磁盘上。
有关参数：**mapreduce.reduce.shuffle.memory.limit.percent**

### Default Configuration 默认参数：
```
<property>
   <name>mapreduce.reduce.shuffle.memory.limit.percent</name>
  <value>0.25</value>
  <description>Expert: Maximum percentage of the in-memory limit that a
  single shuffle can consume</description>
</property>
```
OR 或者
```
## hive
hive>set mapreduce.reduce.shuffle.memory.limit.percent;
mapreduce.reduce.shuffle.memory.limit.percent=0.15
```

### Solution 处理方案：限制reduce的shuffle内存使用
#### 如果是hive sql,在sql执行之前，增加如下语句：
```
set mapreduce.reduce.shuffle.memory.limit.percent=0.15;
```
#### 如果是 MapReduce 程序，在job conf中设置如下:
```
job.getConfiguration().setStrings("mapreduce.reduce.shuffle.memory.limit.percent", "0.15");
```

参考：http://www.sqlparty.com/yarn%E5%9C%A8shuffle%E9%98%B6%E6%AE%B5%E5%86%85%E5%AD%98%E4%B8%8D%E8%B6%B3%E9%97%AE%E9%A2%98error-in-shuffle-in-fetcher/