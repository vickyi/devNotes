## kafka 数据落地HDFS目录

``` shell
## 以 2017-01-23 号为例
hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_event_hash2/date=2017-01-23/gu_hash=0/merged/

hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_pageinfo_hash2/date=2017-01-23/gu_hash=0/merged/

hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/pc_events_hash3/date=2017-01-23/gu_hash=0/merged/
```


数据问题排查
------------
```
hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r1_dw_real_path_list_jobs/date=2017-01-23/gu_hash=0/part-r-00000 | grep self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30 > da30.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r1_dw_real_path_list_jobs/date=2017-01-23/gu_hash=0/part-r-00000 | grep self_f4933440-ad92-4afc-a0a9-68db436e3a70 > 3a70.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_event_hash2/date=2017-01-23/gu_hash=0/merged/mer* | grep self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30 > source_gu.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_pageinfo_hash2/date=2017-01-23/gu_hash=0/merged/mer* | grep self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30 > source_gu1.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/pc_events_hash3/date=2017-01-23/gu_hash=0/merged/mer* | grep self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30 > source_gu2.txt

------------------------
hadoop fs -rm -r hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r2_dw_real_path_list_jobs/date=2017-01-23/

hadoop fs -rm -r hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r2_dw_real_path_list_jobs/date=2017-01-23/gu_hash=0

yarn jar ./pathlist-1.0.jar com.abcplus.bi.mapred.PathListControledJobs 2017-01-23

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r2_dw_real_path_list_jobs/date=2017-01-23/gu_hash=0/part-r-00000 | grep self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30 > d24h11_da30.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_event_hash2/date=2017-01-23/gu_hash=0/merged/mer* | grep self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30 > source_e3a70.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_pageinfo_hash2/date=2017-01-23/gu_hash=0/merged/mer* | grep self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30 > source_pda30.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/pc_events_hash3/date=2017-01-23/gu_hash=0/merged/mer* | grep self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30 > source_gu2.txt


------------

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r2_dw_real_path_list_jobs/date=2017-01-22/gu_hash=0/part-r-00000 | grep 00000000-0000-0030-236b-695a0e039150 > d22h11_9150.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r2_dw_real_path_list_jobs/date=2017-01-22/gu_hash=0/part-r-00000 | grep 00000000-0000-0030-38b9-ff5007c74e40 > d22h11_4e40.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r2_dw_real_path_list_jobs/date=2017-01-22/gu_hash=0/part-r-00000 | grep 00000000-0000-0030-39e6-3b617c9ec1c0 > d22h11_c1c0.txt

hadoop fs -cat hdfs://nameservice1/user/hadoop/dw_realtime/hotTest_r2_dw_real_path_list_jobs/date=2017-01-22/gu_hash=0/part-r-00000 | grep 00000000-0000-0030-3ff0-707000000030 > d22h11_0030.txt


scp hadoop@spark001.abcplus:/home/hadoop/users/vk/test_path_list/pathlist-1.0.jar .

scp hadoop@ops001.abcplus:/data/jenkins_workspace/workspace/path_list_offline/pathlist/target/pathlist-1.0.jar .

bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list slave6:9092 -topic videoplay --time -2

select
table_source,
entrance_page_id,
entrance_page_value,
entrance_event_id,
entrance_event_value,
entrance_starttime,
entrance2_page_id,
entrance2_starttime,
guide_page_id,
guide_starttime,
guide2_page_id,
guide2_starttime,
gu_id,
gu_create_time,
session_id,
page_id,
page_value,
page_level_id,
starttime,
endtime from dw.path_list_real where date="2017-01-23" and gu_id = "self_f7e0ac3b-ab6c-4d00-9a09-531edd8fda30";

/home/hadoop/users/shisan/devTools/kafka_2.10-0.8.2.1/bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list kafka-broker-000.abcplus:9082 -topic mb_event_hash2 --time -2

```

