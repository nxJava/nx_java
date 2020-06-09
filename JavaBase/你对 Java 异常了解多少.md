> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
>
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;

![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)

-----

## 1. 异常的概念

如果某个方法不能按照正常的途径完成任务，就可以通过另一种路径退出方法。在这种情况下会抛出一个封装了错误信息的对象。此时，这个方法会立刻退出同时不返回任何值。另外，调用这个方法的其他代码也无法继续执行，异常处理机制会将代码执行交给异常处理器。

![Java异常概念](https://img-blog.csdnimg.cn/20200427134201871.png)

## 2. Java 中异常分为哪些种类
按照异常需要处理的时机分为编译时异常（也叫强制性异常）也叫CheckedException 和运行时异常（也叫非强制性异常）也叫RuntimeException。

只有java 语言提供了Checked 异常，Java 认为 Checked 异常都是可以被处理的异常，所以Java 程序必须显式处理Checked 异常。如果程序没有处理Checked 异常，该程序在编译时就会发生错误无法编译。这体现了Java 的设计哲学：没有完善错误处理的代码根本没有机会被执行。对Checked 异常处理方法有两种：

1. 当前方法知道如何处理该异常，则用try...catch 块来处理该异常。
2. 当前方法不知道如何处理，则在定义该方法是声明抛出该异常。

运行时异常只有当代码在运行时才发行的异常，编译时不需要try catch。Runtime 如除数是0 和数组下标越界等，其产生频繁，处理麻烦，若显示申明或者捕获将会对程序的可读性和运行效率影响很大。所以由系统自动检测并将它们交给缺省的异常处理程序。当然如果你有处理要求也可以显示捕获它们。

那么，调用下面的方法，会得到什么结果呢？

```java
public int getNum(){
	try {
		int a = 1/0;
		return 1;
	} catch (Exception e) {
		return 2;
	}finally{
		return 3;
	}
}
```
代码在走到第3 行的时候遇到了一个MathException，这时第四行的代码就不会执行了，代码直接跳转到catch语句中，走到第6 行的时候，异常机制有这么一个原则如果在catch 中遇到了return 或者异常等能使该函数终止的话那么有finally 就必须先执行完finally 代码块里面的代码然后再返回值。因此代码又跳到第8 行，可惜第8 行是一个return 语句，那么这个时候方法就结束了，因此第6 行的返回结果就无法被真正返回。如果finally 仅仅是处理了一个释放资源的操作，那么该道题最终返回的结果就是2。因此上面返回值是3。

## 3. error 和exception 有什么区别？
Error 类和Exception 类的父类都是Throwable 类，他们的区别如下。
Error 类一般是指与虚拟机相关的问题，如系统崩溃，虚拟机错误，内存空间不足，方法调用栈溢出等。对于这类错误的导致的应用程序中断，仅靠程序本身无法恢复和和预防，遇到这样的错误，建议让程序终止。
Exception 类表示程序可以处理的异常，可以捕获且可能恢复。遇到这类异常，应该尽可能处理异常，使程序恢复运行，而不应该随意终止异常。
Exception 类又分为运行时异常（Runtime Exception）和受检查的异常(Checked Exception )，运行时异常；ArithmaticException,IllegalArgumentException，编译能通过，但是一运行就终止了，程序不会处理运行时异常，出现这类异常，程序会终止。而受检查的异常，要么用try。。。catch 捕获，要么用throws 字句声明抛出，交给它的父类处理，否则编译不会通过。

## 4. throw 和 throws 的区别是什么？
Java 中的异常处理除了包括捕获异常和处理异常之外，还包括声明异常和拋出异常，可以通过 throws 关键字在方法上声明该方法要拋出的异常，或者在方法内部通过 throw 拋出异常对象。

throws 关键字和 throw 关键字在使用上的几点区别如下：

1. throw 关键字用在方法内部，只能用于抛出一种异常，用来抛出方法或代码块中的异常，受查异常和非受查异常都可以被抛出。
2. throws 关键字用在方法声明上，可以抛出多个异常，用来标识该方法可能抛出的异常列表。一个方法用 throws 标识了可能抛出的异常列表，调用该方法的方法中必须包含可处理异常的代码，否则也要在方法签名中用 throws 关键字声明相应的异常。

## 5. Java 的异常处理机制
Java 对异常进行了分类，不同类型的异常分别用不同的Java 类表示，所有异常的根类为`java.lang.Throwable`，Throwable 下面又派生了两个子类：Error 和Exception，Error 表示应用程序本身无法克服和恢复的一种严重问题。
Exception 表示程序还能够克服和恢复的问题，其中又分为系统异常和普通异常，系统异常是软件本身缺陷所导致的问题，也就是软件开发人员考虑不周所导致的问题，软件使用者无法克服和恢复这种问题，但在这种问题下还可以让软件系统继续运行或者让软件死掉，例如，数组脚本越界（ArrayIndexOutOfBoundsException），空指针异常（NullPointerException）、类转换异常（ClassCastException）；普通异常是运行环境的变化或异常所导致的问题，是用户能够克服的问题，例如，网络断线，硬盘空间不够，发生这样的异常后，程序不应该死掉。java 为系统异常和普通异常提供了不同的解决方案，编译器强制普通异常必须try..catch 处理或用throws 声明继续抛给上层调用方法处理，所以普通异常也称为checked 异常，而系统异常可以处理也可以不处理，所以，编译器不强制用try..catch 处理或用throws 声明，所以系统异常也称为unchecked 异常。

## 6. 请写出你最常见的5 个RuntimeException

这是面试过程中，很喜欢问的问题，下面列举几个常见的RuntimeException。

1）java.lang.NullPointerException 空指针异常；出现原因：调用了未经初始化的对象或者是不存在的对象。
2）java.lang.ClassNotFoundException 指定的类找不到；出现原因：类的名称和路径加载错误；通常都是程序试图通过字符串来加载某个类时可能引发异常。
3）java.lang.NumberFormatException 字符串转换为数字异常；出现原因：字符型数据中包含非数字型字符。
4）java.lang.IndexOutOfBoundsException 数组角标越界异常，常见于操作数组对象时发生。
5）java.lang.IllegalArgumentException 方法传递参数错误。
6）java.lang.ClassCastException 数据类型转换异常。
7）java.lang.NoClassDefFoundException 未找到类定义错误。
8）SQLException SQL 异常，常见于操作数据库时的SQL 语句错误。
9）java.lang.InstantiationException 实例化异常。
10）java.lang.NoSuchMethodException 方法不存在异常。

## 7. final、finally、finalize 的区别
1）final：用于声明属性，方法和类，分别表示属性不可变，方法不可覆盖，被其修饰的类不可继承。
2）finally：异常处理语句结构的一部分，表示总是执行。
3）finalize：Object 类的一个方法，在垃圾回收器执行的时候会调用被回收对象的此方法，可以覆盖此方法提供垃圾收集时的其他资源回收，例如关闭文件等。该方法更像是一个对象生命周期的临终方法，当该方法被系统调用则代表该对象即将“死亡”，但是需要注意的是，我们主动行为上去调用该方法并不会导致该对象“死亡”，这是一个被动的方法（其实就是回调方法），不需要我们调用。

## 8. NoClassDefFoundError 和 ClassNotFoundException 区别
NoClassDefFoundError 是一个 Error 类型的异常，是由 JVM 引起的，不应该尝试捕获这个异常。

引起该异常的原因是 JVM 或 ClassLoader 尝试加载某类时在内存中找不到该类的定义，该动作发生在运行期间，即编译时该类存在，但是在运行时却找不到了，可能是变异后被删除了等原因导致；

ClassNotFoundException 是一个受查异常，需要显式地使用 try-catch 对其进行捕获和处理，或在方法签名中用 throws 关键字进行声明。当使用 Class.forName, ClassLoader.loadClass 或 ClassLoader.findSystemClass 动态加载类到内存的时候，通过传入的类路径参数没有找到该类，就会抛出该异常；另一种抛出该异常的可能原因是某个类已经由一个类加载器加载至内存中，另一个加载器又尝试去加载它。

Java 异常 就总结这么多，如果有问题，欢迎讨论。

----
> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
>
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;

![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)

