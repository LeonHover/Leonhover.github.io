---
layout: post
title:  "优雅地编写Android中findView代码——ButterKnife使用"
date:   2016-08-19 09:36:25
categories: Android 开源 Library
---


### 一、简介
  ButterKnife是现在很多Android应用开发者都会使用的开源库，它可以将你从冗余、模板式的`findViewById`、`setOnClickListener`、`setOnItemClickLister`等代码中解放出来，极大地提高编程效率。不同于其他依赖注入的框架，它只针对Android的View的字段和方法进行绑定。

  ![ButterKnife图标][ButterKnife图标]

  [ButterKnife的Github地址][github_url]

### 二、使用配置

  在工程级的`build.gradle`中引入`android-apt`插件
  {% highlight java %}
  buildscript {
    repositories {
      mavenCentral()
     }
    dependencies {
      classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
    }
  }
  {% endhighlight %}


  在模块级的`build.gradle`中应用`android-apt`插件并添加ButterKnife的依赖:

  {% highlight java %}
  apply plugin: 'android-apt'

  android {
    ...
  }

  dependencies {
    compile 'com.jakewharton:butterknife:8.2.1'
    apt 'com.jakewharton:butterknife-compiler:8.2.1'
  }
  {% endhighlight %}

配合Android Studio中的插件`ButterKnife Zelezny`使用，可以自动生成ButterKnife的注解，进一步提高编码效率。

  注意：文章中`butterknife`、`butterknife-compiler`以及`android-apt`的版本是当时的最新版本，请在[The Central Repository][mavenCentral]上查找最新的版本。

### 三、Show me the code

#### 绑定控件或资源

  对控件或资源字段添加`@BindView(View的Id)`、`@BindString(String的ID)`等注解，ButterKnife就会找到这些字段并自动注入且转换成对应的在布局文件的视图控件或资源。

  Activity使用ButterKnifer的场景：


  {% highlight java %}
  public class ExampleAcitivity extends AppCompatActivity {
    //视图控件绑定
    @BindView(R.id.title) TextView title;
    @BindView(R.id.subtitle) TextView subtitle;
    @BindView(R.id.footer) TextView footer;
    //字符串资源绑定
    @BindString(R.string.name) String name;
    //图片资源绑定
    @BindDrawable(R.drawable.ic_launcher) Drawable icon;
    @BindBitmap(R.drawable.ic_launcher) Bitmap iconBmp;
    //颜色资源
    @BindColor(R.color.red) int red;
    //尺寸资源,会根据设备的Density来换算。
    @BindDimen(R.dimen.spacer) float spacer;
    //整型
    @BindInt(R.integer.number) int number;
    //布尔
    @BindBool(R.bool.updated) boolean updated;
    //数组资源
    @BindArray(R.array.friends_array) String[] friends;
    @BindArray(R.array.numbers) int[] numbers;
    @Override
    public void onCreate(Bundle savedInstanceState){
      super.onCreate(savedInstanceState);
      setContentView(R.layout.simple_acitivyt);
      //注意一定要记得添加到setContentView之后。
      ButterKnife.bind(this);
    }
  }
  {% endhighlight %}

  Fragment使用ButterKnife的场景：

  {% highlight java %}
  public class FancyFragment extends Fragment {
    @BindView(R.id.button1) Button button1;
    @BindView(R.id.button2) Button button2;

    @Override
    public View onCreateView(LayoutInflater inflater,ViewGroup container,Bundle savedInstanceState) {
      View view = inflater.inflate(R.layout.fancy_fragment,container,false);
      //在Fragment中在得到RootView后来bind。
      ButterKnife.bind(this,view);
      // TODO 使用控件字段
    }
  }
  {% endhighlight %}

  ViewHolder使用ButterKnife的场景:

  {% highlight java %}
  public class MyAdapter extends BaseAdapter {
  @Override
  public View getView(int position, View view, ViewGroup parent) {
    ViewHolder holder;
    if (view != null) {
      holder = (ViewHolder) view.getTag();
    } else {
      view = inflater.inflate(R.layout.whatever, parent, false);
      holder = new ViewHolder(view);
      view.setTag(holder);
    }

    holder.name.setText("John Doe");
    // etc...

    return view;
  }

  static class ViewHolder {
    @BindView(R.id.title) TextView name;
    @BindView(R.id.job_title) TextView jobTitle;

    public ViewHolder(View view) {
      //构造ViewHolder的时Bind。
      ButterKnife.bind(this, view);
    }
  }
}
{% endhighlight %}

另外ButterKnife还提供其他的绑定API：
  * 使用Activity作为视图根布局来绑定任何实例。如果你使用像MVC的开发模式，你可以使用它的activity同`ButterKnife.bind(this,activity)`来绑定controller。
  * 使用`ButterKnife.bind(this)`来绑定视图的子视图到成员变量字段。如果你在一个布局中使用 `<merge>`标签，你可以在自定义视图的构造方法后立即调用`bind方法`。另一种方式，在XML中定义的视图可以在`onFinishInflate()`中使用。

#### 视图列表

你可以集合多个视图到列表或数组中。

{% highlight java %}
@BindView({R.id.first_name,R.id.middle_name,R.id.last_name})
List<EditText> nameViews
{% endhighlight %}
ButterKnife的`apply`方法可以使你同时对列表中的所有视图做动作。

