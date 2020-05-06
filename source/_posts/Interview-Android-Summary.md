---
title: Android面试要点总结
date: 2016-07-15 09:28:13
tags: [Android, Java, Interview]
---

# Android面试要点总结
## 1.Java技术
*   1.1 Java基础
    *   对象、继承、多态理解
    *   泛型作用及使用场景
    *   枚举的特点及使用场景
    *   线程sleep和wait的区别
    *   Java反射机制
    *   weak/soft/strong的区别
    *   Object的hasCode()与equals()的区别和作用
<!--more-->
*   1.2 集合类
    *   Java常用集合类、区别和性能
    *   并发相关集合类
    *   部分常用集合类的内部实现方式

*   1.3 多线程相关
    *   Thread、Runable、Callable、Futrue类关系和区别
    *   JDK中默认提供哪些线程池，有何区别
    *   线程同步有几种方式，分别阐述在项目中的用法
    *   在理解默认线程池前提下，自己实现线程池

*   1.4 字符
    *   注解的使用
    *   注解的级别及意义
    *   如何自定义注解

## 2.Android技术
*   2.1 Android基础
    *   四大组件的意义及使用，生命周期回调及意义
    *   AsynTask、Handle的使用
    *   Android系统层次框架结构
    *   AsynTask的实现方式
    *   AsynTask使用的时候应该注意什么
    *   Android常见的存储方式
    *   Looper、Handler和MessageQueue的关系
    *   Activity的启动流程（考察队Framework的熟悉程度）、
    *   多进程开发注意事项（Application类区分进程，进程间内存不可见、进程间通讯方式）

*   2.2 Resource相关
    *   .9图片的意义
    *   style和theme的作用和用法
    *   dpi、sp、px的区别以及转换关系
    *   raw和assets的文件夹作用，二者的区别
    *   Android系统如何在多个资源文件夹下查找匹配最合适的资源

*   2.3 虚拟机
    *   Java内存模型
    *   Android虚拟机特点
    *   Dalvik和Art的区别
    *   熟悉垃圾回收的实现机制，了解虚拟机的GC类型

*   2.4 View相关
    *   常用组件的使用：ListView、RecyclerView及Adapter的使用
    *   View之间的继承关系
    *   Invalidate与postInvalidate的区别
    *   自定义View的实现方式（根据项目经验询问相关条件）
    *   onMeasure/onLayout/onDraw的作用
    *   Paint、Matrix、Shader等绘制相关类的方法作用
    *   详细描述事件分发机制

*   2.5 动画
    *   Android动画的实现方式
    *   Interpolatio类的意义和常用的Interpolator
    *   ViewAnimation与属性动画有什么区别
    *   如何自定义ViewAnimation
    *   属性动画的实现原理

*   2.6 图片处理
    *   一般图片中如何加载大图
    *   图片压缩方式
    *   如何不压缩图片加载高清图
    *   图片加载过程中，一般会使用缓存，这个缓存的主要作用是什么
    *   谈谈自己熟悉的图片加载框架

## 3.项目经验
*   3.1 开发工具
    *   代码管理工具：SVN、Git
    *   常用调试工具：Hierarchy Viewer、 DDMS等
    *   CI工具：Lint检查，Findbugs，Jenkins
    *   内存分析工具：dumpsys、memoinfo、MAT

*   3.2 项目工程
    *   APP更新策略：推送/拉取
    *   如何统一处理错误*   如何收集用户行为
    *   日志管理有哪些要点？(Debug包开放，release包关闭，错误日志反馈)
    *   打包方式Ant、Maven、Gradle
    *   机型适配情况

*   3.3 开发平台
    *   是否使用过第三方平台
    *   常用开放平台的熟悉度(微信、QQ、微博、支付宝等常用的支付和分享)
    *   是否进行过对第三方平台的统一封装？
    *   是否自己开发过SDK？

*   3.4 设计相关
    *   有哪些常用的设计模式、设计原则
    *   设计模式在Android源码中的应用
    *   设计模式在项目中的应用
    *   项目中的分包方式
    *   MVC在android中的应用，利弊
    *   android项目中的常见层次结构,包划分
    *   有没有设计过项目中的层级结构、包划分

