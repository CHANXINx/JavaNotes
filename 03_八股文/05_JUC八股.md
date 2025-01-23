# <font color="#245bdb">基础</font>
## 1. 什么是多线程中的上下文切换？
上下文切换是指 **CPU 从一个线程转到另一个线程时，需要保存当前线程的上下文状态，恢复另一个线程的上下文状态，以便于下一次恢复执行该线程时能够正确地运行。**

上下文切换通常是指在一个 CPU 上，由于**多个线程共享 CPU 时间片**，当一个线程的时间片用完后，需要切换到另一个线程运行。此时需要保存当前线程的状态信息，包括程序计数器、寄存器、栈指针等，以便下次继续执行能恢复正常的执行状态。
## 2. 谈谈你对线程安全的理解
线程安全是指某个方法或某个代码块在**并发环境**被调用时，能够**正确地处理多个线程之间的共享变量**，使程序功能正确完成。
## 3. 谈谈线程和进程的区别
1. 进程是相互独立的，而线程存在于进程内，一个进程可以有多个线程；
2. 线程作为最小调度单位，进程作为资源分配的最小单位。

## 4. 什么是并发？什么是并行？

CPU的时间被划分为长短相同的时间片，通过操作系统的管理调度，将时间片依次轮流地分配给各个用户使用。

**并发**：多个线程抢占同一个CPU时间片。（**系统有处理多个任务的能力，但同一时刻只有一个任务在执行**）【通过CPU时间片轮转实现】
**并行**：当系统有一个以上CPU时，当一个CPU执行一个进程时，另一个CPU可以执行另一个进程，多个进程互不抢占CPU资源，可以同时进行。（**多个处理器同时执行多个任务**）
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
Java线程分为两类，一类是用户线程，另一类就是守护线程。
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
新引入的虚拟线程，是JDK 实现的**轻量级线程**，他可以**避免上下文切换带来的的额外耗费**。他的实现原理其实是JDK**不再是每一个线程都一对一的对应一个操作系统的线程**，而是会**将多个虚拟线程映射到少量操作系统线程**中，通过有效的调度来避免那些上下文切换。
1. **由JDK实现的轻量级线程，由JVM调度；**
2. **多个虚拟线程共享同一个操作系统线程；**

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
共有**4种**创建方式：
### ①继承Thread类创建线程：
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
	t1.start();
}

