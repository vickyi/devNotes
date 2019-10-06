### Linux和Windows和换行符不一样。
Windows下是CRLF(\r\n或0d0a)，Linux下是LF(\n或0a)。在Linux下有时会遇到从Windows过来的文本文件，这些文件带了Windows换行符，Linux下进行脚本处理时有可能会出一些莫名其妙的错误。因此需要将这些文件转换为Linux换行符。

之前都是用dos2unix命令转换，但是很多系统没装这个命令。

Linux自带的sed命令一样可以完成相同的任务。

### 单个的文件装换

```shell
sed -i 's/\r//'  filename
```

### 批量的文件装换

```shell
sed -i 's/\r//'  filename1 filename2 ...
```
或

```shell
find conf/  -name "*.*" |xargs sed -i 's/\r//'
```

### 转换举例：
```
[root@vk ~]## echo -e 'a\n\r\b'>file1
[root@vk ~]## file file1
file1: ASCII text, with CR, LF line terminators, with overstriking
[root@vk ~]## sed -i 's/\r//'  filename
[root@vk ~]## file file1
file1: ASCII text, with overstriking
```

```
1 -- SHELL读取文件的方法
#使用read命令读取一行数据
while read myline
do
 echo "LINE:"$myline
done < datafile.txt

#使用read命令读取一行数据
cat datafile.txt | while read myline
do
 echo "LINE:"$myline
done

#读取一行数据
cat datafile.txt | while myline=$(line)
do
 echo "LINE:"$myline
done

#读取一行数据
while myline=$(line)
do
 echo "LINE:"$myline
done < datafile.txt

#使用read命令读取变量数据
cat datafile.txt | while read paraa parab parac
do
 echo "PARAA:"$paraa
 echo "PARAB:"$parab
 echo "PARAC:"$parac
done

#使用read命令读取变量数据
while read paraa parab parac
do
 echo "PARAA:"$paraa
 echo "PARAB:"$parab
 echo "PARAC:"$parac
done < datafile.txt
```
