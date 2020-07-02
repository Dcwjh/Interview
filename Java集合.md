### 1. 简单介绍一下List下的集合框架
![集合框架](./image/jihe.png)

List集合代表一个有序、可重复集合，集合中每个元素都有其对应的顺序索引。List集合默认按照元素的添加顺序设置元素的索引，可以通过索引（类似数组的下标）来访问指定位置的集合元素。

`实现List接口的集合主要有：ArrayList、LinkedList、Vector、Stack。`

1. ArrayList

    ArrayList是一个动态数组，也是我们最常用的集合，是List类的典型实现。它允许任何符合规则的元素插入甚至包括null。每一个ArrayList都有一个初始容量（10），该容量代表了数组的大小。随着容器中的元素不断增加，容器的大小也会随着增加。在每次向容器中增加元素的同时都会进行容量检查，当快溢出时，就会进行扩容操作。所以如果我们明确所插入元素的多少，最好指定一个初始容量值，避免过多的进行扩容操作而浪费时间、效率。


    - 优点：
    ArrayList擅长于随机访问。同时ArrayList是非同步的。
    - 缺点：
    插入和删除元素需要大量移动元素

2. LinkedList

    LinkedList是List接口的另一个实现，除了可以根据索引访问集合元素外，LinkedList还实现了Deque接口，可以当作双端队列来使用，也就是说，既可以当作“栈”使用，又可以当作队列使用。
    - 优点：
    插入删除元素时有较好的性能
    - 缺点：
    随机访问集合中的元素性能较差，每次查询都需要从头开始遍历

3. Vector
    与ArrayList相似，但是Vector是同步的。所以说Vector是线程安全的动态数组。它的操作与ArrayList几乎一样。
    - 对比ArrayList
    1.因为vector是线程安全的，所以效率低，这容易理解，类似StringBuffer
    2.**Vector空间满了之后，扩容是一倍**，而ArrayList仅仅是一半
    3.Vector分配内存的时候需要连续的存储空间，如果数据太多，容易分配内存失败
    4.只能在尾部进行插入和删除操作，效率低

4. Stack
     Stack继承自Vector，实现一个后进先出的堆栈。Stack提供5个额外的方法使得Vector得以被当作堆栈使用。基本的push和pop 方法，还有peek方法得到栈顶的元素，empty方法测试堆栈是否为空，search方法检测一个元素在堆栈中的位置。Stack刚创建后是空栈。

5. Iterator接口和ListIterator接口

    - Iterator是一个接口，它是集合的迭代器。集合可以通过Iterator去遍历集合中的元素。Iterator提供的API接口如下：
　　`boolean hasNext()`：判断集合里是否存在下一个元素。如果有，`hasNext()`方法返回 true。
　　`Object next()`：返回集合里下一个元素。
　　`void remove()`：删除集合里上一次next方法返回的元素。

    
    - ListIterator接口继承Iterator接口，提供了专门操作List的方法。ListIterator接口在Iterator接口的基础上增加了以下几个方法：
    `boolean hasPrevious()`：判断集合里是否存在上一个元素。如果有，该方法返回 true。
　　`Object previous()`：返回集合里上一个元素。
　　`void add(Object o)`：在指定位置插入一个元素。

    以上两个接口相比较，不难发现，ListIterator增加了向前迭代的功能（Iterator只能向后迭代），ListIterator还可以通过add()方法向List集合中添加元素（Iterator只能删除元素）。


# 2. 集合框架(重点是map)


# 3. ConcurrentHashMap的扩容机制




### 参考链接

[JavaGuide面试突击版:Java集合](https://snailclimb.gitee.io/javaguide-interview/#/./docs/b-2Java%E9%9B%86%E5%90%88)
[]()