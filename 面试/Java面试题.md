## Java面试题

### 一、Java基础

#### 1.Java面向对象有哪些特征

* 封装：说明一个类的行为和属性与其他类的关系，低耦合，高内聚。

* 继承：是父类和子类的关系。

* 多态：说明类与类的关系，两个类由继承关系，存在由方法的重写，故可以在调用时有父类引用指向子类对象。多态必备的三个要素：继承、重写、父类引用指向子类对象。

#### 2.ArrayList和LinkedList有什么区别

ArrayList和LinkedList都实现了List接口，它们有以下不同点：

* ArrayList是基于索引的数据接口，它的底层是数组。它可以以O(1)时间复杂度对元素进行随机访问。但是要删除数据开销很大，因为需要重排数组中的所有数据，时间复杂度为O(n)。
* LinkedList是以元素列表的形式存储它的数据，每一个元素都和它前一个和后一个元素链接在一起，这种情况下，查找某个元素的时间复杂度是O(n)。它插入或删除的时间复杂度为O(1)
* LinkedList比ArrayList更占内存，因为ArrayList的每个索引的位置是实际的数据，而LinkedList中每个节点中存储的是实际的数据和前后节点的位置。(一个LinkedList实例存储了两个值：Node<E> first和Node<E> last分别表示链表的头节点和尾节点，每个Node实例存储了三个值：E item, Node next, Node pre)

什么场景下更适合用LinkedList

* 应用不会随机访问数据。因为如果你需要LinkedList中的第n个元素的时候，需要从第1个元素顺序数到第n个数据，然后读取数据。
* 应用更多的是删除和插入操作，更少的读取操作。因为LinkedList的插入和删除并不涉及重排数据，所以它要比ArrayList更快。



#### 3.高并发中的集合有哪些问题

##### 第一代线程安全集合类

Vector、Hashtable

是怎么保证线程安全的：使用synchronized修饰方法

缺点：效率低下

##### 第二代非线程安全集合类

ArrayList、HashMap（作者意识到大多情况下用不需要多线程，所以不需要考虑线程安全问题）

线程不安全、但性能好，用来替代Vector和Hashtable。

使用ArrayList和HashMap需要线程安全怎么办？

使用Collections.syschronizedList(list);Collections.syschronizedMap(m);

底层使用sysnchronized代码块锁，虽然也是锁住了所有代码，但是锁在方法里边，并所在方法外边性能可以理解为稍有提高。毕竟方法本身就要分配资源。

##### 第三代线程安全集合类

在大量并发情况下如何提高集合的效率和安全呢？**CAS和AQS**

java.util.concurrent.*里面的

ConcurrentHashMap

CopyOnWriteArrayList

CopyOnWriteArraySet

底层大都采用Lock锁（1.8的ConcurrentHashMap不使用Lock锁），保证线程安全的同时，性能也很高。

![image-20220217200456453](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220217200456453.png)

![image-20220217200520811](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220217200520811.png)



#### 4.JDK1.8的新特性有哪些

* ##### 接口的默认方法

Java8允许我们给接口添加一个非抽象的方法实现，只需要使用default关键字即可，这个特征又叫做扩展方法

```java
interface Formula{
    double calculate(int a);
    
    // Formula接口的实现类可以直接使用sqrt方法，而不用实现
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```

* ##### Lambda表达式

老本版的Java如何排序字符串：只需要给静态方法Collections.sort传入一个List对象以及一个比较器来按指定顺序排列。

```java 
List<String> names = Arrays.asList("peterF", "anna", "mike", "xenia");
Collections.sort(names, new Comparator<String>(){
    @Override
    public int compare(String a, String b){
        return b.compareTo(a);
    }
})
```

在Java8中可以直接使用lambda表达式进行操作

```java 
Collections.sort(names, (String a, String b) -> (return b.compareTo(a)));
//或
Collections.sort(names, (a,b) -> b.compareTo(a));
```

* ##### 函数式接口

Lambda表达式是如何在Java的类型系统中表示的呢？每一个lambda表达式都对应一个类型，通常是接口类型。而“函数式接口“是指仅仅包含一个抽象方法的接口，每一个类型的lambda表达式都会被匹配到这个抽象方法。因为默认方法不算抽象方法，送一你也可以给你的函数式接口添加默认方法。

