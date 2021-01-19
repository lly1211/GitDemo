# CPU cache

​	

```java
int[][] array = new int[64 * 1024][1024];

// 横向遍历  80ms
for(int i = 0; i < 64 * 1024; i ++)
    for(int j = 0; j < 1024; j ++)
        array[i][j] ++;

// 纵向遍历 2100+ms
for(int i = 0; i < 1024; i ++)
    for(int j = 0; j < 64 * 1024; j ++)
        array[j][i] ++;
```

 横向遍历的CPU cache命中率高 所以快



### CPU加载概念

​	CPU加载数据时，优先从cache中查询数据是否缓存，有则返回，无则查询内存，而对于CPU而言，cache是一个透明的部件（存在的意义是提高处理器访问内存的速率），而CPU拥有 L1,L2,L3 三级缓存，速度从快到慢，缓存大小从小到大，再往后则是内存（最慢，最大）。当CPU执行运算时，优先从L1获取，再L2,L3 如果都没有 就去内存获取，越往后 运算时间越长，所以尽量保证数据存在L1中。



![图片描述](C:\Users\wh-wisdom\Desktop\look\cache1)

### 程序对内存的访问存在一种概率上的局部特征：

- Spatial Locality：对于刚被访问的数据，其相邻的数据在将来被访问的概率高。
- Temporal Locality：对于刚被访问的数据，其本身在将来被访问的概率高。

## CPU缓存行

​	：**(cache line)**

### 概念： 

​	**cache**由多个 **cache line** 组成 ，一个  **cache line** 通常为64byte， **cache line** 是 **cache 和 Ram**   交换数据 最小的单位，CPU把内存的数据载入到 cache中时，临近共64bytes 数据放在同一个缓存行

### 伪共享

​	当多线程修改一个变量时，如果这个变量共享同一个缓存行，会相互影响性能（两个线程同时修改一个cacheLine中的字节）

​	导致并行的程序 变成 串行 （cpu的各个核对 cacheLine的争抢 导致 变量不断地失效和被读取）

```
在核心1上运行的线程想更新变量X，同时核心2上的线程想要更新变量Y，不幸的是，这两个变量在同一个缓存行中。
每个线程都要去竞争缓存行的所有权来更新变量。
如果核心1获得了所有权，缓存子系统将会使核心2中对应的缓存行失效。
当核心2获得了所有权然后执行更新操作，核心1就要使自己对应的缓存行失效。
这会来来回回的经过L3缓存，大大影响了性能。如果互相竞争的核心位于不同的插槽，就要额外横跨插槽连接，问题可能更加严重。
```

​	

```java
public final class FalseSharingNo implements Runnable {

    public final static long ITERATIONS = 500L * 1000L * 100L;
    private int arrayIndex = 0;

    private static ValuePadding[] longs;
    public FalseSharingNo(final int arrayIndex) {
        this.arrayIndex = arrayIndex;
    }

    public static void main(final String[] args) throws Exception {
        for(int i = 1; i < 10; i++){
            System.gc();
            final long start = System.currentTimeMillis();
            runTest(i);
            System.out.println(i + " Threads, duration = " + (System.currentTimeMillis() - start));
        }

    }

    private static void runTest(int NUM_THREADS) throws InterruptedException {
        Thread[] threads = new Thread[NUM_THREADS];
        longs = new ValuePadding[NUM_THREADS];
        for (int i = 0; i < longs.length; i++) {
            longs[i] = new ValuePadding();
        }
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(new FalseSharingNo(i));
        }

        for (Thread t : threads) {
            t.start();
        }

        for (Thread t : threads) {
            t.join();
        }
    }

    public void run() {
        long i = ITERATIONS + 1;
        while (0 != --i) {
            longs[arrayIndex].value = 0L;
        }
    }

    public final static class ValuePadding {
        
        //long 为8个字节   
        protected long p1, p2, p3, p4, p5, p6, p7;
        protected volatile long value = 0L;
        protected long p9, p10, p11, p12, p13, p14;
        protected long p15;
    }
    public final static class ValueNoPadding {
        // protected long p1, p2, p3, p4, p5, p6, p7;
        protected volatile long value = 0L;
        // protected long p9, p10, p11, p12, p13, p14, p15;
    }

}
```

​	

```java
// ####### 运行结果 #######
// nopadding  ms
1 Threads, duration = 372
2 Threads, duration = 1495
3 Threads, duration = 1353
4 Threads, duration = 1450
5 Threads, duration = 2927
6 Threads, duration = 3562
7 Threads, duration = 3203
8 Threads, duration = 4140
9 Threads, duration = 3147
    
// addpadding ms
1 Threads, duration = 361
2 Threads, duration = 407
3 Threads, duration = 507
4 Threads, duration = 493
5 Threads, duration = 503
6 Threads, duration = 619
7 Threads, duration = 772
8 Threads, duration = 685
9 Threads, duration = 740
    
```





代码运行后可以发现

**ValuePadding** 的速度 高于 **ValueNoPadding**

所以我们得到了 **伪共享的解决办法**

1. 字节填充：增大元素的间隔，使得不同线程存取的元素位于不同的cache line上，典型的空间换时间。**(缓存行填充)**
2. 在每个线程中创建对应元素的本地拷贝，结束后再写回全局数组。



**jdk7**

 jvm会自动优化填充的代码段 所以需要通过继承来实现

```java
abstract class AbstractPaddingLeftObject{
    protected long p1, p2, p3, p4, p5, p6,p7;// 填充
}

public class PaddingObject extends AbstractPaddingObject{
    public volatile long value = 0L;    // 实际数据
}


public class  AbstractPaddingRightObject extends PaddingObject{
    protected long  p8, p9, p10, p11, p12,p13,p14;// 填充
}
```



**jdk8**

jvm不回去自动优化填充，并提供了注解**@contended**(实验性功能，需要添加参数 ：**-XX:-RestrictContended** )

具体实现例子可以参考： **ConcurrentHashMap** 



**RingBuffer**

来源于一款优秀的开源框架 Disruptor 中的一个数据结构 **RingBuffer**。

```java
abstract class RingBufferPad {
    protected long p1, p2, p3, p4, p5, p6, p7;
}

abstract class RingBufferFields<E> extends RingBufferPad{}12345
```

使用字节填充和继承的方式来避免伪共享。