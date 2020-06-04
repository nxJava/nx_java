> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
>
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;

![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)

-----
Java 有一系列的关键字，在代码中各自有自己的重要用途与意义，今天就带着大家一起来了解一下 Java 的关键字！

Java 的关键字特别多，本文先主要介绍一下各个关键字的用途，然后重点介绍一下 final、static 和 this 这三个常用的关键字，其他的关键字大家用到的时候可以去网上查一下。

## Java关键字汇总

| 数据类型  | 含义 |
| :-------: | :------ |
|abstract|表明类或者成员方法具有抽象属性|
|assert	|断言，用来进行程序调试|
|boolean	|基本数据类型之一，布尔类型|
|break	|提前跳出一个块|
|byte	|基本数据类型之一，字节类型|
|case	|用在switch语句之中，表示其中的一个分支|
|catch	|用在异常处理中，用来捕捉异常|
|char|	基本数据类型之一，字符类型|
|class|	声明一个类|
|const|	保留关键字，没有具体含义|
|continue|	回到一个块的开始处|
|default|	默认，例如，用在switch语句中，表明一个默认的分支|
|do|	用在do-while循环结构中|
|double	|基本数据类型之一，双精度浮点数类型|
|else	|用在条件语句中，表明当条件不成立时的分支|
|enum|	枚举|
|extends|	表明一个类型是另一个类型的子类型，这里常见的类型有类和接口|
|final|	用来说明最终属性，表明一个类不能派生出子类，或者成员方法不能被覆盖，或者成员域的值不能被改变，用来定义常量|
|finally|	用于处理异常情况，用来声明一个基本肯定会被执行到的语句块|
|float|	基本数据类型之一，单精度浮点数类型|
|for|	一种循环结构的引导词|
|goto|	保留关键字，没有具体含义|
|if|	条件语句的引导词|
|implements|	表明一个类实现了给定的接口|
|import|	表明要访问指定的类或包|
|instanceof	|用来测试一个对象是否是指定类型的实例对象|
|int|	基本数据类型之一，整数类型|
|interface|	接口|
|long|	基本数据类型之一，长整数类型|
|native|	用来声明一个方法是由与计算机相关的语言（如C/C++/FORTRAN语言）实现的|
|new|	用来创建新实例对象|
|package|	包|
|private|	一种访问控制方式：私用模式|
|protected|	一种访问控制方式：保护模式|
|public|	一种访问控制方式：共用模式|
|return|	从成员方法中返回数据|
|short|	基本数据类型之一,短整数类型|
|static	|表明具有静态属性|
|strictfp|	用来声明FP_strict（单精度或双精度浮点数）表达式遵循IEEE 754算术规范 [1] |
|super	|表明当前对象的父类型的引用或者父类型的构造方法|
|switch|	分支语句结构的引导词|
|synchronized|	表明一段代码需要同步执行|
|this|	指向当前实例对象的引用|
|throw|	抛出一个异常|
|throws|	声明在当前定义的成员方法中所有需要抛出的异常|
|transient	|声明不用序列化的成员域|
|try|	尝试一个可能抛出异常的程序块|
|void|	声明当前成员方法没有返回值|
|volatile|	表明两个或者多个变量必须同步地发生变化|
|while|	用在循环结构中|

是不是不列不知道，一列吓一跳，原来 Java 里还有这么多关键字，大部分我们平时都在用，只是没有特意去注意这个而已。所以大部分大家都很熟了，有些不常用的我也不总结了，我接下来主要总结几个比较有代表性的关键字。

## final 关键字

Java 中的 final 关键字可以用来修饰类、方法和变量（包括实例变量和局部变量）

### final 修饰类

使用final修饰类则该类不能被继承，同时类中的所有成员方法都会被隐式定义为final方法（只有在需要确保类中的所有方法都不被重写时才使用final修饰类）。**final修饰类的成员变量是可以更改的**

