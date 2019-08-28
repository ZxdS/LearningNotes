## 8张图带你轻松温习Java知识

鸿哥 [Java技术栈](javascript:void(0);) *2018-02-19*

![img](https://mmbiz.qpic.cn/mmbiz_gif/TNUwKhV0JpRX3S6bD8E19tDEc6C3eXpicAEhFbqv13RpibujIwzuOhicicQUgTvmDg5j1lUGjCG0oMtM48MUBeAeZg/640?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

**大年初四好，一图胜千言，下面图解均来自Program Creek 网站，目前它们拥有最多的票选。**

**如果图解没有阐明问题，那么你可以借助它的标题来一窥究竟。** 



## **1.**字符串不变性

下面这张图展示了这段代码做了什么 

String s = "abcd"; 
s = s.concat("ef");

![img](https://mmbiz.qpic.cn/mmbiz_jpg/rOeDV5Q9ZqBQEm43HdgicFiagoHSTK5MCGFc8drKBiabNOMkKvhjEEHd5k6uZ7Nh2krLhCbL7VE7GSMibxq5YxibKvg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

为什么Java字符串是不可变对象？

### **答案一：**Java的String类为什么要设成immutable类型

最流行的Java面试题之一就是：什么是不可变对象(immutable object)，不可变对象有什么好处，在什么情况下应该用，或者更具体一些，Java的String类为什么要设成immutable类型?

不可变对象，顾名思义就是创建后不可以改变的对象，典型的例子就是Java中的String类。

1. ```
   String s = "ABC"; 
   s.toLowerCase(); 
   ```

    

如上s.toLowerCase()并没有改变“ABC“的值，而是创建了一个新的String类“abc”，然后将新的实例的指向变量s。

相对于可变对象，不可变对象有很多优势：

1).不可变对象可以提高String Pool的效率和安全性。如果你知道一个对象是不可变的，那么需要拷贝这个对象的内容时，就不用复制它的本身而只是复制它的地址，复制地址(通常一个指针的大小)需要很小的内存效率也很高。对于同时引用这个“ABC”的其他变量也不会造成影响。

2).不可变对象对于多线程是安全的，因为在多线程同时进行的情况下，一个可变对象的值很可能被其他进程改变，这样会造成不可预期的结果，而使用不可变对象就可以避免这种情况。

当然也有其他方面原因，但是Java把String设成immutable最大的原因应该是效率和安全。

 

### **答案二：**This is an old yet still popular question

这是一个老生常谈的话题(This is an old yet still popular question). 在Java中将String设计成不可变的是综合考虑到各种因素的结果,想要理解这个问题,需要综合内存,同步,数据结构以及安全等方面的考虑. 在下文中,我将为各种原因做一个小结。

**1. 字符串常量池的需要**

字符串常量池(String pool, String intern pool, String保留池) 是Java堆内存中一个特殊的存储区域, 当创建一个String对象时,假如此字符串值已经存在于常量池中,则不会创建一个新的对象,而是引用已经存在的对象。

如下面的代码所示,将会在堆内存中只创建一个实际String对象.

1. ```
   String s1 = "abcd"; 
   String s2 = "abcd"; 
   ```

    

示意图如下所示:

 

 

 

