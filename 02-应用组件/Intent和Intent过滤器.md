# Intent和Intent过滤器

> 翻译：[JiongBull](https://github.com/JiongBull)  
 微博：[@JiongBull](http://weibo.com/jiongbull/)  
 索引：[Android API Guide](https://github.com/JiongBull/Android-API-Guide)

[Intent](http://developer.android.com/reference/android/content/Intent.html)是一种消息对象，可以用它请求其他应用组件（[app component](http://developer.android.com/guide/components/fundamentals.html#Components)）的操作。尽管intent有许多方式可以帮助组件之间进行交流，常用的是这三种最基础的用例：

- 启动activity:

	在应用中，一个[Activity](http://developer.android.com/reference/android/app/Activity.html)代表一个页面。你可以传递一个[Intent](http://developer.android.com/reference/android/content/Intent.html)给[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))来启动一个新的[Activity](http://developer.android.com/reference/android/app/Activity.html)实例。该[Intent](http://developer.android.com/reference/android/content/Intent.html)描述了要启动的activity，并且可以携带任何需要的数据。

	调用[startActivityForResult()](http://developer.android.com/reference/android/app/Activity.html#startActivityForResult(android.content.Intent,%20int))，就能在activity结束后接收到它返回的结果了。activity会在[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))回调中接收到另外一个Intent对象作为返回的结果。更多信息，请参阅[Activities](http://developer.android.com/guide/components/activities.html)指南。
	
- 启动service:

	[Service](http://developer.android.com/reference/android/app/Service.html)是一种不需要用户界面、在后台执行操作的组件。你可以传递一个[Intent](http://developer.android.com/reference/android/content/Intent.html)给[startService()](http://developer.android.com/reference/android/content/Context.html#startService(android.content.Intent))来开启一个service，通过service来执行一次性的操作（例如下载文件）。该[Intent](http://developer.android.com/reference/android/content/Intent.html)描述了要启动的service，并且可以携带任何需要的数据。

	如果service是使用客户端-服务器接口设计的，你可以从其他组件传递一个[Intent](http://developer.android.com/reference/android/content/Intent.html)给[bindService()](http://developer.android.com/reference/android/content/Context.html#bindService(android.content.Intent,%20android.content.ServiceConnection,%20int))来绑定到service上。更多信息，请参阅[Services](http://developer.android.com/guide/components/services.html)指南。

- 分发broadcast:

	broadcast是一种任何应用都能接收到的消息。系统会为系统事件分发不同的broadcast，比如系统启动时或设备充电时。你可以传递一个[Intent](http://developer.android.com/reference/android/content/Intent.html)给[sendBroadcast()](http://developer.android.com/reference/android/content/Context.html#sendBroadcast(android.content.Intent))、[sendOrderedBroadcast()](http://developer.android.com/reference/android/content/Context.html#sendOrderedBroadcast(android.content.Intent,%20java.lang.String))或[sendStickyBroadcast()](http://developer.android.com/reference/android/content/Context.html#sendStickyBroadcast(android.content.Intent))来把broadcast分发给其他应用。

## Intent类型

intent有两种类型：

- 显式intent通过名称（全限定类名）指定要启动的组件。通常可以使用显式intent来启动应用里的组件，因为你知道想启动的activity和service的类名。例如，启动一个响应用户操作的新的activity或者启动一个在后台下载文件的service。
- 隐式intent不用指定组件的名称，但是必须声明要执行的操作，这样其他应用的组件就能捕获到它。例如，如果想在地图上显示用户的位置，可以使用隐式intent去请求其他满足条件的应用在地图上显示具体的位置。

当创建了显式intent来启动activity或service时，系统就会立即启动在Intent对象中指定的组件。

当创建了隐式intent时，Android系统就会通过把intent里的内容与设备上其他应用在[manifest file](http://developer.android.com/guide/topics/manifest/manifest-intro.html)中声明的intent filter进行比较，来找出合适的组件启动。如果intent能匹配到一个intent filter，系统就会启动那个组件，并把[Intent](http://developer.android.com/reference/android/content/Intent.html)对象传递给它。如果匹配了不止一个intent filter，系统就会显示一个对话框，让用户来挑选使用哪个应用。

intent filter是一种在应用manifest文件里的表达式，它说明了组件想要收到的intent类型。例如，为activity声明intent filter，其他应用就能使用某种确定类型的intent启动你的activity。同样地，如果你没有为activity声明任何intent filter，那么它只能被显式intent启动。

> **注意**：为了保证应用的安全性，在启动Service时请确保使用显式intent，并且不要为你的service声明intent filter。使用隐式intent启动service是有安全风险的，因为你不能确定什么service会响应你的intent，并且用户也不知道哪个service启动了。

![intent-filters](https://raw.githubusercontent.com/JiongBull/Android-API-Guide/master/images/intent-filters@2x.png)

图1. 插图显示了隐式intent如何通过系统分发来启动其他的activity：[1] Activity A创建带操作描述的[Intent](http://developer.android.com/reference/android/content/Intent.html)，然后把它传递给[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))。 [2] Android系统搜索所有的应用来寻找匹配该intent的intent filter。当有匹配时，[3] 系统通过把[Intent](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))传递给匹配的activity（Activity B）然后调用它的[onCreate()](http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle))方法启动它。

## 创建Intent

[Intent](http://developer.android.com/reference/android/content/Intent.html)对象携带了一些信息，Android系统可以使用它们来决定启动哪个组件（例如确切的组件名或可以接收该intent的组件类别），为了确保接收的组件能正确的执行操作，intent还可以携带一些附加的信息给接收的组件使用（例如要执行的操作和处理的数据）。

[Intent](http://developer.android.com/reference/android/content/Intent.html)中主要包含的信息如下：

**组件名称**

要启动的组件的名称。

这是可选的，但是建议使用显示intent，这意味着该intent只会被分发给组件名称定义的应用组件。如果没有组件名称，那么这个intent就是隐式的，系统会基于intent中的其他信息决定应该把该intent分发给哪个组件（例如下面描述的操作、数据和类别）。所以如果你希望启动应用中某个特定的组件，那么应该指定组件的名称。

> **注意**：启动Service时，你应该永远指定组件的名称。 否则，你无法确定哪个service会响应那个intent，并且用户也不知道哪个service启动了。

[Intent](http://developer.android.com/reference/android/content/Intent.html)里名称的域变量是[ComponentName](http://developer.android.com/reference/android/content/ComponentName.html)对象，可以使用目标组件包含包名的全限定类名来指定。例如，`com.example.ExampleActivity`。你可以使用[setComponent()](http://developer.android.com/reference/android/content/Intent.html#setComponent(android.content.ComponentName))、[setClass()](http://developer.android.com/reference/android/content/Intent.html#setClass(android.content.Context, java.lang.Class))、[setClassName()](http://developer.android.com/reference/android/content/Intent.html#setClassName(java.lang.String,%20java.lang.String))或[Intent](http://developer.android.com/reference/android/content/Intent.html)的构造方法来设置组件名称。
	
**Action**

表示要执行操作的字符串（比如view或pick）。

在与broadcast相关的intent里，action表示会发生并报告的操作。action大体上决定了intent剩余部分的结构，具体来讲就是data和extra里包含什么。

虽然可以自己定义action来启动自己应用中的组件（或让其他应用来调用你应用里的组件），但是你最好使用Intent类或其他框架类中已定义好的操作常量。下面是启动activity的一些常见操作：

- [ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW)

当有些信息需要启动activity来展示给用户看时，可以在[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))处理的intent里使用这个action，比如在相册应用里预览图片，或在地图应用中查看地址。

- [ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)

也被称为“分享”intent，当有些数据需要通过其他应用来分享时，可以在[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))处理的intent里使用这个action，例如邮件应用或社交分享应用。
	
请参阅[Intent](http://developer.android.com/reference/android/content/Intent.html)类的参考文档获取更多预定义action的常量。其他action定义在Android框架的其他地方，例如在[Settings](http://developer.android.com/reference/android/provider/Settings.html)中定义了一些打开系统设置应用指定页面的action。

可以使用[setAction()](http://developer.android.com/reference/android/content/Intent.html#setAction(java.lang.String))或[Intent](http://developer.android.com/reference/android/content/Intent.html)构造方法来为intent指定action。

如果你定义了自己的action，请确保它包含了你应用的包名作为前缀。例如：

``` java
static final String ACTION_TIMETRAVEL = "com.example.action.TIMETRAVEL";
```

**Data**

一种URI（[Uri](http://developer.android.com/reference/android/net/Uri.html)对象），代表要操作的数据和/或数据的MIME类型。提供的data类型通常是由intent的action决定的。例如，如果action是[ACTION_EDIT](http://developer.android.com/reference/android/content/Intent.html#ACTION_EDIT)，那么data应该包含要编辑文档的URI。

在创建intent时，通常要在data的URI里附加指定data的类型（MIME类型），这点非常重要。例如，可以显示图片的activity就不能播放音频文件，尽管它们的URI的格式看起来很相似。所以指定data的MIME类型可以帮助Android系统找到最适合的组件来接收你的intent。然而，MIME类型有时可以从URI中推出来，特别当data是`content`（一种指明数据存储在设备位置的URI，受到[ContentProvider](http://developer.android.com/reference/android/content/ContentProvider.html)控制，它可以使data的MIME类型对系统可见）时。

仅设置data的URI，请调用[setData()](http://developer.android.com/reference/android/content/Intent.html#setData(android.net.Uri))。仅设置MIME类型，请调用[setType()](http://developer.android.com/reference/android/content/Intent.html#setType(java.lang.String))。如果需要的话，你可以使用[setDataAndType()](http://developer.android.com/reference/android/content/Intent.html#setDataAndType(android.net.Uri,%20java.lang.String))同时设置它们。

> **注意**：如果你想同时设置URI和MIME类型，不要调用[setData()](http://developer.android.com/reference/android/content/Intent.html#setData(android.net.Uri))和[setType()](http://developer.android.com/reference/android/content/Intent.html#setType(java.lang.String))，因为它们都会让对方的值失效。一定要使用[setDataAndType()](http://developer.android.com/reference/android/content/Intent.html#setDataAndType(android.net.Uri,%20java.lang.String))来设置URI和MIME类型。

**Category**

一种字符串，包含了一些附加的信息，关于应该捕获intent的组件的类别。虽然在intent里可以放置任意数量的category描述，但是大多数的intent甚至都不需要category。下面是一些常见category：

- [CATEGORY_BROWSABLE](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_BROWSABLE)

	目标activity允许自己被浏览器启动来显示链接引用的数据，例如图像或邮件信息。 

- [CATEGORY_LAUNCHER](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_LAUNCHER)

	activity是任务栈里最顶端的activity，并在系统应用启动器里列举出来。

请参阅[Intent](http://developer.android.com/reference/android/content/Intent.html)类的描述查看全部的category列表。

你可以使用[addCategory()](http://developer.android.com/reference/android/content/Intent.html#addCategory(java.lang.String))来指定category。

上面列举的这些属性（组件名称、action、data和category）可以定义独一无二的intent。通过读取这些属性，Android系统可以决定应该启动哪个组件。

然而，intent同样也可以携带其他信息，它们不会影响到决定启动哪个组件。intent也支持下面的属性： 

**Extras**

键-值对携带了完成请求action必须的附加信息。就像某些action需要特定类型的data URI，有些action也需要特定的extra。

你可以使用多种[putExtra()](http://developer.android.com/reference/android/content/Intent.html#putExtra(java.lang.String,%20android.os.Bundle))方法来添加extra数据，它们都需要两种参数：键的名称和值。你也可以创建一个[Bundle](http://developer.android.com/reference/android/os/Bundle.html)对象来添加所有的extra数据，然后使用[putExtras()](http://developer.android.com/reference/android/content/Intent.html#putExtras(android.content.Intent))方法把[Bundle](http://developer.android.com/reference/android/os/Bundle.html)插入到[Intent](http://developer.android.com/reference/android/content/Intent.html)中。

例如，当使用[ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)创建了一个用来发送邮件的intent，你可以把“地址”指定给[EXTRA_EMAIL](http://developer.android.com/reference/android/content/Intent.html#EXTRA_EMAIL)键，把“主题”指定给[EXTRA_SUBJECT](http://developer.android.com/reference/android/content/Intent.html#EXTRA_SUBJECT)键。

[Intent](http://developer.android.com/reference/android/content/Intent.html)类为标准数据类型指定了许多`EXTRA_*`常量。如果需要声明自己的extra键（为你的应用接收的intent使用），请确保包含你的应用的包名作为前缀。例如：

``` java
static final String EXTRA_GIGAWATTS = "com.example.EXTRA_GIGAWATTS";
```

**Flags**

Flag定义在[Intent](http://developer.android.com/reference/android/content/Intent.html)类中，对于intent来说它的功能就像元数据。flag可以告诉Android系统如何启动activity（例如，activity应该属于哪个[task](http://developer.android.com/guide/components/tasks-and-back-stack.html)，启动后如何处理activity（例如，是否在最近启动activity列表中））。

更多信息，请参阅[setFlags()](http://developer.android.com/reference/android/content/Intent.html#setFlags(int))方法。

### 显式intent事例

可以使用显式intent启动指定的应用组件，例如应用中某个特定的activity或service。为[Intent](http://developer.android.com/reference/android/content/Intent.html)对象指定组件名称就能创建显式的intent了，其他的intent属性是可选的。

例如，如果在应用中创建了一个名为DownloadService的service，用来从网络上下载文件，那么你可以使用下面的代码来启动它：

```java
// 在Activity中执行，所以'this'即Context
// fileUrl是URL字符串，比如"http://www.example.com/image.png"
Intent downloadIntent = new Intent(this, DownloadService.class);
downloadIntent.setData(Uri.parse(fileUrl));
startService(downloadIntent);
```

构造方法[Intent(Context, Class)](http://developer.android.com/reference/android/content/Intent.html#Intent(android.content.Context,%20java.lang.Class<?>))传递应用的Context和组件的Class对象。像这样，intent显式的启动了应用中的`DownloadService`类。

更多关于创建和启动service的信息，请参阅[Services](http://developer.android.com/guide/components/services.html)指南。

### 隐式intent事例

隐式intent指定了action，可以调用设备上任何可以执行该action的应用。当你的应用无法执行某个action，但是其他应用可能可以，同时你希望让用户来选择使用哪个应用，这种情形下使用隐式intent是非常有用的。

例如，如果有一些想要用户分享给其他人的内容，使用[ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND) action创建intent，然后把表示要分享内容的extra添加进来。当使用那个intent调用[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))后，用户就能选择使用哪个应用来分享内容了。

> **注意**：也有可能用户的设备上没有任何可以处理通过[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))发送过来的隐式intent的应用。如果真的那样，调用会失败并且你的应用会崩溃掉。为了验证是否有activity能接收到该intent，应该在[Intent](http://developer.android.com/reference/android/content/Intent.html)对象上调用[resolveActivity()](http://developer.android.com/reference/android/content/Intent.html#resolveActivity(android.content.pm.PackageManager))。如果结果是非null的，那么说明至少有一个应用可以处理该intent，这样就能安全的调用[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))了。如果结果是null，那么不应该使用这个intent，如果可能的话，应该禁用会发出该intent的功能。

```java
// 创建字符串文本消息
Intent sendIntent = new Intent();
sendIntent.setAction(Intent.ACTION_SEND);
sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
sendIntent.setType(HTTP.PLAIN_TEXT_TYPE); // MIME类型为"text/plain"

// 验证该intent是否会匹配activity
if (sendIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(sendIntent);
}
```

> **注意**： 在这种情况下，虽然并没有使用到URI，但是通过声明intent的data类型来指定extra中携带的内容。

当调用了[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))，系统就会检查所有安装的应用，确定哪个应用应该处理这种类型的intent（action类型为[ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)，并且携带"text/plain”数据的intent）。如果只有一个应用可以处理它，那么会立即打开那个应用，并且该intent会传递给那个应用。 如果多个activity都可以处理该intent，那么系统会给用户显示一个对话框，让用户来选择使用哪个应用。

### 强制的应用选择器

当不止一个应用可以可以响应隐式intent时，用户可以选择使用哪个应用并使它作为该action的默认选择。对于执行那些用户以后愿意用相同应用处理的action来说，这是非常棒的功能。比如打开网页（用户经常使用某款浏览器）。

然而，如果多种应用都可以响应该intent并且用户每次都想使用不同的应用来处理，那么应该显式的显示选择对话框。该选择对话框每次都会询问用户使用哪个应用来处理该action（用户不能为该action选择默认的应用）。例如，当应用使用[ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND) action来“分享”时，用户可能希望根据当前的情况来选择不同的应用分享，所以应该总是使用选择对话框，就像图2所示的。

使用[createChooser()](http://developer.android.com/reference/android/content/Intent.html#createChooser(android.content.Intent,%20java.lang.CharSequence))创建个[Intent](http://developer.android.com/reference/android/content/Intent.html)，然后把它传递给[startActivity()](http://developer.android.com/reference/android/app/Activity.html#startActivity(android.content.Intent))，这样就能显示一个选择器了。例如：

```java
Intent sendIntent = new Intent(Intent.ACTION_SEND);
...

// 对于UI上的文本请使用字符串资源。
// 这里说些什么，比如“Share this photo with”
String title = getResources().getString(R.string.chooser_title);
// 创建intent来显示选择器
Intent chooser = Intent.createChooser(sendIntent, title);

// 检验至少有一个activity可以匹配该intent
if (sendIntent.resolveActivity(getPackageManager()) != null) {
    startActivity(chooser);
}
```

然后就会显示一个对话框，内容是可以响应通过[createChooser()](http://developer.android.com/reference/android/content/Intent.html#createChooser(android.content.Intent,%20java.lang.CharSequence))方法传递过去的intent的应用列表，标题是传递的文本信息。

![intent-chooser](https://raw.githubusercontent.com/JiongBull/Android-API-Guide/master/images/intent-chooser.png)

图2. 选择对话框。

## 接收隐式Intent

为了声明应用可以接收到哪些隐式intent，应该在[manifest file](http://developer.android.com/guide/topics/manifest/manifest-intro.html)中使用[`<intent-filter>`](http://developer.android.com/guide/topics/manifest/intent-filter-element.html)元素为每个应要组件声明一个或多个intent filter。每个intent filter都会根据intent的action、data和category来指定它能接收到的intent类型。如果intent能满足其中任意一个intent filter，系统就会把这个隐式intent分发给该应用组件。

> **注意**：不管该组件有没有声明其他的intent filter，该隐式intent总是会被分发给它的目标。

应用组件应该为它能处理的每个独立功能单独声明filter。例如，在图库应用中某个activity可能有两个filter：一个filter用来预览图片，另一个用来编辑图片。当启动activity时，它会检查[Intent](http://developer.android.com/reference/android/content/Intent.html)，然后根据该[Intent](http://developer.android.com/reference/android/content/Intent.html)中的信息决定操作（比如是否显示编辑选项）。

每个intent filter都是在应用的manifest file里通过[`<intent-filter>`](http://developer.android.com/guide/topics/manifest/intent-filter-element.html)元素定义的，并且嵌套在相关的应用组件中（比如[`<activity>`](http://developer.android.com/guide/topics/manifest/activity-element.html)元素）。在[`<intent-filter>`](http://developer.android.com/guide/topics/manifest/intent-filter-element.html)中，可以使用下面三个元素中的一个或多个来指定它能接受的intent类型：

[`<action>`](http://developer.android.com/guide/topics/manifest/action-element.html)

在name属性里声明intent接收的action。值必须是action的字面字符串值，而不是类常量。

[`<data>`](http://developer.android.com/guide/topics/manifest/data-element.html)

声明接收data的类型，使用一个或多个能指定数据URI的各方面信息（scheme、host、port、path等）和MIME类型的属性。

[`<category>`](http://developer.android.com/guide/topics/manifest/category-element.html)

在`name`属性里声明intent接收的category 。值必须是category的字面字符串值，而不是类常量。

> **注意**：为了接收隐式intent，必须在intent filter中包含[CATEGORY_DEFAULT](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_DEFAULT)的category。方法[startActivity()](http://developer.android.com/reference/android/app/Activity.html#startActivity(android.content.Intent))和[startActivityForResult()](http://developer.android.com/reference/android/app/Activity.html#startActivityForResult(android.content.Intent,%20int))默认把所有的intent当做声明为[CATEGORY_DEFAULT](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_DEFAULT)的category来处理。如果没有在intent filter中这样声明，就不会有隐式intent传递给你的activity。

例如，下面是用intent filter声明的一个activity，它可以接收action为[ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)并且data是文本的intent：

```java
<activity android:name="ShareActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity>
```

创建一个包含多个[`<action>`](http://developer.android.com/guide/topics/manifest/action-element.html)、[`<data>`](http://developer.android.com/guide/topics/manifest/data-element.html)、或[`<category>`](http://developer.android.com/guide/topics/manifest/category-element.html)的filter是允许的。如果你这么做了，只需要确保那个组件能匹配这个filter元素中的所有元素。

如果想要处理多种不同的intent，但是它们仅仅匹配一些特定的action、data和category类型，那么就需要创建多种intent filter了。

隐式intent会把这三种元素里的每样都拿来和filter进行测试对比。intent必须通过所有的这三种类型的测试后才能被分发给对应的组件。如果它一样都没匹配，那么Android系统不会把该intent分发给组件。不过，因为组件可能拥有多个intent filter，如果某个intent没有匹配组件的一个filter，它还有可能匹配另外一个filter。更多关于系统如何解析intent的信息请参阅下面关于[Intent Resolution](http://developer.android.com/guide/components/intents-filters.html#Resolution)的章节。

> **注意**：请确保每次都使用显式intent去启动自己的service，并且不要为你的service声明intent filter，因为这样可以避免不经意间运行其他应用的[Service](http://developer.android.com/reference/android/app/Service.html)。

> **注意**：对于activity来说，必须在manifest文件声明intent filter。但是，board cast receiver的filter可以通过调用[registerReceiver()](http://developer.android.com/reference/android/content/Context.html#registerReceiver(android.content.BroadcastReceiver,%20android.content.IntentFilter,%20java.lang.String,%20android.os.Handler))来动态注册。然后可以使用[unregisterReceiver()](http://developer.android.com/reference/android/content/Context.html#unregisterReceiver(android.content.BroadcastReceiver))来解除receiver。这样可以让你的应用只在运行期间的某个时间段里监听特定的broadcast。

> **限制对组件的访问**

> 使用intent filter来阻止其他应用启动你的应用不是一种安全的方式。尽管intent filter限制了组件只会响应某些特定的隐式intent，但是如果开发者公开了组件的名称，其他应用就可以悄悄的通过显式intent来启用你的组件。如果要求只有你自己应用才能启动你的组件，请把那些组件的[exported](http://developer.android.com/guide/topics/manifest/activity-element.html#exported)属性设置为`“false”`。

### filter事例

为了更好的理解intent filter的行为，请阅读下面某个社交分享应用的manifest file的代码片段。

```java
<activity android:name="MainActivity">
    <!-- 这个activity是主入口，应该显示在应用启动器上 -->
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>

<activity android:name="ShareActivity">
    <!-- 这个activity处理带文本数据的“SEND” action -->
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
    <!-- 这个activity可以处理媒体数据的“SEND”和“SEND_MULTIPLE” -->
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <action android:name="android.intent.action.SEND_MULTIPLE"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="application/vnd.google.panorama360+jpg"/>
        <data android:mimeType="image/*"/>
        <data android:mimeType="video/*"/>
    </intent-filter>
</activity>
```

第一个activity，`MainActivity`，是应用的主入口，用户通过应用的启动图标启动应用后就打开这个activity了：

- [ACTION_MAIN](http://developer.android.com/reference/android/content/Intent.html#ACTION_MAIN) action说明这是主入口并且不需要任何intent data。
- [CATEGORY_LAUNCHER](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_LAUNCHER) category说明这个activity的图标应该出现在系统的应用启动器中。如果[`<activity>`](http://developer.android.com/guide/topics/manifest/activity-element.html)元素没有使用`icon`指定过图标，那么系统会使用[`<application>`](http://developer.android.com/guide/topics/manifest/application-element.html)元素中的图标。

为了在应用启动器中出现该activity，这两个必须成对出现。

第二个activity，`ShareActivity`，被用来分享文本和媒体内容。用户可以直接从`MainActivity`的导航功能进入该activity，也可以从其他应用通过发出能匹配这两个intent filter之一的隐式intent来进入`ShareActivity`。 

> **注意**：这个MIME类型，[application/vnd.google.panorama360+jpg](https://developers.google.com/panorama/android/)，是一种指定全景图片的特殊数据类型，你可以使用[Google panorama](http://developer.android.com/reference/com/google/android/gms/panorama/package-summary.html) API来处理它。

## 使用Pending Intent

[PendingIntent](http://developer.android.com/reference/android/app/PendingIntent.html)对象是对[Intent](http://developer.android.com/reference/android/content/Intent.html)对象的包装。[PendingIntent](http://developer.android.com/reference/android/app/PendingIntent.html)的主要作用是授予外部应用使用被包含的intent的权限，就好像在应用自己的进程中执行一样。

pending intent的主要使用情形包括：

- 声明在用户对[Notification](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)执行操作时，执行某个intent（Android系统会执行[NotificationManager](http://developer.android.com/reference/android/app/NotificationManager.html)这个[Intent](http://developer.android.com/reference/android/content/Intent.html)）。
- 声明在用户对[App Widget](http://developer.android.com/guide/topics/appwidgets/index.html)执行操作时，执行某个intent（桌面应用会执行这个[Intent](http://developer.android.com/reference/android/content/Intent.html)）。
- 声明会在未来的某个时间执行该intent（Android系统的[AlarmManager](http://developer.android.com/reference/android/app/AlarmManager.html)会执行这个[Intent](http://developer.android.com/reference/android/content/Intent.html)）。

因为每个[Intent](http://developer.android.com/reference/android/content/Intent.html)对象都被设计为被特定类型的应用组件处理（[Activity](http://developer.android.com/reference/android/app/Activity.html)，[Service](http://developer.android.com/reference/android/app/Service.html)或[BroadcastReceiver](http://developer.android.com/reference/android/content/BroadcastReceiver.html)），所以[PendingIntent](http://developer.android.com/reference/android/app/PendingIntent.html)和他们情况相同。在使用pending intent时，应用不是调用诸如[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))等方法来执行该intent，而是在调用相关构造方法创建[PendingIntent](http://developer.android.com/reference/android/app/PendingIntent.html)时声明目标组件类型：

- [PendingIntent.getActivity()](http://developer.android.com/reference/android/app/PendingIntent.html#getActivity(android.content.Context,%20int,%20android.content.Intent,%20int))创建针对启动[Activity](http://developer.android.com/reference/android/app/Activity.html)的[Intent](http://developer.android.com/reference/android/content/Intent.html)。
- [PendingIntent.getService()](http://developer.android.com/reference/android/app/PendingIntent.html#getService(android.content.Context,%20int,%20android.content.Intent,%20int))创建针对启动[Service](http://developer.android.com/reference/android/app/Service.html)的[Intent](http://developer.android.com/reference/android/content/Intent.html)。
- [PendingIntent.getBroadcast()](http://developer.android.com/reference/android/app/PendingIntent.html#getBroadcast(android.content.Context,%20int,%20android.content.Intent,%20int))创建针对启动[BroadcastReceiver](http://developer.android.com/reference/android/content/BroadcastReceiver.html)的[Intent](http://developer.android.com/reference/android/content/Intent.html)。

除非应用要接收其他应用的pending intent，否则你只用得到上面[PendingIntent](http://developer.android.com/reference/android/app/PendingIntent.html)的方法来创建[PendingIntent](http://developer.android.com/reference/android/app/PendingIntent.html)。

每种方法都需要当前应用的[Context](http://developer.android.com/reference/android/content/Context.html)，和要包装的[Intent](http://developer.android.com/reference/android/content/Intent.html)，还有一个或多个用来指定该intent应该如何被使用的flag（比如该intent是否能被多次使用）。

更多关于使用pending intent的信息请查看对应使用案例的文档，例如[Notifications](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)和[App Widgets API](http://developer.android.com/guide/topics/appwidgets/index.html)指南。

## Intent解析

当系统接收到一个启动activity的隐式intent时，它会基于下面三个方面来把intent与intent filter进行比较，找出最佳的activity：

- intent action
- intent data （URI和data类型都考虑）
- intent category

下面的章节描述了intent如何根据应用的manifest文件里的intent filter匹配合适的组件。

### Action测试

指定接收的intent action，intent filter可以声明0个或多个[`<action>`](http://developer.android.com/guide/topics/manifest/action-element.html)元素。例如：

```xml
<intent-filter>
    <action android:name="android.intent.action.EDIT" />
    <action android:name="android.intent.action.VIEW" />
    ...
</intent-filter>
```

要匹配这个filter的话，在[Intent](http://developer.android.com/reference/android/content/Intent.html)中指定的action必须要匹配上面列举的action之一。

如果filter中没有列举任何action，那么就没有东西给intent匹配了，所有的intent都不会通过测试。但是，如果Intent没有指定任何action，那么它会通过测试（只要filter包含至少一个action）。

### Category测试

指定接收的intent category，intent filter可以声明0个或多个[`<category>`](http://developer.android.com/guide/topics/manifest/category-element.html)元素。例如：

```xml
<intent-filter>
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    ...
</intent-filter>
```

intent要通过category测试的话，在Intent中的每个category都必须匹配filter中的category。反之不必要，intent filter中声明的category可能比[Intent](http://developer.android.com/reference/android/content/Intent.html)中的还多，但是[Intent](http://developer.android.com/reference/android/content/Intent.html)依然会通过测试。因此，没有category的intent不管filter里声明了什么category都会通过测试。

> **注意**：使用[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))和[startActivityForResult()](http://developer.android.com/reference/android/app/Activity.html#startActivityForResult(android.content.Intent,%20int))时，Android会自动的在所有的隐式intent中添加[CATEGORY_DEFAULT](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_DEFAULT) category。所以如果想要activity能够接收到这些隐式intent的话，就必须在它的intent filter中包含`"android.intent.category.DEFAULT”` category（如前面`<intent-filter>`例子所示）。

### Data测试

指定接收的intent data，intent filter可以声明0个或多个[`<data>`](http://developer.android.com/guide/topics/manifest/data-element.html)元素。例如：

```xml
<intent-filter>
    <data android:mimeType="video/mpeg" android:scheme="http" ... />
    <data android:mimeType="audio/mpeg" android:scheme="http" ... />
    ...
</intent-filter>
```

每个[`<data>`](http://developer.android.com/guide/topics/manifest/data-element.html) 元素可以指定一个URI结构和一个数据类型（MIME媒体类型）。有许多独立的属性，`scheme`、`host`、`port`和`path`，对应着URI的各个部分：

`<scheme>://<host>:<port>/<path>`

例如：

`content://com.example.project:200/folder/subfolder/etc`

在这个URI中，scheme是`content`，host是`com.example.project`，端口是`200`，path是`folder/subfolder/etc`。

在[`<data>`](http://developer.android.com/guide/topics/manifest/data-element.html)元素中的这些属性都是可选的，但是它们有线性依赖关系：

- 如果没有指定schema，那么host就被忽略了。
- 如果没有指定host，那么port就被忽略了。
- 如果schema和host都没有被指定，那么path就被忽略了。

当把intent中的URI与filter中指定的URI标准进行比较时，只需要比较filter中包含部分的URI就行了。例如：

- 如果filter只指定了scheme，所有拥有那个scheme的URI都会匹配这个filter。
- 如果filter指定了scheme和authority，但是没有path，那么所有拥有相同scheme和authority的URI都会通过该filter，而不用管它们的path是什么。
- 如果filter指定了scheme、authority和path，那么只有拥有相同scheme、authority和path的才能通过该filter。

> **注意**：path表达式可能包含星号（*）通配符，会匹配部分path名称。

data测试会把intent中的URI和MIME类型与filter中指定的URI和MIME类型进行比较。 规则如下

1. 既不包含URI，又不包含MIME类型的intent只能通过那些没有指定任何URI和MIME类型的filter的测试。
2. 包含URI，但是不包含MIME类型（URI中没有隐含也无法推理出来）的intent只能通过那些URI格式匹配并且没有指定MIME类型的filter的测试。
3. 包含MIME类型，但是不包含URI的intent只能通过那些有相同MIME类型并且没有指定URI格式的filer的测试。
4. 既包含URI又包含MIME类型（URI隐含或者可以推理出来）的intent，它的MIME类型匹配filter中列举的MIME类型时，它才能通过MIME类型部分的测试，它的URI匹配filter中的URI，或它包含`content:`或`file:`URI并且filter没有指定URI时，它才能通过URI部分的测试。

最后一条规则，就是说组件可以从文件或content provider中获取本地数据。因此，它们的filter可以只列举data type，而不需要显式标明`content:`和`file:scheme`。这是一种典型的使用方式。例如，像下面的[`<data>`](http://developer.android.com/guide/topics/manifest/data-element.html)元素，告诉Android这个组件可以从content provider中获取图片并显示：

```xml
<intent-filter>
    <data android:mimeType="image/*" />
    ...
</intent-filter>
```

因为大多数的数据都是通过content provider分发的，所以filter只指定data类型而不指定URI是很常见的。

另外一种常见的filter配置是使用scheme和data type。例如，像下面的[`<data>`](http://developer.android.com/guide/topics/manifest/data-element.html)元素，告诉Android这个组件可以从网络上获取视频数据来播放：

```xml
<intent-filter>
    <data android:scheme="http" android:type="video/*" />
    ...
</intent-filter>
```

### Intent匹配

intent与intent filter比较不只是用来发现要启动的目标组件，同时也是为了发现设备上组件的其他信息。例如，Home应用通过寻找所有指定action intent为[ACTION_MAIN](http://developer.android.com/reference/android/content/Intent.html#ACTION_MAIN) action和[CATEGORY_LAUNCHER](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_LAUNCHER) category的activity来放置应用启动器。

你的应用可以很容易的使用intent匹配。[PackageManager](http://developer.android.com/reference/android/content/pm/PackageManager.html)有一系列得`query...()`方法，它可以返回所有可以接收某个特定intent的组件，同样，还有一系列的`resolve...()`方法，可以找出最适合响应某个intent的组件。例如，[queryIntentActivities()](http://developer.android.com/reference/android/content/pm/PackageManager.html#queryIntentActivities(android.content.Intent,%20int))会返回能执行参数intent的所有activity列表，同样，[queryIntentServices()](http://developer.android.com/reference/android/content/pm/PackageManager.html#queryIntentServices(android.content.Intent,%20int))会返回service列表。所有的方法都不会激活组件的，它们只是列举它们能影响的组件列表。同样，[queryBroadcastReceivers()](http://developer.android.com/reference/android/content/pm/PackageManager.html#queryBroadcastReceivers(android.content.Intent,%20int))针对broadcast receivers。