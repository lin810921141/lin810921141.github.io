---
layout: default
title: Video��Controller
---
###About Controller
>Androidֱ�����ṩVideoView��MediaController�����ǣ����ǲ����ɣ������ǰ�Controller������VideoView�������һ��������.
������һ������֮���ҵ��˸�ͦ����Ŀ�Դ��Controller������Ҳ���㸴�ӣ����һ��в��ͽ̳�:
http://www.brightec.co.uk/blog/custom-android-media-controller
Github:https://github.com/brightec/ExampleMediaController


MediaControllerView�������ʵ���Ƿ�Ϊ��ʾ���߼�2�����֣��ȷ�������ʾ���֣��ȴ�setAnchorView���������������2���£�һ���Ǽ�¼mAnchor����һ�����ǻ�ȡһ��View��addView��ע�����������FrameLayout������Ŷ�����ﻹû��ʼ��Controller�ŵ�mAnchor����ȥ��
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
��makeControllerView()����������Կ�����ʵ����inflateһ��View�����ض��ѣ����ǿ����ʵ��޸ģ�ʹ֮���ص���������Ҫ��View��Ȼ����initControllerView(mRoot);��������View�ĸ������д��Ӧ����
>ע��Ŷ������Ĵ�������ͽ������޸ģ����������ж��Ƿ�ȫ�������ز�ͬ��View
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