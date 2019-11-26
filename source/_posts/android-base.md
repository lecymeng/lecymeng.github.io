---
title: Android Base
date: 2016-10-23 10:48:27
tags: [Android]
---

### 基础
1. Android四大组件
    - Activity：Android程序与用户交互的窗口，Android构造块中最基本的一种，它需要为保持各界面状态做许多持久化的事情
    - Service：后台服务于Activity，封装有完整的功能逻辑实现，接受上层指令，完成相关事物，定义好需要接受的Intent，提供同步和异步的接口
    - Content Provider：Android提供的第三方应用数据的访问方案，可以派生Content Provider类，对外提供数据
    - Broadcast Receiver：接受一种或者多种Intent作触发事件，接受相关消息，做一些简单处理，转换成一条Notification
<!--more-->
2. Android五种布局
    - FrameLayout：所有控件一次放在右上角，可重叠
    - LinearLayout：可设置垂直布局（android:orientation="vertical"）和水平布局（android:orientation="horizontal" ）
    - AbsoluteLayout：使用X,Y坐标制定元素位置（旋转屏幕时容易出现问题，多个元素计算麻烦）
    - RelativeLayout：以某个元素作为参照物，来定位控件位置
    - TableLayout：包含TableRow，TableRow里面可以定义具体元素
3. Activity生命周期
生命周期方法：onCreate(),onStart(),onResume(),onRestart(),onPause(),onStop(),onDestroy()
    - 启动Activity：onCreate()->onStart()->onResume()
    - Activity进入后台（当前Activity转到新的Activity界面或者按Home键返回主屏）：onPause()->onStop()
    - Activity返回前台：onRestart()->onStart()->onResume()
    - Activity进入后台且系统内存不足，系统会杀死后台的Activity（此时这个Activity引用仍然处在任务栈中，只是这个时候引用指向的对象已经为null）若再次回到这个Activity,则会走onCreate()–>onStart()—>onResume()(将重新走一次Activity的初始化生命周期)
    - 锁屏：onPause()->onStop()
    - 解锁：onStart()->onResume()
    ![activity](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2016-10-23-Activity%20life.png)

4. Activity启动模式
使用android:launchMode="standard|singleInstance|singleTask|singleTop"来控制Activity任务栈
    - 任务栈属于后进先出的结构，位于栈顶的Activity处于焦点状态，当按下back按钮时，栈内的Activity会分别出栈并且调用onDestroy()方法，如果栈内没有Activity那么系统就会回收这个栈，每个APP默认只有一个栈
    - standard：标准模式，每次启动Activity都会创建一个新的Activity实例，并且压入任务栈栈顶，而不管这个Activity是否已经存在Activity的启动过程onCreate()->onStart()->onResume()都会执行
    - singleTop：栈顶复用模式，如果新的Activity已经位于栈顶位置那么就不在创建这个Activity，同时Activity的onNewIntent()方法会被回调。例如A-B-C-D和A-B-D-C在创建D时分别变成A-B-C-D和A-B-D-C-D
    - singleTask：栈内复用模式，创建Activity时，系统会先确认它所需的任务栈是否已经创建，否则先创建任务栈然后放入Activity，如果栈内已经有这个Activity实例，那么这个Activity会被调到栈顶，onNewIntent()方法会被回调，并且singleTask会清理在当前Activity上面的所有Activity.(clear top)
    - singleInstance：加强版singleTask模式，Activity只能单独存在一个任务栈内，由于栈内复用特性，后续请求都不会创建新的Activity除非这个任务栈被销毁

