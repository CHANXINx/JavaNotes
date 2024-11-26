## 1. 什么是多线程中的上下文切换？
上下文切换是指 CPU 从一个线程转到另一个线程时，需要保存当前线程的上下文状态，恢复另一个线程的上下文状态，以便于下一次恢复执行该线程时能够正确地运行。

上下文切换通常是指在一个 CPU 上，由于**多个线程共享 CPU 时间片**，当一个线程的时间片用完后，需要切换到另一个线程运行。

## 2. 什么是并发？什么是并行？
并发：多个线程抢占同一个CPU时间片；
并行：多个进程互不抢占CPU资源，可以同时进行。
![[Pasted image 20241122223825.png]]

## 3. 线程有几种状态，状态之间的流转是怎样的？
1. 初始(NEW)：新创建了一个线程对象，但还没有调用start()方法。  
2. 运行(RUNNABLE)：Java线程中将就绪（READY）和运行中（RUNNING）两种状态笼统的称为“运行”。  
- 就绪（READY）:线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中并分配cpu使用权 。  
- 运行中（RUNNING）：就绪(READY)的线程获得了cpu 时间片，开始执行程序代码。  
3. 阻塞(BLOCKED)：表示线程阻塞于锁（关于锁，在后面章节会介绍）。  
4. 等待(WAITING)：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。  
5. 超时等待(TIMED_WAITING)：该状态不同于WAITING，它可以在指定的时间后自行返回。  
6. 终止(TERMINATED)：表示该线程已经执行完毕。

## 4. 什么是守护线程，和普通线程有什么区别？
- 用户线程一般用于执行用户级任务，而守护线程也就是“后台线程”，一般用**来执行后台任务**，守护线程最典型的应用就是GC(垃圾回收器)。
- 若用户线程在运行，则JVM会等用户线程运行结束后才退出；而若守护线程仍在运行，JVM会直接退出，不必等待守护线程运行结束。

**创建守护线程：**
```java
Thread t1 = new Thread();
t1.setDaemon(true);
```
**判断是否是守护线程：**
```java
t1.isDaemon();
```
## 5. JDK21中的虚拟线程是什么？
新引入的虚拟线程，是JDK 实现的**轻量级线程**，他可以**避免上下文切换带来的的额外耗费**。他的实现原理其实是JDK**不再是每一个线程都一对一的对应一个操作系统的线程**，而是会将多个虚拟线程映射到少量操作系统线程中，通过有效的调度来避免那些上下文切换。

## 6. 创建线程有几种方式？
共有四种，不过归根结底就是1）继承Thread类；2）实现Runnable接口。
### 继承Thread类创建线程：
```java
class MyThread extends Thread{
	@Override
	public void run(){
		//业务代码
	}
}
public static void main(String[] args){
	MyThread t1 = new MyThread();
	t.start();
}

```
优点：
1. 编写简单，
缺点：
1. 无法再继承其他父类。
### 实现Runnable接口创建线程：
```java
class MyRunnable implements Runnable{
	@Override
	public void run(){
		// 业务代码
	}
}

public static void main(String[] args){
	Thread t1 = new Thread(new MyRunnable());
	t1.start();
}
```

### 通过Callable和FutureTask创建线程：


### 通过线程池创建线程


### 如何停止一个线程的运行?
1. 调用Interrupt方法；

## 7. 什么是线程池，如何实现的？

## 8. 什么是ThreadLocal，如何实现的？
通过为每一个线程创建一份共享变量的副本来保证各个线程之间的变量的访问和修改互相不影响。

ThreadLocal存放的值是线程内共享的，线程间互斥的，主要用于线程内共享一些数据，避免通过参数来传递，这样处理后，能够优雅的解决一些实际问题。

### 应用场景：
主要就两个作用：1）解决并发问题；2）在线程内传递数据，避免一直使用参数进行传递。
1. **用户信息存储**：使用ThreadLocal存储用户信息，例如JWT鉴权后存储userId；
2. **线程安全**：用来定义一些需要并发安全处理的成员变量，例如SimpleDateFormat，可以使用ThreadLocal为每个线程创建一个独立的SimpleDateFormat实例。

## 谈谈ThreadLocal的内存泄漏问题以及解决办法