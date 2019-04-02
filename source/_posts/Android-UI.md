---
title: Android-UI
date: 2019-03-28 21:09:45
categories:	笔记
tags: Android
---

Android学习笔记第二章，记录UI的使用。重点时自定义布局、自定义控件和RecyclerView的基本用法。

<!--more-->

---

# 常用控件 #

## TextView ##
用于显示文本信息，不能输入。

## Button ##
顾名思义，要注意的是Button里的文字默认是大写，可以在配置中修改。

## EditText ##
输入框，可以使用`android:hint`属性指定提示性的文本，这些文本会在输入任意内容后消失。`android:maxLines`属性可以用于限制输入框的大小，让他不会随着输入内容变多而变得过大。

## ImageView ##
是用于展示图片的一个控件，需要在res目录下的drawable目录或新建一个目录放入你需要的图片，然后再在控件中的`android:src`中指定，也可以调用`setImageResource()`方法更换图片。

## ProgressBar ##
进度条，可以通过`android:visibility`属性指定它的值来控制它的显示与否(所有Android控件都有这个属性)，属性值有：

1. Visible	可见
2. invisible 不可见，但仍然会占据位置，可以理解为变透明
3. gone 不可见且不占位置

## AlertDialog ##
弹出对话框，并屏蔽掉其他控件的交互能力。一般在java文件中使用，举例：

```java
AlertDialog.Builder dialog = newAlertDialog.Builder (MainActivity.this);
dialog.setTitle("This is Dialog");
dialog.setMessage("Something important.");
dialog.setCancelable(false);
dialog.setPositiveButton("OK", new DialogInterface.OnClickListener() {
    @Override
    public void onClick(DialogInterface dialog, int which) {}
});
dialog.setNegativeButton("Cancel", new DialogInterface.OnClickListener() {
	@Override
    public void onClick(DialogInterface dialog, int which) {}
});
dialog.show();
```

## ProgressDialog ##
和AlertDialog有点类似，都可以在界面上弹出一个对话框，都能够屏蔽掉其他控件的交互能力。不同的是，ProgressDialog会在对话框中显示一个进度条，一般用于表示当前操作比较耗时，让用户耐心地等待。用法和AlertDialog也比较相似。

# 4种基本布局 #

## LinearLayout ##
LinearLayout又称作线性布局，是一种非常常用的布局。正如它的名字所描述的一样，这个布局会将它所包含的控件在线性方向上依次排列。通过设置`android:orientation`属性修改排列方向。

## RelativeLayout ##
RelativeLayout又称作相对布局，也是一种非常常用的布局。和LinearLayout的排列规则不同，RelativeLayout显得更加随意一些，它可以通过相对定位的方式让控件出现在布局的任何位置。

## FrameLayout ##
FrameLayout又称作帧布局，它相比于前面两种布局就简单太多了，因此它的应用场景也少了很多。这种布局没有方便的定位方式，所有的控件都会默认摆放在布局的左上角，而且都会重合在一起。

## PercentFrameLayout和PercentRelativeLayout ##

PercentFrameLayout和PercentRelativeLayout是为FrameLayout和RelativeLayout进行了功能扩展的百分比布局。可以使用`app:layout_widthPercent`和`app:layout_heightPercent`来按百分比指定控件的宽高。

# 自定义控件 #

