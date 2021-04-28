#### 1 什么是spring

Spring 是一种轻量级开发框架，旨在提高开发人员的开发效率以及系统的可维护性

我们一般说 Spring 框架指的都是 Spring Framework，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发。这些模块是：核心容器、数据访问/集成,、Web、AOP（面向切面编程）、工具、消息和测试模块。比如：Core Container 中的 Core 组件是Spring 所有组件的核心，Beans 组件和 Context 组件是实现IOC和依赖注入的基础，AOP组件用来实现面向切面编程。



#### 2 一些重要的模块

- **Spring Core：** 基础,可以说 Spring 其他所有的功能都需要依赖于该类库。主要提供 IoC 依赖注入功能。
- **Spring Aspects** ： 该模块为与AspectJ的集成提供支持。
- **Spring AOP** ：提供了面向切面的编程实现。
- **Spring JDBC** : Java数据库连接。
- **Spring JMS** ：Java消息服务。
- **Spring ORM** : 用于支持Hibernate等ORM工具。
- **Spring Web** : 为创建Web应用程序提供支持。
- **Spring Test** : 提供了对 JUnit 和 TestNG 测试的支持。

#### 谈一谈对 Spring IOC 和 Spring AOP 的理解

IOC

IoC（Inverse of Control:控制反转）是一种**设计思想**，就是 **将原本在程序中手动创建对象的控制权，交由Spring框架来管理。** IoC 在其他语言中也有应用，并非 Spring 特有。 **IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value）,Map 中存放的是各种对象。**

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 **IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的**



https://www.zhihu.com/question/23277575/answer/169698662

依赖倒置原则（下层控制上层） 

如果是上层建筑依赖下层建筑，（汽车 依赖 车身， 车身 依赖 底盘， 地盘 依赖 轮胎），就是在  汽车里 new 车身 ..... 

这样有个问题，仅仅是为了修改轮胎的构造函数，这种设计却需要**修改整个上层所有类的构造函数**！在软件工程中，**这样的设计几乎是不可维护的**



控制反转（IoC） 上层控制下层

我们用依赖注入（Dependency Injection）这种方式来实现控制反转。**所谓依赖注入，就是把底层类作为参数传入上层类，实现上层类对下层类的“控制**”。这里我们用**构造方法传递的依赖注入方式**重新写车类的定义：

这里我们是采用的**构造函数传入**的方式进行的依赖注入。其实还有另外两种方法：**Setter传递**和**接口传递**。这里就不多讲了，核心思路都是一样的，都是为了实现**控制反转**。





显然你也应该观察到了，因为采用了依赖注入，在初始化的过程中就不可避免的会写大量的new。这里IoC容器就解决了这个问题。**这个容器可以自动对你的代码进行初始化，你只需要维护一个Configuration（可以是xml可以是一段代码），而不用每次初始化一辆车都要亲手去写那一大段初始化的代码**。这是引入IoC Container的第一个好处。





IoC Container的第二个好处是：**我们在创建实例的时候不需要了解其中的细节。**在上面的例子中，我们自己手动创建一个车instance时候，是从底层往上层new的：

