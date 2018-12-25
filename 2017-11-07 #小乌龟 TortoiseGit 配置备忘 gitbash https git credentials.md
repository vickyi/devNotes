[TOC]

# gitbash https git credentials 小乌龟 TortoiseGit 配置备忘


## 小乌龟 TortoiseGit
[git credentials Docs](https://git-scm.com/docs/gitcredentials): https://git-scm.com/docs/gitcredentials

参考官方创建了 .git-credentials 文件，怎么都不生效。遇见好几种错误：
```
- fatal: unable to access 'https://gitlab.example.org/bi-source/hiveUDF.git/': Couldn't resolve host 'gitlab.example.org'
- fatal: unable to access 'https://gitlab.example.org/bi-source/hiveUDF.git/': The requested URL returned error: 500
```

最后啊最好，还是用小乌龟才终于解决问题。

- [小乌龟官网](https://tortoisegit.org)
- [	for 64-bit Windows:TortoiseGit-2.5.0.0-64bit.msi](https://download.tortoisegit.org/tgit/2.5.0.0/TortoiseGit-2.5.0.0-64bit.msi)
- [for 32-bit Windows:TortoiseGit-2.5.0.0-32bit.msi](https://download.tortoisegit.org/tgit/2.5.0.0/TortoiseGit-2.5.0.0-32bit.msi)

## 小乌龟配置

安装就不多说了吧，按照提示一步步点就好了。


### 关键配置
#### 第1步：找到配置
![第一步：找到配置](http://olx1ji9hn.bkt.clouddn.com/image/tortoiseGit-settings.png)

#### 第2步：配置htps的证书：
![](http://olx1ji9hn.bkt.clouddn.com/image/tortoiseGit-set-credential%281%29.png)

#### 第3步：配置git账户信息
![](http://olx1ji9hn.bkt.clouddn.com/image/tortoiseGit-set-userInfol%281%29.png)

## 访问https验证

### 小乌龟界面验证
先用小乌龟的界面pull代码，系统会弹出一个用户名密码的输入框，输入自己在*https://gitlab.example.org*上的用户名密码。

pull代码成功。

### gitbash 更新代码
```
git pull origin master
```
**成功！**


## 小乌龟创建的配置文件
上面设置小乌龟以后，打开gitbash，在用户目录下
```
cd ~
cat .gitconfig
```

其内容如下：
``` shell
[user]
        name = bibi
[user]
        email = bibi@example.com
[credential "https://gitlab.example.org"]
        helper = manager
        username = 比比
        useHttpPath = true

```

**跟 【git credentials 官方文档】文档的配置有较大差异。但是却是有效的。花了不少时间帮同事配置这个，特写此文档备忘。**