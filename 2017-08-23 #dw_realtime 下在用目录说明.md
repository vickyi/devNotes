
## dw_realtime 下在用目录说明
```
## hive udf 使用到的dim_page表文件
hdfs://nameservice1/user/hadoop/dw_realtime/config

## spark streaming 解析 kafka 数据输出目录，需要定时清理
hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list

## 实时path_list计算的输出路径
hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_path_list_jobs

## 离线路劲计算作业941的输出路径
hdfs://nameservice1/user/hadoop/dw_realtime/fct_for_path_list_offline

## 重新消费kafka的数据输出目录
hdfs://nameservice1/user/hadoop/dw_realtime/reprod

## 路径计算 2960 的数据临时输出目录，计算以后后续程序会将文件移到2960对应的hive表文件目录
hdfs://nameservice1/user/hadoop/dw_realtime/test
```