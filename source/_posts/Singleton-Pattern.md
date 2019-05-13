---
title: 设计模式之单例模式
date: 2016-10-18 09:37:48
tags: [Java, Design Pattern]
---

### 1.问题：
对于系统中的某些类来说，只有一个实例很重要，例如，一个系统中可以存在多个打印任务，但是只能有一个正在工作的任务；一个系统只能有一个窗口管理器或文件系统；一个系统只能有一个计时工具或ID（序号）生成器。如何保证一个类只有一个实例并且这个实例易于被访问呢？

### 2.模式定义
单例模式（Singleton Pattern）：确保一个类只有一个实例，而且自行实例化并向系统提供这个实例，提供全局访问方法。

实现思路：
    
* 在单例的类中设置一个private静态变量sInstance，sInstance类型为当前类，用来持有单例唯一的实例。
* 将（无参数）构造器设置为private，避免外部使用new构造多个实例。
* 提供一个public的静态方法，如getInstance，用来返回该类的唯一实例sInstance。

### 3.模式结构
UML类图：
![Singleton](http://ob9ev3u0o.bkt.clouddn.com/2016-10-18-Singleton.jpg)

### 4.时序图
![seq_Singleton](http://ob9ev3u0o.bkt.clouddn.com/2016-10-18-seq_Singleton.jpg)

### 5.七种实现方式
#### 懒汉式,线程不安全
 * 在多个线程密集调用getInstance时，存在创建多个实例的可能。
 * 比如线程A进入null == sInstance这段代码块，而在A线程未创建完成实例时，
 * 如果线程B也进入了该代码块，必然会造成两个实例的产生。

```java
/**
 * Created by weicools on 2016/10/18.
 * 
 * 1.0 懒汉式,线程不安全
 */
public class SingletonLazy {
    // 1.一个私有的指向自己的静态变量
    private static SingletonLazy instance = null;
    // 2.私有的构造方法,保证不能从外部创建对象
    private SingletonLazy() {}
    // 3.公开的静态工厂方法,返回该类的唯一实例(当发现没有实例没有初始化的时候才初始化)
    public static SingletonLazy getInstance() {
        if (instance == null) {
            instance = new SingletonLazy();
            System.out.println("创建SingletonLazy类的实例");
        } else {
            System.out.println("实例已经创建,不能再创建");
        }
        return instance;
    }

    public static void main(String[] args) {
        SingletonLazy s = new SingletonLazy();
        System.out.println(SingletonLazy.getInstance());
        System.out.println(SingletonLazy.getInstance());
        System.out.println(SingletonLazy.getInstance());
    }
}
```

#### 饿汉式,线程安全
* 不能完成懒加载

```java
/**
 * Created by weicools on 2016/10/18.
 * 
 * 2.0 饿汉式,线程安全
 */
public class SingletonHungry {
    // 1.私有的唯一的静态实例变量,在类加载的时候就创建好单例对象
    private final static SingletonHungry INSTANCE = new SingletonHungry();
    
    // 2.私有的构造函数,确保不能在类的外部访问该类的构造函数
    private SingletonHungry() {
        System.out.println("执行构造函数");
    }
    
    // 3.公开的静态工厂返回此类的唯一实例
    public static SingletonHungry getInstance() {
        return INSTANCE;
    }
}
```

#### 懒汉式，线程安全，多线程环境下效率不高
 * 使用synchrnozed修饰getInstance方法可能是最简单的一个保证多线程保证单例唯一性的方法。synchronized修饰的方法后，当某个线程进入调用这个方法，该线程只有当其他线程离开当前方法后才会进入该方法。
 * 所以可以保证getInstance在任何时候只有一个线程进入，但是会导致性能下降，而且getInstance是一个被频繁调用的方法。虽然这种方法能解决问题，但是不推荐。

```java
/**
 * Created by weicools on 2016/10/18.
 * 
 * 3.0 单例模式，懒汉式，线程安全，多线程环境下效率不高
 */
public class SingletonLazySynchronized {
    private static SingletonLazySynchronized instance = null;
   
    private SingletonLazySynchronized() {}
    
    public static synchronized SingletonLazySynchronized getInstance() {
        if (instance == null) {
            instance = new SingletonLazySynchronized();
        } else {
            System.out.println("实例已经创建,不能再创建");
        }
        return instance;
    }
}
```

#### 懒汉式，变种，线程安全
* 添加静态代码块,初始化实例

```java
/**
 * Created by weicools on 2016/10/18.
 * 
 * 4.0 单例模式，懒汉式，变种，线程安全
 */
public class SingletonLazyStatic {
    private static SingletonLazyStatic instance = null;
    
    static {
        instance = new SingletonLazyStatic();
    }
    
    private SingletonLazyStatic() {}
    
    public static SingletonLazyStatic getInstance() {
        return instance;
    }
}
```

#### 使用静态内部类，线程安全【推荐】
```java
/**
 * Created by weicools on 2016/10/18.
 * 
 * 5.0 单例模式，使用静态内部类，线程安全【推荐】
 */
public class SingletonStaticInner {
    private final static class SingletonHolder {
        private final static SingletonStaticInner INSTANCE = new SingletonStaticInner();
    }
    
    private SingletonStaticInner() {}
    
    public static SingletonStaticInner getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

#### 使用枚举方式，线程安全
```java
/**
 * Created by weicools on 2016/10/18.
 * 
 * 6.0 单例模式，使用枚举方式，线程安全
 */
public enum  SingletonEnum {
    INSTANCE;
    public void whateverMethod() {

    }
}
```

#### 使用双重校验锁，线程安全【推荐】
* 使用双重检查加锁，首先进入该方法时进行null == sInstance检查，如果第一次检查通过，即没有实例创建，则进入synchronized控制的同步块,并再次检查实例是否创建，如果仍未创建，则创建该实例。

* 双重检查加锁保证了多线程下只创建一个实例，并且加锁代码块只在实例创建的之前进行同步。如果实例已经创建后，进入该方法，则不会执行到同步块的代码。

* Volatile是轻量级的synchronized，它在多处理器开发中保证了共享变量的“可见性”。可见性的意思是当一个线程修改一个共享变量时，另外一个线程能读到这个修改的值。使用volatile修饰sInstance变量之后，可以确保多个线程之间正确处理sInstance变量。

```java
/**
 * Created by weicools on 2016/10/18.
 * 
 * 7.0 单例模式，使用双重校验锁，线程安全【推荐】
 */
public class SingletonVerifyLock {
    private volatile static SingletonVerifyLock instance = null;
    
    private SingletonVerifyLock() {}
    
    public static SingletonVerifyLock getInstance() {
        if (instance == null) {
            synchronized (SingletonVerifyLock.class) {
                if (instance == null) {
                    instance = new SingletonVerifyLock();
                }
            }
        }
        return instance;
    }
}
```

### 6.模式分析
单例模式的目的是保证一个类仅有一个实例，并提供一个访问它的全局访问点。单例模式包含的角色只有一个，就是单例类——Singleton。单例类拥有一个私有构造函数，确保用户无法通过new关键字直接实例化它。除此之外，该模式中包含一个静态私有成员变量与静态公有的工厂方法，该工厂方法负责检验实例的存在性并实例化自己，然后存储在静态成员变量中，以确保只有一个实例被创建

在单例模式的实现过程中，需要注意如下三点：
    - 单例类的构造函数为私有；
    - 提供一个自身的静态私有成员变量；
    - 提供一个公有的静态工厂方法

### 7.实例
在操作系统中，打印池(Print Spooler)是一个用于管理打印任务的应用程序，通过打印池用户可以删除、中止或者改变打印任务的优先级，在一个系统中只允许运行一个打印池对象，如果重复创建打印池则抛出异常。现使用单例模式来模拟实现打印池的设计

### 8.优点&缺点
1. 优点：
    - 提供了对唯一实例的受控访问。因为单例类封装了它的唯一实例，所以它可以严格控制客户怎样以及何时访问它，并为设计及开发团队提供了共享的概念
    - 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象，单例模式无疑可以提高系统的性能
    - 允许可变数目的实例。我们可以基于单例模式进行扩展，使用与单例控制相似的方法来获得指定个数的对象实例
2. 缺点：
    - 由于单例模式中没有抽象层，因此单例类的扩展有很大的困难
    - 单例类的职责过重，在一定程度上违背了“单一职责原则”。因为单例类既充当了工厂角色，提供了工厂方法，同时又充当了产品角色，包含一些业务方法，将产品的创建和产品的本身的功能融合到一起
    - 滥用单例将带来一些负面问题，如为了节省资源将数据库连接池对象设计为单例类，可能会导致共享连接池对象的程序过多而出现连接池溢出

### 9.使用环境
1. 系统只需要一个实例对象，如系统要求提供一个唯一的序列号生成器，或者需要考虑资源消耗太大而只允许创建一个对象。
2. 客户调用类的单个实例只允许使用一个公共访问点，除了该公共访问点，不能通过其他途径访问该实例。
3. 在一个系统中要求一个类只有一个实例时才应当使用单例模式。反过来，如果一个类可以有几个实例共存，就需要对单例模式进行改进，使之成为多例模式

### 参考
* [单例这种设计模式](https://droidyue.com/blog/2015/01/11/looking-into-singleton/)
* SwordOffer


