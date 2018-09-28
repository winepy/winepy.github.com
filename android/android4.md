## ArrayList，LinkList，Vector区别与联系

arraylist基于动态数组的数据结构，而linklist基于链表的数据结构；
1. 数组对于大量的从中间增加数据，或者从中间删除数据，这种操作十分消耗性能；
而链表对于这种操作只需要对指针进行控制就行；
2. 数组查找某个数据比较快，因为不需要移动指针，而链表每次操作都必须移动指针；


```
1,ArrayList,LinkedList,Vector这三个类都实现了java.util.List接口，但它们有各自不同的特性

2,ArrayList Vector内部实现都是数组，LinkedList是一个双向链表

3，LinkedList 是基于链表结构实现，所以在类中包含了 first 和 last 两个指针(Node)。Node 中包含了上一个节点和下一个节点的引用，这样就构成了双向的链表。每个 Node 只能知道自己的前一个节点和后一个节点，但对于链表来说，这已经足够了。

4，Vector是线程安全的，适合多线程下的编程。ArrayList,LinkedList是线程不安全的，适合单线程下的编程。因此Vector的操作比ArrayList,LinkedList开销要大

5，LinkedList适合增删操作，因为只需要改变指针，而查询某个元素必须从第一个开始，因此时间复杂度是O(n)。ArrayList 适合查询操作，查询某个元素的时间复杂度是O(1);
```