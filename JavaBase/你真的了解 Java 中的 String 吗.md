> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
>
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;

![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)

-----

## 1. 看看源码

大家都知道， String 被声明为 final，因此它不可被继承。(Integer 等包装类也不能被继承）。我们先来看看 String 的源码。

在 Java 8 中，String 内部使用 char 数组存储数据。
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```

在 Java 9 之后，String 类的实现改用 byte 数组存储字符串，同时使用 coder 来标识使用了哪种编码。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```
value 数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 String 不可变。

## 2.  不可变有什么好处呢

### 2.1 可以缓存 hash 值

因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。

### 2.2 String Pool 的使用

如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。

### 2.3 安全性

String 经常作为参数，String 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接过程中，String 被改变，改变 String 的那一方以为现在连接的是其它主机，而实际情况却不一定是。

### 2.4 线程安全

String 不可变性天生具备线程安全，可以在多个线程中安全地使用。

## 3. 再来深入了解一下 String

### 3.1 "+" 连接符

字符串对象可以使用“+”连接其他对象，其中字符串连接是通过 StringBuilder（或 StringBuffer）类及其 append 方法实现的，对象转换为字符串是通过 toString 方法实现的。可以通过反编译验证一下：
```java
/**
 * 测试代码
 */
public class Test {
    public static void main(String[] args) {
        int i = 10;
        String s = "abc";
        System.out.println(s + i);
    }
}

/**
 * 反编译后
 */
public class Test {
    public static void main(String args[]) {    //删除了默认构造函数和字节码
        byte byte0 = 10;      
        String s = "abc";      
        System.out.println((new StringBuilder()).append(s).append(byte0).toString());
    }
}

```
由上可以看出，Java中使用"+"连接字符串对象时，会创建一个StringBuilder()对象，并调用append()方法将数据拼接，最后调用toString()方法返回拼接好的字符串。那这个 "+" 的效率怎么样呢？

### 3.2 “+”连接符的效率

使用“+”连接符时，JVM会隐式创建StringBuilder对象，这种方式在大部分情况下并不会造成效率的损失，不过在进行大量循环拼接字符串时则需要注意。比如：

```java
String s = "abc";
for (int i=0; i<10000; i++) {
    s += "abc";
}
```
这样由于大量StringBuilder创建在堆内存中，肯定会造成效率的损失，所以在这种情况下建议在循环体外创建一个StringBuilder对象调用append()方法手动拼接（如上面例子如果使用手动拼接运行时间将缩小到1/200左右）。

与此之外还有一种特殊情况，也就是当"+"两端均为编译期确定的字符串常量时，编译器会进行相应的优化，直接将两个字符串常量拼接好，例如：
```java
System.out.println("Hello" + "World");

/**
 * 反编译后
 */
System.out.println("HelloWorld");
```

## 4. 字符串常量

JVM为了提高性能和减少内存的开销，在实例化字符串的时候进行了一些优化：使用字符串常量池。每当创建字符串常量时，JVM会首先检查字符串常量池，如果该字符串已经存在常量池中，那么就直接返回常量池中的实例引用。如果字符串不存在常量池中，就会实例化该字符串并且将其放到常量池中。由于String字符串的不可变性，**常量池中一定不存在两个相同的字符串**。

![](https://img-blog.csdnimg.cn/20200609003544858.png)

我们来看一个例子：

```java
/**
 * 运行结果为true false
 */
String s1 = "abc";
String s2 = "abc";
String s3 = new String("abc");
System.out.println(s1 == s2);
System.out.println(s1 == s3);
```
由于常量池中不存在两个相同的对象，所以s1和s2都是指向JVM字符串常量池中的"AB"对象。new关键字一定会产生一个对象，并且这个对象存储在堆中。所以`String s3 = new String(“AB”);`产生了两个对象：保存在栈中的s3和保存堆中的String对象。

String 就总结这么多，如果有问题，欢迎讨论。

----
> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
>
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;

![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)

