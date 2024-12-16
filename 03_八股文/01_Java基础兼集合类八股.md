# <font color="#245bdb">Java基础</font>
## 1. 接口和抽象类的区别：
共同点：都可以用来实现抽象层。
区别：
- 接口主要用于制定规范，抽象类主要是为了复用，例如模板方法模式；
- 接口是可以多实现的，但抽象类只能单继承；
## 2. 为什么Java不支持多继承？
因为参考了C++的设计，考虑到多继承会引入菱形继承问题，且会造成歧义。但是，Java支持接口的多实现，并且将多实现中默认方法的冲突交由开发者来解决（主动重写方法）。

多实现中：①两个接口都有默认方法；②一接口是默认方法，一接口是抽象方法。都要求手动实现重写接口中的抽象方法。
```java
>>> 接口Pet <<<
public interface Pet {  
    public default void eat(){  
        System.out.println("Pet eat!!!");  
    }  
}
>>> 接口Mamal <<<
public interface Mamal {  
    default void eat(){  
        System.out.println("Mamal eat");  
    }  
}
public class MultiImplTest implements Pet{  
    public static void main(String[] args) {  
        MultiImplTest multiImplTest = new MultiImplTest();
        // Pet multiImplTest = new MultiImplTest();  
        multiImplTest.eat();  
    }  
}
>>>OUTPUT: Pet eat!!!
```
此时`MultiImplTest`实现了Pet接口，并且**由于Pet接口中的`eat`是默认方法，所以可以不实现**，此时调用eat方法会输出接口Pet中的`Pet eat!!!`。

但是，若`MultiImplTest`实现了两个接口，且两个接口中都有默认方法，此时就必须重写`eat()`方法！否则会报错：
```cmd
test.chapter8.multipleImplTest.MultiImplTest **inherits unrelated defaults for eat() from types** test.chapter8.multipleImplTest.Pet and test.chapter8.multipleImplTest.Mamal
```
## 3. 如何理解Java中的多态？
- **定义**：同一操作作用于不同的对象，可以有不同的解释，产生不同的执行结果。
- 多态是一种运行期的状态，需要满足三个条件：
	- 实现接口或继承父类；
	- 子类或实现类重写父类的方法
	- **父类的引用指向子类对象**。

在继承中的体现：
```java
public class Parent{
    public void call(){
        sout("im Parent");
    }
}

public class Son extends Parent{// 1.有类继承或者接口实现
    public void call(){// 2.子类要重写父类的方法
        sout("im Son");
    }
}

public class Daughter extends Parent{// 1.有类继承或者接口实现
    public void call(){// 2.子类要重写父类的方法
        sout("im Daughter");
    }
}

public class Test{

    public static void main(String[] args){
        Parent p = new Son(); //3.父类的引用指向子类的对象
        Parent p1 = new Daughter(); //3.父类的引用指向子类的对象
    }
}
	```
在接口实现中的体现：
```java
public interface Mamal {  
    void eat();  
}

public class Dog implements Mamal {  
    @Override  
    public void eat() {  
        System.out.println("dog eat!");  
    }  
}
public class Cat implements Mamal{  
    @Override  
    public void eat() {  
        System.out.println("cat eat!");  
    }  
}

public class Test {  
    public static void main(String[] args) {  
        Mamal dog = new Dog();  
        Mamal cat = new Cat();  
        dog.eat();  
        cat.eat();  
    }  
}
```

## 4. 为什么需要包装类？
- 因为Java是面向对象的语言，包装类能**让基本数据类型具有对象的性质**，并且添加了属性和方法，丰富了基本类型的操作。

基本数据类型和包装类的区别：
1. **存储方式不同**：基本类型保存在栈上，包装类保存在堆上。
2. **默认值不同**：基本类型的默认值为0、false，包装类的默认值为null。（因此，在接口定义中一般使用包装类，避免返回值的歧义。）

