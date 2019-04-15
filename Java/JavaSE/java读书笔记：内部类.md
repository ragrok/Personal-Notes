#### 定义
- 把类写在其他类的内部，和Ecmascript的闭包很相似，可以理解为类的私有类。
- 为何拥有访问外部类方法和属性的原理：当外围类的对象创建了一个内部类对象时，此内部类会秘密捕获一个指向外围类对象的引用，通过引用来访问外部类的属性和方法，这是think in java 中的原话。
- 内部类强大之处：每个内部类都能独立地继承一个（接口的）实现，所以无论外围类是否已经继承了某个（接口的）实现，对于内部类都没有影响。     
- 内部类的作用：在我们程序设计中有时候会存在一些使用接口很难解决的问题，这个时候我们可以利用内部类提供的、可以继承多个具体的或者抽象的类的能力来解决这些程序设计问题。可以这样说，接口只是解决了部分问题，而内部类使得多重继承的解决方案变得更加完整。
- 更多请参考这篇文章：[详解内部类](http://www.cnblogs.com/chenssy/p/3388487.html)
﻿
#### 基础使用和创建对象
```
package cn.com.github;
public class DotThis {
    void f() {
        System.out.println("DontThis.f()");
    }
    public class Inners {
        public DotThis outer() {
            return DotThis.this;
        }
    }
    
    public Inners inners(){
        return new Inners();
    }
    
    //可以使用私有内部类来隐藏实现细节
    private class privateClass{
        private int i = 11;
        public int value(){
            return i;
        }
    }
    //可以使用保护类来隐藏实现细节
    protected class protectedClass{
        private int i = 10;
        public int value(){
            return i;
        }
    }
   
    public static void main(String[] args) {
        DotThis oDotThis = new DotThis();
        DotThis.Inners dti = oDotThis.inners();
        dti.outer().f();
        //创建内部类的对象必须通过使用外部类的对象来创建
        DotThis.Inners dts = oDotThis.new Inners();
        dts.outer().f();
        //创建私有类的对象
        privateClass class1 = oDotThis.new privateClass();
         int s = class1.value();
         //10
         System.out.println(s);
        //创建保护类型
         protectedClass class2 = oDotThis.new protectedClass();
         s = class2.value();
         //10
         System.out.println(s);
         
    }
}
```
#### 使用内部类来实现多重继承
```
package cn.com.github;
//使用内部类来实现多重继承
interface A {
}
interface B {
}
class X implements A, B {
}
class Y implements A {
    B makeb() {
        // 创建内部类，相当于又开了个口子，让内部类来实现外围类不能做的事，使整个类的机制更加灵活
        // 例如继承一个类的时候又能继承另一个类
        return new B() {
        };
    }
}
public class PowerInner {
    static void taskesA(A a) {
    }
    static void taskesB(B b) {
    }
    public static void main(String[] args) {
        X x = new X();
        Y y = new Y();
        taskesA(x);
        taskesA(y);
        taskesB(x);
        taskesB(y.makeb());
    }
}
```
#### 多重继承
```
package cn.com.github;
class D {
}
abstract class E {
}
class Z extends D {
    // 使用内部类在另一个角度实现了同时继承两个类的特性，相当于开了个小号
    // 内部类的威力展现了
    E makeE() {
        return new E() {
        };
    }
}
public class PowerInner2 {
    static void taskesD(D d) {
    }
    static void taskesE(E e) {
    }
    
    public static void main(String[] args) {
        Z z = new Z();
        taskesD(z);
        //相当于让z的一部分实现了e的特性，补全了单继承的缺陷
        taskesE(z.makeE());
    }
}
```
#### 参考
- [详解内部类](http://www.cnblogs.com/chenssy/p/3388487.html)
- Think in java

