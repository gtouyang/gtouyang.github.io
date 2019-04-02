---
title: Java-Basic
date: 2019-03-30 16:20:26
categories: 笔记
tags:
- Java
- Java Learning Route
---
谈一谈java的基础语法
<!--more-->
---

# 数据类型 #

## 基本类型 ##

- byte/8
- char/16
- short/16
- int/32
- float/32
- long/64
- double/64
- boolean/~

boolean 只有两个值：true、false，可以使用 1 bit 来存储，但是具体大小没有明确规定。JVM 会在编译时期将 boolean 类型的数据转换为 int，使用 1 来表示 true，0 表示 false。JVM 支持 boolean 数组，但是是通过读写 byte 数组来实现的。

## 包装类型 ##

基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成。

```java
Integer x = 2;     // 装箱
int y = x;         // 拆箱
```

## 缓存池 ##

`new Integer(123)`与`Integer.valueOf(123)`的区别在于：

- `new Integer(123)`每次都会新建一个对象；
- `Integer.valueOf(123)`会使用缓存池中的对象，多次调用会取得同一个对象的引用。

	Integer x = new Integer(123);
	Integer y = new Integer(123);
	System.out.println(x == y);    // false
	Integer z = Integer.valueOf(123);
	Integer k = Integer.valueOf(123);
	System.out.println(z == k);   // true

`valueOf()`方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

在 Java 8 中，`Integer`缓存池的大小默认为 -128~127。

