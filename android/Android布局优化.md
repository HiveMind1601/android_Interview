&nbsp;&nbsp;&nbsp;&nbsp;我的简书[传送门](https://www.jianshu.com/p/faf57bd030ee)
&nbsp;&nbsp;&nbsp;&nbsp;我的CSDN[传送门](https://blog.csdn.net/github_38372075/article/details/80393167)


&nbsp;&nbsp;&nbsp;&nbsp;作为android应用来讲，无论应用本身多么美观，功能多么强大，内容多么丰富。但如果App本身打开界面缓慢超过手机16ms刷新一次页面的时间，就会产生卡顿。用户体验都会变得极差，导致用户量减少。所以我们在开发过程中同样要注重布局优化。
###     &lt;include&gt;标签
&nbsp;&nbsp;&nbsp;&nbsp;在Layout布局中如果有你想要引用的布局时，若该布局在不同的布局是公共布局，我们会多次使用到。这时可以使用<include>标签。并且便于统一的修改与查看。
```xml
    <-- container为引用布局的布局id -->
    <include layout="@layout/container"/>
```
&nbsp;&nbsp;&nbsp;&nbsp;非常简单只要在你所需要放置该布局的布局内部使用<include>标签引入该布局就可以了。
在<include>标签当中，我们是可以覆写所有layout属性的，即include中指定的layout属性将会覆盖掉。如我们想修改它的宽高为`wrap_content`。
```xml
    <include  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content"  
        layout="@layout/container" />  
```
&nbsp;&nbsp;&nbsp;&nbsp;除了layout_width与layout_height之外，我们还可以覆写container中的任何一个layout属性，如layout_gravity、layout_margin等，而非layout属性则无法在<include>标签当中进行覆写。另外需要注意的是，如果我们想要在<include>标签当中覆写layout属性，必须要将layout_width和layout_height这两个属性也进行覆写，否则覆写效果将不会生效。
###     &lt;merge&gt;标签
&nbsp;&nbsp;&nbsp;&nbsp;&lt;merge&gt;标签是作为<include>标签的一种辅助扩展来使用的，它的主要作用是为了防止在引用布局文件时产生多余的布局嵌套。Android解析和展示一个布局需要消耗时间，布局嵌套的越多，那么解析起来也就越耗时，性能也就越差，因此我们在编写布局文件时应该让嵌套的层数越少越好。
```xml
    <merge  
        android:layout_width="match_parent"  
        android:layout_height="wrap_content" >
        <View  
            android:layout_width="match_parent"  
            android:layout_height="wrap_content"/>
        <View  
            android:layout_width="match_parent"  
            android:layout_height="wrap_content"/>
    </merge>
```
&nbsp;&nbsp;&nbsp;&nbsp;大家可以见到&lt;merge&gt;标签的使用方法是直接当做该布局的根布局节点使用，而当在其他位置需要引用该布局时，则使用&lt;include&gt;标签进行引用，同时该节点会同步变成父容器的根节点。比如你使用在LinearLayout中则两个view线性排列，而在RelativeLayout中则&lt;merge&gt;标签就相当于相对布局标签。这样就可以省略一些不必要的布局嵌套了。
###     &lt;ViewStub&gt;标签
&nbsp;&nbsp;&nbsp;&nbsp;&lt;ViewStub&gt;标签实际上是一个轻量级的View，它既没有尺寸，也不会绘制任何东西，所以将它放置在布局当中基本可以认为是完全不会影响性能的。只要在需要的时候显示它，才会进行加载。
```xml
<ViewStub
    android:id="@+id/stub"
    android:inflatedId="@+id/container_layout"
    android:layout="@layout/stub_layout"
    android:layout_width=",match_parent"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom" />
```
&nbsp;&nbsp;&nbsp;&nbsp;虽然ViewStub是不占用任何空间的，但是每个布局都必须要指定layout_width和layout_height属性，否则运行就会报错。且ViewStub所要替代的layout文件中不能含有&lt;merge&gt;标签，所以使用前需要构思好界面布局，以免不必要的嵌套。一旦ViewStub被显示后，则ViewStub将从视图框架中移除，其id也会失效，此时findViewById()得到的也是空的。
&nbsp;&nbsp;&nbsp;&nbsp;ViewStub使用起来非常简单，只要在需要的时候findViewById()招到它并调用setVisibility(View.VISIBLE)或者inflate()显示它就可以了。
####     标签小结
| 标签               | 使用原因 | 优化结果 | 使用举例 |
| :----------------- |  :----:  |  :----:  | :------: |
| &lt;include&gt;    | 提取公共部分，提高布局复用性 |   减少测量，绘制时间     | App中有多个UI界面需要使用同一布局或部分布局时。如页面标题toolBar复用时使用。 |
| &lt;merge&gt;      |   布局层级减少   |   减少绘制工作量   | 当所需要复用的部分布局与要合并到的布局的根标签一致时使用。（类似加强版include，减少布局层级，但耦合性更强）。 |
| &lt;ViewStub&gt;   |    无需第一时间展示于界面上，在需要时加载  |  减少测量，绘制时间  | 该界面不需要第一时间展示给用户，如网络报错界面，或用户信息下拉界面，在该界面中，但第一时间不需要显示给用户时使用。 |
###     ConstraintLayout约束布局
![布局可视化操作.png](https://upload-images.jianshu.io/upload_images/12239817-d4414d2af1196126.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
&nbsp;&nbsp;&nbsp;&nbsp;AndroidStudio上有一个神奇的功能，就是在Xml布局中我们可以在design标签下进行布局可视化操作。但是该功能并不完善，属于系统自动生成的一个布局，反而会让布局内部凌乱不堪，难以读懂，同时会造成卡顿，所以以前我们都是用该界面进行预览查看；而ConstraintLayout约束布局这一新布局，它反而支持布局可视化操作，可以把它比喻成一个可视化视图操作布局的RelativeLayout，ConstraintLayout是使用约束的方式来指定各个控件的位置和关系的。布局内部不需要嵌套其他布局，就可以完成你想要的界面出现。所以它可以有效的避免布局的嵌套，从而达到优化布局的效果。因为使用太过复杂，想要深入了解使用方法请点击[ConstraintLayout](https://blog.csdn.net/guolin_blog/article/details/53122387)。


###     减少视图树层级结构
&nbsp;&nbsp;&nbsp;&nbsp;系统在显示没一个视图的时候，都要经理测量，布局，绘制的过程。如果我们的布局嵌套层数太多，会导致额外的测量、布局等，十分消耗系统资源，使UI卡顿，影响用户体验。所以要尽量减少是图书层级结构，避免不必要的布局嵌套，使用更少嵌套的布局方式。
![DDMS.png](https://upload-images.jianshu.io/upload_images/12239817-1b2971d44799a92d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


&nbsp;&nbsp;&nbsp;&nbsp;查看文件的视图树，我们可以使用DDMS来查看。首先运行项目在真机或虚拟机上。而后再到tools中打开DDMS。这里就不展开介绍了。
###     其他
* 嵌套的LinearLayout中，尽量不要使用weight，因为weight会重新测量两次。
* Layout的选择，以尽量减少View树的层级为主，去除不必要的嵌套和View节点。比如如果LinearLayout嵌套过多，建议使用RelativeLayout减少布局嵌套。
* RelativeLayout本身尽量不要嵌套使用。
* View视图的隐藏与现实，尽量使用invisible。因为gone,不占用空间，视图会重新测量绘制；而invisible视图不会重新绘制，但仍然占用空间位置。
* 布局调优工具：[hierarchy viewer](https://developer.android.com/studio/profile/hierarchy-viewer)，[Lint tool](https://blog.csdn.net/u011240877/article/details/54141714)