{% highlight java %}
ButterKnife.apply(nameViews,DISABLE);
ButterKnife.apply(nameViews,ENABLED,false);
{% endhighlight %}

`Action`和`Setter`接口允许指定简单的行为。

{% highlight java %}
static final ButterKnife.Action<View> DISABLE = new ButterKnife.Action<View>() {
  @Override public void apply(View view, int index) {
    view.setEnabled(false);
  }
};
static final ButterKnife.Setter<View, Boolean> ENABLED = new ButterKnife.Setter<View, Boolean>() {
  @Override public void set(View view, Boolean value, int index) {
    view.setEnabled(value);
  }
};
{% endhighlight %}

Android中视图的`Property`也可以在`apply`方法中使用。

{% highlight java %}
ButterKnife.apply(nameViews,View.ALPHA,0.0f);
{% endhighlight %}


#### 绑定事件监听器

监听器也可以自动设置到方法上。

{% highlight java %}
@OnClick(R.id.submit)
public void submit(View view){
  // TODO submit action.
}
{% endhighlight %}

监听器的所有方法参数都是可选的。

{% highlight java %}
@OnClick(R.id.submit)
public void submit(){
  // TODO submit action.
}
{% endhighlight %}

定义一个特定类型的参数，它会被自动转换。

{% highlight java %}
@OnClick(R.id.submit)
public void sayHi(Button button){
  button.setText("Hello!");
}
{% endhighlight %}

指定多个视图ID绑定到一个通常的事件处理上。

{% highlight java %}
@OnClick({ R.id.door1, R.id.door2, R.id.door3 })
public void pickDoor(DoorView door) {
  if (door.hasPrizeBehind()) {
    Toast.makeText(this, "You win!", LENGTH_SHORT).show();
  } else {
    Toast.makeText(this, "Try again", LENGTH_SHORT).show();
  }
}
{% endhighlight %}

自定义视图可以绑定他们自己的监听器，而不用指定一个视图ID。

{% highlight java %}
public class FancyButton extends Button {
  @OnClick
  public void onClick() {
    // TODO do something!
  }
}
{% endhighlight %}

#### 重置绑定

Fragment与Activity有不同的视图生命周期。在`onCreateView`中绑定Fragment,在`onDestroyView`中置空视图引用。ButterKnife在你调用`bind`方法时候返回一个`Unbinder`的实例对象，可以用来做置空操作。在推荐的生命周期毁掉中调用它的`unbind`方法即可。

{% highlight java %}
public class FancyFragment extends Fragment {
  @BindView(R.id.button1) Button button1;
  @BindView(R.id.button2) Button button2;
  private Unbinder unbinder;

  @Override public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    View view = inflater.inflate(R.layout.fancy_fragment, container, false);
    unbinder = ButterKnife.bind(this, view);
    // TODO Use fields...
    return view;
  }

  @Override public void onDestroyView() {
    super.onDestroyView();
    unbinder.unbind();
  }
}
{% endhighlight %}

#### 可选的绑定
默认地情况下，都需要视图或资源绑定以及监听器绑定。如果目标视图不能找到，就会抛出一个异常。
为了抑制这种情况发生，可以创建一个可选绑定，添加一个`@Nullable`的注解到字段上或在方法上添加`@Optional`注解。

记住：`@Nullable`注解可以用在任何字段域。[Androids' "support-annotations" library][support-annotations]鼓励使用`@Nullable`注解。

{% highlight java %}
@Nullable @BindView(R.id.might_not_be_there) TextView mightNotBeThere;

@Optional @OnClick(R.id.maybe_missing) void onMaybeMissingClicked() {
  // TODO ...
}
{% endhighlight %}

#### 多方法监听器
那些有多个回调方法的监听器对应的方法注解可以用来绑定它们其中的任何一个。每个注解都有一个默认的它绑定的回调方法。使用`callback`参数来指定备用的回调。

{% highlight java %}
@OnItemSelected(R.id.list_view)
void onItemSelected(int position) {
  // TODO ...
}

@OnItemSelected(value = R.id.maybe_missing, callback = NOTHING_SELECTED)
void onNothingSelected() {
  // TODO ...
}
{% endhighlight %}

#### 额外补充
此外还包括了`findById`方法，简化在`View`、`Activity`、或`Dialog`上find控件的代码实现。它使用泛型来推断返回类型并自动转换。

{% highlight java %}
View view = LayoutInflater.from(context).inflate(R.layout.thing, null);
TextView firstName = ButterKnife.findById(view, R.id.first_name);
TextView lastName = ButterKnife.findById(view, R.id.last_name);
ImageView photo = ButterKnife.findById(view, R.id.photo);
{% endhighlight %}


### 四、总结
  很强大的View注入框架，现在大部分开发者都有使用它。对于新开始一个项目，强烈推荐使用，如果是老代码或项目，其中的现在看起来不怎么优雅的代码可以保留，毕竟不会影响应用程序的任何功能；而新的需求界面可以使用。总结来说，对于任何Android项目都可以使用，也推荐尽快使用，它只会提供你的编码效率，没有任何负面的功能或性能影响。

[github_url]:https://github.com/JakeWharton/butterknife
[ButterKnife图标]: http://jakewharton.github.io/butterknife/static/logo.png
[mavenCentral]: http://search.maven.org/#search%7Cga%7C1%7C
[support-annotations]:http://tools.android.com/tech-docs/support-annotations
