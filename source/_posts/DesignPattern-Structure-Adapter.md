---
title: 《Android 源码设计模式解析与实战》读书笔记 - 适配器模式
date: 2017-12-10 11:18:27
tags: [Java, Android, DesignPattern]
---

# 《Android 源码设计模式解析与实战》读书笔记 - 适配器模式

> 适配器模式把一个类的接口变换成客户端所期待的另外一个接口，从而使原本因接口不匹配而无法一起工作的两个类能够在一起工作
> 适配器分为类适配器和对象适配器两张

<!--more-->

## 使用场景

1. 系统需要使用现有的类，而此类的接口不符合系统的需要，即接口不兼容
2. 想要建立一个可重复利用的类，用于与一些彼此之间没有太大关联的类一起工作
3. 需要一个统一的输出接口，而输入端的类型不可预知

## 类适配器

## UML 类图

![类适配器](http://blog-1251678165.coscd.myqcloud.com/2018-03-14-Adapter-of-class.uml.png)
类适配器是通过实现 Target 接口以及继承 Adaptee 类来实现接口转换，如目标接口需要 operator2(), 但是 Adaptee 对象只有 operator3()，因此就出现了不兼容情况。此时可以通过 Adapter 实现一个 operator2()将 Adaptee 的 operator3()转换为 Target 需要的 operator2()

角色介绍:

- Target: 目标角色，也就是接口所期待得到的接口。（类适配器模式，此目标不可以是类）
- Adaptee: 现在需要适配的接口
- Adapter: 适配器角色，核心类，把不兼容的接口转换成目标所需接口，这一角色必须是具体类

### 示例

```java
/**
 * Created by Weicools on 2017/12/10.
 * <p>
 * desc: 笔记本电脑一都是需要5V电压，而生活电压一般都是220V，这个时候就产生了不兼容情况
 * 需要一个适配器来把220V电压转换成5V电压
 * 对应就是Target需要得到5V电压，而Adaptee正常输出220V，Adapter类则是继承Adaptee实现Target
 * 将220V转为5V，解决接口不兼容问题
 */
public class Test {
    public static void main(String[] args) {
        VoltAdapter adapter = new VoltAdapter();
        System.out.println("输出电压: " + adapter.getVolt5());
    }
}

/**
 * Created by Weicools on 2017/12/10.
 * <p>
 * desc: Target 角色
 */
public interface FiveVolt {
    int getVolt5();
}

/**
 * Created by Weicools on 2017/12/10.
 * <p>
 * desc: Adaptee 角色
 */
public class Volt220 {
    public int getVolt220() {
        return 220;
    }
}

/**
 * Created by Weicools on 2017/12/10.
 * <p>
 * desc: Adapter 角色
 */
public class VoltAdapter extends Volt220 implements FiveVolt {
    @Override
    public int getVolt5() {
        return 5;
    }
}
```

## 对象适配器

### UML 类图

![](http://blog-1251678165.coscd.myqcloud.com/2018-03-14-Adapter-of-object.uml.png)
与类适配器一样，对象适配器模式把被适配类的 API 转为目标类的 API，与类适配器模式不同的是：对象适配器模式不是使用继承关系连接到 Adaptee，而是使用代理关系，UML 类图如下:

```java
// 基于类适配器的示例修改Adapter
/**
 * Created by Weicools on 2017/12/10.
 * <p>
 * desc: Adapter 角色
 */
public class VoltAdapter implements FiveVolt {
    private Volt220 mVolt220;

    VoltAdapter(Volt220 volt220) {
        mVolt220 = volt220;
    }

    public int getVOlt220() {
        return mVolt220.getVolt220();
    }

    @Override
    public int getVolt5() {
        return 5;
    }
}

/**
 * Created by Weicools on 2017/12/10.
 * <p>
 * desc: 笔记本电脑一都是需要5V电压，而生活电压一般都是220V，这个时候就产生了不兼容情况
 * 需要一个适配器来把220V电压转换成5V电压
 * 直接将被适配的对象传到Adapter中，使用组合形式实现接口兼容效果
 * 比类适配器更加灵活，适配对象的方法也不会暴露出来
 */
public class Test {
    public static void main(String[] args) {
        VoltAdapter adapter = new VoltAdapter(new Volt220());
        System.out.println("输出电压: " + adapter.getVolt5());
    }
}
```

## Summary

1. 对象适配器直接将要适配的对象传到 Adapter 中，使用组合的形式实现接口兼容的效果，比类适配器更加灵活
2. 对象适配器还不会将被适配对象的方法暴露出来，而类适配器是继承被适配的类，不够灵活。
3. 实际开发中 Adapter 常用预进行不兼容类型的转换场景，还有一种就是输入有多种情况，而输出只有一种的时候，可以通过 Adapter 统一进行输出。
4. 尽可能使用对象适配器，多使用组合少使用继承。
