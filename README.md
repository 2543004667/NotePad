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
        />
```
### 要将时间显示，首先要在PROJECTION中定义显示的时间，原应用有两种时间，我选择修改时间作为显示的时间。        
![](https://i.loli.net/2019/05/21/5ce410983940153814.jpg)
### Cursor不变，在dataColumns，viewIDs中补充时间部分：
![](http://chuantu.xyz/t6/702/1558259179x1954578459.png)
### 下面是效果图
![](http://chuantu.xyz/t6/702/1558259205x1954578459.png)

## 添加笔记查询功能（根据标题查询）
### 找到菜单的xml文件，list_options_menu.xml，添加一个搜索的item，搜索图标用安卓自带的图标，设为总是显示
![](http://chuantu.xyz/t6/702/1558259467x1954578459.png)
### 在NoteList中找到onOptionsItemSelected方法，在switch中添加搜索的case语句
![](http://chuantu.xyz/t6/702/1558259601x1954578459.png)
### 先布局搜索页面，在layout中新建布局文件note_search_list.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <SearchView
        android:id="@+id/search_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:iconifiedByDefault="false"
        android:queryHint="输入搜索内容..."
        android:layout_alignParentTop="true">
    </SearchView>
    <ListView
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
    </ListView>
</LinearLayout>
```
### 要动态地显示搜索结果，就要对SearchView文本变化设置监听，NoteSearch除了要继承ListView外还要实现SearchView.OnQueryTextListener接口
```
public class NoteSearch extends ListActivity  implements SearchView.OnQueryTextListener {
    private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            //扩展 显示时间 颜色
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 2
            NotePad.Notes.COLUMN_NAME_BACK_COLOR
    };
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.note_search_list);
        Intent intent = getIntent();
        if (intent.getData() == null) {
            intent.setData(NotePad.Notes.CONTENT_URI);
        }
        SearchView searchview = (SearchView)findViewById(R.id.search_view);
        //为查询文本框注册监听器
        searchview.setOnQueryTextListener(NoteSearch.this);  
    }
    @Override
    public boolean onQueryTextSubmit(String query) {
        return false;
    }
    @Override
    public boolean onQueryTextChange(String newText) {
        String selection = NotePad.Notes.COLUMN_NAME_TITLE + " Like ? ";
        String[] selectionArgs = { "%"+newText+"%" };
        Cursor cursor = managedQuery(
                getIntent().getData(),            // Use the default content URI for the provider.
                PROJECTION,                       // Return the note ID and title for each note. and modifcation date
                selection,                        // 条件左边
                selectionArgs,                    // 条件右边
                NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.
        );
        String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };
        int[] viewIDs = { android.R.id.text1 , R.id.text1_time };
        MyCursorAdapter adapter = new MyCursorAdapter(
                this,
                R.layout.noteslist_item,
                cursor,
                dataColumns,
                viewIDs
        );
        setListAdapter(adapter);
        return true;
    }
    @Override
    protected void onListItemClick(ListView l, View v, int position, long id) {
        // Constructs a new URI from the incoming URI and the row ID
        Uri uri = ContentUris.withAppendedId(getIntent().getData(), id);
        // Gets the action from the incoming Intent
        String action = getIntent().getAction();
        // Handles requests for note data
        if (Intent.ACTION_PICK.equals(action) || Intent.ACTION_GET_CONTENT.equals(action)) {
            // Sets the result to return to the component that called this Activity. The
            // result contains the new URI
            setResult(RESULT_OK, new Intent().setData(uri));
        } else {
            // Sends out an Intent to start an Activity that can handle ACTION_EDIT. The
            // Intent's data is the note ID URI. The effect is to call NoteEdit.
            startActivity(new Intent(Intent.ACTION_EDIT, uri));
        }
    }
}
```
### 最后要在AndroidManifest.xml注册NoteSearch
![](http://chuantu.xyz/t6/702/1558260485x1954578459.png)
### 最终效果图
![](http://chuantu.xyz/t6/702/1558260585x1709417261.png)
![](http://chuantu.xyz/t6/702/1558260606x1709417261.png)

# 扩展功能
## 我暂时实现了笔记排序+简单的UI美化功能
## 笔记排序
### 笔记排序相对来说简单，只要把Cursor的排序参数变换下就可以了。在菜单文件list_options_menu.xml中添加
```
<item
    android:id="@+id/menu_sort"
    android:title="@string/menu_sort"
    android:icon="@android:drawable/ic_menu_sort_by_size"
    android:showAsAction="always" >
    <menu>
        <item
            android:id="@+id/menu_sort1"
            android:title="@string/menu_sort1"/>
        <item
            android:id="@+id/menu_sort2"
            android:title="@string/menu_sort2"/>
        <item
            android:id="@+id/menu_sort3"
            android:title="@string/menu_sort3"/>
        </menu>
    </item>
```
### 在NoteList菜单switch下添加case
![](http://chuantu.xyz/t6/702/1558260857x1709417261.png)
### 因为排序会多次使用到cursor，adapter，所以将adapter,cursor,dataColumns,viewIDs定义在函数外类内
![](http://chuantu.xyz/t6/702/1558260923x1954578459.png)
### 最终效果图（颜色排序暂未实现）
![](http://chuantu.xyz/t6/702/1558260923x1954578459.png)
## UI美化
### UI美化主要是让NoteList和NoteSearch每条笔记都有背景色，并且能保存。要做到保存颜色的数据，最直接的办法就是在数据库中添加一个颜色的字段，在这之前在NotePad契约类中添加
![](http://chuantu.xyz/t6/702/1558261207x1954578459.png)
### 创建数据库表地方添加颜色的字段
![](http://chuantu.xyz/t6/702/1558261257x1954578459.png)
### 最终效果图
![](http://chuantu.xyz/t6/702/1558261472x1954578459.png)






