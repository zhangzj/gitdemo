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
