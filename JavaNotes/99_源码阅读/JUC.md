## ConcurrentHashMap
### 初始化
### `get()`操作
区分于HashTable的`get()`操作，ConcurrentHashMap的`get()`操作是不需要加锁的。因为`get()`方法里将要使用的共享变量都被定义成了volatile类型，如count字段和存储值的HashEntry的value字段。

`get()`方法首先通过Key的`hashCode()`进行一次散列计算，再利用计算出的散列值再进行一次散列运算定位到Segment，在通过散列运算定位到元素。

### `put()`操作

### 扩容操作

## AQS
#### 简介
抽象队列同步器，是构建锁、或其它同步组件的基础框架。

可以通过继承AQS类，实现几个抽象方法来管理同步状态。

使用`int`类型的`state`变量来表示同步状态：
- 当`state > 0`时，表示已经获取了锁；
- 当`state = 0`时，表示已经释放了锁。

提供了三个方法，来对同步状态`state`进行操作，确保对`state`变量的操作是线程安全的。
- `getState()`、`setState(int new State)`、`compareAndSetState(int expect, int update)`

#### 同步队列
AQS底层是通过CLH队列的变种实现同步队列的。