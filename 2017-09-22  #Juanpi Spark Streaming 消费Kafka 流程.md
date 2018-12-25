
abcplus Spark Streaming 消费Kafka 流程
=====

[Toc]
## 开发知识准备
1. 了解待消费的kafka topic，[BI流量TOPIC清单](http://wiki.abcplus.org/pages/viewpage.action?pageId=1507559)
2. 明确自己需要的topic中有哪些字段(自己找架构确认)
3. 广点通项目结构
![广点通项目](http://olx1ji9hn.bkt.clouddn.com/image/gtd-proj.png)

## 开发流程
1. 在abcplus现有的Spark Streaming 项目模板上进行开发
2. 主类：com.abcplus.streaming.KafkaConsumer，入口函数是main方法
3. 消费逻辑自己处理
```
    // 解析逻辑：page 和 event 分开解析
    if (topic.equals("mb_pageinfo_hash2")) {
      val consumer = new KafkaConsumer(topic, zkQuorum)
      consumer.goodsPage(message, ssc, km)
    } else if (topic.equals("mb_event_hash2")) {
      val consumer = new KafkaConsumer(topic, zkQuorum)
      consumer.eventProcess1(message, ssc, km)
    }
    else if (topic.equals("esb.order.topic.OrderPaid")) {
      val consumer = new KafkaConsumer(topic, zkQuorum)
      consumer.orderPaidParse(message, ssc, km)
    } else {
      println("请指定需要解析的kafka Topic-Group！")
      System.exit(1)
    }
```
4. KafkaManager 是一个封装好的读取kafka 数据的类。消费了多少数据，消费到哪里，并将这些信息记录到独立的zk。

## 作业上线
1. 上线地址： http://bimgr.abcplus.org/sparkjobsinfo/list.html
2. 参数配置
```
"zkQuorum"="kafka-sharing-000.abcplus:3181,kafka-sharing-002.abcplus:3181" "brokerList"="kafka-sharing-000.abcplus:9092,kafka-sharing-001.abcplus:9092,kafka-sharing-002.abcplus:9092" "topic"="esb.order.topic.OrderPaid" "groupId"="8prod_bi_gdt_esb.order.topic.OrderPaid" "consumerType"=1 "consumerTime"=60 "maxRecords"=50
```
**关键参数**：topic 、groupId consumerTime maxRecords

- topic：即时kafka数据topic

- groupId：则是用来存储当前spark streaming作业消费kafka的数据进度

- consumerTime：时间单位为秒

- maxRecords：每个线程消费的最大记录数

## 数据落地
消费完的数据直接落地到hdfs

## 数据加载
以下为hive命令，将落地好的数据与**hive外表**关联
1. load data local inpath ...   -- 数据在linux本地
2. load data inpath ... -- 数据在hdfs上