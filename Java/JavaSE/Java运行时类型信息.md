#### RTTI(Runtime Type Information):运行时类型信息可以在程序运行时发现和使用类型信息。
#### Java在运行时识别对象类型和信息的两种方式:
> 1.传统的RTTI，假定在编译时就已经知道了所有的类型信息。
> 2.反射机制:运行时再去检测和使用类的信息。
#### 为什么需要RTTI
先看下面这幅图，基类是Shape，派生类是Circle，Square，Triangle，他们都继承了基类的方法draw().
![这里写图片描述](http://img.blog.csdn.net/20161119181830651)
假设我们要新增一个派生类，那么使用***基类引用派生类的对象***就显得异常的方便，也不会影响原来的代码。
而draw()方法在所有派生类中都会被覆盖，在运行时使用了***动态绑定***这个机制。当我们使用泛化的Shape来调用子类draw()方法时，也能产生正确的行为，这就是***多态***的原理。
**RTTI例子**
```
class Shape {
    public void draw() {
        System.out.println(this + "====:Shape Class");
    };
}
class Circle extends Shape {
    public void draw() {
        System.out.println(this + "====:Circle Class");
    }
}
class Triangle extends Shape {
    public void draw() {
        System.out.println(this + "====:Triangle Class");
    }
}
//新增的派生类
class Oblong  extends Shape {
    public void draw() {
        System.out.println(this + "====:Oblong Class");
    }
}
public class TestRTTI {
    public static void main(String[] args) {
          Shape shape = new Circle();
          shape.draw();
          shape = new Triangle();
          shape.draw();
          //引用新增的派生类
          shape = new Oblong();
          shape.draw();
    }
}  
```
***输出结果***
>  cn.com.github.rtti.Circle@15db9742====:Circle Class
> cn.com.github.rtti.Triangle@6d06d69c====:Triangle Class
> cn.com.github.rtti.Oblong@7852e922====:Oblong Class
#### Class对象
要理解RTTI在Java中的工作原理，首先要知道类型信息在运行时的工作机制,这项工作由称为Class对象的特殊对象完成，它包含了与类有关的信息。至于这里面细 致的原理，简单介绍来说，就是每当我们新建一个java文件时，Eclipse或者Idea这种IDE就会调用Javac编译器将java文件编译为class文件，之后JVM就调用类加载器(ClassLoader)根据实际情况来动态处理。我们知道Java程序在它开始运行之前并非被完全加载，各部分都是根据必要时才加载的。在实际的情况中，类加载器首先检查这个类的对象是否已经加载，如果尚未加载，默认的类加载器就会根据类名查找class文件来创建对象，这种动态加载的机制和JS这种脚本语言的运行原理非常相似，也使得Java与C++这种静态加载的语言相比有非常大的灵活性。
***下面我们来看一个例子***
```
class Candy {
    static {
        System.out.println("Loanding Candy");
    }
}
class Gum {
    static {
        System.out.println("Loanding Gum");
    }
}
class Cookie {
    static {
        System.out.println("Loanding Cookie");
    }
}
public class SweetShop {
      public static void main(String[] args) {
        System.out.println("inside main");
        new Candy();
        System.out.println("After createing Candy");
        try {
            Class.forName("Gum");
        } catch (Exception e) {
            // TODO: handle exception
            System.out.println("Can't found Gum");
        }
        System.out.println("After class.forName(Gum)");
        new Cookie();
        System.out.println("After createing cookie");
    }   
}
```
***输出结果***
>inside main
>Loanding Candy
>After createing Candy
>Can't found Gum
>After class.forName(Gum)
>Loanding Cookie
>After createing cookie

***分析***
从结果我们可以看到，```Can't found Gum```,说明根本就没有创建```Gum```对象，使用```Class.forName("Gum")```这条语句，类加载器根本没有找到Gum.class文件，如果我们把路径补全，写成这样：```Class.forName("cn.com.github.rtti.Gum")```
你就会看到这样的结果：
> Loanding Gum

***补充***
所以我们在使用Class.forName()时，要记着类加载器会去当前工程的编译文件夹中寻找class文件，如果没有正确的路径，是无法创建对象的。
#### 类字面常量
这是另一种方法来生成对Class对象的引用，写法:```ClassName.class```。
优点：和使用```Class.forName()```比较起来，它简单，安全，高效，在编译时会受到检查(不需要放在try语句块中)，更重要的时，它不仅可以使用在普通的类中，还可以应用于接口，数组及其他基本数据类型，在实际编程时，我们优先推荐这种写法。
对于普通包装器类型：
```
boolean.class ============ Boolean.TYPE
char.class  ============ Char.TYPE
byte.class  ============ Byte.TYPE
short.class ============ Short.TYPE
int.class   ============ Integer.TYPE
long.class  ============ Long.TYPE
```
注意点：使用```.class```来创建对象时，不会自动初始化该Class对象，这是它运行的步骤
1. 加载：找到编译文件夹下的字节码，创建Class对象。
2. 链接：验证字节码，为静态域分配空间。
3. 初始化：当该类拥有基类时，初始化，执行静态初始化器和静态代码块。

***我们来看一个例子***
```
class Initable {
    static {
        System.out.println("Loading Initable");
    }
}
class Initable1 {
    static {
        System.out.println("Loading Initable1");
    }
}
class Initable2 {
    static {
        System.out.println("Loading Initable2");
    }
}
public class ClassInit {
    public static void main(String[] args) {
        //使用.class来引用对像
         System.out.println(Initable.class);
         try {
             //使用Class.forName
            System.out.println(Class.forName("cn.com.github.rtti.Initable2"));
        } catch (ClassNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}  
```
***结果***
>class cn.com.github.rtti.Initable
>Loading Initable2
>class cn.com.github.rtti.Initable2

***分析***
从结果可以看到，使用类字面常量会使我们的对象惰性初始化，没有去具体的引用类中的方法，是不会有新对象产生的。而使用```Class.forName()```则立即初始化，调用静态代码块打印信息。
#### 泛化的Class引用
这部分的知识需要你有泛型的知识才会理解，不过没关系，泛型就在类型信息的下一节，我们可以结合着两章一起来看。事实上，我刚开始看这章的时候也是云里雾里，加上老外的东西用中文表达起来非常啰嗦，常常用几句话表达的事要写一大段，所以英文好的同学直接去看原著吧。实在看不懂的，知道有这么个东西，忘掉中文的意思继续往下面看，说不定你哪天就开窍了^_^。
Class类的使用总是指向该类的对象，有了对象，我们就可以操作该类包含的方法和静态成员，结合泛型的话，我们会省掉相当多的代码，让符合该类型的数据成员都能使用该类中定义的方法。
***泛化的Class语法定义***
```
Class intClass = int.class;
Class<Integer> intclass1 = int.class;
//使用包装类也是可以的
Class<Integer> intclass1 = Integer.class;
```
如果这样来表示：
```
Class<Number> numberClass = int.class;
```
现在的Jdk1.8是会报错的，以前这个地方是个bug，因为```Integer```继承自```Number```，但也运行不了，牛头不对马嘴，怎么运行。
我们在实际的项目中看到的一般是这种：
```
Class<? extends Integer> intClass2 = int.class;  
```
这样写的好处有很多，即指明了我们基类的类型，又可以在代码编译阶段进行检查，又可以使用泛型，一举三得，何乐而不为。
关于新的转型语法Cast()，书中说用的不多，略过。
***类型转换前的检查***
RTTI拥有三种形式的检查
> 1.传统的类型转换，如果执行错误的类型转换，会抛出```ClassCastException```。
> 2.代表对象类型的Class对象，通过查询Class对象可以获取运行时所需的信息。
> 3.使用```instanceof```关键字，返回一个布尔值，告诉我们右边类是否是左边Class对象的基类。

instanceof写法，有了这个关键字，可以像打注解一样的实现动态的检查类型转换。
```
if ((X) instaceof Dog) {
      ((Dog)x).bark();
    }  
```
***instanceof与Class的等价性***
我们先看一个例子
```
class Base{}
class Derived extends Base{}
public class CompareRtti {
    static void test(Object x){
        System.out.println("Type of x:"+x.getClass());
        System.out.println("x instanceof Base:" + (x instanceof Base));
        System.out.println("Base.isInstanceof(x):"+Base.class.isInstance(x));
        System.out.println("Derived.isInstanceof(x):"+Derived.class.isInstance(x));
        System.out.println("x.getClass() == Base.class:"+(x.getClass() == Base.class));
        System.out.println("x.getClass() == Derived.class:"+(x.getClass() == Derived.class));
        System.out.println("x.getClass().equals(Derived.class):"+(x.getClass().equals(Derived.class)));
        System.out.println("x.getClass().equals(Base.class):"+(x.getClass().equals(Base.class)));
    }

    public static void main(String[] args) {
        test(new Base());
        test(new Derived());
    }
}  
```
***结果***
>Type of x:class cn.com.github.rtti.Base
>x instanceof Base:true
>Base.isInstanceof(x):true
>Derived.isInstanceof(x):false
>x.getClass() == Base.class:true
>x.getClass() == Derived.class:false
>x.getClass().equals(Derived.class):false
>x.getClass().equals(Base.class):true
>Type of x:class cn.com.github.rtti.Derived
>x instanceof Base:true
>Base.isInstanceof(x):true
>Derived.isInstanceof(x):true
>x.getClass() == Base.class:false
>x.getClass() == Derived.class:true
>x.getClass().equals(Derived.class):true
>x.getClass().equals(Base.class):false

***反射：运行时的类信息***
相比于传统的RTTI需要在编译时就需要知道类的信息相比，反射是一种在运行时才知道类的信息。之所以这样设计，
> 一时为了适应快速应用开发(RAD)，
> 二是为了在跨网络的远程平台上创建和运行对象，这种能力通常称为远程方法调用(RMI)。

在反射中，Class类和java.lang.reflect类库一起对反射的概念进行了支持。包括了得到基本的属性，方法和构造器的接口，通过getName()可以轻易的去得到类中的这些信息。
***例子***
- 首先定义一个类
```
public class ManEntity {

	private String name = "xiaowang";
	private String password = "1234";
	private int age = 123;

	private void sayHellow(){
		System.out.println("Hello,ManEntity");
	}

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public ManEntity(String name, String password, int age) {
		super();
		this.name = name;
		this.password = password;
		this.age = age;
	}
	public ManEntity(String name) {
		super();
		this.name = name;
	}
	@Override
	public String toString() {
		return "ManEntity [name=" + name + ", password=" + password + ", age=" + age + "]";
	}

	public ManEntity() {
		super();
	}




}


```
- 得到基本信息
```
public class TestReflect {

	  public static void main(String[] args) {
		try {
			//通过class.forName()得到类对象
			Class class1 = Class.forName("cn.com.github.reflect.ManEntity");
			//通过字面常亮得到类对象
			Class class2 = ManEntity.class;
			//通过new方法得到对象，通过对象间接得到类对象
			ManEntity nEntity = new ManEntity();
			Class class3 = nEntity.getClass();

			//得到所有的方法
			Method[] methods = class1.getDeclaredMethods();
			for(Method method : methods){
				System.out.println(method);
			}
//			private void cn.com.github.reflect.ManEntity.sayHellow()
//			public java.lang.String cn.com.github.reflect.ManEntity.getPassword()
//			public void cn.com.github.reflect.ManEntity.setPassword(java.lang.String)
//			public int cn.com.github.reflect.ManEntity.getAge()
//			public void cn.com.github.reflect.ManEntity.setAge(int)
//			public java.lang.String cn.com.github.reflect.ManEntity.toString()
//			public java.lang.String cn.com.github.reflect.ManEntity.getName()
//			public void cn.com.github.reflect.ManEntity.setName(java.lang.String)

			//得到所有的属性
			Field[] fields = class2.getDeclaredFields();
			for(Field field : fields){
				System.out.println(field);
			}
//			private java.lang.String cn.com.github.reflect.ManEntity.name
//			private java.lang.String cn.com.github.reflect.ManEntity.password
//			private int cn.com.github.reflect.ManEntity.age

		    //得到所有的构造器
			try {
				Constructor[] constructor = class3.getDeclaredConstructors();
				for(Constructor constructor2 : constructor){
					System.out.println(constructor2);
				}
//				public cn.com.github.reflect.ManEntity()
//				public cn.com.github.reflect.ManEntity(java.lang.String)
//				public cn.com.github.reflect.ManEntity(java.lang.String,java.lang.String,int)

			} catch (SecurityException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}

		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}
```
- 获取类中的信息并使用
```
public class TestReflect1 {

	public static void main(String[] args) {
		Class class1 = ManEntity.class;

		// 获取公有的构造器
		try {
			Constructor con = class1.getConstructor(String.class, String.class, int.class);
			Object object = con.newInstance("ragrok", "1234", 123);
			System.out.println(object.toString());
			// ManEntity [name=ragrok, password=1234, age=123]
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		// 获取私有的构造器
		try {
			Constructor con = class1.getDeclaredConstructor(String.class);
			// 开启暴力破解
			con.setAccessible(true);
			Object object = con.newInstance("秋水");
			System.out.println(object.toString());
			// ManEntity [name=秋水, password=1234, age=123]
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		// 获取公有的方法
		try {
			Constructor con = class1.getConstructor(String.class, String.class, int.class);
			Object object = con.newInstance("秋香", "1234", 123);
			Method method = class1.getMethod("getName");
			System.out.println(method.invoke(object));
			// 秋香
		} catch (Exception e) {
			// TODO: handle exception
		}

		// 获取私有方法
		try {
			Constructor con = class1.getConstructor(String.class, String.class, int.class);
			Object object = con.newInstance("秋香", "1234", 123);
			Method method = class1.getDeclaredMethod("sayHellow");
			method.setAccessible(true);
			System.out.println(method.invoke(object));
			// Hello,ManEntity
			// null 会调用一个空的tostring方法
		} catch (Exception e) {
			// TODO: handle exception
		}

		// 获取公有的属性,设值
		try {
			Constructor con = class1.getConstructor(String.class, String.class, int.class);
			Object object = con.newInstance("秋香", "1234", 123);
			Field object2 = class1.getField("age");
			object2.set(object, 18);
			System.out.println(object.toString());
			// ManEntity [name=秋香, password=1234, age=18]
		} catch (Exception e) {
			// TODO: handle exception
		}

		// 获取私有的属性，设值
		try {
			Constructor con = class1.getConstructor(String.class, String.class, int.class);
			Object object = con.newInstance("秋香", "1234", 123);
			// 找到这个私有属性
			Field object2 = class1.getDeclaredField("name");
			// 暴力破解
			object2.setAccessible(true);
			// 设值
			object2.set(object, "mark");
			System.out.println(object.toString());
			// ManEntity [name=mark, password=1234, age=123]
		} catch (Exception e) {
			// TODO: handle exception
		}

		// 获取私有的属性，取值
		try {
			Constructor con = class1.getConstructor(String.class, String.class, int.class);
			Object object = con.newInstance("秋香在哪里", "1234", 123);
			// 找到这个私有属性
			Field object2 = class1.getDeclaredField("name");
			// 暴力破解
			object2.setAccessible(true);
			// 取值
			Object object3 = object2.get(object);
			System.out.println(object3.toString());
			// 秋香在哪里
		} catch (Exception e) {
			// TODO: handle exception
		}
	}
}


```
- 通过反射机制越过泛型检查
```
public class TestReflect3 {


	public static void main(String[] args) {
		ArrayList<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(2);

		try {
			Class class1 = java.util.ArrayList.class;
			Method add = class1.getMethod("add", Object.class);
			add.invoke(list, "ragrok");
			System.out.println(list);
			//[1, 2, ragrok] very beatiful
		} catch (Exception e) {
			// TODO: handle exception
		}
	}



}

```
***结论***
泛型和反射结合起来，会产生很大的威力，很多的框架都是采用反射和泛型来达到自己的目的，比如spring的注解，就是采用这种方式来达到动态运行的目的。
总结
---
总的来说，掌握运行时真的很有必要，能帮助我们理解之后JavaEE的很多东西，也是java的在魅力所在。
