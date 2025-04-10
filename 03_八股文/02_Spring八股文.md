## 1. 介绍一下Spring的IOC:
>[!ABSTRACT]-
>1. **是什么：** 将类的创建和销毁交由Spring容器，由容器负责对象的创建和管理。
>2. **优点：** 
>

IOC，全称Inverse Of Control，控制反转.
传统的程序设计中，应用程序代码通常控制对象的创建和管理。例如：当一个对象需要依赖其他对象时，程序通过new等方式创建对象。而在IOC中，控制关系反转，**将对象的控制权交由Spring容器管理，容器负责创建和管理对象，极大提高代码的模块化和可维护性**！并在有需要时**依赖注入**实现注入到程序中。

**优点:**
1. 使用者无需关心引用bean的全部细节.
2. 不用创建多个相同的bean导致浪费.
3. Bean的修改使用方无需感知.
## 2. 介绍一下DI：
依赖注入，Spring框架实现IOC的具体方式。可以通过XML手动注入或自动注入两种方式实现。

三种自动注入方式：
1. Setter注入；
2. 构造器注入；
3. 字段注入（即通过`@Autowired`注解）.
## 3. 介绍一下Spring的AOP:

AOP，Aspect Oriented Programming，面向切面编程，**把业务逻辑中的通用代码抽取到独立的模块中**。

优点：
- 使代码模块化，减少代码冗余；
- 提高代码的可重用性，并有利于未来的拓展。
## 4. BeanFactory 和 ApplicationContext 的区别？ TODO

## 5. Bean标签的属性
在XML实现注入Bean时，可配置的属性。
- id：Bean的唯一标识符
- name：Bean的别名，可以有1个或多个。
- class：Bean的全限定类名
- scope：Bean的作用域，如单例Singleton（默认）、prototype、request、session等；
- init-method：自定义初始化方法；
- destroy-method：自定义销毁方法；
- factory-method：用于创建Bean实例的静态工厂方法。
- factory-bean：用于创建Bean实例的工厂Bean的名称。

配置demo：
```xml
<bean id="myBean" class="com.example.MyBean" scope="singleton" init-method="init" destroy-method="cleanup" lazy-init="true" primary="true" autowire="byType">
    <constructor-arg value="someValue"/>
    <constructor-arg ref="anotherBean"/>
    <property name="propertyName" value="someValue"/>
    <property name="anotherProperty" ref="anotherBean"/>
</bean>
```
## 6. Bean的作用域有哪些？
Bean的作用域决定了bean在哪个范围内被使用，同时决定了Bean的创建、管理和销毁的方法，可以通过XML注入时的scope属性或自动注入时的`@Scope()`进行配置。Bean的作用域有以下六种：
1. Singleton（单例）：默认作用域。对于一个IoC容器，只创建一个单例Bean。适用于状态是**全局共享**的状态。
2. Prototype（原型）：每次请求该Bean都会创建一个新的Bean实例。适用于**所有状态是非共享**的情况。
	- 每次请求指的是注入，或是调用getBean获取实例。
3. Request（请求）：每次HTTP请求都会创建一个新的Bean实例。用于**请求级别**的数据存储和处理。
4. Session（会话）：每个 HTTP 会话都会创建一个新的 Bean 实例。适用于**会话级别**的数据存储和处理。
5. Application（应用）：在 ServletContext 的生命周期内，只创建一个 Bean 实例。适用于**全应用程序级别**的共享数据。
6. 自定义作用于：实现@Scope接口，实现`get()`、`remove()`等方法，然后在Config类中注册自定义作用域（通过`registerScope()`方法）
后四种仅适用于Web应用。
## 7. Bean的实例化方法有哪几种？TODO

## 8. Spring的@Autowired能用在Map上吗？
可以。@Autowired**可以用于注入复杂的集合类型**，尤其在Map中比较常见。例如当某个Service有多个实现类，则通过@Autowired注解可以实现注入不同实现类的实例，其中KEY为Bean的名称，VALUE为Bean的实例。【通常用在工厂模式，根据不同名称获取不同实例】
## 9. @Autowired和@Resource的关系？
@Autowired是Spring框架提供的自动注入注解，@Resource是JDK官方提供的自动注入注解。

@Autowired：
- @Autowired注解主要是基于类型注入；类型无法匹配，则通过bean名称（看根据注入的字段名是否能找到相同的bean名称）匹配。
- @Autowired注解可以作用于构造器、字段、setter方法上。
- 要求注入的bean必须存在，否则会抛异常。
- 可以配合`@Qualifier`注解使用，用于指定具体的bean。

