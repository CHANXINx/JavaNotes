# <font color="#245bdb">Java基础</font>
## 1. Java的异常体系
![[Pasted image 20250213011606.png]]
主要基于两大类：Throwable类及其子类。Throwable有两大子类：Error和Exception。
- **Error**：表示系统级错误，是Java运行环境内部错误或硬件问题，不指望程序来处理此问题，只能退出。是由Java虚拟机抛出的，如OOMError、StackOverFlowError。
- **Exception**：表示程序需要捕获、处理的异常，是由程序设计的不完善而出现的问题，程序必须进行处理。可分为**运行时异常**和**非运行时异常**。
	- **运行时异常**：
		- 无需显式捕获或声明，编译器不会强制要求处理这类异常；
		- 通常是由编程错误引起。【不可预见】
		- 常见的运行时异常有：空指针异常`NullPointerException`、索引越界异常`IndexOutOfBoundsException`、类型转换错误`ClassCastException`、`NoSuchElementException`等。
	- **非运行时异常**：
		- 必须显式捕获或声明；
		- 通常是可预见的异常情况：通常是由于合理、可预见的异常情况引起的。
		- 常见的非运行时异常：`IOException`、`ClassNotFoundException`、`FileNotFoundException`

注意，开发者也可以**自定义异常**：一般通过继承Exception的子类`RuntimeException`实现。自定义异常常常具有业务含义，方便理解与处理。
## 2. 异常处理相关的关键字及其作用。
- `try`： 用来指定一块预防异常的程序，其中包含可能会发生异常的代码；
- `catch`：配合try使用，用于指定想要捕获的异常并进行处理异常。
- `finally`：用于确保一段代码不管发生什么异常都要被执行，常用于资源释放操作。
- `throw`：用于显式抛出异常。
- `throws`：用于声明某个方法可能抛出的异常。调用者可以处理或想继续抛出这些异常。
## 3. 谈谈你对反射的理解。
- 反射机制指的是**程序在运行时能够获取自身的信息**。在java中，只要给定类的名字，那么就可以通过反射机制来获得类的所有属性和方法。
- 反射可以提高程序的灵活性和扩展性，
- 反射的常用API：
	- 判断对象所属类：`getClass()`
	- 获取成员变量：`getDeclaredFields()`
	- 获取方法：`getDeclaredMethods()`
	- 获取构造函数：`getConstructor()`
	- 设置私有方法/属性可访问：`setAccessible(true)`
	- 调用默认构造函数new对象：`newInstance()`

##### 为什么不建议使用反射？
1. 代码可读性及可维护性差；
2. 反射代码执行的性能低；
3. 反射破坏了封装性。
##### 为什么反射慢？
>[!口语化回答]
>反射之所以慢，主要是因为它是在**运行时动态查找和调用类的方法或属性**。这个过程需要**额外的类型检查、安全验证**，而且**无法像直接调用那样利用编译期的优化**。简单来说，就是灵活性换来了性能的损耗。

1. 反射**所操作的目标在编译期时未知的**，因此不能执行某些JVM优化，例如JIT编译；
2. 反射允许**在运行时动态获取和操作类的信息**，
3. 反射**调用方法时会从方法数组中遍历查找，并且会检查可见性**，这些动作都是耗时的。
4. 在某些情况下，反射操作可能涉及**基本数据类型与包装类型之间的转换**（装箱/拆箱），进一步增加了性能负担。
##### 反射主要的应用场景有什么？
- 框架设计：如**依赖注入**、**序列化及反序列化**、**动态代理**等场景。
- BeanUtils类的属性值拷贝。
##### 如何优化反射的性能？
1. 采用缓存技术，把反射获取到的方法或字段缓存起来，减少重复查找的开销。
2. 尽量减少反射的使用，或在初始化阶段集中完成反射操作，后续直接使用缓存的结果。

