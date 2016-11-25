# 1 Android 第一行代码

教学使用4.0版本，写下这行笔记的时候7.0刚刚发布，6.0还没有完全普及

## 1.1 了解全貌，Android王国简介

安卓生态是由用户，系统开发商，应用开发商共同形成的；

### 安卓系统架构

Linux内核层，2.6的内核，各种驱动的管理；系统运行库层，SQLite，OpenGL，WebKit等；应用框架层，各种安卓自带的api；应用层，app安装的层面；

![](http://ww2.sinaimg.cn/large/8d6a2535gw1f9zsjt043mj20p30k9grg.jpg)

### 安卓应用开发特色

安卓系统提供了什么？四大组件：

活动，Activity，凡事看得到的东西，都在activity中

服务。Service，，service一直在后台默默运行

广播接收器，Broadcast Receiver，广播接收器允许节后来自各处的消息，比如短信电话等；

内容提供器，Content Provider；为app之间提供共享数据的途径，比如读取通讯录等；

系统空间集合；SQLite数据路；地理位置定位；多媒体，音乐视频，录音；传感器

### 搭建开发环境

这里的开发环境是ADT，Eclipse的插件，我这边使用Android Studio；

新建一个空项目，空的Activity，helloworld；

安卓项目的目录结构，源代码文件和资源文件

### 潜行必备，掌握日至工具的使用

logcat；android的自带log工具

## 2 先从看到的入手，探究活动activity

活动，activity是什么？是可以包含ui的组件，主要用于和用户交互；

基本用法；新建一个继承Activity的类来创建活动；

布局资源文件，XML；使用布局文件创建页面上可见的控件，布局，大小；

所有的活动都要在AndroidManifest.xml文件中注册才能生效，

Toast是安卓应用中作为提醒的方式，用户可以看到一些短小的消息；

活动的生命周期；运行，暂停，停止，销毁；

活动的启动模式；singleTop，standard；singleTask；singleInstance；

活动的最佳实践；知道当前在哪一个活动；随时随地退出程序，而不是被挂起；如何用正确的姿势启动活动；

## 3 软件也要看脸，UI开发的点滴

如何编写程序界面；UI编写方式，DroidDraw，可视化界面编辑工具；XML布局界面手撸；

常见UI空间的使用方法；TextView；Button，使用匿名类或者实现监听器接口的方式来添加click逻辑；EditText；ImageView；progressbar；

AlertDialog对话框；ProgressDialog对话框；

**详解四种基本布局**；

LinearLayout，线性布局；RelativeLayout，相对布局；FrameLayout，都摆在左上角；TableLayout，表格布局；

自定义控件；最常用和最难用的控件，ListView；ListView的点击事件；

单位和尺寸，px和pt；sp和sp；

## 4 手机平板要兼顾，探究碎片化

前端屏幕碎片化问题；fragment，平板和手机使用不同的布局；

## 5 全局大喇叭，详解广播机制

广播机制简介；注册广播，广播接收器；有序广播，同步的按序发送广播消息；

接收系统广播；电量变化的广播，时间时区广播；想接受广播需要先注册，分为动态静态，动态是在代码中注册，静态实在AndroidManifest.xml文件中注册；

新建内部类继承BroadCastReciever，之后实现方法，当消息发生就会执行代码；

静态注册实现开机启动；

发送自定义广播；标准广播和有序广播；

本地广播，之前的都是全局广播，LocalReciever；

Git简介

## 6 数据存储全方案，详解持久化技术

瞬时数据，存在内存中，关机就没有的数据；

持久化技术简介；文件存储，存到文件中，从文件读取数据；SharedPreferences存储；SQLite数据库存储；

## 7 跨程序共享数据，探究内容提供器

Content Provider；访问其他程序中的数据，通讯录，短信，媒体库都有对外的接口来共享数据，ContentResolver；

创建自己的content provider，

Git，版本控制进阶；忽略文件；

## 8 丰富你的程序，运用手机多媒体

通知，Notification，NotificationManager；

接受和发送短信；拦截短信；

调用摄像头和相册；只有真机可以运行；

播放多媒体，音频视频

## 9 后台默默的劳动者，探究服务

服务是什么？实现后台程序运行的解决方案，执行长时间的，不需要交互的任务；

安卓多线程编程；异步消息处理；

服务的基本用法；Service类；地洞和停止服务；服务的生命周期；后台定时任务；

## 10 看看精彩的世界，使用网络技术

WebView，浏览网页；

HTTP协议访问web；HttpURLConnection；HttpClient；

解析XML格式数据，或者常用的JSON数据；

## 11 安卓特色开发，基于位置的服务

LocationManager；解析经纬度确定地点；

使用百度地图，申请APIkey；

Git高级用法，分支，远程，

## 12 安卓特色开发，使用传感器

光照传感器；加速苏传感器；方向传感器；

## 13 继续进阶，你还需要掌握的高级技巧

全局获取Context的技巧；定制日志工具；

## 14 进入实战，开发酷欧天气

托管代码到github上，天气数据来自weather.com.cn

切换城市和手动更新天气；后台自动更新天气；

## 15 最后一步，经app发布到Google Play

生成正式签名的APK文件；
