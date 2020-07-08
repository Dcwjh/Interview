# 1. CAS(compare and swap) : 无锁（没有内核的锁），自旋锁，轻量级锁（不经过内核态）

![csa](./Image/cas.png)

ABA问题：加版本号或者加上时间戳

AtomicInteger::incrementAndGet()

```java
public final int incrementAndGet() {
    return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}
```

```java
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}
```

![compare and exchange](./Image/cmpexch.png)


最底层实现：`lock cmpxchg`指令 。比较并交换

cmpxchg也不是原子操作

硬件：lock指令在执行后面指令的时候锁定一个北桥信号
（不采用锁总线的方式）

# 2. 内存布局

markword信息：锁信息，GC信息，hashcode

- markword结构如下
![markword](./Image/hotspot.png)
 

 ![](./Image/markword.png)
 ![](./Image/neicunbuju.png)


 # 3. 锁升级过程

 ![锁升级过程](./Image/suo.png)

 jdk1.6锁升级过程就是：blue颜色

单个线程用偏向锁
 - 偏向锁：偏向于第一个访问锁的线程，如果在运行过程中，同步锁只有一个线程访问，不存在多线程争用的情况，则线程是不需要触发同步的，这种情况下，就会给线程加一个偏向锁。 
 **Mark Word中线程ID设置为当前线程ID**（这个线程就是我的）

 - 为什么会有偏向锁？
 多数synchroinzed方法，在很多情况下只有一个线程在运行。
 例如：StringBuffer中的sync方法（append）
 vector中一些sync方法


 ## 偏向锁--》自旋锁（轻量级锁）
 当有其他的线程来抢锁，锁升级
 - 自旋锁（轻量级锁）->乐观锁（线程少，处理短），占用cpu资源
 轻量级锁的目标是，减少无实际竞争情况下，使用重量级锁产生的性能消耗（包括系统调用引起的内核态与用户态切换、线程阻塞造成的线程切换等）

 JVM启动时，偏向锁会**延迟4s中启动**


## 轻量级锁什么时候升级位重量级锁
JDK1.6 之前自旋超过10次或者线程数占用cpu超过1/2，升级为重量级锁，如果太多线程自旋CPU消耗过大，不如升级为重量级锁，进入等待队列（不消耗cpu资源）

现在采用自旋自适应。有JVM决定是否升级为重量级锁


- 为什么又自旋锁还需要重量级锁
自旋锁是消耗CPU资源的， 如果锁的时间过长或者自旋锁线程多，CPU就会被大量消耗
重量级锁有等待队列，所有拿不到锁的进入等待队列，不需要消耗CPU资源

- 偏向锁是否一定比自旋锁效率高吗？
不一定，在明确知道会有多线成竞争的情况下，偏向锁会升级，肯定会涉及锁撤销。这时候直接使用自旋锁比较好
JVM启动过程，会有很多线程竞争（明确），所以默认情况启动时不打开偏向锁，过一段儿时间在打开。

## 总结
偏向锁-markword上记录当前线程指针，下次同一个线程加锁的时候，不需要争用，只需要判断线程指针是否同一个，所以，偏向锁：偏向枷锁的第一个线程。hashCode备份在线程栈上线程销毁，锁降级为无锁。

有争用-锁升级为轻量级锁-每个线程都有自己的LockRecord在自己的线程栈上，用CAS去竞争markword的LR的指针，指针指向哪个进程的LR，哪个线程就拥有锁。

JDK1.6以前，自旋锁超过十次或者线程占用cpu资源超过1/2，就会升级为重量级锁，没有获得锁的进程就进入等待队列（不消耗CPU）

 # 4. 重量级锁（synchronized）:要进入内核态拿锁



 ![](./Image/suo1.png)
  ![](./Image/suo2.png)

  # 5. volatile

  1. 线程可见性
  - MESI
  - 缓冲一致性
  2. 防止指令重排
- DCL单例
- Double Check Lock
- loadfence指令原语
- storefence指令原语
![](./Image/volatile.png)
![](./Image/volatile1.png)

底层是： lock addl

- Lock
1. 用于在多处理器中执行指令时对共享内存的独占使用。
他的作用是能够将当前处理器对应**缓存的内容刷新到内存**，并使用其他处理器对应的缓存失效（线程可见性）。另外还提供了有序的指令无法越过这个内存屏障的作用。

# 6. ReentrantLock(可重入锁)
手动上锁解锁。解锁一定要写在finally里面，确保锁的释放。

例如：
```java
Lock lock = new ReentrantLock(); 
try{
    lock.lock()
    代码逻辑；
}catch(Exception e){

} finally{
    lock.unlock()
}
```

- 特点：
1. 使用tryLock()进行舱室锁定，不管锁定与否，方法都将继续执行
2. 可以根据tryLock的返回值来判定是否锁定
3. 也可以指定tryLock的时间。由于tryLock(time)抛出异常，所以要注意unLock的解锁
4. 构造函数传参true,表示这是一个公平锁 （队列），（synchronized只有非公平锁，谁抢到是谁的）

# 7. CyclicBarrier
限流(semaphore)

- 复杂操作
1. 数据库
2. 网络
3. 文件

- 并发执行
多线线程

# 8. 读写锁（共享锁）
[共享读锁](https://github.com/bjmashibing/JUC/blob/master/src/main/java/com/mashibing/juc/c_020/T10_TestReadWriteLock.java)

# 9. semaphore信号量（线程同步）
Semaphore s = new Semaphore(2, true); //true公平，有队列


# 10. 锁的分类
- 乐观锁（cas）、悲观锁(synchronized)、自旋锁(cas)、读写锁（排他锁、共享锁）、分段锁(LongAdder, concurrentHashMap)等各自的特点和实现方式


# 11. [LcokSupport](https://github.com/bjmashibing/JUC/blob/master/src/main/java/com/mashibing/juc/c_020/T13_TestLockSupport.java)

58 面试题

Lock.notify()不释放锁
ation





# 线程和进程各自有什么区别和优劣呢？
- 进程是资源分配的最小单位，线程是程序执行的最小单位。

- 进程有自己的独立地址空间，每启动一个进程，系统就会为它分配地址空间，建立数据表来维护代码段、堆栈段和数据段，这种操作非常昂贵。而线程是共享进程中的数据的，使用相同的地址空间，因此CPU切换一个线程的花费远比进程要小很多，同时创建一个线程的开销也比进程要小很多。

- 线程之间的通信更方便，同一进程下的线程共享全局变量、静态变量等数据，而进程之间的通信需要以通信的方式（IPC)进行。不过如何处理好同步与互斥是编写多线程程序的难点。

- 但是多进程程序更健壮，多线程程序只要有一个线程死掉，整个进程也死掉了，而一个进程死掉并不会对另外一个进程造成影响，因为进程有自己独立的地址空间。

### 线程启动的三种方式
1. thread
2. Runnable
3. Lambda(Excutors.newCacheThread)



# 常见面试题
- synchronized 和ReentrantLock的区别
1. synchronized 是系统自带的，自动上锁，自动解锁。而ReentrantLock需要自己手动上锁并解锁。
2. synchronized有锁升级过程，而ReetrantLock是基于CAS实现的


