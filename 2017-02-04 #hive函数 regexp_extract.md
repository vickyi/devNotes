 ## hive函数 regexp_extract
 [Hive LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF)
 
 ### 语法描述

 ```
  老的函数格式
 regexp_extract(string subject, string pattern[, index])
 
 最新的函数格式：
 
 regexp_extract(string subject, string pattern, int index)
 ```
 
 > Returns the string extracted using the pattern. For example, regexp_extract('foothebar', 'foo(.*?)(bar)', 2) returns 'bar.' Note that some care is necessary in using predefined character classes: using '\s' as the second argument will match the letter s; '\\s' is necessary to match whitespace, etc. The 'index' parameter is the Java regex Matcher group() method index. See docs/api/java/util/regex/Matcher.html for more information on the 'index' or Java regex group() method.
 
参数解释:
subject 是被解析的字符串

pattern 是正则表达式

index 是返回结果，取表达式的哪一部，默认值为1。
    0表示把整个正则表达式对应的结果全部返回；
    1表示返回正则表达式中第一个() 对应的结果 以此类推。


注意点：
要注意的是idx的数字不能大于表达式中()的个数。
否则报错：