编译器会在自动装箱过程调用`valueOf()`方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```

基本类型的缓冲池：

- boolean有true和false
- byte所有的值
- short是-128到127
- int也是-128到127
- char是\u0000到\u007F

# String #

## 概览 ##
`String`被声明为`final`，因此它不可被继承。

在Java 8中，`String`内部使用`char`数组存储数据。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```
在Java 9之后，`String`类的实现改用`byte`数组存储字符串，同时使用`coder`来标识使用了哪种编码。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```
`value`数组被声明为`final`，这意味着`value`数组初始化之后就不能再引用其它数组。并且`String`内部没有改变`value`数组的方法，因此可以保证`String`不可变。

## 不可变的好处 ##

### 可以缓存hash值 ###

因为`String`的 hash 值经常被使用，例如`String`用做`HashMap`的`key`。不可变的特性可以使得`hash`值也不可变，因此只需要进行一次计算。

### `String Pool`的需要 ###

如果一个`String`对象已经被创建过了，那么就会从`String Pool`中取得引用。只有`String`是不可变的，才可能使用`String Pool`。

### 安全性 ###

`String`经常作为参数，`String`不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果`String`是可变的，那么在网络连接过程中`String`被改变，改变`String`对象的那一方以为现在连接的是其它主机，而实际情况却不一定是。

### 线程安全 ###

`String`不可变性天生具备线程安全，可以在多个线程中安全地使用。

## String, StringBuffer and StringBuilder ##

### 可变性 ###

- `String`不可变
- `StringBuffer`和`StringBuilder`可变

### 线程安全 ###

`String`不可变，因此是线程安全的
`StringBuilder`不是线程安全的
`StringBuffer`是线程安全的，内部使用`synchronized`进行同步

## String Pool ##

字符串常量池（`String Pool`）保存着所有字符串字面量（`literal strings`），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 `intern()` 方法在运行过程中将字符串添加到 `String Pool` 中。

当一个字符串调用 `intern()` 方法时，如果 `String Pool` 中已经存在一个字符串和该字符串值相等（使用 `equals()` 方法进行确定），那么就会返回 `String Pool` 中字符串的引用；否则，就会在 `String Pool` 中添加一个新的字符串，并返回这个新字符串的引用。

下面示例中，s1 和 s2 采用 `new String()` 的方式新建了两个不同字符串，而 s3 和 s4 是通过 `s1.intern()` 方法取得一个字符串引用。`intern()` 首先把 s1 引用的字符串放到 `String Pool` 中，然后返回这个字符串引用。因此 s3 和 s4 引用的是同一个字符串。

```java
String s1 = new String("aaa");
String s2 = new String("aaa");
System.out.println(s1 == s2);           // false
String s3 = s1.intern();
String s4 = s1.intern();
System.out.println(s3 == s4);           // true
```

如果是采用 "bbb" 这种字面量的形式创建字符串，会自动地将字符串放入`String Pool`中，因为等效于`valueOf()`。

```java
String s5 = "bbb";
String s6 = "bbb";
System.out.println(s5 == s6);  // true
```

注意，自行new的字符串对象是放在堆中，与`String Pool`中的不一样。

```java
String s7 = new String("ccc");
String s8 = "ccc";
String s9 = String.valueOf("ccc");
System.out.println(s7 == s8);   //false
System.out.println(s7 == s9);   //false
System.out.println(s8 == s9);   //true
```

在 Java 7 之前，`String Pool`被放在运行时常量池中，它属于永久代。而在 Java 7，`String Pool`被移到堆中。这是因为永久代的空间有限，在大量使用字符串的场景下会导致`OutOfMemoryError`错误。

## new String("abc") ##

使用这种方式一共会创建两个字符串对象（前提是 `String Pool` 中还没有 "abc" 字符串对象）。

- "abc" 属于字符串字面量，因此编译时期会在 `String Pool` 中创建一个字符串对象，指向这个 "abc" 字符串字面量；
- 而使用 new 的方式会在堆中创建一个字符串对象。

以下是 `String` 构造函数的源码，可以看到，在将一个字符串对象作为另一个字符串对象的构造函数参数时，并不会完全复制 `value` 数组内容，而是都会指向同一个 `value` 数组。

```java
public String(String original) {
    this.value = original.value;
    this.hash = original.hash;
}
```

# 运算 #

## 参数传递 ##

Java中的参数传递传的是值，而不是引用。当你将基本类型外的类型(`Cat c`)作为参数进行传递时，是将指针作为值传入进去，所以如果你调用一个对象的方法(`c.eat()`)时会对那个对象造成影响，但当你让变量等于一个新的对象时(`c = new Cat()`)不会对原对象造成影响。

## float 与 double ##

Java 不能隐式执行向下转型，因为这会使得精度降低。

1.1 字面量属于 double 类型，不能直接将 1.1 直接赋值给 float 变量，因为这是向下转型。

```java
float f = 1.1; //error
```

1.1f 字面量才是 float 类型。

```java
float f = 1.1f;
```

## 隐式类型转换 ##

因为字面量 1 是 int 类型，它比 short 类型精度要高，因此不能隐式地将 int 类型下转型为 short 类型。

```java
short s1 = 1;	//这里没事
s1 = s1 + 1; //error
```

但是使用 += 或者 ++ 运算符可以执行隐式类型转换。

```java
s1 += 1;
s1++;
```

上面的语句相当于将 s1 + 1 的计算结果进行了向下转型：

```java
s1 = (short) (s1 + 1);
```

## switch ##

从 Java 7 开始，可以在 `switch` 条件判断语句中使用 `String` 对象。

```java
String s = "a";
switch (s) {
    case "a":
        System.out.println("aaa");
        break;
    case "b":
        System.out.println("bbb");
        break;
}
```
`switch` 不支持 `long`，是因为 `switch` 的设计初衷是对那些只有少数的几个值进行等值判断，如果值过于复杂，那么还是用 `if` 比较合适。

# 继承 #

## 访问权限 ##

Java 中有三个访问权限修饰符：private、protected 以及 public，如果不加访问修饰符，表示包级可见。

| 权限修饰符 | 同一个类 | 同一个包 | 不同包得子类 | 不同包得非子类 |
| :--------: | :------: | :------: | :----------: | :------------: |
|  private   |    √     |          |              |                |
|  default   |    √     |    √     |              |                |
| protected  |    √     |    √     |      √       |                |
|   public   |    √     |    √     |      √       |       √        |

可以对类或类中的成员（字段以及方法）加上访问修饰符。

- 类可见表示其它类可以用这个类创建实例对象。
- 成员可见表示其它类可以用这个类的实例对象访问到该成员；

设计良好的模块会隐藏所有的实现细节，把它的 API 与它的实现清晰地隔离开来。模块之间只通过它们的 API 进行通信，一个模块不需要知道其他模块的内部工作情况，这个概念被称为信息隐藏或封装。因此访问权限应当尽可能地使每个类或者成员不被外界访问。

如果子类的方法重写了父类的方法，那么子类中该方法的访问级别不允许低于父类的访问级别。这是为了确保可以使用父类实例的地方都可以使用子类实例，也就是确保满足里氏替换原则。

字段决不能是公有的，因为这么做的话就失去了对这个字段修改行为的控制，客户端可以对其随意修改。可以使用公有的 `getter` 和` setter` 方法来替换公有字段，这样的话就可以控制对字段的修改行为。但是也有例外，如果是包级私有的类或者私有的嵌套类，那么直接暴露成员不会有特别大的影响。

```java
public class AccessExample {

    private int id;

    public String getId() {
        return id + "";
    }

