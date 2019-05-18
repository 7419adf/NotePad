# NotePad
## 一：时间戳显示

### 截图：
![时间戳](https://github.com/7419adf/NotePad/blob/master/image/%E6%97%B6%E9%97%B4%E6%88%B3.png)

### 代码：
在NotePadProvider的insert函数中修改时间的显示格式：
``` 
        Long now = Long.valueOf(System.currentTimeMillis());
        Date date = new Date(now);
        SimpleDateFormat format = new SimpleDateFormat("yyyy.MM.dd HH:mm:ss");
        format.setTimeZone(TimeZone.getTimeZone("GMT+8:10"));
        String dateTime = format.format(date);
```

在noteslist_item.xml中增加一个用来显示时间的TextView：
``` 
<TextView
        android:id="@+id/text2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:textSize="20sp"
        android:background="@drawable/list_selector"/>
```

在NotesList的数据定义中增加修改时间：
``` 
private static final String[] PROJECTION = new String[] {
        NotePad.Notes._ID, // 0
        NotePad.Notes.COLUMN_NAME_TITLE, // 1
        NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,//在这里加入了修改时间的显示
};
```

在dataColumns,viewIDs这两个参数需要加入时间：
``` 
String[] dataColumns = {
        NotePad.Notes.COLUMN_NAME_TITLE,
        NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE
} ;

int[] viewIDs = { android.R.id.text1 ,R.id.text2};
```

因为每次编辑笔记后都会获取当前系统时间，更新时间戳并插入数据库，所以也要在NoteEdit中的updateNote函数中添加更改时间格式的代码：
``` 
 //更改时间格式
        Long now = Long.valueOf(System.currentTimeMillis());
        Date date = new Date(now);
        SimpleDateFormat format = new SimpleDateFormat("yyyy.MM.dd HH:mm:ss");
        format.setTimeZone(TimeZone.getTimeZone("GMT+8:10"));
        String dateTime = format.format(date);
```

## 二：笔记搜索

### 截图：
![笔记搜索](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E6%90%9C%E7%B4%A21.png)
![笔记搜索](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E6%90%9C%E7%B4%A22.png)

### 代码：
在list_options_menu.xml中新建一个查询的按钮：
``` 
 <item
        android:id="@+id/menu_search"
        android:icon="@drawable/search"
        android:title="@string/menu_search"
        android:showAsAction="always"
        />
```

在onOptionsItemSelected的switch (item.getItemId())中添加对应menu_search的case：
```
case R.id.menu_search:
    startActivity(new Intent(Intent.ACTION_SEARCH,getIntent().getData()));
    return true;
```

加入隐式Intent的跳转:
```
startActivity(new Intent(Intent.ACTION_SEARCH,getIntent().getData()));
```

然后在AndroidManifest.xml中加入相应的声名,实现由按钮跳转到搜索界面：
```
<activity
    android:name=".NoteSearch"
    android:label="NoteSearch"
    >

    <intent-filter>
        <action android:name="android.intent.action.NoteSearch" />
        <action android:name="android.intent.action.SEARCH" />
        <action android:name="android.intent.action.SEARCH_LONG_PRESS" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
        <!--1.vnd.android.cursor.dir代表返回结果为多列数据-->
        <!--2.vnd.android.cursor.item 代表返回结果为单列数据-->
    </intent-filter>
</activity>
```

以下是搜索功能从数据库查询匹配条件的代码：
```
String selection = NotePad.Notes.COLUMN_NAME_TITLE + " Like ? ";//查询条件
String[] selectionArgs = { "%"+s+"%" };//查询条件参数，配合selection参数使用,%通配多个字符

//查询数据库中的内容,当我们使用 SQLiteDatabase.query()方法时，就会得到Cursor对象， Cursor所指向的就是每一条数据。
//managedQuery(Uri, String[], String, String[], String)等同于Context.getContentResolver().query()
Cursor cursor = managedQuery(
        getIntent().getData(),            // Use the default content URI for the provider.用于ContentProvider查询的URI，从这个URI获取数据
        PROJECTION,                       // Return the note ID and title for each note. and modifcation date.用于标识uri中有哪些columns需要包含在返回的Cursor对象中
        selection,                        // 作为查询的过滤参数，也就是过滤出符合selection的数据，类似于SQL的Where语句之后的条件选择
        selectionArgs,                    // 查询条件参数，配合selection参数使用
        NotePad.Notes.DEFAULT_SORT_ORDER  // Use the default sort order.查询结果的排序方式，按照某个columns来排序，例：String sortOrder = NotePad.Notes.COLUMN_NAME_TITLE
);
```

写一个适配器，把游标中的数据映射到ListView中:
```
//一个简单的适配器，将游标中的数据映射到布局文件中的TextView控件或者ImageView控件中
String[] dataColumns = { NotePad.Notes.COLUMN_NAME_TITLE ,  NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE };
int[] viewIDs = { android.R.id.text1 , R.id.text2 };
SimpleCursorAdapter adapter = new SimpleCursorAdapter(
        this,                   //context:上下文
        R.layout.noteslist_item,         //layout:布局文件，至少有int[]的所有视图
        cursor,                          //cursor：游标
        dataColumns,                     //from：绑定到视图的数据
        viewIDs                          //to:用来展示from数组中数据的视图
                                         //flags：用来确定适配器行为的标志，Android3.0之后淘汰
);
setListAdapter(adapter);
return true;
```


## 三：UI美化

### 截图：
![UI美化](https://github.com/7419adf/NotePad/blob/master/image/UI%E7%BE%8E%E5%8C%96.png)

### 代码：
先把界面主题改为白色：
```
 android:theme="@android:style/Theme.Holo.Light"
```

在drawable中添加一个list_selector.xml，用于显示list中item的背景颜色，以及单击选中时的颜色：
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_focused="true" android:drawable="@color/colorAccent"/>
    <item android:state_pressed="true" android:drawable="@color/colorPrimary" />
    <item android:drawable="@color/blue"/>
</selector>
```

并从网上找一些编辑及查找的图标，来美化界面。

## 四：字体大小、颜色改变

### 截图：
![字体大小改变1](https://github.com/7419adf/NotePad/blob/master/image/%E5%AD%97%E4%BD%93%E5%A4%A7%E5%B0%8F%E6%94%B9%E5%8F%981.png)
![字体大小改变2](https://github.com/7419adf/NotePad/blob/master/image/%E5%AD%97%E4%BD%93%E5%A4%A7%E5%B0%8F%E6%94%B9%E5%8F%982.png)
![字体颜色改变](https://github.com/7419adf/NotePad/blob/master/image/%E5%AD%97%E4%BD%93%E9%A2%9C%E8%89%B2%E6%94%B9%E5%8F%98.png)

### 代码：
在editor_options_menu.xml中添加对应的菜单：
```
<item
    android:id="@+id/font_size"
    android:title="@string/font_size">
    <!--子菜单-->
    <menu>
        <!--定义一组单选菜单项-->
        <group>
            <!--定义多个菜单项-->
            <item
                android:id="@+id/font_10"
                android:title="@string/font10"
                />

            <item
                android:id="@+id/font_16"
                android:title="@string/font16" />
            <item
                android:id="@+id/font_20"
                android:title="@string/font20" />
        </group>
    </menu>
</item>

<item
    android:title="@string/font_color"
    android:id="@+id/font_color"
    >
    <menu>
        <!--定义一组普通菜单项-->
        <group>
            <!--定义两个菜单项-->
            <item
                android:id="@+id/red_font"
                android:title="@string/red_title" />
            <item
                android:title="@string/black_title"
                android:id="@+id/black_font"/>
        </group>
    </menu>
</item>
```

在NoteEditor的onOptionsItemSelected中添加相应的case:
```
case R.id.font_10:

    mText.setTextSize(20);
    Toast toast =Toast.makeText(NoteEditor.this,"修改成功",Toast.LENGTH_SHORT);
    toast.show();
    finish();
    break;

case R.id.font_16:
    mText.setTextSize(32);
    Toast toast2 =Toast.makeText(NoteEditor.this,"修改成功",Toast.LENGTH_SHORT);
    toast2.show();
    finish();
    break;
case R.id.font_20:
    mText.setTextSize(40);
    Toast toast3 =Toast.makeText(NoteEditor.this,"修改成功",Toast.LENGTH_SHORT);
    toast3.show();
    finish();
    break;
case R.id.red_font:
    mText.setTextColor(Color.RED);
    Toast toast4 =Toast.makeText(NoteEditor.this,"修改成功",Toast.LENGTH_SHORT);
    toast4.show();
    finish();
    break;
case R.id.black_font:
    mText.setTextColor(Color.BLACK);
    Toast toast5 =Toast.makeText(NoteEditor.this,"修改成功",Toast.LENGTH_SHORT);
    toast5.show();
    finish();
    break;
```
## 五：笔记导出

### 截图：
![笔记导出1](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E5%AF%BC%E5%87%BA1.png)
![笔记导出2](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E5%AF%BC%E5%87%BA2.png)
![笔记导出3](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E5%AF%BC%E5%87%BA3.png)
![笔记导出4](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E5%AF%BC%E5%87%BA4.png)

### 代码：
在editor_options_menu.xml中添加一个item：
```
<item android:id="@+id/menu_output"
    android:title="@string/menu_output" />
```

像之前一样在NoteEditor中onOptionsItemSelected()方法里的switch加一个case：
```
//导出笔记选项
   case R.id.menu_output:
        outputNote();
        break;
```

以下时outputNote函数：
```
//跳转导出笔记的activity，将uri信息传到新的activity
    private final void outputNote() {
        Intent intent = new Intent(null,mUri);
        intent.setClass(NoteEditor.this,OutputText.class);
        NoteEditor.this.startActivity(intent);
    }
```

创建OutputText的Acitvity:
```
public class OutputText extends Activity {
   //要使用的数据库中笔记的信息
    private static final String[] PROJECTION = new String[] {
            NotePad.Notes._ID, // 0
            NotePad.Notes.COLUMN_NAME_TITLE, // 1
            NotePad.Notes.COLUMN_NAME_NOTE, // 2
            NotePad.Notes.COLUMN_NAME_CREATE_DATE, // 3
            NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 4
    };
    //读取出的值放入这些变量
    private String TITLE;
    private String NOTE;
    private String CREATE_DATE;
    private String MODIFICATION_DATE;
    //读取该笔记信息
    private Cursor mCursor;
    //导出文件的名字
    private EditText mName;
    //NoteEditor传入的uri，用于从数据库查出该笔记
    private Uri mUri;
    //关于返回与保存按钮的一个特殊标记，返回的话不执行导出，点击按钮才导出
    private boolean flag = false;
    private static final int COLUMN_INDEX_TITLE = 1;
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.output_text);
        mUri = getIntent().getData();
        mCursor = managedQuery(
                mUri,        // The URI for the note that is to be retrieved.
                PROJECTION,  // The columns to retrieve
                null,        // No selection criteria are used, so no where columns are needed.
                null,        // No where columns are used, so no where values are needed.
                null         // No sort order is needed.
        );
        mName = (EditText) findViewById(R.id.output_name);
    }
    @Override
    protected void onResume(){
        super.onResume();
        if (mCursor != null) {
            // The Cursor was just retrieved, so its index is set to one record *before* the first
            // record retrieved. This moves it to the first record.
            mCursor.moveToFirst();
            //编辑框默认的文件名为标题，可自行更改
            mName.setText(mCursor.getString(COLUMN_INDEX_TITLE));
        }
    }
    @Override
    protected void onPause() {
        super.onPause();
        if (mCursor != null) {
        //从mCursor读取对应值
            TITLE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_TITLE));
            NOTE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_NOTE));
            CREATE_DATE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_CREATE_DATE));
            MODIFICATION_DATE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE));
            //flag在点击导出按钮时会设置为true，执行写文件
            if (flag == true) {
                write();
            }
            flag = false;
        }
    }
    public void OutputOk(View v){
        flag = true;
        finish();
    }
    private void write()
    {
        try
        {
            // 如果手机插入了SD卡，而且应用程序具有访问SD的权限
            if (Environment.getExternalStorageState().equals(
                    Environment.MEDIA_MOUNTED)) {
                // 获取SD卡的目录
                File sdCardDir = Environment.getExternalStorageDirectory();
                //创建文件目录
                File targetFile = new File(sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt");
                //写文件
                PrintWriter ps = new PrintWriter(new OutputStreamWriter(new FileOutputStream(targetFile), "UTF-8"));
                ps.println(TITLE);
                ps.println(NOTE);
                ps.println("创建时间：" + CREATE_DATE);
                ps.println("最后一次修改时间：" + MODIFICATION_DATE);
                ps.close();
                Toast.makeText(this, "保存成功,保存位置：" + sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt", Toast.LENGTH_LONG).show();
            }
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
    }
}
```

并为此activity添加一个相应的布局：
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:paddingLeft="6dip"
    android:paddingRight="6dip"
    android:paddingBottom="3dip">
    <EditText android:id="@+id/output_name"
        android:maxLines="1"
        android:layout_marginTop="2dp"
        android:layout_marginBottom="15dp"
        android:layout_width="wrap_content"
        android:ems="25"
        android:layout_height="wrap_content"
        android:autoText="true"
        android:capitalize="sentences"
        android:scrollHorizontally="true" />
    <Button android:id="@+id/output_ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="right"
        android:text="@string/output_ok"
        android:onClick="OutputOk" />
</LinearLayout>
```

因为要在sd卡中写入数据，所以要获得相应的权限：
```
<!-- 在SD卡中创建与删除文件权限 -->
    <permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
    <!-- 向SD卡写入数据权限 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
