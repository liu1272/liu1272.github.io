title: Java容器
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-12-20 20:04:00
---
## 泛型
#### 泛型概念
  泛型的本质就是“数据类型的参数化”处理的数据类型不是固定的，而是可以作为参数传入，在调用泛型时必须传入实际类型。
1. 把类型当作是参数一样传递
2. <数据类型>只能是引用类型
3. 泛型只在编译阶段有效，编译后生成的字节码 class 文件不包含泛型中的类型信息（类型参数在编译后会被替换成 object，运行时虚拟机并不知道泛型。）

#### 泛型定义
| 泛型 | 对应单词 | 说明 |
|--------|--------|--------|
| E | Element | 在容器中使用，表示容器中的元素 |
| T | Type | 表示普通的JAVA类 |
| K | Key | 表示键，例如: Map 中的键 Key |
| V | Value | 表示值 |
| N | Number | 表示数值类型 |
| ? |        | 表示不确定的JAVA类型 |

#### 非静态方法泛型
```Java

// 定义一个泛型方法
public class GenericMethods {
    public <T> void f(T x) {
        ......
    }
}


// 定义一个泛型类
// 这里T可以随便写为任意标识，但是实例化泛型类时必须指定T的具体类型
public interface Dao<T> {
    void add(T t);
    T get(int index);
}


// 定义一个泛型接口
// 未传入泛型实参时，T与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中
// 传入泛型实参时，所有使用泛型的地方都要替换成传入的实参类型
public interface Generator<T> {
    public T next();
}
```
- 泛型的类型参数只能是类类型，不能是简单类型。
- 不能对确切的泛型类型使用instanceof操作。如下面的操作是非法的，编译时会出错。
`if(ex_num instanceof Generic<Number>){ } `

#### 静态方法泛型
```Java

// 定义一个静态方法的泛型类
public class StaticGenerator<T> {
     * 如果在类中定义使用泛型的静态方法，需要添加额外的泛型声明（将这个方法定义成泛型方法）
    public static <T> void show(T t){
		......
    }
}
```

#### 泛型数组
  java中是”不能创建一个确切的泛型类型的数组”的。
```Java

不可以：
List<String>[] ls = new ArrayList<String>[10];  

可以：
List<?>[] ls = new ArrayList<?>[10]; 
List<String>[] ls = new ArrayList[10];
对于通配符的方式，最后取出数据是要做显式的类型转换的。
```

#### 无界通配符
  当具体类型不确定的时候，这个通配符就是` ? ` ；当操作类型时，不需要使用类型的具体功能时，只使用Object类中的功能。那么可以用` ? `通配符来表未知类型。
  **上限限定**表示通配符的类型是T类以及T类的子类或者T接口以及T接口的子接口。
```Java

public void showKeyValue1(Generic<? extends Number> obj){
	// 表示只能使用Number及其子类
    ......
}
```
  **下限限定**表示通配符的类型是 T 类以及 T类的父类或者 T 接口以及 T接口的父接口
注意:该方法不适用泛型类
```Java

public void showKeyValue1(Generic<? super Number> obj){
	// 表示只能使用Number及其父类
    ......
}
```

## 容器
### 容器的概念
  容器是存储存储对象和数据的载体，数组是其中之一，使用Java中写好的容器API我们可以很方便的存储、操作我们的数据。

### 容器的分类
<a href="https://smms.app/image/J6sWdwRzbqQElkn" target="_blank"><img src="https://s2.loli.net/2023/12/20/J6sWdwRzbqQElkn.jpg" ></a>
  Collection：存放独立元素的序列。
  Map：存放key-value型的元素对。（这对于需要利用key查找value的程序十分的重要！）

  *Collection是一个接口，它是Set、List等容器的父接口；Collections是个一个工具类，提供了一系列的静态方法来辅助容器操作，这些方法包括对容器的搜索、排序、线程安全化等等。*

#### 【List】
  **有序**: 元素**存入集合的顺序和取出的顺序一致**，可以根据元素的索引标记访问元素
  **可重复**:  List **允许加入重复的元素**。
  List主要分为ArrayList和LinkedList，前者底层是使用数组实现的List，后者是使用链表实现的List。 
  Vector是一个已经被弃用的类，因为他是线程同步的，会导致访问速度变慢。
  Stack是满足“后进先出”规则的容器，然而LinkedList可以实现所有的栈功能。

