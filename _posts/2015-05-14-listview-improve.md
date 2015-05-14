---
layout: default
title: listView优化+图片缓存+异步加载图片回调显示
---

##ListView的优化
这个优化是很普遍的了，主要就是表现在Adapter的getView函数里面，View convertView参数的利用
<pre>
@Override
	public View getView(int position, View convertView, ViewGroup parent)
	{
		// TODO Auto-generated method stub
		final Holder holder;
		if(convertView==null)
		{
			convertView=LayoutInflater.from(mContext).inflate(R.layout.listview_item,null);
			holder=new Holder();
			holder.img=(ImageView) convertView.findViewById(R.id.item_img);
			holder.heading=(TextView) convertView.findViewById(R.id.item_heading);
			holder.texting=(TextView) convertView.findViewById(R.id.item_texting);
			convertView.setTag(holder);
		}else {
			holder=(Holder) convertView.getTag();
		}
		holder.heading.setText(infos.get(position).heading);
		holder.texting.setText(infos.get(position).texting);
		holder.img.setImageBitmap(null);
		
		Bitmap bmp=asyncBitmapLoader.loadBitmap(holder.img, MainActivity.IP+"images/"+infos.get(position).img, new ImageCallBack()
		{

			@Override
			public void imageLoad(ImageView imageView, Bitmap bmp)
			{
				// TODO Auto-generated method stub
				holder.img.setImageBitmap(bmp);
			}
			
		});
		if(bmp!=null)
		{
			holder.img.setImageBitmap(bmp);
		}
		
		return convertView;
	}
	private class Holder
	{
		public ImageView img;
		public TextView texting,heading;
	}
</pre>

##图片缓存
ListView每个Item都带图片，显然每次都从服务器下载下来会显得费时又费流量，是很不好的做法。可以把已经下载下来的图片缓存到内存中，或者是存到SD卡中，不过我作为平时的Android的使用者，我都不喜欢一些APP擅作主张占了我的SD卡创建一些莫名其妙的文件夹。用的是软引用技术，软引用引用的图片在遇到内存不足的时候，图片对象会被销毁掉，所以这种方式下就不会引起OOM问题了。[软引用的一些具体介绍]http://soft.chinabyte.com/database/149/12485149.shtml)
<pre>

</pre>

##异步加载图片的回调
从上面可以看出开子线程下载图片之后，就马上返回一个null，所以getView那边实际上拿到的是一个null值。如何在下载完图片通知UI去更新呢，用到的当然就是handler了，不过这次使用handler的方式确实诡异得很，和一般学习Handler的例子很不同，平时接触handler的时候他都是在Activity里面，就是说它的生命周期跟随Activity那么长，而且handleMessage里面都是处理一些ContentView的组件成员，看起来很正常，毫无疑问的使用方式。但是这里涉及到ListView里面众多的Item，而且要更新的UI对象只有在getView里面才拿得到，情况很特别。

