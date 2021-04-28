AOP

AOP 要实现的是在我们原来写的代码的基础上，进行一定的包装，如在方法执行前、方法返回后、方法抛出异常后等地方进行一定的拦截处理或者叫增强处理。



我们要实现一个代理，实际运行的实例其实是生成的代理类的实例。



Spring AOP 是基于代理实现的，在容器启动的时候需要生成代理实例，在方法调用上也会增加栈的深度，使得 Spring AOP 的性能不如 AspectJ 那么好



AspectJ  是 **AOP 编程的完全解决方案**

AspectJ 在实际代码运行前完成了织入，所以大家会说它生成的类是没有额外运行时开销的



Spring AOP 致力于解决的是企业级开发中最普遍的 AOP 需求（方法织入），而不是力求成为一个像 AspectJ 一样的 AOP 编程完全解决方案



AOP 术语

Advice、Advisor、Pointcut、Aspect、Joinpoint 等等





目前 Spring AOP 一共有三种配置方式，Spring 做到了很好地向下兼容，所以大家可以放心使用。

- Spring 1.2 **基于接口的配置**：最早的 Spring AOP 是完全基于几个接口的，想看源码的同学可以从这里起步。
- Spring 2.0 **schema-based 配置**：Spring 2.0 以后使用 XML 的方式来配置，使用 命名空间 `<aop />`
- Spring 2.0 **@AspectJ 配置**：使用注解的方式来配置，这种方式感觉是最方便的，还有，这里虽然叫做 `@AspectJ`，但是这个和 AspectJ 其实没啥关系。





1.2

**ProxyFactoryBean** 

​	支持：**Advice** **Advisor** **Interceptor**

**BeanNameAutoProxyCreator** 



**DefaultAdvisorAutoProxyCreator**



DefaultAdvisorAutoProxyCreator 类了，它能实现自动将所有的 advisor 生效。



2.0

接下来，我们需要关心的是 @Aspect 注解的 bean 中，我们需要配置哪些内容。

**首先，我们需要配置 Pointcut，**Pointcut 在大部分地方被翻译成切点，用于定义哪些方法需要被增强或者说需要被拦截，有点类似于之前介绍的 **Advisor** 的方法匹配













如果被代理的目标类实现了一个或多个自定义的接口，那么就会使用 JDK 动态代理，如果没有实现任何接口，会使用 CGLIB 实现代理，如果设置了 proxy-target-class="true"，那么都会使用 CGLIB。

JDK 动态代理基于接口，所以只有接口中的方法会被增强，而 CGLIB 基于类继承，需要注意就是如果方法使用了 final 修饰，或者是 private 方法，是不能被增强的。



InstantiationAwareBeanPostProcessor 是 `Instantiation`，BeanPostProcessor 是 `Initialization`，它代表的是 bean 在实例化完成并且属性注入完成，在执行 init-method 的前后进行作用的。

而 InstantiationAwareBeanPostProcessor 的执行时机要前面一些







getEarlyBeanReference





InstantiationAwareBeanPostProcessor 这个BeanPostProcessor还是挺重要的，除了题主说的的resolveBeforeInstantiation之外



**在Spring自动处理循环依赖（A依赖B，B依赖A）的时机，通过SmartInstantiationAwareBeanPostProcessor#getEarlyBeanReference拿到最终的AOP代理对象，而这块代码的执行时机是 B getBean(A)的时候，通过三级缓存里的ObjectFactory执行的。**









#### AOP 当中的概念：

- 切入点（Pointcut） 在哪些类，哪些方法上切入（**where**）
- 通知（Advice） 在方法执行的什么实际（**when:**方法前/方法后/方法前后）做什么（**what:**增强的功能）
- 切面（Aspect） 切面 = 切入点 + 通知，通俗点就是：**在什么时机，什么地方，做什么增强！**
- 织入（Weaving） 把切面加入到对象，并创建出代理对象的过程。（由 Spring 来完成）