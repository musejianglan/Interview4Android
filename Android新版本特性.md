## Android5.0：
#### API级别：21
又称为Lollipop（棒棒糖），我觉得其最大的改变在于界面风格和交互体验，用的最多的可以说是Material Design设计风格，Recyclerview，CardView等。

### 1.Android Runtime(ART)
### 2.声音和振动
如果您当前使用 Ringtone、MediaPlayer 或 Vibrator 类向通知中添加声音和振动，则移除此代码，以便系统可以在“优先”模式中正确显示通知。取而代之的是，使用 Notification.Builder 方法添加声音和振动。

将设备设为 RINGER_MODE_SILENT 可使设备进入新的优先模式。如果您将设备设为 RINGER_MODE_NORMAL 或 RINGER_MODE_VIBRATE，则设备将退出优先模式。

以前，Android 使用 STREAM_MUSIC 作为主流式传输来控制平板电脑设备上的音量。在 Android 5.0 中，手机和平板电脑设备的主音量流式传输现已合并，由 STREAM_RING 或 STREAM_NOTIFICATION 进行控制。

### 3.锁定屏幕可见性
### 4.浮动通知
现在，当设备处于活动状态时(即，设备未锁定且其屏幕已打开)，通知可以显示在小型浮动窗口中(也称为“浮动通知”)。这些通知看上去类似于精简版的通知，只是浮动通知还显示操作按钮。用户可以在不离开当前应用的情况下处理或清除浮动通知。

可能触发浮动通知的条件示例包括：

用户的 Activity 处于全屏模式中(应用使用 fullScreenIntent)

通知具有较高的优先级并使用铃声或振动

如果您的应用在以上任何情形下实现通知，请确保系统正确显示浮动通知。

### 5.getRecentTasks()

为提升用户隐私的安全性，现已弃用 ActivityManager.getRecentTasks() 方法。对于向后兼容性，此方法仍会返回它的一小部分数据，包括调用应用自己的任务和可能的一些其他非敏感任务(如首页)。如果您的应用使用此方法检索它自己的任务，则改用 getAppTasks() 检索该信息。

### 6.绑定到服务

Context.bindService() 方法现在需要显式 Intent，如果提供隐式 intent，将引发异常。为确保应用的安全性，请使用显式 intent 启动或绑定 Service，且不要为服务声明 intent 过滤器。

### 7.webview

Android 5.0 更改了应用的默认行为。

如果您的应用是面向 API 级别 21 或更高级别：

默认情况下，系统会阻止混合内容和第三方 Cookie。要允许混合内容和第三方 Cookie，请分别使用 setMixedContentMode() 和 setAcceptThirdPartyCookies() 方法。

系统现在可以智能地选择要绘制的 HTML 文档部分。这个新的默认行为有助于减少内存占用和提升性能。如果您要一次渲染整个文档，可通过调用 enableSlowWholeDocumentDraw() 停用此优化。

如果您的应用是面向低于 21 的 API 级别：系统允许混合内容和第三方 Cookie，并始终一次渲染整个文档。

### 8.Material Design中常用的控件：
* AppBarLayout、ToolBar
* DrawerLayout、NavigationView
* RecyclerView、CardView、AutoScrollViewPager
* CoordinatorLayout、CollapsingToolbarLayout、TabLayout
* TextInputLayout
* FloatingActionButton、Snackbar  
对于上面这些控件，我是将他们常在一起搭配使用的放在一起，如果你想更好的了解Material Design，请移步12个Material Design风格控件的使用，一般面试官会着重问RecyclerView，为什么？因为RecyclerView现在正在逐步代替ListView和GridView，它的功能很强大，性能更好（Item的复用不需要自己去维护），如果有兴趣了解更多ListView与RecyclerView的区别的同学，请移步RecyclerView和ListView 使用对比分析。

## Android6.0：
#### API级别：23
又称为Marshmallow（棉花糖）,我觉得其最大的改变在于用户对权限的管理，我们都知道，6.0以下的Android系统在安装app的时候会默认勾选一些权限，一旦用户安装了，app可以在用户毫不知晓的情况下访问权限内的所有东西，这样感觉起来挺不好的。6.0以后，app将不会在安装的时候授予权限，取而代之的是，app会在运行时一个一个询问用户授予权限。
### 1. 运行时权限