[![为什么Java字符串是不可变对象？](http://s4.51cto.com/wyfs02/M00/5B/82/wKiom1UKddDBg-Y4AAD9ueRddMs759.jpg)](http://s4.51cto.com/wyfs02/M00/5B/82/wKiom1UKddDBg-Y4AAD9ueRddMs759.jpg)

 

请思考: 假若代码如下所示，s1和s2还会指向同一个实际的String对象吗?假若字符串对象允许改变,那么将会导致各种逻辑错误,比如改变一个对象会影响到另一个独立对象. 严格来说，这种常量池的思想,是一种优化手段.

1. ```
   String s1= "ab" + "cd"; 
   String s2= "abc" + "d"; 
   ```

    

也许这个问题违反新手的直觉, 但是考虑到现代编译器会进行常规的优化, 所以他们都会指向常量池中的同一个对象. 或者,你可以用 jd-gui 之类的工具查看一下编译后的class文件.

**2. 允许String对象缓存HashCode**

Java中String对象的哈希码被频繁地使用, 比如在hashMap 等容器中。

字符串不变性保证了hash码的唯一性,因此可以放心地进行缓存.这也是一种性能优化手段,意味着不必每次都去计算新的哈希码. 在String类的定义中有如下代码:

private int hash;//用来缓存HashCode

**3. 安全性**

String被许多的Java类(库)用来当做参数,例如 网络连接地址URL,文件路径path,还有反射机制所需要的String参数等, 假若String不是固定不变的,将会引起各种安全隐患。

假如有如下的代码:

1. [![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

   ```
   boolean connect(string s){ 
    
   if (!isSecure(s)) { 
    
   throw new SecurityException(); 
    
   } 
    
   // 如果在其他地方可以修改String,那么此处就会引起各种预料不到的问题/错误 
    
   causeProblem(s); 
    
   } 
   ```

   [![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

    

总体来说, String不可变的原因包括 设计考虑,效率优化问题,以及安全性这三大方面. 事实上,这也是Java面试中的许多 “为什么” 的答案。

### **答案三：String类不可变性的好处**

String是所有语言中最常用的一个类。我们知道在Java中，String是不可变的、final的。Java在运行时也保存了一个字符串池(String pool)，这使得String成为了一个特别的类。

String类不可变性的好处

1.只有当字符串是不可变的，字符串池才有可能实现。字符串池的实现可以在运行时节约很多heap空间，因为不同的字符串变量都指向池中的同一个字符串。但如果字符串是可变的，那么String interning将不能实现(译者注：String interning是指对不同的字符串仅仅只保存一个，即不会保存多个相同的字符串。)，因为这样的话，如果变量改变了它的值，那么其它指向这个值的变量的值也会一起改变。

2.如果字符串是可变的，那么会引起很严重的安全问题。譬如，数据库的用户名、密码都是以字符串的形式传入来获得数据库的连接，或者在socket编程中，主机名和端口都是以字符串的形式传入。因为字符串是不可变的，所以它的值是不可改变的，否则黑客们可以钻到空子，改变字符串指向的对象的值，造成安全漏洞。

3.因为字符串是不可变的，所以是多线程安全的，同一个字符串实例可以被多个线程共享。这样便不用因为线程安全问题而使用同步。字符串自己便是线程安全的。

4.类加载器要用到字符串，不可变性提供了安全性，以便正确的类被加载。譬如你想加载java.sql.Connection类，而这个值被改成了myhacked.Connection，那么会对你的数据库造成不可知的破坏。

5.因为字符串是不可变的，所以在它创建的时候hashcode就被缓存了，不需要重新计算。这就使得字符串很适合作为Map中的键，字符串的处理速度要快过其它的键对象。这就是HashMap中的键往往都使用字符串



## **2**.equals()方法、hashCode()方法的区别

HashCode被设计用来提高性能。equals()方法与hashCode()方法的区别在于：

1. 如果两个对象相等(equal)，那么他们一定有相同的哈希值。
2. 如果两个对象的哈希值相同，但他们未必相等(equal)。

![img](https://mmbiz.qpic.cn/mmbiz_jpg/rOeDV5Q9ZqBQEm43HdgicFiagoHSTK5MCGAvNnxoWWxCgaZFCo1C2PpVuFxgSl95UPjdtIFR7qvwu9Hib8jDz0MpA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

内容主要解决下面几个问题：

1 **equals() 的作用是什么**？

2 **equals() 与 == 的区别是什么**？

3 **hashCode() 的作用是什么**？

4 **hashCode() 和 equals() 之间有什么联系？**

------

### **第1部分 equals() 的作用**

equals() 的作用是 **用来判断两个对象是否相等**。

equals() 定义在JDK的Object.java中。通过判断两个对象的地址是否相等(即，是否是同一个对象)来区分它们是否相等。源码如下：

```
public boolean equals(Object obj) {
    return (this == obj);
}
```

既然Object.java中定义了equals()方法，这就意味着所有的Java类都实现了equals()方法，所有的类都可以通过equals()去比较两个对象是否相等。 但是，我们已经说过，使用默认的“**equals()**”方法，等价于“**==**”方法。因此，我们通常会重写equals()方法：若两个对象的内容相等，则equals()方法返回true；否则，返回fasle。  

下面根据“**类是否覆盖equals()方法**”，将它分为2类。
(01) 若某个类没有覆盖equals()方法，当它的通过equals()比较两个对象时，实际上是比较两个对象是不是同一个对象。这时，等价于通过“==”去比较这两个对象。
(02) 我们可以覆盖类的equals()方法，来让equals()通过其它方式比较两个对象是否相等。通常的做法是：若两个对象的内容相等，则equals()方法返回true；否则，返回fasle。

下面，举例对上面的2种情况进行说明。

**1.  “没有覆盖equals()方法”的情况**

**代码如下 (EqualsTest1.java)**：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
import java.util.*;
import java.lang.Comparable;

/**
 * @desc equals()的测试程序。
 *
 * @author skywang
 * @emai kuiwu-wang@163.com
 */
public class EqualsTest1{

    public static void main(String[] args) {
        // 新建2个相同内容的Person对象，
        // 再用equals比较它们是否相等
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        System.out.printf("%s\n", p1.equals(p2));
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return name + " - " +age;
        }
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**运行结果**：

```
false
```

**结果分析**：

​       我们通过 p1.equals(p2) 来“比较p1和p2是否相等时”。实际上，调用的Object.java的equals()方法，即调用的 (p1==p2) 。它是比较“p1和p2是否是同一个对象”。
       而由 p1 和 p2 的定义可知，它们虽然内容相同；但它们是两个不同的对象！因此，返回结果是false。

 

\2. "**覆盖equals()方法**"**的情况**

我们修改上面的*EqualsTest1.java*：**覆盖equals()方法**。

**代码如下 (EqualsTest2.java)**：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 import java.util.*;
 2 import java.lang.Comparable;
 3 
 4 /**
 5  * @desc equals()的测试程序。
 6  *
 7  * @author skywang
 8  * @emai kuiwu-wang@163.com
 9  */
10 public class EqualsTest2{
11 
12     public static void main(String[] args) {
13         // 新建2个相同内容的Person对象，
14         // 再用equals比较它们是否相等
15         Person p1 = new Person("eee", 100);
16         Person p2 = new Person("eee", 100);
17         System.out.printf("%s\n", p1.equals(p2));
18     }
19 
20     /**
21      * @desc Person类。
22      */
23     private static class Person {
24         int age;
25         String name;
26 
27         public Person(String name, int age) {
28             this.name = name;
29             this.age = age;
30         }
31 
32         public String toString() {
33             return name + " - " +age;
34         }
35 
36         /** 
37          * @desc 覆盖equals方法 
38          */  
39         @Override
40         public boolean equals(Object obj){  
41             if(obj == null){  
42                 return false;  
43             }  
44               
45             //如果是同一个对象返回true，反之返回false  
46             if(this == obj){  
47                 return true;  
48             }  
49               
50             //判断是否类型相同  
51             if(this.getClass() != obj.getClass()){  
52                 return false;  
53             }  
54               
55             Person person = (Person)obj;  
56             return name.equals(person.name) && age==person.age;  
57         } 
58     }
59 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**运行结果**：

```
true
```

**结果分析**：

我们在EqualsTest2.java 中重写了Person的equals()函数：当两个Person对象的 name 和 age 都相等，则返回true。
因此，运行结果返回true。

 

讲到这里，顺便说一下java对equals()的要求。有以下几点：

```
1. 对称性：如果x.equals(y)返回是"true"，那么y.equals(x)也应该返回是"true"。
2. 反射性：x.equals(x)必须返回是"true"。
3. 类推性：如果x.equals(y)返回是"true"，而且y.equals(z)返回是"true"，那么z.equals(x)也应该返回是"true"。
4. 一致性：如果x.equals(y)返回是"true"，只要x和y内容一直不变，不管你重复x.equals(y)多少次，返回都是"true"。
5. 非空性，x.equals(null)，永远返回是"false"；x.equals(和x不同类型的对象)永远返回是"false"。
```

 

现在，再回顾一下equals()的作用：判断两个对象是否相等。当我们重写equals()的时候，可千万不好将它的作用给改变了！

 

------

### **第2部分 equals() 与 == 的区别是什么？**

== : 它的作用是**判断两个对象的地址是不是相等。**即，判断两个对象是不试同一个对象。

equals() : 它的作用也是**判断两个对象是否相等。**但它一般有两种使用情况(前面第1部分已详细介绍过)：
                 情况1，类没有覆盖equals()方法。则通过equals()比较该类的两个对象时，等价于通过“==”比较这两个对象。
                 情况2，类覆盖了equals()方法。一般，我们都覆盖equals()方法来两个对象的内容相等；若它们的内容相等，则返回true(即，认为这两个对象相等)。

 

下面，通过示例比较它们的区别。

**代码如下**： 

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

**运行结果**：

```
p1.equals(p2) : true
p1==p2 : false
```

**结果分析**：

在EqualsTest3.java 中：
(01) **p1.equals(p2)** 
        这是判断p1和p2的内容是否相等。因为Person覆盖equals()方法，而这个equals()是用来判断p1和p2的内容是否相等，恰恰p1和p2的内容又相等；因此，返回true。

(02) **p1==p2**
       这是判断p1和p2是否是同一个对象。由于它们是各自新建的两个Person对象；因此，返回false。

 

------

### **第3部分 hashCode() 的作用**

hashCode() 的作用是**获取哈希码**，也称为散列码；它实际上是返回一个int整数。这个**哈希码的作用**是确定该对象在哈希表中的索引位置。

hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode() 函数。
        虽然，每个Java类都包含hashCode() 函数。但是，仅仅当创建并某个“类的散列表”(关于“散列表”见下面说明)时，该类的hashCode() 才有用(作用是：确定该类的每一个对象在散列表中的位置；其它情况下(例如，创建类的单个对象，或者创建类的对象数组等等)，类的hashCode() 没有作用。
       上面的散列表，指的是：Java集合中本质是散列表的类，如HashMap，Hashtable，HashSet。

​       也就是说：**hashCode() 在散列表中才有用，在其它情况下没用。**在散列表中hashCode() 的作用是获取对象的散列码，进而确定该对象在散列表中的位置。

 

OK！至此，我们搞清楚了：hashCode()的作用是获取散列码。但是，散列码是用来干什么的呢？为什么散列表需要散列码呢？要解决这些问题，就需要理解散列表！关于散列表的内容，非三言两语道的明白；大家可以通过下面几篇文章来学习：

[[转载\] 散列表(Hash Table)从理论到实用（上）](http://www.cnblogs.com/skywang12345/p/3311899.html)

[[转载\] 散列表(Hash Table)从理论到实用（中）](http://www.cnblogs.com/skywang12345/p/3311909.html)

[[转载\] 散列表(Hash Table)从理论到实用（下）](http://www.cnblogs.com/skywang12345/p/3311915.html) 

 

为了能理解后面的内容，这里简单的介绍一下散列码的作用。

```
我们都知道，散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！
散列表的本质是通过数组实现的。当我们要获取散列表中的某个“值”时，实际上是要获取数组中的某个位置的元素。而数组的位置，就是通过“键”来获取的；更进一步说，数组的位置，是通过“键”对应的散列码计算得到的。
```

下面，我们以HashSet为例，来深入说明hashCode()的作用。

​        假设，HashSet中已经有1000个元素。当插入第1001个元素时，需要怎么处理？因为HashSet是Set集合，它允许有重复元素。
        “将第1001个元素逐个的和前面1000个元素进行比较”？显然，这个效率是相等低下的。散列表很好的解决了这个问题，它根据元素的散列码计算出元素在散列表中的位置，然后将元素插入该位置即可。对于相同的元素，自然是只保存了一个。
        由此可知，若两个元素相等，它们的散列码一定相等；但反过来确不一定。在散列表中，
                           1、如果两个对象相等，那么它们的hashCode()值一定要相同；
                           2、如果两个对象hashCode()相等，它们并不一定相等。
                           注意：这是在散列表中的情况。在非散列表中一定如此！

 

对“hashCode()的作用”就谈这么多。

 

------

### **第4部分 hashCode() 和 equals() 的关系**

接下面，我们讨论另外一个话题。网上很多文章将 hashCode() 和 equals 关联起来，有的讲的不透彻，有误导读者的嫌疑。在这里，我自己梳理了一下 “hashCode() 和 equals()的关系”。

我们以“**类的用途**”来将“hashCode() 和 equals()的关系”分2种情况来说明。

 

**1. 第一种 不会创建“类对应的散列表”**

​         这里所说的“不会创建类对应的散列表”是说：我们不会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中，用到该类。例如，不会创建该类的HashSet集合。

​        在这种情况下，该类的“hashCode() 和 equals() ”没有半毛钱关系的！
        这种情况下，equals() 用来比较该类的两个对象是否相等。而hashCode() 则根本没有任何作用，所以，不用理会hashCode()。

下面，我们通过示例查看类的**两个对象相等** 以及 **不等**时hashCode()的取值。

**源码如下** (NormalHashCodeTest.java)：

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

**运行结果**：

```
p1.equals(p2) : true; p1(1169863946) p2(1901116749)
p1.equals(p3) : false; p1(1169863946) p3(2131949076)
```

从结果也可以看出：**p1和p2相等的情况下，hashCode()也不一定相等。**

 

**2. 第二种 会创建“类对应的散列表”**

​        这里所说的“会创建类对应的散列表”是说：我们会在HashSet, Hashtable, HashMap等等这些本质是散列表的数据结构中，用到该类。例如，会创建该类的HashSet集合。

​        在这种情况下，该类的“hashCode() 和 equals() ”是有关系的：
        1)、如果两个对象相等，那么它们的hashCode()值一定相同。
              这里的相等是指，通过equals()比较两个对象时返回true。
        2)、如果两个对象hashCode()相等，它们并不一定相等。
               因为在散列表中，hashCode()相等，即两个键值对的哈希值相等。然而哈希值相等，并不一定能得出键值对相等。补充说一句：“两个不同的键值对，哈希值相等”，这就是哈希冲突。

​        此外，在这种情况下。若要判断两个对象是否相等，除了要覆盖equals()之外，也要覆盖hashCode()函数。否则，equals()无效。
例如，创建Person类的HashSet集合，必须同时覆盖Person类的equals() 和 hashCode()方法。
        如果单单只是覆盖equals()方法。我们会发现，equals()方法没有达到我们想要的效果。

**参考代码** (ConflictHashCodeTest1.java)：

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

**运行结果**：

```
p1.equals(p2) : true; p1(1169863946) p2(1690552137)
set:[(eee, 100), (eee, 100), (aaa, 200)]
```

**结果分析**：

​        我们重写了Person的equals()。但是，很奇怪的发现：HashSet中仍然有重复元素：p1 和 p2。为什么会出现这种情况呢？

​        这是因为虽然p1 和 p2的内容相等，但是它们的hashCode()不等；所以，HashSet在添加p1和p2的时候，认为它们不相等。

 

下面，我们同时覆盖equals() 和 hashCode()方法。

参考代码 (ConflictHashCodeTest2.java)：

![img](https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif) View Code

**运行结果**：

```
p1.equals(p2) : true; p1(68545) p2(68545)
p1.equals(p4) : false; p1(68545) p4(68545)
set:[aaa - 200, eee - 100]
```

**结果分析**：

​        这下，equals()生效了，HashSet中没有重复元素。
        *比较p1和p2*，我们发现：它们的hashCode()相等，通过equals()比较它们也返回true。所以，p1和p2被视为相等。
        *比较p1和p4*，我们发现：虽然它们的hashCode()相等；但是，通过equals()比较它们返回false。所以，p1和p4被视为不相等。



## **3**

Java异常类的层次结构

图中红色部分为受检查异常。它们必须被捕获，或者在函数中声明为抛出该异常。 

![img](https://mmbiz.qpic.cn/mmbiz_jpg/rOeDV5Q9ZqBQEm43HdgicFiagoHSTK5MCGoibNwQ6ia4kR3NNeWRib174x3tEuRbFV7CQVx0zqdGXu1FgfmtDfhqQ4w/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## **4**

集合类的层次结构

注意Collections和Collection的区别。（Collections包含有各种有关集合操作的静态多态方法） 

![1559118189832](C:\Users\SWX\AppData\Local\Temp\1559118189832.png)

## **5**

Java同步

Java同步机制可通过类比建筑物来阐明。 

![1559118364695](C:\Users\SWX\AppData\Local\Temp\1559118364695.png)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==) 

## **6**

别名

别名意味着有多个变量指向同一可被更新的内存块，这些别名分别是不同的对象类型。 

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==) ![1559118382104](C:\Users\SWX\AppData\Local\Temp\1559118382104.png)

## **7**

堆和栈

图解表明了方法和对象在运行时内存中的位置。 

![1559118418289](C:\Users\SWX\AppData\Local\Temp\1559118418289.png)

## **8**

Java虚拟机运行时数据区域

图解展示了整个虚拟机运行时数据区域的情况。 

![1559118435349](C:\Users\SWX\AppData\Local\Temp\1559118435349.png)

