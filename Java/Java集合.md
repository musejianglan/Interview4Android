[Java集合框架](#Java集合框架)  
[Java集合——ArrayList](#Java集合——ArrayList)  
[Java集合——LinkedList](#Java集合——LinkedList)  
[Java集合——HashMap](#Java集合——HashMap)  
[Java集合——TreeMap](#Java集合——TreeMap)  
[Java集合——LinkedHashMap](#Java集合——LinkedHashMap)  

# Java集合框架

## Java集合类简介
Java集合大致可以分为Set、List、Queue和Map四种体系。

其中Set代表无序、不可重复的集合；List代表有序、重复的集合；而Map则代表具有映射关系的集合。Java 5 又增加了Queue体系集合，代表一种队列集合实现。

Java集合就像一种容器，可以把多个对象（实际上是对象的引用，但习惯上都称对象）“丢进”该容器中。从Java 5 增加了泛型以后，Java集合可以记住容器中对象的数据类型，使得编码更加简洁、健壮。

### 1.Java集合和数组的区别：
1. 数组长度在初始化时指定，意味着只能保存定长的数据。而集合可以保存数量不确定的数据。同时可以保存具有映射关系的数据（即关联数组，键值对 key-value）。

2. 数组元素即可以是基本类型的值，也可以是对象。集合里只能保存对象（实际上只是保存对象的引用变量），基本数据类型的变量要转换成对应的包装类才能放入集合类中。

### 2.Java集合类之间的继承关系:

Java的集合类主要由两个接口派生而出：Collection和Map,Collection和Map是Java集合框架的根接口。

![java_collection](https://github.com/musejianglan/Interview4Android/blob/master/img/java_collection.png)

Map实现类用于保存具有映射关系的数据。Map保存的每项数据都是key-value对，也就是由key和value两个值组成。Map里的key是不可重复的，key用户标识集合里的每项数据。

![java_map](https://github.com/musejianglan/Interview4Android/blob/master/img/java_map.png)  

## Collection接口
Collection接口是Set,Queue,List的父接口。Collection接口中定义了多种方法可供其子类进行实现，以实现数据操作。

![collection_method](https://github.com/musejianglan/Interview4Android/blob/master/img/collection_method.png)  

Collection用法有：添加元素，删除元素，返回Collection集合的个数以及清空集合等。 其中重点介绍iterator()方法，该方法的返回值是Iterator

#### 用Iterator模式实现遍历集合
Collection有一个重要的方法：iterator()，返回一个Iterator（迭代器），用于遍历集合的所有元素。Iterator模式可以把访问逻辑从不同的集合类中抽象出来，从而避免向客户端暴露集合的内部结构。典型的用法如下：

```
	Iterator it = collection.iterator(); // 获得一个迭代器
	while(it.hasNext()) {
	    Object obj = it.next(); // 得到下一个元素
	}
```
不需要维护遍历集合的“指针”，所有的内部状态都由Iterator来维护，而这个Iterator由集合类通过工厂方法生成。

每一种集合类返回的Iterator具体类型可能不同，但它们都实现了Iterator接口，因此，我们不需要关心到底是哪种Iterator，它只需要获得这个Iterator接口即可，这就是接口的好处，面向对象的威力。

要确保遍历过程顺利完成，必须保证遍历过程中不更改集合的内容（Iterator的remove()方法除外），所以，确保遍历可靠的原则是：只在一个线程中使用这个集合，或者在多线程中对遍历代码进行同步。

由Collection接口派生的两个接口是List和Set。

### Set集合
Set集合与Collection集合基本相同，没有提供任何额外的方法。实际上Set就是Collection，只是行为略有不同（Set不允许包含重复元素）。

Set集合不允许包含相同的元素，如果试图把两个相同的元素加入同一个Set集合中，则添加操作失败，add()方法返回false，且新元素不会被加入。

### List集合
List集合代表一个元素有序、可重复的集合，集合中每个元素都有其对应的顺序索引。List集合允许使用重复元素，可以通过索引来访问指定位置的集合元素 。List集合默认按元素的添加顺序设置元素的索引，例如第一个添加的元素索引为0，第二个添加的元素索引为1......

List作为Collection接口的子接口，可以使用Collection接口里的全部方法。而且由于List是有序集合，因此List集合里增加了一些根据索引来操作集合元素的方法。

### Queue集合

Queue用户模拟队列这种数据结构，队列通常是指“先进先出”(FIFO，first-in-first-out)的容器。队列的头部是在队列中存放时间最长的元素，队列的尾部是保存在队列中存放时间最短的元素。新元素插入（offer）到队列的尾部，访问元素（poll）操作会返回队列头部的元素。通常，队列不允许随机访问队列中的元素。

![queue](https://github.com/musejianglan/Interview4Android/blob/master/img/queue.png)

## Map集合

### Map集合与Set集合、List集合的关系

①.与Set集合的关系

如果 把Map里的所有key放在一起看，它们就组成了一个Set集合（所有的key没有顺序，key与key之间不能重复），实际上Map确实包含了一个keySet()方法，用户返回Map里所有key组成的Set集合。

②.与List集合的关系

如果把Map里的所有value放在一起来看，它们又非常类似于一个List：元素与元素之间可以重复，每个元素可以根据索引来查找，只是Map中索引不再使用整数值，而是以另外一个对象作为索引。

## 总结
* 如果涉及到堆栈，队列等操作，应该考虑用List，对于需要快速插入，删除元素，应该使用LinkedList，如果需要快速随机访问元素，应该使用ArrayList。
* 如果程序在单线程环境中，或者访问仅仅在一个线程中进行，考虑非同步的类，其效率较高，如果多个线程可能同时操作一个类，应该使用同步的类。
* 要特别注意对哈希表的操作，作为key的对象要正确复写equals和hashCode方法。
* 尽量返回接口而非实际的类型，如返回List而非ArrayList，这样如果以后需要将ArrayList换成LinkedList时，客户端代码不用改变。这就是针对抽象编程。


# Java集合——ArrayList

## 优劣势
以数组实现。节约空间，但数组有容量限制。超出限制时会增加50%容量，用System.arraycopy()复制到新的数组，因此最好能给出数组大小的预估值。默认第一次插入元素时创建大小为10的数组。

按数组下标访问元素—get(i)/set(i,e) 的性能很高，这是数组的基本优势。

直接在数组末尾加入元素—add(e)的性能也高，但如果按下标插入、删除元素—add(i,e), remove(i), remove(e)，则要用System.arraycopy()来移动部分受影响的元素，性能就变差了，这是基本劣势。

ArrayList是一个相对来说比较简单的数据结构，最重要的一点就是它的自动扩容，可以认为就是我们常说的“动态数组”。

ArrayList具有自动扩容机制。当增加数据的时候，如果ArrayList的大小已经不满足需求时，那么就将数组变为原长度的1.5倍，之后的操作就是把老的数组拷到新的数组里面。例如，默认的数组大小是10，也就是说当我们add10个元素之后，再进行一次add时，就会发生自动扩容，数组长度由10变为了15。




# Java集合——LinkedList

以双向链表实现。链表无容量限制，但双向链表本身使用了更多空间，也需要额外的链表指针操作。

按下标访问元素—get(i)/set(i,e) 要悲剧的遍历链表将指针移动到位(如果i>数组大小的一半，会从末尾移起)。

插入、删除元素时修改前后节点的指针即可，但还是要遍历部分链表的指针才能移动到下标所指的位置，只有在链表两头的操作—add()，addFirst()，removeLast()或用iterator()上的remove()能省掉指针的移动。

LinkedList是一个简单的数据结构，与ArrayList不同的是，他是基于链表实现的。

# Java集合——HashMap

基于Map接口实现、允许null键/值、非同步、不保证有序(比如插入的顺序)、也不保证序不随时间变化。

## 两个重要的参数

在HashMap中有两个很重要的参数，容量(Capacity)和负载因子(Load factor)

> **Initial capacity** :The capacity is the number of buckets in the hash table, The initial capacity is simply the capacity at the time the hash table is created.
> **Load factor** :The load factor is a measure of how full the hash table is allowed to get before its capacity is automatically increased.  

简单的说，Capacity就是bucket的大小，Load factor就是bucket填满程度的最大比例。如果对迭代性能要求很高的话，不要把capacity设置过大，也不要把load factor设置过小。当bucket中的entries的数目大于capacity*load factor时就需要调整bucket的大小为当前的2倍。

## put函数大致的思路为：

1. 对key的hashCode()做hash，然后再计算index;
2. 如果没碰撞直接放到bucket里；
3. 如果碰撞了，以链表的形式存在buckets后；
4. 如果碰撞导致链表过长(大于等于TREEIFY_THRESHOLD)，就把链表转换成红黑树；
5. 如果节点已经存在就替换old value(保证key的唯一性)
6. 如果bucket满了(超过load factor*current capacity)，就要resize。

### 1. 什么时候会使用HashMap？他有什么特点？
是基于Map接口的实现，存储键值对时，它可以接收null的键值，是非同步的，HashMap存储着Entry(hash, key, value, next)对象。

### 2. 你知道HashMap的工作原理吗？
通过hash的方法，通过put和get存储和获取对象。存储对象时，我们将K/V传给put方法时，它调用hashCode计算hash从而得到bucket位置，进一步存储，HashMap会根据当前bucket的占用情况自动调整容量(超过Load Facotr则resize为原来的2倍)。获取对象时，我们将K传给get，它调用hashCode计算hash从而得到bucket位置，并进一步调用equals()方法确定键值对。如果发生碰撞的时候，Hashmap通过链表将产生碰撞冲突的元素组织起来，在Java 8中，如果一个bucket中碰撞冲突的元素超过某个限制(默认是8)，则使用红黑树来替换链表，从而提高速度。

### 3. 你知道get和put的原理吗？equals()和hashCode()的都有什么作用？
通过对key的hashCode()进行hashing，并计算下标( (n-1) & hash)，从而获得buckets的位置。如果产生碰撞，则利用key.equals()方法去链表或树中去查找对应的节点

### 4. 你知道hash的实现吗？为什么要这样实现？
在Java 1.8的实现中，是通过hashCode()的高16位异或低16位实现的：(h = k.hashCode()) ^ (h >>> 16)，主要是从速度、功效、质量来考虑的，这么做可以在bucket的n比较小的时候，也能保证考虑到高低bit都参与到hash的计算中，同时不会有太大的开销。

### 5. 如果HashMap的大小超过了负载因子(load factor)定义的容量，怎么办？
如果超过了负载因子(默认0.75)，则会重新resize一个原来长度两倍的HashMap，并且重新调用hash方法。

# Java集合——TreeMap

HashMap不保证数据有序，LinkedHashMap保证数据可以保持插入顺序，而如果我们希望Map可以保持key的大小顺序的时候，我们就需要利用TreeMap了

# Java集合——LinkedHashMap

LinkedHashMap是Hash表和链表的实现，并且依靠着双向链表保证了迭代顺序是插入的顺序。

---

[感谢LRH1993/android_interview](https://github.com/LRH1993/android_interview)
