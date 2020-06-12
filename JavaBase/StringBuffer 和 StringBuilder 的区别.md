> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
>
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;

![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)

-----

StringBuilder 和 StringBuffer 的问题已经是老生常谈了，但是为什么还有那么多面试官喜欢问这个问题呢？不知道，我觉得是面试官太low了……🤣🤣

不过我也来把这个知识点给大家总结一下，先说说他们的相同点。

## 1. 相同点

说到这单，我又忍不住要跟大家唠叨唠叨 String 了，我们先看下 String 的一个例子：
```java
String a = "123";
a = "456";
// 打印出来的a为456
System.out.println(a)
```
不是说 String 是不可改变的吗？这怎么就变了呢？逗我吗？其实是没变的，原来那个 123 没变，相当于重新创建了一个 456，然后把 a 指向了这个 456，那个 123 会被回收掉。看看我给大家画的图：

![](https://img-blog.csdnimg.cn/20200612220539886.png)

而 StringBuffer 和 StringBuilder 就不一样了，他们是可变的，当一个对象被创建以后，通过`append()`、`insert()`、`reverse()`、`setCharAt()`、`setLength()`等方法可以改变这个字符串对象的字符序列。也来看个例子：

```java
StringBuffer b = new StringBuffer("123");
b.append("456");
// b打印结果为：123456
System.out.println(b);
```
![](https://img-blog.csdnimg.cn/20200612221256392.png)

OK，那现在我们知道了，StringBuffer 和 StringBuilder 有个共同点，那就是它们代表一个字符可变的字符串。那除此之外，还有没有其他相同点呢？

肯定是有的，比如：
1. StringBuilder 与 StringBuffer 有公共父类 AbstractStringBuilder (抽象类);
2. StringBuilder、StringBuffer 的方法都会调用 AbstractStringBuilder 中的公共方法，如`super.append(...)`。

## 2. 不同点

StringBuffer 是线程安全的，StringBuilder 是非线程安全的。我们看源码都知道，StringBuffer 的方法上面都加了 synchronized 关键字，所以没有线程安全问题，但是性能会受影响，所以 StringBuilder 会比 StringBuffer 快，在单线程环境下，建议使用 StringBuilder。

另外还有个不同点是缓冲区。

先看下 StringBuilder 的`toString()`方法：
```java
@Override
public String toString() {
	// Create a copy, don't share the array
	return new String(value, 0, count);
}
```

再看下 StringBuffer 的`toString()`方法：

```java
@Override
public synchronized String toString() {
    if (toStringCache == null) {
    	toStringCache = Arrays.copyOfRange(value, 0, count);
    }
    return new String(toStringCache, true);
}

```
可以看出：StringBuffer 每次获取 toString 都会直接使用缓存区的 toStringCache 值来构造一个字符串。而 StringBuilder 则每次都需要复制一次字符数组，再构造一个字符串。

所以， StringBuffer 对缓存区优化，不过 StringBuffer 的这个toString 方法仍然是同步的。

OK，StringBuffer 和 StringBuilder 就总结这么多吧，如有问题，欢迎指正。

-----

> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
>
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;

![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)
