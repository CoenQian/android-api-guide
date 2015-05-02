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

## 启动activity

可以通过调用[startActivity()](http://developer.android.com/reference/android/app/Activity.html#startActivity(android.content.Intent))来启动其他activity，只需要把描述目标activity的[Intent](http://developer.android.com/reference/android/content/Intent.html)传递给该方法就可以了。该intent要么指定了确切的activity，要么描述了想要执行的操作类型（系统会选择合适的activity，甚至可以是来自不同应用的activity）。intent也可以携带少量的数据，以便给启动的activity使用。

在你自己的应用中，经常需要简单的启动一个已知的activity。可以创建一个intent，使用类名来明确指定想要启动的activity。例如，下面介绍了activity如何启动另外一个名为`SignInActivity`的activity：

```java
Intent intent = new Intent(this, SignInActivity.class);
startActivity(intent);
```

另外，应用可能还想使用activity中的数据执行一些操作，例如发送电子邮件、发送文本消息或更新状态。在这种情况下，应用自己可能没有能执行这些操作的activity，所以可以转而利用设备上其他应用提供的能执行这些操作的activity。这就是intent的价值所在，可以创建一个intent，在上面描述想要执行的操作，然后系统会为你从另外一个应用启动合适的activity。如果有多个activity可以处理该intent，那么用户可以选择使用哪一个。例如，如果想让用户可以发送电子邮件信息，可以创建如下的intent：

```java
Intent intent = new Intent(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_EMAIL, recipientArray);
startActivity(intent);
```

添加到intent中的[EXTRA_EMAIL](http://developer.android.com/reference/android/content/Intent.html#EXTRA_EMAIL) extra是一个字符串数组，里面包含了邮件的收件地址列表。如果有邮件应用响应了该intent，它会读取extra中提供的字符串数组并把它们放到电子邮件结构表格的“to”域中。在这种情况下，会启动电子邮件应用，当用户执行完操作后，会恢复到你的activity。

### 启动activity来获取结果

有时，可能想从启动的activity里获取结果。在这种情况下，请调用[startActivityForResult()](http://developer.android.com/reference/android/app/Activity.html#startActivityForResult(android.content.Intent,%20int))来启动activity（而不是[startActivity()](http://developer.android.com/reference/android/app/Activity.html#startActivity(android.content.Intent))）。然后实现[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))回调方法并在其中获取来自后续启动的activity的结果。在后续activity执行完毕时，它会在[Intent](http://developer.android.com/reference/android/content/Intent.html)中把结果返回给[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))方法。

例如，可能需要用户从联系人列表中选择一个用户，这样activity就能做一些与那个联系人信息相关的事了。下面是如何创建这样的intent并处理结果的事例：

```java
private void pickContact() {
    // 创建intent来“选取”在content provider URI中定义的联系人
    Intent intent = new Intent(Intent.ACTION_PICK, Contacts.CONTENT_URI);
    startActivityForResult(intent, PICK_CONTACT_REQUEST);
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // 如果请求执行顺利（OK）并且请求是PICK_CONTACT_REQUEST
    if (resultCode == Activity.RESULT_OK && requestCode == PICK_CONTACT_REQUEST) {
        // 对联系人的content provider执行查询来获取联系人姓名
        Cursor cursor = getContentResolver().query(data.getData(), new String[] {Contacts.DISPLAY_NAME}, null, null, null);
        if (cursor.moveToFirst()) { // 若cursor非null则为true
            int columnIndex = cursor.getColumnIndex(Contacts.DISPLAY_NAME);
            String name = cursor.getString(columnIndex);
            // 处理被选择的联系人姓名...
        }
    }
}
```

这个事例显示了处理activity结果的基本逻辑，你应该在[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))方法中像这样使用。首要条件是检查请求是否成功，如果成功了，那么不管响应请求的是什么样的结果，`resultCode`都应该为[RESULT_OK](http://developer.android.com/reference/android/app/Activity.html#RESULT_OK)，在首要条件检查通过的情况下，再检查`requestCode`是否匹配传入[startActivityForResult()](http://developer.android.com/reference/android/app/Activity.html#startActivityForResult(android.content.Intent,%20int))中的第二个参数。从那里开始，代码主要查询[Intent](http://developer.android.com/reference/android/content/Intent.html)里返回的activity结果数据（在data参数中）并处理。

然后，[ContentResolver](http://developer.android.com/reference/android/content/ContentResolver.html)会针对content provider执行查询，返回一个[Cursor](http://developer.android.com/reference/android/database/Cursor.html)，可以通过它来读取查询的数据。更多信息，请参阅[Content Providers](http://developer.android.com/guide/topics/providers/content-providers.html)文档。

更多关于使用intent的信息，请参阅[Intents and Intent Filters](http://developer.android.com/guide/components/intents-filters.html)文档。

## 关闭activity

可以通过调用activity的[finish()](http://developer.android.com/reference/android/app/Activity.html#finish())方法来关闭它。也可以通过调用[finishActivity(](http://developer.android.com/reference/android/app/Activity.html#finishActivity(int)))来关闭先前启动的独立activity。

> **注意**：在大多数情况下，你不应该明确的使用这些方法结束activity。在下面章节中会探讨activity的生命周期，Android系统会为你管理activity的生命周期，所以就不需要自己去关闭activity了。调用这些方法会对用户体验产生不利的影响，只有在你完全不想让用户返回这个activity的实例时才适合调用它们。

## 管理activity的生命周期

对于开发一款健壮和灵活的应用来说，实现activity的回调方法来管理它的生命周期是至关重要的。与其他activity关系、任务栈和回退栈都会影响activity的生命周期。

activity大体上可以存在以下三种状态：

* 恢复（Resumed）
activity处于屏幕的前台并且拥有用户的焦点。（该状态有时也被称为“运行”）
	
* 暂停（Paused）
其他的activity处于前台并拥有焦点，但是这个activity依然可见。也就是说，其他的activity在该activity的上面可见，部分透明或不占有全部的屏幕空间。暂停的activity是完全存在着的（[Activity](http://developer.android.com/reference/android/app/Activity.html)对象保留在内存中，它维护了所有的状态和成员信息，并依附于窗口管理器），但是在内存极低的情况下会被系统杀掉。
	
* 停止（Stopped）
activity完全被其他的activity遮住了（该activity现在在“后台”中）。停止的activity也还是存在着的（Activity对象保留在内存中，它维护了所有的状态和成员信息，但是没有依附于窗口管理器了）。然而，它不再对于用户可见了，并且当其他地方需要内存时它会被系统杀掉。

如果activity被暂停或停止，那么系统就能从内存中移除它，要么是要求结束（调用它的finish()方法），要么干脆杀掉它的进程。当这个activity被重新打开时（在被结束或杀掉之后），它必须重建所有一切。

### 实现生命周期的回调

 当activity在上面所说的各种状态间互相转换时，会通过不同的回调方法进行通知。所有的回调方法都是钩子，所以可以覆盖它们以便在activity状态变化时执行合适的任务。下面的activity包含了基本的生命周期方法：

```java
public class ExampleActivity extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // activity被创建了。
    }
    @Override
    protected void onStart() {
        super.onStart();
        // activity刚要变得可见。
    }
    @Override
    protected void onResume() {
        super.onResume();
        // activity已经变得可见了（现在是“恢复”（"resumed”)）。
    }
    @Override
    protected void onPause() {
        super.onPause();
        // 其他的activity获取了焦点（当前的activity即将“暂停”（”paused”））。
    }
    @Override
    protected void onStop() {
        super.onStop();
        // activity不再可见了（现在是“停止”（”stopped”））。
    }
    @Override
    protected void onDestroy() {
        super.onDestroy();
        // activity即将被摧毁。
    }
}
```

> **注意**：在实现这些生命周期方法之前必须先调用父类的实现，正如上面的事例展示的那样。

总之，这些方法定义了activity的整个生命周期。通过实现这些方法，可以监视在activity生命周期里的三个嵌套循环：

* activity的**整个生命周期**发生在调用[onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))和[onDestroy()](http://developer.android.com/reference/android/app/Activity.html#onDestroy())之间。activity应该在[onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))中进行“全局”状态的设定（例如定义布局）， 在[onDestroy()](http://developer.android.com/reference/android/app/Activity.html#onDestroy())中释放所有的剩余资源。例如，如果activity有一个运行在后台的线程，该线程的作用是从网络上下载数据，它可能这样的，在[onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))中创建线程，在[onDestroy()](http://developer.android.com/reference/android/app/Activity.html#onDestroy())中停止线程。
* activity的**可见生命周期**发生在调用[onStart()](http://developer.android.com/reference/android/app/Activity.html#onStart())和[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())之间。在此期间，用户在屏幕上看到activity并可以与之交互。例如，在新的activity启动时，当前activity会调用[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())并且不再可见。在这两个方法之间，要维持给用户显示activity所必须的资源。例如，可以在[onStart()](http://developer.android.com/reference/android/app/Activity.html#onStart())中注册一个[BroadcastReceiver](http://developer.android.com/reference/android/content/BroadcastReceiver.html)来监控那些会影响UI的变化，而当用户不再看到显示的东西时，在[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())中注销它。在activity整个生命周期中，因为activity在对用户可见与不可见之间转换，系统可能会多次调用[onStart()](http://developer.android.com/reference/android/app/Activity.html#onStart())和[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())。
* activity的**后台生命周期**发生在调用[onResume()](http://developer.android.com/reference/android/app/Activity.html#onResume())和[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())之间。 在此期间，activity在屏幕上所有其他的activity之上，并且拥有用户输入焦点。activity会经常在前台和后台之间转换。例如，当设备休眠或弹出对话框时就会调用[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())。因为状态会经常转换，所以这两个方法中的代码应该尽量轻量，以避免转换缓慢让用户等候。

图1阐明了这些循环和activity状态转换时可能的路径。矩形框表示你可以继承这些回调方法，以便在activity转换状态时执行特定的操作。

![activity_lifecycle](https://raw.githubusercontent.com/JiongBull/Android-API-Guide/master/images/activity_lifecycle.png)

图1. activity的生命周期。

表1同样列出了所有的生命周期回调方法，描述了每个回调方法的细节和处于activity整个生命周期的位置，还有系统是否可以在执行完回调方法后杀死activity。

| 方法 | 说明 | 结束后可杀 | 之后调用 |
| :--- | :--- | :--- | :--- |
| [onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)) | activity第一次被创建时调用。应该在这里处理通用静态设置，例如创建视图、把数据绑定到列表中等等。该方法被传递了一个Bundle对象，如果状态可以被捕获，那么里面就包含了以前的状态（参见后面的[Saving Activity State](http://developer.android.com/guide/components/activities.html#actstate)）。后面通常跟着`onStart()`。 | 否 | `onStart()` |
| [onRestart()](http://developer.android.com/reference/android/app/Activity.html#onRestart()) | activity停止之后，再次启动之前调用。后面通常跟着`onStart()`。 | 否 | `onStart()` |
| [onStart()](http://developer.android.com/reference/android/app/Activity.html#onStart()) | activity将要变得对用户可见时调用。如果activity进入前台，那么后面执行`onResume()`，如果不可见则执行`onStop()`。 | 否 | `onResume()`或`onStop()` |
| [onResume()](http://developer.android.com/reference/android/app/Activity.html#onResume()) | 在activity变得可以与用户交互之前调用。此时，activity位于activity栈的顶部，并且拥有输入焦点。后面通常跟着`onPause()`。 | 否 | `onPause()` |
| [onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause()) | 当系统将要恢复另外的activity时调用。该方法通常用来把未保存的变化提交为持久数据，停止可能会消耗CPU的动画和其他业务，等等。不管它执行什么任务都必须很快做完，因为下一个activity只有等它执行完了才会进入恢复状态。如果该activity返回前台，那么后面执行`onResume()`，如果变得对用户不可见了则执行`onStop()`。 | 是 | `onResume()`或`onStop()` |
| [onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop()) | 当activity不再对用户可见时调用。activity要被摧毁或其他的activity（已存在的或者新的都行）要进入恢复状态并覆盖它时就会执行。如果activity将要返回前台与用户交互，那么后面就执行`onRestart()`，如果activity将要离开则执行`onDestroy()`。 | 是 | `onRestart()`或`onDestroy()` |
| [onDestroy()](http://developer.android.com/reference/android/app/Activity.html#onDestroy()) | activity被销毁前调用。这是activity能接收到的最后一个调用。该方法被调用要么是因为activity的结束（有时调用它的[finish()](http://developer.android.com/reference/android/app/Activity.html#finish()))，要么是因为系统为腾出空间销毁该activity的实例。可以通过[isFinishing()](http://developer.android.com/reference/android/app/Activity.html#isFinishing())方法区分这两种情景。 | 是 | 无 |

标签为“**结束后可杀？**”的列指示着系统能否在该方法返回后立即杀掉activity的宿主进程，而不用再执行activity代码中的其他回调。有三个方法被标记为“是”：（[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())、[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())和[onDestroy()](http://developer.android.com/reference/android/app/Activity.html#onDestroy())）。因为[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())是这三个里面第一个被调用的，一旦activity被创建，[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())是系统杀掉进程前最后一个能保证调用的方法，如果系统在紧急情况下必须恢复内存，那么[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())和[onDestroy()](http://developer.android.com/reference/android/app/Activity.html#onDestroy())可能不会被调用。因此，应该使用[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())来写入重要的持久态数据。然而，对于在[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())中保存什么样的数据应该有选择，因为在这个方法中的任何阻塞都会影响转换到下一个activity，降低了用户体验。

在“**结束后可杀？**”列中被标记为“否”的方法会保护activity的宿主进程不会在方法执行完之后被立即杀掉。因此，activity在[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())返回时到[onResume()](http://developer.android.com/reference/android/app/Activity.html#onResume())被调用时的这段时间是可以被立即杀掉的。在调用[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())返回之前是不能被立即杀掉的。

> **注意**：对于表1中定义的那些从技术上说无法被“杀掉”的activity，还是有可能被系统杀掉的，不过只会发生在缺少资源的极端情况下。在[Processes and Threading](http://developer.android.com/guide/components/processes-and-threads.html)文档中论述了更多activity可能被杀掉的情形。

### 保存activity状态

在介绍管理activity的生命周期（[Managing the Activity Lifecycle](http://developer.android.com/guide/components/activities.html#Lifecycle)）时简单的提到过在activity暂停或停止时，activity的状态会被保存。这是成立的，因为当[Activity](http://developer.android.com/reference/android/app/Activity.html)暂停或停止时，内存中仍然保留着[Activity](http://developer.android.com/reference/android/app/Activity.html)对象，所有关于它的成员和当前状态的信息都未释放。因此，用户对activity做的任何改变都保存下来了，这样在activity返回前端时（处于“恢复”状态），那些变化依旧还在。

然而，系统为了回收内存会销毁activity，[Activity](http://developer.android.com/reference/android/app/Activity.html)对象也就被销毁了，所以系统不能那么简单的就恢复到完整状态了。相反，如果用户回退到该activity，系统必须重建该Activity对象。然而，用户不知道系统销毁了该activity并重建了它，因此依旧期望它还是原来的样子。在这种情况下，你可以实现onSaveInstanceState()回调方法，它可以让你保存关于activity状态的信息，这样你就能确保activity状态的重要信息保存下来了。

系统会在activity变得不稳定前调用[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))。系统会往该方法里传递一个[Bundle](http://developer.android.com/reference/android/os/Bundle.html)，可以使用诸如[putString()](http://developer.android.com/reference/android/os/BaseBundle.html#putString(java.lang.String,%20java.lang.String))和[putInt()](http://developer.android.com/reference/android/os/BaseBundle.html#putInt(java.lang.String,%20int))的方法在那里保存关于activity的状态信息。如果系统杀掉了应用进程，然后当用户回退到activity时，系统就会重建该activity，并把这个[Bundle](http://developer.android.com/reference/android/os/Bundle.html)传递给[onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))和[onRestoreInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onRestoreInstanceState(android.os.Bundle))。无论使用哪种方法，都能从[Bundle](http://developer.android.com/reference/android/os/Bundle.html)中提取保存的状态并恢复。如果没有可以恢复的状态信息，那么传递给你的[Bundle](http://developer.android.com/reference/android/os/Bundle.html)就是null（和第一次创建activity时一样的情形）。

![restore_instance](https://raw.githubusercontent.com/JiongBull/Android-API-Guide/master/images/restore_instance.png)

图2. 有两种方式可以让activity返回到用户焦点时保持完整状态：要么是activity被销毁后重建，activity恢复到之前保存的状态；要么是activity被停止后恢复，activity保存了完整状态。

> **注意**：并不保证[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))会在activity被销毁前调用，因为有些情况下没有必要保存状态（例如使用回退按钮离开activity时，因为用户明确表示要关掉该activity了）。如果系统调用[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))，那么可能在[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())前也可能在[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())前。

然而，即使你什么也不做，也没有实现[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))，[Activity](http://developer.android.com/reference/android/app/Activity.html)类的默认[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))实现还是会保存一些activity状态的。具体来说，默认实现会调用布局中每个[View](http://developer.android.com/reference/android/view/View.html)的相应[onSaveInstanceState()](http://developer.android.com/reference/android/view/View.html#onSaveInstanceState())方法，这样就能让每个视图提供那些应该保存的信息了。Android框架里的几乎所有组件都适当的实现了这个方法，这样，UI上的任何可见变化都会自动保存并在activity重建时恢复。例如，[EditText](http://developer.android.com/reference/android/widget/EditText.html)组件会保存用户输入的任何文本，[CheckBox](http://developer.android.com/reference/android/widget/CheckBox.html)组件会保存它是否被选中的状态。你只需要为想保存状态的每个组件提供唯一的ID（使用[android:id](http://developer.android.com/guide/topics/resources/layout-resource.html#idvalue)属性）就行了。如果组件没有ID，系统就不能保存它的状态了。

> 你还可以设置[android:saveEnabled](http://developer.android.com/reference/android/R.attr.html#saveEnabled)属性为`"false"`或调用[setSaveEnabled()](http://developer.android.com/reference/android/view/View.html#setSaveEnabled(boolean))方法来显示的禁止布局中的视图保存它的状态。通常情况下，不应该禁用它，但是如果想以不同的效果恢复activity状态，也可以这样使用。

虽然[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))的默认实现已经保存了关于activity的UI的有用信息，你仍然可能需要重写它来保存一些附加信息。例如，你可能需要保存activity生命周期里变化的成员值（可能在UI恢复时需要关联这些值，但是默认情况下，保存这些UI值的成员不会被恢复）。

因为[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))的默认实现帮我们保存了UI状态，所以如果你想重写该方法来保存更多状态信息，就应该在做任何工作之前先调用[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))的超类实现。同样，如果你想重写[onRestoreInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onRestoreInstanceState(android.os.Bundle))，也应该调用它的超类实现，以便默认实现可以恢复视图状态。

> **注意**：因为不保证[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))一定会被调用，所以当用户离开该activity时，应该只是用它记录activity的瞬时状态（UI状态），而不能用它存储持久化的数据。相反，应该使用[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())存储持久化数据（例如需要保存到数据库中的数据）。

测试应用能否恢复状态的一个简单的方式是旋转设备使屏幕方向变化。当屏幕方向变化，系统会销毁和重建activity，为新屏幕配置引用可用的替换资源。单独针对这个动机来说，因为用户在使用应用时可能会经常翻转屏幕，所以activity在重建时能否恢复状态是至关重要的。

### 处理配置变化

某些设备配置在运行时是可以变更的（例如屏幕方向、键盘可用性和语言）。当这种变化发生时，Android会重建正在运行的activity（系统调用[onDestroy()](http://developer.android.com/reference/android/app/Activity.html#onDestroy())），然后立即调用[onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))。这种行为旨在帮助应用适配新的配置，它可以自动使用你提供的替换资源（例如为不同屏幕方向和屏幕大小准备的布局）重新加载应用。

如果你设计好的activity，可以恰当的处理因为屏幕方向变化导致的重启和上面提到的恢复activity状态，那么针对activity生命周期中的其他突发事件，你的应用会更有弹性。

处理诸如重启的最好方式是使用[onSaveInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))和[onRestoreInstanceState()](http://developer.android.com/reference/android/app/Activity.html#onRestoreInstanceState(android.os.Bundle))（或[onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))）保存和恢复activity的状态，在前面的章节里论述过。

更多关于发生在运行时的配置变化以及如何处理这种情况的信息，请参阅[Handling Runtime Changes](http://developer.android.com/guide/topics/resources/runtime-changes.html)。

### 协调activity

当一个activity启动另一个时，它们都要经历生命周期的转换。当另一个activity被创建时，第一个activity会暂停和停止（不过，如果它仍然在背景中可见的话就不会停止）。如果这些activity会共享存储在磁盘或其他地方的数据，那么理解在第二个activity被创建之前第一个activity不会完全停止就非常重要了。相当于，启动第二个activity的进程与结束第一个的进程有重叠部分。

生命周期回调方法的执行顺序都已经定义好了，特别是针对在同一个进程中有两个activity，且其中一个启动另外一个的情况。下面是activity A启动activity B时出现的操作执行顺序：

1. 执行activity A的[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())方法。
2. 按顺序执行activity B的[onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))、[onStart()](http://developer.android.com/reference/android/app/Activity.html#onStart())和[onResume()](http://developer.android.com/reference/android/app/Activity.html#onResume())方法。（activity B现在没有获取用户焦点。）
3. 然后，如果activity A不再屏幕上可见了，那么执行它的[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())方法。

这种可预测的生命周期回调顺序，可以让你在处理从一个activity到另一个时管理信息的转换。例如，如果必须在第一个activity停止时写数据库，以便让后面的activity可以读取，那么你应该在[onPause()](http://developer.android.com/reference/android/app/Activity.html#onPause())写数据库而不是在[onStop()](http://developer.android.com/reference/android/app/Activity.html#onStop())执行时。