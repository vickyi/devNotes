### CentOS 上 Java Scala 开发环境配置

#### maven 安装配置

官网地址：http://maven.apache.org/

##### install方式

```
# centos
yum install -y maven
```

#### 下载tar包
```
cd ~

wget http://mirrors.cnnic.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz

tar xzf apache-maven-3.1.1-bin.tar.gz
```

##### 配置当前用户的环境变量
```
vim .bash_profile
## 增加以下两行
// maven解压缩后的目录
export M2_HOME=/data/home/vk/dev_env/apache-maven-3.3.9/
PATH=$M2_HOME/bin:$PATH

```

设置好Maven的路径之后，需要运行下面的命令：
```
source .bash_profile
```

##### 如果有权限，想配置到系统全局变量
```
vim /etc/profile

## 增加以下两行
export M2_HOME='path_to_maven'
PATH=$M2_HOME/bin:$PATH
```


弄完之后，你可以运行下面的命令
```
mvn --version
```
如果出现以下信息：
> [vk@ABCBI-OPS-001 ~]$ mvn -v
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
Maven home: /usr/share/apache-maven
Java version: 1.7.0_79, vendor: Oracle Corporation
Java home: /usr/local/jdk1.7.0_79/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-431.el6.x86_64", arch: "amd64", family: "unix"

说明配置成功