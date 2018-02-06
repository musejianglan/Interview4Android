# Interview4Android
为Android面试整理的知识树

---
[四大组件]()
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


**欢迎扫描下方二维码或者公众号搜索「musejianglan」关注我的微信公众号**
![](https://github.com/musejianglan/Interview4Android/blob/master/img/qrcode.jpg)
