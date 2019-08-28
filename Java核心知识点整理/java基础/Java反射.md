

# JAVA反射

## 动态语言

动态语言，是指程序在运行时可以改变其结构：新的函数可以引进，已有的函数可以被删除等结 构上的变化。比如常见的JavaScript就是动态语言，除此之外Ruby,Python等也属于动态语言， 而C、C++则不属于动态语言。从反射角度说JAVA属于半动态语言。 

### 反射机制概念 （运行状态中知道类所有的属性和方法） 

![java反射机制](E:\gitbook\Java基础篇\images\java反射机制.png)

在 Java 中的反射机制是指**在运行状态中，对于任意一个类都能够知道这个类所有的属性和方法； 并且对于任意一个对象，都能够调用它的任意一个方法**；这种动态获取信息以及动态调用对象方 法的功能成为Java语言的反射机制。

### 反射的应用场合 

**编译时类型和运行时类型**
 在Java程序中许多对象在运行是都会出现两种类型：编译时类型和运行时类型。 编译时的类型由 声明对象时使用的类型来决定，运行时的类型由实际赋值给对象的类型决定 。如： 
Person p=new Student(); 
其中编译时类型为Person，运行时类型为Student。

程序在运行时还可能接收到外部传入的对象，该对象的编译时类型为 Object,但是程序有需要调用 该对象的运行时类型的方法。为了解决这些问题，程序需要在运行时发现对象和类的真实信息。 然而，如果编译时根本无法预知该对象和类属于哪些类，程序只能依靠运行时信息来发现该对象 和类的真实信息，此时就必须使用到反射了。 

### Java反射API 

反射 API用来生成 JVM中的类、接口或则对象的信息。  

1. Class类：反射的核心类，可以获取类的属性，方法等信息。  
2. Field类：Java.lang.reflec包中的类，表示类的成员变量，可以用来获取和设置类之中的属性 值。 
3. Method类： Java.lang.reflec 包中的类，表示类的方法，它可以用来获取类中的方法信息或 者执行方法。  
4. Constructor类： Java.lang.reflec包中的类，表示类的构造方法。 

### 反射使用步骤（获取Class对象、调用对象方法） 

1. 获取想要操作的类的 Class 对象，他是反射的核心，通过 Class 对象我们可以任意调用类的方 法。 
2. 调用Class类中的方法，既就是反射的使用阶段。
3. 使用反射API来操作这些信息。 

### 获取Class对象的3种方法 

**调用某个对象的 getClass() 方法**
	 Person p=new Person(); 
	 Class clazz=p.getClass(); 
**调用某个类的 class属性来获取该类对应的 Class对象**

​	 Class clazz=Person.class; 
**使用 Class类中的 forName() 静态方法 ( 最安全 / 性能最好 )** 

​	 Class clazz=Class.forName("类的全路径"); (最常用) 

**当我们获得了想要操作的类的 Class 对象后，可以通过 Class 类中的方法获取并查看该类中的方法 和属性。**  

```
 //获取Person类的Class对象 
            Class clazz=Class.forName("reflection.Person"); 
   //获取Person类的所有方法信息 
            Method[] method=clazz.getDeclaredMethods(); 
            for(Method m:method){ 
                System.out.println(m.toString()); 
            } 
            //获取Person类的所有成员属性信息 
            Field[] field=clazz.getDeclaredFields(); 
            for(Field f:field){ 
                System.out.println(f.toString()); 
            } 
            //获取Person类的所有构造方法信息 
            Constructor[] constructor=clazz.getDeclaredConstructors(); 
            for(Constructor c:constructor){ 
                System.out.println(c.toString()); 
            } 
```

### 创建对象的两种方法 

**Class对象的 newInstance()** 

1. 使用 Class 对象的 newInstance()方法来创建该 Class 对象对应类的实例，但是这种方法要求 该Class对象对应的类有默认的空构造器。 

**调用 Constructor对象的 newInstance()** 

2. 先使用Class对象获取指定的Constructor对象，再调用Constructor对象的newInstance() 方法来创建 Class对象对应类的实例,通过这种方法可以选定构造方法创建实例。 

   ```
    //获取Person类的 Class对象 
               Class clazz=Class.forName("reflection.Person");  
               //使用.newInstane方法创建对象 
               Person p=(Person) clazz.newInstance(); 
      //获取构造方法并创建对象 
               Constructor c=clazz.getDeclaredConstructor(String.class,String.class,int.class); 
               //创建对象并设置属性
               Person p1=(Person) c.newInstance("李四","男",20); 
           
   ```