我们可以将lambda表达式当作任意只包含一个抽象方法的接口类型，确保你的接口一定达到这个要求，你只需要给你的接口添加@FunctionalInterface注解，编译器如果发现你标注了这个注解的接口有多于一个抽象方法时就会报错。

```java
@FuncationalInterface // 没有加这个注解，也能正确执行
interface Converter<F, T>{
    T convert(F from);
}
Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
Integer converted = converter.convert("123");
```

 

... 还有很多相关的函数式接口ing





#### 5.Java的抽象类和接口有什么区别

* 相同点

  * 不能被实例化
  * 可以将抽象类和接口类型作为应用类型
  * 一个类如果继承了某个抽象类或实现了某个接口都需要对其中的抽象方法全部进行实现，否则该类仍需要被声明为抽象类

* 不同点

  对抽象类而言

  * 抽象类可以定义构造器
  * 可以有具体方法
  * 抽象类中的成员可以是private、默认、protected、public
  * 可以定义成员变量
  * 有抽象方法的必定是抽象类，但抽象类未必要有抽象方法
  * 可以包含静态方法
  * 一个类只能继承一个抽象类

  对接口而言

  * 接口中不能定义构造器
  * 方法全部是抽象方法
  * 接口中的成员全部都是public的
  * 接口中定义的成员变量实际上是常量
  * 接口中不能有静态方法
  * 一个类可以实现多个接口。

**抽象类针对某个概念，比如动物；接口针对某个特征，比如Flyable(会飞的)，Runnable**



#### 6.hashCode和equals如何使用（就是在问底层源码HashMap的实现）

hashCode()源自Java.lang.Object，该方法用于获取给定对象的唯一整数（散列码）。当这个对象需要存储在哈希表这样的数据结构时，这个整数用于确定桶的位置。默认情况下，对象的hashCode()方法返回对象所在内存地址的整数表示。hashCode()是HashTable、HashMap和HashSet使用的。默认的，Object类的hashCode()方法返回这个对象存储的内存地址的编号。

equals()源自java.lang.Object，该方法用来简单验证两个对象的相等性。Object类中定义的默认实现**只检查两个对象的对象引用**，以验证它们的相等性。通过重写该方法，可以自定义验证对象相等新的规则。如果你使用ORM处理一些对象的话，要确保hashCode()和equals()对象中使用getter和setter而不是直接引用成员变量。

hash散列算法，使得在hash表中查找一个记录速度标为O(1)。每个记录都有自己的hashCode，散列算法按照hashCode把记录放在合适的位置。在查找一个记录，首先通过hashCode快速定位记录的位置，然后再通过equals来比较是否相等。如果hashCode没找到，则不equals，说明元素不存在于哈希表中；即使找到了，也只需要执行hashCode相同的几个元素的equals，如果不equals，还是不存在哈希表中。

![image-20220217204833843](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220217204833843.png)

#### 7.Java代理的几种实现方式

* ##### 静态代理：

  只能静态的代理某些类或方法，不推荐使用，功能比较弱，但编码简单。

