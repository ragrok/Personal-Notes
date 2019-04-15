## 引言
由前一篇的文章，大家大概可以看到Java集合框架的基本结构和重点，可以这么说Java集合的重心就在List，Set，Map，Queue还有Iterator(迭代器)上。我写这篇文章主要借鉴了李春春的博客和AlienStar的专栏，以及chenssy的博客。因为《Java编程思想》说的太散了，在没法去深入了解更多的情况下，只能去高手的文章中吸取精华。
## Collection
这部分主要分两部分，Collection接口和抽象类AbstractCollection
- Collection接口
```
//继承Iterable接口，拥有迭代数据的特性
public interface Collection<E> extends Iterable<E> {}
```
再看我们接口中的主要方法
![20](../../图片/12月/集合/20.png)
1. 添加
```
//增加单个元素
boolean add(E e);
//增加整个集合
boolean addAll(Collection<? extends E> c);
```
2. 删除
```
//删除单个元素
boolean remove(Object o);
//删除整个集合
boolean removeAll(Collection<?> c);
```
3. 判断
```
//是否包含单个元素
boolean contains(Object o);
//是否包含真个集合
boolean containsAll(Collection<?> c);
//是否相同，比较值和内容
boolean equals(Object o);
//是否为空
boolean isEmpty();
//是否有交集
boolean retainAll(Collection<?> c);
```
4. 转化数组
```
//object类型转化为数组
Object[] toArray();
//泛型转化为数组
<T> T[] toArray(T[] a);
```
5. 迭代器
```
//迭代器
Iterator<E> iterator();
```
6. 其他特性
```
//返回集合大小
int size();
//返回hashcode
int hashCode();
//清空集合
void clear();
```
可以看到此接口有判断，增加，删除，取交集，获取长度，将集合封装为数组，迭代元素的作用，这意味着所有后面的子接口和抽象类以及实现类都会拥有这些特性。
- 抽象类AbstractCollection
```
//实现Collection接口所定义的功能
public abstract class AbstractCollection<E> implements Collection<E> {}
```
1. 构造器
![20](../../图片/12月/集合/2016-12-13_101614.png)
2. 方法
![20](../../图片/12月/集合/2016-12-13_101554.png)
可以看到，就增加了一个```toString()```方法，其他部分都是实现Collection接口的方法，这样最大限度的减少了后面子类的重复书写。
## List
这里我摘取了chenssy对List部分所绘的图，看这张图，我们就能知道List部分基本的结构，对比API文档，我们的重点在List接口，AbstractList抽象类，ListIterator迭代器，ArrayList，LinkedList和四个实现类的比较上，知道这些，我们就把重点一一来解读。
首先要知道的一点，List就是有顺序的列表，简称序列。不管哪一种实现方式，始终离不开序列这两个字眼，应该明白ArrayList是数组实现的序列，LinkedList是链表实现的序列，Vector是数组实现的序列，Stack是栈实现的序列。序列多态性的表现就在这里。
- List接口
  List底部是由数组来存储实际数据，所以引入了索引，在list接口的方法中自然有了对索引的操作。
1. 方法
 ![20](../../图片/12月/集合/2016-12-13_110034.png)
2. 增加
 ```
//增加单个元素
boolean add(E e);
//增加整个集合
boolean addAll(Collection<? extends E> c);
//增加整个集合
boolean addAll(int index, Collection<? extends E> c);
//增加单个元素
void add(int index, E element);
 ```
 3. 删除
