这里只列一些常见的方法，更多方法请参考：[Oracle在线API](https://docs.oracle.com/javase/7/docs/api/)
---
#### 1.String常用方法
a. ``` startsWith()```：字符串是否以某个字符或者字符串开头。
```
public static void testStartsWith() {
        String string = "mike,wang,lol,gege,meizi";
        System.out.println(string.startsWith("mik")); // true
        System.out.println(string.startsWith("mikasd")); // false
    }  
```
b. ``` endsWith()```：字符串是否以某个字符或者字符串结尾。
```
public static void testEndsWith() {
        String string = "mike,wang,lol,gege,meizi";
        System.out.println(string.endsWith("meizi")); // true
        System.out.println(string.endsWith("mei")); // false
    }  
```
c. ```matches()```：匹配正则表达式，正则表达式后面详细讲，先给一个例子
```
public static void testMatches() {
        String string = "mikewanglolgegemeizi";
        System.out.println(string.matches("\\w+")); // true
        System.out.println(string.matches("\\W+")); // false
    }  
```
d. ```split()```：将匹配的字符切割成数组
```
public static void testSplit() {
        String string = "mike,wang,lol,gege,meizi";
        // 去掉,
        String[] strings = string.split(",");
        for (String string2 : strings) {
            System.out.println(string2); // mike wang lol gege meizi
        }
        String string1 = "mike,wang,lol,gege,meizi";
        // 去掉以m开头的字符串
        String[] strings1 = string1.split("m\\w+");
        for (String string2 : strings1) {
            System.out.println(string2); // ,wang,lol,gege
        }
    }
```
e. ```replace()```：覆盖匹配的字符
```
public static void testReplace() {
        String string = "mike,wang,lol,gege,meizi";
        System.out.println(string.replace("mike", "xiaowang")); // xiaowang,wang,lol,gege,meizi
        System.out.println(string.replace("mikse", "xiaowang"));// mike,wang,lol,gege,meizi
    }  
```
f. ```replaceAll()```：覆盖正则匹配的全部字符，适合大数据处理
```
public static void testReplaceAll(){
        String string = "mike,wang,lol,gege,meizi";
        //将符合正则表达式的部分用某个字符串来代替,特别适合处理大量数据时，所以我们接下来要重点介绍正则表达式
        System.out.println(string.replaceAll("\\w+", "xiaowang")); 
        //xiaowang,xiaowang,xiaowang,xiaowang,xiaowang
    }  
```
g. ```substring()```：截取
```
public static void testSubstring() {
        String string = "mike,wang,lol,gege,meizi";
        System.out.println(string.substring(0, string.length() - 4));// 截取0到length-4形成一个新的字符串
        //mike,wang,lol,gege,m
    }  
```
H.当然还有一些就不一一介绍了,自己翻API去吧。
#### 2.StringBuffer常用方法
1.```append()```：串联
```
public static void testAppend() {
        StringBuffer buffer = new StringBuffer("Init-");
        buffer.append("Buffer-");
        buffer.append("Entity");
        System.out.println(buffer.toString());
        //Init-Buffer-Entity
    }  
```
2.```delete()```：删除
```
public static void testDelete() {
        StringBuffer buffer = new StringBuffer("Init-");
        buffer.append("Buffer-");
        buffer.append("Entity");
        buffer.delete(2, buffer.length() - 7);
        System.out.println(buffer.toString());
        //In-Entity
    } 
```
3.```insert()```：插入数字，看到结果，我只能说这个方法很诡异
```
public static void testInsert() {
        StringBuffer buffer = new StringBuffer("Init-");
        buffer.append("Buffer-");
        buffer.append("Entity-");
        buffer.insert(3,buffer.length()-1).append("Insert value");
        System.out.println(buffer.toString());
        //Ini18t-Buffer-Entity-Insert value
    }  
```
4.```replace()```：覆盖
```
public static void testReplace() {
        StringBuffer buffer = new StringBuffer("Init-");
        buffer.append("Buffer-");
        buffer.append("Entity");
        buffer.replace(2, buffer.length() -5, "replace value");
        System.out.println(buffer.toString());
        //Inreplace valuentity
    }  
```
5.其他的方法参考API,真的没什么太多介绍的。
#### 3. StringBuilder常用方法 参考StringBuffer的方法，两个类除了线程安不安全，其他地方一模一样。
字符串与正则表达式的结合
---
- 正则表达式这个工具，在厉害的人手上会发挥巨大的威力，这是Thinking in java中特别强调的，也是我们单独把字符串和正则表达式划出来将的原因。具体到开发中，关键字过滤，例如知乎这样的网站，不允许你去搜索现任国家领导人信息，就是先把不能搜索的数据存到数据库中，之后利用正则表达式把你输入的信息和数据库里的敏感信息去比对，比对完，没这些东西就放开权限让你去检索内容，不然就给个查无此人的错误页面。可以看到，正则表达式这种简洁，动态的语言，在处理大批量数据这方面有着得天独有的优势。
- 根据Oracle的文档，找到java.util.regex包，我们先把正则表达式的一部分语法搬出来。
```
Construct	Matches
 	
Characters
x	The character x
\\	The backslash character
\0n	The character with octal value 0n (0 <= n <= 7)
\0nn	The character with octal value 0nn (0 <= n <= 7)
\0mnn	The character with octal value 0mnn (0 <= m <= 3, 0 <= n <= 7)
\xhh	The character with hexadecimal value 0xhh
\uhhhh	The character with hexadecimal value 0xhhhh
\x{h...h}	The character with hexadecimal value 0xh...h (Character.MIN_CODE_POINT  <= 0xh...h <=  Character.MAX_CODE_POINT)
\t	The tab character ('\u0009')
\n	The newline (line feed) character ('\u000A')
\r	The carriage-return character ('\u000D')
\f	The form-feed character ('\u000C')
\a	The alert (bell) character ('\u0007')
\e	The escape character ('\u001B')
\cx	The control character corresponding to x
 	
Character classes
[abc]	a, b, or c (simple class)
[^abc]	Any character except a, b, or c (negation)
[a-zA-Z]	a through z or A through Z, inclusive (range)
[a-d[m-p]]	a through d, or m through p: [a-dm-p] (union)
[a-z&&[def]]	d, e, or f (intersection)
[a-z&&[^bc]]	a through z, except for b and c: [ad-z] (subtraction)
[a-z&&[^m-p]]	a through z, and not m through p: [a-lq-z](subtraction)
 	
Predefined character classes
.	Any character (may or may not match line terminators)
\d	A digit: [0-9]
\D	A non-digit: [^0-9]
\s	A whitespace character: [ \t\n\x0B\f\r]
\S	A non-whitespace character: [^\s]
\w	A word character: [a-zA-Z_0-9]
\W	A non-word character: [^\w]
```
本来想详细写的，看了几篇博文，把书上这部分也看了一遍，发现自己无法掌握基本的东西，力不能及，所以放弃了，待以后再补充吧。
---
- [正则表达式基础](http://blog.csdn.net/yanbober/article/details/45556185)
- [JAVA正则表达式：Pattern类与Matcher类详解(转)](http://www.cnblogs.com/ggjucheng/p/3423731.html)
- [java正则表达式的应用](https://www.ibm.com/developerworks/cn/java/l-regp/part1/)
