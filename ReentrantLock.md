##### ReentrantLock

ReentrantLock通过实现lock()接口来实现加锁和释放锁.

核心功能通过内部类Sync对象实现。该类基础于AbstractQueuedSynchronizer，即AQS

ReentrantLock的公平锁和非公平锁通过两个静态内部类FairSync和NonfairSync再去继承Sync重写获取锁的方法。这是由于AQS对尝试获取锁和释放锁的逻辑是预留给子类是重写的，父类的原方法只是直接抛出异常。

##### AQS中的核心变量

```java
public abstract class AbstractQueuedSynchronizer
    extends AbstractOwnableSynchronizer
    implements java.io.Serializable {
     /**
     * Head of the wait queue, lazily initialized.  Except for
     * initialization, it is modified only via method setHead.  Note:
     * If head exists, its waitStatus is guaranteed not to be
     * CANCELLED.
     */
    private transient volatile Node head;
 
    /**
     * Tail of the wait queue, lazily initialized.  Modified only via
     * method enq to add new wait node.
     */
    private transient volatile Node tail;
 
    /**
     * The synchronization state.
     */
    private volatile int state;  

	static final class Node {
        /** Marker to indicate a node is waiting in shared mode */
        static final Node SHARED = new Node();
        /** Marker to indicate a node is waiting in exclusive mode */
        static final Node EXCLUSIVE = null;

        /** waitStatus value to indicate thread has cancelled */
        static final int CANCELLED =  1;
        /** waitStatus value to indicate successor's thread needs unparking */
        static final int SIGNAL    = -1;
        /** waitStatus value to indicate thread is waiting on condition */
        static final int CONDITION = -2;
        /**
         * waitStatus value to indicate the next acquireShared should
         * unconditionally propagate
         */
        static final int PROPAGATE = -3;

        volatile int waitStatus;

        volatile Node prev;

        volatile Node next;

        volatile Thread thread
    }
 } 

```

- 核心变量使用volatile修饰,保证可见性。

- 核心变量使用CAS修改
- AQS使用volatile维护获取对象锁的等待队列,通过链表实现维护头节点head和尾节点tail.每个要获取对象锁的线程被封装称Node(AQS的一个静态内部类)对象.
- AQS使用volatile修饰int变量state,用来记录同步锁的状态。0表示没有线程持有，大于0代表已被线程持有。因为是可重入锁，所以state值代表一个线程持有锁的此时。当释放锁时，state减1，直到减到0则完全释放。
- AQS继承AbstractOwnableSynchronizer使用Thread类型的变量`exclusiveOwnerThread`记录获取到对象锁的线程。这个变量没有volatile修饰,因为这个变量赋值的前提是获取到对象锁，而获取到对象锁就不会存在并发问题了。
- 通过AQS的Node.waitStatus节点的状态来决定线程在获取不到同步锁时是否需要调用LockSupport的park沉睡以及当调用释放锁的方法释放超成功时调用LockSupport的unpark方法来唤醒线程，让沉睡的线程重新参与竞争。



##### 加锁

非公平锁通过ReentrantLock的内部类NonfairSync实现，如果获取成功，记录获取锁的线程。

![image-20220425215839320](C:\Users\逐梦\AppData\Roaming\Typora\typora-user-images\image-20220425215839320.png)

关键的代码`compareAndSetState`调用的还是`AQS`的代码

```java
java.util.concurrent.locks.AbstractQueuedSynchronizer#compareAndSetState
```

```java
protected final boolean compareAndSetState(int expect, int update) {
    // See below for intrinsics setup to support this
    return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```

通过CAS修改state的值，如果为0则代表没有线程持有锁，修改为1，代表当前对象锁已经被持有，其他线程再竞争都要进入等待队列。如果加锁成功，会把当前线程赋值给`AbstractOwnableSynchronizer`的`exclusiveOwnerThread`属性，用来记录当前持有对象锁的线程。

如果加锁失败，会调用acquire方法，父类的tryAcquire方法会抛出异常，留给子类去实现。非公平锁的实现，默认是ReentrantLock的内部类Sync，这里会再次去尝试获取一次锁。先获取到当前要获取锁的线程，如果再次判断对象锁是否被线程持有，如果没有持有，则直接加锁成功。加锁加锁记录加锁的线程。如果还是加锁失败，则判断当前线程是否持有正在持有对象锁的线程，如果是的话，则state持有的次数加1，然后将state再次刷回主存，还是加锁成功。从这里可以看出ReentrantLock支持可重入锁。相对的当释放锁的时候，如果多个同步方法所需要的同步锁对象是同一个，加锁的时候每进入一次同步方法，则`state`累加1，而当每个同步方法执行结束的时候则依次递减1，而不是直接将锁完全释放。如果还是没有获取到锁，就把当前线程包装称Node对象加入等待队列。等待队列是一个链表，维护了头部和尾部(尾插法)