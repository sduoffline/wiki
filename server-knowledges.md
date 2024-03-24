---
title: 后端面试八股速记
description: 
published: true
date: 2024-03-24T02:44:57.503Z
tags: 
editor: markdown
dateCreated: 2024-03-24T02:44:57.503Z
---

### 进程间的通讯方式

信号量

共享内存

管道

消息队列

socket

信号



### 创建线程的方法

- 继承Thread类，实现run方法。（单继承特性，有局限）
- 实现Runnable接口，实现一个执行目标类target对象，然后创建新的thread线程把target填进去。
- 实现Callable接口。实现call方法。有返回值。
- 线程池

实现Runnable VS  继承Thread

- a.覆写Runnable接口实现多线程可以避免单继承局限
- b.实现Runnable()可以更好的体现共享的概念

### 死锁的条件

互斥条件

请求并保持

非剥夺

循环等待

### 单例双检锁

```java
private volatile  static DoubleCheckSingleton instance;//volatile防止指令重排

    //私有的构造方法
    private DoubleCheckSingleton() {}

    public static DoubleCheckSingleton getInstance(){

        if(instance==null){ //第一层检查,提高效率

            synchronized (DoubleCheckSingleton.class){

                if(instance==null){ //第二层检查，放在同步代码块中，防止出现多次实例化

                    instance=new DoubleCheckSingleton();

                }

            }

        }
        return instance;

    }
```

### 线程间通信方式

 volatile 防止指令重排，告知所有对于变量的访问都需要从内存中获取

synchronized临界区。通过synchronized(Object) 来进行同步，通过wait和notify和notifyAll来通信

reentrantLock方式，使用lock加锁，通过signal、signalAll和await来进行通信

信号量

管道

### 线程池有什么作用？

- 1.提高响应速度，如果线程池有空闲线程的话，可以直接复用这个线程执行任务，而不用去创建。
- 2.减少资源占用，每次都创建线程都需要申请资源，而使用线程池可以复用已创建的线程。
- 3.可以控制并发数，可以通过设置线程池的最大线程数量来控制最大并发数，如果每次都是创建新线程，来了大量的请求，可能会因为创建的线程过多，造成内存溢出。
- 4.更加方便来管理线程资源。





### Redis性能高的原因

基于内存

单线程（没有上下文切换，不用考虑锁）

数据结构专门设计的（简单动态字符串）

多路复用IO模型



### http的缓存方式

强制缓存：cache-control和expires字段

协商缓存：etag字段和if-modified-since+last-modified字段



### http1.1性能

长连接：防止重复三次握手

支持管道传输：解决了发送方的队头阻塞，没有解决响应的队头阻塞

队头阻塞：存在队头阻塞问题





### http2的特点

使用双向**流传输**

消息头压缩

单TCP的多路复用

服务端推送

二进制格式传输

缺陷：TCP层的队头阻塞问题，多个流复用同一个TCP连接，如果一个流阻塞了，那么其他的流都会被阻塞。



### http3的特点

使用基于UDP的QUIC协议

无队头阻塞：丢包时只会阻塞流

更快的建立连接：TLS握手的过程与QUIC握手同时进行

连接迁移：使用连接ID标识，而不是四元组（IP、端口），双方IP变化时可以复用之前的上下文。





### https的优化

在http的三次握手之后执行SSL/TLS的握手过程。

混合加密

数字签名（摘要）

数字证书



