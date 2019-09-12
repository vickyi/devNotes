# docker cp host/path:/container/path

[docker 命令](Docker命令.md)

## tomcat

### 安装 tomcat 镜像

[Docker tomcat image](https://hub.docker.com/_/tomcat?tab=description)

```bash
Run the default Tomcat server (CMD ["catalina.sh", "run"]):

$ docker run -it --rm tomcat:8.0
You can test it by visiting http://container-ip:8080 in a browser or, if you need access outside the host, on port 8888:

$ docker run -it --rm -p 8888:8080 tomcat:8.0
You can then go to http://localhost:8888 or http://host-ip:8888 in a browser.

The default Tomcat environment in the image for versions 7 and 8 is:

CATALINA_BASE:   /usr/local/tomcat
CATALINA_HOME:   /usr/local/tomcat
CATALINA_TMPDIR: /usr/local/tomcat/temp
JRE_HOME:        /usr
CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
The default Tomcat environment in the image for version 6 is:

CATALINA_BASE:   /usr/local/tomcat
CATALINA_HOME:   /usr/local/tomcat
CATALINA_TMPDIR: /usr/local/tomcat/temp
JRE_HOME:        /usr
CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar

The configuration files are available in /usr/local/tomcat/conf/. By default, no user is included in the "manager-gui" role required to operate the "/manager/html" web application. If you wish to use this app, you must define such a user in tomcat-users.xml.

```

### 查看所有 docker images

```bash
docker images
```

### 查看 container-id

```bash
docker ps
```

### 查看 container-ip

```bash
docker insept container-id
```

### 进入tomcat 镜像

```bash
docker exec -it 89c525ad8ac3 b
```

### 镜像加速

[Docker 中国官方镜像加速](https://www.docker-cn.com/registry-mirror)

Docker 中国官方镜像加速可通过 registry.docker-cn.com 访问。该镜像库只包含流行的公有镜像。私有镜像仍需要从美国镜像库中拉取。

您可以使用以下命令直接从该镜像加速地址进行拉取，格式如下：

```bash
docker pull registry.docker-cn.com/myname/myrepo:mytag
```

例如；

```bash
docker pull registry.docker-cn.com/library/ubuntu:16.04
```

#### 使用 --registry-mirror 配置 Docker 守护进程

您可以配置 Docker 守护进程默认使用 Docker 官方镜像加速。这样您可以默认通过官方镜像加速拉取镜像，而无需在每次拉取时指定 registry.docker-cn.com。

您可以在 Docker 守护进程启动时传入 --registry-mirror 参数：

```bash
docker --registry-mirror=https://registry.docker-cn.com daemon
```

为了永久性保留更改，您可以修改 /etc/docker/daemon.json 文件并添加上 registry-mirrors 键值。

```json
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

修改保存后重启 Docker 以使配置生效。

### docker怎么修改tomcat的配置文件

> 需要安装 vim，在进入镜像后才能执行安装命令，请参考后面工具安装部分。

1. -v 挂载 docker run -v localConfigFile:/containerPath
2. exec 进入容器修改 docker exec -it containerID bash 可以安装vi进行修改
3. cp 可以将宿主机的配置文件拷贝到container中， docker cp host/path:/container/path

建议使用第三个

### 部署 war

```bash
docker cp host/path:/container/path

eg:
    cd /Users/aiplus/Documents/gitlab/oneData/aiplus-web/target
    docker cp oneData2.war 68b7e865e256:/usr/local/tomcat/webapps/
```

#### Tomcat容器部署应用有两种方式。

1. 静态部署: 把你准备好的war包上传到tomcat/webapps目录下即可。

2. 动态部署: 可以在服务器启动之后部署web应用程序，而不用重新启动服务器。需要你修改tomcat/conf/tomcat-users.xml 新增一个manager-gui的角色role:
    ```xml
    <role rolename="manager-gui"/>
    <user username="tomcat" password="tomcat" roles="manager-gui"/>
    ```


## docker容器安装工具

### 安装 vim

```bash
apt-get update
apt-get install vim
```