```
优点：
1. 编写简单。
缺点：
1. 无法再继承其他父类。
### ②实现Runnable接口创建线程：
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
### ③通过Callable和FutureTask创建线程：（实现Callable接口）
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
### ④通过线程池创建线程：（Executor框架）
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
- start方法用于启动线程；
- 若调用`t1.run()`，**只会在当前线程运行run方法，不会创建新线程**。
### wait/sleep：
- sleep方法可以在任意地方调用；而wait方法只能在同步代码块中调用；
- sleep不会释放对象锁，而wait方法会释放对象锁；
- wait的线程会进入WAITING状态，直至被唤醒；sleep的线程会进入TIME_WAITING，等待休眠结束后再重新尝试获取时间片；
### notify/notifyAll：
- notify只会随机唤醒一个线程，而notifyAll会唤醒所有处于WAITING/TIME_WAITING的线程；
	- 对于hotspot虚拟机，使用“先进先出”的顺序唤醒。
- 被唤醒后会重新竞争锁，并且notifyAll唤醒的线程最终也只会有一个线程成功获取锁；
## 10. ⭐`run()`和`start()`的区别？
**run()方法是在当前线程中同步执行线程任务，而start()方法则是启动一个新的线程来异步执行线程任务。**
## 11. 如何停止一个线程的运行? TODO
1. 调用Interrupt方法；

## 12. 线程同步方式
线程同步指的是让多个线程按顺序访问同一共享资源。

1. synchronized：保证同一时间只有一个线程访问共享资源；
2. ReentrantLock：也保证同一时间只有一个线程访问共享资源，但是更灵活，通过`lock.lock()`加锁。并且支持公平锁、可中断锁、多个条件变量等；
3. Semaphore：允许多个线程同时访问共享资源，并**通过计数器来控制访问量**。通过`lock.acquire()`加锁。
4. CountDownLatch：允许一个线程或多个线程**等待其它线程执行完毕后再执行**，可用于线程之间的协调和通信。
5. CyclicBarrier：用来进行线程协作，**允许多个线程相互等待，当满足计数后，所有等待线程同步执行**。
6. Phaser：与CyclicBarrier类似，但是支持更灵活的栅栏操作，可以动态地注册和注销参与者，并可以控制各个参与者的到达和离开。

## 13. 介绍下线程死锁
死锁发生在多个线程相互等待对方释放锁资源，导致所有线程都无法继续执行。

**产生死锁的四个必要条件：**  
（1） 互斥条件：一个资源每次只能被一个进程使用。  
（2） 占有且等待：一个进程因请求资源而阻塞时，对已获得的资源保持不放。  
（3）不可强行占有：进程已获得的资源，在末使用完之前，不能强行剥夺。  
（4） 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。
## 14. 死锁问题如何排查？
1. 系统级别的排查，Linux环境中，使用`top ps`可查看进程信息，查看哪个进程占用资源多；
2. 使用JDK自带的监控工具进行排查，如使用`jstack pid`命令可以查看死锁的线程信息。

## 15. Thread.sleep(0)的作用是什么？
让当前线程释放CPU时间片，然后重新开始争抢。

使用场景主要为某些底层框架中，**让长期占用CPU资源的线程主动释放CPU时间片**，让其它线程可以有机会获取CPU时间片。

## 16. 实现线程安全的方案有哪些？
1. 单线程执行，例如Redis；
2. 互斥锁，例如synchronized；
3. 读写分离，例如COW；
4. 原子操作，例如AtomicInteger、CAS；
5. 不可变模式，让共享变量只有读操作，例如不可变模式；
6. 数据不共享，例如ThreadLocal，线程之间数据不共享。
## 17. 什么是可重入锁？
可重入锁是一种多线程同步机制，**允许同一线程多次获取同一个锁而不会导致死锁**。

**优点：**
- 有助于避免死锁和提高代码的可维护性，因为它允许在一个线程中嵌套地调用锁定的方法。

**实现：**
- state记录重入次数，threadId记录获取锁的线程ID。
# <font color="#245bdb">线程池</font>
## 1. 什么是线程池，如何实现的？
- 线程池是一种池化技术。**提前创建一批线程保存到线程池中**，当有任务需要执行时，从线程池中选择一个线程来执行任务。
- **作用：** 
	- **降低资源消耗**：减少了频繁的线程创建和线程销毁所带来的性能损耗。
	- **提高响应速度**：有任务提交时，可以立即执行，无需等待线程创建。
	- **提高线程的可管理性**：使用线程池可以进行统一的分配、调优和监控，避免资源的调度失衡，降低系统稳定性。
![[Pasted image 20241128215126.png|550]]
## 2. 线程池由哪些组件构成？^

## 3. 线程池工作流程：
![[Pasted image 20241128223908.png|550]]

## 4. 线程池的参数：
- corePoolSize：线程池的**核心线程数量**。若线程池中的线程数量少于核心线程数，那么这些线程**不会被销毁（回收）**。
- maximumPoolSize：**最多可容纳的线程数量**。`max-core=临时线程（救急线程）的数量。也叫临时线程、非核心线程。`
- keepAliveTime：超过核心线程数的线程，空闲时间超过了keepAliveTime后会被销毁；
- unit：超时时间的单位；
- workQueue：工作队列。若无空闲线程（核心线程）执行新任务，任务就会被放入工作队列等待执行；
- threadFactory：**线程工厂**，用于为新创建的线程取名字、设置优先级等；
- handler：拒绝策略。当核心线程都在忙、且工作队列也已经满了，那么此时新任务就会被拒绝。
	- 常见的拒绝策略：
		1. AbortPolicy：直接抛出`Task xx rejected from XX`，**默认拒绝策略**。
		2. CallerRunsPolicy：使用线程池的调用者所在的线程去执行被拒绝的任务；
		3. DiscardPolicy：不做任何处理，静默拒绝提交的任务；
		4. DiscardOldestPolicy：抛弃最老任务，然后执行该任务；
		5. 自定义。
## 5. 线程池的拒绝策略有哪些？
1. **AbortPolicy**：默认拒绝策略。线程池无法接收新任务时，会**抛出RejectedExecutionException**，任务不会被加入到阻塞队列中，也不会被执行。
2. **CallerRunsPolicy**：使用线程池的调用者所在的线程去执行被拒绝的任务。
3. **DiscardPolicy**：阻塞队列已满时，**会丢弃新任务且不抛异常**。不做任何处理，静默拒绝提交的任务；
4. **DiscardOldestPolicy**：抛弃最老任务（队首任务），然后执行该任务；
5. **自定义拒绝策略**：实现`RejectedExecutionHandler` 接口，实现`rejectedExecution`方法。

>[!拒绝策略的源代码]-
>AbortPolicy：
>![[Pasted image 20241217011938.png]]
>CallerRunsPolicy：
>![[Pasted image 20241217012020.png]]
>DiscardPolicy：
>![[Pasted image 20241217012041.png]]
>DiscardOldestPolicy：
>![[Pasted image 20241217012111.png]]
## 6. 线程池如何预热？
- 通过调用`preStartCoreThread()`或者`preStartAllCoreThread`来创建一个或所有核心线程。
- 提交与核心线程数相等的空任务，确保核心线程被提前创建。
## 7. 线程池有哪几种？
![[Pasted image 20241217002505.png|600]]

### newFixedThreadPool
```java
public static ExecutorService newFixedThreadPool(int nThreads) {  
    return new ThreadPoolExecutor(nThreads, nThreads,  
                                  0L, TimeUnit.MILLISECONDS,  
                                  new LinkedBlockingQueue<Runnable>());  
}
```
- 核心线程数和最大线程数相等（不会创建救急线程）；
- 空闲时间为0；（无救急线程，自然也有无空闲时间）
- 阻塞队列为无界队列LinkedBlockingQueue，会导致请求堆积过多而OOM。

**适用场景：**
- FixedThreadPool 适用于处理 CPU 密集型的任务，确保 CPU 在长期被工作线程使用的情况下，尽可能的少的分配线程，即适用执行长期的任务。
### newCachedThreadPool
```java
public static ExecutorService newCachedThreadPool(ThreadFactory threadFactory) {  
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,  
                                  60L, TimeUnit.SECONDS,  
                                  new SynchronousQueue<Runnable>(),  
                                  threadFactory);  
}
```
- 核心线程为0，会创建至多$2^{31}-1$个救急线程，可能会因为创建过多线程而OOM。
- 阻塞队列是 SynchronousQueue；
- 救急线程在空闲时最多存活60秒。

**适用场景：**
- 用于并发执行大量短期的小任务。
### newSingleThreadExecutor
```java
public static ExecutorService newSingleThreadExecutor() {  
    return new FinalizableDelegatedExecutorService  
        (new ThreadPoolExecutor(1, 1,  
                                0L, TimeUnit.MILLISECONDS,  
                                new LinkedBlockingQueue<Runnable>()));  
}
```
- 核心线程和最大线程都为1，不会创建救急线程；
- 阻塞队列为LinkedBlockingQueue，会导致请求堆积过多OOM；

**适用场景：**
- 适用于串行执行任务的场景，一个任务一个任务地执行。
### newScheduledThreadPool
```java
public ScheduledThreadPoolExecutor(int corePoolSize) {  
    super(corePoolSize, Integer.MAX_VALUE,  
          DEFAULT_KEEPALIVE_MILLIS, MILLISECONDS,  
          new DelayedWorkQueue());  
}
```
- 最大线程数为Integer.MAX_VALUE，可能会导致线程创建过多而OOM！
- 阻塞队列是 DelayedWorkQueue；

**适用场景：**
- **周期性执行任务的场景**，需要限制线程数量的场景

## 8. ==线程数设定成多少更合适？==
根据实际场景决定。需要区分三种任务类型：
### 1. **CPU密集型任务**
- 此时任务需要耗费大量CPU资源进行计算，因此最好避免CPU在上下文切换上带来的损耗，故最好将核心线程和最大线程数都**设置为CPU核数**。
### 2. IO密集型任务
- IO密集型对CPU的使用率较低，导致线程空余时间很多。故最好将核心线程数和最大线程数都设置为**CPU核数的两倍**，以保证当IO线程空闲时，能启用其它线程继续使用CPU资源。
### 3. 混合型任务

### 动态线程池
[[线程池参数设置为多少才合适？]]

>[!查看线程数]-
可通过`Runtime.getRuntime().availableProcessors()`查看系统可用线程数。
![[Pasted image 20241217165633.png]]
![[Pasted image 20241217165739.png]]

## 9. ForkJoinPool和ThreadPoolExecutor区别是什么？
### ForkJoinPool
- ForkJoinPool是基于工作窃取算法实现的线程池，**内部每个线程都有自己的工作队列**，用于存储待执行的任务。当线程执行完自己的任务后，会从其它线程窃取任务来执行，以此实现任务的动态均衡和线程利用率最大化。
- ForkJoinPool适用于**能够进行任务拆分的cpu密集型运算**，例如快速排序。
- ForkJoinPool中的工作线程是一种特殊线程，会自动创建和销毁、自动管理线程的数量和调度。
## 10. 为什么不建议通过Executors构建线程池？
**FixedThreadPool和SingleThreadPool：**【两者都是核心（最大）线程数固定的线程池，因此OOM只会发生在请求数过多】
- 内部的阻塞队列为LinkedBlockingQueue，会导致任务请求堆积过多而OOM。

**CachedThreadPool和ScheduledThreadPool：**
- 最大线程数为Integer.MAX_VALUE，会导致线程创建过多而OOM。

1. 正确的创建线程池方式应该是调用ThreadPoolExecutor来创建线程池，并为阻塞队列指定容量。
	```java