```
//删除单个元素
boolean remove(E e);
//删除整个元素
boolean removeAll(Collection<? extends E> c);
//删除整个元素
boolean removeAll(int index, Collection<? extends E> c);
//删除单个元素
void remove(int index, E element);
```
4. 判断
```
//是否包含单个元素
boolean contains(Object o);
//是否包含真个集合
boolean containsAll(Collection<?> c);
//是否相同，比较值和内容
boolean equals(Object o);
//是否为空
boolean isEmpty();
//是否有交集
boolean retainAll(Collection<?> c);
```
5. 和索引相关
```
//按照索引快速访问取值
E get(int index);
//按照索引快速访问更新值
E set(int index, E element);
//从头到尾第一次出现的索引值
int indexOf(Object o);
//从尾到头第一次出现的索引值
int lastIndexOf(Object o);
//按照索引截取list上的一部分数据
List<E> subList(int fromIndex, int toIndex);
```
6. 迭代器
```
//迭代器
Iterator<E> iterator();
//list特有的迭代器
ListIterator<E> listIterator();
//按照索引值开始迭代
ListIterator<E> listIterator(int index);
```
7. 其他特性
```
//返回集合大小
int size();
//返回hashcode
int hashCode();
//清空集合
void clear();
```
由图片可以看到，拓展了很多东西，不管是add方法，remove方法，还是Iterator，都有了属于自己独有的部分，在取值和更新值上还有get和set方法，这一切都是根据序列和索引的特性来安排的。
- AbstractList抽象类
```
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> {}
```
具体的不去说了，这一部分就是实现list接口中的方法，最大限度的让实现类共用公共的代码。
- ArrayList
首先要说的，ArrayList是我们最常用的集合类，非常便于快速访问和修改集合中的元素。为何会有这样的特性，看完这部分，你就明白了。
1. 实现接口和父类
```
//支持随机访问，克隆，序列化
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{

        }
```
2. 多线程实现方式
```
//使用Collections辅助类来实现支持多线程
List list = Collections.synchronizedList(new ArrayList(...));
```
3. 构造器和相关
```
//默认容量
private static final int DEFAULT_CAPACITY = 10;
//使用数组来存放数据
private static final Object[] EMPTY_ELEMENTDATA = {};
//默认的空数组
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
//定义存放数组
transient Object[] elementData; // non-private to simplify nested class access   
private int size;
//初始化容量构造
public ArrayList(int initialCapacity) {}
//空构造
public ArrayList() {}
//指定集合构造
public ArrayList(Collection<? extends E> c) {}
```
4. 扩容方式
```
  //定义内部私有增长方法
  private void grow(int minCapacity) {
        // overflow-conscious code
        //得到原来的数组长度
        int oldCapacity = elementData.length;
        //新的数组长度为原来的1.5倍，>>符号就是二进制左移一位，相当于除二，这是C/C++写法
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //和原来数组长度比较
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //和默认最大长度比较
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            //取Int的最大值
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        //迁移数组，很费开销，所以大家使用ArrayList时最好指定初始容量
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
关于为何扩容1.5倍，大家可以上stackoverflow上去找答案，我的理解是从资源浪费的角度来看的，ArrayList是Java用来取代Vector的，Vector的扩容方式是原来的两倍(默认情况下)，ArrayList最多是浪费1-1/1.5=0.33，而Vector是1-1/2=0.5，光从容量开销来看，Vector就占了下风。所以扩容1.5倍是最佳的区间，既不浪费太多，也不至于增长太少。
5. 取值和更新
```
//定义取值方法
public E get(int index) {
       //检查下标越界
        rangeCheck(index);
       //调用查询数组下标的方法
        return elementData(index);
    }
  //直接通过下标拿到数组内的元素  
@SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }
  //通过下标来更新值，返回原来的值  
 public E set(int index, E element) {
       //检查下标越界
        rangeCheck(index);
        //获取原来的值
        E oldValue = elementData(index);
        //数组指向新值
        elementData[index] = element;
        //返回旧值
        return oldValue;
    }        
```
这里我们就可以解释，为何ArrayList查值和更新值的效率比较高了，只要在知道下标的情况下，直接就去操作元素了，时间复杂度为N，而空间复杂度为0，这就不难理解ArrayList在查取上面的的优越性了。
6. 增加和删除
```
public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            //调用扩容方法
            grow(minCapacity);
    }    
```
增加容量调用了前面我们说的```grow()```来扩容，使用了```Arrays.copyOf(elementData, newCapacity)```方法，相当于把前面数组的东西全部复制一遍到新数组，之后新数组再把新内容加到里边去，这导致了巨额的开销。从源码的角度就可以看到，使用ArrayList集合，一定要初始化容量！！！，尽可能的避免扩容。这就好像搬家，累坏人。
```
 public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            //index后的元素整体向前移动一位
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
```
删除也是同理，把中间的元素去掉后，使用```System.arraycopy(elementData, index+1, elementData, index,numMoved)```让后面的元素整体向前移动一位构成一个新数组，这也导致了很多不必要的开销。如果大量的数据挪来挪去，JVM，CPU表示压力很大。
- LinkedList
  LinkedList是一种双向链表实现的序列，有头尾节点，每个节点保存自身值的同时还存有对前后节点的引用，同时还有索引的应用，结合了队列和列表两种特性，了解了内部数据结构，我们来看LinkedList的源码。
1. 实现接口和父类
```
//实现序列接口，队列接口，支持克隆，支持序列化，继承抽象AbstractSequentialList类
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable{

    }
