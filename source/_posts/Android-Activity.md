---
title: Android Activity

date: 2019/3/12 21:24 

tags:
- Android

categories:
- 笔记

---

Android学习笔记第一章，记录Activity的使用。重点是显式和隐式Intent的使用、返回栈、活动状态以及活动的启动模式。

<!--more-->

---

# AndroidManifest 文件 #
所有的活动都要在AndroidManifest.xml的`<activity>`标签中对活动进行注册才能生效，实际上当我们新建Activity时Android Studio替我们完成了这一步骤。不过，仅仅是这样注册了活动，我们还要为程序配置主活动让程序知道从哪个活动开始才行，就是在<activity>标签的内部加入`<intent-filter>`标签，并在这个标签里添加`<action android:name="android.intent.action.MAIN"/>`和`<category android:name="android.intent.category.LAUNCHER" />`这两句声明即可。


# 在活动中使用Toast #
Toast的用法非常简单，通过静态方法`makeText()`创建出一个Toast对象，然后调用`show()`将Toast显示出来就可以了。这里需要注意的是，`makeText()`方法需要传入3个参数。第一个参数是`Context`，也就是Toast要求的上下文，由于活动本身就是一个`Context`对象，因此这里直接传入`FirstActivity.this`即可。第二个参数是Toast显示的文本内容，第三个参数是Toast显示的时长，有两个内置常量可以选择`Toast.LENGTH_SHORT`和`Toast.LENGTH_LONG`。

例如

```java
Toast.makeText(Activity.this, "You used Toast",Toast.LENGTH_SHORT).show();
```

# 在活动中使用menu #
在res目录下新建一个menu文件夹并在menu文件夹中添加一个菜单文件*(Menu resource file)*然后在菜单文件中添加代码

```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">
	<item
		android:id="@+id/first_item"
		android:title="First"/>
	<item
		android:id="@+id/second_item"
		android:title="Second"/>
</menu>
```

其中`<item>`标签就是用来创建具体的某一个菜单项，然后通过`android:id`给这个菜单项指定一个唯一的标识符，通过`android:title`给这个菜单项指定一个名称。

接着在活动中重写`onCreateOptionsMenu()`方法：

```java
public boolean onCreateOptionsMenu(Menu menu) {
	getMenuInflater().inflate(R.menu.main, menu);
	return true;
}
```

通过`getMenuInflater()`方法能够得到`MenuInflater`对象，再调用它的inflate()方法就可以给当前活动创建菜单了。`inflate()`方法接收两个参数，第一个参数用于指定我们通过哪一个资源文件来创建菜单，这里当然传入`R.menu.main`。第二个参数用于指定我们的菜单项将添加到哪一个`Menu`对象当中，这里直接使用`onCreateOptionsMenu()`方法中传入的`menu`参数。然后给这个方法返回`true`，表示允许创建的菜单显示出来，如果返回了`false`，创建的菜单将无法显示

然后，为了让菜单可用，我们还需重写`onOptionsItemSelected()`方法，通过调用`item.getItemId()`来判断用户点击了哪个菜单项，然后给每个菜单项加入自己的逻辑处理。例如：

```java
public boolean onOptionsItemSelected(MenuItem item) {
	switch (item.getItemId()) {
	case R.id.first_item:
		//点击了第一个菜单项后的逻辑操作
		Toast.makeText(this, "You clicked First", Toast.LENGTH_SHORT).show();
		break;
	case R.id.second_item:
		//点击了第二个菜单项的逻辑操作
		Toast.makeText(this, "You clicked Second", Toast.LENGTH_SHORT).show();
		break;
	default:
	}
	return true;
}
```



# 销毁一个活动 #
在当前活动中调用`finish()`方法

# Intent的使用 #
Intent是**Android程序中各组件之间进行交互的一种重要方式**，它不仅可以指明当前组件想要执行的动作，还可以在不同组件之间传递数据。Intent一般可被用于启动活动、启动服务以及发送广播等场景。

## 显性Intent ##
Intent有多个构造函数的重构，其中一个是`Intent(Context packageContext, Class<?> cls)`。这个构造函数接收两个参数，第一个参数`Context`要求提供一个启动活动的上下文，第二个参数`Class`则是指定想要启动的目标活动，通过这个构造函数就可以构建出`Intent`的“意图”。然后使用Activity类中的`startActivity()`方法将构建好的Intent传入就可以启动目标代码了。

```java
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
startActivity(intent);
```

可见，显性Intent意图非常明显，如上就是在`FirstActivity`中打开`SecondActivity`。

## 隐式Intent ##

隐式Intent并不明确指出我们想要启动哪一个活动，而是指定了一系列更为抽象的`action`和`category`等信息，然后交由系统去分析这个Intent，并帮我们找出合适的活动去启动。

为了让系统能找到合适的活动，我们需要在AndroidManifest.xml中通过在`<activity>`标签下配置`<intent-filter>`的内容，可以指定当前活动能够响应的`action`和`category`。只有`<action>`和`<category>`中的内容同时能够匹配上Intent中指定的`action`和`category`时，这个活动才能响应该Intent。

例如：

当我们启动一个Intent，`action`为`com.example.activitytest.ACTION_START`，而`category`为`com.example.activitytest.MY_CATEGORY`

```java
Intent intent = new Intent("com.example.activitytest.ACTION_START");
intent.addCategory("com.example.activitytest.MY_CATEGORY");
startActivity(intent);
```

此时系统就会在AndroidManifest.xml中找一个合适的活动

```xml
<activity android:name=".SecondActivity" >
	<intent-filter>
		<action android:name="com.example.activitytest.ACTION_START" />
		<category android:name="android.intent.category.DEFAULT" />
		<category android:name="com.example.activitytest.MY_CATEGORY"/>
	</intent-filter>
</activity>
```