对于以 Android 6.0(API 级别 23)或更高版本为目标平台的应用，请务必在运行时检查和请求权限。要确定您的应用是否已被授予权限，请调用新增的 checkSelfPermission() 方法。要请求权限，请调用新增的 requestPermissions() 方法。即使您的应用并不以 Android 6.0(API 级别 23)为目标平台，您也应该在新权限模式下测试您的应用。

### 2. 取消支持Apache HTTP客户端

Android 6.0 版移除了对 Apache HTTP 客户端的支持。如果您的应用使用该客户端，并以 Android 2.3(API 级别 9)或更高版本为目标平台，请改用 HttpURLConnection 类。此 API 效率更高，因为它可以通过透明压缩和响应缓存减少网络使用，并可最大限度降低耗电量。要继续使用 Apache HTTP API，您必须先在 build.gradle 文件中声明以下编译时依赖项：
```
android {

  useLibrary 'org.apache.http.legacy'

}
```

### 3. BoringSSL

Android 正在从使用 OpenSSL 库转向使用 BoringSSL 库。如果您要在应用中使用 Android NDK，请勿链接到并非 NDK API 组成部分的加密库，如 libcrypto.so 和 libssl.so。这些库并非公共 API，可能会在不同版本和设备上毫无征兆地发生变化或出现故障。此外，您还可能让自己暴露在安全漏洞的风险之下。请改为修改原生代码，以通过 JNI 调用 Java 加密 API，或静态链接到您选择的加密库。

### 4. 通知

此版本移除了Notification.setLatestEventInfo()方法。请改用 Notification.Builder 类来构建通知。要重复更新通知，请重复使用 Notification.Builder 实例。调用 build() 方法可获取更新后的 Notification 实例。

adb shell dumpsys notification命令不再打印输出您的通知文本。请改用adb shell dumpsys notification --noredact命令打印输出 notification 对象中的文本。

### 5. 音频管理器变更

不再支持通过 AudioManager 类直接设置音量或将特定音频流静音。setStreamSolo() 方法已弃用，您应该改为调用 requestAudioFocus() 方法。类似地，setStreamMute() 方法也已弃用，请改为调用 adjustStreamVolume() 方法并传入方向值 ADJUST_MUTE 或 ADJUST_UNMUTE。

### 6. 相机服务变更

在此版本中，相机服务中共享资源的访问模式已从之前的“先到先得”访问模式更改为高优先级进程优先的访问模式。对服务行为的变更包括：

根据客户端应用进程的“优先级”授予对相机子系统资源的访问权，包括打开和配置相机设备。带有对用户可见 Activity 或前台Activity 的应用进程一般会被授予较高的优先级，从而使相机资源的获取和使用更加可靠;

当高优先级的应用尝试使用相机时，系统可能会“驱逐”正在使用相机客户端的低优先级应用。在已弃用的 Camera API 中，这会导致系统为被驱逐的客户端调用 onError()。在 Camera2 API 中，这会导致系统为被驱逐的客户端调用onDisconnected();

在配备相应相机硬件的设备上，不同的应用进程可同时独立打开和使用不同的相机设备。但现在，如果在多进程用例中同时访问相机会造成任何打开的相机设备的性能或能力严重下降，相机服务会检测到这种情况并禁止同时访问。即使并没有其他应用直接尝试访问同一相机设备，此变更也可能导致低优先级客户端被“驱逐”。

更改当前用户会导致之前用户帐户拥有的应用内活动相机客户端被驱逐。对相机的访问仅限于访问当前设备用户拥有的用户个人资料。举例来说，这意味着，当用户切换到其他帐户后，“来宾”帐户实际上无法让使用相机子系统的进程保持运行状态

## Android 7.0
#### API级别：24

### 1.电池和内存

Android 7.0 包括旨在延长设备电池寿命和减少 RAM 使用的系统行为变更。这些变更可能会影响您的应用访问系统资源，以及您的应用通过特定隐式 intent 与其他应用交互的方式。

### 2.Project Svelte：后台优化

Android 7.0 移除了三项隐式广播，以帮助优化内存使用和电量消耗。此项变更很有必要，因为隐式广播会在后台频繁启动已注册侦听这些广播的应用。删除这些广播可以显著提升设备性能和用户体验。

移动设备会经历频繁的连接变更，例如在 WLAN 和移动数据之间切换时。目前，可以通过在应用清单中注册一个接收器来侦听隐式 CONNECTIVITY_ACTION 广播，让应用能够监控这些变更。由于很多应用会注册接收此广播，因此单次网络切换即会导致所有应用被唤醒并同时处理此广播。

