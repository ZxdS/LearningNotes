# Java集合笔记--Zxd	

1. List

   List是有序的Collection。Java List一共三个实现类： 分别是ArrayList、Vector和LinkedList。 

1. 1.ArrayList （数组）

   ArrayList 是最常用的 List 实现类，内部是通过数组实现的，它允许对元素进行快速随机访问。**数组的缺点是每个元素之间不能有间隔，当数组大小不满足时需要增加存储能力，就要将已有数组的数据复制到新的存储空间中**。当从 ArrayList 的中间位置插入或者删除元素时，需要对数组进 行复制、移动、代价比较高。因此，它**适合随机查找和遍历，不适合插入和删除。** 

1. 2.Vector（数组实现、线程同步） 

   Vector与ArrayList一样，也是通过数组实现的，不同的是**它支持线程的同步，即某一时刻只有一 个线程能够写 Vector**，避免多线程同时写而引起的不一致性，但实现同步需要很高的花费，因此， 访问它比访问ArrayList慢。

1. 3.LinkedList（链表） 

   **LinkedList是用链表结构存储数据的，很适合数据的动态插入和删除，**随机访问和遍历速度比较 慢。另外，他还**提供了List接口中没有定义的方法，专门用于操作表头和表尾元素，可以当作堆 栈、队列和双向队列使用**。 

   

2. Set

   Set注重独一无二的性质,该体系集合用于存储无序(存入和取出的顺序不一定相同)元素，**值不能重 复**。对象的相等性本质是对象hashCode值（java是依据对象的内存地址计算出的此序号）判断 的，**如果想要让两个不同的对象视为相等的，就必须覆盖Object的hashCode方法和equals方 法**。

   2.1. HashSet（Hash 表） 

   哈希表边存放的是哈希值。HashSet存储元素的顺序并不是按照存入时的顺序（和List显然不 同） 而是按照哈希值来存的所以取数据也是按照哈希值取得。**元素的哈希值是通过元素的 hashcode方法来获取的, HashSet首先判断两个元素的哈希值，如果哈希值一样，接着会比较 equals方法 如果 equls结果为true ，HashSet就视为同一个元素。如果equals 为false就不是 同一个元素**。

   哈希值相同equals为false的元素是怎么存储呢,就是在同样的哈希值下顺延（可以认为哈希值相 同的元素放在一个哈希桶中）。也就是哈希一样的存一列。如图1表示hashCode值不相同的情 况；图2表示hashCode值相同，但equals不相同的情况。 

   ![HashSet](E:\gitbook\Java基础篇\images\HashSet.png)

   HashSet通过hashCode值来确定元素在内存中的位置。**一个hashCode位置上可以存放多个元 素。** 

   2.2TreeSet（二叉树） 

   [TreeSet]: https://www.jianshu.com/p/12f4dbdbc652

   1. TreeSet()是使用二叉树的原理对新add()的对象按照指定的顺序排序（升序、降序），每增 加一个对象都会进行排序，将对象插入的二叉树指定的位置。
   2. Integer和String对象都可以进行默认的TreeSet排序，而自定义类的对象是不可以的，**自 己定义的类必须实现Comparable接口，并且覆写相应的compareTo()函数，**才可以正常使 用。 
   3. 在覆写compare()函数时，要返回相应的值才能使TreeSet按照一定的规则来排序 
   4. 比较此对象与指定对象的顺序。如果该对象小于、等于或大于指定对象，则分别返回负整 数、零或正整数。 

   2.3LinkHashSet（HashSet+LinkedHashMap）

   对于 LinkedHashSet 而言，它继承与 HashSet、又基于 LinkedHashMap 来实现的。 LinkedHashSet 底层使用 LinkedHashMap 来保存所有元素，它继承与 HashSet，其所有的方法 操作上又与HashSet相同，因此LinkedHashSet 的实现上非常简单，只提供了四个构造方法，并 通过传递一个标识参数，调用父类的构造器，底层构造一个 LinkedHashMap 来实现，在相关操 作上与父类HashSet的操作相同，直接调用父类HashSet的方法即可。 

   

