# NotePad
## 一：时间戳显示

### 截图：
![时间戳](https://github.com/7419adf/NotePad/blob/master/image/%E6%97%B6%E9%97%B4%E6%88%B3.png)

### 代码：
在NotePadProvider的insert函数中修改时间的显示格式：
``` 
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

## 二：笔记搜索

![笔记搜索](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E6%90%9C%E7%B4%A21.png)
![笔记搜索](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E6%90%9C%E7%B4%A22.png)

## 三：UI美化

![UI美化](https://github.com/7419adf/NotePad/blob/master/image/UI%E7%BE%8E%E5%8C%96.png)


## 四：字体大小、颜色改变

![字体大小改变1](https://github.com/7419adf/NotePad/blob/master/image/%E5%AD%97%E4%BD%93%E5%A4%A7%E5%B0%8F%E6%94%B9%E5%8F%981.png)
![字体大小改变2](https://github.com/7419adf/NotePad/blob/master/image/%E5%AD%97%E4%BD%93%E5%A4%A7%E5%B0%8F%E6%94%B9%E5%8F%982.png)
![字体颜色改变](https://github.com/7419adf/NotePad/blob/master/image/%E5%AD%97%E4%BD%93%E9%A2%9C%E8%89%B2%E6%94%B9%E5%8F%98.png)

## 五：笔记导出

![笔记导出1](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E5%AF%BC%E5%87%BA1.png)
![笔记导出2](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E5%AF%BC%E5%87%BA2.png)
![笔记导出3](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E5%AF%BC%E5%87%BA3.png)
![笔记导出4](https://github.com/7419adf/NotePad/blob/master/image/%E7%AC%94%E8%AE%B0%E5%AF%BC%E5%87%BA4.png)
