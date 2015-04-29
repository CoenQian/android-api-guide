# 常用Intent

> 翻译：[JiongBull](https://github.com/JiongBull)  
 微博：[@JiongBull](http://weibo.com/jiongbull/)  
 索引：[Android API Guide](https://github.com/JiongBull/Android-API-Guide)  
 
intent可以启动其他应用的activity，只要在[Intent](http://developer.android.com/reference/android/content/Intent.html)对象里描述想要执行的操作就可以了（比如“查看地图”或“拍照“）。这种类型的intent被称为隐式intent，因为它不是明确指定要启动的应用组件，而是指定action并提供执行action需要的一些数据。 

在调用[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))或[startActivityForResult()](http://developer.android.com/reference/android/app/Activity.html#startActivityForResult(android.content.Intent,%20int))并把隐式intent作为参数传入时，系统会[解析该intent](http://developer.android.com/reference/android/app/Activity.html#startActivityForResult(android.content.Intent,%20int))并把它传递给给能处理它的应用，启动相关的[Activity](http://developer.android.com/reference/android/app/Activity.html)。如果不止一个应用可以处理该intent，系统会给用户显示一个对话框，让用户来选择使用哪个应用。

这篇文档介绍了一些隐式intent，按照处理intent的应用类型进行归类，你可以使用它们来执行常用操作。每个章节还介绍了如何通过创建[intent filter](http://developer.android.com/guide/components/intents-filters.html#Receiving)来告诉系统你的应用有处理相同操作的能力。

> **注意**：如果当前设备上没有可以接收某个隐式intent的应用，那么应用会在调用[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))时崩溃。为了验证是否存在能接收该intent的应用，请对[Intent](http://developer.android.com/reference/android/content/Intent.html)对象调用[resolveActivity()](http://developer.android.com/reference/android/content/Intent.html#resolveActivity(android.content.pm.PackageManager))。如果结果是非null的，说明至少有一个应用可以处理该intent，调用[startActivity()](http://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent))就是安全的。如果结果是null，那么就不应该使用该intent，并且如果可能的话，应该关闭那些会调用该intent的功能。

如果你还不熟悉如何创建intent和intent filter，那么应该先阅读[Intents and Intent Filters](http://developer.android.com/guide/components/intents-filters.html)。

要了解如何在开发主机上使用这篇文档中列举的intent，请参阅[Verify Intents with the Android Debug Bridge](http://developer.android.com/guide/components/intents-common.html#AdbIntents)。

**Google Now** 

[Google Now](http://www.google.com/landing/now/)使用这篇文档中列举的一些intent来响应语音命令。更多信息，请参阅[Intents Fired by Google Now](http://developer.android.com/guide/components/intents-common.html#Now)。

## 闹钟

### 创建闹钟

想要创建一个新的闹钟，请使用[ACTION_SET_ALARM](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SET_ALARM) action并使用下面定义的extra指定诸如时间和信息等闹钟的详细信息。

> **注意**：小时、分钟和信息的extra只有在Android 2.3（API级别9）或更高的平台上可用。其他的extra是在该版本的后续版本上引入的。

**Action**
[ACTION_SET_ALARM](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SET_ALARM)

**Data URI**
无

**MIME Type**
无

**Extras**
[EXTRA_HOUR](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_HOUR) 
闹钟的小时。

[EXTRA_MINUTES](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_MINUTES)
闹钟的分钟。

[EXTRA_MESSAGE](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_MESSAGE)
标识闹钟的自定义信息。

[EXTRA_DAYS](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_DAYS)
一个[ArrayList](http://developer.android.com/reference/java/util/ArrayList.html)，指定了闹钟在每周的哪几天重复。每一天都必须使用[Calendar](http://developer.android.com/reference/java/util/Calendar.html)类中定义的整型值来表示，例如[MONDAY](http://developer.android.com/reference/java/util/Calendar.html#MONDAY)。 

如果是一次性闹钟，请不要指定该extra。

[EXTRA_RINGTONE](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_RINGTONE)
一个`content:`类型的URI，用来指定闹钟使用的铃声，如果不需要铃声，请使用[VALUE_RINGTONE_SILENT](http://developer.android.com/reference/android/provider/AlarmClock.html#VALUE_RINGTONE_SILENT)。

如果要使用默认铃声，请不要指定该extra。

[EXTRA_VIBRATE](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_VIBRATE)
布尔值，用来指定闹钟是否需要震动功能。

[EXTRA_SKIP_UI](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_SKIP_UI)
布尔值，用来指定响应的应用在设置闹钟时是否跳过自身的UI。如果是true，应用会跳过所有确认UI，直接设置需要的闹钟。

**intent事例：**
```java
public void createAlarm(String message, int hour, int minutes) {
    Intent intent = new Intent(AlarmClock.ACTION_SET_ALARM)
            .putExtra(AlarmClock.EXTRA_MESSAGE, message)
            .putExtra(AlarmClock.EXTRA_HOUR, hour)
            .putExtra(AlarmClock.EXTRA_MINUTES, minutes);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

> **注意**：为了调用[ACTION_SET_ALARM](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SET_ALARM) intent，应用必须拥有[SET_ALARM](http://developer.android.com/reference/android/Manifest.permission.html#SET_ALARM)权限：
> `<uses-permission android:name="com.android.alarm.permission.SET_ALARM" />
`

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.SET_ALARM" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

### 创建计时器

想要创建一个倒计时计时器，请使用[ACTION_SET_TIMER](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SET_TIMER) action并使用下面定义的extra指定诸如持续时长等计时器的详细信息。

> **注意**：该intent是从Android 4.4（API级别19）开始引入的。

**Action**
[ACTION_SET_TIMER](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SET_TIMER)

**Data URI**
无

**MIME Type**
无

**Extras**
[EXTRA_LENGTH](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_LENGTH)
计时器的时长，单位秒。

[EXTRA_MESSAGE](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_MESSAGE)
标识计时器的自定义信息。

[EXTRA_SKIP_UI](http://developer.android.com/reference/android/provider/AlarmClock.html#EXTRA_SKIP_UI)
布尔值，用来指定响应的应用在设置计时器时是否跳过自身的UI。如果是true，应用会跳过所有确认UI，直接启动需要的计时器。

**intent事例：**
```java
public void startTimer(String message, int seconds) {
    Intent intent = new Intent(AlarmClock.ACTION_SET_TIMER)
            .putExtra(AlarmClock.EXTRA_MESSAGE, message)
            .putExtra(AlarmClock.EXTRA_LENGTH, seconds)
            .putExtra(AlarmClock.EXTRA_SKIP_UI, true);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

> **注意：**为了调用[ACTION_SET_TIMER](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SET_TIMER) intent，你的应用必须拥有[SET_ALARM](http://developer.android.com/reference/android/Manifest.permission.html#SET_ALARM)权限：
> `<uses-permission android:name="com.android.alarm.permission.SET_ALARM" />
`

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.SET_TIMER" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

### 显示所有闹钟

想要显示闹钟列表，请使用[ACTION_SHOW_ALARMS](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SHOW_ALARMS) action。

虽然会调用该intent的应用不会很多（它主要是给系统应用使用的），任何有闹钟功能的应用都应该实现该intent filter，以便能显示当前所有的闹钟列表。

> **注意**：该intent是从Android 4.4（API级别19）开始引入的。

**Action**
[ACTION_SHOW_ALARMS](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SHOW_ALARMS)

**Data URI**
无

**MIME Type**
无

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.SHOW_ALARMS" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

## 日历

### 添加日历事件

想要在用户的日历中添加一个新的事件，请使用[ACTION_INSERT](http://developer.android.com/reference/android/content/Intent.html#ACTION_INSERT) action并使用[Events.CONTENT_URI](http://developer.android.com/reference/android/provider/CalendarContract.Events.html#CONTENT_URI)指定data URI。可以使用下面定义的extra指定多种事件的详情。

**Action**
[ACTION_INSERT](http://developer.android.com/reference/android/content/Intent.html#ACTION_INSERT)

**Data URI**
[Events.CONTENT_URI](http://developer.android.com/reference/android/provider/CalendarContract.Events.html#CONTENT_URI)

**MIME Type**
`vnd.android.cursor.dir/event`

**Extras**
[EXTRA_EVENT_ALL_DAY](http://developer.android.com/reference/android/provider/CalendarContract.html#EXTRA_EVENT_ALL_DAY)
布尔值，指定是否为全天事件。

[EXTRA_EVENT_BEGIN_TIME](http://developer.android.com/reference/android/provider/CalendarContract.html#EXTRA_EVENT_BEGIN_TIME)
事件的开始时间（从UNIX计时元年开始以来的毫秒值，UNIX的计时元年也就是1970年1月1日的0点0分0秒0毫秒）。

[EXTRA_EVENT_END_TIME](http://developer.android.com/reference/android/provider/CalendarContract.html#EXTRA_EVENT_END_TIME)
事件的结束时间（从UNIX计时元年开始以来的毫秒值）。

[TITLE](http://developer.android.com/reference/android/provider/CalendarContract.EventsColumns.html#TITLE)
事件的标题。

[DESCRIPTION](http://developer.android.com/reference/android/provider/CalendarContract.EventsColumns.html#DESCRIPTION)
事件的描述。

[EVENT_LOCATION](http://developer.android.com/reference/android/provider/CalendarContract.EventsColumns.html#EVENT_LOCATION)
事件的位置。

[EXTRA_EMAIL](http://developer.android.com/reference/android/content/Intent.html#EXTRA_EMAIL)
指定受邀请人的电子邮件地址，用逗号隔开。

可以使用在[CalendarContract.EventsColumns](http://developer.android.com/reference/android/provider/CalendarContract.EventsColumns.html)类中定义的常量来指定更多的事件细节。

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.INSERT" />
        <data android:mimeType="vnd.android.cursor.dir/event" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

## 相机

### 拍照或录像并返回结果

想要打开相机应用并接收返回的照片或视频，请使用[ACTION_IMAGE_CAPTURE](http://developer.android.com/reference/android/provider/MediaStore.html#ACTION_IMAGE_CAPTURE)或[ACTION_VIDEO_CAPTURE](http://developer.android.com/reference/android/provider/MediaStore.html#ACTION_VIDEO_CAPTURE) action。记得在[EXTRA_OUTPUT](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_OUTPUT) extra中指定相机保存照片或视频的位置。

**Action**
[ACTION_IMAGE_CAPTURE](http://developer.android.com/reference/android/provider/MediaStore.html#ACTION_IMAGE_CAPTURE)或[ACTION_VIDEO_CAPTURE](http://developer.android.com/reference/android/provider/MediaStore.html#ACTION_VIDEO_CAPTURE)

**Data URI Scheme**
无

**MIME Type**
无

**Extras**
[EXTRA_OUTPUT](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_OUTPUT)
相机应用保存图片或视频文件的URI地址（Uri对象）。

在焦点重新从相机应用返回到你的activity时（你的应用会接收到[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))回调），可以从你在[EXTRA_OUTPUT](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_OUTPUT)值里指定的URI位置访问那些照片和视频。

> **注意**：在使用[ACTION_IMAGE_CAPTURE](http://developer.android.com/reference/android/provider/MediaStore.html#ACTION_IMAGE_CAPTURE)拍照时，相机可能会在结果[Intent](http://developer.android.com/reference/android/content/Intent.html)里返回照片的一个缩小尺寸的拷贝（缩略图），存储在名为`data`的extra中，是一个Bitmap对象。

**intent事例：**
```java
static final int REQUEST_IMAGE_CAPTURE = 1;
static final Uri mLocationForPhotos;

public void capturePhoto(String targetFilename) {
    Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	intent.putExtra(MediaStore.EXTRA_OUTPUT,Uri.withAppendedPath(mLocationForPhotos, targetFilename);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivityForResult(intent, REQUEST_IMAGE_CAPTURE);
    }
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == REQUEST_IMAGE_CAPTURE && resultCode == RESULT_OK) {
        Bitmap thumbnail = data.getParcelable("data");
        // 处理保存在mLocationForPhotos中的全尺寸照片
        ...
    }
}
```

更多关于如何使用intent拍照的信息，包括如何为输出路径创建合适的[Uri](http://developer.android.com/reference/android/net/Uri.html)，请阅读[Taking Photos Simply](http://developer.android.com/training/camera/photobasics.html)或[Taking Videos Simply](http://developer.android.com/training/camera/videobasics.html)。

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.media.action.IMAGE_CAPTURE" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

在处理该intent时，activity应该检查传入[Intent](http://developer.android.com/reference/android/content/Intent.html)中的[EXTRA_OUTPUT](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_OUTPUT) extra，然后把捕获的图片或视频保存在extra指定的路径中，最后调用[setResult()](http://developer.android.com/reference/android/app/Activity.html#setResult(int,%20android.content.Intent))，并传入在`“data”` extra中包含压缩缩略图的[Intent](http://developer.android.com/reference/android/content/Intent.html)。

### 启动只有拍照模式的相机应用

想要打开只有拍照模式的相机应用，请使用[INTENT_ACTION_STILL_IMAGE_CAMERA](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_STILL_IMAGE_CAMERA) action。

**Action**
[INTENT_ACTION_STILL_IMAGE_CAMERA](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_STILL_IMAGE_CAMERA)

**Data URI Scheme**
无

**MIME Type**
无

**Extras**
无

**intent事例：**
```java
public void capturePhoto() {
    Intent intent = new Intent(MediaStore.INTENT_ACTION_STILL_IMAGE_CAMERA);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivityForResult(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.media.action.STILL_IMAGE_CAMERA" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

### 启动只有录制模式的相机应用

想要打开只有录制模式的相机应用，请使用[INTENT_ACTION_VIDEO_CAMERA](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_VIDEO_CAMERA) action。

**Action**
[INTENT_ACTION_VIDEO_CAMERA](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_VIDEO_CAMERA)

**Data URI Scheme**
无

**MIME Type**
无

**Extras**
无

**intent事例：**
```java
public void capturePhoto() {
    Intent intent = new Intent(MediaStore.INTENT_ACTION_VIDEO_CAMERA);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivityForResult(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.media.action.VIDEO_CAMERA" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

## 通讯录/联系人应用

### 选择联系人

想要让用户选择联系人并使应用能访问所有联系人的信息，请使用[ACTION_PICK](http://developer.android.com/reference/android/content/Intent.html#ACTION_PICK) action并把MIME类型指定为[Contacts.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.Contacts.html#CONTENT_TYPE)。

在回传给[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))回调方法中的结果Intent中，包含了指向被选择联系人的`content:` URI。响应的应用还会给你的应用授予临时权限，来使用[Contacts Provider](http://developer.android.com/guide/topics/providers/contacts-provider.html)读取联系人信息，尽管你的应用没有包含[READ_CONTACTS](http://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS)权限。

> 提示：如果只需要访问联系人的部分信息，比如电话号码和电子邮件地址，请参阅下一章节[select specific contact data](http://developer.android.com/guide/components/intents-common.html#PickContactData)。

**Action**
[ACTION_PICK](http://developer.android.com/reference/android/content/Intent.html#ACTION_PICK)

**Data URI Scheme**
无

**MIME Type**
[Contacts.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.Contacts.html#CONTENT_TYPE)

**intent事例:**
```java
static final int REQUEST_SELECT_CONTACT = 1;

public void selectContact() {
    Intent intent = new Intent(Intent.ACTION_PICK);
    intent.setType(ContactsContract.Contacts.CONTENT_TYPE);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivityForResult(intent, REQUEST_SELECT_CONTACT);
    }
}

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == REQUEST_SELECT_CONTACT && resultCode == RESULT_OK) {
        Uri contactUri = data.getData();
        // 处理存储在contactUri中被选中的联系人信息
        ...
    }
}
```

更多关于在拥有联系人URI的情况下如何获取联系人详细内容的信息，请参阅[Retrieving Details for a Contact](http://developer.android.com/training/contacts-provider/retrieve-details.html)。请牢记， 在使用上面的intent从联系人URI中抽取信息时，不需要[READ_CONTACTS](http://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS)权限就能读取那个联系人的详细信息。 

### 选择联系人的特定数据

要想让用户选择联系人的特定信息，比如电话号码、电子邮件地址或其他数据类型，请使用[ACTION_PICK](http://developer.android.com/reference/android/content/Intent.html#ACTION_PICK) action并使用下面列举的某个content type来指定MIME类型， 比如[CommonDataKinds.Phone.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.CommonDataKinds.Phone.html#CONTENT_TYPE)来获取联系人的电话号码。

如果只需要从通讯录中获取某种类型的数据，使用[ContactsContract.CommonDataKinds](http://developer.android.com/reference/android/provider/ContactsContract.CommonDataKinds.html)类来指定`CONTENT_TYPE`比使用[Contacts.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.Contacts.html#CONTENT_TYPE)（就像前一章节展示的那样）更高效，因为返回的结果可以让你直接访问目标数据，而不需要对[Contacts Provider](http://developer.android.com/guide/topics/providers/contacts-provider.html)行更多复杂的查询。

在回传给[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))回调中的结果Intent中，包含了指向被选择联系人的`content:` URI。响应的应用还会给你的应用授予临时权限，来读取联系人信息，尽管你的应用没有包含[READ_CONTACTS](http://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS)权限。

**Action**
[ACTION_PICK](http://developer.android.com/reference/android/content/Intent.html#ACTION_PICK)

**Data URI Scheme**
无

**MIME Type**
[CommonDataKinds.Phone.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.CommonDataKinds.Phone.html#CONTENT_TYPE)
从联系人信息中挑选电话号码。

[CommonDataKinds.Email.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.CommonDataKinds.Email.html#CONTENT_TYPE)
从联系人信息中挑选电子邮件地址。

[CommonDataKinds.StructuredPostal.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.CommonDataKinds.StructuredPostal.html#CONTENT_TYPE)
从联系人信息中挑选邮寄地址。

或者是[ContactsContract](http://developer.android.com/reference/android/provider/ContactsContract.html)里的其他`CONTENT_TYPE`值。

**intent事例：**
```java
static final int REQUEST_SELECT_PHONE_NUMBER = 1;

public void selectContact() {
    // 启动activity供用户从通讯录中选取一个电话号码
    Intent intent = new Intent(Intent.ACTION_PICK);
    intent.setType(CommonDataKinds.Phone.CONTENT_TYPE);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivityForResult(intent, REQUEST_SELECT_PHONE_NUMBER);
    }
}

protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == REQUEST_SELECT_PHONE_NUMBER && resultCode == RESULT_OK) {
        // 获取URI并在content provider中查询该电话号码
        Uri contactUri = data.getData();
        String[] projection = new String[]{CommonDataKinds.Phone.NUMBER};
        Cursor cursor = getContentResolver().query(contactUri, projection,
                null, null, null);
        // 如果返回的游标有效，获取电话号码
        if (cursor != null && cursor.moveToFirst()) {
            int numberIndex = cursor.getColumnIndex(CommonDataKinds.Phone.NUMBER);
            String number = cursor.getString(numberIndex);
            // 处理电话号码
            ...
        }
    }
}
```

### 查看联系人

想要显示一个已知联系人的详细信息，请使用[ACTION_VIEW ](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW) action并在intent data中使用`content:`URI指定联系人。

获取联系人URI的方法主要有两种：

* 使用[ACTION_PICK](http://developer.android.com/reference/android/content/Intent.html#ACTION_PICK)返回联系人URI，就像上一章节里展示的那样（不需要任何应用权限）。
* 直接访问包含所有联系人的列表，就像在[Retrieving a List of Contacts](http://developer.android.com/training/contacts-provider/retrieve-names.html)中介绍的那样（需要[READ_CONTACTS](http://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS)权限）。

**Action**
[ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW)

**Data URI Scheme**
`content:<URI>`

**MIME Type**
无。类型可以从联系人URI推测出来。

**intent事例：**
```java
public void viewContact(Uri contactUri) {
    Intent intent = new Intent(Intent.ACTION_VIEW, contactUri);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

### 编辑已有的联系人

想要编辑一个已有的联系人，请使用[ACTION_EDIT](http://developer.android.com/reference/android/content/Intent.html#ACTION_EDIT) action，在intent data中使用`content:` URI来指定联系人，并且使用[ContactsContract.Intents.Insert](http://developer.android.com/reference/android/provider/ContactsContract.Intents.Insert.html)里的常量在extra中指定所有已知的联系人信息。

获取联系人URI的方法主要有两种：

* 使用[ACTION_PICK](http://developer.android.com/reference/android/content/Intent.html#ACTION_PICK)返回联系人URI，就像上一章节里展示的那样（不需要任何应用权限）。
* 直接访问包含所有联系人的列表，就像在[Retrieving a List of Contacts](http://developer.android.com/training/contacts-provider/retrieve-names.html)中介绍的那样（需要[READ_CONTACTS](http://developer.android.com/reference/android/Manifest.permission.html#READ_CONTACTS)权限）。

**Action**
[ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW)

**Data URI Scheme**
`content:<URI>`

**MIME Type**
无。类型可以从联系人URI推测出来。

**Extras**
在[ContactsContract.Intents.Insert](http://developer.android.com/reference/android/provider/ContactsContract.Intents.Insert.html)中还定义了其他extra，可以用来填充联系人的详细信息。

**intent事例：**
```java
public void editContact(Uri contactUri, String email) {
    Intent intent = new Intent(Intent.ACTION_EDIT);
    intent.setData(contactUri);
    intent.putExtra(Intents.Insert.EMAIL, email);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

更多关于如何编辑联系人的信息，请参阅[Modifying Contacts Using Intents](http://developer.android.com/training/contacts-provider/modify-data.html)。

### 添加联系人

想要添加一个新的联系人，请使用[ACTION_INSERT](http://developer.android.com/reference/android/content/Intent.html#ACTION_INSERT) action，用[Contacts.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.Contacts.html#CONTENT_TYPE)指定MIME类型，并且使用[ContactsContract.Intents.Insert](http://developer.android.com/reference/android/provider/ContactsContract.Intents.Insert.html)里的常量在extra中指定所有已知的联系人信息。

**Action**
[ACTION_INSERT](http://developer.android.com/reference/android/content/Intent.html#ACTION_INSERT)

**Data URI Scheme**
无

**MIME Type**
[Contacts.CONTENT_TYPE](http://developer.android.com/reference/android/provider/ContactsContract.Contacts.html#CONTENT_TYPE)

**Extras**
在[ContactsContract.Intents.Insert](http://developer.android.com/reference/android/provider/ContactsContract.Intents.Insert.html)中定义了许多extra。

**intent事例：**
```java
public void insertContact(String name, String email) {
    Intent intent = new Intent(Intent.ACTION_INSERT);
    intent.setType(Contacts.CONTENT_TYPE);
    intent.putExtra(Intents.Insert.NAME, name);
    intent.putExtra(Intents.Insert.EMAIL, email);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

更多关于如何新建一个联系人的信息，请参阅[Modifying Contacts Using Intents](http://developer.android.com/training/contacts-provider/modify-data.html)。

## 电子邮件

### 编写可选附件的电子邮件

想要编写一封电子邮件，可以根据是否包含附件选择下面的action，并且在intent中使用下面列举的extra键指定电子邮件的详细信息，例如收件人和主题等。

**Action**
[ACTION_SENDTO](http://developer.android.com/reference/android/content/Intent.html#ACTION_SENDTO) （针对不带附件）或
[ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND) （针对带一个附件）或
[ACTION_SEND_MULTIPLE](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND_MULTIPLE) （针对带多个附件）

**Data URI Scheme**
无

**MIME Type**
[PLAIN_TEXT_TYPE](http://developer.android.com/reference/org/apache/http/protocol/HTTP.html#PLAIN_TEXT_TYPE) ("text/plain")
`"*\*"`

**Extras**
[Intent.EXTRA_EMAIL](http://developer.android.com/reference/android/content/Intent.html#EXTRA_EMAIL)
字符串数组，包含所有收件人的电子邮件地址。

[Intent.EXTRA_CC](http://developer.android.com/reference/android/content/Intent.html#EXTRA_CC)
字符串数据，包含所有抄送收件人的电子邮件地址。

[http://developer.android.com/reference/android/content/Intent.html#EXTRA_BCC](http://developer.android.com/reference/android/content/Intent.html#EXTRA_BCC)
字符串数组，包含所有密送收件人的电子邮件地址。

[Intent.EXTRA_SUBJECT](http://developer.android.com/reference/android/content/Intent.html#EXTRA_SUBJECT)
电子邮件主题字符串。

[Intent.EXTRA_TEXT](http://developer.android.com/reference/android/content/Intent.html#EXTRA_TEXT)
电子邮件正文字符串。

[Intent.EXTRA_STREAM](http://developer.android.com/reference/android/content/Intent.html#EXTRA_STREAM)
指定附件的Uri。如果使用的是[ACTION_SEND_MULTIPLE](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND_MULTIPLE) action，那么这里应该使用包含多个[Uri](http://developer.android.com/reference/android/net/Uri.html)对象的[ArrayList](http://developer.android.com/reference/java/util/ArrayList.html)替代。

**intent事例：**
```java
public void composeEmail(String[] addresses, String subject, Uri attachment) {
    Intent intent = new Intent(Intent.ACTION_SEND);
    intent.setType("*/*");
    intent.putExtra(Intent.EXTRA_EMAIL, addresses);
    intent.putExtra(Intent.EXTRA_SUBJECT, subject);
    intent.putExtra(Intent.EXTRA_STREAM, attachment);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

如果想要确保intent只会被电子邮件应用处理（而不是其他文本消息应用或社交应用），那么请使用[ACTION_SENDTO](http://developer.android.com/reference/android/content/Intent.html#ACTION_SENDTO) action并且把data scheme设置为`"mailto:”`。例如：

```java
public void composeEmail(String[] addresses, String subject) {
    Intent intent = new Intent(Intent.ACTION_SENDTO);
    intent.setData(Uri.parse("mailto:")); // 只有电子邮件应用可以处理
    intent.putExtra(Intent.EXTRA_EMAIL, addresses);
    intent.putExtra(Intent.EXTRA_SUBJECT, subject);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
        <data android:type="*/*" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
    <intent-filter>
        <action android:name="android.intent.action.SENDTO" />
        <data android:scheme="mailto" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

## 文件存储

### 获取指定类型的文件

想要请求用户选择某个文件，例如文档或图片，然后把引用返回给应用，请使用[ACTION_GET_CONTENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_GET_CONTENT) action并指定期望的MIME类型。返回给应用的文件引用对activity当前的生命周期来说是瞬时的，所以如果想要晚点再访问的话，必须把它复制到一个拷贝文件中。这个intent也允许用户在当前进程中创建新的文件（例如，用户可以不选取已存在的照片，而是使用相机拍照）。

在回传给[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))回调方法中的结果Intent中，包含指向文件的URI数据。该URI可能是任何类型的，例如`http:` URI，`file:` URI或`content:` URI。然而，如果想把可选文件限制为只能通过content provider（一种`content: `URI）访问，并且可以作为文件流给[openFileDescriptor()](http://developer.android.com/reference/android/content/ContentResolver.html#openFileDescriptor(android.net.Uri,%20java.lang.String))使用，那么应该把[CATEGORY_OPENABLE](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_OPENABLE)添加到intent中。

在Android 4.3（API级别18）和更高版本的平台上，还可以通过把[EXTRA_ALLOW_MULTIPLE](http://developer.android.com/reference/android/content/Intent.html#EXTRA_ALLOW_MULTIPLE)添加到intent并设置为true来让用户可以选择多个文件。然后你可以在[getClipData()](http://developer.android.com/reference/android/content/Intent.html#getClipData())方法返回的[ClipData](http://developer.android.com/reference/android/content/ClipData.html)对象中访问每个被选中的文件。

**Action**
[ACTION_GET_CONTENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_GET_CONTENT)

**Data URI Scheme**
无

**MIME Type**
MIME类型应该与用户选择的文件类型一致。

**Extras**
[EXTRA_ALLOW_MULTIPLE](http://developer.android.com/reference/android/content/Intent.html#EXTRA_ALLOW_MULTIPLE)
布尔值，用来声明用户能否同时选择多个文件。

[EXTRA_LOCAL_ONLY](http://developer.android.com/reference/android/content/Intent.html#EXTRA_LOCAL_ONLY)
布尔值，用来声明返回的文件是否必须能从设备直接访问，而不是需要从远程服务器上下载。

**Category **(可选)
[CATEGORY_OPENABLE](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_OPENABLE)
只返回“可打开的”文件，也就是可以被[openFileDescriptor()](http://developer.android.com/reference/android/content/ContentResolver.html#openFileDescriptor(android.net.Uri,%20java.lang.String))当做文件流使用的。

**获取照片的intent事例：**
```java
static final int REQUEST_IMAGE_GET = 1;

public void selectImage() {
    Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
    intent.setType("image/*");
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivityForResult(intent, REQUEST_IMAGE_GET);
    }
}

protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == REQUEST_IMAGE_GET && resultCode == RESULT_OK) {
        Bitmap thumbnail = data.getParcelable("data");
        Uri fullPhotoUri = data.getData();
        // 处理保存在ullPhotoUri中的图片信息
        ...
    }
}
```

**返回照片的intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.GET_CONTENT" />
        <data android:type="image/*" />
        <category android:name="android.intent.category.DEFAULT" />
        <!-- OPENABLE category声明了它返回的文件是可以从content provider中访问的，支持OpenableColumns
             和ContentResolver.openFileDescriptor() -->
        <category android:name="android.intent.category.OPENABLE" />
    </intent-filter>
</activity>
```

### 打开指定类型的文件

当运行在Android 4.4或更高版本的平台上时，可以不像前面打开文件那样必须在应用中复制一份文件拷贝（通过使用[ACTION_GET_CONTENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_GET_CONTENT) action），而是可以请求打开其他应用管理的文件，只要使用[ACTION_OPEN_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_OPEN_DOCUMENT) action并指定MIME类型即可。同样可以使用[ACTION_CREATE_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_CREATE_DOCUMENT) action让用户创建一个你的应用可编写的新的文档。例如，[ACTION_CREATE_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_CREATE_DOCUMENT) intent不是从已存在的PDF文档中选取，而是让用户选择他们想在哪里创建新的文档（在其他管理文档存储的应用中），然后你的应用就会接收到URI位置，它描述了你可以编写新文档的位置。

然而通过[ACTION_GET_CONTENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_GET_CONTENT) action回传的[onActivityResult()](http://developer.android.com/reference/android/app/Activity.html#onActivityResult(int,%20int,%20android.content.Intent))方法的intent中，可能返回一个是任何类型的URI，但是通过[ACTION_OPEN_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_OPEN_DOCUMENT)和[ACTION_CREATE_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_CREATE_DOCUMENT)返回的结果intent却总是指定了被选中的文件的格式，`content: `URI，可以通过[DocumentsProvider](http://developer.android.com/reference/android/provider/DocumentsProvider.html)来回溯。使用[openFileDescriptor()](http://developer.android.com/reference/android/content/ContentResolver.html#openFileDescriptor(android.net.Uri,%20java.lang.String))打开文件，使用[DocumentsContract.Document](http://developer.android.com/reference/android/provider/DocumentsContract.Document.html)里的列查询它的详细信息。

返回的URI给应用授予了长时间读取该文件的权限（也可能是编写权限）。所以当想读取一个已存在的文件并且不想在应用中复制一份拷贝时，或者想在某个地方打开并编辑一个文件时，[ACTION_OPEN_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_OPEN_DOCUMENT) action更有用（而不是使用[ACTION_GET_CONTENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_GET_CONTENT)）。

通过在intent中添加[EXTRA_ALLOW_MULTIPLE](http://developer.android.com/reference/android/content/Intent.html#EXTRA_ALLOW_MULTIPLE)并赋值true，就能让用户选择多个文件了。如果用户只选择了一项，那么可以直接从[getData()](http://developer.android.com/reference/android/content/Intent.html#getData())获取该项目。如果用户选中了多项，那么[getData()](http://developer.android.com/reference/android/content/Intent.html#getData())会返回null，必须通过[getClipData()](http://developer.android.com/reference/android/content/Intent.html#getClipData())获取[ClipData](http://developer.android.com/reference/android/content/ClipData.html)对象，然后从中获取每个项目。

> **注意**：intent必须指定MIME类型，必须声明[CATEGORY_OPENABLE](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_OPENABLE) category。如果合适的话，可以把MIME类型数组指定给[EXTRA_MIME_TYPES](http://developer.android.com/reference/android/content/Intent.html#EXTRA_MIME_TYPES) extra来添加多个MIME类型，如果这么做的话，必须在[setType()](http://developer.android.com/reference/android/content/Intent.html#setType(java.lang.String))中设置主要的MIME类型为`"*/*”`。

**Action**
[ACTION_OPEN_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_OPEN_DOCUMENT) 或
[ACTION_CREATE_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_CREATE_DOCUMENT)

**Data URI Scheme**
无

**MIME Type**
MIME类型与用户选择的文件类型一致。

**Extras**
[EXTRA_MIME_TYPES](http://developer.android.com/reference/android/content/Intent.html#EXTRA_MIME_TYPES)
MIME类型数组，与应用请求的文件类型一致。当你使用这个extra时，必须在[setType()](http://developer.android.com/reference/android/content/Intent.html#setType(java.lang.String))中设置主要的MIME类型为`"*/*”`。

[EXTRA_ALLOW_MULTIPLE](http://developer.android.com/reference/android/content/Intent.html#EXTRA_ALLOW_MULTIPLE)
布尔值，用来指定用户是否能同时选取多个文件。

[EXTRA_TITLE](http://developer.android.com/reference/android/content/Intent.html#EXTRA_TITLE)
在使用[ACTION_CREATE_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_CREATE_DOCUMENT)时来指定初始化的文件名称。

[EXTRA_LOCAL_ONLY](http://developer.android.com/reference/android/content/Intent.html#EXTRA_LOCAL_ONLY)
布尔值，用来声明返回的文件是否必须从设备直接访问，而不是需要从远程服务器上下载。

**Category**
[CATEGORY_OPENABLE](http://developer.android.com/reference/android/content/Intent.html#CATEGORY_OPENABLE)
只返回“可打开的”文件，也就是可以被[openFileDescriptor()](http://developer.android.com/reference/android/content/ContentResolver.html#openFileDescriptor(android.net.Uri,%20java.lang.String))当做文件流使用的。

**获取照片的intent事例：**
```java
static final int REQUEST_IMAGE_OPEN = 1;

public void selectImage() {
    Intent intent = new Intent(Intent.ACTION_OPEN_DOCUMENT);
    intent.setType("image/*");
    intent.addCategory(Intent.CATEGORY_OPENABLE);
    // 只有系统能接收到这个ACTION_OPEN_DOCUMENT，所以不需要测试了。
    startActivityForResult(intent, REQUEST_IMAGE_OPEN);
}

protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == REQUEST_IMAGE_OPEN && resultCode == RESULT_OK) {
        Uri fullPhotoUri = data.getData();
        // 处理保存在fullPhotoUri中的未压缩照片。
        ...
    }
}
```

第三方应用实际上是不能响应使用[ACTION_OPEN_DOCUMENT](http://developer.android.com/reference/android/content/Intent.html#ACTION_OPEN_DOCUMENT) action的intent。系统会接收到这个intent，然后在一个统一的用户界面上显示来自各种应用的可用文件。

要在这个UI中显示你的应用的文件并允许其他应用打开它们，就[必须实现一个[DocumentsProvider](http://developer.android.com/reference/android/provider/DocumentsProvider.html)，然后包含一个名为[PROVIDER_INTERFACE](http://developer.android.com/reference/android/provider/DocumentsContract.html#PROVIDER_INTERFACE)(`"android.content.action.DOCUMENTS_PROVIDER"`)的intent filter。例如：

```xml
<provider ...
    android:grantUriPermissions="true"
    android:exported="true"
    android:permission="android.permission.MANAGE_DOCUMENTS">

    <intent-filter>
        <action android:name="android.content.action.DOCUMENTS_PROVIDER" />
    </intent-filter>
</provider>
```

更多关于如何让你的应用管理的文件可以从其他应用中打开的信息，请参阅[Storage Access Framework](http://developer.android.com/guide/topics/providers/document-provider.html)指南。 

## 健康

### 开始/结束骑单车

想要追踪骑单车，请使用[ACTION_TRACK](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_TRACK) action和`"vnd.google.fitness.activity/biking"` MIME类型，并且在开始时把[EXTRA_STATUS](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#EXTRA_STATUS) extra设置为[STATUS_ACTIVE](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#STATUS_ACTIVE)，在结束时设置为[STATUS_COMPLETED](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#STATUS_COMPLETED)。

**Action**
[ACTION_TRACK](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_TRACK)

**Data URI**
无

**MIME Type**
`"vnd.google.fitness.activity/biking"`

**intent事例：**
```java
public void startWorkout() {
    Intent intent = new Intent(FitnessIntents.ACTION_TRACK)
            .setType("vnd.google.fitness.activity/other")
            .putExtra(FitnessIntents.EXTRA_STATUS, FitnessIntents.STATUS_ACTIVE);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="vnd.google.fitness.TRACK" />
        <data android:mimeType="vnd.google.fitness.activity/other" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

### 显示心率

想要显示用户的心率，请使用[ACTION_VIEW](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_VIEW) action和`"vnd.google.fitness.data_type/com.google.heart_rate.bpm" `MIME 类型。

**Action**
[ACTION_VIEW](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_VIEW)

**Data URI**
无

**MIME Type**
`"vnd.google.fitness.data_type/com.google.heart_rate.bpm"`

**Extras**
无

**intent事例：**
```java
public void showHeartRate() {
    Intent intent = new Intent(FitnessIntents.ACTION_VIEW)
            .setType("vnd.google.fitness.data_type/com.google.heart_rate.bpm");
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="vnd.google.fitness.VIEW" />
        <data android:mimeType="vnd.google.fitness.data_type/com.google.heart_rate.bpm" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

### 显示步数

想要显示用户的步数，请使用[ACTION_VIEW](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_VIEW) action和`"vnd.google.fitness.data_type/com.google.step_count.cumulative"` MIME类型。

**Action**
[ACTION_VIEW](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_VIEW)

**Data URI**
无

**MIME Type**
`"vnd.google.fitness.data_type/com.google.step_count.cumulative"`

**Extras**
无

**intent事例：**
```java
public void showStepCount() {
    Intent intent = new Intent(FitnessIntents.ACTION_VIEW)
            .setType("vnd.google.fitness.data_type/com.google.step_count.cumulative");
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="vnd.google.fitness.VIEW" />
        <data android:mimeType="vnd.google.fitness.data_type/com.google.step_count.cumulative" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

## 本地action

### 叫车

想要叫车，请使用[ACTION_RESERVE_TAXI_RESERVATION](http://developer.android.com/com/google/android/gms/actions/ReserveIntents.html#ACTION_RESERVE_TAXI_RESERVATION) action。

> **注意**：在使用action前，应用必须要询问用户得到确定才行。

**Action**
[ACTION_RESERVE_TAXI_RESERVATION](http://developer.android.com/com/google/android/gms/actions/ReserveIntents.html#ACTION_RESERVE_TAXI_RESERVATION)

**Data URI**
无

**MIME Type**
无

**Extras**
无

**intent事例：**
```java
public void callCar() {
    Intent intent = new Intent(ReserveIntents.ACTION_RESERVE_TAXI_RESERVATION);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="com.google.android.gms.actions.RESERVE_TAXI_RESERVATION" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

## 地图

### 在地图上显示位置

想要打开地图，请使用[ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW) action并使用下面定义的一种scheme在intent data中指定位置信息。

**Action**
[ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW)

**Data URI Scheme**
`geo:latitude,longitude`
在地图上显示指定经纬度的的位置。
事例：`"geo:47.6,-122.3"`

`geo:latitude,longitude?z=zoom`
以某个缩放级别在地图上显示指定经纬度的位置。缩放级别1显示给定经纬度位于中心的整个地球，最高的（最近的）缩放级别是23。
事例：`"geo:47.6,-122.3?z=11"`

`geo:0,0?q=lat,lng(label)`
在指定经纬度的地图上显示字符串标签。
事例：`"geo:0,0?q=34.99,-106.61(Treasure)"`

`geo:0,0?q=my+street+address`
显示“my street address”的位置（可能是指定的地址，也可能是根据该位置的查询）。
事例：`"geo:0,0?q=1600+Amphitheatre+Parkway%2C+CA"`

> **注意**：所有传入`geo` URI中的字符串都必须进行编码。例如，字符串1st & Pike, Seattle应该变成1st%20%26%20Pike%2C%20Seattle。字符串中的空格可以用%20编码，也可以用加号（+）来替代。

**MIME Type**
无

**intent事例：**
```java
public void showMap(Uri geoLocation) {
    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.setData(geoLocation);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <data android:scheme="geo" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

## 音乐或视频

### 播放媒体文件

想要播放音乐文件，请使用[ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW) action并在intent data中指定文件的URI位置。

**Action**
[ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW)

**Data URI Scheme**
`file:<URI>`
`content:<URI>`
`http:<URL>`

**MIME Type**
`"audio/*"`
`"application/ogg"`
`"application/x-ogg"`
`"application/itunes"`
或应用可能用到的其他格式。

**intent事例：**
```java
public void playMedia(Uri file) {
    Intent intent = new Intent(Intent.ACTION_VIEW);
    intent.setData(file);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <data android:type="audio/*" />
        <data android:type="application/ogg" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

### 根据搜索请求播放音乐

想要根据搜索请求播放音乐，请使用[INTENT_ACTION_MEDIA_PLAY_FROM_SEARCH](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_MEDIA_PLAY_FROM_SEARCH) intent。应用在响应用户播放音乐的语音命令时可能会触发这个intent。接收该intent的应用会在它的播放清单中检索该曲目，在匹配到指定查询的内容时就会播放。

这个intent应该包含[EXTRA_MEDIA_FOCUS](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_FOCUS)字符串extra，它指定了检索模式。例如，检索模式可以指定为搜索艺术家姓名还是歌曲名。

**Action**
[INTENT_ACTION_MEDIA_PLAY_FROM_SEARCH](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_MEDIA_PLAY_FROM_SEARCH)

**Data URI Scheme**
无

**MIME Type**
无

**Extras**
[MediaStore.EXTRA_MEDIA_FOCUS](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_FOCUS)（必须）

指定检索模式（可以指定查询艺术家、专辑、歌曲或播放列表）。大部分检索模式都需要附加的extra。例如，如果用户感兴趣听某首指定的歌，那么这个intent可能需要三个附加extra：歌曲标题、艺术家和专辑。针对[EXTRA_MEDIA_FOCUS](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_FOCUS)里的值，该intent支持下列检索模式：

* 任意 - `"vnd.android.cursor.item/*"`

	播放任何音乐。接收的应用应该基于智能选择来播放一些音乐，比如用户最近听过的播放列表。
	
	附加extra：

	* [QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY) （必须） - 空字符串。这个extra是为了向后兼容， 所以需要提供该字段：那些不了解检索模式的已有应用可以把该intent当做非结构化检索。
	
* 非结构化 - `"vnd.android.cursor.item/*"`

	根据某个非结构化的检索模式查询播放指定的歌曲、专辑或类型。如果应用无法辨识用户想听的音乐类型，那就可以使用这种检索模式创建intent。应用应该尽可能多的指定检索模式。

	附加extra：

	* [QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY)（必须） - 字符串，包含艺术家、专辑、歌曲名称或风格的组合。

* 风格 - [Audio.Genres.ENTRY_CONTENT_TYPE](http://developer.android.com/reference/android/provider/MediaStore.Audio.Genres.html#ENTRY_CONTENT_TYPE)

	播放指定风格的音乐。

	附加extra：

	* `"android.intent.extra.genre”`（必须） - 风格。
	* [QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY)（必须） - 风格。这个extra是为了向后兼容， 所以需要提供该字段：那些不了解检索模式的已有应用可以把该intent当做非结构化检索。

* 艺术家 - Audio.Artists.ENTRY_CONTENT_TYPE

	播放指定艺术家的音乐。

	附加extras：

	* [EXTRA_MEDIA_ARTIST](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_ARTIST)（必须） - 艺术家。
	* `"android.intent.extra.genre"` - 风格。
	* [QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY)（必须） - 字符串，包含艺术家、专辑、歌曲名称或风格的组合。这个extra是为了向后兼容， 所以需要提供该字段：那些不了解检索模式的已有应用可以把该intent当做非结构化检索。

	* 专辑 - [Audio.Albums.ENTRY_CONTENT_TYPE](http://developer.android.com/reference/android/provider/MediaStore.Audio.Albums.html#ENTRY_CONTENT_TYPE)

* 播放指定专辑的音乐。

	附加extra：

	* [EXTRA_MEDIA_ALBUM](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_ALBUM) - 专辑。
	* [EXTRA_MEDIA_ARTIST](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_ARTIST) - 艺术家。
	* `"android.intent.extra.genre"` - 风格。
	* [EXTRA_MEDIA_TITLE](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_TITLE)（必须） - 歌曲名称。
	* [QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY)（必须） - 字符串，包含艺术家、专辑、歌曲名称或风格的组合。这个extra是为了向后兼容， 所以需要提供该字段：那些不了解检索模式的已有应用可以把该intent当做非结构化检索。

* 播放列表 - [Audio.Playlists.ENTRY_CONTENT_TYPE](http://developer.android.com/reference/android/provider/MediaStore.Audio.Playlists.html#ENTRY_CONTENT_TYPE)

	播放指定的播放列表或匹配了指定的附加extra的标准播放列表。

	附加extras：

	* [EXTRA_MEDIA_ALBUM](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_ALBUM) - 专辑。
	* [EXTRA_MEDIA_ARTIST](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_ARTIST) - 艺术家。
	* `"android.intent.extra.genre"` - 风格。
	* `"android.intent.extra.playlist"` - 播放列表。
	* [EXTRA_MEDIA_TITLE](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_TITLE)（必须） - 歌曲名称。
	* [QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY)（必须） - 字符串，包含艺术家、专辑、歌曲名称或风格的组合。这个extra是为了向后兼容， 所以需要提供该字段：那些不了解检索模式的已有应用可以把该intent当做非结构化检索。

**intent事例：**
如果用户想听指定艺术家的音乐，那么可能生成下面的intent来检索应用：

```java
public void playSearchArtist(String artist) {
    Intent intent = new Intent(MediaStore.INTENT_ACTION_MEDIA_PLAY_FROM_SEARCH);
    intent.putExtra(MediaStore.EXTRA_MEDIA_FOCUS,
                    MediaStore.Audio.Artists.ENTRY_CONTENT_TYPE);
    intent.putExtra(MediaStore.EXTRA_MEDIA_ARTIST, artist);
    intent.putExtra(SearchManager.QUERY, artist);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.media.action.MEDIA_PLAY_FROM_SEARCH" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

在处理这个intent时，activity应该检查传入的[Intent](http://developer.android.com/reference/android/content/Intent.html)中[EXTRA_MEDIA_FOCUS](http://developer.android.com/reference/android/provider/MediaStore.html#EXTRA_MEDIA_FOCUS) extra的值来确定检索模式。一旦activity辨别出检索模式了，就应该读取那个特定的检索模式对应的附加extra值。有了这些信息，应用就能在自己的曲库中播放那些匹配检索查询条件的歌曲了。例如：

```java
protected void onCreate(Bundle savedInstanceState) {
    ...
    Intent intent = this.getIntent();
    if (intent.getAction().compareTo(MediaStore.INTENT_ACTION_MEDIA_PLAY_FROM_SEARCH) == 0) {

        String mediaFocus = intent.getStringExtra(MediaStore.EXTRA_MEDIA_FOCUS);
        String query = intent.getStringExtra(SearchManager.QUERY);

        // 根据检索类型而异，下面有些extra可能不可用
        String album = intent.getStringExtra(MediaStore.EXTRA_MEDIA_ALBUM);
        String artist = intent.getStringExtra(MediaStore.EXTRA_MEDIA_ARTIST);
        String genre = intent.getStringExtra("android.intent.extra.genre");
        String playlist = intent.getStringExtra("android.intent.extra.playlist");
        String title = intent.getStringExtra(MediaStore.EXTRA_MEDIA_TITLE);

        // 确定检索模式并使用合适的extra
        if (mediaFocus == null) {
            // ‘非结构化’检索模式（向后兼容）
            playUnstructuredSearch(query);
        } else if (mediaFocus.compareTo("vnd.android.cursor.item/*") == 0) {
            if (query.isEmpty()) {
                // ‘任意’检索模式
                playResumeLastPlaylist();
            } else {
                // ‘非结构化’检索模式
                playUnstructuredSearch(query);
            }
        } else if (mediaFocus.compareTo(MediaStore.Audio.Genres.ENTRY_CONTENT_TYPE) == 0) {
            // ‘风格’检索模式
            playGenre(genre);
        } else if (mediaFocus.compareTo(MediaStore.Audio.Artists.ENTRY_CONTENT_TYPE) == 0) {
            // ‘艺术家’检索模式
            playArtist(artist, genre);
        } else if (mediaFocus.compareTo(MediaStore.Audio.Albums.ENTRY_CONTENT_TYPE) == 0) {
            // ‘专辑’检索模式
            playAlbum(album, artist);
        } else if (mediaFocus.compareTo("vnd.android.cursor.item/audio") == 0) {
            // ‘歌曲’检索模式
            playSong(album, artist, genre, title);
        } else if (mediaFocus.compareTo(MediaStore.Audio.Playlists.ENTRY_CONTENT_TYPE) == 0) {
            // ‘播放列表’检索模式
            playPlaylist(album, artist, genre, playlist, title);
        }
    }
}
```

## 电话

### 拨打电话

想要打开电话应用拨号，请使用[ACTION_DIAL](http://developer.android.com/reference/android/content/Intent.html#ACTION_DIAL) action，并使用下面定义的URI scheme指定电话号码。在电话应用打开时，它已经显示了该电话号码，但是用户必须按下拨号键才会开始拨号。

要想直接拨打电话，请使用[ACTION_CALL](http://developer.android.com/reference/android/content/Intent.html#ACTION_CALL) action，并使用下面定义的URI scheme指定电话号码。 当电话应用打开时，它就直接拨号了，用户不需要再去按拨号键了。

[ACTION_CALL](http://developer.android.com/reference/android/content/Intent.html#ACTION_CALL) action需要在manifest文件中添加`CALL_PHONE`权限：

> `<uses-permission android:name="android.permission.CALL_PHONE" />`

**Action**
* [ACTION_DIAL](http://developer.android.com/reference/android/content/Intent.html#ACTION_DIAL) - 打开拨号或电话应用。
* [ACTION_CALL](http://developer.android.com/reference/android/content/Intent.html#ACTION_CALL) - 直接拨号（需要`CALL_PHONE`权限）

**Data URI Scheme**
* `tel:<phone-number>`
* `voicemail:<phone-number>`

**MIME Type**
无

合法的电话号码是像[the IETF RFC 3966](http://tools.ietf.org/html/rfc3966)中定义的那样。下面列举一些合法的事例：
* `tel:2125551212`
* `tel:(212) 555 1212`

电话拨号应用擅长处理各种规范的格式，例如电话号码。所以在[Uri.parse()](http://developer.android.com/reference/android/net/Uri.html#parse(java.lang.String))方法中并没有要求使用严格定义的scheme。然而，如果你没有试过某个scheme或者不确定它能否被处理，请换用[Uri.fromParts()](http://developer.android.com/reference/android/net/Uri.html#fromParts(java.lang.String,%20java.lang.String,%20java.lang.String))。

**intent事例：**
```java
public void dialPhoneNumber(String phoneNumber) {
    Intent intent = new Intent(Intent.ACTION_DIAL);
    intent.setData(Uri.parse("tel:" + phoneNumber));
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

## 搜索

### 使用指定应用来搜索

想要在应用的上下文里支持搜索，请像下面的intent filter事例那样在你的应用中使用`SEARCH_ACTION` action声明intent filter。

**Action**
`"com.google.android.gms.actions.SEARCH_ACTION"`
支持从Google Now的搜索查询。

**Extras**
[QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY)
字符串，包含搜索查询的信息。

**intent filter事例：**
```xml
<activity android:name=".SearchActivity">
    <intent-filter>
        <action android:name="com.google.android.gms.actions.SEARCH_ACTION"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
```

### 执行网络搜索

想要使用网络搜索，请使用[ACTION_WEB_SEARCH](http://developer.android.com/reference/android/content/Intent.html#ACTION_WEB_SEARCH) action，并在[SearchManager.QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY) extra中指定要搜索的字符串。

**Action**
[ACTION_WEB_SEARCH](http://developer.android.com/reference/android/content/Intent.html#ACTION_WEB_SEARCH)

**Data URI Scheme**
无

**MIME Type**
无

**Extras**
[SearchManager.QUERY](http://developer.android.com/reference/android/app/SearchManager.html#QUERY)
要搜索的字符串。

**intent事例：**
```java
public void searchWeb(String query) {
    Intent intent = new Intent(Intent.ACTION_SEARCH);
    intent.putExtra(SearchManager.QUERY, query);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

## 设置

### 打开设置中的某个部分

当应用需要打开系统设置中的窗口让用户修改时，请在intent action中使用对应的action名称打开设置窗口。

**Action**
[ACTION_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_SETTINGS)
[ACTION_WIRELESS_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_WIRELESS_SETTINGS)
[ACTION_AIRPLANE_MODE_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_AIRPLANE_MODE_SETTINGS)
[ACTION_WIFI_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_WIFI_SETTINGS)
[ACTION_APN_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_APN_SETTINGS)
[ACTION_BLUETOOTH_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_BLUETOOTH_SETTINGS)
[ACTION_DATE_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_DATE_SETTINGS)
[ACTION_LOCALE_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_LOCALE_SETTINGS)
[ACTION_INPUT_METHOD_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_INPUT_METHOD_SETTINGS)
[ACTION_DISPLAY_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_DISPLAY_SETTINGS)
[ACTION_SECURITY_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_SECURITY_SETTINGS)
[ACTION_LOCATION_SOURCE_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_LOCATION_SOURCE_SETTINGS)
[ACTION_INTERNAL_STORAGE_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_INTERNAL_STORAGE_SETTINGS)
[ACTION_MEMORY_CARD_SETTINGS](http://developer.android.com/reference/android/provider/Settings.html#ACTION_MEMORY_CARD_SETTINGS)

请参阅[Settings](http://developer.android.com/reference/android/provider/Settings.html)文档了解更多可用的设置窗口。

**Data URI Scheme**
无

**MIME Type**
无

**intent事例：**
```java
public void openWifiSettings() {
    Intent intent = new Intent(Intent.ACTION_WIFI_SETTINGS);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

## 文本消息

### 编写带附件的SMS/MMS消息

想要编写一条SMS或MMS文本消息，请使用下面其中一种intent action，并使用下面列举的extra键来指定消息细节，例如电话号码、主题、消息内容等。

**Action**
[ACTION_SENDTO](http://developer.android.com/reference/android/content/Intent.html#ACTION_SENDTO)或
[ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND) 或
[ACTION_SEND_MULTIPLE](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND_MULTIPLE)

**Data URI Scheme**
`sms:<phone_number>`
`smsto:<phone_number>`
`mms:<phone_number>`
`mmsto:<phone_number>`

这些scheme可以用同样的方式处理。

**MIME Type**
[PLAIN_TEXT_TYPE](http://developer.android.com/reference/org/apache/http/protocol/HTTP.html#PLAIN_TEXT_TYPE) (`"text/plain"`)
`"image/*"`
`"video/*"`

**Extras**
`"subject"`
字符串，用做消息主题（通常仅用于MMS）。

`"sms_body"`
字符串，用做文本消息。

[EXTRA_STREAM](http://developer.android.com/reference/android/content/Intent.html#EXTRA_STREAM)
一个指向图片或视频附件的Uri。如果使用的是[ACTION_SEND_MULTIPLE](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND_MULTIPLE) action，那么这个extra应该是一个由指向图片/视频的[Uri](http://developer.android.com/reference/android/net/Uri.html)组成的[ArrayList](http://developer.android.com/reference/java/util/ArrayList.html)。

**intent事例：**
```java
public void composeMmsMessage(String message, Uri attachment) {
    Intent intent = new Intent(Intent.ACTION_SENDTO);
    intent.setType(HTTP.PLAIN_TEXT_TYPE);
    intent.putExtra("sms_body", message);
    intent.putExtra(Intent.EXTRA_STREAM, attachment);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

如果想确保intent只能被文本消息应用处理（而不是其他的邮件或社交应用），那么请使用[ACTION_SENDTO](http://developer.android.com/reference/android/content/Intent.html#ACTION_SENDTO) action，并包含`"smsto:"` data scheme。例如：

```java
public void composeMmsMessage(String message, Uri attachment) {
    Intent intent = new Intent(Intent.ACTION_SEND);
    intent.setData(Uri.parse("smsto:"));  // 这样确保只有SMS应用可以响应
    intent.putExtra("sms_body", message);
    intent.putExtra(Intent.EXTRA_STREAM, attachment);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
        <data android:type="text/plain" />
        <data android:type="image/*" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

> **注意**：如果你正在开发一款SMS/MMS消息应用，为了能在Android 4.4或更高版本的平台上作为系统的默认SMS应用，必须实现针对许多额外action的intent filter。更多信息，请参阅[Telephony](http://developer.android.com/reference/android/provider/Telephony.html)。

## 网络浏览器

### 加载网络URL

想要打开网络页面，请使用[ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW) action并在intent data中指定网络URL。 

**Action**
[ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW)

**Data URI Scheme**
`http:<URL>`
`https:<URL>`

**MIME Type**
[PLAIN_TEXT_TYPE](http://developer.android.com/reference/org/apache/http/protocol/HTTP.html#PLAIN_TEXT_TYPE) (`"text/plain"`)
`"text/html"`
`"application/xhtml+xml"`
`"application/vnd.wap.xhtml+xml"`

**intent事例：**
```java
public void openWebPage(String url) {
    Uri webpage = Uri.parse(url);
    Intent intent = new Intent(Intent.ACTION_VIEW, webpage);
    if (intent.resolveActivity(getPackageManager()) != null) {
        startActivity(intent);
    }
}
```

**intent filter事例：**
```xml
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <!-- 如果想要应用只响应自己域名的URL，只需要把这个域名属性包含进来就可以了。 -->
        <data android:scheme="http" android:host="www.example.com" />
        <category android:name="android.intent.category.DEFAULT" />
        <!-- 打开从链接打开网络页面的话，这个BROWSABLE category是必须的。 -->
        <category android:name="android.intent.category.BROWSABLE" />
    </intent-filter>
</activity>
```

> **提示**：如果你的Android应用提供的功能和你的网站差不多，请为指向你网站的URL指定一个intent filter。然后，如果用户安装了你的应用，那么在邮件或其他网页中打开指向你网站站点的超链接时，会直接打开你的Android应用，而不是你的网页。

## 使用Android Debug Bridge验证intent

要验证应用能否响应那些你想要支持的intent，可以使用adb工具来发出特定的intent：

1. 把Android设备设置为开发者模式（[development](http://developer.android.com/tools/device.html#setting-up)），或使用虚拟设备（[virtual device]()）。
2. 安装应用，该版本可以处理你想要支持的intent。
3. 使用`adb`发出intent：
 `adb shell am start -a <ACTION> -t <MIME_TYPE> -d <DATA> \
  -e <EXTRA_NAME> <EXTRA_VALUE> -n <ACTIVITY>`
  
  例如：
  `adb shell am start -a android.intent.action.DIAL \
  -d tel:555-5555 -n org.example.MyApp/.MyActivity`
4. 如果你已经定义了必须的intent filter，那么应用应该就能处理到该intent了。

更多信息，请参阅[Using activity manager (am)](http://developer.android.com/tools/help/adb.html#am)。

## Google Now发出的intent

[Google Now](http://www.google.com/landing/now/)可以辨识出许多语音命令并为它们发出对应的intent。像这样，如果应用声明了相应的intent filter，用户就能通过Google Now语音命令来启动应用了。例如，如果应用可以设置闹钟（[set an alarm](http://developer.android.com/guide/components/intents-common.html#CreateAlarm)）并且在manifest文件中添加了相应的intent filter，那么当用户需要设置闹钟时，Google Now就能让用户选择你的应用了，如图1所示。

![google-action_2x](https://raw.githubusercontent.com/JiongBull/Android-API-Guide/master/images/google-action_2x.png)
图1. Google Now让用户从已安装的支持给定action的应用里挑选。

Google Now识别针对表1所列举的action的语音命令。更多关于每种intent filter声明的信息，请点击相关action描述。

表1. Google Now可以识别的语音命令（Google Search应用v3.6）.

| 类别 | 详情和事例 | Action名称 |
| :------ | :------ | :------|
| 闹钟 | [Set alarm](http://developer.android.com/guide/components/intents-common.html#CreateAlarm)（设置闹钟） | [AlarmClock.ACTION_SET_ALARM](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SET_ALARM) |
|  | [Set timer](http://developer.android.com/guide/components/intents-common.html#CreateTimer)（设置时间） | [AlarmClock.ACTION_SET_TIMER](http://developer.android.com/reference/android/provider/AlarmClock.html#ACTION_SET_TIMER) |
| 联系 | [Call a number](http://developer.android.com/guide/components/intents-common.html#DialPhone)（拨号） | [Intent.ACTION_CALL](http://developer.android.com/reference/android/content/Intent.html#ACTION_CALL) |
| 健康 | [Start/stop a bike ride](http://developer.android.com/guide/components/intents-common.html#TrackRide)（开始/结束骑单车） | [FitnessIntents.ACTION_TRACK](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_TRACK) |
|  | [Start/stop a run](http://developer.android.com/guide/components/intents-common.html#TrackRun)（开始/结束跑步） | [FitnessIntents.ACTION_TRACK](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_TRACK) |
|  | [Start/stop a workout](http://developer.android.com/guide/components/intents-common.html#TrackWorkout)（开始/结束锻炼） | [FitnessIntents.ACTION_TRACK](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_TRACK) |
|  | [Show heart rate](http://developer.android.com/guide/components/intents-common.html#ShowHeartRate)（显示心率） | [FitnessIntents.ACTION_VIEW](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_VIEW) |
|  | [Show step count](http://developer.android.com/guide/components/intents-common.html#ShowStepCount)（显示步数） | [FitnessIntents.ACTION_VIEW](http://developer.android.com/reference/com/google/android/gms/fitness/FitnessIntents.html#ACTION_VIEW) |
| 本地 | [Book a car](http://developer.android.com/guide/components/intents-common.html#CallCar)（叫车） | [ReserveIntents.ACTION_RESERVE_TAXI_RESERVATION](http://developer.android.com/reference/com/google/android/gms/actions/ReserveIntents.html#ACTION_RESERVE_TAXI_RESERVATION) |
| 媒体 | [Play music from search](http://developer.android.com/guide/components/intents-common.html#PlaySearch)（搜索并播放音乐） | [MediaStore.INTENT_ACTION_MEDIA_PLAY_FROM_SEARCH](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_MEDIA_PLAY_FROM_SEARCH) |
|  | [Take a picture](http://developer.android.com/guide/components/intents-common.html#CameraStill)（拍照） | [MediaStore.INTENT_ACTION_STILL_IMAGE_CAMERA](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_STILL_IMAGE_CAMERA) |
|  | [Record a video](http://developer.android.com/guide/components/intents-common.html#CameraVideo)（录视频） | [MediaStore.INTENT_ACTION_VIDEO_CAMERA](http://developer.android.com/reference/android/provider/MediaStore.html#INTENT_ACTION_VIDEO_CAMERA) |
| 搜索 | [Search using a specific app](http://developer.android.com/guide/components/intents-common.html#SearchOnApp)（使用指定应用搜索） | `"com.google.android.gms.actions.SEARCH_ACTION"` |
| 网络浏览器 | [Open URL](http://developer.android.com/guide/components/intents-common.html#ViewUrl)（打开URL） | [Intent.ACTION_VIEW](http://developer.android.com/reference/android/content/Intent.html#ACTION_VIEW) |