3. Map

   3.1HashMap（数组+链表+红黑树)

   HashMap根据键的hashCode值存储数据，大多数情况下可以直接定位到它的值，因而具有很快 的访问速度，但遍历顺序却是不确定的。 HashMap最多只允许一条记录的键为null，允许多条记 录的值为 null。HashMap 非线程安全，即任一时刻可以有多个线程同时写 HashMap，可能会导 致数据的不一致。如果需要满足线程安全，可以用 Collections 的 synchronizedMap 方法使 HashMap 具有线程安全的能力，或者使用 ConcurrentHashMap。我们用下面这张图来介绍 HashMap 的结构。 

   大方向上，HashMap 里面是一个数组，然后数组中每个元素是一个单向链表。上图中，每个绿色 的实体是嵌套类 Entry 的实例，Entry 包含四个属性：key, value, hash 值和用于单向链表的 next。 

   1. capacity：当前数组容量，**始终保持 2^n**，可以扩容，扩容后数组大小为当前的 2 倍。 
   2. loadFactor：负载因子，默认为 0.75。 
   3. threshold：扩容的阈值，等于 capacity * loadFactor 

   Java8 对 HashMap 进行了一些修改，最大的不同就是利用了红黑树，所以其由 数组+链表+红黑 树 组成。 
   根据 Java7 HashMap 的介绍，我们知道，查找的时候，根据 hash 值我们能够快速定位到数组的 具体下标，但是之后的话，需要顺着链表一个个比较下去才能找到我们需要的，时间复杂度取决 于链表的长度，为 O(n)。为了降低这部分的开销，在 Java8 中，当链表中的元素超过了 8 个以后， 会将链表转换为红黑树，在这些位置进行查找的时候可以降低时间复杂度为 O(logN)。 

   3.2 ConcurrentHashMap 

   1.Segment 段 :ConcurrentHashMap 和 HashMap 思路是差不多的，但是因为它支持并发操作，所以要复杂一 些。整个 ConcurrentHashMap 由一个个 Segment 组成，Segment 代表”部分“或”一段“的 意思，所以很多地方都会将其描述为分段锁。注意，行文中，我很多地方用了“槽”来代表一个 segment。 

   2.线程安全（Segment 继承 ReentrantLock 加锁） :简单理解就是，ConcurrentHashMap 是一个 Segment 数组，Segment 通过继承 ReentrantLock 来进行加锁，所以每次需要加锁的操作锁住的是一个 segment，这样只要保证每 个 Segment 是线程安全的，也就实现了全局的线程安全。 

   ![Java7ConcurrentHashMap结构](E:\gitbook\Java基础篇\images\Java7ConcurrentHashMap结构.png)

   3.并行度（默认 16） concurrencyLevel：并行级别、并发数、Segment 数，怎么翻译不重要，理解它。默认是 16， 也就是说 **ConcurrentHashMap 有 16 个 Segments**，所以理论上，这个时候，最多可以同时支 持 16 个线程并发写，只要它们的操作分别分布在不同的 Segment 上。这个值可以在初始化的时 候设置为其他值，但是一旦初始化以后，它是不可以扩容的。再具体到每个 Segment 内部，其实 每个 Segment 很像之前介绍的 HashMap，不过它要保证线程安全，所以处理起来要麻烦些。 

   4.Java8 实现 （引入了红黑树） :Java8 对 ConcurrentHashMap 进行了比较大的改动,Java8 也引入了红黑树。 

   ![Java8ConcurrentHashMap结构](E:\gitbook\Java基础篇\images\Java8ConcurrentHashMap结构.png)

   3.3 HashTable（线程安全） 

   Hashtable 是遗留类，很多映射的常用功能与 HashMap 类似，不同的是它承自 Dictionary 类， 并且是线程安全的，任一时间只有一个线程能写 Hashtable，并发性不如 ConcurrentHashMap， 因为 ConcurrentHashMap 引入了分段锁。Hashtable 不建议在新代码中使用，不需要线程安全 的场合可以用HashMap替换，需要线程安全的场合可以用ConcurrentHashMap替换。 

   3.4 TreeMap（可排序） 

   TreeMap 实现 SortedMap 接口，能够把它保存的记录根据键排序，默认是按键值的升序排序， 也可以指定排序的比较器，当用Iterator遍历TreeMap时，得到的记录是排过序的。 
   如果使用排序的映射，建议使用TreeMap。 
   在使用 TreeMap 时，key 必须实现 Comparable 接口或者在构造 TreeMap 传入自定义的 Comparator，否则会在运行时抛出java.lang.ClassCastException类型的异常。 
   参考：https://www.ibm.com/developerworks/cn/java/j-lo-tree/index.html 

   3.5 LinkHashMap（记录插入顺序） 

   LinkedHashMap 是 HashMap 的一个子类，保存了记录的插入顺序，在用 Iterator 遍历 LinkedHashMap时，先得到的记录肯定是先插入的，也可以在构造时带参数，按照访问次序排序。 参考1：http://www.importnew.com/28263.html 
   参考2：http://www.importnew.com/20386.html#comment-648123 