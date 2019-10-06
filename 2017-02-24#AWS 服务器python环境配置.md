## [Python 安装](https://www.python.org)
```sh
#!/bin/bash

cd ~
mkdir py-env
mkdir ~/dev-tools
cd ~/dev-tools

wget https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tgz --no-check-certificate

gunzip -d Python-2.7.13.tgz

tar xvf Python-2.7.13.tar

cd ~/dev-tools/Python-2.7.13

mkdir -p ~/dev/python

## [aws](https://aws.amazon.com/cn/getting-started/tutorials/launch-a-virtual-machine/)
## 如果使用的是AWS，需要自己安装gcc
sudo yum install gcc

## prefix 指定安装的目标路径，此处要用绝对路径
./configure --prefix=/home/ec2-user/dev-tools/python
 
 sudo make && sudo make install
 
```

## setuptools
```
cd ~/dev-tools

wget https://pypi.python.org/packages/source/s/setuptools/setuptools-4.0.1.tar.gz#md5=190b1d4470de9bae0b4414353e14700d --no-check-certificate tar xvf setuptools-4.0.1.tar.gz 

```

## [源码安装pip](https://pypi.python.org/pypi/pip/)
```
cd ~/dev-tools

wget https://pypi.python.org/packages/11/b6/abcb525026a4be042b486df43905d6893fb04f05aac21c32c638e939e447/pip-9.0.1.tar.gz --no-check-certificate 

OR

#md5=35f01da33009719497f01a4ba69d63c9
curl -O https://pypi.python.org/packages/11/b6/abcb525026a4be042b486df43905d6893fb04f05aac21c32c638e939e447/pip-9.0.1.tar.gz

tar xvf pip-1.5.6.tar.gz

python setup.py install
```

## [源码安装zlib](http://www.zlib.net/)
```
curl -O http://www.zlib.net/zlib-1.2.11.tar.gz

tar xvfz zlib-1.2.11.tar.gz

./configure
 
 sudo make && sudo make install
```