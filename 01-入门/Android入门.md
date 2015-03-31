#Android入门

> 翻译：[JiongBull](https://github.com/JiongBull)
> 微博：[@JiongBull](http://weibo.com/jiongbull/)
> 索引：[Android API Guide](https://github.com/JiongBull/Android-API-Guide/blob/master/README.md)

---

Android提供了一种富应用框架，你可以在Java语言环境下为移动设备构建富有新意的应用和游戏。左边导航栏中列举的文档提供了关于如何使用Android种类繁多的API构建应用的细节。

- 想要学习应用是如何工作的，从[App Fundamentals](http://developer.android.com/guide/components/fundamentals.html)这里开始。
- 想要立即开始编码，请阅读[Building Your First App](http://developer.android.com/training/basics/firstapp/index.html)。

如果你刚从事Android开发，理解下面关于Android应用框架的基本概念是非常重要的：

---

## 应用提供了多种入口

Android应用是由许多不同的组件组成的，并且这些组件都可以单独的被调用。举例来说，activity可以独立的为用户界面提供显示屏幕，service可以独立的在后台执行工作。

你可以从一个组件内使用intent启动另一个组件。你甚至可以启动其他应用中的组件，例如在地图应用中显示地址的activity。这种模型为单一的应用提供了多种入口，并且任何应用调用其他应用的操作时，都好像在调用它们自己的一部分一样。

**了解更多：**

[App Fundamentals](http://developer.android.com/guide/components/fundamentals.html)
[Intents and Intent Filters](http://developer.android.com/guide/components/intents-filters.html)
[Activities](http://developer.android.com/guide/components/activities.html)

---

## 应用可以适配多种设备

Android提供了自适应的应用框架，你可以针对不同的设备配置独一无二的资源。举例来说，你可以针对不同尺寸的屏幕创造不同的XML布局文件，系统会根据当前的设备屏幕尺寸决定使用哪种布局。

如果应用的特性需要特定的诸如相机的硬件来实现，你可以在运行时查看设备这些特性的可用性。如果必要的话，你也可以声明你的应用需要的特性，以便类似Google Play的市场可以阻止应用安装到不支持这些特性的设备上。

**了解更多：**

[Device Compatibility](http://developer.android.com/guide/practices/compatibility.html)
[Resources Overview](http://developer.android.com/guide/topics/resources/overview.html)
[User Interface Overview](http://developer.android.com/guide/topics/ui/overview.html)
