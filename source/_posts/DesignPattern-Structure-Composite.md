---
title: 《Android 源码设计模式解析与实战》读书笔记 - 组合模式
date: 2017-03-18 12:16:27
tags: [Java, Android, DesignPattern]
---

> 组合模式也称为部分-整体模式，结构型设计模式之一。组合模式比较简单，它将一组相似的对象当作一个对象处理，并根据树状结构来组合对象
> 定义：将对象组合成树形结构以表示“部分-整体”的层次结构，使得用户对单个对象和组合对象的使用具有一致性。

<!--more-->
### 使用场景
1. 表示对象的部分-整体层次结构时。
2. 从一个整体中能够独立出部分模块或功能的场景。

### UML类图
#### 安全组合模式
![Composite](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-composite.png)

* （1）`Component`：抽象根节点，为组合中的对象声明接口。在适当的情况下，实现所有类共有接口的缺省行为。声明一个接口用于访问和管理Component的子节点。可在递归结构中定义一个接口，用于访问一个父节点，并在合适的情况下实现它。
* （2）`Composite`：定义有子节点的那些枝干节点行为，存储子节点，在Component接口中实现与子节点有关的操作。
* （3）`Leaf`：在组合中表示叶子节点对象，叶子节点没有子节点，在组合中定义节点对象的行为。
* （4）`Client`：通过Component接口操纵组合节点的对象。

> 如图这种将组合所使用的方法全部定义在抽象类的方式称为**透明的组合模式**，如果将Component中的Add、Remove、GetChild去除，只在Composite中单独添加，这种方式称为**安全的组合模式**。然而后者违背了依赖倒置原则。

通用组合摸底代码

```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 抽象根节点，为组合中的对象声明接口。在适当的情况下，实现所有类共有接口的缺省行为
 */
public abstract class Component {
    protected String name;

    public Component(String name) {
        this.name = name;
    }

    /**
     * 需要子类实现
     */
    public abstract void doSomething();
}

/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 具体分支节点，定义有子节点行为
 */
public class Composite extends Component {
    /**
     * 存储节点容器
     */
    private List<Component> mComponentList = new ArrayList<>();

    public Composite(String name) {
        super(name);
    }

    @Override
    public void doSomething() {
        System.out.println(name);
        if (mComponentList != null) {
            for (Component component : mComponentList) {
                component.doSomething();
            }
        }
    }

    /**
     * 添加子节点
     * @param component 子节点
     */
    public void addChild(Component component) {
        mComponentList.add(component);
    }

    /**
     * 移除子节点
     * @param component 子节点
     */
    public void removeChild(Component component) {
        mComponentList.remove(component);
    }

    /**
     * 获取子节点
     * @param index 子节点下标
     * @return 子节点
     */
    public Component getChildren(int index) {
        return mComponentList.get(index);
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 具体叶子节点，叶子节点没有子节点，在组合中定义节点对象的行为
 */
public class Leaf extends Component {
    public Leaf(String name) {
        super(name);
    }

    @Override
    public void doSomething() {
        System.out.println(name);
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 通过Component接口操纵组合节点的对象
 */
public class Client {
    public static void main(String[] args) {
        //构造一个根节点
        Composite root = new Composite("Root");

        //构造两个分支子节点
        Composite branch1 = new Composite("Branch1");
        Composite branch2 = new Composite("Branch2");

        //构造两个叶子节点
        Leaf leaf1 = new Leaf("Leaf1");
        Leaf leaf2 = new Leaf("Leaf2");

        //将叶子节点添加到分支子节点
        branch1.addChild(leaf1);
        branch2.addChild(leaf2);

        //将分支子节点添加到根节点
        root.addChild(branch1);
        root.addChild(branch2);

        root.doSomething();
    }
}
```

