## Java基础

### 集合框架

* ArrayList源码，重点看put和get方法
* Vector和ArrayList的区别，线程安全怎么实现
* ArrayList多线程替换方案（CopyOnWriteArrayList，实现原理）
* HashMap源码，重点看put和get方法，扩容机制
* HashMap 在JDK 1.7和1.8实现的区别
* HashMap在1.7中会出现死循环的原因
* 1.8中对HashMap做了哪些优化
* HashMap中的一些常量设置，为什么设置成那样
* 多线程场景下HashMap的替换方案（CurrentHashMap）
* CurrentHashMap实现原理（CAS）
* 什么是CAS，存在什么问题，怎么解决，1.8中对它做了哪些优化？

### 多线程

* 创建线程有哪些方式？各个状态之间的转化和常见方法含义
* 结合项目说说哪些地方用了Synchronized锁、ReentrantLock锁
* Synchronized实现原理
* ReentrantLock实现原理（AQS）
* ReentrantReadWriteLock读写锁
* AQS原理
* AQS源码
* 什么是线程池，怎么创建线程池，有哪些参数
* 说说线程池的工作原理
* 线程池拒绝策略有哪些
* 阻塞队列有哪些？为什么不推荐使用Executor创建线程池？
![在这里插入图片描述](https://github.com/tony-wnx/DailyImprove/blob/master/docs/Interview/img/Java面试复习.png)

### IO/NIO

* 有什么区别
* NIO实现机制（多路复用）

## JVM

* 内存区域的划分
* 判断对象是否存活的方法有哪些
* 垃圾回收算法有哪些
* 说说垃圾回器有哪些，各有什么特点（重点CMS、G1，流程、算法、优缺点）
* 1.8和1.7虚拟机有什么区别（去永久代）
* 优化措施

## Mysql

* 说说你平时怎么对sql进行优化
* Mysql数据结构（B+树），了解B+树存储方式
* 索引（为什么用索引就快，使用索引一定快吗，索引失效场景、有索引查询过程是怎样的、非聚簇索引能一次检索出结果吗，怎么实现）
* 事务、事务的隔离级别、mysql和oracle默认级别，虚读幻读不可重复读解决
* 分布式锁实现，结合项目说明
* 分布式事务实现方式，结合项目说明

## Redis

* 数据类型和使用场景
* 说说你们项目中怎么使用的Redis
* Redis线程模型
* 缓存击穿、缓存穿透、缓存雪崩解决方案
* 双写一致性
* 高可用实现方案
* 说说Redis集群或哨兵模式原理
* 持久化方式，优缺点
* 分布式锁实现
* 分布式事务

## 分布式

* Dubbo工作机制
* 协议有哪些
* Dubbo 负载均衡策略和集群容错策略都有哪些
* spi
* 接口幂等性解决方案

## ZK

* 使用场景
* 分布式锁实现方案，优缺点，哪个效率高

## MQ

* 你们用的哪个，为什么使用它
* 怎么保证可靠性传输、重复消费、顺序消费、高可用实现、消息失效、消息积压...

## 常用开发框架

* springAOP 原理、底层实现
* springboot 自动配置原理、源码
* mybatis原理

## 设计模式