#### ArrayList
  ArrayList是一个**可以动态增长**的数组。
  ArrayList默认的长度是10，如果我们插入的数据超过了10，ArrayList会不断的自我增长。
  ArrayList扩容的本质就是计算出新的扩容数组的size后实例化，并将原有数组内容复制到新数组中去。默认情况下，新的容量会是原容量的1.5倍。
  ArrayList由于底层是**使用数组实现**的，所以随机访问速度快，插入删除较慢，而且线程不安全。

| 方法 | 解释 |
|--------|--------|
| add(1, "C#") | 添加单个元素 |
| get() | 指定位置的元素 |
| size() | 列表的大小 |
| List.copy() | 生成列表的副本 |
| toArray() | 把LinkedList转化为Array |
| addAll(2, colours2) | 添加多个元素 |
| set(3, "watch") | 修改第n个元素 |
| remove() | 删除索引为 0 的第一个元素 |
| 重载的remove(pen) | 删除“ pen”项的第一次出现 |
| clear() | 从列表中删除所有元素 |
| isEmpty() | 确定列表是否为空 |
| contains() | 是否包含该元素 |
| indexOf() | 返回首次出现的索引 |
| sort() | 进行排序，可自定义 |

#### LinkedList
  LinkedList是一种线性表（链表），但是并**不会按线性的顺序存储数据**，而是在每一个节点里存到下一个节点的地址。
  链表可分为单向链表和双向链表。
  在列表中插入和删除速度快，但是查找需要遍历整个链表速度较慢。
  使用LinkedList可以实现很多队列、栈的数据结构。

| 方法 | 解释 |
|--------|--------|
| add(E e) | 链表末尾添加元素 |
| add(int index, E element) | 向指定位置插入元素。 |
| addAll(Collection c) | 将一个集合的所有元素添加到链表后面 |
| addAll(int index, Collection c) | 将一个集合的所有元素添加到链表的指定位置后面 |
| addFirst(E e) | 元素添加到头部。 |
| addLast(E e) | 元素添加到尾部。 |
| offer(E e) | 向链表末尾添加元素 |
| offerFirst(E e) | 头部插入元素 |
| offerLast(E e) | 尾部插入元素 |
| clear() | 清空链表。 |
| removeFirst() | 删除并返回第一个元素。 |
| removeLast() | 删除并返回最后一个元素。 |
| remove(Object o) | 删除某一元素 |
| remove(int index) | 删除指定位置的元素。 |
| poll() | 删除并返回第一个元素。 |
| remove() | 删除并返回第一个元素。 |
| contains(Object o) | 判断是否含有某一元素。 |
| get(int index) | 返回指定位置的元素。 |
| getFirst() | 返回第一个元素。 |
| getLast() | 返回最后一个元素。 |
| indexOf(Object o) | 查找指定元素从前往后第一次出现的索引。 |
| lastIndexOf(Object o) | 查找指定元素最后一次出现的索引。 |
| peek() | 返回第一个元素。 |
| element() | 返回第一个元素。 |
| peekFirst() | 返回头部元素。 |
| peekLast() | 返回尾部元素。 |
| set(int index, E element) | 设置指定位置的元素。 |
| clone() | 克隆该列表。 |
| descendingIterator() | 返回倒序迭代器。 |
| size() | 返回链表元素个数。 |
| listIterator(int index) | 返回从指定位置开始到末尾的迭代器。 |
| toArray(T[] a) | 返回一个由链表元素转换类型而成的数组。 |
```

getFirst和element都返回列表的头，但是不删除它，如果列表为空，抛出异常
peek实现的功能一样，但是列表为空时返回null
removeFirst和remove都是删除并返回列表的头，如果列表为空抛出异常
pool实现的功能一样，但是列表为空时返回null
```

#### Queue
  队列Queue是一个满足“先进先出”的数据结构。
  队列是一种特殊的线性表，它**只允许在表的前端进行删除操作，而在表的后端进行插入操作**。
  LinkedList类实现了Queue接口，因此我们可以把LinkedList当成Queue来用。
  LinkedList提供了方法支持队列操作，并且实现了Queue接口，所以LinkedList是队列的一种实现，可以通过LinkedList向上转型为Queue
