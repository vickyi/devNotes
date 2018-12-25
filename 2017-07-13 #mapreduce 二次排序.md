## mapreduce 二次排序
[Hadoop 官方 MapReduceTutorial](http://hadoop.apache.org/docs/r2.6.1/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html#Partitioner)

### 需求
对用户的访问（点击和浏览）进行排序和归类
### 思路
通过mapreduce计算，并将结果写入hive ORC 外表

#### 1 自定义key

在mr中，所有的key是需要被比较和排序的，并且是二次，先根据partitione，再根据大小。而本例中也是要比较两次。先按照第一字段排序，然后再对第一字段相同的按照第二字段排序。根据这一点，我们可以构造一个复合类IntPair，他有两个字段，先利用分区对第一字段排序，再利用分区内的比较对第二字段排序。
所有自定义的key应该实现接口WritableComparable，因为是可序列的并且可比较的。并重载方法：
```
//反序列化，从流中的二进制转换成IntPair  
public void readFields(DataInput in) throws IOException          
//序列化，将IntPair转化成使用流传送的二进制  
public void write(DataOutput out)  
//key的比较  
public int compareTo(IntPair o)          
//另外新定义的类应该重写的两个方法  
//The hashCode() method is used by the HashPartitioner (the default partitioner in MapReduce)  
public int hashCode()   
public boolean equals(Object right)  
```
#### 2 由于key是自定义的，所以还需要自定义一下类：
##### 2.1 分区函数类。这是key的第一次比较。
```
public static class FirstPartitioner extends Partitioner<IntPair,IntWritable>
```
在job中使用setPartitionerClasss设置Partitioner。
##### 2.2 key比较函数类。这是key的第二次比较。这是一个比较器，需要继承WritableComparator。
```
public static class KeyComparator extends WritableComparator
```
必须有一个构造函数，并且重载 public int compare(WritableComparable w1, WritableComparable w2)

另一种方法是 实现接口RawComparator。
在job中使用setSortComparatorClass设置key比较函数类。
##### 2.3 分组函数类。在reduce阶段，构造一个key对应的value迭代器的时候，只要first相同就属于同一个组，放在一个value迭代器。这是一个比较器，需要继承WritableComparator。
```
public static class GroupingComparator extends WritableComparator
```

分组函数类也必须有一个构造函数，并且重载 public int compare(WritableComparable w1, WritableComparable w2)
分组函数类的另一种方法是实现接口RawComparator。
在job中使用setGroupingComparatorClass设置分组函数类。

### 项目解析
#### 项目结构
#### GuIdDatePair
#### GuIdPartitioner
#### PathComputeDriver
#### PathMapper
#### PathGroupingComparator
#### PathReducer
#### TextArrayWritable

### 项目地址
https://github.com/vickyi/VisitPath