#### 透明组合模式
上面安全组合模式与依赖倒置原则相违背，所定义的抽象Component在这里的作用不大，既然是面向接口编程，那么更多的应该是完善接口设计，对上面的接口做一些修改：
![](http://blog-1251678165.coscd.myqcloud.com/2018-03-18-composite-transparent.png)

```java
/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 抽象根节点，为组合中的对象声明接口。在适当的情况下，实现所有类共有接口的缺省行为
 */
public abstract class Component {
    protected String name;

    public Component(String name) {
        this.name = name;
    }

    /**
     * 具体由子类实现
     */
    public abstract void doSomething();

    /**
     * 添加子节点
     *
     * @param child 子节点
     */
    public abstract void addChild(Component child);

    /**
     * 移除子节点
     *
     * @param child 子节点
     */
    public abstract void removeChild(Component child);

    /**
     * 获取子节点
     *
     * @param index 子节点下标
     * @return 子节点
     */
    public abstract Component getChildren(int index);
}

/**
 * Created by Weicools on 2017/3/17.
 * <p>
 * desc: 具体分支节点，定义有子节点行为
 */
public class Composite extends Component {
    /**
     * 存储节点容器
     */
    private List<Component> mComponentList = new ArrayList<>();

    public Composite(String name) {
        super(name);
    }

    @Override
    public void doSomething() {
        System.out.println(name);
        if (mComponentList != null) {
            for (Component component : mComponentList) {
                component.doSomething();
            }
        }
    }

    @Override
    public void addChild(Component component) {
        mComponentList.add(component);
    }

    @Override
    public void removeChild(Component component) {
        mComponentList.remove(component);
    }

    @Override
    public Component getChildren(int index) {
        return mComponentList.get(index);
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 具体叶子节点，叶子节点没有子节点，某些操作抛出异常
 */
public class Leaf extends Component {
    public Leaf(String name) {
        super(name);
    }

    @Override
    public void doSomething() {
        System.out.println(name);
    }

    @Override
    public void addChild(Component child) {
        throw new UnsupportedOperationException("叶子节点没有子节点");
    }

    @Override
    public void removeChild(Component child) {
        throw new UnsupportedOperationException("叶子节点没有子节点");
    }

    @Override
    public Component getChildren(int index) {
        throw new UnsupportedOperationException("叶子节点没有子节点");
    }
}

/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 通过Component接口操纵组合节点的对象
 */
public class Client {
    public static void main(String[] args) {
        //构造一个根节点
        Composite root = new Composite("Root");

        //构造两个分支子节点
        Composite branch1 = new Composite("Branch1");
        Composite branch2 = new Composite("Branch2");

        //构造两个叶子节点
        Leaf leaf1 = new Leaf("Leaf1");
        Leaf leaf2 = new Leaf("Leaf2");

        //将叶子节点添加到分支子节点
        branch1.addChild(leaf1);
        branch2.addChild(leaf2);

        //将分支子节点添加到根节点
        root.addChild(branch1);
        root.addChild(branch2);

        root.doSomething();
    }
}
```

### 简单实现

> 以文件系统为例，文件系统包含文件和文件夹，而文件夹也可以包含文件。

#### 文件和文件夹的抽象类：（Component）

```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 表示文件和文件夹的抽象类(Component角色)
 */
public abstract class Dir {
    //申明List成员变量存储文件夹下所有元素
    protected List<Dir> mDirList = new ArrayList<>();

    //文件名或者文件夹名称
    private String name;

    public Dir(String name) {
        this.name = name;
    }

    /**
     * 添加一个文件或者文件夹
     *
     * @param dir 文件或者文件夹
     */
    public abstract void addDir(Dir dir);

    /**
     * 删除文件或者文件夹
     *
     * @param dir 文件或者文件夹
     */
    public abstract void removeDir(Dir dir);

    /**
     * 情况文件夹下所有元素
     */
    public abstract void clear();

    /**
     * 输出文件夹目录结构
     */
    public abstract void print();

    /**
     * 获取文件夹下所有的文件和子文件夹
     *
     * @return 文件和子文件夹
     */
    public abstract List<Dir> getFiles();

    public String getName() {
        return name;
    }
}
```

#### 表示文件夹的类：（Composite）
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 文件夹类(Composite角色)
 */
public class Folder extends Dir {
    public Folder(String name) {
        super(name);
    }

    @Override
    public void addDir(Dir dir) {
        mDirList.add(dir);
    }

    @Override
    public void removeDir(Dir dir) {
        mDirList.remove(dir);
    }

    @Override
    public void clear() {
        mDirList.clear();
    }

    @Override
    public void print() {
        System.out.print(getName() + "(");
        Iterator<Dir> iterator = mDirList.iterator();
        while (iterator.hasNext()) {
            Dir dir = iterator.next();
            dir.print();
            if (iterator.hasNext()) {
                System.out.print(", ");
            }
        }
        System.out.print(")");
    }

    @Override
    public List<Dir> getFiles() {
        return mDirList;
    }
}
```

#### 表示文件夹的类：（Leaf）
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 文件类(Leaf角色)
 */
public class File extends Dir {
    public File(String name) {
        super(name);
    }

    @Override
    public void addDir(Dir dir) {
        throw new UnsupportedOperationException("文件不支持该操作");
    }

    @Override
    public void removeDir(Dir dir) {
        throw new UnsupportedOperationException("文件不支持该操作");
    }

    @Override
    public void clear() {
        throw new UnsupportedOperationException("文件不支持该操作");
    }

    @Override
    public void print() {
        System.out.print(getName());
    }

    @Override
    public List<Dir> getFiles() {
        throw new UnsupportedOperationException("文件不支持该操作");
    }
}
```

#### 客户测试类：
```java
/**
 * Created by Weicools on 2017/3/18.
 * <p>
 * desc: 测试
 */
public class Client {
    public static void main(String[] args) {
        //构造一个目录表示SD卡根目录
        Dir sdCard = new Folder("Storage");

        //SD卡下有一个文件"设计模式.pdf"
        sdCard.addDir(new File("设计模式.pdf"));

        //SD卡下有3个子文件夹"ADM", "Android", "Books"
        Dir adm = new Folder("ADM");
        adm.addDir(new File("google.html"));
        sdCard.addDir(adm);

        Dir an = new Folder("Android");
        an.addDir(new File("cache.txt"));
        sdCard.addDir(an);

        Dir books = new Folder("Books");
        books.addDir(new File("跳槽指南.md"));
        sdCard.addDir(books);

        sdCard.print();
    }
}
```

#### 输出结果：

```
Storage(设计模式.pdf, ADM(google.html), Android(cache.txt), Books(跳槽指南.md))
```

### Android源码中的模式实现
- View和ViewGroup的嵌套组合

View和ViewGroup的结构很像上面的UML类图，不过View的视图层级使用的是安全的组合模式。ViewGroup有对View的addView、removeView、getChildAt等方法，想必大家也很熟悉。

### 总结
1. 优点
    * （1）组合模式可以清楚地定义分层次的复杂对象，表示对象的全部或部分层次，他让高层模块忽略了层次的差异，方便对整个层次结构进行控制。
    * （2）简化了高层模块的代码。
    * （3）在组合模式中增加新的枝干构件和叶子构件都很方便，无须对现有类库进行修改，符合“开闭原则”。
    * （4）对树形结构的控制变得简单。

2. 缺点
    * 组合模式不容易限制组合中的构件。因为大多数情况下，它们都来自于相同的抽象层，此时，必须进行类型检查来实现，这个实现过程较为复杂。

