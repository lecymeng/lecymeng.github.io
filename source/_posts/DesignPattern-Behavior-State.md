---
title: 《Android 源码设计模式解析与实战》读书笔记 - 状态模式
date: 2017-03-18 12:16:27
tags: [Java, Android, DesignPattern]
---

# 《Android 源码设计模式解析与实战》读书笔记 - 状态模式

> 定义：状态模式中的行为是由状态来决定，不同的状态下有不同的行为。当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。

<!--more-->

## 使用场景

1. 一个对象的行为取决于它的状态，并且它必须在运行时根据状态改变它的行为。
2. 代码中包含大量与对象状态有关的条件语句，例如，一个操作中含有大量的多分支语句，且这些分支依赖于该对象的状态。
   ![](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-State.png)

## 简单实现

> 实现效果：首先将电视的状态分为开机与关机状态，开机时可以通过遥控器实现频道切换和调节音量，但是关机时，这些操作都会失效。

### 普通的实现方法：

```java
public class TVController {
    //开机状态
    private final static int POWER_ON = 1;
    //关机状态
    private final static int POWER_OFF = 2;
    //默认状态
    private int mState = POWER_OFF;

    public void powerOn(){
        if(mState ==POWER_OFF){
            System.out.println("电视开机了");
        }
        mState = POWER_ON;
    }

    public void powerOff(){
        if(mState ==POWER_ON){
            System.out.println("电视关机了");
        }
        mState = POWER_OFF;
    }

    public void nextChannel(){
        if(mState ==POWER_ON){
            System.out.println("下一频道");
        }else{
            System.out.println("没有开机");
        }
    }

    public void prevChannel(){
        if(mState ==POWER_ON){
            System.out.println("上一频道");
        }else{
            System.out.println("没有开机");
        }
    }

    public void turnUp(){
        if(mState ==POWER_ON){
            System.out.println("调高音量");
        }else{
            System.out.println("没有开机");
        }
    }

    public void turnDown(){
        if(mState ==POWER_ON){
            System.out.println("调低音量");
        }else{
            System.out.println("没有开机");
        }
    }
}
```

可以看到，每次执行通过判断当前状态来进行操作，部分的代码重复，假设状态和功能增加，就会越来越难以维护。这时可以使用状态模式，如下：

### 电视的操作

```java
/**
 * 电视状态接口，定义了电视的操作函数
 *
 * */
public interface TVState {

    public void nextChannel();
    public void prevChannel();
    public void turnUp();
    public void turnDown();

}
12345678910111213
```

### 关机状态

```java
/**
 *
 * 关机状态，操作无结果
 *
 * */
public class PowerOffState implements TVState{

    @Override
    public void nextChannel() {

    }

    @Override
    public void prevChannel() {

    }

    @Override
    public void turnUp() {

    }

    @Override
    public void turnDown() {

    }
}
```

### 开机状态

```java
/**
 *
 * 开机状态，操作有效
 *
 * */
public class PowerOnState implements TVState{

    @Override
    public void nextChannel() {
        System.out.println("下一频道");
    }

    @Override
    public void prevChannel() {
        System.out.println("上一频道");
    }

    @Override
    public void turnUp() {
        System.out.println("调高音量");
    }

    @Override
    public void turnDown() {
        System.out.println("调低音量");
    }
}
```

### 电源操作接口

```java
/**
 * 电源操作接口
 *
 * */
public interface PowerController {
    public void powerOn();

    public void powerOff();
}
```

### 电视遥控器

```java
/**
 * 电视遥控器
 *
 * */
public class TVController implements PowerController{

    TVState mTVState;

    public void setTVState(TVState mTVState){
        this.mTVState = mTVState;
    }

    @Override
    public void powerOn() {
        setTVState(new PowerOnState());
        System.out.println("开机了");
    }

    @Override
    public void powerOff() {
        setTVState(new PowerOffState());
        System.out.println("关机了");
    }

    public void nextChannel(){
        mTVState.nextChannel();
    }

    public void prevChannel(){
        mTVState.prevChannel();
    }

    public void turnUp(){
        mTVState.turnUp();
    }

    public void turnDown(){
        mTVState.turnDown();
    }
}
```

### 调用：

```java
public class Client {
    public static void main(String[] args) {
        TVController tvController = new TVController();
        //设置开机状态
        tvController.powerOn();
        //下一频道
        tvController.nextChannel();
        //调高音量
        tvController.turnUp();
        //关机
        tvController.powerOff();
        //调低音量，此时不会生效
        tvController.turnDown();
    }
}
```

### 结果

```
开机了
下一频道
调高音量
关机了1234
```

可以看出，状态模式将这些行为封装到状态类中，在进行操作时将这些功能转发给状态对象，不同的状态有不同的实现，去除了重复了 if-else 语句，这正是状态模式的精髓所在。

## 与策略模式的区别

状态模式与策略模式的结构几乎是一样的，就像是孪生兄弟。但是他们的目地、本质不一样。状态模式的行为是平行的、不可替换的，策略模式的行为是彼此独立的、可相互替换的。状态模式，通常是自我控制状态的改变。而策略模式，是由外部指定使用什么样的策略。

## Android 实战中的使用

1. 登录系统，根据用户是否登录，判断事件的处理方式。
2. Wi-Fi 管理，在不同的状态下，WiFi 的扫描请求处理不一。

## 总结

1. 优点
   - 将所有与一个特定的状态相关的行为都放入一个状态对象中，它提供了一个更好的方法来组织与特定状态相关的代码，将繁琐的状态判断转换成结构清晰的状态类族，在避免代码膨胀的同时也保证了可扩展性与可维护性。
2. 缺点
   - 状态模式的使用必然会增加系统类和对象的个数。
