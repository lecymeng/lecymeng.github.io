---
title: 《Android 源码设计模式解析与实战》读书笔记 - 迭代器模式
date: 2017-03-18 12:16:27
tags: [Java, 设计模式, Android]
---

> 迭代器模式，又叫做游标模式，是行为型设计模式之一。我们知道对容器对象的访问必然会涉及遍历算法，我们可以将遍历的方法封装在容器中，或者不提供遍历方法，让使用容器的人自己去实现去吧。这两种情况好像都能够解决问题。
> 然而在前一种情况，容器承受了过多的功能，它不仅要负责自己“容器”内的元素维护（添加、删除等等），而且还要提供遍历自身的接口；而且由于遍历状态保存的问题，不能对同一个容器对象同时进行多个遍历。第二种方式倒是省事，却又将容器的内部细节暴露无遗。
> 正因于此，迭代器模式应运而生，在客户访问类与容器体之间插入一个第三者–迭代器，很好的解决了上述弊端。
> 定义：提供一种方法顺序访问一个容器对象中的各个元素，而又不需要暴露该对象的内部表示。

<!--more-->
### 使用场景
遍历一个容器对象时。

### UML类图
![](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-Iterator.png)

### 简单实现
> 用书中的例子：小民和小辉分别在公司两个事业部，某天老板安排任务让他们俩统计一下各自部门的员工数据。

#### 员工实体类：
```java
public class Employee {

    private String name;// 姓名
    private int age;// 年龄
    private String sex;// 性别
    private String position;// 职位

    public Employee(String name, int age, String sex, String position) {
        super();
        this.name = name;
        this.age = age;
        this.sex = sex;
        this.position = position;
    }

    // 简化代码，省略setter和getter方法

    @Override
    public String toString() {
        return "Employee{" + "name='" + name + '\'' + ", age=" + age + ", sex="
                + sex + ", position='" + position + '\'' + "}";
    }
}
```

#### 小辉部门：
```java
public class CompanyHui {

private Employee[] array = new Employee[3];

    public CompanyHui(){
        array[0] = new Employee("辉哥", 28, "男", "程序猿");
        array[1] = new Employee("小红", 23, "男", "程序猿");
        array[2] = new Employee("小辉", 25, "男", "程序猿");
    }

    public Employee[] getEmployees(){
        return array;
    }
}
```

#### 小民部门：
```java
public class CompanyMin {

    private List<Employee> list = new ArrayList<>();

    public CompanyMin(){
        list.add(new Employee("小民", 26, "男", "程序猿"));
        list.add(new Employee("小芸", 22, "女", "测试"));
        list.add(new Employee("小方", 18, "女", "测试"));
        list.add(new Employee("可儿", 21, "女", "设计"));
        list.add(new Employee("朗情", 19, "女", "设计")); //吐槽一下，为什么就小民一个男的，小辉部门全男的。
    }

    public List<Employee> getEmployees(){
        return list;
    }
}
```

#### Boss查看：
```java
public class Boss {
    public static void main(String[] args) {
        CompanyHui hui = new CompanyHui();
        Employee[] huiList = hui.getEmployees();
        for(int i = 0; i < huiList.length; i++){
            System.out.println(huiList[i]);
        }

        CompanyMin min = new CompanyMin();
        List minList = min.getEmployees();
        for(int i = 0; i < minList.size(); i++){
            System.out.println(minList.get(i).toString());
        }
    }
}
```

#### 结果：
```
Employee{name='辉哥', age=28, sex=男, position='程序猿'}
Employee{name='小红', age=23, sex=男, position='程序猿'}
Employee{name='小辉', age=25, sex=男, position='程序猿'}
Employee{name='小民', age=26, sex=男, position='程序猿'}
Employee{name='小芸', age=22, sex=女, position='测试'}
Employee{name='小方', age=18, sex=女, position='测试'}
Employee{name='可儿', age=21, sex=女, position='设计'}
Employee{name='朗情', age=19, sex=女, position='设计'}12345678
```