    public void setId(String id) {
        this.id = Integer.valueOf(id);
    }
}
```

## 抽象类与接口 ##

### 抽象类 ###

抽象类和抽象方法都使用 abstract 关键字进行声明。抽象类一般会包含抽象方法，抽象方法一定位于抽象类中。

抽象类和普通类最大的区别是，抽象类不能被实例化，需要继承抽象类才能实例化其子类。

### 接口 ###

接口是抽象类的延伸，在 Java 8 之前，它可以看成是一个完全抽象的类，也就是说它不能有任何的方法实现。

从 Java 8 开始，接口也可以拥有默认的方法实现，这是因为不支持默认方法的接口的维护成本太高了。在 Java 8 之前，如果一个接口想要添加新的方法，那么要修改所有实现了该接口的类。

接口的成员（字段 + 方法）默认都是 `public` 的，并且不允许定义为 `private` 或者 `protected。`

接口的字段默认都是 `static` 和 `final` 的。

### 比较 ###

- 从设计层面上看，抽象类提供了一种 IS-A 关系(是什么)，那么就必须满足里式替换原则，即子类对象必须能够替换掉所有父类对象。而接口更像是一种 HAS-A 关系(有什么)，它只是提供一种方法实现契约，并不要求接口和实现接口的类具有 IS-A 关系。
- 从使用上来看，一个类可以实现多个接口，但是不能继承多个抽象类。
- 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。
- 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限

### 使用选择 ###

使用接口：

- 需要让不相关的类都实现一个方法，例如不相关的类都可以实现 Compareable 接口中的 compareTo() 方法；
- 需要使用多重继承。

使用抽象类：

- 需要在几个相关的类中共享代码。
- 需要能控制继承来的成员的访问权限，而不是都为 public。
- 需要继承非静态和非常量字段。

在很多情况下，接口优先于抽象类。因为接口没有抽象类严格的类层次结构要求，可以灵活地为一个类添加行为。并且从 Java 8 开始，接口也可以有默认的方法实现，使得修改接口的成本也变的很低。

在选择中我们应该多思考对象的本质是什么，例如我们有一个防盗门，防盗门有门、报警器2个属性。那么到底哪一个定义为抽象类，而哪一个定义为接口呢。如果我们认为防盗门的本质是门，那么就应该把门定义为抽象类，报警器定义为接口，即防盗门是一个带报警器功能的门。但是如果我们认为防盗门是一个带门功能的报警器，那就应该反过来定义。

## super ##
- 访问父类的构造函数：可以使用 super() 函数访问父类的构造函数，从而委托父类完成一些初始化的工作。
- 访问父类的成员：如果子类重写了父类的某个方法，可以通过使用 super 关键字来引用父类的方法实现。

  public class SuperExtendExample extends SuperExample {

  ```java
  private int z;
  
  public SuperExtendExample(int x, int y, int z) {
      super(x, y);	//访问父类的构造函数
      this.z = z;
  }
  
  @Override
  public void func() {
      super.func();	//访问父类的成员
      System.out.println("SuperExtendExample.func()");
  }
  ```
  }

## 重写与重载 ##

### 重写（Override） ###

存在于继承体系中，指子类实现了一个与父类在方法声明上完全相同的一个方法。

为了满足里式替换原则，重写有有以下三个限制：

- 子类的访问权限不低于父类
- 子类的返回类型是父类返回类型或是其子类
- 子类抛出的异常类型为 Exception，是父类抛出异常 Throwable 的子类

使用 `@Override` 注解，可以让编译器帮忙检查是否满足上面的三个限制条件。

涉及到重写时，方法调用的优先级为：

- this.show(O)
- super.show(O)
- this.show((super)O)
- super.show((super)O)

### 重载（Overload） ###

存在于同一个类中，指一个方法与已经存在的方法名称上相同，但是参数类型、个数、顺序至少有一个不同。

应该注意的是，返回值不同，其它都相同不算是重载。

# Object 通用方法 #

## 概览 ##

```java
public native int hashCode()

public boolean equals(Object obj)

protected native Object clone() throws CloneNotSupportedException

public String toString()

public final native Class<?> getClass()

protected void finalize() throws Throwable {}

public final native void notify()

public final native void notifyAll()

public final native void wait(long timeout) throws InterruptedException

public final void wait(long timeout, int nanos) throws InterruptedException

