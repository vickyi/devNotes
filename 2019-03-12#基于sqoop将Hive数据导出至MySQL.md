# 2019-03-12#基于sqoop将Hive数据导出至MySQL

该方式下需要先将数据从Hive表导出到HDFS，再从HDFS将数据导入到RDBMS。虽然比直接导出多了一步操作，但是可以实现对数据的更精准的操作，特别是在从Hive表导出到HDFS时，可以进一步对数据进行字段筛选、字段加工、数据过滤操作，从而使得HDFS上的数据更“接近”或等于将来实际要导入RDBMS表的数据。在从HDFS导入RDBMS时，也是将一个“小数据集”与目标表中的数据做对比，会提高导出速度。
![]()

> 作者：汀桦坞，原文：https://blog.csdn.net/wiborgite/article/details/80991567

## 全量导出

```sh
sqoop export --connectjdbc:mysql://localhost:3306/wht --username root --password cloudera --table wht_test2 --fields-terminated-by ',' --export-dir /user/hive/warehouse/wht_test1

> 原文：https://blog.csdn.net/wiborgite/article/details/80958201

```

## 增量导出

```sh
sqoop export --connectjdbc:mysql://localhost:3306/wht --username root --password cloudera --tablewht_test2 --fields-terminated-by ',' --update-key c_id --export-dir /user/hive/warehouse/wht_test1
```

## 写入更新

```sh
# 全量导出
# HQL示例：
insert overwrite  directory ‘/user/root/export/test’ row format delimited fields terminated by ‘,’ STORED AS textfile select F1,F2,F3 from <sourceHiveTable>;

# SQOOP脚本：
sqoop export --connect jdbc:mysql://localhost:3306/wht --username root --password cloudera --table <targetTable> --fields-terminated-by ','  --columns F1,F2,F3 --export-dir /user/root/export/test

# 增量导出（insert模式）
# HQL示例：
insert overwrite  directory ‘/user/root/export/test’ row format delimited fields terminated by ‘,’ STORED AS textfile select F1,F2,F3 from <sourceHiveTable> where <condition>;

# SQOOP脚本：
sqoop export --connect jdbc:mysql://localhost:3306/wht --username root --password cloudera --table <targetTable> --fields-terminated-by ‘,’  --columns F1,F2,F3 --update-key F4 --update-mode  allowinsert --export-dir /user/root/export/test


# 更新导出（update模式）
# HQL示例：
insert overwrite  directory ‘/user/root/export/test’ row format delimited fields terminated by ‘,’ STORED AS textfile select F1,F2,F3 from <sourceHiveTable> where <condition>;

# SQOOP脚本：
sqoop export --connect jdbc:mysql://localhost:3306/wht --username root --password cloudera --table <targetTable> --fields-terminated-by ‘,’  --columns F1,F2,F3 --update-key F4 --update-mode  updateonly --export-dir /user/root/export/test
```