这样看似也没有问题，但是如果有多个部门，每个部门有各自的实现，那么我们就要在Boss类中增加一遍遍历逻辑，这样Boss类的功能会越来越多，同时暴露了内部细节。那么我们需要定义一个迭代器接口：

```java
public interface Iterator {

    /**
     * 是否还有下一个元素 
     * 
     * @return true表示有，false表示没有
     */
    boolean hasNext();

    /**
     * 返回当前元素，并将位置移至下一位
     */
    Object next();
}
```

小民的迭代器：

```java
public class MinIterator implements Iterator{

    private List<Employee> list;
    private int position;

    public MinIterator(List<Employee> list){
        this.list = list;
    }


    @Override
    public boolean hasNext() {
        return !(position > list.size() - 1 || list.get(position) == null);
    }

    @Override
    public Object next() {
        Employee e = list.get(position);
        position++;
        return e;
    }

}
```

小辉的迭代器：

```java
public class HuiIterator implements Iterator{

    private Employee[] array;
    private int position;

    public HuiIterator(Employee[] array){
        this.array = array;
    }

    @Override
    public boolean hasNext() {
        return !(position > array.length - 1 || array[position] == null);
    }

    @Override
    public Object next() {
        Employee e = array[position];
        position++;
        return e;
    }

}
```

定义容器类的接口

```java
public interface Company {

    /**
     * 返回一个迭代器对象
     * 
     * @return 迭代器对象
     */
    Iterator iterator();

}
```

修改一下之前的两个容器类：

```java
public class CompanyHui implements Company{

    private Employee[] array = new Employee[3];

    public CompanyHui(){
        array[0] = new Employee("辉哥", 28, "男", "程序猿");
        array[1] = new Employee("小红", 23, "男", "程序猿");
        array[2] = new Employee("小辉", 25, "男", "程序猿");
    }

    public Employee[] getEmployees(){
        return array;
    }

    @Override
    public Iterator iterator() {
        return new HuiIterator(array);
    }
}
```

```java
public class CompanyMin implements Company{

    private List<Employee> list = new ArrayList<>();

    public CompanyMin(){
        list.add(new Employee("小民", 26, "男", "程序猿"));
        list.add(new Employee("小芸", 22, "女", "测试"));
        list.add(new Employee("小方", 18, "女", "测试"));
        list.add(new Employee("可儿", 21, "女", "设计"));
        list.add(new Employee("朗情", 19, "女", "设计"));
    }

    public List<Employee> getEmployees(){
        return list;
    }

    @Override
    public Iterator iterator() {
        return new MinIterator(list);
    }
}
```

Boss查看：

```java
public class Boss {
    public static void main(String[] args) {
        CompanyHui hui = new CompanyHui();
        check(hui.iterator());

        CompanyMin min = new CompanyMin();
        check(min.iterator());
    }

    private static void check(Iterator iterator){
        while (iterator.hasNext()) {
            System.out.println(iterator.next().toString());
        }
    }
}
```

结果不变，就不重复写了。

### Android源码中的模式实现
#### Cursor

当我们使用SQLiteDatabase的query方法查询数据库时，会返回一个Cursor游标对象，该游标的实质就是一个具体的迭代器，我们可以使用它来遍历数据库查询所得的结果集。

### 总结

> 迭代器模式发展至今，几乎所有的高级语言都有相应的内置实现，对于开发者而言，已经极少会自己去实现迭代器了，所以本章内容更多的是了解而非应用。

1. 优点
    * 符合面向对象设计原则中的单一职责原则。
    * 支持对容器对象的多种遍历。弱化了容器类与遍历算法之间的关系。

2. 缺点
    * 类文件的增加。
    * 会出现ConcurrentModificationException异常。
    * 遍历过程是一个单向且不可逆的遍历。

### 参考
● [深入浅出Java设计模式之迭代器模式](http://dev.yesky.com/474/2168474.shtml)
● [ConcurrentModificationException异常](http://blog.csdn.net/lirunfa/article/details/7353857)

