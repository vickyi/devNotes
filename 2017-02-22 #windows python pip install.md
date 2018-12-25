# windows 下使用python pip install

参考：[How do I install pip on Windows?](http://stackoverflow.com/questions/4750806/how-do-i-install-pip-on-windows)

## Python 2.7.9+ and 3.4+

Good news! [Python 3.4](https://docs.python.org/3/whatsnew/3.4.html) (released March 2014) and [Python 2.7.9](https://docs.python.org/2/whatsnew/2.7.html#pep-477-backport-ensurepip-pep-453-to-python-2-7) (released December 2014) ship with Pip. This is the best feature of any Python release. It makes the community's wealth of libraries accessible to everyone. Newbies are no longer excluded from using community libraries by the prohibitive difficulty of setup. In shipping with a package manager, Python joins Ruby, Node.js, Haskell, Perl, Go--almost every other contemporary language with a majority open-source community. 

Thank you Python.

好消息！好消息！

[Python 2.7.9](https://docs.python.org/2/whatsnew/2.7.html#pep-477-backport-ensurepip-pep-453-to-python-2-7) (released December 2014)以上或者 [Python 3.4](https://docs.python.org/3/whatsnew/3.4.html) (released March 2014)以上的版本，Python已经将pip工具包含进去了：



终于可以愉快的使用`pip install`Python包了：

```
 ./pip2.7.exe install pelican
```
![image](E:\BaiduYunPanRsync\百度云同步盘\image\写作配图\win10-python-pip.png)

```
./pip2.7.exe install markdown
```
![image](E:\BaiduYunPanRsync\百度云同步盘\image\写作配图\win10-pip-install.png)

Of course, that doesn't mean Python packaging is problem solved. The experience remains frustrating. [I discuss this in Stack Overflow question Does Python have a package/module management system?](http://stackoverflow.com/questions/2436731/does-python-have-a-package-module-management-system/13445719#13445719).

And, alas for everyone using Python 2.7.8 or earlier (a sizable portion of the community). There's no plan to ship Pip to you. Manual instructions follow.

Python 2 ≤ 2.7.8 and Python 3 ≤ 3.3

Flying in the face of its 'batteries included' motto, Python ships without a package manager. To make matters worse, Pip was--until recently--ironically difficult to install.

## Official instructions 官方推荐

Per https://pip.pypa.io/en/stable/installing/#do-i-need-to-install-pip:

Download get-pip.py, being careful to save it as a .py file rather than .txt. Then, run it from the command prompt:

```
python get-pip.py
```
如果安装失败，就以管理员权限打开cmd，再来执行该命令。

## 可选的方案

The official documentation tells users to install Pip and each of its dependencies from source. That's tedious for the experienced, and prohibitively difficult for newbies.

For our sake, Christoph Gohlke prepares Windows installers (.msi) for popular Python packages. He builds installers for all Python versions, both 32 and 64 bit. You need to

1. [Install setuptools](http://www.lfd.uci.edu/~gohlke/pythonlibs/#setuptools)
2. [Install pip](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pip)

For me, this installed Pip at D:\Python27\Scripts\pip2.7.exe. Find pip.exe on your computer, then add its folder (for example, C:\Python27\Scripts) to your path (Start / Edit environment variables). Now you should be able to run pip from the command line. Try installing a package:

```
pip install httpie
```

There you go (hopefully)! Solutions for common problems are given below:

## Proxy problems 代理问题

If you work in an office, you might be behind a HTTP proxy. If so, set the environment variables http_proxy and https_proxy. Most Python applications (and other free software) respect these. Example syntax:

http://proxy_url:port
http://username:password@proxy_url:port
If you're really unlucky, your proxy might be a Microsoft NTLM proxy. Free software can't cope. The only solution is to install a free software friendly proxy that forwards to the nasty proxy. http://cntlm.sourceforge.net/

## Unable to find vcvarsall.bat

Python modules can be part written in C or C++. Pip tries to compile from source. If you don't have a C/C++ compiler installed and configured, you'll see this cryptic error message.

`Error: Unable to find vcvarsall.bat`

You can fix that by installing a C++ compiler such as MinGW or Visual C++. Microsoft actually ship one specifically for use with Python. Or try Microsoft Visual C++ Compiler for Python 2.7.

Often though it's easier to check Christoph's site for your package.