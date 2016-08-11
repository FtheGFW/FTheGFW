# ConstraintLayout的使用简介

## 1 Setup

1. 下载Android Studio 2.2 Preview
2. android sdk manager更新support包，在项目的gradle脚本里添加配置：

```groovy
dependencies {
  compile 'com.android.support.constraint:constraint-layout:1.0.0-alpha5'
}
```

 注：更详细的可以参考[Android官方文档](http://tools.android.com/tech-docs/layout-editor)

## 2 约束(attr)简介和使用

### 2.1  Left/Top/Right/Bottom约束

```json
app:layout_constraint[SourceAnchor]_[TargetAnchor]="@id/[TargetId]"
```

其中TargetId可以是Sibling view， 也可以是其**Parent（即ConstraintLayout)**；

SourceAnchor与TargetAnchor的组合规律是左右约束对应于targetView的左右边界，

上下约束应用在targetView的上下边界，没有类似左右约束对应targetView的上下边界这种情况：

layout_constraintTop_toTopOf ：顶部与targetView的顶部对齐

layout_constraintTop_toBottomOf ：顶部与targetView的底部对齐

layout_constraintBottom_toTopOf ：底部和targetView的顶部对齐

layout_constraintBottom_toBottomOf ：底部和targetView的底部对齐

….

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.wwm.constraintlayoutdemo.MainActivity"
    >

    <!--constraintLeft/Right/Top/Bottom demo-->
    <ProgressBar
        style="?android:attr/progressBarStyle"
        android:layout_width="145dp"
        android:layout_height="62dp"
        android:id="@+id/progressBar"
        android:layout_marginStart="136dp"
        android:layout_marginLeft="136dp"
        android:layout_marginTop="72dp"
        app:layout_constraintTop_toTopOf="@+id/activity_main"
        app:layout_constraintLeft_toLeftOf="@+id/activity_main"
        android:layout_marginEnd="16dp"
        app:layout_constraintRight_toRightOf="@+id/activity_main"
        android:layout_marginRight="16dp"
        app:layout_constraintBottom_toBottomOf="@+id/activity_main"
        android:layout_marginBottom="16dp"
        app:layout_constraintVertical_bias="0.38"
        app:layout_constraintHorizontal_bias="0.33"
        />
  
</android.support.constraint.ConstraintLayout>
```
***预览截图：***![Screen Shot 2016-08-08 at 4.29.50 PM](/Users/wwm/Desktop/Screen Shot 2016-08-08 at 4.29.50 PM.png)



### 2.2 Bias(percentage based position)约束

```json
app:layout_constraintVertical_bias="[40%]"
app:layout_constraintHorizontal_bias="[60%]"
```

实现按父控件(即ConstraintLayout)宽高比例布局childView，例如从把child布局在ConstraintLayout的X轴40%处，Y轴60%处。

```xml
<!--bias(percentage) demo-->
<RatingBar
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:id="@+id/ratingBar"
    android:layout_marginStart="16dp"
    app:layout_constraintHorizontal_bias="0.75"
    app:layout_constraintVertical_bias="0.3"
    app:layout_constraintRight_toRightOf="@+id/activity_main"
    app:layout_constraintLeft_toLeftOf="@+id/activity_main"
    android:layout_marginLeft="16dp"
    android:layout_marginTop="16dp"
    app:layout_constraintTop_toTopOf="@+id/activity_main"
    app:layout_constraintBottom_toBottomOf="@+id/activity_main"
    android:layout_marginBottom="16dp"
    />
```
***预览截图：*** ![Screen Shot 2016-08-08 at 4.33.17 PM](/Users/wwm/Desktop/Screen Shot 2016-08-08 at 4.33.17 PM.png)

### 2.3 CenterX/Y约束

```json
app:layout_constraintCenterX_toCenterX="@id/TargetId"
app:layout_constraintCenterY_toCenterY="@+id/TargetId"
```

布局的时候实现与targetView水平居中对齐，垂直居中对齐

```xml
​```xml
<!-- center constraint -->
<ImageView
    android:id="@+id/image"
    android:layout_width="wrap_content"
    android:layout_height="150dp"
    android:minWidth="200dp"
    android:background="#666"
    android:src="@mipmap/ic_launcher"
    />

<TextView
    android:id="@+id/checkBox"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="sit in center of the image"
    android:textColor="@android:color/holo_red_light"
    android:textStyle="bold"
    app:layout_constraintBottom_toBottomOf="@+id/image"
    app:layout_constraintCenterX_toCenterX="@id/image"
    app:layout_constraintCenterY_toCenterY="@+id/image"
    app:layout_constraintLeft_toLeftOf="@+id/image"
    app:layout_constraintRight_toRightOf="@+id/image"
    app:layout_constraintTop_toTopOf="@+id/image"
    />
```

