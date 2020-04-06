---
title: 《Android 源码设计模式解析与实战》读书笔记 - 装饰着模式
date: 2017-03-18 08:16:27
tags: [Java, Android, DesignPattern]
---

# 《Android 源码设计模式解析与实战》读书笔记 - 装饰着模式
> 装饰模式也称为包装模式，是结构型设计模式之一。它使用一种对客户端透明的方式来动态扩展对象的功能，是一种用于替代继承技术的一种方案。
> 定义：动态的给对象添加一些额外的职责，就增加功能来说，它比生成子类更加灵活。

## 使用场景
* 需要透明且动态地扩展类的功能时。且在不影响其他对象的情况下。
* 当不能采用继承对系统进行扩展时可以使用装饰模式。比如final类。

## UML类图
![decorator](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-decorator.png)

* `Component`：抽象组件。可以是一个接口或抽象类，其充当的就是被装饰的原始对象。
* `ConcreteComponent`：组件具体实现类，该类是Component类的基本实现，也是我们装饰的具体对象。
* `Decorator`：抽象装饰者，其职责就是装饰我们的组件对象，通过其子类扩展该方法以达到装饰的目的。其内部一定要有一个指向组件对象的引用。在大多数情况下，该类为抽象类，需要根据不同的装饰逻辑实现不同的具体子类。
* `ConcreteDecoratorA`、`ConcreteDecoratorB`：装饰着具体实现类。负责向构件添加新的职责。

```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 抽象组件类，可以是抽象类也可以是接口
 */
public abstract class Component {
    /**
     * 抽象组件的抽象方法
     */
    public abstract void operator();
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 具体组件实现类
 */
public class ConcreteComponent extends Component {
    @Override
    public void operator() {
        // 具体逻辑
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 抽象装饰者
 */
public abstract class Decorator extends Component {
    private Component mComponent;

    public Decorator(Component component) {
        mComponent = component;
    }

    @Override
    public void operator() {
        mComponent.operator();
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 装饰者具体实现类
 */
public class ConcreteDecoratorA extends Decorator {
    public ConcreteDecoratorA(Component component) {
        super(component);
    }

    @Override
    public void operator() {
        operatorA();
        super.operator();
        operatorB();
    }

    public void operatorA() {

    }

    public void operatorB() {

    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 装饰者具体实现类
 */
public class ConcreteDecoratorB extends Decorator {
    public ConcreteDecoratorB(Component component) {
        super(component);
    }

    @Override
    public void operator() {
        super.operator();
        operatorA();
        operatorB();
    }

    public void operatorA() {

    }

    public void operatorB() {

    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 客户调用
 */
public class Client {
    public static void main(String[] args) {
        //被装饰的组件对象
        Component component = new ConcreteComponent();

        //根据组件对象构造装饰者A并调用，相当于给组件对象增加装饰着A的功能
        Decorator decoratorA = new ConcreteDecoratorA(component);
        decoratorA.operator();

        //根据组件对象构造装饰者B并调用，相当于给组件对象增加装饰着B的功能
        Decorator decoratorB = new ConcreteDecoratorA(component);
        decoratorB.operator();
    }
}
```

## 简单实现
> 以一个男孩穿衣装扮为例。实现给男孩在家与出门的穿衣装扮。

### 抽象组件类（Component）：
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public abstract class Person {
    /**
     * 穿着
     */
    public abstract void dressed();
}
```

### 具体实现类（ConcreteComponent）：表示要装扮的Boy
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class Boy extends Person {
    @Override
    public void dressed() {
        System.out.println("Boy穿了内衣内裤");
    }
}
```

### 抽象装饰类（Decorator）：PersonCloth 表示人所穿着的衣服
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public abstract class PersonCloth extends Person {
    protected Person mPerson;

    public PersonCloth(Person person) {
        mPerson = person;
    }

    @Override
    public void dressed() {
        //调用Person的dressed()方法
        mPerson.dressed();
    }
}
```

### 穿贵的衣服：
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class ExpensiveCloth extends PersonCloth {
    public ExpensiveCloth(Person person) {
        super(person);
    }

    private void dressJean() {
        System.out.println("穿条牛仔裤");
    }

    private void dressLeather() {
        System.out.println("穿件皮衣");
    }

    @Override
    public void dressed() {
        super.dressed();
        dressJean();
        dressLeather();
    }
}
```