![img](https://pic2.zhimg.com/50/v2-555b2be7d76e78511a6d6fed3304927f_hd.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-555b2be7d76e78511a6d6fed3304927f_720w.jpg?source=1940ef5c)

这个过程中，我们需要了解整个Car/Framework/Bottom/Tire类构造函数是怎么定义的，才能一步一步new/注入。

而IoC Container在进行这个工作的时候是反过来的，它先从最上层开始往下找依赖关系，到达最底层之后再往上一步一步new（有点像深度优先遍历）：

![](https://pic4.zhimg.com/80/v2-24a96669241e81439c636e83976ba152_720w.jpg?source=1940ef5c)

这里IoC Container可以直接隐藏具体的创建实例的细节，在我们来看它就像一个工厂：

![img](https://pic4.zhimg.com/80/v2-5ca61395f37cef73c7bbe7808f9ea219_720w.jpg?source=1940ef5c)

我们就像是工厂的客户。我们只需要向工厂请求一个Car实例，然后它就给我们按照Config创建了一个Car实例。我们完全不用管这个Car实例是怎么一步一步被创建出来。

实际项目中，有的Service Class可能是十年前写的，有几百个类作为它的底层。假设我们新写的一个API需要实例化这个Service，我们总不可能回头去搞清楚这几百个类的构造函数吧？IoC Container的这个特性就很完美的解决了这类问题——**因为这个架构要求你在写class的时候需要写相应的Config文件，所以你要初始化很久以前的Service类的时候，前人都已经写好了Config文件，你直接在需要用的地方注入这个Service就可以了**。这大大增加了项目的可维护性且降低了开发难度。





自己理解

1 **所谓依赖注入，就是把底层类作为参数传入上层类，实现上层类对下层类的“控制**，ioc容器帮我们初始化各种类

依赖注入比直接new的好处是，直接new万一想修改一下构造方法（新增一个参数），需要每一层调用的地方都得新增一个参数，而依赖注入的话直接就是传入的底层对象，底层对象想咋改就咋改（随便增加参数），上层对象完全不用变，

2 **并且在创建实例时不用了解其中的细节**。

我们就像是工厂的客户。我们只需要向工厂请求一个Car实例，然后它就给我们按照Config创建了一个Car实例。我们完全不用管这个Car实例是怎么一步一步被创建出来。

就是说ioc容器会给你解决循环依赖，单例等问题，不用你操心



第一，资源集中管理，实现资源的可配置和易管理。第二，降低了使用资源双方的依赖程度，也就是我们说的耦合度





#### [AOP](https://snailclimb.gitee.io/javaguide/#/docs/system-design/framework/spring/Spring常见问题总结?id=aop)

AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，**却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来**，便于**减少系统的重复代码**，**降低模块间的耦合度**，并**有利于未来的可拓展性和可维护性**。

**Spring AOP就是基于动态代理的**，如果要代理的对象，实现了某个接口，那么Spring AOP会使用**JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候Spring AOP会使用**Cglib** ，这时候Spring AOP会使用 **Cglib** 生成一个被代理对象的子类来作为代理







### [4.2 Spring AOP 和 AspectJ AOP 有什么区别？](https://snailclimb.gitee.io/javaguide/#/docs/system-design/framework/spring/Spring常见问题总结?id=_42-spring-aop-和-aspectj-aop-有什么区别？)

**Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比Spring AOP 快很多。





### [5.2 Spring 中的单例 bean 的线程安全问题了解吗？](https://snailclimb.gitee.io/javaguide/#/docs/system-design/framework/spring/Spring常见问题总结?id=_52-spring-中的单例-bean-的线程安全问题了解吗？)

的确是存在安全问题的。因为，当多个线程操作同一个对象的时候，对这个对象的成员变量的写操作会存在线程安全问题。

但是，一般情况下，我们常用的 `Controller`、`Service`、`Dao` 这些 Bean 是无状态的。无状态的 Bean 不能保存数据，因此是线程安全的。

常见的有 2 种解决办法：

1. 在类中定义一个 `ThreadLocal` 成员变量，将需要的可变成员变量保存在 `ThreadLocal` 中（推荐的一种方式）。
2. 改变 Bean 的作用域为 “prototype”：每次请求都会创建一个新的 bean 实例，自然不会存在线程安全问题。



### [5.3 @Component 和 @Bean 的区别是什么？](https://snailclimb.gitee.io/javaguide/#/docs/system-design/framework/spring/Spring常见问题总结?id=_53-component-和-bean-的区别是什么？)

1. 作用对象不同: `@Component` 注解作用于类，而`@Bean`注解作用于方法。
2. `@Component`通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中（我们可以使用 `@ComponentScan` 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）。`@Bean` 注解通常是我们在标有该注解的方法中定义产生这个 bean,`@Bean`告诉了Spring这是某个类的示例，当我需要用它的时候还给我。
3. `@Bean` 注解比 `Component` 注解的自定义性更强，而且很多地方我们只能通过 `@Bean` 注解来注册bean。比如当我们引用第三方库中的类需要装配到 `Spring`容器时，则只能通过 `@Bean`来实现

### 5.4 将一个类声明为Spring的 bean 的注解有哪些?

1 @Autowired

2 @Component

3 @Repository

4 @Service

5 @Controller

6 @dao



##  Bean 生命周期（未完成）

- Bean 容器找到配置文件中 Spring Bean 的定义。
- Bean 容器利用 Java Reflection API 创建一个Bean的实例。
- 如果涉及到一些属性值 利用 `set()`方法设置一些属性值。
- 如果 Bean 实现了 `BeanNameAware` 接口，调用 `setBeanName()`方法，传入Bean的名字。
- 如果 Bean 实现了 `BeanClassLoaderAware` 接口，调用 `setBeanClassLoader()`方法，传入 `ClassLoader`对象的实例。
- 与上面的类似，如果实现了其他 `*.Aware`接口，就调用相应的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessBeforeInitialization()` 方法
- 如果Bean实现了`InitializingBean`接口，执行`afterPropertiesSet()`方法。
- 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
- 如果有和加载这个 Bean的 Spring 容器相关的 `BeanPostProcessor` 对象，执行`postProcessAfterInitialization()` 方法
- 当要销毁 Bean 的时候，如果 Bean 实现了 `DisposableBean` 接口，执行 `destroy()` 方法。
- 当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法







实例化Bean

设置对象属性

检查是否实现了 Aware 相关接口并调用对应方法

BeanPostProcessor 前置处理

调用初始化方法

BeanPostProcessor 后置处理

使用

销毁



## Spring MVC



**流程说明（重要）：**

1. 客户端（浏览器）发送请求，直接请求到 `DispatcherServlet`。
2. `DispatcherServlet` 根据请求信息调用 `HandlerMapping`，解析请求对应的 `Handler`。
3. 解析到对应的 `Handler`（也就是我们平常说的 `Controller` 控制器）后，开始由 `HandlerAdapter` 适配器处理。
4. `HandlerAdapter` 会根据 `Handler `来调用真正的处理器来处理请求，并处理相应的业务逻辑。
5. 处理器处理完业务后，会返回一个 `ModelAndView` 对象，`Model` 是返回的数据对象，`View` 是个逻辑上的 `View`。
6. `ViewResolver` 会根据逻辑 `View` 查找实际的 `View`。
7. `DispaterServlet` 把返回的 `Model` 传给 `View`（视图渲染）。
8. 把 `View` 返回给请求者（浏览器）





spring 用了哪些设计模式



- **工厂设计模式** : Spring使用工厂模式通过 `BeanFactory`、`ApplicationContext` 创建 bean 对象。
- **代理设计模式** : Spring AOP 功能的实现。
- **单例设计模式** : Spring 中的 Bean 默认都是单例的。
- **模板方法模式** : Spring 中 `jdbcTemplate`、`hibernateTemplate` 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。
- **包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
- **观察者模式:** Spring 事件驱动模型就是观察者模式很经典的一个应用。
- **适配器模式** :Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配`Controller`









事务的传播行为

支持当前事务的：

当前有事务，直接使用，否则创建新事务

当前有事务，直接使用，否则已非事务的运行

当前有十五，直接使用，否则抛异常



不支持当前事务

创建新事务，当前有事务，把当前事务挂起

以非事务方式运行，当前有事务，挂起

以非事务方式运行，当前有事务，抛异常



当前有事务，创建一个新事务嵌套运行，否则，创建一个新事务运行







![image-20210401101645519](C:\Users\v_zhimingying\AppData\Roaming\Typora\typora-user-images\image-20210401101645519.png)