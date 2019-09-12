# spark-sql替换hive查询引擎

> CONSOLE# WARNING: Hive-on-MR is deprecated in Hive 2 and may not be available in the future versions. Consider using a different execution engine (i.e. spark, tez) or using Hive 1.X releases.

hive 查询的时候经常会提醒这一句，那就改成 spark-sql 吧。哦弥陀佛~~

## spark-sql

```shell
#!/bin/bash

. /etc/profile

yesterday=`date -d -1days '+%Y-%m-%d'`
echo "yesterday,(format :yyyy-MM-dd):"$yesterday

today=`date '+%Y-%m-%d'`
echo "today,(format :yyyy-MM-dd):"$today

tomarrow=`date -d 1days '+%Y-%m-%d'`
echo "tomarrow,(format :yyyy-MM-dd):"$tomarrow

hour_nowk=`date '+%k'`
echo "hour_now,(format %k:0-23):"$hour_nowk

hour_nowH=`date '+%H'`
echo "hour_now,(format %H:00-23):"$hour_nowH

hour_agok=`date -d -1hours '+%k'`
echo "hour_ago,(format %k:0-23):"$hour_agok

hour_agoH=`date -d -1hours '+%H'`
echo "hour_ago,(format %H:00-23):"$hour_agoH

#echo "从外部只传递两个参数时，第一个参数必须是 hive sql 文件，第二个必须是YYYY-MM-DD格式的日期!"
#echo "从外部只传递一个参数时，第一个参数必须是 hive sql 文件"

if [ $# == 2 ]; then
   sqlfile=$1
   echo "从外部只传递两个参数时，第一个参数必须是 hive sql 文件，第二个必须是YYYY-MM-DD格式的日期!"
   if [ $2 == "1970-01-01" ]; then
      ## 正常调度时，real 作业的日期是当天，但是依赖 real 作业的离线作业的日期是前一天
      if [[ $sqlfile == *"real.sql"* ]]; then
         echo "定时调度运行real 作业时，只需要一个sql文件作为参数，日期默认取当天"
         dt=$today
      else
         echo "正常调度时，real 作业的日期是当天，但是依赖 real 作业的离线作业的日期是前一天"
         dt=$yesterday
      fi
   elif [[ $2 =~ ^[0-9]{4}-[0-9]{2}-[0-9]{2}$ ]] && date -d "$2" >/dev/null
      then echo "Date $2 is valid and matches the format (YYYY-MM-DD)"
      echo "手动运行的时候，第二个参数为日期，需要手动指定，格式: YYYY-MM-DD"
      dt=$2
   else
      echo "Date $2 not matches the format (YYYY-MM-DD)!"
      exit 11
   fi
else
   echo "无法识别的参数!"
   exit 11
fi

echo "===>> 当前sql: $sqlfile, 执行日期: $dt"

spark-sql \
--queue bi \
--name dw_etl_$dt \
-d date=$dt \
-f $sqlfile

##-i hdfs://emr-header-1.cluster-70637:9000/dw/hive_udf/hive_udf_init.hql

if test $? -ne 0
then
exit 11
fi
```
