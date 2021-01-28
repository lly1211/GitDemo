# JAVA基础

#### 1.java接口和抽象类的区别

​		抽象类：被abstract方法修饰的类为抽象类   **只能被继承**，。包含抽象方法的一定是抽象类，抽象类不一定包含抽象方法。**子类必须实现父类的抽象方法**

​		抽象方法：**只有方法的声明没有方法体**，**修饰符只能是public 或 protected**

​		接口：支持多继承，只能被实现    JDK1.8前 接口方法默认是abstract（只能定义抽象方法）， JDK1.8后使用default关键字和static关键字修饰之后可以添加方法实现。

相同点：

（1）都是抽象类，不能被实例化 （2）接口的实现类或抽象类的子类都只有实现了接口或抽象类中的方法后才能实例化。

不同点：

（1）接口默认只能声明方法，除非方法增加关键字（JDK1.8后）

（2）使用接口实现间接的多继承， 一个类只能继承一个

（3）接口强调实现，抽象类强调所属关系

​	

**实际用途上**

​	抽象类作为模版，  接口作为一个标准或者表示一种能力

#### 2.什么是面向对象

​	例子: 面向过程：  泡衣服，洗涤衣服，抽湿衣服，甩干衣服。

​			面向对象： 丢进洗衣机， 选择对应模式   启动。   **把行为和功能模块化，把共性封装**  





#### 3.object里面有哪些方法？

clone  hashCode  wait  notify  getClass  equals toString

#### 4.强软弱虚

```java
 Object o = new Object();   //强引用
new Softreference<T>(o); //  O对象的 软引用
new WeakRefrence<T>(); //弱引用
new PhantomRefrence<T>();//虚引用 虚引用必须和引用队列 （ReferenceQueue）联合使用
// 弱引用例子
ReferenceQueue<LLyTestObj> referenceQueue = new ReferenceQueue<>();
		WeakReference<LLyTestObj> stringWeakReferenceA = new WeakReference<>(new LLyTestObj("我是弱引用A"),referenceQueue);
		WeakReference<LLyTestObj> stringWeakReferenceB = new WeakReference<>(new LLyTestObj("我是弱引用B"),referenceQueue);
		System.out.println("---gc回收前---");
		System.out.println(stringWeakReferenceA.get());
		System.out.println(stringWeakReferenceB.get());
		System.out.println(stringWeakReferenceA);
		System.out.println(stringWeakReferenceB);
		System.out.println("---调用gc回收---");
		System.gc();
		try{
			Thread.sleep(5000);
		}catch (InterruptedException e) {
			e.printStackTrace();
		}
		System.out.println("---gc回收后---");
		System.out.println(stringWeakReferenceA.get());
		System.out.println(stringWeakReferenceB.get());
		Reference<? extends LLyTestObj> poll  = null;
		while (( poll =referenceQueue.poll()) != null ) {
			System.out.println("appleReferenceQueue中：" + poll);
		}

```



#### 5.散列表（哈希表）

​	hashMap  hashSet

​	特点： 无序，空间换时间，通过key映射到地址（访问速度快），会碰撞（不同的key访问同一个地址） 

​	作用：提高取对象的效率

​	场景：无序，需要快速访问

​	常见哈希算法：

- 直接定址法 （取关键字自己或者他的线性函数为散列地址）

  : f(key) = key  或者 f(key) = a*key+b;  a,b为常数

- 除留余数法 (取关键字被某个小于m的p值取余， m为hash表的长度)

  ：f(key) = key%p , p<m    **最为常见**

- 数字分析法

  ：仅适用于所有关键字已知的情况

- 平方取值法（计算关键字的平方，取平方值中间几位做hash地址）

  ：随机分布的关键字

- 随机数法 （选一个随机函数 用关键字的随机函数作为hash值）

  :关键字长度不等时 使用

​	解决碰撞问题：

（场景：储存值对key哈希的时候，发现地址有值了） 

- ​	开放寻址法 ： 

  比如往后移动一个地址，如果没人占用，就用这个地址。如果超过最大长度，则可以对总长度取余。这里移动的地址是产生冲突时的增列序量

- 再哈希法:

  ​	在产生冲突之后，使用关键字的其他部分继续计算地址，如果还是有冲突，则继续使用其他部分再计算地址。这种方式的缺点是时间增加了

- 链地址法：

  **链地址法其实就是对Key通过哈希之后落在同一个地址上的值，做一个[链表]** (主要使用这一种)

- 建立一个公共溢出区：

  这种方式是建立一个公共溢出区，当地址存在冲突时，把新的地址放在公共溢出区里。



#### 6.HashCode的作用

​		在对象层面的比较或者读的时候 ，快速确定对象的存储地址，提高取对象的效率。

​		hashCode由指定的hash算法生成 所以 ObjectA，ObjectB 的hash算法一致时，name如果相同而且hash算法通过name值进行生成 那么 ObjectA.hashCode = ObjectB.hashCode  但是他们是不同对象 所以 ObjectA.equals(ObjectB) = false  

​	 所以： HashCode相同 equals不一定为true  ，但equals为true，hashCode一定相同

​	**重写hashCode的原因**

​		比如有个学生对象A  学生对象B ，他们学号都是相等的（意味着是同一个学生），但是在hashMap，hashSet这种散列结构的存储中，以Object默认的hashCode方法得到的哈希值可能不同 那么就会造成  拿对象A去Hash表中取值时可以取到，但是拿对象B去表中取值却取不到的问题。

​		重写equals 是因为默认 比较的是内存地址 如果你想比较值之类的 则需要重写

#### 7.队列和栈的区别

​	队列和栈是描述数据存取方式的概念，队列是先进先出，而堆栈是后进先出；队列和栈都可以使用数组或者链表实现

#### 8.数组和链表的区别

​	**场景**：电影院看电影坐座位

​	数组的**优点**： 查询快，随机访问性强

​				**缺点**：插入删除慢，必须有连续的内存空间，可能浪费内存，数组大小固定

​	**理解**： 数组等于   坐座位必须连续坐（连续空间）必须在电影院找十连坐，买了十张票 如果10个座位来了11个人 那么座位不够坐（大小固定），来小于十个票就浪费了（浪费内存），如果前五个位置坐了人，后面来了第六个人要坐第二位则后续的所有人必须往后挪一个座位（插入删除慢），因为座位是连续的所以知道每个人都坐哪（查询快）。

​	链表的**优点**:插入删除快，内存利用率高，大小不固定拓展灵活

​				**缺点**：必须遍历

​	**理解**： 链表等于   坐座位随便坐， 来个人多买一张票就行 也不用找连坐，但是坐的比较散  所以找人要从第一个人找起...找到第一个人才知道第二个人的位置 如此来找。 坐第二个位置的人只需要让第一个人知道他的位置即可 其他位置上的人不用动也不用管