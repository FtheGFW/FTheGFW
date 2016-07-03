# 我的Android开发备忘录

### 1. RelativeLayout#onMeasure

在开发app的时候，有的时候会需要手动测量一个视图的大小，代码一般长这样 ↓↓↓ 

```java
private void measureView() {
  // measure a view without layout params set on
  RelativeLayout v = new RelativeLayout(MainActivity.this);
  int maxWidth = 1080; 
  int maxHeight = 1920;
  v.measure(View.MeasureSpec.makeMeasureSpec(maxWidth, View.MeasureSpec.AT_MOST),
            View.MeasureSpec.makeMeasureSpec(maxHeight, View.MeasureSpec.AT_MOST));
  int measuredWidth = v.getMeasuredWidth();
  int measuredHeight = v.getMeasureHeight();
  ...
}
```

当我信心满满的把上面的代码跑在Android 5.0的设备上时，正确的输出了我需要的宽和高，**但是**，当我又在 **4.3** 版本的手机上测试了一下的时候，竟然就直接crash了，输出的crash栈如下 ↓↓↓

```json
Fatal Exception: java.lang.NullPointerException
       at android.widget.RelativeLayout.onMeasure(RelativeLayout.java:548)
       at android.widget.RelativeLayout.onMeasure(SourceFile:105)
       at android.view.View.measure(View.java:16831)
       ...
```

从调用栈可以很清晰的看出来是在调用RelativeLayout的onMeasure方法时出现了NPE，于是便从GrepCode上搜索RelativeLayout的源码，跳到548行  ↓↓↓

```java
// 4.3中RelativeLayout#onMeasure()源码片段
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    ...
    if (isWrapContentWidth) {
        // Width already has left padding in it since it was calculated by
        // looking at the right of each child view
        width += mPaddingRight;

        if (mLayoutParams.width >= 0) { // 导致NPE的根源，没有判断mLayoutParams是否为空
          width = Math.max(width, mLayoutParams.width);
        }

        width = Math.max(width, getSuggestedMinimumWidth());
        width = resolveSize(width, widthMeasureSpec);
        ...
    }
    ...
}
```

很自然的，我便想看一下5.0上的源码，其中这个bug在4.4上就被修复了，所以我这里就直接给出4.3和4.4的diff↓↓↓

```diff
@@ -545,7 +536,7 @@ public class RelativeLayout extends ViewGroup {
             // the right of each child view
             width += mPaddingRight;
 
-            if (mLayoutParams.width >= 0) {
+            if (mLayoutParams != null && mLayoutParams.width >= 0) {
                 width = Math.max(width, mLayoutParams.width);
             }
```



找到了根本原因，问题也就好办了，只要被测量的视图的mLayoutParams不为空就OK了！修复这个bug的方法有两种：   
1. 把target view 加到一个ViewGroup里去，这样那个ViewGroup会设置一个默认的LayoutParams给它的child view（如果这个child view的LayoutParams为空的话）   
2. 手动实例化一个LayoutParams，然后set到 target view      

最后给出用第二种方式修正这个bug后的代码 ↓↓↓ 

```java
private void measureView() {
    // measure a view without layout params set on
    RelativeLayout v = new RelativeLayout(MainActivity.this);
    int maxWidth = 1080; 
    int maxHeight = 1920;
  	v.setLayoutParams(new ViewGroup.MarginLayoutParams(
      ViewGroup.LayoutParams.MATCH_PARENT,
        ViewGroup.LayoutParams.WRAP_CONTENT));
    v.measure(View.MeasureSpec.makeMeasureSpec(maxWidth, View.MeasureSpec.AT_MOST),
        View.MeasureSpec.makeMeasureSpec(maxHeight, View.MeasureSpec.AT_MOST));
   	int measuredWidth = v.getMeasuredWidth();
   	int measuredHeight = v.getMeasureHeight();
   	...
}
```

