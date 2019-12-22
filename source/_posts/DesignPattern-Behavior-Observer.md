---
title: 《Android 源码设计模式解析与实战》读书笔记 - 观察者模式
date: 2017-03-12 10:16:27
tags: [Java, Android, DesignPattern]
---

> 观察者模式是一个使用率非常高的模式，它最常用在GUI系统、订阅–发布系统。因为这个模式的一个重要作用就是解耦，将被观察者和观察者解耦，使得它们之间的依赖性更小，甚至做到毫无依赖。比如安卓的开源项目EventBus、Otto、AndroidEventBus等事件总线类的和RxJava响应式编程其核心都是使用观察者模式。

> 观察者模式是定义对象间一对多的依赖关系，使得每当一个对象状态改变时可以通知 依赖于它的所有对象进行更新。

<!--more-->
### 1.使用场景
1. 关联行为场景，需要注意的是，关联行为是可拆分的，而不是“组合”关系。
2. 事件多级触发场景。
3. 跨系统的消息交换场景，如消息队列、事件总线的处理机制。

### 2.UML类图
![](http://blog-1251678165.coscd.myqcloud.com/2018-03-16-Observer.jpg)
UML类图介绍：

1. Subject：抽象主题，也就是被观察者角色(Observable)，抽象主题把所有观察者对象的引用保存到一个集合里，每个主题都可以有任意数量的观察者，抽象主题提供接口可以删除和增加观察者对象
2. ConcreteSubject：具体主题，将有关状态存入具体的观察者对象，在具体主题的内部发生改变时，给所有注册过的观察者发送通知。
3. Observer：抽象观察者，它定义了一个更新接口，使得在得到被观察者的更改通知时更新自身状态。
4. ConcreteObserver：具体观察者，实现抽象观察者角色所定义的更新接口，在被观察者的状态发生变化时更新自身状态。

### 3.实现示例
* **实现用户追番**

```java
/**
 * Created by Weicools on 2017/3/16.
 * <p>
 * desc: 抽象观察者类，为所有具体观察者定义一个接口，在得到通知时更新自己
 */
public interface Observer {
    /**
     * 更新消息
     * @param message 消息
     */
    void update(String message);
}

/**
 * Created by Weicools on 2017/3/16.
 * <p>
 * desc: 具体的观察者类，也就是订阅者
 */
public class User implements Observer {
    private String mName;

    public User(String name) {
        mName = name;
    }

    @Override
    public void update(String message) {
        System.out.println(mName + ": 订阅的" + message + "更新了");
    }
}

/**
 * Created by Weicools on 2017/3/16.
 * <p>
 * desc: 抽象被观察者类
 */
public abstract class Subject {
    /**
     * 推送消息
     * @param message 内容
     */
    public abstract void pushMsg(String message);

    /**
     * 订阅
     * @param observer 订阅者
     */
    public abstract void addObserver(Observer observer);
}

/**
 * Created by Weicools on 2017/3/16.
 * <p>
 * desc: 具体的被观察者类，也就是订阅的节目
 */
public class Animation extends Subject {
    private List<Observer> mObserverList = new ArrayList<>();//储存订阅者

    @Override
    public void pushMsg(String message) {
        for (Observer observer : mObserverList) {
            observer.update(message);
        }
    }

    @Override
    public void addObserver(Observer observer) {
        mObserverList.add(observer);
    }
}

/**
 * Created by Weicools on 2017/3/16.
 * <p>
 * desc:
 */
public class Test {
    public static void main(String[] args) {
        // 观察者, 即订阅用户
        User user1 = new User("XXX");
        User user2 = new User("YYY");
        User user3 = new User("ZZZ");

        // 被观察者, 用户订阅的……
        Animation animation = new Animation();
        animation.addObserver(user1);
        animation.addObserver(user2);
        animation.addObserver(user3);
        animation.pushMsg("境界的彼方");
    }
}
```

```
// 输出
XXX: 订阅的境界的彼方更新了
YYY: 订阅的境界的彼方更新了
ZZZ: 订阅的境界的彼方更新了
```

由上面的代码可以看出实现了一对多的消息推送，推送消息都是依赖Observer和Observable这些抽象类，而User和Teleplay完全没有耦合，保证了订阅系统的灵活性和可扩展性。

### 4.Android源码中的观察者模式
#### BaseAdapter
在ListView的适配器中我们都是继承BaseAdapter。下面来简单分析分析。BaseAdapter 部分代码：

```Java
public abstract class BaseAdapter implements ListAdapter, SpinnerAdapter {

    //数据集观察者
    private final DataSetObservable mDataSetObservable = new DataSetObservable();

    public boolean hasStableIds() {
        return false;
    }

    public void registerDataSetObserver(DataSetObserver observer) {
        mDataSetObservable.registerObserver(observer);
    }

    public void unregisterDataSetObserver(DataSetObserver observer) {
        mDataSetObservable.unregisterObserver(observer);
    }

    /**
     * 当数据集变化时，通知所有观察者
     */
    public void notifyDataSetChanged() {
        mDataSetObservable.notifyChanged();
    }
}
```

看看mDataSetObservable.notifyChanged()方法：

```Java
public class DataSetObservable extends Observable<DataSetObserver> {
    /**
     * Invokes {@link DataSetObserver#onChanged} on each observer.
     * Called when the contents of the data set have changed.  The recipient
     * will obtain the new contents the next time it queries the data set.
     */
    public void notifyChanged() {
        synchronized(mObservers) {
            // since onChanged() is implemented by the app, it could do anything, including
            // removing itself from {@link mObservers} - and that could cause problems if
            // an iterator is used on the ArrayList {@link mObservers}.
            // to avoid such problems, just march thru the list in the reverse order.
            for (int i = mObservers.size() - 1; i >= 0; i--) {
                mObservers.get(i).onChanged();
            }
        }
    }
}
```

可以看出在mDataSetObservable.notifyChanged()中遍历所有观察者，并调用他们的onChanged()，从而告知观察者需要做什么。

那么观察者怎么来的，那就是setAdapter方法，代码如下：

```Java
@Override
public void setAdapter(ListAdapter adapter) {
    if (mAdapter != null && mDataSetObserver != null) {
        mAdapter.unregisterDataSetObserver(mDataSetObserver);
    }

    resetList();
    mRecycler.clear();

    if (mHeaderViewInfos.size() > 0|| mFooterViewInfos.size() > 0) {
        mAdapter = new HeaderViewListAdapter(mHeaderViewInfos, mFooterViewInfos, adapter);
    } else {
        mAdapter = adapter;
    }

    mOldSelectedPosition = INVALID_POSITION;
    mOldSelectedRowId = INVALID_ROW_ID;

    // AbsListView#setAdapter will update choice mode states.
    super.setAdapter(adapter);

    if (mAdapter != null) {
        mAreAllItemsSelectable = mAdapter.areAllItemsEnabled();
        mOldItemCount = mItemCount;
        mItemCount = mAdapter.getCount();
        checkFocus();

        mDataSetObserver = new AdapterDataSetObserver();
        mAdapter.registerDataSetObserver(mDataSetObserver);//注册观察者

        ......省略
    }
}
```

AdapterDataSetObserver定义在ListView的父类AbsListView中，是一个数据集观察者，代码：

```Java
class AdapterDataSetObserver extends AdapterView<ListAdapter>.AdapterDataSetObserver {
    @Override
    public void onChanged() {
        super.onChanged();
        if (mFastScroller != null) {
            mFastScroller.onSectionsChanged();
        }
    }

    @Override
    public void onInvalidated() {
        super.onInvalidated();
        if (mFastScroller != null) {
            mFastScroller.onSectionsChanged();
        }
    }
}
```

它由继承自AbsListView的父类AdapterView的AdapterDataSetObserver, 代码如下 :

```Java
class AdapterDataSetObserver extends DataSetObserver {

    private Parcelable mInstanceState = null;
    // 上文有说道，调用Adapter的notifyDataSetChanged的时候会调用所有观察者的onChanged方法,核心实现就在这里
    @Override
    public void onChanged() {
        mDataChanged = true;
        mOldItemCount = mItemCount;
        // 获取Adapter中数据的数量
        mItemCount = getAdapter().getCount();

        // Detect the case where a cursor that was previously invalidated has
        // been repopulated with new data.
        if (AdapterView.this.getAdapter().hasStableIds() && mInstanceState != null
                && mOldItemCount == 0 && mItemCount > 0) {
            AdapterView.this.onRestoreInstanceState(mInstanceState);
            mInstanceState = null;
        } else {
            rememberSyncState();
        }
        checkFocus();
        // 重新布局ListView、GridView等AdapterView组件
        requestLayout();
    }

    // 代码省略

    public void clearSavedState() {
        mInstanceState = null;
    }
}
```

当ListView的数据发生变化时，调用Adapter的notifyDataSetChanged函数，这个函数又会调用DataSetObservable的notifyChanged函数，这个函数会调用所有观察者 (AdapterDataSetObserver) 的onChanged方法，这就是一个观察者模式！

### 5.Summary
1. 优点
    * 观察者和被观察者之间是抽象耦合，将观察者与被观察者完全隔离，只依赖于Observer和Observable抽象，应对业务变化。
    * 增强系统的灵活性和可扩展性。
2. 缺点
    * 在应用观察者模式时需要考虑一下开发效率和运行效率的问题，程序中包括一个被观察者、多个观察者，开发、调试等内容会比较复杂，而且在Java中消息的通知一般是顺序执行，那么一个观察者卡顿，会影响整体的执行效率，在这种情况下，一般会采用异步实现。

