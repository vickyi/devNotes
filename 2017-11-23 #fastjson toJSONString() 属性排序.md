
## 注解 JSONType 指定顺序
```
package com.abcplus.hive.dwudf;

import com.alibaba.fastjson.annotation.JSONType;

/**
 * Created by gz on 2017/11/17.
 */
@JSONType(orders={"id","title","itemId","cid","keyword"})
public class Menu {
    private int id;
    private String title;
    private String itemId;
    private String cid;
    private String keyword;

}


public class MenuSortTest {

    public static void main(String[] args) {
    Menu menu = new Menu();
    menu.setId(1);
    menu.setTitle("精品配饰");
    menu.setItemId("2048");
    menu.setCid("1238");
    menu.setKeyword("配饰");

    String jsonString = JSON.toJSONString(menu);

    System.out.println(jsonString);
    }
}

```