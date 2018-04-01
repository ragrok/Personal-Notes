1.Java内存划分
---
##### Java内存空间分为五个部分：堆，栈，方法区，本地方法区，寄存器。
    a. 寄存器：给CPU使用。
    b. 本地方法区：和系统底层方法相关，比如Windows本地方法，native关键字修饰。
    c. 栈内存：存储的都是局部变量（函数中定义的变量、函数上的形参、语句中的变量）。只要在方法中定义的变量都是局部变量。局部变量都有所属，属于某一方法。 一旦变量的生命周期结束，该变量就被释放。      
    d. 堆内存：存储的都是实体(对象)。凡是用new创建的，都在堆内存里面，开辟一片空间，创建一个对应的实体。每一个实体都有一个首地址值。对象在堆内存中存在的目的就是为了存储（封装）成员变量（数据）的。堆内存的变量都有默认初始化值。不同数据类型不一样。int-0,  double-0.0,  boolean-false,  char-'\u0000', 引用数据类型-null。当实体不再使用时（即没有变量对其引用，指向该对象时），就会被垃圾回收机制处理，后面博客的多线程中会讲到。
    e. 方法区：后续博客说到对象的创建过程、静态变量时，会详解。

2. 基本数据类型
---
![4](../../图片/12月/4.png) 
3. 数组
--- 
 在Java中，数组被看成一个对象，定义的方式有两种，int[] a和int a[]；第二种是c/c++对数组的定义方式，java推荐第一种。
- 3.1 一维数组：
    1. 先new对象，再初始化，默认值是0
       a. int [] a = new a[i]；
      之后再来赋值是不对的
      例如 a = {1,2,3}; //常量不可以再赋值一次
    2. 直接初始化
       b. int a = {1,2,3};
- 3.2 二维数组：
    1. 先new对象，再初始化
      a. int[][] a = new int[3][5]
      a1.  int[][] array = new int[3][];
        array[0] = new int[3];
        array[1] = new int[1];
        array[2] = new int[2];
    2. 直接赋值初始化
      b. int[][] b = {{1,2,3}，{4,5,5}，{7,8,9}}；
    3. new 完直接初始化
    c. int[][] a = new int[][] {{1,1,1,1,1}, {2,2,2,2,2}, {3,3,3,3,3} };
  4. 二维数组要注意长度问题
```
        int[][] array = new int[3][];  
        array[0] = new int[3];  
        array[1] = new int[1];  
        array[2] = new int[2];  
        /*array[1][2] = 34;*///ArrayIndexOutOfBoundsException  
        array[0][1] = 89;  
        array[2][1] = 44;  
        System.out.print("array length:");  
        System.out.println(array.length);/*二维数组的长度*/  
        System.out.print("array[2] length:");  
        System.out.println(array[2].length);/*某一个一维数组的长度*/  
```
5.二维数组的应用：
二维数组一般用在坐标，矩阵的应用中。
4. 参考文章
- [春哥JavaSE实战: Jdk配置，数组及其应用，栈和堆内存详解](http://blog.csdn.net/zhongkelee/article/details/43239355)
- [Java中一维数组和二维数组的定义](http://blog.csdn.net/gideal_wang/article/details/3647837)
- Thinking in Java

