---
title: 几种支持timeExpire的Map
date: 2016-01-11 14:41:35
tags: Java
---

。类似 Redis 和 Java 的缓存 Ehcache 都提供了一种很便利 Cache 功能，即可以设置一条记录（一个存储）的超时时间，当超时时间达到时，会自动进行回收，即失效
。今天工作中有了类似的需求，但是因为需求不多，并不想再项目中额外的引入 Redis，造成太多的不必要的开销，所以研究比较了现在几个实现该功能的 Map
<!--more-->

## 0x01 Google Guava
***
。好吧，把 Guava 放在第一位完全是因为对 Google 的尊敬。Google Guava 项目是一个谷歌自己的 Java 库，其中放了很多 Google 自己使用的对 JDK 本身的增强机制。比如上面的带时间限制的 Map、不可变集合等
。具体介绍可以参见下面的 Reference，不过其实也就是 Google 官方文档的翻译。
。因为只需要使用到带 timeExpired 的 Map，也就是 Guava 中的 Cache，所以一个很简单的案例：
```java
public static void main(String[] args) throws InterruptedException {
    Cache<Object, Object> demo = CacheBuilder.newBuilder()
            .maximumSize(10000)
            .expireAfterWrite(1, TimeUnit.SECONDS)
            .build();
    demo.put("a1","a1");
    demo.put("a2","a2");
    demo.put("a3","a3");
    demo.put("a4","a4");
    System.out.println(demo.size());
    Thread.sleep(2000);
    System.out.println(demo.size());
}
```
。当然，不要忘了引入包，我这里使用 Gradle，所以在 build.gradle 中添加依赖：
```java
compile('com.google.guava:guava:19.0')
```

。但是上述方法在我这边是不能实现到时间自动回收的，哪怕我 build 中传入自己 CacheLoader，Google 搜了，结果如下：
![](/images/20150111/guavadontwork.png)

。当然可能是因为自己代码有问题，不过从上面的例子能看出来，起码，使用 Guava 并不是很简单，也许 API 并不和自己平时熟悉的一致

## 0x02 Apache Collections4
***
。Collections 是大名鼎鼎的 Apache Commons 系列中提供的一系列对 JDK 集合的增强。其他比如 lang 中对 StringUtils 等工具类都是很好的工具，也很有参考、使用价值
。这里我们使用的是 Collections4 中的
。查看手册我们知道，可以直接在构建时传递一个 Expire Time ，之后只需要像使用普通的 Map 即可  
```java
public static void main(String[] args) throws InterruptedException {
    PassiveExpiringMap map = new PassiveExpiringMap(1, TimeUnit.SECONDS);
    map.put("a1","a1");
    map.put("a2","a2");
    map.put("a3","a3");
    map.put("a4","a4");
    System.out.println(map.size());
    Thread.sleep(2000);
    map.put("a5","a5");
    System.out.println(map.size());
    Thread.sleep(2000);
    System.out.println(map.size());
}
```

。打印结果为 4、1、0，如我们猜测的一样，可见，其使用起来相对 Guava 方便了很多
。但是这里有一个坑，就是 PassiveExpiringMap 是非线程安全的，所以如果有多线程的需求，最好手工加锁

## 0x03 Expiringmap
***
。好吧，这知识一个歪果仁的作品，从 Github 上看，也不是来自 Apache 或者 Google 这样的大组织
。但是看过他源码之后，并没有找到什么问题，也完全能够实现功能
。其使用如下：
```java
Map<String, Connection> map = ExpiringMap.builder()
  .expiration(30, TimeUnit.SECONDS)
  .build();
```

。还可以添加 Policy，比如访问后多少秒超时、写入后多少秒超时：
```java
Map<String, Connection> map = ExpiringMap.builder()
  .expirationPolicy(ExpirationPolicy.ACCESSED)
  .build();
```

。使用也和 PassiveExpiringMap 一样，原生的 put\get ，但是比 PassiveExpiriingMap 不同的是，他是一个线程安全的 Map
。内部原理其实也并不复杂，维护了一个 Scheduled 的 SingleThread 来持续回收过期的 Entry 而已：
```java
void scheduleEntry(ExpiringEntry<K, V> entry) {
if (entry == null || entry.scheduled)
  return;
Runnable runnable = null;
synchronized (entry) {
  if (entry.scheduled)
    return;
  final WeakReference<ExpiringEntry<K, V>> entryReference = new WeakReference<ExpiringEntry<K, V>>(entry);
  runnable = new Runnable() {
    @Override
    public void run() {
      ExpiringEntry<K, V> entry = entryReference.get();
      writeLock.lock();
      try {
        if (entry != null && entry.scheduled) {
          entries.remove(entry.key);
          notifyListeners(entry);
        }
        try {
          // Expires entries and schedules the next entry
          Iterator<ExpiringEntry<K, V>> iterator = entries.valuesIterator();
          boolean schedulePending = true;
          while (iterator.hasNext() && schedulePending) {
            ExpiringEntry<K, V> nextEntry = iterator.next();
            if (nextEntry.expectedExpiration.get() <= System.nanoTime()) {
              iterator.remove();
              notifyListeners(nextEntry);
            } else {
              scheduleEntry(nextEntry);
              schedulePending = false;
            }
          }
        } catch (NoSuchElementException ignored) {
        }
      } finally {
        writeLock.unlock();
      }
    }
  };
  Future<?> entryFuture = expirer.schedule(runnable, entry.expectedExpiration.get() - System.nanoTime(),
      TimeUnit.NANOSECONDS);
  entry.schedule(entryFuture);
}
}
```

## 结论
***
。如果考虑到牌子的话，推荐选择 Guava 或者 Collections4
。但是因为 Guava 我实验并未成功，所以我个人推荐 Apache，但是，如果你对线程安全又十分在意，而且不想手工加锁，可以考虑 ExpiringMap，而且介于现在项目中并没有太多代码，所以维护成本并不是特别高，又或者你可以完全按照其思想自己再实现一个。

## Reference
***
[Google Guava官方教程（中文版）](http://ifeve.com/google-guava/)
[Guava CacheBuilder doesn’t call removal listener](https://stackoverflow.com/questions/21986551/guava-cachebuilder-doesnt-call-removal-listener)
[PassiveExpiringMap Reference](http://commons.apache.org/proper/commons-collections/apidocs/org/apache/commons/collections4/map/PassiveExpiringMap.html)