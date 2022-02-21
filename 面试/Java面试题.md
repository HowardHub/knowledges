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

Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSIC语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

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



#### 3.Redis的过期键有哪些删除策略（★★★）

##### 过期精度

在2.4及以前版本，过期时间可能不是十分准确，有0-1s的误差。从2.6起，过期时间误差缩小到0-1ms

##### 过期和持久

键的过期时间使用unix时间戳存储（这是一个**绝对时间**，从2.6开始以ms为单位）。这意味着即使redis实例不可用，时间也是一直在流逝的。

要向过期的工作处理好，计算机必须采用稳定的时间。如果将RDB文件在两台时钟不同步的电脑间同步，将会发生“所有的键装载时就会过期”。

即使正在运行的实例也会检查计算机的时钟，例如设置了一个key的有效期时1000秒，然后设置计算机为未来2000秒，这时key会立即失效，而不是等1000秒之后。

##### Redis如何淘汰过期的keys

redis keys的过期有两种方式：被动和主动方式。

当一些客户端尝试访问它时，key会被发现并主动的过期。

这样是不够的，因为有些过期的keys，永远不会被访问。无论如何，这些keys应该过期，所以定时随机测试设置keys的过期时间。所有这些过期的keys将会从密钥空间删除。

具体就是redis每秒10次做的事情：

​	1.测试随机的20个keys进行相关过期检测。

​	2.删除所有已经过期的keys。

​	3.如果有多于25%的keys过期，重复步骤1

这是一个平凡的概率算法。我们不断重复过期检测，直到过期的keys的百分比低于25%，这意味着在给定的任何时刻，最多会清除1/4的过期keys。

##### 在复制AOF文件时如何处理过期的keys

为了获得正确的行为而不牺牲一致性，当一个key过期，DEL将会随着AOF文件一起合成到所有附加的slaves。在master实例中，这种方法是集中的，并且不存在一致性错误的机会。

然后，当slaves连接到master时，不会独立删除过期keys（会等到master执行DEL命令），过期keys仍然会在数据集里面存在，所以当slave当选为master时淘汰keys会独立执行，然后成为master。



#### 4.Redis缓存如何回收（★★）

**删除是针对key过期的情况，而回收是针对内存空间不足的情况。**

##### 回收策略

* noeviction：返回错误，当前内存限制达到并且客户端尝试执行会让更多内存被使用的命令（大部分是写指令，但DEL）

* allkeys-lru：尝试回收最少使用的键（LRU），使得新添加的数据有空间存放。
* volatile-lru：尝试回收最少使用的键（LRU），但仅限于在过期集合的键，使得新添加的数据有空间存放。
* allkeys-random：回收随机的键，使得新添加的数据有空间存放。
* volatile-random：回收随机的键，但仅限于在过期集合的键，使得新添加的数据有空间存放。
* volatile-ttl：回收在过期集合的键，并优先回收存活时间（TTL）较短的键，使得新添加的数据有空间存放。
* volatile-lfu：从所有配置了过期时间的键中回收使用频率最少的键。
* allkeys-lfu：从所有键中回收使用频率最少的键。

如果redis中没有设置任何过期的keys，那么volatile和allkeys算法就没有区别。

如果没有键满足回收的前提条件，策略volatile-lru，volatile-random以及volatile-ttl就和noeviction一样。

在redis运行时，监控缓存命中率和失效的次数，通过redis info命令输出来调优，从而调整相关策略。

##### 一般的经验规则：

* 使用allkeys-lru策略：当你希望你的请求符合一个幂定律分布，即你希望部分的子集元素将比其它元素被访问的更多。如果不确定选择什么，那就用该策略。
* 使用allkeys-random：如果你是循环访问，那么所有的键被连续扫描，或者你希望请求分布正常（所有元素被访问的概率相等）。
* 使用volatile-ttl：如果你想要通过创建缓存对象时设置TTL值，来决定哪些对象应该被过期。

allkeys-lru和allkeys-random策略对于当你想要单一的实例实现缓存及持久化一些键时很有用。不过一般运行两个实例时解决这个问题的更好方法。

为了键设置过期时间也是消耗内存的，所以使用allkeys-lru这种策略更加高效，因为当内存有压力时，没有必要为键去设置过期时间。

##### 回收进行时如何工作的，这很重要

* 一个客户端运行了新的命令，添加了新的数据
* redis检查内存的使用情况，如果大于maxmemory的限制，则根据设定好的策略进行回收
* 一个新的命令被执行，等等
* 所以我们不断地穿越内存限制的边界，通过不断达到边界然后不断地回收到边界以下

如果一个命令的结果导致大量内存被使用（例如很大的集合的交集保存到一个新的键），不用多久内存限制就i会被这个内存使用量超越。



#### 5.Redis集群方案有哪些（★★★）

##### 常见集群分类