***预览截图：***  ![Screen Shot 2016-08-08 at 4.49.47 PM](/Users/wwm/Desktop/Screen Shot 2016-08-08 at 4.49.47 PM.png)

### 2.4 baseline 约束

```json
app:layout_constraintBaseline_toBaselineOf="@+id/TargetId"
```

这个约束的效果和LinearLayout，RelativeLayout里的baseline对齐效果类似。

Android中文本的绘制是基于baseline：

```java
/**
 * Draw the text, with origin at (x,y), using the specified paint. The
 * origin is interpreted based on the Align setting in the paint.
 *
 * @param text  The text to be drawn
 * @param x     The x-coordinate of the origin of the text being drawn
 *              （ 注意，这里需要传baseline!!! ）
 * @param y     The y-coordinate of the baseline of the text being drawn
 * @param paint The paint used for the text (e.g. color, size, style)
 */
Canvas.drawText(@NonNull String text, float x, float y, @NonNull Paint paint)
```

baseline的说明可以看下图，更详细的介绍可以参考[[自定义控件其实很简单1/4](http://blog.csdn.net/aigestudio/article/details/41447349)](http://img.blog.csdn.net/20141124164740174?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWlnZXN0dWRpbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

 ![font_metrics](/Users/wwm/Downloads/font_metrics.gif)

```xml
<!-- baseline constraint demo-->
<TextView
    android:layout_width="120dp"
    android:layout_height="wrap_content"
    android:text="Big text"
    app:layout_constraintTop_toTopOf="@+id/activity_main"
    app:layout_constraintLeft_toLeftOf="@+id/activity_main"
    android:layout_marginLeft="48dp"
    android:id="@+id/big_text"
    android:textSize="26sp"
    android:layout_marginTop="40dp"
    android:layout_marginStart="48dp"
    />

<TextView
    android:id="@+id/small_text"
    android:layout_marginRight="48dp"
    app:layout_constraintBaseline_toBaselineOf="@+id/big_text"
    app:layout_constraintRight_toRightOf="@+id/activity_main"
    android:layout_width="100dp"
    android:layout_height="wrap_content"
    android:padding="10dp"
    android:text="Small text"
    android:textSize="14sp"
    tools:layout_editor_absoluteX="301dp"
    />
```
***预览截图：***  ![Screen Shot 2016-08-08 at 6.48.03 PM](/Users/wwm/Desktop/Screen Shot 2016-08-08 at 6.48.03 PM.png)

### 2.5 guideline 辅助约束 

```json
app:layout_constraintGuide_begin="[16dp]"
app:layout_constraintGuide_end="[16dp]"
app:layout_constraintGuide_Percent="[85]"
```

功能类似Space控件，即实际在测量大小为0，且不会被绘制，仅为其他的view提供一个基线（参照）。

使用时需要为它设置orientation属性：

如果orientation=horizontal，则layout_constraintGuide_begin表示距离ConstraintLayout左边界的距离，layout_constraintGuide_Percent表示与ConstraintLayout左边界constraintLayoutWidth * percentage距离，layout_constraintGuide_end表示与ConstraintLayout右边界的距离；

如果orientation=vertical，则layout_constraintGuide_begin表示距离ConstraintLayout上边界的距离，ayout_constraintGuide_Percent表示与ConstraintLayout上边界constraintLayoutWidth * percentage距离，layout_constraintGuide_end表示距离ConstraintLayout下边界的距离。

```xml
<!-- guideline demo 水平的line-->
<android.support.constraint.Guideline
    android:id="@+id/guideline4"
    android:layout_width="411dp"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    app:layout_constraintGuide_begin="77dp"
    tools:layout_editor_absoluteX="0dp"
    tools:layout_editor_absoluteY="66dp"
    />
<!-- 竖直的line-->
<android.support.constraint.Guideline
    android:id="@+id/guideline6"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    app:layout_constraintGuide_Percent="25"
    tools:layout_editor_absoluteX="102dp"
    tools:layout_editor_absoluteY="0dp"
    />
```
***预览截图：***   ![Screen Shot 2016-08-08 at 7.08.10 PM](/Users/wwm/Desktop/Screen Shot 2016-08-08 at 7.08.10 PM.png)

### 2.6 dimension_ratio约束 

```json
app:layout_constraintDimensionRatio="16:9"
```

实现高和宽（纵横比）的约束（仅当设置宽或高为一个固定大小时有效），可以用于替代原来的RatioImageView，SquareImageView等。

```xml
<!-- dimension ratio demo -->
<ImageView
    android:id="@+id/image"
    android:layout_width="200dp"
    android:layout_height="0dp"
    android:background="#666"
    app:layout_constraintDimensionRatio="1:2"
    android:src="@mipmap/ic_launcher"
    app:layout_constraintBottom_toBottomOf="@+id/activity_main"
    android:layout_marginBottom="16dp"
    android:layout_marginTop="16dp"
    app:layout_constraintTop_toTopOf="@+id/activity_main"
    android:layout_marginStart="16dp"
    app:layout_constraintLeft_toLeftOf="@+id/activity_main"
    android:layout_marginLeft="16dp"
    />
```

***预览截图：***  ![Screen Shot 2016-08-08 at 6.50.01 PM](/Users/wwm/Desktop/Screen Shot 2016-08-08 at 6.50.01 PM.png)

### 2.7 基于百分比的宽高测量

alpha4版本的ConstraintLayout还没法完整的实现类似PercentageFrameLayout和PercentageRelativeLayout的测量方式，参考[Romain Guy的回答](http://stackoverflow.com/a/37369672)，以后的版本可能会实现相关的支持，现在通过guideline只能实现类似下面简单的功能：

```xml
<android.support.constraint.Guideline
    android:id="@+id/guideline6"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    app:layout_constraintGuide_Percent="25"
    tools:layout_editor_absoluteX="102dp"
    tools:layout_editor_absoluteY="0dp"
    />

<android.support.constraint.Guideline
    android:id="@+id/guideline5"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    app:layout_constraintGuide_Percent="55"
    app:layout_constraintGuide_begin="0dp"
    app:layout_constraintGuide_end="0dp"
    tools:layout_editor_absoluteX="308dp"
    tools:layout_editor_absoluteY="0dp"
    />

<ImageView
    android:id="@+id/image"
    android:layout_width="0dp"
    android:layout_height="0dp"
    android:layout_marginBottom="16dp"
    android:layout_marginEnd="16dp"
    android:layout_marginRight="16dp"
    android:layout_marginTop="16dp"
    android:background="@color/colorAccent"
    android:minWidth="100dp"
    android:src="@mipmap/ic_launcher"
    app:layout_constraintBottom_toBottomOf="@+id/activity_main"
    app:layout_constraintDimensionRatio="16:9"
    app:layout_constraintLeft_toLeftOf="@+id/guideline6"
    app:layout_constraintRight_toLeftOf="@+id/guideline5"
    app:layout_constraintTop_toTopOf="@+id/activity_main"
    />
```

***预览截图：***![Screen Shot 2016-08-08 at 7.02.00 PM](/Users/wwm/Desktop/Screen Shot 2016-08-08 at 7.02.00 PM.png)

  



注：

1. app:layout_constraintXXX_creator 是LayoutEditor用来识别是谁创建的这个约束，在运行时忽略
2. tools:layout_editor_absoluteX 仅用于预览


## 3 总结

ConstrainLayout的优点是在于可以扁平化视图层级，减少嵌套，从而带来UI性能的优化与内存占用的减小（但是复杂布局还是自定义view更靠谱点，仅在不经常变化的前提下，否则维护起来不方便）

# 参考

[1. Use ConstraintLayout to design your views](https://codelabs.developers.google.com/codelabs/constraint-layout/index.html)

[2. ConstraintLayout](http://blog.dreamtobe.cn/2016/08/03/constraint-layout/?utm_source=tuicool&utm_medium=referral)


