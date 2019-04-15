前面一篇讲了java泛型的基本概念，作用以及如何构建泛型接口，泛型类，泛型方法，也说到了类型擦除。这一篇着重的介绍java类型擦除的内部原理和边界问题。
## 神秘的擦除
在这一部分，作者详细的解释了java为什么要用擦除来实现泛型。事实就是泛型是在jdk1.5的时候引入的，之前的代码没有泛型这个东西，为了向后兼容原先的代码，java做出了一个痛苦的选择，在编译期间将类型信息擦除，以便和原来的代码能通用，这样我们就可以继续使用原先的类库，也对我们的老项目不会有什么影响。从今天的角度看，这个痛苦的选择成了java沉重的包袱，也是java为人诟病的一点。
擦除毫无疑问带来了问题，为了在不破坏现有类库的情况，将泛型融入java。泛型就不能显式的引用运行时类型信息，例如转型，instanceof动态判定类型，new表达式，也就是前面部分说的泛型只能在编译阶段起作用，不能在运行期间起作用。
那么，大家会很好奇，***泛型既然在编译阶段把类型信息去掉了，运行阶段也不知道它的类型，最后看到的结果不都是该是哪样还是哪样吗?***
到这里，就涉及到java泛型的核心部分，***边界处的动作***。
## 边界
***边界：即对象进入和离开方法的地点***。
我们来看一个例子:
```
public class FiledListMarker<T> {

	 List<T> create(T t, int n){
		 List<T> result = new ArrayList<T>();
		 for(int i = 0; i < n; i++){
			 result.add(t);
		 }
		return result;
	 }
	 public static void main(String[] args) {
		 FiledListMarker<String> sFiledListMarker = 
				 new FiledListMarker<String>();
		 List<String> list = sFiledListMarker.create("hello", 4);
		 System.out.println(list);
	}
}
```
> [hello, hello, hello, hello]

