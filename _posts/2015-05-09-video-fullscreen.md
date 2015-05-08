---
layout: default
title: 视频全屏与非全屏的切换
---
###怎样实现视频的全屏与非全屏切换
首先视频播放的途中决不能因为切换全屏而重新加载，所以首先遇到的问题就是竖屏，横屏的切换，并且不能令Activity重构
在&lt;activity&gt;标签中防止重构需要加上：
>android:configChanges="orientation|screenSize"
把activity设为全屏显示：
>android:theme="@android:style/Theme.Holo.Light.NoActionBar.Fullscreen"
再来就是在代码中强制转换横竖屏的方法：
<pre>
//设为横屏
setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
//设为竖屏
setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
</pre>

###接着是竖屏的时候，Activity还会显示其他内容，在这个前提下如何实现全屏与非全屏的切换
>原理很简单，就是把其他内容隐藏起来就行，变成竖屏的时候就显示出来，横屏就设为View.GONE就可以了,
另外改变一下播放视频的SurfaceView的LayoutParams就可以了。
<pre>
public void toggleFullScreen() 
	{ 
		flag=!flag;
		if(flag)
		{
			scrollView.setVisibility(View.GONE);
			surfaceContainer.setLayoutParams(landParams);
			
			setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
		}else {
			
			scrollView.setVisibility(View.VISIBLE);
			surfaceContainer.setLayoutParams(portpParams);
			
			setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
			
		}
	} 
</pre>
