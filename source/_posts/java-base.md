---
title: Java基础知识
date: 2016-10-12 12:24:37
tags: [Java, Interview]
---

# Java 基础知识

## 4.1 基本概念

1.  Java 语言优点： - 1).Java 语言为**纯面向对象语言**，编写程序更为容易 - 2).**平台无关性**，可以做到一次编译，到处运行（Java 是解释型语言编译器把 Java 代码变成中间代码，然后在 Java VM 上解释执行，中间代码与平台无关） - 3).Java 内置很多的类库，简化了程序设计工作（提供**多线程支持，网络通信，垃圾回收**……） - 4).提供了**对 Web 应用开发支持**（Applet，Servlet，JSP，Socket，RIM） - 5).具有**较好的安全性和健壮性** - 6).去除 C++难以理解，容易混淆的特性
<!--more-->
2.  Java 与 C/C++区别

    - Java 与 C++都是面向对象语言，都使用面向对象思想（封装，继承，多态）
    - Java 是**解释型语言**（过程：源代码经过 Java 编译器编译成字节码，然后由 JVM 执行）
    - Java**没有指针概念，使程序更加安全**
    - Java 可以**自动回收垃圾**

3.  public static void main(String[][1] args)方法

    - 这是程序的入口方法，JVM 在执行程序时，会先找到 main 方法，public 表示任何一个类或者对象都可以访问这个方法，static 表示 main 方法是静态的
    - 每个类都可以定义 main 方法，但只是与文件名相同的 public 修饰的类中的方法才可以作为程序入口

4.  实现在 main 方法之前输出“Hello world”

    - main 方法是程序入口方法，**在程序运行时最先加载；但是不一定是最先执行的**
    - 使用静态代码块可以实现

5.  Java 程序初始化顺序

    - Java 程序初始化一般遵循三个原则（优先级递减）
    - 1. 静态对象（变量）优先于非静态对象（变量）初始化
    - 2. 父类优先于之类进行初始化
    - 3. 按照成员变量定义顺序进行初始化

6.  Java 作用域

    - 声明在不同地方的变量具有不同作用域（局部变量、全局变量），在 Java 中作用域由花括号决定（可见性与生命周期）
    - 成员变量和方法可见性

    | 作用域与可见性 | 当前类 | 同一 package | 子类 | 其他 package |
    | -------------- | ------ | ------------ | ---- | ------------ |
    | public         | ✔️     | ✔️           | ✔️   | ✔️           |
    | protected      | ✔️     | ✔️           | ✔️   | ❌           |
    | default        | ✔️     | ✔️           | ❌   | ❌           |
    | private        | ✔️     | ❌           | ❌   | ❌           |

7.  Java 文件可以定义多个类，但是最多只有一个能被 public 修饰，并且这个类必须与文件名相同

    ```java
    // Derived.java
    class Base{
        public void print() {
            System.out.println("Base");
        }
    }
    public class Derived extends Base {
        public static void main(String[] args) {
            Base b = new Derived();
            b.print();
        }
    }
    // 使用java Derived.java指令编译会生成两个字节码文件Base.class和Derived.class
    // java Derived指令执行代码，输出：Base
    ```

8.  构造函数：是一种特殊函数，用来在对象实例化的时候初始化对象的成员变量。Java 中它具有以下特点：

    - 1）构造函数必须与类名相同，并且不能有返回值（返回值也不能是 void）
    - 2）每个类可以呢有多个构造函数，当开发人员没有提供构造函数时，编译器在把源代码编译成字节码的过程中提供默认无参构造函数，已有构造函数编译器则不会再提供默认构造函数
    - 3）构造函数可以有 0，1，1+个参数
    - 4）主要作用是完成对象初始化工作
    - 5）构造函数总是伴随 new 操作一起调用，不能由程序员直接调用，必须由系统调用；构造函数在对象实例化时被自动调用，且只运行一次；普通方法在程序执行到它时才会调用且可以多次调用
    - 6）构造函数不能被继承，所以它不能被覆盖但是可以重载
    - 7）子类可以通过 super 关键字显式调用父类构造函数；当父类没有提供无参构造函数时，子类的构造函数必须显式调用父类构造函数；当父类提供无参数构造函数时，子类可以不显式调用父类构造函数，默认就好调用父类无参数构造函数；对象实例化会先执行父类构造函数然后执行子类构造函数
    - 8）父类和子类都没有定义构造函数，编译器会给父类和子类分别都生成默认无参构造函数

