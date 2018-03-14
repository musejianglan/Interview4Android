# Interview4Android
为Android面试整理的知识树

## Java

[Java集合](https://github.com/musejianglan/Interview4Android/blob/master/Java/Java集合.md)
---

[java中的栈和堆](https://github.com/musejianglan/Interview4Android/blob/master/Java/java中的栈和堆.md)
---

## Android
[四大组件](https://github.com/musejianglan/Interview4Android/blob/master/Android/四大组件.md)
---

[Android_res](https://github.com/musejianglan/Interview4Android/blob/master/Android/Android_res.md)
---

[Handler](https://github.com/musejianglan/Interview4Android/blob/master/Android/Handler.md)
---

[Android的数据存储方式](https://github.com/musejianglan/Interview4Android/blob/master/Android/Android的数据存储方式.md)
---

[网络通信](https://github.com/musejianglan/Interview4Android/blob/master/Android/网络通信.md)
---

[Android版本新特性](https://github.com/musejianglan/Interview4Android/blob/master/Android/Android新版本特性.md)
---

[Android优化](https://github.com/musejianglan/Interview4Android/blob/master/Android/Android优化.md)
---

[bitmap&图片](https://github.com/musejianglan/Interview4Android/blob/master/Android/bitmap&图片.md)
---

---

### Dalvik虚拟机与java虚拟机的区别
1. java虚拟机运行的是Java字节码，Dalvik虚拟机运行的是Dalvik字节码；传统的Java程序经过编译，生成Java字节码保存在class文件中，java虚拟机通过解码class文件中的内容来运行程序。而Dalvik虚拟机运行的是Dalvik字节码，所有的Dalvik字节码由Java字节码转换而来，并被打包到一个DEX(Dalvik Executable)可执行文件中Dalvik虚拟机通过解释Dex文件来执行这些字节码。

2. Dalvik可执行文件体积更小。SDK中有一个叫dx的工具负责将java字节码转换为Dalvik字节码。

3. java虚拟机与Dalvik虚拟机架构不同。java虚拟机基于栈架构。程序在运行时虚拟机需要频繁的从栈上读取或写入数据。这过程需要更多的指令分派与内存访问次数，会耗费不少CPU时间，对于像手机设备资源有限的设备来说，这是相当大的一笔开销。Dalvik虚拟机基于寄存器架构，数据的访问通过寄存器间直接传递，这样的访问方式比基于栈方式快的多.

---

## 面试题

[Android面试题](https://github.com/musejianglan/Interview4Android/blob/master/面试题.md)
---

[Java面试题](https://github.com/musejianglan/Interview4Android/blob/master/Java面试题.md)
---

---

[Android校招面试指南](https://github.com/LRH1993/android_interview)  
[Everything you need to know to get the job.](https://github.com/kdn251/interviews)  
[LearningNotes](https://github.com/francistao/LearningNotes)  

---


大部分内容整理自互联网，来源太多无法一一标明；如有侵权，请联系我，马上删除：musejianglan@163.com


**欢迎扫描下方二维码或者公众号搜索「musejianglan」关注我的微信公众号**
![](https://github.com/musejianglan/Interview4Android/blob/master/img/qrcode.jpg)