## 4. 怎么理解面向对象？
面向对象是一种编程范式，它**将现实世界中的事物抽象为对象**，对象具有属性（称为字段或属性）和行为（称为方法）。面向对象编程的设计思想是**以对象为中心**，通过对象之间的交互来完成程序的功能，具有灵活性和可扩展性，通过封装和继承可以更好地应对需求变化。
## 5. Java三大特性是什么？
封装、继承和多态。
- **封装**：指将对象的属性（数据）和行为（方法）结合在一起，对外隐藏对象的内部细节，仅通过对象提供的接口与外界交互。
- **继承**：是一种可以使得子类自动共享父类数据结构和方法的机制。它是代码复用的重要手段，通过继承可以建立类与类之间的层次关系，使得结构更加清晰。
- **多态**：同一操作作用于不同的对象，可以有不同的解释，产生不同的执行结果。
## 6. 接口和抽象类的区别：
共同点：都可以用来实现抽象层。
区别：
- 接口主要用于制定规范，抽象类主要是为了复用，例如模板方法模式；
- 接口是可以多实现的，但抽象类只能单继承；
## 7. 为什么Java不支持多继承？
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
## 8. 如何理解Java中的多态？
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

## 9. 为什么需要包装类？
- 因为Java是面向对象的语言，包装类能**让基本数据类型具有对象的性质**，并且添加了属性和方法，丰富了基本类型的操作。

基本数据类型和包装类的区别：
3. **存储方式不同**：基本类型保存在栈上，包装类保存在堆上。
4. **默认值不同**：基本类型的默认值为0、false，包装类的默认值为null。（因此，在接口定义中一般使用包装类，避免返回值的歧义。）
## 10. 自动装拆箱是什么？底层原理？
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
## 11. 自动装拆箱的缓存是什么？
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
## 12. String为什么设计成不可变？
1. **性能**：
	- String的不可变带来了**字符串常量池机制**：String变量被创建后，会存储在常量池中。如果有多个相同内容的String对象，JVM可以直接引用常量池的同一个对象，而无需创建新的实例，节省内存。
	- **hashCode缓存**：String类不可变性保证了字符串的值不会改变，因此会在第一次调用`hashCode()`方法期间计算并缓存，并在后续直接返回相同的值。
2. **线程安全**：String不可变，因此多个线程可以**安全地读取同一个String对象**。
3. **安全性**：String类常用于存储敏感信息，因此不可变保证了其内容是可信任的。

>[!String安全性的理解]-
![[Pasted image 20241216142627.png]]
## 13. String是如何实现不可变的？
1. **不可被继承**：String类被final修饰，不可被继承，因此内部方法不可被重写；
2. **数组不可变**：内部`char[](byte[])`数组被final修饰，因此一旦被初始化，就不能再让其指向另一个对象；并且`value[]`数组是private的，不暴露给外部，因此内部值也无法被修改。
3. **内部方法的不可变**：String的`substring()`、`concat()`方法等都会创建新String对象。
>[!数组不可变的测试]-
>可以修改test数组内部的值，但不能让其指向新的`int[]`数组。
>![[Pasted image 20241216143725.png]]
## 14. `String str = new String("cx");`创建了几个对象？
分两种情况："cx"是否已经被加入到字符串常量池中。

7. **若"cx"不存在**：会在堆上创建一个str对象，在字符串常量池中创建一个"cx"对象；（str的引用指向常量池中的"cx"）
8. **若"cx"已存在**：只会在堆上创建一个字符串对象，其引用指向字符串常量池中的"cx"对象。

## 15. `String a = "ab"';String b = "a" + "b";` a\==b吗？
相等的。"a"和"b"都是字面量常量，因此在**编译期**，**"a"+"b"会被拼接成"ab"并放入字符串常量池中**，因此b对象也是指向字符串常量池中的"ab"对象，故二者的引用相同，返回true。


```java
String a = "ab";  
String c = "b";  
String b = "a" + c;  
System.out.println(a==b);

>>> false
```
此时`String b = "a" + c;`，c是变量，**无法在编译期确认其内部的值**。因此，`b="a"+c`会**在运行时动态生成新的String对象"ab"**，并且内存地址与字符串常量池中的"ab"地址不同。故返回值是false！
## 16. String中intern的原理是什么？ TODO
- 如果字符串常量池内有已有相同的字符串对象，就会返回该对象的引用；
- 若字符串常量池内还没有与字符串内容相同的一些，就会将该字符串对象的引用添加到常量池内，并返回该引用。
## 17. 为什么JDK9把String的char\[]改成了byte\[]？
**节省字符串占用的内存空间**，减少 GC 次数。

