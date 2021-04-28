### map 6种遍历方式

#### 1.迭代器 EntrySet

```java
Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
    Map.Entry<Integer, String> entry = iterator.next();
    System.out.print(entry.getKey());
    System.out.print(entry.getValue());
    
    // 只有迭代器可以安全删除
    iterator.remove();
}
```



#### 2.迭代器 KeySet

```java
Iterator<Integer> iterator = map.keySet().iterator();
while (iterator.hasNext()) {
    Integer key = iterator.next();
    System.out.print(key);
    System.out.print(map.get(key));
}
```



#### 3.ForEach EntrySet

```java
for (Map.Entry<Integer, String> entry : map.entrySet()) {
    System.out.print(entry.getKey());
    System.out.print(entry.getValue());
}
```



#### 4.ForEach KeySet

```java
for (Integer key : map.keySet()) {
    System.out.print(key);
    System.out.print(map.get(key));
}
```



#### 5.Lambda

```java
map.forEach((key, value) -> {
    System.out.print(key);
    System.out.print(value);
});
```



#### 7.Streams API 多线程

```java
map.entrySet().parallelStream().forEach((entry) -> {
    System.out.print(entry.getKey());
    System.out.print(entry.getValue());
});
```



#### 8 总结

只有迭代器可以安全删除，综合性能和简洁性来说推荐使用 lambda和stream



####

####