public final void wait() throws InterruptedException
```

## equals() ##

### 作用 ###
用于比较对象是否等价，对任何不是 `null` 的对象 x 调用 `x.equals(null)` 结果都为 `false`。

### 与==比较 ###
- 对于基本类型，== 判断两个值是否相等，基本类型没有 equals() 方法。
- 对于引用类型，== 判断两个变量是否引用同一个对象，而 equals() 判断引用的对象是否等价。

### 实现 ###
- 检查是否为同一个对象的引用，如果是直接返回 true；
- 检查是否是同一个类型，如果不是，直接返回 false；
- 将 Object 对象进行转型；
- 判断每个关键域是否相等。

## hashCode() ##

`hashCode()` 返回散列值，而 `equals()` 是用来判断两个对象是否等价。等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价。

在覆盖 `equals()` 方法时应当总是覆盖 `hashCode()` 方法，保证等价的两个对象散列值也相等。

下面的代码中，新建了两个等价的对象，并将它们添加到 `HashSet` 中。我们希望将这两个对象当成一样的，只在集合中添加一个对象，但是因为 `EqualExample` 没有实现 `hashCode()` 方法，因此这两个对象的散列值是不同的，最终导致集合添加了两个等价的对象。

当往一个集合(`HashSet`或`HashMap`)放入对象是，集合先会调用`hashCode()`方法获取hash值，然后检查该hash值位置上有没有对象，如果没有，则直接放入，如果有，就会调用`equals()`方法，比较2个对象是否等价，等价就不放。

理想的散列函数应当具有均匀性，即不相等的对象应当均匀分布到所有可能的散列值上。这就要求了散列函数要把所有域的值都考虑进来。可以将每个域都当成 R 进制的某一位，然后组成一个 R 进制的整数。R 一般取 31，因为它是一个奇素数，如果是偶数的话，当出现乘法溢出，信息就会丢失，因为与 2 相乘相当于向左移一位。

## toString() ##
默认返回 `ToStringExample@4554617c` 这种形式，其中 @ 前面的是该对象的类名，后面的数值为散列码的无符号十六进制表示。

## clone() ##

### cloneable ###
`clone()` 是 `Object` 的 `protected` 方法，它不是 `public`，一个类不显式去重写 `clone()`，其它类就不能直接去调用该类实例的 `clone()` 方法。如果直接使用`clone()`方法，就会抛出`CloneNotSupportedException`，这是因为没有实现 `Cloneable` 接口。

应该注意的是，`clone()` 方法并不是 `Cloneable` 接口的方法，而是 `Object` 的一个 `protected` 方法。`Cloneable` 接口只是规定，如果一个类没有实现 `Cloneable` 接口又调用了 `clone()` 方法，就会抛出 `CloneNotSupportedException`。

### 浅拷贝 ###

拷贝对象和原始对象的引用类型引用同一个对象。

### 深拷贝 ###

拷贝对象和原始对象的引用类型引用不同对象。

### clone() 的替代方案 ###

使用 `clone()` 方法来拷贝一个对象即复杂又有风险，它会抛出异常，并且还需要类型转换。Effective Java 书上讲到，最好不要去使用 `clone()`，可以使用拷贝构造函数或者拷贝工厂来拷贝一个对象。

# 关键字 #

## final ##

### 数据 ###

声明数据为常量，可以是编译时常量，也可以是在运行时被初始化后不能被改变的常量。

- 对于基本类型，final 使数值不变；
- 对于引用类型，final 使引用不变，也就不能引用其它对象，但是被引用的对象本身是可以修改的。

	final int x = 1;
	// x = 2;  // cannot assign value to final variable 'x'
	final A y = new A();
	y.a = 1;

### 方法 ###

声明方法不能被子类重写。

`private` 方法隐式地被指定为 `final`，如果在子类中定义的方法和基类中的一个 `private` 方法签名相同，此时子类的方法不是重写基类方法，而是在子类中定义了一个新的方法。

### 类 ###

声明类不允许被继承。

## static ##

### 静态变量 ###

- 静态变量：又称为类变量，也就是说这个变量属于类的，类所有的实例都共享静态变量，可以直接通过类名来访问它。静态变量在内存中只存在一份。
- 实例变量：每创建一个实例就会产生一个实例变量，它与该实例同生共死。

### 静态方法 ###

静态方法在类加载的时候就存在了，它不依赖于任何实例。所以静态方法必须有实现，也就是说它不能是抽象方法。只能访问所属类的静态字段和静态方法，方法中不能有 `this` 和 `super` 关键字。

### 静态语句块 ###

静态语句块在类初始化时运行一次。

```java
public class A {
    static {
        System.out.println("123");
    }

    public static void main(String[] args) {
        A a1 = new A();
        A a2 = new A();
    }
}
```

### 静态内部类 ###

非静态内部类依赖于外部类的实例，而静态内部类不需要。

```java
public class OuterClass {

