---
说明：2018年2月2日，首次创建，读取《java并发编程实战笔记》

---

[TOC]

---

# 1.线性安全基本概念

## 1.1 线性安全的代码＝》对访问状态的进行管理＝》对共享的可变状态的管理

* 无状态的对象是线性安全的，如一般的servlet
* 共享状态是不可变的是线性安全的，如枚举类型、常亮
* 对共享可变状态的访问是受保护的，如原子操作或者加锁
* 对象的状态：一般存储于实例的状态变量（基本类型或者引用类型)或者类的静态变量中。
* 共享：状态变量可由多个线程访问
* 可变：在对象的生命周期中，对象的状态是可变的。
* 要保持状态的一致性，需要在一个原子事务中改变所有的可变状态

先验条件、后验条件、不变性条件：

1. Pre-conditions are the things that must be true before a method is called. The method tells clients "this is what I expect from you".
2. Post-conditions are the things that must be true after the method is complete. The method tells clients "this is what I promise to do for you".
3. Invariants are the things that are always true and won't change. The method tells clients "if this was true before you called me, I promise it'll still be true when I'm done".


## 1.2 线性安全定义：多线程访问对象时，无论线程的执行顺序如何，类都能执行正确的行为。如下计数器。

``` java

public class Counter{
  private int count =0;
  public void synchronized increase(){
    count++;
  }
}

```

## 1.3 竞态条件：由于多线程的不恰当的执行顺序而导致的不正确的结果（不变性条件和后验条件被破坏）

* 典型如“check－then－act"。核心是A线程在check完成，act执行之前的这段时间内，对象的状态被其它线程修改。如：A线程执行读取count（假设为9），B线程也读取count也为9，B线程执行count＋1为10，A线程执行count＋1为10。虽然调用了两次，实际只统计了1次

```java
public class UnsafeCounter implements Servlet{
  private long count =0;
  public long getCount(){
    return count;
  }
  
  public void service(ServletRequest req,ServletResponse resp){
    BigInteger i = extractFromRequest(req)
      // race condition 
      ++count;
    encodeIntoResponse(resp,i);
  }
}
```

* 延迟加载的竞态条件：

```java
//单线程的单例模式，多线性不安全。
public class UnsafeLazyInit{
  private UnsafeLazyInit lz=null;
  public UnsafeLzayInit getInstance(){
    if(lz==null){
      //A线程在创建lz之前，B线程检查的结果同样为null，会执行两次初始化
      lz = new UnsafeLazyInit();
    }
    return lz;
  }
}
```

## 1.4 解决竞态条件的思路：

* 原子操作，将改变状态的多个操作变为原子操作，如：
```java
public class UnsafeCounter implements Servlet{
  private AtomitLong count = new AtomicLong(0);
  public long getCount(){
    return count.get();
  }
  
  public void service(ServletRequest req,ServletResponse resp){
    BigInteger i = extractFromRequest(req)
      // race condition 
      //++count;
      count.incrementAndGet();
    encodeIntoResponse(resp,i);
  }
}
```

* 加锁：延迟加载的check－then－act操作加锁

```java
public class UnsafeCounter implements Servlet{
  private Long count = new Long(0);
  public long getCount(){
    return count;
  }
  
  public void service(ServletRequest req,ServletResponse resp){
    BigInteger i = extractFromRequest(req)
      //use lock to avoid race condition 
    syncronized(this){
        ++count;
    }
      //count.incrementAndGet();
    encodeIntoResponse(resp,i);
  }
}
```

# 2. 内置锁Synchronized

* synchronized 标记方法体，内置锁为对象。
* 静态方法用的class类作为锁对象。
* 多个共享可变的状态需要由同一个锁来控制访问。

# 3.简单性与性能评估--鱼与熊掌

 * 不要为了性能简单的牺牲简单性（安全性）
 * 大计算时长较长或者IO读取很慢时，不要枷锁
 * 幂操作 

# 4. 可见性

多线程操作时，读操作读取值不一定能够适时看到写操作的值。

