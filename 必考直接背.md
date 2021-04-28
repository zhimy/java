# 高频考点  没有解释只有结论

记录一下自己理解的内容，不理解的话去自行百度



# java 基础（暂未）

有必要？

## bio、nio、aio

java  中的三种IO模型

bio 同步阻塞i/o流

nio 同步非阻塞i/o流

aio 异步非阻塞i/o流



io 可以分为两步， 等待数据；拷贝数据

**阻塞 **指的是在等待数据过程中要一直等在这

**同步** 指的是真正的数据拷贝过程是同步的



在Linux(UNIX)操作系统中，共有五种IO模型，分别是：**阻塞IO模型**、**非阻塞IO模型**、**IO复用模型**、**信号驱动IO模型**以及**异步IO模型**

前4种IO模型都是同步的IO模型。原因是因为，无论以上那种模型，真正的数据拷贝过程，都是同步进行的。

https://mp.weixin.qq.com/s?__biz=Mzg3MjA4MTExMw==&mid=2247484746&idx=1&sn=c0a7f9129d780786cabfcac0a8aa6bb7&source=41&scene=21#wechat_redirect



![图片](https://mmbiz.qpic.cn/mmbiz_png/mQlO20PgUDLJyNAPpmHXFWjrXZ2uXvSeqeQfjNIVKzKA4lJUFPDKUic0FiayuEXticzTtnFPN74Y7poNjZbV0DygQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## coding

### 1

```java
public class Main {

    static class Father{

        public int money = 1;
        public Father(){
            money = 2;
            showMoney();
        }

        public void showMoney(){
            System.out.println("father: money:" + money);
        }
    }

    static class Son extends Father{

        public int money = 3;
        private Son(){
            money = 4;
            showMoney();
        }

        public void showMoney(){
            System.out.println("son: money:" + money);
        }
    }

    public static void main(String[] args) {
        Father son = new Son();
        System.out.println("main： money：" + son.money);
    }
}

结果 ：
son: money:0
son: money:4
main： money：2
    
如果  Son son = new Son();   
结果 ： 0 4 4
```

### 2 java类执行顺序

　   1、父类的静态变量和静态块赋值（按照声明顺序）
　　2、自身的静态变量和静态块赋值（按照声明顺序）
　　3、main方法
　　3、父类的成员变量和块赋值（按照声明顺序）
　　4、父类构造器赋值
　　5、自身成员变量和块赋值（按照声明顺序）
　　6、自身构造器赋值
　　7、静态方法，实例方法只有在调用的时候才会去执行





# 网络

## TCP 如何保证传输的可靠性

TCP主要提供了检验和、序列号/确认应答、超时重传、最大消息长度、滑动窗口控制、阻塞控制等方法实现了可靠性传输。



超时重传的机制存在效率低下的问题可以用滑动窗口来优化

滑动窗口：窗口的大小就是在无需等待确认包的情况下，发送端还能发送的最大数据量。这个机制的实现就是使用了大量的缓冲区

阻塞控制：如果出现网络拥堵就开启“慢启动”，先发少量的数据“探路”，如果网络稳定就按正常的速度传输。

## TCP 三次握手四次挥手

### 三次握手：

客户端               SYN=1     seq=a   ->                       服务端

​                <-   SYN=1   ACK=1   ack=a+1   seq=b                                

​                   ACK=1   ack=b+1   seq=a+1        -> 



举个例子：

A 和 B 打电话，A说：听得到吗？B说：听得到，你呢？A说：我也听到了。然后才开始真正对话



### 四次挥手：

客户端                  FIN=1    seq=a    ->                              服务端

​                         <-   ACK=1   ack=a+1      seq=b

​                       <-    FIN=1    ACK=1    ack=a+1   seq=c

​                         ACK=1  ack=c+1    seq=a+1 ->

发起方等待2MSL(最长报文寿命)后close



举个例子：

A 和 B 打电话，通话即将结束后，A 说“我没啥要说的了”，B回答“我知道了”，但是 B 可能还会有要说的话，A 不能要求 B 跟着自己的节奏结束通话，于是 B 可能又巴拉巴拉说了一通，最后 B 说“我说完了”，A 回答“知道了”，这样通话才算结束



### 为什么关闭连接要四次挥手，比握手多一次？

因为关闭的时候接收方可能还有数据未传输完毕，所以回复**接收到关闭通知**和**数据传输完毕**分两次发送

而建立连接的时候不存在消息没传完的情况，很明确的知道从哪里开始接收报文



## 从输入URL到页面加载发生了什么

1. **DNS解析**：把域名解析成ip端口
2. **TCP连接**：HTTP协议是使用TCP作为其传输层协议的，将http请求报文分割成报文段，按序号发送
3. **发送HTTP请求**：cookies会随着请求发送给服务器
4. **服务器处理请求并返回HTTP报文**
5. **浏览器解析渲染页面**
6. **连接结束**



HTTP报文是包裹在TCP报文中发送的，服务器端收到TCP报文时会解包提取出HTTP报文。但是这个过程中存在一定的风险，HTTP报文是明文，如果中间被截取的话会存在一些信息泄露的风险。那么在进入TCP报文之前对HTTP做一次加密就可以解决这个问题了。HTTPS协议的本质就是HTTP + SSL(or TLS)。在HTTP报文进入TCP报文之前，先使用SSL对HTTP报文进行加密。



#  spring（未完成）



## 基础

spring bean 的 scope有：

`singleton` 单例，容器只会创建一个实例，并且创建后会放入缓存中，是无状态的bean

`prototype` 原型，每次请求都会生成一个新实例，有状态的bean

`request` 每次http请求都会生成一个

`session` 在一个http session 中，只有一个



## 对 IOC 、DI 的理解 

**控制反转( Inversion of Control )**

**依赖注入（Dependency Inversion）**



### 概念

控制反转是一种理念，依赖注入是实现这种理念的设计模式



我们用依赖注入（Dependency Injection）这种方式来实现控制反转。**所谓依赖注入，就是把底层类作为参数传入上层类，实现上层类对下层类的“控制**”



### 理解：

使用造车的例子 - - 

直接new对象的时候：上层依赖下层

汽车依赖车身，车身依赖底盘，底盘依赖轮子

我们是根据轮子的尺寸设计的底盘，轮子的尺寸一改，底盘的设计就得修改；同样因为我们是根据底盘设计的车身，那么车身也得改，同理汽车设计也得改——整个设计几乎都得改



依赖注入：下层依赖上层

轮子依赖底盘， 底盘依赖车身， 车身依赖汽车。

我们先设计汽车的大概样子，然后根据汽车的样子来设计车身，根据车身来设计底盘，最后根据底盘来设计轮子



### 好处：

业务逻辑解耦，之前是在上层业务类中创建下层业务类，代码耦合度很高，引入spring后，创建和维护对象的工作交给了spring ioc容器，实现了解构，提高了系统的可维护性。并且spring还根据业务维护的bean对象的单例，减少了大量对象的重复创建，提高了系统的性能。解决了对象之间调用的循环依赖，如果没有spring这些都需要我们自己来完成。





## 1 IOC容器启动过程

13还是12个方法 refresh()？？？

## 2 spring bean 加载过程



## 循环依赖

构造器注入无法解决，setter方法注入可以解决，能解决循环依赖最主要的原因是对象的**实例化**和**初始化**(填充属性、执行初始化方法)是分开的

只有单例可以解决循环依赖，如果bean类型时原型会报错的

三级缓存是：

singletonObjects、earlySingletonObjects、singletonFactories

分别装的成品对象、半成品对象、**lambda表达式（getEarlyBeanReference() 判断bean是否需要被代理，如果需要代理返回AOP织入后的bean）**



**总结**：

1 Spring 创建bean分为两个步骤，实例化和初始化（填充属性、执行初始化方法）

2 每次创建bean之前 我们都会从缓存查下有没有 bean，因为是单例，只能有一个

3 当我们实例化beanA后会把他放入第三级缓存中，接下来就要填充属性，因为依赖beanB，接着去创建beanB，同样的流程，实例化beanB，接着发现他依赖beanA

**不同的是**：

这个时候可以在第三级缓存查询到beanA（也就是调用上面那个lambda表达式，检查是否需要代理最后得到bean，放入二级缓存中）用它注入beanB，然后beanB创建完成addSingleton添加到一级缓存中

4 到此beanB创建好了，a也填充属性完毕，a执行初始化之后，将二级缓存得到a的bean，最后addSingleton把a添加到一级缓存中



关键方法：

getSingleton  、doCreateBean、 populateBean 、 addSingleton



## AOP 相关

主要用到了动态代理模式、适配器模式



AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，**却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来**，便于**减少系统的重复代码**，**降低模块间的耦合度**，并**有利于未来的可拓展性和可维护性**。

**Spring AOP就是基于动态代理的**，如果要代理的对象，实现了某个接口，那么Spring AOP会使用**JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候Spring AOP会使用**Cglib** ，这时候Spring AOP会使用 **Cglib** 生成一个被代理对象的子类来作为代理



### 动态代理 JDK、CGlib

jdk 需要实现接口

cglib会生成一个子类，然后去增强子类，性能没有jdk高



### 在哪块调用

一般会触发在bean执行初始化方法的前后(before、after方法)

如果有循环依赖会在 getEarlyBeanRenfxxxxx() 中判断是否需要代理



### aop 顺序

spring 4

循环前、before、循环后、after、调用

spring 5

循环前、before、调用、after、循环后



## 3 spring mvc 执行过程



第一步：用户发起请求到前端控制器（DispatcherServlet）
第二步：前端控制器请求处理器映射器（HandlerMappering）去查找处理器（Handle）：通过xml配置或者注解进行查找
第三步：找到以后处理器映射器（HandlerMappering）像前端控制器返回执行链（HandlerExecutionChain）
第四步：前端控制器（DispatcherServlet）调用处理器适配器（HandlerAdapter）去执行处理器（Handler）
第五步：处理器适配器去执行Handler
第六步：Handler执行完给处理器适配器返回ModelAndView
第七步：处理器适配器向前端控制器返回ModelAndView
第八步：前端控制器请求视图解析器（ViewResolver）去进行视图解析
第九步：视图解析器向前端控制器返回View
第十步：前端控制器对视图进行渲染
第十一步：前端控制器向用户响应结果





## 用到了哪些设计模式



## spring 事务

Spring 事务管理分为编程式和声明式两种。编程式事务指的是通过编码方式实现事务；声明式事务基于 AOP，将具体的逻辑与事务处理解耦

声明式事务有两种方式，一种是在配置文件（XML）中做相关的事务规则声明，另一种是基于 `@Transactional` 注解的方式



只有目标方法由外部调用，才能被 Spring 的事务拦截器拦截。



### 事务的隔离级别 

比 mysql 的隔离级别多了个默认数据库自己的隔离级别

- Isolation.DEFAULT：使用底层数据库默认的隔离级别
- Isolation.READ_UNCOMMITTED：读取未提交数据（会出现脏读，不可重复读）基本不使用
- Isolation.READ_COMMITTED：读取已提交数据（会出现不可重复读和幻读）
- Isolation.REPEATABLE_READ：可重复读（会出现幻读）
- Isolation.SERIALIZABLE：串行化

### 事务的传播机制

默认值为 Propagation.REQUIRED

- PROPAGATION.REQUIRED：如果当前没有事务，则创建一个新事务。如果当前存在事务，就加入该事务。该设置是最常用的设置。
- PROPAGATION.SUPPORTS：支持当前事务，如果当前存在事务，就加入该事务。如果当前不存在事务，就以非事务执行。
- PROPAGATION.MANDATORY：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。
- PROPAGATION.REQUIRE_NEW：创建新事务，无论当前存不存在事务，都创建新事务。
- PROPAGATION.NOT_SUPPORTED：以非事务方式执行操作，如果当前事务存在，就把当前事务挂起。
- PROPAGATION.NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。
- PROPAGATION.NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按 REQUIRED 属性执行



支持当前事务：

必须(required)：没有就new

支持(supports)：没有就不用

强制(mandatory)：抛异常



不支持当前事务：

必须新建(required new)：必须new

不支持(not supported)：有就挂起

决不(never)：有就抛异常

嵌套(nested)：外层影响里层，里层不影响外层



#### 总结

Spring 事务传播机制总共有 7 种，其中使用最多的应该是 PROPAGATION_REQUIRES、PROPAGATION_REQUIRES_NEW 和 PROPAGATION_NESTED。

其中所谓的嵌套事务，是指外层的事务如果回滚，会导致内层的事务也回滚；但是内层的事务如果回滚，仅仅是滚回自己的代码。



比如现在有一段业务代码，方法 A 调用方法 B，我希望的是如果方法 A 出错了，此时仅仅回滚方法 A，不能回滚方法 B，这个时候可以给方法 B 使用 REQUIRES_NEW 传播机制，让他们两的事务是不同的。

如果方法 A 调用方法 B，如果出错，方法 B 只能回滚它自己，方法 A 可以带着方法 B 一起回滚。那这种情况可以给方法 B 加上 NESTED 嵌套事务。



# 并发（暂未完成）

## 线程池（暂未完成）

public ThreadPoolExecutor(int corePoolSize,  // 核心线程数
                              int maximumPoolSize,  // 最大线程数
                              long keepAliveTime,  // 空闲存活时间
                              TimeUnit unit,  // 空闲存活时间单位
                              BlockingQueue<Runnable> workQueue,  // 阻塞队列
                              ThreadFactory threadFactory,  // 线程工厂
                              RejectedExecutionHandler handler) // 拒绝策略



阻塞队列种类



拒绝策略



项目中使用







## 集合(姑且放这里吧)（暂未完成）



## HashMap







###  基础(有必要吗?)





### 源码解读





# 锁/同步器（暂未完成）

## volatile

JMM java线程模型的特性

原子性、可见性、有序性

volatile可以保证可见性和有序性，保证不了原子性，如果需要保证原子性就用锁吧

主内存和线程的本地内存

线程必须在自己的本地内存内读写，不能直接在主内容中读写这个时候volatile可以保证各个线程之间对共享变量的可见性

主线嗅探技术，一直去嗅探主内存对应的对象地址是否发生变化



volatile 也可以禁止指令重排

### 双重检查单例模式





## CAS

compareAndSweep

俗称乐观锁



调用了 unsafe 类里相关的 native 方法



问题

ABA 问题，引入版本

争抢太激烈会出现一直自旋的问题，损耗cpu性能

## synchronized

和下面那个称作悲观锁

java6做过优化，性能已经不比lock锁差了，很多框架源码里面都用的synchronized

但是呢，不能主动释放锁，不像lock有丰富的API，没那么灵活



他和lock一样，非公平只是比公平多两次抢锁机会，如果没抢到就去乖乖排队了（只不过是后来的排在最前面，有点奇怪）

证明

```java
    static void a() throws Exception {
        new Thread(() -> {
            synchronized (demo) {
                System.out.println("开始");
                try { Thread.sleep(10 * 1000); } catch (InterruptedException e) { e.printStackTrace(); }
            }
        }).start();

        for (int i = 0; i < 10; i++) {
            Thread.sleep(100);
            new Thread(() -> {
                synchronized (demo) {
                    System.out.println(Thread.currentThread().getName());

                }
            }, "i" + i).start();
        }
    }
```



### synchronized 三种用法

1 修饰实例方法

2 修饰静态方法

3 修饰代码块



### 原理

synchronized 同步语句块的实现使⽤的是 monitorenter 和 monitorexit 指令，其中monitorenter 指令指向同步代码块的开始位置， monitorexit 指令则指明同步代码块的结束位置。
当执⾏ monitorenter 指令时，线程试图获取锁也就是获取 对象监视器 monitor 的持有权。

在执⾏ monitorenter 时，会尝试获取对象的锁，如果锁的计数器为 0 则表示锁可以被获取，获取 后将锁计数器设为 1 也就是加 1。 在执⾏ monitorexit 指令后，将锁计数器设为 0，表明锁被释放。如果获取对象锁失败，那当前 线程就要阻塞等待，直到锁被另外⼀个线程释放为⽌。



## TheadLocal

### 有什么用

用来解决线程安全问题

### 原理

它通过为每个线程提供一个独立的变量副本解决了变量并发访问的冲突问题。在很多情况下，ThreadLocal比直接使用synchronized同步机制解决线程安全问题更简单，更方便



### 主要方法

get() 、set(T value) 、remove()

**get() 方法会调用 getMap(t) ，从而拿到当前 Thread 中的 threadLocals 对象，也就是说 TheadLocal 变量是存储在 Thread 中的。**



Thread中的threadLocals 变量的类型是 ThreadLocalMap<ThreadLocal<?> k, Object v> （其实里面还有一层Entry）

因为一个类中可能有多个TheadLocal 变量，所以threadLocals 这个map的key是ThreadLocal对象



### 有啥缺点

可能会导致内存泄露，因为他的key是弱引用，会在GC的时候被回收，而value是强引用，如果不手动释放内存则无法被回收。



为啥key使用弱引用

**ThreadLocalMap的生命周期跟Thread一样长，如果都没有手动删除对应key，都会导致内存泄漏，但是使用弱引用可以多一层保障：弱引用ThreadLocal不会内存泄漏**



### 和 HashMap 中解决hash冲突的方法有啥不一样

ThreadLocalMap中解决Hash冲突的方式并非链表的方式，而是采用线性探测的方式。

所谓线性探测，就是根据初始key的hashcode值确定元素在table数组中的位置，如果发现这个位置上已经有其他key值的元素被占用，则利用固定的算法寻找一定步长的下个位置，依次判断，直至找到能够存放的位置。


为啥这样用？

ThreadLocalMap 里数据量不会很大；线性探测相对简单



### 项目中有没有用到？

在redis分布式锁中用到了，用ThreadLocal来记录当前线程加锁的value，需要在释放锁的时候对redis中的值和该值进行比较来判断是不是自己的锁

## lock

这是一个接口，常用的实现是 ReentrantLock

## AQS 原理

### LockSupport 

LockSupport 是一个线程阻塞工具类，底层调用了 unsafe 类native方法（park方法）

LockSupport 的 park/unpark 比wait/notify和Lock的await/？的优势是 1、不需要同步块也能使用；2、可以先先唤醒再等待



LockSupport 维护了一个许可证permit，值是一个信号量（0,1）

permit默认为0，调unpark方法会使permit + 1，调park方法会使permit - 1

这个许可证最多只有1个

当调用park方法时如果有许可证则消耗许可证正常退出，否则线程阻塞



## 原理

AQS是用来构建锁或者其他同步器组件的重量级基础框架及整个juc体系的基石

AQS使用一个volatile的int类型的成员变量来表示同步状态，通过内置的FIFO队列来完成资源获取的排队工作将每条要去抢占资源的线程封装成一个Node节点来实现锁的分配，通过CAS完成对State值得修改



AQS 里面有个内部静态类 Node ，这个node用来包装thread，如果队列是空，则会调用初始化方法，生成一个空的head节点，并且将尾节点也指向head



非公平锁和公平锁一样，都需要排队去获得锁，只不过非公平锁有两次直接抢锁的机会，一次是刚开始得到任务的时候，一次是在加入队列前

**抢锁就是使用cas去给state字段赋值**

如果一个线程没抢到锁，就会被封装成一个Node节点然后调用park去阻塞，等待有人通过unpark去唤醒他

添加Node过程中也是cas来保证顺序（线程安全）的。



### AQS 使用了模板⽅法模式

⾃定义同步器时需要重写下⾯⼏个 AQS 提供的模板⽅法：

```
isHeldExclusively()//该线程是否正在独占资源。只有⽤到condition才需要去实现它。
tryAcquire(int)//独占⽅式。尝试获取资源，成功则返回true，失败则返回false。
tryRelease(int)//独占⽅式。尝试释放资源，成功则返回true，失败则返回false。
tryAcquireShared(int)//共享⽅式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可⽤资
源；正数表示成功，且有剩余资源。
tryReleaseShared(int)//共享⽅式。尝试释放资源，成功则返回true，失败则返回false。
```





### ⽤过 CountDownLatch 么？什么场景下⽤的？

CountDownLatch 的作⽤就是 允许 count 个线程阻塞在⼀个地⽅，直⾄所有线程的任务都执⾏完 毕。

```
final CountDownLatch countDownLatch = new CountDownLatch(threadCount);
... 

try {
//处理⽂件的业务操作
......
 } finally {
 countDownLatch.countDown();//表示⼀个⽂件已经被完成
 }
```

可以使⽤ CompletableFuture 类来改进

```
CompletableFuture.supplyAsync(()->{//⾃定义业务操作});
```

在使用CompletableFuture的时候可以指定线程池，出现问题时更方便我们去定位

```
CompletableFuture.supplyAsync(() -> doCheckHeart(tableCode), commonPool);
```





# redis（暂未完成）

## 原理

文件事件处理器分为四部分：客户端（socket连接）、IO多路复用程序（用来监听多个socket）、文件事件分派器、事件处理器



使用的多路复用IO模型来监听socket连接，处理各类型事件（执行命令）是使用的单线程



### redis 使用多线程

#### 1

4.0新增的多线程主要是在删除大键值对的时候进行异步删除



不使用多线程的主要原因是

1 cpu不是redis的瓶颈

2 单线程编程容易并且好维护

3 多线程会有死锁、线程上下文切换等问题，可能回影响性能

#### 2

6.0之后引入多线程

Redis6.0 引⼊多线程主要是为了提⾼⽹络 IO 读写性能，执行命令仍然是单线程顺序执行











### 一些题目

1 安装 redis6.0.8

2 数据类型的实践

3 分布式锁及要注意的地方

4 redis 缓存过期淘汰策略

5 LRU

## 数据类型

原始  string hash list set  zset

新的  bitmap      hyperloglog      GEO     stream



各自的适用场景



### 缓存雪崩

针对 Redis 服务不可⽤的情况：

1. 采⽤ Redis 集群，避免单机出现问题整个缓存服务都没办法使⽤。 

2. 限流，避免同时处理⼤量的请求。 

   

试试针对热点缓存失效的情况： 

1. 设置不同的失效时间⽐如随机设置缓存的失效时间。
2. 缓存永不失效。



### 缓存穿透

1 缓存无效key

2 布隆过滤器

### 布隆过滤器

将数据使用多个哈希函数哈希后放入为数组中

在布隆过滤器中查到的数据不一定存在，如果查不到的肯定不存在



位数组中的每个元素都只占用 1 bit ，并且每个元素只能是 0 或者 1。这样申请一个 100w 个元素的位数组只占用  1000000 / 8 = 125000 B = 15625 byte ≈ 15.3kb 的空间



布隆过滤器-》redis缓存-》mysql

## RDB AOF 持久化比较

**RDB(Redis DataBase)**

**AOF(Append Only File)**



**1  文件大小**   AOF 比 RDB 大

**2 恢复速度**    RDB 恢复数据比AOF快

**3 优先级**      如果同时开始两者时优先使用AOF恢复，AOF 文件所保存的数据通常是最完整的。

**4 文件类型**    AOF是写命令append（追加）生成的日志文件，RDB是某一时间的全量数据的二进制文件（数据快照）

**5 数据安全**    RDB 可能会丢几分钟的数据，而一般AOF只会丢一秒的，如果是设置的每秒同步的话

**6 性能**    AOF 会影响redis的QPS，但是开启AOF后性能也是很高的

**7  适合的地方**  RDB 文件紧凑，适合灾难恢复，也适合数据库备份，AOF比较完整会优先使用，并且有误操作时可以在aof文件中删除那条命令后进行恢复

**8 各自的实现方式**

 RDB 是 fork()一个子进程去做持久化功能，子进程完成进的RDB文件后会删除旧的，每次fork的成本比较大，所以一般几分钟持久化一次。（有三种方式触发RDB，save、bgsave、自动触发，一般设置自动触发，触发后执行bgsave命令）

AOF 日志文件会每秒（一般设置每秒持久化一次）将写命令和删除命令追加到文件末尾，并且在即将过大的时候会在后台做重写操作(BG REWRITE AOF)，重写不会读取旧的AOF文件，重写后的新 AOF 文件包含了恢复当前数据集所需的最小命令集合

**9 开启方式**

```
# aof
appendonly yes
appendfsync everysec
# rdb
 save 60 1000
#这个设置会让 Redis 在满足“ 60 秒内有至少有 1000 个键被改动”这一条件时， 自动保存一次数据集
```

10 一般情况下 **aof和rdb会同时开启**，相互配合在完成数据的持久化





## redis 分布式锁





## 缓存过期策略

三种过期键删除策略

定时、惰性、定期（两种结合使用）



八种缓存删除策略

lru x2、lfu x2、随机 x2、报错、删过期时间最短的



最常使用 allkeys-lru



## 手写lru

投机可以使用 LinkedHashMap来实现，LinkedHashMap 天然支持



手写的话使用 HashMap + DoubleLinkedList

hashMap 查询速度快

DoubleLinkedList 写入和删除的速度快（只要get或put某个元素就把它放到head）



## 主从同步过程（暂未完成）





## 跳跃表



#### 原理：

跳跃表（skiplist）是一种有序数据结构， 它通过在每个节点中维持多个指向其他节点的指针， 从而达到快速访问节点的目的



跳跃表的实现还是一个链表，是一个有序的链表，在遍历的时候基于比较，而普通链表只能遍历，跳跃表加入了一个层的概念（也就是索引），层数越高的元素越少，每次先从高层查找，再逐渐降层，直到找到合适的位置。

高层的节点远远少于底层的节点数，从而实现了跳跃式查找



#### 空间复杂度：

跳跃表支持平均 O(log N) 最坏 O(N) 复杂度的节点查找 

**和红黑树相比较**

最差时间复杂度要差很多，红黑树是O(nlogn)，而跳跃表是O(n)

平均时间复杂度是一样的

实现要简单很多



#### 哪些场景适用：

 Redis使用跳跃表作为 `有序集合键` 的底层实现之一,如果一个有序集合包含的**元素数量比较多**,又或者有序集合中元素的**成员是比较长的字符串**时, Redis就会使用跳跃表来作为有序集合健的底层实现。



#### 为什么：

索引是占内存的。原始链表中存储的有可能是很大的对象，而索引结点只需要存储关键值值和几个指针，并不需要存储对象，因此当节点本身比较大或者元素数量比较多的时候，其优势必然会被放大，而缺点则可以忽略。





# JVM（暂未完成）

## 垃圾回收 gc



### 垃圾回收算法

复制、标记清除、标记整理



### 垃圾收集器

年轻代  Serial、ParNew、Parallel

老年代  Serial Old、Parallel Old、CMS

g1



### 如何判断对象死亡

引用计数法

可达性分析算法：如果对象到GC Roots是不可达的，就认定为此对象可回收

可作为GC Roots的对象包括：虚拟机栈中引用的对象、java堆类静态属性引用的对象、方法区中常量引用的对象、本地方法栈中JNI引用的对象



### 年轻代、老年代

年轻代、幸存者s0、幸存者s1、老年代    对应比例 (8:1:1):20

默认15岁进到老年代，除非 

1> 某些大对象之间进入;  2> 如果s1放不下了也会直接进入



一般年轻代使用 复制算法

老年代使用 标记整理、标记清除（cms）





### 常用收集器开启和配合情况



`-XX:+UseSerialGC`，虚拟机在Client模式下的默认值，Serial+Serial Old

`-XX:+UseParNewGC`，ParNew+Serial Old，在JDK1.8中已经不推荐使用并且将被移除

`-XX:+UseConcMarkSweepGC`，ParNew+CMS+Serial Old（CMS执行失败时会切换到Serial Old）

`-XX:+UseParallelGC` ，虚拟机在Server模式下的默认值，Parallel Scavenge+Serial Old

`-XX:+UseParallelOldGC` ，Parallel Scavenge+Parallel Old

`-XX:+UseG1GC`，G1 Young Generation+G1 Old Generation



CMS 是标记清除算法，所以会有大量内存碎片，如果内存满了之后CMS会报错，就会使用后备收集器Serial Old

在java1.8中设置 -XX:+UseParallelGC 和 -XX:+UseParallelOldGC 老年代都会使用Parallel Old



#### 常用命令

xms

xmx

xmn

xss

-XX:NewRatio 老年/新生代的比例

-XX:SurvivorRatio 新生代/幸存者s0的比例

-XX:+PrintGCDetails



调优目的

将转移到老年代的对象数量降低到最小； 减少 GC 的执行时间。



调优策略

**策略 1：**将新对象预留在新生代，由于 Full GC 的成本远高于 Minor GC，因此尽可能将对象分配在新生代是明智的做法，实际项目中根据 GC 日志分析新生代空间大小分配是否合理，适当通过“-Xmn”命令调节新生代大小，最大限度降低新对象直接进入老年代的情况。

**策略 2：**大对象进入老年代，虽然大部分情况下，将对象分配在新生代是合理的。但是对于大对象这种做法却值得商榷，大对象如果首次在新生代分配可能会出现空间不足导致很多年龄不够的小对象被分配的老年代，破坏新生代的对象结构，可能会出现频繁的 full gc。因此，对于大对象，可以设置直接进入老年代（当然短命的大对象对于垃圾回收来说简直就是噩梦）。`-XX:PretenureSizeThreshold` 可以设置直接进入老年代的对象大小。

**策略 3：**合理设置进入老年代对象的年龄，`-XX:MaxTenuringThreshold` 设置对象进入老年代的年龄大小，减少老年代的内存占用，降低 full gc 发生的频率。

**策略 4：**设置稳定的堆大小，堆大小设置有两个参数：`-Xms` 初始化堆大小，`-Xmx` 最大堆大小。



## JAVA 内存区域

线程私有的有：java 栈、程序计数器、本地方法栈

线程共享的有：堆、方法区



### 堆

**此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存。**

垃圾回收主要发生在堆中，堆内存又分为Eden 空间、From Survivor、To Survivor、老年代，**进一步划分的目的是更好地回收内存，或者更快地分配内存。**



默认15岁进入老年代，貌似现在已经是动态判断了，如果某个年龄的对象超过了在Survivor内存的一半，那么这个年龄就成为新的晋升年龄。

### 栈

Java 虚拟机栈是由一个个栈帧组成，而每个栈帧中都拥有：局部变量表、操作数栈、动态链接、方法出口信息



**Java 虚拟机栈会出现两种错误：`StackOverFlowError` 和 `OutOfMemoryError`。**



每一次函数调用都会有一个对应的栈帧被压入 Java 栈，每一个函数调用结束后，都会有一个栈帧被弹出。

Java 方法有两种返回方式：1，return 语句；2，抛出异常。不管哪种返回方式都会导致栈帧被弹出。



### 方法区

JDK1.8 方法区的实现从永久代变成了元空间

它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据（字符串常量池在堆中）



为什么要将永久代 (PermGen) 替换为元空间 (MetaSpace) 呢?

1， 永久代受jvm本身大小的限制，而元空间使用的是直接内存，溢出的可能性更小

2，元空间里面存放的是类的元数据，使用直接内存可以加载更多的类。



运行时常量池是方法区的一部分。Class 文件中除了有类的版本、字段、方法、接口等描述信息外，还有常量池表（用于存放编译期生成的各种字面量和符号引用）



### 本地方法栈

本地方法栈则为虚拟机使用到的 Native 方法服务

比如 Thread类底层的  start 方法就是native方法，unsafe类都是native方法，都用到了c或c++的函数库



### 程序计数器

实现代码的流程控制

当线程被切换回来的时候能够知道该线程上次运行到哪儿了



两个作用：

1. 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。

2. 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

   

程序计数器是唯一一个不会出现OOM的内存区域

## 类加载器（暂未完成）

核心类加载器

扩展类加载器

应用程序类加载器

自定义类加载器



双亲委派机制，要是有爸爸就让爸爸来加载（如果父加载器为null就使用核心类加载器）



### 类加载步骤

加载、验证、准备、解析、初始化



#加载
				#通过一个类的全限定名来获取定义此类的二进制字节流
				#将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构
				#在内存中生成一个代表这个类的class对象，作为方法区这个类的各种数据的访问入口
#验证
				#文件格式验证：验证字节流是否符合Class文件格式规范
				#元数据验证：是否有父类、父类是否继承final修饰类、不是抽象类是否实现父类或接口需要实现的方法
				#字节码验证：确定语义合法、符合逻辑
				#引用符号验证：检查引用的类、字段、方法的访问性是否可悲当前类访问等。
#准备
				#为类变量分配内存并设置初始化值
#解析
				#类或接口解析
				#字段解析
				#类方法解析
				#接口方法解析
#初始化
				#执行初始化方法：调用静态代码块和给静态变量赋值，先从父类开始

## 对象

### 对象的创建

> 1 类加载检查  ：检查是否已经被加载过
>
> 2 分配内存 ：把一块确定大小的内存从堆中划出来
>
> 3 初始化零值 ： 字面意思
>
> 4 设置对象头 ：
>
> 5 执行init方法 ：按程序员的意愿进行初始化

4 设置对象头

初始化零值完成之后，**虚拟机要对对象进行必要的设置**，对象头里存储：

类型指针（指向类的元数据信息）、对象的哈希码、对象的 GC 分代年龄、锁的状态（是否是偏向锁、 锁的计数器也存储在这，用来判断对象是否处于被锁定状态）



### 对象的内存布局

对象分为三部分： **对象头**、**实例数据**和**对齐填充**



**Hotspot 虚拟机的对象头包括两部分信息**，**第一部分用于存储对象自身的运行时数据**（哈希码、GC 分代年龄、锁状态标志等等），**另一部分是类型指针**，即对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是那个类的实例。

**实例数据部分是对象真正存储的有效信息**，也是在程序中所定义的各种类型的字段内容。

**对齐填充部分不是必然存在的，也没有什么特别的含义，仅仅起占位作用。** 因为 Hotspot 虚拟机的自动内存管理系统要求对象起始地址必须是 8 字节的整数倍，换句话说就是对象的大小必须是 8 字节的整数倍。而对象头部分正好是 8 字节的倍数（1 倍或 2 倍），因此，当对象实例数据部分没有对齐时，就需要通过对齐填充来补全。





# mysql（暂未完成）



## 索引



MyISAM和InnoDB区别



mysql InnoDB引擎下

 主键索引（聚簇索引）中非叶子结点存储的是主键，叶子结点存储的是数据

非主键索引（非聚簇索引）中非叶子结点存储的是普通索引（辅助所以），叶子结点存储的是主键值



在根据主索引搜索时，直接找到key所在的节点即可取出数据；在根据辅助索引查找时，则需要先取出主键的值，再⾛⼀遍主索引



InnoDB存储引擎的锁的算法有三种：

Record lock：单个⾏记录上的锁

Gap lock：间隙锁，锁定⼀个范围，不包括记录本身

Next-key lock：record+gap 锁定⼀个范围，包含记录本身



共享锁：阻止其他事务获取排他锁，可能出现死锁

排他锁：对于UPDATE、DELETE和INSERT语句，InnoDB会自动给涉及数据集加排他锁（X)，查询的时候SELECT ... FOR UPDATE也会加排他锁，排他锁自动开启间隙锁