Java内部使用UTF16编码，导致某些字符只需要用一个字节表示，但仍占用两个字节。
因此，**通过`coder`变量区分是否使用哪个编码与数组**：
- 如果所有字符都能用一个字节表示，就会使用LATIN1编码与`byte[]`数组；
- 如果有字符需要用两个字节表示，就仍会使用UTF16编码与`char[]`数组。。

## 18. JDK 9中对字符串的拼接做了什么优化？
JDK9之前，总是会创建StringBuilder对象，利用append方法进行拼接，最后通过toString方法返回。

JDK9之后：
- 引入了`StringConcatFactory`负责生成字符串拼接所需要的代码，提供了一种**动态生成字符串拼接代码**的方法。
- 基于`invokedynamic`指令实现，允许将字符串拼接的操作延迟到运行时，并根据实际场景选择最优的拼接策略。

## 19. JDK6与JDK7中的substring原理与区别:
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

## 20. 什么是SPI？和API的区别是什么？^
API：Application Programming Interface，用于定义调用接口。

SPI：Service Provider Interface，通常用于在应用程序中提供可插拔的实现。
## 21. 什么是AIO、BIO和NIO？
**AIO**：Asynchronous I/O，异步非阻塞I/O模型。线程发起IO请求后，不需要阻塞，立即返回，也不需要定时轮询检查结果，**异步IO操作之后会回调通知调用方**。

**BIO**：Blocking I/O，同步阻塞I/O模型。线程发起IO请求后，**一直阻塞，直到缓冲区数据就绪后**，再进入下一步操作。

**NIO**：Non Blocking I/O，同步非阻塞I/O。线程发起IO请求后，不需要阻塞，立即返回。用户线程不原地等待IO缓冲区，可以先做一些其他操作，只需要**定时轮询检查IO缓冲区数据是否就绪**即可。
![[Pasted image 20241216161021.png|500]]
## 22. 什么是深拷贝和浅拷贝？
**浅拷贝**：将一个对象复制到另一个变量中，但是只复制对象的地址，而不是对象本身。也就是说，原始对象和复制对象实际上是共享同一个内存地址的。
**深拷贝**：将一个对象及其所有子对象都复制到另一个变量中，也就是说，它会**创建一个全新的对象，并将原始对象中的所有属性或元素都复制到新的对象中**。

## 23. 有了equals为啥需要hashCode方法？
`hashCode()`方法提供了一种**快速计算对象哈希值**的方式，这些哈希值用于确定对象在哈希表中的位置。这意味着可以快速定位到对象应该存储在哪个位置或者从哪个位置检索，显著提高了查找效率。

## 24. 什么是反射机制？为什么反射慢？
- 反射机制是指**程序在运行时能够获取自身信息**。

**为什么慢：**
- 反射涉及动态解析，因此不能执行某些JVM优化，如JIT优化；
- 使用反射时，参数需要包装成`Object[]`类型，并且真正执行时，又要拆包成真正的类型，耗时且容易GC；
- 调用方法时，需要遍历方法数组并检查可见性，耗时；

## 25. 什么是序列化与反序列化？^
**序列化**：将Java对象转换为字节数组的过程；
**反序列化**：将字节数组转换为Java对象的过程。

对象序列化机制（object serialization）是Java语言内建的一种对象持久化方式，通过对象序列化，可以把对象的状态保存为字节数组，并且可以在有需要的时候将这个字节数组通过反序列化的方式再转换成对象。对象序列化可以很容易的在JVM中的活动对象和字节数组（流）之间进行转换。
## 26. transient关键字的作用是什么？
## 27. final关键字的作用是什么？
1. **修饰类**：无法被继承。
	- 同时，会导致如果类中的方法存在问题，也无法通过重写的方式来修复。
