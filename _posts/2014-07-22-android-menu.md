---
layout: default
---

用XML写菜单：
菜单资源位于res/menu中，就3个标签&lt;menu&gt;,&lt;item&gt;,&lt;group&gt;,&lt;item&gt;代表的就是菜单项了&lt;item&gt;里面可以加&lt;menu&gt;形成子菜单，&lt;group&gt;把一组&lt;item&gt;包含起来，形成一个小组。下面是一个示例：
<pre>
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;menu xmlns:android="http://schemas.android.com/apk/res/android" &gt;
    &lt;item 
        android:title="字体大小"
        android:icon="@drawable/ic_launcher"&gt;
        &lt;menu &gt;
            &lt;group android:checkableBehavior="single"&gt;
                &lt;item 
                    android:id="@+id/font_10"
                    android:title="10号字" /&gt;
                &lt;item 
                    android:id="@+id/font_12"
                    android:title="12号字" /&gt;
                &lt;item 
                    android:id="@+id/font_14"
                    android:title="14号字" /&gt;
                &lt;item 
                    android:id="@+id/font_16"
                    android:title="16号字" /&gt;
            &lt;/group&gt;
        &lt;/menu&gt;
    &lt;/item&gt;
    
    &lt;item 
        android:id="@+id/plain"
        android:title="普通菜单项"/&gt;
    
    &lt;item 
        android:title="字体颜色"&gt;
        &lt;menu &gt;
            &lt;item 
                android:id="@+id/color_red"
                android:title="红色"/&gt;
            &lt;item
                android:id="@+id/color_green"
                android:title="绿色"/&gt;
            &lt;item 
                android:id="@+id/ccolor_blue"
                android:title="蓝色"/&gt;
        &lt;/menu&gt;
    &lt;/item&gt;

&lt;/menu&gt;
</pre>
-------------
<pre>
public boolean onCreateOptionsMenu(Menu menu)
	{
		MenuInflater inflater=new MenuInflater(this);
		inflater.inflate(R.menu.my_menu, menu);
		return super.onCreateOptionsMenu(menu);
	}
</pre>
-------------
<pre>
public boolean onOptionsItemSelected(MenuItem item)
	{
		switch(item.getItemId())
		{
		case R.id.font_10:
			editText.setTextSize((float) (20));item.setChecked(true);break;
		case R.id.font_12:
			editText.setTextSize((float) (24));item.setChecked(true);break;
		case R.id.font_14:
			editText.setTextSize((float) (28));item.setChecked(true);break;
		case R.id.font_16:
			editText.setTextSize((float) (32));item.setChecked(true);break;
		case R.id.color_red:
			editText.setTextColor(Color.RED);break;
		case R.id.color_green:
			editText.setTextColor(Color.GREEN);break;
		case R.id.ccolor_blue:
			editText.setTextColor(Color.BLUE);break;
			default:
				break;
		}
		return true;
	}
</pre>