同理，在之前版本的 Android 中，应用可以注册接收来自其他应用(例如相机)的隐式 ACTION_NEW_PICTURE 和 ACTION_NEW_VIDEO 广播。当用户使用相机应用拍摄照片时，这些应用即会被唤醒以处理广播。

为缓解这些问题，Android 7.0 应用了以下优化措施：

面向 Android 7.0 开发的应用不会收到 CONNECTIVITY_ACTION 广播，即使它们已有清单条目来请求接受这些事件的通知。在前台运行的应用如果使用 BroadcastReceiver 请求接收通知，则仍可以在主线程中侦听 CONNECTIVITY_CHANGE。

应用无法发送或接收 ACTION_NEW_PICTURE 或 ACTION_NEW_VIDEO 广播。此项优化会影响所有应用，而不仅仅是面向 Android 7.0 的应用。

如果您的应用使用任何 intent，您仍需要尽快移除它们的依赖关系，以正确适配 Android 7.0 设备。Android 框架提供多个解决方案来缓解对这些隐式广播的需求。例如，JobScheduler API 提供了一个稳健可靠的机制来安排满足指定条件(例如连入无限流量网络)时所执行的网络操作。您甚至可以使用 JobScheduler 来适应内容提供程序变化。

### 3.系统权限更改

为了提高私有文件的安全性，面向 Android 7.0 或更高版本的应用私有目录被限制访问　(0700)。此设置可防止私有文件的元数据泄漏，如它们的大小或存在性。此权限更改有多重副作用：

1. 私有文件的文件权限不应再由所有者放宽，为使用 MODE_WORLD_READABLE 和/或 MODE_WORLD_WRITEABLE 而进行的此类尝试将触发 SecurityException。
注：迄今为止，这种限制尚不能完全执行。应用仍可能使用原生 API 或 File API 来修改它们的私有目录权限。但是，我们强烈反对放宽私有目录的权限。

2. 传递软件包网域外的 file:// URI 可能给接收器留下无法访问的路径。因此，尝试传递 file:// URI 会触发 FileUriExposedException。分享私有文件内容的推荐方法是使用 FileProvider。

3. DownloadManager 不再按文件名分享私人存储的文件。旧版应用在访问 COLUMN_LOCAL_FILENAME 时可能出现无法访问的路径。面向 Android 7.0 或更高版本的应用在尝试访问 COLUMN_LOCAL_FILENAME 时会触发 SecurityException。通过使用DownloadManager.Request.setDestinationInExternalFilesDir()或DownloadManager.Request.setDestinationInExternalPublicDir()将下载位置设置为公共位置的旧版应用仍可以访问 COLUMN_LOCAL_FILENAME 中的路径，但是我们强烈反对使用这种方法。对于由 DownloadManager 公开的文件，首选的访问方式是使用ContentResolver.openFileDescriptor()。

### 4.在应用件共享文件

对于面向 Android 7.0 的应用，Android 框架执行的 StrictMode API 政策禁止在您的应用外部公开 file:// URI。如果一项包含文件 URI 的 intent 离开您的应用，则应用出现故障，并出现 FileUriExposedException 异常。

要在应用间共享文件，您应发送一项 content:// URI，并授予 URI 临时访问权限。进行此授权的最简单方式是使用 FileProvider 类。

### 5.屏幕缩放

Android 7.0 支持用户设置显示尺寸，以放大或缩小屏幕上的所有元素，从而提升设备对视力不佳用户的可访问性。用户无法将屏幕缩放至低于最小屏幕宽度 sw320dp，该宽度是 Nexus 4 的宽度，也是常规中等大小手机的宽度。

当设备密度发生更改时，系统会以如下方式通知正在运行的应用：

如果是面向 API 级别 23 或更低版本系统的应用，系统会自动终止其所有后台进程。这意味着如果用户切换离开此类应用，转而打开 Settings 屏幕并更改 Display size 设置，则系统会像处理内存不足的情况一样终止该应用。如果应用具有任何前台进程，则系统会如处理运行时更改中所述将配置变更通知给这些进程，就像对待设备屏幕方向变更一样。

如果是面向 Android 7.0 的应用，则其所有进程(前台和后台)都会收到有关配置变更的通知，如处理运行时更改中所述。

大多数应用并不需要进行任何更改即可支持此功能，不过前提是这些应用遵循 Android 最佳做法。具体要检查的事项：

