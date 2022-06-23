ArrayList和LinkedList的区别

1. ArrayList底层是基础数组实现，LinkedList底层是基于链表实现
2. ArrayList进行数据的读取和修改时速度较快，因为可以通过索引下标直接访问，而LinkedList访问较慢，因为需要移动指针遍历链表.
3. 在非尾节点增删元素时，LinkedList的增删一般比ArrayList快，但当数据量较大的时候，且一直往中间位置添加元素，Arraylist会比LinkedList快(此时LinkedList二分查找元素的效率最低，O(n)),在尾节点增删元素，ArrayList比linkedList快，因为linkedlist还要涉及指针的移动。
4. LinkedList比ArrayList更占内存，因为LinkedList的节点除了存储数据，还存储了两个引用，一个指向前一个元素，一个指向后一个元素。