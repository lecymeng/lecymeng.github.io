---
title: Android TabLayout
date: 2020-02-25 12:27:20
updated: 2020-02-25 12:27:20
tags: [Android, Widget]
---

# Android TabLayout

## 优雅地修改指示器宽度
在开发过程中，经常会碰到把指示器的宽度，做的和文字宽度一样，或者比文字宽度还要短的设计。使用 TabLayout 我们可以快速实现一个 Material Design 风格的标签栏，但 TabLayout 的指示线 Indicator 默认是占满一格 Tab 的，且官方未直接提供修改 Indicator 宽度的方法。

### 反射
如果你的项目中也有修改指示线宽度的需求，并且已经在网上找过修改方法，很可能你现在项目中用的就是这个方法。通过分析源码用反射实现的，代码如下：
```java
public void setIndicatorWidth(@NonNull final TabLayout tabLayout, final int margin) {
    tabLayout.post(new Runnable() {
        @Override
        public void run() {
            try {
                Field mTabStripField = tabLayout.getClass().getDeclaredField("mTabStrip");
                mTabStripField.setAccessible(true);
                LinearLayout mTabStrip = (LinearLayout) mTabStripField.get(tabLayout);
                for (int i = 0; i < mTabStrip.getChildCount(); i++) {
                    View tabView = mTabStrip.getChildAt(i);
                    Field mTextViewField = tabView.getClass().getDeclaredField("mTextView");
                    mTextViewField.setAccessible(true);
                    TextView mTextView = (TextView) mTextViewField.get(tabView);
                    tabView.setPadding(0, 0, 0, 0);
                    int width = mTextView.getWidth();
                    if (width == 0) {
                        mTextView.measure(0, 0);
                        width = mTextView.getMeasuredWidth();
                    }
                    LinearLayout.LayoutParams params = (LinearLayout.LayoutParams) tabView.getLayoutParams();
                    params.width = width;
                    params.leftMargin = margin;
                    params.rightMargin = margin;
                    tabView.setLayoutParams(params);
                    tabView.invalidate();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    });
}
```

这样做是在28以下没问题的，但如果把项目的 SDK 升级到 28 或以上，它就不再有效了，原因是 TabLayout 源码中的变量名修改了，所以代码也要改成这样：
```java
public void setIndicatorWidth(@NonNull final TabLayout tabLayout, final int margin) {
    tabLayout.post(new Runnable() {
        @Override
        public void run() {
            try {
                Field slidingTabIndicatorField = tabLayout.getClass().getDeclaredField("slidingTabIndicator");
                slidingTabIndicatorField.setAccessible(true);
                LinearLayout mTabStrip = (LinearLayout) slidingTabIndicatorField.get(tabLayout);
                for (int i = 0; i < mTabStrip.getChildCount(); i++) {
                    View tabView = mTabStrip.getChildAt(i);
                    Field textViewField = tabView.getClass().getDeclaredField("textView");
                    textViewField.setAccessible(true);
                    TextView mTextView = (TextView) textViewField.get(tabView);
                    tabView.setPadding(0, 0, 0, 0);
                    int width = mTextView.getWidth();
                    if (width == 0) {
                        mTextView.measure(0, 0);
                        width = mTextView.getMeasuredWidth();
                    }
                    LinearLayout.LayoutParams params = (LinearLayout.LayoutParams) tabView.getLayoutParams();
                    params.width = width;
                    params.leftMargin = margin;
                    params.rightMargin = margin;
                    tabView.setLayoutParams(params);
                    tabView.invalidate();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    });
}
```

通过反射虽然可以实现，但是反射不够优雅，并且它有可能因为 SDK 的升级而失效。

### 自定义 Tab
TabLayout 中的 Tab 是允许自定义的，但 Indicator 不属于 Tab。
所以有这样一种解决方案，把 Indicator 隐藏掉，然后在自定义 Tab 的布局中加入指示线。
我们可以通过把 Indicator 的颜色设为透明来隐藏它：
```xml
<android.support.design.widget.TabLayout
    android:id="@+id/tab_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:tabIndicatorColor="@android:color/transparent" />
```

在代码中，当 Tab 添加完毕后，替换成自定义的 Tab：
```java
TabLayout.Tab tab = tabLayout.getTabAt(i);
tab.setCustomView(R.layout.layout_tab);
TextView tv = tab.getCustomView().findViewById(R.id.text_view);
tv.setText(tab.getText());
```

并且还需要监听 Tab 的切换，控制指示线的显示隐藏：
```java
tabLayout.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
    @Override
    public void onTabSelected(TabLayout.Tab tab) {
        // TODO 刷新Tab
    }

    @Override
    public void onTabUnselected(TabLayout.Tab tab) {
        // TODO 刷新Tab
    }

    @Override
    public void onTabReselected(TabLayout.Tab tab) { }
});
```

用这种方法，什么样式都能实现了。但有个缺点是: 在 Tab 切换的时候，没有了指示线的移动动画。

### Support v28+ 属性配置
如果你使用的 SDK 版本是 28 或以上，并且需要将 Indicator 的宽度修改成和文字宽度一样，那么太棒了，现在你只需要给 TabLayout 配置一个属性就好了：
```xml
<android.support.design.widget.TabLayout
    android:id="@+id/tab_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:tabIndicatorFullWidth="false" />
```

当 tabIndicatorFullWidth 取 false 的时候，Indicator 的宽度会和文字的宽度一样，但这也意味着，当不同 Tab 里的文字宽度不一样时，Indicator 的宽度也会不一样，像下面这样。

//todo 贴图

如果设计要求 Indicator 的宽度必须固定，或者宽度要比文字短，那我们还要接着找别的解决方案。

### 使用 Drawable 样式
这种方案可能是最优雅的解决方案，使用也特别简单。
Indicator 是允许我们设置 drawable 来自定义样式的，比如添加圆角什么的。但无论什么样式，Indicator 依然是占满 Tab 宽度的。没关系，我们把它的背景设成透明，包含一个固定宽度的 shape 就好了，像这样：
```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <item android:gravity="center">
        <shape>

            <size
                android:width="16dp"
                android:height="4dp" />

            <corners android:radius="4dp" />

            <solid android:color="@color/colorAccent" />
        </shape>
    </item>
</layer-list>
```

然后在布局文件中配置 tabIndicator 属性：
```xml
<!-- app:tabIndicator 需要升级Support库到v28 -->

<android.support.design.widget.TabLayout
    android:id="@+id/tab_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:tabIndicatorHeight="10dp"
    app:tabIndicator="@drawable/tab_indicator" />
```

也可以在代码中设置：
```java
// setSelectedTabIndicator 需要升级Support库到v28
tabLayout.setSelectedTabIndicator(R.drawable.tab_indicator);
```

从上面这个例子还可以发现，使用这个方法，不仅可以在视觉上增加 Indicator 的左右边距，还可以增加它的上下边距。

好啦，其实就是配置一个 Drawable 文件这么简单。