## 5. 自动装拆箱是什么？底层原理？
- 是Java引入的一项语法糖特性，能自动实现基本数据类型和对应包装类的转换，减少开发人员的工作；
- **底层原理**：`Integer.valueOf()`实现自动装箱；`Integer.intValue()`实现自动拆箱；
```java hl:7,10
public static void main(String[] args) {  
    Integer a = 10;  
    int b = a;  
}
对应字节码↓
 0 bipush 10
 2 invokestatic #7 <java/lang/Integer.valueOf : (I)Ljava/lang/Integer;>
 5 astore_1
 6 aload_1
 7 invokevirtual #13 <java/lang/Integer.intValue : ()I>
10 istore_2
11 return
```
## 6. 自动装拆箱的缓存是什么？
- Integer类通过**使用相同的对象引用**实现了缓存和重用，默认缓存范围$[-128,127]$；
	- Long类型也有缓存，通过LongCache类实现。
- 优点：提高了性能，节省内存；

**内部实现：**
- 通过IntegerCache类实现；
- 可以通过设置VM参数：`java.lang.Integer.IntegerCache.high`修改缓存最大值，但不能超过MAX_VALUE
```java
public static Integer valueOf(int i) {  
    if (i >= IntegerCache.low && i <= IntegerCache.high)  
        return IntegerCache.cache[i + (-IntegerCache.low)];  
    return new Integer(i);
}
```

## 7. String为什么设计成不可变？
## 8. String是如何实现不可变的？

## 9. `String str = new String("cx");`创建了几个对象？

## 10. `String a = "ab"';String b = "a" + "b";` a\==b吗？

## 11. String中intern的原理是什么？

## 12. 为什么JDK9把String的char\[]改成了byte\[]？


## 13. 什么是SPI？和API的区别是什么？

## 14. 什么是AIO、BIO和NIO？

## 15. 什么是深拷贝和浅拷贝？

---
# <font color="#245bdb">Java集合类</font>
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
## 3. HashMap、Hashtable和ConcurrentHashMap的区别？
- HashMap线程不安全，效率更高，可以存储一个null的key；
- Hashtable线程安全，效率较低，内部**方法通过synchronized修饰**。底层数组为数组+链表，不可以有null的key和value，默认初始容量为11.
- ConcurrentHashMap是线程安全的HashMap，内部通过**CAS+分段锁**实现线程安全。它将哈希表分为多个Segment，每个Segment都类似于一个小的HashMap。执行写操作时，会直接锁定对应的Segment，而不是整个哈希表，据此大大提高并发性能。

## 4. HashMap在get和put时经过哪些步骤？
### get方法详解：
1. 调用`hash(key)`方法计算key的哈希值，计算出在数组中的索引位置；
2. 若索引位置为空，则直接返回null；
3. 若不为空，则遍历链表元素，若找到了与key相等的键值对就返回，否则返回null。

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
1. 首先是调用`hash(key)`计算key的哈希值，找到在数组中的索引位置；
2. 若为空，则直接将创建新的Node，将键值对存储在索引位置上
3. 若不为空，则遍历链表或红黑树，若找到与key相同的键值对，则更新为当前值，并返回旧值；
4. 若不为空，但没有找到相同key的键值对，则在插入新节点；
5. 若插入元素后超过转换阈值（默认为8），则将链表结构转换为红黑树；
6. 插入成功后，若元素超过`Threshold`，则调用`resize()`方法进行扩容操作。
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
## 5. HashMap的key可以为null吗？
可以有一个值为null的key。
当出现key为null时，会直接令哈希值为0，不走`hashCode()`计算哈希值。
## 6. HashMap的remove方法是如何实现的？
1. 调用hash方法计算哈希值，获取在数组中的索引位置；
2. 为空，则直接返回null；
3. 不为空，则检查是否与当前key相等，是的话则直接删除并返回键值对的值
4. 不是的话，则会遍历链表或红黑树，找到对应键值对并删除。

## 7. 讲讲HashMap的延迟初始化
HashMap的构造过程中，虽然创建了HashMap对象，但并不会立即分配内存给底层数组（table），只会设置一些基本参数，如负载因子。只有在第一次调用`put()`方法时，HashMap才会调用`resize()`方法来初始化哈希表并设置容量，从而实际分配内存。