```
2. 多线程实现方式
```
//通过Collections辅助类来实现多线程
List list = Collections.synchronizedList(new LinkedList(...));
```
3. 构造器
```
    //节点数
    transient int size = 0;
    //头节点
    transient Node<E> first;
    //尾节点
    transient Node<E> last;
   //空构造
   public LinkedList() {
       }

   //添加集合构造
    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }
```
4. 节点的定义
```
 //私有化的方法，定义了前后节点的引用，自身元素
 private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```
5. 添加和删除
以在头节点添加为例
```
 //在头节点添加
 public void addFirst(E e) {
        linkFirst(e);
    }
 //替换头节点添加的方法   
 private void linkFirst(E e) {
        //得到原来的头节点，相当于中间变量
        final Node<E> f = first;
        //创建一个新节点
        final Node<E> newNode = new Node<>(null, e, f);
        //新节点替代头节点
        first = newNode;
        //判空，没有头节点，新节点就是头节点
        if (f == null)
            last = newNode;
        else
            //否则，中间变量的前引用指向头节点，后引用不变
            f.prev = newNode;
        size++;
        modCount++;
    }   
```
可以看到，LinkedList添加节点非常的方便，只需要把引用重新梳理一下就可以添加元素了，这是ArrayList比不上的。
以头节点删除为例
```
//删除尾节点
public E removeLast() {
        final Node<E> l = last;
        //判空
        if (l == null)
            throw new NoSuchElementException();
        return unlinkLast(l);
    }
//剔除尾节点
private E unlinkLast(Node<E> l) {
        // assert l == last && l != null;
        //得到节点内容
        final E element = l.item;
        //得到前一个节点
        final Node<E> prev = l.prev;
        //内容变空
        l.item = null;
        //前引用变空，等待垃圾回收
        l.prev = null; // help GC
        //前一个节点变为现在的尾节点
        last = prev;
        //判空，前后节点是否是一个节点
        if (prev == null)
            //这样的话前节点为空
            first = null;
        else
           //否则，前一个节点的下一个节点为空，彻底断开了引用
            prev.next = null;
        size--;
        modCount++;
        return element;
    }    
```
6. 更新和查询
```
//按照索引去得到值
public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
//按照二分查找来寻找节点    
Node<E> node(int index) {
        // assert isElementIndex(index);
        // >>是左移符号，相当于二进制左移一位，也就是除以二，C/C++写法
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
//按照索引去更新值，同样调用了node方法    
public E set(int index, E element) {
        checkElementIndex(index);
        Node<E> x = node(index);
        E oldVal = x.item;
        x.item = element;
        return oldVal;
    }        
```
更新查找，会去遍历链表一半的节点，这样不必要的开销，造成了LinkedList在查找节点上的低效。所以可以看到LinkedList的特性，添加删除非常的方便，更新和查找非常的繁琐。
- Vector
Vector要说的不多，和ArrayList和类似，是一个比较老的实现类，现在Java官方已经不推荐使用了。我们来了解一些基本的东西。
1. 实现类和实现接口
```
//支持随机访问，克隆，序列化，实现了AbstractList接口
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable{

    }
```
2. 构造器
```
    //内部以数组来存储数据
    protected Object[] elementData;
    //下标个数
    protected int elementCount;
    //默认增长系数
    protected int capacityIncrement;
    //以初始化容量和增长容量初始化构造器
    public Vector(int initialCapacity, int capacityIncrement) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity];
        this.capacityIncrement = capacityIncrement;
    }
    //以初始化容量初始化构造器
    public Vector(int initialCapacity) {
        this(initialCapacity, 0);
    }

    //默认个数为10
    public Vector() {
        this(10);
    }

    //以初始化集合为构造器
    public Vector(Collection<? extends E> c) {
        elementData = c.toArray();
        elementCount = elementData.length;
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, elementCount, Object[].class);
    }
