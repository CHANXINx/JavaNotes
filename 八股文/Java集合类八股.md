## 1. Hash冲突的解决方式有什么？
### 链地址法：
将同一个hash值的元素连接成一个链表。

优点：
1. 处理冲突简单；
2. 适合经常插入和删除的情况；
3. 适合没有预定空间的情况。

缺点：
1. 冲突较多时，链表过长使得查询复杂度变高。

### 开放定址法:
当发生Hash冲突时，继续探索其他空位，直到找到空槽位。实现方式有1）线性探测；2）二次探测；3）双重散列

开放定址法的优点是：  
- 空间效率：由于不需要额外的数据结构（如链表），开放寻址法通常比链地址法**使用更少的内存**
- 缓存友好性：由于数据存储在连续的内存空间，所以在**寻址时可能有更好的缓存性能**。

开放寻址法也有缺点：  
- 当负载因子（即表中已占用的槽位比例）较高时，**查找空槽位的时间可能会显著增加**。  
- **删除操作相对复杂**，因为简单地将槽位置为空可能会打断探测序列。

**线性探测**：
- 发生冲突时，顺序检查表中的下一个槽位，直到找到空位。
- 线性探测的问题在于“聚集”：一旦发生多次连续冲突，就会形成一长串被占用的槽位，这会影响后续插入和查找的效率。

**二次探测：**
- 与线性探测相同的是都是顺序检查，但是二次探测是依次检查后1、4、9个槽位。
- 可以减少聚集问题，但仍可能发生较小范围的聚集。

**双重散列：**
- 使用与初始定位不同的哈希函数来确定下一个探测位置。
	- 例如`hash2(key) = 3 - key % 3`，在插入key=15发生冲突，第一个探测的位置为1，则下一个探测的位置为 $1+(3- 15 \% 3)=4$
	  ![[Pasted image 20241120223713.png]]
- 减少了聚集，并能更好地分散键的分布。

### 再哈希法：
发生冲突时，使用不同的哈希函数计算哈希值，并在该哈希值对应槽位为空时直接存储。
- 双重散列是在第一个冲突槽位的基础上加上第二个哈希值来获得下一个检测槽位，而再哈希是直接的第二个槽位就是第二个哈希值。

## 2. HashMap的数据结构是怎样的？
JDK1.7以前，是通过数组+链表的数据结构进行存储的。数组中的每一个元素都被称为一个桶(bucket)，桶中存放链表的头结点(Entry)。
![[Pasted image 20241120225226.png]]
JDK1.8以后，在链表长度大于8时，会将链表转换为红黑树结构，并在小于6时再次转换为链表结构。
![[Pasted image 20241121183819.png]]
## 3. 如何再次转换回链表的？
红黑树结构中，存在next和prev节点
![[Pasted image 20241121183819.png]]
## 4. HashMap、Hashtable和ConcurrentHashMap的区别？
- HashMap线程不安全，效率更高，可以存储
- Hashtable线程安全，效率较低，内部方法通过synchronized修饰，不可以有null的key和value，默认初始容量为11.

## 5. HashMap在get和put时经过哪些步骤？^
### get方法详解：
```java
public V get(Object key) {  
    Node<K,V> e;  
    return (e = getNode(key)) == null ? null : e.value;  
}
```

```java
final Node<K,V> getNode(Object key) {  
    Node<K,V>[] tab; 
    Node<K,V> first, e; 
    int n, hash; 
    K k;  
    // (tab = table) != null && (n = tab.length) > 0: 保证桶数组不为空且有元素
    // first = tab[(n - 1) & (hash = hash(key))]) != null：计算哈希值，并且含有该元素
    if ((tab = table) != null && (n = tab.length) > 0 &&  
        (first = tab[(n - 1) & (hash = hash(key))]) != null) {  
        // 判断两者的hash值是否相等
        if (first.hash == hash && // always check first node  
            ((k = first.key) == key || (key != null && key.equals(k))))  
            return first;  
		// 循环遍历链表，直到得到所需元素，或者到尾仍未找到，返回null
        if ((e = first.next) != null) {  
            if (first instanceof TreeNode)  
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);  
            do {  
                if (e.hash == hash &&  
                    ((k = e.key) == key || (key != null && key.equals(k))))  
                    return e;  
            } while ((e = e.next) != null);  
        }  
    }  
    return null;  
}
```