```

offer:将一个元素插入对尾
peek:不移除的情况下将元素插入队尾，队列为空返回null
element:不移除的情况下将元素插入队尾，队列为空报错
poll:移除并返回队头，队列为空返回null
remove:不移除的情况下将元素插入队尾，队列为空报错

offer，add 区别：
一些队列会因为有大小限制而在插入时被拒绝。
这时它不是对调用 add() 方法抛出一个 unchecked 异常，而只是得到由 offer() 返回的 false。

poll，remove 区别：
remove() 和 poll() 方法都是从队列中删除第一个元素。
poll() 方法在用空集合调用时不是抛出异常，只是返回 null。

peek，element区别：
element() 和 peek() 用于在队列的头部查询元素。
element() 抛出一个异常，而 peek() 返回 null。
```

#### PriorityQueue
  PriorityQueue类提供堆数据结构的功能。
  优先队列元素是**按排序顺序检索**的。
  假设我们想以升序检索元素。在这种情况下，优先队列的头是最小的元素。检索到该元素后，下一个最小的元素将成为队列的头。
  优先队列的元素可能没有排序。但是元素总是按排序顺序检索的。
<a href="https://smms.app/image/4oRSTwb98gnPM5y" target="_blank"><img src="https://s2.loli.net/2023/12/21/4oRSTwb98gnPM5y.jpg" width="300"></a>
PriorityQueue方法与Queue基本相同。

#### Vector
  底层是**用数组实现**的，相关的方法都加了同步检查，因此**“线程安全,效率低“**比如indexof方法就增加了 synchronized 同步标记。
  Vector 类实现了一个**动态数组**。和 ArrayList 很相似，但是两者是不同的：
  Vector 包含了许多传统的方法，这些方法不属于集合框架，而且它是同步访问的。
  Vector 主要用在事先不知道数组的大小，或者只是需要一个可以改变大小的数组的情况。 

#### Stack
  栈是Vector的一个子类，它实现了一个标准的后进先出的栈。
  堆栈只定义了默认构造函数，用来创建一个空栈。堆栈除了包括由Vector定义的所有方法，也定义了自己的一些方法。

| 外加方法 | 解释 |
|------|------|
| empty() | 测试堆栈是否为空。 |
| peek() | 查看堆栈顶部的对象，但不从堆栈中移除它。 |
| pop() | 移除堆栈顶部的对象，并作为此函数的值返回该对象。 |
| push(Object element) | 把项压入堆栈顶部。 |
| search(Object element) | 返回对象在堆栈中的位置，以 1 为基数。 |

#### 【Set】
  Collections框架的Set接口提供Java中数学集合的功能。它继承了Collection接口。
  与List接口不同，Set集合**不能包含重复的元素**，而且由于Set是接口，因此无法从中创建对象。
- HashSet
- LinkedHashSet
- EnumSet
- TreeSet
以上类在Collections框架中定义并实现Set接口。

| 方法 | 解释 |
|------|------|
| add() | 将指定的元素添加到集合中 |
| addAll() | 将指定集合的所有元素添加到集合中 |
| iterator() | 返回一个迭代器，该迭代器可用于顺序访问集合中的元素 |
| remove() | 从集合中移除指定的元素 |
| removeAll() | 从存在于另一个指定集合中的集合中删除所有元素 |
| keepAll() | 保留集合中所有还存在于另一个指定集合中的所有元素 |
| clear() | 从集合中删除所有元素 |
| size() | 返回集合的长度（元素数） |
| toArray() | 返回包含集合中所有元素的数组 |
| contains() | 如果集合包含指定的元素，则返回true |
| containsAll() | 如果集合包含指定集合的所有元素，则返回true |
| hashCode() | 返回哈希码值（集合中元素的地址） |
| Union | 为了得到两个集合x和y的并集，我们可以使用x.addAll(y) |
| Intersection | 要获得两个集合x和y的交集，我们可以使用x.retainAll(y) |
| Subset | 要检查x是否是y的子集，我们可以使用y.containsAll(x) |

####  HashSet
  HashSet 基于 HashMap 来实现的，是一个**无序**（底层使用的是数组与链表实现，对元素的哈希值进行运算决定元素在数组中的位置）、**线程不安全**、**不允许有重复元素**（元素哈希计算后使用equals判断是否相同）的集合。
  HashSet 允许有 null 值。
  HashSet 是无序的，即不会记录插入的顺序。
  HashSet 不是线程安全的， 如果多个线程尝试同时修改 HashSet，则最终结果是不确定的。
  方法与Set接口提供的方法相同。

