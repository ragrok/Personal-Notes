#### Dubbo
1. 测试和生产zk地址一样，怎么保证消费不冲突
- A. group设置不一致
- B. 多版本

2. dubbo运行时,突然所有的zookeeper全部宕机,dubbo是否还会继续提供服务？
- A. 会的，zookeeper会根据本地缓存服务地址进行调用

3. 服务提供者能实现失效踢出是什么原理
- 基于zookeeper的临时节点原理
- 持久节点：是指节点创建后，就一直存在，直到有主动删除来清除这个节点，就是指该节点不会随着会话结束而消失。
- 临时节点：临时节点的生命周期和客户端回话绑定，客户端回话失效，那么这个节点就会自动被清除。

4. 创建临时节点什么时候会被删除
- 连接断了以后，不会马上移除数据，有一个session_time_out时间,过滤这个时间，就会自动删除。

5. Dubbo在安全机制方面是如何解决的
- Dubbo通过Token令牌防止用户绕过注册中心直连，然后在注册中心上管理授权。

6. 核心配置
- dubbo:service/ 服务提供者暴露服务配置
- dubbo:reference/ 服务消费者引用服务配置
- dubbo:protocol/ 服务提供者协议配置
- dubbo:registry/ 注册中心配置
- dubbo:application/ 应用信息配置
- dubbo:provider/ 服务提供者缺省值配置
- dubbo:consumer/ 服务消费者缺省值配置
- dubbo:method/ 方法级配置

7. Dubbo默认使用什么通讯框架，还有什么别的选择吗
- 默认使用Netty，还有mina做备份，他们的主要开发者都是韩国人Line公司的Trusting lee

8. 服务调用是阻塞的吗
- 默认是阻塞的，可以设置为异步调用，前提是不能有返回值

9. 推荐注册中心，还有其他选择吗
- 推荐是Zookeeper,还有redis等做选择

10. 默认使用什么序列化框架，还有哪些
- Hessian序列化，还有dubbo，gson，fastjson，jdk自带序列化

11. 同一个服务多个注册的情况下可以直连某个服务吗
- 可以直连，修改配置即可，也可以使用telnet直连某个服务。

12. 如何解决服务链过长的问题
- 可以结合zipkin实现分布式服务追踪

13. dubbo有哪些协议
- 默认使用dubbo协议，http协议，Rmi协议，Hessian协议

14. dubbo和dubbox的区别
- Duubo是当当网在dubbo上的一些扩展，加了服务可用restful调用，更新了开源组件等
- Dubbox默认使用http协议+rest传入json或者xml来调用
- Dubbo使用Dubbo协议

参考文章：
1. https://my.oschina.net/hp2017/blog/1932446
2. https://blog.csdn.net/eaphyy/article/details/82467239
3. https://www.cnblogs.com/aspirant/p/9003255.html

#### zookeeper
1. zookeeper是什么
zookeeper是一个分布式协调服务，是一个集群的管理者，从字面意思动物饲养员就可以体现出来。

2. zookeeper保证的一致性特性
- 顺序一致性
- 原子性
- 单一视图
- 可靠性
- 实时性

3. zookeper的应用场景
分布式协调：
分布式锁：
配置信息管理：

参考文章：
1. https://www.cnblogs.com/lanqiu5ge/p/9405601.html

#### netty 和 Nio 和 io
1. io 基础：
- 阻塞： 发起一个调用，调用者等待结果返回，也就是当前线程会被挂起，无法继续其他的任务，只有当条件满足线程才会继续。
- 非阻塞： 发起调用，调用者不用一直等待结果返回可以先去干其他事情。
- 同步：发起请求，需要马上获取结果。
- 异步：发起请求，不需要同步获取结果，可以先去做别的事。



参考文章：
https://blog.csdn.net/chenssy/article/details/78703515
https://segmentfault.com/a/1190000014932357

#### 

#### JVM
- [为什么老年代垃圾回收效率比新生代低很多？](https://www.zhihu.com/question/32730099)
- [Java虚拟机垃圾回收(三) 7种垃圾收集器：主要特点 应用场景 设置参数 基本运行原理](https://blog.csdn.net/tjiyu/article/details/53983650)
- [JDK中的命令行工具](https://blog.csdn.net/u013457382/article/details/51044853)
- [JVM参数优化（基础篇）](https://blog.csdn.net/liuxinghao/article/details/73963399)
- [Java三种编译方式：前端编译 JIT编译 AOT编译](https://blog.csdn.net/tjiyu/article/details/53748965)
- [Java前端编译：Java源代码编译成Class文件的过程](https://blog.csdn.net/tjiyu/article/details/53786262)
- [Java即时编译(JIT编译)：运行时把Class文件字节码编译成本地机器码](https://blog.csdn.net/tjiyu/article/details/53948009)

#### 多线程

#### 数据库
- msyql索引使用参考：https://tech.meituan.com/2014/06/30/mysql-index.html
- 事务的隔离级别：https://tech.meituan.com/2014/08/20/innodb-lock.html
       https://www.cnblogs.com/zhoujinyi/p/3437475.html