### MVCC 多版本并发控制

更新版本、删除版本

只能查当前事务大于等于更新版本，小于或者没有删除版本的数据

## 基础

远程登录mysql

mysql -h ip -P port -u 用户 -p  '密码'



show databases、use database、show tables、desc  table

基本sql



## 调优

###  ⼤表优化

限定数据的范围、读写分离、垂直拆分、水平拆分



### 分库分表之后，id 主键如何处理



## 幻读？

幻读有两种，一种是快照读中的幻读，一种是当前读中的幻读

幻读主要是插入和删除时发生、不可重复读是批量更新时发生

快照读的幻读通过 MVCC 解决，默认解决的

当前读的幻读通过 间隙锁、行锁 解决



## 主从同步



## 如何定位并优化慢查询sql 



### 1 根据慢日志定位慢查询sql



SHOW VARIABLES LIKE '%query%'   查询慢日志相关信息

如何打开慢查询 ： SET GLOBAL slow_query_log = ON;

将默认时间改为1S： SET GLOBAL long_query_time = 1;





### 2 使用explain等工具分析sql

在要执行的sql前加上explain 例如：EXPLAIN SELECT menu_name FROM t_sys_menu ORDER BY menu_id DESC;



```
id //select查询的序列号，包含一组数字，表示查询中执行select子句或操作表的顺序
select_type //查询类型
table //正在访问哪个表
partitions //匹配的分区
type //访问的类型
possible_keys //显示可能应用在这张表中的索引，一个或多个，但不一定实际使用到
key //实际使用到的索引，如果为NULL，则没有使用索引
key_len //表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度
ref //显示索引的哪一列被使用了，如果可能的话，是一个常数，哪些列或常量被用于查找索引列上的值
rows //根据表统计信息及索引选用情况，大致估算出找到所需的记录所需读取的行数
filtered //查询的表行占表的百分比
Extra //包含不适合在其它列中显示但十分重要的额外信息
```

