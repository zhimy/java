### ConcurrentHashMap1.7

1. 存储结构

   Java 7 中 ConcurrentHashMap 的存储结构如上图，ConcurrnetHashMap 由很多个 Segment 组合，而每一个 Segment 是一个类似于 HashMap 的结构，所以每一个 HashMap 的内部可以进行扩容。但是 Segment 的个数一旦初始化就不能改变，默认 Segment 的个数是 16 个，你也可以认为 ConcurrentHashMap 默认支持最多 16 个线程并发。

2. 初始化

   总结一下在 Java 7 中 ConcurrnetHashMap 的初始化逻辑。
   1. 必要参数校验。
   2. 校验并发级别 concurrencyLevel 大小，如果大于最大值，重置为最大值。无惨构造**默认值是 16.**
   3. 寻找并发级别 concurrencyLevel 之上最近的 **2 的幂次方**值，作为初始化容量大小，**默认是 16**。
   4. 记录 segmentShift 偏移量，这个值为【容量 = 2 的N次方】中的 N，在后面 Put 时计算位置时会用到。**默认是 32 - sshift = 28**.
   5. 记录 segmentMask，默认是 ssize - 1 = 16 -1 = 15.
   6. **初始化 segments[0]\**，**默认大小为 2**，**负载因子 0.75**，**扩容阀值是 2*0.75=1.5**，插入第二个值时才会进行扩容。

3. 23

4. 44



可以发现 Java8 的 ConcurrentHashMap 相对于 Java7 来说变化比较大，不再是之前的 **Segment 数组 + HashEntry 数组 + 链表**，而是 **Node 数组 + 链表 / 红黑树**。当冲突链表达到一定长度时，链表会转换成红黑树。





#### 初始化

```java
/**
 * Initializes table, using the size recorded in sizeCtl.
 */
private final Node<K,V>[] initTable() {
    Node<K,V>[] tab; int sc;
    while ((tab = table) == null || tab.length == 0) {
        //　如果 sizeCtl < 0 ,说明另外的线程执行CAS 成功，正在进行初始化。
        if ((sc = sizeCtl) < 0)
            // 让出 CPU 使用权
            Thread.yield(); // lost initialization race; just spin
        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
            try {
                if ((tab = table) == null || tab.length == 0) { 
                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
                    @SuppressWarnings("unchecked")
                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                    table = tab = nt;
                    sc = n - (n >>> 2); // 装B写法  (n - 0.25n) = 0.75n
                }
            } finally {
                sizeCtl = sc;
            }
            break;
        }
    }
    return tab;
}
```



从源码中可以发现 ConcurrentHashMap 的初始化是通过**自旋和 CAS** 操作完成的。里面需要注意的是变量 `sizeCtl` ，它的值决定着当前的初始化状态。

1. -1 说明正在初始化
2. -N 说明有N-1个线程正在进行扩容
3. 表示 table 初始化大小，如果 table 没有初始化
4. 表示 table 容量，如果 table　已经初始化。





#### put

```java
public V put(K key, V value) {
    return putVal(key, value, false);
}

/** Implementation for put and putIfAbsent */
final V putVal(K key, V value, boolean onlyIfAbsent) {
    // key 和 value 不能为空
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode()); // 去除负值
    
   ----------------------------
       		static final int spread(int h) {
                return (h ^ (h >>> 16)) & HASH_BITS;
            }
    // 与hashmap计算hash基本一样，但多了一步& HASH_BITS，HASH_BITS是0x7fffffff，该步是为了消除最高位上的负符号 hash的负在ConcurrentHashMap中有特殊意义表示在扩容或者是树节点
   ----------------------------
  
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        // f = 目标位置元素
        Node<K,V> f; int n, i, fh;// fh 后面存放目标位置的元素 hash 值
        if (tab == null || (n = tab.length) == 0)
            // 数组桶为空，初始化数组桶（自旋+CAS)
            tab = initTable();
        //获取数组i索引的Node CAS操作 保持其它线程对table的改变在这里可见
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            // 桶内为空，CAS 放入，不加锁，成功了就直接 break 跳出
            if (casTabAt(tab, i, null,new Node<K,V>(hash, key, value, null)))
                break;  // no lock when adding to empty bin
        }
        else if ((fh = f.hash) == MOVED) // 首地址处不null 并且Node的hash是-1  表示是ForwardingNode节点正在rehash扩容
            tab = helpTransfer(tab, f); // 帮助扩容
        else {
            V oldVal = null;
            // 使用 synchronized 加锁加入节点
            synchronized (f) {
                if (tabAt(tab, i) == f) { // 这里volatile获取首节点与8处获取的首节点对比判断f还是不是首节点 ****注意因为是多线程的关系虽然remove等操作也会锁首节点但在从第8处执行到锁这里的时候这里的首节点是完全存在被其它线程干掉或者空旋的情况的
                    // 说明是链表
                    if (fh >= 0) { // fh即节点的的二次hash值，判断是为了等待扩容完成(上面是个死循环，第一次执行是在扩容中的话第二次会来这里的)
                        binCount = 1;
                        // 循环加入新的或者覆盖节点
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    else if (f instanceof TreeBin) {
                        // 红黑树
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key, value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    // bitCount 链表时 记录链表长度
    addCount(1L, binCount); // 这就是最后的插入数据后根据一定条件来进行扩容的方法了
    return null;
}
```

