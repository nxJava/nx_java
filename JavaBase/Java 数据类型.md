## Java 数据类型

> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;
![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)

-----
Java 数据类型有很多，本文主要从基本类型、包装类型、引用类型和缓存池四个方面来总结，目录如下。
[toc]

### 基本数据类型

基本数据类型有 byte、short、int、long、float、double、boolean、char，关于它们的分类，我画了个图。

![基本类型](https://img-blog.csdnimg.cn/20200601102739431.png)

接下来我主要从字节数、数据范围、默认值、以及用途等方面给大家总结成一个表格，一目了然。

| 数据类型  | 字节数 | 位数 | 最小值 | 最大值 | 默认值 |用途|
| :-------: | :------: | :------: | :------: | :----: | :------: |:---|
|  byte   | 1 | 8      | -128      | 127      |0|byte 类型用在大型数组中节约空间，主要代替整数。因为 byte 变量占用的空间只有 int 类型的四分之一|
| short | 2      | 16      | -32768 | 32767      |0|Short 数据类型也可以像 byte 那样节省空间。一个short变量是int型变量所占空间的二分之一|
|  int  | 4      | 32      |-2^31      |2^31 - 1      |0|一般地整型变量默认为 int 类型|
|  long  | 8      | 64     | -2^63 | 2^63-1 |0L|这种类型主要使用在需要比较大整数的系统上|
|  float  | 4      | 32     | 1.4e-45 | 3.4e38 |0.0F|float 在储存大型浮点数组的时候可节省内存空间。浮点数不能用来表示精确的值，如货币|
|  double  | 8      | 64     | 4.9e-324 | 1.8e308 |0.0D|浮点数的默认类型为double类型。double类型同样不能表示精确的值，如货币|
|  boolean  |   ×    | ×      | ×      | ×      |false|true和false|
|  char  | 2      | 16     | \u0000      | \uffff  ||char 数据类型可以储存任何字符|

### 包装数据类型

上面提到的基本类型都有对应的包装类型，为了方便读者查看，我也整了一个表格。

| 基本类型  | 包装类型 | 最小值 | 最大值 |
| :-------: | :------: | :------: | :----: |
|byte|Byte|`Byte.MIN_VALUE`|`Byte.MAX_VALUE`|
|short|Short|`Short.MIN_VALUE`|`Short.MAX_VALUE`|
|int|Integer|`Integer.MIN_VALUE`|`Integer.MAX_VALUE`|
|long|Long|`Long.MIN_VALUE`|`Long.MAX_VALUE`|
|float|Float|`Float.MIN_VALUE`|`Float.MAX_VALUE`|
|double|Double|`Double.MIN_VALUE`|`Double.MAX_VALUE`|
|boolean|Boolean|`Boolean.MIN_VALUE`|`Boolean.MAX_VALUE`|
|char|Char|`Char.MIN_VALUE`|`Char.MAX_VALUE`|



### 引用类型

在Java中，引用类型的变量非常类似于 C/C++ 的指针。引用类型指向一个对象，指向对象的变量是引用变量。这些变量在声明时被指定为一个特定的类型，比如 Student、Dog 等。变量一旦声明后，类型就不能被改变了。

对象、数组都是引用数据类型。所有引用类型的默认值都是null。一个引用变量可以用来引用任何与之兼容的类型。例如：

```java
Dog dog = new Dog("旺财")。
```



### 数据类型转换

包装类型和基本类型之间如何转化呢？

```java
Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
int y = x;         // 拆箱 调用了 X.intValue()
```
基本类型之间如何转换呢？有两个点：

> 1. 把大容量的类型转换为小容量的类型时必须使用强制类型转换。
> 2. 把小容量的类型转换为大容量的类型可以自动转换。

比如：

```java
int i =128;   
byte b = (byte)i;
long c = i;
```



### 缓存池

大家思考一个问题：`new Integer(123)` 与` Integer.valueOf(123)` 有什么区别？

有些人可能知道，有些人可能不知道。其实他们的区别很大。
>1. new Integer(123) 每次都会新建一个对象；
>2. Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

我写个demo大家就知道了

```java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

编译器会在自动装箱过程调用`valueOf()`方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。如：
```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```
`valueOf()`方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。我们看下源码就知道。

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```
根据数据类型的不一样，这个缓存池的上下限也不同，比如这个 Integer，就是 -128~127。不过这个上界是可调的，在启动 jvm 的时候，通过 -XX:AutoBoxCacheMax=<size> 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。

参考自：[StackOverflow : Differences between new Integer(123), Integer.valueOf(123) and just 123](https://stackoverflow.com/questions/9030817/differences-between-new-integer123-integer-valueof123-and-just-123)

&nbsp;

OK，这篇文章就分享到这，Java 的数据类型虽然简单，但是里面还是有很多小细节值得我们玩味的，希望这篇文章能给大家带来一些帮助。

&nbsp;

------

> 文章可以白嫖，公众号不能不关注，哈哈哈，手动滑稽🤣🤣&nbsp;
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;
![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)
