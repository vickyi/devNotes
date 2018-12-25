Hive Analytics Functions : row_number rank over(partition by )
========

在做数据分析是，会统计用户访问app不同页面的开始时间，理论上同一个用户访问不同页面的开始时间应该不同。为了排查app端是否存在bug，用到row_number() over () 窗口函数。

## row_number over
sql如下
```
select
a.gu_id,starttime,
row_number() over (partition by gu_id order by starttime) rn
from test.fct_path_list_off_5levels a
where a.date = '2017-07-19'
and gu_id = "00000000-0000-0030-ffff-ffffef748aff";
```

效果如下：
a.gu_id	                             | starttime	 | rn
------------------------------------ | ------------- | --
00000000-0000-0030-ffff-ffffef748aff | 1500437284798 | 1
00000000-0000-0030-ffff-ffffef748aff | 1500437284798 | 2
00000000-0000-0030-ffff-ffffef748aff | 1500437285979 | 3
00000000-0000-0030-ffff-ffffef748aff | 1500437286845 | 4
00000000-0000-0030-ffff-ffffef748aff | 1500437287872 | 5
00000000-0000-0030-ffff-ffffef748aff | 1500437290902 | 6
00000000-0000-0030-ffff-ffffef748aff | 1500437291624 | 7
00000000-0000-0030-ffff-ffffef748aff | 1500437298189 | 8
00000000-0000-0030-ffff-ffffef748aff | 1500437316584 | 9
00000000-0000-0030-ffff-ffffef748aff | 1500437328388 | 10
00000000-0000-0030-ffff-ffffef748aff | 1500437328548 | 11
00000000-0000-0030-ffff-ffffef748aff | 1500437340583 | 12
00000000-0000-0030-ffff-ffffef748aff | 1500448200225 | 13
00000000-0000-0030-ffff-ffffef748aff | 1500448202219 | 14
00000000-0000-0030-ffff-ffffef748aff | 1500448202987 | 15


根据这个结果，我们可以知道，尽管有相同的记录，row_number还是会根据order by字段依次递增编号，且编号不重复。

## rank() OVER 
sql如下
```
select
a.gu_id,starttime,
rank() OVER (partition by gu_id order by starttime) rn
from test.fct_path_list_off_5levels a
where a.date = '2017-07-19'
and gu_id = "00000000-0000-0030-ffff-ffffef748aff";
```

结果如下：
a.gu_id	                             | starttime	 | rn
------------------------------------ | ------------- | --
00000000-0000-0030-ffff-ffffef748aff | 1500437284798 | 1
00000000-0000-0030-ffff-ffffef748aff | 1500437284798 | 1
00000000-0000-0030-ffff-ffffef748aff | 1500437285979 | 3
00000000-0000-0030-ffff-ffffef748aff | 1500437286845 | 4
00000000-0000-0030-ffff-ffffef748aff | 1500437287872 | 5
00000000-0000-0030-ffff-ffffef748aff | 1500437290902 | 6
00000000-0000-0030-ffff-ffffef748aff | 1500437291624 | 7
00000000-0000-0030-ffff-ffffef748aff | 1500437298189 | 8
00000000-0000-0030-ffff-ffffef748aff | 1500437316584 | 9
00000000-0000-0030-ffff-ffffef748aff | 1500437328388 | 10
00000000-0000-0030-ffff-ffffef748aff | 1500437328548 | 11
00000000-0000-0030-ffff-ffffef748aff | 1500437340583 | 12
00000000-0000-0030-ffff-ffffef748aff | 1500448200225 | 13
00000000-0000-0030-ffff-ffffef748aff | 1500448202219 | 14
00000000-0000-0030-ffff-ffffef748aff | 1500448202987 | 15

对比row_number 和 rank() OVER 的结果，可以发现，rank在处理相同的记录时候，编号是一样的，同时编号就不再连续，直到遇到有差异的记录。

### Hive 官方文档
[Hive LanguageManual+WindowingAndAnalytics](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+WindowingAndAnalytics)