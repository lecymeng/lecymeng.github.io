---
title: 《Android 源码设计模式解析与实战》读书笔记 - 命令模式
date: 2017-03-18 12:16:27
tags: [Java, Android, DesignPattern]
---

# 《Android 源码设计模式解析与实战》读书笔记 - 命令模式
> 命令模式是行为型模式之一。总体来说并不难理解，只是比较繁琐，他会将简单的调用关系解耦成多个部分，增加类的复杂度，但是即便如此，命令模式的结构依然清晰。
> 定义: 将一个请求封装成一个对象，从而让用户使用不同的请求把客户端参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。
<!--more-->

## 使用场景
* （1）需要抽出待执行的动作，然后以参数的形式提供出来。
* （2）在不同的时刻指定、排列和执行请求。一个命令对象可以有与初始请求无关的生存期。
* （3）需要支持操作取消。
* （4）支持修改日志功能，这样当系统崩溃时，这些修改可以被重做一遍。
* （5）需要支持事务操作。

## UML类图
![](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-Command.png)

## 简单实现
> 以推箱子游戏为例，一般游戏中会有五个按钮，分别是左移、右移、下移、上移和撤销。那么玩游戏的人就是客户端，五个按钮就是调用者，执行具体按钮命令的方法是命令角色。

### 接收者角色：推箱子游戏
```java
public class PushBox {
    /**
     * 执行向左命令 
     */
    public void toLeft(){
        System.out.println("向左");
    }

    /**
     * 执行向右命令 
     */
    public void toRight(){
        System.out.println("向右");
    }

    /**
     * 执行向下命令 
     */
    public void toDown(){
        System.out.println("向下");
    }

    /**
     * 执行向上命令 
     */
    public void toUp(){
        System.out.println("向上");
    }

    /**
     * 执行撤销命令 
     */
    public void revoke(){
        System.out.println("撤销");
    }
}
```

### 命令者抽象
```java
public interface Command {
    /**
     * 命令执行方法
     */
    void execute();

    /**
     * 获取命令类型
     */
    void getCommand();
}
```

### 具体命令者，左移命令类
```java
public class LeftCommand implements Command{

    //持有一个接受推箱子游戏对象的引用
    private PushBox pushBox;

    public LeftCommand(PushBox pushBox){
        this.pushBox = pushBox;
    }

    @Override
    public void execute() {
        //调用具体命令
        pushBox.toLeft();
    }

    @Override
    public void getCommand() {
        System.out.print("向左-->");
    }
}
```

### 具体命令者，右移命令类
```java
public class RightCommand implements Command{

    //持有一个接受推箱子游戏对象的引用
    private PushBox pushBox;

    public RightCommand(PushBox pushBox){
        this.pushBox = pushBox;
    }

    @Override
    public void execute() {
        //调用具体命令
        pushBox.toRight();
    }

    @Override
    public void getCommand() {
        System.out.print("向右-->");
    }
}
```

### 具体命令者，上移命令类
```java
public class UpCommand implements Command{

    //持有一个接受推箱子游戏对象的引用
    private PushBox pushBox;

    public UpCommand(PushBox pushBox){
        this.pushBox = pushBox;
    }

    @Override
    public void execute() {
        //调用具体命令
        pushBox.toUp();
    }

    @Override
    public void getCommand() {
        System.out.print("向上-->");
    }
}
```

### 具体命令者，下移命令类
```java
public class DownCommand implements Command{

    //持有一个接受推箱子游戏对象的引用
    private PushBox pushBox;

    public DownCommand(PushBox pushBox){
        this.pushBox = pushBox;
    }

    @Override
    public void execute() {
        //调用具体命令
        pushBox.toDown();
    }

    @Override
    public void getCommand() {
        System.out.print("向下-->");
    }
}
```

### 具体命令者，撤销命令类
```java
public class RevokeCommand implements Command{

    //持有一个接受推箱子游戏对象的引用
    private PushBox pushBox;

    public RevokeCommand(PushBox pushBox){
        this.pushBox = pushBox;
    }

    @Override
    public void execute() {
        //调用具体命令
        pushBox.revoke();;
    }
    @Override
    public void getCommand() {
    }
}
```