整个AsyncBitmapLoader.java
<pre>
public class AsyncBitmapLoader
{
	private Map<String, SoftReference<Bitmap>> imgCacheMap;//缓存Map
	private boolean isAllowDownloadbmp=true;//判断是否允许去下载图片，这里又涉及到另一个ListView的优化，假设你ListView有100项，每一项都要加载一张图片，当ListView一显示出来，用户就使劲往下拉，拉到100项那里去，那不就运行了100次getView了么，难道任由ListView那次都去开线程去下图片么？肯定不行呀，100个线程可不是闹着玩的，而且你下载中间那么多图片又不会马上显示给用户看，很可能一会儿就因内存满了而被GC回收掉，那不教白费力气了么，所以只有当用户停止了滑动的时候才会允许加载图片
	public AsyncBitmapLoader()
	{
		imgCacheMap=new HashMap<>();
	}
	//参数imageView用于更新UI,imageURL用于下载图片，imageCallBack是一个回调接口Interface
	public Bitmap loadBitmap(final ImageView imageView,final String imageURL, final ImageCallBack imageCallBack) 
	{
		//检查缓存map中时候有保存着图片
		if(imgCacheMap.containsKey(imageURL))
		{
			Bitmap bmp=imgCacheMap.get(imageURL).get();
			if(bmp!=null)
			{
				return bmp;
			}else {
				imgCacheMap.remove(imageURL);
			}
		}
		//如果有缓存就早return了，所以运行到这里肯定是没有缓存的情况，下载图片呗
		if (isAllowDownloadbmp)
		{
		//创建一个handler，处理信息的做法就是调用callback接口的imageLoad函数
			final Handler handler = new Handler()
			{
				@Override
				public void handleMessage(Message msg)
				{
					imageCallBack.imageLoad(imageView, (Bitmap) msg.obj);
				}
			};
		//新建一个线程下载图片，下载完之后就缓存进Map，并且把bmp作为msg发送给刚才建的handler
			new Thread()
			{

				@Override
				public void run()
				{
					HttpClient client = new DefaultHttpClient();
					HttpGet get = new HttpGet(imageURL);
					try
					{
						HttpResponse response = client.execute(get);
						InputStream is = response.getEntity().getContent();
						Bitmap bmp = BitmapFactory.decodeStream(is);
						Log.e("Lin", "download " + imageURL);
						imgCacheMap.put(imageURL,
								new SoftReference<Bitmap>(bmp));
						Message msg = handler.obtainMessage(0, bmp);
						handler.sendMessage(msg);
					} catch (ClientProtocolException e)
					{
						e.printStackTrace();
					} catch (IOException e)
					{
						e.printStackTrace();
					}
				}

			}.start();
		}
		//下载图片是异步进行的，函数实际上在没缓存的情况下返回的是null
		return null;
	}
	//上面ImageCallBack的接口的定义
	public interface ImageCallBack
	{
		public void imageLoad(ImageView imageView,Bitmap bmp);
	}
	//用于定义是否允许下载图片的标志
	public void setDownloadFlag(boolean flag)
	{
		isAllowDownloadbmp=flag;
	}
}
</pre>
Adapter如何应用上面这个类：
<pre>
public class MyAdapter extends BaseAdapter
{
	private List<Info> infos=new ArrayList<>();
	private Context mContext;
	public AsyncBitmapLoader asyncBitmapLoader;
	
	//构造函数中初始化该对象
	public MyAdapter(Context context)
	{
		this.mContext=context;
		this.asyncBitmapLoader=new AsyncBitmapLoader();
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent)
	{
		// TODO Auto-generated method stub
		final Holder holder;
		if(convertView==null)
		{
			convertView=LayoutInflater.from(mContext).inflate(R.layout.listview_item,null);
			holder=new Holder();
			holder.img=(ImageView) convertView.findViewById(R.id.item_img);
			holder.heading=(TextView) convertView.findViewById(R.id.item_heading);
			holder.texting=(TextView) convertView.findViewById(R.id.item_texting);
			convertView.setTag(holder);
		}else {
			holder=(Holder) convertView.getTag();
		}
		holder.heading.setText(infos.get(position).heading);
		holder.texting.setText(infos.get(position).texting);
		holder.img.setImageBitmap(null);//防止使用convertView时旧图片的影响
		//分别在要更新的UI组件，实现了具体方法的CallBack接口作为参数调用函数
		Bitmap bmp=asyncBitmapLoader.loadBitmap(holder.img, MainActivity.IP+"images/"+infos.get(position).img, new ImageCallBack()
		{

			@Override
			public void imageLoad(ImageView imageView, Bitmap bmp)
			{
				//线程下载完图片利用handler发送信息，信息自然就还是那个handler来处理，处理的方式就是调用这个ImageCallBack对象的这个函数了
				holder.img.setImageBitmap(bmp);
			}
			
		});
		if(bmp!=null)
		{
			holder.img.setImageBitmap(bmp);
		}
		
		return convertView;
	}
	private class Holder
	{
		public ImageView img;
		public TextView texting,heading;
	}
</pre>

此外还有设置是否允许下载图片的标志：
<pre>
listView.setOnScrollListener(new AbsListView.OnScrollListener()
		{
			
			@Override
			public void onScrollStateChanged(AbsListView view, int scrollState)
			{
				// 0表示没有滑动，1表示手指在屏幕上，2表示惯性滑动
				if(scrollState==1 || scrollState==2)
				{
					myAdapter.asyncBitmapLoader.setDownloadFlag(false);
				}else {
					myAdapter.asyncBitmapLoader.setDownloadFlag(true);
					myAdapter.notifyDataSetChanged();//设置了可以下载图片之后还要告诉Adapter设置变了，要调用下getView来更新才会再次去下载图片的
				}
			}
			
			@Override
			public void onScroll(AbsListView view, int firstVisibleItem,
					int visibleItemCount, int totalItemCount)
			{
				// TODO Auto-generated method stub
				
			}
		});
</pre>