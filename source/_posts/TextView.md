---
title: Android TextViews
date: 2018-12-10 11:28:27
tags: [Android, Widget]
---

## Autosizing TextViews

使用Android 8.0（API级别26）及更高版本，可以让 `TextView`文本大小自动扩展或收缩，以根据其`TextView`特征和边界填充其布局 。此设置可以更轻松地使用动态内容优化不同屏幕上的文本大小。

Support Library 26.0完全支持 `TextView`在Android 8.0（API级别26）之前运行Android版本的设备上的自动调整功能。该库提供对Android 4.0（API级别14）及更高版本的支持。该`android.support.v4.widget` 软件包含`TextViewCompat`以向后兼容方式访问功能的类

<!--more-->
### 设置TextView自动调整大小
可以使用框架或支持库以 `TextView`编程方式或以XML格式设置自动调整大小。要设置XML属性，还可以使用 Android Studio中的“ **属性”**窗口。

有三种方法可以设置自动调整 `TextView`：[默认](https://developer.android.com/guide/topics/ui/look-and-feel/autosizing-textview#default), [粒度](https://developer.android.com/guide/topics/ui/look-and-feel/autosizing-textview#granularity), [预设尺寸](https://developer.android.com/guide/topics/ui/look-and-feel/autosizing-textview#preset-sizes)

**注**：如果设置在一个XML文件自动调整大小，不建议使用值“WRAP_CONTENT”为 `layout_width`或`layout_height`的属性`TextView`。它可能会产生意外的结果。

### 默认

默认设置允许`TextView`在水平轴和垂直轴上均匀地自动调整刻度。

- 要以Code方式定义默认设置，请调用 `setAutoSizeTextTypeWithDefaults(int autoSizeTextType) `方法。提供`AUTO_SIZE_TEXT_TYPE_NONE`关闭自动调整功能或`AUTO_SIZE_TEXT_TYPE_UNIFORM`均匀缩放水平轴和垂直轴。
- **注**：为统一缩放的默认尺寸`minTextSize = 12sp`， `maxTextSize = 112sp`以及 `granularity = 1px.`

- 要在XML中定义默认设置，请使用`android`命名空间并将`autoSizeTextType`属性设置为*none*或 *uniform*。
- ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <TextView
      android:layout_width="match_parent"
      android:layout_height="200dp"
      android:autoSizeTextType="uniform" />
  ```

#### 使用支持库

- 要通过支持库以编程方式定义默认设置，请调用该`TextViewCompat.setAutoSizeTextTypeWithDefaults(TextView textview, int autoSizeTextType)` 方法。提供`TextView`窗口小部件的实例和其中一种文本类型，例如 `TextViewCompat.AUTO_SIZE_TEXT_TYPE_NONE`或`TextViewCompat.AUTO_SIZE_TEXT_TYPE_UNIFORM`。
- 要通过支持库在XML中定义默认设置，请使用 `app`命名空间并将`autoSizeTextType` 属性设置为*none*或*uniform*。
- ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout
      xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      android:layout_width="match_parent"
      android:layout_height="match_parent">
  
    <TextView
        android:layout_width="match_parent"
        android:layout_height="200dp"
        app:autoSizeTextType="uniform" />
  </LinearLayout>
  ```

### 粒度
可以定义最小和最大文本大小的范围以及指定每个步骤大小的维度。的 `TextView`均匀的最小和最大尺寸的属性之间的范围内的鳞片。每个增量按粒度属性中设置的步长进行。

- 要以编程方式定义文本大小范围和维度，请调用该 `setAutoSizeTextTypeUniformWithConfiguration(int autoSizeMinTextSize, int autoSizeMaxTextSize, int autoSizeStepGranularity, int unit)` 方法。提供最大值，最小值，粒度值和任何`TypedValue`维度单位。
- 要在XML中定义一系列文本大小和维度，请使用`android`命名空间并设置以下属性：
  - 将 [`autoSizeText`](https://developer.android.com/reference/android/widget/TextView.html#attr_android:autoSizeText) 属性设置为*none*或*uniform*。*none* 是默认值，*uniform*可以 `TextView`在水平和垂直轴上*均匀*缩放。
  - 设置[`autoSizeMinTextSize`](https://developer.android.com/reference/android/widget/TextView.html#attr_android:autoSizeMinTextSize)， [`autoSizeMaxTextSize`](https://developer.android.com/reference/android/widget/TextView.html#attr_android:autoSizeMaxTextSize)和[`autoSizeStepGranularity`](https://developer.android.com/reference/android/widget/TextView.html#attr_android:autoSizeStepGranularity) 属性以定义自动调整大小的维度 `TextView`。
- ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <TextView
      android:layout_width="match_parent"
      android:layout_height="200dp"
      android:autoSizeTextType="uniform"
      android:autoSizeMinTextSize="12sp"
      android:autoSizeMaxTextSize="100sp"
      android:autoSizeStepGranularity="2sp" />
  ```

#### 使用支持库

- 要通过支持库以编程方式定义文本大小范围和维度，请调用该`TextViewCompat.setAutoSizeTextTypeUniformWithConfiguration(int autoSizeMinTextSize, int autoSizeMaxTextSize, int autoSizeStepGranularity, int unit)` 方法。提供最大值，最小值，粒度值和任何`TypedValue`维度单位。
- 要定义范围的文本大小，并通过支持库中的XML格式的尺寸，使用的`app`命名空间和设置 `autoSizeText`，`autoSizeMinTextSize`， `autoSizeMaxTextSize`，和 `autoSizeStepGranularity`布局XML文件中的属性。
- ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout
      xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      android:layout_width="match_parent"
      android:layout_height="match_parent">
  
    <TextView
        android:layout_width="match_parent"
        android:layout_height="200dp"
        app:autoSizeTextType="uniform"
        app:autoSizeMinTextSize="12sp"
        app:autoSizeMaxTextSize="100sp"
        app:autoSizeStepGranularity="2sp" />
  </LinearLayout>
  ```

### 预设尺寸

预设尺寸允许您指定`TextView`自动调整文本大小时选择的所有值 。

- 要使用预设大小以`TextView`编程方式设置自动调整大小 ，请调用该`setAutoSizeTextTypeUniformWithPresetSizes(int[] presetSizes, int unit)` 方法。为尺寸提供尺寸和任何`TypedValue` 尺寸单位的数组。
- 要使用预设大小设置 `TextView` XML的自动调整大小，请使用`android`命名空间并设置以下属性：
  - 将[`autoSizeText`](https://developer.android.com/reference/android/widget/TextView.html#attr_android:autoSizeText) 属性设置为*none*或*uniform*。*none* 是默认值，*uniform*可以 `TextView`在水平和垂直轴上*均匀*缩放。
  - 将[`autoSizePresetSizes`](https://developer.android.com/reference/android/widget/TextView.html#attr_android:autoSizePresetSizes) 属性设置 为预设大小的数组。要将数组作为资源访问，请在`res/values/arrays.xml` 文件中定义数组。
- ```xml
  <resources>
    <array name="autosize_text_sizes">
      <item>10sp</item>
      <item>12sp</item>
      <item>20sp</item>
      <item>40sp</item>
      <item>100sp</item>
    </array>
  </resources>
  ```

- ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <TextView
      android:layout_width="match_parent"
      android:layout_height="200dp"
      android:autoSizeTextType="uniform"
      android:autoSizePresetSizes="@array/autosize_text_sizes" />
  ```

#### 使用支持库

- 要使用预设大小`TextView`通过支持库以编程方式设置自动调整大小 ，请调用该`TextViewCompat.setAutoSizeTextTypeUniformWithPresetSizes(TextView textView, int[] presetSizes, int unit)` 方法。提供`TextView`类的实例，大小数组以及大小的任何`TypedValue`维度单位。
- 要使用预设大小`TextView`通过支持库设置XML的自动调整大小 ，请使用布局XML文件中的 `app`命名空间和set `autoSizeText`和 `autoSizePresetSizes`attributes。
- ```xml
  <resources>
    <array name="autosize_text_sizes">
      <item>10sp</item>
      <item>12sp</item>
      <item>20sp</item>
      <item>40sp</item>
      <item>100sp</item>
    </array>
  </resources>
  ```

- ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <LinearLayout
      xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      android:layout_width="match_parent"
      android:layout_height="match_parent">
  
    <TextView
        android:layout_width="match_parent"
        android:layout_height="200dp"
        app:autoSizeTextType="uniform"
        app:autoSizePresetSizes="@array/autosize_text_sizes" />
  </LinearLayout>
  ```



## XML中的字体

Android 8.0（API级别26）引入了一项新功能，即XML中的字体，它允许您将字体用作资源。您可以在`font`文件`res/font/`夹中添加文件以将字体捆绑为资源。这些字体在您的`R`文件中编译，并在Android Studio中自动提供。您可以借助新资源类型访问字体资源`font`。例如，要访问字体资源，请使用`@font/myfont`或`R.font.myfont`。

要在运行Android 4.1（API级别16）及更高版本的设备上使用字体XML功能，请使用支持库26.有关使用支持库的更多信息，请参阅 [使用支持库](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml#using-support-lib)部分。

要将字体添加为资源，请在Android Studio中执行以下步骤：

1. 右键单击res文件夹，然后转到“ 新建”>“Android资源目录”。将出现“ *新建资源目录”*窗口。
2. 在“资源类型”列表中，选择“ 字体”，然后单击“ 确定”。**注意**：资源目录的名称必须是 **font**

![图1添加字体资源目录](http://blog-1251678165.coscd.myqcloud.com/2018-12-27-055422.jpg)

3. 在字体文件夹中添加字体文件。下面的文件夹结构生成 `R.font.dancing_script`，`R.font.lobster`和 `R.font.typo_graphica`。

![在资源目录中添加字体文件](http://blog-1251678165.coscd.myqcloud.com/2018-12-27-055530.jpg)

4. 双击字体文件以在编辑器中预览文件的字体。

![预览字体文件](http://blog-1251678165.coscd.myqcloud.com/2018-12-27-055559.jpg)

### 创建font family
font family是一组字体文件及其样式和重量详细信息。在Android中，您可以创建一个新的字体系列作为XML资源，并将其作为单个单元访问，而不是将每个样式和权重作为单独的资源引用。通过这样做，系统可以根据您尝试使用的文本样式选择正确的字体。

要创建字体系列，请在Android Studio中执行以下步骤：

1. 右键单击该`font`文件夹，然后转到“ **新建”>“字体资源文件”**。将出现“ *新建资源文件”*窗口。
2. 输入文件名，然后单击“ **确定”**。新的字体资源XML在编辑器中打开。
3. 将每个字体文件，样式和权重属性包含在`<font` 元素中。以下XML说明了在字体资源XML中添加与字体相关的属性：

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <font-family xmlns:android="http://schemas.android.com/apk/res/android">
       <font
           android:fontStyle="normal"
           android:fontWeight="400"
           android:font="@font/lobster_regular" />
       <font
           android:fontStyle="italic"
           android:fontWeight="400"
           android:font="@font/lobster_italic" />
   </font-family>
   ```

### 在XML布局中使用字体
在`TextView`对象或样式中使用字体，可以是单个字体文件，也可以是字体系列中的字体。要`TextView`在样式中添加字体，请使用该 `fontFamily`属性。   **注意：**当您使用字体系列时， `TextView`交换机可以根据需要自行使用该系列中的字体文件。

#### 将字体添加到TextView
要为其设置字体`TextView`，请执行以下操作之一：

- 在布局XML文件中，将`fontFamily`属性设置为要访问的字体文件。

  ```xml
  <TextView
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:fontFamily="@font/lobster"/>
  ```

Android Studio布局预览（如图5最右侧窗格所示）允许您预览中的字体集`TextView`。

![在布局预览中预览字体](http://blog-1251678165.coscd.myqcloud.com/2018-12-27-060116.jpg)

#### 将字体添加到Style
打开`styles.xml`，并将`fontFamily` 属性设置为您要访问的字体文件。

```xml
<style name="customfontstyle" parent="@android:style/TextAppearance.Small">
    <item name="android:fontFamily">@font/lobster</item>
</style>
```

### 在Java/Kotlin中使用字体
要在Java/Kotlin中使用字体，请调用`getFont(int)`方法并提供要使用的字体 的资源标识符。此方法返回一个 `Typeface`对象。虽然系统从字体信息中为您选择最佳样式，但您可以使用该 `setTypeface(android.graphics.Typeface, int)` 方法设置具有特定样式的字体。

#### Kotin
```kotlin
val typeface = resources.getFont(R.font.myfont)
textView.typeface = typeface
```

#### Java
```java
Typeface typeface = getResources().getFont(R.font.myfont);
textView.setTypeface(typeface);
```

### 使用支持库
Support Library 26.0 在运行Android 4.1（API级别16）及更高版本的设备上提供对XML字体功能的支持。

**注意**：通过支持库在XML布局中声明字体系列时，请使用**app**命名空间来确保加载字体

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:app="http://schemas.android.com/apk/res-auto">
    <font app:fontStyle="normal" app:fontWeight="400" app:font="@font/myfont-Regular"/>
    <font app:fontStyle="italic" app:fontWeight="400" app:font="@font/myfont-Italic" />
</font-family>
```

在Java/Kotlin中使用字体，请调用 `ResourceCompat.getFont(Context, int)`方法并提供Context和资源标识符的实例。