#### FastJson的介绍
最近在项目中使用Http接口访问对方的API，双方约定的数据格式是Json，项目中使用的是
[阿里fastJson](https://github.com/alibaba/fastjson/wiki/Quick-Start-CN)
来做数据的配置，基本的信息大家可以去看wiki文档，我这里作一个大致的介绍.
>FastJson主要用来处理Json对象和JavaBean对象之间的互相转换，顶层的接口是JSONStreamAware和JSONAware，JSON是实现这两个接口的抽象类，有JSONObject和JSONArray两个具体实现类，也就是数组的实现方式和键值对的实现方式，底层使用我们熟悉的HashMap和ArrayList来做支持。

#### 问题
在开发过程中，我常常发现有这种```{"$ref":"$.array1[0]"}```数据出现，当时和对方的接口联调，遇到这种数据简直崩溃，在网上找了原因，说是因为有重复引用的问题。我们现在以一段代码来重现这种情况。
```
@Slf4j
public class TestJson {


    @Test
    public void testJson() {
        JSONObject object = new JSONObject();
        JSONObject object1 = new JSONObject();
        JSONObject object2 = new JSONObject();
        JSONArray array = new JSONArray();

        object.put("name", "ragrok");
        object.put("age", "12");
        object.put("sex", "男");
        array.add(object);

        object.put("name", "ragrok");
        object.put("age", "12");
        object.put("sex", "男");
        array.add(object);

        /***********************未添加前*********************/
        String arrayStr = JSON.toJSONStringWithDateFormat(array, "yyyy-MM-dd HH:mm:ss", SerializerFeature.DisableCircularReferenceDetect);
        log.info("禁止重复引用的字符串{}", arrayStr);
        //[{"sex":"男","age":"12","name":"ragrok"},{"sex":"男","age":"12","name":"ragrok"}]
        log.info("原生的数组字符{}", arrayStr);
        //[{"sex":"男","age":"12","name":"ragrok"},{"sex":"男","age":"12","name":"ragrok"}]

        /***********************添加后***********************/
        object1.put("array", arrayStr);
        object2.put("array1", array);

        JSONObject object3 = new JSONObject();
        object3.putAll(object1);
        log.info("禁止重复引用的字符串{},", object3.toString());
        //{"array":"[{\"sex\":\"男\",\"age\":\"12\",\"name\":\"ragrok\"},{\"sex\":\"男\",\"age\":\"12\",\"name\":\"ragrok\"}]"}
        JSONObject object4 = new JSONObject();
        object4.putAll(object2);
        log.info("原生的数组字符{}", object4.toString());
        //{"array1":[{"sex":"男","age":"12","name":"ragrok"},{"$ref":"$.array1[0]"}]}
    }
}
```
可以看到在数组未添加前，无论是禁止了```fast互相引用```，还是使用原生的数据传送过去，数据的输出都很正常，但是在数据添加到别的FastJson对象里之后，问题就来了，禁止```fast互相引用```的对象输出有```\```斜杆,不做任何处理的对象出现了```{"$ref":"$.array1[0]"}```的问题，简直是一筹莫展，左右都是错。
#### 分析
那问题在哪里呢，问题出在了前面添加的部分。
```
JSONObject object = new JSONObject();
JSONArray array = new JSONArray();

object.put("name", "ragrok");
object.put("age", "12");
object.put("sex", "男");
array.add(object);

object.put("name", "ragrok");
object.put("age", "12");
object.put("sex", "男");
array.add(object);
```
仔细看这段代码，用了同一个对象来添加键值对数据，如果用JVM知识来解释的话，就是两个相同的引用指向了同一个对象， 所以我们要分开使用不同的对象来添加，应该是这种方式：
```
JSONObject object = new JSONObject();
JSONArray array = new JSONArray();

object.put("name", "ragrok");
object.put("age", "12");
object.put("sex", "男");
array.add(object);

JSONObject object1 = new JSONObject();
object1.put("name", "ragrok");
object1.put("age", "12");
object1.put("sex", "男");
array.add(object1);
```
完整的代码是这种的：
```
@Test
    public void testJson1() {
        JSONObject object = new JSONObject();
        JSONObject object1 = new JSONObject();
        JSONObject object2 = new JSONObject();
        JSONArray array = new JSONArray();

        object.put("name", "ragrok");
        object.put("age", "12");
        object.put("sex", "男");
        array.add(object);

        JSONObject object5 = new JSONObject();
        object5.put("name", "ragrok");
        object5.put("age", "12");
        object5.put("sex", "男");
        array.add(object5);

        /**********未添加前*********/
        String arrayStr = JSON.toJSONStringWithDateFormat(array, "yyyy-MM-dd HH:mm:ss", SerializerFeature.DisableCircularReferenceDetect);
        log.info("禁止重复引用的字符串{}", arrayStr);
        //禁止重复引用的字符串[{"sex":"男","age":"12","name":"ragrok"},{"sex":"男","age":"12","name":"ragrok"}]
        log.info("原生的数组字符{}", array);
        //原生的数组字符[{"sex":"男","age":"12","name":"ragrok"},{"sex":"男","age":"12","name":"ragrok"}]

        /**********添加后*********/
        object1.put("array", arrayStr);
        object2.put("array1", array);

        JSONObject object3 = new JSONObject();
        object3.putAll(object1);
        log.info("禁止重复引用的字符串{},", object3.toString());
        //禁止重复引用的字符串{"array":"[{\"sex\":\"男\",\"age\":\"12\",\"name\":\"ragrok\"},{\"sex\":\"男\",\"age\":\"12\",\"name\":\"ragrok\"}]"},
        JSONObject object4 = new JSONObject();
        object4.putAll(object2);
        log.info("原生的数组字符{}", JSON.toJSONString(object4));
        //原生的数组字符{"array1":[{"sex":"男","age":"12","name":"ragrok"},{"sex":"男","age":"12","name":"ragrok"}]}
    }
```
#### 总结
网上还有其他办法，例如，建立一个javaBean，将json转化一下再添加，但是出现这种情况本质都是因为同一个对象添加数据导致的循环引用，我们只要破坏这种循环就可以了。这里还有一点在提醒我们，有些代码该写的要写，不要偷懒，多new一个对象就能解决的事，就不要搞那么复杂了。
