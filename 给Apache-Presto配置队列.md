# 给Apache-Presto配置队列

## 找到 presto

```shell
which presto
# /usr/lib/presto-current/bin/presto

cd /usr/lib/presto-current/
```

找到配置文件`config.properties`，添加：

```xml
query.queue-config-file=/etc/ecm/presto-conf-0.188/queue_config.json
```

具体内容如下：

```json
{
    "queues": {
        "bi": {
            "maxConcurrent": 5,
            "maxQueued": 50
        },
        "admin": {
            "maxConcurrent": 5,
            "maxQueued": 100
        },
        "global": {
            "maxConcurrent": 3,
            "maxQueued": 5
        }
    },
    "rules": [
        {
            "user": "hadoop",
            "queues": [
                "bi"
            ]
        },
        {
            "queues": [
                "bi"
            ]
        }
    ]
}
```

这个配置中包含两部分，一个是queues ,一个是rules

### queues

队列定义在queues下，每个队列包含三个属性：

1. queuesname 队列的名称，例如`bi`
2. maxConcurrent:改队列允许同时运行查询的最大并发数量
3. maxQueued：改队列运行同时接受的提交查询请求的最大数量

### Rules的定义

规则定义在rules标签下，每个规则可以定义四个属性：

1. user：用户名，取值来源于session中的user，若在cli中默认为操作系统用户，presto jdbc为传入的用户名
2. source：sql来源，一般有两种，presto-cli和presto-jdbc
3. session：session中定义的参数，分为在System session中所包含的参数
4. queues：队列列表，可以定义多个

例如：

```json
{
    "user":"hive",
    "source":".*presto-cli.*",
    "queues":[
        "user.hive","global"
    ]
}
```

## 转自

作者：张峰
链接：<a href='https://www.jianshu.com/p/6cab21a04cc9'>https://www.jianshu.com/p/6cab21a04cc9</a>
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。