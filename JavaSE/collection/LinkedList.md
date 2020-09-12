## 1. 概述：

1. LinkedList ，基于节点实现的**双向**链表的 List ，每个节点都指向前一个和后一个节点从而形成链表（但是需要注意就是LinkedList 1.6之前使用的是循环链表）
2. 相比 ArrayList 来说，我们日常开发使用 LinkedList 相对比较少 但是重要程度和ArrayList是一致的。

## 2. 类图：

![image-20200909180641336](https://gitee.com/ssdls/blogimg/raw/master/img/20200909180641.png)

如下 3 个接口是 ArrayList 一致的：

- `java.util.List` 接口
- `java.io.Serializable` 接口
- `java.lang.Cloneable` 接口

如下1个接口 ArrayList 有LinkedList中没有的：

- `java.util.RandomAccess` 接口，LinkedList 不同于 ArrayList 的很大一点，不支持随机访问。

如下1个接口是LinkedList有但是ArrayList没有的：

- `java.util.Deque` 接口，提供**双端**队列的功能，LinkedList 支持快速的在头尾添加元素和读取元素，所以很容易实现该特性。

  > 注意，以为 LinkedList 实现了 Deque 接口，所以我们在 「5. 添加单个元素」 和 「7. 移除单个元素」 中，会看到多种方法
  >
  > 也因为实现 Deque 即可以作为队列使用，也可以作为栈使用。当然，作为双端队列，也是可以的

继承了 `java.util.AbstractSequentialList` 抽象类，它是 AbstractList 的子类，实现了只能**连续**访问“数据存储”（例如说链表）的 `get(int index) ``add(int index, E element)` 等等**随机**操作的方法。可能这样表述有点抽象，点到 `java.util.AbstractSequentialList` 抽象类中看看这几个方法，基于迭代器顺序遍历后，从而实现后续的操作。

## 3. 属性：

LinkedList属性同样很少 只有三个。如下图所示：环境是1.8双向链表实现（1.6及以前使用的是环形链表实现）![640](https://gitee.com/ssdls/blogimg/raw/master/img/20200909164615.png)

- 通过 Node 节点指向前后节点，从而形成双向链表。

- first 和last属性：链表的头尾指针。

- - 在初始时候，`first` 和 `last` 指向 `null` ，因为此时暂时没有 Node 节点。
  - 在添加完首个节点后，创建对应的 Node 节点 `node1` ，前后指向 `null` 。此时，`first` 和 `last` 指向该 Node 节点。
  - 继续添加一个节点后，创建对应的 Node 节点 `node2` ，其 `prev = node1` 和 `next = null` ，而 `node1` 的 `prev = null` 和 `next = node2` 。此时，`first` 保持不变，指向 `node1` ，`last` 发生改变，指向 `node2` 。

- `size` 属性链表的节点数量。通过它进行计数，避免每次需要 List 大小时，需要从头到尾的遍历![640 (34)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909164729.png)

## 4. 构造方法：

LinkedList 一共有两个构造方法（相比 ArrayList 来说，因为没有容量一说，所以不需要提供 `ArrayList(int initialCapacity)` ）这个构造方法，代码如下：![640 (1)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909164834.png)

## 5.添加单个元素：

`add(E e)` 方法，**顺序**添加单个元素到链表。代码如下：![640 (2)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909165022.png)

- `<X>` 处，调用 `linkLast(E e)` 方法，将新元素添加到链表的尾巴。所以，`add(E e)` 方法，实际就是 `linkLast(E e)` 方法。

- 总体来说，代码实现比较简单。重点就是对 `last` 的处理。

- 相比 ArrayList 来说，无需考虑容量不够时的扩容

  看懂这个方法后，我们来看看 `add(int index, E element)` 方法，**插入**单个元素到指定位置。代码如下：

![640 (3)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909165343.png)

- `<1>` 处，如果刚好等于链表大小，直接调用 `linkLast(E element)` 方法，添加到尾部即可。

- `<2>` 处，先调用 `node(int index)` 方法，获得第 `index` 位置的 Node 节点 `node` 。然后，调用 `linkBefore(E element, Node node)` 方法，将新节点添加到 `node` 的前面。相当于说，`node` 的前一个节点的 `next` 指向新节点，`node` 的 `prev` 指向新节点。

  `node(int index)` 方法，获得第 `index` 个 Node 节点。（思考题：**LinkedList使用双向链表而不是使用单向链表的原因是什么？看完下面的代码相信会有答案**）代码如下：![640 (4)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909165533.png)

`linkBefore(E e, Node<E> succ)` 方法，添加元素 `e` 到 `succ` 节点的前面。代码如下：![640 (5)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909165645.png)

**LinkedList**实现了 **Queue 接口**，所以它实现了 `push(E e)` 和 `offer(E e)` 方法，添加元素到链表的头尾 这个不是重点不细说：![640 (6)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909165920.png)  

![](https://gitee.com/ssdls/blogimg/raw/master/img/20200909170114.png)

## 6. 添加多个元素：

`addAll(Collection<? extends E> c)` 方法，批量添加多个元素,内部调用的是 `addAll(int index, Collection<? extends E> c)` 方法，表示在队列之后，继续添加 `c` 集合。代码如下:![640 (8)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909170205.png)

- `<2>` 处，获得第 `index` 位置的节点 `succ` ，和其前一个节点 `pred` 。分成两种情况，看注释。实际上，ArrayList 在添加 `c` 集合的时候，也是分成跟 LinkedList 一样的两种情况，只是说 LinkedList 在一个方法统一实现了。
- `<3>` 处，遍历 `a` 数组，添加到 `pred` 的后面。其实，我们可以把 `pred` 理解成“尾巴”，然后不断的指向新节点，而新节点又称为新的 `pred` 尾巴。如此反复插入~
- `<4>` 处，修改 `succ` 和 `pred` 的指向。根据 `<2>` 处分的两种情况，通过判断succ是有为null进行处理

## 7. 移除单个元素：

`remove(int index)` 方法，移除指定位置的元素，并返回该位置的原元素。代码如下：![640 (9)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909170614.png)

首先，调用 `node(int index)` 方法，获得第 `index` 的 Node 节点。然后，调用 `unlink(Node<E> x)` 方法，移除该节点 `unlink(Node<E> x)` 方法，代码如下：![640 (10)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909170944.png)

`remove(Object o)` 方法，移除首个为 `o` 的元素，并返回是否移除到（相比 `remove(int index)` 方法来说，需要去寻找首个等于 `o` 的节点进行移除。当然，最终还是调用 `unlink(Node<E> x)` 方法，移除该节点）代码如下：

![640 (11)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909171213.png)

`removeFirstOccurrence(Object o)`和 `removeLastOccurrence(Object o)` 方法，分别实现移除链表首个或者是最后一个为o的节点。代码如下：

![640 (12)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909171415.png)

`remove()` 方法，移除链表首个节点。代码如下：![640 (13)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909173809.png)

`removeLast()` 方法，移除链表末尾节点。代码如下：和上面的一个方法实现差不多![640 (14)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909173833.png)

`poll()` 和 pop() 方法，移除链表的头或尾，差异点在于链表为空时候，会不会抛出 **NoSuchElementException** 异常

![640 (15)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909174006.png)

## 8. 移除多个元素:

`removeAll(Collection<?> c)` 方法，批量移除指定的多个元素。代码如下：该方法，是通过父类 AbstractCollection 来实现的，通过迭代器来遍历 LinkedList ，然后判断 `c` 中如果包含，则进行移除。

![640 (16)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909174222.png)

`retainAll(Collection<?> c)` 方法，求 LinkedList 和指定多个元素的交集。简单来说，恰好和 `removeAll(Collection<?> c)` 相反，移除不在 `c` 中的元素 在<X> 处判断条件不同，代码如下:

![640 (17)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909174252.png)

## 9. 查找单个元素：

`indexOf(Object o)` 方法，查找首个为指定元素的位置。代码如下：

![640 (18)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175119.png)

`contains(Object o)` 方法，就是基于该方法实现。代码如下

![640 (19)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175159.png)

有时我们需要查找最后一个为指定元素的位置，所以会使用到 `lastIndexOf(Object o)` 方法 （上述的三个方法本质上实现的逻辑是一样的 不进行赘述）代码如下：

![640 (20)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175211.png)

## 10.获取指定位置的元素：

`get(int index)` 方法，获得指定位置的元素。代码如下：随机访问 `index` 位置的元素，时间复杂度为 O(n)

因为 LinkedList 实现了 Deque 接口，所以它实现了 `#peekFirst()` 和 `#peekLast()` 方法，分别获得元素到链表的头尾。代码如下  

因为 LinkedList 实现了 Queue 接口，所以它实现了 `#peek()` 和 `#peek()` 和 `#element()` 方法，分别获得元素到链表的头。代码如下

![640 (21)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175305.png)

LinkedList 实现了 Deque 接口，所以它实现了 `peekFirst()` 和 `peekLast()` 方法，分别获得元素到链表的头尾。代码如下：

![640 (22)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175503.png)

LinkedList 实现了 Queue 接口，所以它实现了 `peek()`  和 `element()` 方法，分别获得元素到链表的头

![640 (23)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175525.png)

## 11. 设置指定位置的元素：

`set(int index, E element)` 方法，设置指定位置的元素。代码如下：

![640 (24)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175559.png)

## 12. LinkedList转换成数组：

`toArray()` 方法，将 LinkedList转换成 `[]` 数组。代码如下：需要注意的一点就是返回的是Object[] 数组

![640 (25)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175715.png)

实际场景下 我们可能想要指定的是T泛型数组 这样就需要使用toArray(T[] a)方法 代码如下：

![640 (1)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175746.webp)

## 13.求哈希值 hashCode：

`hashCode()` 方法，求 LinkedList的哈希值。该方法，是通过父类 AbstractList 来实现的，通过 `for` 来遍历 LinkedList ，然后进行求哈希，代码如下：（这里为什么使用31就不说了  没有看过上一篇的可以看一下上一篇：ArrayList源码分析）

![640 (2)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909175845.webp)

## 14. 判断相等：

`equals(Object o)` 方法，判断是否相等 代码如下 该方法是通过父类 AbstractList 来实现的，通过迭代器，实现遍历比对

![image-20200909181406425](https://gitee.com/ssdls/blogimg/raw/master/img/20200909181406.png)

## 15. 清空链表：

`clear()` 方法，清空链表。代码如下：

![image-20200909181513095](https://gitee.com/ssdls/blogimg/raw/master/img/20200909181513.png)

## 16. 序列化与反序列化：

`writeObject(java.io.ObjectOutputStream s)` 方法，实现 LinkedList 的序列化，readObject(java.io.ObjectInputStream s)` 方法，反序列化为链表。代码如下：

![image-20200909181902152](https://gitee.com/ssdls/blogimg/raw/master/img/20200909181902.png)

![image-20200909181924913](https://gitee.com/ssdls/blogimg/raw/master/img/20200909181924.png)

## 17.克隆：

`clone()` 方法，克隆 LinkedList 对象。代码如下：

![image-20200909181941122](https://gitee.com/ssdls/blogimg/raw/master/img/20200909181941.png)

注意，`first`、`last` 等都是重新初始化进来，不与原 LinkedList 共享。

## 总结：

LinkedList 做一个简单的小结

- LinkedList 基于节点实现的**双向**链表的 List ，每个节点都指向前一个和后一个节点从而形成链表。

- LinkedList 提供队列、双端队列、栈的功能。

  > 因为 `first` 节点，所以提供了队列的功能的实现的功能。因为 `last` 节点，所以提供了栈的功能的实现的功能。因为同时具有 `first` + `last` 节点，所以提供了双端队列的功能。

- LinkedList 随机访问**平均**时间复杂度是 O(n) ，查找指定元素的**平均**时间复杂度是 O(n) 。

- LinkedList 移除指定位置的元素的最好时间复杂度是 O(1) ，最坏时间复杂度是 O(n) ，平均时间复杂度是 O(n) 。

  > 最好时间复杂度发生在头部、或尾部移除的情况。

- LinkedList 移除指定位置的元素的最好时间复杂度是 O(1) ，最坏时间复杂度是 O(n) ，平均时间复杂度是 O(n) 。

  > 最好时间复杂度发生在头部移除的情况。

- LinkedList 添加元素的最好时间复杂度是 O(1) ，最坏时间复杂度是 O(n) ，平均时间复杂度是 O(n) 。

  > 最好时间复杂度发生在头部、或尾部添加的情况。

因为 LinkedList 提供了多种添加、删除、查找的方法，会根据是否能够找到对应的元素进行操作，抛出 NoSuchElementException 异常 常用的方法详见下表：

|      | 返回结果                                        | 抛出异常   |
| :--- | :---------------------------------------------- | :--------- |
| 添加 | `add(…)`、`offset(...)`                         |            |
| 删除 | `remove(int index)`、`remove(E e)`、`poll(E E)` | `remove()` |
| 查找 | `get(int index)`、`peek()`                      | `poll()`   |

 这个表主要整理了 List 和 Queue 的操作，暂时没有整理 Deque 的操作。因为，Deque 相同前缀的方法，表现结果同 Queue。