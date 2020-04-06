---
title: 《Android 源码设计模式解析与实战》读书笔记 - 责任链模式
date: 2017-03-18 12:16:27
tags: [Java, Android, DesignPattern]
---

# 《Android 源码设计模式解析与实战》读书笔记 - 责任链模式
> 锁链这种链式结构，每个节点都可以被拆开再连接，因此链式结构也具有很好的灵活性，在编程领域，将每一个节点看作一个对象，每个对象有不同的处理逻辑，将一个请求从链式的首端出发，沿着路径依次传递到每个节点对象，直到有对象处理这个请求为止。
> 定义: 责任链模式是行为型设计模式之一，它使多个对象都有机会处理请求，从而避免了请求的发送者和接受者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有对象处理它为止。
<!--more-->

## 使用场景
1. 多个对象可以处理同一请求，但具体由哪个对象处理则在运行时动态决定。 
2. 在请求处理者不明确的情况下向多个对象中的一个提交请求。 
3. 需要动态指定一组对象处理请求。

## UML类图
![](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-Chain.png)

```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 抽象处理角色，申明一个请求处理方法
 * 并在其中保持一个对下一个处理节点Handler的引用
 */
public abstract class Handler {
    //下一个节点处理者
    protected Handler successor;

    public abstract void handleRequest(String condition);
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 具体处理者角色，对请求进行处理，如果不能处理就转发给下一个处理
 */
public class ConcreteHandlerA extends Handler {
    @Override
    public void handleRequest(String condition) {
        if (condition.equals("ConcreteHandlerA")) {
            System.out.println("ConcreteHandlerA handled");
        } else {
            successor.handleRequest(condition);
        }
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 具体处理者角色，对请求进行处理，如果不能处理就转发给下一个处理
 */
public class ConcreteHandlerB extends Handler {
    @Override
    public void handleRequest(String condition) {
        if (condition.equals("ConcreteHandlerB")) {
            System.out.println("ConcreteHandlerB handled");
        } else {
            successor.handleRequest(condition);
        }
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc:
 */
public class Client {
    public static void main(String[] args) {
        ConcreteHandlerA handlerA = new ConcreteHandlerA();
        ConcreteHandlerB handlerB = new ConcreteHandlerB();

        handlerA.successor = handlerB;
        handlerB.successor = handlerA;

        handlerA.handleRequest("ConcreteHandleB");
    }
}
```

## 简单实现
> 这个例子我觉得很贴切。我们在公司有各种原因需要报销费用，首先我们要找我们的上级领导去审批，报销额度如果在领导的权限范围内，那就审批通过，否则领导在找自己的上级去审批，以此类推。

### 抽象领导类
```java
public abstract class Leader {
    /**
     * 上级领导处理者
     */
    protected Leader nextHandler;

    /**
     * 处理报账请求
     * 
     * @param money 能批复的报账额度 
     * 
     */
    public final void handleRequest(int money){
        System.out.println(getLeader());
        if(money <=limit()){
            handle(money);
        }else{
            System.out.println("报账额度不足，提交领导");
            if(null != nextHandler){
                nextHandler.handleRequest(money);
            }
        }
    }

    /**
     * 自身能批复的额度权限
     * 
     * @return 额度
     */
    public abstract int limit();

    /**
     * 处理报账行为
     * 
     * @param money 具体金额
     */
    public abstract void handle(int money);

    /**
     * 获取处理者
     * 
     * @return 处理者
     */
    public abstract String getLeader();
}
```

### 组长（额度1000）：
```java
public class GroupLeader extends Leader{

    @Override
    public int limit() {
        return 1000;
    }

    @Override
    public void handle(int money) {
        System.out.println("组长批复报销"+ money +"元");
    }

    @Override
    public String getLeader() {
        return "当前是组长";
    }

} 
```

### 主管（额度5000）：
```java
public class Director extends Leader{

    @Override
    public int limit() {
        return 5000;
    }

    @Override
    public void handle(int money) {
        System.out.println("主管批复报销"+ money +"元");
    }

    @Override
    public String getLeader() {
        return "当前是主管";
    }

}
```

### 经理（额度10000）：
```java
public class Manager extends Leader{

    @Override
    public int limit() {
        return 10000;
    }

    @Override
    public void handle(int money) {
        System.out.println("经理批复报销"+ money +"元");
    }

    @Override
    public String getLeader() {
        return "当前是经理";
    }

}
```

### 老板（额度无上限）:
```java
public class Boss extends Leader{

    @Override
    public int limit() {
        return Integer.MAX_VALUE;
    }

    @Override
    public void handle(int money) {
        System.out.println("老板批复报销"+ money +"元");
    }

    @Override
    public String getLeader() {
        return "当前是老板";
    }

}
```

### 发起申请：
```java
public class Client {
    public static void main(String[] args) {
        //构造各个领导对象
        GroupLeader groupLeader = new GroupLeader();
        Director director = new Director();
        Manager manager = new Manager();
        Boss boss = new Boss();
        //设置上级领导处理者对象
        groupLeader.nextHandler = director;
        director.nextHandler = manager;
        manager.nextHandler = boss;

        //发起报账申请
        groupLeader.handleRequest(8000);

    }
}
```

### 结果：
```
当前是组长
报账额度不足，提交领导
当前是主管
报账额度不足，提交领导
当前是经理
经理批复报销8000元123456
```

责任链模式非常灵活，请求的发起可以从责任链的任何一个节点开始，也可以改变内部的传递规则。比如主管不在，我们完全可以跨过主管直接从组长那里转到经理。

> 对于责任链中的一个处理者对象，有两个行为。一是处理请求，二是将请求传递到下一节点，不允许某个处理者对象在处理了请求后又将请求传送给上一个节点的情况。
> 对于一条责任链来说，一个请求最终只有两种情况。一是被某个处理对象所处理，另一个是所有对象均未对其处理，对于前一种情况我们称为**纯的责任链模式**，后一种为**不纯的责任链**。实际中大多为不纯的责任链。

## Android源码中的责任链模式
* View事件的分发处理

ViewGroup事件投递的递归调用就类似于一条责任链，一旦其寻找到责任者，那么将由责任者持有并消费掉该次事件，具体体现在View的onTouchEvent方法中返回值的设置，如果返回false，那么意味着当前的View不会是该次的责任人，将不会对其持有；如果返回true，此时View会持有该事件并不再向外传递。

## 总结
1. 优点
    * 可以对请求者和处理者的关系解耦，提高代码的灵活性。
2. 缺点
    * 每次都需要对链中请求处理者遍历，如果处理者太多那么遍历必定会影响性能，特别是在一些递归调用者中，要慎用。