2. **修饰方法**：无法被重写，并且被private修饰的方法会默认被final修饰。
3. **修饰变量**：一旦初始化，就无法被修改。基本数据类型，则值不可再被更改；引用数据类型，则初始化后，无法让其再指向另外一个对象。
	- `final` 和 `static` 一起修饰的成员变量叫做常量，常量名必须全部大写。
## 28. `static`关键字的作用
可以用于修饰方法、变量、代码块，**表明该成员不属于具体的对象，而是属于整个类，因此可以直接通过类名访问，而无需先创建对象**。但是无法通过静态方法来访问类中的非静态成员。

使用static关键字，可以①减少内存使用（共享静态变量）；②提供全局访问点，无需创建类的对象即可使用其内部的方法和变量。
#### 修饰变量-静态变量
静态变量属于类本身，而不是类的任何实例。
- 所有实例共享统一静态变量；
- **类加载到内存时就被初始化**，而不是在创建对象时。
- **用处**：用于管理类的全局状态或作为常量仓库。
#### 修饰方法-静态方法
静态方法属于类本身，而不属于类的任何实例。使方法可以在不创建类的实例的情况下调用。
- 可以无需创建类的对象，直接通过类名调用。
- 无法访问类的实例变量或实例方法，只能访问其他静态变量。
- **用处**：常用于工具类的方法，如`Arrays.sort()`
#### 修饰代码块-静态代码块
用于初始化类的静态变量。
- 当类被JVM加载并初始化时执行。
- **用处**：通常用于静态变量的复杂初始化。
#### 修饰类-静态内部类
在一个类的内部定义的静态类。
- 可以不依赖于外部类的实例而独立存在。
- 可以访问外部类的静态成员，但不可以直接访问外部类的实例成员。
- **用处**：常用于当内部类的行为不应依赖于外部类的实例时。

---

# <font color="#245bdb">Java集合类</font>

## 1. ArrayList和LinkedList的区别是什么？
- ArrayList基于数组实现；LinkedList基于链表实现。
- ArrayList的get操作时间复杂度为O(1)；LinkedList的get操作时间复杂度是O(N)；
## 2. ArrayList的扩容机制是如何实现的？
add方法添加元素时，会先检查是否需要扩容：当前容量+1是否大于数组长度，若超过则需要扩容：
	1. `int newCapacity = oldCapacity + (oldCapacity >> 1);`，扩容为原来的1.5倍；
	2. 然后调用`Arrays.copyOf()`方法将原数组的值拷贝到新数组中。
## 3. ArrayList的序列化是如何实现的？
## 4. COW是什么，如何保证的线程安全？
- Copy-On-Write，写时复制，通过**读写分离**策略来解决并发问题。
- 一开始多个线程都共享同一个内容，当某个线程需要修改时，会将内容Copy形成一份新内容然后在**新内容上进行写操作**，**写完毕后再将原数组引用指向新数组**。
- `CopyOnWriteArrayList`的整个add操作都是在`synchronized`的保护下进行的。也就是说add方法是线程安全的。
  ![[Pasted image 20250112152228.png]]
	- CopyOnWriteArrayList适合读多写少的场景，允许读读并发、读写并发、但不允许写写并发，会被`syhchronized`阻塞。

## 5. 为什么后面CopyOnWriteArrayList采用synchronized加锁代替ReentrantLock加锁？
因为后面JVM引入了锁升级机制，即偏向锁、轻量级锁、重量级锁几种锁状态，使得其性能已经与ReentrantLock相近，甚至在某些情况更优了！

>`Since Java 9, they use synchronized rather than ReentrantLock in order to save 32 bytes per instance.`
## 6. 什么是fail-fast？什么是fail-safe？ TODO
#### fail-fast快速失败
先考虑异常情况，出现异常了就直接返回并立即上报。

#### fail-safe安全失败
在遍历时防止抛异常中断执行。

JUC包下的集合类都是fail-safe的，可以在多线程并发下使用与修改。
## 7. ConcurrentHashMap是如何保证
## 8. Hash冲突的解决方式有什么？
### 链地址法：
将同一个hash值的元素连接成一个链表。