***分析***
可以看到，即使泛型有擦除动作，移除掉了在方法或类内部有关的实际信息，但编译器仍旧可以确保在方法或类中使用类型的内部一致性。现在就剩下一个问题：在哪里擦除的？又是在哪里恢复的？
我们接着看下一个例子
```
public class SimpleHolder {
      
	private Object object;

	public Object getObject() {
		return object;
	}

	public void setObject(Object object) {
		this.object = object;
	}
	
	public static void main(String[] args) {
		SimpleHolder holder = new SimpleHolder();
		holder.setObject("秋香");
		String string  = (String)holder.getObject();
	}
}
```
之后使用```javap -c SimpleHolder.class```命令反编译class文件。
```
ragrok@ragrok-debian:/home/gitspace/practiceJava/Test/bin/cn/com/github/generic$ javap -c SimpleHolder.class 
Picked up _JAVA_OPTIONS:   -Dawt.useSystemAAFontSettings=gasp
Compiled from "SimpleHolder.java"
public class cn.com.github.generic.SimpleHolder {
  public cn.com.github.generic.SimpleHolder();
    Code:
       0: aload_0
       1: invokespecial #10                 // Method java/lang/Object."<init>":()V
       4: return

  public java.lang.Object getObject();
    Code:
       0: aload_0
       1: getfield      #18                 // Field object:Ljava/lang/Object;
       4: areturn

  public void setObject(java.lang.Object);
    Code:
       0: aload_0
       1: aload_1
       2: putfield      #18                 // Field object:Ljava/lang/Object;
       5: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #1                  // class cn/com/github/generic/SimpleHolder
       3: dup
       4: invokespecial #24                 // Method "<init>":()V
       7: astore_1
       8: aload_1
       9: ldc           #25                 // String 秋香
      11: invokevirtual #27                 // Method setObject:(Ljava/lang/Object;)V
      14: aload_1
      15: invokevirtual #29                 // Method getObject:()Ljava/lang/Object;
      18: checkcast     #31                 // class java/lang/String
      21: astore_2
      22: return
```
***分析***
我们可以看到main()方法下，set()方法和get()方法直接存储值，转型是在调用get()方法时接受检查的。
接着我们使用泛型来写一下
```
public class GenericHolder<T> {

      private T object;

	public T getObject() {
		return object;
	}

	public void setObject(T object) {
		this.object = object;
	}
      
	public static void main(String[] args) {
		GenericHolder<String> sHolder = new 
				GenericHolder<String>();
		sHolder.setObject("秋香");
		String string = sHolder.getObject();
	}
}
```
接着使用java命令行:```javap -c GenericHolder.class```反编译class文件。
```
ragrok@ragrok-debian:/home/gitspace/practiceJava/Test/bin/cn/com/github/generic$ javap -c GenericHolder.class 
Picked up _JAVA_OPTIONS:   -Dawt.useSystemAAFontSettings=gasp
Compiled from "GenericHolder.java"
public class cn.com.github.generic.GenericHolder<T> {
  public cn.com.github.generic.GenericHolder();
    Code:
       0: aload_0
       1: invokespecial #12                 // Method java/lang/Object."<init>":()V
       4: return

  public T getObject();
    Code:
       0: aload_0
       1: getfield      #23                 // Field object:Ljava/lang/Object;
       4: areturn

  public void setObject(T);
    Code:
       0: aload_0
       1: aload_1
       2: putfield      #23                 // Field object:Ljava/lang/Object;
       5: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #1                  // class cn/com/github/generic/GenericHolder
       3: dup
       4: invokespecial #30                 // Method "<init>":()V
       7: astore_1
       8: aload_1
       9: ldc           #31                 // String 秋香
      11: invokevirtual #33                 // Method setObject:(Ljava/lang/Object;)V
      14: aload_1
      15: invokevirtual #35                 // Method getObject:()Ljava/lang/Object;
      18: checkcast     #37                 // class java/lang/String
      21: astore_2
      22: return
}
```
***分析***
同使用Object一样，使用泛型也是使用set()方法和get()方法直接存储值，转型同样是在调用get()方法时接受检查的。而生成的代码呢，可以看到，一模一样。
***结论***
***在泛型中的所有动作，都发生在边界处--------对传递进来的值进行额外的编译期检查，并插入对传递出去值的转型***。
现在，就可以回答我们前面的问题了，在边界处擦除，在边界处恢复。   我们就可以下一个这样的结论：***边界就是发生动作的地方***。
**java擦除示意图**
![擦除示意图](../../图片/12月/1.png)
到这里，java泛型最难理解的东西也就一清二楚了。
## 擦除的补偿
擦除丢失了在泛型代码中执行某些操作的能力，但如果我们引入类型标签，可以使用动态的isInstance()方法来检查类型。
下面是一个例子:
```
class Building {}

class House extends Building {}

public class ClassTypeCapture<T> {

	Class<T> kind;

	public ClassTypeCapture(Class<T> kind) {
		this.kind = kind;
	}
      
	public boolean f(Object arg){
		return kind.isInstance(arg);
	}
	
	public static void main(String[] args) {

		ClassTypeCapture<Building> build = 
				new ClassTypeCapture<Building>(Building.class);
		System.out.println(build.f(new Building()));
		System.out.println(build.f(new House()));
		
		ClassTypeCapture<House> build2 = 
				new ClassTypeCapture<House>(House.class);
		System.out.println(build2.f(new Building()));
		System.out.println(build2.f(new House()));
	}
}
```
结果:
> true
 true
false
> true
##  类型通配符
这部分可以参考泛型(一)，作者说的很好，我就大致的讲一下。
java使用类型通配符```?```来代替具体的***类型实参***，注意这是类型实参，***不是***类型形参。
我们知道```ArrayList<Number>```和```ArrayList<Integer>```在泛型的中都是ArrayList类型，但在逻辑上，即使```Number```是```Integer```的父类，但加入容器之后他们显然不是父子关系，这个时候需要一个符号来统一他们，但又能在实际使用时区分两者的差异，于是通配符就产生了。
至于```extends```关键字，则是规定了父类是谁，规定了上限，```super```则是规定了子类是谁，规定了下限，这个很好理解，就不具体说了。
