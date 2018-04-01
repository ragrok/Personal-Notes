## 写在前面的话
我们前面把Java运行时类型信息和Java泛型说了，就是为现在要研究的Java集合做准备。Java集合前还有一节数组篇，这是Java集合容器的根基，很多的容器类型实际上都是数组负责实际存储，其他数据结构来负责处理数据。个人打算在12月底将《Java编程思想》看完，所以时间上不怎么宽裕。集合这部分就讲个大概的。我打算把基本的点都涉及到，至于集合源码那些细节的部分，以后再来补充。下面部分是我为研究Java集合做的基本准备。
## JDK文档
涉及到任何语言开发，毫无疑问，API文档是最便捷地了解这门语言的工具。Java在工业强语言领域之所以独步天下，详尽的文档也是背后功臣。很多人即使不知道这个类是干嘛的，靠着API也能把业务做出来。
- 首先我们从网上下载或者到JDK目录去找一份这样的文档
- 解压进入文件夹
![这里写图片描述](http://img.blog.csdn.net/20161129125333568)
- 点击进入首页
![这里写图片描述](http://img.blog.csdn.net/20161129125407850)
- 找到Collections进入集合部分，之后调整一下样式，点击Frames调整为窗口模式
![这里写图片描述](http://img.blog.csdn.net/20161129125442007)
## 数组和泛型
1. 我们先抛出这样的问题：Java中有大量的容器，为何**数组**特殊呢？
没有比较就没有威力(伤害)，我们从三方面来说，分别是效率，类型，保存基本类型的能力。
**效率**：数组是一种线性序列，在存储和随机访问这块的效率最高，在效率上与其他的容器相比有着天然的优势。
**类型**：数组可以持有任何类型，并提供编译器检查。泛型出现之前，其他容器把类型都看做Object类型。无法做编译期间的检查，泛型出现之后，也可以做编译期检查了。
**保存基本类型**：数组可以持有基本类型。泛型出现之前，其他容器不可以持有基本类型，泛型出现之后，其他的容器也可以持有基本类型，在内部是以触发自动包装机制来做到这一点的。
可以看到，在泛型未出现前，数组相比其他容器有着巨大的优势，也是我们单独拎出来讲的原因。
2. 数组在使用上又分为对象数组和基本类型数组
   对象数组保存的是引用，存了个地址。
   基本类型数组直接保存值。
3. 至于**泛型**，上两篇就是关于它的介绍，不懂赶紧去找资料。
4. 数组与泛型的结合
   数组和泛型不能很好的结合，我们不能实例化有参数类型的泛型数组
   ```
    List<String>[] list = new ArrayList<String>[10];
   //Cannot create a generic array of ArrayList<String>
   ```
 我们的确不能参数化类，但我们可以参数化数组
```
class classParameter<T>{
    public T[] f(T[] arg){
        return arg;
    }
}
class MethodParamenter{
    public static<T> T[] f(T[] arg){
        return arg;
    }
}
public class ParameterizedArrayType {

    public static void main(String[] args) {
        Integer[] ints = {1,2,3,4,5};
        Double[] doubles = {1.1,2.2,3.3};

        Integer[] ints2 = new classParameter<Integer>().f(ints);
        Double[] doubles2 = new classParameter<Double>().f(doubles);
        ints2 = MethodParamenter.f(ints);
        doubles2 = MethodParamenter.f(doubles);
    }
}
```
使用曲线救国来达到目的。另外，编译器确实不能让你实例化泛型数组，但它允许你创建对数组引用，有的时候我们可以利用这些**后门**来做很多事。至于更多，请参考《Java编程思想》。
## System类和Arrays类
1. ```System```类在```java.lang.object```包下，是一个十分基础的系统类，在我们平时的使用中，对```System.gc()```垃圾回收这个方法方法使用比较多，涉及到集合这块，对迁移数组这个方法使用的比较多，这里重点讲。
```
     * @param      src      the source array.
     * @param      srcPos   starting position in the source array.
     * @param      dest     the destination array.
     * @param      destPos  starting position in the destination data.
     * @param      length   the number of array elements to be copied.
     * @exception  IndexOutOfBoundsException  if copying would cause
     *               access of data outside array bounds.
     * @exception  ArrayStoreException  if an element in the <code>src</code>
     *               array could not be stored into the <code>dest</code> array
     *               because of a type mismatch.
     * @exception  NullPointerException if either <code>src</code> or
     *               <code>dest</code> is <code>null</code>.
     */
    public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);  
```
可以看到，加上了```native```关键字，说明这是一个本地方法，JVM会专门针对优化这种方法，这个方法的效率比```for```循环(迭代器)复制数组快得多，针对所要复制的类型做了重载，是一种浅复制。在对上面说到的基本类型数组和引用类型数组方面，只复制引用类型数组的对象引用，不对对象本身进行拷贝。
对于这个方法的功能，看得懂英文的同学自然就明白了，看不懂的我就说下。既然是数据迁移，那么自然就有源数组，接收数组了。可以看到这个方法所涉及的参数，第一参数就是源数组，第二个参数是复制开始的索引值，第三个是接收数组，第四个是接收开始的索引值，第五个值是复制数组的个数。说成大白话就是，把一组数据从某个地方起个头，复制N个，把复制的数据放在另一个数组的中，又从该数组的某个地方开始放起。
2. ```Arrays```类是集合类库中一个很重要的类，提供了```sort()```排序，```copyOf()```复制，```binarySearch()```搜寻数据，```fill()```填充，```equals()```判断值和类型是否相同，```asList()```返回一个List集合，```toString()```返回数组的String形式，```hashCode() ```返回数组hash值。因为数组在集合中无处不在，我们单独拎出来说。
## CompareTo()和equals()
我们只需清楚```CompareTo()```和```equals()```之间的区别即可，前者单独比较值的大小，后者不但比较值，还比较值和类型，只要有一个地方不同，就返回```false```。
## 设计模式和类库结构图
**设计模式**
集合这种Java重要的容器组件，设计他们的Java专家们肯定在中间使用的各种各样的模式。我个人是新手，不懂这些东西。我的思路是尽量不去牵扯这些太复杂的东西，干扰自己当前研究的重点，但知道使用了设计模式这个东西还是很有必要的。有能力的同学可以去看API文档，看设计模式，印证它的设计思想。
**Java集合类结构图**
 了解大概的东西即可。
![这里写图片描述](http://img.blog.csdn.net/20161129125510293)
## 数据结构
容器既然存储数据，就涉及到各种各样的处理数据的数据结构。数组，双链表，hash算法，hash碰撞，栈，堆，树，什么的。具体到研究某个具体类实现的方式再来说，看不懂也没关系，丢一边，总之要在意识上知道这个东西的存在，刻意去看数据结构和算法的书，那就钻牛角尖了。
##《Effective Java》
这是本书，配合《Java编程思想》有奇效。之所以推荐它，是因为Java集合的类库设计者就是它的作者Joshua Bloch，作者对Java类库设计的套路肯定会在这本书里体现出来。我们不需要一板一眼的全读，就读涉及到Java集合类库这一部分，哪里出现读哪里。
## Eclipse反编译工具
要研究Java集合，免不了要直接去看源码，那就需要反编译工具。Eclipse和IDEA都有自己的反编译工具，以Eclipse为例，来安装反编译工具。
- 点击Window--->Eclipse Marketplace
![这里写图片描述](http://img.blog.csdn.net/20161129125548900)
- 搜索"反编译"
![这里写图片描述](http://img.blog.csdn.net/20161129125630652)
- 点击安装，等待完成
![这里写图片描述](http://img.blog.csdn.net/20161129125654840)
- 使用，以Collection集合为例，鼠标点击类名，直接进入。
![这里写图片描述](http://img.blog.csdn.net/20161129125717480)
- 窗口上方会出现反编译器几个汉字，点击菜单栏能选择使用哪个反编译器。更多的用法请自己Google。
![这里写图片描述](http://img.blog.csdn.net/20161129125730809)
## Java常用命令
这些常用命令请下载官方文档，英文好的同学直接看就行，英文不好的就用Google搜索汉化的文章吧，反正知道怎么用就行。我这里截个图。
![这里写图片描述](http://img.blog.csdn.net/20161129125921264)
对于常用的命令，一般是javap和jconsole，一个是用来反编译class文件，看编译细节，一个是用来比较集合不同数据结构实现时，看虚拟机内存使用情况，从而知道它们的优缺点。
例如这样一段代码
```
import java.awt.*;
import java.applet.*;

public class DocFooter extends Applet {
        String date;
        String email;

        public void init() {
                resize(500,100);
                date = getParameter("LAST_UPDATED");
                email = getParameter("EMAIL");
        }

        public void paint(Graphics g) {
                g.drawString(date + " by ",100, 15);
                g.drawString(email,290,15);
        }
}
```
- 使用```javap -c DocFooter.class```来看编译完后的字节码
```
Compiled from "DocFooter.java"
public class DocFooter extends java.applet.Applet {
  java.lang.String date;

  java.lang.String email;

  public DocFooter();
    Code:
       0: aload_0       
       1: invokespecial #1                  // Method java/applet/Applet."<init>":()V
       4: return        

  public void init();
    Code:
       0: aload_0       
       1: sipush        500
       4: bipush        100
       6: invokevirtual #2                  // Method resize:(II)V
       9: aload_0       
      10: aload_0       
      11: ldc           #3                  // String LAST_UPDATED
      13: invokevirtual #4                  // Method getParameter:(Ljava/lang/String;)Ljava/lang/String;
      16: putfield      #5                  // Field date:Ljava/lang/String;
      19: aload_0       
      20: aload_0       
      21: ldc           #6                  // String EMAIL
      23: invokevirtual #4                  // Method getParameter:(Ljava/lang/String;)Ljava/lang/String;
      26: putfield      #7                  // Field email:Ljava/lang/String;
      29: return        

  public void paint(java.awt.Graphics);
    Code:
       0: aload_1       
       1: new           #8                  // class java/lang/StringBuilder
       4: dup           
       5: invokespecial #9                  // Method java/lang/StringBuilder."<init>":()V
       8: aload_0       
       9: getfield      #5                  // Field date:Ljava/lang/String;
      12: invokevirtual #10                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      15: ldc           #11                 // String  by
      17: invokevirtual #10                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      20: invokevirtual #12                 // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      23: bipush        100
      25: bipush        15
      27: invokevirtual #13                 // Method java/awt/Graphics.drawString:(Ljava/lang/String;II)V
      30: aload_1       
      31: aload_0       
      32: getfield      #7                  // Field email:Ljava/lang/String;
      35: sipush        290
      38: bipush        15
      40: invokevirtual #13                 // Method java/awt/Graphics.drawString:(Ljava/lang/String;II)V
      43: return        
}
```
- 使用```jconsole```来参看JVM内存使用情况
![这里写图片描述](http://img.blog.csdn.net/20161129125816497)
- 点进去，就可以在控制台看到虚拟机的使用情况
![这里写图片描述](http://img.blog.csdn.net/20161129125904388)
## Google和Stackoverflow
作为技术人员，fangqiang不是个很难的事吧，遇到不懂的就问这两个，现在不都面向Google和Stackoverflow编程吗?
## 总结
Java集合是一个综合性的类库，运用到很多知识。例如：设计模式，泛型，数组，数据结构，序列化，克隆，多线程...等等。这就要求我们对这些知识要有基本的意识，不懂没关系，这里面很多我也不懂，但你要知道有这回事。不然钻了牛角尖，陷入到细节里出不来就麻烦了。我们的目标是把容器基本的增删改查，迭代器，扩容方式，效率分析，多线程使用情况，内部使用的数据结构研究一遍。这样有助于我们灵活的使用容器，分而治之，而不是千篇一律的```new ArrayList()```和```new HashMap()```。同时，为研究Java集合做一些准备工作是很有必要的，这是我们学习态度的表现。
## 后话
最后说一个自己的事，没时间的同学就不要看了。我之前因为深感工作无力(太菜了)，想搞点事。看到时下NodeJS非常火，就想做一个全栈。之后跟着百度前端学院的课程，学了三个月，学HTML，CSS，JS，不停的做任务打怪升级。但学到后面，却茫然了。不是说我毅力不行(公司除了加班的，就我一个人最后走)，而是学这玩意干什么，意义在哪？我放着好好的Java不学，不好好的做一个JavaWeb工程师，自己的Java技术都是个刚入门级别的，就这也要，那也要，像个孩子一样贪得无厌。
之后自己又老是一个人呆房间，连个鬼也没有，常常自言自语，不停的想些不愉快的往事，不停地刺激自己。这样一个恶性循环，竟然有些抑郁的倾向。意识到这样下去会毁了自己，我就周末去父母那里，或者和同学一起玩。到最严重的时候，我眼睛看周围的事物都是幻觉，真的是现实世界，脑子里想的世界，傻傻分不清。到最后，我搬离了自己的那个小房子，换了个看起像样子的，厨具，电器什么的，都弄了一套，尽量给自己一个舒适的环境，想吃什么吃什么，想去哪里玩马上去，年轻也不在乎钱。换房子和买电器中间遇到一些事，吃了些小亏，看起来也没啥。之后慢慢走上正轨，摆平了心态，该留的留，该扔的扔。到这里就过去半年了，又重新拿起《Java编程思想》看，时不待我，不能做充分的准备，只能把重点的东西看完，再来补上以前的坑。
我说的这些牢骚，目的是想表达学习也要有一个强者的心态。学习上哪些重要，哪些不重要，心中要有数。重要的，你就马上去做，不重要的，那就要暂时或者永久的放下，别把当前最重要的东西耽误了。学Java也好，学NodeJS也好，都是门技术，都是用来做事的，不是说哪个火就去做哪个，把一门技术玩通，那其他的我也有能力玩通。而如果是一个弱者的心态，即怕学习，怕吃苦，又怕没有高工资，怕技术不强过不了笔试面试，遭人嫌弃。那么这种患得患失的心态，就是你弱者心态的体现。所以学习同样体现一个人的性格啊。