1.在屏幕宽度为 sw320dp 的设备上测试您的应用，并确保其充分运行。

2.当设备配置发生变更时，更新任何与密度相关的缓存信息，例如缓存位图或从网络加载的资源。当应用从暂停状态恢复运行时，检查配置变更。

注：如果您要缓存与配置相关的数据，则最好也包括相关元数据，例如该数据对应的屏幕尺寸或像素密度。保存这些元数据便于您在配置变更后决定是否需要刷新缓存数据。

3.避免用像素单位指定尺寸，因为像素不会随屏幕密度缩放。应改为使用与密度无关像素 (dp) 单位指定尺寸。

### 6. 检查你的应用是否使用私有库

为帮助您识别加载私有库的问题，logcat 可能会生成一个警告或运行时错误。例如，如果您的应用面向 API 级别 23 或更低级别，并在运行 Android 7.0 的设备上尝试访问私有库，您可能会看到一个类似于下面所示的警告：

```
03-21 17:07:51.502 31234 31234 W linker :

library "libandroid_runtime.so"("/system/lib/libandroid_runtime.so") needed
or dlopened by "/data/app/com.popular-app.android-2/lib/arm/libapplib.
so" is not accessible for the namespace "classloader-namespace" -
the access is temporarily granted as a workaround for https://b/26394120```

这些 logcat 警告通知您哪个库正在尝试访问私有平台 API，但不会导致您的应用崩溃。但是，如果应用面向 API 级别 24 或更高级别，logcat 会生成以下运行时错误，您的应用可能会崩溃：

```
java.lang.UnsatisfiedLinkError: dlopen failed:

library "libcutils.so"("/system/lib/libcutils.so") needed or dlopened by"/system/lib/libnativeloader.
so" is not accessible for the namespace "classloader-namespace"

at java.lang.Runtime.loadLibrary0(Runtime.java:977)

at java.lang.System.loadLibrary(System.java:1602)
```

如果您的应用使用动态链接到私有平台 API 的第三方库，您可能也会看到上述 logcat 输出。利用 Android 7.0DK 中的 readelf 工具，您可以通过运行以下命令生成给定 .so 文件的所有动态链接的共享库列表：

`aarch64-linux-android-readelf -dW libMyLibrary.so`

### 7. 其他重要说明

⑴如果一个应用在 Android 7.0 上运行，但却是针对更低 API 级别开发的，那么在用户更改显示尺寸时，系统将终止此应用进程。应用必须能够妥善处理此情景。否则，当用户从最近使用记录中恢复运行应用时，应用将会出现崩溃现象。

您应测试应用以确保不会发生此行为。要进行此测试，您可以通过 DDMS 手动终止应用，以造成相同的崩溃现象。

在密度发生更改时，系统不会自动终止面向 N 及更高版本的应用;不过，这些应用仍可能对配置变更做出不良响应。

⑵Android 7.0 上的应用应能够妥善处理配置变更，并且在后续启动时不会出现崩溃现象。您可以通过更改字体大小 (Setting >Display > Font size) 并随后从最近使用记录中恢复运行应用，来验证应用行为。

⑶由于之前的 Android 版本中的一项错误，系统未能将对主线程上的一个 TCP 套接字的写入操作举报为违反严格模式。Android 7.0 修复了此错误。呈现出这种行为的应用现在会引发android.os.NetworkOnMainThreadException。一般情况下，我们不建议在主线程上执行网络操作，因为这些操作通常会出现可能导致 ANR 和卡顿的高尾延迟。

⑷Debug.startMethodTracing()方法系列现在默认在您的共享存储空间上的软件包特定目录中存储输出，而非 SD 卡根目录。这意味着应用不再需要请求WRITE_EXTERNAL_STORAGE权限来使用这些 API 。

⑸许多平台 API 现在开始检查在 Binder 事务间发送的大负载，系统现在会将TransactionTooLargeExceptions作为 RuntimeExceptions 再次引发，而不再只是默默记录或抑制它们。一个常见例子是在Activity.onSaveInstanceState()上存储过多数据，导致ActivityThread.StopInfo在您的应用面向 Android 7.0 时引发 RuntimeException。

⑹如果应用向 View 发布 Runnable 任务，并且 View 未附加到窗口，系统会用 View 为 Runnable 任务排队;在 View 附加到窗口之前，不会执行 Runnable 任务。此行为会修复以下错误：

如果一项应用是从并非预期窗口 UI 线程的其他线程发布到 View，则 Runnable 可能会因此运行错误的线程。

