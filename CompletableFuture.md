### CompletableFuture

CompletableFuture 类主要会用到两个方法

`runAsync`、`supplyAsync` 这两个方法的传参是`lomdba`和`executor`线程

可以使用自定义的线程池

```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier) {
    return asyncSupplyStage(asyncPool, supplier);
}

public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor) {
    return asyncSupplyStage(screenExecutor(executor), supplier);
}

public static CompletableFuture<Void> runAsync(Runnable runnable) {
    return asyncRunStage(asyncPool, runnable);
}

public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor) {
    return asyncRunStage(screenExecutor(executor), runnable);
}
```



#### 主要方法

**thenApply** 方法可以改变方法返回的值

**exceptionally** 方法可以捕获异常

**whenComplete** 会在异步方法执行完成后被调用（如需获取任务完成先后顺序，此处代码即可）

**allOf** 聚合多个CompletableFuture

**join** 阻塞主线程，必须等到所有异步方法都执行完成后才继续执行主线程



#### 使用示例

使用supplyAsync方法，可以获取返回值，list2是按任务完成先后顺序获取、list是按任务提交顺序获取

```java
////方式一：循环创建CompletableFuture list,调用sequence()组装返回一个有返回值的CompletableFuture，返回结果get()获取
for(int i=0;i<taskList.size();i++){
    final int j=i;
    //异步执行
    CompletableFuture<String> future = CompletableFuture.supplyAsync(()->calc(taskList.get(j)), exs)
        //Integer转换字符串    thenAccept只接受不返回不影响结果
        .thenApply(e->Integer.toString(e))
        .exceptionally(e -> {
            System.out.println("可以捕获异常");
            return "000";
        })
        //如需获取任务完成先后顺序，此处代码即可
        .whenComplete((v, e) -> {
            System.out.println("任务"+v+"完成!result="+v+"，异常 e="+e+","+new Date());
            list2.add(v);
        });
    futureList.add(future);
}
```



```java
//方式二：全流式处理转换成CompletableFuture[]+组装成一个无返回值CompletableFuture，join等待执行完毕。返回结果whenComplete获取
CompletableFuture[] cfs = taskList.stream()
    	.map(object-> CompletableFuture.supplyAsync(()->calc(object), exs)
    	.thenApply(h->Integer.toString(h))
        .exceptionally(e -> {
             System.out.println("!!!!!!!!!!!!出错了");
             return "000";
        })
        //如需获取任务完成先后顺序，此处代码即可
        .whenComplete((v, e) -> {
         	System.out.println("!!!!!!!!!!!!!!!!!任务"+v+"完成!result="+v+"，异常 e="+e+",");
            list2.add(v);
        })).toArray(CompletableFuture[]::new);
//等待总任务完成，但是封装后无返回值，必须自己whenComplete()获取
CompletableFuture.allOf(cfs).join();
```



```java
/**
     * @Description 组合多个CompletableFuture为一个CompletableFuture,所有子任务全部完成，组合后的任务才会完成。带返回值，可直接get.
     * @param futures List
     * @since JDK1.8
     */
public static <T> CompletableFuture<List<T>> sequence(List<CompletableFuture<T>> futures) {
    //1.构造一个空CompletableFuture，子任务数为入参任务list size
    CompletableFuture<Void> allDoneFuture = CompletableFuture.allOf(futures.toArray(new CompletableFuture[futures.size()]));
    //2.流式（总任务完成后，每个子任务join取结果，后转换为list）
    return allDoneFuture.thenApply(v -> futures.stream().map(CompletableFuture::join).collect(Collectors.toList()));
}

/**
     * @Description Stream流式类型futures转换成一个CompletableFuture,所有子任务全部完成，组合后的任务才会完成。带返回值，可直接get.
     * @param futures Stream
     * @since JDK1.8
     */
public static <T> CompletableFuture<List<T>> sequence(Stream<CompletableFuture<T>> futures) {
    List<CompletableFuture<T>> futureList = futures.filter(f -> f != null).collect(Collectors.toList());
    return sequence(futureList);
}
```



CompletableFuture.allOf(cfs).join(); 可以阻塞主线程等所有任务执行完才继续下去

```java
CompletableFuture<String> f1 = CompletableFuture.supplyAsync(() -> {
    try {
        TimeUnit.SECONDS.sleep(3);
        System.out.println("f1");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }

    return "f1-";
});

CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> {
    try {
        TimeUnit.SECONDS.sleep(10);
        System.out.println("f2");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }

    return "f2-";
});

CompletableFuture<String> f3 = CompletableFuture.supplyAsync(() -> {
    try {
        TimeUnit.SECONDS.sleep(1);
        System.out.println("f3");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }

    return "f3-";
});

CompletableFuture.allOf(f1, f2, f3).join(); // 阻塞，如果光聚合不阻塞是没用的
// 阻塞完之后可以挨个调get方法拿
System.out.println(f2.get());
System.out.println(f1.get());

System.out.println(System.currentTimeMillis() + ":阻塞");
```

配合线程池使用

```java
@Configuration
@EnableAsync
@Slf4j
public class SpringAsyncConfigurer extends AsyncConfigurerSupport {

    @Bean("commonPool")
    public ThreadPoolTaskExecutor commonPool() {
        return buildThreadPoolTaskExecutor("commonPool", 128, 256,
                5000, new ThreadPoolExecutor.CallerRunsPolicy(), false);
    }


    private ThreadPoolTaskExecutor buildThreadPoolTaskExecutor(String threadNamePrefix, int 			corePoolSize, int maxPoolSize, int queueCapacity, RejectedExecutionHandler rejectedExecutionHandler, boolean isDaemon) {
        String hostName = getHostName();
        ThreadPoolTaskExecutor threadPool = new ThreadPoolTaskExecutor();
        threadPool.setTaskDecorator(new FeedTaskDecorator());
        threadPool.setCorePoolSize(corePoolSize); //最小线程数
        threadPool.setMaxPoolSize(maxPoolSize); //最大线程数
        threadPool.setQueueCapacity(queueCapacity);
        threadPool.setDaemon(isDaemon);
        threadPool.setThreadNamePrefix(threadNamePrefix + "-" + hostName + "-");
        threadPool.setRejectedExecutionHandler(rejectedExecutionHandler);
        threadPool.initialize();
        return threadPool;
    }
}


@Component
@Slf4j
public class DeviceHeartSchedule {
    @Resource(name = "commonPool")
    ThreadPoolTaskExecutor commonPool;
    
    public void heartSchedule() {
    CompletableFuture<String> result = CompletableFuture.supplyAsync(() -> doCheckHeart(tableCode), commonPool);
    }
|    
    
```