## 反射机制的相关类

与Java反射相关的类如下：

|     类名      | 用途                                             |
| :-----------: | ------------------------------------------------ |
|    Class类    | 代表类的实体，在运行的Java应用程序中表示类和接口 |
|    Field类    | 代表类的成员变量（成员变量也称为类的属性）       |
|   Method类    | 代表类的方法                                     |
| Constructor类 | 代表类的构造方法                                 |

### 1.Class类

[Class](https://developer.android.google.cn/reference/java/lang/Class)代表类的实体，在运行的Java应用程序中表示类和接口 

- **获得类相关的方法**

| 方法                       | 用途                                                   |
| -------------------------- | ------------------------------------------------------ |
| asSubclass(Class<U> clazz) | 把传递的类的对象转换成代表其子类的对象                 |
| Cast                       | 把对象转换成代表类或是接口的对象                       |
| getClassLoader()           | 获得类的加载器                                         |
| getClasses()               | 返回一个数组，数组中包含该类中所有公共类和接口类的对象 |
| getDeclaredClasses()       | 返回一个数组，数组中包含该类中所有类和接口类的对象     |
| forName(String className)  | 根据类名返回类的对象                                   |
| getName()                  | 获得类的完整路径名字                                   |
| newInstance()              | 创建类的实例                                           |
| getPackage()               | 获得类的包                                             |
| getSimpleName()            | 获得类的名字                                           |
| getSuperclass()            | 获得当前类继承的父类的名字                             |
| getInterfaces()            | 获得当前类实现的类或是接口                             |

- **获得类中属性相关的方法**

| 方法                          | 用途                   |
| ----------------------------- | ---------------------- |
| getField(String name)         | 获得某个公有的属性对象 |
| getFields()                   | 获得所有公有的属性对象 |
| getDeclaredField(String name) | 获得某个属性对象       |
| getDeclaredFields()           | 获得所有属性对象       |

- **获得类中注解相关的方法**

| 方法                                            | 用途                                   |
| ----------------------------------------------- | -------------------------------------- |
| getAnnotation(Class<A> annotationClass)         | 返回该类中与参数类型匹配的公有注解对象 |
| getAnnotations()                                | 返回该类所有的公有注解对象             |
| getDeclaredAnnotation(Class<A> annotationClass) | 返回该类中与参数类型匹配的所有注解对象 |
| getDeclaredAnnotations()                        | 返回该类所有的注解对象                 |

- **获得类中构造器相关的方法**

| 方法                                               | 用途                                   |
| -------------------------------------------------- | -------------------------------------- |
| getConstructor(Class...<?> parameterTypes)         | 获得该类中与参数类型匹配的公有构造方法 |
| getConstructors()                                  | 获得该类的所有公有构造方法             |
| getDeclaredConstructor(Class...<?> parameterTypes) | 获得该类中与参数类型匹配的构造方法     |
| getDeclaredConstructors()                          | 获得该类所有构造方法                   |

- **获得类中方法相关的方法**

| 方法                                                       | 用途                   |
| ---------------------------------------------------------- | ---------------------- |
| getMethod(String name, Class...<?> parameterTypes)         | 获得该类某个公有的方法 |
| getMethods()                                               | 获得该类所有公有的方法 |
| getDeclaredMethod(String name, Class...<?> parameterTypes) | 获得该类某个方法       |
| getDeclaredMethods()                                       | 获得该类所有方法       |

- **类中其他重要的方法**

| 方法                                                         | 用途                             |
| ------------------------------------------------------------ | -------------------------------- |
| isAnnotation()                                               | 如果是注解类型则返回true         |
| isAnnotationPresent(Class<? extends Annotation> annotationClass) | 如果是指定类型注解类型则返回true |
| isAnonymousClass()                                           | 如果是匿名类则返回true           |
| isArray()                                                    | 如果是一个数组类则返回true       |
| isEnum()                                                     | 如果是枚举类则返回true           |
| isInstance(Object obj)                                       | 如果obj是该类的实例则返回true    |
| isInterface()                                                | 如果是接口类则返回true           |
| isLocalClass()                                               | 如果是局部类则返回true           |
| isMemberClass()                                              | 如果是内部类则返回true           |