```java
public final class FinalClass{

	int i = 1;
    
	void test(){
		System.out.println("FinalClass:test");
	}
    
	public static void main( String[] args ){
		FinalClass ficl = new FinalClass();
    
		System.out.println("ficl.i = " + ficl.i);
		ficl.i = 2;
		System.out.println("ficl.i = " + ficl.i);
	}
}
```

### final 修饰方法

使用final修饰方法可以将方法“锁定”，以防止任何继承类对方法的修改，也即使用final修饰方法，则子类无法重写（但并不影响继承和重载，即子类调用父类方法不受影响)。

### final 修饰变量

使用final关键字修饰变量是使用最多的情况。

使用final修饰变量的值不能做再次更改，即不能重新赋值。

>1. 如果final修饰的变量是基本数据类型，则变量的值不可更改；
>2. 如果final修饰的变量是引用数据类型，则该变量不能再次指向其他引用（如重新指向新的对象或数组）但是该变量本身的内容可以再做修改（如数组本身的内容，或者对象的属性的修改）。
>

无论final修饰实例变量还是局部变量，都必须在使用前显式赋初值。
>1. Java中的实例变量系统会对其默认赋初值，但是局部变量必须先声明后赋值再使用。
>2. 虽然对于实例变量，系统会默认赋初值，但是Java仍然规定final修饰的实例变量必须显式赋初值。实例变量显式赋值的时机可以是在声明时直接赋值，也可以先声明，后在构造方法中赋值（对于含有多个构造方法，必须在每个构造方法中都显示赋值）。

我们来看个例子：
```java
public void fun(){

	//BufferedImage src = null;//0. 声明的同时赋值
	BufferedImage src;//1. 这里不用赋初值,也不会出错
	try{
		src = ImageIO.read(new File("1.jpg"));//2.
	} catch (Exception e){
	//3. 如果出异常了就会进入这里,那么src可能无法被赋值
	}
	
	System.out.println(src.getHeight()); //4. src不一定有值,所以无法使用
}
```
如果静态变量同时被final修饰则可以将变量视为全局变量，即在整个类加载期间，其值不变。（static保证变量属于整个类，在类加载时只对其分配一次内存；final保证变量的值不被改变）

## static 关键字

static方法一般称作静态方法，由于静态方法不依赖于任何对象就可以进行访问，因此对于静态方法来说，是没有this的，因为它不依附于任何对象，**既然都没有对象，就谈不上this了**。并且由于这个特性，在静态方法中不能访问类的非静态成员变量和非静态成员方法，因为非静态成员方法/变量都是必须依赖具体的对象才能够被调用。

但是要注意的是，虽然在静态方法中不能访问非静态成员方法和非静态成员变量，但是在非静态成员方法中是可以访问静态成员方法/变量的。也就是说，反过来是可以的。

如果说想在不创建对象的情况下调用某个方法，就可以将这个方法设置为static。static修饰成员方法最大的作用，就是可以使用"`类名.方法名`"的方式调用方法，避免了new出对象的繁琐和资源消耗。

我们最常见的static方法就是main方法。至于为什么main方法必须是static的，这是因为程序在执行main方法的时候没有创建任何对象，因此只有通过类名来访问。



### static 变量

static变量也称作静态变量，静态变量和非静态变量的区别是：静态变量被所有的对象所共享，在内存中只有一个副本，它当且仅当在类初次加载时会被初始化。而非静态变量是对象所拥有的，在创建对象的时候被初始化，存在多个副本，各个对象拥有的副本互不影响。

static成员变量的初始化顺序按照定义的顺序进行初始化。

### static 代码块

static块可以置于类中的任何地方，类中可以有多个static块。在类初次被加载的时候，会按照static块的顺序来执行每个static块，并且只会执行一次。为什么说static块可以用来优化程序性能，是因为它的特性:只会在类加载的时候执行一次。

所谓的代码块就是当我们初始化static修饰的成员时，可以将他们统一放在一个以static开始，用花括号包裹起来的块状语句中。例如：