关键字段 type



### 3 修改sql或者尽量让sql走索引



#### 最左前缀原则

在使用联合索引是要遵循最左前缀原则，比如联合索引有a,b,c三个字段，where后面的查询条件如果没有a就没办法使用这个索引



索引的底层实现是b+树，会先第一个字段从小到大排序，如果第一个字段相同则根据第二个字段排序，以此类推来建立索引，所以说如果查询的时候跳过第一个索引字段是没办法使用第二个索引字段的。



注意事项

1 范围查询

2 like查询，前面有%无法使用索引

3 不要再列上运算

4 索引字段不要为NULL

5 尽量选择高区分度的字段加索引

6 尽量使用覆盖索引，查询的每个字段都在索引中，不去读表 ，性能很高



order by 一定要加索引

查询时如果要加行锁，where后面的字段一定要加索引，不然就会升级会表锁



# 分布式（暂未完成）

### CAP







# linux（暂未完成）

常用命令

tialf xxx | grep xxx | grep xxx    实时打印日志其实是有延迟的! 有时候会稳定延迟两条数据、有时候会导致不实时打印



根据端口查看进程  lsof -i:3306

redis 删除所有key   flushall

启动 nginx  ./sbin/nginx -c conf/nginx.conf

单机启动nacos   sh startup.sh -m standalone