#### TreeSet
  TreeSet 是一个**可以对元素进行排序**的容器。
  **底层实际是用 TreeMap 实现的**，内部维持了一个简化版的 TreeMap，通过 key 来存储 Set 的元素。 
对元素排序规则的实现方式:
1. 通过元素自身实现比较规则。
2. 通过比较器指定比较规则。

| 方法 | 解释 |
|------|------|
| clone() | 创建TreeSet的副本 |
| contains() | 在TreeSet中搜索指定的元素并返回布尔结果 |
| isEmpty() | 检查是否TreeSet为空 |
| size() | 返回TreeSet的大小 |
| clear() | 从TreeSet中删除所有元素 |

#### 【Map】
  Map（也称为字典、关联数组）是用于保存具有映射关系的数据，保存两组值，key和value，这两组值可以是任何应用类型的数据。
  Map的key不允许重复**（底层Map的keySet()返回的是key的Set集合，所以key不会重复）**而value值是可以重复的**（Map的底层values()方法返回类型是Collection，可以存储重复元素）**。


| 方法 | 解释 |
|--------|--------|
| getKey() | 获取Entry中的key值 |
| getValue() | 获取Entry中的value值 |
| setValue(V value) | 设置Entry中的value值，并返回新设置的value值 |
| size() | 返回Map的key-value对的长度。 |
| isEmpty() | 判断该Map是否为空。 |
| containsKey(Object key) | 判断该Map中是否包含指定的key。 |
| containsValue(Object value) | 判断该Map是否包含一个或多个value。 |
| get(Object key) | 获取某个key所对应的value；若不包含该key，则返回null。 |
| put(K key, V value) | 向Map添加key-value对，当Map中有一个与该key相等的key-value对，则新的会去覆盖旧的。 |
| remove(Object key) | 移除指定的key所对应的key-value对，若成功删除，则返回移除的value值。 |
| putAll(Map<? extends K, ? extends V> m) | 将指定的Map中的key-value对全部复制到该Map中。 |
| clear() | 清除Map中的所有key-value对。 |
| keySet() | 获取该Map中所有key组成的Set集合。 |
| values() | 获取该Map中所有value组成的Collection。 |
| entrySet() | 返回该Map中Entry类的Set集合。 |
| remove(Object key, Object value) | 删除指定的key-value对，若删除成功，则返回true；否则，返回false。 |

#### HashMap
  HashMap 是一个散列表，它存储的内容是键值对(key-value)映射。
  HashMap 是无序的、不支持线程同步的、访问速度很快的、根据键 HashCode 值存储数据的，数组和链表的结合体。
  当新建一个HashMap的时候，就会初始化一个数组。数组是Entry[]数组，静态内部类。Entry就是数组中的元素，每个 Map.Entry 其实就是一个key-value对，它持有一个指向下一个元素的引用next，这就构成了链表。
  HashMap 的 key 与 value 类型可以相同也可以不同，因为HashMap 中的元素实际上是对象。可以使用null作为key或value。

| 方法 | 解释 |
|--------|--------|
| clear() | 删除hashMap中的所有键/值对。 |
| clone() | 复制一份hashMap。 |
| isEmpty() | 判断hashMap是否为空。 |
| size() | 计算hashMap中键/值对的数量。 |
| put() | 将键/值对添加到hashMap中。 |
| putAll() | 将所有键/值对添加到hashMap中。 |
| putIfAbsent() | 如果hashMap中不存在指定的键，则将指定的键/值对插入到hashMap中。 |
| remove() | 删除hashMap中指定键key的映射关系。 |
| containsKey() | 检查hashMap中是否存在指定的key对应的映射关系。 |
| containsValue() | 检查hashMap中是否存在指定的value对应的映射关系。 |
| replace() | 替换hashMap中指定的key对应的value。 |
| replaceAll() | 将hashMap中的所有映射关系替换成给定的函数所执行的结果。 |
| get() | 获取指定key对应的value。 |
| getOrDefault() | 获取指定key对应的value，如果找不到key，则返回设置的默认值。 |
| forEach() | 对hashMap中的每个映射执行指定的操作。 |
| entrySet() | 返回hashMap中所有映射项的集合视图。 |
| keySet() | 返回hashMap中所有key组成的集合视图。 |
| values() | 返回hashMap中存在的所有value值。 |
| merge() | 添加键值对到hashMap中。 |
| compute() | 对hashMap中指定key的值进行重新计算。 |
| computeIfAbsent() | 对hashMap中指定key的值进行重新计算，如果不存在这个key，则添加到hashMap中。 |
| computeIfPresent() | 对hashMap中指定key的值进行重新计算，前提是该key存在于hashMap中。 |

