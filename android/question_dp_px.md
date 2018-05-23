# Android dp px sp in dpi ppi 之间的区别和联系

[作者：Lovesosoi](https://github.com/lvm0306)


### 导读
* 简介
* 关联
* 换算
* 延伸问题 1.dp 为什么不能做为字体大小单位
* 延伸问题 2.计算一款手机的宽为多少dp
* 延伸问题 3.屏幕适配

### 简介

**px**: pixel 简称像素，我们常说的分辨率中1080*1920 就是指像素，即屏幕横向1080个像素点，纵向1920个像素点

**in**: inch 简称英寸，1英寸约为2.54cm ，手机尺寸 5.2in 一般是指对角线的大小位（5.2*2.54）13.28cm

**dp**:也可以叫做dip，全拼device independent pixels。设备不依赖像素的一个单位。与密度无关的像素，这是一个基于屏幕物理密度的抽象单位。

**sp**: Scale-independent Pixels 可伸缩像素的意思，采用和dp同样的设计理念

**dpi**: dots per inch 即对角线每英寸包含点的个数也叫屏幕密度（印刷点的密度），dot 是指物理的点，物理点的大小可能等于像素点的大小 比如手机中大多数情况下 dpi=ppi

**ppi**: pixels per inch 每英寸的像素数 即像素密度 也称图片分辨率
![](http://mdeandroid.oss-cn-beijing.aliyuncs.com/article/android_interview/01/android_interview01_01.jpg)
比如我们算一下小米6的ppi
ppi=(1920\*1920+1080\*1080)^2/5.15=427.74
官网显示为：`屏幕像素密度:428ppi` 

### 关联
dp是安卓创造了一个新的单位，中文名设备独立像素。并且规定在**160ppi的屏幕上，1dp=1px**。

Android 常见分辨率(最后一项为图片大小)

| Android    | dpi | 手机分辨率| icon 图片分辨率 |dp与px转化|
| --- | --- |--- | --- |
|mdpi|160dpi| 320*480| 48*48|1dp=1px|
|hdpi|240dpi|480*800 |72*72|1dp=1.5px|
|xhdpi|320dpi|720*1080 |96*96|1dp=2px |
|xxhdpi|480dpi|1080*1920 |144*144|1dp=3px |
|xxxhdpi|640dpi|1440*2560 | 192*192|1dp=4px|



如果在布局文件中使用100px 的话在160dpi 下 等同于100dp,在320dpi下等同于50dp,所以控件会变小，也就是没有达到适配的目的。 

### 换算
px = dp * (dpi / 160)
dp = px * (160 / ppi)

Java代码
```java
public static int dp2px(Context context,final float dpValue) {
        final float scale = context.getResources().getDisplayMetrics().density;
        return (int) (dpValue * scale + 0.5f);
    }

public static int px2dp(Context context,final float pxValue) {
        final float scale = context.getResources().getDisplayMetrics().density;
        return (int) (pxValue / scale + 0.5f);
    }
解释下为什么加0.5f，因为四舍五入时保持精度
```

### 延伸问题

#### 1.为什么用 dp 做文字单位不如 sp 好
Google [文档](https://developer.android.com/guide/topics/resources/more-resources#Dimension)中对sp 是这样描述的
```
Scale-independent Pixels - This is like the dp unit, but it is also scaled by the user's font size preference. It is recommend you use this unit when specifying font sizes, so they will be adjusted for both the screen density and the user's preference.
```
翻译过来就是
```
这与dp单位相似，但也会根据用户的字体大小偏好进行缩放。 建议您在指定字体大小时使用本单位，以便针对屏幕密度和用户偏好进行调整。
```

建议：如果textview的大小有严格的比例或者高度限制,我们需要给出textview的最大高度和宽度或者行数等限制,避免布局因用户字体放大导致整体布局严重变形

#### 2.如果算一个手机的宽总长是多少dp?手机的宽是固定为360dp么?
我以一加三T为例
1080*1920 5.5英寸 
经计算：dpi 400
dpi/160 =2.5
宽为：1080/2.5=432dp

而在720*1080 手机上 宽往往为360dp,不同的手机的宽是不一样的。
所以dp 不能作为屏幕适配的单位，相同dp的控件在分辨率低的手机会表现的更大。

#### 3.如果dp不能作为适配的单位的话，那么什么单位可以？
回答：px。
**[鸿阳文章传送门](https://blog.csdn.net/lmj623565791/article/details/45460089)**
总结下就是，按照一套分辨率为基础，如720*1080，就是将屏幕的宽分为720份，算好每一份对应多少px，写在dimen文件下，使用时通过引用的方式即可，将其他主流分辨率的宽也分成720分，算出对应的值，最后放进value/dimen 文件下。
一个一个算太麻烦了，洪洋有个一键生成版。需要使用jar 包，洪洋的jar包可以在传送门中可以下载，如果你找不到的话,我在我服务器中也传了一份，下面这个连接也可以直接下载
**[autolayout.jar](http://mdeandroid.oss-cn-beijing.aliyuncs.com/article/android_interview/01/autolayout.jar)**
使用方式为:
![](http://mdeandroid.oss-cn-beijing.aliyuncs.com/article/android_interview/01/interview01_02.jpg)
生成文件为：
![](http://mdeandroid.oss-cn-beijing.aliyuncs.com/article/android_interview/01/interview01_03.jpg)
![](http://mdeandroid.oss-cn-beijing.aliyuncs.com/article/android_interview/01/interview01_04.jpg)
将生成的文件放入项目中即可
建议：最好和ui沟通好，设计图用的是什么分辨率，就以哪一套为基础，这样可以直接用他标注的px值。

比如：ui 给你一套 720 * 1080  的设计稿，其中有个宽高为100px \* 100px的 你只需要按上面的方式导入到项目中，然后通过引用的方式定义出这个控件即可。
![](http://mdeandroid.oss-cn-beijing.aliyuncs.com/article/android_interview/01/interview01_06.jpg)
其中宽是引用x 高是引用y。

如此使用，在不同分辨率下也会有相同的样子，控件会随着手机的分辨率不同，进行相应的缩放。从而达到适配的效果。

另一种适配方式就是使用百分比布局 `android-percent-support`
，具体使用方式，可以参考[文章](https://www.jianshu.com/p/7a6475757743?from=jiantop.com)，我就不再此展开了。

### 总结
屏幕适配可以说一直都是Android 开发者的痛苦，因为ios 没有这个问题，而Android 由于碎片化太严重导致适配很痛苦，看过文章后，你一定理解了 dp px sp in dpi ppi 之间的区别和联系 了，对屏幕适配是也会有一定的了解了。