```java
class Person{
	private Date birthDate;
	 
	public Person(Date birthDate){
		this.birthDate = birthDate;
	}
	 
	boolean isBornBoomer(){
		Date startDate = Date.valueOf("1946");
		Date endDate = Date.valueOf("1964");
		return birthDate.compareTo(startDate)>=0 && birthDate.compareTo(endDate) < 0;
	}
}
```
isBornBoomer是用来这个人是否是1946-1964年出生的，而每次isBornBoomer被调用的时候，都会生成startDate和birthDate两个对象，造成了空间浪费，如果改成这样效率会更好：

```java
class Person{
	private Date birthDate;
	private static Date startDate,endDate;
	static{
		startDate = Date.valueOf("1946");
		endDate = Date.valueOf("1964");
	}
	 
	public Person(Date birthDate){
		this.birthDate = birthDate;
	}
	 
	boolean isBornBoomer(){
		return birthDate.compareTo(startDate)>=0 && birthDate.compareTo(endDate) < 0;
	}
}
```

将一些只需要进行一次的初始化操作都放在static代码块中进行。

## this 关键字

this代表它所在函数所属对象的引用。简单说：哪个对象在调用this所在的函数，this就代表哪个对象。this关键字主要有以下三个作用：

>1. this调用本类中的属性，也就是类中的成员变量；
>2. this调用本类中的其他方法；
>3. this调用本类中的其他构造方法，调用时要放在构造方法的首行。(this语句只能定义在构造函数的第一行，因为在初始化时须先执行)

### 引用成员变量

```java
public class Person{ 
	String name; //定义成员变量name
	private void SetName(String name) { //定义一个参数(局部变量)name
		this.name=name; //将局部变量的值传递给成员变量
	}
}
```
虽然我们可以看明白这个代码的含义，但是作为Java编译器它是怎么判断的呢?到底是将形式参数name的值传递给成员变量name，还是反过来将成员变量name的值传递给形式参数name呢?也就是说，两个变量名字如果相同的话，那么Java如何判断使用哪个变量？此时this这个关键字就起到作用了。this这个关键字其代表的就是对象中的成员变量或者方法。也就是说，如果在某个变量前面加上一个this关键字，其指的就是这个对象的成员变量或者方法，而不是指成员方法的形式参数或者局部变量。

### 调用类的构造器方法

```java
public class Person { 
	public Person(){ //无参构造器方法
		this(“Hello!”);
	}
	public Person(String name){ //定义一个带形式参数的构造方法
	}
}
```
在上述代码中，定义了两个构造方法，一个带参数，另一个没有带参数。在第一个没有带参数的构造方法中，使用了this(“Hello!”)这句代码，这句代码表示什么含义呢?在构造方法中使this关键字表示调用类中的构造方法。

如果一个类中有多个构造方法，因为其名字都相同，跟类名一致，那么这个this到底是调用哪个构造方法呢?其实，这跟采用其他方法引用构造方法一样，都是通过形式参数来调用构造方法的。

注意的是：**利用this关键字来调用构造方法，只有在无参数构造方法中第一句使用this调用有参数的构造方法。否则的话，翻译的时候，就会有错误信息**。这跟引用成员变量不同。如果引用成员变量的话，this关键字是没有位置上的限制的。

### 返回对象的引用
```java
public HttpConfig url(String url) {
	urls.set(url);
	//return this就是返回当前对象的引用(就是实际调用这个方法的实例化对象)
	return this;
}
```
return this就是返回当前对象的引用(就是实际调用这个方法的实例化对象)，就像我们平时使用StringBuilder一样，可以一直`.append()`，因为每次调用，返回的都是该对象的引用。

关于关键字，这篇文章就总结这么多，如有错误，欢迎指正，我们一起进步。

------

> 文章可以白嫖，公众号不能不关注，手动滑稽🤣🤣 &nbsp;
>
> 欢迎大家关注：**武哥聊编程**、**Java学习指南**，您的支持，是我们创作的持续动力！&nbsp;&nbsp;

![武哥聊编程](https://img-blog.csdnimg.cn/202002150421550.jpg)![Java学习指南](https://img-blog.csdnimg.cn/20200601113720522.png)
