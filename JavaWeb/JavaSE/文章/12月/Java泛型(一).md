先说读后感
---
为什么把读后感放在最前面说呢？我看完这一章节的时候，发现自己完全没有领悟到bruce写这一节的意义所在，也就是这一节我完全看懵了，八成的东西，没有掌握。之后连看了两遍，发现还是不能。真的，当时我严重怀疑是自己接受能力差，还是bruce没写好，还是中文翻译没翻译好。总之，先找个背锅的出来。但我知道，这样做并不能解决问题，还是得理解啊。没办法，咱们有个好朋友，google，于是我找啊找，还是没找到一篇我觉得能读进的，真的请原谅我能力太差，最后终于在我老是鄙视的百度帮助下，找到了一篇。
>  [Java总结篇系列:泛型](http://www.cnblogs.com/lwbqqyumidi/p/3837629.html)。
##### 先说明，如果你看完这篇能理解泛型基本的东西，就不要看下面的内容了。作者功力深厚，写的真是通俗易懂，简洁明了。
#### Java泛型的作用
Java泛型主要是为了把***类型参数化***，最主要的目的就是为下一部分的集合打基础，可以这样说，Java类型信息，Java泛型，Java集合就是一体的，是一条线上的蚂蚱。在编程思想中，这三部分也是连在一起的，作者煞费精力，步步为营的苦心，咱们要去理解它。
我们看一个例子:
```
public class GenericTest {

       public static void main(String[] args) {

    	   List list = new ArrayList();
    	   list.add("sdf");
    	   list.add("sdf");
    	   list.add("sdf");
    	   list.add(1);
    	   for (int i =1; i < list.size(); i++) {
			  String string = (String) list.get(i);
			  System.out.println(string);
		}
	}
}
```
***结果***
> Picked up _JAVA_OPTIONS:   -Dawt.useSystemAAFontSettings=gasp
> Exception in thread "main" sdf
> sdf
> java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
> at cn.com.github.generic.GenericTest.main(GenericTest.java:16)

***分析和结论***
我们可以看到，报了```java.lang.ClassCastException```异常，也就是在没有泛型之前，我们心中不知道集合类型之前，随便的向下转型，会出很多的错误，这些警告虽然IDE会提醒我们，但很多人并不会在意。而泛型就可以解决它。如果在集合括号里加上泛型，那么在编译阶段我们就可以避免这些错误的出现。
#### 泛型接口，泛型类，泛型方法
三者也就是泛化的接口，类，和方法。这么做的目的，是为了最大限度的消除类型对代码的约束，使得代码能更加通用的适用于不同的接口，类，方法。还不理解，我们以Java集合list部分的源码做例子。
```
    //泛化的list接口
public interface List<E> extends Collection<E> {}
   //泛化的类
public class ArrayList<E> extends AbstractList<E>

        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
   //泛化的方法
  public E set(int index, E element) {}
  public E get(int index) {}
}
```
***结论***
可以看到，Java集合将泛型运用得无处不在，为的就是消除类型不同对集合这种存储大量数据容器的影响，解决掉了这个硬伤，Java集合的作者才能放手去写各种集合的实现，使之能存储各种各样的数据。Joshua Bloch凭借集合类库拿遍Java各大奖项，背后的泛型要算上一大功臣。
***泛化方法和泛化类的一些差异***
泛化方法和泛化类之间的差异主要体现在使用时是否需要指明参数类型。在Java中，使用泛化类时需要指明类型信息的，而使用泛化方法不需要，我们下面举一个例子
```
public class GenericTest1<T> {

      public <T> void f(T x){
    	  System.out.println(x.getClass().getName());
      }
      public static void main(String[] args) {
    	       //没见过这种用法吧
		 // GenericTest<T> test = new GenericTest<T>();
    	 GenericTest1 gs = new GenericTest1<>();
         //泛型方法是可以随便用的
    	 gs.f("");
    	 gs.f(1);
    	 gs.f(1.0);
    	 gs.f(1.0F);
    	 gs.f(gs);
	}
}
```
***分析***
之所以产生这种差异，是因为编译器***自动类型推断***机制，帮我们自动找出了类型参数。另外一个地方要注意的是基本类型无法作为类型参数，在我们当前方法中能用是因为使用了***自动打包***机制，基本类型变成了它们对应的包装类型了。
#### 泛型分析
看完上面，也许你还是不了解泛型的含义。我们就来看任何编程语言的书写方式。
> 1.先定义方法，方法使用的是形参。
> 2.调用方法，往方法中传入实参。

那么你可以看到，形参是对实参的抽象。假设有一个这样类型的数据在方法中运行，这是人类抽象思维的体现，也是编程语言提高生产效率的一个具体例子。
那么到了泛型，我们就是在这个抽象思维上再抽象一次，我们把不同的数据类型也看成同一种类型，这样做，又一次提高了编程语言的生产效率。

#### 类型擦除
Java的设计者们在设计Java时，没有考虑将泛型带入Java运行(Runtime)阶段，而是在Java编译(Build)阶段，去掉了具体的类型信息，这就是类型擦除。
例如：
***我们可以这样写***： ```ArrayList.class```
***但绝不能这样写***：```ArrayList<Integer>.class```
下面举一个例子:
```
public class TestGeneric {
     public static void main(String[] args) {
             ArrayList<String> strings = new ArrayList<String>();
             strings.add("秋香");
             System.out.println(strings);
             ArrayList<Integer> integer = new ArrayList<Integer>();    
             integer.add(1);
             System.out.println(integer);
             System.out.println(strings.getClass().getName() == integer.getClass().getName());
    }
}
```
***输出结果***
> [秋香]
> [1]
> true

我们使用```javap -c TestGeneric.class``` 反编译class文件
```
$ javap -c TestGeneric.class
Compiled from "TestGeneric.java"
public class cn.com.github.generic.TestGeneric {
  public cn.com.github.generic.TestGeneric();
    Code:
       0: aload_0
       1: invokespecial #8                  // Method java/lang/Object."<init>":()V
       4: return
  public static void main(java.lang.String[]);
    Code:
       0: new           #16                 // class java/util/ArrayList
       3: dup
       4: invokespecial #18                 // Method java/util/ArrayList."<init>":()V
       7: astore_1
       8: aload_1
       9: ldc           #19                 // String ▒▒▒▒
      11: invokevirtual #21                 // Method java/util/ArrayList.add:(Ljava/lang/Object;)Z
      14: pop
      15: getstatic     #25                 // Field java/lang/System.out:Ljava/io/PrintStream;
      18: aload_1
      19: invokevirtual #31                 // Method java/io/PrintStream.println:(Ljava/lang/Object;)V
      22: new           #16                 // class java/util/ArrayList
      25: dup
      26: invokespecial #18                 // Method java/util/ArrayList."<init>":()V
      29: astore_2
      30: aload_2
      31: iconst_1
      32: invokestatic  #37                 // Method java/lang/Integer.valueOf:(I)Ljava/lang/Integer;
      35: invokevirtual #21                 // Method java/util/ArrayList.add:(Ljava/lang/Object;)Z
      38: pop
      39: getstatic     #25                 // Field java/lang/System.out:Ljava/io/PrintStream;
      42: aload_2
      43: invokevirtual #31                 // Method java/io/PrintStream.println:(Ljava/lang/Object;)V
      46: getstatic     #25                 // Field java/lang/System.out:Ljava/io/PrintStream;
      49: aload_1
      50: invokevirtual #43                 // Method java/lang/Object.getClass:()Ljava/lang/Class;
      53: invokevirtual #47                 // Method java/lang/Class.getName:()Ljava/lang/String;
      56: aload_2
      57: invokevirtual #43                 // Method java/lang/Object.getClass:()Ljava/lang/Class;
      60: invokevirtual #47                 // Method java/lang/Class.getName:()Ljava/lang/String;
      63: if_acmpne     70
      66: iconst_1
      67: goto          71
      70: iconst_0
      71: invokevirtual #53                 // Method java/io/PrintStream.println:(Z)V
      74: return
}
```
***分析***
在第9行，此时类型还是String，到第11行，调用Java虚拟机，运行期间加入的是一个Object类型，而不是我们在Java文件中写的String类型。同样到了32步，这个时候还是Integer类型，而到了35步马上就是Object类型，再看到第53步和60步，运行时的类型统统都是Object。
从这里可以看出，在编译Java文件后，在编译完的class文件中，String类型也好，Integer类型也好，通通擦除了，全部看做Object类型。看console结果，打印了true，更加毫无疑问。
***结论***
我们可以看到，泛型仅仅只能在Java编译(Build)阶段去检查类型信息，不能在运行的时候去检查。也就是说，我们平时的泛型写法，通通都是在文字上骗自己，泛型通过擦除来实现，所有的类型都变成了他们的原生类型。Java虚拟机运行时，只知道他们的原生类型，不知道具体的类型。因此对于泛型，一个残酷的事实就是：***在泛型代码内部，无法获得任何有关泛型参数类型信息***。
所以***理解擦除以及如何处理它，是学习Java泛型时面临的最大障碍***。