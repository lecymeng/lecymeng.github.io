---
title: 《Android 源码设计模式解析与实战》读书笔记 - 桥接模式
date: 2017-03-18 10:16:27
tags: [Java, Android, DesignPattern]
---

> 桥接模式也称为桥梁模式，是结构型设计模式之一。桥接模式中体现了“单一职责原则”、“开闭原则”、“里氏替换原则”、“依赖倒置原则”等，同时它也是很实用的一种模式。
> **定义**：将抽象部分 与 实现部分 分离，使它们都可以独立地进行变化。

## 使用场景
* （1）如果一个系统需要在构建的抽象化角色和具体角色之间增加更多的灵活性，避免在两个层次之间建立静态的继承联系。
* （2）对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，也可以考虑使用桥接模式。
* （3）一个类存在两个独立变化的维度，且这两个维度都需要扩展。

## UML类图
![Bridge](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-Bridge.png)

* （1）`Abstraction`：抽象部分，该类保持一个对实现部分对象的引用，抽象部分中的方法需要调用实现部分的对象来实现，该类一般为抽象类。
* （2）`RefinedAbstraction`：优化抽象部分，抽象部分的具体实现，该类一般是对抽象部分的方法进行完善和扩展。
* （3）`Implementor`：实现部分。可以为接口或抽象类，其方法不一定要与抽象部分中的一致，一般情况下是由现实部分提供基本操作，而抽象部分定义的则是基于实现部分这些基本操作的业务方法。
* （4）`ConcreteImplementorA`、`ConcreteImplementorB`：实现部分的具体实现。完善实现部分中的方法定义的具体逻辑。

```Java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 实现部分的抽象接口
 */
public interface Implementor {
    /**
     * 实现部分的接口方法
     */
    void operationImpl();
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 实现部分的具体实现
 */
public class ConcreteImplementorA implements Implementor {
    @Override
    public void operationImpl() {
        // 实现部分的具体实现
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 抽象部分的抽象类
 */
public abstract class Abstraction {
    //实现部分的引用
    private Implementor mImplementor;

    /**
     * 通过实现部分的对象引用构造抽象部分的对象
     * @param implementor 实现部分对象引用
     */
    public Abstraction(Implementor implementor) {
        mImplementor = implementor;
    }

    /**
     * 调用实现部分的具体方法实现
     */
    public void operation() {
        mImplementor.operationImpl();
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 抽象部分的子类
 */
public class RefineAbstraction extends Abstraction {
    /**
     * 通过实现部分的对象引用构造抽象部分的对象
     *
     * @param implementor 实现部分对象引用
     */
    public RefineAbstraction(Implementor implementor) {
        super(implementor);
    }

    public void refineOperation() {
        //对Abstraction方法进行扩展
    }
}
```

## 简单实现

> 以去咖啡店喝咖啡为例，我们假定咖啡有大杯加糖、大杯不加糖、小杯加糖和小杯不加糖四种
> 这四种实质上就是两种变化：一是大杯小杯，二是加糖不加糖

### 给咖啡添加东西的抽象类：（Implementor）
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public abstract class CoffeeAdditives {
    /**
     * 需要往咖啡中加什么
     * @return 具体添加的东西
     */
    public abstract String addSomething();
}
```

### 加糖类实现：（ConcreteImplementorA）
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class AddSugar extends CoffeeAdditives {
    @Override
    public String addSomething() {
        return "加糖";
    }
}
```

### 原味类实现：（ConcreteImplementorB）
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class Ordinary extends CoffeeAdditives {
    @Override
    public String addSomething() {
        return "原味";
    }
}
```

### 咖啡类：（Abstraction）
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public abstract class Coffee {
    protected CoffeeAdditives mAdditives;

    public Coffee(CoffeeAdditives additives) {
        mAdditives = additives;
    }

    /**
     * 咖啡杯大小由子类决定
     */
    public abstract void makeCoffee();
}
```

