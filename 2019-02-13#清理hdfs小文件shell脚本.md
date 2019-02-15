# 清理hdfs小文件shell脚本

```sh
#!/bin/bash
echo "--------------------------------------------------------------------------------------------------------------------"
echo "BASH_VERSION: $BASH_VERSION"
echo "参数说明: "
echo "从外部只传递零个参数时，遍历字典，删除 hdfs 对应日期下的文件目录"
echo "从外部只传递一个参数时，根据指定的 hive 外表，删除 hdfs 对应日期下的文件目录"
echo "从外部只传递两个参数时，第一个参数必须是 hdfs-to-hive 的外表名，第二个必须是YYYY-MM-DD格式的日期! 默认是当天日期减去20天"
echo "--------------------------------------------------------------------------------------------------------------------"

os_name=`uname`
if [ "Darwin" == $os_name ]; then
   # For BSD date:If you are using OS X or FreeBSD, use the following instead because BSD date is different from GNU date
   tmp_date_20days_ago=$(date -j -v-20d +"%Y-%m-%d")
elif [ "Linux" == $os_name ]; then
   #For GNU date:
   tmp_date_20days_ago=$(date --date="20 days ago" +"%Y-%m-%d")
fi

#必须先声明
declare -A small_file_paths
small_file_paths=(
   [hdfs_cc_dm_real]="/dw/cc/prod/dm/"
   [hdfs_cc_sms_processor_real]="/dw/cc/prod/sms_processor/"
   [hdfs_cc_sms_real]="/dw/cc/prod/sms/"
   [hdfs_crm_clients_real]="/dw/crm/prod/crm_clients/data_time="
   [hdfs_crm_contact_records_real]="/dw/crm/prod/crm_contact_records/"
   [hdfs_crm_contacts_real]="/dw/crm/prod/crm_contacts/"
   [hdfs_crm_opportunities_real]="/dw/crm/prod/crm_opportunities/"
   [hdfs_im_leave_message_content_real]="/dw/im/prod/message/"
   [hdfs_im_session_content_real]="/dw/im/prod/sessionContent/"
   [hdfs_im_view_history_real]="/dw/im/prod/viewHistory/"
   [hdfs_cc_cdr_real]="/dw/cc/prod/cdr/"
   [hdfs_ticket_logs_real]="/dw/ticket/prod/ticket/"
   [hdfs_user_center_real]="/dw/usercenter/prod/user/"
   [hdfs_cc_asr_real]="/dw/cc/prod/asr/"
)

is_valid_date() {
   if [ ${#date_20days_ago} == 10 ];then
      echo "$date_20days_ago 格式正确。必须生成合适的日期，以避免操作错误！"
   else
      echo "$date_20days_ago 格式错误，程序退出！必须生成合适的日期，以避免操作错误！"
      exit 11
   fi
}

if [ $# == 0 ]; then
    date_20days_ago=$tmp_date_20days_ago
    # 检查
    is_valid_date
    for key in $(echo ${!small_file_paths[*]})
    do
        path="${small_file_paths[$key]}${date_20days_ago}"
        echo "清理 Hadoop 上的小文件目录：$key : $path"
        hadoop fs -rm -r $path
    done
elif [ $# == 1 ]; then
   hive_ext_tbl=$1
   date_20days_ago=$tmp_date_20days_ago
   is_valid_date
   path="${small_file_paths["${hive_ext_tbl}"]}${date_20days_ago}"
   echo "===>> 1个参数. 当前hive 外表: $hive_ext_tbl, 清除的 hdfs 文件目录是: $path"
   # hadoop fs -rm -r $path
elif [ $# == 2 ]; then
   hive_ext_tbl=$1
   if [[ $(date "+%Y-%m-%d" -d "$2") == "$2" ]]; then
      echo "Date $2 is valid and matches the format (YYYY-MM-DD)"
      date_20days_ago=$2
      is_valid_date
      path="${small_file_paths["${hive_ext_tbl}"]}${date_20days_ago}"
      echo "===>> 1个参数. 当前hive 外表: $hive_ext_tbl, 清除的 hdfs 文件目录是: $path"
      # hadoop fs -rm -r $path
   else
      echo "Date $2 not matches the format (YYYY-MM-DD)!"
      exit 11
   fi
fi

if test $? -ne 0
then
exit 11
fi
```