@Resource：
- @Resource注解是先通过名称注入；名称无法匹配，则通过类型注入。
- @Resource注解可以作用在字段、setter方法上。
- 注入的bean可以不存在，此时会注入null值。
```java
@Component("beanOne")
class BeanOne implements Bean {}
@Component("beanTwo")
class BeanTwo implements Bean {}
@Service
class Test {
    // 此时会报错，先byType找到两个bean：beanOne和beanTwo
    // 然后通过byName（bean）仍然没办法匹配
	@Autowired
    private Bean bean; 

    // 先byType找到两个bean，然后通过byName确认最后要注入的bean
	// (指下面的beanOne和已注册的beanOne名称相同)
    @Autowired
    private Bean beanOne;

    // 先byType找到两个bean，然后通过byName确认最后要注入的bean
    @Autowired
    @Qualifier("beanOne")
    private Bean bean;
}
```

```java
@Component("beanOne")
class BeanOne implements Bean {}
@Component("beanTwo")
class BeanTwo implements Bean {}
@Service
class Test {
    // 此时会报错，先byName，发现没有找到bean
    // 然后通过byType找到了两个Bean：beanOne和beanTwo，仍然没办法匹配
	@Resource
    private Bean bean; 

    // 先byName直接找到了beanOne，然后注入
    @Resource
    private Bean beanOne;

    // 显示通过byType注入，能注入成功
    @Resource(type = BeanOne.class)
    private Bean bean;
}
```
## 10. @Bean 与 @Component 注解有什么区别?
>[!abstract]
>“`@Bean` 和 `@Component` 都是用来定义 Spring 管理的 Bean，但它们的用法不太一样。`@Bean` 是放在方法上的，通常出现在 `@Configuration` 注解的类中，它的作用是通过方法返回一个对象，这个对象会被 Spring 容器管理。而 `@Component` 是放在类上的，标记一个类让 Spring 容器自动扫描并创建该类的实例，通常用来定义那些不需要特别配置的简单 Bean。
>简单来说，`@Bean` 是显式声明 Bean，并允许你在方法内部做一些逻辑处理；而 `@Component` 则是隐式声明，Spring 会**自动扫描并注册标记了这个注解的类**。”


@Bean
- 方法级别的注解，用于在配置类`@Configuration`的方法上。注解的作用是告诉Spring，该方法将返回一个对象并应该被Spring容器管理。
- 给予了开发者更细粒度的控制，可以在配置类中编程式地定义Bean，同时指定初始化和销毁时的特定行为。
- 通常用于注册比较复杂的Bean，可以实现很多定制化处理。例如：当你需要设置一个外部库中的类作为Bean【如NFT项目中的wxPayService】，或者需要复杂的初始化逻辑时，通常会使用`@Bean`。

@Component：
- 类级别的注解。注解的作用是标记一个类，告诉Spring容器自动扫描并创建该类的实例交由Spring容器管理。
- 通常用于简单的Bean。
- 结合`@ComponentScan`使用，Spring容器会自动扫描指定包及其子包的所有类，将含有@Component、@Service、@Repository注解的类注册为Bean。【在结合Springboot项目中，`@ComponentScan`注解被@SpringbootApplication注解涵盖】

## 11. 介绍一下AspectJ的两种代理实现方式
有两种实现方式：
- **静态代理**：在代码运行前，通过修改.class字节码文件完成代理，功能可以无限扩大，并且运行效率比动态代理快很多，但复杂性相对较高。有三种方式：
	- **编译时**：在代码编译时，通过AspectJ编译器重新生成一个新代理类，并把该类加载到JVM中；
	- **编译后**：在代码编译后，通过字节码技术重新构建class和jar文件，通常在代理第三方jar时使用；
	- **加载时**：在JVM加载类时植入代理。
- **动态代理**：在代码运行时进行代理，功能受限且有额外的运行时性能开销，不过使用相对简单！
	- CGLIB动态代理：通过**字节码技术在运行时生成主类的代理类**；【默认】
	- JDK PROXY：通过**实现主类接口**，从而构造一个足以伪装成主类的代理类。
## 12. AOP是如何实现的？
基于**动态代理**实现的：在运行时动态生成代理对象。允许开发者在运行时指定要代理的接口和行为，从而实现在不修改源码的情况下增强方法的功能。

SpringAOP支持两种动态代理：
- **基于JDK的动态代理**：
	- 使用`Java.lang.reflect.Proxy`类和`Java.lang.reflect.InnovationHandler`实现，需要被代理类实现一个或多个接口；
		- 实现`InnovationHandler`接口，通过`invoke()`方法拦截目标对象的所有方法调用；使用`Proxy.newProxyInstance()`方法生成代理对象。
	- 通过**反射的方式**动态获取代理类。
