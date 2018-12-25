Java API 读取 Hive ORC 文件
===========

> Orc是Hive特有的一种列式存储的文件格式，它有着非常高的压缩比和读取效率，因此很快取代了之前的RCFile，成为Hive中非常常用的一种文件格式。

在实际业务场景中，可能需要使用Java API，或者MapReduce读写Orc文件。

Hive从0.11版本开始提供了ORC的文件格式，ORC文件不仅仅是一种列式文件存储格式，最重要的是有着很高的压缩比，并且对于MapReduce来说是可切分（Split）的。因此，在Hive中使用ORC作为表的文件存储格式，不仅可以很大程度的节省HDFS存储资源，而且对数据的查询和处理性能有着非常大的提升，因为ORC较其他文件格式压缩比高，查询任务的输入数据量减少，使用的Task也就减少了。关于Orc文件格式的官网介绍，见：

> [文档LanguageManual+ORC](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)

> [官方首页](https://orc.apache.org/docs/mapred.html)

将相应的 ORC 和 hadoop 版本添加到pom.xml文件:

```xml
<dependencies>
  <dependency>
    <groupId>org.apache.orc</groupId>
    <artifactId>orc-mapreduce</artifactId>
    <version>1.1.0</version>
  </dependency>
  <dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-core</artifactId>
    <version>2.7.0</version>
  </dependency>
</dependencies>
```

需要注意的是，ORC能很大程序的节省存储和计算资源，但它在读写时候需要消耗额外的CPU资源来压缩和解压缩，当然这部分的CPU消耗是非常少的。

对性能提升的另一个方面是通过在ORC文件中为每一个字段建立一个轻量级的索引，来判定一个文件中是否满足WHERE子句中的过滤条件。比如：当执行HQL语句 `SELECT COUNT(1) FROM lxw1234_orc WHERE id = 0` 时候，先从ORC文件的metadata中读取索引信息，快速定位到id=0所在的offsets，如果从索引信息中没有发现id=0的信息，则直接跳过该文件。详见后面介绍。

[本文](http://lxw1234.com/archives/2016/04/630.htm)先介绍使用Java API读取Hive Orc文件。

```java
package com.abcplus.test;

import java.util.List;
import java.util.Properties;

import org.apache.hadoop.fs.Path;
import org.apache.hadoop.hive.ql.io.orc.OrcInputFormat;
import org.apache.hadoop.hive.ql.io.orc.OrcSerde;
import org.apache.hadoop.hive.serde2.objectinspector.StructField;
import org.apache.hadoop.hive.serde2.objectinspector.StructObjectInspector;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.InputFormat;
import org.apache.hadoop.mapred.InputSplit;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.RecordReader;
import org.apache.hadoop.mapred.Reporter;

/**
 * @author bi.com
 *
 */
public class TestOrcReader {

	public static void main(String[] args) throws Exception {
		JobConf conf = new JobConf();
		Path testFilePath = new Path(args[0]);
		Properties p = new Properties();
		OrcSerde serde = new OrcSerde();
		p.setProperty("columns", "url,word,freq,weight");
		p.setProperty("columns.types", "string:string:string:string");
		serde.initialize(conf, p);
		StructObjectInspector inspector = (StructObjectInspector) serde.getObjectInspector();
		InputFormat in = new OrcInputFormat();		FileInputFormat.setInputPaths(conf, testFilePath.toString());
		InputSplit[] splits = in.getSplits(conf, 1);
		System.out.println("splits.length==" + splits.length);

		conf.set("hive.io.file.readcolumn.ids", "1");
		RecordReader reader = in.getRecordReader(splits[0], conf, Reporter.NULL);
		Object key = reader.createKey();
		Object value = reader.createValue();
		List<? extends StructField> fields = inspector.getAllStructFieldRefs();
		long offset = reader.getPos();
		while(reader.next(key, value)) {
			Object url = inspector.getStructFieldData(value, fields.get(0));
			Object word = inspector.getStructFieldData(value, fields.get(1));
			Object freq = inspector.getStructFieldData(value, fields.get(2));
			Object weight = inspector.getStructFieldData(value, fields.get(3));
			offset = reader.getPos();
			System.out.println(url + "|" + word + "|" + freq + "|" + weight);
		}
		reader.close();

	}

}

```