后台启动java jar   nohup java -Dserver.port=8448 -jar sentinel-dashboard-1.8.0.jar &

根据进程号查询项目路径   ll /proc/1374320/cwd

只想查看一条数据    cat  xxx | grep  xxx  | tail  -n  1

查看文件md5值     md5sum face.jar

设置定时任务    crontab -e       * * * * * /home/data/xxxx.sh 1>>/home/data/xxxx.log

解压   tar -zxvf   xxx.tar

压缩  tar -zcvf    xxx.tar  （不太确定啊）

cat   xxxx  awk -F 'xxxx' {'print $1'} |sort |uniq -c



adb    ：

adb.exe connect 172.24.60.183   

adb.exe install -r  app.apk



top



如何连接mongo

1

mongo [yourIP]:27017
use admin // 需要先选择admin数据库才可以作验证
db.auth('useradmin','adminpassword') // 返回1就表示验证成功，获得所有权限了

2

mongo localhost:27017/admin -u useradmin -p //如果是普通用户的话，admin 改为你的数据库



# 23种设计模式

创建型(5种)：
		#单例模式、
		#工厂方法模式、
		#抽象工厂模式、
		#建造者模式、
		#原型模式
	结构型(7种)：适配器模式、装饰者模式、代理模式、外观模式、桥接模式、组合模式、享元模式
	行为型(11种)：策略模式、模版方法模式、观察者模式、迭代模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式



# 项目经验

？？？



@SpringQueryMap 可以解决open feign get请求无法传对象参数



mq集群

redis



# 打卡 

210329 aqs+基本目录

0331 循环依赖

0401 上线有点忙 - - !

0402 结束循环依赖，开始redis

0409 redis 持久化

0410  redis 缓存淘汰策略，jvm垃圾收集器设置

0411 xxx第三季结束~

0412 打卡

redis  ioc原理及bean加载过程（生命周期）  aop tcp如果保证可靠





0428 大部分完成，准备开始算法 push一波











































# 算法

简单记一下步骤，方便回忆起来

## LRU



## DCL



## 快排













14： 87+10  

15： 19   27.95

16： 36.1    34.9

18:    19

19： 18     

20： 16











16

399



15号  19

429



19号 459   

18



18号  19   79.7  429 



17号  389 

16号 429



20号  1600









192.168.37.10