* ##### 动态代理包含Proxy和CGLIB动态代理（涉及到Spring AOP源码）

  #### **Proxy代理是JDK内置的动态代理**

  特点：面向接口的、不需要导入第三方依赖的动态代理，可以对多个不同的接口进行增强，**通过反射**读取注解时，只能读取到接口上的注解。

  原理：面向接口，只能对实现类在实现接口中定义的方法进行增强。

  ```java
  #JDK动态代理demo
  public interface UserService{
      String getName(int id);
  }
  
  public class UserServiceImpl implements UserService{
      @Override
      public String getName(int id){
          System.out.println("------getName-------");
          return "riemana";
      }
      
  }
      
  public class MyInvocationHandler implements InvocationHandler{
      public Object target;
      
      MyInvocationHandler(){
          super();
      }
      
      MyInvocationHandler(Object target){
          super();
          this.target = target;
      }
      
      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
          if("getName".equals(methos.getName())){
              System.out.println("------before" + method.getName() + ""-------");
              Object res = method.invoke(target, args);
              System.out.println("------after" + method.getName() + ""-------");                     
              return res;             
          } else {
              Object res = method.invoke(target, args);
              return res;     
          }
      }
      
  }
                                 
  public class ProxyPlay{
      
      public static void main(String[] args){ 
          UserService userService = new UserServiceImpl();
          InvocationHandler invocationHandler = new MyInvocationHandler(userService);
          UserService userServiceProxy = (UserService)Proxy.newProxyInstance(userService.getClass(), invocationHandler);
          System.out.println(userServiceProxy.getName());
      } 
      
  }                           
  ```

  #### CGLIB动态代理

  特点：面向父类的动态代理，需要导入第三方依赖ASM框架。比Proxy使用起来更简单，效率更高。

  原理：面向父类、底层通过子类继承父类并重写方法的形式实现增强。

  核心类：

  net.sf.cglib.proxy.Enhancer - 主要的增强类

  net.sf.cglib.proxy.MethodInterceptor - 主要的方法拦截类，它是Callback接口的子接口，需要用户实现。它经常被基于代理的AOP用来实现拦截(intercept)方法的调用。这个接口只定义了一个方法

  ```java
  public interface MethodInterceptor {
      
      /**
        object:代理对象
        method：拦截的方法
        args：拦截方法的参数
        proxy：原来的方法可能通过使用java.lang.reflectd.Method对象的一般反射调用，或者使用net.sf.cglib.proxy.MethodProxy对象调用。net.sf.cglib.proxy.MethodProxy通常被首选使用，因为它更快。
       
      */
      public Object intercept(Object object, java.lang.reflect.Method method, Object[] args, MethodProxy proxy) throws Throwable;
      
      
  }
  ```

  

  net.sf.cglib.proxy.MethodProxy - JDK的java.lang.reflect.Method类的代理类，可以方便的实现对源对象方法的调用，如使用：

  ​	Object o = MethodProxy.invokeSuper(proxy, args);// 虽然第一个参数是被代理对象，也不会出现死循环问题。

  ```java
  public class CglibProxy implements MethodInterceptor{
      @Override
      public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable{
           System.out.println("------before" + methodProxy.getSuperName() + ""-------");
           System.out.println(method.getName());                   
           Object res = methodProxy.invokeSuper(o, args)
           System.out.println("------after" + methodProxy.getSuperName() + ""-------");                     
           return res;  
      }
  
  }
  
  public class CglibProxyPlay{
      
      public static void main(String[] args){ 
          
          Enhancer enhancer = new Enhancer();
          enhancer.setSuperclass(UserServiceImpl.class);
          enhancer.setCallback(new CglibProxy());
          UserService target = (UserService) enhancer.create();
          target.getName(1);   
      } 
      
  }                           
  ```



#### 8.equals()和==的区别

* equals是一个方法，==是一个运算符

* ==，如果比较的对象是基本数据类型，则比较的是数值是否相等；如果比较的是引用数据类型，则比较的是对象的地址是否相等。
* equals()，用来比较两个对象的内容是否相等。equals方法不能用于基本数据类型的变量，如果没有对equals方法进行重写，则比较的是引用类型的变量所指向的对象的地址。

#### 9.java中的异常处理机制是什么

通过5个关键字实现

![image-20220217215748856](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220217215748856.png)

Java通过面向对象的方法进行异常处理，一旦方法抛出异常，系统自动根据该异常对象寻找合适的异常处理器来处理该异常，把各种不同的异常进行分类，并提供了良好的接口。在Java中，每个异常都是一个对象，它是Throwable类或其子类的实例。当一个方法出现异常后便抛出一个异常对象，该对象中包含有异常信息，调用这个对象的方法可以捕获到这个异常并可以对其进行处理。Java的异常处理是通过5个关键字来实现的try、catch、throw、throws和finally。

在Java应用中，异常的处理机制分为声明异常、抛出异常和捕获异常

##### throw和throws的区别

* 位置不同：throw在方法内部、throws在方法签名处或声明处
* 内容不同：throw+异常对象（检查异常，运行时异常）；throws+异常的类型（可以多个类型，用逗号拼接）
* 作用不同：throw用于制造异常。throws则告诉方法的调用者，这个方法中可能会出现我声明的这些异常，然后调用者对这个异常进行处理，要么自己处理要么继续向外抛出异常。



#### 10.Java重写和重载有哪些区别

