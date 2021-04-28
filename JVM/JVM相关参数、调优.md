-XX:+UseSerialGC  串行  串行

-XX:+UseParallelGC 	Parallel   串行old

-XX:+UseParallelOldGC 	Parallel     Parallel old

-XX:+USeParNewGC 	ParNew  cms       后备 串行old   ？？？？

-XX:+UseConcMarkSweepGC  串行  cms    后备 串行old  ？？？？

-XX:+UseG1GC	g1







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

**策略5：**注意： 如果满足下面的指标，**则一般不需要进行 GC 优化：**

> MinorGC 执行时间不到50ms； Minor GC 执行不频繁，约10秒一次； Full GC 执行时间不到1s； Full GC 执行频率不算频繁，不低于10分钟1次。