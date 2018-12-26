# hbase mapping hive error

## error msg

> message:org.apache.hadoop.hive.serde2.SerDeException org.apache.hadoop.hive.hbase.HBaseSerDe: columns has 6 elements while hbase.columns.mapping has 7 elements (counting the key if implicit))

## 原因

在创建hive/hbase相关联的表时，hbase表结构默认会有一个字段key，如果没有一个显示的字段'key'那么在创建表的进修，会自己创建，这样hive对应的表就会出现问题，所以在hive对应的表里一定要加上key这个字段，为了避免这个问题，在hbase表结构里可以显示的添加'key'字段，这样不容易出问题。

## 正确mapping格式

```sql
use ods;
drop table ods.hbase_mapping_crm_client_details;
CREATE EXTERNAL TABLE ods.hbase_mapping_crm_client_details(
`row_key` string comment "hbase rowkey",
`cust_id` string comment "代理主键，由全局ID服务生成",
`vcc_id` int comment "企业ID",
`original_id` int comment "原始的客户ID（用于记录老crm中的客户数据的ID）",
`cust_name` string comment "客户名称",
`cust_pinyin` string comment "客户名称对应的拼音全拼",
`phone_num` string comment "客户电话"
)STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = "i:cust_id#b,i:vcc_id#b,i:original_id#b,i:cust_name,i:cust_pinyin,i:phone_num")
TBLPROPERTIES("hbase.table.name" = "ns_ods:data_client_details");
```

**或者**：

```sql
use ods;
drop table ods.hbase_mapping_crm_client_details;
CREATE EXTERNAL TABLE ods.hbase_mapping_crm_client_details(
`row_key` string comment "hbase rowkey",
`cust_id` string comment "代理主键，由全局ID服务生成",
`vcc_id` int comment "企业ID",
`original_id` int comment "原始的客户ID（用于记录老crm中的客户数据的ID）",
`cust_name` string comment "客户名称",
`cust_pinyin` string comment "客户名称对应的拼音全拼",
`phone_num` string comment "客户电话"
)STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,i:cust_id#b,i:vcc_id#b,i:original_id#b,i:cust_name,i:cust_pinyin,i:phone_num")
TBLPROPERTIES("hbase.table.name" = "ns_ods:data_client_details");

```
