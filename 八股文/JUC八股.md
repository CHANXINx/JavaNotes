## 1. 什么是多线程中的上下文切换？
上下文切换是指 CPU 从一个线程转到另一个线程时，需要保存当前线程的上下文状态，恢复另一个线程的上下文状态，以便于下一次恢复执行该线程时能够正确地运行。

上下文切换通常是指在一个 CPU 上，由于**多个线程共享 CPU 时间片**，当一个线程的时间片用完后，需要切换到另一个线程运行。

## 2. 谈谈你对线程安全的理解

线程安全是指某个方法或某个代码块在**并发环境** 被调用时，能够**正确地处理多个线程之间的共享变量**，使程序功能正确完成。

## 3. 谈谈线程和进程的区别^

进程是程序的一次执行过程，是系统运行程序的基本单位。

线程是操作系统中调度的最小单位，它是进程中的独立执行单元。多个线程可以共享同一进程的共享资源。与进程相比，线程之间的上下文切换开销更小。

## 4. 什么是并发？什么是并行？

CPU的时间被划分为长短相同的时间片，通过操作系统的管理调度，将时间片依次轮流地分配给各个用户使用。

并发：多个线程抢占同一个CPU时间片。（**系统有处理多个任务的能力，但同一时刻只有一个任务在执行**）【通过CPU时间片轮转实现】
并行：当系统有一个以上CPU时，当一个CPU执行一个进程时，另一个CPU可以执行另一个进程，多个进程互不抢占CPU资源，可以同时进行。（**多个处理器同时执行多个任务**）
![[Pasted image 20241122223825.png]]

## 5. 线程有几种状态，状态之间的流转是怎样的？
1. **初始(NEW)**：新创建了一个线程对象（如通过`new Thread()`)，处于新建状态，但还没有调用`start()`方法开始执行。
   
