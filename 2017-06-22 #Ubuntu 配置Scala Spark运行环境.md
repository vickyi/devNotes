### Ubuntu 配置spark运行环境

#### 源设置
```
sudo vi /etc/apt/sources.list

deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse

deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security multiverse
```
```
sudo apt-get update
```

http://spark.apache.org/downloads.html

Java SE Development Kit 8 Downloads
http://www.oracle.com/technetwork/java/javase/downloads/index.html
http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz

https://www.python.org/downloads/windows/

http://www.scala-sbt.org/download.html
http://www.scala-sbt.org/0.13/docs/Setup.html

http://www.scala-lang.org/download/

http://www.jetbrains.com/idea/
http://www.jetbrains.com/idea/download/download-thanks.html?platform=linuxWithoutJDK&code=IIC

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
cd ~spa

wget http://mirrors.cnnic.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz

tar xzf apache-maven-3.1.1-bin.tar.gz
```

##### 配置Java Scala Spark环境变量
```
vim .bash_profile
## 增加以下两行
// maven解压缩后的目录

export SBT_HOME=/home/vk/Documents/dev-env/sbt
export M2_HOME=/home/vk/Documents/dev-env/apache-maven-3.3.9
export SPARK_HOME=/home/vk/Documents/dev-env/spark-1.6.1-bin-hadoop2.6
export JAVA_HOME=/home/vk/Documents/dev-env/jdk1.8.0_111
export IDEA_HOME=/home/vk/Documents/dev-env/idea-IU-171.4694.23

export PATH=$M2_HOME/bin:$SPARK_HOME/bin:$SBT_HOME/bin:$JAVA_HOME/bin:$IDEA_HOME/bin:$PATH

vim ~/.bashrc
source ~/.bashrc
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