![](http://qiniuyun.gtouyang.com/%E5%B8%B8%E7%94%A8%E6%8E%A7%E4%BB%B6%E5%92%8C%E5%B8%83%E5%B1%80%E7%9A%84%E7%BB%A7%E6%89%BF%E7%BB%93%E6%9E%84.jpg)

我们所用的所有控件都是直接或间接继承自View的，所用的所有布局都是直接或间接继承自ViewGroup的。View是Android中最基本的一种UI组件，它可以在屏幕上绘制一块矩形区域，并能响应这块区域的各种事件，因此，我们使用的各种控件其实就是在View的基础之上又添加了各自特有的功能。而ViewGroup则是一种特殊的View，它可以包含很多子View和子ViewGroup，是一个用于放置控件和布局的容器。

## 引入布局 ##
在layout目录下新建一个布局xml文件，在里面完成自己想要的布局，放入想用的控件，然后再想在别的界面引入该布局时，只需`<include layout="@layout/你的布局名" />`。引入布局可以解决我们重复编写布局代码的问题。当我们想在多个页面中加入一个布局时，其实我们只需写一个布局文件，然后再别的页面include就好。

## 创建自定义控件 ##
在引入布局后，如果我们想要为布局中的控件(如Button)注册一个事件，我们要在每一个活动中重新注册，这会增加很多重复代码，我们可以通过创建自定义控件来解决。

我们可以新建一个布局继承自一个已有布局，然后在新布局中重载方法

```java
public class TitleLayout extends LinearLayout {

	public TitleLayout(Context context, AttributeSet attrs) {
    	super(context, attrs);
    	LayoutInflater.from(context).inflate(R.layout.title, this);
    	Button titleBack = (Button) findViewById(R.id.title_back);
    	Button titleEdit = (Button) findViewById(R.id.title_edit);
    	titleBack.setOnClickListener(new OnClickListener() {
        	@Override
        	public void onClick(View v) {
            	((Activity) getContext()).finish();
        	}
    	});
    	titleEdit.setOnClickListener(new OnClickListener() {
        	@Override
        	public void onClick(View v) {
            	Toast.makeText(getContext(), "You clicked Edit button",
                Toast.LENGTH_SHORT).show();
        	}
    	});
	}
}
```

然后在xml文件中使用

```xml
<com.example.uicustomviews.TitleLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

这样我们就可以不用重复为每个activity中的这个布局重新注册事件了。

# 最常用和最难用的控件——ListView #

建议直接不用，用后面的RecyclerView

## ListView的简单用法 ##

在xml文件中加入ListView控件

```xml
<ListView
    android:id="@+id/list_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

然后在java文件(控制当前活动的)中添加

```java
private String[] data = 一个字符串数组

ArrayAdapter<String> adapter = new ArrayAdapter<String>(MainActivity.this, android.R.layout.simple_list_item_1, data);

ListView listView = (ListView) findViewById(R.id.list_view);

listView.setAdapter(adapter);
```

## 定制ListView的界面 ##

新建一个实体类

```java
public class Fruit {

	private String name;

	private int imageId;

	public Fruit(String name, int imageId) {
    this.name = name;
    this.imageId = imageId;
	}

	public String getName() {
    	return name;
	}

	public int getImageId() {
    	return imageId;
	}

}
```

和一个自定义布局

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
	android:layout_width="match_parent"
	android:layout_height="wrap_content">

	<ImageView
    	android:id="@+id/fruit_image"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content" />

	<TextView
    	android:id="@+id/fruit_name"
    	android:layout_width="wrap_content"
    	android:layout_height="wrap_content"
    	android:layout_gravity="center_vertical"
    	android:layout_marginLeft="10dp" />

</LinearLayout>
```

然后再创建一个自定义适配器

```java
public class FruitAdapter extends ArrayAdapter<Fruit> {

	private int resourceId;

	public FruitAdapter(Context context, int textViewResourceId,List<Fruit> objects) {
    	super(context, textViewResourceId, objects);
    	resourceId = textViewResourceId;
	}

	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
    	Fruit fruit = getItem(position); // 获取当前项的Fruit实例
    	View view = LayoutInflater.from(getContext()).inflate(resourceId, parent,
        false);
    	ImageView fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
    	TextView fruitName = (TextView) view.findViewById(R.id.fruit_name);
    	fruitImage.setImageResource(fruit.getImageId());
    	fruitName.setText(fruit.getName());
    	return view;
	}

}
```

然后在Activity中使用

```java
public class MainActivity extends AppCompatActivity {

	private List<Fruit> fruitList = new ArrayList<>();

	@Override
	protected void onCreate(Bundle savedInstanceState) {
    	super.onCreate(savedInstanceState);
    	setContentView(R.layout.activity_main);
    	initFruits(); // 初始化水果数据
    	FruitAdapter adapter = new FruitAdapter(MainActivity.this,
        R.layout.fruit_item, fruitList);
    	ListView listView = (ListView) findViewById(R.id.list_view);
    	listView.setAdapter(adapter);
	}

