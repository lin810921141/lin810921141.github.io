---
layout: default
title: Preference的使用
---

##Preference的介绍
>Preference用于APP的设置，它会记录用户的设置，并且每次都实时更新UI，省下我们不少的功夫。不然我们如果想要做到同样的效果，我们用View来写好界面，然后还要保存一堆的SharePreference的值，来维护这个界面，而Preference就能自动帮我们完成

##Preference的使用
* Preference系统提供了CheckBoxPreference，ListPreference，EditTextPreference4种
* 定义Preference在XML中，首先必须把XML文件保存在res/xml中，xml文件的根节点必须是&lt;PreferenceScreen&gt;
例如：
<pre>
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;CheckBoxPreference
        android:key="pref_sync"
        android:title="@string/pref_sync"
        android:summary="@string/pref_sync_summ"
        android:defaultValue="true" /&gt;
    &lt;ListPreference
        android:dependency="pref_sync"
        android:key="pref_syncConnectionType"
        android:title="@string/pref_syncConnectionType"
        android:dialogTitle="@string/pref_syncConnectionType"
        android:entries="@array/pref_syncConnectionTypes_entries"
        android:entryValues="@array/pref_syncConnectionTypes_values"
        android:defaultValue="@string/pref_syncConnectionTypes_default" /&gt;
&lt;/PreferenceScreen&gt;
</pre>
其中android:key对应于SharePreferences中的key值
android:title对应于标题
android:defaultVaule对应默认值

* 创建分组，有2种方法，第一种是用PreferenceCategory：
<pre>
&lt;PreferenceCategory
     android:title="影视圈隐私"&gt;
        &lt;Preference
            android:title="好友隐私"&gt; 
        &lt;/Preference&gt;
&lt;/PreferenceCategory&gt;
</pre>
第二种就是用PreferenceScreen:
<pre>
&lt;PreferenceScreen  xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;!-- opens a subscreen of settings --&gt;
    &lt;PreferenceScreen
        android:key="button_voicemail_category_key"
        android:title="@string/voicemail"
        android:persistent="false"&gt;
        &lt;ListPreference
            android:key="button_voicemail_provider_key"
            android:title="@string/voicemail_provider" ... /&gt;
        &lt;!-- opens another nested subscreen --&gt;
        &lt;PreferenceScreen
            android:key="button_voicemail_setting_key"
            android:title="@string/voicemail_settings"
            android:persistent="false"&gt;
            ...
        &lt;/PreferenceScreen&gt;
        &lt;RingtonePreference
            android:key="button_voicemail_ringtone_key"
            android:title="@string/voicemail_ringtone_title"
            android:ringtoneType="notification" ... /&gt;
        ...
    &lt;/PreferenceScreen&gt;
    ...
&lt;/PreferenceScreen&gt;
</pre>

* 使用Intent，当按Preference时可以触发Intent跳转
<pre>
&lt;Preference android:title="@string/prefs_web_page" &gt;
    &lt;intent android:action="android.intent.action.VIEW"
            android:data="http://www.example.com" /&gt;
&lt;/Preference&gt;
</pre>
当然还能跳转到其他Activity去，android:targetClass，android:targetPackage

##Activity如何显示Preference
首先Activity必须是继承自PreferenceActivity
<pre>
public class SettingsActivity extends PreferenceActivity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        addPreferencesFromResource(R.xml.preferences);
    }
}
</pre>

##读取Preferences
我们知道Preference能帮我们自动联系SharePreference，设置好的Setting，当我们需要读取用户的设置时应该怎做？
<pre>
SharedPreferences sharedPref = PreferenceManager.getDefaultSharedPreferences(this);
String syncConnPref = sharedPref.getString(SettingsActivity.KEY_PREF_SYNC_CONN, "");
</pre>

##监听Preference
<pre>
@Override
protected void onResume() {
    super.onResume();
    getPreferenceScreen().getSharedPreferences()
            .registerOnSharedPreferenceChangeListener(this);
}

@Override
protected void onPause() {
    super.onPause();
    getPreferenceScreen().getSharedPreferences()
            .unregisterOnSharedPreferenceChangeListener(this);
}
</pre>
<pre>
public void onSharedPreferenceChanged(SharedPreferences sharedPreferences,
        String key) {
        if (key.equals(KEY_PREF_SYNC_CONN)) {
            Preference connectionPref = findPreference(key);
            // Set summary to be the user-description for the selected value
            connectionPref.setSummary(sharedPreferences.getString(key, ""));
        }
    }
</pre>

#自定义进阶  
 
###主题的影响  
 不同的主题Theme都会影响到Preference的样式  
android:theme="@android:style/Theme.Holo.Light"  
![](/images/preference2.png)  
android:theme="@android:style/Theme.Light"  
![](/images/preference1.png)  

###自定义的PreferenceActivity的布局  
PreferenceActivity是继承自ListActivity，之前使用PreferenceActivity时都是直接的addPreferencesFromResource(R.xml.test);  
但是这样做的话就会造成这个Activity都是设置，我想插个图片在底部卖卖广告都不行呢，所以可以做个自定义的layout给PreferenceActivity用  
<pre>
&lt;RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.example.preferenceactivity.MainActivity" &gt;

    &lt;ListView 
        android:id="@android:id/list"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"&gt;
        
    &lt;/ListView&gt;
    &lt;ImageView 
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/ic_launcher"
        android:layout_below="@android:id/list"
        android:layout_centerHorizontal="true"/&gt;
    
&lt;/RelativeLayout&gt;
</pre>
唯一要保证的就是这个layout里面有一个listView，id要等于android:id/list才行


###自定义的PreferenceCategory
继承PreferenceCategory之后在onBindView函数中处理就可以
<pre>
public class MyPreferenceCategory extends PreferenceCategory
{
	@Override
	protected void onBindView(View view)
	{
		// TODO Auto-generated method stub
		super.onBindView(view);
		view.setLayoutParams(new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, 40));
		if(view instanceof TextView)
		{
			((TextView) view).setGravity(Gravity.BOTTOM);
		}
	} 
}
</pre>
事实上PreferenceCategory就是一个TextView而已，可以查看源码的layout,位于frameworks/base/core/res/layout/preference_category.xml
<pre>
&lt;TextView xmlns:android="http://schemas.android.com/apk/res/android"
    style="?android:attr/listSeparatorTextViewStyle"
    android:id="@+android:id/title"
/&gt;
</pre>
所以理论上你可以在onBindView函数返回的那个View中做你任何想要的处理，改变背景颜色啦，改变字体大小，字体颜色之类都可以。  

