## 1. 概述：

1. HashSet ，基于 HashMap 的 Set 实现类，在进行操作的时候大多也是调用HashMap的方法，所以这篇文章会比较短。HashMap详细解释可以看上一篇：HashMap源码分析
2. 在业务中，如果我们有去重的需求，一般会考虑使用 HashSet 。
3. 在 Redis 提供的 Set 数据结构，不考虑编码的情况下，它是基于 Redis 自身的 Hash 数据结构实现的。这点，JDK 和 Redis 是相同的。

## 2. 类图：

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAFBbWdib5fnVkDvmzmIsSCwOjtywgVF1BiaA7uc1KcVR1CaQcqEHtEiaHeXHL0Fx80lG7yPAG2mn2D5Q/640?wx_fmt=png)

- 实现 `java.util.Set` 接口。
- 继承 `java.util.AbstractSet` 抽像类。
- 实现 `java.io.Serializable` 接口。
- 实现 `java.lang.Cloneable` 接口。

## 3. 属性：

1. HashSet 只有两个属性，那就是 一个HashMap的`map属性`
2. 另一个就是**final Object PERSENT** 存放到**HashMap的value中**（为什么要使用这个Object 使用null 不是更好吗？还可以节省空间 这个疑问将会在【6.移除单个元素】中解答）

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzWiaIGSpBIwMcSNqAu9w4HZExdZNmiaBJpKybhrcase90Y1yaH9k3qlJpg/640?wx_fmt=png)

## 4. 构造方法：

HashSet一共有五个构造方法，看起来比较的多，但是大多都是调用的HashMap的构造函数，相信看过上一篇HashMap分析都能看得懂，这里就不详细解释了。

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzWOcvibmW4anEdAWCzFgUhWxQcxORMvV9XXmnJYk2ZaLDCOZkladHM6AA/640?wx_fmt=png)

## 5. 添加单个元素：

`add(E e)` 方法，添加单个元素，`map` 的 value 值，就是我们看到的 `PRESENT` ，所以不要说HashSet中value位置上是null了。

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzWv2D1zXDWKQnPqfUbUJlkASKHUfejrP4UVdMsTdKF9xlPEia5DLGKibHA/640?wx_fmt=png)

而添加多个元素，继承自 AbstractCollection 抽象类，通过 `addAll(Collection<? extends E> c)` 方法，方法内部，会逐个调用 `add(E e)` 方法，逐个添加单个元素，代码如下：

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzWVuayBXZPuocabV4B0sHjVtp64CeR2qiaY0qZuHeYObpTEPVdP00ib9hQ/640?wx_fmt=png)

## 6. 移除单个元素：

`remove(Object key)` 方法，移除 key 对应的 value ，并返回该 value 【代码注释中回答了上面的疑问】代码如下：

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzW7ugTZ6Rt7HictlVzV3xaske4weictRg4ficFK2m14vcTMibMvHDE41Eghw/640?wx_fmt=png)





## 7.查找单个元素：

`contains(Object key)` 方法判断 key 是否存在代码如下：

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzW48zOH5xmOxwJ0iaeSiaEuWxpN0rVAuxCMNkT4ibEVvzxKazooLnKHqibAg/640?wx_fmt=png)



## 8. 转换成数组：

`toArray(T[] a)` 方法，转换出 key 数组返回。代码如下：

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzWX7zLWGLGa6Pb1RTPjOkHZgwqZFga1JlgL1I6HlacRFBjN5zxvCqNLA/640?wx_fmt=png)

## 9. 清空：

`clear()` 方法，清空 HashSet 。代码如下：

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzWzs8J5eaicy4HWOAMVGW518ialdmPnyWokBPo30SJSkLx6oE2ZUGsKcdQ/640?wx_fmt=png)

## 10. 克隆：

`clone()` 方法，克隆 HashSet 对象。代码如下：

![img](https://mmbiz.qpic.cn/mmbiz_png/wK7nic6ZIDAEICw7pTanMfviajvssV8PzWFpXDGzX7rDnZ0UQibbru8EmkQDYb3p6z4n44elPCt1ibHRjAWV45ZcfQ/640?wx_fmt=png)

## 总结：

HashSet做一个简单的小结：

- HashSet 是基于 HashMap 的 Set 实现类，底层大多使用的是HashMap的方法。
- HashSet常常用来进行去重操作。