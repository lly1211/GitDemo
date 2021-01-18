# JUC锁

### 1.ReenTrantLock

​	在部分场合可以替代synchronized

```java
ReenTrantLokc lock =  new ReenTrantLock();
lock.lock();
try{
    //业务代码
}finally{
    lock.unlock();
}
```

- 可以是公平锁 

   ```java
  new ReenTrantLock(true);
   ```

  

  - 公平锁：线程排队去抢锁
  - 非公平锁：线程到了就插队去抢锁

- 可以被打断的上锁过程

  - tryLock() 尝试去上锁
  - lockInterruptibly();  打断上锁过程

- 锁的队列可以指定任意数量

  - 区分不同条件下的等待队列