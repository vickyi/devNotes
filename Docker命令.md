# Docker命令

## Docker cp 命令

```bash
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-

docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```

eg:

将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下:

```bash
docker cp /www/runoob 96f7f14e99ab:/www/
```

将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www:

```bash
docker cp /www/runoob 96f7f14e99ab:/www
```

将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中:

```bash
docker cp  96f7f14e99ab:/www /tmp/
```

参考：

[Docker cp 命令](http://www.runoob.com/docker/docker-cp-command.html)
