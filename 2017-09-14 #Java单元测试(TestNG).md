
## 简介

 TestNG 是一个测试框架，旨在覆盖单元测试、测试功能、测试端到端、测试集成等测试需求。

单元测试在开发人员层面进行，通过单元测试每个单体（类、方法），以确保最终产品满足要求



**TestNG 的优势**

- 支持测试集成类（例如，默认情况下，不需要为每个测试方法创建一个新的测试类实例）。
- 灵活的运行时配置。
- Group 概念，在运行编排方面提供最大灵活性
- 支持依赖测试方法，并行测试，负载测试和部分失败。
- 灵活的插件API。
- 支持多线程测试。




## 通过 Maven 使用 TestNG

在工程的pom.xml文件的`<dependencies>`部分添加依赖引用
```xml
<dependency>
    <groupId>org.testng</groupId>
    <artifactId>testng</artifactId>
    <version>6.8</version>
    <scope>test</scope>
</dependency>
```
pom文件中默认包含的junit依赖引用不需要删除，TestNG可以支持对Junit测试用例的编排执行

在pom.xml文件中的`<plugins>`部分添加测试套件xml文件约定
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.19.1</version>
    <configuration>
        <suiteXmlFiles>
        <suiteXmlFile>testng.xml</suiteXmlFile>
        </suiteXmlFiles>
    </configuration>
</plugin>
```

然后在工程的根目录添加一个名为testng.xml文件，内容可以参考如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<suite name="Suite" parallel="false">
  <!-- Unit Test -->
  <test name="All Test">
    <classes>
      <class name="me.sdjkx.java.quickstart.NewTest"/>
    </classes>
   </test> 
</suite> <!-- Suite -->
```

## 断言

**执行顺序**

```
beforeSuite
beforeTest
beforeClass
beforeMethod
test case 1
afterMethod
beforeMethod
test case 2
afterMethod
afterClass
afterTest
afterSuite
```


## 与JUnit伴生

`TestNG` 对`Junit`提供良好编排支持

在POM文件中使用 `junit = "true"` 即可将原来的Junit用例使用TestNG 编排执行
```xml
<suite name="Converted JUnit suite" >
   <test name="JUnitTests" junit="true">
      <classes>
         <class name="TestJunit" />
      </classes>
   </test>
</suite>
```





## 安装

**Maven**

```xml
<dependency>
  <groupId>org.testng</groupId>
  <artifactId>testng</artifactId>
  <version>6.9.12</version>
  <scope>test</scope>
</dependency>
```



## 执行

#### 命令行

在执行 mvn package 命令时，会自动编译和执行src/test 目录下的测试用例。

如果想跳过测试可以使用以下参数

```shell
mvn clean package -DskipTests // 编译不执行
mvn clean package -Dmaven.test.skip=true //不编译不执行
```

#### IDE 支持

- Eclipse：on Eclipse Marketplace search  testng
- IDEA：https://github.com/JetBrains/intellij-community/tree/master/plugins/testng