- **基于CGLIB的动态代理**：
	- 当被代表的类**没有实现接口**时，会使用基于CGLIB的动态代理。**通过继承的方式，在运行时通过字节码技术动态创建子类实例作为代理类。**

## 13. 动态代理是什么？
在**运行时动态创建代理对象**的机制，主要用于在不修改原始类的情况下对方法调用进行拦截和增强，为原始类新增一些功能。
#### 基于接口的动态代理(JDK动态代理)：
使用`Java.lang.reflect.Proxy`类和`Java.lang.reflect.InnovationHandler`实现，**需要被代理类实现一个或多个接口**。
Java会创建一个**实现了相同接口的代理类，在运行时创建该类的实例**。当通过代理对象调用一个方法时，方法的调用会被转发为由InvocationHandler接口的`invoke()`方法进行调用。

#### 基于类的动态代理(CGLIB动态代理):
当被代表的类**没有实现接口**时，会使用CGLIB库通过**字节码技术动态生成一个被代理类的子类作为代理**。

CGLIB会在运行时动态生成一个目标类的子类。CGLIB通过继承的方式创建代理类。
## 14. AOP有什么注解？
- @Aspect：切面
- @Pointcut：切点
- @Before：方法执行前执行通知
- @After：方法执行后执行通知
- **@Around**：方法执行前后都执行通知
- @AfterReturning：方法执行返回结果后通知
- @AfterThrowing：方法抛出异常后执行通知
## 15. AOP什么时候会失效？
AOP生效，需要动态代理生效，且能调用到代理对象的方法。
1. **私有方法、类内部调用、内部类方法调用**：此时不会使用代理对象，而是直接通过this调用方法；
2. **静态方法**：直接通过类调用，不会调用代理对象；（AOP只能代理实例方法）
3. **final方法**：被`final`修饰的方法无法被所在类的子类重写；（CGLIB通过生成子类来实现代理）

## 16. 为什么Spring不建议使用基于字段的依赖注入？
强制依赖使用构造器注入，可选依赖使用setter注入。
- 单一职责问题：一个类的设计应该符合单一职责原则，使用字段注入可能在不自觉中就引入更多的依赖；
- 依赖完整性：确保所有必需依赖在对象创建时就被注入，避免了空指针异常的风险。
- 不可变性：有助于创建不可变对象，提高了线程安全性。
- 初始化保证：组件在使用前已完全初始化，减少了潜在的错误。
- 测试便利性：在单元测试中，可以直接通过构造函数传入模拟的依赖项，而不必依赖 Spring 容器进行注入。