当创建一个 `HashMap` 实例时，可以指定初始容量和负载因子（此时初始容量存储在Threshold变量中），但内部的哈希表（即数组）并不会立即被分配。只有在插入第一个元素时，调用`resize()` 方法来初始化哈希表并设置容量，从而实际分配内存。

**优点：**
- 通过推迟内存分配和资源创建来提高性能并节省内存；

**缺点：**
- 多个线程同时访问未初始化的HashMap时，可能会导致竞态条件。
## 8. 为什么HashMap的容量是$2^n$，如何保证？
简单来说，为了确保能利用位运算替代取模操作来计算桶下标索引。

因为HashMap在计算某个元素对应的桶下标时，**采用了`h & (length - 1)`的位运算操作来代替取模操作**。位运算是基于内存的二进制直接运算，比转成十进制再进行取模运算要快。为了保证取模操作能使用位运算替代，因此要求容量必须是2的n次幂。

**如何保证？**
1. 若不传入初始容量，则默认将HashMap的容量初始化为16.
2. 若传入的初始化容量为$2^n$，则直接将容量初始化为$2^n$。若传入的初始化容量不为$2^n$，则调用`tableSizeFor`方法，找到比传入值大的第一个$2^n$值。

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
## 9. 为什么HashMap的默认负载因子设置成0.75？
>`As a general rule, the default load factor (.75) offers a good tradeoff between time and space costs. Higher values decrease the space overhead but increase the lookup cost (reflected in most of the operations of the HashMap class, including get and put).`

1. 负载因子设置为0.75f，是空间和时间成本的权衡。
	- **过高**：虽减少了空间开销，但是提高了查询成本。假设负载因子取1，则此时在初始容量时，当元素满了才开始扩容，若哈希元素落在了同一个桶中，则查询复杂度增高，退化为$O(n)$.
	- **过低**：导致频繁扩容，插入性能变得很低。
2. 设置为0.75，则此时扩容阈值也肯定为整数。
## 10. HashMap的初始容量设置成多少合适？
- 如果暂时无法确定集合大小，则指定为默认值16即可；
- 若已知需要存储的元素个数，则设定为`initialCapacity= (需要存储的元素个数 / 负载因子) + 1`

不能简单地需要存储多少元素就设置初始容量为多少。例如需要存储7个元素，则设置初始容量为7，这样JDK内部会通过tableSizeFor方法将初始容量设置为8. 但是这个值也不是合理值。因为此时扩容阈值为$0.75\times8=6$，所以存入第6个元素后，内部就会通过resize方法扩容。因此，正确的初始容量设置应该遵循`initialCapacity= (需要存储的元素个数 / 负载因子) + 1`！

**对这个`+1`的理解：**
- ~~假设刚好需要存储12个元素，那么通过`12/0.75`则会获得初始容量为16，那么刚好存储第12个元素就会扩容。而若初始容量+1=16，内部会通过tableSizeFor方法将初始容量设置为32，则可以避免在插入第12个元素的时候扩容。~~
>**stackoverflow:**
>	If you did a `+1` it would add one `Integer` which would then be promoted to a `Float` in the addition to the left hand side `float` value. **They reason they wrote `1.0F` is to tell the compiler that it is a `float` early, avoiding the eventual promotion of `int` to `float`.** **Yes, it's a human optimizing for the needs of the compiler and runtime, but in some JVMs it really can make a difference.** `1.0d` is the `double` version, `1L` is the long version; but, there's no constant specifiers for "less than int" integer data types.

>**stackoverflow:**
>	The table needs to be reallocated when **entry_count > table_size * load_factor**.
>	If we solve for table size, we find that in order to ensure that a reallocation isn't required, we need **table_size >= entry_count / load_factor**.
>	This size is calculated in floating point, and the **1.0F** is added to ensure that the chosen table size will _still_ be large enough after it is rounded down to an integer number of entries.

## 11. ==HashMap是如何扩容的？^==