5. Activity缓存方法
问题描述：有a和b两个Activity，当从a进入b一段时间后，可能系统把a回收，这时按back执行的不是a的onRestart()而是onCreate()，a会被重新创建一次，原来a中的临时数据就可能丢失了
    - 这时可以使用Activity中的onSaveInstanceState()回调方法保持临时数据和状态，这个方法一定会在活动被回收前调用，方法中有一个Bundle参数，putString()，putInt()需要传入两个参数，一个键和一个值。数据保存后会在onCreate()中恢复
    - onSaveInstanceState的调用遵循一个重要原则，即当系统“未经你许可”时销毁了你的activity，则onSaveInstanceState会被系统调用，这是系统的责任，因为它必须要提供一个机会让你保存你的数据
    
    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       setContentView(R.layout.activity_main);
    
       //这里，当Acivity第一次被创建的时候为空
       //所以我们需要判断一下
       if( savedInstanceState != null ){
           savedInstanceState.getString("anAnt");
       }
    }
    
    @Override
    protected void onSaveInstanceState(Bundle outState) {
       super.onSaveInstanceState(outState);
       outState.putString("anAnt","Android");
    }
    ```
6. Fragment生命周期和Activity生命周期关系
![FragmentActivity](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2016-10-23-FragmentFlowchartDiagram.jpg)

7. 为什么在Service创建子线程而不是在Activity
    - Activity很难对Thread进行控制，当Activity被销毁后，就没有办法可以在获取之前创建的子线程实例，而且在一个Activity中创建的子线程，另一个Activity无法对其进行任何操作
    - 但是Service不同，所以得Activity都可以与Service关联，可以很方便的操作其中的方法，即使Activity被销毁之后只要重新与Service建立关联就又可以获取到原有Service中的Bundle实例
8. Intent使用方法
    - 通过Intent/Bundle的API文档，可知Intent/Bundle支持传递基本数据类型的数据和基本类型的数组数据以及String/CharSequence类型数据和String/CharSequence类型的数组数据；另外还可以传递Parcelabe(包裹化，邮包)和Serializable序列化类型数据以及他们的数组/列表数据
9. Fragment生命周期
![Fragmentlife](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2016-10-23-FragmentLife.png)

10. Service两种启动方法
    - 1）在Context中通过public boolean bindService(Intent service, ServiceConnection conn, int flags)方法来进行Service与Context的关联启动，并且Service的生命周期依附于Context
    - 2）通过public ComponentName startService(Intent service)方法去启动一个Service，此时Service生命周期与Context无关
    - > 要注意，无论何时都需要在xml里注册Service

    ```
    <service
            android:name=".packnameName.youServiceName"
            android:enabled="true" />
    ```
11. 广播注册方式&区别
    - 在android中，我们如果想接收到广播信息，必须自定义我们的广播接收者。要写一个类来继承BroadcastReceiver，并且重写其onReceive()方法，实现接收到特定广播所要做的事情
    
    ```
    public class MyBroadCastReceiver extends BroadcastReceiver   
    {  
       @Override  
       public void onReceive(Context context, Intent intent)   
       {   
           //在这里可以写相应的逻辑来实现一些功能
           //可以从Intent中获取数据、还可以调用BroadcastReceiver的getResultData()获取数据
       }   
    }
    ```
    - 1）静态注册：在AndroidManifest.xml文件的<application>节点中进行注册，当APP退出后Receiver仍然可以接受广播并进行相应处理
    
    ```
     <receiver android:name=".MyBroadCastReceiver">  
        <!-- android:priority属性是设置此接收者的优先级（从-1000到1000） -->
        <intent-filter android:priority="20">
            <actionandroid:name="android.provider.Telephony.SMS_RECEIVED"/>  
        </intent-filter>  
    </receiver>
    ```
    - 2）动态注册：在代码中动态注册，APP退出就无法接收广播
    
    ```
    // new出上边定义好的BroadcastReceiver
    MyBroadCastReceiver yBroadCastReceiver = new MyBroadCastReceiver();
    // 实例化过滤器并设置要过滤的广播
    IntentFilter intentFilter = new IntentFilter("android.provider.Telephony.SMS_RECEIVED");
    // 注册广播   
    myContext.registerReceiver(smsBroadCastReceiver,intentFilter, 
                 "android.permission.RECEIVE_SMS", null);
    ```
    - 3）发送广播两种方式：通过mContext.sendBroadcast(Intent)或mContext.sendBroadcast(Intent, String)发送的是无序广播(后者加了权限)；通过mContext.sendOrderedBroadcast(Intent, String, BroadCastReceiver, Handler, int, String, Bundle)发送的是有序广播

12. Android数据存储方式
    - 1）SQLite：轻量级数据库，支持基本SQL语法，是常用的数据存储方式，Android提供了SQLiteDatabase的类封装了一些操作数据库的API
    - 2）SharedPreference：存储比较简单的参数设置
    - 3）File：文件（I/O）存储方式，常用于存储大量数据，缺点是更新数据很麻烦
    - 4）ContentProvider：Android系统能实现所有应用程序共享数据的方式，由于数据通常在各个应用中是相互私密的，所有这种方式使用较少，一般用在音频，视频，图片和通讯录；每个Content Provider都会对外提供一个公共的URI（包装成Uri对象），如果应用程序有数据需要共享时，就需要使用Content Provider为这些数据定义一个URI，然后其他的应用程序就通过Content Provider传入这个URI来对数据进行操作

13. Android动画分类
    - tween补间动画，通过指定View的初始状态和变化时间、方式，对View的内容完成一系列的图形变换来实现动画效果，这种实现方式可以使视图组件移动、放大、缩小以及产生透明度的变化
    - frame帧动画，AnimationDrawable控制animation-list.xml布局，通过顺序的播放排列好的图片来实现

14. Android解析XML的类
    - 1）SAX：读取是单向的，优点是不占内存空间、解析属性方便；缺点是对于嵌套多个分支处理不方便
    - 2）DOM：把整个XML放到内存中
    - 3）PUU：常用在J2ME对于节点处理比较好，类似SAX同样很节省内存，在J2ME中常使用KXML库来解析
15. Android如何加速启动Activity
    - 启动应用：Application的构造方法，onCreate方法中不要进行耗时的操作，数据预读取/初始化数据放在异步中进行操作
    - 普通Activity启动应用：A启动B时不要在A的onPause方法中进行耗时操作，因为B的onResume方法必须等待A的onPause方法执行完成才可以运行
16. Android内存优化方法
    - ListView优化
    - 及时关闭资源
    - 图片缓存
17. ListView优化方案
    - 1）如果自定义适配器，那么在getView中需要考虑方法传进来的参数contentView是否为null，若为null则创建contentView并返回，否则直接使用（尽可能减少创建view）
    - 2）给contentView设置tag（setTag()）传入一个viewHolder对象用于缓存需要显示的数据，可以达到图像数据异步加载的效果
    - 3）如果ListView需要显示很多item时则需要考虑分页加载
18. Activity在屏幕旋转时的生命周期
    - 不设置Activity的android:configChanges时，切屏会重新调用各个生命周期，切横屏执行一次，切竖屏执行2次
    - 设置Activity的android:configChanges = "orientation"时，切屏还是会重新调用各个生命，横屏竖屏各一次
    - 设置Activity的android:configChanges="orientation|keyboardHidden"时，切屏不会调用各个生命周期，只会调用onConfigurationChanged()方法
19. 如何启用&停用Service
    - 1）继承Service类`public class SMSService extends Service{}`
    - 2）在AndroidManifest.xml文件中的<application>节点中对服务进行配置<service android:name=".SMSService" />
    - 3）若采用Context.startService()方法启动服务，在服务未被创建时，系统会先调用服务的onCreate()方法，接着调用onStart()方法。如果调用startService()方法前服务已经被创建，多次调用startService()方法并不会导致多次创建服务，但会导致多次调用onStart()方法。采用startService()方法启动的服务，只能调用Context.stopService()方法结束服务，服务结束时会调用onDestroy()方法
    - 4）采用Context.bindService()方法启动服务，在服务未被创建时，系统会先调用服务的onCreate()方法，接着调用onBind()方法。这个时候调用者和服务绑定在一起，调用者退出了，系统就会先调用服务的onUnbind()方法，接着调用onDestroy()方法。如果调用bindService()方法前服务已经被绑定，多次调用bindService()方法并不会导致多次创建服务及绑定(也就是说onCreate()和onBind()方法并不会被多次调用)。如果调用者希望与正在绑定的服务解除绑定，可以调用unbindService()方法，调用该方法也会导致系统调用服务的onUnbind()-->onDestroy()方法
    - 服务常用生命周期回调方法如下： 
        - onCreate() 该方法在服务被创建时调用，该方法只会被调用一次，无论调用多少次startService()或bindService()方法，服务也只被创建一次。
        - onDestroy()该方法在服务被终止时调用。
        - 与采用Context.startService()方法启动服务有关的生命周期方法
        - onStart() 只有采用Context.startService()方法启动服务时才会回调该方法。该方法在服务开始运行时被调用。多次调用startService()方法尽管不会多次创建服务，但onStart() 方法会被多次调用。
        - 与采用Context.bindService()方法启动服务有关的生命周期方法
        - onBind()只有采用Context.bindService()方法启动服务时才会回调该方法。该方法在调用者与服务绑定时被调用，当调用者与服务已经绑定，多次调用Context.bindService()方法并不会导致该方法被多次调用。
        - onUnbind()只有采用Context.bindService()方法启动服务时才会回调该方法。该方法在调用者与服务解除绑定时被调用
20. 单线程模型中Message、Handler、Message Queue、Looper之间的关系
    - Handler获取当前线程中的looper对象，looper用来存放Message，MessageQueue可取出Message，由Handler对Message进行分发和处理
    - Message Queue消息队列：用来存放通过Handler发布的消息，通常附属于某个创建它的线程，可以通过looper.myQueue()得到当前线程的消息队列
    - Handler：可以发布或者处理一个消息或者操作一个Runnable，通过Handler发布消息，消息将只会发送到与它相关联的消息队列，也只能处理该消息队列的消息
    - Looper：是Handler和消息队列之间通信的桥梁，程序组件首先通过Handler将消息传递给Looper，Looper把消息放入队列；Looper也可以把消息广播给所有的Handler,Handler接受到消息后调用handleMessage进行处理
    - Message：消息的类型，在Handler类中的handleMessage方法得到单个消息进行处理
21. 在单线程模型下，为了线程通信问题，Android设计了一个Message Queue(消息队列)， 线程间可以通过该Message Queue并结合Handler和Looper组件进行信息交换。下面将对它们进行分别介绍：
    - 1）Message消息：理解为线程之间交流的信息，处理数据后台线程需要更新UI，则发送Message内含一些数据给UI线程
    - 2）Handler处理者：是Message的主要处理者，负责Message的发送。Message内容的执行处理，后台线程就是通过传进来的Handler对象引用来sendMessage(Message)；使用Handler需要实现该类的handleMessage(Message)方法，它是处理这些Message的操作
    - 3）Message Queue消息队列，用来存放Handler发布的消息，按照先进先出执行；每个Message Queue都会有一个对应的Handler，Handler会通过两种方式向message queue发生消息：sendMessage/post这两种消息都会插在message queue队尾并按先进先出执行。但通过这两种方法发送的消息执行的方式略有不同：通过sendMessage发送的是一个message对象,会被 Handler的handleMessage()函数处理；而通过post方法发送的是一个runnable对象，则会自己执行
    - 4）Looper: Looper是每条线程里的Message Queue的管家。Android没有Global的Message Queue，而Android会自动替主线程(UI线程)建立Message Queue，但在子线程里并没有建立Message Queue。所以调用Looper.getMainLooper()得到的主线程的Looper不为NULL，但调用Looper.myLooper() 得到当前线程的Looper就有可能为NULL
14. Activity、Intent、IntentFilter、Service、Broadcast、BroadcastReceiver
    - 一个activity呈现了一个用户可以操作的可视化界面
    - 一个service不包含可视化界面而是后台运行，可与一个activity绑定，通过绑定暴露出来的接口并与其通信
    - 一个broadcastreceiver是一个接收广播消息并作出回应的component，没有界面
    - 一个intent是一个intent对象，保存了消息的内容，对activity和service来说，它指定了请求的操作名称和待操作数据URIIntent对象可以显式的指定一个目标component。如果这样的话，android会找到这个component(基于manifest文件中的声明)并激活它。但如果一个目标不是显式指定的，android必须找到响应intent的最佳component。它是通过将Intent对象和目标的intent filter相比较来完成这一工作的；
    - 一个component的intent filter告诉android该component能处理的intent。intent filter也是在manifest文件中声明的。

15. MVC模式原理
    - 模型model对象：是应用程序主体部分，负责所有业务逻辑都写在这里
    - 视图view对象：是应用程序中负责界面的部分，也是MVC中唯一可见的一层，接受用户输入，显示处理结果
    - 控制器control对象：根据用户输入，控制用户界面数据显示及更新model对象
16. Android系统架构
    - Linux内核、运行库、应用框架层、应用层

17. ContentProvider是如何实现数据共享