#### TreeMap
  与TreeSet非常相似。
  能够把它保存的记录根据键(key)排序,默认是按升序排序，也可以指定排序的比较器，当用Iterator 遍历TreeMap时，得到的记录是排过序的。
  TreeMap不允许key的值为null，value允许为null。 
  底层是红黑树数据结构，每个key-value作为红黑树的一个节点。TreeMap存储节点时，根据key对节点进行排序，主要是自然排序和自定义排序。类似于TreeSet。

#### Hashtable
  与HashMap类似，但key和value的值均不允许为null;它支持线程的同步（线程安全），也导致了Hashtale在写入时会比较慢。 

#### LinkedHashMap
  LinkedHashMap是HashMap的子类，使用双向链表维护key-value对的顺序（只是关注key的顺序），迭代顺序和key-value插入Map中的顺序保持一致。
  LinkedHashMap的插入和读取速度都是最优的。

#### 【Iterator】
  Java迭代器（Iterator）是 Java 集合框架中的一种机制，是一种用于遍历集合（如列表、集合和映射等）的接口。它提供了一种统一的方式来访问集合中的元素，而不需要了解底层集合的具体实现细节。
  
| 方法 | 解释 |
|--------|--------|
| next() | 返回迭代器的下一个元素，并将迭代器的指针移到下一个位置。 |
| hasNext() | 用于判断集合中是否还有下一个元素可以访问 |
| remove() | 从集合中删除迭代器最后访问的元素（可选操作）。 |

```Java

// 获取迭代器
Iterator<String> it = sites.iterator();
```

#### ListIterator
ListIterator是Collection框架中的一个接口；是用于扩展Iterator接口的。使用ListIterator，可以向前和向后遍历集合的元素。还可以添加、删除或修改集合中的任何元素。
1. 遍历
使用Iterator，可以遍历所有集合，但只能单向遍历。
使用ListIterator，只能遍历List实现的对象，但可以双向遍历。
2. 添加元素
Iterator无法向集合中添加元素；而ListIteror可以向集合添加元素。
3. 修改元素
Iterator无法修改集合中的元素；而ListIterator可以使用set()修改集合中的元素。
4. 索引
Iterator无法获取集合中元素的索引；而使用ListIterator可以获取集合中元素的索引。

#### 【Collections】
Collections 是一个工具类，它提供了对 Set、List、Map 进行排序、填充、查找元素的辅助方法。该类中所有的方法都为静态方法

| 方法 | 解释 |
|--------|--------|
| reverse(List list) | 反转列表中元素的顺序 |
| shuffle(List list) | 随机打乱列表中元素的顺序 |
| sort(List list) | 将列表中的元素按自然顺序进行升序排序 |
| sort(List list, Comparator c) | 根据自定义的比较器对列表中的元素进行排序 |
| swap(List list, int i, int j) | 交换列表中指定位置i和j的元素 |
| binarySearch(List list, Object key) | 使用二分查找算法在已排序的列表中查找指定的元素 |
| max(Collection coll) | 返回集合中的最大元素，要求集合中的元素类型实现了`Comparable`接口 |
| max(Collection coll, Comparator comp) | 根据自定义的比较器返回集合中的最大元素 |
| min(Collection coll) | 返回集合中的最小元素，要求集合中的元素类型实现了`Comparable`接口 |
| min(Collection coll, Comparator comp) | 根据自定义的比较器返回集合中的最小元素 |
| fill(List list, Object obj) | 用指定的对象填充列表中的所有元素 |
| frequency(Collection c, Object o) | 返回集合中指定对象出现的次数 |
| addAll(Collection<? super T> c, T... elements) | 将元素数组中的所有元素添加到集合中 |
| disjoint(Collection<?> c1, Collection<?> c2) | 判断两个集合是否没有交集，即它们没有共同的元素 |