1. 根据 key 计算出 hashcode 。
2. 判断是否需要进行初始化。
3. 即为当前 key 定位出的 Node，如果为空表示当前位置可以写入数据，利用 CAS 尝试写入，失败则自旋保证成功。
4. 如果当前位置的 `hashcode == MOVED == -1`,~~则需要进行扩容~~**表示正在进行扩容，可以进行帮助扩容**
5. 如果都不满足，则利用 synchronized 锁写入数据。
6. 如果数量大于 `TREEIFY_THRESHOLD` 则要转换为红黑树。



#### addCount 计数并判断是否扩容

```java
private final void addCount(long x, int check) {
    1    CounterCell[] as; long b, s;
    2    if ((as = counterCells) != null ||
    3        !U.compareAndSwapLong(this, BASECOUNT, b = baseCount, s = b + x)) {
    4        CounterCell a; long v; int m;
    5        boolean uncontended = true;
    6        if (as == null || (m = as.length - 1) < 0 ||
    7            (a = as[ThreadLocalRandom.getProbe() & m]) == null ||
    8            !(uncontended =
    9              U.compareAndSwapLong(a, CELLVALUE, v = a.value, v + x))) {
    10            fullAddCount(x, uncontended);
    11            return;
    12        }
    13        if (check <= 1)
    14            return;
    15        s = sumCount();
    16    }
    17    if (check >= 0) {
    18        Node<K,V>[] tab, nt; int n, sc;
    19        while (s >= (long)(sc = sizeCtl) && (tab = table) != null &&
    20               (n = tab.length) < MAXIMUM_CAPACITY) {
    21            int rs = resizeStamp(n);
    22            if (sc < 0) {
    23                if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
    24                    sc == rs + MAX_RESIZERS || (nt = nextTable) == null ||
    25                    transferIndex <= 0)
    26                    break;
    27                if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1))
    28                    transfer(tab, nt);
    29            }
    30            else if (U.compareAndSwapInt(this, SIZECTL, sc,
    31                                         (rs << RESIZE_STAMP_SHIFT) + 2))
    32                transfer(tab, null);
    33            s = sumCount();
            }
        }
    }
```







#### get



```java
public V get(Object key) {
    Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
    // key 所在的 hash 位置
    int h = spread(key.hashCode());
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (e = tabAt(tab, (n - 1) & h)) != null) {
        // 如果指定位置元素存在，头结点hash值相同
        if ((eh = e.hash) == h) {
            if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                // key hash 值相等，key值相同，直接返回元素 value
                return e.val;
        }
        else if (eh < 0)
            // 头结点hash值小于0，说明正在扩容或者是红黑树，find查找
            return (p = e.find(h, key)) != null ? p.val : null;
        while ((e = e.next) != null) {
            // 是链表，遍历查找
            if (e.hash == h &&
                ((ek = e.key) == key || (ek != null && key.equals(ek))))
                return e.val;
        }
    }
    return null;
}
```

总结一下 get 过程：

1. 根据 hash 值计算位置。
2. 查找到指定位置，如果头节点就是要找的，直接返回它的 value.
3. 如果头节点 hash 值小于 0 ，说明正在扩容或者是红黑树，查找之。
4. 如果是链表，遍历查找之。

总结：

总的来说 ConcruuentHashMap 在 Java8 中相对于 Java7 来说变化还是挺大的，



#### 总结

Java7 中 ConcruuentHashMap 使用的分段锁，也就是每一个 Segment 上同时只有一个线程可以操作，每一个 Segment 都是一个类似 HashMap 数组的结构，它可以扩容，它的冲突会转化为链表。但是 Segment 的个数一但初始化就不能改变。

Java8 中的 ConcruuentHashMap 使用的 Synchronized 锁加 CAS 的机制。结构也由 Java7 中的 **Segment 数组 + HashEntry 数组 + 链表** 进化成了 **Node 数组 + 链表 / 红黑树**，Node 是类似于一个 HashEntry 的结构。它的冲突再达到一定大小时会转化成红黑树，在冲突小于一定数量时又退回链表。

大于等于8会转换为红黑树，小于等于6会转换为链表

有些同学可能对 Synchronized 的性能存在疑问，其实 Synchronized 锁自从引入锁升级策略后，性能不再是问题，有兴趣的同学可以自己了解下 Synchronized 的**锁升级**。

ConcruuentHashMap 的key 和value 不能为 null

添加和删除时都有死循环，防止执行操作过程中正在扩容









#### 源码未学习