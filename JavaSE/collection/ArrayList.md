## 1. 概述：

1. ArrayList ，基于 `[]` 数组实现的，支持**自动扩容**的动态数组。相比数组来说，因为其支持**自动扩容**的特性，成为我们日常开发中，最常用的集合类之一 另一个不用说就是HashMap
2. 在前些年，实习或初级工程师的面试，可能最爱问的就是 ArrayList 和 LinkedList 的区别与使用场景。不过貌似，现在问的已经不多了，因为现在信息非常发达，这种常规面试题已经无法区分能力了。当然即使如此，也不妨碍我们拿它开刀，毕竟是咱的“老朋友”

## 2. 类图：

![640 (1)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160842.png)

实现了 4 个接口，分别是：

- `java.util.List` 接口，提供数组的添加、删除、修改、迭代遍历等操作。
- `java.util.RandomAccess` 接口，表示 ArrayList 支持**快速**的随机访问。
- `java.io.Serializable` 接口，表示 ArrayList 支持序列化的功能。
- `java.lang.Cloneable` 接口，表示 ArrayList 支持克隆。

继承了 `java.util.AbstractList` 抽象类，而 AbstractList 提供了 List 接口的骨架实现，大幅度的减少了实现**迭代遍历**相关操作的代码。可能这样表述有点抽象，可以点到 `java.util.AbstractList` 抽象类中看看，例如说 `#iterator()`、`#indexOf(Object o)` 等方法。不过在下面中我们会看到，ArrayList 大量重写了 AbstractList 提供的方法实现。所以，AbstractList 对于 ArrayList 意义不大，更多的是 AbstractList 其它子类享受了这个福利

## 3. 属性：

![640](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160851.png)

ArrayList 的属性很少，仅仅 **2** 个。如下图所示：

- `elementData` 属性：元素数组。其中，图中红色空格代表我们已经添加元素，白色空格代表我们并未使用。
- `size` 属性：数组大小。注意，`size` 代表的是 ArrayList 已使用 `elementData` 的元素的数量，对于开发者看到的 `#size()` 也是该大小。并且，当我们添加新的元素时，恰好其就是元素添加到 `elementData` 的位置（下标）。当然，我们知道 ArrayList **真正**的大小是 `elementData` 的大小

![640 (2)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160855.png)

## 4. 构造方法：

ArrayList 一共有三个构造方法，我们分别来看看：

1. **ArrayList(int initialCapacity)**

   `ArrayList(int initialCapacity)` 构造方法，根据传入的初始化容量，创建 ArrayList 数组。如果我们在使用时，如果预先指到数组大小，一定要使用该构造方法，可以避免数组扩容提升性能，同时也是合理使用内存。代码如下：比较特殊的是，如果初始化容量为 0 时，使用 `EMPTY_ELEMENTDATA` 空数组。在添加元素的时候，会进行扩容创建需要的数组

   ![640 (3)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160859.png)

2. **ArrayList(Collection<? extends E> c)**

   `ArrayList(Collection<? extends E> c)` 构造方法，使用传入的 `c` 集合，作为 ArrayList 的 `elementData` 。代码如下：比较让人费解的是，在 `<X>` 处的代码。它是用于解决 JDK-6260652 的 Bug 。它在 JDK9 中被解决，也就是说，JDK8 还会存在该问题

   ![640 (4)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160902.png)

3. **ArrayList()**

   无参数构造方法 `ArrayList()` 构造方法，也是我们使用最多的构造方法。代码如下：

   ![640 (5)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160905.png)

