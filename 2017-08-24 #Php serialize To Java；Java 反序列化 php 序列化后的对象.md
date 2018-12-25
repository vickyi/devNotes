Php serialize To Java；Java 反序列化 php 序列化后的对象
=====

[TOC]

## Maven Dependency
```
<!-- https://mvnrepository.com/artifact/com.xk72/pherialize -->
<dependency>
    <groupId>com.xk72</groupId>
    <artifactId>pherialize</artifactId>
    <version>1.2.4</version>
</dependency>
```

## Java Code
```java
package com.abcplus.hive.dwudf;

import de.ailis.pherialize.Mixed;
import org.apache.commons.lang.StringUtils;
import org.apache.hadoop.hive.ql.exec.UDF;
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONArray;
import de.ailis.pherialize.MixedArray;
import de.ailis.pherialize.Pherialize;

/**
 * Created by gz on 2017/11/17.
 */
public class GetMenuArray extends UDF{

    public String evaluate(String input) {

        // If the value is null, return a null
        if(StringUtils.isEmpty(input)) return null;

        MixedArray mixedArray;

        mixedArray = Pherialize.unserialize(input).toArray();

        JSONArray jsonArray = getMixedArrays(mixedArray);

        return jsonArray.toJSONString();
    }

    private JSONArray getMixedArrays(MixedArray mixedArray) {
        JSONArray jsonArray = new JSONArray();
        for(Object key1 : mixedArray.keySet()){
            Mixed mix = mixedArray.getMixed(key1);
            if (mix != null) {
                MixedArray arr = mix.toArray();
                for(Object key2 : arr.keySet()){
                    Mixed value = arr.getMixed(key2);
                    String jsonString = getJSONString(value);
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
        String data = "a:1:{i:1;a:9:{i:1;a:26:{s:2:\"id\";s:1:\"1\";s:5:\"title\";s:6:\"推荐\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:7:\"#e22400\";s:5:\"act_c\";s:7:\"#FFFFFF\";s:3:\"d_c\";s:7:\"#FD9480\";s:5:\"act_p\";s:52:\"/bao/171101/7/e/59f9175a8150a14eb3621b87_240x120.png\";s:3:\"d_p\";s:52:\"/bao/171101/7/e/59f9175a8150a14eb3621b87_240x120.png\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";s:1:\"2\";s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:8:\"xcx_home\";s:4:\"type\";i:1;s:4:\"item\";s:8:\"xcx_home\";s:7:\"ys_cate\";i:1;s:10:\"tab_search\";s:1:\"2\";s:11:\"tab_keyword\";s:0:\"\";s:15:\"tab_search_logo\";s:0:\"\";s:9:\"tab_color\";s:0:\"\";s:11:\"tab_message\";s:1:\"1\";s:12:\"tab_category\";s:1:\"1\";s:13:\"tab_cate_type\";s:1:\"1\";s:12:\"tab_cate_url\";s:0:\"\";}i:2;a:18:{s:2:\"id\";i:2;s:5:\"title\";s:6:\"女装\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:0:\"\";s:5:\"act_c\";s:0:\"\";s:3:\"d_c\";s:0:\"\";s:5:\"act_p\";s:0:\"\";s:3:\"d_p\";s:0:\"\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";i:2;s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:12:\"xcx_nvzhuang\";s:4:\"type\";i:1;s:4:\"item\";s:12:\"xcx_nvzhuang\";s:7:\"ys_cate\";i:1;}i:3;a:18:{s:2:\"id\";i:3;s:5:\"title\";s:6:\"鞋包\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:0:\"\";s:5:\"act_c\";s:0:\"\";s:3:\"d_c\";s:0:\"\";s:5:\"act_p\";s:0:\"\";s:3:\"d_p\";s:0:\"\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";i:2;s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:10:\"xcx_xiebao\";s:4:\"type\";i:1;s:4:\"item\";s:10:\"xcx_xiebao\";s:7:\"ys_cate\";i:1;}i:4;a:18:{s:2:\"id\";i:4;s:5:\"title\";s:6:\"居家\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:0:\"\";s:5:\"act_c\";s:0:\"\";s:3:\"d_c\";s:0:\"\";s:5:\"act_p\";s:0:\"\";s:3:\"d_p\";s:0:\"\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";i:2;s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:9:\"xcx_jujia\";s:4:\"type\";i:1;s:4:\"item\";s:9:\"xcx_jujia\";s:7:\"ys_cate\";i:1;}i:5;a:18:{s:2:\"id\";i:5;s:5:\"title\";s:12:\"家电数码\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:0:\"\";s:5:\"act_c\";s:0:\"\";s:3:\"d_c\";s:0:\"\";s:5:\"act_p\";s:0:\"\";s:3:\"d_p\";s:0:\"\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";i:2;s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:16:\"xcx_jiadianshuma\";s:4:\"type\";i:1;s:4:\"item\";s:16:\"xcx_jiadianshuma\";s:7:\"ys_cate\";i:1;}i:6;a:18:{s:2:\"id\";i:6;s:5:\"title\";s:6:\"男士\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:0:\"\";s:5:\"act_c\";s:0:\"\";s:3:\"d_c\";s:0:\"\";s:5:\"act_p\";s:0:\"\";s:3:\"d_p\";s:0:\"\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";i:2;s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:10:\"xcx_nanshi\";s:4:\"type\";i:1;s:4:\"item\";s:10:\"xcx_nanshi\";s:7:\"ys_cate\";i:1;}i:7;a:18:{s:2:\"id\";i:7;s:5:\"title\";s:6:\"母婴\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:0:\"\";s:5:\"act_c\";s:0:\"\";s:3:\"d_c\";s:0:\"\";s:5:\"act_p\";s:0:\"\";s:3:\"d_p\";s:0:\"\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";i:2;s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:10:\"xcx_muying\";s:4:\"type\";i:1;s:4:\"item\";s:10:\"xcx_muying\";s:7:\"ys_cate\";i:1;}i:8;a:18:{s:2:\"id\";i:8;s:5:\"title\";s:6:\"美妆\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:0:\"\";s:5:\"act_c\";s:0:\"\";s:3:\"d_c\";s:0:\"\";s:5:\"act_p\";s:0:\"\";s:3:\"d_p\";s:0:\"\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";i:2;s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:13:\"xcx_meizhuang\";s:4:\"type\";i:1;s:4:\"item\";s:13:\"xcx_meizhuang\";s:7:\"ys_cate\";i:1;}i:9;a:18:{s:2:\"id\";i:9;s:5:\"title\";s:6:\"美食\";s:8:\"act_bg_c\";s:0:\"\";s:6:\"d_bg_c\";s:0:\"\";s:5:\"act_c\";s:0:\"\";s:3:\"d_c\";s:0:\"\";s:5:\"act_p\";s:0:\"\";s:3:\"d_p\";s:0:\"\";s:3:\"url\";s:0:\"\";s:9:\"show_type\";i:2;s:8:\"sale_img\";s:0:\"\";s:8:\"sel_type\";s:1:\"7\";s:7:\"ys_type\";s:1:\"1\";s:7:\"content\";s:0:\"\";s:3:\"cid\";s:10:\"xcx_meishi\";s:4:\"type\";i:1;s:4:\"item\";s:10:\"xcx_meishi\";s:7:\"ys_cate\";i:1;}}}\n";
        GetMenuArray menu = new GetMenuArray();
        String res = menu.evaluate(data);
        System.out.println(res);
    }
}

```

