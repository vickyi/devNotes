[TOC]
## hadoop hive 分区表移动
###  shell脚本
```
### fct_path_list_history.sh
#!/bin/sh

. /etc/profile
. ~/.bash_profile

if [ $# == 2 ]; then
    datebeg=$1
    dateend=$2
else
    echo "请输入开始时间和结束日期，格式为2017-04-04"
    exit 1
fi


THIS="$0"
THIS_DIR=`dirname "$THIS"`
cd ${THIS_DIR}


beg_s=`date -d "$datebeg" +%s`
end_s=`date -d "$dateend" +%s`

echo "处理时间范围：$beg_s 至 $end_s"

while [ "$beg_s" -le "$end_s" ];do
    day=`date -d @$beg_s +"%Y-%m-%d"`;
    echo "当前日期：$day"
    hive -d date=$day -f ./fct_path_list_history.sql
    if test $? -ne 0
    then
    echo "处理失败，日期：$day, 跳过"
    else
    echo "处理成功，日期：$day"
    fi
    beg_s=$((beg_s+86400));
done

echo "全部处理完成！"
```

### hive sql
```
#### fct_path_list_history.sql
use dw;
LOAD DATA INPATH 'hdfs://nameservice1/user/hive/warehouse/dw.db/fct_path_list/date=${date}' OVERWRITE INTO TABLE fct_path_list_history PARTITION (date='${date}');

```

### 备份表创建

```
use dw;
CREATE TABLE `fct_path_list_history`(
  `gu_id` string,
  `endtime` bigint,
  `last_entrance_page_id` int,
  `last_guide_page_id` int,
  `last_before_goods_page_id` int,
  `last_entrance_page_value` string,
  `last_guide_page_value` string,
  `last_before_goods_page_value` string,
  `last_entrance_event_id` int,
  `last_guide_event_id` int,
  `last_before_goods_event_id` int,
  `last_entrance_event_value` string,
  `last_guide_event_value` string,
  `last_before_goods_event_value` string,
  `last_entrance_timestamp` bigint,
  `last_guide_timestamp` bigint,
  `last_before_goods_timestamp` bigint,
  `guide_lvl2_page_id` int,
  `guide_lvl2_page_value` string,
  `guide_lvl2_event_id` int,
  `guide_lvl2_event_value` string,
  `guide_lvl2_timestamp` bigint,
  `guide_is_del` int,
  `guide_lvl2_is_del` int,
  `before_goods_is_del` int,
  `entrance_page_lvl2_value` string,
  `guide_page_lvl2_value` string,
  `guide_lvl2_page_lvl2_value` string,
  `before_goods_page_lvl2_value` string,
  `entrance_event_lvl2_value` string,
  `guide_event_lvl2_value` string,
  `guide_lvl2_event_lvl2_value` string,
  `before_goods_event_lvl2_value` string,
  `rule_id` string,
  `test_id` string,
  `select_id` string,
  `last_entrance_pit_type` int,
  `last_entrance_sortdate` string,
  `last_entrance_sorthour` int,
  `last_entrance_lplid` int,
  `last_entrance_ptplid` int,
  `last_entrance_ug_id` int)
PARTITIONED BY (`date` string)
 stored as orc;
```

### nohup后台执行
```
nohup sh ./fct_path_list_history.sh 2016-01-06 2016-04-01 > ./fct_path_list_history.log 2>&1 &

nohup sh ./fct_path_list_history.sh 2016-04-02 2016-12-31 >> ./fct_path_list_history.log 2>&1 &

nohup sh ./fct_path_list_history.sh 2016-04-21 2016-12-31 >> ./fct_path_list_history.log 2>&1 &
```

### 异常登记
- 2016-01-05 在test.fct_path_list_bak
- 处理失败，日期：2016-04-20
