---
title: 《Android 源码设计模式解析与实战》读书笔记 - 模板方法模式
date: 2017-03-18 12:16:27
tags: [Java, Android, DesignPattern]
---

> 模板方法模式是结构最简单的行为型设计模式，也是所有模式中最为常见的几个模式之一，是基于继承的代码复用的基本技术。在其结构中只存在父类与子类之间的继承关系。
> 定义：定义一个操作中的算法的框架，而将一些步骤延迟到子类中，使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

<!--more-->
### 模板方法模式中的方法
![](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-Template.png)
#### 模板方法
一个模板方法是定义在抽象类中的，把基本操作方法组合在一起形成一个总算法或一个总行为的方法。这个模板方法定义在抽象类中，并由子类不加以修改地完全继承下来。所以模板方法大多会定义为**final类型**，指明主要的逻辑功能在子类中不能被重写。模板方法是一个具体方法，它给出了一个顶层逻辑框架，而逻辑的组成步骤在抽象类中可以是具体方法，也可以是抽象方法。由于模板方法是具体方法，因此模板方法模式中的抽象层只能是抽象类，而不是接口。

#### 基本方法
（1）抽象方法：一个抽象方法由**抽象类声明，由具体子类实现**。在Java语言里抽象方法以abstract关键字标示。
（2）钩子方法：一个钩子方法由抽象类**声明并实现**，而子类会加以扩展。子类可以通过扩展钩子方法来影响模板方法的逻辑。

### 使用场景

（1）多个子类有公有的方法，并且逻辑基本相同。

（2）重要、复杂的算法，可以把核心算法设计为模板方法，周边的相关细节功能由各个子类实现。

（3）重构时，模板方法是一个经常使用的模式，把相同的代码抽取到父类中，然后通过钩子方法约束其行为。

### 简单实现
> 以电脑开机为例，假设现在有两台电脑，一台Windows系统电脑，一台Mac系统电脑。但是开机流程基本一致：步骤为开启电源、系统检查、加载系统、检查是否需要登录。

#### 抽象的 Computer
```java
/**
 * 抽象的 Computer
 */
public abstract class AbstractComputer {
    //下面是抽象方法，子类可以覆盖,不允许外部直接调用这些方法，所以用protected
    /**
     * 开启电源
     */
    protected abstract void powerOn();

    /**
     * 检查硬件
     */
    protected abstract void checkHardware();

    /**
     * 载入操作系统
     */
    protected abstract void loadOS();

    /**
     * 登录
     */
    protected abstract void login();

    //下面是钩子方法，声明并实现
    /**
     * 是否需要登录
     * 
     * @return true为需要登录
     */
    protected boolean isLogin(){
        return true;
    }

    //下面是模板方法，定义为final，子类不能覆盖此方法 
    /**
     * 启动计算机方法，步骤为开启电源、系统检查、加载系统、检查是否登录。
     */
    public final void startUp(){
        System.out.println("--------开机 START--------");
        powerOn();
        checkHardware();
        loadOS();
        if(isLogin()){
            login();
        }
        System.out.println("-------- 开机 END --------");
    }

}
```

### Windows系统电脑（不需登录）：
```java
/**
 * Windows系统电脑
 */
public class WindowsComputer extends AbstractComputer{

    @Override
    protected void powerOn() {
        System.out.println("Windows电脑开启电源");
    }

    @Override
    protected void checkHardware() {
        System.out.println("Windows电脑检查硬件");
    }

    @Override
    protected void loadOS() {
        System.out.println("Windows电脑载入操作系统");
    }

    @Override
    protected void login() {

    }

    @Override
    protected boolean isLogin() {
        return false;//返回false，不需登录
    }

}
```

### Mac系统电脑（需登录）：
```java
/**
 * Mac系统电脑
 */
public class MacComputer extends AbstractComputer{

    @Override
    protected void powerOn() {
        System.out.println("Mac电脑开启电源");
    }

    @Override
    protected void checkHardware() {
        System.out.println("Mac电脑检查硬件");
    }

    @Override
    protected void loadOS() {
        System.out.println("Mac电脑载入操作系统");
    }

    @Override
    protected void login() {
        System.out.println("Mac电脑登录");
    }

}
```

#### 调用：
```java
public class Client {
    public static void main(String[] args) {
        AbstractComputer comp = new WindowsComputer();
        comp.startUp();

        comp = new MacComputer();
        comp.startUp();
    }
}
```

#### 结果：
```
--------开机 START--------
Windows电脑开启电源
Windows电脑检查硬件
Windows电脑载入操作系统
-------- 开机 END --------
--------开机 START--------
Mac电脑开启电源
Mac电脑检查硬件
Mac电脑载入操作系统
Mac电脑登录
-------- 开机 END --------1234567891011
```

### Android源码中的模板方法模式
#### AsyncTask
在使用AsyncTask时，我们都知道把耗时操作放到doInBackground(Params… params)中，在doInBackground之前，如果想做一些初始化操作，可以把实现写在onPreExecute中，当doInBackground执行完后会执行onPostExecute方法，而我们只需要构建AsyncTask对象，然后执行execute方法。

#### Activity的生命周期
ActivityThread的main函数被调用后，依次执行Activity的onCreate、onStart、onResume函数，用户通常在Activity的子类中覆写onCreate方法，并且在该方法中调用setContentView来设置布局。

### 区别
* （1）工厂方法是模板方法的一种特殊版本。
* （2）策略模式和模板方法模式都是封装算法，一个用组合，一个用继承。
* （3）策略模式和模板模式通常可以互相替换。它们都像试卷，策略模式是选择题，模板模式是填空题。

### 总结
> 模板方法模式用4个字概括就是：流程封装。也就是把某个固定的流程封装到一个final方法中，并且让子类能够定制这个流程中的某些或者所有步骤，这就要求父类提取公用的代码，提升代码的复用率，同时带来了更好的可扩展性。

1. 优点
    * （1）封装不变部分，扩展可变部分。
    * （2）提取公共部分代码，便于维护。

2. 缺点
    * 需要为每一个基本方法的不同实现提供一个子类，如果父类中可变的基本方法太多，将会导致类的个数增加，系统更加庞大，设计也更加抽象，此时，可结合桥接模式来进行设计。

### 参考
1. [ 模板方法模式（Head first 设计模式——7）](http://www.cnblogs.com/lzhp/p/3404514.html)
2. [ 模板方法模式](http://blog.csdn.net/zhengzhb/article/details/7405608)