9.  为什么 Java 部分接口没有任何方法

    - 1）Java 为克服单继承缺点，引入接口；接口是抽象方法定义的集合，只有方法定义没有实现，且所有方法均是抽象的；接口中成员作用域修饰符都是 public，接口中常量默认值使用 public static final 修饰；一个类可以实现多个接口，可用来间接达到多重继承目的
    - 2）有些接口没有任何方法即实现这些接口不需要重写任何方法，这些接口被叫做标识接口，仅充当标识作用，表明实现它的类属于一个特定类型（例如：Cloneable、Serializable）

    ```java
    // Test.java
    import Java.Util.ArrayList;

    interface Stuff{}
    // 矿石
    interface Ore exrends Stuff{}
    // 武器
    interface Weapon extends Stuff{}
    // 垃圾
    interface Rubbish extends Stuff{}

    // 金矿
    class Gold implements Ore {
        public String toString() {
            return "Gold";
        }
    }
    // 铜矿
    class Copper implements Ore {
        public String toString() {
            return "Copper";
        }
    }
    // 枪
    class Gun implements Weapon {
        public String toString() {
            return "Gun";
        }
    }
    class Stone implements Rubbish {
        public String toString() {
            return "Stone";
        }
    }

    public class Test {
        public static void main(String[] args) {
            Stuff[] s = {new Gold(), new Copper(), new Gun(), new Stone()};
            ArrayList<Stuff> list = collectStuff(s);
            System.out.println("The usefull Stuff collected is : ");
            for(int i = 0; i < list.size(); i++){
                System.out.println(list.get(i));
            }
        }

        public static ArrayList<Stuff> collectStuff(Stuff[] s) {
            ArrayList<Stuff> list = new ArrayList<Stuff>();
            for(int i = 0; i < s.length(); i++){
                if(!(s[i).instanceof Rubbish)
                    list.add(s[i]);
            }
            return list;
        }
    }
    ```