## 17. 什么是循环依赖？
在Setter注入或字段注入时，A依赖B，B依赖A，导致A在实例化时需要创建B实例，而B在实例化时又要创建A实例。
![[Pasted image 20241208190639.png|450]]
## 18. 什么是Spring的三级缓存？
在`DefaultSingletonBeanRegistry`类中实现了BeanFactory接口，并且维护了三级缓存：
- 一级缓存`singletonObjects`：存放完整创建好的单例Bean对象；【完成实例化、初始化的Bean】
- 二级缓存`earlySingletonObjects`：存放尚未完全创建好的单例Bean对象【未完全初始化】；
- 三级缓存`singletonFactories`：存储的是单例Bean的创建工厂。【用于创建代理对象】
![[Pasted image 20241208191125.png]]
```java
public class DefaultSingletonBeanRegistry extends SimpleAliasRegistry implements SingletonBeanRegistry {
  //一级缓存，保存「完成的Bean对象」
  private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
  //三级缓存，保存「单例Bean创建工厂」
  private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);
  //二级缓存，存储「"半成品"的Bean对象」
  private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);
}
```
## 19. Spring是如何解决循环依赖的？
通过三级缓存机制解决的【本质是实例化和初始化是可以分开的，即初始化可延后】，但是无法解决非单例Bean以及构造注入的循环依赖问题。具体流程如下：
- 实例化A，发现需要属性B，并且从一级缓存中获取不到实例B，那么就先将实例A放到三级缓存中；
- 实例化B，发现需要属性A，此时从一级缓存与二级缓存中获取不到实例A，从三级缓存中获取到实例A的Bean工厂，创建实例A，并将创建好的实例A放到二级缓存中，并删除实例A的Bean工厂。至此，实例B初始化完成，加入到一级缓存中。
- B初始化完毕，实例A直接从一级缓存中获取实例B进行初始化，初始化完毕后将实例A加入到一级缓存中。
```java
@Nullable
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    // 首先从一级缓存中获取bean实例对象，如果已经存在，则直接返回
    Object singletonObject = this.singletonObjects.get(beanName);
    if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
        // 如果一级缓存中不存在bean实例对象，而且当前bean正在创建中，则从二级缓存中获取bean实例对象
        singletonObject = this.earlySingletonObjects.get(beanName);
        if (singletonObject == null && allowEarlyReference) {
            // 如果二级缓存中也不存在bean实例对象，并且允许提前引用，则需要在锁定一级缓存之前，
            // 先锁定二级缓存，然后再进行一系列处理
            synchronized (this.singletonObjects) {
                // 进行一系列安全检查后，再次从一级缓存和二级缓存中获取bean实例对象
                singletonObject = this.singletonObjects.get(beanName);
                if (singletonObject == null) {
                    singletonObject = this.earlySingletonObjects.get(beanName);
                    if (singletonObject == null) {
                        // 如果二级缓存中也不存在bean实例对象，则从三级缓存中获取bean的ObjectFactory，并创建bean实例对象
                        ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                        if (singletonFactory != null) {
                            singletonObject = singletonFactory.getObject();
                            // 将创建好的bean实例对象存储到二级缓存中
                            this.earlySingletonObjects.put(beanName, singletonObject);
                            // 从三级缓存中移除bean的ObjectFactory
                            this.singletonFactories.remove(beanName);
                        }
                    }
                }
            }
        }
    }
    return singletonObject;
}
```
![[Pasted image 20250220212012.png]]
## 20. 解决循环依赖一定需要三级缓存吗？ TODO
其实通过二级缓存也可以实现。基本思路就是将已实例化但未初始化的实例A放入二级缓存中，以便实例化B时能从二级缓存中获取实例A进行注入，完成初始化。

![[Pasted image 20250220214300.png]]
但是， 
## 21. 三级缓存是能解决所有循环依赖问题吗？ TODO
不能，无法解决以下两类循环依赖问题：
1. 使用构造器注入的循环依赖问题。
2. 非单例Bean的循环依赖问题。
## 22. ⭐Bean的生命周期(Hollis)
![[Pasted image 20241123151334.png]]
可粗略分为三个阶段：创建、使用、销毁。
也可进一步细分为：实例化、初始化、注册Destruction回调、Bean的使用、Bean的销毁。

## 23. Bean的初始化过程
![[Pasted image 20241123151820.png|500]]
初始化是在Bean实例化后，进行一些设置或准备工作，包括Bean属性的赋值、调用各种前置后置换处理器。

## 24. 介绍一下Bean的生命周期
Bean的生命周期分为5个阶段：**①实例化；②属性赋值；③初始化；④Bean的使用；⑤Bean的销毁。**

①实例化：Spring使用构造方法或工厂方法创建Bean的实例，此时为空对象，并未设置属性值；
②属性赋值：Spring将配置文件中的属性值或依赖的Bean注入到该Bean中；
③初始化：调用afterPropertiesSet方法，或通过配置文件中的init-method方法，完成初始化；
④Bean的使用；
⑤销毁：调用destory方法销毁。
![[Pasted image 20250113005405.png|300]]
![[Pasted image 20250113005638.png|300]]
![[Pasted image 20250113013019.png|300]]
**整个Bean的创建的过程都依赖于AbstractAutowireCapableBeanFactory这个类，而销毁主要依赖DisposableBeanAdapter这个类。**
## 25. 如何让Bean按顺序加载？
通过@**DependsOn注解**，可以指定某个Bean先加载。
## 26. Spring的事务传播机制
Spring的事务传播机制用于控制在多个事务方法相互调用时事务的行为，用于确保事务的一致性和完整性。
Spring规定了**7种**事务的传播机制，**默认为REQUIRED**。【一般就用默认的REQUIRED】
1. **REQUIRED：** 如果不存在，则开启事务；若存在，则加入之前已开启的事务，总是只有一个事务在执行；
2. **REQUIRES_NEW**：每次执行新开一个事务，如果当前已存在事务，则挂起当前事务；
	1. 新开事务（被调用方）抛异常会影响调用方；而调用方抛异常不会影响被调用方。
3. **SUPPORTS**：如果当前存在一个事务，则加入该事务；如果没有，则以非事务方式执行。
4. **NOT_SUPPORTED**：以非事务方式执行操作。如果当前存在一个事务，则将其挂起。
5. **MANDATORY**：要求当前存在一个事务。如果没有，则抛出异常。
6. **NEVER**：有事务则报异常；
7. **NESTED**：如果有事务，则创建嵌套事务。
	- **主事务回滚会影响子事务（即嵌套事务）；但嵌套事务回滚不会影响主事务**。

