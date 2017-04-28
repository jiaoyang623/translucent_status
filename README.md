# translucent_status

这个项目用于记录如何让activity全屏，隐藏ActionBar，显示statusbar，并背景透明。效果见末尾。
这个项目参考了Android源代码中[Launcher3](https://android.googlesource.com/platform/packages/apps/Launcher3)的代码编写而成，主要包括以下几部分：

1. 设置values/styles.xml，按照不同版本分成不同文件：
- values/styles.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <item name="windowActionBar">false</item>
        <item name="windowNoTitle">true</item>
        <item name="android:windowBackground">@android:color/transparent</item>
        <item name="android:colorBackgroundCacheHint">@null</item>
        <item name="android:windowShowWallpaper">true</item>
        <item name="android:windowNoTitle">true</item>
    </style>
</resources>

```

- values-v19/styles.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
    </style>
</resources>
```

- values-v21/styles.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="AppTheme" parent="Theme.AppCompat.NoActionBar">
        <item name="android:windowSoftInputMode">adjustResize</item>
        <item name="android:windowTranslucentStatus">false</item>
        <item name="android:windowTranslucentNavigation">false</item>
        <item name="android:windowDrawsSystemBarBackgrounds">true</item>
        <item name="android:statusBarColor">#00000000</item>
    </style>
</resources>
```

2. 设置AndroidManifest.xml，将主题设置成AppTheme
```xml
<activity
    android:name=".MainActivity"
    android:theme="@style/AppTheme">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

3. 设置在Activity代码中设置：
- activity_main.xml中设置
```xml
android:fitsSystemWindows="true"
```
- MainActivity.java中设置
```java
findViewById(R.id.root).setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
        | View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION | View.SYSTEM_UI_FLAG_LAYOUT_STABLE);
```

当然拉，到这里还没完。这时候系统会自动给内部view添加一个paddingTop和paddingBottom。如果想让内容向上放到状态栏后面，就得做点什么。我的做法是在系统设置padding之后再设置一遍。自定义RelativeLayout如下：

```java
@Override
public WindowInsets onApplyWindowInsets(WindowInsets insets) {
    WindowInsets i = super.onApplyWindowInsets(insets);
    setPadding(getPaddingLeft(), 0, getPaddingRight(), getPaddingBottom());
    return i;
}
```
即可。


效果如下：
- 无键盘
![preview1](https://github.com/jiaoyang623/translucent_status/blob/master/doc/preview1.png)
- 有键盘
![preview2](https://github.com/jiaoyang623/translucent_status/blob/master/doc/preview2.png)
