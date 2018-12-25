Hive UDF 反序列化 php
=======

### code示例

```java
package com.abcplus.hive.dwudf;

import com.alibaba.fastjson.serializer.SerializerFeature;
import de.ailis.pherialize.Mixed;
import org.apache.commons.lang.StringUtils;
import org.apache.hadoop.hive.ql.exec.UDF;
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONArray;
import de.ailis.pherialize.MixedArray;
import de.ailis.pherialize.Pherialize;

import java.util.ArrayList;
import java.util.Set;

/**
 * Created by gz on 2017/11/17.
 */
public class GetMenuArray extends UDF{

    public ArrayList<String> evaluate(String input) {

        // If the value is null, return a null
        if(StringUtils.isEmpty(input)) return null;

        MixedArray mixedArray;

        mixedArray = Pherialize.unserialize(input).toArray();

        ArrayList jsonArray = getMixedArrays(mixedArray);

        return jsonArray;
    }

    public static boolean isNumeric(String str){
        for (int i = str.length();--i>=0;){
            if (!Character.isDigit(str.charAt(i))){
                return false;
            }
        }
        return true;
    }

    private ArrayList<String> getMixedArrays(MixedArray mixedArray) {
        ArrayList jsonArray = new ArrayList();
        Set keys1 = mixedArray.keySet();
        for(Object key1 : keys1){
            Mixed mix = mixedArray.getMixed(key1);
            if (mix != null) {
                MixedArray arr = mix.toArray();
                Set keys2 = arr.keySet();
                for(Object key2 : keys2){
                    String jsonString;
                    if(isNumeric(key2.toString())) {
                        Mixed value = arr.getMixed(key2);
                        jsonString = getJSONString(value);
                    } else {
                        jsonString = getJSONString(mix);
                    }
                    jsonArray.add(jsonString);
                }
            }
        }
        return jsonArray;
    }

    private String getJSONString(Mixed value) {
        MixedArray arr = value.toArray();
        String title = arr.containsKey("title") ? arr.getString("title"):"";
        String itemId = arr.containsKey("item") ? arr.getString("item"):"";
        int id = arr.containsKey("id") ? arr.getInt("id"):0;
        String cid = arr.containsKey("cid") ? arr.getString("cid"):"";
        String keyword = arr.containsKey("tab_keyword") ? arr.getString("tab_keyword"):"";

        Menu menu = new Menu();
        menu.setId(id);
        menu.setTitle(title);
        menu.setItemId(itemId);
        menu.setCid(cid);
        menu.setKeyword(keyword);

        String jsonString = JSON.toJSONString(menu);
        return jsonString;
    }

    public static void main(String[] args) {
        String data = "";
        GetMenuArray menu = new GetMenuArray();
        ArrayList<String> res = menu.evaluate(data);
        System.out.println(res);
    }
}
```

## Menu 类

注意，在定义Menu类的时候，采用注解JSONType来固定字段顺序，否则在JSON.toJSONString(menu)后得到的字段就是字典序。

```java
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
    // 省略了setter，自行补充
    ......
}
```

## 测试

```java
select t.id,t.title,tf.*
from
    (SELECT
    id, title, menu_list
    from ods.xd_js_appmenu_list
    where id = 500
    ) t lateral view explode(GetMenuArray(menu_list)) tf;
```

### 查询结果

```text
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":1,"title":"推荐","itemId":"all","cid":"","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":2,"title":"女装","itemId":"","cid":"","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":3,"title":"鞋包","itemId":"","cid":"","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":5,"title":"居家","itemId":"","cid":"","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":4,"title":"母婴","itemId":"","cid":"","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":7,"title":"美妆","itemId":"317","cid":"317","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":10,"title":"家电数码","itemId":"1296","cid":"1296","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":6,"title":"男士","itemId":"316","cid":"316","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":8,"title":"美食","itemId":"318","cid":"318","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":9,"title":"全球购","itemId":"310","cid":"310","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":12,"title":"精品配饰","itemId":"2048","cid":"2048","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":11,"title":"即将上线","itemId":"1238","cid":"1238","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":1,"title":"热销榜单","itemId":"topsale","cid":"","keyword":""}
500     APP450菜单-双十一开售菜单-底部热销榜    {"id":1,"title":"VIP专享","itemId":"","cid":"","keyword":""}
```