方法的重载和重写都是多态的方式，区别在于前者实现的是编译时的多态性，而后者实现的是运行时的多态性。重载发生在一个类中，同名的方法如果有不同的参数列表（参数类型不同、参数个数不同或二者都不同）则视为重载；重写发生在子类与父类之间，重写要求子类被重写方法与父类被重写方法有相同的返回类型，比父类被重写方法更松的访问权限，不能比父类被重写方法声明更多的异常（里氏代换原则）。重载对返回类型没有特殊的要求。

##### 重载的规则

* 方法名一致，参数列表中参数的顺序，类型，个数不同
* 重载与方法的返回值无关，存在于父类和子类（？），同类中
* 可以抛出不同的异常，可以有不同的修饰符

##### 重写的规则

* 参数列表必须完全与被重写方法的一致，返回类型必须完全与被重写方法的返回类型一致
* 构造方法不能被重写，声明为final的方法不能被重写，声明为static的方法不能被重写，但是能够被再次声明。
* 访问权限不能比父类中被重写的方法的访问权限更低。
* 重写的方法能够抛出任何非强制异常，无论被重写的方法是否抛出异常。但是，重写的方法不能抛出新的强制性异常，或者比被重写方法声明的更广泛的强制性异常，反之则可以。



#### 11.String、StringBuffer和StringBuilder的区别及使用场景（需要看源码）（★★）

Java平台提供了两种类型的字符串：String和StringBuffer/StringBuilder，它们都可以存储和操作字符串。

区别如下：

* String是只读的字符串，这意味着String引用的字符串内容是不能被改变的

```java
String s = "abc"; # 
s = "abcd"; # "abcd"是一个新的对象，而不是在"abc"的基础上变化过来的，"abc"对象没有任何变化
```

* StringBuffer/StringBuilder表示的字符串对象可以直接进行修改，它的底层是一个数组，存满后会自动扩容
* StringBuilder是Java5引入的，它和StringBuffer(JDK1.0就有)的方法完全相同。区别在于它是在单线程环境下使用的，因为它的所有方法都没有被synchronized修饰，因此它的效率比StringBuffer要高



#### 12.怎样声明一个类不会被继承，什么场景下使用（★）

如果一个类被final修饰，则此类不可以被继承。如果一个类中的所有方法都没有重写的需要，当前类没有子类也罢，就可以使用final修饰此类。Math类



#### 13.自定义异常在生产中如何应用（★）

Java虽然提供了丰富的异常处理类，但是在项目中还会经常使用自定义异常，其主要原因是Java提供的异常类在某些情况下还是不能满足实际需求。例如以下情况

* 系统中有些错误是符合Java语法，但不符合业务逻辑
* 在分层的软件结构中，通常是在表现层统一对系统其他层次的异常进行捕获处理。





### 二、redis

#### 1.Redis线程模型有哪些，单线程为什么快？（★★★★）

##### IO模型维度的特征

IO模型使用了多路复用器，在Linux系统中使用的是EPOLL

类似netty的BOSS，WORKER使用一个EventLoopGroup(threads=1单线程)

单线程的Reactor模型，每次循环取socket中的命令然后逐一操作，可以保证socket中的指令是按顺序的，不保证不同的socket也就是客户端的命令的顺序性。

命令操作在单线程中顺序操作，没有多线程的困扰不需要锁的复杂度，在操作数据上相对来说是原子性质的。

##### 架构设计模型

自身的内存存储数据，读写操作不涉及磁盘IO

redis除了提供Value具备类型还为每种类型实现了一些操作命令，实现了计算向数据移动，而非数据向计算移动，这样在IO的成本上有一定优势

且在数据结构类型上，丰富了一些统计类属性，读写操作中，写操作会O(1)负载度更新length类属性，使得读操作也是O(1)的。



#### 2.Redis持久化机制：RDB和AOF（★★★）

##### Redis持久化

redis提供了不同级别的持久化方式：

* RDB持久化方式能够在指定的时间间隔内能对你的数据进行**全量**快照存储
* AOF持久化方式记录每次对服务器写的操作，当服务器重启的时候会重新执行这些命令来恢复原始的数据，AOF命令以redis协议追加保存每次写的操作到文件末尾。redis还能对AOF文件进行后台重写，使得AOF文件的体积不至于过大
* 如果你只希望你的数据在服务器运行的时候存在，你也可以不使用任何持久化的方式
* 你也可以同时开启两种持久化方式，在这种情况下，当redis重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整。
* 最重要的事情是了解RDB和AOF持久化方式的不同，