```java
static final int hash(Object key) {  
    int h;  
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  
}
```
### put方法详解：
```java
public V put(K key, V value) {  
    return putVal(hash(key), key, value, false, true);  
}
```

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,  
               boolean evict) {  
    Node<K,V>[] tab; Node<K,V> p; int n, i;  
    if ((tab = table) == null || (n = tab.length) == 0)  
        n = (tab = resize()).length;  
    if ((p = tab[i = (n - 1) & hash]) == null)  
        tab[i] = newNode(hash, key, value, null);  
    else {  
        Node<K,V> e; K k;  
        if (p.hash == hash &&  
            ((k = p.key) == key || (key != null && key.equals(k))))  
            e = p;  
        else if (p instanceof TreeNode)  
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);  
        else {  
            for (int binCount = 0; ; ++binCount) {  
                if ((e = p.next) == null) {  
                    p.next = newNode(hash, key, value, null);  
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st  
                        treeifyBin(tab, hash);  
                    break;  
                }  
                if (e.hash == hash &&  
                    ((k = e.key) == key || (key != null && key.equals(k))))  
                    break;  
                p = e;  
            }  
        }  
        if (e != null) { // existing mapping for key  
            V oldValue = e.value;  
            if (!onlyIfAbsent || oldValue == null)  
                e.value = value;  
            afterNodeAccess(e);  
            return oldValue;  
        }  
    }  
    ++modCount;  
    if (++size > threshold)  
        resize();  
    afterNodeInsertion(evict);  
    return null;  
}
```
## 6. HashMap的remove方法是如何实现的？


## 7. 为什么HashMap的容量是$2^n$，如何保证？
因为HashMap在计算某个元素对应的桶下标时，采用了`h & (length - 1)`的位运算操作来代替取模操作。位运算是基于内存的二进制直接运算，比转成十进制再进行取模运算要快。为了保证取模操作能使用位运算替代，因此要求容量必须是2的n次幂。

**如何保证？**
1. 若不传入初始容量，则默认将HashMap的容量初始化为16.
2. 若传入的初始化容量为$2^n$，则直接将容量初始化为$2^n$。若传入的初始化容量不为$2^n$，则调用tableSizeFor方法，找到比传入值大的第一个$2^n$值。

```java
static final int tableSizeFor(int cap) {  
    int n = -1 >>> Integer.numberOfLeadingZeros(cap - 1);  
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;  
}
```
```java
public static int numberOfLeadingZeros(int i) {  
    // HD, Count leading 0's  
    if (i <= 0)  
        return i == 0 ? 32 : 0;  
    int n = 31;  
    if (i >= 1 << 16) { n -= 16; i >>>= 16; }  
    if (i >= 1 <<  8) { n -=  8; i >>>=  8; }  
    if (i >= 1 <<  4) { n -=  4; i >>>=  4; }  
    if (i >= 1 <<  2) { n -=  2; i >>>=  2; }  
    return n - (i >>> 1);  
}
```
## 8. 为什么HashMap的默认负载因子设置成0.75？
>`As a general rule, the default load factor (.75) offers a good tradeoff between time and space costs. Higher values decrease the space overhead but increase the lookup cost (reflected in most of the operations of the HashMap class, including get and put).`

1. 负载因子设置为0.75f，是空间和时间成本的权衡。若设置的过高，虽减少了空间开销，但是提高了查询成本。假设负载因子取1，则此时在初始容量时，当元素满了才开始扩容，若哈希元素落在了同一个桶中，则查询复杂度增高，退化为$O(n)$.
2. 设置为0.75，则此时扩容阈值也肯定为整数。
## 9. HashMap的初始容量设置成多少合适？
- 如果暂时无法确定集合大小，则指定为默认值16即可；
- 若已知需要存储的元素个数，则设定为`initialCapacity= (需要存储的元素个数 / 负载因子) + 1`

## 10. ==HashMap是如何扩容的？==

## 11. 为什么JDK8中HashMap的数据结构要转成红黑树？
1. 使用链表时，若某一个bucket的链表过长，则会导致查询的时间复杂度过高；
2. 使用二叉树，在极端情况下，也会退化成链表，导致查询的时间复杂度过高；
3. 使用AVL树，虽然能克服二叉树的退化情况，但是由于需要保持平衡，所以在插入删除时可能会频繁左旋、右旋，导致插入性能低；
4. -**红黑树**在平衡性上更为宽松，允许更少的旋转来维护平衡，在插入上最多2次旋转，在删除上最多3次旋转，因此在插入和删除操作上通常比AVL树快。
## 12. 为什么是链表长度达到8的时候转？
### 11.1 为什么不在冲突时立刻转换为红黑树？
1. 因为在存储相同数量的节点，红黑树所占用的空间是链表的2倍，因此发生冲突立刻转换，会导致空间浪费；
2. 红黑树的插入删除操作比链表慢（需要节点变色和旋转），因此小于8就转换为红黑树的话，在时间和空间的综合平衡上没链表好。
### 11.2 为什么是在长度为8的时候转，而不是其他长度？
当 hashCode遵循泊松分布时，因为哈希冲突造成桶的链表长度等于8的概率只有0.00000006，官方认为这个概率足够的低，所以指定链表长度为 8 时转化为红黑树。

### 11.3 为什么在长度为6时转换回来？
8的时候转成红黑树，那么如果小于8立刻转回去，那么就可能会导致频繁转换，所以要选一个小于8的值，但是又不能是7。而通过前面提到的泊松分布可以看到，当红黑树节点数小于 6 时，它所带来的优势其实就是已经没有那么大了，就不足以抵消由于红黑树维护节点所带来的额外开销，此时转换回链表能够节省空间和时间。

## 13. HashMap的hash方法是如何实现的？


## 14. ==ConcurrentHashMap是如何保证线程安全的==？
### JDK1.7以前

使用**分段锁技术**，将哈希表分为多个段，每个段拥有独立的锁。这样当多个线程同时访问哈希表时，只需要锁住需要操作的段而不是整个表，以此保证线程安全，并提高并发度。

### JDK1.8及之后
使用节点锁的思想，即采用“CAS+Synchronized”的机制来保证线程安全：
- 若某个段为空，则使用CAS来添加新节点；若段不为空，则使用synchronized加锁，再次尝试put。

这样可以避免分段锁机制下的锁粒度太大，以及在高并发场景下，由于线程数量过多导致的锁竞争问题，提高了并发性能。


## 15. Set是如何保证元素不重复的？
Set的实现类有HashSet和TreeSet，二者保证不重复的机制不同。
### HashSet：
1. 通过`hashCode()`方法获取插入元素的哈希码，并经过扰动处理后用于确定元素在HashSet中的存储位置；
2. 若位置已被占用，则通过`equals()`方法判断二者是否相等，若相等则不插入；若不相等，则会使用链表或红黑树来解决哈希冲突，继续寻找空位来存储元素。

### TreeSet：
TreeSet中的元素都必须实现Comparable接口，通过compareTo方法比较新元素和树中已有元素，若出现结果为0，则代表有重复元素，不插入；否则插入节点。