2. **运行(RUNNABLE)**：已调用线程的`start()`方法，线程进入可运行状态，此时线程可能正在运行（RUNNING），也可能正在等待CPU分配时间片（READY）。
	- 就绪（READY）:线程对象创建后，其他线程(比如main线程）调用了该对象的`start()`方法。该状态的线程位于可运行线程池中，**等待被线程调度选中并分配cpu时间片** 。  
	- 运行中（RUNNING）：就绪(READY)的线程获得了cpu 时间片，开始执行程序代码。  
  
3. **阻塞(BLOCKED)**：表示线程尝试获取锁以进入一个代码块或方法中，但锁被其他线程所持有，所以此时当前线程进入阻塞状态，等待获取锁。
   
4. **等待(WAITING)**：调用了`Object.wait()`或`LockSupport.park()`。进入等待状态的线程需要等待其他线程做出一些特定动作（通知`notify`或中断`interrupt`）。
   
5. **超时等待(TIMED_WAITING)**：具备等待时间的WAITING，**它可以在指定的时间后自行返回可运行状态(RUNNABLE)**。
   
6. **终止(TERMINATED)**：表示该线程已经执行完毕，生命周期结束，不会再被重启。
![[Pasted image 20241126233627.png|]]

## 6. 什么是守护线程，和普通线程有什么区别？
Java线程分为两类，一类是普通线程，另一类就是守护线程。
- 用户线程一般用于执行用户级任务，而守护线程也就是“后台线程”，一般用**来执行后台任务**，守护线程最典型的应用就是**GC(垃圾回收器)。**
- 若用户线程在运行，则JVM会等所有用户线程运行结束后才退出；而若守护线程仍在运行，JVM会直接退出，不必等待守护线程运行结束。

**创建守护线程：**
```java
Thread t1 = new Thread();
t1.setDaemon(true);
```
**判断是否是守护线程：**
```java
t1.isDaemon();
```
## 7. JDK21中的虚拟线程是什么？
新引入的虚拟线程，是JDK 实现的**轻量级线程**，他可以**避免上下文切换带来的的额外耗费**。他的实现原理其实是JDK**不再是每一个线程都一对一的对应一个操作系统的线程**，而是会将多个虚拟线程映射到少量操作系统线程中，通过有效的调度来避免那些上下文切换。

1. 由JDK实现的轻量级线程，由JVM调度；
2. 多个虚拟线程共享同一个操作系统线程；

### 虚拟线程与平台线程的区别：
1. 虚拟线程总是守护线程，无法使用`setDaemon()`方法将虚拟线程设置为非守护线程；
2. 虚拟线程始终为NORMAL优先级，无法通过`setPriority()`进行修改；
3. 不支持`stop()`,`suspend()`,`resume()`等方法。
### 优缺点：
**优点：**
1. **轻量级**：可以在单个线程中创建多个虚拟线程而不会导致过多的线程上下文切换；
2. **简化异步编程**：简化了异步编程，避免了回调地狱（Callback Hell）
3. **减少资源开销**：不必创建过多线程，并且能够更高效地利用底层资源，如CPU和内存。

**缺点：**
1. **不适合计算密集型任务**：虚拟线程适用于I/O密集型任务，不适用于计算密集型任务，因为需要CPU资源作为支持。

### 使用方式：
- 使用 `Thread.startVirtualThread()` 创建
- 使用 `Thread.ofVirtual()` 创建
- 使用 `ThreadFactory` 创建
- 使用线程池创建虚拟线程： `Executors.newVirtualThreadPerTaskExecutor()`
	- 不建议使用。因为线程池的使用场景为避免创建新的操作系统线程带来的额外开销，然而创建虚拟线程开销并不大。



## 8. 创建线程有几种方式？
共有4种创建方式：
### 继承Thread类创建线程：
1.创建MyThread类并继承Thread类；2. 调用`new MyThread()`方法创建线程。
```java hl:1
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
1. 编写简单。
缺点：
1. 无法再继承其他父类。
### 实现Runnable接口创建线程：
1\. 创建MyRunnable类并实现Runnable接口；2. 调用`new Thread(new MyRunnable())`创建线程。
```java hl:1
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
优点：
1. 仍可以继承其他类。
### 通过Callable和FutureTask创建线程：（实现Callable接口）
1. 创建MyCallable类实现Callable接口，实现call方法；
2. 创建FutureTask类实例`futureTask`，传入所要执行的任务`new MyCallable()`；
3. `new Thread(futureTask)`创建线程；
4. 通过`futureTask.get()`获取返回值。
	- Thread类只接收Runnable参数，因此需要通过FutureTask进行封装（实现了Runnable接口）。
```java
class MyCallable implements Callable<T> {
	public T call(){
		return new T();
	}

	public static void main(String[] args){
		MyCallable task = new MyCallable();  
		FutureTask<String> ft = new FutureTask<>(task);  
		Thread t1 = new Thread(ft);  
		t1.start();  
		System.out.println(ft.get());
	}
}
```
### 通过线程池创建线程：（Executor框架）
1. 实现Runnable接口，实现run方法，编写具体任务；
2. `Executors.newXxx(args)`创建线程池；
3. `pool.submit(task)`创建线程，并执行task任务。
```java
class Task implements Runnable{  
    @Override  
    public void run() {  
        System.out.println("hello!" + Thread.currentThread());  
    }  
  
    public static void main(String[] args) {  
        ExecutorService pool = Executors.newFixedThreadPool(10);  
        for (int i = 0; i < 100; i++) {  
            pool.submit(new Task());  
        }  
        pool.shutdown();  
    }  
}
>>> 只会调用预先创建好的10个线程执行任务。
```
优点：
1. 线程池可以重用预先创建好的线程，避免了频繁创建和销毁线程的开销，显著提高程序性能；
2. 对于需要快速响应的并发请求，线程池可以迅速提供线程来处理任务，减少了等待时间；
3. 有效控制线程数量，避免因创建线程过多导致的线程资源耗尽；
	```java
class Task implements Runnable{  
    @Override  
    public void run() {  
        System.out.println("hello!" + Thread.currentThread());  
    }
    public static void main(String[] args) {  
        for (int i = 0; i < 100; i++) {  
            Thread t1 = new Thread(new Task());  
            t1.start();  
        }  
    }  
}
>>> 此处会创建100个线程！
	```
4. 合理控制线程池大小，可以最大化CPU利用率和系统吞吐量。

## 9. run/start、wait/sleep、notify/notifyAll区别?

### run/start：
start方法用于启动线程；
若调用`t1.run()`，只会在当前线程运行run方法，不会创建新线程。

### wait/sleep：
- sleep方法可以在任意地方调用；而wait方法只能在同步代码块中调用；
- sleep不会释放对象锁，而wait方法会释放对象锁；
- wait的线程会进入WAITING状态，直至被唤醒；sleep的线程会进入TIME_WAITING，等待休眠结束后再重新尝试获取时间片；

### notify/notifyAll：
- notify只会随机唤醒一个线程，而notifyAll会唤醒所有处于WAITING/TIME_WAITING的线程；
- 被唤醒后会重新竞争锁，并且notifyAll唤醒的线程最终也只会有一个线程成功获取锁；
### 如何停止一个线程的运行?
1. 调用Interrupt方法；

## 10. 什么是线程池，如何实现的？

## ==线程数设定成多少更合适？==

## 11. 什么是ThreadLocal，如何实现的？
通过为每一个线程创建一份共享变量的副本来保证各个线程之间的变量的访问和修改互相不影响。

ThreadLocal存放的值是线程内共享的，线程间互斥的，主要用于线程内共享一些数据，避免通过参数来传递，这样处理后，能够优雅的解决一些实际问题。

### 应用场景：
主要就两个作用：1）解决并发问题；2）在线程内传递数据，避免一直使用参数进行传递。
1. **用户信息存储**：使用ThreadLocal存储用户信息，例如JWT鉴权后存储userId；
2. **线程安全**：用来定义一些需要并发安全处理的成员变量，例如SimpleDateFormat，可以使用ThreadLocal为每个线程创建一个独立的SimpleDateFormat实例。

## 12. 谈谈ThreadLocal的内存泄漏问题以及解决办法