## 12. 为什么JDK8中HashMap的数据结构要转成红黑树？
1. **使用链表**：若某一个bucket的链表过长，则会导致查询的时间复杂度过高；
2. **使用二叉树**：在极端情况下，也会退化成链表，导致查询的时间复杂度过高；
3. **使用AVL树**：虽然能克服二叉树的退化情况，但是由于需要保持平衡，所以在插入删除时可能会频繁左旋、右旋，导致插入性能低；
- 红黑树在**平衡性上更为宽松，允许更少的旋转来维护平衡**，在插入上最多2次旋转，在删除上最多3次旋转，因此在插入和删除操作上通常比AVL树快。
	- 平衡二叉树要求任意左右子树的高度差不超过1，而红黑树对平衡的要求较宽松，因此不需要频繁的旋转调整。
## 13. 为什么是链表长度达到8的时候转？
### 为什么不在冲突时立刻转换为红黑树？
1. 因为在存储相同数量的节点，红黑树所占用的空间是链表的2倍，因此发生冲突立刻转换，会导致空间浪费；
2. 红黑树的插入删除操作比链表慢（需要节点变色和旋转），因此小于8就转换为红黑树的话，在时间和空间的综合平衡上没链表好。
### 为什么是在长度为8的时候转，而不是其他长度？
当 hashCode遵循泊松分布时，因为哈希冲突造成桶的链表长度等于8的概率只有0.00000006，官方认为这个概率足够的低，所以指定链表长度为 8 时转化为红黑树。

### 为什么在长度为6时转换回来？
>[!abstract]-
>1. 就是长度为6时维护红黑树的节点开销较大，性能低于链表。
>2. 如果在8转换回来

8的时候转成红黑树，那么如果小于8立刻转回去，那么就可能会导致频繁转换，所以要选一个小于8的值，但是又不能是7。而通过前面提到的泊松分布可以看到，当红黑树节点数小于 6 时，它所带来的优势其实就是已经没有那么大了，就不足以抵消由于红黑树维护节点所带来的额外开销，此时转换回链表能够节省空间和时间。

## 14. 如何再次转换回链表的？
会在红黑树的节点小于6时调用`untreeify()`方法重新转换回链表结构。

```java
final Node<K,V> untreeify(HashMap<K,V> map) {  
    Node<K,V> hd = null, tl = null;  
    for (Node<K,V> q = this; q != null; q = q.next) {  
        Node<K,V> p = map.replacementNode(q, null);  
        if (tl == null)  
            hd = p;  
        else  
            tl.next = p;  
        tl = p;  
    }  
    return hd;  
}
```
## 15. ==HashMap的hash方法是如何实现的？==

### JDK1.7：
1. `hashseed`与key的哈希值进行异或，增加随机性；
2. 进行一系列异或操作进行扰动计算；
3. 调用indexFor方法：与`length-1`进行与操作，获取在数组中的索引下标。
```java
final int hash(Object k) {
    int h = hashSeed; // transient int hashSeed = 0;
    if (0 != h && k instanceof String) {
        return sun.misc.Hashing.stringHash32((String) k);
    }

    h ^= k.hashCode();
    h ^= (h >>> 20) ^ (h >>> 12);
    return h ^ (h >>> 7) ^ (h >>> 4);
}

static int indexFor(int h, int length) {
    return h & (length-1);
}
```
### JKD1.8：
- **若key为null**：则直接令哈希值为0；（只能有一个Key为null）
- **若key不为null**：则调用key的`hashCode()`方法获取哈希值，并与哈希值左移16位的结果（高位信息）异或进行扰动计算，以有效混合哈希值的高位和低位信息。
```JAVA
static final int hash(Object key) {  
    int h;  
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  
}
```

>[!NOTE]
>**扰动计算：**
>在 `HashMap` 中，直接使用 `hashCode()` 值作为数组下标可能会导致较多的哈希冲突，因为虽然 `hashCode()` 的取值范围很大（\[-2147483648, 2147483647]），但**实际数组的大小通常较小**（如默认大小为 16），所以与`length-1`做与运算后，高位信息会被丢失，这就意味着许多不同的键可能会映射到同一个下标。
>
>使用扰动函数可以**有效地混合哈希值的高位和低位信息，使得相同的低位哈希值不再总是映射到同一个桶中，从而提高了元素在哈希表中的分散程度。**
## 16. ==ConcurrentHashMap是如何保证线程安全的==？
### JDK1.7以前
![[Pasted image 20241214160057.png|450]]
- 底层数据结构是“Segment数组+HashEntry数组+链表”。
- 通过**分段锁机制**实现线程安全：
	- 底层的Segment数组有多个Segment，每个段拥有独立的锁。这样当多个线程同时访问，**只需要锁住需要操作的段**而不是整个表，以此保证线程安全，并提高并发度。