### 请求者类，命令由按钮发起
```java
public class Buttons {
    private LeftCommand leftCommand; //向左移动的命令对象引用
    private RightCommand rightCommand; //向右移动的命令对象引用
    private UpCommand upCommand; //向上移动的命令对象引用
    private DownCommand downCommand; //向下移动的命令对象引用
    private RevokeCommand revokeCommand; //撤销命令对象引用

    private ArrayList<Command> commandList = new ArrayList<Command>();//用于记录命令动作

    /**
     * 获取执行命令
     */
    public void getCommandList(){
        for(Command c : commandList){
            c.getCommand();
        }
        System.out.println("");
    }

    /**
     * 设置向左移动的命令对象
     * 
     * @param leftCommand 向左移动的命令对象
     */
    public void setLeftCommand(LeftCommand leftCommand){
        this.leftCommand = leftCommand;
    }

    /**
     * 设置向右移动的命令对象
     * 
     * @param rightCommand 向右移动的命令对象
     */
    public void setRightCommand(RightCommand rightCommand){
        this.rightCommand = rightCommand;
    }

    /**
     * 设置向上移动的命令对象
     * 
     * @param upCommand 向上移动的命令对象
     */
    public void setUpCommand(UpCommand upCommand){
        this.upCommand = upCommand;
    }

    /**
     * 设置向下移动的命令对象
     * 
     * @param downCommand 向下移动的命令对象
     */
    public void setDownCommand(DownCommand downCommand){
        this.downCommand = downCommand;
    }

    /**
     * 设置撤销命令对象
     * 
     * @param revokeCommand 撤销命令对象
     */
    public void setRevokeCommand(RevokeCommand revokeCommand){
        this.revokeCommand = revokeCommand;
    }

    /**
     * 按下向左按钮 
     */
    public void toLeft(){
        leftCommand.execute();
        commandList.add(leftCommand);
    }

    /**
     * 按下向右按钮 
     */
    public void toRight(){
        rightCommand.execute();
        commandList.add(rightCommand);
    }

    /**
     * 按下向上按钮 
     */
    public void toUp(){
        upCommand.execute();
        commandList.add(upCommand);
    }

    /**
     * 按下向下按钮 
     */
    public void toDown(){
        downCommand.execute();
        commandList.add(downCommand);
    }

    /**
     * 按下撤销按钮 
     */
    public void toRevoke(){
        revokeCommand.execute();
        commandList.remove(commandList.size()-1);
    }
}
```

### 客户端调用
```java
public class Client {

    public static void main(String[] args) {
        //首先创建游戏
        PushBox pushBox = new PushBox();

        //根据游戏构造5种命令
        LeftCommand leftCommand = new LeftCommand(pushBox);
        RightCommand rightCommand = new RightCommand(pushBox);
        UpCommand upCommand = new UpCommand(pushBox);
        DownCommand downCommand = new DownCommand(pushBox);
        RevokeCommand revokeCommand = new RevokeCommand(pushBox);

        //按钮可以执行不同命令
        Buttons buttons = new Buttons();
        buttons.setLeftCommand(leftCommand);
        buttons.setRightCommand(rightCommand);
        buttons.setUpCommand(upCommand);
        buttons.setDownCommand(downCommand);
        buttons.setRevokeCommand(revokeCommand);

        //执行操作
        buttons.toLeft();
        buttons.toDown();
        buttons.toDown();
        buttons.toRight();
        buttons.getCommandList();
        buttons.toRevoke();
        buttons.toUp();
        buttons.toLeft();
        buttons.toDown();
        buttons.toUp();
        buttons.getCommandList();
    }
}
```

### 执行结果
```
向左
向下
向下
向右
向左-->向下-->向下-->向右-->
撤销
向上
向左
向下
向上
向左-->向下-->向下-->向上-->向左-->向下-->向上-->1234567891011
```

在这么长的代码之后是不是觉得很烦琐，明明可以很简单的实现，如下：

```java
public class Client {

    public static void main(String[] args) {
        //首先创建游戏
        PushBox pushBox = new PushBox();

        pushBox.toDown();
        pushBox.toRight();
        pushBox.toUp();
    }
}
```

其实设计模式有一个重要的原则：对修改关闭对扩展开放。如果使用如上的简单方式，那么以后的修改只能去修改PushBox类，然后修改Client类，这显然违反了这一原则。如果使用命令模式，那么Client类无需修改，只需要修改PushBox类的内部操作，Client类无需知道具体的内部实现。

> 设计模式的使用之前也有提到，主要是要看当前场景的复杂度和以后的需求进行扩展、维护等方面，完全使用设计模式也是不提倡的，这就需要设计者权衡利弊了。

## Android源码中的命令模式实现
1. PackageHandler

PackageManagerService中，其对包的相关消息处理右其内部类PackageHandler承担，其将需要处理的请求作为对象通过消息传递给相关的方法，而对于包的安装、移动以及包大小的测量则分别封装为**HandlerParams**的具体子类**InstallParams**、**MoveParams**和**MeasureParams**。源码如下：

```java
private abstract class HandlerParams {
        private static final int MAX_RETRIES = 4;

        /**
         * Number of times startCopy() has been attempted and had a non-fatal
         * error.
         */
        private int mRetries = 0;

        final boolean startCopy() {
            boolean res;
            try {
                if (DEBUG_INSTALL) Slog.i(TAG, "startCopy");

                if (++mRetries > MAX_RETRIES) {
                    Slog.w(TAG, "Failed to invoke remote methods on default container service. Giving up");
                    mHandler.sendEmptyMessage(MCS_GIVE_UP);
                    handleServiceError();
                    return false;
                } else {
                    handleStartCopy();
                    res = true;
                }
            } catch (RemoteException e) {
                if (DEBUG_INSTALL) Slog.i(TAG, "Posting install MCS_RECONNECT");
                mHandler.sendEmptyMessage(MCS_RECONNECT);
                res = false;
            }
            handleReturnCode();
            return res;
        }

        final void serviceError() {
            if (DEBUG_INSTALL) Slog.i(TAG, "serviceError");
            handleServiceError();
            handleReturnCode();
        }

        abstract void handleStartCopy() throws RemoteException;
        abstract void handleServiceError();
        abstract void handleReturnCode();
    }
```

可以看出**HandlerParams**也是一个抽象命令者。

## 总结
1. 优点
    * 命令模式的封装性很好，更弱的耦合性，更灵活的控制性以及更好的扩展性。
2. 缺点
    * 类的膨胀，大量衍生类的创建。