    class InnerClass {
    }

    static class StaticInnerClass {
    }

    public static void main(String[] args) {
        // InnerClass innerClass = new InnerClass(); // 'OuterClass.this' cannot be referenced from a static context
        OuterClass outerClass = new OuterClass();
        InnerClass innerClass = outerClass.new InnerClass();
        StaticInnerClass staticInnerClass = new StaticInnerClass();
    }
}
```

### 静态导包 ###

在使用静态变量和方法时不用再指明 ClassName，从而简化代码，但可读性大大降低。

```java
import static com.xxx.ClassName.*
```

### 初始化顺序 ###

静态变量和静态语句块优先于实例变量和普通语句块，静态变量和静态语句块的初始化顺序取决于它们在代码中的顺序。

存在继承的情况下，初始化顺序为：

- 父类（静态变量、静态语句块）
- 子类（静态变量、静态语句块）
- 父类（实例变量、普通语句块）
- 父类（构造函数）
- 子类（实例变量、普通语句块）
- 子类（构造函数）

# 反射 #

每个类都有一个 `Class` 对象，包含了与类有关的信息。当编译一个新类时，会产生一个同名的 `.class` 文件，该文件内容保存着 `Class` 对象。

类加载相当于 `Class` 对象的加载，类在第一次使用时才动态加载到 JVM 中。也可以使用 `Class.forName("com.mysql.jdbc.Driver")` 这种方式来控制类的加载，该方法会返回一个 `Class` 对象。

反射可以提供运行时的类信息，并且这个类可以在运行时才加载进来，甚至在编译时期该类的 `.class` 不存在也可以加载进来。

`Class` 和 `java.lang.reflect` 一起对反射提供了支持，`java.lang.reflect` 类库主要包含了以下三个类：

- `Field` ：可以使用 `get()` 和 `set()` 方法读取和修改 Field 对象关联的字段；
- `Method` ：可以使用 `invoke()` 方法调用与 `Method` 对象关联的方法；
- `Constructor` ：可以用 `Constructor` 创建新的对象。

反射的优点：

- **可扩展性** ：应用程序可以利用全限定名创建可扩展对象的实例，来使用来自外部的用户自定义类。
- **类浏览器和可视化开发环境** ：一个类浏览器需要可以枚举类的成员。可视化开发环境（如 IDE）可以从利用反射中可用的类型信息中受益，以帮助程序员编写正确的代码。
- **调试器和测试工具** ： 调试器需要能够检查一个类里的私有成员。测试工具可以利用反射来自动地调用类里定义的可被发现的 API 定义，以确保一组测试中有较高的代码覆盖率。

反射的缺点：

尽管反射非常强大，但也不能滥用。**如果一个功能可以不用反射完成，那么最好就不用。**在我们使用反射技术时，下面几条内容应该牢记于心。

- **性能开销** ：反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。我们应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。
- **安全限制** ：使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须在有安全限制的环境中运行，如 Applet，那么这就是个问题了。
- **内部暴露** ：由于反射允许代码执行一些在正常情况下不被允许的操作（比如访问私有的属性和方法），所以使用反射可能会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。

# 异常 #

`Throwable` 可以用来表示任何可以作为异常抛出的类，分为两种： `Error` 和 `Exception`。其中 `Error` 用来表示 JVM 无法处理的错误，`Exception` 分为两种：

- **受检异常** ：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；
- **非受检异常** ：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。

![](http://qiniuyun.gtouyang.com/Exception%E7%BB%A7%E6%89%BF%E5%85%B3%E7%B3%BB.png)

# 泛型 #

```java
public class Example<T>{
	private T t;
	public T get();
}
```

## 作用 ##

**泛型的作用是类型的参数化，就是可以把类型像方法的参数那样传递。**这一点意义非凡。在集合中存储对象并在使用前进行类型转换是多么的不方便。泛型防止了那种情况的发生。它提供了编译期的类型安全，确保你只能把正确类型的对象放入集合中，避免了在运行时出现`ClassCastException`。

## 类型擦除 ##

泛型是通过类型擦除来实现的，编译器在编译时擦除了所有类型相关的信息，所以在运行时不存在任何类型相关的信息。例如`List<String>`在运行时仅用一个`List`来表示。这样做的目的，是确保能和 Java 5 之前的版本开发二进制类库进行兼容。你无法在运行时访问到类型参数，因为编译器已经把泛型类型转换成了原始类型。

## 限定通配符和非限定通配符 ##

限定通配符对类型进行了限制。有两种限定通配符，一种是`<? extends T>`它通过确保类型必须是T的子类来设定类型的上界，另一种是`<? super T>`它通过确保类型必须是T的父类来设定类型的下界。泛型类型必须用限定内的类型来进行初始化，否则会导致编译错误。另一方面<?>表示了非限定通配符，因为`<?>`可以用任意类型来替代。

## 泛型方法 ##

```java
public static <T> class example(<T> t){}
```

## 可以把List<String>传递给一个接受List<Object>参数的方法吗？ ##

这个Java泛型题目看起来令人疑惑，因为乍看起来String是一种Object，所以List<String>应当可以用在需要List<Object>的地方，但是事实并非如此。真这样做的话会导致编译错误。如果你再深一步考虑，你会发现Java这样做是有意义的，因为List<Object>可以存储任何类型的对象包括String, Integer等等，而List<String>却只能用来存储Strings。　

```java
List<Object> objectList;
List<String> stringList;
objectList = stringList;  //compilation error incompatible types
```

## Array中可以用泛型吗？ ##

`Array`事实上并不支持泛型，这也是为什么Joshua Bloch在Effective Java一书中建议使用`List`来代替`Array`，因为`List`可以提供编译期的类型安全保证，而`Array`却不能。

## ava中的类型未检查的警告 ##

如果把泛型和原始类型混合起来使用，例如下列代码，Java 5的javac编译器会产生类型未检查的警告，例如　　

```java
   List<String> rawList = new ArrayList()
