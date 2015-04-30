# Activity

> 翻译：[JiongBull](https://github.com/JiongBull)  
 微博：[@JiongBull](http://weibo.com/jiongbull/)  
 索引：[Android API Guide](https://github.com/JiongBull/Android-API-Guide)  

[Activity](http://developer.android.com/reference/android/app/Activity.html)是一种可以提供屏幕功能的应用组件，用户可以与之交互来完成一些事情，比如拨号、拍照、发邮件或查看地图。每个activity都提供了一个窗口，并在上面绘制自己的用户界面。窗口通常会填充整个屏幕，但也有可能会比屏幕小并浮动在其他窗口上面。

应用通常由许多activity组成，而且他们彼此不耦合。一般来说，用户在第一次启动应用时，会启动一个被称为“main”的activity。每个activity执行不同的操作，又都可以启动另外的activity。一旦新的activity启动了，前一个activity就会被停止，但是系统会在栈中保存activity（“回退栈”）。当新的activity启动时，它会被压入栈中并获取用户的焦点。回退栈遵守基本的“后入先出”的栈机制，所以当用户按下回退键时，当前activity会从栈中弹出（并被摧毁），而前一个activity会恢复。（关于回退栈在[Tasks and Back Stack](http://developer.android.com/guide/components/tasks-and-back-stack.html)文档中有更多论述）

当新的activity启动时，当前activity就会停止，其中的状态变化会通过activity生命周期的回调方法来通知。因为activity内部状态的变化，不管是系统在创建、停止、恢复还是摧毁activity时，它都会接收到多个回调方法，并且每个回调都提供了执行特定工作的机会， 可以用来应对状态的变化。例如，在activity停止时，应该释放大型对象，例如网络或者数据库连接。在activity恢复时，可以重新获取必备的资源并恢复被中断的操作。这些状态转换都是activity生命周期的一部分。

文档的剩余部分讨论了如何构建和使用activity，包含对activity的生命周期的彻底讨论，以便你可以在activity各种状态间正确的切换。

## 创建activity

想要创建activity，就必须创建一个[Activity](http://developer.android.com/reference/android/app/Activity.html)的子类（或者已存在的某个子类）。在子类中，你需要去实现一些回调方法，activity在它生命周期的不同状态内间切换时（例如创建、停止、恢复或摧毁activity时）会调用那些回调方法。最重要的两个回调方法是：

* [onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))
	必须实现该方法。系统在创建activity时会调用该方法。在你的实现里，应该初始化activity的全部组件。最重要的是，在这里必须调用[setContentView()](http://developer.android.com/reference/android/app/Activity.html#setContentView(android.view.View))定义activity的用户界面布局。
* [onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())
	在出现用户要离开activty的迹象时，系统会调用这个方法（尽管这不总是意味着该activity会被销毁）。通常应该在这里提交当前会话中需要保存的变化（因为用户可能会返回）。

还应该使用一些其他的生命周期回调方法，这样就能在各个activity之间提供流畅的用户体验，并且可以处理那些可能会导致activity停止甚至被销毁的意外中断。晚点会在关于[Managing the Activity Lifecycle](http://developer.android.com/guide/components/activities.html#Lifecycle)的章节中讨论所有生命周期的回调方法。

### 实现用户界面

activity的用户界面是由一系列的视图派生类组成的，这些对象都派生自[View](http://developer.android.com/reference/android/view/View.html)类。activity窗口内的每个视图都控制一块特定的矩形空间，以便响应用户交互。例如，某个可能是按钮的视图会在用户触碰它时触发操作。

Android提供了一系列现成的视图，可以直接使用它们来设计和组织布局。“widget”是可以为屏幕提供可视化（并且可交互）元素的视图，例如按钮、文本框、复选框或只是一张图片。“layout”是派生自[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)的视图，它可以为它的子视图提供独一无二的布局模式，例如线形布局或相对布局。也可以继承[View](http://developer.android.com/reference/android/view/View.html)和[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)类（或它们已存在的子类）来创造自己的控件和布局，然后把他们应用到activity布局中。

使用保存在应用资源文件里的XML布局文件是定义视图布局的最常见的方式。使用这种方式，可以保证用户界面的设计始终与决定activity行为的源代码是分开的。可以把布局的资源ID传递给[setContentView()](http://developer.android.com/reference/android/app/Activity.html#setContentView(int))来设置activity的UI布局。然而，也可以在activity的代码中创建一些新的[View](http://developer.android.com/reference/android/view/View.html)，把这些新[View](http://developer.android.com/reference/android/view/View.html)插入到[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)中来构建视图层级，然后把根[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)传递给[setContentView()](http://developer.android.com/reference/android/app/Activity.html#setContentView(android.view.View))来应用布局。

更多关于创建用户界面的信息，请参阅[User Interface](http://developer.android.com/guide/topics/ui/index.html)文档。

### 在manifest中声明activity

为了让系统可以访问你的activity，必须在manifest文件中声明activity。想要声明activity，只需打开manifest文件并在`<application>`元素下面添加一个`<activity>`子元素。例如：

```xml
<manifest ... >
  <application ... >
      <activity android:name=".ExampleActivity" />
      ...
  </application ... >
  ...
</manifest >
```

还有许多其他属性可以被拿来包含在这个元素里，例如定义activity标签的属性，定义图标的属性或定义UI风格的主题属性。只有`android:name`属性是必须的，它指定了activity的类名。一旦发布了应用，就不应该再修改这个名称了，因为如果修改了就有可能破坏一些功能，例如应用的快捷方式（阅读博客文章[Things That Cannot Change](http://android-developers.blogspot.com/2011/06/things-that-cannot-change.html))。

阅读[<activity>](http://developer.android.com/guide/topics/manifest/activity-element.html)元素的声明了解更多关于在manifest中声明activity的信息。

### 使用intent filter

在[`<activity>`](http://developer.android.com/guide/topics/manifest/activity-element.html)中使用[`<intent-filter>`](http://developer.android.com/guide/topics/manifest/intent-filter-element.html)元素指定各种intent filter，就可以声明其他应用的组件如何才能激活该activity。

当使用Android SDK工具创建新的应用时，会自动为你创建一个站位的activity，其中包含了一个intent filter，声明该activity可以响应被称为“main”的action，并且应该被归类于“launcher”的类别。该intent filter看起来就像这样：

```xml
<activity android:name=".ExampleActivity" android:icon="@drawable/app_icon">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

[`<action>`](http://developer.android.com/guide/topics/manifest/action-element.html)元素	指定了这是应用的“主”入口。[`<category>`](http://developer.android.com/guide/topics/manifest/category-element.html)元素指明了这个activity应该被列举在系统的应用启动器（用来让用户启动该activity）上。

如果想要让应用成为一个独立的应用并且不允许其他应用激活它的activity，那么就不需要任何intent filter了。就像前面的事例那样，有且只有一个activity拥有“main” action和“launcher” category就行了。activity不拥有intent filter时，对其他应用来说是不可用的，不过你自己却可以通过显示intent来启动它们（就像在下面的章节中讨论的那样）。

然而，如果希望activity可以响应来自其他应用（或自己应用）传递的隐式intent，那么必须为activity定义一些附加的intent filter。针对每种想要响应的intent，都必须对应包含一个[`<intent-filter>`](http://developer.android.com/guide/topics/manifest/intent-filter-element.html)，其中包含了[`<action>`](http://developer.android.com/guide/topics/manifest/action-element.html)元素，还有可选的[`<category>`](http://developer.android.com/guide/topics/manifest/category-element.html)元素和/或[`<data>`](http://developer.android.com/guide/topics/manifest/data-element.html)元素。这些元素指定了activity会响应什么类型的intent。

更多关于activity如何响应intent的信息，请参阅[Intents and Intent Filters](http://developer.android.com/guide/components/intents-filters.html)文档。