## 测试结果
```
["{\"cid\":\"xcx_home\",\"id\":1,\"itemId\":\"xcx_home\",\"keyword\":\"\",\"title\":\"推荐\"}","{\"cid\":\"xcx_nvzhuang\",\"id\":2,\"itemId\":\"xcx_nvzhuang\",\"keyword\":\"\",\"title\":\"女装\"}","{\"cid\":\"xcx_xiebao\",\"id\":3,\"itemId\":\"xcx_xiebao\",\"keyword\":\"\",\"title\":\"鞋包\"}","{\"cid\":\"xcx_jujia\",\"id\":4,\"itemId\":\"xcx_jujia\",\"keyword\":\"\",\"title\":\"居家\"}","{\"cid\":\"xcx_jiadianshuma\",\"id\":5,\"itemId\":\"xcx_jiadianshuma\",\"keyword\":\"\",\"title\":\"家电数码\"}","{\"cid\":\"xcx_nanshi\",\"id\":6,\"itemId\":\"xcx_nanshi\",\"keyword\":\"\",\"title\":\"男士\"}","{\"cid\":\"xcx_muying\",\"id\":7,\"itemId\":\"xcx_muying\",\"keyword\":\"\",\"title\":\"母婴\"}","{\"cid\":\"xcx_meizhuang\",\"id\":8,\"itemId\":\"xcx_meizhuang\",\"keyword\":\"\",\"title\":\"美妆\"}","{\"cid\":\"xcx_meishi\",\"id\":9,\"itemId\":\"xcx_meishi\",\"keyword\":\"\",\"title\":\"美食\"}"]

```

## 参考
[pherialize](https://github.com/kayahr/pherialize/)