- 在我们学习 ArrayList 的时候，一直被灌输了一个概念，在未设置初始化容量时，ArrayList 默认大小为 10 。但是此处，我们可以看到初始化为 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` 这个空数组。这是为什么呢？ArrayList 考虑到节省内存，一些使用场景下仅仅是创建了 ArrayList 对象，实际并未使用。所以，ArrayList 优化成初始化是个空数组，在首次添加元素时，才真正初始化为容量为 10 的数组。
- 那么为什么单独声明了 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` 空数组，而不直接使用 `EMPTY_ELEMENTDATA`呢？在下文中，我们会看到 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` 首次扩容为 10 ，而 `EMPTY_ELEMENTDATA` 按照 **1.5倍** 扩容从 0 开始而不是 10 

## 5. 添加单个元素到数组：

`add(E e)` 方法，**顺序**添加单个元素到数组。代码如下：

![640 (6)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160909.png)

- `<1>` 处，增加数组修改次数 `modCount` 。在父类 AbstractList 上，定义了 `modCount` 属性，用于记录数组修改次数

- `<2>` 处，如果元素添加的位置就超过末尾（数组下标是从 0 开始，而数组大小比最大下标大 1），说明数组容量不够，需要进行扩容，那么就需要调用 `grow()` 方法，进行扩容。稍后我们在 「**6. 数组扩容**」 小节来讲。

- `<3>` 处，设置到末尾。

- `<4>` 处，数量大小加一。

  总体流程上来说，抛开扩容功能，和我们日常往 `[]` 数组里添加元素是一样的。看懂这个方法后，那么 `add(int index, E element)` 方法道理也是一致的，**插入**单个元素到指定位置。代码如下：

![640 (7)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160913.png)

## 6. 数组扩容：

`grow()` 方法，扩容数组，并返回它。整个的扩容过程，首先创建一个新的更大的数组，一般是 **1.5 倍**大小（为什么说是一般呢，稍后我们会看到，会有一些小细节），然后将原数组复制到新数组中，最后返回新数组。代码如下：

![640 (8)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160916.png)

- `<1>` 处，调用 `#grow(int minCapacity)` 方法，要求扩容后**至少**比原有大 1 。因为是最小扩容的要求，实际是允许比它大。

- `<2>`，如果原容量大于 0 时，又或者数组不是 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` 时，则计算新的数组大小，并创建扩容。

  * `ArraysSupport newLength(int oldLength, int minGrowth, int prefGrowth)` 方法，计算新的数组大小。简单来说，结果就是 `Math.max(minGrowth, prefGrowth) + oldLength` ，按照 `minGrowth` 和 `prefGrowth` 取大的。

  - 一般情况下，从 `oldCapacity >> 1` 可以看处，是 **1.5 倍**扩容。但是会有两个特殊情况：1）初始化数组要求大小为 0 的时候，`0 >> 1` 时（`>> 1 为右移操作，相当于除以 2`）还是 0 ，此时使用 `minCapacity`传入的 1 。2）在下文中，我们会看到添加多个元素，此时传入的 `minCapacity` 不再仅仅加 1 ，而是扩容到 `elementData` 数组恰好可以添加下多个元素，而该数量可能会超过当前 ArrayList **0.5** 倍的容量。

- `<3>` 处，如果是 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` 数组，直接创建新的数组即可。思考下，如果无参构造方法使用 `EMPTY_ELEMENTDATA` 的话，无法实现该效果了。

  既然有数组扩容方法，那么是否有缩容方法呢？在 `trimToSize()` 方法中，会创建大小恰好够用的新数组，并将原数组复制到其中。代码如下：

![640 (9)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160919.png)

同时，提供 `ensureCapacity(int minCapacity)` 方法，保证 `elementData` 数组容量至少有 `minCapacity`  这个方法比较的简单 可以理解成是用来主动扩容的。代码如下:

![640 (10)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160922.png)

## 7.添加多个元素：

`addAll(Collection<? extends E> c)` 方法，批量添加多个元素。在我们明确知道会**添加多个元素时**，**推荐使用该方法而不是添加单个元素**(这个原因是为什么呢？？将在下面的代码中进行解释)代码如下：![640 (11)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160925.png)

- `<1>` 处，如果 `elementData` 剩余的空间不足，则进行扩容。要求扩容的大小，至于能够装下 `a` 数组。当然，在 「**6. 数组扩容**」 的小节，我们已经看到，如果要求扩容的空间太小，则扩容 **1.5 倍**。

- `<2>` 处，将 `a` 复制到 `elementData` 从 `s` 开始位置。

  总的看下来，就是 `#add(E e)` 方法的批量版本，优势就正如我们在本节开头说的，避免可能多次扩容。

  `addAll(int index, Collection<? extends E> c)` 方法，从指定位置开始插入多个元素原理是一致的就不进行赘述

## 8.移除单个元素

`remove(int index)` 方法，移除指定位置的元素，并返回该位置的原元素 调用 `fastRemove(Object[] es, int i)` 方法(本质上就是数组的拷贝)快速移除代码如下：

