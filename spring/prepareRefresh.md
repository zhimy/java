### prepareRefresh  

创建 Bean 容器前的准备工作







### obtainFreshBeanFactory  

创建 Bean 容器，加载并注册 Bean

xml 文件转换为 Document 对象  把Document 对象转换为BeanDefinition







**BeanFactory 是 Bean 容器**

所以，如果有人问你 Bean 是什么的时候，你要知道 Bean 在代码层面上可以简单认为是 BeanDefinition 的实例。

BeanDefinition 中保存了我们的 Bean 信息，比如这个 Bean 指向的是哪个类、是否是单例的、是否懒加载、这个 Bean 依赖了哪些 Bean 等等

BeanDefinition 描述了一个Bean

Object 描述了一个类





继续往下看怎么解析之前，我们先看下 **`<bean />`** 标签中可以定义哪些属性：

| Property                 |                                                              |
| ------------------------ | ------------------------------------------------------------ |
| class                    | 类的全限定名                                                 |
| name                     | 可指定 id、name(用逗号、分号、空格分隔)                      |
| scope                    | 作用域                                                       |
| constructor arguments    | 指定构造参数                                                 |
| properties               | 设置属性的值                                                 |
| autowiring mode          | no(默认值)、byName、byType、 constructor                     |
| lazy-initialization mode | 是否懒加载(如果被非懒加载的bean依赖了那么其实也就不能懒加载了) |
| initialization method    | bean 属性设置完成后，会调用这个方法                          |
| destruction method       | bean 销毁后的回调方法                                        |

总结一下，到这里已经初始化了 Bean 容器，`<bean />` 配置也相应的转换为了一个个 BeanDefinition，然后注册了各个 BeanDefinition 到注册中心，并且发送了注册事件





### prepareBeanFactory

**准备 Bean 容器: prepareBeanFactory**

Spring 把我们在 xml 配置的 bean 都注册以后，会"手动"注册一些特殊的 bean。



### finishBeanFactoryInitialization

**初始化所有的 singleton beans**



注意，后面的描述中，我都会使用**初始化**或**预初始化**来代表这个阶段，Spring 会在这个阶段完成所有的 singleton beans 的实例化。



我们来总结一下，到目前为止，应该说 BeanFactory 已经创建完成，并且所有的实现了 BeanFactoryPostProcessor 接口的 Bean 都已经初始化并且其中的 postProcessBeanFactory(factory) 方法已经得到回调执行了。而且 Spring 已经“手动”注册了一些特殊的 Bean，如 `environment`、`systemProperties` 等。



#### getBean



vim /etc/my.cnf

[mysqld]
skip-grant-tables



flush privileges;











- `+UseParallelGC` = `新生代ParallelScavenge + 老年代ParallelOld`
- `+UseParallelOldGC` = 同上
- `-UseParallelOldGC` = `新生代ParallelScavenge + 老年代SerialOld`











