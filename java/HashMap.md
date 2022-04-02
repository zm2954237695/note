# 1 概述

HashMap是基于[哈希表]()实现的,每一个元素是一个key-value对,其内部通过单[链表]()解决冲突问题,容量不足(超过了阀值)时,同样会自动增长.

HashMap是非线程安全的,只适用于单线程环境,多线程环境可以采用并发包下的concurrentHashMap

HashMap 实现了Serializable接口，因此它支持序列化，实现了Cloneable接口，能被克隆

HashMap是基于[哈希表]()的Map接口的非同步实现.此实现提供所有可选的映射操作,并允许使用null值和null键.此类不保证映射的顺序,特别是它不保证该顺序恒久不变.

Java8中又对此类底层实现进行了优化，比如引入了[红黑树]()的结构以解决哈希碰撞

# 2 HashMap的数据结构

hashMap的主结构类似于一个数组,添加值时通过key确定储存位置.
每个位置是一个Entry的数据结构,该结构可组成[链表]().
当发生冲突时,相同hash值的键值对会组成[链表]().
这种数组+[链表]()的组合形式大部分情况下都能有不错的性能效果,Java6、7就是这样设计的.
然而,在极端情况下,一组（比如经过精心设计的）键值对都发生了冲突，这时的哈希结构就会退化成一个[链表]()，使HashMap性能急剧下降.

所以在Java8中,HashMap的结构实现变为数组+[链表]()+[红黑树]()

# 三大集合与迭代子

HashMap使用三大集合和三种迭代子来轮询其Key、Value和Entry对象



```java
public class HashMapExam {
    public static void main(String[] args) {
        Map map = new HashMap(16);
        for (int i = 0; i < 15; i++) {
            map.put(i, new String(new char[]{(char) ('A'+ i)}));
        }
        System.out.println("======keySet=======");
        Set set = map.keySet();
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        System.out.println("======values=======");
        Collection values = map.values();
        Iterator stringIterator=values.iterator();
        while (stringIterator.hasNext()) {
            System.out.println(stringIterator.next());
        }
        System.out.println("======entrySet=======");
        for (Map.Entry entry : map.entrySet()) {
            System.out.println(entry);
        }
    }
}
```

# 4 源码分析

```java
//默认的初始容量16,且实际容量是2的整数幂 
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
    //最大容量(传入容量过大将被这个值替换)
    static final int MAXIMUM_CAPACITY = 1 << 30;
    // 默认加载因子为0.75(当表达到3/4满时,才会再散列),这个因子在时间和空间代价之间达到了平衡.更高的因子可以降低表所需的空间,但是会增加查找代价,而查找是最频繁操作
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    //桶的树化阈值：即 链表转成红黑树的阈值，在存储数据时，当链表长度 >= 8时，则将链表转换成红黑树
    static final int TREEIFY_THRESHOLD = 8;
   // 桶的链表还原阈值：即 红黑树转为链表的阈值，当在扩容（resize（））时（HashMap的数据存储位置会重新计算），在重新计算存储位置后，当原有的红黑树内数量 <= 6时，则将 红黑树转换成链表
    static final int UNTREEIFY_THRESHOLD = 6;
   //最小树形化容量阈值：即 当哈希表中的容量 > 该值时，才允许树形化链表 （即 将链表 转换成红黑树）
```

因为[红黑树]()的平均查找长度是log(n)，长度为8的时候，平均查找长度为3，如果继续使用[链表]()，平均查找长度为8/2=4，这才有转换为树的必要
[链表]()长度如果是小于等于6，6/2=3，虽然速度也很快的，但是转化为树结构和生成树的时间并不会太短

还有选择6和8，中间有个差值7可以有效防止[链表]()和树频繁转换
假设一下，如果设计成[链表]()个数超过8,则[链表]()转换成树结构，[链表]()个数小于8则树结构转换成[链表]()，如果一个HashMap不停的插入、删除元素，[链表]()个数在8左右徘徊，就会频繁的发生树转[链表]()、[链表]()转树，效率会很低。