如果 Runnable 任务是从并非环路线程的其他线程发布，则应用可能会曝光 Runnable 任务。

⑺如果 Android 7.0 上一项有 DELETE_PACKAGES 权限的应用尝试删除一个软件包，但另一项应用已经安装了这个软件包，则系统需要用户进行确认。在这种情况下，应用在调用PackageInstaller.uninstall()时预计的返回状态应为STATUS_PENDING_USER_ACTION。

⑻名为 Crypto 的 JCA 提供程序已弃用，因为它仅有的 SHA1PRNG 算法为弱加密。应用无法再使用 SHA1PRNG(不安全地)派生密钥，因为不再提供此提供程序。


## Android 8.0
#### API级别：26
### 通知渠道 — Notification Channels
通知渠道是由应用自行定义的通知内容类别，借助渠道，开发者可以让用户对不同种类的通知进行精细控制，用户可以单独拦截或更改每个渠道的行为，而不是统一管理应用的所有通知。

创建通知渠道的步骤：

1. 创建 NotificationChannel 对象，并设置应用内唯一的通知 ID。
2. 配置通知渠道的属性，比如提示声音等。
3. 在 NotificationManager 中注册通知渠道对象。
![111](https://github.com/musejianglan/Interview4Android/blob/master/img/notification_channel.png)

### 画中画模式 — PIP


1> 关于生命周期

PIP 模式不会改变 Activity 的生命周期。在指定时间只有最近与用户交互过的 Activity 为活动状态。 该 Activity 将被视为顶级 Activity。 所有其他 Activity 虽然可见，但均处于暂停状态。当一个 Activity 处于 PIP 模式时，其实它是出在暂停状态，但其内容会继续展示。

2> API变更

在 Android O 中新增 PictureInPictureArgs 对象来指明你的 Activity 在 PIP 模式中的属性，比如长宽比等。

Android O 还新增了以下方法来支持 PIP。

Activity.enterPictureInPictureMode(PictureInPictureArgs args)：将Activity置于 PIP 模式之下。
Activity.setPictureInPictureArgs()：用于更新 Activity 在 PIP 模式下的设置。如果 Activity 正处于 PIP 模式之下，那么更改的属性将立即生效。

### 自适应图标 — Adaptive Icons

1> 自适应图标支持多种形状

通过定义两张图层（前景与背景）你可以制定你的桌面图标外观，你必须提供没有形状和阴影的 PNG 格式图象作为图层。

2> 自适应图标由两张图层和一个形状来定义

在以前的 Android 版本中，图标大小定义为 48 x 48 dp。现在你必须按照以下的规范定义你的图层大小：

* 两张图层大小都必须为 108 x 108 dp。
* 图层中心 72 x 72 dp 范围为可视范围。
* 系统会保留四周外的 36dp 范围用于生成有趣的视觉效果（如视差和跳动）。

3> 创建你的自适应图标

首先你需要在 Application 标签中加入 Android:icon 属性，定义你的 icon 图标。其次如果你需要创建一个原型的 icon，你还需要加入 Android:roundIcon 属性。
```
<application
    android:icon = "@mipmap/ic_launcher"
    android:roundIcon = "@mipmap/ic_launcher_round"
>
</application>
```
接下来，你需要 res/mipmap-anydpi/ic_launcher.xml 文件中定义您的图层。在 选项中加入您的前景和背景图层。

```
<maskable-icon>
  <background android:drawable="@color/ic_launcher"/>
  <foreground android:drawable="@mipmap/ic_foreground"/>
</maskable-icon>
```

### 固定快捷方式和小部件 — Pinning shortcuts

Pinning shortcuts 是一个比 APP shortcuts 更小的快捷方式，放置于桌面上，用于更快速的打开某一 APP 的某单一任务。Pinning shortcuts 在桌面上可呈现不同的图标显示。

![shortcuts](https://github.com/musejianglan/Interview4Android/blob/master/img/shortcuts.png)

开发指南

1. 首先使用 isRequestPinShortcutSupported() 方法校验手机是否支持启动这种快捷方式。
2. 创建 ShortcutInfo 对象。
3. 用 requestPinShortcut() 方法应用 Pinning shortcuts。你可以通过 PendingIntent 来通知你的 shortcuts 有没有创建成功。

![shortcuts](https://github.com/musejianglan/Interview4Android/blob/master/img/shortcuts_dev.png)