##### RDB的优点

* RDB是一个非常紧凑的文件，它保存了某个时间点的数据集，非常适用于数据集的备份，比如你可以在每个小时保存一下过去24小时内的数据，同时每天保存过去30天的数据，这样即使出了问题，你也可以根据需求恢复到不同版本的数据集
* RDB是一个紧凑的单一文件，很方便传送到另一个远端数据中心，非常适用于灾难恢复
* RDB在保存RDB文件时父进程唯一需要做的就是fork出一个子进程，接下来的工作全部由子进程来做，父进程不需要再做其他的IO操作，所以RDB持久化方式可以最大化redis的性能
* 与AOF相比，在恢复大数据集的时候，RDB方式会更快一些

##### RDB的缺点

* 如果你希望在redis意外停止工作（断电）的情况下丢失的数据最少的化，那么RDB不适合你。虽然你可以配置不同的save时间点（例如每个5分钟并且对数据集由100个写的操作）时，redis要完整的保存整个数据集是一个比较繁重的工作，你通常会每个5分钟或更久做一次完整的保存，万一在redis意外宕机，你可能会丢失几分钟的数据
* RDB需要经常fork子进程来保存数据集到硬盘上，当数据集比较大的时候，fork的过程是非常耗时的，可能会导致redis在一些毫秒级内不能响应客户端的请求。如果数据集巨大并且CPU性能不是很好的情况下，这种情况会持续1秒，AOF也需要fork,但是你可以调节重写日志文件的频率来提高数据集的耐久度。

##### AOF的优点

* 使用AOF会让redis更加耐久：你可以使用不同的fsync策略：无fsync，每秒fsync，每次写的时候fsync。使用默认的每秒fsync策略，redis的性能依然很好(fsync是由后台线程进行处理的，主线程会尽力处理客户端请求)，一旦出现故障，最多丢失1秒的数据
* AOF文件是一个只进行追加的日志文件，所以不需要写入seek，即使由于某些原因（磁盘空间已满，写的过程中宕机等等）未执行完整的写入命令，也可以使用redis-check-aof工具来修复这些问题
* redis可以在AOF文件体积变得过大时，自动地在后台对AOF进行重写：重写后的新AOF文件包含了恢复当前数据集所需要的最小命令集合。整个重写操作时绝对安全的，因为redis在创建新AOF文件的过程中，会继续将命令追加到现有的AOF文件里面，即使重写过程中发生宕机，现有的AOF文件也不会丢失。而且一旦新AOF文件创建完毕，redis就会从旧AOF文件切换到新AOF文件，并开始对新AOF文件进行追加操作
* AOF文件有序地保存了对数据库执行的所有写入操作，这些写入操作以redis协议的格式保存，因此AOF文件的内容非常容易被读懂，对文件进行分析也很轻松。导出AOF文件也非常简单：举个例子，你不小心执行了flushall命令，但只要AOF文件未被重写，那么只要停止服务器，移除AOF文件末尾的flushall命令，并重启redis，就可以将数据恢复到flushall执行之前的状态。

##### AOF的缺点

* 对于相同的数据集来说，AOF文件的体积通常大于RDB文件的体积
* 根据所使用的fsync策略，AOF的速度可能会慢于RDB。在一般情况下，每秒fsync的性能依然非常高，而关闭fsync可以让AOF的速度和RDB一样快，即使在高负荷之下也是如痴。不过在处理巨大的写入载入时，RDB可以提供更有保证的最大延迟时间（latency）

##### 4.X版本的整合策略

在AOF重写策略上做了优化。在重写AOF文件时，4.X版本以前时把内存数据集的操作指令落地，而新版本是把内存的数据集以rdb的形式落地。这样重写后的AOF依然追加的是日志，但是在恢复的时候先rdb再增量的日志，性能更优秀。





### 三、MySQL



### 四、多线程高并发





### 五、JVM





### 六、分布式





### 七、微服务







### 八、Spring





### 九、计算机网络



























































































