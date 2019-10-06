# Apache Spark

### 1、Spark编程模型

- Spark生态系统概述
- RDD
- 缓存策略介绍
- transformation
- action
- lineage
- 容错处理
- 宽依赖与窄依赖
- 集群配
-

### 2、Spark内核剖析

- Spark术语解释
- 集群概览
- 核心组件
- 数据本地性
- 常用RDD
- 任务调度(DAGScheduler ,TaskScheduler)
- Task细节
- 广播变量
- 累加器
- 性能调优
-

### 3、Spark Streaming流式计算

- DStream
- 数据源
- 无状态transformation与有状态transformation
- checkpoint
- 容错
-

### 4、Spark SQL
- DataFrame API
- Hive交互
- 外部数据源API
- 与Spark其他组件的交互
- Catalyst查询优化器
-

### 5、Spark MLlib
- MLlib最新进展简介
- 模型表示 --> MLlib的向量模型与矩阵模型
- 优化并行 --> 同步方式、优化调度、以及模型存储
- 计算模式 --> MLlib与GraphX
- 数据承载 --> MLlib与SparkSQL
- 实例分析 --> MLlib与Scikit-learn