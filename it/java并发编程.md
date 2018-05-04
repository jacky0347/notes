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

**10.154.0.0－10.154.255.255**  10.155.0.0－10.155.225.255



