# 一、Spring启示录
## OCP开闭原则
- （Open/Closed Principle)，对扩展开发，对修改关闭。在需要对接口进行功能他拓展时，应添加额外的类等，但不应该修改已正常运行的代码，否则会大大增加测试成本。
## DIP依赖倒置原则
### 思想：
- 高层模块不应该依赖低层模块，二者都应依赖于抽象接口；
- 抽象接口不应该依赖于具体实现；具体实现应该依赖于抽象接口。
	- 因为相对于细节的多变性，抽象往往比较稳定。抽象指的是接口或者抽象类，而细节就是具体的实现类。接口的作用就是指定好规范和契约，具体的实现及细节交给实现类来做。
### 实现：
- DIP原则实现的核心就是面向接口编程。降低代码的耦合度，提高代码的拓展力和复用性。

## IoC控制反转
### 思想：
- 将对象的创建权、对象与对象之间关系的管理权和依赖管理的控制权交由第三方容器来创建和维护。
### 实现方法：
- Dependency Injection，依赖注入。
	- Set方法注入
	- 构造方法注入
# 二、Spring概述
## 2.1 Spring简介
- Spring是一个**轻量级的控制反转(IoC)和面向切面(AOP)的容器框架**。 Spring最初的出现是为了解决EJB臃肿的设计，以及难以测试等问题。 Spring为简化开发而生，让程序员只需关注核心业务的实现，尽可能的不再关注非业务逻辑代码（事务控制，安全日志等）。*
## 2.2 Spring八大模块
![[SpringNote_SpringAllModules.png]]
### 1. Spring Core
- 最基础的部分，提供了IoC特征来实现容器对Bean的管理。核心容器的主要组件是 BeanFactory，BeanFactory是工厂模式的一个实现，是任何Spring应用的核心。它使用IoC将应用配置和依赖从实际的应用代码中分离出来。
### 2. Spring Context
- 这个模块扩展了BeanFactory，增加了对国际化（I18N）消息、事件传播、验证的支持。另外提供了许多企业服务，例如电子邮件、JNDI访问、EJB集成、远程以及时序调度（scheduling）服务。也包括了对模版框架例如Velocity和FreeMarker集成的支持
### 3. Spring AOP模块
- Spring在它的AOP模块中提供了对**面向切面编程**的丰富支持，Spring AOP 模块为基于 Spring 的应用程序中的对象提供了**事务管理服务**。通过使用 Spring AOP，不用依赖组件，就可以将声明性事务管理集成到应用程序中，可以自定义拦截器、切点、日志等操作。
### 4. Spring DAO模块
- 提供了一个JDBC的抽象层和异常层次结构，消除了烦琐的JDBC编码和数据库厂商特有的错误代码解析，用于简化JDBC。
## 2.3 Spring特点
### 1. 轻量级
- 完整Spring框架的大小只有1MB左右；
- Spring是非侵入式的：对象不依赖于Spring的特定类。
### 2. 控制反转
- Spring通过控制反转来实现松耦合（减少组件之间的依赖性和耦合度）。
### 3. 面向切面
- Spring框架在AOP模块中提供了对面向切面编程的丰富支持，允许**通过分离应用的业务逻辑和系统级服务来实现高内聚开发**。应用对象只完成业务逻辑，不负责其他的系统级关注点，例如日志或事务支持。
- 高内聚是模块内部各部分紧密相关，共同完成一个功能。低耦合是模块之间的依赖性尽量减少，减少模块间的相互影响。理想的设计应是**高内聚，低耦合。**
### 4. 容器
- Spring**包含并管理应用对象的配置和生命周期**，在这个意义上它是一种容器，你可以**配置你的每个bean如何被创建**——基于一个可配置原型（prototype），你的bean可以创建一个单独的实例或者每次需要时都生成一个新的实例(单例或多例)——以及它们是如何相互关联的。
### 5. 框架
- Spring可以将简单的组件配置、组合成为复杂的应用。在Spring中，应用对象被声明式地组合，典型地是在一个XML文件里。
# 三、Spring的入门程序
## 3.1 Spring的下载与安装
1. 打开[Maven Repository: Search/Browse/Explore (mvnrepository.com)](https://mvnrepository.com/)，搜索`org.springframework>>spring-context`，复制Maven依赖，导入xml文件中。
## 3.2 第一个Spring程序
### 1. 创建类
- 创建项目所需类文件，如`User`、`UserDao`、`UserService`等;
### 2. 配置Xml文件
- 首先在`src/main/resources`中创建`spring.xml`；
- 添加依赖：①spring框架依赖；②junit测试依赖。
```
~
<dependencies>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>6.0.11</version>
		<dependency>
	<dependency>  
	    <groupId>junit</groupId>  
	    <artifactId>junit</artifactId>  
	    <version>4.13.1</version>  
	    <scope>test</scope>  
	</dependency>
</dependencies>
```
- 定义bean
```
<beans>
	<bean id="userBean" class="com.chanxin.bean.User"/>
	<bean id="userDaoBean" class="com.chanxin.bean.UserDao"/>
	<bean id="userServiceBean" class="com.chanxin.bean.UserService/>
<beans>
```
- 获取bean对象：（通过传入`User.class`来获取User对象）
```
ApplicationContext applicationcontext = new ClassPathXmlApplicationContext("spring.xml");
User user = applicationcontext.getBean("userBean", User.class);


```
## 3.3 细节剖析
- 每个bean标签都有id和class属性，并且不可重复。在配置时可以不给bean标签的id属性传入值，此时默认的beanId为全类名！
- bean创建时底层会**使用反射机制调用默认无参构造器**来创建对象。因此，若在类中定义了有参构造器，必须主动再提供一个无参构造器，否则创建对象时会报错！
- Spring框架将创建好的对象存储在`Map<String, Object>`集合中，其中`key`为`beanid`，`value`为bean对象。
## 3.4 Spring6启用Log4j2日志框架
1. 导入依赖：去[[mvnrepository.com]]中搜索"log4j"，找到两个依赖:`Apache Log4j Core`和`Apache Log4j SLF4J Binding`并引入
```
<!--log4j2的依赖-->
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-core</artifactId>
  <version>2.19.0</version>
</dependency>
<dependency>
  <groupId>org.apache.logging.log4j</groupId>
  <artifactId>log4j-slf4j2-impl</artifactId>
  <version>2.19.0</version>
</dependency>
```
2. 在`src/main/resources`中提供`log4j2.xml`，文件名和路径均固定！
```
<?xml version="1.0" encoding="UTF-8"?>

<configuration>

    <loggers>
        <!--
            level指定日志级别，从低到高的优先级：
                ALL < TRACE < DEBUG < INFO < WARN < ERROR < FATAL < OFF
        -->
        <root level="DEBUG">
            <appender-ref ref="spring6log"/>
        </root>
    </loggers>

    <appenders>
        <!--输出日志信息到控制台-->
        <console name="spring6log" target="SYSTEM_OUT">
            <!--控制日志输出的格式-->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss SSS} [%t] %-3level %logger{1024} - %msg%n"/>
        </console>
    </appenders>

</configuration>
```
3. 指定日志级别：ALL < TRACE < DEBUG < INFO < WARN < ERROR < FATAL < OFF。常用级别为DEBUG、INFO、ERROR等。
4. 使用日志框架：①在需要使用的类中使用`LoggerFactory`实例化`logger`对象；②调用`info`方法打印信息。
 ```
Logger logger = LoggerFactory.getLogger(当前类的类名);
logger.info(~);
```
# 四、Spring对IoC的实现
## 4.1 IoC控制反转
- 控制反转是一种程序设计思想，通过将①对象的创建权；②对象与对象之间关系的管理权转移给第三方容器负责，降低程序耦合度，提高扩展力，达到OCP(开闭原则)、DIP(依赖倒置)原则。
- IoC控制反转通过**依赖注入**来实现。
## 4.2 依赖注入
- Spring框架通过依赖注入来实现Bean管理(Bean对象的创建以及属性赋值)，实现控制反转思想。
- **依赖：** 对象与对象之间的关系；**注入**：数据传递行为，通过注入来让对象之间产生关联。
- 常见的依赖注入方法：①Set注入；②构造注入。
### 4.2.1 Set注入
- 基于set方法实现，底层通过反射机制调用属性对应的set方法来给属性赋值。要求必须对外提供set方法。
- **配置方法**：使用 `property 标签。
```java
<beans>
	<bean id="userDaoBean" class="com.powernode.spring6.dao.UserDao"/>

    <bean id="userServiceBean" class="com.powernode.spring6.service.UserService">
        <property name="userDao" ref="userDaoBean"/>
    </bean>
	
</beans>
```
#### 原理解析
- 


# 六、GoF工厂模式