## 引言
前面一篇我们说了Collection接口和List部分，这一篇我们来分析Queue和Map。由于Set是基于Map来实现的，我们简略分析即可。
## Queue
Queue也就是队列，是一种先进先出的数据结构(FIFO)，从尾部添加，从头部取出，只能单向操作。大家常用的LinkedList就实现了它的接口，这是我们熟悉的。
- Queue接口
```
public interface Queue<E> extends Collection<E> {
    //增加
    boolean add(E e);
    //添加一个元素并返回true，如果队列已满，这返回false
    boolean offer(E e);
    //删除队列头部的元素，如果为空，抛出NoSuchElementException异常
    E remove();
    //返回并删除队列头部的元素，如果队列为空，则返回Null
    E poll();
    //返回队列头部的元素，如果队列为空，则抛出NoSuchElementException异常
    E element();
    //返回队列头部的元素，如果队列为空，返回Null    
    E peek();
}
```
分析Queue接口，会发现这是一种非常简单的数据形式，整个接口只有短短六个定义方法。可以说很好理解。
- AbstractQueue抽象类
```
//继承抽象AbstractCollection类，实现Queue接口
public abstract class AbstractQueue<E>
    extends AbstractCollection<E>
    implements Queue<E> {

    //空构造方法
    protected AbstractQueue() {
    }

    //增加元素
    public boolean add(E e) {
        //调用了子方法offer()
        if (offer(e))
            return true;
        else
            throw new IllegalStateException("Queue full");
    }
    //删除队列头部的元素，如果为空，抛出NoSuchElementException异常
    public E remove() {
        E x = poll();
        if (x != null)
            return x;
        else
            throw new NoSuchElementException();
    }

    //返回头部元素
    public E element() {
        E x = peek();
        if (x != null)
            return x;
        else
            throw new NoSuchElementException();
    }

    //清除队列
    public void clear() {
        while (poll() != null)
            ;
    }

    //将集合全部元素添加至队列
    public boolean addAll(Collection<? extends E> c) {
        //判空
        if (c == null)
            throw new NullPointerException();
        //判断元素是否一样    
        if (c == this)
            throw new IllegalArgumentException();
        boolean modified = false;
        //循环添加
        for (E e : c)
            if (add(e))
                modified = true;
        return modified;
    }

}

```
AbstractQueue实现了Queue接口方法的细节部分，减少了后面继承类的重复实现
- Deque接口
这部分在书里面特别点了一下，我觉得是因为双端队列的特性引起了作者的注意。事实上，双端队列既有队列的特性，也拥有栈的特性，实现类可以在头部操作元素也可以在尾部操作元素，LinkedList和ArrayDeque都有这种特性。
```
public interface Deque<E> extends Queue<E> {
    
    //添加到头部
    void addFirst(E e);

    //添加尾部
    void addLast(E e);

    //添加元素到头部，返回true或者false
    boolean offerFirst(E e);

    //添加元素到尾部，返回true或者false
    boolean offerLast(E e);

    //删除头部元素
    E removeFirst();

    //删除尾部元素
    E removeLast();

    //删除头部元素，为空返回null
    E pollFirst();

    //删除尾部元素，为空返回null
    E pollLast();

    //得到头部元素
    E getFirst();

    //得到尾部元素
    E getLast();

    //得到头部元素，但不删除
    E peekFirst();

    //得到尾部元素，但不删除
    E peekLast();

    //删除队列中第一次出现的元素，从头到尾
    boolean removeFirstOccurrence(Object o);

    //删除队列中最后一次出现的元素，从头到尾
    boolean removeLastOccurrence(Object o);

    // *** Queue methods ***
    //增加
    boolean add(E e);

    //添加一个元素并返回true，如果队列已满，这返回false
    boolean offer(E e);

    //删除
    E remove();

    //返回并删除队列头部的元素，如果队列为空，则返回Null
    E poll();
    
    //得到头部元素
    E element();

    //返回队列头部的元素，如果队列为空，返回Null
    E peek();


    // *** Stack methods ***

    //压栈
    void push(E e);

    //出栈
    E pop();

    
    // *** Collection methods ***

    //删除
    boolean remove(Object o);

    //是否包含
    boolean contains(Object o);

    //队列的大小
    public int size();

    //迭代器
    Iterator<E> iterator();

    //按相反顺序迭代
    Iterator<E> descendingIterator();

}
```
- ArrayDeque实现类
ArrayDeque是Java官方推荐用来取代Stack类的，它实现自Deque，是一种双端队列，内部由数组来担任实际存储。
1. 实现接口和继承类
```
//实现Deque接口，继承AbstractCollection抽象类，支持克隆，支持序列化
public class ArrayDeque<E> extends AbstractCollection<E>
                           implements Deque<E>, Cloneable, Serializable{

   }
```
2. 多线程支持
```
//这个地方表示疑问很大
Collection<String> strings = Collections.synchronizedCollection(new ArrayDeque<String>());
```
3. 构造器和属性
```
    //存放队列元素的数组
    transient Object[] elements; 

    //头部的位置
    transient int head;

    //尾部的位置
    transient int tail;
    
    //最少的初始化容量 
    private static final int MIN_INITIAL_CAPACITY = 8;
    
    //空的构造器,默认数组大小为16
     public ArrayDeque() {
        elements = new Object[16];
    }

    //带初始化容量的构造器
    public ArrayDeque(int numElements) {
        allocateElements(numElements);
    }

    //带初始化集合的构造器
    public ArrayDeque(Collection<? extends E> c) {
        allocateElements(c.size());
        addAll(c);
    }
```
4. 扩容方式
```
//按两倍容量来扩容
private void doubleCapacity() {
        //assert默认后面的表达式为true
        assert head == tail;
        //中间变量
        int p = head;
        int n = elements.length;
        int r = n - p; // number of elements to the right of p
        //n扩大一倍
        int newCapacity = n << 1;
        if (newCapacity < 0)
            throw new IllegalStateException("Sorry, deque too big");
        Object[] a = new Object[newCapacity];
        System.arraycopy(elements, p, a, 0, r);
        System.arraycopy(elements, 0, a, r, p);
        elements = a;
        head = 0;
        tail = n;
    }
```
ArrayDeque按照两倍的方式来扩容，之后调用```System.arraycopy()```将旧数组的值复制到新数组。
5. 双端队列操作
```
    //在头部添加
    public void addFirst(E e) {
        if (e == null)
            throw new NullPointerException();
        elements[head = (head - 1) & (elements.length - 1)] = e;
        if (head == tail)
            doubleCapacity();
    }

    //在头尾添加
    public void addLast(E e) {
        if (e == null)
            throw new NullPointerException();
        elements[tail] = e;
        if ( (tail = (tail + 1) & (elements.length - 1)) == head)
            doubleCapacity();
    }

    //在头部添加，成功返回true
    public boolean offerFirst(E e) {
        addFirst(e);
        return true;
    }

    //在尾部添加，成功返回true
    public boolean offerLast(E e) {
        addLast(e);
        return true;
    }

    //删除头部元素，为空抛出NoSuchElementException
    public E removeFirst() {
        E x = pollFirst();
        if (x == null)
            throw new NoSuchElementException();
        return x;
    }

    ////删除尾部元素，为空抛出NoSuchElementException
    public E removeLast() {
        E x = pollLast();
        if (x == null)
            throw new NoSuchElementException();
        return x;
    }
    
    //添加到头部，为空返回null
    public E pollFirst() {
        int h = head;
        @SuppressWarnings("unchecked")
        E result = (E) elements[h];
        // Element is null if deque empty
        if (result == null)
            return null;
        elements[h] = null;     // Must null out slot
        head = (h + 1) & (elements.length - 1);
        return result;
    }
    
    //添加到尾部，为空返回null
    public E pollLast() {
        int t = (tail - 1) & (elements.length - 1);
        @SuppressWarnings("unchecked")
        E result = (E) elements[t];
        if (result == null)
            return null;
        elements[t] = null;
        tail = t;
        return result;
    }

    //得到头部的元素，为空抛出NoSuchElementException
    public E getFirst() {
        @SuppressWarnings("unchecked")
        E result = (E) elements[head];
        if (result == null)
            throw new NoSuchElementException();
        return result;
    }

    //得到尾部的元素，为空抛出NoSuchElementException
    public E getLast() {
        @SuppressWarnings("unchecked")
        E result = (E) elements[(tail - 1) & (elements.length - 1)];
        if (result == null)
            throw new NoSuchElementException();
        return result;
    }
    
    //得到头部的元素
    @SuppressWarnings("unchecked")
    public E peekFirst() {
        // elements[head] is null if deque empty
        return (E) elements[head];
    }

    //得到尾部的元素
    @SuppressWarnings("unchecked")
    public E peekLast() {
        return (E) elements[(tail - 1) & (elements.length - 1)];
    }

    //删除第一次匹配到的元素，从头部到尾部
    public boolean removeFirstOccurrence(Object o) {
        if (o == null)
            return false;
        int mask = elements.length - 1;
        int i = head;
        Object x;
        while ( (x = elements[i]) != null) {
            if (o.equals(x)) {
                delete(i);
                return true;
            }
            i = (i + 1) & mask;
        }
        return false;
    }

    //删除第一次匹配到的元素，从尾部到头部
    public boolean removeLastOccurrence(Object o) {
        if (o == null)
            return false;
        int mask = elements.length - 1;
        int i = (tail - 1) & mask;
        Object x;
        while ( (x = elements[i]) != null) {
            if (o.equals(x)) {
                delete(i);
                return true;
            }
            i = (i - 1) & mask;
        }
        return false;
    }

```
可以看到，双端队列可以在头尾处做各种增删改查的工作，即可以做队列使用，也可以做栈使用。
- LinkedList，ArrayDeque和Stack比较选择
当需要较多使用增删的队列的时候，优先使用LinkedList，查询和更新比较多的时候，优先ArrayDeque，Stack用到的地方优先用ArrayDeque来替代。