优点：
4. 处理冲突简单；
5. 适合经常插入和删除的情况；
6. 适合没有预定空间的情况。

缺点：
7. 冲突较多时，链表过长使得查询复杂度变高。

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

## 9. HashMap的数据结构是怎样的？
JDK1.7以前，是通过数组+链表的数据结构进行存储的。数组中的每一个元素都被称为一个桶(bucket)，桶中存放链表的头结点(Entry)。
![[Pasted image 20241120225226.png]]
JDK1.8以后，在链表长度大于8时，会将链表转换为红黑树结构，并在小于6时再次转换为链表结构。
![[Pasted image 20241121183819.png]]
## 10. HashMap、Hashtable和ConcurrentHashMap的区别？
- HashMap线程不安全，效率更高，可以存储一个null的key；
- Hashtable线程安全，效率较低，内部**方法通过synchronized修饰**。底层数组为数组+链表，不可以有null的key和value，默认初始容量为11.
- ConcurrentHashMap是线程安全的HashMap，内部通过**CAS+分段锁**实现线程安全。它将哈希表分为多个Segment，每个Segment都类似于一个小的HashMap。执行写操作时，会直接锁定对应的Segment，而不是整个哈希表，据此大大提高并发性能。

## 11. HashMap在get和put时经过哪些步骤？
### get方法详解：
8. 调用`hash(key)`方法计算key的哈希值，计算出在数组中的索引位置；
9. 若索引位置为空，则直接返回null；
10. 若不为空，则遍历链表元素，若找到了与key相等的键值对就返回，否则返回null。

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
11. 首先是调用`hash(key)`计算key的哈希值，找到在数组中的索引位置；
12. 若为空，则直接将创建新的Node，将键值对存储在索引位置上
13. 若不为空，则遍历链表或红黑树，若找到与key相同的键值对，则更新为当前值，并返回旧值；
14. 若不为空，但没有找到相同key的键值对，则在插入新节点；
15. 若插入元素后超过转换阈值（默认为8），则将链表结构转换为红黑树；
16. 插入成功后，若元素超过`Threshold`，则调用`resize()`方法进行扩容操作。
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
## 12. HashMap的key可以为null吗？
可以有一个值为null的key。
当出现key为null时，会直接令哈希值为0，不走`hashCode()`计算哈希值。

```java
static final int hash(Object key) {  
    int h;  
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  
}
```

## 13. HashMap的remove方法是如何实现的？
1. 调用hash方法计算哈希值，获取在数组中的索引位置；
2. 为空，则对应键值对不存在，直接返回null；
3. 不为空，则检查是否与当前key相等，是的话则直接删除并返回键值对的**值**；
4. 不是的话，则会遍历链表或红黑树，找到对应键值对并删除。

>若传入了val，则同时还需要比较value是否相等。
## 14. 讲讲HashMap的延迟初始化
HashMap的构造过程中，虽然创建了HashMap对象，但并不会立即分配内存给底层数组（table），只会设置一些基本参数，如负载因子。**只有在第一次调用`put()`方法时，HashMap才会调用`resize()`方法来初始化哈希表并设置容量，从而实际分配内存**。

当创建一个 `HashMap` 实例时，可以指定初始容量和负载因子（此时初始容量存储在Threshold变量中），但内部的哈希表（即数组）并不会立即被分配。只有在插入第一个元素时，调用`resize()` 方法来初始化哈希表并设置容量，从而实际分配内存。

**优点：**
- 通过推迟内存分配和资源创建来提高性能并节省内存；

**缺点：**
- 多个线程同时访问未初始化的HashMap时，可能会导致竞态条件。
## 15. 为什么HashMap的容量是$2^n$，如何保证？
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
## 16. 为什么HashMap的默认负载因子设置成0.75？
>`As a general rule, the default load factor (.75) offers a good tradeoff between time and space costs. Higher values decrease the space overhead but increase the lookup cost (reflected in most of the operations of the HashMap class, including get and put).`