**如何开启事务？**
在方法上添加`@Transactional`注解。

[18个示例详解 Spring 事务传播机制-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/2226182)


## 27. ==Spring事务失效原因==
`@Transactional`声明式事务是基于Spring的AOP机制实现的，而AOP是基于动态代理实现的，因此动态代理失效，事务也就会失效。

#### 1. 非public使用@Transactional
```java
public class MyService {
    @Transactional
    private void doInternal() {
        System.out.println("Doing internal work...");
    }
}
```
private方法只会在当前类中的其他方法被调用，属于对象的自调用，**是通过this调用的，不会走代理对象**，因此AOP和事务都会失效。
#### 2. 同类的非事务方法调用事务方法
```java
public class MyService {
    public void doSomething() {
        doInternal(); // 自调用方法
    }

  	 @Transactional
    public void doInternal() {
        System.out.println("Doing internal work...");
    }
}
```
在类的非事务方法调用了事务方法，此时是类的内部调用，也是通过this实现的，不会走代理对象，因此AOP和事务都会失效。
#### 3. 事务的属性设置不当
```java
@Service
public class ExampleService {
    
    @Autowired
    private ExampleRepository repository;

    @Transactional(propagation = Propagation.NOT_SUPPORTED)
    public void methodA() {
        repository.someDatabaseOperation();
    }

    public void methodB() {
        repository.anotherDatabaseOperation();
    }
}


@Service
public class SomeService {
    
    @Autowired
    private ExampleService exampleService;

    @Transactional
    public void doSomething() {
        // 这里执行一些业务逻辑
        exampleService.methodA(); // 这个方法由于 NOT_SUPPORTED 属性，会在非事务下执行
        exampleService.methodB();
        // ...
    }
}
```
由于`methodA`使用了`NOT_SUPPORTED`注解，因此会将当前`doSomething()`方法开启的事务挂起，以非事务的方式执行`methodA`，因此即使后续抛异常导致回滚，`methodA`的操作也不会被回滚。
#### 4. 异常类型不匹配（注解属性`rollbackfor`设置错误）
`rollbackfor = xxExcepiton.class`属性设置错误【默认为`RuntimeException`和`Error`】。例如：
```java
public class MyService {
    @Transactional(rollbackFor = RuntimeException.class)
    private void doInternal() {
        System.out.println("Doing internal work...");
    }
}
```
由于此时设置的回滚异常类型是RuntimeException，因此若发生非RuntimeException，则该事务不会回滚。
#### 5. 事务中用了多线程
`@Transactional`的事务管理是通过ThreadLocal机制来存储事务上下文，而ThreadLocal变量是线程隔离的，因此多线程环境下会失效。

## 28. Spring常用注解
- @SpringBootApplication：标注主应用程序类，标识一个Spring Boot应用程序的入口点，同时启用**自动配置和组件扫描**。
	- @EnableAutoConfiguration自动配置：扫描类路径下的所有spring.factories文件，然后进行Bean的自动化配置。
	- @ComponentScan组件扫描：扫描被@Component注解标识的Bean。
- @Autowired：用于Bean的自动装配；
- @Configuration：标识配置类

- @PathVariable：用于从URL路径中提取变量；（`/items/123`）
- @RequestParam：用于从HTTP请求的查询参数中获取值；（`/items?category=books`）
- @RequestBody：用于获取请求体中的值。

- @RequestMapping：用于映射HTTP请求路径到Controller。【`@RequestMapping("/users/{userId})`代表以该路径结尾都会由该Controller进行处理】
	- @GetMapping、@PostMapping、@PutMapping、@DeleteMapping。

- @Component：通用的Spring组件注解，表示受Spring管理的组件；
	- @Controller：标识控制器类；
	- @Service：标识服务类；
	- @Repository：标识数据访问组件
## 29. Springboot Starter有什么优点？TODO

## 30. Spring中使用了什么设计模式？ TODO
#### 工厂模式

#### 单例模式

#### 代理模式

#### 模板方法模式

#### 观察者模式

#### 装饰器模式

#### 策略模式

## 31. MVC是什么？
Model-View-Controller，是一种软件设计模式。
## 32. SpringMVC是如何将不同的Request路由到不同Controller中的？


## 33. 过滤器和拦截器的区别是什么？
## 自动配置 TODO
自动扫描类路径下的类，将其注册为Bean，交由Spring容器管理。