	private void initFruits() {
    	for (int i = 0; i < 2; i++) {
        	Fruit apple = new Fruit("Apple", R.drawable.apple_pic);
       		fruitList.add(apple);
        	Fruit banana = new Fruit("Banana", R.drawable.banana_pic);
        	fruitList.add(banana);
        	Fruit orange = new Fruit("Orange", R.drawable.orange_pic);
        	fruitList.add(orange);
    	}
	}

}
```

## 提升ListView的运行效率 ##

修改适配器代码

```java
public class FruitAdapter extends ArrayAdapter<Fruit> {
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
    	Fruit fruit = getItem(position);
    	View view;
    	ViewHolder viewHolder;
    	if (convertView == null) {
        	view = LayoutInflater.from(getContext()).inflate(resourceId, parent,false);
        	viewHolder = new ViewHolder();
        	viewHolder.fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
        	viewHolder.fruitName = (TextView) view.findViewById (R.id.fruit_name);
        	view.setTag(viewHolder); // 将ViewHolder存储在View中
    	} else {
        	view = convertView;
        	viewHolder = (ViewHolder) view.getTag(); // 重新获取ViewHolder
    	}
    	viewHolder.fruitImage.setImageResource(fruit.getImageId());
    	viewHolder.fruitName.setText(fruit.getName());
    	return view;
	}

	class ViewHolder {

    	ImageView fruitImage;

    	TextView fruitName;

	}

}
```

`getView()`方法中还有一个`convertView`参数，这个参数用于将之前加载好的布局进行缓存，以便之后可以进行重用。我们在`getView()`方法中进行了判断，如果`convertView`为`null`，则使用`LayoutInflater`去加载布局，如果不为`null`则直接对`convertView`进行重用。并且新增了一个内部类`ViewHolder`，用于对控件的实例进行缓存。当`convertView`为`null`的时候，创建一个`ViewHolder`对象，并将控件的实例都存放在`ViewHolder`里，然后调用`View`的`setTag()`方法，将`ViewHolder`对象存储在`View`中。当convertView不为null的时候，则调用`View`的`getTag()`方法，把`ViewHolder`重新取出。

## ListView的点击事件 ##

在activity中使用`setOnItemClickListener()`方法为ListView注册了一个监听器，当用户点击了ListView中的任何一个子项时，就会回调`onItemClick()`方法。在这个方法中可以通过`position`参数判断出用户点击的是哪一个子项，然后再实现相应的逻辑操作。

#  更强大的滚动控件——RecyclerView #

Android提供了一个更强大的滚动控件——RecyclerView。它可以说是一个增强版的ListView，不仅可以轻松实现和ListView同样的效果，还优化了ListView中存在的各种不足之处。目前Android官方更加推荐使用RecyclerView，未来也会有更多的程序逐渐从ListView转向RecyclerView。

## RecyclerView的基本用法 ##
RecyclerView属于新增的控件，为了让RecyclerView在所有Android版本上都能使用，Android团队采取了同样的方式，将RecyclerView定义在了support库当中。因此，想要使用RecyclerView这个控件，首先需要在项目的build.gradle中添加相应的依赖库才行。

	dependencies {
		implementation fileTree(dir: 'libs', include: ['*.jar'])
		implementation 'com.android.support:appcompat-v7:28.0.0'
		implementation 'com.android.support:recyclerview-v7:28.0.0'
		implementation 'com.android.support.constraint:constraint-layout:1.1.3'
		testImplementation 'junit:junit:4.12'
		androidTestImplementation 'com.android.support.test:runner:1.0.2'
		androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
	}

添加完之后记得要点击一下Sync Now来进行同步。然后和ListView类似，在布局中加入RecyclerView控件。

```xml
<android.support.v7.widget.RecyclerView
    android:id="@+id/recycler_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```


新建一个适配器
	public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {

```java
	private List<Fruit> mFruitList;

	static class ViewHolder extends RecyclerView.ViewHolder {
    	ImageView fruitImage;
    	TextView fruitName;

    	public ViewHolder(View view) {
        	super(view);
        	fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
        	fruitName = (TextView) view.findViewById(R.id.fruit_name);
    	}
	}

	public FruitAdapter(List<Fruit> fruitList) {
    	mFruitList = fruitList;
	}

	@Override
	public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
   		View view = LayoutInflater.from(parent.getContext())
        .inflate(R.layout.fruit_item, parent, false);
    	ViewHolder holder = new ViewHolder(view);
    	return holder;
	}

	@Override
	public void onBindViewHolder(ViewHolder holder, int position) {
    	Fruit fruit = mFruitList.get(position);
    	holder.fruitImage.setImageResource(fruit.getImageId());
    	holder.fruitName.setText(fruit.getName());
	}

	@Override
	public int getItemCount() {
    	return mFruitList.size();
	}

}
```

这里我们首先定义了一个内部类`ViewHolder`，`ViewHolder`要继承自`RecyclerView.ViewHolder`。然后`ViewHolder`的构造函数中要传入一个`View`参数，这个参数通常就是`RecyclerView`子项的最外层布局，那么我们就可以通过`findViewById()`方法来获取到布局中的`ImageView`和`TextView`的实例了。
接着往下看，`FruitAdapter`中也有一个构造函数，这个方法用于把要展示的数据源传进来，并赋值给一个全局变量`mFruitList`，我们后续的操作都将在这个数据源的基础上进行。
继续往下看，由于`FruitAdapter`是继承自`RecyclerView.Adapter`的，那么就必须重写`onCreateViewHolder()`、`onBindViewHolder()`和`getItemCount()`这3个方法。`onCreateViewHolder()`方法是用于创建ViewHolder实例的，我们在这个方法中将`fruit_item`布局加载进来，然后创建一个`ViewHolder`实例，并把加载出来的布局传入到构造函数当中，最后将`ViewHolder`的实例返回。`onBindViewHolder()`方法是用于对RecyclerView子项的数据进行赋值的，会在每个子项被滚动到屏幕内的时候执行，这里我们通过`position`参数得到当前项的`Fruit`实例，然后再将数据设置到`ViewHolder`的`ImageView`和`TextView`当中即可。`getItemCount()`方法就非常简单了，它用于告诉RecyclerView一共有多少子项，直接返回数据源的长度就可以了。

然后在Activity类中注册

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    initFruits(); // 初始化水果数据
    RecyclerView recyclerView = (RecyclerView) findViewById(R.id.recycler_view);
    LinearLayoutManager layoutManager = new LinearLayoutManager(this);
    recyclerView.setLayoutManager(layoutManager);
    FruitAdapter adapter = new FruitAdapter(fruitList);
    recyclerView.setAdapter(adapter);
}
```

## 实现横向滚动和瀑布流布局 ##

### 横向滚动 ###
修改Activity中的方法

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    initFruits();
    RecyclerView recyclerView = (RecyclerView) findViewById(R.id.recycler_view);
    LinearLayoutManager layoutManager = new LinearLayoutManager(this);
    layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);
    recyclerView.setLayoutManager(layoutManager);
    FruitAdapter adapter = new FruitAdapter(fruitList);
    recyclerView.setAdapter(adapter);
}
```

