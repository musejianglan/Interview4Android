# 四、Handler
## 4.1.组成
* Handler 负责向消息池发送各种消息事件和处理相应的消息事件
* Message 信息载体
* MessageQueue 消息队列。按时序将消息插入队列，最小的时间戳将被优先处理
* Looper 负责从消息队列中读取西消息，然后分发给对应的handler进行处理，它是一个死循环，不断的调用MessageQueue.next()去读取消息，在没有消息分发时变成阻塞状态。

> 在工作线程中创建自己的消息队列必须调用Looper。prepare()，并且在一个线程中只能调用一次。还必须使用Looper.loop()开启消息循环。

> 主线程中会默认调用主线程的Looper。一个线程中只能由一个Looper和一个MessageQueue对象

Handler机制是Android中异步消息处理机制，也就是异步消息处理线程启动后会进入一个无限的循环体之中，每循环一次，从其内部的消息队列中取出一个消息，然后回调相应的消息处理函数，执行完成一个消息后则继续循环。若消息队列为空，线程则会阻塞等待。而他们之间的关系就是，消息循环Looper负责创建一个消息队列MessageQueue，而消息队列MessageQueue是消息Message的容器，也就是装消息Message的地方，然后进入一个无限循环体不断从该消息队列MessageQueue中读取消息，而消息的创建者和处理者就是一个或多个Handler。

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