```java

/**
 * Created by jacky on 2018/5/4.
 */
public class NoVisibility {
    private static boolean ready;
    private static int number;

    public static class ReaderThread extends Thread{
        @Override
        public void run(){
            while (!ready){
                Thread.yield();
            }
            System.out.print(number);
        }
    }
    public static void main(String args[]){
        new ReaderThread().start();
        number=42;
        ready=true;
    }
}
```

失效数据：获取的并非是最新的数据，如上例子。程序可能无法结束；可能等于0；可能等于42

非原子的64位操作：非volatile 修饰的long和double

## 4.1 volatile变量可见性

​	java内存模型规定了所有的变量都存储在主内存中。每条线程中还有自己的工作内存，线程的工作内存中保存了被该线程所使用到的变量（这些变量是从主内存中拷贝而来）。线程对变量的所有操作（读取，赋值）都必须在工作内存中进行。不同线程之间也无法直接访问对方工作内存中的变量，线程间变量值的传递均需要通过主内存来完成。

![volatile原理](http://upload-images.jianshu.io/upload_images/3985563-8a3d9a1b94b97e83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

典型用法，作为是否退出循环的标识。

```java
boolean flag;
.......
    public void run(){
    while(flage){
        .........
    }
}
```

volatile只能保证可见性，无法保证事务原子性；枷锁均可保证

volatile使用原则，当满足下列所有条件时可用：

* 对变量的写操作不依赖当前值，或者单线程操作
* 该变量不会与其它变量一起纳入到不变性当中（类似事务一致性）
* 访问变量时不需要枷锁

## 4.2 对象发布与溢出

对象发布：使对象能够在当前作用域之外使用，典型的发布为静态变量值

对象溢出：不该发布的对象被发布。典型为某个发布对象的引用变量。（深度拷贝与浅度拷贝）

安全发布可变对象常用模式：

* 静态初始化模块
* 对象应用赋值给final域（属性）
* 对象应用赋值给volatile 域
* 枷锁的域

##　4.3线性安全

线程封闭：将可变变量封装在单一线程中，典型应用数据库连接池等。

栈封闭：线程的局部变量存于栈中，对象存于局部变量中

jvm内存分配模型

![](https://images2015.cnblogs.com/blog/652544/201605/652544-20160511133545843-1824443220.png)

ThreadLocal类每个线程缓存一份本地变量，不共享，Thread关闭时本地变量对象回收。

不可变对象满足条件：

* 对象状态在对象创建后不可修改
* 对象所有域（属性）都是final类型（常量通常采用大写形式）
* 对象是正确创建的（对象没有this溢出)

java线性安全的容器

同步容器类：使用了synchronized
1.Vector
2.HashTable

并发容器：
3.ConcurrentHashMap:分段
4.CopyOnWriteArrayList：写时复制
5.CopyOnWriteArraySet：写时复制

Queue:
6.ConcurrentLinkedQueue：是使用非阻塞的方式实现的基于链接节点的无界的线程安全队列，性能非常好。
（java.util.concurrent.BlockingQueue 接口代表了线程安全的队列。）
7.ArrayBlockingQueue：基于数组的有界阻塞队列
8.LinkedBlockingQueue：基于链表的有界阻塞队列。
9.PriorityBlockingQueue：支持优先级的无界阻塞队列，即该阻塞队列中的元素可自动排序。默认情况下，元素采取自然升序排列
10.DelayQueue：一种延时获取元素的无界阻塞队列。
11.SynchronousQueue：不存储元素的阻塞队列。每个put操作必须等待一个take操作，否则不能继续添加元素。内部其实没有任何一个元素，容量是0

Deque:
(Deque接口定义了双向队列。双向队列允许在队列头和尾部进行入队出队操作。)
12.ArrayDeque:基于数组的双向非阻塞队列。
13.LinkedBlockingDeque:基于链表的双向阻塞队列。

Sorted容器：
14.ConcurrentSkipListMap：是TreeMap的线程安全版本
15.ConcurrentSkipListSet：是TreeSet的线程安全版本


## 4.1大数据副本一致性



**10.154.0.0－10.154.255.255**  10.155.0.0－10.155.225.255



