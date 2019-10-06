[TOC]

## Crontab

参　　数：
- -e 　编辑该用户的计时器设置。
- -l 　列出该用户的计时器设置。
- -r 　删除该用户的计时器设置。
- -u<用户名称> 　指定要设定计时器的用户名称。

crontab 格式

基本格式:
* *　 *　 *　 *　　command
分　时　日　月　周　 命令

- 第1列表示分钟1～59 每分钟用*或者 */1表示
- 第2列表示小时1～23（0表示0点）
- 第3列表示日期1～31
- 第4列 表示月份1～12
- 第5列标识号星期0～6（0表示星期天）
- 第6列要运行的命令

## Best Online Crontab Editor
**[Original Online Crontab Editor](http://www.corntab.com/)**
每一个crontab配置，都会有详细的说明。废话不如上图。

## 主页
![image](E:\BaiduYunPanRsync\百度云同步盘\image\写作配图\online-crontab-editor.png)

## 列子1：`5 1,5-22 * * *`
![image](E:\BaiduYunPanRsync\百度云同步盘\image\写作配图\crontab-editor.png)

## 列子2 `*/5 1-2 * * *`
![image](E:\BaiduYunPanRsync\百度云同步盘\image\写作配图\crontab-editor-default.png)

## 网络历史
![image](E:\BaiduYunPanRsync\百度云同步盘\image\写作配图\crontab-editor-recent.png)