1. 负载因子设置为0.75f，是空间和时间成本的权衡。
	- **过高**：虽减少了空间开销，但是提高了查询成本。假设负载因子取1，则此时在初始容量时，当元素满了才开始扩容，若哈希元素落在了同一个桶中，则查询复杂度增高，退化为$O(n)$.
	- **过低**：导致频繁扩容，插入性能变得很低。
2. 设置为0.75，则此时扩容阈值也肯定为整数。
## 17. HashMap的初始容量设置成多少合适？
- 如果暂时无法确定集合大小，则指定为默认值16即可；
- 若已知需要存储的元素个数，则设定为`initialCapacity= (需要存储的元素个数 / 负载因子) + 1`

不能简单地需要存储多少元素就设置初始容量为多少。例如需要存储7个元素，则设置初始容量为7，这样JDK内部会通过tableSizeFor方法将初始容量设置为8. 但是这个值也不是合理值。因为此时扩容阈值为$0.75\times8=6$，所以存入第6个元素后，内部就会通过resize方法扩容。因此，正确的初始容量设置应该遵循`initialCapacity= (需要存储的元素个数 / 负载因子) + 1`！

>[!对这个`+1`的理解：]-
->- ~~假设刚好需要存储12个元素，那么通过`12/0.75`则会获得初始容量为16，那么刚好存储第12个元素就会扩容。而若初始容量+1=16，内部会通过tableSizeFor方法将初始容量设置为32，则可以避免在插入第12个元素的时候扩容。~~
>**stackoverflow:**
>	If you did a `+1` it would add one `Integer` which would then be promoted to a `Float` in the addition to the left hand side `float` value. **They reason they wrote `1.0F` is to tell the compiler that it is a `float` early, avoiding the eventual promotion of `int` to `float`.** **Yes, it's a human optimizing for the needs of the compiler and runtime, but in some JVMs it really can make a difference.** `1.0d` is the `double` version, `1L` is the long version; but, there's no constant specifiers for "less than int" integer data types.
>
>**stackoverflow:**
>	The table needs to be reallocated when **entry_count > table_size * load_factor**.
>	If we solve for table size, we find that in order to ensure that a reallocation isn't required, we need **table_size >= entry_count / load_factor**.
>	This size is calculated in floating point, and the **1.0F** is added to ensure that the chosen table size will _still_ be large enough after it is rounded down to an integer number of entries.

## 18. ==HashMap是如何扩容的？==TODO

- **第1步**是对哈希表长度的扩展（2倍）
- **第2步**是将旧哈希表中的数据放到新的哈希表中

索引计算为`hashCode & newLength-1`


## 19. 为什么JDK8中HashMap的数据结构要转成红黑树？
1. **使用链表**：若某一个bucket的链表过长，则会导致查询的时间复杂度过高；
2. **使用二叉树**：在极端情况下，也会退化成链表，导致查询的时间复杂度过高；
3. **使用AVL树**：虽然能克服二叉树的退化情况，但是由于需要保持平衡，所以在插入删除时可能会频繁左旋、右旋，导致插入性能低；
- 红黑树在**平衡性上更为宽松，允许更少的旋转来维护平衡**，在插入上最多2次旋转，在删除上最多3次旋转，因此在插入和删除操作上通常比AVL树快。
	- 平衡二叉树要求任意左右子树的高度差不超过1，而红黑树对平衡的要求较宽松，因此不需要频繁的旋转调整。
## 20. 为什么是链表长度达到8的时候转？
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

## 21. 如何再次转换回链表的？
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
## 22. ==HashMap的hash方法是如何实现的？==
hash方法的作用：计算哈希值并进行混合处理，使哈希值分布更均匀，从而减少冲突。

### JDK1.7：
30. `hashseed`与key的哈希值进行异或，增加随机性；
31. 进行一系列异或操作进行扰动计算；
32. 调用indexFor方法：与`length-1`进行与操作，获取在数组中的索引下标。
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
- **若key不为null**：则调用key的`hashCode()`方法获取哈希值，并**与哈希值左移16位的结果（高位信息）异或进行扰动计算**，以有效混合哈希值的高位和低位信息。
```JAVA
static final int hash(Object key) {  
    int h;  
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  
}
```