这里要注意的时一个活动能有多个category但只能有一个action。

# 向下一个活动传递数据 #

## 在FirstActivity中传出 ##
在`startActivity(intent);`之前加入`intent.putExtra("extra_data", data);`，即使用putExtra()方法可以使我们向下一个活动传递数据，注意这里`putExtra()`方法接收两个参数，第一个参数是键，用于后面从Intent中取值，第二个参数才是真正要传递的数据。

## 在SecondActivity中接收 ##
首先可以通过`getIntent()`方法获取到用于启动`SecondActivity`的Intent，然后调用`getStringExtra()`方法，传入相应的键值，就可以得到传递的数据了。

# 返回数据给上一个活动 #

以FirstActivity←SecondActivity为例

## FirstActivity ##
**使用`startActivityForResult()`方法启动活动**，这个方法期望在活动销毁的时候能够返回一个结果给上一个活动。`startActivityForResult()`方法接收两个参数，第一个参数还是Intent，第二个参数是请求码，用于在之后的回调中判断数据的来源。

**使用`onActivityResult()`方法处理接收到的数据**,`onActivityResult()`方法带有三个参数，第一个参数`requestCode`，即我们在启动活动时传入的请求码。第二个参数`resultCode`，即我们在返回数据时传入的处理结果。第三个参数`data`，即携带着返回数据的Intent。由于在一个活动中有可能调用`startActivityForResult()`方法去启动很多不同的活动，每一个活动返回的数据都会回调到`onActivityResult()`这个方法中，因此我们首先要做的就是通过检查`requestCode`的值来判断数据来源。确定数据是从`SecondActivity`返回的之后，我们再通过`resultCode`的值来判断处理结果是否成功。最后从`data`中取值并打印出来，这样就完成了向上一个活动返回数据的工作。

## SecondActivity ##
构建一个Intent，把要传递的数据放在Intent中，然后调用`setResult()`方法将数据传送回去。`setResult()`方法接收两个参数，第一个参数用于向上一个活动返回处理结果，一般只使用`RESULT_OK`或`RESULT_CANCELED`这两个值，第二个参数则把带有数据的Intent传递回去。若用户使用Back键回到上一个Activity时，若我们想要将数据传送回去，我们可以重写`onBackPressed()`方法,然后在这个方法里`setResult()`。

# 活动的生命周期 #
掌握活动的生命周期对任何Android开发者来说都**非常重要**，当你深入理解活动的生命周期之后，就可以写出更加连贯流畅的程序，并在如何合理管理应用资源方面发挥得游刃有余。你的应用程序将会拥有更好的用户体验。

## 返回栈 ##
Android是使用任务（Task）来管理活动的，一个任务就是一组存放在栈里的活动的集合，这个栈也被称作返回栈（Back Stack）。

![](http://qiniuyun.gtouyang.com/Activity%E8%BF%94%E5%9B%9E%E6%A0%88.jpg)

没当我们启动一个新的活动时，这个活动就会入栈，作为当前活动。而当我们销毁了一个活动时，这个活动就会在出栈，栈中的下一个活动重新变成栈顶活动，而系统总是显示处于栈顶的活动。


## 活动状态 ##
每个活动在其生命周期中最多可能会有4种状态。

1. 运行状态
2. 暂停状态
3. 停止状态
4. 销毁状态

![](http://qiniuyun.gtouyang.com/image/blog/chapter2Activity%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.jpg)


# 活动的启动模式 #
启动模式一共有4种，分别是standard、singleTop、singleTask和singleInstance，可以在`AndroidManifest.xml`中通过给`<activity>`标签指定`android:launchMode`属性来选择启动模式。

## standard ##
在standard模式（即默认情况）下，每当启动一个新的活动，它就会在返回栈中入栈，并处于栈顶的位置。对于使用standard模式的活动，系统不会在乎这个活动是否已经在返回栈中存在，每次启动都会创建该活动的一个新的实例。

![](http://qiniuyun.gtouyang.com/standard%E6%A8%A1%E5%BC%8F%E7%A4%BA%E6%84%8F%E5%9B%BE.jpg)

## singleTop ##
在singleTop模式下，在启动活动时如果发现返回栈的栈顶已经是该活动，则认为可以直接使用它，不会再创建新的活动实例。但如果该活动并没有处于栈顶的位置，还是可能会创建多个活动实例的。

![](http://qiniuyun.gtouyang.com/singleTop%E6%A8%A1%E5%BC%8F%E7%A4%BA%E6%84%8F%E5%9B%BE.jpg)

## singleTask ##
在singleTask模式下，每次启动该活动时系统首先会在返回栈中检查是否存在该活动的实例，如果发现已经存在则直接使用该实例，并把在这个活动之上的所有活动统统出栈，如果没有发现就会创建一个新的活动实例。

![](http://qiniuyun.gtouyang.com/singleTask%E6%A8%A1%E5%BC%8F%E7%A4%BA%E6%84%8F%E5%9B%BE.jpg)

##  singleInstance ##
在singleInstance模式下，活动会启用一个新的返回栈来管理这个活动。当我们希望我们程序中的一个活动可以与其他程序共享这个活动的实例时，我们可以使用singleInstance模式解决这个问题，在这种模式下会有一个单独的返回栈来管理这个活动，不管是哪个应用程序来访问这个活动，都共用的同一个返回栈。

![](http://qiniuyun.gtouyang.com/singleInstance%E6%A8%A1%E5%BC%8F%E7%A4%BA%E6%84%8F%E5%9B%BE.jpg)

# 引用 #
以上是我在郭霖所著的《第一行代码：Android(第2版)》中学习Android的学习笔记，部分内容和图片摘自书中，有兴趣的可以自行购买电子书或实体书查阅。
