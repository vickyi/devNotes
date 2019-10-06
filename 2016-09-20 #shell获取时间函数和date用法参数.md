# shell

## shell系统变量

```txt
shell下获取系统时间的方法直接调用系统变量
获取今天时期：`date +%Y%m%d` 或 `date +%F` 或 $(date +%y%m%d)
获取昨天时期：`date -d yesterday +%Y%m%d`
获取前天日期：`date -d -2day +%Y%m%d`
依次类推比如获取10天前的日期：`date -d -10day +%Y%m%d`
或n天前的 `date -d "n days ago" +%y%m%d`
明天：`date -d tomorrow +%y%m%d`
注意以上中间有空格
时间域
% H 小时（00..23）
% I 小时（01..12）
% k 小时（0..23）
% l 小时（1..12）
% M 分（00..59）
% p 显示出AM或PM
% r 时间（hh：mm：ss AM或PM），12小时
% s 从1970年1月1日00：00：00到目前经历的秒数
% S 秒（00..59）
% T 时间（24小时制）（hh:mm:ss）
% X 显示时间的格式（％H:％M:％S）
% Z 时区 日期域
% a 星期几的简称（ Sun..Sat）
% A 星期几的全称（ Sunday..Saturday）
% b 月的简称（Jan..Dec）
% B 月的全称（January..December）
% c 日期和时间（ Mon Nov 8 14：12：46 CST 1999）
% d 一个月的第几天（01..31）
% D 日期（mm／dd／yy）
% h 和%b选项相同
% j 一年的第几天（001..366）
% m 月（01..12）
% w 一个星期的第几天（0代表星期天）
% W 一年的第几个星期（00..53，星期一为第一天）
% x 显示日期的格式（mm/dd/yy）
% y 年的最后两个数字（ 1999则是99）
% Y 年（例如：1970，1996等）
注意：只有超级用户才有权限使用date命令设置时间，一般用户只能使用date命令显示时间
```

## shell 时间函数

```txt
=============================================时间函数=============================================================
######################################
#SHELL日期计算函数 #
#1:判断是否闰年check_leap() #
#2:获取月份最大日期get_mon_days() #
#3:检查日期格式check_date() #
#4:返回昨天日期get_before_date() #
#5:返回明天日期get_next_date() #
#6:返回当月月末日期YYYYMMDD get_cur_date()
#7:返回当月月份YYYYMM get_cur_month()
#8:返回上月月末日期YYYYMMDD get_last_date()
#9:返回上月月份YYYYMM get_last_month()
######################################
#-----------------------------------------------------------------
#判断是否闰年
#input:year
#output: "true" "fase"
check_leap()
{
Y=`expr substr $1 1 4`
r1=`expr $Y % 4`
r2=`expr $Y % 100`
r3=`expr $Y % 400`
if [ $r1 -eq 0 -a $r2 -ne 0 -o $r3 -eq 0 ]
then
FRUN="true"
else
FRUN="false"
fi
echo $FRUN
}
#-----------------------------------------------------------------

# 获取月份最大日期
#方法1
get_mon_days()
{
Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`

case "$M" in
01|03|05|07|08|10|12) days=31;;
04|06|09|11) days=30;;
02)
_tmpStr=`check_leap "$Y"` #判断是否闰年
if [ "$_tmpStr" = "true" ] ; then
#闰年
days=29
else
days=28
fi
;;
*)
days=0
;;
esac
echo $days
}
#-----------------------------------------------------------------
# 获取月份最大日期
#方法2
get_mon_days2()
{
Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`

#取当月底最后一天
aa=`cal $M $Y` #日历
days=`echo $aa | awk '{print $NF}'`
echo $days
}

#检查日期格式(例:20090227)
#返回状态($?) 0 合法 1 非法
check_date()
{
#检查是否传入一个参数
[ $# -ne 1 ] &&echo 1&&exit 1

#检查字符串长度
_lenStr=`expr length "$1"`
[ "$_lenStr" -ne 8 ] &&echo 1&&exit 1

#检查是否输入的是非0开头的数字
_tmpStr=`echo "$1" | grep "^[^0][0-9]*$"`
[ -z "$_tmpStr" ] &&echo 1&&exit 1

Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`
D=`expr substr $1 7 2`
#检查月份
[ "$M" -lt 1 -o "$M" -gt 12 ] &&echo 1&&exit 1
#取当月天数
days=`get_mon_days "$Y$M"`
#检查日
[ "$D" -lt 1 -o "$D" -gt "$days" ] &&echo 1&&exit 1

echo 0
}
#-----------------------------------------------------------------

#返回昨天日期
get_before_date()
{
Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`
D=`expr substr $1 7 2`

#某月01日的情况
if [ "$D" -eq 01 ]
then
if [ "$M" -eq 01 ]
then
#某年01月01日的情况
#取上年年底日期(12月31日)
YY=`expr $Y - 1`
be_date="${YY}1231"
else
#取上个月月末日期
MM=`expr $M - 1`
MM=`printf "%02d" $MM`
dad=`get_mon_days "$Y$MM"`
be_date="$Y$MM$dad"
fi
else
#通常情况
DD=`expr $D - 1`
DD=`printf "%02d" $DD`
be_date="$Y$M$DD"
fi
echo $be_date
}
#-----------------------------------------------------------------

#返回明天日期
get_next_date()
{
Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`
D=`expr substr $1 7 2`

dad=`get_mon_days "$Y$M"` #当月天数

if [ "$D" = "$dad" ];then
#特殊情况:月底
if [ "$M$D" = "1231" ];then
#年底的情况
YY=`expr $Y + 1`
next_date="${YY}0101"
else
MM=`expr $M + 1`
MM=`printf "%02d" $MM`
next_date="$Y${MM}01"
fi
else
#通常情况
DD=`expr $D + 1`
DD=`printf "%02d" $DD`
next_date="$Y$M$DD"
fi

echo $next_date
}
#-----------------------------------------------------------------
#返回当月月末日期YYYYMMDD
get_cur_date()
{
Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`
r1=`expr $Y % 4`
r2=`expr $Y % 100`
r3=`expr $Y % 400`
case $M in 01|03|05|07|08|10|12) days=31;; 04|06|09|11) days=30;;
esac
if [ $M -eq 02 ]
then if [ r1 -eq 0 -a r2 -ne 0 -o r3 -eq 0 ]
then days=29
else days=28
fi
fi
echo $Y$M$days
}
#返回当月月份YYYYMM
get_cur_month()
{
Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`
echo $Y$M
}
#返回上月月末日期YYYYMMDD
get_last_date()
{
Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`
M=`expr $M "-" 1`
if [ $M -eq 0 ]
then
let Y=Y-1
M=12
else M=`printf "%02d" $M`
fi
r1=`expr $Y % 4`
r2=`expr $Y % 100`
r3=`expr $Y % 400`
case $M in 01|03|05|07|08|10|12) days=31;; 04|06|09|11) days=30;;
esac
if [ $M -eq 02 ]
then if [ r1 -eq 0 -a r2 -ne 0 -o r3 -eq 0 ]
then days=29
else days=28
fi
fi
echo $Y$M$days
}
#返回上月月份YYYYMM
get_last_month()
{
Y=`expr substr $1 1 4`
M=`expr substr $1 5 2`
M=`expr $M "-" 1`
if [ $M -eq 0 ]
then
let Y=Y-1
M=12
else M=`printf "%02d" $M`
fi
echo $Y$M
}

```