### JDK1.8及之后
![[Pasted image 20241214160619.png|450]]
- 底层数据结构为Node数组+链表/红黑树。
- 采用“CAS+Synchronized”的机制来保证线程安全：
	- 若某个Node为空，则使用CAS来添加新节点；
	- 若Node不为空，则使用synchronized锁住当前节点，然后遍历链表或红黑树来插入新节点。
## 17. Set是如何保证元素不重复的？
Set的实现类有HashSet和TreeSet，二者保证不重复的机制不同。
### HashSet：
1. 通过`hashCode()`方法获取插入元素的哈希码，并经过扰动处理后用于确定元素在HashSet中的存储位置；
2. 若位置已被占用，则通过`equals()`方法判断二者是否相等，若相等则不插入；若不相等，则会使用链表或红黑树来解决哈希冲突，继续寻找空位来存储元素。
### TreeSet：
TreeSet中的元素都必须实现Comparable接口，通过compareTo方法比较新元素和树中已有元素，若出现结果为0，则代表有重复元素，不插入；否则插入节点。

## 18. JDK6与JDK7中的substring原理与区别:
**substring的作用:**
`substring(int beginIndex, int endIndex)`:截取并返回`[beginIndex,endIndex-1]`范围的字符串子串.

### JDK6中的substring:
jkd6中,String类内部存在三个成员变量:
`char value[]`:存储真正的字符数组;
`int offset`:数组的第一个位置索引
`int count`:字符串中包含的字符个数.

jdk6中,当调用substring方法的时候，会**创建一个新的string对象**，但是这个string的值仍然指向堆中的**同一个字符数组**。这两个对象中**只有count和offset 的值是不同的**:
![[Pasted image 20241016153919.png|450]]
`this.value = value`,引用的还是原来的字符数组.
```java
//JDK 6
String(int offset, int count, char value[]) {
    this.value = value; //相同的字符数组.
    this.offset = offset;
    this.count = count;
}

public String substring(int beginIndex, int endIndex) {
    //check boundary
    return  new String(offset + beginIndex, endIndex - beginIndex, value);
}
```

#### **存在的问题:**
如果你有一个很长很长的字符串，但是当你使用substring进行切割的时候你只需要很短的一段。**这可能导致性能问题，因为你需要的只是一小段字符序列，但是你却引用了整个字符串**（因为这个非常长的字符数组一直在被引用，所以无法被回收，就可能导致内存泄露）。在JDK 6中，一般用以下方式来解决该问题，原理其实就是生成一个新的字符串并引用他。

```java
x = x.substring(x, y) + ""
```

### JDK7中的substring:
在jdk 7 中，substring方法会在**堆内存中创建一个新的数组**。
![[Pasted image 20241016154303.png]]
使用`this.value = Arrays.copyOfRange(value, offset, offset + count); `创建新数组.
```java
//JDK 7
public String(char value[], int offset, int count) {
    //check boundary
    // 与jdk6中不同,创建了一个新的字符数组!
    this.value = Arrays.copyOfRange(value, offset, offset + count); 
    
}

public String substring(int beginIndex, int endIndex) {
    //check boundary
    int subLen = endIndex - beginIndex;
    return new String(value, beginIndex, subLen);
}
```

## 19. 重写HashMap的equal和hashcode方法需要注意什么？
重写规则：
- 如果`o1.equals(o2)`，那么`o1.hashCode() == o2.hashCode()`始终为true；
- 如果`o1.hashCode() == o2.hashCode()`，并不意味着`o1.equals(o2)`会为true

假如重写规则不当，那么会导致不同的key会获得相同的`hashCode()`和`equals()`输出，导致其中一个key-value会被覆盖。