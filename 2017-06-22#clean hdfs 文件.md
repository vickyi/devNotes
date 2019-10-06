### clean hdfs

[hadoop@ABCBI-SPARK-001 vk]$ hadoop fs -du -h hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list
108.4 G  325.1 G  hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/abcplus_hash3
1.4 T    4.1 T    hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_event_hash2
3.3 T    10.0 T   hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_pageinfo_hash2
129.3 G  387.8 G  hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/pc_events_hash3

hadoop fs -ls hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/mb_pageinfo_hash2 > mb_pageinfo_hash2.txt

hadoop fs -rm -r hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/{mb_event_hash2,mb_pageinfo_hash2,pc_events_hash3,abcplus_hash3}/date=2017-0[3-4]*

hadoop fs -rm -r hdfs://nameservice1/user/hadoop/dw_realtime/dw_real_for_path_list/{mb_event_hash2,mb_pageinfo_hash2,pc_events_hash3,abcplus_hash3}/date=2017-05-[0-1]*