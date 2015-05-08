---
layout: default
---
####引导页面的实现
>先讲讲实现的思路吧：一个Activity上的布局是一个FrameLayout，这个Layout上面是空白，一开始打开APP的时候就会见到这个空白的界面，然后生成一个全屏的Dialog，Dialog带着这个图片出现，遮住底下的Activity，然后在这个时候用Fragment把Activity的FrameLayout的内容换掉，搞好一切准备事项就把Dialog dismiss掉就可以了。

<pre>
public void show(final int ID_img,final int exit_way)
	{
		Runnable runnable=new Runnable()
		{
			@Override
			public void run()
			{
				// TODO Auto-generated method stub
				LinearLayout root=new LinearLayout(activity);
				root.setOrientation(LinearLayout.VERTICAL);
				root.setLayoutParams(new LinearLayout.LayoutParams(LinearLayout.LayoutParams.MATCH_PARENT,
						LinearLayout.LayoutParams.MATCH_PARENT));
				root.setBackgroundResource(ID_img);
				
				dialog=new Dialog(activity, android.R.style.Theme_Translucent_NoTitleBar);
				if((activity.getWindow().getAttributes().flags & WindowManager.LayoutParams.FLAG_FULLSCREEN)==WindowManager.LayoutParams.FLAG_FULLSCREEN)
				{
					dialog.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);
				}
				switch(exit_way)//设置dismiss时Dialog的动画
				{
				case FADE_OUT:
					dialog.getWindow().setWindowAnimations(R.style.fade_out);break;
				case SLIDE_LEFT:
					dialog.getWindow().setWindowAnimations(R.style.slide_left);break;
				case SLIDE_UP:
					dialog.getWindow().setWindowAnimations(R.style.slide_up);break;
					default:
						break;
				}
				
				dialog.setContentView(root);
				dialog.setCancelable(false);//取消BACK键的响应
				dialog.show();
			}
		};
		activity.runOnUiThread(runnable);
	}
</pre>
可以看得出引导页的show首先就是创一个root布局，然后把背景换成引导页图片，再创一个Dialog，检测下Activity是不是满屏的，满屏就把Dialog也设成满屏的，最后把Dialog的ContentView设置成root就显示出来就OK了。另外关于Dialog退出时的效果设置dialog.getWindow().setWindowAnimations(StyleID);
其中StyleID的写法:
</pre>
	&lt;!-- 引导页面用的Style --&gt;
    &lt;style name="fade_out" parent="android:Animation" mce_bogus="1"&gt;
        &lt;item name="android:windowExitAnimation"&gt;@anim/anim_fade_out&lt;/item&gt;
    &lt;/style&gt;
    &lt;style name="slide_left" parent="android:Animation" &gt;
        &lt;item name="android:windowExitAnimation"&gt;@anim/anim_slide_left&lt;/item&gt;
    &lt;/style&gt;
    &lt;style name="slide_up" parent="android:Animation"&gt;
        &lt;item name="android:windowExitAnimation"&gt;@anim/anim_slide_up&lt;/item&gt;
    &lt;/style&gt;
</pre>
还有那些Animation的res写法：
<pre>
&lt;set xmlns:android="http://schemas.android.com/apk/res/android"&gt;
&lt;alpha
        android:fromAlpha="1.0"
        android:toAlpha="0.0"
        android:duration="1000" /&gt;
        &lt;/set&gt;
</pre>
引导页的remove:

<pre>
public void remove()
	{
		if(dialog!=null && dialog.isShowing())
		{
			dialog.dismiss();
			dialog=null;
		}
	}
<pre>

最后在Activity里面的应用：

<pre>
@Override
	protected void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		//开启引导页面
		setContentView(R.layout.frame_main);//一开始的空白页面
		splashScreen=new SplashScreen(this);
		splashScreen.show(R.drawable.image_splash_background, SplashScreen.FADE_OUT);
		//使用MainFragment代替主界面
		mMainFragment=new MainFragment();
		getFragmentManager().beginTransaction().replace(R.id.frame_main, mMainFragment).commit();
		//注册Handler
		mhandler=new Handler()
		{
			@Override
			public void handleMessage(Message msg)
			{
				// TODO Auto-generated method stub
				if(msg.what==1)
				{
					splashScreen.remove();
				}
			}
		};
		getData();//获取数据的结束之后利用handler发送msg.what==1的消息告诉handler把引导页dismiss掉
	}
<pre>

