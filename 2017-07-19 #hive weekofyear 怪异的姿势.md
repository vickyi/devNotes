hive weekofyear 怪异的姿势
====
今天在使用hive函数weekofyear的时候遇到一个奇怪的情况，原sql如下：

```
select aa.w,count(distinct aa.user_id),count(distinct bb.user_id)
from
(
select weekofyear(date)w,user_id
from dw.fct_ordr_pay
where date>='2017-06-19' and date<='2017-07-09'
and terminal_id in(5)
group by weekofyear(date),user_id
)aa 
left join 
(
select weekofyear(date)w,user_id
from dw.fct_ordr_pay
where date>='2017-06-23' and date<='2017-07-16'
and terminal_id in(5)
group by weekofyear(date),user_id
)bb on aa.user_id=bb.user_id and aa.w+1=bb.w
group by aa.w
order by aa.w

```

执行过程中报错：
```
Diagnostic Messages for this Task:
Error: java.lang.RuntimeException: org.apache.hadoop.hive.ql.metadata.HiveException: Hive Runtime Error while processing row 
        at org.apache.hadoop.hive.ql.exec.mr.ExecMapper.map(ExecMapper.java:179)
        at org.apache.hadoop.mapred.MapRunner.run(MapRunner.java:54)
        at org.apache.hadoop.mapred.MapTask.runOldMapper(MapTask.java:453)
        at org.apache.hadoop.mapred.MapTask.run(MapTask.java:343)
        at org.apache.hadoop.mapred.YarnChild$2.run(YarnChild.java:164)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1693)
        at org.apache.hadoop.mapred.YarnChild.main(YarnChild.java:158)
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: Hive Runtime Error while processing row 
        at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.process(VectorMapOperator.java:52)
        at org.apache.hadoop.hive.ql.exec.mr.ExecMapper.map(ExecMapper.java:170)
        ... 8 more
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: Error evaluating weekofyear(date)
        at org.apache.hadoop.hive.ql.exec.vector.VectorSelectOperator.processOp(VectorSelectOperator.java:126)
        at org.apache.hadoop.hive.ql.exec.Operator.forward(Operator.java:815)
        at org.apache.hadoop.hive.ql.exec.vector.VectorFilterOperator.processOp(VectorFilterOperator.java:111)
        at org.apache.hadoop.hive.ql.exec.Operator.forward(Operator.java:815)
        at org.apache.hadoop.hive.ql.exec.TableScanOperator.processOp(TableScanOperator.java:95)
        at org.apache.hadoop.hive.ql.exec.MapOperator$MapOpCtx.forward(MapOperator.java:157)
        at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.process(VectorMapOperator.java:45)
        ... 9 more
Caused by: java.lang.NullPointerException
        at java.nio.HeapByteBuffer.<init>(HeapByteBuffer.java:70)
        at java.nio.ByteBuffer.wrap(ByteBuffer.java:369)
        at org.apache.hadoop.io.Text.decode(Text.java:389)
        at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorUDFWeekOfYearString.doGetField(VectorUDFWeekOfYearString.java:54)
        at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorUDFTimestampFieldString.getField(VectorUDFTimestampFieldString.java:63)
        at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorUDFTimestampFieldString.evaluate(VectorUDFTimestampFieldString.java:106)
        at org.apache.hadoop.hive.ql.exec.vector.VectorSelectOperator.processOp(VectorSelectOperator.java:124)
        ... 15 more
```

分块执行：
```
select weekofyear(date)w,user_id
from dw.fct_ordr_pay
where date>='2017-06-19' and date<='2017-07-09'
and terminal_id in(5)
group by weekofyear(date),user_id
```
**啊咧，WTF，同样的错误！！**

经过多次尝试，我发现只要where条件中加了terminal_id这个查询条件，就会报错。

遂将原sql改为如下：

```
select aa.w,aa.terminal_id,count(distinct aa.user_id),count(distinct bb.user_id)
from
(
select weekofyear(date) as w,user_id,terminal_id
from dw.fct_ordr_pay
where date>='2017-06-19' and date<='2017-07-09'
group by weekofyear(date),user_id,terminal_id
)aa 
left join 
(
select weekofyear(date)w,user_id,terminal_id
from dw.fct_ordr_pay
where date>='2017-06-23' and date<='2017-07-16'
group by weekofyear(date),user_id,terminal_id
)bb on aa.user_id=bb.user_id and aa.w+1=bb.w and aa.terminal_id=bb.terminal_id
group by aa.w,aa.terminal_id
order by aa.w,aa.terminal_id;
```

yeah，终于跑通了！

经验：凡事，不要在一棵树上吊死，去傍边的树上多试试。关键的时候，换个姿势说不定更顺利。
