# JDK1.8的新特性

### 接口的默认方法

Java 8允许我们给接口添加一个非抽象的方法实现，只需要使用 default关键字即可，这个特征又叫做扩展方法。

### Lambda 表达式

在Java 8 中你就没必要使用这种传统的匿名对象的方式了，Java 8提供了更简洁的语法，lambda表达式：

```java
Collections.sort(names, (String a, String b) -> {

  return b.compareTo(a);

});
```

### 函数式接口

##### 什么是函数是接口

函数式接口(Functional Interface)是Java 8对一类特殊类型的接口的称呼。这类接口只定义了唯一的抽象方法的接口（除了隐含的Object对象的公共方法，因此最开始也就做SAM类型的接口（Single Abstract Method）。定义函数式接口的原因是在Java Lambda的实现中,开发组不想再为Lambda表达式单独定义一种特殊的Structural函数类型,称之为箭头类型（arrow type,依然想采用Java既有的类型(class, interface, method等).原因是增加一个结构化的函数类型会增加函数类型的复杂性，破坏既有的Java类型，并对成千上万的Java类库造成严重的影响。权衡利弊,因此最终还是利用SAM 接口作为 Lambda表达式的目标类型.另外对于函数式接口来说@FunctionalInterface并不是必须的,只要接口中只定义了唯一的抽象方法的接口那它就是一个实质上的函数式接口,就可以用来实现Lambda表达式。 

#####  Lambda表达式的组成

java 8 中Lambda 表达式由三个部分组成：第一部分为一个括号内用逗号分隔的形式参数，参数是函数式接口里面方法的参数；第二部分为一个箭头符号：->；第三部分为方法体，可以是表达式和代码块。语法如下

1、 方法体为表达式，该表达式的值作为返回值返回。

<img src="/img/clip_image002.jpg">

2、 方法体为代码块，必须用 {} 来包裹起来，且需要一个 return 返回值，但若函数式接口里面方法返回值是 void，则无需返回值。

<img src="/img/clip_image004.jpg">

##### Lambda表达式的底层实现

java 8 内部Lambda 表达式的实现方式在本质是以匿名内部类的形式的实现的，看下面代码。代码中我们定义了一个叫binaryOperator的Lambda表达式，看返回值它是一个IntBinaryOperator实例。

<img src="/img/clip_image006.jpg">

我们得知IntBinaryOperator是一个接口并且上面有一个@FunctionalInterface的注解，@FunctionalInterface标注了这是一个函数式接口，所以我们知道了(int a, int b) -> {return a + b;}返回的一个IntBinaryOperator的匿名实现类。

##### Lambda 作用域

在lambda表达式中访问外层作用域和老版本的匿名对象中的方式很相似。你可以直接访问标记了final的外层局部变量，或者实例的字段以及静态变量。

##### Lambda表达式的应用场景

   1、使用() -> {} 替代匿名类

```java
 Thread t1 = new Thread(new Runnable() {

	      @Override

	      public void run() {

          System.out.println("no use lambda");

          }

       });

Thread t2 = new Thread(() -> System.out.println("use lambda"));
```

我们看到相对而言Lambda表达式要比匿名类要优雅简洁很多~。

2、遍历集合

```java
map.forEach((k, v) -> {

        System.out.print("key=" + k);

        System.out.print("\t");

        System.out.println("value=" + v);

     });
```

### 方法与构造函数引用

Java 8 允许你使用 :: 关键字来传递方法或者构造函数引用，上面的代码展示了如何引用一个静态方法，我们也可以引用一个对象的方法：

```java
converter = something::startsWith;

String converted = converter.convert("Java");

System.out.println(converted);
```

### 访问接口的默认方法

JDK 1.8 API包含了很多内建的函数式接口，在老Java中常用到的比如Comparator或者Runnable接口，这些接口都增加了@FunctionalInterface注解以便能用在lambda上。

Java 8 API同样还提供了很多全新的函数式接口来让工作更加方便，有一些接口是来自Google Guava库里的，即便你对这些很熟悉了，还是有必要看看这些是如何扩展到lambda上使用的