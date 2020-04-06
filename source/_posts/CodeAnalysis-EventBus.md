---
title: EventBus 源码解析
date: 2018-09-13 23:18:12
tags: [Android, SourceCode Analysis]
---

# EventBus 源码解析
## EventBus简介
[](http://a.codekk.com/detail/Android/Trinea/EventBus%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90)

EventBus是Android端优化的publish/subscribe消息总线，简化了应用程序内各组件间、组件与后台线程的通信，主要由三个部分组成:
* 事件Event: 可以是任意对象，通过事件的发布者将事件进行传递
* 事件订阅者Subscriber: 接收特定事件
* 事件发布者Publisher: 用于通知Subscriber有事件发生，可以在任意线程任意位置发送事件
<!--more-->

  ![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2018-11-13-142814.jpg)
  
  上图解释了整个EventBus的大概工作流程：`发布者Publisher`将事件Event通过`post()方法`发送；EventBus内部进行处理，找到订阅了该`事件Event`的`订阅者Subscriber`；然后该事件Event的订阅者Subscriber通过`onEvent()方法`接收事件进行相关处理（关于onEvent()在EventBus 3.0中有改动，下面详细说明）

## EventBus简单使用
添加EventBus依赖到项目 `implementation 'org.greenrobot:eventbus:3.1.1'`
构造事件（Event）对象。也就是发送消息类 每一个消息类，对应一种事件。这里我们定义两个消息发送类：
```java
public class NewsEvent {
    private String message;

    public NewsEvent(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}

public class ToastEvent {
    private String content;

    public ToastEvent(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }
}
```

注册/解除事件订阅（Subscriber）：
```java
//一般在onCreate()方法中进行注册订阅。在onDestory()方法中进行解除订阅
@Override
protected void onCreate() {
    super.onCreate();
    //注册事件 其中this代表订阅者
    //通过register(this)来表示该订阅者进行了订阅
    EventBus.getDefault().register(this);
}

@Override
protected void onDestroy() {
    super.onDestroy();
    //解除注册事件
    EventBus.getDefault().unregister(this);
}
```

具体注册了对什么事件的订阅，这个需要onEvent()方法来说明。在EventBus 3.0之前，onEvent()方法是用来接收指定事件（Event）类型对象，然后进行相关处理操作。在EventBus 3.0之后，onEvent()方法可以自定义方法名，不过要加入注解@Subscribe。
```java
@Subscribe
public void onToastEvent(ToastEvent event){
//通过onToastEvent(ToastEvent event)表示指定对事件ToastEvent的订阅
Toast.makeText(MainActivity.this,event.getContent(),Toast.LENGTH_SHORT).show();
}
```

发送消息 订阅已经完成，那么便可以发送订阅了`EventBus.getDefault().post(new ToastEvent("Toast,发个提示，祝大家新年快乐！"));`
那么onToastEvent(ToastEvent event)会收到事件，并弹出提示，EventBus的基础使用流程就是这样的。

## EventBus进阶使用
### 线程模式ThreadMode
当你接收的的事件后，如果处于非UI线程，你要更新UI怎么办？如果处于UI线程，你要进行耗时操作，怎么办?等等其他情况，通过ThreadMode统统帮你解决

```java
@Subscribe(threadMode = ThreadMode.MainThread)
public void  onNewsEvent(NewsEvent event){
    String message = event.getMessage();
    mTv_message.setText(message);
}
```
使用起来很简单，通过@Subscribe(threadMode = ThreadMode.MainThread)即可指定。 下面具体介绍下ThreadMode：
1. **PostThread**：事件的处理在和事件的发送在相同的进程，所以事件处理时间不应太长，不然影响事件的发送线程
2. **MainThread**：事件的处理会在UI线程中执行。事件处理时间不能太长，这个不用说的，长了会ANR的
3. **BackgroundThread**：如果事件是在UI线程中发布出来的，那么事件处理就会在子线程中运行，如果事件本来就是子线程中发布出来的，那么事件处理直接在该子线程中执行。所有待处理事件会被加到一个队列中，由对应线程依次处理这些事件，如果某个事件处理时间太长，会阻塞后面的事件的派发或处理
4. **Async**：事件处理会在单独的线程中执行，主要用于在后台线程中执行耗时操作，每个事件会开启一个线程

### priority事件优先级
事件的优先级类似广播的优先级，优先级越高优先获得消息，用法展示：

```java
@Subscribe(priority = 100)
public void onToastEvent(ToastEvent event){
    Toast.makeText(MainActivity.this, event.getContent(), Toast.LENGTH_SHORT).show();
}
```

当多个订阅者（Subscriber）对同一种事件类型进行订阅时，即对应的事件处理方法中接收的事件类型一致，则优先级高（priority 设置的值越大），则**会先接收事件进行处理；优先级低（priority 设置的值越小），则会后接收事件进行处理**

除此之外，EventBus也可以终止对事件继续传递的功能，这样其他优先级比100低，并且订阅了该事件的订阅者就会接收不到该事件
```java
@Subscribe(priority = 100)
public void onToastEvent(ToastEvent event){
    Toast.makeText(MainActivity.this, event.getContent(), Toast.LENGTH_SHORT).show();
    //拦截事件
    EventBus.getDefault().cancelEventDelivery(event);
}
```

### EventBus黏性事件
EventBus除了普通事件也支持粘性事件。可以理解成：订阅在发布事件之后，但同样可以收到事件。订阅/解除订阅和普通事件一样，但是处理订阅的方法有所不同，需要注解中添加`sticky = true`
```java
@Subscribe(priority = 100, sticky = true)
public void onToastEvent(ToastEvent event){
    Toast.makeText(MainActivity.this, event.getContent(), Toast.LENGTH_SHORT).show();
    //拦截事件
    EventBus.getDefault().cancelEventDelivery(event);
}
```

这样假设一个ToastEvent 的事件已经发布，此时还没有注册订阅。当设置了sticky = true，在ToastEvent 的事件发布后，进行注册，依然能够接收到之前发布的事件。
不过这个时候，发布事件的方式就改变了：`EventBus.getDefault().postSticky(new ToastEvent("Toast,发个提示，祝大家新年快乐！"));`
我们如果不再需要该粘性事件我们可以移除：`EventBus.getDefault().removeStickyEvent(ToastEvent.class);`
或者调用移除所有粘性事件：`EventBus.getDefault().removeAllStickyEvents();`

### EventBus配置
EventBus在2.3版本中添加了EventBuilder去配置EventBus的各方各面。比如：如何去构建一个在发布事件时没有订阅者时保持沉默的EventBus。
```java
EventBus eventBus = EventBus.builder()
.logNoSubscriberMessages(false)
.sendNoSubscriberEvent(false)
.build();
```
通过上述设置，当一个事件没有订阅者时，不会输出log信息，也不会发布一条默认信息。

配置默认的EventBus实例，使用EventBus.getDefault()是一个简单的方法。获取一个单例的EventBus实例。EventBusBuilder也允许使用installDefaultEventBus方法去配置默认的EventBus实例。

注意：不同的EventBus 的对象的数据是不共享的。通过一个EventBus 对象去发布事件，只有通过同一个EventBus 对象订阅事件，才能接收该事件。所以以上使用EventBus.getDefault()获得的都是同一个实例。

## 源码解析

1. 注册订阅：`EventBus.getDefault().register(this);`
2. 事件处理

    ```java
    @Subscribe(threadMode = ThreadMode.MainThread)
    public void  onNewsEvent(NewsEvent event){
        String message = event.getMessage();
        mTv_message.setText(message);
    }
    ```
3. 发布事件：`EventBus.getDefault().post(new NewsEvent("我是来自SecondActivity的消息，你好！"));`

以上是EventBus的基本使用。我们先从getDefault说起

### getDefault()

```java
static volatile EventBus defaultInstance;
public static EventBus getDefault() {
    if (defaultInstance == null) {
        synchronized (EventBus.class) {
            if (defaultInstance == null) {
                defaultInstance = new EventBus();
            }
        }
    }
    return defaultInstance;
}
```
通过上述代码可以得知，getDefault()中通过双检查锁（DCL）机制实现了EventBus的单例机制，获得了一个默认配置的EventBus对象

### register()
在了解register()之前，我们先要了解一下EventBus中的几个关键的成员变量。方便对下面内容的理解：
```java
/** Map<订阅事件, 订阅该事件的订阅者集合> */
private final Map<Class<?>, CopyOnWriteArrayList<Subscription>> subscriptionsByEventType;

/** Map<订阅者, 订阅事件集合> */
private final Map<Object, List<Class<?>>> typesBySubscriber;

/** Map<订阅事件类类型,订阅事件实例对象>. */
private final Map<Class<?>, Object> stickyEvents;
```
下面看具体的register()中执行的代码：
```java
public void register(Object subscriber) {
    //订阅者类型
    Class<?> subscriberClass = subscriber.getClass(); 
    //判断该类是不是匿名类，如果是匿名累要使用反射
    boolean forceReflection = subscriberClass.isAnonymousClass();
    //获取订阅者全部的响应函数信息（即上面的onNewsEvent()之类的方法）
    List<SubscriberMethod> subscriberMethods = subscriberMethodFinder.findSubscriberMethods(subscriberClass, forceReflection);
    //循环每一个事件响应函数，执行 subscribe()方法，更新订阅相关信息
    for (SubscriberMethod subscriberMethod : subscriberMethods) {
        subscribe(subscriber, subscriberMethod);
    }
}
```
由此可见，register()第一步获取订阅者的类类型. 第二步,通过SubscriberMethodFinder类来解析订阅者类,获取所有的响应函数集合. 第三步,遍历订阅函数,执行 subscribe()方法，更新订阅相关信息。 关于 subscriberMethodFinder这里就不介绍了。先跟着线索，继续看subscribe()方法。 

subscribe 函数分三步：
```java
//第一步: 通过subscriptionsByEventType得到该事件类型所有订阅者信息队列，根据优先级将当前订阅者信息插入到订阅者队列subscriptionsByEventType中；
//获取订阅的事件类型
Class<?> eventType = subscriberMethod.eventType;
//获取订阅该事件的订阅者集合
CopyOnWriteArrayList<Subscription> subscriptions = subscriptionsByEventType.get(eventType);
//把通过register()订阅的订阅者包装成Subscription 对象
Subscription newSubscription = new Subscription(subscriber, subscriberMethod);
//订阅者集合为空，创建新的集合，并把newSubscription 加入
if (subscriptions == null) {
  subscriptions = new CopyOnWriteArrayList<Subscription>();
  subscriptionsByEventType.put(eventType, subscriptions);
} else {
  //集合中已经有该订阅者，抛出异常。不能重复订阅
  if (subscriptions.contains(newSubscription)) {
    throw new EventBusException(
        "Subscriber " + subscriber.getClass() + " already registered to event " + eventType);
  }
}
//把新的订阅者按照优先级加入到订阅者集合中。
synchronized (subscriptions) {
  int size = subscriptions.size();
  for (int i = 0; i <= size; i++) {
    if (i == size || subscriberMethod.priority > subscriptions.get(i).subscriberMethod.priority) {
      subscriptions.add(i, newSubscription);
      break;
    }
  }
}


//第二步: 在typesBySubscriber中得到当前订阅者订阅的所有事件队列，将此事件保存到队列typesBySubscriber中，用于后续取消订阅
//根据订阅者，获得该订阅者订阅的事件类型集合
List<Class<?>> subscribedEvents = typesBySubscriber.get(subscriber);
//如果事件类型集合为空，创建新的集合，并加入新订阅的事件类型。
if (subscribedEvents == null) {
  subscribedEvents = new ArrayList<Class<?>>();
  typesBySubscriber.put(subscriber, subscribedEvents);
}
//如果事件类型集合不为空，加入新订阅的事件类型
subscribedEvents.add(eventType);

//第三步: 检查这个事件是否是 Sticky 事件，如果是则从stickyEvents事件保存队列中取出该事件类型最后一个事件发送给当前订阅者
//该事件是stick=true。
if (subscriberMethod.sticky) {
  //响应订阅事件的父类事件
  if (eventInheritance) {
    Set<Map.Entry<Class<?>, Object>> entries = stickyEvents.entrySet();
    //循环获得每个stickyEvent事件
    for (Map.Entry<Class<?>, Object> entry : entries) {
      Class<?> candidateEventType = entry.getKey();
      //是该类的父类
      if (eventType.isAssignableFrom(candidateEventType)) {
        //该事件类型最新的事件发送给当前订阅者。
        Object stickyEvent = entry.getValue();
        checkPostStickyEventToSubscription(newSubscription, stickyEvent);
      }
    }
  } else {
    Object stickyEvent = stickyEvents.get(eventType);
    checkPostStickyEventToSubscription(newSubscription, stickyEvent);
  }
}
```

到此，便完成了订阅功能。下面是订阅的具体流程图：
![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2018-11-13-150526.jpg)

### unregister()
```java
public synchronized void unregister (Object subscriber){
  // 获取该订阅者所有的订阅事件类类型集合.
  List<Class<?>> subscribedTypes = typesBySubscriber.get(subscriber);
  if (subscribedTypes != null) {
    for (Class<?> eventType : subscribedTypes) {
      unsubscribeByEventType(subscriber, eventType);
    }
    // 从typesBySubscriber删除该<订阅者对象,订阅事件类类型集合>
    typesBySubscriber.remove(subscriber);
  } else {
    Log.e("EventBus", "Subscriber to unregister was not registered before: " + subscriber.getClass());
  }
}


private void unsubscribeByEventType (Object subscriber, Class < ?>eventType){
  // 获取订阅事件对应的订阅者信息集合.
  List<Subscription> subscriptions = subscriptionsByEventType.get(eventType);
  if (subscriptions != null) {
    int size = subscriptions.size();
    for (int i = 0; i < size; i++) {
      Subscription subscription = subscriptions.get(i);
      // 从订阅者集合中删除特定的订阅者.
      if (subscription.subscriber == subscriber) {
        subscription.active = false;
        subscriptions.remove(i);
        i--;
        size--;
      }
    }
  }
}
```
unregister()方法比较简单，主要完成了subscriptionsByEventType以及typesBySubscriber两个集合的同步

### post()
```java
public void post (Object event){
  // 获取当前线程的Posting状态.
  PostingThreadState postingState = currentPostingThreadState.get();
  // 获取当前线程的事件队列.
  List<Object> eventQueue = postingState.eventQueue;
  //将当前事件添加到其事件队列
  eventQueue.add(event);
  //判断新加入的事件是否在分发中
  if (!postingState.isPosting) {
    postingState.isMainThread = Looper.getMainLooper() == Looper.myLooper();
    postingState.isPosting = true;
    if (postingState.canceled) {
      throw new EventBusException("Internal error. Abort state was not reset");
    }
    try {
      // 循环处理当前线程eventQueue中的每一个event对象.
      while (!eventQueue.isEmpty()) {
        postSingleEvent(eventQueue.remove(0), postingState);
      }
    } finally {
      // 处理完知乎重置postingState一些标识信息.
      postingState.isPosting = false;
      postingState.isMainThread = false;
    }
  }
}
```

post 函数会首先得到当前线程的 post 信息PostingThreadState，其中包含事件队列，将当前事件添加到其事件队列中，然后循环调用 postSingleEvent 函数发布队列中的每个事件
```java
private void postSingleEvent (Object event, PostingThreadState postingState) {
  //分发事件的类型
  Class<?> eventClass = event.getClass();
  boolean subscriptionFound = false;
  //响应订阅事件的父类事件
  if (eventInheritance) {
    //找出当前订阅事件类类型eventClass的所有父类的类类型和其实现的接口的类类型
    List<Class<?>> eventTypes = lookupAllEventTypes(eventClass);
    int countTypes = eventTypes.size();
    for (int h = 0; h < countTypes; h++) {
      Class<?> clazz = eventTypes.get(h);
      //发布每个事件到每个订阅者
      subscriptionFound |= postSingleEventForEventType(event, postingState, clazz);
    }
  } else {
    subscriptionFound = postSingleEventForEventType(event, postingState, eventClass);
  }
  //....................................
}
```
调用 postSingleEventForEventType 函数发布每个事件到每个订阅者
```java
private boolean postSingleEventForEventType (Object event, PostingThreadState postingState, Class<?> eventClass) {
  CopyOnWriteArrayList<Subscription> subscriptions;
  synchronized (this) {
    // 获取订阅事件类类型对应的订阅者信息集合.(register函数时构造的集合)
    subscriptions = subscriptionsByEventType.get(eventClass);
  }
  
  if (subscriptions != null && !subscriptions.isEmpty()) {
    for (Subscription subscription : subscriptions) {
      postingState.event = event;
      postingState.subscription = subscription;
      boolean aborted = false;
      try {
        // 发布订阅事件给订阅函数
        postToSubscription(subscription, event, postingState.isMainThread);
        aborted = postingState.canceled;
      } finally {
        postingState.event = null;
        postingState.subscription = null;
        postingState.canceled = false;
      }
      if (aborted) {
        break;
      }
    }
    return true;
  }
  return false;
}
```
调用 postToSubscription 函数向每个订阅者发布事件。 postToSubscription 函数中会判断订阅者的 ThreadMode，从而决定在什么 Mode 下执行事件响应函数。这里就不贴源码了。后续还牵着到反射以及线程调度问题，这里就不展开了。 以上就是post的流程，下面是具体的post的流程图:
![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2018-11-13-150953.jpg)