```

# 注解 #

Java 注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。

## 注解的定义 ##

注解通过 @interface关键字进行定义。

```java
public @interface TestAnnotation {
}
```

它的形式跟接口很类似，不过前面多了一个 @ 符号。上面的代码就创建了一个名字为 TestAnnotaion 的注解。

你可以简单理解为创建了一张名字为 TestAnnotation 的标签。

## 注解的应用 ##

上面创建了一个注解，那么注解的的使用方法是什么呢。

```java
@TestAnnotation
public class Test {
}
```

创建一个类 Test,然后在类定义的地方加上 @TestAnnotation 就可以用 TestAnnotation 注解这个类了。

你可以简单理解为将 TestAnnotation 这张标签贴到 Test 这个类上面。

## 元注解 ##

单单注解是不够的，要想注解能够正常工作，还需要介绍一下一个新的概念那就是元注解。

元注解是可以注解到注解上的注解，或者说元注解是一种基本注解，但是它能够应用到其它的注解上面。

如果难于理解的话，你可以这样理解。元注解也是一张标签，但是它是一张特殊的标签，它的作用和目的就是给其他普通的标签进行解释说明的。

元标签有 @Retention、@Documented、@Target、@Inherited、@Repeatable 5 种。

### @Retention ###

`Retention` 的英文意为保留期的意思。当 `@Retentio`n 应用到一个注解上的时候，它解释说明了这个注解的的存活时间。

它的取值如下：

- RetentionPolicy.SOURCE 注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视。
- RetentionPolicy.CLASS 注解只被保留到编译进行的时候，它并不会被加载到 JVM 中。
- RetentionPolicy.RUNTIME 注解可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们。

我们可以这样的方式来加深理解，@Retention 去给一张标签解释的时候，它指定了这张标签张贴的时间。@Retention 相当于给一张标签上面盖了一张时间戳，时间戳指明了标签张贴的时间周期。

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
}
```

上面的代码中，我们指定 TestAnnotation 可以在程序运行周期被获取到，因此它的生命周期非常的长。

### @Documented ###

顾名思义，这个元注解肯定是和文档有关。它的作用是能够将注解中的元素包含到 Javadoc 中去。

### @Target ###

`Target` 是目标的意思，`@Target` 指定了注解运用的地方。

你可以这样理解，当一个注解被 `@Target` 注解时，这个注解就被限定了运用的场景。

类比到标签，原本标签是你想张贴到哪个地方就到哪个地方，但是因为 @Target 的存在，它张贴的地方就非常具体了，比如只能张贴到方法上、类上、方法参数上等等。@Target 有下面的取值

- ElementType.ANNOTATION_TYPE 可以给一个注解进行注解
- ElementType.CONSTRUCTOR 可以给构造方法进行注解
- ElementType.FIELD 可以给属性进行注解
- ElementType.LOCAL_VARIABLE 可以给局部变量进行注解
- ElementType.METHOD 可以给方法进行注解
- ElementType.PACKAGE 可以给一个包进行注解
- ElementType.PARAMETER 可以给一个方法内的参数进行注解
- ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举

