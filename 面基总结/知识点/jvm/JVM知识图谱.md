# JVM知识图谱

## 重新认识JVM世界

### 学习jvm的目的

- 面试重灾区, 我们必须搞懂它
- 生产过程中没肯定会面临JVM调优相关问题, 需要也必须搞懂它
- 打基础,想搞懂我们java赖以生存的环境长什么样子,提升对java知识点的理解

### jvm基本常识

#### 程序的执行方式有哪些

主要有三种:静态编译执行,动态编译执行和动态解释执行

> 此处所说的编译指的是编译成可让操作系统直接执行的机器码

#### 为什么使用jvm?

![image-20191210210034640](/Users/lee/Library/Application Support/typora-user-images/image-20191210210034640.png)

#### 字节码和机器码的区别

>  机器码是电脑CPU直接读取运行的机器指令，运行速度最快，但是非常晦涩难懂，也比较难编写，一般从 业人员接触不到。
>
> 字节码是一种中间状态(中间码)的二进制代码(文件)。需要直译器转译后才能成为机器码。



#### JDK, JRE与JVM的关系

![image-20191210210426403](/Users/lee/Library/Application Support/typora-user-images/image-20191210210426403.png)

#### OracleJDK和OpenJDK



##### 查看jdk版本

```shell
java -version
```

(1) 如果是**SUN/OracleJDK**, 显示信息为:

> [root@localhost ~]# java -version 
>
>  java version "1.8.0_162"  Java(TM) SE Runtime Environment (build 1.8.0_162-b12) 
>
>  Java HotSpot(TM) 64-Bit Server VM (build 25.162-b12, mixed mode) 							 					 		

说明:

>  Java HotSpot(TM) 64-Bit Server VM 表明, 此JDK的JVM是**Oracle的64位HotSpot虚拟机**，运行在Server模式下(虚拟机有Server和Client两种运行模式).
>  Java(TM) SE Runtime Environment (build 1.8.0_162-b12) 是Java运行时环境(即JRE)的版本信息.

(2) 如果OpenJDK, 显示信息为:

> [root@localhost ~]# java -version
>  openjdk version "1.8.0_144"
>  OpenJDK Runtime Environment (build 1.8.0_144-b01) OpenJDK 64-Bit Server VM (build 25.144-b01, mixed mode)

**OpenJDK** **的来历**

Java由SUN公司(Sun Microsystems, 发起于美国斯坦福大学, SUN是Stanford University Network的缩 写)发明, 2006年SUN公司将Java开源, 此时的JDK即为OpenJDK.	

也就是说, OpenJDK是Java SE的开源实现, 它由SUN和Java社区提供支持, 2009年Oracle收购了Sun公司, 自此Java的维护方之一的SUN也变成了Oracle .

大多数JDK都是在OpenJDK的基础上编写实现的, 比如IBM J9, Azul Zulu, Azul Zing和Oracle JDK. 几乎现 有的所有JDK都派生自OpenJDK, 它们之间不同的是许可证:

>OpenJDK根据许可证GPL v2发布;
>
>Oracle JDK根据Oracle二进制代码许可协议获得许可。

**Oracle JDK的来历**

Oracle JDK之前被称为SUN JDK, 这是在2009年Oracle收购SUN公司之前, 收购后被命名为Oracle JDK。

**实际上, Oracle JDK是基于OpenJDK源代码构建的, 因此Oracle JDK和OpenJDK之间没有重大的技术 差异。**

Oracle的项目发布经理Joe Darcy在OSCON 2011 上对两者关系的介绍也证实了OpenJDK 7和Oracle JDK 7在程序上是非常接近的, 两者共用了大量相同的代码(如下图), 注意: 图中提示了两者共同代码的占 比要远高于图形上看到的比例, 所以我们编译的OpenJDK基本上可以认为性能、功能和执行逻辑上都和 官方的Oracle JDK是一致的

![image-20191210210711295](/Users/lee/Library/Application Support/typora-user-images/image-20191210210711295.png)

**Oracle JDK与OpenJDK的区别**

> OpenJDK使用的是开源免费的FreeType, 可以按照GPL v2许可证使用.GPL V2允许在商业上使用;
>
> Oracle JDK则采用JRL(Java Research License，Java研究授权协议) 放出.JRL只允许个人研究使 用，要获得Oracle JDK的商业许可证, 需要联系Oracle的销售人员进行购买。

<font color="blue">JRockit是Oracle的JVM, 从Java SE 7开始, HotSpot和JRockit合并为一个JVM。</font>

#### **JVM和Hotspot的关系**

> JVM是《JVM虚拟机规范》中提出来的规范 
>
> Hotspot是使用JVM规范的商用产品，除此之外还有Oracle JRockit、IBM的J9也是JVM产品

#### **JVM和Java的关系**

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191211212618878.png" alt="image-20191211212618878" style="zoom:50%;" />

#### **JVM的运行模式**

JVM有两种运行模式:Server模式与Client模式。 

两种模式的区别在于:

- Client模式启动速度较快，Server模式启动较慢; 
- 但是启动进入稳定期长期运行之后Server模式的程序运行速度比Client要快很多。
-  因为Server模式启动的JVM采用的是重量级的虚拟机，对程序采用了更多的优化;而Client模式启 动的JVM采用的是轻量级的虚拟机。所以Server启动慢，但稳定后速度比Client远远要快。

### **JVM架构理解**

![image-20191211212939374](/Users/lee/Library/Application Support/typora-user-images/image-20191211212939374.png)

### **JVM程序执行流程**

Java编译成字节码、动态编译和解释为机器码的过程分析:

![image-20191211213131073](/Users/lee/Library/Application Support/typora-user-images/image-20191211213131073.png)

#### 编译器和解释器的协调工作流程

![image-20191211213244509](/Users/lee/Library/Application Support/typora-user-images/image-20191211213244509.png)

在部分商用虚拟机中(如HotSpot)，Java程序最初是通过解释器(Interpreter)进行解释执行的，当 虚拟机发现某个方法或代码块的运行特别频繁时，就会把这些代码认定为“**热点代码**”。为了提高热点代 码的执行效率，在运行时，虚拟机将会把这些代码编译成与本地平台相关的机器码，并进行各种层次的 优化，完成这个任务的编译器称为   **即时编译器**(Just In Time Compiler，下文统称JIT编译器)。

**由于Java虚拟机规范并没有具体的约束规则去限制即使编译器应该如何实现，所以这部分功能完全是与 虚拟机具体实现相关的内容，如无特殊说明，我们提到的编译器、即时编译器都是指Hotspot虚拟机内 的即时编译器，虚拟机也是特指HotSpot虚拟机。**

我们的JIT是属于动态编译方式的，**动态编译** (dynamic compilation)指的是“在运行时进行编译”;与 之相对的是事前编译(ahead-of-time compilation，简称AOT)，也叫**静态编译** (static compilation)。

**JIT**编译(just-in-time compilation)狭义来说是当某段代码即将第一次被执行时进行编译，因而叫“即 时编译”。**JIT编译是动态编译的一种特例** 。JIT编译一词后来被**泛化**，时常与动态编译等价;但要注意广 义与狭义的JIT编译所指的区别。

#### 哪些程序代码会被即时编译

程序中的代码只有是热点代码时，才会编译为本地代码，那么什么是 呢? 运行过程中会被即时编译器编译的“热点代码”有两类:

1. **被多次调用的方法。** 
2. **被多次执行的循环体。**

两种情况，编译器都是以**整个方法**作为编译对象。 这种编译方法因为编译发生在方法执行过程之中，因 此形象的称之为栈上替换(On Stack Replacement，OSR)，即方法栈帧还在栈上，方法就被替换
 了。

#### **如何判断热点代码呢**

要知道方法或一段代码是不是热点代码，是不是需要触发即时编译，需要进行Hot Spot Detection(热点探测)。

目前主要的热点探测方式有以下两种:

- 基于采样的热点探测

**采用这种方法的虚拟机会周期性地检查各个线程的栈顶，如果发现某些方法经常出现在栈顶，那这 个方法就是“热点方法”。**这种探测方法的好处是实现简单高效，还可以很容易地获取方法调用关系 (将调用堆栈展开即可)，缺点是很难精确地确认一个方法的热度，容易因为受到线程阻塞或别的 外界因素的影响而扰乱热点探测。

**基于计数器的热点探测**

**采用这种方法的虚拟机会为每个方法(甚至是代码块)建立计数器，统计方法的执行次数，如果执 行次数超过一定的阀值，就认为它是“热点方法”。**这种统计方法实现复杂一些，需要为每个方法建 立并维护计数器，而且不能直接获取到方法的调用关系，但是它的统计结果相对更加精确严谨。

#### **热点检测方式**

在HotSpot虚拟机中使用的是第二种——基于计数器的热点探测方法，因此它为每个方法准备了两个计 数器:**方法调用计数器**和**回边计数器**。在确定虚拟机运行参数的前提下，这两个计数器都有一个确定的 阈值，当计数器超过阈值溢出了，就会触发JIT编译。

**方法调用计数器 **

**顾名思义，这个计数器用于统计方法被调用的次数。**

![image-20191211215047587](/Users/lee/Library/Application Support/typora-user-images/image-20191211215047587.png)

**回边计数器** 

它的作用就是统计一个方法中**循环体**代码执行的次数，在字节码中遇到控制流向后跳转的指令称为“回

边”。

### JIT使用

- 为何HotSpot需要使用解释器和编译器并存的架构?
-  JVM为什么要实现两个不同的即时编译器? 
- 程序何时会使用解释器执行?何时会使用编译器执行? 
- 哪些程序代码会被编译成为本地代码?如何编译? 
- JAVA代码的执行效率就一定比C,C++静态执行的执行差?JAVA代码解析执行有何优势?

#### **为什么要使用解释器与编译器并存的架构**

尽管并不是所有的Java虚拟机都采用解释器与编译器并存的架构，但许多主流的**商用虚拟机(如HotSpot)**，都同时**包含解释器和编译器**。

**解释器与编译器特点**

- 当程序需要**迅速启动和执行**的时候，解释器可以首先发挥作用，省去编译的时间，立即执行。在程序运行后，随着时间的推移，编译器逐渐发挥作用，把越来越多的代码编译成本地代码之后，可以 获取**更高的执行效率**。
-  当程序运行环境中**内存资源限制较大**(如部分嵌入式系统中), 可以使用**解释器执行节约内存**, 反之可以使用**编译执行来提升效率** 。

**编译的时间开销**

解释器的执行，抽象的看是这样的:

**输入的代码-> [解释器解释执行 ] ->执行结果**

而要JIT编译然后再执行的话，抽象的看则是:
 **输入的代码** **-> [** **编译器 编译** **] ->** **编译后的代码** **-> [** **执行** **] ->** **执行结果**

说JIT比解释快，其实说的是“执行编译后的代码”比“解释器解释执行”要快，并不是说“编译”这个动作 比“解释”这个动作快。JIT编译再怎么快，至少也比解释执行一次略慢一些，而要得到最后的执行结果还 得再经过一个“执行编译后的代码”的过程。所以，对“只执行一次”的代码而言，解释执行其实总是比JIT 编译执行要快。

怎么算是“只执行一次的代码”呢?粗略说，下面两个条件同时满足时就是严格的“只执行一次”

 1、只被调用一次，例如类的构造器(class initializer，())
 2、没有循环
 对只执行一次的代码做JIT编译再执行，可以说是得不偿失。 对只执行少量次数的代码，JIT编译带来的执行速度的提升也未必能抵消掉最初编译带来的开销。

**只有对频繁执行的代码, JIT编译才能保证有正面的收益.**

**编译的空间开销**

对一般的Java方法而言，编译后代码的大小相对于字节码的大小，膨胀比达到10x是很正常的。同上面 说的时间开销一样，这里的空间开销也是，只有对执行频繁的代码才值得编译，如果把所有代码都编译 则会显著增加代码所占空间，导致“代码爆炸”。

**这也就解释了为什么有些JVM会选择不总是做JIT编译,而是选择用解释器+JIT编译器的混合执行引擎.**

#### 为何要实现两个不同的即时编译器

HotSpot虚拟机中内置了两个即时编译器:Client Complier和Server Complier，简称为C1、C2编译 器，分别用在客户端和服务端。

目前主流的HotSpot虚拟机中默认是采用解释器与其中一个编译器直接配合的方式工作。程序使用哪个 编译器，取决于**虚拟机运行的模式**。HotSpot虚拟机会根据自身版本与宿主机器的硬件性能自动选择运 行模式，用户也可以使用“**-client**”或“**-server**”参数去强制指定虚拟机运行在Client模式或Server模式。

用Client Complier获取更高的**编译速度**，用Server Complier 来获取更好的**编译质量**。为什么提供多个 即时编译器与为什么提供多个垃圾收集器类似，都是为了适应不同的应用场景。

#### **如何编译为本地代码?**

**Server Compiler**和**Client Compiler**两个编译器的编译过程是不一样的。

对Client Compiler来说，它是一个简单快速的编译器，主要关注点在于**局部优化**，而放弃许多耗时较长 的全局优化手段。

而Server Compiler则是专门面向服务器端的，并为服务端的性能配置特别调整过的编译器，是一个**充分优化**过的高级编译器。

### JIT优化

HotSpot 虚拟机使用了很多种优化技术，这里只简单介绍其中的几种，完整的优化技术介绍可以参考官

网内容。

#### 公共子表达式的消除

公共子表达式消除是一个普遍应用于各种编译器的经典优化技术，他的含义是:如果一个表达式E已经 计算过了，并且从先前的计算到现在E中所有变量的值都没有发生变化，那么E的这次出现就成为了公共 子表达式。对于这种表达式，没有必要花时间再对他进行计算，只需要直接用前面计算过的表达式结果 代替E就可以了。

如果这种优化仅限于程序的基本块内，便称为**局部公共子表达式消除(Local Common Subexpression Elimination)**

如果这种优化范围涵盖了多个基本块，那就称为**全局公共子表达式消除(Global Common Subexpression Elimination)**。

举个简单的例子来说明他的优化过程，假设存在如下代码:

```java
intd=(c*b)*12+a+(a+b*c);
```

**如果**这段代码交给Javac编译器则**不会进行任何优化**，那生成的代码如下所示，是完全遵照Java源码的写 法直译而成的。

> iload_2 // b
>  imul // 计算b*c
>  bipush 12 // 推入12
>  imul // 计算(c*b)*12
>  iload_1 // a
>  iadd // 计算(c*b)*12+a iload_1 // a
>  iload_2 // b
>  iload_3 // c
>  imul // 计算b*c
>  iadd // 计算a+b*c
>  iadd // 计算(c*b)*12+a+(a+b*c) istore 4

当这段代码进入到虚拟机即时编译器后，他将进行如下优化:编译器检测到”c*b“ ”b*c“是一样的表达 式，而且在计算期间b与c的值是不变的。因此，这条表达式就可能被视为:

```java
intd=E*12+a+(a+E);
```

这时，编译器还可能(取决于哪种虚拟机的编译器以及具体的上下文而定)进行另外一种优化:代数化 简(Algebraic Simplification)，把表达式变为:

```java
intd=E*13+a*2;
```

表达式进行变换之后，再计算起来就可以节省一些时间了。

#### **方法内联**

在使用JIT进行即时编译时，将**方法调用**直接使用**方法体中的代码**进行替换，这就是**方法内联**，减少了方法调用过程中压栈与入栈的开销。同时为之后的一些优化手段提供条件。如果JVM监测到一些小方法被 频繁的执行，它会把方法的调用替换成方法体本身。

比如说下面这个:

```java
private int add4(int x1, int x2, int x3, int x4) {
    return add2(x1, x2) + add2(x3, x4);
}
private int add2(int x1, int x2) {
    return x1 + x2;
}
```

可以肯定的是运行一段时间后JVM会把add2方法去掉，并把你的代码翻译成:

```java
private int add4(int x1, int x2, int x3, int x4) {
       return x1 + x2 + x3 + x4;
}
```

#### **逃逸分析**

逃逸分析(Escape Analysis)是目前Java虚拟机中比较前沿的优化技术。这是一种可以有效减少Java 程序中同步负载和内存堆分配压力的跨函数全局数据流分析算法。通过**逃逸分析**，Java Hotspot编译器能够 分析出一个**新的对象的引用的使用范围**从而决定是否要**将这个对象分配到堆上**。

**逃逸分析的基本行为就是分析对象动态作用域:当一个对象在方法中被定义后，它可能被外部方法所引 用，例如作为调用参数传递到其他地方中，称为方法逃逸。**

逃逸分析包括:

- 全局变量赋值逃逸
-  方法返回值逃逸
-  实例引用发生逃逸 
-  线程逃逸:赋值给类变量或可以在其他线程中访问的实例变量

例如:

```java
public class EscapeAnalysis { //全局变量
  public static Object object;
  public void globalVariableEscape(){//全局变量赋值逃逸 
    object = new Object();
  }
  public Object methodEscape(){ //方法返回值逃逸 
    return new Object();
  }
  public void instancePassEscape(){ //实例引用发生逃逸 
    this.speak(this);
  }
  public void speak(EscapeAnalysis escapeAnalysis){ 
    System.out.println("Escape Hello");
  } 
}
```

使用方法逃逸的案例进行分析:

```java
public static StringBuffer craeteStringBuffer(String s1, String s2) { 
  StringBuffer sb = new StringBuffer();
  sb.append(s1);
  sb.append(s2);
  return sb; 
}
```

> **StringBuffer sb是一个方法内部变量，上述代码中直接将sb返回，这样这个StringBuffer有可 能被其他方法所改变，这样它的作用域就不只是在方法内部，虽然它是一个局部变量，称其逃逸 到了方法外部。甚至还有可能被外部线程访问到，譬如赋值给类变量或可以在其他线程中访问的 实例变量，称为线程逃逸。**

上述代码如果想要StringBuffer sb不逃出方法，可以这样写:

```java
public static String createStringBuffer(String s1, String s2) {
	StringBuffer sb = new StringBuffer();
  sb.append(s1);
  sb.append(s2);
  return sb.toString();
}
```

不直接返回 StringBuffer，那么StringBuffer将不会逃逸出方法。

使用逃逸分析，编译器可以对代码做如下优化:

> 一、同步省略。如果一个对象被发现只能从一个线程被访问到，那么对于这个对象的操作可以不考虑同步。
>
> 二、将堆分配转化为栈分配。如果一个对象在子程序中被分配，要使指向该对象的指针永远不会逃逸，对象可能是栈分配的候选，而不是堆分配。
>
> 三、分离对象或标量替换。有的对象可能不需要作为一个连续的内存结构存在也可以被访问到，那么对象的部分(或全部)可以不存储在内存，而是存储在CPU寄存器中。

在Java代码运行时，通过JVM参数可指定是否开启逃逸分析，

```java
-XX:+DoEscapeAnalysis : 表示开启逃逸分析 
-XX:-DoEscapeAnalysis : 表示关闭逃逸分析
```

从jdk 1.7开始已经默认开始逃逸分析，如需关闭，需要指定 -XX:-DoEscapeAnalysis

#### **对象的栈上内存分配**

我们知道，在一般情况下，对象和数组元素的内存分配是在堆内存上进行的。但是随着JIT编译器的日渐成熟，很多优化使这种分配策略并不绝对。JIT编译器就可以在编译期间根据逃逸分析的结果，来决定是否可以将对象的内存分配从堆转化为栈。

我们来看以下代码:

```java
public class EscapeAnalysisTest {
    public static void main(String[] args) {
      long a1 = System.currentTimeMillis(); for (int i = 0; i < 1000000; i++) {
      alloc(); }
      // 查看执行时间
      long a2 = System.currentTimeMillis(); 
      System.out.println("cost " + (a2 - a1) + " ms"); 			 
      // 为了方便查看堆内存中对象个数，线程sleep
      try {
      Thread.sleep(100000);
      } catch (InterruptedException e1) {
      e1.printStackTrace(); }
      }
          private static void alloc() {
              User user = new User();
      }
          static class User {
      } 
}
```

> 其实代码内容很简单，就是使用for循环，在代码中创建100万个User对象。 
>
> **我们在alloc方法中定义了User对象，但是并没有在方法外部引用他。也就是说，这个对象并不会逃逸到alloc外部。经过JIT的逃逸分析之后，就可以对其内存分配进行优化。**

我们指定以下JVM参数并运行:

```java
-Xmx4G
-Xms4G 
-XX:-DoEscapeAnalysis
-XX:+PrintGCDetails 
-XX:+HeapDumpOnOutOfMemoryError
```

在程序打印出 `cost XX ms` 后，代码运行结束之前，我们使用jmap命令，来查看下当前堆内存中有多 少个User对象:

```
~ jps

2809 StackAllocTest

2810 Jps

~ jmap -histo 2809

num #instances #bytes class name 
----------------------------------------------
1:           524		87282184  [I
2:       1000000		16000000  StackAllocTest$User
3:          6806		 2093136  [B
4:          8006		 1320872  [C
5:          4188		100512 java.lang.String 
6:           581		66304 java.lang.Class
```

> 从上面的jmap执行结果中我们可以看到，堆中共创建了100万个 StackAllocTest$User 实例。
>
> 在关闭逃避分析的情况下(-XX:-DoEscapeAnalysis)，虽然在alloc方法中创建的User对象并没 有逃逸到方法外部，但是还是被分配在堆内存中。也就说，如果没有JIT编译器优化，没有逃逸分 析技术，正常情况下就应该是这样的。即所有对象都分配到堆内存中。

接下来，我们开启逃逸分析，再来执行下以上代码。

在程序打印出 cost XX ms 后，代码运行结束之前，我们使用 jmap 命令，来查看下当前堆内存中有多 少个User对象:

```
~ jps
709
2858 Launcher
2859 StackAllocTest
2860 Jps
~ jmap -histo 2859
num #instances ---------------------------------------------
1:           524		101944280  [I
2:          6806		2093136  [B
3:         83619		1337904  StackAllocTest$User
4:          8006		1320872  [C
5:          4188		100512 java.lang.String
6:           581		66304 java.lang.Class
```

> 从以上打印结果中可以发现，开启了逃逸分析之后(-XX:+DoEscapeAnalysis)，在堆内存中只有 8万多个 `StackAllocTest$User` 对象。也就是说在经过JIT优化之后，堆内存中分配的对象数量， 从100万降到了8万。
>
> 除了以上通过jmap验证对象个数的方法以外，还可以**尝试将堆内存调小**，然后执行以上代码，根 据GC的次数来分析，也能发现，**开启了逃逸分析之后，在运行期间，GC次数会明显减少**。正是 因为很多堆上分配被优化成了栈上分配，所以GC次数有了明显的减少。

**总结**

所以，如果以后再有人问你:**是不是所有的对象和数组都会在堆内存分配空间**?

那么你可以告诉他:**不一定**，随着JIT编译器的发展，在编译期间，如果JIT经过逃逸分析，发现有些对象 没有逃逸出方法，那么有可能堆内存分配会被优化成栈内存分配。但是这也并不是绝对的。就像我们前 面看到的一样，在开启逃逸分析之后，也并不是所有User对象都没有在堆上分配。



#### 标量替换

**标量(Scalar)**是指一个无法再分解成更小的数据的数据 。 

在JIT阶段，如果经过逃逸分析，发现一个对象不会被外界访问的话，那么经过JIT优化，就会把这个对象拆解成若干个其中包含的若干个成员变量来代替。

```java
//有一个类A 
public class A{
     public int a=1;
     public int b=2
}
//方法getAB使用类A里面的a,b 
private void getAB(){
	A x = new A(); 
  x.a;
	x.b;
}
//JVM在编译的时候会直接编译成 
private void getAB(){
	a = 1;
	b = 2; 
}
//这就是标量替换
```

#### **同步锁消除**

同样基于逃逸分析，当加锁的变量不会发生逃逸，是线程私有的完全没有必要加锁。 在JIT编译时期就 可以将同步锁去掉，以减少加锁与解锁造成的资源开销。

```java
public class TestLockEliminate {
    public static String getString(String s1, String s2) {
      StringBuffer sb = new StringBuffer(); sb.append(s1);
      sb.append(s2);
      return sb.toString();
    }
    public static void main(String[] args) {
      long tsStart = System.currentTimeMillis(); 
      for (int i = 0; i < 10000000; i++) {
        getString("TestLockEliminate ", "Suffix");
      }
      System.out.println("一共耗费:" + (System.currentTimeMillis() - tsStart) + " ms");
    } 
}
```

> getString()方法中的StringBuffer数以函数内部的局部变量，进作用于方法内部，不可能逃逸出该方法，因此他就不可能被多个线程同时访问，也就没有资源的竞争，但是StringBuffer的append 操作却需要执行同步操作，代码如下:

```java
@Override
public synchronized StringBuffer append(String str) { 
  toStringCache = null;
	super.append(str);
	return this;
}
```

逃逸分析和锁消除分别可以使用参数 `-XX:+DoEscapeAnalysis` 和 `-XX:+EliminateLocks` (锁消除必须 在-server模式下)开启。使用如下参数运行上面的程序:

> -XX:+DoEscapeAnalysis
>
>  -XX:-EliminateLocks

得到如下结果:



// TODO :结果未执行

使用如下命令运行程序:

> -XX:+DoEscapeAnalysis 
>
> -XX:+EliminateLocks

得到如下结果:



// TODO :结果未执行

## 带你认识一下class文件

### class文件概述

我们可任意打开一个Class文件(**使用Hex Editor等工具打开**)，内容如下(**内容是16进制**):

![image-20191211223614004](/Users/lee/Library/Application Support/typora-user-images/image-20191211223614004.png)

**十六进制转字符串**:http://www.bejson.com/convert/ox2str/ 

进制转换网址(**十六进制转十进制**):http://tool.oschina.net/hexconvert/

**参考下图去阅读上面的十六进制文档:**

![image-20191211223711994](/Users/lee/Library/Application Support/typora-user-images/image-20191211223711994.png)

据上述的叙述，我们可以将class的文件组织结构概括成以下面这个表格(**其中U表示u4表示4个无符号字节，u2表示2个无符号字节**):

| 类型           | 名称                            | 数量                  |
| -------------- | ------------------------------- | --------------------- |
| u4             | magic(魔数)                     | 1                     |
| u2             | minor_version(jdk次版本号)      | 1                     |
| u2             | major_version( JDK主版本号)     | 1                     |
| u2             | constant_pool_count(常量池数量) | 1                     |
| cp_info        | constan_pool(常量表)            | Constant_pool_count-1 |
| u2             | access_flags(访问标志)          | 1                     |
| u2             | this_class(类引用)              | 1                     |
| u2             | super_class(父类引用)           | 1                     |
| u2             | interfaces_count(接口数量)      | 1                     |
| u2             | interfaces(接口数组)            | Interface_count       |
| u2             | fields_count(字段数量)          | 1                     |
| field_info     | fields(字段表)                  | Fields_cout           |
| u2             | methods_count(方法数量)         | 1                     |
| method_info    | methods(方法表)                 | methods_count         |
| u2             | attributes_count(属性数量)      | 1                     |
| attribute_info | attributes(属性表)              | Attribuates_count     |

#### 魔数

**所有的由Java编译器编译而成的class文件的前四个字节都是"0xCAFEBABE".**

它的作用在于:当JVM在尝试加载某个文件到内存中来的时候，会首先判断此class文件有没有JVM认为 可以接受的“签名”，即JVM会首先读取文件的前4个字节，判断该4个字节是否是“0xCAFEBABE”，如果 是，则JVM会认为可以将此文件当作class文件来加载并使用。

#### 版本号

随着Java本身的发展，Java语言特性和JVM虚拟机也会有相应的更新和增强。目前我们能够用到的JDK版 本如:1.5，1.6，1.7，还有现如今的1.8及更高的版本。发布新版本的目的在于:在原有的版本上增加 新特性和相应的JVM虚拟机的优化。而随着主版本发布的次版本，则是修改相应主版本上出现的bug。 我们平时只需要关注主版本就可以了。

**主版本号和次版本号在class文件中各占两个字节，副版本号占用第5、6两个字节，而主版本号则占用 第7，8两个字节。**JDK1.0的主版本号为45，以后的每个新主版本都会在原先版本的基础上加1。若现在 使用的是JDK1.7编译出来的class文件，则相应的主版本号应该是51,对应的7，8个字节的十六进制的值 应该是 0x33。

一个 JVM实例只能支持特定范围内的主版本号 (Mi 至Mj) 和 0 至特定范围内 (0 至 m) 的副版本 号。假设一个 Class 文件的格式版本号为 V， 仅当**Mi.0 ≤ v ≤ Mj.m**成立时，这个 Class 文件才可以被 此 Java 虚拟机支持。不同版本的 Java 虚拟机实现支持的版本号也不同，高版本号的 Java 虚拟机实现可 以支持低版本号的 Class 文件，反之则不成立。

JVM在加载class文件的时候，会读取出主版本号，然后比较这个class文件的主版本号和JVM本身的版本 号，如果JVM本身的版本号 < class文件的版本号，JVM会认为加载不了这个class文件，会抛出我们经常 见到的" `java.lang.UnsupportedClassVersionError: Bad version number in .class file` " **Error错误**;反之，JVM会认为可以加载此class文件，继续加载此class文件。

**JDK版本号信息对照表:**

| JDK版本 | 16进制版本号 | 十进制版本号 |
| ------- | ------------ | ------------ |
| JDK8    | 00 00 00 34  | 52           |
| JDK7    | 00 00 00 33  | 51           |
| JDK6    | 00 00 00 32  | 50           |
| JDK5    | 00 00 00 31  | 49           |
| JDK1.4  | 00 00 00 30  | 48           |
| JDK1.3  | 00 00 00 2F  | 47           |
| JDK1.2  | 00 00 00 2E  | 46           |
| JDK1.1  | 00 00 00 2D  | 45           |

**小贴士:**

> 1. 有时候我们在运行程序时会抛出这个**Error** **错误**:"*java.lang.UnsupportedClassVersionError: Bad version number in .class file*"。上面已经揭示了出现这个问题的原因，就是在于当前尝 试加载class文件的JVM虚拟机的版本 低于class文件的版本。解决方法:1.重新使用当前jvm 编译源代码，然后再运行代码;2.将当前JVM虚拟机更新到class文件的版本。
>
> 2. 怎样查看class文件的版本号?可以借助于文本编辑工具，直接查看该文件的7，8个字节的 值，确定class文件是什么版本的。
>
> 当然快捷的方式使用JDK自带的javap工具，如当前有Math.class 文件，进入此文件所在的目录， 然后执行 ”**javap -v Math**“,结果会类似如下所示:
>
> ![image-20191212105512156](/Users/lee/Library/Application Support/typora-user-images/image-20191212105512156.png)

#### 常量池计数器

常量池是class文件中的非常重要的结构,它描述着整个class文件的字面量信息. 常量池是由一组constant_pool结构体数组组成的,而数组的大小则由常量池计数器指定.常量池计数器constant_pool_count的值=constant_pool表中的成员数+1.constant_pool表的索引值只有在大于0且小于constant_pool_count时才会被认为是有效的.

**注意事项:**

常量池计数器默认从1开始而不是从0开始:

当constant_pool_count = 1时, 常量池中查到cp_info个数为0;当constant_pool_count为n时,常量池中的cp_info个数为n-1.

原因:

在制定class文件规范的时候,将索引#0项常量空出来是有特殊考虑的.这样当:某些数据在特定情况下想表达"不引用任何一个常量池项"的意思时,就可以将其引用的常量的索引值设置为#0来表示.

#### 常量池数据区

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191212110602591.png" alt="image-20191212110602591" style="zoom:50%;" />

#### 访问标志

访问标志，access_flags 是一种掩码标志，用于表示某个类或者接口的访问权限及基础属性。

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191212110808578.png" alt="image-20191212110808578" style="zoom:50%;" />

#### 类索引

> 类索引，this_class的值必须是对constant_pool表中项目的一个有效索引值。constant_pool表 在这个索引处的项必须为CONSTANT_Class_info 类型常量，表示这个 Class 文件所定义的类或接 口。

#### 父类索引

> 父类索引，对于类来说，super_class 的值必须为 0 或者是对constant_pool 表中项目的一个有 效索引值。
>
> 如果它的值不为 0，那 constant_pool 表在这个索引处的项必须为CONSTANT_Class_info 类型常 量，表示这个 Class 文件所定义的类的直接父类。当前类的直接父类，以及它所有间接父类的 access_flag 中都不能带有ACC_FINAL 标记。对于接口来说，它的Class文件的super_class项的 值必须是对constant_pool表中项目的一个有效索引值。constant_pool表在这个索引处的项必须为 代表 java.lang.Object 的 CONSTANT_Class_info 类型常量 。
>
> 如果 Class 文件的 super_class的值为 0，那这个Class文件只可能是定义的是 java.lang.Object类，只有它是唯一没有父类的类。

#### 接口计数器

```
接口计数器,interfaces_count的值表示当前类或接口的[直接父接口数量]
```

接口信息数据区

```
接口表，interfaces[]数组中的每个成员的值必须是一个对constant_pool表中项目的一个有效索引值，它的长度为interfaces_count。每个成员interfaces[i] 必须为CONSTANT_Class_info类型常量，其中【0 ≤ i<interfaces_count】。在interfaces[]数组中，成员所表示的接口顺序和对应的源代码中给定的接口顺序(从左至右)一样，即interfaces[0]对 应的是源代码中最左边的接口。
```

#### 字段计数器

```
字段计数器，fields_count的值表示当前 Class文件fields[]数组的成员个数。fields[]数组中每一项都是一个field_info结构的数据项，它用于表示该类或接口声明的类字段或者实例字段。
```

#### 字段信息数据区

```
字段表，fields[]数组中的每个成员都必须是一个fields_info结构的数据项，用于表示当前类或接 口中某个字段的完整描述。 fields[]数组描述当前类或接口声明的所有字段，但不包括从父类或父接 口继承的部分。
```

#### 方法计数器

```
方法计数器， methods_count的值表示当前Class 文件 methods[]数组的成员个数。Methods[] 数组中每一项都是一个 method_info 结构的数据项。
```

#### 方法信息数据区

```
方法表，methods[] 数组中的每个成员都必须是一个 method_info 结构的数据项，用于表示当前类 或接口中某个方法的完整描述。

如果某个method_info 结构的access_flags 项既没有设置 ACC_NATIVE 标志也没有设置 ACC_ABSTRACT 标志，那么它所对应的方法体就应当可以被 Java 虚拟机直接从当前类加载，而不需 要引用其它类。

method_info结构可以表示类和接口中定义的所有方法，包括实例方法、类方法、实例初始化方法和类 或接口初始化方法 。

【methods[]数组只描述当前类或接口中声明的方法，不包括从父类或父接口继承的方法】。
```

#### 属性计数器

```
属性计数器，attributes_count的值表示当前 Class 文件attributes表的成员个数。 attributes表中每一项都是一个attribute_info 结构的数据项。
```

#### 属性信息数据区

```
属性表，attributes 表的每个项的值必须是attribute_info结构。

在Java 7 规范里，Class文件结构中的attributes表的项包括下列定义的属性: InnerClasses
、 EnclosingMethod 、 Synthetic 、Signature、SourceFile，SourceDebugExtension 、Deprecated、RuntimeVisibleAnnotations 、RuntimeInvisibleAnnotations以及 BootstrapMethods属性。

对于支持 Class 文件格式版本号为 49.0 或更高的 Java 虚拟机实现，必须正确识别并读取 attributes表中的Signature、RuntimeVisibleAnnotations和 RuntimeInvisibleAnnotations属性。对于支持Class文件格式版本号为 51.0 或更高的 Java 虚拟机实现，必须正确识别并读取 attributes表中的BootstrapMethods属性。Java 7 规范 要求 任一 Java 虚拟机实现可以自动忽略 Class 文件的 attributes表中的若干 (甚至全部) 它不可 识别的属性项。任何本规范未定义的属性不能影响Class文件的语义，只能提供附加的描述信息 。
 
```

### class常量池理解

#### 1.常量池在class文件的什么位置

![image-20191212204435785](/Users/lee/Library/Application Support/typora-user-images/image-20191212204435785.png)

#### 2.常量池的里面是怎么组织的

**cp_info**:常量池项

**constant_pool_count**:常量池计算器

![image-20191212204513443](/Users/lee/Library/Application Support/typora-user-images/image-20191212204513443.png)

#### 3.常量池项(cp_info)的结构是什么?

![image-20191212204540800](/Users/lee/Library/Application Support/typora-user-images/image-20191212204540800.png)

JVM虚拟机规定了不同的tag值和不同类型的字面量对应关系如下:

