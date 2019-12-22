---
title: 《Android 源码设计模式解析与实战》读书笔记 - 外观模式
date: 2017-03-18 10:16:27
tags: [Java, Android, DesignPattern]
---

> 外观模式(Facade)是结构型设计模式之一，它在开发中的运用频率非常高，尤其是在SDK模块上，是我们封装API的常用手段。我们经常使用的三方SDK基本都使用的外观模式，这样可以对用户屏蔽很多实现细节，降低用户使用成本。

<!--more-->
### 1.定义

要求一个子系统的外部与其内部的通信必须通过一个统一的对象进行。外观模式提供一个高层次的接口，使得子系统更易于使用。

### 2.使用场景

（1）为复杂子系统提供一个简单接口，对外隐藏子系统的具体实现、隔离变化。

（2）当你需要构建一个层次结构的子系统时，使用外观模式定义子系统中每层的入口点。如果子系统之间是相互依赖的，你可以让它们仅通过外观接口进行通信，从而简化了它们之间的依赖关系。

### 3.UML类图

![](http://blog-1251678165.coscd.myqcloud.com/2018-03-17-Facade-Pattern-1.png)

（1）`Facade`：系统对外的统一接口，系统内部系统地工作。

（2）`其他分支`：子系统接口。

可以看出外观模式结构很简单，但是如果没有封装，那么用户就要操作几个子系统的交互逻辑，容易出现错误。

### 4.简单实例

> 手机集合了电话功能、短信功能、拍照和GPS等功能。那么以手机为例，简单的用外观模式实现一下。

#### Phone接口与PhoneImpl：
```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc:
 */
public interface Phone {
    /**
     * 打电话
     */
    void call();

    /**
     * 挂断
     */
    void hangUp();
}
```

```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 电话模块实现
 */
public class PhoneImpl implements Phone {
    @Override
    public void call() {
        System.out.println("打电话");
    }

    @Override
    public void hangUp() {
        System.out.println("挂断电话");
    }
}
```


#### Camera接口与Camera的实现类：
```
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc:
 */
public interface Camera {
    /**
     * 打开相机
     */
    void open();

    /**
     * 拍照
     */
    void takePicture();

    /**
     * 关闭相机
     */
    void close();
}
```

```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 相机模块实现
 */
public class SonyCamera implements Camera {

    @Override
    public void open() {
        System.out.println("打开相机");
    }

    @Override
    public void takePicture() {
        System.out.println("拍个照片");
    }

    @Override
    public void close() {
        System.out.println("关闭相机");
    }
}
```

#### 外观类MobileFacade：
```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 外观类
 */
public class MobileFacade {
    private Phone mPhone = new PhoneImpl();
    private Camera mCamera = new SonyCamera();

    public void call() {
        mPhone.call();
    }

    public void videoChat() {
        System.out.println("视频正在连接……");
        mCamera.open();
        mPhone.call();
    }

    public void hangUp() {
        mPhone.hangUp();
    }

    public void takePicture() {
        mCamera.open();
        mCamera.takePicture();
    }

    public void closeCamera() {
        mCamera.close();
    }
}
```

#### 测试：
```java
/**
 * Created by Weicools on 2018/3/17.
 * <p>
 * desc:
 */
public class Client {
    public static void main(String[] args) {
        MobileFacade mobileFacade = new MobileFacade();

        mobileFacade.videoChat();
        mobileFacade.takePicture();
        mobileFacade.closeCamera();
    }
}
```

结果：

```
打开相机
拍照
--> 视频聊天接通中
打开相机
打电话12345
```

### 5.Android源码中的外观模式
`Context` 是Android开发中的最重要一个，是程序的运行环境，它封装了很多重要操作，例如: startActivity(), startService(), sendBroadcast(), bindService().

`Context` 是一个抽象类，它的真正实现是`ContextImpl` 类，通过查看`ContextImpl` 源码我们可以看到`ContextImpl`内部封装了很多不同子系统的操作。例如：Activity的跳转、发送广播、启动服务和设置壁纸等，这些工作不是在`ContextImpl` 中实现，而是交给了具体的子系统进行处理。通过`Context` 这个抽象类定义了一组接口，`ContextImpl`实现。这样用户通常情况下就不需要对每个子系统进行了解。这样对用户屏蔽了具体的实现细节，降低了使用成本。

### 6.Summary
1. 优点
    * 对客户程序隐藏子系统的细节，因而减少了客户对于子系统的耦合，能够拥抱变化。
    * 外观类对子系统的接口封装，使得系统更易于使用。

2. 缺点
    * 外观类接口膨胀。由于子系统的接口都有外观类统一对外暴露，使得外观类的API接口较多，在一定程度上增加了用户使用成本。
    * 外观类没有遵循开闭原则，当业务出现变更时，可能需要直接修改外观类。

