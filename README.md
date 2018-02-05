# Interview4Android
为Android面试整理的知识树

---



# 一、四大组件

## 1.1Activity

### 1.1.1Activity生命周期
> 生命周期：onCreate()  onStart() onResum()  onPause() onStop() onDestroy() onReStart()  

![Activity生命周期](https://github.com/musejianglan/Interview4Android/blob/master/img/activity_life.png)


* 启动一个A Activity，分别执行onCreate()、onStart()、onResume()方法。
* 从A Activity打开B Activity分别执行A onPause()、B onCreate()、B onStart()、B onResume()、A onStop()方法。
* 关闭B Activity，分别执行B onPause()、A onRestart()、A onStart()、A onResume()、B onStop()、B onDestroy()方法。
* 横竖屏切换A Activity，清单文件中不设置android:configChanges属性时，先销毁onPause()、onStop()、onDestroy()再重新创建onCreate()、onStart()、onResume()方法，设置orientation|screenSize（一定要同时出现）属性值时，不走生命周期方法，只会执行onConfigurationChanged()方法。
* Activity之间的切换可以看出onPause()、onStop()这两个方法比较特殊，切换的时候onPause()方法不要加入太多耗时操作否则会影响体验。


* 启动 Activiy：onCreate => onStart() => onResume(), Activity 进入运行状态.
* Activity 退居后台 ( Home 或启动新 Activity ): onPause() => onStop().
* Activity 返回前台: onRestart() => onStart() => onResume().
* Activity 后台期间内存不足情况下当再次启动会重新执行启动流程。
* 锁屏: onPause() => onStop().
* 解锁: onStart() => onResume().
#### a.生命周期详解
1. onCreate() 会在activity第一次创建时被调用，完成初始化操作（家在布局、绑定事件等）
2. onStart() **这个方法在活动由不可见变为可见的时候调用**
3. onResume() 在活动准备好和用户进行交互的时候调用，**此时的活动一定位于返回栈的栈顶并且处于运行状态**
4. onPause() 这个方法在系统准备去启动或者恢复另一个活动的时候调用。**通常会在这个方法中将一些及其消耗CPU的资源释放掉（比如显示地图或者大规模图形），以及保存一些关键数据（用户输入的数据）。但这个方法的执行速度一定要快，不然会影响新的栈顶活动的使用**
5. onStop() 这个方法在activity完全不可见的时候调用。**它和 onPause()方法的主要区别在于，如果启动的新活动是一个对话框式的活动，那么 onPause() 方法会得到执行，而 onStop()方法并不会执行。**
6. onDestroy() activity被销毁之前调用，之后的activity的状态变为销毁状态
7. onRestart() activity 由停止状态变为运行状态之前调用，也就是活动被重新启动了

#### b.三个时期
* 完整生存期  活动在 onCreate()方法和 onDestroy() 方法之间所经历的，就是完整生存期。
* 可见生存期 活动在 onStart() 方法和 onStop() 方法之间所经历的，就是可见生存期。在可见生存期内， 活动对于用户总是可见的， 即便有可能无法和用户进行交互。 我们可以通过这两个方法，合理地管理那些对用户可见的资源。比如在 onStart() 方法中对资源进行加载，而在 onStop() 方法中对资源进行释放， 从而保证处于停止状态的活动不会占用过多内存。
* 前台生存期 活动在 onResume() 方法和 onPause() 方法之间所经历的，就是前台生存期。在前台生存期内， 活动总是处于运行状态的， 此时的活动是可以和用户进行相互的， 我们平时看到和接触最多的也这个状态下的活动。

启动方式：1_ startActivity(intent)
2_ startActivityForResult(intent)重写onActivityResult()方法，B页面通过setReult(),回传值  




### 1.1.2启动模式
> 启动模式：standard singleTask singleTop singleInstance  
android:launchMode = "standard"  

#### a.intent标签，代码设置flag
> 通过代码来设置 Activity 的启动模式的方式，优先级比清单文件设置更高。  

* FLAG_ACTIVITY_NEW_TASK 会使新启动的activity独立创建一个Task
* FLAG_ACTIVITY_CLEAR_TOP 会使新启动的Activity检查是否存在于Task中，如果存在则清楚其之上的Activity，使它获的焦点，并不会重新实例化一个Activity，一般结合FLAG_ACTIVITY_NEW_TASK一起使用
* FLAG_ACTIVITY_SINGLE_TOP 等同于在launchmode属性设置为singleTop

#### b.问题1
> 1、设置为 singleTask 的启动模式，当 Activity 的实例已经存在时，再启动它，它的哪个回调函数会被执行？我们可以在哪个回调中处理新的 Intent 协带的参数？（通过 startActivity(Intent) 方式启动）

> 2、设置为 singleTop 的启动模式，当 Activity 的实例已经存在于 Task 的栈顶，我们可以在哪个回调中处理新的 Intent 协带的参数？（在当前 Activity 中从通知栏点击再跳转到此 Activity 就是这种在栈顶的情况）

> 答:在onNewIntent()中实现

#### c.问题2
> startActivityForResult 启动一个 Activity，还没有开始界面跳转，直接就执行了 onActivityResult()。
> 原因：通过startActivityForResult()启动的Activity在AndroidManifest设置了singleTask的启动模式。Android5.0后修复了这个问题，但是在代码中设置FLAG_ACTIVITY_NEW_TASK后还是会出现这个问题。
> 注意：MainActivity 的 onResume() 也会被触发。因为 onActivityResult() 被执行时，它会重新获得焦点。很多人也会遇到 onResume() 被无故调用，也许就是这种情况。

只要是不和原来的 Activity 在同一个 Task 就会产生这种立即执行 onActivityResult() 的情况



#### d.应用场景
> singleTop适合接收通知启动的内容显示页面。例如，某个新闻客户端的新闻内容页面，如果收到10个新闻推送，每次都打开一个新闻内容页面是很烦人的。

>singleTask适合作为程序入口点。例如浏览器的主界面。不管从多少个应用启动浏览器，只会启动主界面一次，其余情况都会走onNewIntent，并且会清空主界面上面的其他页面。之前打开过的页面，打开之前的页面就ok，不再新建。

>singleInstance适合需要与程序分离开的页面。例如闹铃提醒，将闹铃提醒与闹铃设置分离。singleInstance不要用于中间页面，如果用于中间页面，跳转会有问题，比如：A -> B (singleInstance) -> C，完全退出后，在此启动，首先打开的是B。

## 1.2.（补充4大组件之外的）Fragment
### 1.2.1生命周期
![fragment](https://github.com/musejianglan/Interview4Android/blob/master/img/fragment_life.png)
![fragment和activity生命周期比对](https://github.com/musejianglan/Interview4Android/blob/master/img/activity_fragment_life_compare.png)

Fragment的生命周期方法主要有onAttach()、onCreate()、onCreateView()、onActivityCreated()、onstart()、onResume()、onPause()、onStop()、onDestroyView()、onDestroy()、onDetach()等11个方法。

切换到该Fragment，分别执行onAttach()、onCreate()、onCreateView()、onActivityCreated()、onstart()、onResume()方法。
锁屏，分别执行onPause()、onStop()方法。
亮屏，分别执行onstart()、onResume()方法。
覆盖，切换到其他Fragment，分别执行onPause()、onStop()、onDestroyView()方法。
从其他Fragment回到之前Fragment，分别执行onCreateView()、onActivityCreated()、onstart()、onResume()方法。



## 1.3Service
### 1.3.1.启动方式
* started
* bind

> 将 Service 在 AndroidMenifest.xml 文件中配置成私有的，不允许其他应用访问。将 android:exported 属性设为 false，表示不允许其他应用程序启动本应用的组件，即便是显式 Intent 也不行（even when using an explicit intent）。这可以防止其他应用程序启动您的 Service 组件。

### 1.3.2生命周期
![生命周期](https://github.com/musejianglan/Interview4Android/blob/master/img/service_life.png)
> Service    
启动方式：1、context.startService()；启动时：startService -> onCreate()-> onStart()，停止时：onstopservice()->onDestroy()。如果调用者直接退出而没有停止Service，则service会一直在后台运行context.startService()启动服务。服务未创建时，系统会先条用服务的onCreate()方法，接着调用onStart()方法。如果调用

生命周期：Context.bindService()方式的生命周期： 绑定时,bindService -> onCreate() –> onBind()调用者退出了，即解绑定时,Srevice就会unbindService –>onUnbind() –> onDestory()Context.bindService()方式启动 Service的方法：绑定Service需要三个参数：bindService(intent, conn, Service.BIND_AUTO_CREATE);第一个：Intent对象第二个：ServiceConnection对象，创建该对象要实现它的onServiceConnected()和 onServiceDisconnected()来判断连接成功或者是断开连接第三个：如何创建Service，一般指定绑定的时候自动创建附代码

### 1.3.3.IntentService
* 默认在子线程中处理回传到 onStartCommand() 方法中的 Intent；
* 在重写的 onHandleIntent() 方法中处理按时间排序的 Intent 队列，所以不用担心多线程（multi-threading）带来的问题。
* 当所有请求处理完成后，自动停止 Service，无需手动调用 stopSelf() 方法；
* 默认实现了 onBind() 方法，并返回 null；
* 默认实现了 onStartCommand() 方法，并将回传的 Intent 以序列的形式发送给 onHandleIntent()，您只需重写该方法并处理 Intent 即可。



## 1.4Broadcast Receiver
### 1.4.1.BroadcastReceiver内部基本原理
BroadcastReceiver是一个全局的监听器，主要用于监听、接收应用发出的广播消息，并作出响应。采用了设计模式中的观察者模式，可将广播基于消息订阅者、消息发布者、消息中心解耦，通过Binder机制形成订阅关系。
![广播](https://github.com/musejianglan/Interview4Android/blob/master/img/broadcast1.png)
### 1.4.2.注册方式
* 静态注册 在AndroidManifest.xml中通过<receiver>标签声明
```
  <receiver
      android:enabled=["true" | "false"]
      //此 broadcastReceiver 能否接收其他 App 发出的广播
      //默认值是由 receiver 中有无 intent-filter 决定的：如果有 intent-filter，默认值为 true，否则为 false
      android:exported=["true" | "false"]
      android:icon="drawable resource"
      android:label="string resource"
      //继承 BroadcastReceiver 子类的类名
      android:name=".mBroadcastReceiver"
      //具有相应权限的广播发送者发送的广播才能被此 BroadcastReceiver 所接收；
      android:permission="string"
      // BroadcastReceiver 运行所处的进程
      // 默认为 App 的进程，可以指定独立的进程
      //注：Android 四大基本组件都可以通过此属性指定自己的独立进程
      android:process="string" >

      //用于指定此广播接收器将接收的广播类型
      //本示例中给出的是用于接收网络状态改变时发出的广播
       <intent-filter>
            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
      </intent-filter>
  </receiver>

```
* 动态注册 动态注册方式是通过调用 Context 下面的 registerReceiver() 进行注册，可以调用 unregisterReceiver() 进行注销。需要注意的是：动态广播最好在 Activity 的 onResume() 注册，并在 onPause() 进行注销。
![两种方式比较](https://github.com/musejianglan/Interview4Android/blob/master/img/两种方式比较.png)

### 1.4.3.广播类型
> * 无序广播
> 无序广播是完全异步的，通过context.sendBroadcast()方式来发送。对所有的广播接受者而言是无序的，所有接收者无法确定接收时序的顺序，导致无序广播无法被停止。
> * 有序广播
> 有序广播通过context.sendOrderedBroadcast()方法来发送。允许接受者设定优先级，它会按照接受者设定的优先级依次传播。而高优先级的接受者，可以对广播的数据进行处理或者停止掉此条广播的继续传播。广播会优先发送给优先级高（android:priority）的Receiver，而这个Receiver有权决定是继续发送还是终止掉。
> * 粘性广播Sticky
> 通过context.sendStickyBroadcast()方式发送广播。需要在AndroidManifest中注册BROADCAST_STICKY权限。系统会保留最后一条Sticky广播，并且一直保留下去。如果我们发送的 Sticky 广播不被取消，当有一个接收者的时候就会收到它，再来一个还是能收到。所有我们需要在合适的实际，调用 removeStickyBoradcast() 方法，将其取消掉。

#### a.发送广播只有自己（本进程）能接收到
* 发送广播的时候直接通过 Intent.setPackage(packageName) 指定广播接收器的包名
* 使用LocalBroadcast；这个的使用非常的简单，只需要将 Broadcast 的对应 API，替换为 LocalBroadcast 为我们提供的 API 即可。LBM 是一个单例对象，可以使用 LocalBroadcastManager.getInstance(Context context) 方法获取到。

### 1.4.4.BroadcastReceiver 的生命周期
> BroadcastReceiver 有生命周期，但比较短，而且很短。当它的 onReceive() 方法执行完成后，它的生命周期也就随之结束了。这时候由于 BroadcastReceiver 已经不处于 active 状态，所以极有可能被系统干掉。也就是说如果你在 onReceive() 去开线程进行异步操作或者打开 Dialog 都有可能在没达到你要的结果时进程就被系统杀掉了。
>  Receiver 也是运行在主线程的，不能做耗时操作。虽然超时时间相对于 Activity 的 5 秒更高，有足足的 10 秒。

## 1.5Content Provider
---
# 二、布局

---
### Dalvik虚拟机与java虚拟机的区别
1. java虚拟机运行的是Java字节码，Dalvik虚拟机运行的是Dalvik字节码；传统的Java程序经过编译，生成Java字节码保存在class文件中，java虚拟机通过解码class文件中的内容来运行程序。而Dalvik虚拟机运行的是Dalvik字节码，所有的Dalvik字节码由Java字节码转换而来，并被打包到一个DEX(Dalvik Executable)可执行文件中Dalvik虚拟机通过解释Dex文件来执行这些字节码。

2. Dalvik可执行文件体积更小。SDK中有一个叫dx的工具负责将java字节码转换为Dalvik字节码。

3. java虚拟机与Dalvik虚拟机架构不同。java虚拟机基于栈架构。程序在运行时虚拟机需要频繁的从栈上读取或写入数据。这过程需要更多的指令分派与内存访问次数，会耗费不少CPU时间，对于像手机设备资源有限的设备来说，这是相当大的一笔开销。Dalvik虚拟机基于寄存器架构，数据的访问通过寄存器间直接传递，这样的访问方式比基于栈方式快的多.
---

# 三、网络
网络七层：物 数 网 传 会 表 应
## 3.1.物理层协议
## 3.2.数据链路层协议
## 3.3.网络层协议
## 3.4.传输层协议
### 3.4.1.tcp/udp协议：传输控制协议，对应于传输层，主要解决数据在网络中的传输

### 3.4.2.Socket连接

#### a.TCP 三次握手：
握手过程中并不传输数据，在握手后服务器与客户端才开始传输数据，理想状态下，TCP 连接一旦建立，在通讯双方中的任何一方主动断开连接之前 TCP 连接会一直保持下去。

Socket 是对 TCP/IP 协议的封装，Socket 只是个接口不是协议，通过 Socket 我们才能使用 TCP/IP 协议，除了 TCP，也可以使用 UDP 协议来传递数据。

创建 Socket 连接的时候，可以指定传输层协议，可以是 TCP 或者 UDP，当用 TCP 连接，该Socket就是个TCP连接，反之。

#### b.Socket 原理

Socket 连接,至少需要一对套接字，分为 clientSocket，serverSocket 连接分为3个步骤:

(1) 服务器监听:服务器并不定位具体客户端的套接字，而是时刻处于监听状态；

(2) 客户端请求:客户端的套接字要描述它要连接的服务器的套接字，提供地址和端口号，然后向服务器套接字提出连接请求；

(3) 连接确认:当服务器套接字收到客户端套接字发来的请求后，就响应客户端套接字的请求,并建立一个新的线程,把服务器端的套接字的描述发给客户端。一旦客户端确认了此描述，就正式建立连接。而服务器套接字继续处于监听状态，继续接收其他客户端套接字的连接请求.

#### c.Socket为长连接：
通常情况下Socket 连接就是 TCP 连接，因此 Socket 连接一旦建立,通讯双方开始互发数据内容，直到双方断开连接。在实际应用中，由于网络节点过多，在传输过程中，会被节点断开连接，因此要通过轮询高速网络，该节点处于活跃状态。

### 3.4.3.socket
Socket又称套接字，在程序内部提供了与外界通信的端口，即端口通信、通过建立socket连接，可为通信双方的数据chuanshu提供通道。socket的主要特点是数据丢失率低，使用简单且易于移植。


### 3.4.4.socket
Socket又称套接字，在程序内部提供了与外界通信的端口，即端口通信、通过建立socket连接，可为通信双方的数据chuanshu提供通道。socket的主要特点是数据丢失率低，使用简单且易于移植。
#### a.socket分类
* 基于TCP协议的流套接字（streamsocket）
* 基于UDP协议的数据报套接字（datagramsocket）

## 3.5.会话层协议
## 3.6.表示层协议
## 3.7.应用层协议
### 3.7.1.http协议：超文本传输协议，对应于应用层，用于如何封装数据
http为短连接：客户端发送请求都是需要服务端回送响应，请求结束后，主动释放链接，因此为短连接。  

HTTP连接使用的是"请求-响应"方式，不仅在请求时建立连接，而且客户端向服务器端请求后，服务器才返回数据。
---

# 四、Handler
## 4.1.组成
* Handler 负责向消息池发送各种消息事件和处理相应的消息事件
* Message 信息载体
* MessageQueue 消息队列。按时序将消息插入队列，最小的时间戳将被优先处理
* Looper 负责从消息队列中读取西消息，然后分发给对应的handler进行处理，它是一个死循环，不断的调用MessageQueue.next()去读取消息，在没有消息分发时变成阻塞状态。

> 在工作线程中创建自己的消息队列必须调用Looper。prepare()，并且在一个线程中只能调用一次。还必须使用Looper.loop()开启消息循环。

> 主线程中会默认调用主线程的Looper。一个线程中只能由一个Looper和一个MessageQueue对象
```
子线程中标准写法：
Looper.prepare();
Handler mHandler = new Handler() {
   @Override
   public void handleMessage(Message msg) {
          Log.i(TAG, "在子线程中定义Handler，并接收到消息。。。");
   }
};
Looper.loop();

```

```
UI线程中最好做法：
private static final class MyHandler extends Handler{
        private final WeakReference<MainActivity> mWeakReference;

        public MyHandler(MainActivity activity){
            mWeakReference = new WeakReference<>(activity);
        }

        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            MainActivity activity = mWeakReference.get();
            if (activity != null){
                // 开始写业务代码
            }
        }
    }

private MyHandler mMyHandler = new MyHandler(this);


```

## 4.2 HandlerThread

HandlerThread 是 Android API 提供的一个便捷的类，使用它可以让我们快速地创建一个带有 Looper 的线程，有了 Looper 这个线程，我们又可以生成 Handler，本质上也就是一个建立了内部 Looper 的普通 Thread。


```
// 1. 创建 HandlerThread 并准备 Looper
handlerThread = new HandlerThread("myHandlerThread");
handlerThread.start();

// 2. 创建 Handler 并绑定 handlerThread 的 Looper
new Handler(handlerThread.getLooper()).post(new Runnable() {
    @Override
    public void run() {
          // 注意：Handler 绑定了子线程的 Looper，这个方法也会运行在子线程，不可以更新 UI
          MLog.i("Handler in " + Thread.currentThread().getName());
    }
});

// 3. 退出
@Override public void onDestroy() {
    super.onDestroy();
    handlerThread.quit();
}

```

### 4.2.1.HandlerThread 的 quit() 和 quitSafety() 有啥区别？

两个方法作用都是结束 Looper 的运行。它们的区别是，quit() 方法会直接移除 MessageQueue 中的所有消息，然后终止 MesseageQueue，而 quitSafety() 会将 MessageQueue 中已有的消息处理完成后（不再接收新消息）再终止 MessageQueue。



---
# 五、Android的数据存储方式
## 5.1.数据库
## 5.2.sd卡
## 5.3.SharedPreferences

# 六、优化
### 6.1.1ANR
ANR的全称application not responding 应用程序未响应。

* 在android中Activity的最长执行时间是5秒。
* BroadcastReceiver的最长执行时间则是10秒。
* Service的最长执行时间则是20秒。
> 超出执行时间就会产生ANR。注意：ANR是系统抛出的异常，程序是捕捉不了这个异常的。

#### a.解决方法:

1. 运行在主线程里的任何方法都尽可能少做事情。特别是，Activity应该在它的关键生命周期方法 （如onCreate()和onResume()）里尽可能少的去做创建操作。（可以采用重新开启子线程的方式，然后使用Handler+Message 的方式做一些操作，比如更新主线程中的ui等）
2. 应用程序应该避免在BroadcastReceiver里做耗时的操作或计算。但不再是在子线程里做这些任务（因为 BroadcastReceiver的生命周期短），替代的是，如果响应Intent广播需要执行一个耗时的动作的话，应用程序应该启动一个 Service。


### Listview的优化，与scollview的区别
### view状态与重绘，view的绘制过程，view的事件分发机制，view的事件冲突处理
### Android多线程异步机制，AsyncTask工作原理与源码实现，Handler,Message,Looper异步实现机制与源码分析
### Android常见的开源框架(主要是网络通信，图片加载这些)，了解怎么使用，分析源码
### Oom和anr异常引发的原因，怎么解决
### 了解一些常见的图片缓存技术

---
## 源码
Binder  
LruCache  
Handler  
AsyncTask  
EventBus  

---
大部分内容整理自互联网，来源太多无法一一标明；如有侵权，请联系我，马上删除：musejianglan@163.com