求取桶下标，在put、get等方法内部实现：
`e = tab[index = (n - 1) & hash]`
>[!NOTE]
>**扰动计算：**
>在 `HashMap` 中，直接使用 `hashCode()` 值作为数组下标可能会导致较多的哈希冲突，因为虽然 `hashCode()` 的取值范围很大（\[-2147483648, 2147483647]），但**实际数组的大小通常较小**（如默认大小为 16），所以与`length-1`做与运算后，高位信息会被丢失，这就意味着许多不同的键可能会映射到同一个下标。
>
>使用扰动函数可以**有效地混合哈希值的高位和低位信息，使得相同的低位哈希值不再总是映射到同一个桶中，从而提高了元素在哈希表中的分散程度。**

## 23. 重写HashMap的equal和hashCode方法需要注意什么？
重写规则：
- 如果`o1.equals(o2)`，那么`o1.hashCode() == o2.hashCode()`始终为true；
- 如果`o1.hashCode() == o2.hashCode()`，并不意味着`o1.equals(o2)`会为true

假如重写规则不当，那么会导致不同的key会获得相同的`hashCode()`和`equals()`输出，导致其中一个key-value会被覆盖。
## 24. hashCode方法是如何实现的？TODO
## 25. HashMap为什么在1.7之前采用头插法？为什么1.8以后用尾插法？
### 头插法
优点：
1. **插入效率高**：O(1)复杂度进行插入；
2. **符合时间局部性原理**：最近插入的数据被使用的概率更高，因此通过头插法插入到链表头部，可以使查询效率更高。

缺点：
1. **并发问题**：多线程并发会产生循环引用问题。
### 尾插法：
优点：
1. 更适合红黑树：
2. 解决了循环引用问题：

## 26. ==ConcurrentHashMap是如何保证线程安全的==？
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
## 27. Set是如何保证元素不重复的？
Set的实现类有HashSet和TreeSet，**二者保证不重复的机制不同**。
### HashSet：
HashSet的基本操作都是**基于HashMap**底层实现：![[Pasted image 20241216191335.png]]
1. 通过`hashCode()`方法获取插入元素的哈希码，并经过扰动处理后用于确定元素的索引；
2. 若位置已被占用，则通过`equals()`方法判断二者是否相等，**若相等则不插入**；若不相等，则进行插入。
### TreeSet：
TreeSet的基本操作是**基于TreeMap**底层实现的。
在 `TreeSet` 中，元素的**唯一性由 `compareTo()`（如果元素是可比较的）或 `Comparator` 接口决定**。如果两个元素的排序顺序相同（即 `compareTo()` 返回 `0` 或 `Comparator.compare()` 返回 `0`），则它们被认为是相等的，不会被添加。

## 28. ConcurrentHashMap为什么不允许null值？TODO
HashMap可以存储**一个null的key和不限数量的多个null的值**；而ConcurrentHashMap**既不能存储null的key、也不能存储null的值。**

主要是为了避免并发场景下的二义性。
- HashMap允许null值，因为其是设计给单线程使用的，可以通过`contains(key)`是否真正存储了null（在无其他线程修改的情况下）；
- ConcurrentHashMap是为了多线程并发而设计的，因此假设存储了null值，此时通过`get(key)`获取了null值，是无法通过`contains(key)`来判断是否真正存储了null值的，因此**可能有其他线程在这期间修改了该值**！

![[Pasted image 20250112143012.png]]

**HashMap测试：**
![[Pasted image 20250112143546.png]]
```java
>> null
>> false
```
使用HashMap的`get(3)`方法获取null值，此时可以通过`containsKey(3)`来判断是因为到底是key不存在还是就是存了null值。【在无其他线程修改的情况下】

#### 如何解决？
如果需要在 `ConcurrentHashMap` 中表示键没有值的场景，通常**可以使用一个特殊的默认值（例如空字符串、默认对象等）来代替 `null`。**