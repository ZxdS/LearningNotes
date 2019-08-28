# JAVA异常

## 1.分类及处理 

### 1.1概念

如果某个方法不能按照正常的途径完成任务，就可以通过另一种路径退出方法。在这种情况下 会抛出一个封装了错误信息的对象。此时，这个方法会立刻退出同时不返回任何值。另外，调用 这个方法的其他代码也无法继续执行，异常处理机制会将代码执行交给异常处理器。 

![java异常](E:\gitbook\Java基础篇\images\java异常.png)

### 1.2异常分类

**Throwable**是 Java 语言中所有错误或异常的超类。下一层分为**Error**和**Exception**  
**Error** 

1. Error类是指java运行时系统的内部错误和资源耗尽错误。应用程序不会抛出该类对象。如果 出现了这样的错误，除了告知用户，剩下的就是尽力使程序安全的终止。  

**Exception** （ RuntimeExcepti on 、 CheckedException ）

 2. Exception 又 有 两 个 分 支 ， 一 个 是 运 行 时 异 常 RuntimeException ， 一 个 是 CheckedException。 
    **RuntimeException** 如：NullPointerException、ClassCastException；

  一个是检查异常 CheckedException，如 I/O 错误导致的 IOException、SQLException。 RuntimeException 是 那些可能在 Java 虚拟机正常运行期间抛出的异常的超类。 如果出现RuntimeException，那么一 定是程序员的错误. 

  **检查异常 CheckedException**：一般是外部错误，这种异常都发生在编译阶段，Java 编译器会强 制程序去捕获此类异常，即会出现要求你把这段可能出现异常的程序进行 try catch，该类异常一 般包括几个方面：  

  1. 试图在文件尾部读取数据  

  2. 试图打开一个错误格式的URL  

  3. 试图根据给定的字符串查找class对象，而这个字符串表示的类并不存在 

     

### 1.3异常的处理方式 

**遇到问题不进行具体处理，而是继续抛给调用者**
 （ throw,throws ）
 抛出异常有三种形式，一是throw,一个throws，还有一种系统自动抛异常。 
public static void main(String[] args) {  
    String s = "abc";  
    if(s.equals("abc")) {  
      throw new NumberFormatException();  
    } else {  
      System.out.println(s);  
    }  
}  
int div(int a,int b) throws Exception{ 
return a/b;} 
**try catch 捕获异常针对性处理方式**

### 1.4 Throw和throws的区别：  

位置不同

  1. throws 用在函数上，后面跟的是异常类，可以跟多个；而 throw 用在函数内，后面跟的 是异常对象。 

功能不同：

 2. throws 用来声明异常，让调用者只知道该功能可能出现的问题，可以给出预先的处理方 式；throw抛出具体的问题对象，执行到throw，功能就已经结束了，跳转到调用者，并 将具体的问题对象抛给调用者。也就是说 throw 语句独立存在时，下面不要定义其他语 句，因为执行不到。 

 3. throws 表示出现异常的一种可能性，并不一定会发生这些异常；throw 则是抛出了异常， 执行throw则一定抛出了某种异常对象。  

4. 两者都是消极处理异常的方式，只是抛出或者可能抛出异常，但是不会由函数去处理异 常，真正的处理异常由函数的上层调用处理。 

   

   **以下是根据前人总结的一些异常处理的建议：**

   1.只在必要使用异常的地方才使用异常，不要用异常去控制程序的流程

   谨慎地使用异常，异常捕获的代价非常高昂，异常使用过多会严重影响程序的性能。如果在程序中能够用if语句和Boolean变量来进行逻辑判断，那么尽量减少异常的使用，从而避免不必要的异常捕获和处理。 比如下面这段经典的程序： 

   ```
   public void useExceptionsForFlowControl() {  
     try {  
     while (true) {  
       increaseCount();  
       }  
     } catch (MaximumCountReachedException ex) {  
     }  
     //Continue execution  
   }  
       
   public void increaseCount() throws MaximumCountReachedException {  
     if (count >= 5000)  
       throw new MaximumCountReachedException();  
   }
   ```

   上边的useExceptionsForFlowControl()用一个无限循环来增加count直到抛出异常，这种做法并没有说让代码不易读，而是使得程序执行效率降低。 

   2.切忌使用空catch块

   　　在捕获了异常之后什么都不做，相当于忽略了这个异常。千万不要使用空的catch块，空的catch块意味着你在程序中隐藏了错误和异常，并且很可能导致程序出现不可控的执行结果。如果你非常肯定捕获到的异常不会以任何方式对程序造成影响，最好用Log日志将该异常进行记录，以便日后方便更新和维护。

   3.检查异常和非检查异常的选择

   　　一旦你决定抛出异常，你就要决定抛出什么异常。这里面的主要问题就是抛出检查异常还是非检查异常。

   　　检查异常导致了太多的try…catch代码，可能有很多检查异常对开发人员来说是无法合理地进行处理的，比如SQLException，而开发人员却不得不去进行try…catch，这样就会导致经常出现这样一种情况：逻辑代码只有很少的几行，而进行异常捕获和处理的代码却有很多行。这样不仅导致逻辑代码阅读起来晦涩难懂，而且降低了程序的性能。

   　　我个人建议尽量避免检查异常的使用，如果确实该异常情况的出现很普遍，需要提醒调用者注意处理的话，就使用检查异常；否则使用非检查异常。

   　　因此，在一般情况下，我觉得尽量将检查异常转变为非检查异常交给上层处理。

   4.注意catch块的顺序

   　　不要把上层类的异常放在最前面的catch块。比如下面这段代码：

   ```
   try {
           FileInputStream inputStream = new FileInputStream("d:/a.txt");
           int ch = inputStream.read();
           System.out.println("aaa");
           return "step1";
       } catch (IOException e) {
   　　      System.out.println("io exception");　　      
            return "step2";
       }catch (FileNotFoundException e) {
           System.out.println("file not found");　　　　      
           return "step3";
       }finally{
           System.out.println("finally block");
           //return "finally";
       }
   ```

   第二个catch的FileNotFoundException将永远不会被捕获到，因为FileNotFoundException是IOException的子类。 

 5. 不要将提供给用户看的信息放在异常信息里

      比如下面这段代码：

    ```
    public class Main {
        public static void main(String[] args) {
            try {
                String user = null;
                String pwd = null;
                login(user,pwd);
            } catch (Exception e) {
                System.out.println(e.getMessage());
            }
             
        }
         
        public static void login(String user,String pwd) {
            if(user==null||pwd==null)
                throw new NullPointerException("用户名或者密码为空");
            //...
        }
    }
    ```
    
    展示给用户错误提示信息最好不要跟程序混淆一起，比较好的方式是将所有错误提示信息放在一个配置文件中统一管理。 
    
    6.避免多次在日志信息中记录同一个异常
    
    　　只在异常最开始发生的地方进行日志信息记录。很多情况下异常都是层层向上跑出的，如果在每次向上抛出的时候，都Log到日志系统中，则会导致无从查找异常发生的根源。
    
    7. 异常处理尽量放在高层进行
    
    　　尽量将异常统一抛给上层调用者，由上层调用者统一之时如何进行处理。如果在每个出现异常的地方都直接进行处理，会导致程序异常处理流程混乱，不利于后期维护和异常错误排查。由上层统一进行处理会使得整个程序的流程清晰易懂。
    
    8. 在finally中释放资源
    
    　　如果有使用文件读取、网络操作以及数据库操作等，记得在finally中释放资源。这样不仅会使得程序占用更少的资源，也会避免不必要的由于资源未释放而发生的异常情况。