### @Inherited ###

`Inherited` 是继承的意思，但是它并不是说注解本身可以继承，而是说如果一个超类被 `@Inherited `注解过的注解进行注解的话，那么如果它的子类没有被任何注解应用的话，那么这个子类就继承了超类的注解。

```Java
@Inherited
@Retention(RetentionPolicy.RUNTIME)
@interface Test {}

@Test
public class A {}

public class B extends A {}
```

注解 `Test` 被 `@Inherited`修饰，之后类 A 被 `Test` 注解，类 B 继承 A,类 B 也拥有 Test 这个注解。

### @Repeatable ###

`Repeatable` 自然是可重复的意思。`@Repeatable` 是 Java 1.8 才加进来的，所以算是一个新的特性。

举个例子，一个人他既是程序员又是产品经理,同时他还是个画家。

```Java
@interface Persons {
	Person[]  value();
}

@Repeatable(Persons.class)
@interface Person{
	String role default "";
}

@Person(role="artist")
@Person(role="coder")
@Person(role="PM")
public class SuperMan{
}
```

注意上面的代码，`@Repeatable` 注解了 `Person`。而 `@Repeatable` 后面括号中的类相当于一个容器注解。这里有2个注解，`Person`和`Persons`，按照规定，`Persons`里面必须要有一个 `value` 的属性，属性类型是一个被 `@Repeatable` 注解过的注解数组，注意它是数组。

如果不好理解的话，可以这样理解。`Persons` 是一张总的标签，上面贴满了 `Person` 这种同类型但内容不一样的标签。把 `Persons` 给一个 `SuperMan` 贴上，相当于同时给他贴了程序员、产品经理、画家的标签。

## 注解的属性 ##

注解的属性也叫做成员变量。注解只有成员变量，没有方法。注解的成员变量在注解的定义中以“无形参的方法”形式来声明，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。
	
```Java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {	
	public int id() default -1;
	public String msg() default "Hi";
}
```

需要注意的是，在注解中定义属性时它的类型必须是 8 种基本数据类型外加 类、接口、注解及它们的数组。

注解中的属性可以有默认值，如果需要默认值，这要加default来指定默认值是多少。

```Java
@TestAnnotation(id=3,msg="hello annotation")
public class Test {}

@TestAnnotation()
public class Test {}
```

可以在注解中给注解属性赋值，如果注解有默认值，也可以不赋值。

当注解中只有一个属性时，可以省去指定哪个属性

```java
public @interface Check {
	String value();
}

@Check("hi")
int a;
```

注解中没有属性时，连括号都能省

```Java
public @interface Perform {}

@Perform
public void testMethod(){}
```

## Java 预置的注解 ##

### @Deprecated ###

这个元素是用来标记过时的元素，想必大家在日常开发中经常碰到。编译器在编译阶段遇到这个注解时会发出提醒警告，告诉开发者正在调用一个过时的元素比如过时的方法、过时的类、过时的成员变量。

```java
public class Hero {
	
	@Deprecated
	public void say(){
		System.out.println("Noting has to say!");
	}
	
	public void speak(){
		System.out.println("I have a dream!");
	}

}
```

定义了一个 Hero 类，它有两个方法 `say()` 和 `speak()` ，其中 `say()` 被 `@Deprecated` 注解。然后我们在 IDE 中分别调用它们。

