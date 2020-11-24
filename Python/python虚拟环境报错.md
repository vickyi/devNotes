## Error while finding module specification for 'virtualenvwrapper.hook_loader'

```
(base)  vkzhang@zhangjiqiangs-MacBook-Pro  ~  /bin/zsh
/Users/vkzhang/opt/anaconda3/bin/python: Error while finding module specification for 'virtualenvwrapper.hook_loader' (ModuleNotFoundError: No module named 'virtualenvwrapper')
virtualenvwrapper.sh: There was a problem running the initialization hooks.

If Python could not import the module virtualenvwrapper.hook_loader,
check that virtualenvwrapper has been installed for
VIRTUALENVWRAPPER_PYTHON=/Users/vkzhang/opt/anaconda3/bin/python and that PATH is
set properly.
(base)  vkzhang@zhangjiqiangs-MacBook-Pro  ~  /Users/vkzhang/opt/anaconda3/bin/python
Python 3.8.3 (default, Jul  2 2020, 11:26:31)
[Clang 10.0.0 ] :: Anaconda, Inc. on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> exit()

(base)  vkzhang@zhangjiqiangs-MacBook-Pro  ~  python -m pip install --user virtualenvwrapper --upgrade
DEPRECATION: Python 2.7 will reach the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 won't be maintained after that date. A future version of pip will drop support for Python 2.7.
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting virtualenvwrapper
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/c1/6b/2f05d73b2d2f2410b48b90d3783a0034c26afa534a4a95ad5f1178d61191/virtualenvwrapper-4.8.4.tar.gz (334kB)
     |████████████████████████████████| 337kB 103kB/s
Requirement already satisfied, skipping upgrade: virtualenv in /Library/Python/2.7/site-packages (from virtualenvwrapper) (15.2.0)
Requirement already satisfied, skipping upgrade: virtualenv-clone in /Library/Python/2.7/site-packages (from virtualenvwrapper) (0.3.0)
Requirement already satisfied, skipping upgrade: stevedore in /Library/Python/2.7/site-packages (from virtualenvwrapper) (1.28.0)
Requirement already satisfied, skipping upgrade: pbr!=2.1.0,>=2.0.0 in /Library/Python/2.7/site-packages (from stevedore->virtualenvwrapper) (4.0.1)
Requirement already satisfied, skipping upgrade: six>=1.10.0 in /Library/Python/2.7/site-packages (from stevedore->virtualenvwrapper) (1.11.0)
Building wheels for collected packages: virtualenvwrapper
  Building wheel for virtualenvwrapper (setup.py) ... done
  Stored in directory: /Users/vkzhang/Library/Caches/pip/wheels/f0/eb/c4/19a6c0366b7d3654385c16559db7dcad8d9169d1662782e988
Successfully built virtualenvwrapper
Installing collected packages: virtualenvwrapper
Successfully installed virtualenvwrapper-4.8.4
```
