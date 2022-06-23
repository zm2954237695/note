##### JMM

JMM本身是一种抽象的概念并不真实存在，它仅仅描述的是一组约定或规范。

- 通过JMM实现线程和主内存之间的抽象关系
- 屏蔽各个硬件平台和操作系统的内存访问差异以实现让java程序在各种平台下都能达到一致的内存访问效果。s









##### happens-before总原则

- 如果一个操作happens-before另一个操作，那么第一个操作的执行结果对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。
- 两个操作之间存在happens-before关系，并不意味着一定要按照happens-before制定的顺序来执行。如果重排序之后的执行结果与按照happens-before关系来执行的结果一致，那么这种重排序并不非法。

##### volatile

**两大特性**

- 可见性

  - 当写一个volatile变量时，这个变量的值会立即刷新回到主内存。
  - 当读一个volatile变量时，总是能够读取到这个变量的最新值，也就是这个变量最后被修改的值
  - 当某个线程收到通知，去读取volatile修饰的变量时，线程私有工作内存的数据失效，需要重新回到主内存中读取最新的数据。

- （有序性）禁止重排序

   <font size=1>重排序是指编译器和处理器为了优化程序性能而对指令序列进行重新排序的一种手段。不存在数据依赖关系，可以重排，</font><font size=1 color=red>存在数据依赖关系，不能重排序</font><font size=1>但重排后的指令绝对不能改变原有的串行语义

  

  **内存屏障**：是一种屏障指令，它使得CPU或编译器对屏障指令的前和后所发出的内存操作执行一个排序的约束。也叫内存栅栏或栅栏指令。

  

  

  **写屏障**

  ```StoreStore屏障```

  禁止上面的普通写和下面的volatile写操作重排序

  前面所有的普通写操作，数据都已经刷新到主内存

  普通写和volatile写禁止重排；volatile写和volatile写重排。

  

  ```volatile写操作```

  

  ```
  storeLoad屏障
  ```

  禁止上面的volatile写和下面的volatile读/写或普通写操作重排序

  前面volatile写的操作，数据都已经刷新到主内存

  volatile写和普通写禁止重排；volatile写和volatile读/写禁止重排

  

  **读屏障**

  

  ```volatile读操作```

  ```LoadLoad屏障```

  禁止下面的普通读、volatile读和上面的volatile读重排序；volatile读和volatile读禁止重排

  ```loadstore屏障```

  禁止上面的volatile读和下面的volatile写或普通写重排序

  volatile读和普通写禁止重排；volatile读和volatile写禁止重排

  

  **CAS**

  CAS是靠硬件实现的从而在硬件层面提高效率，最底层还是交给硬件来保证原子性和可见性。实现方式是基于硬件平台的汇编指令.

  核心思想就是:比较要更新变量的值V和预期值E，相等才会将V的值设置为N,如果不相等自旋再来。

  **AtomicReference**(CAS-原子引用类)

  **两大缺点**

  - 循环时间长开销很大
  - 会有ABA问题

  **AtomicStampedReference**(版本号时间戳原子引用)

  - 携带版本号的引用类型原子类，可以解决ABA问题(解决修改过几次)

  **AtomicMarkableReference**

  - 原子更新带有标志位的引用类型对象(解决是否修改过 状态戳简化为true和false)

  

  

  

  

  

  

  

  

  

  **AtomicIntFieldUpdater**

  <font color=blue>**目的**</font> 以一种线程安全的方式操作非线程安全对象内的某些字段  

  <font color=red>在需要对对象的某个属性进行写操作时，不需要使用synchronized来锁住整个对象，而可以对对象的某些字段进行原子更新，从而提高并发粒度</font>

  

  <font color=blue>要求</font>  - 更新的对象必须使用public volatile修饰符修饰

  

  ​        - 因为对象的属性修改类型原子类都是抽象类，所以每次使用都必须使用静态方法newUpater()创建一个更新器，并且需要设置想要更新的类和属性

  ```java
  AtomicIntegerFieldUpdater updater = 
      AtomicIntegerFieldUpdater.newUpdater(myObject.class,"name");
  ```

  **LongAdder和LongAccumulator**

  - LongAdder只能用来计算加法，且从零开始计算

  - LongAccumulator提供了自定义的函数操作 

     

    

  ##### LongAdder的add(long x)方法

  1. 如果cells表为空，尝试用CAS更新base字段，成功则退出；
  2. 如果cells表为空，CAS更新base字段失效，出现竞争，uncontended为true，调用longAccumulate;
  3. 如果cells表非空，但当前线程映射的槽为空，uncontended为true，调用longAccumulate;
  4. 如果cells表非空，且当前线程映射的槽非空，CAS更新Cell的值，成功则返回，否则，uncontended设为false，调用LongAccumulate;

  

  

  

  