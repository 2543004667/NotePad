# NotePad
# 基本功能
## NoteList中显示条目增加时间戳显示
### 要在标题下方加时间显示，就要在标题的TextView下再加一个时间的TextView。但是由于原应用列表item只需要一个标题，所以不需要用上别的布局，而我要多加一个时间TextView，就要把标题TextView和时间TextView放入垂直的线性布局。由于要美化UI，所以将TextView原字体颜色改为黑色。新加的时间TextView字体大小小于标题TextView。

```<!--添加一个垂直的线性布局-->
<LinearLayout  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <!--原标题TextView-->
    <TextView xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@android:id/text1"
        android:layout_width="match_parent"
        android:layout_height="?android:attr/listPreferredItemHeight"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:gravity="center_vertical"
        android:paddingLeft="5dip"
        android:singleLine="true"
        />
    <!--添加显示时间的TextView-->
    <TextView
        android:id="@+id/text1_time"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceSmall"
        android:paddingLeft="5dip"
        />```


