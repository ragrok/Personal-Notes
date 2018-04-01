#### 说明
- 这里主要参考两位高手的文章，自己选了重要的部分单独捡了出来，想参考更详细的，请戳下面的两篇文章
- [java_集合体系之List体系总结、应用场景——07](http://blog.csdn.net/crave_shy/article/details/17523865)
- [Java提高篇（三二）—–List总结](http://cmsblogs.com/?p=1201)
#### list接口示意图
![这里写图片描述](http://img.blog.csdn.net/20161023210706361)
#### List接口特性
- List接口为Collection的直接接口，代表的是有序的Collection，即将元素按照某种特定的规则来排列和操作，可以根据元素的整数索引(下标)来访问元素，你可以把它理解为一个有标号的地址，地址中存有元素的值。主要的实现集合有：ArrayList，LinkedList，Vector，Stack。
- 主要内部接口：
```
       //数量大小
    int size();
    //是否为空
    boolean isEmpty();
    //是否包含某元素
    boolean contains(Object o);
    //依赖的迭代器接口
    Iterator<E> iterator();
    //将元素数组化，得到一个新数组
    Object[] toArray()
    //使用了泛型，将泛型元素数组化
    <T> T[] toArray(T[] a);
    //增加元素
    boolean add(E e);
    //删除元素
    boolean remove(Object o);
    //是否包含整个Collection集合
    boolean containsAll(Collection<?> c);
    //添加整个Collection集合
    boolean addAll(Collection<? extends E> c);
    //从某个位置添加整个数组
    boolean addAll(int index, Collection<? extends E> c);
    //删除整个Collection集合
    boolean removeAll(Collection<?> c);
    //保留整个Collection集合
    boolean retainAll(Collection<?> c);
    //全部清除
    void clear();
    //元素类型和数值是否完全一致
    boolean equals(Object o);
    //返回hashcode值
    int hashCode();
    //访问index位置的元素
    E get(int index);
    //设置index位置的元素
    E set(int index, E element);
    //在index处添加元素
    void add(int index, E element);
    //删除index位置的元素
    E remove(int index);
    //返回元素第一次出现的下标，从左向右
    int indexOf(Object o);
    //返回元素第一次出现的下标，从右向左
    int lastIndexOf(Object o);
    //迭代器，依次返回元素
    ListIterator<E> listIterator();
    //迭代器，从index位置开始，依次返回元素
    ListIterator<E> listIterator(int index);
    //迭代器，从fromIndex位置到toIndex位置，依次返回元素
    List<E> subList(int fromIndex, int toIndex);
}
```
#### 主要实现类
1. ArrayList类：继承AbstractList，以动态数组的形式存储，操作数据。
2. LinkedList类：继承AbstactLinkedList，实现Deque,List接口，以双向链表的形式存储，操作元素。
3. Vector类：继承AbstractList，以动态数组的形式存储，操作数据，线程安全。
4. Stack类：继承Vector，以栈的形式存储，操作数据。
#### LinkedList和 ArrayList的比较
- 相同处
a. 都直接或者间接继承了AbstractList，都支持以索引的方式操作元素。
b. 都不必担心容量问题，ArrayList通过动态数组来保存数据，当容量不足时，自动扩容，LinkedList是以双链表的形式来实现的，不存在容量问题。
c. 都时线程不安全的，一般用于单线程环境下，在多线程环境下使用Collections.synchronized()来保证数据的一致性。
- 不同处
a. LinkedList是由链表实现的，ArrayList是由数组实现的。
b. 相对于ArrayList而言，LinkedList实现Deque接口，在保留了索引操作元素的同时，也实现了双链表所具有的功能，这也决定了LinkedList的特性。
c. 多集合元素的操作效率不同，LinkedList善于增删，ArrayList善于查询和修改，这个本质就是数组和链表的区别。
- 效率分析
a. ArrayList的随机访问效率高于LinkedList的源码分析：
1. 随机访问指的是通过索引去查找元素
2. LinkedList获取指定索引处值的源码:
```
// Positional Access Operations

    /**
     * Returns the element at the specified position in this list.
     *
     * @param index index of the element to return
     * @return the element at the specified position in this list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E get(int index) {
       //检查值是否越界
        checkElementIndex(index);
        //返回节点的值
        return node(index).item;
    }

 /**
     * Returns the (non-null) Node at the specified element index.
     */
      //通过二分法来寻找节点的值
    Node<E> node(int index) {
        // assert isElementIndex(index);

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
```
3. ArrayList得到数组值的源码分析
```
/**
     * Returns the element at the specified position in this list.
     *
     * @param  index index of the element to return
     * @return the element at the specified position in this list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E get(int index) {
        //检查数组是否越界
       rangeCheck(index);
       //调用elementData(int index)方法
        return elementData(index);
    }
 // Positional Access Operations
      //返回index处的值
    @SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }
```
- LinkedList的增删效率高于ArrayList的源码分析
1. LinkedList删除源码
```
/**
     * Removes the element at the specified position in this list.  Shifts any
     * subsequent elements to the left (subtracts one from their indices).
     * Returns the element that was removed from the list.
     *
     * @param index the index of the element to be removed
     * @return the element previously at the specified position
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
    public E remove(int index) {
       //检查是否越界
        checkElementIndex(index);
        //调用unlink(Node<E> x)
        return unlink(node(index));
    }
/**
     * Unlinks non-null node x.
     */
     //不再链接当前节点
    E unlink(Node<E> x) {
        // assert x != null;
       //得到节点的值
        final E element = x.item;
       //得到后节点
        final Node<E> next = x.next;
       //得到前节点
        final Node<E> prev = x.prev;
       //如果前节点为空，意味这不再链接的节点就是头节点
        if (prev == null) {
           //后节点变为头节点
            first = next;
        } else {
            //否则，前节点的下一个节点不再指向当前节点，指向后节点
            prev.next = next;
           //当前节点的前节点变为空，也就是断掉当前节点的前引用
            x.prev = null;
        }
         //如果后节点为空，也就是当前节点是尾节点
        if (next == null) {
             //前节点变为尾节点
            last = prev;
        } else {
            //否则，后节点的前节点是前节点
            next.prev = prev;
           //当前节点的前节点引用设空
            x.next = null;
        }
        //值变为空
        x.item = null;
        //大小减一
        size--;
          //修改次数增加
        modCount++;
         //返回被修改的节点
        return element;
    }
```
2. LinkedList增加的源码原理和删除节点的原理相反，但都是对节点的修改，不再贴出源码
3. ArrayList增加的源码分析
```
public void add(int index, E element) {
        //检查数组越界
        rangeCheckForAdd(index);
       //自动扩容
        ensureCapacityInternal(size + 1);  // Increments modCount!!
       //copy旧数组，形成新数组，这个地方是最耗费时间的，也是ArrayList增加元素效率低的关键
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
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
            grow(minCapacity);
    }

    /**
     * The maximum size of array to allocate.
     * Some VMs reserve some header words in an array.
     * Attempts to allocate larger arrays may result in
     * OutOfMemoryError: Requested array size exceeds VM limit
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    //着重分析这里的源码
    private void grow(int minCapacity) {
        // overflow-conscious code
        //得到旧容量的大小
        int oldCapacity = elementData.length;
        //欲扩容的容量是旧容量的1.5倍
        int newCapacity = oldCapacity + (oldCapacity >> 1);
       //欲扩容的容量和需要的实际容量的比较
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //  欲扩容的容量和max容量比较
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
       //扩容，形成新数组
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```
4.  ArrayList删除元素主要是删除索引位置的值，然后修改容量形成一个新数组，和增加源码很相似，这里不再贴出源码。
5. 源码分析总结：
     对比上面代码可以看出来ArrayList每当插入一个元素时、都会调用System.arraycopy()将指定位置后面的所有元素后移一位、重新构造一个数组、这是比较消耗资源的、而LinkedList是直接改变index前后元素的上一个节点和下一个节点的引用、而不需要动其他的东西、所以效率很高。
#### 总结

- 学以致用、最后总结下上述List集合体系的各个类的使用环境：
- 当需要对集合进行大量的查询和修改时、并且是单线程环境下使用ArrayList。
- 当需要对集合进行大量添加、删除时、并且是单线程环境下使用LinkedList。
- 当多线程时、需要对集合进行大量的查询时、可以考虑使用Vector或者Stack、但是不建议、我们可以使用多次提到的Collections类包装。
