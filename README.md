# Android Image Slider [![Build Status](https://travis-ci.org/daimajia/AndroidImageSlider.svg)](https://travis-ci.org/daimajia/AndroidImageSlider)

[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/daimajia/AndroidImageSlider?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
 
This is an amazing image slider for the Android platform. I decided to open source this because there is really not an attractive, convenient slider widget in Android.
 
You can easily load images from an internet URL, drawable, or file. And there are many kinds of amazing animations you can choose. :-D
 
## Demo
 
![](http://ww3.sinaimg.cn/mw690/610dc034jw1egzor66ojdg20950fknpe.gif)

[Download Apk](https://github.com/daimajia/AndroidImageSlider/releases/download/v1.0.8/demo-1.0.8.apk)
 
## Usage

### Step 1

#### Gradle

```groovy
dependencies {
    	compile "com.android.support:support-v4:+"
    	compile 'com.squareup.picasso:picasso:2.3.2'
    	compile 'com.nineoldandroids:library:2.4.0'
    	compile 'com.daimajia.slider:library:1.1.5@aar'
}
```


#### Maven

```xml
<dependency>
    <groupId>com.squareup.picasso</groupId>
    <artifactId>picasso</artifactId>
    <version>2.3.2</version>
</dependency>
<dependency>
    <groupId>com.nineoldandroids</groupId>
    <artifactId>library</artifactId>
    <version>2.4.0</version>
</dependency>
<dependency>
    <groupId>com.daimajia.slider</groupId>
    <artifactId>library</artifactId>
    <version>1.1.2</version>
    <type>apklib</type>
</dependency>
```

#### Eclipse

For Eclipse users, I provided a sample project which orgnized as Eclipse way. You can download it from [here](https://github.com/daimajia/AndroidImageSlider/releases/download/v1.0.9/AndroidImageSlider-Eclipse.zip), and make some changes to fit your project.

Notice: It's the version of 1.0.9, it may not update any more. You can update manually by yourself.

### Step 2

Add permissions (if necessary) to your `AndroidManifest.xml`

```xml
<!-- if you want to load images from the internet -->
<uses-permission android:name="android.permission.INTERNET" /> 

<!-- if you want to load images from a file OR from the internet -->
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

**Note:** If you want to load images from the internet, you need both the `INTERNET` and `READ_EXTERNAL_STORAGE` permissions to allow files from the internet to be cached into local storage.

If you want to load images from drawable, then no additional permissions are necessary.

### Step 3

Add the Slider to your layout:
 
```java
<com.daimajia.slider.library.SliderLayout
        android:id="@+id/slider"
        android:layout_width="match_parent"
        android:layout_height="200dp"
/>
```        
 
There are some default indicators. If you want to use a provided indicator:
 
```java
<com.daimajia.slider.library.Indicators.PagerIndicator
        android:id="@+id/custom_indicator"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:gravity="center"
        />
```

[Code example](https://github.com/daimajia/AndroidImageSlider/blob/master/demo%2Fsrc%2Fmain%2Fjava%2Fcom%2Fdaimajia%2Fslider%2Fdemo%2FMainActivity.java)
 
====
 
## Advanced usage

Please visit [Wiki](https://github.com/daimajia/AndroidImageSlider/wiki)
 
## Thanks

- [Picasso](https://github.com/square/picasso)
- [NineOldAndroids](https://github.com/JakeWharton/NineOldAndroids)
- [ViewPagerTransforms](https://github.com/ToxicBakery/ViewPagerTransforms)

##About me
 
I am a student in mainland China. I love Google, love Android, love everything that is interesting. If you get any problems when using this library or you have an internship opportunity, please feel free to [email me](mailto:daimajia@gmail.com). :smiley:



在做项目的时候遇到了这样的问题，当加载网络上的图片的时候，如果图片比较大，那么图片是加载不上来的，引起这个问题的原因是AndroidImageSlider用的是picasso-2.3.3.jar,我们只要将源码中的BaseSliderView做修改，用Android-Universal-ImageLoader来替换picasso-2.3.3.jar。具体过程如下：
1.将源文件com.daimajia.slider.library.SliderTypes.BaseSliderView.java文件中的代码替换如下：
package com.daimajia.slider.library.SliderTypes;

import android.content.Context;
import android.graphics.Bitmap;
import android.os.Bundle;
import android.view.View;
import android.widget.ImageView;

import com.daimajia.slider.library.R;
import com.nostra13.universalimageloader.cache.disc.naming.Md5FileNameGenerator;
import com.nostra13.universalimageloader.core.DisplayImageOptions;
import com.nostra13.universalimageloader.core.ImageLoader;
import com.nostra13.universalimageloader.core.ImageLoaderConfiguration;
import com.nostra13.universalimageloader.core.assist.QueueProcessingType;
import com.nostra13.universalimageloader.core.display.FadeInBitmapDisplayer;
import com.nostra13.universalimageloader.core.listener.ImageLoadingProgressListener;
import com.nostra13.universalimageloader.core.listener.SimpleImageLoadingListener;

import java.io.File;

/**
 * When you want to make your own slider view, you must extends from this class.
 * BaseSliderView provides some useful methods. I provide two example:
 * {@link com.daimajia.slider.library.SliderTypes.DefaultSliderView} and
 * {@link com.daimajia.slider.library.SliderTypes.TextSliderView} if you want to
 * show progressbar, you just need to set a progressbar id as @+id/loading_bar.
 */
public abstract class BaseSliderView {

	protected Context mContext;

	private Bundle mBundle;

	/**
	 * Error place holder image.
	 */
	private int mErrorPlaceHolderRes;

	/**
	 * Empty imageView placeholder.
	 */
	private int mEmptyPlaceHolderRes;

	private String mUrl;
	private File mFile;
	private int mRes;

	protected OnSliderClickListener mOnSliderClickListener;

	private boolean mErrorDisappear;

	private ImageLoadListener mLoadListener;

	private String mDescription;

	/**
	 * Scale type of the image.
	 */
	private ScaleType mScaleType = ScaleType.Fit;

	/*
	 * Universal Image Loader Option
	 */
	DisplayImageOptions options;

	public enum ScaleType {
		CenterCrop, CenterInside, Fit, FitCenterCrop
	}

	protected BaseSliderView(Context context) {
		mContext = context;
		this.mBundle = new Bundle();
	}

	/**
	 * the placeholder image when loading image from url or file.
	 * 
	 * @param resId
	 *            Image resource id
	 * @return
	 */
	public BaseSliderView empty(int resId) {
		mEmptyPlaceHolderRes = resId;
		return this;
	}

	/**
	 * determine whether remove the image which failed to download or load from
	 * file
	 * 
	 * @param disappear
	 * @return
	 */
	public BaseSliderView errorDisappear(boolean disappear) {
		mErrorDisappear = disappear;
		return this;
	}

	/**
	 * if you set errorDisappear false, this will set a error placeholder image.
	 * 
	 * @param resId
	 *            image resource id
	 * @return
	 */
	public BaseSliderView error(int resId) {
		mErrorPlaceHolderRes = resId;
		return this;
	}

	/**
	 * the description of a slider image.
	 * 
	 * @param description
	 * @return
	 */
	public BaseSliderView description(String description) {
		mDescription = description;
		return this;
	}

	/**
	 * set a url as a image that preparing to load
	 * 
	 * @param url
	 * @return
	 */
	public BaseSliderView image(String url) {
		if (mFile != null || mRes != 0) {
			throw new IllegalStateException("Call multi image function,"
					+ "you only have permission to call it once");
		}
		mUrl = url;
		return this;
	}

	/**
	 * set a file as a image that will to load
	 * 
	 * @param file
	 * @return
	 */
	public BaseSliderView image(File file) {
		if (mUrl != null || mRes != 0) {
			throw new IllegalStateException("Call multi image function,"
					+ "you only have permission to call it once");
		}
		mFile = file;
		return this;
	}

	public BaseSliderView image(int res) {
		if (mUrl != null || mFile != null) {
			throw new IllegalStateException("Call multi image function,"
					+ "you only have permission to call it once");
		}
		mRes = res;
		return this;
	}

	public String getUrl() {
		return mUrl;
	}

	public boolean isErrorDisappear() {
		return mErrorDisappear;
	}

	public int getEmpty() {
		return mEmptyPlaceHolderRes;
	}

	public int getError() {
		return mErrorPlaceHolderRes;
	}

	public String getDescription() {
		return mDescription;
	}

	public Context getContext() {
		return mContext;
	}

	/**
	 * set a slider image click listener
	 * 
	 * @param l
	 * @return
	 */
	public BaseSliderView setOnSliderClickListener(OnSliderClickListener l) {
		mOnSliderClickListener = l;
		return this;
	}

	/**
	 * When you want to implement your own slider view, please call this method
	 * in the end in `getView()` method
	 * 
	 * @param v
	 *            the whole view
	 * @param targetImageView
	 *            where to place image
	 * @param context
	 */
	protected void bindEventAndShow(final View v, ImageView targetImageView) {
		final BaseSliderView me = this;

		v.setOnClickListener(new View.OnClickListener() {
			public void onClick(View v) {
				if (mOnSliderClickListener != null) {
					mOnSliderClickListener.onSliderClick(me);
				}
			}
		});

		mLoadListener.onStart(me);
		/*
		 * set option values
		 */
		options = new DisplayImageOptions.Builder().cacheInMemory(true)
				.cacheOnDisk(true).considerExifParams(true)
				.displayer(new FadeInBitmapDisplayer(300, true, true, true))
				.delayBeforeLoading(100).resetViewBeforeLoading(true)
				.bitmapConfig(Bitmap.Config.RGB_565).build();
		if (!ImageLoader.getInstance().isInited()) {
			initImageLoader(mContext);
		}
		showImage(v, getUrl(), targetImageView);

	}

	public BaseSliderView setScaleType(ScaleType type) {
		mScaleType = type;
		return this;
	}

	public ScaleType getScaleType() {
		return mScaleType;
	}

	/**
	 * the extended class have to implement getView(), which is called by the
	 * adapter, every extended class response to render their own view.
	 * 
	 * @return
	 */
	public abstract View getView();

	/**
	 * set a listener to get a message , if load error.
	 * 
	 * @param l
	 */
	public void setOnImageLoadListener(ImageLoadListener l) {
		mLoadListener = l;
	}

	public interface OnSliderClickListener {
		public void onSliderClick(BaseSliderView slider);
	}

	/**
	 * when you have some extra information, please put it in this bundle.
	 * 
	 * @return
	 */
	public Bundle getBundle() {
		return mBundle;
	}

	public interface ImageLoadListener {
		public void onStart(BaseSliderView target);

		public void onEnd(boolean result, BaseSliderView target);
	}

	public static void initImageLoader(Context context) {

		ImageLoaderConfiguration config = new ImageLoaderConfiguration.Builder(
				context).threadPriority(Thread.NORM_PRIORITY - 2)
				.denyCacheImageMultipleSizesInMemory()
				.diskCacheFileNameGenerator(new Md5FileNameGenerator())
				.diskCacheSize(50 * 1024 * 1024)
				// 50 Mb
				.tasksProcessingOrder(QueueProcessingType.LIFO)
				.writeDebugLogs() // Remove for release app
				.build();

		ImageLoader.getInstance().init(config);
	}

	private void showImage(final View v, String imageURL,
			final ImageView imageView) {
		ImageLoader.getInstance().displayImage(imageURL, imageView, options,
				new SimpleImageLoadingListener() {
					@Override
					public void onLoadingStarted(String imageUri, View view) {

					}

					@Override
					public void onLoadingComplete(String imageUri, View view,
							Bitmap loadedImage) {

						imageView.setImageBitmap(loadedImage);
						if (v.findViewById(R.id.loading_bar) != null) {
							v.findViewById(R.id.loading_bar).setVisibility(
									View.INVISIBLE);
						}

					}
				}, new ImageLoadingProgressListener() {
					public void onProgressUpdate(String imageUri, View view,
							int current, int total) {

					}
				});
	}
}

2.将Android-Universal-ImageLoader包导入



