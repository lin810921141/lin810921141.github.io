---
layout: default
title: Preference的使用
---

##Preference的介绍
>Preference用于APP的设置，它会记录用户的设置，并且每次都实时更新UI，省下我们不少的功夫。不然我们如果想要做到同样的效果，我们用View来写好界面，然后还要保存一堆的SharePreference的值，来维护这个界面，而Preference就能自动帮我们完成

##Preference的使用
按照官网的教程：
1. Preference系统提供了CheckBoxPreference，ListPreference，EditTextPreference4种
2. 定义Preference在XML中，首先必须把XML文件保存在res/xml中，xml文件的根节点必须是&lt;PreferenceScreen&gt;
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

3. 创建分组，有2种方法，第一种是用PreferenceCategory：
<pre>
&lt;PreferenceCategory
     android:title="影视圈隐私"&gt;
        &lt;Preference
            android:title="好友隐私"&gt; 
        &lt;/Preference&gt;
&lt;/PreferenceCategory&gt;
</pre>