MainActivity中只加入了一行代码，调用LinearLayoutManager的`setOrientation()`方法来设置布局的排列方向，默认是纵向排列的，我们传入`LinearLayoutManager.HORIZONTAL`表示让布局横行排列，这样RecyclerView就可以横向滚动了。

### 瀑布流 ###

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    initFruits();
    RecyclerView recyclerView = (RecyclerView) findViewById(R.id.recycler_view);
    StaggeredGridLayoutManager layoutManager = new 
    StaggeredGridLayoutManager(3, StaggeredGridLayoutManager.VERTICAL);
    recyclerView.setLayoutManager(layoutManager);
    FruitAdapter adapter = new FruitAdapter(fruitList);
    recyclerView.setAdapter(adapter);
}
```

在`onCreate()`方法中，我们创建了一个`StaggeredGridLayoutManager`的实例。`StaggeredGridLayoutManager`的构造函数接收两个参数，第一个参数用于指定布局的列数，传入3表示会把布局分为3列；第二个参数用于指定布局的排列方向，传入`StaggeredGridLayoutManager.VERTICAL`表示会让布局纵向排列，最后再把创建好的实例设置到RecyclerView当中就可以了。

## RecyclerView的点击事件 ##

不同于ListView的是，RecyclerView并没有提供类似于`setOnItemClickListener()`这样的注册监听器方法，而是需要我们自己给子项具体的View去注册点击事件。因为在过去的ListView中，虽然我们可以很方便的通过`setOnItemClickListener()`去注册子项的点击事件，但当我们想要点击的是子项里具体的某一个按钮的时候，用ListView实现起来就相对比较麻烦了。RecyclerView干脆直接摒弃了子项点击事件的监听器，所有的点击事件都由具体的View去注册，就再没有这个困扰了。

修改`FruitAdapter`中的代码

```java
public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {

	private List<Fruit> mFruitList;

	static class ViewHolder extends RecyclerView.ViewHolder {
    	View fruitView;
    	ImageView fruitImage;
    	TextView fruitName;

    	public ViewHolder(View view) {
        	super(view);
        	fruitView = view;
        	fruitImage = (ImageView) view.findViewById(R.id.fruit_image);
        	fruitName = (TextView) view.findViewById(R.id.fruit_name);
    	}
	}

	public FruitAdapter(List<Fruit> fruitList) {
    	mFruitList = fruitList;
	}

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.
            fruit_item, parent, false);
        final ViewHolder holder = new ViewHolder(view);
        holder.fruitView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Toast.makeText(v.getContext(), "you clicked view " + fruit.getName(),
                    Toast.LENGTH_SHORT).show();
            }
        });
        holder.fruitImage.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Toast.makeText(v.getContext(), "you clicked image " + fruit.getName(),
                    Toast.LENGTH_SHORT).show();
            }
        });
        return holder;
    }

}
```


我们先是修改了`ViewHolder`，在`ViewHolder`中添加了`fruitView`变量来保存子项最外层布局的实例，然后在`onCreateViewHolder()`方法中注册点击事件就可以了。这里分别为最外层布局和ImageView都注册了点击事件，RecyclerView的强大之处也在这里，它可以轻松实现子项中任意控件或布局的点击事件。我们在两个点击事件中先获取了用户点击的position，然后通过position拿到相应的`Fruit`实例，再使用Toast分别弹出两种不同的内容以示区别。

# 引用 #

以上是我在郭霖所著的《第一行代码：Android(第2版)》中学习Android的学习笔记，部分内容和图片摘自书中，有兴趣的可以自行购买电子书或实体书查阅。