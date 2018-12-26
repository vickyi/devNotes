## ns_ods:data_im_session_details

## hive sql

```sql
select * from ods.hbase_mapping_im_session_details a where source_id > 0 limit 100;
```

## hbase shell

```bash
scan 'ns_ods:data_im_session_details', FILTER=>"ValueFilter(=,'binary:sku188')"


scan 'ns_ods:data_im_session_details', {FILTER=>"ColumnPrefixFilter('create_user_id') AND ValueFilter(>,'binary:0')", LIMIT => 10}

scan 'ns_ods:data_im_session_details', {COLUMNS => ['i:create_user_id'],LIMIT => 10}

scan 'ns_ods:data_im_session_details', {COLUMNS => ['i:remark_type_id','i:operate_time'],LIMIT => 10}

scan 'ns_ods:data_im_session_details', {COLUMNS => ['i:create_time'],LIMIT => 10}

scan 'ns_ods:data_im_session_details', {LIMIT => 100}

scan 'ns_ods:data_im_session_details', {FILTER=>"ColumnPrefixFilter('create_user') AND ValueFilter(>,'binary:0')"}

scan 'ns_ods:data_im_session_details', {FILTER=>"ColumnPrefixFilter('i:create_user_id') AND ValueFilter(>,'binary:0')"}


scan 'ns_ods:data_im_session_details', {LIMIT => 100}

```

## ns_ods:data_client_details
```shell
scan 'ns_ods:data_client_details', {LIMIT => 100}
-- 暂时没有数据

cust_id
scan 'ns_ods:data_client_details', {COLUMNS => ['i:vcc_id','i:im_third_id','i:cust_id#b'],LIMIT => 10}

scan 'ns_ods:data_client_details', {COLUMNS => ['i:vcc_id','i:im_third_id','i:import_task_id','i:import_task_name'],LIMIT => 100}

scan 'ns_ods:data_client_details', {COLUMNS => ['i:import_task_id','i:import_task_name'],LIMIT => 100}

```

