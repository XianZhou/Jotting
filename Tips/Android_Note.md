# A Little Android

## 1. Layout
文件位置：```/app/src/main/res/layout```
### 1.1 LinearLayout
```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <LinearLayout
        android:layout_width="0pt"
        android:layout_height="match_parent"
        android:layout_weight="1"
        android:background="#EFA90D"
        android:orientation="vertical">

    </LinearLayout>

    <LinearLayout
        android:layout_width="0pt"
        android:layout_height="match_parent"
        android:layout_weight="1"
        android:background="#059341"
        android:orientation="vertical">

    </LinearLayout>
</LinearLayout>
```
* 两种布局 **horizontal** & **vertical**
* 改变宽度的因素  ```android:layout_width``` & ```android:layout_weight``` <br/>
最后控件的宽度 = 控件本身宽度 + 权重比例分配宽度<br/>
参考： http://blog.csdn.net/itermeng/article/details/52159997

## 2. build.gradle
gradle是一个项目构建工具，不同于Maven基于XML文件来完成配置，Gradle使用特定的语言（DSL）来声明配置 <br/>
参考： https://blog.csdn.net/small_mouse0/article/details/58596098