### 总结
#### ArrayList 和 LinkedList 的区别是什么？
  数据结构实现：ArrayList 是动态数组的数据结构实现，而 LinkedList 是双向链表的数据结构实现。
  随机访问效率：ArrayList 比 LinkedList 在随机访问的时候效率要高，因为 LinkedList 是线性的数据存储方式，所以需要移动指针从前往后依次查找。
  增加和删除效率：在非首尾的增加和删除操作，LinkedList 要比 ArrayList 效率要高，因为 ArrayList 增删操作要影响数组内的其他数据的下标。
  综合来说，在需要频繁读取集合中的元素时，更推荐使用 ArrayList，而在插入和删除操作较多时，更推荐使用 LinkedList。

#### ArrayList 和 Vector 的区别是什么？
  线程安全：Vector 使用了 Synchronized 来实现线程同步，是线程安全的，而 ArrayList 是非线程安全的。
  性能：ArrayList 在性能方面要优于 Vector。
  扩容：ArrayList 和 Vector 都会根据实际的需要动态的调整容量，只不过在 Vector 扩容每次会增加 1 倍，而 ArrayList 只会增加 50%。

#### Array 和 ArrayList 有何区别？
  Array 可以存储基本数据类型和对象，ArrayList 只能存储对象。
  Array 是指定固定大小的，而 ArrayList 大小是自动扩展的。
  Array 内置方法没有 ArrayList 多，比如 addAll、removeAll、iteration 等方法只有 ArrayList 有。

#### 在 Queue 中 poll()和 remove()有什么区别？
  相同点：都是返回第一个元素，并在队列中删除返回的对象。
  不同点：如果没有元素 poll()会返回 null，而 remove()会直接抛出 NoSuchElementException 异常。

#### 哪些集合类是线程安全的？
  Vector、Hashtable、Stack 都是线程安全的，而像 HashMap 则是非线程安全的，不过在 JDK 1.5 之后随着 Java. util. concurrent 并发包的出现，它们也有了自己对应的线程安全类，比如 HashMap 对应的线程安全类就是 ConcurrentHashMap。
  
#### 迭代器 Iterator 是什么？
  Iterator 接口提供遍历任何 Collection 的接口。我们可以从一个 Collection 中使用迭代器方法来获取迭代器实例。迭代器取代了 Java 集合框架中的 Enumeration，迭代器允许调用者在迭代过程中移除元素。

#### Iterator 和 ListIterator 有什么区别？
  Iterator 可以遍历 Set 和 List 集合，而 ListIterator 只能遍历 List。
  Iterator 只能单向遍历，而 ListIterator 可以双向遍历（向前/后遍历）。
  ListIterator 从 Iterator 接口继承，然后添加了一些额外的功能，比如添加一个元素、替换一个元素、获取前面或后面元素的索引位置。

#### 怎么确保一个集合不能被修改？
  可以使用 Collections. unmodifiableCollection(Collection c) 方法来创建一个只读集合，这样改变集合的任何操作都会抛出 Java.lang.UnsupportedOperationException 异常。

#### List Set Map 之间的区别
  List是可重复集合，Set是不可重复集合，这两个接口都实现了Conllection父类接口。
  但是，Map并没有继承Conllection，他是一个独立的接口，Map是一种把键对象和值对象进行映射的集合，他的每一个元素都包含了一对键对象和值对象，Map中存储的数据是没有顺序的，它的key是不能重复的，他的值是可以有重复的。

#### List的实现类
  ArrayList和Vector内部是线性动态数组结构，所以查询效率上会高很多，Vector是线程安全的，相比ArrayList线程是不安全的，所以性能会稍慢一些。
  LinkedList：是双向链表的数据结构存储数据，在做查询时会按照序号索引数据进行前向和后向遍历，查询效率偏低，但插入数据时只需要记录本项的前后项即可，所以插入速度较快。

#### 红黑树的基本原理
  **红黑树定义和性质**：红黑树是一种含有红黑结点并能自平衡的二叉查找树。
  **有以下性质**：
