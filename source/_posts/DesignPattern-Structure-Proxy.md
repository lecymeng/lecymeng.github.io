---
title: 《Android 源码设计模式解析与实战》读书笔记 - 代理模式
date: 2017-03-17 10:16:27
tags: [Java, Android, DesignPattern]
---

# 《Android 源码设计模式解析与实战》读书笔记 - 代理模式
> 代理模式也称委托模式，是结构型设计模式之一。是应用广泛的模式之一。
> 定义：为其他对象提供一种代理以控制对这个对象的访问。

## 1.使用场景

当无法或不想直接访问某个对象或访问某个对象存在困难时可以通过一个代理对象来间接访问，为了保证客户端使用的透明性，委托对象与代理对象需要实现相同的接口。

## 2.UML类图

![Proxy](http://blog-1251678165.coscd.myqcloud.com/2018-03-17-Proxy-Pattern-1.png)
各个角色介绍：

```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 抽象主题类，主要职责是申明真实与代理主题的共同接口方法
 * 可以是抽象类也可以是接口
 */
public abstract class Subject {
    public abstract void visit();
}

/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 真实主题类，也称被代理类
 */
public class RealSubject extends Subject {

    @Override
    public void visit() {
        System.out.println("Real subject");
    }
}

/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 代理类，持有真实主题的引用，它实现的接口方法中调用真实主题的方法
 */
public class ProxySubject extends Subject {
    private Subject mRealSubject;

    ProxySubject(Subject subject) {
        mRealSubject = subject;
    }

    @Override
    public void visit() {
        System.out.println("Proxy subject");

        // 调用真实主题方法
        mRealSubject.visit();
    }
}

/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc:
 */
public class Client {
    public static void main(String[] args) {
        Subject subject = new ProxySubject(new RealSubject());

        subject.visit();
    }
}
```

## 3.简单示例

> 书中例子：以小民诉讼的流程举例。那么需要代理律师代理，诉讼简单流程：提交申请–>进行举证–>开始辩护–>诉讼完成。

### 诉讼接口类：
```java
/**
 * Created by Weicools on 2018/3/17.
 * <p>
 * desc: 诉讼接口类
 */
public interface ILawsuit {
    /**
     * 提交申请
     */
    void submit();

    /**
     * 禁行举证
     */
    void burden();

    /**
     * 开始辩护
     */
    void defend();

    /**
     * 诉讼完成
     */
    void finish();
}
```

### 具体诉讼人Weicools：
```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 具体诉讼人
 */
public class Weicools implements ILawsuit {
    @Override
    public void submit() {
        System.out.println("老板拖欠工资，特此申请仲裁！");
    }

    @Override
    public void burden() {
        System.out.println("这是合同书和过去一年的银行工资流水。");
    }

    @Override
    public void defend() {
        System.out.println("证据确凿，不需要做任何辩护！");
    }

    @Override
    public void finish() {
        System.out.println("诉讼成功，判决老板七天内结算工资！");
    }
}
```

### 代理律师：
```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 代理律师
 */
public class ProxyLayer implements ILawsuit {
    private ILawsuit mLawsuit;

    public ProxyLayer(ILawsuit lawsuit) {
        mLawsuit = lawsuit;
    }

    @Override
    public void submit() {
        mLawsuit.submit();
    }

    @Override
    public void burden() {
        mLawsuit.burden();
    }

    @Override
    public void defend() {
        mLawsuit.defend();
    }

    @Override
    public void finish() {
        mLawsuit.finish();
    }
}
```

### 开始仲裁：
```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc:
 */
public class Test {
    public static void main(String[] args) {
        ILawsuit weico = new Weicools();
        ILawsuit layer = new ProxyLayer(weico);

        layer.submit();
        layer.burden();
        layer.defend();
        layer.finish();
    }
}
```

### 结果：

```
老板拖欠工资，特此申请仲裁！
这是合同书和过去一年的银行工资流水。
证据确凿，不需要做任何辩护！
诉讼成功，判决老板七天内结算工资！
```

同样我们也可以代理其他人，只需要实现ILawsuit即可。上面的代理模式也叫静态代理，也就是在代码运行前代理类的class文件就已经存在。那么相反，当然也会有动态代理，通过反射机制动态生成代理类，也就是在编码阶段不需要知道代理谁。

## 4.动态代理实现
Java提供了一个便捷的动态代理接口InvocationHandler，我们来实现它：

```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 动态代理类
 */
public class DynamicPorxy implements InvocationHandler {
    // 被代理类的引用
    private Object mObject;

    public DynamicPorxy(Object o) {
        mObject = o;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        return method.invoke(mObject, args);
    }
}
```

这里我们通过invoke方法来调用具体的被代理方法，增加DynamicTest类测试：

```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 动态代理测试
 */
public class DynamicTest {
    public static void main(String[] args) {
        //构造出诉讼人小民
        ILawsuit weicools = new Weicools();

        //1.静态代理
        //构造一个代理律师，并将小民传递进去
        //ILawsuit lawyer = new Lawyer(xiaomin);

        //2.动态代理
        //构造一个动态代理
        DynamicProxy proxy = new DynamicProxy(weicools);

        //获取被代理类小民的ClassLoader
        ClassLoader loader = weicools.getClass().getClassLoader();

        //动态构造一个代理者律师
        ILawsuit lawyer = (ILawsuit) Proxy.newProxyInstance(loader, new Class[]{ ILawsuit.class }, proxy);

        //律师提交申请
        lawyer.submit();

        //律师进行举证
        lawyer.burden();

        //律师代替weicools辩护
        lawyer.defend();

        //完成诉讼
        lawyer.finish();
    }
}
```

结果不变，由此可以看出动态代理通过一个代理类来处理N多个被代理类，其实质是对代理者与被代理者解耦。相对而言静态代理则只能为给定接口下的实现类做代理，如果接口不同那么就需要重新定义不同的代理类，较为复杂，但是静态代理更符合面向对象原则。具体使用哪种方式，根据个人喜好。

## 5.Android源码中的代理模式实现

### ActivityManagerProxy代理类
ActivityManager是Android中管理和维护Activity的相关信息的类，为了隔离它与ActivityManagerService，有效降低二者的耦合，在这中间使用了ActivityManagerProxy代理类，其抽象主题类是IActivityManager接口，所有对ActivityManagerService的访问都转换成对代理类的访问，这样ActivityManager就与ActivityManagerService解耦了。
![](http://blog-1251678165.coscd.myqcloud.com/2018-03-17-Activity-Manaher.png)

## 6.总结
1. 优点
    * （1）对代理者与被代理者进行解耦。
    * （2）代理对象在客户端和目标对象之间起到一个中介的作用，这样可以起到对目标对象的保护。

2. 缺点
    * 基本没有缺点，真要说缺点就是设计模式的通病：对类的增加。

