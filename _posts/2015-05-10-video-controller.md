---
layout: default
title: Video的Controller
---
###About Controller
>Android直接有提供VideoView和MediaController给我们，但是不自由，单单是把Controller设置在VideoView里面就是一件难事了.
经过我一番查找之后，找到了个挺不错的开源的Controller，代码也不算复杂，而且还有博客教程:
http://www.brightec.co.uk/blog/custom-android-media-controller
Github:https://github.com/brightec/ExampleMediaController


MediaControllerView这个类其实就是分为显示和逻辑2个部分，先分析下显示部分，先从setAnchorView看起，这个函数做了2件事，一个是记录mAnchor，另一个就是获取一个View来addView，注意下这个类是FrameLayout的子类哦，这里还没开始把Controller放到mAnchor里面去的
<pre>
public void setAnchorView(ViewGroup view) {
        mAnchor = view;
 
        FrameLayout.LayoutParams frameParams = new FrameLayout.LayoutParams(
                ViewGroup.LayoutParams.MATCH_PARENT,
                ViewGroup.LayoutParams.MATCH_PARENT
        );
 
        removeAllViews();
        View v = makeControllerView();
        addView(v, frameParams);
    }
</pre>
在makeControllerView()函数里面可以看到其实就是inflate一个View来返回而已，我们可以适当修改，使之返回的是我们想要的View，然后在initControllerView(mRoot);这个里面对View的各个组件写响应函数
>注意哦，下面的代码里面就进行了修改，我在这里判断是否全屏来返回不同的View
<pre>
protected View makeControllerView() {
        LayoutInflater inflate = (LayoutInflater) mContext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        //mRoot = inflate.inflate(R.layout.media_controller, null);
        if(mPlayer!=null)
        {
        	if(mPlayer.isFullScreen())
        	{
        		mRoot = inflate.inflate(R.layout.my_media_controller_fullscreen, null);
        	}
        	else {
				mRoot = inflate.inflate(R.layout.my_media_controller, null);
			}
        }

        initControllerView(mRoot);
 
        return mRoot;
    }
</pre>