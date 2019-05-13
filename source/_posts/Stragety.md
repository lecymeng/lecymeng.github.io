---
title: 《Android 源码设计模式解析与实战》读书笔记 - 策略模式
date: 2017-03-18 12:16:27
tags: [Java, 设计模式, Android]
---

> 通常如果一个问题有多个解决方案时，最简单的就是利用if-else或者switch-case方式根据不同的情景选择不同的解决方案，但是这样耦合性太高 、代码臃肿、难以维护等。这时就可以使用策略模式来解决。
> 定义：策略模式定义了一系列的算法，并将每一个算法封装起来，而且使他们还可以相互替换。策略模式让算法独立于使用它的客户而独立变化。

<!--more-->
### 使用场景
1. 针对同一类型问题的多种处理方式，仅仅是具体行为有差别时。 
2. 需要安全地封装多种同一类型的操作时。 
3. 出现同一抽象类有多个子类，而又需要使用if-else或者switch-case来选择具体子类时。

### UML类图
![](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-Stragety-2.png)

### 简单实现
> 需求：计算图书价格，初级会员没有折扣，中级会员打9折，高级会员打8折。如果一般写法，应该是if-else判断他是什么级别的会员，在计算相应的折扣。下面使用策略模式来进行实现。

#### 抽象折扣类
```java
public interface MemberStrategy {
    /**
     * 计算图书的价格
     * @param booksPrice    图书的原价
     * @return    计算出打折后的价格
     */
    public double calcPrice(double booksPrice);
}
```

#### 初级会员折扣类
```java
public class PrimaryMemberStrategy implements MemberStrategy{
    /**
     * 初级会员折扣
     */
    @Override
    public double calcPrice(double booksPrice) {
        System.out.println("对于初级会员的没有折扣");
        return booksPrice;
    }
}
```

#### 中级会员折扣类
```java
public class IntermediateMemberStrategy implements MemberStrategy{
    /**
     * 中级会员折扣
     */
    @Override
    public double calcPrice(double booksPrice) {
        System.out.println("对于中级会员的折扣为10%");
        return booksPrice * 0.9;
    }
}12345678910
```

#### 高级会员折扣类
```java
public class AdvancedMemberStrategy implements MemberStrategy{
    /**
     * 高级会员折扣
     */
    @Override
    public double calcPrice(double booksPrice) {
        System.out.println("对于高级会员的折扣为20%");
        return booksPrice * 0.8;
    }
}
```

#### 价格类
```java
public class Price {
    //持有一个具体的策略对象
    private MemberStrategy strategy;
    /**
     * 构造函数，传入一个具体的策略对象
     * @param strategy    具体的策略对象
     */
    public Price(MemberStrategy strategy){
        this.strategy = strategy;
    }

    /**
     * 计算图书的价格
     * @param booksPrice    图书的原价
     * @return    计算出打折后的价格
     */
    public double quote(double booksPrice){
        return this.strategy.calcPrice(booksPrice);
    }
}
```

#### 客户端
```java
public class Client {

    public static void main(String[] args) {
        //选择并创建需要使用的策略对象
        MemberStrategy strategy1 = new AdvancedMemberStrategy();
        //创建环境
        Price price = new Price(strategy1);
        //计算价格
        double quote = price.quote(300);
        System.out.println("图书的最终价格为：" + quote);
    }

}
```

#### 结果
```
对于高级会员的折扣为20%
图书的最终价格为：240.012
```

### 策略模式和工厂模式的区别
| 工厂模式                         | 策略模式                     |
| -------------------------------- | ---------------------------- |
| 创建型的设计模式                 | 行为型的设计模式             |
| 关注对象创建                     | 关注行为的选择               |
| 黑盒子（无需知道具体的实现过程） | 白盒子（知道具体的实现过程） |

### Android源码中的策略模式实现
时间插值器（TimeInterpolator）

LinearInterpolator、AccelerateInterpolator、CycleInterpolator等实现Interpolator，通过getInterpolator（float input）获取当前的时间百分比，以此来计算动画的属性值。

### 总结
策略模式主要用来分离算法，在相同的行为抽象下有不同的具体实现策略。这个模式很好地演示了开闭原则，也就是定义抽象，注入不同的实现，从而达到很好的可扩展性。

1. 优点：
    * 结构清晰明了、使用简单直观。 
    * 耦合度相对而言较低，扩展方便。 
    * 操作封装也更为彻底，数据更为安全。
2. 缺点：
    * 随着策略的增加，子类也会变得繁多。

### 参考
1. [《JAVA与模式》之策略模式](http://www.cnblogs.com/java-my-life/archive/2012/05/10/2491891.html) 
2. [策略模式和工厂模式的区别](http://www.cnblogs.com/me115/p/3790615.html)