| Tag值 | 表示的字面量                                                 | 更细化的结构                          |
| ----- | ------------------------------------------------------------ | ------------------------------------- |
| 1     | 用于表示字符串常量的值                                       | CONSTANT_Utf8_info                    |
| 3     | 表示4字节(int)的数值常量                                     | CONSTANT_Utf8_Integer_info            |
| 4     | 表示4字节(Float)的数值常量                                   | CONSTANT_Utf8_Flot_info               |
| 5     | 表示8字节(Long)的数值常量                                    | CONSTANT_Utf8_Long_info               |
| 6     | 表示8字节(dubbo)的数值常量                                   | CONSTANT_Utf8_Dubbo_info              |
| 7     | 表示类或接口的完全限定名                                     | CONSTANT_Utf8_Class_info              |
| 8     | 用于表示java.lang.String类型的常量对象                       | CONSTANT_Utf8_String_info             |
| 9     | 表示类中的字段                                               | CONSTANT_Utf8_Fieldref_info           |
| 10    | 表示类中的方法                                               | CONSTANT_Utf8_Methodref_info          |
| 11    | 表示类所实现的接口的方法                                     | CONSTANT_Utf8_InterfaceMethodref_info |
| 12    | 表示字段或方法的名称和类型                                   | CONSTANT_Utf8_NameAndType_info        |
| 15    | 表示方法句柄                                                 | CONSTANT_Utf8_MethodHandle_info       |
| 16    | 表示方法类型                                                 | CONSTANT_Utf8_MethodType_info         |
| 18    | 用于表示invokedynamic指令所使用的到的引导方法(Bootstrap Method).引导方法使用到动态调用名称(dynamic Invocation Name), 参数和请求返回类型,以及可以选择性的副驾被称为静态参数(static Arguments)的常量序列 | CONSTANT_Utf8_InvokeDynamic_info      |

所以根据cp_info中的tag 不同的值，可以将cp_info 更细化为以下结构体:

![image-20191212225132033](/Users/lee/Library/Application Support/typora-user-images/image-20191212225132033.png)

现在让我们看一下细化了的常量池的结构会是类似下图所示的样子:

![image-20191212225149101](/Users/lee/Library/Application Support/typora-user-images/image-20191212225149101.png)

#### 4.int和float数据类型的常量在常量池中是怎样表示存储的?

Java语言规范规定了 int类型和Float 类型的数据类型占用 **4** 个字节的空间。那么存在于class字节码文件中的该类型的常量是如何存储的呢?

![image-20191212225300569](/Users/lee/Library/Application Support/typora-user-images/image-20191212225300569.png)

举例:建下面的类 IntAndFloatTest.java，在这个类中，我们声明了五个变量，但是取值就两种int类型 的**10** 和Float类型的**11f**。

```java
package com.kkb.jvm;
public class IntAndFloatTest {
    private final int a = 10;
    private final int b = 10;
    private float c = 11f;
    private float d = 11f;
    private float e = 11f;
}
```

然后用编译器编译成IntAndFloatTest.class字节码文件，我们通过**javap -v IntAndFloatTest** 指令来看 一下其常量池中的信息，可以看到虽然我们在代码中写了两次**10** 和三次**11f**，但是常量池中，就只有一 个常量**10** 和一个常量**11f**,如下图所示:

![image-20191213195748566](/Users/lee/Library/Application Support/typora-user-images/image-20191213195748566.png)

从结果上可以看到常量池第**#8** 个**常量池项(cp_info)** 就是CONSTANT_Integer_info,值为**10**;第**#23**个**常 量池项(cp_info)** 就是CONSTANT_Float_info,值为**11f**。

代码中所有用到 int 类型 **10** 的地方，会使用指向常量池的指针值**#8** 定位到第**#8** 个**常量池项 (cp_info)**，即值为 **10**的结构体CONSTANT_Integer_info，而用到float类型的**11f**时，也会指向常量池 的指针值#23来定位到第#23个**常量池项(cp_info)** 即值为**11f**的结构体CONSTANT_Float_info。如下图 所示:

![image-20191213200010560](/Users/lee/Library/Application Support/typora-user-images/image-20191213200010560.png)

#### 5.long和dubbo数据类型的常量在常量池中怎么存储的呢?

Java语言规范规定了 **long** 类型和 **double**类型的数据类型占用**8** 个字节的空间。那么存在于class 字节 码文件中的该类型的常量是如何存储的呢?

![image-20191213200157151](/Users/lee/Library/Application Support/typora-user-images/image-20191213200157151.png)

举例:建下面的类 LongAndDoubleTest.java，在这个类中，我们声明了六个变量，但是取值就两种 **Long** 类型的**-6076574518398440533L**和**Dubbo**类型的**10.1234567890D**.

![image-20191213200310291](/Users/lee/Library/Application Support/typora-user-images/image-20191213200310291.png)

然后用编译器编译成 LongAndDoubleTest.class 字节码文件，我们通过**javap -v LongAndDoubleTest**指令来看一下其常量池中的信息，可以看到虽然我们在代码中写了三 次**-6076574518398440533L** 和三次**10.1234567890D**，但是常量池中，就只有一个常 量**-6076574518398440533L** 和一个常量**10.1234567890D**,如下图所示:

![image-20191213200331212](/Users/lee/Library/Application Support/typora-user-images/image-20191213200331212.png)

从结果上可以看到常量池第 **#18** 个**常量池项(cp_info)** 就是**CONSTANT_Long_info**,值为**-6076574518398440533L** ;

第 **#26**个**常量池项(cp_info)** 就是**CONSTANT_Double_info**,值为 **10.1234567890D**。

代码中所有用到 long 类型**-6076574518398440533L** 的地方，会使用指向常量池的指针值**#18** 定位到 第 **#18** 个**常量池项(cp_info)**，即值为**-6076574518398440533L** 的结构体**CONSTANT_Long_info**，而 用到double类型的**10.1234567890D**时，也会指向常量池的指针值**#26**来定位到第 **#26** 个**常量池项** **(cp_info)** 即值为**10.1234567890D**的结构体**CONSTANT_Double_info**。如下图所示:

![image-20191213200437098](/Users/lee/Library/Application Support/typora-user-images/image-20191213200437098.png)

#### 6.String类型的字符串常量在常量池中是怎样是怎样表示和存储的?

对于字符串而言，JVM会将字符串类型的字面量以UTF-8 编码格式存储到在class字节码文件中。这么说 可能有点摸不着北，我们先从直观的Java源码中中出现的用双引号"" 括起来的字符串来看，在编译器编 译的时候，都会将这些字符串转换成**CONSTANT_String_info**结构体，然后放置于常量池中。其结构如 下所示:

![image-20191213200601810](/Users/lee/Library/Application Support/typora-user-images/image-20191213200601810.png)

如上图所示的结构体，**CONSTANT_String_info**结构体中的string_index的值指向了 CONSTANT_Utf8_info结构体，而字符串的utf-8编码数据就在这个结构体之中。如下图所示:

![image-20191213200621231](/Users/lee/Library/Application Support/typora-user-images/image-20191213200621231.png)

请看一例，定义一个简单的StringTest.java类，然后在这个类里加一个"JVM原理" 字符串，然后，我们 来看看它在class文件中是怎样组织的。

```java
package com.kkb.jvm;
public class StringTest {
	private String s1 = "JVM原理"; 
  private String s2 = "JVM原理"; 
  private String s3 = "JVM原理"; 
  private String s4 = "JVM原理";
}
```

将Java源码编译成StringTest.class文件后，在此文件的目录下执行 javap -v StringTest 命令，会看到如 下的常量池信息的轮廓:

![image-20191213200713482](/Users/lee/Library/Application Support/typora-user-images/image-20191213200713482.png)

(PS :使用javap -v 指令能看到易于我们阅读的信息，查看真正的字节码文件可以使用HEXWin、 NOTEPAD++、UtraEdit 等工具。)

在面的图中，我们可以看到**CONSTANT_String_info**结构体位于常量池的第**#15**个索引位置。而存 放"Java虚拟机原理" 字符串的 UTF-8编码格式的字节数组被放到**CONSTANT_Utf8_info**结构体中，该结 构体位于常量池的第**#16**个索引位置。上面的图只是看了个轮廓，让我们再深入地看一下它们的组织 吧。请看下图:

![image-20191213201437675](/Users/lee/Library/Application Support/typora-user-images/image-20191213201437675.png)

由上图可见:“**JVM**原理”的UTF-8编码的数组是:4A564D E5 8E 9FE7 90 86，并且存入了 **CONSTANT_Utf8_info**结构体中。

#### 7.类文件中定义的类名和类中使用到的类在常量池重视怎样被组织和存储的?

JVM会将某个Java 类中所有使用到了的**类的完全限定名** 以**二进制形式的完全限定名** 封装成 **CONSTANT_Class_info**结构体中，然后将其放置到常量池里。**CONSTANT_Class_info** 的tag值为 **7** **。** 其结构如下:

![image-20191213201624548](/Users/lee/Library/Application Support/typora-user-images/image-20191213201624548.png)

> Tips:**类的完全限定名**和**二进制形式的完全限定名**
>
> 在某个Java源码中，我们会使用很多个类，比如我们定义了一个 **ClassTest**的类，并把它放到 **com.kkb.jvm** 包下，则 **ClassTest**类的完全限定名为**com.kkb.jvm.ClassTest**，将JVM编译器将 类编译成class文件后，此完全限定名在class文件中，是以二进制形式的完全限定名存储的，即它 会把完全限定符的"**.**"换成**"/"** ，即在class文件中存储的 **ClassTest**类的完全限定名称 是"**com/kkb/jvm/ClassTest**"。因为这种形式的完全限定名是放在了class二进制形式的字节码 文件中，所以就称之为 **二进制形式的完全限定名。**

举例，我们定义一个很简单的**ClassTest**类，来看一下常量池是怎么对类的完全限定名进行存储的。

```java
package com.jvm;
import java.util.Date; 
public class ClassTest {
   private Date date =new Date();
}
```

将Java源码编译成**ClassTest.class**文件后，在此文件的目录下执行 javap -v ClassTest 命令，会看到如 下的常量池信息的轮廓:

![image-20191213201718322](/Users/lee/Library/Application Support/typora-user-images/image-20191213201718322.png)

如上图所示，在**ClassTest.class**文件的常量池中，共有 3 个**CONSTANT_Class_info**结构体，分别表示 **ClassTest** 中用到的Class信息。 我们就看其中一个表示**com/jvm.ClassTest**的 **CONSTANT_Class_info** 结构体。它在常量池中的位置是**#1**，它的name_index值为**#2**，它指向了常量 池的第**2** 个常量池项，如下所示:

![image-20191213201746026](/Users/lee/Library/Application Support/typora-user-images/image-20191213201746026.png)

**注意:**

```
对于某个类而言，其class文件中至少要有两个CONSTANT_Class_info常量池项，用来表示自己的类信息和其父类信息。(除了java.lang.Object类除外，其他的任何类都会默认继承自java.lang.Object)如果类声明实现了某些接口，那么接口的信息也会生成对应的CONSTANT_Class_info常量池项。
```

除此之外，如果在类中使用到了其他的类，只有真正使用到了相应的类，JDK编译器才会将类的信息组 成CONSTANT_Class_info常量池项放置到常量池中。如下图:

```java
package com.kkb.jvm; 
import java.util.Date; 
public class Other{
    private Date date;
    public Other()  {
			Date da; 
    }
}
```

上述的Other的类，在JDK将其编译成class文件时，常量池中并没有java.util.Date对应的 CONSTANT_Class_info常量池项，为什么呢?

在Other类中虽然定义了Date类型的两个变量date、da，但是JDK编译的时候，认为你只是声明 了“Ljava/util/Date”类型的变量，并没有实际使用到Ljava/util/Date类。将类信息放置到常量池中的目 的，是为了在后续的代码中有可能会反复用到它。很显然，JDK在编译Other类的时候，会解析到Date 类有没有用到，发现该类在代码中就没有用到过，所以就认为没有必要将它的信息放置到常量池中了。

将上述的Other类改写一下，仅使用new Date()，如下图所示:

```java
package com.kkb.jvm; 
import java.util.Date; 
public class Other{
   public Other() {
			new Date(); 
   }
}
```

这时候使用javap -v Other ，可以查看到常量池中有表示java/util/Date的常量池项:

![image-20191213201930524](/Users/lee/Library/Application Support/typora-user-images/image-20191213201930524.png)

**总结:**

1. 对于某个类或接口而言，其自身、父类和继承或实现的接口的信息会被直接组装成 CONSTANT_Class_info常量池项放置到常量池中;

2. 类中或接口中使用到了其他的类，只有在类中实际使用到了该类时，该类的信息才会在常量池中有 对应的CONSTANT_Class_info常量池项;

3. 类中或接口中仅仅定义某种类型的变量，JDK只会将变量的类型描述信息以UTF-8字符串组成 CONSTANT_Utf8_info常量池项放置到常量池中，上面在类中的private Date date;JDK编译器只会 将表示date的数据类型的“Ljava/util/Date”字符串放置到常量池中。

#### 8.哪些字面量会进入常量池中?

**结论:**

```
1. final类型的8种基本类型的值会进入常量池。
2. 非final类型(包括static的)的8种基本类型的值，只有double、float、long的值会进入常量 池。
3. 常量池中包含的字符串类型字面量(双引号引起来的字符串值)。
```

**测试代码:**

```java
public class Test{
  private int int_num = 110;
  private char char_num = 'a';
  private short short_num = 120; 
  private float float_num = 130.0f; 
  private double double_num = 140.0; 
  private byte byte_num = 111;
  private long long_num = 3333L; 
  private long long_delay_num;
  private boolean boolean_flage = true;
  public void init() { 
    this.long_delay_num = 5555L;
	} 
}
```

**使用javap命令打印的结果如下:**

![image-20191213202641157](/Users/lee/Library/Application Support/typora-user-images/image-20191213202641157.png)

### class文件中的引用和特殊字符串

#### 符号引用

符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能够无歧义的定 位到目标即可。

例如，在Class文件中它以**CONSTANT_Class_info**、**CONSTANT_Fieldref_info**、**CONSTANT_Methodref_info**等类型的常量出现。

**符号引用与虚拟机的内存布局无关，引用的目标并不一定加载到内存中。**

在Java中，一个java类将会编译成一个class文件。在编译时，java类并不知道所引用的类的实际地址， 因此只能使用符号引用来代替。

比如 org.simple.People类 引用了 `org.simple.Language`类 ，在编译时People类并不知道Language 类的实际内存地址，因此只能使用符号 `org.simple.Language` (假设是这个，当然实际中是由类似于 CONSTANT_Class_info的常量来表示的)来表示Language类的地址。

各种虚拟机实现的内存布局可能有所不同，但是它们能接受的符号引用都是一致的，因为符号引用的字 面量形式明确定义在Java虚拟机规范的Class文件格式中。

#### 直接引用

直接引用可以是:

1. 直接指向目标的指针(比如，指向“类型”【Class对象】、类变量、类方法的直接引用可能是指向 方法区的指针)

2. 相对偏移量(比如，指向实例变量、实例方法的直接引用都是偏移量) 3. 一个能间接定位到目标的句柄

> **直接引用是和虚拟机的布局相关的，同一个符号引用在不同的虚拟机实例上翻译出来的直接引用 一般不会相同。如果有了直接引用，那引用的目标必定已经被加载入内存中了。**

#### 引用替换的时机

<font color="red">符号引用替换为直接引用的操作发生在类加载过程(加载 -> 连接(验证、准备、解析) -> 初始化)中的<font color="blue"><u>解析阶段</u></font>，会将符号引用转换(替换)为对应的直接引用，放入运行时常量池中。</font>

后面我们会通过一些问题，来更深入的了解class常量池相关的知识点!!!

#### 特殊字符串

特殊字符串包括三种: <font color="blue"><u>**类的全限定名， 字段和方法的描述符， 特殊方法的方法名**</u></font>。 下面我们就分别介 绍这三种特殊字符串。

##### **类的全限定名**

Object类，在源文件中的全限定名是 `java.lang.Object 。` 

而class文件中的全限定名是将点号替换成“/” 。 也就是 `java/lang/Object` 。 

<font color="blue"><u>源文件中一个类的名字， 在class文件中是用全限定名表述的。</u></font>

##### **描述符**

###### **各类型的描述符**

对于字段的数据类型，其描述符主要有以下几种

- **基本数据类型**(byte、char、double、float、int、long、short、boolean):除 long 和 boolean，其他基本数据类型的描述符用对应单词的大写首字母表示。**long** **用** **J** **表示，****boolean** **用** **Z** **表示**。
- **void**:描述符是 V。
- **对象类型**:描述符用字符 L 加上对象的全限定名表示，如 String 类型的描述符为`Ljava/lang/String` 。
-  **数组类型**:每增加一个维度则在对应的字段描述符前增加一个 `[` ，如一维数组 `int[]` 的描述符 为 `[I` ，二维数组 `String[][]` 的描述符为 `[[Ljava/lang/String` 。

| 数据类型     | 描述符                                                       |
| ------------ | ------------------------------------------------------------ |
| byte         | B                                                            |
| char         | C                                                            |
| dubbo        | D                                                            |
| float        | F                                                            |
| int          | I                                                            |
| long         | J                                                            |
| short        | S                                                            |
| boolean      | Z                                                            |
| 特殊类型void | V                                                            |
| 对象类型     | `“L”` + 类型的全限定名 + “;”。如 `Ljava/lang/String`; 表示 String 类型 |
| 数组类型     | 若干个 `“[”` + 数组中元素类型的对应字符串，如一维数组 `int[]` 的描述符为 `[I` ， 型 二维数组 `String[][]` 的描述符为 `[[java/lang/String`; |

###### 字段描述符

字段的描述符就是字段的类型所对应的字符或字符串。 如:

```
int i 中， 字段i的描述符就是 I
Object o中， 字段o的描述符就是 Ljava/lang/Object; double[][] d中， 
字段d的描述符就是 [[D
```

###### 方法描述符

方法的描述符比较复杂， 包括所有参数的类型列表和方法返回值。 它的格式是这样的:

```
(参数1类型 参数2类型 参数3类型 ...)返回值类型
```

##### 注意事项:

> 不管是参数的类型还是返回值类型， 都是使用对应字符和对应字符串来表示的， 并且参数列表使 用小括号括起来， 并且**各个参数类型之间没有空格， 参数列表和返回值类型之间也没有空格**。

