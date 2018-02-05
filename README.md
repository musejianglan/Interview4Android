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
![fragment]{https://github.com/musejianglan/Interview4Android/blob/master/img/fragment_life.png}
![fragment和activity生命周期比对](https://github.com/musejianglan/Interview4Android/blob/master/img/activity_fragment_life_compare.png)

Fragment的生命周期方法主要有onAttach()、onCreate()、onCreateView()、onActivityCreated()、onstart()、onResume()、onPause()、onStop()、onDestroyView()、onDestroy()、onDetach()等11个方法。

切换到该Fragment，分别执行onAttach()、onCreate()、onCreateView()、onActivityCreated()、onstart()、onResume()方法。
锁屏，分别执行onPause()、onStop()方法。
亮屏，分别执行onstart()、onResume()方法。
覆盖，切换到其他Fragment，分别执行onPause()、onStop()、onDestroyView()方法。
从其他Fragment回到之前Fragment，分别执行onCreateView()、onActivityCreated()、onstart()、onResume()方法。



## 1.3Service
### 1.3.1生命周期
> Service    
启动方式：1、context.startService()；启动时：startService -> onCreate()-> onStart()，停止时：onstopservice()->onDestroy()。如果调用者直接退出而没有停止Service，则service会一直在后台运行context.startService()启动服务。服务未创建时，系统会先条用服务的onCreate()方法，接着调用onStart()方法。如果调用

生命周期：Context.bindService()方式的生命周期： 绑定时,bindService -> onCreate() –> onBind()调用者退出了，即解绑定时,Srevice就会unbindService –>onUnbind() –> onDestory()Context.bindService()方式启动 Service的方法：绑定Service需要三个参数：bindService(intent, conn, Service.BIND_AUTO_CREATE);第一个：Intent对象第二个：ServiceConnection对象，创建该对象要实现它的onServiceConnected()和 onServiceDisconnected()来判断连接成功或者是断开连接第三个：如何创建Service，一般指定绑定的时候自动创建附代码
## 1.4Broadcast Receiver
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
# 四、优化
### 4.1.1ANR
ANR的全称application not responding 应用程序未响应。

* 在android中Activity的最长执行时间是5秒。
* BroadcastReceiver的最长执行时间则是10秒。
* Service的最长执行时间则是20秒。
> 超出执行时间就会产生ANR。注意：ANR是系统抛出的异常，程序是捕捉不了这个异常的。

#### a.解决方法:

1. 运行在主线程里的任何方法都尽可能少做事情。特别是，Activity应该在它的关键生命周期方法 （如onCreate()和onResume()）里尽可能少的去做创建操作。（可以采用重新开启子线程的方式，然后使用Handler+Message 的方式做一些操作，比如更新主线程中的ui等）
2. 应用程序应该避免在BroadcastReceiver里做耗时的操作或计算。但不再是在子线程里做这些任务（因为 BroadcastReceiver的生命周期短），替代的是，如果响应Intent广播需要执行一个耗时的动作的话，应用程序应该启动一个 Service。

### Android的数据存储方式，数据库，sd卡，SharedPreferences 这些
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