private static ExecutorService executor = new ThreadPoolExecutor(10, 10,
        60L, TimeUnit.SECONDS,
        new ArrayBlockingQueue(10));
	```

2. 更推荐使用guava提供的`ThreadFactoryBuilder`创建线程池。
## 11. 提交给线程池的任务能否撤回？
- 提交给线程池并开始执行后，则无法撤销！
- 但若提交给线程池，尚未进行处理，可以撤销：
	- 通过`submit()` 方法向线程池内提交任务，可通过`cancel`方法进行取消。![[Pasted image 20241217200937.png|600]]
## 12. `submit()`和`execute()`有什么区别？
**`execute()`方法：**
- 只能提交实现Runnable接口的任务（不带返回值）；
- 无返回对象。
	![[Pasted image 20241217202141.png]]
**`submit()`方法：**
- 可以提交实现Runnable接口（不带返回值）或实现Callable接口的任务（带返回值）；
- 返回`Future<T>`对象，可根据返回对象获取返回值、撤销任务、判断任务是否完成等。
![[Pasted image 20241217202342.png]]
# <font color="#245bdb">ThreadLocal：</font>
## 1. 什么是ThreadLocal，如何实现的？
通过为每一个线程创建一份共享变量的副本来保证各个线程之间的变量的访问和修改互相不影响。

ThreadLocal存放的值是**线程内共享的，线程间互斥的**，主要用于线程内共享一些数据，避免通过参数来传递，这样处理后，能够优雅的解决一些实际问题。
### 应用场景：
主要就两个作用：1）解决并发问题；2）在线程内传递数据，避免一直使用参数进行传递。
1. **用户信息存储**：使用ThreadLocal存储用户信息，例如JWT鉴权后存储userId；
2. **线程安全**：用来定义一些需要并发安全处理的成员变量，例如SimpleDateFormat，可以使用ThreadLocal为每个线程创建一个独立的SimpleDateFormat实例。

## 2. ThreadLocalMap的内部结构是怎样的？

![[Pasted image 20250113074825.png|350]]
## ThreadLocalMap中过期Key的清理方法 TODO
过期Key指的是ThreadLocal对象已被回收，而Value无法被回收，导致仍然占用内存。

分为探测式清理和启发式清理。
#### 探测式清理
探测式清理是以当前`Entry` 往后清理，遇到值为`null`则结束清理，属于**线性探测清理**。
#### 启发式清理

## 3. 谈谈ThreadLocal的内存泄漏问题以及解决办法^
- 在 `ThreadLocalMap` 中，`ThreadLocal` 的键（`Key`）是弱引用，而 `Value` 是强引用。
- 当 `ThreadLocal` 对象被回收（因为是弱引用），而 `Value` 没有被主动清理时，`Value` 将无法访问但仍然被线程引用，导致内存泄漏。
	- 线程（如线程池中的线程）持续存活，导致这些无法被清理的值一直占用内存。

解决：
调用`threadLocal.remove();`显式清理。
## 4. 有了InheritableThreadLocal为啥还需要TransmittableThreadLocal？
`InheritableThreadLocal`用于**父子线程之间的参数传递**，但是只适用于在主线程中手动创建子线程。而**无法在复用线程的线程池中使用。**

`TransmittableThreadLocal`是继承并加强了`InheritableThreadLocal`类，用于实现**线程池环境下的线程之间的参数传递**。
使用场景有：
1.  分布式跟踪系统 或 全链路压测（链路打标）；
2. 日志收集记录系统上下文。

## 5. ThreadLocal为什么会导致内存泄漏？TODO
ThreadLocal的内存泄露来自于内部ThreadLocalMap的Entry，Entry是一个键值对，其中Key是ThreadLocal的引用，Value是存储的值。Key就是造成内存泄漏的原因。
![[Pasted image 20241202215828.png|650]]
Key有两个引用源：一个是来自于栈上的ThreadLocal，另一个是ThreadLocalMap的Key。如下图所示。
![[Pasted image 20241202220110.png|650]]
value只有一个引用，就是Thread对象。
![[Pasted image 20241202220212.png|650]]
所以
# <font color="#245bdb">Synchronized</font>

## 1. Synchronized是如何实现的？
1. 针对于代码块，**通过字节码`monitorenter`和`monitorexit`实现**，前者代表加锁，后者代表释放锁。每个对象都维护着一个记录着被锁次数的计数器，未加锁的对象计数器值为0，加锁后计数器自增变1，重入后次数会再加1，解锁的话会减1。计数器为0的话就会释放锁。
2. 针对于方法，是**通过`ACC_SYNCHRONIZED`（ACCESS）标志**实现。当某个线程要访问某个方法时，会先检查是否有该标志。若有，则需要先获得监视器锁。此时若有其它线程来访问，则会因为无法获得监视器锁而被阻塞！
## 2. Monitor是什么？
Java中每个对象都有自己的监视器Monitor，当尝试获取对象的锁时，实质上就是对对象监视器Monitor的获取。

Hotspot中，Monitor由ObjectMonitor实现，ObjectMonitor有几个关键属性：
- \_owner：指向**持有ObjectMonitor对象的线程**；
- \_WaitSet：存放**处于wait状态的线程队列**；【持有锁的线程调用`wait()`方法】
- \_EntryList：存放处于**等待锁block状态的线程队列**；
- \_recursions：**锁的重入次数**；
- \_count：用来记录**线程获取锁的次数**。

**流程：**
- 多个线程尝试获取锁，此时会先进入\_EntryList队列中，当某个线程获取到Monitor，将\_owner设置为当前线程，同时计数器\_count+1；
- 若持有锁的线程调用`wait()`方法，则会释放当前持有的Monitor，并将\_owner设置为null，将计数器\_count-1，并且当前线程会进入WaitSet等待被唤醒；
- 若持有Monitor线程已结束执行代码块，那么会唤醒\_EntryList中的线程来竞争锁； 
## 3. Synchronized锁的是什么？
锁对象、锁类，**实质上都是锁的对象（实例对象或类对象）**，一个是锁的当前实例this，另一个锁的是类对象。

同步方法：
1. `public synchronized void print(){}`：锁的是调用此方法的实例对象；
2. `public static synchronized void print()`：锁的是类对象。
同步代码块：
1. `synchronized (this)`：锁的是this这个实例对象；
2. `synchronized (MyThread.class)`：锁的是`xx.class`类对象。
## 4. synchronized是如何保证原子性、可见性、有序性的？
### 原子性：
由`monitorenter`和`monitorexit`实现，保证了在锁未释放前，内部代码块不会被其他线程访问到。即使在`monitorexit`执行前，CPU时间片用完了，由于synchronized是可重入的，下一个时间片还是会被当前线程获取到，直至代码执行完毕并释放锁。
- 其它线程获取了时间片，也无法执行内部代码块。
### 有序性：
>**Java程序中天然的有序性可以总结为一句话：如果在本线程内观察，所有操作都是天然有序的。如果在一个线程中观察另一个线程，所有操作都是无序的。**

和as-if-serial语义相关，保证了不管如何重排序，单线程程序的执行结果不会改变。
- 仍会发生指令重排！
### 可见性：
当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。
1. 加锁前，会删除工作内存中共享变量的值，从而使用共享变量时会从主存中读取变量最新的值；
2. 加锁后，其它线程无法获取主内存中的共享变量；
3. 解锁前，必须把工作内存中变量的值同步到主存中。
## 5. synchronized的可重入是如何实现的？
因为锁对象的对象头包含了一个Mark Word，存储着对象的状态以及锁信息。当线程尝试重入锁时，JVM会检查线程ID与Mark Word中的线程ID是否匹配，若匹配则锁计数器+1.
## 6. synchronized的锁升级过程是怎样的？
由轻量级锁升级到重量级锁的过程，**无锁→偏向锁→轻量级锁→重量级锁**。

因为重量级锁，在获取锁和释放锁时都需要**在操作系统层面进行线程的阻塞和唤醒**，带来很大开销！因此，引入了“偏向锁”、“轻量级锁”来适应不同场景下的锁竞争情况。

**Mark Word**
- 使用低两位来表示锁状态：01（无锁/偏向锁）、00（轻量级锁）、10（重量级锁），并且引入低三位用于区分无锁和偏向锁。
![[Pasted image 20241130211945.png|700]]
![[Pasted image 20241216225131.png|700]]
- **Lock Record 的指针**：
    - 是轻量级锁的核心，存储于线程栈中，用于快速实现锁的获取和释放。
- **互斥量的指针**：
    - 是重量级锁的核心，存储于 Monitor 对象中，用于线程竞争激烈时实现线程的阻塞和唤醒。
### 无锁→偏向锁：
>**同步代码块被线程首次进入。**

**偏向锁结构**：
- JVM会**在对象头(Mark Word)中设置该线程的Thread ID**，并将锁标志位设置成"01"，偏向锁位也设置成1。此时对象会偏向第一个访问的线程。
### 偏向锁→轻量级锁：
>**有其它线程尝试获取偏向锁。**

**轻量级锁结构**：
- 在轻量级锁状态中，JVM为对象头的Mark Word预留了一部分空间，用于**存储指向线程栈中锁记录的指针**。
### 轻量级锁→重量级锁：
>**CAS操作失败。**（CAS失败10次后就会升级成重量级锁）

**重量级锁结构：**
- JVM会将对象头中的Mark Word修改为指向**一个重量级锁结构（Monitor）**，该结构包含一个Entry Set，用于管理那些尝试获取锁但暂时无法获得的线程。

当有其它线程尝试获取锁时，就会先进入等待队列（Wait Set），等待锁被释放。锁被释放后，会在等待队列中选择一个线程唤醒，此时线程进入就绪状态，尝试重新获取锁。

## 7. 为什么JDK 15要废弃偏向锁？
**偏向锁**：同一个线程重复进入代码块，能快速获得锁，避免CAS的原子操作。

1. **性能损耗**：但是，当有**其它线程尝试获取偏向锁时，需要等到safe point时，才能将偏向锁撤销为无锁状态或者升级为轻量锁**，此过程需要消耗一定性能。偏向锁的撤销需要触发全局安全点，**导致所有线程暂停**，可能引发显著的性能波动，尤其在大型多线程应用中。
2. **减少复杂性，提高代码可维护性**：偏向锁的引入导致代码变复杂，给HotSpot虚拟机中锁相关部分与其他组件之间的交互也带来了复杂性。
## 8. synchronized能降级吗？
锁一旦升级为重量级锁，它将保持在这个状态，直到锁被完全释放。

**特殊情况：**
1. 锁状态检查：在STW停顿期间，JVM会检查所有Monitor对象；
2. 确定降级对象：JVM会识别出没有被任何线程持有的Monitor对象；
3. “降级”操作：对于未被使用的Monitor对象，JVM会进行"deflation"操作，即清理对象状态，使其不再占用系统资源。

## 9. synchronized的重量级锁很慢，为什么还需要重量级锁？

使用轻量级锁，是因为重量级锁的阻塞和唤醒是比较“重”的操作，需要CPU从用户态切换到内核态，开销较大。

但是，在并发高、竞争激烈的环境下，**轻量级锁的自旋会导致CPU资源的浪费**，此时就需要重量级锁**通过阻塞来避免其他线程不断尝试、自旋，造成资源浪费。**

>重量级锁是指在多线程竞争激烈的情况下，synchronized锁膨胀为一种**系统级别**的锁机制。与偏向锁和轻量级锁不同，重量级锁会**阻塞线程**，并会在**加锁和解锁过程中频繁地与操作系统交互**。

## 10. synchronized的锁优化是怎样的？

### 自旋锁：
- 一直占用CPU资源，**重复检查锁是否可用**。
- **优点**：避免了线程上下文切换带来的性能损耗。
- **缺点**：会一直占用CPU资源，可能导致其它线程无法获取CPU时间片，降低系统整体性能。
### 锁消除：
- 在动态编译同步块的时候，JIT编译器通过**逃逸分析技术**判断所使用的锁对象是否只能够被一个线程访问而没有被发布到其它线程。若是，则会将锁优化掉。
### 锁粗化：
- JIT发现**一系列连续的操作都对同一个对象反复加锁和解锁，甚至加锁操作出现在循环体中的时候**，会**将加锁同步的范围扩散（粗化）到整个操作序列的外部**。
```java
for(int i=0;i<100000;i++){  
    synchronized(this){  
        do();  
}
会被优化成：
synchronized(this){  
    for(int i=0;i<100000;i++){  
        do();  
}
```
# <font color="#245bdb">volatile</font>
## 1. volatile关键字有什么作用？^
1. **保证变量对所有线程的可见性**。通过强制线程在读写`volatile`变量时直接与主存交互：
	- 写操作会立刻同步到主存中；
	- 读操作会直接从主存中读取。
2. **禁止指令重排序**。通过内存屏障来禁止特定类型的指令重排序。

**读操作的内存屏障：**
- 当线程读取一个 `volatile` 变量时，JVM 会在读取操作前插入一个 **加载屏障（Load Barrier）**，确保在这个 `volatile` 变量之前的所有写操作（尤其是其他线程对共享数据的修改）已经被刷新到主内存。这样，当前线程读取的 `volatile` 变量总是能看到最新的值。
**写操作的内存屏障：**
- 当线程写入一个 `volatile` 变量时，JVM 会在写操作后插入一个 **存储屏障（Store Barrier）**，确保在写入 `volatile` 变量之前的所有操作（包括当前线程的所有修改）已经被刷新到主内存。这保证了其他线程可以在稍后的时间点看到这个 `volatile` 变量的更新。
## 2. 指令重排序的原理是什么？
为了提高执行性能，**处理器和编译器对字节码指令进行重排序**。

重排序需要满足以下两个要求：
1. 不改变运行结果；
2. 存在数据依赖关系的指令不允许重排序。

## 3. 指令重排有限制吗？
有限制，需要满足happens-before原则和as-if-serial语义。

**happens-before原则：**
- 多线程程序中操作执行顺序的规则：如果一个操作 A “happen-before” 另一个操作 B，那么 A 的结果对 B 是可见的。

**as-if-serial语义：**
- 单线程程序中的执行顺序规则：不管怎么重排序，单线程程序的执行结果都不能被改变
## 4. volatile是如何保证有序性和可见性的？
### 有序性：
- 通过添加内存屏障：
	- 写屏障会确保指令重排序时，不会将写屏障之前的代码排在写屏障之后；
	- 读屏障会确保指令重排序时，不会将读屏障之后的代码排在读屏障之前。
### 可见性：
- 写操作之后，立马刷新到主存；
- 读操作，永远读的是主存中的最新数据。（将本地内存的变量置为无效，直接从主存中读取共享变量）
## 5. volatile能保证原子性吗？（volatile能保证线程安全吗？）
不能。但是可以保证有序性和可见性。
## 6. 有了synchronized为何还需要volatile？^

## 7. 有了CAS为什么还需要volatile？
CAS只能保证对变量修改的原子性，但不能保证可见性。通过添加volatile可以使得CAS的修改能立刻刷新到主存中，避免出现内存不一致错误。

## 8. 什么是happens-before原则和as-if-serial原则?^
### **happens-before原则：**
- 多线程程序中操作执行顺序的规则：如果一个操作 A “happen-before” 另一个操作 B，那么 A 的结果对 B 是可见的。
### **as-if-serial语义：**
- 单线程程序中的执行顺序规则：不管怎么重排序，单线程程序的执行结果都不能被改变
# <font color="#245bdb">AQS</font>
## 1. ==AQS是什么== TODO

- AbstractQueueSynchronizer，抽象队列同步器，是很多同步器的基础框架，如ReentrantLock、Semaphore、CountDownLatch，并且还能通过AQS类来自定义同步器。
- **核心思想**：如果被请求的共享资源空闲，则当前线程能够成功获取资源；否则，它将进入一个等待队列，当有其他线程释放资源时，系统会挑选等待队列中的一个线程，赋予其资源。
	- 如果被请求的共享资源空闲，那么就将当前请求资源的线程设置为有效的工作线程，将共享资源设置为锁定状态；如果共享资源被占用，就需要一定的阻塞等待唤醒机制来保证锁分配。这个机制主要用的是CLH队列的变体实现的，将暂时获取不到锁的线程加入到队列中。【封装成节点加入到队列中】
- 内部维护了一个**FIFO队列**和一个**volatile的int类型的state变量**，为构建锁和同步器提供了一些通用功能的实现。因此，使用AQS能简单且高效的构造出应用广泛的同步器。

#### FIFO队列：
用来实现多线程的排队工作，当线程加锁失败后，会被封装成一个Node添加至队尾。
#### Node变量：
![[Pasted image 20241221113058.png]]
![[Pasted image 20250113022415.png|600]]
`waitStatus>0`：代表取消等待获取资源；
`waitStatus<0`：代表处于有效的等待状态。

![[Pasted image 20250113022604.png]]
#### state变量：
用于判断当前对象锁是否已经被占有，变量的值通过CAS进行修改。
提供了三个基本方法来操作同步状态：get、set和CAS。
```java
// 同步状态
private volatile int state;

// 获取状态
protected final int getState() {
    return state;
}

// 设置状态
protected final void setState(int newState) {
    state = newState;
}

// CAS更新状态
protected final boolean compareAndSetState(int expect, int update) {
    // See below for intrinsics setup to support this
    return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```
## 为什么AQS内部要采用双端队列？
AQS 将队列改为 双向队列，新增了 `next` 指针，使得节点不仅知道前驱节点，也可以直接唤醒后继节点，从而简化了队列操作，提高了唤醒效率。

**实现出队和入队的O(1)操作**
## 2. AQS是如何实现线程的等待和唤醒的？
主要是通过park和unpark实现，
- 获取锁失败的线程会被封装成Node节点并被添加到队列尾部，通过`park()`方法阻塞线程；
- 当锁被释放，会通过`unpark()`方法唤醒队列中的线程来尝试获取锁。
## 3. 讲讲ReentrantLock。
是一种可重入独占锁。有以下几个特性：
1. **可重入性**：通过维护`state`变量来实现可重入。当线程重复进入时，`state`会+1；当线程释放锁时，`state`会-1；
2. **支持公平锁和非公平锁**：默认是非公平锁，可通过new对象时传入true实参来实现公平锁。![[Pasted image 20241217223419.png]]
3. **提供更灵活的锁操作**：具有响应中断、超时等待等操作。

**如何获取的公平锁？**
- 根据传入的fair变量，返回不同的内部类对象。
![[Pasted image 20241218141639.png|400]]

**加锁如何实现的？**
- 非公平锁：
  ![[Pasted image 20241218142023.png]]
- 公平锁：
  ![[Pasted image 20241218142712.png]]
- `hasQueuedThreads()`为false时，才加锁成功，说明此时Node的state均<0!
![[Pasted image 20241218143229.png]]

**解锁是如何实现的？**

## 4. 公平锁和非公平锁的区别是什么？
区别在于线程是否按照入队顺序来获取锁。
- 非公平锁是锁释放后，队列中的线程不按照申请锁的顺序来获得锁，而是竞争锁，失败的继续会队列中等待；
	- **优点**：减少CPU唤醒线程的开销；
	- **缺点**：可能会有排队线程一直无法获取锁，直至饿死。

- 公平锁是锁释放后，队列中的线程按照申请锁的顺序去获得锁，保证队首元素先获得锁。
	- **优点**：所有等待线程都能获取资源，不会饿死；
	- **缺点**：队列中除了第一个线程，其它线程都处于阻塞状态，唤醒阻塞线程的开销会很大。

## 5. 非公平锁吞吐量为什么比公平锁大？|  为什么默认都是非公平锁？
吞吐量是指系统在单位时间内处理请求的数量。

**公平锁：**
- 获取锁时，线程在等待队列中休眠等待；当某线程用完锁后，会唤醒线程中队首线程尝试去获取锁。因此，此时线程需要在运行状态和休眠状态中转换，因此也需要从用户态与内核态之间转换，性能比较低。
**非公平锁：**
- 获取锁时，可以直接尝试CAS获取锁，当CAS失败后才进入等待队列中等待。

性能差别就在此：非公平锁在线程在尝试获取锁时，可能会直接获取锁，**避免了线程状态切换带来的开销**。
![[Pasted image 20241217224848.png|450]]
## 6. LongAdder和AtomicLong的区别？
LongAdder的出现是为了解决AtomicLong在多线程竞争激烈的情况下性能不高的问题，采用**分段+CAS操作**来提升原子操作的性能。
- **优点**：性能更高！
- **缺点**：以空间换时间，因此占用内存会更大；可能出现结果不准确的问题。

### AtomicLong原理：
- 通过Unsafe类调用CAS操作实现原子性。
```java
>>>成员变量和常用方法：
	private volatile long value;

    public final long incrementAndGet() {
        return unsafe.getAndAddLong(this, valueOffset, 1L) + 1L;
    }


    public final long addAndGet(long delta) {
        return unsafe.getAndAddLong(this, valueOffset, delta) + delta;
    }

    public final boolean compareAndSet(long expect, long update) {
        return unsafe.compareAndSwapLong(this, valueOffset, expect, update);
    }

		public final long getAndSet(long newValue) {
        return unsafe.getAndSetLong(this, valueOffset, newValue);
    }
```

### LongAdder原理：
- 通过分散竞争来提高并发：
	- 若竞争不激烈，则直接通过CAS操作更新；
	- 若竞争激烈，则是通过将不同线程分散到Cell数组的不同idx上，并将线程的计数内容保存到该索引上，最后累加统计获取结果。【不准确就来源于累加】
```java
    /**
     * Table of cells. When non-null, size is a power of 2.
     */
    transient volatile Cell[] cells;

    /**
     * Base value, used mainly when there is no contention, but also as
     * a fallback during table initialization races. Updated via CAS.
     */
    transient volatile long base;

    public void add(long x) {
        Cell[] as; long b, v; int m; Cell a;
        if ((as = cells) != null || !casBase(b = base, b + x)) {
            boolean uncontended = true;
            if (as == null || (m = as.length - 1) < 0 ||
                (a = as[getProbe() & m]) == null ||
                !(uncontended = a.cas(v = a.value, v + x)))
                longAccumulate(x, null, uncontended);
        }
    }
	public long sum() {
	    Cell[] as = cells; Cell a;
	    long sum = base;
	    if (as != null) {
	        for (int i = 0; i < as.length; ++i) {
	            if ((a = as[i]) != null)
	                sum += a.value;
	        }
	    }
	    return sum;
	}
```

## 7. CountDownLatch、CyclicBarrier和Semaphore的区别？
三者都用于协调多线程之间的执行。
- CountDownLatch是一个计数器，允许一个或多个线程等待其它线程完成操作。`countDown()`和`await()`。
- CyclicBarrier是一个同步屏障，用于协调多个线程同时执行。
- Semaphore是一个信号量，允许多个线程同时访问共享资源，并通过计数器来控制访问线程数。用于线程需要等待获取许可证才能访问共享资源。`acquire()`和`release()`

# <font color="#245bdb">CAS</font>
## 1. CAS是什么？
全称CompareAndSwap，属于乐观锁，通过比较内存值与预期原值是否相等来决定是否将内存值替换成新值。
## 2. 什么是ABA问题？
CAS会导致ABA问题。

ABA指的是在Compare 和 Swap之间，变量被连续修改了两次，使其值更改又恢复为原值。此时线程1无法感受到这两次修改，仍能成功执行CAS操作。
### **解决办法：**
通过添加版本号来实现。每次修改操作都会使版本号+1，只有在版本号相等时才进行修改。

在JUC类中，可以借助**AtomicStampedReference**来实现。AtomicStampedReference通过维护“引用”和“时间戳”。AtomicStampedReference在执行CAS操作时会检查引用和时间戳是否相等，只有都相等了才会执行更新操作。
- **使用案例：** 初始化时实参为引用和时间戳。
```java
	String initialRef = "hollis";
	int initialStamp = 0;
	
	AtomicStampedReference<String> atomicStampedRef =
		new AtomicStampedReference<>(initialRef, initialStamp);
	
	String newRef = "hollis666";
	int newStamp = initialStamp + 1;
	
	boolean updated = atomicStampedRef.compareAndSet(initialRef, newRef, initialStamp, newStamp);
	System.out.println("Updated: " + updated);
```

## 3. CAS一定有自旋吗？
不一定，但通常为了提高CAS成功率，会做自旋。

自旋指的是CAS在执行失败后，会尝试重新执行CAS操作，直至成功或达到最大尝试次数。
- 采用自旋让CPU空转一段时间，而不是阻塞线程，可以避免线程切换和阻塞带来的开销。但是过多的尝试会耗费CPU资源。
## 4. 什么是Unsafe？
Unsafe是CAS的核心类，用于**提供硬件级别的原子操作**：
1. 通过Unsafe可以分配、释放内存；
2. 可以定位对象某字段的内存位置，也可以修改对象的字段值；
3. 将线程挂起或恢复；
4. CAS操作。
## 5. CAS是如何保证原子性的？
**基于cmpxchg命令（CompareAndExchange）实现。**
1. cmpxchg是原子指令，执行时处理器会自动锁定总线，防止其他CPU访问共享变量，然后执行比较和交换，结束后释放总线；
2. CPU会自动禁止中断；
3. cmpxchg是硬件实现的，CPU的硬件电路确保了执行正确执行以及对共享变量的访问是原子的。

# <font color="#245bdb">内存模型</font>
## 1. ==JMM是什么？==
JMM(Java Memory Model)，Java内存模型，主要用来定义多线程中变量的访问规则，用来解决变量的可见性、有序性和原子性问题，**确保在并发环境中安全地访问共享变量**。

规定：
- 所有共享变量都存储在主存中，每个线程都有自己的工作线程，工作线程中会保存主存中共享变量的副本；
- 线程对共享变量的操作都必须在自己的工作内存中进行，而不能直接读写主存；
- 不同的线程之间无法直接访问对方工作内存中的变量。

>线程在操作volatile变量时，会强制将工作内存中的值与主内存同步，但操作本身还是发生在工作内存中。
### 为什么线程要用自己的内存？
1. 多线程环境下，如果所有线程都直接操作主内存中的共享变量，会**引发更多访问竞争，容易导致数据不一致**；
2. **主内存的访问速度远低于CPU缓存和寄存器**，因此频繁的访问主存会**导致性能降低**；
3. 如果线程始终需要直接操作共享内存，必须严格按照同步协议更新共享变量，**CPU 对指令重排序的自由度会大幅减少，从而影响执行效率。**

## 2. MESI缓存一致性协议是什么？
MESI协议是一种缓存一致性协议，用于保证每个缓存中使用的共享变量的副本是一致的，避免数据不一致导致的问题。

**核心思想：**
- 当CPU操作共享变量时，会发出信号，通知其他CPU将该共享变量副本的缓存行置为无效状态。此时若其他CPU再读取共享变量，就需要从主存中获取最新数据。

**MESI协议中缓存的四种状态：**
- M（Modified）：数据有效，且被修改了，与主存中的数据不一致；
- E（Exclusive）：数据有效，与主存中一致，且只存在于本缓存之中；
- S（Shared）：数据有效，与主存一致，且存在于多个缓存之中；
- I（Invalid）：数据无效。

**工作流程-读：**
1. 处理器读取缓存行时，会先检查缓存行的状态；
2. 若为Exclusive或Shared，则直接从缓存中读取数据（因为此时数据有效！）
3. 若为Modified，则会先将缓存中的数据刷新到主存中，然后再从主存中读取数据；
4. 若为Invalid，则需要从主存中加载数据到缓存，并设置状态为Exclusive或Modified。

**工作流程-写：**
- 当一个处理器修改了缓存中的数据后，会将状态设置为Modified，并通知其他处理器将缓存中的数据状态设置为Invalid。


## 3. ~~什么是总线嗅探和总线风暴，和JMM有什么关系？~~
Java内存模型中保存缓存一致性相关。
- CPU会使用总线嗅探是检测是否有其他处理器修改了变量。
- 总线风暴是由多个处理器同时竞争总线资源时，产生大量的总线通信导致。

## 4. 有了MESI为什么还需要JMM？^
MESI和JMM都旨在解决多线程下的数据一致性问题，但是二者工作在不同层面，并且解决的问题范围不同。



# <font color="#245bdb">多线程编排问题</font>
## 1. CompletableFuture的底层是如何实现的？
CompletableFuture提供了一种简单的方法来实现异步编程和任务组合。
1. 使用**ForkJoinPool**实现异步计算。
2. 内部采用**链式结构**处理异步计算结果。
3. 使用**事件驱动机制**来处理异步计算的的完成事件。
## 2. 为什么CompletableFuture使用ForkJoinPool实现？
1. 因为两者的执行模型和任务分割方式相似。
	1. ForkJoinPool可以自动进行任务拆分与合并，避免了手动拆分与合并任务，简化操作；
	2. 能够自动创建、管理、销毁线程，动态调整线程池大小，并且能够自动调整线程负载，提高线程的利用率，避免过多占用资源。

## 3. 三个线程t1、t2、t3顺序执行
### 使用`join()`实现：
t1正常执行，t2的打印语句前插入`t1.join()`，t3的打印语句前插入`t2.join`。
- 插入`join()`代表将该线程插入在当前线程，所以需要等待该线程完成，才能继续执行后面的代码块。 
```java
  public class PrintTest {  
    public static void main(String[] args) {  
  
        Thread t1 = new Thread(new Runnable() {  
            @Override  
            public void run() {  
                System.out.println(Thread.currentThread().getName() + " is Running!");  
                try {  
                    Thread.sleep(3000);  
                } catch (InterruptedException e) {  
                    throw new RuntimeException(e);  
                }  
            }  
        },"T1");  
  
        Thread t2 = new Thread(new Runnable() {  
            @Override  
            public void run() {  
                try {  
                    t1.join();  
                } catch (InterruptedException e) {  
                    throw new RuntimeException(e);  
                }  
                System.out.println(Thread.currentThread().getName() + " is Running!");  
            }  
        },"T2");  
  
        Thread t3 = new Thread(new Runnable() {  
            @Override  
            public void run() {  
                try {  
                    t2.join();  
                } catch (InterruptedException e) {  
                    throw new RuntimeException(e);  
                }  
                System.out.println(Thread.currentThread().getName() + " is Running!");  
            }  
        },"T3");  
        t1.start();  
        t2.start();  
        t3.start();  
    }  
}
```

### 使用countDownLatch实现：
创建两个countDownLatch，线程1正常执行，t1打印后让`latch1`扣减。t2线程等待`latch1`计数结束后才开始。
```java
public class PrintTest {  
    public static void main(String[] args) {  
        CountDownLatch latch = new CountDownLatch(1);  
        CountDownLatch latch2 = new CountDownLatch(1);  
  
        Thread t1 = new Thread(() -> {  
            System.out.println(Thread.currentThread().getName() + " is Running!");  
            try {  
                Thread.sleep(3000);  
                latch.countDown();  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
        },"T1");  
  
        Thread t2 = new Thread(() -> {  
            try {  
                latch.await();  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
            System.out.println(Thread.currentThread().getName() + " is Running!");  
            try {  
                Thread.sleep(3000);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
            latch2.countDown();  
        },"T2");  
  
        Thread t3 = new Thread(new Runnable() {  
            @Override  
            public void run() {  
                try {  
                    latch2.await();  
                } catch (InterruptedException e) {  
                    throw new RuntimeException(e);  
                }  
                System.out.println(Thread.currentThread().getName() + " is Running!");  
            }  
        },"T3");  
        t1.start();  
        t2.start();  
        t3.start();  
    }  
}
```

#### 实现RUNNABLE接口：
```java
public class PrintTest {  
    public static void main(String[] args) throws InterruptedException {  
        CountDownLatch latch1 = new CountDownLatch(1);  
        CountDownLatch latch2 = new CountDownLatch(1);  
        CountDownLatch latch3 = new CountDownLatch(1);  
  
  
        Thread t1 = new Thread(new MyThread(latch1),"t1");  
        Thread t2 = new Thread(new MyThread(latch2),"t2");  
        Thread t3 = new Thread(new MyThread(latch3),"t3");  
  
        t1.start();  
        latch1.await();  
          
        t2.start();  
        latch2.await();  
          
        t3.start();  
    }  
  
}  
class MyThread implements Runnable{  
    private CountDownLatch latch;  
  
    public MyThread(CountDownLatch latch) {  
        this.latch = latch;  
    }  
  
    @Override  
    public void run() {  
        try {  
            System.out.println(Thread.currentThread().getName() + " is running!");  
            Thread.sleep(2000);  
        } catch (InterruptedException e) {  
            throw new RuntimeException(e);  
        }  
        latch.countDown();  
    }  
}
```
#### 继承Thread类：
```java
public class PrintTest {  
    public static void main(String[] args) throws InterruptedException {  
        CountDownLatch latch1 = new CountDownLatch(1);  
        CountDownLatch latch2 = new CountDownLatch(1);  
        CountDownLatch latch3 = new CountDownLatch(1);  
  
  
        MyThread t1 = new MyThread(latch1,"T1");  
        MyThread t2 = new MyThread(latch2,"T2");  
        MyThread t3 = new MyThread(latch3,"T3");  
  
        t1.start();  
        latch1.await();  
  
        t2.start();  
        latch2.await();  
  
        t3.start();  
    }  
  
}  
class MyThread extends Thread{  
    private CountDownLatch latch;  
  
    public MyThread(CountDownLatch latch,String name) {  
        setName(name);  
        this.latch = latch;  
    }  
  
    @Override  
    public void run() {  
        try {  
            System.out.println(Thread.currentThread().getName() + " is running!");  
            Thread.sleep(2000);  
        } catch (InterruptedException e) {  
            throw new RuntimeException(e);  
        }  
        latch.countDown();  
    }  
}
```

### 使用线程池实现：
- 使用线程池实现时，若使用`Thread.currentThread().getName()`，则只会打印线程池中的唯一核心线程的线程名！
```java
public class PrintTest {  
    public static void main(String[] args) throws InterruptedException {  
        ExecutorService pool = Executors.newFixedThreadPool(1);  
        for (int i = 0; i < 3; i++) {  
            pool.submit(new MyThead("T" + (i+1)));  
        }  
    }  
  
}  
class MyThead extends Thread{  
    private String name;  
  
    public MyThead(String name) {  
        this.name = name;  
        //setName(name);  
    }  
  
    @Override  
    public void run() {  
        System.out.println(name + " is running!");  
        try {  
            Thread.sleep(1500);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    }  
}
```

### 使用CompletableFuture实现：
- 使用CompletableFuture的thenRun进行实现交替打印。
```java
public class PrintTest {  
    public static void main(String[] args) throws InterruptedException, ExecutionException {  
        CompletableFuture<Void> future = CompletableFuture.runAsync(new MyThead("T1")).thenRun(new MyThead("T2")).thenRun(new MyThead("T3"));  
        future.get();  
    }  
  
}  
class MyThead extends Thread{  
    private String name;  
  
    public MyThead(String name) {  
        this.name = name;  
        //setName(name);  
    }  
  
    @Override  
    public void run() {  
        try {  
            System.out.println(name + " is running!");  
            Thread.sleep(1500);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    }  
}
```

## 4. 三个线程顺序打印0~100

## 5. 如何保证i++结果正确？
1. 使用AtomicInteger原子类：
   ![[Pasted image 20241217231047.png]]
2. 使用synchronized锁：
   ![[Pasted image 20241217231218.png]]
3. 使用ReentrantLock锁：
   ![[Pasted image 20241217231338.png]]
## 6. 多线程打印奇偶数如何实现？^
# <font color="#245bdb">分布式锁</font>
## 1. 为什么需要分布式锁？
基于Synchronized的锁是根据每个JVM内部的Monitor实现的，然而在分布式集群的情况下，每个实例都有各自的JVM，所以此时请求进入了不同的JVM，争抢的是不同的Monitor监视器，无法导致互斥效果，自然会出现并发问题。