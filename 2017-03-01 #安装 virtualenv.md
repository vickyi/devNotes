## 安装 [virtualenv](https://virtualenv.pypa.io/en/stable/)

- [下载主页](https://pypi.python.org/pypi/virtualenv)
- [项目主页](https://virtualenv.pypa.io/en/stable/)
- [Installation doc](https://virtualenv.pypa.io/en/stable/installation/)

## 下载
```
mkdir ~/dev-tools

cd ~/dev-tools

#md5=44e19f4134906fe2d75124427dc9b716
curl -O https://pypi.python.org/packages/d4/0c/9840c08189e030873387a73b90ada981885010dd9aea134d6de30cd24cb8/virtualenv-15.1.0.tar.gz
```

## 解压
```
tar xvfz virtualenv-15.1.0.tar.gz
```

## To install version X.X globally from source
```
sudo python setup.py install
```

## To use locally from source:
```
python virtualenv.py myVE
```

## 指定Python 创建 env
```
## 
 cd ~
 
 virtualenv py-env --no-site-packages --python=/home/ec2-user/dev-tools/python/bin/python
 
```

## 添加到系统环境变量
```
vim  ~/.bashrc

## 将下面这段代码添加进去，这样每次登陆系统就会自动激活virtualenv
source ~/py-env/bin/activate
```