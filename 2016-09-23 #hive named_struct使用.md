```
use test;
create table test_named_struct as
    select
    named_struct(
        'id' ,page_id,                                      
        'value' ,page_value
    ) dp
from dw.dim_page dp;

select dp.id from test.test_named_struct;
```

参考：https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF