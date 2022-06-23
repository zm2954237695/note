##### ThreadLocal

- ThreadLocal并不解决线程间共享数据的问题
- ThreadLocal适用于变量在线程间隔离且在方法间共享的场景。
- ThreadLocal通过隐式的在不同线程内创建独立实例副本避免了实例线程安全的问题。

ThreadLocalMap是ThreadLocal的一个静态内部类，从字面上可以看出是一个保存ThreadLocal对象map(其实是以ThreadLocal以key)。

JVM内部维护了一个线程版的Map<ThreadLocal,Value>,

通过ThreadLocal对象set方法，结果把ThreadLocal对象自己当作key，放进了ThreadMap里面。每个线程要用到获取值的时候，用当前的线程去Map里面获取，通过这样让每个线程都拥有了自己独立的变量，竞争条件被消除，在并发环境下是安全的变量。

##### 为什么ThreadLocalMap的key使用弱引用

```java
ThreadLocal<String> tl = new ThreadLocal<>();
```

当在一个方法执行完毕后，栈帧销毁，强引用tl也就没有了。但此时线程的ThreadLocalMap里某个Entry的key还指向此ThreadLocal对象。

- 若这个key是强引用，就会导致key指向的ThreadLocal对象不能被gc回收，造成内存泄漏。
- 若这个key是弱引用，就会大概率减少内存泄漏的问题。使用弱引用，可以使ThreadLocal对象在方法执行完毕后顺利被回收且Entry的key引用指向为null.

ThreadLocalMap使用ThreadLocal的弱引用作为key,如果一个ThreadLocal没有外部强引用引用他，那么系统gc的时候，这个ThreadLocal势必会被回收，这样一来，ThreadLocalMap就会key为null的Entry，就没有办法访问这些key为null的Entry的value，如果当前线程再迟迟不结束的话(比如正好使用线程池)，这些key为null的Entry的value就会一直存在一条强引用链。

虽然弱引用，保证了key指向的ThreadLocal对象能被即时回收，但是v指向的value对象是需要ThreadLocalMap调用get、set时发现key为null时才会去回收整个entry、value，因此弱引用不能保证100%不会内存泄漏。我们需要在不使用某个ThreadLocal对象后，手动调用remove方法来删除它。尤其是在线程池中，不仅仅是内存泄漏的问题，因为线程池的线程是重复使用的，意味着中国线程的ThreadLocalMap对象也是重复使用的，如果不手动调用remove方法，那么后面的线程有可能获取到上个线程遗留下来的value值，造成bug。

expungeStateEntry(),cleanSomeSlots,replaceStateEntry这3方法会把清除key为null的脏Entry