10. clone 方法作用：
11. 反射机制：它允许程序在运行时进行自我检查，同时也允许对其内部成员进行操作；由于反射机制能够实现在运行时对类进行装载因此可以增加程序灵活性；不恰当的使用也会影响系统性能

    反射机制主要功能：得到一个对象所属的类；获取一个类的所有成员变量和方法；在运行时创建对象；在运行时调用对象的方法

    ```java
    // 在运行时动态创建类的对象
    class Base{
        public void f() {
            System.out.println("Base");
        }
    }
    class Sub extends Base {
        public void f() {
            System.out.println("Sub");
        }
    }
    public class Test {
        public static void main(String[] args) {
            try {
            // 使用反射机制动态加载类
                Class c = Class.forName("Sub");
                Base b = (Base)c.newInstance();
                b.f();
            } catch(Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

    >     获取class类的方法：1）class.forName("类的路径");2）类名.class;3）实例.getClass();

12. package 作用：
    - 1）提供多层命名空间，解决命名冲突
    - 2）对类按功能进行分类，使项目结构清晰

## 4.2 面向对象技术

1.  面向对象与面向过程的区别：

    - 1）出发点不同。面向对象方法是用于符合常规思维逻辑的方式来处理客观世界的问题，强调把问题域的要领直接映射到对象与对象及对象之间的接口上；而面向过程方法则是强调过程的抽象化与模块化，它是以过程为中心构造或处理客观世界问题
    - 2）层次逻辑关系不同。
    - 3）数据处理方式与程序控制方式不同。
    - 4）分析设计与编码转换方式不同。

2.  面向对象特征：

    - 1）抽象：抽象是忽略一个主题中与当前目标无关的那些方面，以便充分注意与当前目标有关的方面；抽象包括过程和数据抽象
    - 2）继承：是一种联结类的层次模型，并且允许和鼓励类的重用，它提供一种明确表述共性的方法；对象的一个新类可以从现有的类中派生。新类继承原始类的特性
    - 3）封装：是指将客观事物抽象成类，每个类对自身的数据和方法实行保护，类可以吧自己的数据和方法让可信的类或者对象操作，对不可信的进行隐藏
    - 4）多态：允许不同类的对象对同一消息作出响应；多态包含参数化多态和包含多态；多态性语言具有灵活、抽象、行为共享等优势，很好解决了应用程序函数同名的问题

3.  面向对象开发优点：

    - 1）较高开发效率。
    - 2）保证软件的鲁棒性。面向对象的开发方法有很高的重用性，在开发的过程中可以重用已有的而且相关领域经过长期测试的代码
    - 3）保证软件的高可维护性。代码可读性高；设计模式使得代码结构清晰，并且面对需求变更的时候只需修改部分代码即可满足需求

4.  继承：是面向对象中非常重要的一个特性，通过继承，子类可以使用父类的一些成员变量与方法，从而提高代码复用性，提高开发效率。继承通过 extends 关键字实现。继承具有如下特性：

    - 1）Java 语言不支持多重继承，子类只能继承一个父类，但是可以通过接口的方式实现多重继承
    - 2）子类只能继承父类非 private 得成员变量与方法
    - 3）子类定义的成员变量与父类相同时，子类成员变量会覆盖父类成员变量
    - 4）子类中的方法与父类中的方法有相同的签名（方法名，参数个数与类型）子类将会覆盖父类的方法

5.  组合与继承的区别：继承（is-a）组合（has-a）

6.  多态实现机制：

    - 1）方法重载 overload：同一个类中有多个方法，方法有着不同的参数
    - 2）方法覆盖 override：子类方法覆盖父类方法

7.  重载和覆盖：overload 和 override 是 Java 多态性的不同表现形式

    - 1）重载是多态的一种表现，指一个类定义多个同名方法；重载通过不同方法参数区分的，不能通过访问权限、返回值类型、抛出异常类型区分
    - 2）覆盖是指子类函数覆盖父类函数，对一个方法进行重写以达到不同的作用；覆盖时必须有相同的方法名称和参数、相同返回值类型、抛出的异常也相同；另外父类中的方法是 private 的话，子类则只是定义了一个方法，并没有覆盖
    - 3）两者区别：覆盖是子类和父类之间的关系，属于垂直关系，重载是同一个类中的方法之间的关系，属于水平关系；重载要求参数列表不同，覆盖要求相同。。。

    ```java
    class Super {
        public int f() {
            return 1;
        }
    }
    public class SubClass extends Super {
        public static void main(String[] args){
            Super s = new SubClass();
            System.out.println(s.f());
        }
        public float f() {
            return 2f;
        }
    }
    // 编译错误。因为函数是不能通过返回值来区分
    ```

8.  抽象类与接口：

    - 1）相同点：都不能被实例化；接口的实现类或者抽象类的子类只有实现了接口或者抽象类中的方法才能被实例化
    - 2）不同点：

    | 接口                                                                                                                                   | 抽象类                                                                                                                                               |
    | -------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
    | 只有定义，方法不能在接口中实现                                                                                                         | 方法可以在抽象类中实现                                                                                                                               |
    | 接口需要实现（implements）一个类可以实现多个接口                                                                                       | 抽象类需要继承（extends）一个类只能继承一个抽象类                                                                                                    |
    | 接口强调功能实现，设计理念是”has-a”关系                                                                                                | 抽象类强调所属关系，设计理念是”is-a"关系                                                                                                             |
    | 接口中定义的成员变量默认为 public static final，只能含有静态的不能被修改的数据成员且必须赋初始值，所以成员方法都是 public、abstract 的 | 抽象类成员变量默认是 default，也可以被定义为 public，private，protected；抽象方法不能使用 private，static，synchronized，native 修饰且不能含有方法体 |
    | 接口常用于实现比较常用的功能，便于提后维护或者添加删除方法                                                                             | 抽象类更倾向于充当公共类的角色，不适合日后对代码进行修改                                                                                             |

9.  内部类：在类里面的类叫做内部类，外面就是外部类；共分为 4 种：静态内部类，成员内部类，局部内部类，匿名内部类

    ```java
    class outClass {
        // 静态内部类
        static class innerClass {}
    }
    class outClass {
        // 成员内部类
        class innerClass {}
    }
    class outClass {
        public void f() {
            // 局部内部类
            class innerClass {}
        }
    }
    public class MyFrame extends Frame {
        public MyFrame() {
            addWindowListener(new Windows=Adapter(){    // 匿名内部类
                // 方法。。。
            });
        }
    }
    ```

    >     匿名内部类：不能有构造函数；不能使用关键字class、extends、implements；不能定义静态成员、方法、类；匿名内部类不能是public、protected。private、static；只能创建匿名内部类的一个实例

10. 获取父类类名：通过调用 getClass().getName();

    ```java
    public class Test {
        public void test() {
            System.out.println(this.getClass().getName());
        }
        public static void main(String[] args) {
            new Test().test();
        }
    }
    // 输出：Test
    ```

    ```java
    class A {}

    public class Test extends A {
        public void test() {
            System.out.println(super.getClass().getName());
        }
        public static void main(String[] args) {
            new Test().test();
        }
    }
    // ？输出：Test
    // Java中任何类都继承Object类，getClass()方法在Object中被定义为final与native，子类不能覆盖该方法
    // 所以this.getClass()与super.getClass最终调用的都是Object中的getClass()方法
    // Object的getClass()方法定义是返回此Object的运行时类
    ```

    ```java
    class A {}

    public class Test extends A {
        public void test() {
            System.out.println(this.getClass().getSuperclass().getName());
        }
        public static void main(String[] args) {
            new Test().test();
        }
    }
    // 输出：A
    // 使用反射机制可以得到
    ```

11. this 与 super
    - 1）this 用来指当前实例的对象，它的一个非常重要的作用是区分对象的成员变量与方法的形参
    - 2）super 可以用来访问父类的方法或者成员变量（当子类中的成员方法或者变量与父类相同时会覆盖父类的方法或者成员变量）

```java
class Base {
    public void f() {
        System.out.println("Base:f()");
    }
}
class Sub extends Base {
    public void f() {
        System.out.println("Sub:f()");
    }
    public void subf() {
        f();
    }
    public void basef() {
        super.f();
    }
}
public class Test {
    public static void main(String[] args) {
        Sub s = new Sub();
        s.subf();
        s.basef();
    }
}
```

## 4.3 关键字

1. 变量命名规则：变量名、函数名、数组名统称为标识符，它只能由字母（a~z, A~Z）/数字（0~9）/下划线（\_）/$组成，且第一个不能是数字，也不能含有换行符、空格、制表符、Java 保留的字符
2. break、continue、return：
3. final、finally、finalize：

   - 1）final 用于声明属性、方法、类；分别表属性不可变（引用不可变）、方法不可覆盖、类不可被继承
   - 2）finally 作为异常处理的一部分，只能使用在 try/catch 语句中，表这段语句最终一定被执行，常用语释放资源
   - 3）finalize 是 Object 类的一个方法，

4. assert：assert 断言是作为软件调试的一种方法，提供在代码中进行正确性检查的机制；主要作用是对一个 boolean 表达式进行检查，正确运行程序必须保证 boolean 为 true，若为 false 系统需要提供警告信息并退出
5. static 关键字作用：

   - 1）为特定数据类型或者对象分配单一的存储空间，而与创建对象的个数无关
   - 2）在不创建对象时就可以直接通过调用方法或者使用类的属性
   - 3）static 主要使用在成员变量、成员方法、代码块、内部类

6. switch：switch 语句用于多分支选择，switch(expr)中 expr 只能是枚举常量或者整数表达式

7. volatile 作用：是一个类型修饰符，被设计用来修饰不同线程访问和修改的变量，被 volatile 修饰的变量，系统每次调用到它都是从缓存当中提取

8. instanceof 作用：instanceof 是 Java 语言中一个二次元运算符，它的作用是判断一个引用类型的变量所指向的对象是否是一个类或接口、抽象类、父类的实例。

## 4.4 基本类型运算

1. 基本数据类型：byte-8bit、short-16bit、int-32bit、long-64bit、float-32bit、double-64bit、char-16bit、boolean

2. 不可变类：当创建这个类的实例后，就不允许修改它的值
3. 值传递与引用传递：

   - 1）值传递：在方法调用中，实参会把值传递给形参，形参只是用实参的值初始化一个临时存储的单元，所以形参和实参虽有相同的值，却有不同的存储单元，形参改变不影响实参的值
   - 2）引用传递：在方法调用中传递的是对象（对象的地址）这时形参与实参的对象指向同一块存储单元，修改形参会印象实参

   ```java
   public class Test {
       public static void testPassParameter(StringBuffer ssq, int n){
           ss1.append("World");
           n = 9;
       }

       public static void main(String[] args) {
           int a = 1;
           StringBuffer sb = new StringBuffer("Hello");
           testPassParameter(sb, a);
           // 打印sb和a
       }
   }
   ```

4. 不同数据类型转换规则
5. 强制类型转换注意事项
6. 运算符优先级：

   | 优先级 | 运算符                        |
   | ------ | ----------------------------- | -------------------- | --- |
   | 1      | . () []                       |
   | 2      | +正 -负 ++ — ~ !              |
   | 3      | \* / %                        |
   | 4      | + -                           |
   | 5      | << >>无符号右移 >>>有符号右移 |
   | 6      | < <= > >= instanceof          |
   | 7      | == !=                         |
   | 8      | &                             |
   | 9      |                               |                      |
   | 10     | ^                             |
   | 11     | &&                            |
   | 12     |                               |                      |     |
   | 13     | ?:                            |
   | 14     | = += -= \*= /= &= =           | = ^= ~= <<= >>= >>>= |

7. Math 类中的关键方法：

   - 1）round 方法表四舍五入，返回值为 int 型；Math.round(11.5)=12,Math.round(-11.5)=11
   - 2）ceil 向上取整，返回值 double 型，正数小数入，负数小数舍
   - 3）floor 向下取整

8. i++与++i：前者是郑旭执行完自增，后者是先自增然后执行
9. （>>）有符号右移；（>>>）无符号右移，有符号右移在执行时，正数高位补 0，负数高位补 1，无符号则是都补 0
10.

## 4.5 字符串与数组

1. 字符串创建与存储机制

   - 1）对于 String s1 = new String("abc");和 String s2 = new String("abc");存在两个引用对象，它们在内存中的地址不同
   - 2）对于 String S1 = "abc";和 String S2 = "abc";在 JVM 中存在字符串常量池，保持很多的 String 对象，并且可以被共享使用

2. ==、equals、hashCode 的区别

   - 1）==用来比较两个变量的值是否相等
   - 2）equals()是 Object 类提供的基本方法，，每个类都继承自 Object 类，每个对象都有 equals()方法，比较的是引用（Set 中元素不重复使用 equals()方法）
   - 3）hashCode()方法是从 Object 类中继承来的，也用来鉴定两个对象是否相等，此方法返回对象在内存中地址转换成的一个 int 型的值，如果没有重新 hashCode()方法，任何对象的 hashCode()方法都是不等

3. String,StringBuffer,StringBuilder,StringTokenizer

   - String,StringBuffer,StringBuilder,StringTokenizer 都可以对字符串操作，character 用于单个字符，String 用于字符串操作，属于不可变类；而 StringBuffer 也是用于字符串操作但是属于可变类
   - String 是不可变类，对象一旦被创建其值不可变而 StringBuffer 可以改变值
   - 实例化 String 时可以使用构造函数 String s = new String("qwe");或者 String s = "qwe";

   ```java
   String s = "hell0";
   s += "weico";
   // 等价于
   StringBuffer sb = new StringBuffer(s);
   s.append("weico");
   s = sb.toString();
   ```

   - StringBuilder 也可以修改字符串，都是字符串缓冲区，但是 StringBuilder 不是线程安全的，如果在单线程中使用字符串缓冲区 StringBuilder 效率高一些。效率 StringBuilder>StringBuffer>String
   - StringTokenizer 是用来分割字符串的

4. 数组是一个对象，数组是指具有相同类型的数据集合，它们一般具有固定长度，在内存中占据连续的空间
5. 数组初始化方式：

   - 一维数组：type arrayName[]或者 type[] arrayName,type 可以是基本数据类型也可以是类，Java 创建数组后会根据数组存放类型进行初始化值（int 初始化为 0，对象初始化为 null）
   - 二维数组：type arrayName[][]或者 type[][] arrayName 或者 type[] arrayName[]

6. length 属性与 length()方法：length 可以获取数组长度，length()获取字符串长度，另外 size()是计算泛型集合有多少个元素

## 4.6 异常处理

1. finally 块中代码什么时候执行：（一定会被执行）
2. 异常处理原理：异常是指程序运行时，所发生的非正常或者错误，当程序违语义规则 jvm 将会出现错误表示一个异常并抛出，可以 catch 程序块捕获
3. 运行时异常和普通异常：
   - Java 提供两种错误的异常类 Error 和 Exception，且拥有共同父类 Throwable；Error 不可恢复，Exception 可恢复
   - 检查异常：是程序中最常碰到的异常，所有继承自 Exception 的异常除了运行时异常就是检查异常。比如 IO/SQL 异常
   - 运行时异常，编译器没有强制对其进行捕获处理，由 JVM 来处理，如 NullPointException 异常，ClassCastException 类型转换异常，ArrayIndexOutOfBounds 数组越界异常，StoreException，BufferOverflowException

## 4.7 输入输出流

1. Java IO 流实现机制

   - 输入和输出都被成为抽象的流，流可以看作有序的字节集合
   - 流的本质是数据传输，根据处理数据类型不同可分为字节流和字符流
   - 字节流以字节 8bit 为单位，包含抽象类 InputStream 和 OutputStream；字符流以字符 16bit 为单位，包含 Reader 和 Writer
   - 区别在于字节流处理数据不会用到缓存

2. 管理文件和目录的类：File

   | 方法              | 作用                                             |
   | ----------------- | ------------------------------------------------ |
   | File(String path) | 根据制定的路径创建 File 对象                     |
   | createNewFile()   | 若目录或文件存在返回 false，否则创建文件或文件夹 |
   | delete()          | 删除文件或文件夹                                 |
   | isFile()          | 判断是否是文件                                   |
   | isDirectory()     | 判断是不是文件夹                                 |
   | listFiles()       | 若对象代表目录，则返回目录中所有文件的 File 对象 |
   | mkdir()           | 创建目录                                         |
   | exist()           | 是否存在                                         |

3. Java Socket：也称为套接字，在 Java 中分为：面向连接的 Socket 通信协议 TCP，面向无连接的 UDP
   - 基于 TCP 的通信过程：首先 Server 端 Listen 指定某个端口是否由连接请求，client 向 server 发出连接，最后 server 返回接受信息
   - Socket 生命周期：打开 Socket、使用 Socket 收发数据，关闭 Socket
4. JavaNIO
5. Java 序列化：Java 提供两种对象持久化方式，分别是序列化和外部序列化
   - 1）序列化 Serialization；序列化是一种将对象以一连串的字节描述的过程，用于解决在对对象进行读写操作时所引发的问题，可将对象的状态下载流里进行网络传输，或者保存到文件/数据库，并在需要时将流读取出来重新构造一个相同对象
   - 2）实现序列化的类都必须实现 Serializable 接口（位于 java.lang 包），里面没有任何方法
   - 3）序列化特点：如果一个类能被序列化，其子类也可以被序列化；由于 static 表类的成员是静态的，transient 表对象的临时数据，因此被这两个关键字声明是不能够序列化的

## 4.8 java 平台内存管理

1. Java 平台独立性：中间码，虚拟机将中间码翻译成硬件平台能够执行的代码（.class 文件有 JVM 执行）-动态解释型语言
2. Java 平台特效：JVM+JavaAPI
3. JVM 加载 class 文件机制：类加载分为隐式和显示，隐式加载是指程序使用 new 的方式创建对象，会隐式的调用类的加载器，把对应的类加载到 jvm 中；显示加载是指直接通过 class.forName()把所需要的类加载到 jvm
4. GC 垃圾回收：主要作用回收程序中不再使用的内存，它需要完成三项任务：内存分配，确保被引用的对象的内存不被错误的回收，回收不再被引用的对象

   - 1）开发人员可以从复杂的释放内存工作中解放出来，提高了开发人员的效率
   - 2）屏蔽释放内存的方法，避免开发人员错误的操作内存而导致程序崩溃，保证程序稳定性
   - 3）对于对象若没有任何变量去引用它，那么该对象将不可能被程序访问，因此可视为垃圾信息
   - 4）对于垃圾回收器，它使用有向图来记录和管理堆内存中所有对象
   - 5）常用垃圾回收算法：引用计数-Reference Counting Collector，追踪回收 Tracing Collector，压缩回收 Comparing Collector，赋值回收 Coping Collector，按代回收 Generational Collector

5. java 是否存在内存泄漏
   - 内存泄漏是指不在被程序对象使用的或者变量还在内存中占有一定空间
   -
6. Java 中堆和栈
   - 基本数据类型和对象的引用变量，其内存都分配在栈上，变量出了作用域就会被释放
   - 引用类型的变量，内存分配在堆上或者常量池中（如 String 常量和基本数据类型的常量）

## 4.9 容器

1. Java Collections 框架

   - Java collections 框架包含许多集合接口和这些接口的实现类和操作他们的算法（排序，查找，反转，替换，复制，取最小最大元素），主要提供 List，Queue，Set，Stack，Map，前四个都继承自 Collection 接口
   - Set：数学意义上的集合，主要特点元素不能重复，每个元素都必须定义 equals 方法确保唯一
   - List：有序 collection，它按对象进入的顺序保存，可保存重复对象
   - Map：提供从键映射到值得数据结构，用于保存键值对，值可以重复，键是唯一。Java 有许多实现该接口的类：HashMap，TreeMap，LinkedHashMap，WeakHashMap，IdentityHashMap

2. 迭代器 Iterator：遍历并选择序列中的对象，提供一种访问一个容器对象中的所有元素，而又不暴露该对象的内部细节方法，使用迭代需要注意

   - 1）使用容器的 iterator()方法返回一个 Iterator，然后通过 Iterator 的 next()方法返回第一个元素
   - 2）使用 Iterator 的 hasNext(）方法判断容器中是否还有元素，若有则使用 next()方法获取下一个元素
   - 3）通过 Iterator 的 remove()方法删除迭代器返回的元素

   ```java
   public class IteratorTest {
       public static void main(String[] args) {
           List<String> ll = new LinkedList<String>();
           ll.add("first");
           ll.add("second");
           ll.add("third");
           ll.add("fourth");
           for (Iterator<String> iter  = ll.iterator(); iter.hasNext();) {
               String s = iter.next();
               System.out.println(s);
           }
       }
   }
   ```

3. ArrayList，Vector，LinkedList

   - ArrayList 和 Vector 都是基于存储元素 Object[] array 来实现，他们会在内存中开辟一块连续空间来存储，数据存储是连续的，所以支持使用序列号来访问元素，查找速度较快，插入数据较慢；两者都有初始化的容量大小，为了提高程序效率 vector 每次扩充 2 倍，ArrayList 扩充 1.5 倍
   - LinkedList 是采用双向列表来实现的，对数据的索引需要从表头开始遍历，随机访问效率低，但是插入元素时不需要对数据进行移动，插入效率高

4. HashMap，HashTable，TreeMapWeakHashMap

   - Java 为数据结构的映射定义了一个接口 java.util.Map，它包括 3 个实现类 HashMap，HashTable，TreeHash
   - HashMap 是最常用的 Map，它根据键的 HashCode 存储数据，由键可以获取值，具有访问速度快。HashMap 和 HashTable 都使用 hash 法进行索引。不同之处 1）HashMap 是 HashTable 的轻量级实现（非现场安全的实现），都完成了 Map 接口，HashMap 允许空键值（由于键唯一性，最多允许一条记录键为 null，不允许多条记录的值为空）HashTable 不允许；2）HashMap 把 HashTable 的 contains 方法去掉了，改为 containsvalue 和 containsKey；3）HashTable 的方法是线程安全的，HashMap 不支持线程同步，所以是现场不安全的，需要开发人员提供额外的同步机制；4）HashTable 使用 Enumeration，HashMap 使用 Iterator；5）HashTable 中 hash 数组默认大小是 11，增加方式是原来大小\*2+1，而 HashMap 中 hash 数组的默大小是 16，而且一定是 2 的指数

5. 使用自定义类型作为 HashTable 和 HashMap 需要注意
6.

## 4.10 多线程

1. 线程

   - 线程是程序在执行过程中，能够执行程序代码的一个执行单元，共有四种状态：运行，就绪，挂起，结束
   - 多线程可减少程序执行时间，提供效率
   - 线程切换开销更小

2. 同步和异步
   -
3. 实现 Java 多线程方法

   - 1）继承 Thread 类，重写 run()方法

   ```java
   class MyThread extends Thread {
       public void run() {
           System.out.println("Thread body");
       }

       public class Test {
           public static void main(String[] args) {
               MyThread t = new MyThread();
               t.start();//开启线程，不立即执行多线程代码，而是使线程变为可运行状态
           }
       }
   }
   ```

   - 2）实现 Runnable 接口

   ```java
   // 自定义类并实现Runnable接口，实现run()方法
   // 创建Thread对象，实现Runnable接口的对象作为参数实例化该Thread对象
   // 调用Thread的start()方法
   class MyThread implements Runnable {
       public void run() {
           System.out.println("Thread body");
       }
       public class Test {
           public static void main(String[] args) {
               MyThread t = new MyThread();
               Thread thread = new Thread(t);
               t.start(); //开启线程
               // 最终都是通过Thread的对象的API来控制线程
           }
       }
   }
   ```

   - 3)实现 Callable 接口，重写 call()方法；Callable 在任务结束后提供一个返回值，call()方法可以抛出异常，

4. run()方法与 start()方法
   - 系统通过调用 Thread 的 start 方法启动一个线程，此时处于就绪态，通过调用 run 方法来完成实际操作
5. 多线程同步方法

   - 1）synchronized 关键字：Java 中每个对象都有一个对象锁与之相关联，该锁表明对象在任何时候只允许被一个线程锁拥有，当一个线程调用对象的一段 synchronized 代码时，需要先获得这个锁，然后去执行相关代码，释放锁。synchronized 主要有两种用法：synchronized 方法和 synchronized 块
     - synchronized 方法：public synchronized void mutiThreadAccess();
     - synchronized 块：可以把任意代码段声明为 synchronized，也可以指定上锁的对象 synchronized(syncObject){}
   - 2）wait()与 notify()：使用 synchronized 修饰某个共享资源师，当线程 A1 在执行 synchronized 代码，A2 也在等同一对象的同一个 synchronized 代码才能继续执行，这时需要 wait 和 notify 方法；在 synchronized 被执行时，线程可以调用对象的 wait 方法
   - 3)lock()：以阻塞的方式获取锁，若获取到了锁，立即返回；若别的线程持有锁，当前线程等待，直到获取锁后返回。

6. sleep()方法与 wait()方法区别：sleep()是使线程暂停执行一段时间

   - 1）原理不同：sleep()方法是 Thread 类的静态方法，是线程用来控制自身流程的，它会使此线程暂停执行一段时间
   - 2）对锁的处理机制不同
   - 3）使用区域不同：wait 必须使用在同步方法或者同步代码块；sleep 必须抛出异常

7. 终止线程方法：stop()和 suspend()
8. synchronized 与 lock
9. 守护线程：服务进程/后台进程
10. join()方法：让调用该方法的线程在执行完 run 方法之后，再执行 join 方法后面的代码

## 4.11 Java 数据库操作

1. 通过 JDBC 连接数据库：用于在 Java 程序中实现数据库操作，提供了执行 SQL 语句，访问各种数据库的方法，并为不同的数据库提供接口；java.sql 包含了 JDBC 操作数据库的所有类；通过 JDBC 访问数据库步骤：
   - 1）加载 jDBC 驱动器，将数据库的 JDBC 加载到 classpath 中（JavaEE web 需要把目标数据库的 JDBC 驱动复制到 WEB-INF/lib）
   - 2）加载 JDBC 驱动，并注册到 DriveManager 中；一般使用反射 class.forName(String driveName)
   - 3）建立数据库连接，取得 Connection 对象一般通过 DriveManager.getConnection(url, username, pw)
   - 4）建立 Statement 对象或者 PrepareStatement 对象
   - 5）执行 SQL 语句
   - 6）访问结果集 ResultSet 对象
   - 7）依次将 ResultSet、Statement、PrepareStatement、Connection 关闭，释放掉所有占有的资源（JDBC 在底层通常都是通过网络 IO 实现 SQL 命令）
