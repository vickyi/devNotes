[TOC]

## 0. 下载 path_list 项目代码

### 第一次同步项目
```
git clone https://gitlab.abcplus.org/bi-source/path_list.git
```

### 已经存在path_list项目
```
git checkout develop

git pull origin develop
```

拿到最新的代码以后，导入到Eclipse 或者 Intellij IDEA。这是基本功，此处不再赘述。有问题直接call我。


## 1. 建表
执行test_fct_path_list_off_5levels下的文件
1. 执行 create_fct_path_compute_input.sql    建表：test.fct_path_compute_input
2. 执行 create_fct_path_list_off_5levels.sql 建表：执行 test.fct_path_list_off_5levels


## 2. path_list 改动

以下改动见具体的代码。此处只是将需要改动的java文件罗列出来。

1. PathComputeDriver.java : 方法 jobConstructor 修改 orc.mapred.output.schema的定义
2. PathMapper.java ： 方法 map 组装的key-value的中增加字段
3. PathReducer.java ：修改schema，同时修改方法 initLevl 和 setLevl，分别是初始化变量和赋值

## 3. git提交所有改动

项目在gitlab的地址 ：https://gitlab.abcplus.org/bi-source/path_list

修改完代码以后，将代码提交到gitlab。如果已经提交，就可以跳过这个步骤。

## 4. 打包项目path_lsit

```
mvn clean package -DskipTests -pl pathlist
```

打包成功后应该能在 pathlist/target 目录下找到一个 pathlist-1.0.jar 的文件。先记下，后面会用到。


## 5. 上传文件至服务器

首先，登陆hive001，创建一个目录

```
mkdir -p test_path_list/script
```

然后，将下列文件上传到 test_path_list 目录下：
1. fct_path_list_off_5levels.sh 上传至目录 test_path_list
2. merge_page_event.sql 上传至目录 test_path_list/script
3. load_data_to_5levels.sql 上传至目录 test_path_list/script
4. 将 pathlist-1.0.jar 上传至目录 test_path_list

## 6. 开始测试

```
nohup sh ./fct_path_list_off_5levels.sh 2017-11-15 &
```

这个 fct_path_list_off_5levels.sh 文件做了几件事情：
1. 执行 ./script/merge_page_event.sql，将 dw.fct_page_ref_reg 和 dw.fct_event_reg 的数据 合并到一张新表：test.fct_path_compute_input，并且是按天存放。
2. 执行 pathlist-1.0.jar，读取表 test.fct_path_compute_input中的文件，进行路径计算，并将数据写到指定的日期目录
3. 删除 test.fct_path_list_off_5levels 指定日期中下的文件
4. 执行 ./script/load_data_to_5levels.sql，将 pathlist-1.0.jar 输出的文件加载到表 test.fct_path_list_off_5levels

以上命令会默认将执行的过程信息写到当前目录下的 nohup.out 文件。实时查看日志输出：
```
tailf nohup.out
或者
tail -300f nohup.out
```


## 6. 数据质量检查
如果上面的步骤没有报错，接下来还需要验证数据质量，这里给出两个sql示例：

```
select a.date, a.* from test.fct_path_list_off_5levels a where date = "2017-11-15";
```


```
select a.date, a.entrance_page_id, count(1) as pv, count(distinct gu_id) as uv
from test.fct_path_list_off_5levels a
where a.date = "2017-11-15"
group by a.date, a.entrance_page_id;
```

## 联系方式
10086