![](http://qiniuyun.gtouyang.com/Java%E8%BF%87%E6%97%B6%E6%96%B9%E6%B3%95%E6%95%88%E6%9E%9C%E5%9B%BE.png)

可以看到，`say()` 方法上面被一条直线划了一条，这其实就是编译器识别后的提醒效果。

### @Override ###

提示子类要复写父类中被 `@Override` 修饰的方法

### @SafeVarargs ###

参数安全类型注解。它的目的是提醒开发者不要用参数做一些不安全的操作,它的存在会阻止编译器产生 `unchecked` 这样的警告。它是在 Java 1.7 的版本中加入的。

### @FunctionalInterface ###

函数式接口注解，这个是 Java 1.8 版本引入的新特性。函数式编程很火，所以 Java 8 也及时添加了这个特性。

**函数式接口 (Functional Interface) 就是一个具有一个方法的普通接口。**

可能有人会疑惑，函数式接口标记有什么用，这个原因是函数式接口可以很容易转换为 Lambda 表达式。这是另外的主题了，==**现在我还不会**==。

## 注解的提取 ##

通过用标签来比作注解，前面的内容是讲怎么写注解，然后贴到哪个地方去，而现在我们要做的工作就是检阅这些标签内容。 形象的比喻就是你把这些注解标签在合适的时候撕下来，然后检阅上面的内容信息。

要想正确检阅注解，离不开一个手段，那就是反射。

### 注解与反射 ###

注解通过反射获取，以下是获取注解得方法。

```Java
//判断它是否应用了某个注解
public boolean isAnnotationPresent(Class<? extends Annotation> annotationClass) {}

//获取指定类型的注解
public <A extends Annotation> A getAnnotation(Class<A> annotationClass) {}

//获取注解到这个元素上的所有注解
public Annotation[] getAnnotations() {}
```

需要注意的是，如果一个注解要在运行时被成功提取，那么 `@Retention(RetentionPolicy.RUNTIME)`是必须的。

## 注解的使用场景 ##

注解是一系列元数据，它提供数据用来解释程序代码，但是**注解并非是所解释的代码本身的一部分**。注解对于代码的运行效果没有直接影响。

注解有许多用处，主要如下：

- 提供信息给编译器： 编译器可以利用注解来探测错误和警告信息
- 编译阶段时的处理： 软件工具可以用来利用注解信息来生成代码、Html文档或者做其它相应处理。
- 运行时的处理： 某些注解可以在程序运行的时候接受代码的提取

当开发者使用了Annotation 修饰了类、方法、Field 等成员之后，这些 Annotation 不会自己生效，必须由开发者提供相应的代码来提取并处理 Annotation 信息。这些处理提取和处理 Annotation 的代码统称为 **APT（Annotation Processing Tool)**。

## 用注解进行测试

创建一个标签@check

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
public @interface check{}
```

给需要测试的方法打上标签

```java
public class NoBug {

    @check
    public void addition() {
        System.out.println("1+1=" + (1 + 1));
    }

    @check
    public void subtraction() {
        System.out.println("1-1=" + (1 - 1));
    }

    @check
    public void multiplication() {
        System.out.println("3×3=" + (3 * 3));
    }

    @check
    public void division() {
        System.out.println("6/0=" + (6 / 0));
    }

    public void test(){
        System.out.println("我没有被@check注解，所以不会被测试");
    }

}
```

然后新建一个类Test用于检查

```java
import java.lang.reflect.Method;

public class Test {
    public static void main(String[] args){

        //新建一个NoBug对象
        NoBug test = new NoBug();

        //获得NoBug类的信息
        Class c = test.getClass();

        //获得NoBug类自身说明的方法，如果使用getMethods()会获得包括继承来的方法
        Method[] methods = c.getDeclaredMethods();

        StringBuilder log = new StringBuilder();

        int errorNum = 0;

        for (Method m:methods){

            //如果注解是check
            if(m.isAnnotationPresent( check.class )){
                try {
                    //启用安全检查
                    m.setAccessible(true);
                    
                    //用对象test和参数null启动该方法
                    m.invoke(test,null);
                }catch (Exception e){
                    errorNum++;

                    //获得发生异常的方法名
                    log.append((m.getName()));
                    log.append(" has error:");
                    log.append("\n\r cause by ");

                    //获得异常名称
                    log.append((e.getCause().getClass().getSimpleName()));
                    log.append("\n\r");

                    //获得异常原因
                    log.append((e.getCause().getMessage()));
                    log.append("\n\r");
                }
            }
        }

        log.append(c.getSimpleName() + " has " + errorNum + " error");

        if(errorNum > 1){
            log.append("s");
        }

        System.out.println(log.toString());
    }
}
```

得到结果

> 1-1=0
> 1+1=2
> 3×3=9
> division has error:
>  cause by ArithmeticException
> / by zero
> NoBug has 1 error

可以看到检查到了错误，这就是注解的用处之一。

# Java 与 C++ 的区别 #

- Java 是纯粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 为了兼容 C 即支持面向对象也支持面向过程。
- Java 通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。
- Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。
- Java 支持自动垃圾回收，而 C++ 需要手动回收。
- Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。
- Java 不支持操作符重载，虽然可以对两个 String 对象执行加法运算，但是这是语言内置支持的操作，不属于操作符重载，而 C++ 可以。
- Java 的 goto 是保留字，但是不可用，C++ 可以使用 goto。
- Java 不支持条件编译，C++ 通过 #ifdef #ifndef 等预处理命令从而实现条件编译。

# 引用 #

[CyC2018复习大纲](https://github.com/CyC2018/CS-Notes/blob/master/docs/notes/Java%20%E5%9F%BA%E7%A1%80.md)

[CSDN-frank909](https://blog.csdn.net/briblue/article/details/73824058 )