- 性质1：每个节点要么是黑色，要么是红色。
- 性质2：根节点是黑色。
- 性质3：每个叶子节点（NIL）是黑色。
- 性质4：每个红色结点的两个子结点一定都是黑色。
- 性质5：任意一结点到每个叶子结点的路径都包含数量相同的黑结点。
- 性质5.1：如果一个结点存在黑子结点，那么该结点肯定有两个子结点

  红黑树并不是一个完美平衡二叉查找树，根结点P的左子树显然比右子树高，但左子树和右子树的黑结点的层数是相等的，也即任意一个结点到到每个叶子结点的路径都包含数量相同的黑结点(性质5)。所以我们叫红黑树这种平衡为黑色完美平衡。
  只需要记住红黑树总是通过旋转和变色达到自平衡

  **红黑树查找：**
  因为红黑树是一颗二叉平衡树，并且查找不会破坏树的平衡，所以查找跟二叉平衡树的查找无异：
  从根结点开始查找，把根结点设置为当前结点；若当前结点为空，返回null；若当前结点不为空，用当前结点的key跟查找key作比较；若当前结点key等于查找key，那么该key就是查找目标，返回当前结点；若当前结点key大于查找key，把当前结点的左子结点设置为当前结点，重复步骤2；若当前结点key小于查找key，把当前结点的右子结点设置为当前结点，重复步骤2；

  **红黑树插入：**
  插入操作包括两部分工作：一查找插入的位置；二插入后自平衡。查找插入的父结点很简单，跟查找操作区别不大：
  从根结点开始查找；若根结点为空，那么插入结点作为根结点，结束。若根结点不为空，那么把根结点作为当前结点；若当前结点为null，返回当前结点的父结点，结束。若当前结点key等于查找key，那么该key所在结点就是插入结点，更新结点的值，结束。若当前结点key大于查找key，把当前结点的左子结点设置为当前结点，重复步骤4；若当前结点key小于查找key，把当前结点的右子结点设置为当前结点，重复步骤4；

#### HashMap & Hashtable 的区别
  存储：HashMap 允许 key 和 value 为 null，而 Hashtable 不允许。
  线程安全：Hashtable 是线程安全的，而 HashMap 是非线程安全的。
  推荐使用：在 Hashtable 的类注释可以看到，Hashtable 是保留类不建议使用，推荐在单线程环境下使用 HashMap 替代，如果需要多线程使用则用 ConcurrentHashMap 替代。
  HashMap 同一时间允许多个线程同时进行操作，Hashtable 同一时间只允许一个线程进行操作。
  HashMap 无论主键还是值都可以存放null，Hashtable 对null"零容忍"
  
#### 如何决定使用 HashMap 还是 TreeMap？
  TreeMap默认是按照Key值升序排序的；TreeMap的实现是基于红黑树结构，适用于按自然顺序或自定义顺序遍历键（key）。
  HashMap的分布是散列的、均匀的，不支持排序；数据结构主要是桶(数组)，链表或红黑树，适用于在Map中插入、删除和定位元素。
  **如果你需要得到一个有序的结果时就应该使用TreeMap（因为HashMap中元素的排列顺序是不固定的）。除此之外，由于HashMap有更好的性能，所以大多不需要排序的时候我们会使用HashMap，简单来说，需要排序就用TreeMap，不需排序则使用 HashMap。**

#### 遍历List的三种方法
```Java

List<String> list = new ArrayList<String>();

//第一种方式，普通for循环
for(int i = 0; i < list.size(); i++)
{
    System.out.println(list.get(i));
}

//第二种方式，使用迭代器
for(Iterator<String> iter = list.iterator(); iter.hasNext();)
{
    System.out.println(iter.next());
}

//第三种方式，使用增强型的for循环
//for(类型 每个元素的名字: 对象)
for(String str: list)
{
    System.out.println(str);
}
```

#### Collections的排序与乱序
  **shuffle方法:** 随机排列，只能在有排列顺序的List接口中使用
若集合元素个数小于shufle闽值或者集合支持随机访问，那么从后往前遍历集合，交换元素。
否则先将集合转化为数组(提高访问效率) ，再进行遍历交换元素(在数组中进行)，最后设置集合元素。

  **sort方法:** 排序排列，Collections的sort专门给List排序，而Arrays中sort方法专门给数组进行排序
sort方法只能对同种类型集合进行排序，并且只能对集合进行升序排序。

## 后记
  **终于把Java基础部分大致写完了，接下来我得先接着鸽一段时间，去复习期末考了。咕咕咕~**