# JAVA面试题错题知识点整理

## 一、JVM

###  1、JVM内存 

- 内存区域分为  **方法区、堆区(heap)、程序计数器、JAVA方法栈（虚拟机栈）、本地方法栈(stack)**

- **方法区** 和 **堆区**是被线程共享的区域。

- **方法区** 储存了每个类的信息、常量、静态变量、编译器后的代码。

- **方法区**  中包含的都是整个程序中永远唯一的元素,如class,static变量

- 内存共享的区域在JVM运行时就会被分配，而不共享的区域则在每个线程启动时分配新的（在线程终止时被释放）。所以GC只发生在线程共享的区域。

- **heap区**  存储的全部是对象的实例（new 创建的对象和数组），每个对象包含与之对应的一个class信息（目的是为了得到操作的指令也就是方法）

- **heap区**  不存储基本的数据类型，只存储对象本身

- **stack区**  对象在heap中分配好以后，需要在stack中保存heap内存的地址，用来定位该对象的实例在heap中的位置，便于找到该实例对象

- **stack区**  分为三个部分  数据类型变量区、执行环境上下文、操作指令区

- 每个stack中的数据都是私有的，其他stack不能访问

- **stack区**  由编译器自动分配 也会存放 局部变量、方法的参数值。

  

### 2、JVM参数

```
-Xmx10240m：代表最大堆区
 -Xms10240m：代表最小堆区
 -Xmn5120m：代表新生代
 -XXSurvivorRatio=3：代表Eden:Survivor = 3 
 根据Generation-Collection算法(目前大部分JVM采用的算法)，一般根据对象的生存周期将堆内存分为若干不同的区域，一般情况将新生代分为Eden ，两块Survivor；  计算Survivor大小， Eden:Survivor = 3，总大小为5120,3x+x+x=5120  x=1024
新生代大部分要回收，采用Copying算法，快！(复制算法需要留出一块单独的内存空间来以备垃圾回收时复制对象使用，所以将新生代分为eden区和两个survivor区，每次使用eden和一个survivor区，另一个survivor作为备用的对象复制内存区。)
老年代 大部分不需要回收，采用Mark-Compact算法(标记整理和标记清除算法。)
```



## 二、Statement

- ​	·**PerparedStatement**是预编译的,效率比statement高，可以防止sql注入，适用于batch。







## 三、Spring

**a)**  **传播行为定义了被调用方法的事务边界。**

| **传播行为**                  | **意义**                                                     |
| ----------------------------- | ------------------------------------------------------------ |
| **PROPERGATION_MANDATORY**    | **表示方法必须运行在一个事务中，如果当前事务不存在，就抛出异常** |
| **PROPAGATION_NESTED**        | **表示如果当前事务存在，则方法应该运行在一个嵌套事务中。否则，它看起来和PROPAGATION_REQUIRED** **看起来没什么俩样** |
| **PROPAGATION_NEVER**         | **表示方法不能运行在一个事务中，否则抛出异常**               |
| **PROPAGATION_NOT_SUPPORTED** | **表示方法不能运行在一个事务中，如果当前存在一个事务，则该方法将被挂起** |
| **PROPAGATION_REQUIRED**      | **表示当前方法必须运行在一个事务中，如果当前存在一个事务，那么该方法运行在这个事务中，否则，将创建一个新的事务** |
| **PROPAGATION_REQUIRES_NEW**  | **表示当前方法必须运行在自己的事务中，如果当前存在一个事务，那么这个事务将在该方法运行期间被挂起** |
| **PROPAGATION_SUPPORTS**      | **表示当前方法不需要运行在一个是事务中，但如果有一个事务已经存在，该方法也可以运行在这个事务中** |

**b)**  **隔离级别**

**在操作数据时可能带来 3** **个副作用，分别是脏读、不可重复读、幻读。为了避免这 3** **中副作用的发生，在标准的 SQL** **语句中定义了 4**种隔离级别，分别是未提交读、已提交读、可重复读、可序列化。而在 spring **事务中提供了 5** **种隔离级别来对应在 SQL** **中定义的 4**种隔离级别，如下：

