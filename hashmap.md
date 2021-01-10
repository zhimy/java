### 相关知识

java中有三种移位运算符

<<    :   左移运算符，num << 1,相当于num乘以2

\>>    :   右移运算符，num >> 1,相当于num除以2

\>>>   :   无符号右移，忽略符号位，空位都以0补齐

### 相关算法

1. 计算索引 `tab[i = (n - 1) & hash]`
2. 判断之前是存在相同的key `p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k)))`
3. 判断是否需要到新的索引位置 `(e.hash & oldCap) == 0`

### 通常考点



2. HashMap数组结构为什么用2的倍数
   高速的索引计算，使用HashMap肯定是冲突越少越好，就要求分部均匀，最好的用取模 ` h % length`，但是近一步如果用2的幂`h & (length - 1) == h % length` 是等价的，效率缺差却别非常大
   综合衡量用空间换了时间，且是值得的

3. 线程安全问题
   线程不安全，就put来看全程没考虑线程问题，肯定不安全，现在随便并发一下resize会混乱吧，put链表，红黑树挂载基本都会出问题

### 主要方法

#### put方法

`put()` 会调用 `putVal()` 方法

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

hash 方法

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

分析

1. 看出key是可以空的  hash为0
2. h ^ (h >>> 16)  第二步 高位参与运算
3. hashcode总共32未，异或计算也就变成了高16位和低16位进行异或，降低hash冲突发生的概率























```java
/**
     * Implements Map.put and related methods
     *
     * @param hash hash for key
     * @param key the key
     * @param value the value to put
     * @param onlyIfAbsent if true, don't change existing value   相同key是不是覆盖值
     * @param evict if false, the table is in creation mode.    在hashmap中没用
     * @return previous value, or null if none
     */
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        if ((p = tab[i = (n - 1) & hash]) == null) // 上面table初始化完成，数组都是null，进入if
            tab[i] = newNode(hash, key, value, null); // new一个节点Node，放在数组里
        else { // 如果这次来已经有值了那么走 else
            Node<K,V> e; K k;
            if (p.hash == hash && // 判断链表头部是否和key相同
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) { // 遍历链表
                    if ((e = p.next) == null) { // 如果找到链表尾部也没找到，那么就再添加一个node
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st 添加node之后判断链表长度是否达到了 8，如果达到了就转换为红黑树
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash && // 如果当前node不是尾部就判断是否和key相同，如果相同就返回 e
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e; // 如果不相同就把e赋值给p继续for循环
                }
            }
            if (e != null) { // 如果 e 存在，替换旧值
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount; // 这个是记录数据结构变动次数的
        if (++size > threshold) // 如果大于阈值触发扩容
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // 初始化table
        newCap = DEFAULT_INITIAL_CAPACITY; // 16
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY); // 0.75 * 16 = 12
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap]; // 默认长度为 16
    table = newTab; // 指向新的tab
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) { // 遍历老table
            Node<K,V> e;
            if ((e = oldTab[j]) != null) { // 先看头节点是否为空，如果不为空
                oldTab[j] = null;
                if (e.next == null) // 如果只有一个头结点，没下一个了，就直接把他挪到新的位置
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode) // 如果是红黑树
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // 最后只剩下链表了
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next; // 拿到下一个赋值给 next
                        if ((e.hash & oldCap) == 0) { // &一下，如果为0，就说明扩容之后还待在原来的索引
                            if (loTail == null) // 如果尾节点是null，就把e赋值给头节点
                                loHead = e;
                            else
                                loTail.next = e; // 否则把e放在尾节点后面，成为新的尾节点
                            loTail = e; // 把 e 指向新的尾节点
                        }
                        else { // 如果&完结果不为0，就说明扩容后该到新索引位了，新=老+j
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead; // 将链表放到table上
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

1. 初始化 newCap 默认大小为 16 
2. threshold = newThr = 0.75*16=12
3. Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap]   申明一个16个大小的Node数组
4. 总结，到这put就比较详细了，也知道了基本结构是数组、链表、红黑树，链表到8个时转换成红黑树
   同时每次进行2倍扩容和数据转移，扩容是用新结构的那显然减少扩容次数会有更好的性能
   那就要求每次声明HashMap时最好是指定大小的

#### get方法

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
```

```java
/**
     * Implements Map.get and related methods
     *
     * @param hash hash for key
     * @param key the key
     * @return the node, or null if none
     */
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    // 如果不满足table不为空并且长度大于0，以及对应的索引有值的话就直接返回null
    if ((tab = table) != null && (n = tab.length) > 0 && 
        (first = tab[(n - 1) & hash]) != null) {
        if (first.hash == hash && // 先检查第一个链表头是不是要找的那个key
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) { // 判断该索引处是否有hash冲突，也就是链表上有多个值
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do { // 开始遍历并判断，如果找到直接返回
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```





#### tableSizeFor 方法很神奇

```java
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
} 

自己理解：从左到右，把第一个1后面所有的0都变成1，然后再加1，就得到了最接近的2次幂

这个方法是在找大于等于cap且最小2的幂
        比如cap=1   结果 2 0次方 1
        cap=2  2
        cap=3 4
        cap=9  16
        分析下等于9
        cap - 1  第一步结果8
        00000000000000000000000000001000    8
        00000000000000000000000000000100    右移1位 
        
        00000000000000000000000000001100    或运算 结果
        00000000000000000000000000000011    右移2位
        00000000000000000000000000001111    或运算 结果
                                            
        00000000000000000000000000001111    右移 4 8 16没用全是0结果还是这个15
        最终 +1   16
        
        分析下等于大点 12345678
        00000000101111000110000101001110  12345678
        00000000101111000110000101001101  -1结果   12345677
        00000000010111100011000010100110  右移1位 
        
        00000000111111100111000111101111  或运算 结果
        00000000001111111001110001111011  右移2位
        
        00000000111111111111110111111111  差不多了在移0就没了都是1了，+1不是肯定是2的倍数了
        
        再说开始-1原因这是为了防止，cap已经是2的幂。
        如果cap已经是2的幂， 又没有执行这个减1操作，则执行完后面的几条无符号右移操作之后，返回的capacity将是这个cap的2倍。如果不懂，要看完后面的几个无符号右移之后再回来看看
```



