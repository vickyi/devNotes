# hive mapjoin 方案

## hive 显式使用mapjoin

### 检查 mapjoin 是否开启

```shell
set hive.auto.convert.join=true;

## 检查 mapjoin 开启状态
set hive.auto.convert.join;
hive.auto.convert.join=true
```

### 显示指定 mapjoin

```sql
SELECT
    -- 可以显示的指定如下这一行mapjoin 关键词。
    /*+mapjoin(b)*/
    a.date,a.page_id,b.page_name,
    count(1) as pv,
    count(distinct gu_id) as uv
    from dw.fct_page_ref a
    left join dw.dim_page b on a.page_id = b.page_id
    where date = "2017-09-20"
    group by a.date,a.page_id,b.page_name;
```

### 执行日志

```shell
Query ID = vk_20170922102424_c2e3e956-1c3f-41de-86b6-8531b57a23ab
Total jobs = 1
Execution log at: /tmp/vk/vk_20170922102424_c2e3e956-1c3f-41de-86b6-8531b57a23ab.log
2017-09-22 10:25:01     Starting to launch local task to process map join;      maximum memory = 1908932608
....
...
```

### MapJoin 使用限制

 MapJoin 使用限制，必须是join中从表（子查询）数据比较小。所谓从表，及左外连接的右表，或者右外连接的左表。