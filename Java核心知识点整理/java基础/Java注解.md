#  JAVA注解

## 概念 

> Annotation（注解）是 Java 提供的一种对元程序中元素关联信息和元数据（metadata）的途径 和方法。**Annatation(注解)是一个接口**，程序可以通过反射来获取指定程序中元素的 Annotation 对象，然后通过该Annotation对象来获取注解中的元数据信息。 
>
> 

## 4 种标准元注解

元注解的作用是负责注解其他注解。 Java5.0 定义了 4 个标准的 meta-annotation 类型，它们被 用来提供对其它 annotation类型作说明。 

**@Target修饰的对象范围**
 @Target说明了Annotation所修饰的对象范围： Annotation**可被用于 packages、types（类、 接口、枚举、Annotation 类型）、类型成员（方法、构造方法、成员变量、枚举值）、方法参数 和本地变量（如循环变量、catch参数）**。在Annotation类型的声明中使用了target可更加明晰 其修饰的目标 

- ElementType.ANNOTATION_TYPE 可以给一个注解进行注解

- ElementType.CONSTRUCTOR 可以给构造方法进行注解

- ElementType.FIELD 可以给属性进行注解

- ElementType.LOCAL_VARIABLE 可以给局部变量进行注解

- ElementType.METHOD 可以给方法进行注解

- ElementType.PACKAGE 可以给一个包进行注解

- ElementType.PARAMETER 可以给一个方法内的参数进行注解

- ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举

  **@Retention定义被保留的时间长短**

   **Retention 定义了该Annotation被保留的时间长短**：表示需要在什么级别保存注解信息，用于描 述注解的生命周期（即：被描述的注解在什么范围内有效），取值（RetentionPoicy）由： 
   SOURCE:在源文件中有效（只在源码阶段保留，在编译器进行编译时它将被丢弃忽视 ） 

 CLASS:在class文件中有效（只被保留到编译进行的时候，它并不会被加载到 JVM 中 ）

  RUNTIME:在运行时有效（可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们 ,即运行时保留） 
**@Documented 描述-javadoc** 

@ Documented 用于描述其它类型的 annotation 应该被作为被标注的程序成员的公共 API，因 此可以被例如javadoc此类的工具文档化。


**@Inherited 阐述了某个被标注的类型是被继承的** 

@Inherited 元注解是一个标记注解，**@Inherited 阐述了某个被标注的类型是被继承的**。如果一 个使用了@Inherited 修饰的 annotation 类型被用于一个 class，则这个 annotation 将被用于该 class的子类。



#### @Repeatable

Repeatable 自然是可重复的意思。@Repeatable 是 Java 1.8 才加进来的，所以算是一个新的特性。

什么样的注解会多次应用呢？通常是注解的值可以同时取多个。

举个例子，一个人他既是程序员又是产品经理,同时他还是个画家。

```
@interface Persons {
    Person[]  value();
}
@Repeatable(Persons.class)
@interface Person{
    String role default "";
}
@Person(role="artist")
@Person(role="coder")
@Person(role="PM")
public class SuperMan{
}
```

![Java注解脑图](E:\gitbook\Java核心知识点整理\images\Java注解脑图.png)

 

## 注解处理器 

如果没有用来读取注解的方法和工作，那么注解也就不会比注释更有用处了。使用注解的过程中， 很重要的一部分就是创建于使用注解处理器。Java SE5扩展了反射机制的API，以帮助程序员快速 的构造自定义注解处理器。下面实现一个注解处理器。 

```
  /1：*** 定义注解*/ 
@Target(ElementType.FIELD) 
@Retention(RetentionPolicy.RUNTIME) 
@Documented 
public @interface FruitProvider { 
    /**供应商编号*/ 
public int id() default -1; 
/*** 供应商名称*/ 
public String name() default ""；
        /** * 供应商地址*/ 
public String address() default ""; 
} 

//2：注解使用 
public class Apple { 
    @FruitProvider(id = 1, name = "陕西红富士集团", address = "陕西省西安市延安路") 
    private String appleProvider; 
    public void setAppleProvider(String appleProvider) { 
        this.appleProvider = appleProvider; 
    } 
    public String getAppleProvider() { 
        return appleProvider; 
    } 
} 

/3：*********** 注解处理器 ***************/ 
public class FruitInfoUtil { 
    public static void getFruitInfo(Class<?> clazz) { 
        String strFruitProvicer = "供应商信息："; 
        Field[] fields = clazz.getDeclaredFields();//通过反射获取处理注解 
        for (Field field : fields) { 
             if (field.isAnnotationPresent(FruitProvider.class)) { 
       FruitProvider fruitProvider = (FruitProvider) field.getAnnotation(FruitProvider.class); 
 //注解信息的处理地方        
strFruitProvicer = " 供应商编号：" + fruitProvider.id() + " 供应商名称：" 
                        + fruitProvider.name() + " 供应商地址："+ fruitProvider.address(); 
                System.out.println(strFruitProvicer); 
            } 
        } 
    } 
}
```

[详解参考]: https://blog.csdn.net/qq1404510094/article/details/80577555

### 注解的属性

注解的属性也叫做成员变量。注解只有成员变量，没有方法。注解的成员变量在注解的定义中以“无形参的方法”形式来声明，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。 

```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
    int id();
    String msg();
}
```

上面代码定义了 TestAnnotation 这个注解中拥有 id 和 msg 两个属性。在使用的时候，我们应该给它们进行赋值。

赋值的方式是在注解的括号内以 value=”” 形式，多个属性之前用 ，隔开。

```
@TestAnnotation(id=3,msg="hello annotation")
public class Test {
}
```

需要注意的是，在注解中定义属性时它的类型必须是 8 种基本数据类型外加 类、接口、注解及它们的数组。

注解中属性可以有默认值，默认值需要用 default 关键值指定。比如：

```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
    public int id() default -1;
    public String msg() default "Hi";
}
```

TestAnnotation 中 id 属性默认值为 -1，msg 属性默认值为 Hi。  它可以这样应用。 

```
@TestAnnotation()
public class Test {}
```

因为有默认值，所以无需要再在 @TestAnnotation 后面的括号里面进行赋值了，这一步可以省略。

另外，还有一种情况。如果一个注解内仅仅只有一个名字为 value 的属性时，应用这个注解时可以直接接属性值填写到括号内。

```
public @interface Check {
    String value();
}
```

上面代码中，Check 这个注解只有 value 这个属性。所以可以这样应用。 

```
@Check("hi")
int a;
```

这和下面的效果是一样的 

```
@Check(value="hi")
int a;
```

最后，还需要注意的一种情况是一个注解没有任何属性。比如 

```
public @interface Perform {}
```

那么在应用这个注解的时候，括号都可以省略。 

```
@Perform
public void testMethod(){}
```



### 总结

- 如果注解难于理解，你就把它类同于标签，标签为了解释事物，注解为了解释代码。
- 注解的基本语法，创建如同接口，但是多了个 @ 符号。
- 注解的元注解。
- 注解的属性。
- 注解主要给编译器及工具类型的软件用的。
- 注解的提取需要借助于 Java 的反射技术，反射比较慢，所以注解使用时也需要谨慎计较时间成本。