```
3. 扩容方式
```
 private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        //新的容量是老容量加上默认增长系数或者直接是原来的老容量，这会导致很大的缺陷，无法精准的计算数组开销。
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
Vector是很老的容器集合，实际的开发已经不使用了，即使有线程安全这个特性，和ArrayList比较也没多大优势。
- Stack
Stack继承于Vector，在Vectror的基础上增加了几个方法，使得数据可以先进后出
```
public
class Stack<E> extends Vector<E> {
    //空的构造器
    public Stack() {
    }
    //压栈，先进的数据在栈底
    public E push(E item) {
        addElement(item);

        return item;
    }
    //出栈，删除栈顶数据
    public synchronized E pop() {
        E       obj;
        int     len = size();

        obj = peek();
        removeElementAt(len - 1);

        return obj;
    }

    //出栈，不删除栈顶数据
    public synchronized E peek() {
        int     len = size();

        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }

    //为空
    public boolean empty() {
        return size() == 0;
    }

    //搜索数据
    public synchronized int search(Object o) {
        int i = lastIndexOf(o);

        if (i >= 0) {
            return size() - i;
        }
        return -1;
    }
    //序列化号码
    private static final long serialVersionUID = 1224463164541339165L;
}

```
Stack拥有先进后出的特性，现在Java官方提倡大家使用ArrayQueue(双端队列)来代替这个类的使用。我们在讲解Queque的时候会提到。
- LinkedList，ArrayList，Vector，Stack效率分析和总结
根据AlienStar同学的代码，分别对四个集合按照顺序来做效率分析
```
public class testList {
	// 新建不同的类型
	private static List<String> arrayList = new ArrayList<String>();
	private static Vector<String> vector = new Vector<String>();
	private static Stack<String> stack = new Stack<String>();
	private static LinkedList<String> linkedList = new LinkedList<String>();

	private static long startTime() {
		return System.currentTimeMillis();
	}

	private static long endTime() {
		return System.currentTimeMillis();
	}

	// 测试插入
	private static void testInsert() {
		testInsert(arrayList);
		testInsert(vector);
		testInsert(stack);
		testInsert(linkedList);
	}

	// 测试查询
	private static void testRandomAcess() {
		testRandomAcess(arrayList);
		testRandomAcess(vector);
		testRandomAcess(stack);
		testRandomAcess(linkedList);
	}

	// 测试迭代
	private static void testIterator() {
		testIterator(arrayList);
		testIterator(vector);
		testIterator(stack);
		testIterator(linkedList);
	}

	// 测试删除
	private static void testDelete() {
		testDelete(arrayList);
		testDelete(vector);
		testDelete(stack);
		testDelete(linkedList);
	}

	private static void testDelete(List<String> list) {
		long start = startTime();
		for (int i = 0; i < list.size(); i++) {
			list.remove(i);
		}
		long end = endTime();
		System.out.println("删除:"+(end - start)+"ms");
	}

	private static void testIterator(List<String> list) {
		long start = startTime();
		Iterator<String> iterator = list.iterator();
		if (iterator.hasNext()) {
			iterator.next();
		}
		long end = endTime();
		System.out.println("迭代:"+(end - start)+"ms");
	}

	private static void testRandomAcess(List<String> list) {
		long start = startTime();
		for (int i = 0; i < list.size(); i++) {
			list.get(i);
		}
		long end = endTime();
		System.out.println("测试随机访问:"+(end - start)+"ms");
	}

	private static void testInsert(List<String> list) {
		long start = startTime();
		for (int i = 0; i < 100000; i++) {
			list.add(i,"ssdfssdfsf");
		}
		long end = endTime();
		System.out.println("测试插入:"+(end - start)+"ms");
	}

	public static void main(String[] args) {
		testInsert();
		testIterator();
		testRandomAcess();
		testDelete();
	}

}
```
![1](../../图片/12月/集合/1.png)
- 按照10万条数据来测试，本人电脑8G内存，jdk1.8，可以看到，各自在增删改查迭代上面的性能。
1. ArrayList和Vector的总结：
- a 内部都是数组实现，两者有很大的相似性。
- b ArrayList线程不安全，Vector线程安全，单线程也好，多线程也好，优先选择ArrayList(多线程使用辅助类Collections)。
2. ArrayList和linkedList的总结
- a 两者都是线程不安全，都可以使用索引来操作数据。
- b ArrayList更擅长查询和修改，LinkedList擅长增加和删除。
## 参考
- Java编程思想
-  AlienStar：[Java集合总览](http://blog.csdn.net/crave_shy/article/details/17416791)
-  中科春哥：[JavaSE实战----API(中)集合框架](http://blog.csdn.net/zhongkelee/article/details/46801449)
-  陈明的博客：[chenssy](http://my.csdn.net/chenssy)