![640 (12)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160929.png)

![640 (13)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160936.png)

`remove(Object o)` 方法，移除首个为 `o` 的元素，并返回是否移除到 和 `remove(int index)` 差不多，不同的一点就是需要通过o获取首个为 `o` 的位置，之后就调用 `fastRemove(Object[] es, int i)` 方法，快速移除即可。

![640 (14)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160939.png)

## 9. 移除多个元素：

`removeRange(int fromIndex, int toIndex)` 方法，批量移除 `[fromIndex, toIndex)` 的多个元素，注意不包括 `toIndex` 的元素 Java中的api 都是 左闭右开区间的 `<X>` 处，调用 `shiftTailOverGap(Object[] es, int lo, int hi)` 方法，移除 `[fromIndex, toIndex)` 的多个元素：

![640 (15)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160942.png)

![640 (16)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160946.png)

- 和 `fastRemove(Object[] es, int i)` 方法一样的套路，先挪后置 `null` 
- 有一点要注意，ArrayList 特别喜欢把多行代码写成一行 修改数组的大小 `size` 在 `i = (size -= hi - lo)` 

`removeAll(Collection<?> c)` 方法，批量移除指定的多个元素。实现逻辑比较简单，但是看起来会比较绕。简单来说，通过两个变量 `w`（写入位置）和 `r`（读取位置），按照 `r` 顺序遍历数组(`elementData`)，如果不存在于指定的多个元素中，则写入到 `elementData` 的 `w` 位置，然后 `w` 位置 + 1 ，跳到下一个写入位置。通过这样的方式，实现将不存在 `elementData` 覆盖写到 `w` 位置。可能理解起来有点绕，当然看代码也会有点绕 代码如下：

![640 (17)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160948.png)

![640 (18)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160951.png)

- `<1.1>` 处，遍历到尾，都没不符合条件的，直接返回 `false` 。也就是说，丫根就不需要进行移除的逻辑。
- `<1.2>` 处，如果包含结果不符合 `complement` 时，结束循环。可能有点难理解，我们来举个例子。假设 `elementData` 是 `[1, 2, 3, 1]` 时，`c` 是 `[2]` 时，那么在遍历第 0 个元素 `1` 时，则 `c.contains(es[r]) != complement => false != false` 不符合，所以继续缓存；然后，在遍历第 1 个元素 `2` 时，`c.contains(es[r]) != complement => true != false` 符合，所以结束循环。此时，我们便找到了第一个需要移除的元素的位置。当然，移除不是在这里执行，我们继续往下看
- `<2>` 处，设置开始写入 `w` 为 `r` ，注意不是 `r++` 。这样，我们后续在循环 `elementData` 数组，就会从 `w` 开始写入。并且此时，`r` 也跳到了下一个位置，这样间接我们可以发现，`w` 位置的元素已经被“跳过”了。
- `<3>` 处，继续遍历 `elementData` 数组，如何符合条件，则进行移除。可能有点难理解，我们继续上述例子。遍历第 2 个元素 `3` 时候，`c.contains(es[r]) == complement => false == false` 符合，所以将 `3` 写入到 `w` 位置，同时 `w` 指向下一个位置；遍历第三个元素 `1` 时候，`c.contains(es[r]) == complement => true == false` 不符合，所以不进行任何操作。
- `<4>` 处，如果 contains 方法发生异常，则将 `es` 从 `r` 位置的数据写入到 `es` 从 `w` 开始的位置。这样，保证我们剩余未遍历到的元素，能够挪到从从 `w` 开始的位置，避免多出来一些元素。
- `<5>` 处，是不是很熟悉，将数组 `[w, end)` 位置赋值为 `null`

## 10. 查找单个元素：

`indexOf(Object o)` 方法，查找首个为指定元素的位置。代码如下：

![640 (19)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160954.png)

`contains(Object o)` 方法，就是基于该方法实现。代码如下：

![640 (20)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160957.png)

有时我们需要查找最后一个为指定元素的位置，所以会使用到 `lastIndexOf(Object o)` 方法 （上述的三个方法本质上实现的逻辑是一样的 不进行赘述）代码如下：

![640 (21)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909160959.png)

## 11. 获取指定位置的元素

`get(int index)` 方法，获得指定位置的元素。代码如下：随机访问 `index` 位置的元素，时间复杂度为 O(1) 

