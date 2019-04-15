## 多线程重要知识点

- 多线程实现方式：
  1. 继承Thread类来实现，将要多线程处理的逻辑放到run方法中，再调用start()使线程处于就绪状态。
  2. 实现Runnable接口，将要多线程处理的逻辑放到run方法中，再调用Thread实例使用start()使线程处于就绪状态。 
  3. 基于线程池方式来实现多线程，这也是实际项目中比较常见的方式，毕竟计算机内存资源是很宝贵的。
  
- 线程池介绍
  1.   

## 多线程精华文章  
- [java多线程乐观锁和悲观锁](https://juejin.im/post/5b4977ae5188251b146b2fc8)
- [Java线程池 ExecutorService](https://blog.csdn.net/suifeng3051/article/details/49443835)