| **隔离级别**                   | **意义**                                                     |
| ------------------------------ | ------------------------------------------------------------ |
| **ISOLATION_DEFAULT**          | **使用后端数据库默认的隔离级别**                             |
| **ISOLATION_READ_UNCOMMITTED** | **允许读取未提交的数据（对应未提交读），可能导致脏读、不可重复读、幻读** |
| **ISOLATION_READ_COMMITTED**   | **允许在一个事务中读取另一个已经提交的事务中的数据（对应已提交读）。可以避免脏读，但是无法避免不可重复读和幻读** |
| **ISOLATION_REPEATABLE_READ**  | **一个事务不可能更新由另一个事务修改但尚未提交（回滚）的数据（对应可重复读）。可以避免脏读和不可重复读，但无法避免幻读** |
| **ISOLATION_SERIALIZABLE**     | **这种隔离级别是所有的事务都在一个执行队列中，依次顺序执行，而不是并行（对应可序列化）。可以避免脏读、不可重复读、幻读。但是这种隔离级别效率很低，因此，除非必须，否则不建议使用。** |

 

**c)**  **只读**

**如果在一个事务中所有关于数据库的操作都是只读的，也就是说，这些操作只读取数据库中的数据，而并不更新数据，那么应将事务设为只读模式（ READ_ONLY_MARKER** **） ,** **这样更有利于数据库进行优化** **。**

**因为只读的优化措施是事务启动后由数据库实施的，因此，只有将那些具有可能启动新事务的传播行为(PROPAGATION_NESTED** **、 PROPAGATION_REQUIRED** **、 PROPAGATION_REQUIRED_NEW)** **的方法的事务标记成只读才有意义。**

**如果使用 Hibernate** **作为持久化机制，那么将事务标记为只读后，会将 Hibernate** **的 flush** **模式设置为 FLUSH_NEVER,** **以告诉 Hibernate** **避免和数据库之间进行不必要的同步，并将所有更新延迟到事务结束。**

**d)**  **事务超时**

**如果一个事务长时间运行，这时为了尽量避免浪费系统资源，应为这个事务设置一个有效时间，使其等待数秒后自动回滚。与设**

**置“只读”属性一样，事务有效属性也需要给那些具有可能启动新事物的传播行为的方法的事务标记成只读才有意义。**





## 四、Servlet

- servlet处于服务器进程中，通过多线程的方式运行service，实例一般不会销毁，服务于多个请求。
- **cgi**（common Gateway interface   通用网关接口）对每个请求都产生新的进程，服务完成后销毁。
- doGet/doPost 在httpServlet中实现
- Servlet生命周期器

```
(1)加载: 容器通过类加载器使用servlet类对应的文件 加载servlet
(2)创建：通过调用servlet构造函数创建一个servlet对象  
(3)初始化：调用init方法初始化     （Servlet规范保证调用init方法前不会处理任何请求 ）
(4)处理客户端请求：每当有请求，容器创建一个线程来处理客户请求
(5)卸载:调用的destory方法让servlet释放自己的资源
```

## 五、JDBC

- JDBC步骤有 1.加载JDBC驱动 

  ```java
  //加载驱动的方法
  1.Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
  2.DriverManager.registerDriver(new com.mysql.jdbc.Driver());
  3.System.setProperty("jdbc.drivers", "com.mysql.jdbc.Driver");
  ```

  -->2.提供JDBC连接URL-->3.创建数据库连接(DriverManager.getConnection(user,password)) 

  -->4.创建一个statement（）

  ```xml
  <li style="vertical-align: baseline;">
      执行静态SQL语句。通常通过Statement实例实现。
  </li><li style="vertical-align: baseline;">
      执行动态SQL语句。通常通过PreparedStatement实例实现。
  </li><li style="vertical-align: baseline;">
      执行数据库存储过程。通常通过CallableStatement实例实现。
  ```

  

## 六、JUC

- sleep方法没有释放锁，wait方法释放了锁

- sleep是Thread的方法，导致线程暂停指定时间，给其他线程执行的机会，但仍然保持监控状态。

- Thread.sleep(0)的作用是“触发操作系统立刻重新进行一次CPU竞争”。

- 线程的六种状态 ：

  1.new  尚未启动的线程状态

  2.runnable   运行状态  （CPU层面分为 就绪状态和运行状态）

  3.bolcked  阻塞状态   等待锁

  4.waiting 等待状态  (wait, park , join)

  5.timed_waiting    指定等待时间的等待状态  (sleep,wait,join,parkNanos,parkUntil)

  6.terminated  结束，终止状态



## 七、Throwable

- 异常的捕获顺序是  先捕获子类 再捕获父类