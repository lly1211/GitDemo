# Linux指令

1.jps  查看linux里面有哪些java程序在运行 （需要安装）

2.jstack + pid  查看程序里面有多少线程在运行（查哪个线程cpu占比高，以及是否有没有死锁）

3.（可以使用阿里开源的工具  arthas-boot.jar）

![image-20210114224531282](C:\Users\lly\AppData\Roaming\Typora\typora-user-images\image-20210114224531282.png)

  (thread -b 查看死锁   thread +id 查看哪个方法调用）

4.如果线上没有 arthas  可以使用top-HP找到最高占比的线程 做一个十六进制的转换交给 jstack即可