* 主从复制集群，也叫镜像集群
* 分片集群

##### redis集群分类

* 主从复制集群，手动切换
* 带哨兵的HA的主从复制集群，会自动切换
* 客户端实现路由索引的分片集群
* 使用中间件代理层的分片集群
* redis自身实现的cluster分片集群



#### 6.Redis事务是怎么实现的（★★★）

#####   MULTI、EXEC、DISCARD、WATCH、UNWATCH是Redis事务相关的命令。事务可以一次执行多个命令，并且带有以下两个重要保证：

事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行过程中，不会被其他客户端发送来的命令请求所打断。

事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。

##### EXEC命令负责触发并执行事务中的所有命令：

如果客户端在使用MULTI开启了一个事务之后，却因为断线而没有成功执行EXEC，那么事务中的所有命令都不会被执行。

另一方面，如果客户端成功在开启事务之后执行EXEC，那么事务中的所有命令都会被执行。

##### 当使用AOF方式做持久化的时候，Redis会使用单个write(2)命令将事务写入到磁盘中。

```txt
然而，如果redis服务器因为某些原因被管理员杀死，或者遇上某种硬件故障，那么可能只有部分事务命令会被成功写入磁盘中。

如果redis在重新启动时发现AOF文件出了这样的问题，那么它会退出，并汇报一个错误。

使用redis-check-aof程序可以修复这一问题：它会移除AOF文件中不完整事务的信息，确保服务器可以顺利启动。

从2.2版本开始，redis还可以通过乐观锁实现CAS操作，具体信息请参考文档的后半部分。
```

##### 事务中的错误

使用事务时可能会遇上以下两种错误：

事务在执行EXEC之前，入队的命令可能会出错。比如，命令可能会产生语法错误（参数数量错误，参数名错误等等），或者其他更严重的错误，比如内存不足（如果服务器使用maxmemory设置了最大内存限制的话）。

命令可能在EXEC调用之后失败。举个例子，事务中的命令可能处理了错误类型的键，比如将列表命令用在了字符串键上面，诸如此类。

对于发生了在EXEC执行之前的错误，客户端以前的做法是检查命令入队所得的返回值：如果命令入队时返回QUEUED，那么入队成功；否则，就是入队失败。如果有命令在入队时失败，那么大部分客户端都会停止并取消这个事务。

不过从2.6.5开始，服务器会对命令入队失败的情况进行记录，并在客户端调用EXEC命令时，拒绝执行并自动放弃这个事务。

在2.6.5以前，redis只执行事务那些入队成功的命令，而忽略那些入队失败的命令。而新的处理方式则使得在流水线中包含事务变得简单，因为发送事务和读取事务的回复都只需要和服务器进行一次通讯。

至于那些在EXEC命令执行之后所产生的错误，并没有对它们进行特别处理：即使事务中的某个/某些命令在执行时产生了错误，事务中的其他命令仍然在继续执行。

##### 为什么redis不支持回滚（roll back）

如果你有使用关系式数据库的经验，那么“redis在事务失败时不进行回滚，而是继续执行余下的命令”这种做法可能会让你觉得有点奇怪。

以下是这种做法的优点：

* redis命令只会因为错误的语法而失败（并且这些问题不能在入队时发现），或是命令用在了错误类型的键上面：这也就是说，从实用性的角度来说，失败的命令是由编程造成的，而这些错误应该在开发的过程中被发现，而应该出现在生产环境中。
* 因为不需要对回滚进行支持，所以redis的内部可以保持简单且快速。

有种观点认为redis处理事务的做法会产生bug，然后需要注意的是，在通常情况下，回滚并不能解决编程错误带来的问题。举个例子，如果你本来想通过INCR命令将键的值加上1，却不小心加上了2，又或者对错误类型的键执行了INCR，回滚是没有办法处理这种情况的。



#### 7.Redis主从复制的原理（★★）

##### 主从复制机制

```txt
当一个master 实例和一个slave实例连接正常时，master会发送一连串的命令流来保持对slave的更新，以便于将自身数据集的改变复制给slave，包括客户端的写入、key的过期或被逐出等等。

当master和slave之间的连接断开之后，因为网络问题、或者是主从意识到连接超时，slave重新连接上master并会尝试进行部分重同步：这意味着它会尝试只获取在断开连接期间内丢失的命令流。

当无法进行部分重同步时，slave会请求进行全量重同步。这会涉及到一个更复杂的过程，例如master需要创新所有数据的快照，将之发送给slave，之后在数据集更改时持续发送命令流到slave。
```

##### 主从复制的关注点

```txt
redis使用异步复制，salve和master之间异步地确认处理的数据量

一个master可以拥有多个slave

salve可以接受其他slave的连接。除了多个slave可以连接到同一个master之外，salve之间也可以像层叠状的结构连接到其他slave。
```

