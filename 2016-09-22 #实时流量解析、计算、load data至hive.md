# 实时流量

## 数据对象

基于Spark通用计算平台，可以很好地扩展各种计算类型的应用，尤其是Spark提供了内建的计算库支持，像Spark Streaming、Spark SQL、MLlib、GraphX，这些内建库都提供了高级抽象，可以用非常简洁的代码实现复杂的计算逻辑、这也得益于Scala编程语言的简洁性。这里，我们基于1.3.0版本的Spark搭建了计算平台，实现基于Spark Streaming的实时计算。

### 我们的应用场景是分析用户使用手机App的行为

描述如下所示

- 手机客户端会收集用户的行为事件（我们以点击事件为例），将数据发送到数据服务器，我们假设这里直接进入到Kafka消息队列
- 后端的实时服务会从Kafka消费数据，将数据读出来并进行实时分析，这里选择Spark Streaming，因为Spark Streaming提供了与Kafka整合的内置支持
- 经过Spark Streaming实时计算程序分析，将结果load至hive，可以实时获取用户的行为数据，并可以导出进行离线综合统计分析。

## 项目

```shell
git@gitlab.abcplus.org:vk/dw-realtime.git
```

## Spark Streaming处理Page逻辑概要

> event 采用类似的方式处理，最关键是计算逻辑不同

1. App将用户的浏览页面的数据上传至BI Kafka消息队列。
2. Spark Streaming 实时从Kafka读取数据，并进行实时分析。
3. 计算结果按天+gu_id最后一位分区落地到HDFS。
4. HDFS中的文件是一分钟一个，需要将这些小文件合并。
5. 定时通过MapReduce项目PathList对Page和Event的数据进行路径计算，落地到HDFS
6. 计算之后，调用hive命令将数据load至hive表。

## 项目架构

整个项目包含三个子项目：

1. realtime-sourcedata: 消费Kafka数据，分区存放HDFS。每分钟一个小文件。
2. hdfs-files-merge：合并1中产生的大量小文件
3. pathlist：这是一个MapReduce项目，对page和event的数据一起处理。


## 项目打包

```shell
# ops001.abcplus上项目目录：git 项目
cd /data/home/vk/dev_pro/dw-realtime

# 打包并scp至spark001.abcplus
./run-test.sh

```
## spark项目执行服务器
```
## spark项目需要在此服务器上调起
ssh hadoop@spark001.abcplus

```

## spark

### spark项目jar包目录

```shell
## 实时解析的jar包目录。默认目录
cd /home/hadoop/users/vk/jars/

# 复制到正式目录下
cp /home/hadoop/users/vk/jars/realtime-souredata-1.0-SNAPSHOT.jar /data/abcplus_workspace/spark_workspace/realtime/jars/realtime-souredata-1.0-SNAPSHOT-jar-with-dependencies.jar2

# 替换原有jars
mv /data/abcplus_workspace/spark_workspace/realtime/jars/realtime-souredata-1.0-SNAPSHOT-jar-with-dependencies.jar2 /data/abcplus_workspace/spark_workspace/realtime/jars/realtime-souredata-1.0-SNAPSHOT-jar-with-dependencies.jar

# 打包的saprk解析jar包。默认目录下的jar包复制到这里
ll /data/abcplus_workspace/spark_workspace/realtime/jars/realtime-souredata-1.0-SNAPSHOT-jar-with-dependencies.jar

## 合并小文件，暂时在临时目录下执行
cd /home/hadoop/users/vk/run_filesmerge/

## PathList 计算，暂时在临时目录下执行
cd /home/hadoop/users/vk/run_pathList/

```

## 脚本: 实时解析

```shell
# 实时解析shell
ll /data/abcplus_workspace/spark_workspace/realtime/bin/event-real-bi-dw-vk.sh

ll /data/abcplus_workspace/spark_workspace/realtime/bin/page-real-bi-dw-vk.sh

# 失败调启
ll /bin/bash /home/hadoop/abcplus_workspace/software/trolls/check_task.sh
```

### 实时数据目录

```shell
hadoop fs -du -h hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_event_hash2/
hadoop fs -ls hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_event_hash2/date=2016-09-28/gu_hash=f/logs

hadoop fs -du -h hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_pageinfo_hash2/
hadoop fs -ls hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_pageinfo_hash2/date=2016-09-28/gu_hash=f/logs
```

### 小文件合并后的目录

```shell
hadoop fs -du -h hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_event_hash2/date=2016-09-20/gu_hash=0/merged

hadoop fs -du -h hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_pageinfo_hash2/date=2016-09-20/gu_hash=0/merged
```

### PathListNew 计算结果目录

```shell
hadoop fs -du -h hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_path_list_jobs/
```

### 脚本: 合并小文件 + PathListNew计算 + load至hive（**废弃**）

```shell
ll /home/hadoop/users/vk/run_filesmerge/run_files_merge.sh

# 定时任务配置，每小时的第一分钟执行
01 * * * * sh /home/hadoop/users/vk/run_filesmerge/run_files_merge.sh
```