### 穿便宜的衣服：
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class CheapCloth extends PersonCloth {
    public CheapCloth(Person person) {
        super(person);
    }

    private void dressShirt() {
        System.out.println("穿件短袖");
    }

    private void dressShort() {
        System.out.println("穿条短裤");
    }

    @Override
    public void dressed() {
        super.dressed();
        dressShirt();
        dressShort();
    }
}
```

### 装扮：
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class Test {
    public static void main(String[] args) {
        Person person = new Boy();

        PersonCloth cheapCloth = new CheapCloth(person);
        cheapCloth.dressed();

        System.out.println("*************");

        PersonCloth expensiveCloth = new ExpensiveCloth(person);
        expensiveCloth.dressed();
    }
}
```

### 结果：
```
Boy穿了内衣内裤
穿件短袖
穿条短裤
*************
Boy穿了内衣内裤
穿条牛仔裤
穿件皮衣
```

## Android源码中的实现
* Context

* `Context`类在Android中被称为“上帝对象”，它的本质就是一个抽象类，在装饰模式中相当于抽象组件，而在内部定义了大量的抽象方法，比如我们经常用到的`startActivity`方法。
* 而真正实现是在`ContextImpl`中完成，那么`ContextImpl` 就是抽象组件的具体实现类。
* `Activity`从类的层次来看本质是图个Context，但是`Activity`并非直接继承`Context`，而是继承`ContextThemeWrapper`, `ContextThemeWrapper`有是继承`ContextWrapper`，因为`ContextWrapper` 继承于`Context`，所以`ContextWrapper` 就是装饰者，`ContextWrapper`中持有`Context`的引用。

> Context：https://developer.android.com/reference/android/content/Context.html
> ContextWrapper：https://developer.android.com/reference/android/content/ContextWrapper.html
> ContextThemeWrapper：https://developer.android.com/reference/android/view/ContextThemeWrapper.html
> Activity：https://developer.android.com/reference/android/app/Activity.html

## 与其他模式区别
1. 与[代理模式](http://blog.csdn.net/qq_17766199/article/details/50492805)的区别
    * （1）装饰模式是以对客户端透明的方式扩展对象的功能，是继承方案的一个替代；而代理模式则是给一个对象提供一个代理对象，并有代理对象来控制对原有对象的引用。
    * （2）装饰模式应该为所装饰的对象增强功能；代理模式是对代理对象施加控制，不对对象本身功能进行增强。
2. 与[适配器模式](http://blog.csdn.net/qq_17766199/article/details/50514877)的区别
    * 适配器模式是用新接口来调用原接口，原接口对新系统是不可见的；装饰模式增强了其他对象的功能而同时又不改变它的接口。

## Summary

> 在实际开发中我们应该写过如下代码：其实这些新增方法的调用就类似装饰模式中的装饰者的职责，只不过这里我们没有保持对组件类的引用。

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    //初始化数据
    initData();

    //初始化控件
    initViews();

    //初始化事件
    initEvent();
}
```

1. 优点
    * （1）对于扩展一个对象的功能，装饰模式比继承更加灵活性，不会导致类的个数急剧增加。
    * （2）可以通过一种动态的方式在运行时选择不同的具体装饰类，从而实现不同的行为。
    * （3）可以对一个对象进行多次装饰，通过使用不同的具体装饰类以及这些装饰类的排列组合，可以创造出很多不同行为的组合，得到功能更为强大的对象。
    * （4）具体构件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体构件类和具体装饰类，原有类库代码无须改变，符合“开闭原则”。
2. 缺点
    * （1）使用装饰模式进行系统设计时将产生很多小对象，这些对象的区别在于它们之间相互连接的方式有所不同，而不是它们的类或者属性值有所不同，大量小对象的产生势必会占用更多的系统资源，在一定程序上影响程序的性能。
    * （2）对于多次装饰的对象，调试时寻找错误可能需要逐级排查，较为繁琐。

