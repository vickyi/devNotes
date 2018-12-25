Hive ObjectInspector
==========

ObjectInspector是Hive中一个咋一看比较令人困惑的概念，当初读Hive源代码时，花了很长时间才理解。 当读懂之后，发现ObjectInspector作用相当大，它解耦了数据使用和数据格式，从而提高了代码的复用程度。 简单的说，ObjectInspector接口使得Hive可以不拘泥于一种特定数据格式，使得数据流 1）在输入端和输出端切换不同的输入/输出格式 2）在不同的Operator上使用不同的数据格式。

[转自 iteye:Hive中的ObjectInspector设计](http://db3.iteye.com/blog/1072778)

这是ObjectInspector interface

```java
public interface ObjectInspector extends Cloneable { 
  public static enum Category { 
    PRIMITIVE, LIST, MAP, STRUCT, UNION 
  };

  String getTypeName();

  Category getCategory();
}
```

这个interface提供了最一般的方法 getTypeName 和 getCategory。 
我们再来看它的子抽象类和interface：

- StructObjectInspector
- MapObjectInspector
- ListObjectInspector
- PrimitiveObjectInspector
- UnionObjectInspector

其中，PrimitiveObjectInspector用来完成对基本数据类型的解析，而StructObjectInspector用了完成对一行数据的解析，它本身有一组ObjectInspector组成。

由于Hive支持Nested Data Structure，所以，在StructObjectInspector中又可以（一层或多层的）嵌套任意的ObjectInspector。 Struct, Map, List, Union是Hive支持的4种集合数据类型，比如某一列的数据可以被声明为Struct类型，这样解析这一列的StructObjectInspector中就会嵌套了另一个StructObjectInspector。 

现在我们可以从一个小例子看看ObjectInspector是如何工作的，这是一个Hive SerDe的测试用例代码

```sql
  /**
   * Test the LazySimpleSerDe class. 
   */ 
  public void testLazySimpleSerDe() throws Throwable { 
    try { 
      // Create the SerDe 
      LazySimpleSerDe serDe = new LazySimpleSerDe();
      Configuration conf = new Configuration();
      Properties tbl = createProperties();
      //用Properties初始化serDe 
      serDe.initialize(conf, tbl);

      // Data 
      Text t = new Text("123\t456\t789\t1000\t5.3\thive and hadoop\t1.\tNULL");
      String s = "123\t456\t789\t1000\t5.3\thive and hadoop\tNULL\tNULL";
      Object[] expectedFieldsData = {new ByteWritable((byte) 123), 
          new ShortWritable((short) 456), new IntWritable(789), 
          new LongWritable(1000), new DoubleWritable(5.3), 
          new Text("hive and hadoop"), null, null};

      // Test 
      deserializeAndSerialize(serDe, t, s, expectedFieldsData);
    }catch (Throwable e) { 
      e.printStackTrace();
      throw e;
    }
  }

   private void deserializeAndSerialize(LazySimpleSerDe serDe, Text t, String s, 
      Object[] expectedFieldsData) throws SerDeException { 
    // Get the row ObjectInspector 
    StructObjectInspector oi = (StructObjectInspector) serDe 
        .getObjectInspector();
    // 获取列信息 
    List<? extends StructField> fieldRefs = oi.getAllStructFieldRefs();
    assertEquals(8, fieldRefs.size());

    // Deserialize 
    Object row = serDe.deserialize(t);
    for (int i = 0;i < fieldRefs.size();i++) { 
      Object fieldData = oi.getStructFieldData(row, fieldRefs.get(i));
      if (fieldData != null) { 
        fieldData = ((LazyPrimitive) fieldData).getWritableObject();
      }
      assertEquals("Field " + i, expectedFieldsData[i], fieldData);
    }
    // Serialize 
    assertEquals(Text.class, serDe.getSerializedClass());
    Text serializedText = (Text) serDe.serialize(row, oi);
    assertEquals("Serialized data", s, serializedText.toString());
  }

  //创建schema，保存在Properties中 
  private Properties createProperties() { 
    Properties tbl = new Properties();

    // Set the configuration parameters 
    tbl.setProperty(Constants.SERIALIZATION_FORMAT, "9");
    tbl.setProperty("columns", 
        "abyte,ashort,aint,along,adouble,astring,anullint,anullstring");
    tbl.setProperty("columns.types", 
        "tinyint:smallint:int:bigint:double:string:int:string");
    tbl.setProperty(Constants.SERIALIZATION_NULL_FORMAT, "NULL");
    return tbl;
  }

```

从这个例子中，Hive将对行中列的读取和行的存储方式解耦了，只有ObjectInspector清楚行和行中的列是怎样存取的，但使用者并不知道存储的细节。 对于数据的使用者来说，只需要行的Object和相应的ObjectInspector，就能读取出每一列的对象。 

以下这段代码再清晰不过了，ObjectInspector oi控制了对列的Access：

```
for (int i = 0;i < fieldRefs.size();i++) { 
      Object fieldData = oi.getStructFieldData(row, fieldRefs.get(i));
      if (fieldData != null) { 
        fieldData = ((LazyPrimitive) fieldData).getWritableObject();
      }
      assertEquals("Field " + i, expectedFieldsData[i], fieldData);
  }
```

以下这段代码的作用是把一行deserialize，然后再serialize：
```
    Object row = serDe.deserialize(t);
    Text serializedText = (Text) serDe.serialize(row, oi);
```

由此不难看出，只要有了不同的SerDe对象，可以很容易的将一条数据deserialize，然后再serialize成不同的格式，从而非常方便的实现数据格式的切换。 

理解了上面的例子，就不难理解为什么所有的Hive ExprNodeEvaluator 和 UDF，UDAF, UDTF 都需要 (Object, ObjectInspector) pair了。 数据存储细节和使用的分离，使得Hive不需要针对不同的数据格式对同一个UDF, UDAF 或UDTF实现不同的版本，这些函数看到的只是WritableObject！ 


下面是表达式evaluator的interface： 
```
/** 
* ExprNodeEvaluator. 
* 
*/ 
public abstract class ExprNodeEvaluator { 

  /** 
   * Initialize should be called once and only once. Return the ObjectInspector 
   * for the return value, given the rowInspector. 
   */ 
  public abstract ObjectInspector initialize(ObjectInspector rowInspector) throws HiveException;

  /** 
   * Evaluate the expression given the row. This method should use the 
   * rowInspector passed in from initialize to inspect the row object. The 
   * return value will be inspected by the return value of initialize. 
   */ 
  public abstract Object evaluate(Object row) throws HiveException;

}
```

initialize中需要初始化ObjectInspector，返回输出数据的ObjectInspector（它负责解析evaluate method返回的对象）；而每次evaluate call传进来一条Object数据，它的解析由ObjectInspector负责。 

接下来是GenericUDF抽象类:
```
public abstract class GenericUDF { 

  /** 
   * A Defered Object allows us to do lazy-evaluation and short-circuiting. 
   * GenericUDF use DeferedObject to pass arguments. 
   */ 
  public static interface DeferredObject { 
    Object get() throws HiveException;
  };

  /** 
   * The constructor. 
   */ 
  public GenericUDF() { 
  }

  /** 
   * Initialize this GenericUDF. This will be called once and only once per 
   * GenericUDF instance. 
   * 
   * @param arguments 
   *          The ObjectInspector for the arguments 
   * @throws UDFArgumentException 
   *           Thrown when arguments have wrong types, wrong length, etc. 
   * @return The ObjectInspector for the return value 
   */ 
  public abstract ObjectInspector initialize(ObjectInspector[] arguments) 
      throws UDFArgumentException;

  /** 
   * Evaluate the GenericUDF with the arguments. 
   * 
   * @param arguments 
   *          The arguments as DeferedObject, use DeferedObject.get() to get the 
   *          actual argument Object. The Objects can be inspected by the 
   *          ObjectInspectors passed in the initialize call. 
   * @return The 
   */ 
  public abstract Object evaluate(DeferredObject[] arguments) 
      throws HiveException;

  /** 
   * Get the String to be displayed in explain. 
   */ 
  public abstract String getDisplayString(String[] children);

}

```

它的机制与evaluator非常类似，初始化中敲定ObjectInspector数组，它们负责解析输入，返回output数据(即evaluator method返回的Object)的ObjectInspector；每次evaluate call传进一个Object数组，返回一条数据。 


Hive支持LazySimple, LazyBinary，Thrift等不同的数据格式，同一个查询计划中，可以在operator上切换数据流的格式。比较常见的是在Mapper端使用LazySimpleSerDe，Mapper输出的数据使用LazyBinarySerDe，因为binary格式比较节省空间，从而减少repartition时的网络传输。 如果你想看查询计划的每一步到底使用了哪一种SerDe格式，只要用"Explain Extended"就可以查清楚了。