![640 (22)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161003.png)

## 12. 设置指定位置的元素

`set(int index, E element)` 方法，设置指定位置的元素。代码如下：

<img src="https://gitee.com/ssdls/blogimg/raw/master/img/20200909161006.png" alt="640 (23)" style="zoom:150%;" />

## 13. ArrayList转换成数组

`toArray()` 方法，将 ArrayList 转换成 `[]` 数组。代码如下：需要注意的一点就是返回的是Object[] 数组![640 (24)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161021.png)

实际场景下 我们可能想要指定的是T泛型数组 这样就需要使用toArray(T[] a)方法 代码如下：

![640 (25)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161023.png)

## 14. 求哈希值 hashCode

`hashCode()` 方法，求 ArrayList 的哈希值。代码如下：

![640 (26)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161026.png)

为什么使用 31 作为乘子呢？？这是一个好问题《Effect Java》中有相应的解释：

> The value 31 was chosen because it is an odd prime. If it were even and the multiplication overflowed, information would be lost, as multiplication by 2 is equivalent to shifting. The advantage of using a prime is less clear, but it is traditional. A nice property of 31 is that the multiplication can be replaced by a shift and a subtraction for better performance: `31 * i == (i << 5) - i``. Modern VMs do this sort of optimization automatically.

简单翻译一下：

> 选择数字31是因为它是一个奇质数，如果选择一个偶数会在乘法运算中产生溢出，导致数值信息丢失，因为乘二相当于移位运算。选择质数的优势并不是特别的明显，但这是一个传统。同时，数字31有一个很好的特性，即乘法运算可以被移位和减法运算取代，来获取更好的性能：`31 * i == (i << 5) - i`，现代的 Java 虚拟机可以自动的完成这个优化 本质上两点：一点选的太小容易hash冲突 太大 容易导致int类型的溢出 第二点：31 可以使用移位运算进行简化 效率高

## 15. 判断相等

`equals(Object o)` 方法，判断是否相等。代码如下：**这里留一个问题：ArrayList 中add 1 LinkedList 中add 1 那这两个调用equals方法 返回 true or false？为什么需要两个方法进行判断明明使用equalsRange方法就能实现功能 ？看完下面的相信你会有结果**

![640 (27)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161031.png)

![640 (28)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161038.png)

![640 (29)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161041.png)

## 16. 清空数组

`clear()` 方法，清空数组。代码如下：比较简单 就是数组的清空

![640 (30)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161053.png)

## 17. 序列化与反序列化

`writeObject(java.io.ObjectOutputStream s)` 方法，实现 ArrayList 的序列化

`readObject(java.io.ObjectInputStream s)` 方法，反序列化数组。代码如下：

![640 (31)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161057.png)

* `<3>` 写入 `elementData` 元素的数组。是一个 `transient` 修饰的属性。为什么呢？因为 `elementData` 数组，并不一定是全满的，而可能是扩容的时候有一定的预留，如果直接序列化，会有很多空间的浪费，所以只序列化从 `[0, size)` 的元素，减少空间的占用

![640 (32)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161124.png)

## 18. 克隆

`clone()` 方法，克隆 ArrayList 对象。代码如下：

![640 (33)](https://gitee.com/ssdls/blogimg/raw/master/img/20200909161127.png)

## 总结：

ArrayList 做一个简单的小结：

- ArrayList 是基于 `[]` 数组实现的 List 实现类，支持在数组容量不够时，一般按照 **1.5** 倍**自动**扩容。同时，它支持**手动**扩容、**手动**缩容。
- ArrayList 随机访问时间复杂度是 O(1) ，查找指定元素的**平均**时间复杂度是 O(n) 。
- ArrayList 移除指定位置的元素的最好时间复杂度是 O(1) ，最坏时间复杂度是 O(n) ，平均时间复杂度是 O(n) 。最好时间复杂度发生在末尾移除的情况。
- ArrayList 移除指定元素的时间复杂度是 O(n) 因为首先需要进行查询，然后在使用移除指定位置的元素，无论怎么计算，都需要 O(n) 的时间复杂度。
- ArrayList 添加元素的最好时间复杂度是 O(1) ，最坏时间复杂度是 O(n) ，平均时间复杂度是 O(n) 。最好时间复杂度发生在末尾添加的情况

