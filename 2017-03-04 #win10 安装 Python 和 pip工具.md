## win10 安装 Python 
 
### 下载

选择版本：
 [Latest Python 2 Release - Python 2.7.13](https://www.python.org/downloads/release/python-2713/)
 
![Python 2.7.13](http://olx1ji9hn.bkt.clouddn.com/image/python2.7.13.png)
 
### 安装
 windows下的软件默认安装，基本就一句话：下一步。
 
 我选择的安装路径是D盘。
 
 安装好了以后还需要配置系统环境变量：
 ![配置](http://olx1ji9hn.bkt.clouddn.com/image/win-python2.7.13.png)
 
### 安装pip
> pip 是Python下最出名、最实用、最强大的包管理工具。也是 `The PyPA recommended tool for installing Python packages.`

[关于pip](https://pypi.python.org/pypi/pip)

[Python 2.7.9](https://docs.python.org/2/whatsnew/2.7.html#pep-477-backport-ensurepip-pep-453-to-python-2-7) (released December 2014)以上或者 [Python 3.4](https://docs.python.org/3/whatsnew/3.4.html) (released March 2014)以上的版本，Python已经将pip工具包含进去了：
![](http://olx1ji9hn.bkt.clouddn.com/image/python2.7.13-pip.png)

### 安装 virtualenv

/c/Python27/Scripts/pip.exe install virtualenv

#### 激活 virtualenv
mkdir -p /e/pydev
cd pydev
/c/Python27/Scripts/virtualenv.exe venv

source ./e/pydev/venv/Scripts/activate