### 大杯咖啡：（RefinedAbstraction）
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class LargeCoffee extends Coffee {
    public LargeCoffee(CoffeeAdditives additives) {
        super(additives);
    }

    @Override
    public void makeCoffee() {
        System.out.println("大杯的" + mAdditives.addSomething() + "咖啡");
    }
}
```

### 小杯咖啡：（RefinedAbstraction）
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class SmallCoffee extends Coffee {
    public SmallCoffee(CoffeeAdditives additives) {
        super(additives);
    }

    @Override
    public void makeCoffee() {
        System.out.println("小杯的" + mAdditives.addSomething() + "咖啡");
    }
}
```

### 测试：
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class Test {
    public static void main(String[] args) {
        AddSugar sugar = new AddSugar();
        Ordinary ordinary = new Ordinary();

        //大杯加糖
        LargeCoffee largeSugarCoffee = new LargeCoffee(sugar);
        largeSugarCoffee.makeCoffee();
        //大杯原味
        LargeCoffee largeOrdinaryCoffee = new LargeCoffee(ordinary);
        largeOrdinaryCoffee.makeCoffee();

        //小杯加糖
        SmallCoffee smallSugarCoffee = new SmallCoffee(sugar);
        smallSugarCoffee.makeCoffee();
        //小杯原味
        SmallCoffee smallOrdinaryCoffee = new SmallCoffee(ordinary);
        smallOrdinaryCoffee.makeCoffee();
    }
}
```

### 结果：
```
大杯的加糖咖啡
大杯的原味咖啡
小杯的加糖咖啡
小杯的原味咖啡
```

### Android源码中的桥接模式
* 桥接模式在Android中应用广泛，比如：在View的视图顶层中，CheckBox、CompoundButton、Button、TextView和View之间构成一个继承关系的视图层级，每一层级都仅仅是对一种类型控件的实现，它们定义了该类控件所拥有的基本属性和行为，但是真正将他们绘制到屏幕的部分是由与View相关的功能实现类DisplayList、HardwareLayer、Canvas负责。
* 另外还有比较典型的Window与WindowManager
    ![](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-Window%26WindowManager.png)

主要代码如下：

```Java
public abstract class Window {
   //部分省略
   /**
     * Set the window manager for use by this Window to, for example,
     * display panels.  This is <em>not</em> used for displaying the
     * Window itself -- that must be done by the client.
     *
     * @param wm The window manager for adding new windows.
     */
    public void setWindowManager(WindowManager wm, IBinder appToken, String appName) {
        setWindowManager(wm, appToken, appName, false);
    }

    /**
     * Set the window manager for use by this Window to, for example,
     * display panels.  This is <em>not</em> used for displaying the
     * Window itself -- that must be done by the client.
     *
     * @param wm The window manager for adding new windows.
     */
    public void setWindowManager(WindowManager wm, IBinder appToken, String appName,
            boolean hardwareAccelerated) {
        mAppToken = appToken;
        mAppName = appName;
        mHardwareAccelerated = hardwareAccelerated
                || SystemProperties.getBoolean(PROPERTY_HARDWARE_UI, false);
        if (wm == null) {
            wm = (WindowManager)mContext.getSystemService(Context.WINDOW_SERVICE);
        }
        //*将Window与WindowManager绑定
        mWindowManager = ((WindowManagerImpl)wm).createLocalWindowManager(this);
    }
    //部分省略
 }
```

> Window：https://developer.android.com/reference/android/view/Window.html
> WindowManager：https://developer.android.com/reference/android/view/WindowManager.html

## 总结
1. 优点
    * （1）分离抽象与现实、灵活的扩展以及对客户来说透明的实现。
    * （2）桥接模式可以取代多层继承，大大减少了子类的个数。
2. 缺点
    * 不容易设计，对开发者来说要有一定的经验要求。理解很容易，设计却不容易。