##### Redis复制功能是如何工作的





##### 无需磁盘参与的复制

正常情况下，一个全量重同步要求在磁盘上创建一个RDB文件，然后将它从磁盘加载进内存，然后slave以此进行数据同步。

如果磁盘性能很低的化，这对master是一个压力很大的操作。redis2.8.18是第一个支持无磁盘复制的版本。在次设置中，子进程直接发送RDB文件给slave，无需使用磁盘作为中间存储介质。



#### 8.缓存雪崩、缓存穿透、缓存击穿在实际中如何处理（★★★★★）

##### 缓存穿透

指擦汗寻一个一定不存在的数据，由于缓存时不命中时被动写的，并且出于容错考虑，如果从存储层查不到则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。在流量大时，可能导致DB挂掉。要是有人利用不存在的key频繁攻击我们的应用，这就是漏洞。

##### 解决方案

使用布隆过滤器，将所有可能存在的数据哈希到一个足够大的bitmap中，一个一定不存在的数据会被这个bitmap拦截掉，从而避免了对底层存储系统的查询压力。另外还有一个更粗暴的方法：如果一个查询返回的数据为空，仍然把这个空结果进行缓存，但它的过期时间会很短，最长不超过5分钟。



##### 缓存击穿

对于一些设置了过期时间的key，如果这些key可能会在某些时间点被超高并发地访问，是一种非常“热点”地数据。这个时候，需要考虑一个问题：缓存被“击穿”，这个和缓存雪崩地区别在于这里针对某一key缓存，而雪崩针对很多key。缓存在某个时间点过期地时候，恰好在这个时间点对这个key有大量地并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把DB压垮。

##### 解决方案

缓存失效时的雪崩效应对底层系统的冲击非常可怕。大多数系统设计者考虑用加锁或者队列的方式保证缓存的单线程写，从而避免失效时大量的并发请求落到DB上。我们可以将缓存失效时间分散开，比如在原有失效时间的基础上增加一个随机值（1-5分钟随机），这样每一个缓存的过期时间的重复率就会降低，很难引发集体失效的事件。



##### 缓存雪崩

指我们设置缓存采用了相同的过期时间，导致缓存在某一个时刻同时失效，请求全部转发到DB，导致DB瞬间压力过大雪崩。

##### 解决方案

1.使用互斥锁(mutex key)

即缓存失效的时候（判断拿去来的值为空），不是立即去load db，而是先使用缓存工具的某些带成功操作返回值的操作（比如redis的setnx）去set一个mutex key，当操作返回成功时，再进行load db操作并回设缓存；否则，就重试整个get缓存的方法。setnx是set if not exists的缩写，也就是只有不存在的时候才设置，可以利用它来实现锁的效果。

2.“提前”使用互斥锁

在value内部设置1个超时值(timeout 1)，timeout 1比实际的memcache timeout(timeout2)小。当从cache读取到timeout1发现它已经过期时，马上延长timeout1并重新设置到cache，然后再从数据库加载数据并设置到cache中。

3.“永远不过期”，包含两层意思

(1)：从redis上看，确实没有设置过期时间，这就保证了，不会出现热点key过期问题。即物理不过期

(2)：从功能上看，如果不过期，那不就成了静态的嘛？所以我们把过期时间存在key对应的value里，如果发现要过期了，通过一个后台的异步线程进行缓存的构建，也就是“逻辑”过期。

从实战看，这种方法性能非常好，唯一不足的就是构建缓存的时候，其余线程可能访问的时老数据，但对于一般的业务来讲是可以忍受的。



##### 总结

* 穿透：缓存不存在、数据库不存在、高并发、少量key
* 击穿：缓存不存在、数据库存在、高并发、少量key
* 雪崩：缓存不存在、数据库存在、高并发、大量key

##### 终极方案：

即访问时发现缓存不存在，应该去redis上的另外一个集合中抢锁，只有抢到锁的请求，才能访问DB，此时复杂度为O(1),并回设缓存（复杂度也是O(1)的）；其他的请求因为抢不到锁，而进入等待，然后再去缓存中查询数据。只要DB没事，架构就是稳定的。该解决方案适用于“预热问题”，冷数据受到大量访问时。



### 三、MySQL

#### 1.海量数据下，如何根据执行计划调优SQL

explain最重要的3个字段：type、key、extra

答：先查询执行计划，重点看type,key,extra这三个字段，根据这三个字段的具体值来判断当前的执行效果，然后来进行调整。比如，我在分析dangjian项目的某个表的查询的执行计划时，发现key没有值，然后我就加了一个索引字段，使得key有值了。









#### 2.MySQL索引体系如何应对海量数据存储

##### MySQL的索引底层为何使用B+树？

1.索引数据和实际数据都存储在磁盘上

2.当进行查询的时候，需要将磁盘中的数据读取到内存中

