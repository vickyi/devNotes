
## 打包

```
## login to ops001
scp /data/jenkins_workspace/workspace/FunnelAnalysis/target/FunnelAnalysis-1.0-SNAPSHOT.jar hadoop@spark001.abcplus:/data/users/vk/FunnelAnalysis/

scp /data/jenkins_workspace/workspace/FunnelAnalysis/target/FunnelAnalysis-1.0-SNAPSHOT.jar vk@ABCBI-HIVE-001.abcplus:/data/home/vk/vk/FunnelAnalysis/
-- pwd: vk

```

## 运行

```bash
## login to spark001
## 传两个参数：日期和运行方式
##运行方式有3种：hiveF、dump2mysql、hiveDump

java -cp ./FunnelAnalysis-1.0-SNAPSHOT.jar com.abcplus.FunnelGO 2017-06-22 all

java -cp ./FunnelAnalysis-1.0-SNAPSHOT.jar com.abcplus.FunnelGO 2017-06-22 hiveDump 10

java -cp ./FunnelAnalysis-1.0-SNAPSHOT.jar com.abcplus.FunnelGO 2017-06-22 hiveF 10

java -cp ./FunnelAnalysis-1.0-SNAPSHOT.jar com.abcplus.FunnelGO 2017-06-22 dump2mysql 1

## 测试
java -cp ./FunnelAnalysis-1.0-SNAPSHOT.jar com.abcplus.MySqlConn 1
```

## 配置 dump propert文件

```sql
overwrite=true
task.name=test.rpt_funnel_data
method=sqoop

preprocess.sql=delete from test.rpt_funnel_data where date_id='{$date}'

hive.hql=select date_id,fid,terminal_id,site_id,pageId,pid,cate_level1_id,cate_level2_id,cate_level3_id,uv,main_order_cnt,sales_amount from test.funnel10 where date ='{$date}'

# mysql table name
db.table=test.rpt_funnel_data
# mysql table columns
db.columns="date_id,fid,terminal_id,site_id,page_id,pid,cate_level1_id,cate_level2_id,cate_level3_id,uv,main_ordr_cnt,sales_amount"

db.driver=com.mysql.jdbc.Driver
db.url=jdbc:mysql://db-master-biabcplus-000.abcplus/test?useUnicode=true&characterEncoding=utf-8
db.user=admin
db.password=yourpwd
hive.url=jdbc:hive2://hiveserver2-gold-000.abcplus:10000/default
hive.user=
hive.password=
```

## mysql表
```
CREATE TABLE `rpt_funnel_data` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `date_id` date NOT NULL,
  `fid` int(11) NOT NULL COMMENT '漏斗id',
  `page_level_id` int(11) DEFAULT 0 COMMENT '层级id',
  `terminal_id` int(11) DEFAULT NULL COMMENT '终端id',
  `terminal_name` varchar(20) DEFAULT NULL COMMENT '终端',
  `site_id` int(11) DEFAULT NULL COMMENT '站点id',
  `site_name` varchar(20) DEFAULT NULL COMMENT '站点',
  `page_id` int(11) DEFAULT NULL COMMENT '页面id',
  `page_name` varchar(30) DEFAULT NULL COMMENT '页面名称',
  `pid` int(11) DEFAULT NULL COMMENT '上一级page_id',
  `cate_level1_id` int(11) DEFAULT NULL COMMENT '一级分类id',
  `cate_level1_name` varchar(30) DEFAULT NULL COMMENT '一级分类',
  `cate_level2_id` int(11) DEFAULT NULL COMMENT '二级分类id',
  `cate_level2_name` varchar(30) DEFAULT NULL COMMENT '二级分类',
  `cate_level3_id` int(11) DEFAULT NULL COMMENT '三级分类id',
  `cate_level3_name` varchar(30) DEFAULT NULL COMMENT '三级分类',
  `uv` int(11) DEFAULT '0' COMMENT 'uv',
  `main_ordr_cnt` int(11) DEFAULT '0' COMMENT '订单数（拆前）',
  `ordr_cnt` int(11) DEFAULT '0' COMMENT '订单数（拆后）',
  `sales_amount` decimal(10,2) DEFAULT '0.00' COMMENT '销售金额',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=127 DEFAULT CHARSET=utf8;
```

## hive 表

``` sql
use test;
create table if not exists fct_funnel
(
    page_level_id int,
    pageId int,
    pid int,
    site_id int,
    terminal_id int,
    cate_level1_id int,
    cate_level2_id int,
    cate_level3_id int,
    uv bigint,
    main_order_cnt bigint,
    sales_amount double
)
PARTITIONED BY (
  `date` string,
  `fid` int)
stored as orc;
```
