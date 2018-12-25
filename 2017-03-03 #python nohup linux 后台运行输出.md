
## 遇到问题
```
nohup python flush.py &
```
这样运行，生成了nohup.out文件，但是内容始终是空的，试了半天也不行。浪费了不少时间。

## 原因
> python的输出又缓冲，导致out.log并不能够马上看到输出。

> -u 参数，使得python不启用缓冲。

## 解决
```
nohup python -u flush.py > flush.log 2>&1 &
```

终于好了