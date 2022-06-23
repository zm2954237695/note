#### CompleteFuture的API

##### 获取结果和主动触发计算

**获取结果**

```java
T get() //阻塞等待线程执行的结果
T get(long time,TimeUnit unit) //超过指定的时间就会抛出异常
T join()//join抛出的是未经检查的异常 作用基本和get一样
T getNow(T valueIfAbsent)//立即获取结果不阻塞，无需抛出异常，如果计算完成则返回计算的结果，否则返回valueIfAbsent
```

**主动触发计算**

```java
boolean complete(T value) //根据返回值判断是计算值还是默认值,在
```

code:

```java
System.out.println(future.complete("completeValue")+completeFutre.join());
```

##### 对计算结果进行处理

```java
thenApply //计算结果存在依赖关系，这两个线程串行化，有异常不会走下一步
handle //计算结果存在依赖关系，这两个线程串行化，有异常也可以往下走，根据带的异常参数可以进一步处理
```



##### 为什么任何对象都可以成为一个锁(monitor)

每个对象天生都带着一个对象监视器，每一个被锁住的对象都会和Monitor关联起来。



在Hotspot虚拟机中，monitor使用ObjectMonitor实现。java的每个对象都继承自Object类,而object对应ObjectMonitor.java,在jvm内部有一个native的c++对象 与之对应，而ObjectMonitor.hpp,在该hpp的owner记录了持有ObjectMonitor对象的线程。



##### 为什么默认是非公平锁？

1. 恢复挂起的线程是到真正锁的获取是有时间差的，从开发人员来看这个时间微乎其微，但是从CPU的角度来看，这个时间差存在还是很明显的，所以非公平锁能更充分的利用CPU的时间片，尽量减少CPU空闲状态时间。
2. 使用多线程很重要的考虑就是线程切换的开销，当使用非公平锁时，当1个线程请求锁获取同步状态，然后释放同步状态，所以刚释放锁的线程在此刻再次获取同步状态的概率就变得非常大，所以减少了线程的开销。



##### 中断机制

<font size=3 color=blue>首先</font>

<font size=2> 一个线程不应该由其他线程来强制中断或停止，而应该要线程自己自行停止 </font>

<font size=3 color=blue>其次</font>



<font size=2>在java中没有办法立即停止一个线程，而停止线程却显得尤为重要，如取消一个耗时操作。</font>

<font size=2 color=red>中断只是一种协作协商机制，java没有给中断添加任何语法，中断的过程完全要程序员自己实现。若要中断一个线程，可以通过调用该线程的interrupt方法，该方法也仅仅是将线程对象的中断标识设为true</font>



|                 |                                                              |
| --------------- | ------------------------------------------------------------ |
| interrupt()     | <font color=blue>实例方法</font>，该方法仅仅是设置线程的中断状态为true,发起一个协商而不会立刻停止线程 |
| interrupted()   | <font color=blue>静态方法</font>,<font color=red>判断线程是否被中断并清除当前中断状态</font> |
| isinterrupted() | <font color=blue>实例方法</font>，判断当前线程是否被中断     |





