##### 方法描述符举例说明如下:

| 方法描述符                | 方法声明                                                     |
| ------------------------- | ------------------------------------------------------------ |
| ()I                       | int getSize()                                                |
| ()Ljava/lang/String;      | String toString()                                            |
| ([Ljava/lang/String;)V    | void main(String[] args)                                     |
| ()V                       | void wait()                                                  |
| (JI)V                     | void wait(long timeout, int nanos)                           |
| (ZILjava/lang/String;II)Z | boolean regionMatches(boolean ignoreCase, int toOffset, String other, int ooffset, int len) |
| ([BII)I                   | int read(byte[] b, int off, int len )                        |
| ()[[Ljava/lang/Object;    | Object[][] getObjectArray()                                  |

##### 特殊方法的方法名

首先要明确一下， 这里的特殊方法是指的<font color="blue"><u>类的构造方法</u></font>和<font color="blue"><u>类型初始化方法</u></font>。

构造方法就不用多说了， 至于**类型的初始化方法， 对应到源码中就是静态初始化块**。 也就是说， 静态

初始化块， 在class文件中是以一个方法表述的， 这个方法同样有方法描述符和方法名，具体如下:

- 类的构造方法的方法名使用字符串 表示
-  静态初始化方法的方法名使用字符串 表示。
-  除了这两种特殊的方法外， 其他普通方法的方法名， 和源文件中的方法名相同。

#### 总结:

1. **方法和字段的描述符中， 不包括字段名和方法名**， 字段描述符中只包括字段类型， 方法描述符中 只包括参数列表和返回值类型。

2. **无论method()是静态方法还是实例方法，它的方法描述符都是相同的。**尽管实例方法除了传递自 身定义的参数，还需要额外传递参数this，但是这一点不是由方法描述符来表达的。参数this的传 递，是由Java虚拟机实现在调用实例方法所使用的指令中实现的隐式传递。

### 通过javap命令分析java指令

#### javap命令简述

javap是jdk自带的反解析工具。它的作用就是根据class字节码文件，反解析出当前类对应的code区 (汇编指令)、本地变量表、异常表和代码行偏移量映射表、常量池等等信息。 

当然这些信息中，有些信息(如本地变量表、指令和代码行偏移量映射表、常量池中方法的参数名称等 等)需要在使用javac编译成class文件时，指定参数才能输出，比如，你直接javac xx.java，就不会在 生成对应的局部变量表等信息，如果你使用javac -g xx.java就可以生成所有相关信息了。如果你使用的 eclipse，则默认情况下，eclipse在编译时会帮你生成局部变量表、指令和代码行偏移量映射表等信息 的。 

通过反编译生成的汇编代码，我们可以深入的了解java代码的工作机制。比如我们可以查看i++;这行代 码实际运行时是先获取变量i的值，然后将这个值加1，最后再将加1后的值赋值给变量i。

通过局部变量表，我们可以查看局部变量的作用域范围、所在槽位等信息，甚至可以看到槽位复用等信 息。

javap的用法格式:

```
javap <options> <classes>
```

其中classes就是你要反编译的class文件。
在命令行中直接输入javap或javap -help可以看到javap的options有如下选项:

```
用法: javap <options> <classes>
其中, 可能的选项包括:
  -help  --help  -?        输出此用法消息
  -version                 版本信息
  -v  -verbose             输出附加信息
  -l                       输出行号和本地变量表
  -public                  仅显示公共类和成员
  -protected               显示受保护的/公共类和成员
  -package                 显示程序包/受保护的/公共类
                           和成员 (默认)
  -p  -private             显示所有类和成员
  -c                       对代码进行反汇编
  -s                       输出内部类型签名
  -sysinfo                 显示正在处理的类的
                           系统信息 (路径, 大小, 日期, MD5 散列)
  -constants               显示最终常量
  -classpath <path>        指定查找用户类文件的位置
  -cp <path>               指定查找用户类文件的位置
  -bootclasspath <path>    覆盖引导类文件的位置
```

一般常用-v -l -c三个选项

- **javap -v classxx**，不仅会输出行号、本地变量表信息、反编译汇编代码，还会输出当前类用到的常量池等信息。
- **javap -l** 会输出行号和本地变量表信息。
-  **javap -c** 会对当前class字节码进行反编译生成汇编代码。

查看汇编代码时，需要知道里面的jvm指令，可以参考官方文档: 

https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-6.html 

另外通过jclasslib工具也可以看到上面这些信息，而且是可视化的，效果更好一些。

#### javap测试及内容详解

前面已经介绍过javap输出的内容有哪些，东西比较多，这里主要介绍其中code区(汇编指令)、局部变 量表和代码行偏移映射三个部分。
 如果需要分析更多的信息，可以使用**javap -v**进行查看。 另外，为了更方便理解，所有汇编指令不单拎出来讲解，而是在反汇编代码中以注释的方式讲解。

下面写段代码测试一下:

**例子1:**分析一下下面的代码反汇编之后结果:

```java
public class TestDate {
    private int count = 0;
    public static void main(String[] args) { 
      TestDate testDate = new TestDate(); 				
      testDate.test1();
		}
		public void test1(){
			Date date = new Date();
			String name1 = "wangerbei"; 
      test2(date,name1); 
      System.out.println(date+name1);
		}
    public void test2(Date dateP,String name2){
        dateP = null;
        name2 = "zhangsan";
    }
    public void test3(){
        count++;
		}
  
    public void  test4(){
        int a = 0;
        {
          int b = 0;
          b = a+1; 
        }
				int c = a+1; 
    }
}
```

上面代码通过JAVAC -g 生成class文件，然后通过javap命令对字节码进行反汇编:

```
javap -c -l TestDate
```

得到下面内容(指令等部分是我参照官方文档总结的):

```java
Compiled from "TestDate.java"
public class com.lee.source.TestDate {
  //默认的构造方法，在构造方法执行时主要完成一些初始化操作，包括一些成员变量的初始化赋值 等操作
  public com.lee.source.TestDate();
    Code:
       0: aload_0 //从本地变量表中加载索引为0的变量的值，也即this的引用，压入栈
       1: invokespecial #1   //出栈，调用java/lang/Object."<init>":()V 初始化对象，就是this指定的对象的<init>方法完成初始化 // Method java/lang/Object."<init>":()V 
       4: aload_0	// 4到6表示，调用this.count = 0，也即为count复制为0。这里this 引用入栈
       5: iconst_0	//将常量0，压入到操作数栈
       6: putfield      #2  //出栈前面压入的两个值(this引用，常量值0)， 将0取出，并赋 值给count // Field count:I
       9: return
    //指令与代码行数的偏移对应关系，每一行第一个数字对应代码行数，第二个数字对应前面code中指 令前面的数字
    LineNumberTable:
      line 5: 0
      line 6: 4
    //局部变量表，start+length表示这个变量在字节码中的生命周期起始和结束的偏移位置 (this生命周期从头0到结尾10)，slot就是这个变量在局部变量表中的槽位(槽位可复用)， name就是变量名称，Signatur局部变量类型描述
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0      10     0  this   Lcom/lee/source/TestDate;

  public static void main(java.lang.String[]);
    Code:
  // new指令，创建一个class com/justest/test/TestDate对象，new指令并不能完全创建一 个对象，对象只有在初，只有在调用初始化方法完成后(也就是调用了invokespecial指令之后)， 对象才创建成功,
       0: new           #3    //创建对象，并将对象引用压入栈 // class com/lee/source/TestDate
       3: dup		//将操作数栈定的数据复制一份，并压入栈，此时栈中有两个引用值
       4: invokespecial #4 //pop出栈引用值,调用其构造函数，完成对象的初始化 // Method "<init>":()V
       7: astore_1	//pop出栈引用值，将其(引用)赋值给局部变量表中的变量testDate
       8: aload_1	//将testDate的引用值压入栈，因为testDate.test1();调用了testDate，这里使用aload_1从局部变量表中获得对应的变量testDate的值并压入操作数栈
       9: invokevirtual #5                  // Method test1:()V 引用出栈，调用testDate的test1()方法
      12: return	//整个main方法结束返回
    LineNumberTable:
      line 8: 0
      line 9: 8
      line 10: 12
    //局部变量表，testDate只有在创建完成并赋值后，才开始声明周期
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0      13     0  args   [Ljava/lang/String;
          8       5     1 testDate   Lcom/lee/source/TestDate;

  public void test1();
    Code:
       0: new           #6   // 0到7创建Date对象，并赋值给date变量
       3: dup					
       4: invokespecial #7                 // Method java/util/Date."<init>":()V
       7: astore_1
       8: ldc           #8                  // String wangerbei 将常量"wangerbei"压入栈
      10: astore_2 	//将栈中的“wangerbei”pop出，赋值给name1
      11: aload_0	//11到14，对应test2(date,name1);默认前面加this.
      12: aload_1		//从局部变量表中取出date变量
      13: aload_2		//取出name1变量
      14: invokevirtual #9                  // Method test2:(Ljava/util/Date;Ljava/lang/String;)V	调用test2方法
      17: getstatic     #10                 // Field java/lang/System.out:Ljava/io/PrintStream;
      //20到35是jvm中的优化手段，多个字符串变量相加，不会两两创建一个字符串对象，而使用 StringBuilder来创建一个对象
      20: new           #11                 // class java/lang/StringBuilder
      23: dup
      24: invokespecial #12                 // Method java/lang/StringBuilder."<init>":()V
      27: aload_1
      28: invokevirtual #13                 // Method java/lang/StringBuilder.append:(Ljava/lang/Object;)Ljava/lang/StringBuilder;
      31: aload_2
      32: invokevirtual #14                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      35: invokevirtual #15                 // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      38: invokevirtual #16                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V invokevirtual指令表示基于 类调用方法
      41: return
    LineNumberTable:
      line 12: 0
      line 13: 8
      line 14: 11
      line 15: 17
      line 16: 41
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0      42     0  this   Lcom/lee/source/TestDate;
          8      34     1  date   Ljava/util/Date;
         11      31     2 name1   Ljava/lang/String;

  public void test2(java.util.Date, java.lang.String);
    Code:
       0: aconst_null	//将一个null值压入栈
       1: astore_1	//将null赋值给dateP
       2: ldc           #17         // String zhangsan 从常量池中取出字符串“zhangsan”压入栈中
       4: astore_2	//将字符串赋值给name2
       5: return
    LineNumberTable:
      line 18: 0
      line 19: 2
      line 20: 5
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       6     0  this   Lcom/lee/source/TestDate;
          0       6     1 dateP   Ljava/util/Date;
          0       6     2 name2   Ljava/lang/String;

  public void test3();
    Code:
       0: aload_0	//取出this，压入栈
       1: dup	//复制操作数栈栈顶的值，并压入栈，此时有两个this对象引用值在操作数组栈
       2: getfield      #2  // Field count:I	this出栈，并获取其count字段，然后压入栈， 此时栈中有一个this和一个count的值
       5: iconst_1	//取出一个int常量1,压入操作数栈
       6: iadd	// 从栈中取出count和1，将count值和1相加，结果入栈
       7: putfield      #2    // Field count:I 一次弹出两个，第一个弹出的是上一步计算值，第二个弹出的this，将值赋值给this的count字段
      10: return
    LineNumberTable:
      line 22: 0
      line 23: 10
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0      11     0  this   Lcom/lee/source/TestDate;

  public void test4();
    Code:
       0: iconst_0
       1: istore_1
       2: iconst_0
       3: istore_2
       4: iload_1
       5: iconst_1
       6: iadd
       7: istore_2
       8: iload_1
       9: iconst_1
      10: iadd
      11: istore_2
      12: return
    LineNumberTable:
      line 26: 0
      line 28: 2
      line 29: 4
      line 31: 8
      line 32: 12
		//看下面，b和c的槽位slot一样，这是因为b的作用域就在方法块中，方法块结束，局部变量表 中的槽位就被释放，后面的变量就可以复用这个槽位
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          4       4     2     b   I
          0      13     0  this   Lcom/lee/source/TestDate;
          2      11     1     a   I
         12       1     2     c   I
}
```

**例子2:**下面一个例子

先有一个User类:

```java
package com.lee.source;

public class User {
    private String name;
    private int age;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
}
```

然后写一个操作User对象的测试类:

```java
package com.lee.source;

public class TestUser {
    private int count;
    public void test(int a){
        count = count + a;
    }
    public User initUser(int age,String name){
        User user = new User();
        user.setAge(age);
        user.setName(name);
        return user;
    }
    public void changeUser(User user,String newName){
        user.setName(newName);
    }
}
```

先**javac -g** 编译成class文件。

 然后对TestUser类进行反汇编:

`$ javap -c -l TestUser` 

得到反汇编结果如下:

```java
Compiled from "TestUser.java"
public class com.lee.source.TestUser {
  // 默认的构造函数
  public com.lee.source.TestUser();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return
    LineNumberTable:
      line 3: 0
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       5     0  this   Lcom/lee/source/TestUser;

  public void test(int);
    Code:
       0: aload_0	//取this对应的对应引用值，压入操作数栈
       1: aload_0
       2: getfield      #2  // Field count:I // 引用出栈，通过引用获得对应count的值，并压入栈
       5: iload_1 //从局部变量表中取得a的值，压入栈中
       6: iadd	//弹出栈中的count值和a的值，进行加操作，并将结果压入栈
       7: putfield      #2 // Field count:I	// 经过上一步操作后，栈中有两个值，栈顶为上一步操作结 果，栈顶下面是this引用，这一步putfield指令，用于将栈顶的值赋值给引用对象的count字段
      10: return
    LineNumberTable:
      line 6: 0
      line 7: 10
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0      11     0  this   Lcom/lee/source/TestUser;
          0      11     1     a   I

  public com.lee.source.User initUser(int, java.lang.String);
    Code:
       0: new           #3 // class com/lee/source/User // class com/justest/test/User 创建User对象，并将引用压入栈
       3: dup	//复制栈顶值，再次压入栈，栈中有两个User对象的地址引用
       4: invokespecial #4  // Method com/lee/source/User."<init>":()V 调用user对象初始化
       7: astore_3 //从栈中pop出User对象的引用值，并赋值给局部变量表中user变量
       8: aload_3 //从局部变量表中获得user的值，也就是User对象的地址引用，压入栈中
       9: iload_1 //从局部变量表中获得a的值，并压入栈中，注意aload和iload的区别，一个取值是对象引用，一个是取int类型数据
      10: invokevirtual #5 // Method com/lee/source/User.setAge:(I)V 操作数栈pop出两个值，一个是User对象引用，一个是a的值，调用setAge方法，并将a的值传给这 个方法,setAge操作的就是堆中对象的字段了
      13: aload_3	// 同7, 压入栈
      14: aload_2 //从局部变量表取出name，压入栈
      15: invokevirtual #6                  // Method com/lee/source/User.setName:(Ljava/lang/String;)V 操作数栈pop出两个值，一个是User对象引用，一个是name的值，调用setName方法，并将a的值传 给这个方法，setName操作的就是堆中对象的字段了
      18: aload_3  //从局部变量取出User引用，压入栈
      19: areturn //areturn指令用于返回一个对象的引用，也就是上一步中User的引用,这 个返回值将会被压入调用当前方法的那个方法的栈中objectref is popped from the operand stack of the current frame ([§2.6] (https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.6)) and pushed onto the operand stack of the frame of the invoker
    LineNumberTable:
      line 9: 0
      line 10: 8
      line 11: 13
      line 12: 18
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0      20     0  this   Lcom/lee/source/TestUser;
          0      20     1   age   I
          0      20     2  name   Ljava/lang/String;
          8      12     3  user   Lcom/lee/source/User;

  public void changeUser(com.lee.source.User, java.lang.String);
    Code:
       0: aload_1
       1: aload_2
       2: invokevirtual #6                  // Method com/lee/source/User.setName:(Ljava/lang/String;)V pop出栈newName值和TestUser引用，调用其setName方法，并将newName的值传给这个方法
       5: return
    LineNumberTable:
      line 15: 0
      line 16: 5
    LocalVariableTable:
      Start  Length  Slot  Name   Signature
          0       6     0  this   Lcom/lee/source/TestUser;
          0       6     1  user   Lcom/lee/source/User;
          0       6     2 newName   Ljava/lang/String;
}
```

#### 总结

1、通过javap命令可以查看一个java类反汇编、常量池、变量表、指令代码行号表等等信息。

2、平常，我们比较关注的是java类中每个方法的反汇编中的指令操作过程，这些指令都是顺序执行

的，可以参考官方文档查看每个指令的含义，很简单:

https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-6.html#jvms-6.5.areturn

3、通过对前面两个例子代码反汇编中各个指令操作的分析，可以发现，一个方法的执行通常会涉及下 面几块内存的操作:

(1)**java栈**:局部变量表、操作数栈。这些操作基本上都值操作。

(2)**java堆**:通过对象的地址引用去操作。

(3)**常量池**。

(4)其他如**帧数据区、方法区**(jdk1.8之前，常量池也在方法区)等部分，测试中没有显示出来，这 里说明一下。

**在做值相关操作时:**

一个指令，可以从局部变量表、常量池、堆中对象、方法调用、系统调用中等取得数据，这些数据(可 能是指，可能是对象的引用)被压入操作数栈。

一个指令，也可以从操作数数栈中取出一到多个值(pop多次)，完成赋值、加减乘除、方法传参、系 统调用等等操作。

### 案例分析

#### class文件解读

![image-20191213213926935](/Users/lee/Library/Application Support/typora-user-images/image-20191213213926935.png)

#### javap显示结果解读

![image-20191213213949599](/Users/lee/Library/Application Support/typora-user-images/image-20191213213949599.png)

## 看清楚类加载子系统

### 类加载的过程

![image-20191213214024492](/Users/lee/Library/Application Support/typora-user-images/image-20191213214024492.png)

#### 加载

“加载”是“类加载”(Class Loading)过程的第一步。这个加载过程主要就是靠**类加载器**实现的，包括用户

自定义类加载器。

![image-20191213214047422](/Users/lee/Library/Application Support/typora-user-images/image-20191213214047422.png)

##### 加载的过程

在加载的过程中,JVM主要做3件事情

- **通过一个类的全限定名来获取定义此类的二进制字节流(class文件)** 
- 在程序运行过程中,当要访问一个类时,若发现这个类尚未被加载,并满足类初始化的条件时，就根据 要被初始化的这个类的全限定名找到该类的二进制字节流,开始加载过程 
- **将这个字节流的静态存储结构转化为方法区的运行时数据结构 **
- **在内存中创建一个该类的java.lang.Class对象**,作为方法区该类的各种数据的访问入口

```
程序在运行中所有对该类的访问都通过这个类对象,也就是这个Class对象是提供给外界访问该类的接口。
```

##### 加载源

JVM规范对于加载过程给予了较大的宽松度.一般二进制字节流都从已经编译好的本地class文件中读取, 此外还可以从以下地方读取。

- **zip包**
  Jar、War、Ear等

- **其它文件生成**
  由JSP文件中生成对应的Class类.

- **数据库中**

   将二进制字节流存储至数据库中,然后在加载时从数据库中读取.有些中间件会这么做,用来实现代码 在集群间分发

- 
   **网络** 

  从网络中获取二进制字节流.典型就是Applet.

-  **运行时计算生成** 

  动态代理技术,用ProxyGenerator.generateProxyClass为特定接口生成形式为"*$Proxy"的代理类 的二进制字节流.

##### 类和数组加载的区别

数组也有类型,称为“数组类型”.如:

```java
String[]str=newString[10];
```

这个数组的数组类型是 `[Ljava.lang.String ,而String`只是这个数组的元素类型。

 数组类和非数组类的类加载是不同的，具体情况如下:

- **非数组类:**是由类加载器来完成。
- **数组类:**数组类本身不通过类加载器创建，它是由*java*虚拟机直接创建，但数组类与类加载器有很密切的关系，因为数组类的元素类型最终要靠类加载器创建。

##### 加载过程的注意点

- **JVM规范并未给出类在方法区中存放的数据结构**

  类完成加载后,二进制字节流就以特定的数据结构存储在方法区中,但存储的数据结构是由虚拟机自己定义的,虚拟机规范并没有指定。

- **JVM规范并没有指定Class对象存放的位置** 

  在二进制字节流以特定格式存储在方法区后,JVM会创建一个java.lang.Class类的对象,作为本类的外部访问接口。

  既然是对象就应该存放在Java堆中,不过JVM规范并没有给出限制,不同的虚拟机根据自己的需求存 放这个对象。HotSpot将Class对象存放在方法区。 

- **加载阶段和链接阶段是交叉的**

  类加载的过程中每个步骤的开始顺序都有严格限制,但每个步骤的结束顺序没有限制。也就是说,**类加载过程中,必须按照如下顺序开始:**

  ```
  加载 -> 链接 -> 初始化
  ```

  **但结束顺序无所谓**,因此由于每个步骤处理时间的长短不一就会导致有些步骤会出现交叉。

#### 验证

验证阶段比较耗时,它非常重要但不一定必要(因为对程序运行期没有影响),如果所运行的代码已经被反复 使用和验证过,那么可以使用 `-Xverify:none` 参数关闭,以缩短类加载时间。

##### 验证的目的

保证二进制字节流中的信息符合虚拟机规范,并没有安全问题

##### 验证的必要性

虽然Java语言是一门安全的语言,它能确保程序猿无法访问数组边界以外的内存、避免让一个对象转换成 任意类型、避免跳转到不存在的代码行.也就是说,<u>Java语言的安全性是通过编译器来保证的.</u>

但是我们知道,<u>编译器和虚拟机是两个独立的东西,虚拟机只认二进制字节流,它不会管所获得的二进制字节流是哪来的，</u>当然，如果是编译器给它的，那么就相对安全，但如果是从其它途径获得的，那么无法确保该二进制字节流是安全的。

通过上文可知，虚拟机规范中没有限制二进制字节流的来源，在字节码层面上,上述Java代码无法做到的都是可以实现的,至少语义上是可以表达出来的,为了防止字节流中有安全问题，需要验证!

##### 验证的过程

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191213215001759.png" alt="image-20191213215001759" style="zoom:50%;" />

- **文件格式验证**

验证字节流是否符合Class文件格式的规范,并且能被当前的虚拟机处理. 本验证阶段是**基于二进制字节流**进行的,只有通过本阶段验证,才被允许存到方法区 后面的三个验证阶段都是基于方法区的存储结构进行,不会再直接操作字节流。

印证【加载和验证】是交叉进行的:

```
1.加载开始前，二进制字节流还没进方法区，而加载完成后，二进制字节流已经存入方法区 
2.而在文件格式验证前，二进制字节流尚未进入方法区，文件格式验证通过之后才进入方法区

也就是说，加载开始后，立即启动了文件格式验证，本阶段验证通过后，二进制字节流被转换成特 定数据结构存储至方法区中，继而开始下阶段的验证和创建Class对象等操作
```

- **元数据验证**

  对字节码描述信息进行语义分析,确保符合Java语法规范.

- **字节码验证**

  本阶段是验证过程的最复杂的一个阶段。

  本阶段对方法体进行语义分析,保证方法在运行时不会出现危害虚拟机的事件。

  字节码验证将对类的方法进行校验分析，保证被校验的方法在运行时不会做出危害虚拟机的事，一 个类方法体的字节码没有通过字节码验证，那一定有问题，但若一个方法通过了验证，也不能说明 它一定安全。

- **符号引用验证**

  <u>发生在JVM将符号引用转化为直接引用的时候</u>,这个转化动作发生在解析阶段,对类自身以外的信息 进行匹配校验,确保解析能正常执行。

#### 准备

仅仅<u>为类变量(即static修饰的字段变量)分配内存</u>并且设置该类变量的初始值即零值，这里不包含用 final修饰的static，因为final在编译的时候就会分配了(编译器的优化)，同时这里也不会为实例变量 分配初始化。**类变量会分配在方法区中，而实例变量是会随着对象一起分配到Java堆中**。



准备阶段主要完成两件事情:

- **为已在方法区中的类的静态成员变量分配内存**
- **为静态成员变量设置初始值，初始值为0, false, null等**

| 数据类型  | 默认零值 |
| --------- | -------- |
| int       | 0        |
| long      | 0L       |
| short     | (shotr)0 |
| char      | '\u000'  |
| byte      | (byte)0  |
| boolean   | false    |
| float     | 0.0f     |
| double    | 0.0d     |
| reference | null     |

比如:

```java
public static int x = 1000;
```

注意:

```
实际上变量x在准备阶段过后的初始值为0，而不是1000
将x赋值为1000的putstatic指令是程序被编译后，存放于类构造器<clinit>方法之中
```

但是如果声明为:

```java
public static final int x = 1000;
```

```
在编译阶段会为x生成ConstantValue属性，在准备阶段虚拟机会根据ConstantValue属性将x赋值为 1000。
```

#### 解析

<u>解析是虚拟机将常量池的**符号引用替换为直接引用的过程**。</u>

解析动作主要针对类或接口、字段、类方法、接口方法四类符号引用进行，分别对应于常量池中的

<u>`CONSTANT_Class_info` 、 `CONSTANT_Fieldref_info` 、 `CONSTANT_Methodref_info` 、 `CONSTANT_InterfaceMethodref_info`</u>四种常量类型。

1. **类或接口的解析:** 

   判断所要转化成的直接引用是对数组类型，还是普通的对象类型的引用，从而进行不同的解析。

2. **字段解析:**

   对字段进行解析时，会先在本类中查找是否包含有简单名称和字段描述符都与目标相匹配的字段， 如果有，则查找结束;如果没有，则会按照继承关系从上往下递归搜索该类所实现的各个接口和它 们的父接口，还没有，则按照继承关系从上往下递归搜索其父类，直至查找结束(优先从接口来， 然后是继承的父类.理论上是按照上述顺序进行搜索解析，但在实际应用中，虚拟机的编译器实现 可能要比上述规范要求的更严格一些。如果有一个同名字段同时出现在该类的接口和父类中，或同 时在自己或父类的接口中出现，编译器可能会拒绝编译).

3. **类方法解析:** 

   对类方法的解析与对字段解析的搜索步骤差不多，只是多了判断该方法所处的是类还是接口的步骤，而且对类方法的匹配搜索，是先搜索父类，再搜索接口。

4. **接口方法解析:** 

   与类方法解析步骤类似，只是接口不会有父类，因此，只递归向上搜索父接口就行了。

#### 初始化

初始化是类加载过程的最后一步，到了此阶段，才真正开始执行类中定义的Java程序代码(初始化成为代码设定的默认值)。在准备阶段，类变量已经被赋过一次系统要求的初始值，而在初始化阶段，则是根据程序员通过程序指定的主观计划去初始化类变量和其他资源，或者可以从另一个角度来表达:初始化阶段是执行类构造器()方法的过程。

<u>其实初始化过程就是调用类初始化方法的过程，完成对static修饰的类变量的手动赋值还有主动调用静态代码块。</u>

##### 初始化过程的注意点

- 方法是编译器自动收集**类中所有类变量的赋值动作和静态语句块中的语句合并产生**的，编译器收集的顺序是由语句在源文件中出现的顺序所决定的. 

- 静态代码块只能访问到出现在静态代码块之前的变量,定义在它之后的变量,在前面的静态语句块可以赋值,但是不能访问.

```java
public class Test {
    static {
      i=0;
      System.out.println(i);//编译失败:"非法向前引用" 
    }
    static int i = 1;
}
```

- 实例构造器需要显式调用父类构造函数,而类的不需要调用父类的类构造函数,虚拟机会确 保子类的方法执行前已经执行完毕父类的方法.因此在JVM中第一个被执行的方法的类肯定是java.lang.Object.
-  如果一个类/接口中没有静态代码块,也没有静态成员变量的赋值操作,那么编译器就不会为此类生成方法.

- 接口也需要通过方法为接口中定义的静态成员变量显示初始化。接口中不能使用静态代码块,但仍然有变量初始化的赋值操作,因此接口与类一样都会生成方法.不同 的是,执行接口的方法不需要先执行父接口的方法.只有当父接口中的静态成员变量被使用到时才会 执行父接口的方法.

- <u>虚拟机会保证在多线程环境中一个类的方法别正确地加锁</u>,同步.当多条线程同时去初始化一个类 时，只会有一个线程去执行该类的方法,其它线程都被阻塞等待,直到活动线程执行方法完毕.其他线 程虽会被阻塞,只要有一个方法执行完,其它线程唤醒后不会再进入方法.同一个类加载器下,一个类型 只会初始化一次.

##### 使用静态内部类的单例实现

```java
public class Student {
    private Student() {}
    /*
    * 此处使用一个内部类来维护单例 JVM在类加载的时候，是互斥的，所以可以由此保证线
    程安全问题
    */
    private static class SingletonFactory {
        private static Student student = new Student();
		}
		/* 获取实例 */
		public static Student getSingletonInstance() {
			return SingletonFactory.student; 
    }
}
```

### 类加载的时机

什么时候开始加载，虚拟机规范并没有强制性的约束，对于其它大部分阶段究竟何时开始虚拟机规范也都没有进行规范，这些都是交由虚拟机的具体实现来把握。所以不同的虚拟机它们开始的时机可能是不 同的。但是对于初始化却严格的规定了有且只有四种情况必须先对类进行“初始化”(加载，验证，准备自然需要在初始化之前完成):

1. 遇到 new 、getstatic 、putstatic 和invokestatic 这四条指令时，如果对应的类没有初始 化，则要对对应的类先进行初始化。

这四个指令对应到我们java代码中的场景分别是:

- new关键字实例化对象的时候; 
- 读取或设置一个类的静态字段(读取被final修饰，已在编译器把结果放入常量池的静态字段除外) ;

- 调用类的静态方法时。

2. 使用 java.lang.reflect 包方法时对类进行反射调用的时候。

3. 初始化一个类的时候发现其父类还没初始化，要先初始化其父类。

4. 当虚拟机开始启动时，用户需要指定一个主类，虚拟机会先执行这个主类的初始化。

### 类加载器

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191213220409525.png" alt="image-20191213220409525" style="zoom:50%;" />

- **启动类加载器(Bootstrap ClassLoader):**
  - 负责加载JAVA_HOME\lib目录中的
  - 或通过-Xbootclasspath参数指定路径中的
  - 且被虚拟机认可(按文件名识别,如rt.jar)的类
  - 由c++实现,不是ClassLoader子类

- **扩展类加载器(Extension ClassLoader):**
  - 负责加载JAVA_HOME\lib\ext目录中的
  - 或通过java.ext.dirs系统变量指定路径中的类库
- **应用程序类加载器(Application ClassLoader):**
  - 负责加载用户路径(classpath)上的类库

**JVM的类加载是通过ClassLoader及其子类来完成的，类的层次关系和加载顺序可以由下图来描述:**

![image-20191213220855321](/Users/lee/Library/Application Support/typora-user-images/image-20191213220855321.png)

加载过程中会先检查类是否被已加载，检查顺序是自底向上，从Custom ClassLoader到BootStrap ClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类只所有ClassLoader 加载一次。而加载的顺序是自顶向下，也就是由上层来逐层尝试加载此类。

#### 自定义类加载器

**自定义类加载器步骤**

(1) 继承ClassLoader

(2) 重新findClass()方法

(3) 调用defineClass()方法

**实践**

下面写一个自定义类加载器:指定类加载路径在D盘下的lib文件夹下

(1)在本地磁盘新建一个 `Test.java` 类，代码如下:

```java
package jvm.classloader;
public class Test {
	public void say(){
		System.out.println("Hello MyClassLoader");
	} 
}
```

(2)使用 `javac -d . Test.java` 命令，将生成的 `Test.class` 文件放到 `D:/lib/jvm/classloader` 文件夹下。

(3)在Eclipse中自定义类加载器，代码如下:



### **双亲委派模型**

JVM通过**双亲委派模型**进行类的加载，当然我们也可以通过继承java.lang.ClassLoader实现自定义的类 加载器。

- [当一个类加载器收到类加载任务，会先交给其父类加载器去完成]()，因此最终加载任务都会传递到顶 层的启动类加载器，
- [只有当父类加载器无法完成加载任务时，才会尝试执行加载任务]()。

采用双亲委派的一个好处是:

比如加载位于rt.jar包中的类java.lang.Object，不管是哪个加载器加载这个类，最终都是委托给顶 层的启动类加载器进行加载，这样就保证了使用不同的类加载器最终得到的都是同样一个Object 对象。

#### 为什么要使用双亲委派这种模型呢?

<font color="red">因为这样可以避免重复假造, 当前父亲已经加载了该类的时候, 就没必要子ClassLoader再加载一次.</font>

考虑到安全因素,我们试想一下,如果不适用这种委托模式,那我们就可以随时使用自定义的String来动态替代java核心api中定义的类型,这样会存在非常大的安全隐患,而双亲委派的方式,就可以避免这种情况,**因为String已经在启动时就被引导类加载器(Bootstrap ClassLoader)加载,所以用户自定义的ClassLoader永源也无法加载一个自己写的String,除非你改变JKD中ClassLoader搜索类的默认算法.**

#### 但是JVM在搜索类的时候,又是如何判定两个Class是相同的呢?

<font color="red">JVM在判定两个class是否相同时，不仅要判断两个类名是否相同，而且要判断是否由同一个类加载器实例加载的。</font>

只有两者同时满足的情况下，JVM才认为这两个class是相同的。就算两个class是同一份class字节码， 如果被两个不同的ClassLoader实例所加载，JVM也会认为它们是两个不同class。

#### 既然JVM已经提供了默认的类加载器,为什么还要定义自己的类加载器呢?

<font color="red">因为Java中提供的默认ClassLoader，只加载指定目录下的jar和class，如果我们想加载其它位置的类或jar时。</font>

比如:我要加载网络上的一个class文件，通过动态加载到内存之后，要调用这个类中的方法实现我的业务逻辑。在这样的情况下，默认的ClassLoader就不能满足我们的需求了，所以需要定义自己的 ClassLoader

### 破坏双亲委派模型

#### 为什么要破坏双亲委派?

<font color="red">因为在某些情况下父类加载器需要委托子类加载器去加载class文件(双亲委派模式的话，是子类委托父 类加载器去加载class文件)。</font>因为受到加载范围的限制,父类加载器无法加载到需要的文件.

以Driver接口为例,由于Driver接口定义在jdk当中的,而其实现由各个数据库的服务商来提供,比如mysql就写了`mysql connector`,那么问题就来了, DriverManager(也有jdk提供)要加载各个实现Driver接口的实现类, 然后进行管理,但是[DriverManager由启动类加载器加载](),只能记在JAVA_HOME的lib下文件,而其实现是有服务商提供的,有系统类加载器加载,这个时候就需要启动类来[委托子类来加载Driver实现](),从而破坏了双亲委派,这里仅仅是聚了破坏双亲委派的其中一个情况

#### JDBC代码:

```java
import org.junit.jupiter.api.Test;

import java.sql.Connection;
import java.sql.DriverManager; import java.sql.PreparedStatement; import java.sql.ResultSet;
import java.sql.SQLException;
public class TestJdbc {
    @Test
    public void testJdbc() {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet rs = null;
        try {
            // 加载数据库驱动
            Class.forName("com.mysql.jdbc.Driver");
            // 通过驱动管理类获取数据库链接connection = DriverManager
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/ssm? characterEncoding=utf-8", "root", "root");
            // 定义sql语句 ?表示占位符
            String sql = "select * from user where id = ?"; // 获取预处理 statement
            preparedStatement = connection.prepareStatement(sql);
            // 设置参数，第一个参数为 sql 语句中参数的序号(从 1 开始)，第二个参数为设置的
            preparedStatement.setInt(1, 1);
            // 向数据库发出 sql 执行查询，查询出结果集 rs = preparedStatement.executeQuery();
            // 遍历查询结果集
        } catch (Exception e) {
            e.printStackTrace(); } finally {
            // 释放资源
            if (rs != null) {
                try { rs.close();
                } catch (SQLException e) { e.printStackTrace();
                } }
            if (preparedStatement != null) {
                try { preparedStatement.close();
                } catch (SQLException e) { e.printStackTrace();
                } }
            if (connection != null) {
                try {
                    connection.close();
                } catch (SQLException e) { }
            }
        }
    }
}
```

## 认识运行时数据区

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191216143218408.png" alt="image-20191216143218408"/>



### 概述

### 程序计数器

<font color="red">程序计数器(Program Counter Register)，也叫PC寄存器</font>，是一块较小的内存空间，[它可以看作是 当前线程所执行的字节码指令的行号指示器]()。字节码解释器的工作就是通过改变这个计数器的值来选取 下一条需要执行的字节码指令。[分支，循环，跳转，异常处理，线程回复等都需要依赖这个计数器来完成。]()

由于Java虚拟机的多线程是通过线程轮流切换并分配处理器执行时间的方式来实现的，在任何一个确定的时刻，一个处理器(针对多核处理器来说是一个内核)都只会执行一条线程中的指令。因此，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器，各条线程之间计数器互不影响，独立存储，我们称这类内存区域为“线程私有”的内存。

#### 存储的数据

如果一个线程正在执行的是一个Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址; 如果正在执行的是一个Native方法，这个计数器的值则为空。

#### 异常

<font color="red">此内存区域是唯一一个在Java的虚拟机规范中没有规定任何OutOfMemoryError异常情况的区域。</font>

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191221223507345.png" alt="image-20191221223507345" />

### java虚拟机栈

虚拟机栈也是线程私有，而且生命周期与线程相同，每个Java方法在执行的时候都会创建一个<font color="red">栈帧 (Stack Frame)</font>。

![image-20191216150136374](/Users/lee/Library/Application Support/typora-user-images/image-20191216150136374.png)![image-20191216150209473](/Users/lee/Library/Application Support/typora-user-images/image-20191216150209473.png)

![image-20191221223547312](/Users/lee/Library/Application Support/typora-user-images/image-20191221223547312.png)

#### 栈帧

**栈帧(Stack Frame)是用于支持虚拟机进行方法调用和方法执行的数据结构。栈帧存储了方法的<font color="red">局部变量表、操作数栈、动态连接和方法返回地址</font>等信息。每一个方法从调用至执行完成的过程，都对应着一个栈帧在虚拟机栈里从入栈到出栈的过程。**

一个线程中方法的调用链可能会很长，很多方法都同时处于执行状态。对于JVM执行引擎来说，在在活动线程中，只有位于JVM虚拟机栈栈顶的元素才是有效的，即称为**当前栈帧**，与这个栈帧相关连的方法 称为**当前方法，**定义这个方法的类叫做**当前类**。

执行引擎运行的所有字节码指令都只针对当前栈帧进行操作。如果当前方法调用了其他方法，或者当前方法执行结束，那这个方法的栈帧就不再是当前栈帧了。

调用新的方法时，新的栈帧也会随之创建。并且随着程序控制权转移到新方法，新的栈帧成为了当前栈帧。方法返回之际，原栈帧会返回方法的执行结果给之前的栈帧(返回给方法调用者)，随后虚拟机将会 丢弃此栈帧。

关于「栈帧」，我们在看看《Java虚拟机规范》中的描述:

> 栈帧是用来存储数据和部分过程结果的数据结构，同时也用来处理动态连接、方法返回值和异常分派。 
>
> 栈帧随着方法调用而创建，随着方法结束而销毁——无论方法正常完成还是异常完成都算作方法结束。 
>
> 栈帧的存储空间由创建它的线程分配在Java虚拟机栈之中，每一个栈帧都有自己的本地变量表(局部变量表)、操作数栈和指向当前方法所属的类的运行时常量池的引用。

接下来，详细讲解一下栈帧中的局部变量表、操作数栈、动态连接、方法返回地址等各个部分的数据结 构和作用。

##### 局部变量表

###### 存储内容

**局部变量表(Local Variable Table)**是一组变量值存储空间，用于存放**方法参数**和**方法内定义的局部变量**。

一个局部变量可以保存一个类型为`boolean`, `byte`, `char`, `short`, `int`, `float`, `reference`和`returnAddress类型`的数据。reference类型表示对一个对象实例的引用。returnAddress类型是为jsr、jsr_w和ret指令服务的，目前已经很少使用了。

###### 存储容量

局部变量表的容量以**变量槽(Variable Slot)**为最小单位，Java虚拟机规范并没有定义一个槽所应该占用内存空间的大小，但是规定了一个槽应该可以存放一个32位以内的数据类型。

> 在Java程序编译为Class文件时,就在方法的Code属性中的max_locals数据项中确定了该方法所需分配的局部变量表的最大容量。(最大Slot数量)

###### 其他

虚拟机通过索引定位的方法查找相应的局部变量，索引的范围是从 。如果Slot是32位的，则遇到一个64位数据类型的变量(如long或double型)时，会连续使用两个连续的Slot来存储。

##### 操作数栈

###### 作用

**操作数栈(Operand Stack)**也常称为操作栈，它是一个`后入先出栈(LIFO)`。

当一个方法刚刚开始执行时，其操作数栈是空的，随着方法执行和字节码指令的执行，会从**局部变量表** 或**对象实例的字段**中复制常量或变量写入到操作数栈，再随着计算的进行将栈中元素出栈到局部变量表或者返回给方法调用者，也就是出栈/入栈操作。一个完整的方法执行期间往往包含多个这样出栈/入栈 的过程。

###### 存储内容

操作数栈的每一个元素可以是任意Java数据类型，32位的数据类型占一个栈容量，64位的数据类型占2 个栈容量。

###### 存储容量

同局部变量表一样，操作数栈的最大深度也在编译的时候写入到方法的Code属性的`max_stacks`数据项中。且在方法执行的任意时刻，操作数栈的深度都不会超过`max_stacks`中设置的最大值。

##### 动态链接

在一个class文件中，一个方法要调用其他方法，需要将这些方法的**符号引用**转化为其在内存地址中的**直接引用**，而**符号引用存在于方法区中的运行时常量池**。

Java虚拟机栈中，每个栈帧都包含一个指向运行时常量池中该栈所属方法的符号引用，持有这个引用的目的是为了支持方法调用过程中的**动态连接(Dynamic Linking)**。

这些**符号引用**一部分会在**类加载阶段**或者**第一次使用**时就**直接转化为直接引用**，这类转化称为**静态解析**。另一部分将在每次运行期间转化为直接引用，这类转化称为动态连接。

##### 方法返回

**当一个方法开始执行时，可能有两种方式退出该方法:**

- **正常完成出口**
- **异常完成出口**

**正常完成出口**是指方法正常完成并退出，没有抛出任何异常(包括Java虚拟机异常以及执行时通过throw 语句显示抛出的异常)。如果当前方法正常完成，则根据当前方法返回的字节码指令，这时有可能会有返回值传递给方法调用者(调用它的方法)，或者无返回值。具体是否有返回值以及返回值的数据类型将根据该方法返回的字节码指令确定。

**异常完成出口**是指方法执行过程中遇到异常，并且这个异常在方法体内部没有得到处理，导致方法退出。

> 无论是Java虚拟机抛出的异常还是代码中使用athrow指令产生的异常，只要在本方法的异常表中 没有搜索到相应的异常处理器，就会导致方法退出。

无论方法采用何种方式退出，在方法退出后都需要返回到方法被调用的位置，程序才能继续执行，方法返回时可能需要在当前栈帧中保存一些信息，用来帮他恢复它的上层方法执行状态。

> 方法退出过程实际上就等同于把当前栈帧出栈，因此退出可以执行的操作有:恢复上层方法的局部变量表和操作数栈，把返回值(如果有的话)压如调用者的操作数栈中，调整PC计数器的值以指向方法调用指令后的下一条指令。

一般来说，方法正常退出时，调用者的PC计数值可以作为返回地址，栈帧中可能保存此计数值。而方法异常退出时，返回地址是通过异常处理器表确定的，栈帧中一般不会保存此部分信息。

##### 附加信息

虚拟机规范允许具体的虚拟机实现增加一些规范中没有描述的信息到栈帧之中，例如和调试相关的信息，这部分信息完全取决于不同的虚拟机实现。在实际开发中，一般会把动态连接，方法返回地址与其他附加信息一起归为一类，称为栈帧信息。

#### 异常栈

Java虚拟机规范中，对该区域规定了这两种异常情况:

  1. 如果线程请求的栈深度大于虚拟机所允许的深度，将会抛出`StackOverflowError`异常;
2. 虚拟机栈可以动态拓展，当扩展时无法申请到足够的内存，就会抛出`OutOfMemoryError`异常。

```java
package com.kkb.test.memory; public class StackErrorMock {
    private static int index = 1;
    public void call(){
        index++;
				call(); 
    }
    public static void main(String[] args) {
        StackErrorMock mock = new StackErrorMock();
        try {
          mock.call();
				}catch (Throwable e){
          System.out.println("Stack deep : "+index);
					e.printStackTrace(); 
        } 
    }
}
```



### 本地方法栈

本地方法栈和虚拟机栈相似，区别就是虚拟机栈为虚拟机执行**Java服务(字节码服务)**，而本地方法栈为虚拟机使用到的**Native方法(比如C++方法)服务**。

#### 本地方法介绍

##### 什么是本地方法

简单地讲，一个Native Method就是一个java调用非java代码的接口。

```
"A native method is a Java method whose implementation is provided by non- java code."
```

**一个Native Method是这样的一个java的方法:该方法的实现由非java语言实现,比如C**

在定义一个native method时，并不提供实现体(有些像定义一个java interface)，因为其实现体是由

非java语言在外面实现的。下面给了一个示例:

```java
public class IHaveNatives {
     native public void Native1( int x ) ;
     native static public long Native2() ;
     native synchronized private float Native3( Object o ) ;
     native void Native4( int[] ary ) throws Exception ;
}
```

这些方法的声明描述了一些非java代码在这些java代码里看起来像什么样子。

**标识符native可以与所有其它的java标识符连用，但是abstract除外。**这是合理的，因为native暗示这些方法是有实现体的，只不过这些实现体是非java的，但是abstract却显然的指明这些方法无实现体。

**native与其它java标识符连用时，其意义同非Native Method并无差别**，比如native static表明这个方 法可以在不产生类的实例时直接调用，这非常方便，比如当你想用一个native method去调用一个C的类库时。上面的第三个方法用到了native synchronized，JVM在进入这个方法的实现体之前会执行同步 锁机制(就像java的多线程。)

**一个native method方法可以返回任何java类型，包括非基本类型，而且同样可以进行异常控制。**这些方法的实现体可以制一个异常并且将其抛出，这一点与java的方法非常相似。

**当一个native method接收到一些非基本类型时如Object或一个整型数组时，这个方法可以访问这些非基本型的内部，但是这将使这个native方法依赖于你所访问的java类的实现。**有一点要牢牢记住:我们可以在一个native method的本地实现中访问所有的java特性，但是这要依赖于你所访问的java特性的实现，而且这样做远远不如在java语言中使用那些特性方便和容易。

**native method的存在并不会对其他类调用这些本地方法产生任何影响，实际上调用这些方法的其他类甚至不知道它所调用的是一个本地方法。JVM将控制调用本地方法的所有细节。**需要注意当我们将一个本地方法声明为final的情况。用java实现的方法体在被编译时可能会因为内联而产生效率上的提升。 但是一个native final方法是否也能获得这样的好处却是值得怀疑的，但是这只是一个代码优化方面的问题，对功能实现没有影响。

**如果一个含有本地方法的类被继承，子类会继承这个本地方法并且可以用java语言重写这个方法(这个似乎看起来有些奇怪)，同样的如果一个本地方法被fianl标识，它被继承后不能被重写。**

**本地方法非常有用，因为它有效地扩充了jvm。**事实上，我们所写的java代码已经用到了本地方法，在 sun的java的并发(多线程)的机制实现中，许多与操作系统的接触点都用到了本地方法，这使得java程序能够超越java运行时的界限。有了本地方法，java程序可以做任何应用层次的任务。

##### 为什么要使用本地方法

java使用起来非常方便，然而有些层次的任务用java实现起来不容易，或者我们对程序的效率很在意时，问题就来了。

**有时java应用需要与java外面的环境交互。这是本地方法存在的主要原因，你可以想想java需要与一些底层系统如操作系统或某些硬件交换信息时的情况。**本地方法正是这样一种交流机制:它为我们提供了一个非常简洁的接口，而且我们无需去了解java应用之外的繁琐的细节。

JVM支持着java语言本身和运行时库，它是java程序赖以生存的平台，它由一个解释器(解释字节码) 和一些连接到本地代码的库组成。然而不管怎样，它毕竟不是一个完整的系统，它经常依赖于一些底层 (underneath在下面的)系统的支持。这些底层系统常常是强大的操作系统。通过使用本地方法，我们得以用java实现了jre的与底层系统的交互，甚至JVM的一些部分就是用C写的，还有，如果我们要使用一些java语言本身没有提供封装的操作系统的特性时，我们也需要使用本地方法。

##### JVM怎样使本地方法跑起来

我们知道，当一个类第一次被使用到时，这个类的字节码会被加载到内存，并且只会回载一次。在这个 被加载的字节码的入口维持着一个该类所有方法描述符的list，这些方法描述符包含这样一些信息:方 法代码存于何处，它有哪些参数，方法的描述符(public之类)等等。

如果一个方法描述符内有native，这个描述符块将有一个指向该方法的实现的指针。这些实现在一些 DLL文件内，但是它们会被操作系统加载到java程序的地址空间。当一个带有本地方法的类被加载时， 其相关的DLL并未被加载，因此指向方法实现的指针并不会被设置。当本地方法被调用之前，这些DLL才会被加载，这是通过调用java.system.loadLibrary()实现的。

最后需要提示的是，使用本地方法是有开销的，它丧失了java的很多好处。如果别无选择，我们可以选择使用本地方法。

#### 本地方法栈的使用流程

当某个线程调用一个本地方法时，它就进入了一个全新的并且不再受虚拟机限制的世界。本地方法可以通过本地方法接口来访问虚拟机的运行时数据区，但不止如此，它还可以做任何它想做的事情。

本地方法本质上时依赖于实现的，虚拟机实现的设计者们可以自由地决定使用怎样的机制来让Java程序 调用本地方法。

任何本地方法接口都会使用某种本地方法栈。当线程调用Java方法时，虚拟机会创建一个新的栈帧并压 入Java栈。然而当它调用的是本地方法时，虚拟机会保持Java栈不变，不再在线程的Java栈中压入新的帧，虚拟机只是简单地动态连接并直接调用指定的本地方法。

**如果某个虚拟机实现的本地方法接口是使用C连接模型的话，那么它的本地方法栈就是C栈**。当C程序调用一个C函数时，其栈操作都是确定的。传递给该函数的参数以某个确定的顺序压入栈，它的返回值也以确定的方式传回调用者。同样，这就是虚拟机实现中本地方法栈的行为。

很可能本地方法接口需要回调Java虚拟机中的Java方法，在这种情况下，该线程会保存本地方法栈的状态并进入到另一个Java栈。

**下图描绘了这样一个情景，就是当一个线程调用一个本地方法时，本地方法又回调虚拟机中的另一个Java方法**

**这幅图展示了JAVA虚拟机内部线程运行的全景图。**一个线程可能在整个生命周期中都执行Java方法，操 作它的Java栈;或者它可能毫无障碍地在Java栈和本地方法栈之间跳转。

![image-20191216154448133](/Users/lee/Library/Application Support/typora-user-images/image-20191216154448133.png)

该线程首先调用了两个Java方法，而第二个Java方法又调用了一个本地方法，这样导致虚拟机使用了一个本地方法栈。假设这是一个C语言栈，其间有两个C函数，第一个C函数被第二个Java方法当做本地方法调用，而这个C函数又调用了第二个C函数。之后第二个C函数又通过本地方法接口回调了一个Java方 法(第三个Java方法)，最终这个Java方法又调用了一个Java方法(它成为图中的当前方法)。

### Java堆

#### 概念

Java堆被所有线程共享，在Java虚拟机启动时创建。是虚拟机管理最大的一块内存。 

Java堆是**垃圾回收**的主要区域，主要采用**分代回收算法**。堆进一步划分主要是为了更好的回收内存或更快的分配内存。

#### HotSpot整体JVM的内存图展示

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191221222344284.png" alt="image-20191221222344284" style="zoom:50%;" />



##### jdk1.6

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191221222043218.png" alt="image-20191221222043218" style="zoom:50%;" />

##### jdk1.7

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191221222057438.png" alt="image-20191221222057438" style="zoom:50%;" />

##### jdk1.8

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191221222128683.png" alt="image-20191221222128683" style="zoom:50%;" />



#### 存储内容

Java虚拟机规范的描是:所有的**对象实例**以及**数组**都要在堆上分配。

随着JIT编译器的发展与逃逸分析技术的逐渐成熟，**栈上分配、标量替换优化技术**将会导致一些微妙的变化发生，**所有的对象都分配在堆上也渐渐变得不是那么“绝对”了**。

#### 存储方式

堆内存空间在物理上可以不连续，逻辑上连续即可。

#### 堆内存划分

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191221222419067.png" alt="image-20191221222419067" style="zoom: 50%;" />

- 新生代
  - Eden空间[伊甸园]
  -  From Survivor空间
  -  To Survivor空间
- 老年代

**堆大小=新生代+老年代**

堆的大小可通过参数`-Xms(堆的初始容量),` `-Xmx(堆的最大容量)` 来指定。

- 其中，新生代 ( Young ) 被细分为 Eden 和 两个 Survivor 区域，这两个 Survivor 区域分别被命名 为 from 和 to，以示区分。

  默认的，Eden : from : to = 8 : 1 : 1 。(可以通过参数 –XX:SurvivorRatio 来设定 。 即: Eden = 8/10 的新生代空间大小，from = to = 1/10 的新生代空间大小。

- JVM  每次只会使用 Eden 和其中的一块 Survivor 区域来为对象服务，所以无论什么时候，总是有一块 Survivor 区域是空闲着的。

- 新生代实际可用的内存空间为 9/10 ( 即90% )的新生代空间。

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191217222439434.png" alt="image-20191217222439434" style="zoom:50%;" />

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191216155141608.png" alt="image-20191216155141608" style="zoom:50%;" />

#### 对象创建

```java
 Student stu = new Student();
```

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191216155625448.png" alt="image-20191216155625448" style="zoom:50%;" />

#### 对象内存布局

对象在内存中存储的布局可以分为三块区域:对象头(Header)，实例数据(Instance Data)和对齐填充(Padding)。 

**1) 对象头**

对象头包括两部分信息: 

一部分是[用于存储对象自身的运行数据]()，如`哈希码(hash code), GC分代年龄,锁状态标识,线程持有的锁,偏向线程id,偏向时间戳`等。 

另一部分是类型指针，即对象指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象是哪一个类的实例。当对象是一个java数组的时候，那么对象头还必须有一块用于记录数组长度的数据，因此虚 拟机可以通过普通java对象的元数据信息确定java对象的大小，但是从数组的元数据中无法确定数组的大小。

**2) 实例数据**

存储的是对象真正有效的信息。

**3) 对齐填充**

这部分并不是必须要存在的，没有特别的含义，在jvm中对象的大小必须是8字节的整数倍，而对象头也是8字节的倍数，当对象实例数据部分没有对齐时，就需要通过对齐填充来补全。

#### 内存分配原则

| 序号 | 介绍                                                         |
| ---- | ------------------------------------------------------------ |
| 1    | 优先在Eden分配,如果Eden空间不足虚拟机则会进行一次MinorGC     |
| 2    | 大对象直接接入老年代,大对象一般指的是很长的字符串或数组      |
| 3    | 长期存活的对象进入老年代,每个对象都有一个age,当age到达设定的年龄的时候就会进入老年代,默认15岁 |

#### java堆的整体内存分配

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191221222510159.png" alt="image-20191221222510159" style="zoom: 50%;" />

#### java对象的内存分配

内存分配的方法有两种:[指针碰撞(Bump the Pointer)和空闲列表(Free List)]()

| 分配方法 | 说明             | 收集器                      |
| -------- | ---------------- | --------------------------- |
| 指针碰撞 | 内存地址是连续的 | Serial和ParNew收集器        |
| 空闲列表 | 内存地址不连续   | CMS收集器和Mark-Sweep收集器 |

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191216160008122.png" alt="image-20191216160008122" style="zoom:50%;" />

#### 内存分配线程安全问题

在分配内存的同时，存在线程安全的问题，即虚拟机给A线程分配内存过程中，指针未修改，B线程可能同时使用了同样一块内存。

在JVM中有两种解决办法:

1. **CAS，比较和交换(Compare And Swap)**: CAS 是乐观锁的一种实现方式。所谓乐观锁就是， 每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。虚拟机采用 CAS 配上失败重试的方式保证更新操作的原子性。
2. **TLAB，本地线程分配缓冲(Thread Local Allocation Buffer即TLAB)**: 为每一个线程预先分配一块内存，JVM在给线程中的对象分配内存时，首先在TLAB分配，当对象大于TLAB中的剩余内存 或TLAB的内存已用尽时，再采用上述的CAS进行内存分配。

##### 指针碰撞的内存分配

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191221222651394.png" alt="image-20191221222651394" style="zoom:50%;" />

#### 对象访问

| 方式     | 优点                                |
| -------- | ----------------------------------- |
| 句柄     | 稳定,对象被移动只要修改句柄中的地址 |
| 直接指针 | 访问速度快,节省了一次指针定位的开销 |

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191216160639091.png" alt="image-20191216160639091" style="zoom:50%;" />

#### 数组的内存分析

##### 一维数组

![image-20191216173411826](/Users/lee/Library/Application Support/typora-user-images/image-20191216173411826.png)

```java
int[] arr1 = new int[3];
```

> 先把 arr1 压进栈，然后在堆空间中开辟一个空间，并把值初始化为0(arr1为引用变量，但是内部数据是int类型，默认值为 0),最后把 **开辟的堆空间地址** 赋值给arr1

```java
int[] arr2 = arr1;
```

> 把 arr1 中的 **地址** 赋值给 arr2，此时 arr2 和 arr1 指向同一块空间。

```java
arr2[0] = 20;
```

##### 二维数组

![image-20191216174403911](/Users/lee/Library/Application Support/typora-user-images/image-20191216174403911.png)

```java
int [][] array = new int[3][];
```

> 这条语句会先把 array 压栈，然后在堆中开辟一个空间，初始值为 null(array为引用变量，第一 维同样是引用类型)，最后把**开辟的堆空间地址**赋值给 array。

```java
array[0][] = new int[1];
```

> 这条语句会在堆空间中开辟一个只有一个 int 类型大小的空间，并初始化为 0 ，然后把自己的地址赋值给array\[0][]。

```java
array[1][] = new int [2];
array[2][] = new int [3];
```

> 这两条语句和上一条意义一样，就不再做解释

### 方法区

#### 介绍

线程共享，存储已经被虚拟机加载的**类信息、常量、静态变量、即时编译器编译后的代码**等等。

**jdk1.7**之前，**HotSpot虚拟机**对于方法区的实现称之为**“永久代”， **`Permanent Generation`.

**jdk1.8**之后，**HotSpot虚拟机**对于方法区的实现称之为“**元空间**”，`Meta Space`

**方法区是Java虚拟机规范中的定义，是一种规范，而永久代和元空间是**`hotSpot`**虚拟机不同版本的两种实现。**

![image-20191216180130273](/Users/lee/Library/Application Support/typora-user-images/image-20191216180130273.png)

![image-20191217112139666](/Users/lee/Library/Application Support/typora-user-images/image-20191217112139666.png)



#### 元空间和永久代有什么不同的?

```
存储位置不同，永久代物理是堆的一部分，和新生代，老年代地址是连续的，而元空间属于本地内存;

存储内容不同，元空间存储类的元信息，[静态变量]和[常量池]等并入堆中。相当于永久代的数据被分到 了堆和元空间中。
```

通过上面分析，大家应该大致了解了 JVM 的内存划分，也清楚了 JDK 8 中永久代向元空间的转换。不过 大家应该都有一个疑问，就是**为什么要做这个转换**?带着这个疑问，最后给大家总结以下几点原因:

```
1. 字符串存在永久代中，容易出现性能问题和内存溢出。
2. 类及方法的信息等比较难确定其大小，因此对于永久代的大小指定比较困难，太小容易出现永久代溢 出，太大则容易导致老年代溢出。
3. 永久代会为 GC 带来不必要的复杂度，并且回收效率偏低。
4. Oracle 可能会将HotSpot 与 JRockit 合二为一。
```

#### 存储内容

存储示意图如下，下面的图片显示的是JVM加载类的时候，方法区存储的信息:

![image-20191216180350068](/Users/lee/Library/Application Support/typora-user-images/image-20191216180350068.png)

1. ##### 类型信息

- 类型的全限定名
- 超类的全限定名
- 直接超接口的全限定名
- 类型标志(该类是类类型还是接口类型) 类的访问描述符(public、private、default、abstract、final、static)

2. ##### 类型的常量池

   存放该类型所用到的常量的有序集合，包括**直接常量(如字符串、整数、浮点数的常量)和对其他类型、字段、方法的符号引用**。常量池中每一个保存的常量都有一个索引，就像数组中的字段一样。因为 常量池中保存着所有类型使用到的类型、字段、方法的字符引用，所以它也是动态连接的主要对象(在 动态链接中起到核心作用)。

3. ##### 字段信息(该类生命的所有字段)

- 字段修饰符(public、protect、private、default)
-  字段的类型
-  字段名称

4. ##### 方法信息

   方法信息中包含类的所有方法，每个方法包含以下信息:

- 方法修饰符
- 方法返回类型
- 方法名
- 方法参数个数、类型、顺序等
- 方法字节码 操作数栈和该方法在栈帧中的局部变量区大小异常表

5. ##### 类变量(静态变量)

   指该类所有对象共享的变量，即使没有任何实例对象时，也可以访问的类变量。它们与类进行绑定。

6. ##### 指向类加载器的引用

   每一个被JVM加载的类型，都保存这个类加载器的引用，类加载器动态链接时会用到。

7. ##### 指向Class实例的引用

   类加载的过程中，虚拟机会创建该类型的Class实例，方法区中必须保存对该对象的引用。通过 Class.forName(String className)来查找获得该实例的引用，然后创建该类的对象。

8. ##### 方法表

   Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池，用于存放编

   译器生成的各种字面常量和符号引用，这部分内容被类加载后进入方法区的运行时常量池中存放。

   运行时常量池相对于Class文件常量池的另外一个特征具有动态性，可以在运行期间将新的常量放入池

   中(典型的如String类的intern()方法)。

#### 永久代异常演示

绝大部分 Java 程序员应该都见过 "**java.lang.OutOfMemoryError: PermGen space** "这个异常。这 里的 “PermGen space”其实指的就是方法区。由于方法区主要存储类的相关信息，所以对于动态生成类 的情况比较容易出现永久代的内存溢出。**最典型的场景就是，在** **jsp** **页面比较多的情况，容易出现永久 代内存溢出。**

**我们现在通过动态生成类来模拟永久代的内存溢出(本例中使用的JDK版本是1.7，指定的PermGen区的大小为8M):**

```java
package com.lee.source;
public class Test{}
```

```java
package com.lee.source;
import java.io.File;
import java.net.URL;
import java.net.URLClassLoader; import java.util.ArrayList; import java.util.List;
public class PermGenOomMock{
    public static void main(String[] args) {
        URL url = null;
        List<ClassLoader> classLoaderList = new ArrayList<ClassLoader>();
        try {
            url = new File("/tmp").toURI().toURL(); URL[] urls = {url};
            while (true){
                ClassLoader loader = new URLClassLoader(urls);
                classLoaderList.add(loader);
                loader.loadClass("com.kkb.test.memory.Test"); }
        } catch (Exception e) { e.printStackTrace();
        } }
}
```

通过每次生成不同URLClassLoader对象来加载Test类，从而生成不同的类对象，这样就能看到我们熟 悉的 "**java.lang.OutOfMemoryError: PermGen space** " 异常了。

这里之所以采用 JDK 1.7，是因为在 JDK 1.8 中， HotSpot 已经没有 “PermGen space”这个区间了，取 而代之是一个叫做 Metaspace(元空间) 的东西。下面我们就来看看 Metaspace 与 PermGen space 的区别。

#### 元空间异常演示

**其实，移除永久代的工作从JDK1.7就开始了**。JDK1.7中，存储在永久代的部分数据就已经转移到了Java Heap或者是 Native Heap。但永久代仍存在于JDK1.7中，并没完全移除，譬如**符号引用(Symbols)转移 到了native heap;字面量(interned strings)转移到了java heap;类的静态变量(class statics)转移 到了java heap**。我们可以通过一段程序来比较 JDK 1.6 与 JDK 1.7及 JDK 1.8 的区别，以字符串常量为 例:

```java
package com.lee.source.jvm;

import java.util.ArrayList;
import java.util.List;
public class StringOomMock {
    static String base = "string";
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        for (int i=0;i< Integer.MAX_VALUE;i++){
            String str = base + base;
            base = str;
            list.add(str.intern());
        }
    }
}
```

这段程序以2的指数级不断的生成新的字符串，这样可以比较快速的消耗内存。我们通过 JDK 1.6、JDK 1.7 和 JDK 1.8 分别运行:

**JDK 1.6的运行结果:**

![image-20191216193531083](/Users/lee/Library/Application Support/typora-user-images/image-20191216193531083.png)

**JDK1.7的运行结果:**

![image-20191216193625847](/Users/lee/Library/Application Support/typora-user-images/image-20191216193625847.png)

**JDK 1.8的运行结果:**

![image-20191216193645861](/Users/lee/Library/Application Support/typora-user-images/image-20191216193645861.png)

从上述结果可以看出，**JDK 1.6下，会出现“PermGen Space”的内存溢出，而在 JDK 1.7和 JDK 1.8 中，会出现堆内存溢出，并且 JDK 1.8中 PermSize 和 MaxPermGen 已经无效。因此，可以大致验证 JDK 1.7 和 1.8 将字符串常量由永久代转移到堆中，并且 JDK 1.8 中已经不存在永久代的结论**。

现在我们看看元空间到底是一个什么东西?元空间的本质和永久代类似，都是对JVM规范中方法区的实 现。不过元空间与永久代之间最大的区别在于:<font color="red">元空间并不在虚拟机中，而是使用本地内存。</font>因此，默 认情况下，元空间的大小仅受本地内存限制，但可以通过以下参数来指定元空间的大小:

```
-XX:MetaspaceSize:初始空间大小，达到该值就会触发垃圾收集进行类型卸载，同时GC会对该值进 行调整:如果释放了大量的空间，就适当降低该值;如果释放了很少的空间，那么在不超过 MaxMetaspaceSize时，适当提高该值。

-XX:MaxMetaspaceSize:最大空间，默认是没有限制的。

除了上面两个指定大小的选项以外，还有两个与 GC 相关的属性: 
-XX:MinMetaspaceFreeRatio:在GC之后，最小的Metaspace剩余空间容量的百分比，减少为分配 空间所导致的垃圾收集 
-XX:MaxMetaspaceFreeRatio:在GC之后，最大的Metaspace剩余空间容量的百分比，减少为释放 空间所导致的垃圾收集
```

现在我们在 JDK 8下重新运行一下代码段 4，不过这次不再指定 PermSize 和 MaxPermSize。而是指定 MetaSpaceSize 和 MaxMetaSpaceSize的大小。输出结果如下:

![image-20191216201131126](/Users/lee/Library/Application Support/typora-user-images/image-20191216201131126.png)

从输出结果，我们可以看出，这次不再出现永久代溢出，而是出现了元空间的溢出。

### 运行时常量池和字符串常量池

#### 存储内容

Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池，用于存放编 译期生成的各种<font color="red">**字面量和符号引用，这部分内容将在类加载后进入方法区的[运行时常量池]()中存放。**</font>

> - 字面量:
>   - 双引号引起来的字符串值，“kkb”
>   - 定义为final类型的常量的值。 
> - 符号引用:
>   - 类或接口的全限定名(包括他的父类和所实现的接口)
>   - 变量或方法的名称
>   -  变量或方法的描述信息
>     - 方法的描述:参数个数、参数类型、方法返回类型等等
>     - ​        变量的描述信息:变量的返回值
>   - this

运行时常量池相对于Class文件常量池的另外一个重要特征是**具备动态性**，Java语言并不要求常量一定只 有编译期才能产生，也就是并非预置入Class文件中常量池的内容才能进入方法区运行时常量池，<font color="red">**运行期间也可能将新的常量放入池中，这种特性被开发人员利用比较多的就是String类的intern()方法。**</font>

#### 存储位置

在JDK1.6及以前，运行时常量池是[方法区]()的一部分。
在JDK1.7及以后，运行时常量池在[Java 堆(Heap)]()中。

<font color="red">运行时和class常量池一样，运行时常量池也是每个类都有一个。但是字符串常量池全只有一个</font>

#### 常量池区别

- class常量池中存储的是符号引用，而运行时常量池存储的是被解析之后的直接引用。

- class常量池存在于class文件中，运行时常量池和字符串常量池是存在于JVM内存中。

- 运行时常量池具有动态性，java运行期间也可能将新的常量放入池中(**String#intern()**),

- 字符串常量池逻辑上属于运行时常量池的一部分，但是它和运行时常量池的区别在于，字符串常量 池是全局唯一的，而运行时常量池是每个类一个。

#### 字符串常量池如何存储数据

实际上，[为了提高匹配速度]()，即更快的查找某个字符串是否存在于常量池，Java在设计字符串常量池的 时候，还搞了一张[stringtable]()， stringtable 有点类似于我们的hashtable，里面保存了[字符串的引 用]()。

- 在jdk6中 `StringTable` 是固定的，就是1009的长度，因此如果放入String Pool中的String非常 多，就会造成hash冲突，导致链表过长，当调用String.intern()时会需要到链表上一个一个找，从而导致性能大幅度下降;

- 在jdk7中， `StringTable` 的长度可以通过一个参数指定:

  ```
  -XX:StringTableSize=99991
  ```

##### 字符串常量池查找字符串的方式:

- 根据字符串的 `hashcode` 找到对应entry。如果没冲突，它可能只是一个entry，如果有冲突，它可 能是一个entry链表，然后Java再遍历entry链表，匹配引用对应的字符串。 

- 如果找得到字符串，返回引用。如果找不到字符串，会把字符串放到常量池，并把引用保存到 stringtable里。

<img src="/Users/lee/Library/Application Support/typora-user-images/image-20191216203127662.png" alt="image-20191216203127662" style="zoom:50%;" />



#### 字符串常量池介绍

上面我们已经稍微了解过字符串常量池了，[它是java为了节省空间而设计的一个内存区域，java中所有 的类共享一个字符串常量池。]()

> 比如A类中需要一个“hello”的字符串常量，B类也需要同样的字符串常量，他们都是从字符串常量 池中获取的字符串，并且获得得到的字符串常量的地址是一样的。

#### 字符串常量池案例分析

```
1、单独使用””引号创建的字符串都是常量，编译期就已经确定存储到String Pool中。 

2、使用new String(“”)创建的对象会存储到heap中，是运行期新创建的。

3、使用只包含常量的字符串连接符如”aa”+”bb”创建的也是常量，编译期就能确定已经存储到String Pool中。

4、使用包含变量的字符串连接如”aa”+s创建的对象是运行期才创建的，存储到heap中。 
5、运行期调用String的intern()方法可以向String Pool中动态添加对象。
```

```java
package com.lee.source.jvm;
public class StringTest {
    public void test() {
        String str1 = "abc";
        String str2 = new String("abc"); 
      	System.out.println(str1 == str2);
        String str3 = new String("abc"); 
      	System.out.println(str3 == str2);
        String str4 = "a" + "b"; 
      	System.out.println(str4 == "ab");
        final String s = "a";
        String str5 = s + "b";
      	System.out.println(str5 == "ab");
        String s1 = "a";
        String s2 = "b";
        String str6 = s1 + s2; 
      	System.out.println(str6 == "ab");
        String str7 = "abc".substring(0, 2); 
      	System.out.println(str7 == "ab");
        String str8 = "abc".toUpperCase(); 
     	 	System.out.println(str8 == "ABC");
        String s3 = "ab";
        String s4 = "ab" + getString(); 
      	System.out.println(s3 == s4);
        String s5 = "a";
        String s6 = "abc";
        String s7 = s5 + "bc"; 
      	System.out.println(s6 == s7.intern());
    }

    private String getString(){
        return "c";
    }
}
```

**分析1**

```java
Stringstr1="abc";
System.out.println(str1=="abc");
```

```
步骤:
1) 栈中开辟一块空间存放引用str1，
2) String池中开辟一块空间，存放String常量"abc"，
3) 引用str1指向池中String常量"abc"，
4) str1所指代的地址即常量"abc"所在地址，输出为true
```

**分析2**

```java
Stringstr2=newString("abc"); 
System.out.println(str2=="abc");			
```

```
步骤:
1) 栈中开辟一块空间存放引用str2，
2) 堆中开辟一块空间存放一个新建的String对象"abc"，
3) 引用str2指向堆中的新建的String对象"abc"，
4) str2所指代的对象地址为堆中地址，而常量"abc"地址在池中，输出为false
```

**分析3**

```java
String str2 = new String("abc"); 
String str3 = new String("abc"); 
System.out.println(str3 == str2);
```

```
步骤:
1) 栈中开辟一块空间存放引用str3，
2) 堆中开辟一块新空间存放另外一个(不同于str2所指)新建的String对象， 
3) 引用str3指向另外新建的那个String对象
4) str3和str2指向堆中不同的String对象，地址也不相同，输出为false
```

**分析4**

```java
String str4 = "a" + "b";
System.out.println(str4 == "ab");	
```

```
步骤:
1) 栈中开辟一块空间存放引用str4，
2) 根据编译器合并已知量的优化功能，池中开辟一块空间，存放合并后的String常量"ab"， 
3) 引用str4指向池中常量"ab"，
4) str4所指即池中常量"ab"，输出为true
```

**分析5**

```java
final String s = "a";
String str5 = s + "b"; 
System.out.println(str5 == "ab");
```

```
步骤:
同4
```

**分析6**

```java
String s1 = "a";
String s2 = "b";
String str6 = s1 + s2; 
System.out.println(str6 == "ab");
```

```
步骤:
1) 栈中开辟一块中间存放引用s1，s1指向池中String常量"a"，
2) 栈中开辟一块中间存放引用s2，s2指向池中String常量"b"，
3) 栈中开辟一块中间存放引用str6，
4) s1 + s2通过StringBuilder的最后一步toString()方法还原一个新的String对象"ab"，因此 堆中开辟一块空间存放此对象，
5) 引用str6指向堆中(s1 + s2)所还原的新String对象，
6) str6指向的对象在堆中，而常量"ab"在池中，输出为false
```

**分析7**

```java
Stringstr7="abc".substring(0,2);
System.out.println(str7=="ab");
```

```
步骤:
1) 栈中开辟一块空间存放引用str7，
2) substring()方法还原一个新的String对象"ab"(不同于str6所指)，堆中开辟一块空间存放此 对象，
3) 引用str7指向堆中的新String对象，
```

**分析8**

```java
String str8 = "abc".toUpperCase(); 
System.out.println(str8=="ABC");
```

```
步骤:
1) 栈中开辟一块空间存放引用str8，
2) toUpperCase()方法还原一个新的String对象"ABC"，池中并未开辟新的空间存放String常 量"ABC"，
3) 引用str8指向堆中的新String对象
```

### String的intern方法详解

先让大家做个面试题:

```java
String a = "hello";
String b = new String("hello"); 
System.out.println(a == b);
String c = "world"; 
System.out.println(c.intern() == c);
String d = new String("mike"); 
System.out.println(d.intern() == d);
String e = new String("jo") + new String("hn"); 
System.out.println(e.intern() == e);
String f = new String("ja") + new String("va"); 
System.out.println(f.intern() == f);
```

如果大家能一题不差的全做对，接下来的内容应该不用看了。如果不能并且有兴趣的话，可以稍微了解 一下以下内容。

#### intern的作用

[intern的作用是把new出来的字符串的引用添加到stringtable中]()，java会先计算string的hashcode，查找stringtable中是否已经有string对应的引用了，如果有返回引用(地址)，然后没有把字符串的地址 放到stringtable中，并返回字符串的引用(地址)。

我们继续看例子:

```java
String a = new String("haha");
System.out.println(a.intern() == a);//false
```

> 因为有双引号括起来的字符串，所以会把ldc命令，即"haha"会被我们添加到字符串常量池，它的 引用是string的char数组的地址，会被我们添加到stringtable中。所以a.intern的时候，返回的其 实是string中的char数组的地址，和a的string实例化地址肯定是不一样的。

```java
String e = new String("jo") + new String("hn");
System.out.println(e.intern() == e);//true
```

> new String("jo") + new String("hn")实际上会转为stringbuffer的append 然后tosring()出来，实 际上是new 一个新的string出来。在这个过程中，并没有双引号括起john，也就是说并不会执行 ldc然后把john的引用添加到stringtable中，所以intern的时候实际就是把新的string地址(即e的 地址)添加到stringtable中并且返回回来。

```java
String f = new String("ja") + new String("va");
System.out.println(f.intern() == f);//false
```

> 或许很多朋友感觉很奇怪，这跟上面的例子2基本一模一样，但是却是false呢?这是因为java在启 动的时候，会把一部分的字符串添加到字符串常量池中，而这个“java”就是其中之一。所以intern 回来的引用是早就添加到字符串常量池中的”java“的引用，所以肯定跟f的原地址不一样。

#### JDK6中的理解

**JDK6中,字符串常量位于PermGen(永久代)中,PermGen是一块主要用于存放已加载的类信息和字符串池的大小固定的区域**

执行intern()方法时，若常量池中不存在等值的字符串，[JVM就会在常量池中创建一个等值的字符串]()，然 后返回该字符串的引用。除此以外，JVM 会自动在常量池中保存一份之前已使用过的字符串集合。

Jdk6中使用intern()方法的主要问题就在于字符串常量池被保存在PermGen中:

> - **首先，PermGen是一块大小固定的区域，一般不同的平台PermGen的默认大小也不相同， 大致在32M到96M之间。**所以不能对不受控制的运行时字符串(如用户输入信息等)使用 intern()方法，否则很有可能会引发PermGen内存溢出; 
> - **其次String对象保存在Java堆区，Java堆区与PermGen是物理隔离的，因此如果对多个不 等值的字符串对象执行intern操作，则会导致内存中存在许多重复的字符串，会造成性能损 失。**

#### JDK7+的理解

**Jdk7将常量池从PermGen区移到了Java堆区。**堆区的大小一般不受限，所以将常量池从PremGen区移 到堆区使得常量池的使用不再受限于固定大小。可以使用 虚拟机参数设置字 符串池的map大小。

```
字符串池内部实现为一个HashMap，所以当能够确定程序中需要intern的字符串数目时，可以将该map 的size设置为所需数目*2(减少hash冲突)，这样就可以使得String.intern()每次都只需要常量时 间和相当小的内存就能够将一个String存入字符串池中。
```

执行intern操作时，如果常量池已经存在该字符串，则直接返回字符串引用，否则复制该字符串对象的 引用到常量池中并返回。

除此之外，位于堆区的常量池中的对象可以被垃圾回收。当常量池中的字符串不再存在指向它的引用 时，JVM就会回收该字符串。

#### intern案例分析

```java
public static void main(String[] args) { 
  String s = new String("1"); 
  s.intern();
	String s2 = "1"; 
  System.out.println(s == s2);
	String s3 = new String("1") + new String("1"); 
  s3.intern();
	String s4 = "11";
	System.out.println(s3 == s4);
}
```

打印结果是:

- jdk6下`false false`
- jdk下`false true`

具体为什么稍后再解释，然后将 s3.intern(); 语句下调一行，放到 String s4 = "11"; 后面。将

s.intern(); 放到Strings2="1";后面。是什么结果呢?

```java
public static void main(String[] args) { 
  String s = new String("1"); 
	String s2 = "1"; 
  s.intern();
  System.out.println(s == s2);
	String s3 = new String("1") + new String("1"); 
	String s4 = "11";
  s3.intern();
	System.out.println(s3 == s4);
}
```

- jdk6下`false false`
- jdk下`false false`

##### jdk6中的解释

![image-20191217181021282](/Users/lee/Library/Application Support/typora-user-images/image-20191217181021282.png)

如上图所示。在 jdk6中上述的所有打印都是 false 的，因为 jdk6中的常量池是放在 Perm 区中的， Perm区和正常的 JAVA Heap 区域是完全分开的。上面说过如果是使用引号声明的字符串都是会直接在 字符串常量池中生成，而 new 出来的 String 对象是放在 JAVA Heap 区域。所以拿一个 JAVA Heap 区域 的对象地址和字符串常量池的对象地址进行比较肯定是不相同的，即使调用 方法也是 没有任何关系的。

##### jdk7中解释

在 Jdk6 以及以前的版本中，字符串的常量池是放在堆的Perm区的，Perm区是一个类静态的区域，主 要存储一些加载类的信息，常量池，方法片段等内容，默认大小只有4m，一旦常量池中大量使用 intern 是会直接产生 `java.lang.OutOfMemoryError:PermGen space`错误的。

在 jdk7 的版本中，字符串常量池已经从Perm区移到正常的Java Heap区域了。为什么要移动，Perm 区域太小是一个主要原因，而且jdk8已经直接取消了Perm区域，而新建立了一个元区域。应该是jdk开 发者认为Perm区域已经不适合现在 JAVA 的发展了。正式因为字符串常量池移动到JAVA Heap区域后， 再来解释为什么会有上述的打印结果。

![image-20191217181142181](/Users/lee/Library/Application Support/typora-user-images/image-20191217181142181.png)

- 在第一段代码中,先看S3和S4字符串,`String s3 = new String("1") + new String("1");`,这句代码中现在生成了2个最终对象,是字符串常量池中的"1"和JAVA Heap中的s3引用指向的对象.中间还有2个匿名的new String("1")我们不去讨论他们.此时s3引用对象内容是"11",但此时常量池中是没有"11"对象的
- 接下来`s3.intern()`;这一句代码,试讲s3中的"11"字符串放入String常量池中,因为此时常量池中不存在"11"字符串,因此常规做法是跟jdk6途中表示的那样,在常量池中生成一个"11"的对象,关键点是jdk7中常量池不在Perm区域了,这块做了调整.常量池中不需要再存储一份对象了,可以直接存储堆中的引用.这份引用指向s3引用的对象.也就是说引用地址是相同的.
- 最后`String s4 = "11";`这句代码中"11"是显示声明的,因此会直接去常量池中创建,创建的时候发现已经有这个对象了,此时也就是指向s3引用对象的印个引用.所以s4引用就指向和s3一样了.因此最后的比较s3==s4是true.
- 在看s和s2对象,`String s = new String("1")`;第一句代码,生成了2个对象.常量池中的"1"和JAVA Heap中的字符串对象`s.intern();`这一句是s对象去常量池寻找后发现"1"已经在常量池里了
- 接下来`String s2 = "1"`;这句代码是生成一个s2的引用指向常量池中的"1"对象,结果就是s和s2的引用地址明显不同,图中画的很清晰.

![image-20191217182549633](/Users/lee/Library/Application Support/typora-user-images/image-20191217182549633.png)

- 来看第二段代码，从上边第二幅图中观察。第一段代码和第二段代码的改变就是 `s3.intern();` 的顺序是放在 `String s4 = "11";` 后了。这样，首先执行 `String s4 = "11";` 声明 s4 的时候常 量池中是不存在“11”对象的，执行完毕后，“11“对象是 s4 声明产生的新对象。然后再执行`s3.intern();` 时，常量池中“11”对象已经存在了，因此 s3 和 s4 的引用是不同的。
- 第二段代码中的 s 和 s2 代码中， `s.intern();` ，这一句往后放也不会有什么影响了，因为对象池 中在执行第一句代码 `String s = new String("1");` 的时候已经生成“1”对象了。下边的s2声明 都是直接从常量池中取地址引用的。 s 和 s2 的引用地址是不会相等的。

##### 小结

从上述的例子代码可以看出 jdk7 版本对 intern 操作和常量池都做了一定的修改。主要包括2点:

- 将String常量池从Perm区移动到了JAVA Heap区
- `String#intern`方法时,如果存在堆中的对象,会直接保存对象的引用.而不会重新创建对象.

#### intern方法的好处

如果在字符串拼接中，有一个参数是非字面量，而是一个变量的话，整个拼接操作会被编译成 StringBuilder.append，这种情况编译器是无法知道其确定值的。只有在运行期才能确定。

```java
String s3 = new String("1") + new String("1");
```

那么，有了这个特性了，intern就有用武之地了。那就是很多时候，我们在程序中得到的字符串是只有 在运行期才能确定的，在编译期是无法确定的，那么也就没办法在编译期被加入到常量池中。

这时候，对于那种可能经常使用的字符串，使用intern进行定义，每次JVM运行到这段代码的时候，就 会直接把常量池中该字面值的引用返回，这样就可以减少大量字符串对象的创建了。

```java
		static final int MAX = 1000 * 10000;
    static final String[] arr = new String[MAX];
    public static void main(String[] args) throws Exception { 
        Integer[] DB_DATA = new Integer[10];
        Random random = new Random(10 * 10000);
        for (int i = 0; i < DB_DATA.length; i++) {
            DB_DATA[i] = random.nextInt(); }
        long t = System.currentTimeMillis(); 
        for (int i = 0; i < MAX; i++) {
            arr[i] = new String(String.valueOf(DB_DATA[i % DB_DATA.length])).intern();
        }
        System.out.println((System.currentTimeMillis() - t) + "ms");
        System.gc(); 
    }
```

以上程序会有很多重复的相同的字符串产生，但是这些字符串的值都是只有在运行期才能确定的。[所以，只能我们通过intern显示的将其加入常量池，这样可以减少很多字符串的重复创建。]()

Jdk6 中常量池位于PremGen区，大小受限，不建议使用String.intern()方法，不过Jdk7 将常量池移到了 Java堆区，大小可控，可以重新考虑使用String.intern()方法，[但是由对比测试可知，使用该方法的耗时 不容忽视，所以需要慎重考虑该方法的使用;]()

`String.intern()`方法主要适用于程序中需要保存有限个会被反复使用的值的场景，这样可以减少内 存消耗，同时在进行比较操作时减少时耗，提高程序性能。

### 搞懂JVM是如何运行字节码程序

#### 字节码指令集

ava虚拟机的指令由一个字节长度的、代表着某种特定操作含义的数字(称为[操作码]()，Opcode)以及 跟随其后的零至多个代表此操作所需参数(称为[操作数]()，Operands)而构成。

比如:

| 字节码 | 助记符      | 指令含义             |
| ------ | ----------- | -------------------- |
| 0x00   | nop         | 什么都不做           |
| 0x01   | aconst_null | 将null推送至栈顶     |
| 0x02   | iconst_m1   | 将int型-1推送至栈顶  |
| 0x03   | iconst_0    | 将int型0推送至栈顶   |
| 0x04   | iconst_1    | 将int型1推送至栈顶   |
| 0x05   | iconst_2    | 将int型2推送至栈顶   |
| 0x06   | iconst_3    | 将int型3推送至栈顶   |
| 0x07   | iconst_4    | 将int型4推送至栈顶   |
| 0x08   | iconst_5    | 将int型5推送至栈顶   |
| 0x09   | lconst_0    | 将long型0推送至栈顶  |
| 0x0a   | lconst_1    | 将long型1推送至栈顶  |
| 0x0b   | fconst_0    | 将float型0推送至栈顶 |
| 0x0c   | fconst_1    | 将float型1推送至栈顶 |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |
|        |             |                      |



#### 基本数据类型

1、除了long和double类型外，每个变量都占局部变量区中的一个变量槽(slot)，而long及double会占 用两个连续的变量槽。

2、大多数对于boolean、byte、short和char类型数据的操作，都使用相应的int类型作为运算类型。

## 方法调用和方法执行

### 方法执行

以下面代码为例看一下执行引擎是如何将一段代码在执行部件上执行的，如下一段代码:

```java
public  class Math{
     public static void main(String[] args){
        int a = 1 ;
				int b = 2;
				int c = (a+b)*10;
     } 
}
```

其中main的字节码指令如下:

> 偏移量 		指令 			说明

> 0:  				iconst_1 	常数1入栈

> 1: 				 istore_1 	 将栈顶元素移入本地变量1存储

> 2:  				iconst_2 	 常数2入栈
>
> 3:  				istore_2 	  将栈顶元素移入本地变量2存储
>
> 4: 				 iload_1 		本地变量1入栈
>
> 5:  				iload_2 		本地变量2入栈
>
> 6: 				 iadd       		弹出栈顶两个元素相加
>
> 7: 				 bipush 10  	将10入栈
>
> 9:  				imul        		栈顶两个元素相乘
>
> 10:  			 istore_3   		栈顶元素移入本地变量3存储
>
> 11:  			 return    		 返回

```
对应到执行引擎的各执行部件如图:
```

在开始执行方法之前，PC寄存器存储的指针是第1条指令的地址，局部变量区和操作栈都没有数据。从 第1条到第4条指令分别将a、b两个本地变量赋值，对应到局部变量区就是1和2分别存储常数1和2，如 图:

第5条和第6条指令分别是将两个局部变量入栈，然后相加，如图:



对应到执行引擎的各执行部件如图:

![image-20191221225252955](/Users/lee/Library/Application Support/typora-user-images/image-20191221225252955.png)

[在开始执行方法之前，PC寄存器存储的指针是第1条指令的地址，局部变量区和操作栈都没有数据。]()从 第1条到第4条指令分别将a、b两个本地变量赋值，对应到局部变量区就是1和2分别存储常数1和2，如图:

![image-20191221225323407](/Users/lee/Library/Application Support/typora-user-images/image-20191221225323407.png)

第5条和第6条指令分别是将两个局部变量入栈，然后相加，如图:

![image-20191221225340091](/Users/lee/Library/Application Support/typora-user-images/image-20191221225340091.png)

1先入栈2后入栈，栈顶元素是2，第7条指令是将栈顶的两个元素弹出后相加，结果再入栈，如图:

![image-20191221225358015](/Users/lee/Library/Application Support/typora-user-images/image-20191221225358015.png)

可以看出，变量a和b相加的结果3存在当前栈顶中，接下来第8条指令将10入栈，如图:

![image-20191221225414105](/Users/lee/Library/Application Support/typora-user-images/image-20191221225414105.png)

当前PC寄存器执行的地址是9，下一个操作是将当前栈的两个操作数弹出进行相乘并把结果压入栈中， 如图:

![image-20191221225437145](/Users/lee/Library/Application Support/typora-user-images/image-20191221225437145.png)

第10条指令是将当前的栈顶元素存入局部变量3中，如图:

![image-20191221225455261](/Users/lee/Library/Application Support/typora-user-images/image-20191221225455261.png)

第10条指令执行完后栈中元素出栈，出栈的元素存储在局部变量区3中，对应的是变量c的值。最后一条 指令是return，这条指令执行完后当前的这个方法对应的这些部件会被JVM回收，局部变量区的所有值 将全部释放，PC寄存器会被销毁，在Java栈中与这个方法对应的栈帧将消失。