3.分块进行数据读取（也的整数倍）

4.使用什么样的数据结构存储（格式：K-V）：hash表、树（二叉树、BST、AVL、红黑树）、B+树

![image-20220220141338464](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220141338464.png)





innodb_page_size

B-Tree的结构。键值即索引列值

![image-20220220134716016](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220134716016.png)

3层存满的记录数：16 x 16 x 16



B+Tree的结构

![image-20220220135532870](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220135532870.png)

假设指针加key一共占10kb，因为每页16kb，所以3层存满的记录数：1600 x 1600 x 16 约等于 4千万条



##### MySQL的索引一般是几层的B+树？

答：一般情况下，3~4层的B+树足以支持千万级别的数据量存储。





#### 3.海量数据下，如何设计性能优良的MySQL索引

![image-20220220142033101](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220142033101.png)



#### 4.MySQL的聚簇索引和非聚簇索引解析

![image-20220220145053737](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220145053737.png)









#### 5.MySQL索引面试必问名词：回表、索引覆盖、最左匹配



![image-20220220151501493](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220151501493.png)



![image-20220220151723618](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220151723618.png)

如何解决回表：把要查询的所有字段都放到索引的叶子节点中，就可以防止回表



#### 6.如何针对特定SQL场景，来进行索引的调优

例如：city表有个city_name字段 varchar(520)，如何针对city_name建立索引？

答：采用截取的方式，根据

```mysql
select count(*) as cnt left(city_name,1) as pref 
from city 
group by pref 
order by cnt desc 
limit 10;
```

中的pref从小到大（1、2、3...）来确定截取长度最适合的值，达到一个值后，pref不会发生改变。

然后建立索引

```mysql
alter table city add key(city_name(7)); # 截取city_name的前7位建立索引
```



#### 7.海量数据下，必知必会的MySQL分布式集群

##### 什么是MySQL的主从复制、读写分离、分库分表？读写分离和分库分表基于主从复制

主从复制涉及到binlog、IO Thread、SQL Thread、reloy log 

![image-20220220170040690](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220170040690.png)

master写binlog是顺序的、IO Thread写relay log是顺序的，他们都是以append的方式写文件；但SQL thread写db文件是随机的，因为它要找到具体的数据（指不定哪一条）然后再写入。











#### 8.海量数据下，如何保证不同事务的数据一致性

原子性、隔离性和持久性保证了一致性

![image-20220220202822916](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220202822916.png)







#### 9.MySQL事务的ACID的底层实现机制全解析

![image-20220220201240783](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220201240783.png)



![image-20220220202016699](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220202016699.png)

![image-20220220202633532](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220202633532.png)

![image-20220220202237860](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220202237860.png)



#### 10.MySQL海量数据并发访问的核心机制——MVCC





#### 11.数据并发访问的MySQL幻读问题及解决方案





#### 12.如何监测并调整MySQL的锁状态





#### 13.如果完美回答MySQL的调优问题







### 四、多线程高并发







### 五、JVM









### 六、分布式









### 七、微服务







### 八、Spring

##### 1.使用spring的优势?

* 通过ID、AOP和伤处样板式代码来简化企业级Java开发
* 低侵入设计，代码的污染极低
* 独立于各种应用服务器，基于spring的应用可以实现write once，run anywhere
* IoC容器降低了业务对象替换的复杂性，提高了组件之间的解耦
* AOP支持允许将一些通用任务如安全、事务、日志等进行集中式处理
* ORM和DAO提供了与第三方持久层框架的良好整合，简化了底层数据库访问
* 高度开放，内部组件可以任意组合使用

##### 2.什么是bean的自动装配，它有哪些方式？

![image-20220220212438233](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220212438233.png)

##### 3.ACID是靠什么来保证的？

![image-20220220213220034](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220213220034.png)

##### 4.BeanFactory和ApplicationContext有什么区别

![image-20220220213648914](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220213648914.png)

#### 5.SpringBoot自动装配的原理是什么？（★★★★）





#### 6.SpringMVC工作流程是什么？

```java
org.springframework.web.servlet.DispatcherServlet
```

![image-20220220215153424](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220215153424.png)



建议看DispatcherServlet的源码

![image-20220220215557809](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220215557809.png)

##### 7.SpringMVC的9大组件

![image-20220220220727737](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220220727737.png)



##### 8.Spring的核心是什么？

![image-20220220220835891](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220220835891.png)



#### 9.Spring的事务传播机制是什么？（★★★★）

做个测试：A.a()，B.b()，其中a方法内部调用了b方法

![image-20220220221650581](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220221650581.png)



##### 10.Spring中的单例bean是线程安全的嘛？

![image-20220220221938337](https://gitee.com/wmbyy/typora_pictures/raw/master/pictures/image-20220220221938337.png)











### 九、计算机网络



























































































