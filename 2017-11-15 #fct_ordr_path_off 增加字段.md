[TOC]

水滴要求在成单路径的层级中增加一个字段：smartbox。改动的都是关键hive表，而且已有数据的hive表，字段增加就无法删除，改动风险比较大，需要经过严格的方案验证后才能动，贸然改动，将是骑虎难下。

先测试 path_list。见文档：http://note.youdao.com/noteshare?id=76e80284fa9ce6ffee5deef455065ad0&sub=6D92CBB1EE3144729A84F62AAF2A1D4D

本文链接：http://note.youdao.com/noteshare?id=34f6b2547f1a4b65fcb2360d3cd099fc&sub=89A045D917BB4163832496322FE99482

## 0. 建表

执行以下脚本，如果存在就删除，重新建表。

```
use test;
CREATE EXTERNAL TABLE `fct_ordr_path_off` (
  `gu_id` string,
  `user_id` string,
  `utm_id` string,
  `gu_create_time` string,
  `session_id` string,
  `terminal_id` int,
  `app_version` string,
  `site_id` int,
  `ctag` string,
  `location` string,
  `ugroup` string,
  `hour` string,
  `goods_id` bigint,
  `page_id` int,
  `page_value` string,
  `page_level_id` int,
  `page_pv` int,
  `detl_page_pv` bigint,
  `cart_pv` bigint,
  `ordr_create_time` string,
  `ordr_pay_time` string,
  `cart_time` string,
  `main_order_no` bigint,
  `is_pay` int,
  `goods_nums` bigint,
  `goods_amount` double,
  `goods_fav` double,
  `goods_reduce_price` double,
  `goods_shipping_fav` double,
  `entrance` struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string>,
  `entrance2` struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string>,
  `guide` struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string>,
  `guide2` struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string>,
  `before_goods` struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string>,
  `rpt_tag` string,
  `shop_id` string, 
  `ref_shop_id` string, 
  `rule_id` string, 
  `test_id` string, 
  `select_id` string, 
  `x_page_value` string, 
  `ref_x_page_value` string, 
  `group_goods_id` string, 
  `is_pintuan` string, 
  `admin_id` int, 
  `cate_level1_id` int, 
  `cate_level2_id` int, 
  `cate_level3_id` int, 
  `brand_id` int, 
  `goods_type_id` int, 
  `start_time` string, 
  `goods_source_type` int, 
  `goods_type` int, 
  `old_cate_level1_id` int, 
  `business_id` int, 
  `goods_wm_type` int, 
  `shop_activity_type` int, 
  `grounding_times` int, 
  `first_sale_time` string, 
  `goods_source_code` bigint, 
  `page_click_pv` int)
PARTITIONED BY (`date` string) 
STORED AS ORC;
```

## 1. 测试加字段方案可行性

登陆hive001，或者spark001（需要权限，千万谨慎操作）

#### a.1 准备数据

```
hadoop fs -cp hdfs://nameservice1/user/hive/warehouse/dw.db/fct_ordr_path_off/date=2017-11-10 hdfs://nameservice1/user/hive/warehouse/test.db/fct_ordr_path_off/date=2017-11-10
```

#### a.2 装载数据
在hive下执行：
```
use test;
alter table fct_ordr_path_off add partition (date="2017-11-10") location 'hdfs://nameservice1/user/hive/warehouse/test.db/fct_ordr_path_off/date=2017-11-10';
```

#### a.3 数据质量验证

示例 1：
```
select a.date, a.* 
from test.fct_ordr_path_off a 
where date = "2017-11-10";
```

示例 2：
```
select a.date, a.entrance.page_id, count(1) as pv, count(distinct gu_id) as uv 
from test.fct_ordr_path_off a 
where a.date = "2017-11-10"
group by a.date, a.entrance.page_id;
```

### b. 增加字段
在hive下执行：
```
use test;
ALTER TABLE fct_ordr_path_off CHANGE entrance entrance struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string,test2:string>;
```

#### b.1 准备数据

```
hadoop fs -cp hdfs://nameservice1/user/hive/warehouse/dw.db/fct_ordr_path_off/date=2017-11-14 hdfs://nameservice1/user/hive/warehouse/test.db/fct_ordr_path_off/date=2017-11-14
```

#### b.2 装载数据

在hive下执行：

```
use test;
alter table fct_ordr_path_off add partition (date="2017-11-14") location 'hdfs://nameservice1/user/hive/warehouse/test.db/fct_ordr_path_off/date=2017-11-14';
```

#### b.3 数据质量验证
参见a.3，注意修改下日期。



## 2. dw正式上线准备

**只要当以上数据质量验证无误之后才能进行一下操作**

### dw.fct_ordr_path_off 增加字段

一共需要增加5个字段：5个层级，每个层级都加。

修改脚本如下：
```
-- dw 上线 
ALTER TABLE fct_ordr_path_off CHANGE entrance entrance struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string,smartbox:string>;

ALTER TABLE fct_ordr_path_off CHANGE entrance2 entrance2 struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string,smartbox:string>;

ALTER TABLE fct_ordr_path_off CHANGE guide guide struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string,smartbox:string>;

ALTER TABLE fct_ordr_path_off CHANGE guide2 guide2 struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string,smartbox:string>;

ALTER TABLE fct_ordr_path_off CHANGE before_goods before_goods struct<page_id:int,page_value:string,page_lvl2_value:string,event_id:int,event_value:string,event_lvl2_value:string,starttime:string,loadtime:string,pit_type:string,pit_value:string,pit_no:string,sortdate:string,sorthour:string,lplid:string,ptplid:string,gid:string,testid:string,selectid:string,ug_id:string,rule_id:string,x_page_value:string,ref_x_page_value:string,smartbox:string>;

```

### 修改作业 fct_ordr_path_off

dw.fct_ordr_path_off 5 个层级上虽然增加了smartbox字段，但是相应的脚本也需要修改。