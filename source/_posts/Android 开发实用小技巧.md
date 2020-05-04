---
title: Android 开发实用小技巧
id: 2
date: 2017-09-19 22:21:10
urlname: android_develop_skill
tags: 
	- Android
	- 实战技巧
categories: Android
cover: https://pic.downk.cc/item/5eaeda37c2a9a83be5dd2581.jpg
---

### 文本与布局
1. 字符串资源里的变量替换
  当程序中使用字符串如第 345 页，我们不可能使用两个字符串资源如

    ```java
    <string name="di">第</string> 
    <string name="page">页</string>
    ```
    <!--more-->
  在 android 中有一个东西是 XLIFF，全称是 XML 本地化数据交换格式，英文是 XML Loca lization Interchange File Format。
  用法很简单，如下所示
    ``` java 
    <string name="page">第%1$s页</string>
    程序中只要给变量赋值就好了，如下：
    String page = getString(R.string.page,"345");
    ```
  要是有多个变量，如第 345 页 24 行 ? 这也好办，如下:
    ``` java
    <string name="page">第%1$s页%2$s行</string>
    String page = getString(R.string.page,"345","24");
    ```

2. TextView 设置多种字体大小

 ![TextView字体](https://github.com/xxtlant/BlogPicture/blob/master/android%E5%AE%9E%E7%94%A8%E6%8A%80%E5%B7%A7/TextViewSpan.png?raw=true)

  像这样的两种字体，要如何处理呢

    ``` java
    String text = "Android实战技巧之文本与布局";
    int start = text.indexOf('之');
    int end = text.length();
    Spannable textSpan =  new SpannableString(text);
    textSpan.setSpan(new AbsoluteSizeSpan(20), 0, start,
                    Spannable.SPAN_INCLUSIVE_INCLUSIVE);
    textSpan.setSpan(new AbsoluteSizeSpan(12), start,
                    end, Spannable.SPAN_INCLUSIVE_INCLUSIVE);
    ``` 
    这个 textSpan 就是你想要的。

3. TextView 的超链接
  这个很简单，在 xml 中属性 `autoLink=“all”`。
  程序中 `TextView.setAutoLink(Linkify.ALL)`;
  说一下 `autoLink` 中的参数：

    ```java
    Linkify.EMAIL_ADDRESS -- 仅识别出 TextView 中的 Email 在址，标识为超链接，点击后会跳到 Email，发送邮件给此地址
    Linkify.PHONE_NUMBERS -- 仅识别出 TextView 中的电话号码，标识为超链接，点击后会跳到 Dialer，Call 这个号码
    Linkify.WEB_URLS-- 仅识别出 TextView 中的网址，标识为超链接，点击后会跳到 Browser 打开此 URL Linkify.ALL -- 这个选项是识别出所有系统所支持的特殊 Uri，然后做相应的操作
    ```
    特殊情况:
当一段文字部分是超链接或者我们需要点击超链接跳到另一个 Activity时，如何处理?

    ```java
    public class MainActivity extends Activity {
        private TextView testText;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            testText = (TextView) findViewById(R.id.testText); 
            //将TextView的显示文字设置为SpannableString
            testText.setText(getClickableSpan());
            //设置该句使文本的超连接起作用
            testText.setMovementMethod(LinkMovementMethod.getInstance());
        }
    
        //设置超链接文字
        private SpannableString getClickableSpan() {
            SpannableString spanStr = new SpannableString("使用该软件，即表示您同意该软件的使用条款和隐私政策"); //设置下划线文字
            spanStr.setSpan(new UnderlineSpan(), 16, 20, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE); //设置文字的单击事件
            spanStr.setSpan(new ClickableSpan() {
                @Override
                public void onClick(View widget) {
                    startActivity(new Intent(MainActivity.this, TestActivity1.class));
                }
            }, 16, 20, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            //设置文字的前景色
            spanStr.setSpan(new ForegroundColorSpan(Color.WHITE), 16, 20, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE); //设置下划线文字
            spanStr.setSpan(new UnderlineSpan(), 21, 25, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            //设置文字的单击事件
            spanStr.setSpan(new ClickableSpan() {
                @Override
                public void onClick(View widget) {
                    startActivity(new Intent(MainActivity.this, TestActivity2.class));
                }
            }, 21, 25, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            //设置文字的前景色
            spanStr.setSpan(new ForegroundColorSpan(Color.WHITE), 21, 25, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            return spanStr;
        }
     }
    
    ```
 ```
    
4. Java 文件中字体加粗

    ```java
    //Typeface 
    textView.setTypeface(Typeface.defaultFromStyle(Typeface.BOLD));
    //use TextPaint
    textView.getPaint().setFakeBoldText(true);
 ```

###  按钮控制 ViewPager 的左右翻页

为了实现左右翻页的效果，使用了 ViewPager，它会很方便的实现左右滑动后翻页。 这时需要自己也加上两个 button 来实现同样的操作，如何实现呢?
通过查看 ViewPager 的源码，里面有一个公共方法 arrowScroll ，查看代码 们可以有两个重要的发现:

```java 
 public boolean executeKeyEvent(KeyEvent event) {
        boolean handled = false;
        if (event.getAction() == KeyEvent.ACTION_DOWN) {
            switch (event.getKeyCode()) {
                case KeyEvent.KEYCODE_DPAD_LEFT: //键盘方向键左键的控制，向左翻页
                    handled = arrowScroll(FOCUS_LEFT);//FOCUS_LEFT:17
                    break;
                case KeyEvent.KEYCODE_DPAD_RIGHT://键盘方向键右键的控制，向右翻页
                    handled = arrowScroll(FOCUS_RIGHT);//FOCUS_RIGHT:66
                    break;
                case KeyEvent.KEYCODE_TAB:
                    if (event.hasNoModifiers()) {
                        handled = arrowScroll(FOCUS_FORWARD);// 向前 -> 右翻 FOCUS_FORWARD:2
                    } else if (event.hasModifiers(KeyEvent.META_SHIFT_ON)) {
                        handled = arrowScroll(FOCUS_BACKWARD);// 向后 -> 左翻 FOCUS_BACKWARD:1
                    }
                    break;
            }
        }
        return handled;
    }

public boolean arrowScroll(int direction) {
        ...
        if (nextFocused != null && nextFocused != currentFocused) {
            if (direction == View.FOCUS_LEFT) {
                ... // 省略一些无关代码
            } else if (direction == View.FOCUS_RIGHT) {
                ... // 省略一些无关代码
        } else if (direction == FOCUS_LEFT || direction == FOCUS_BACKWARD){ // 17 or 1 向左翻页
            // Trying to move left and nothing there; try to page.
            handled = pageLeft();
        } else if (direction == FOCUS_RIGHT || direction == FOCUS_FORWARD) { // 66 or 2 向右翻页
            // Trying to move right and nothing there; try to page.
            handled = pageRight();
        }
        ... //省略一些无关代码
        return handled;
    }
```
也就是说， 我们调用 arrowScroll 方法用参数 1 或者 17 就可以实现向左翻页;参数 2 或 66 就可以实现向右翻页。 后记:
当你的 UI 中有 EditText 这种获得 focus 的 widget 时， 必须用 17 和 66，否则要报错。

###  获得屏幕物理尺寸、密度及分辨率 
#### 获取屏幕的宽高
需要注意的原来经常使用的 getHeight() 与 getWidth() 已经不推荐使用了，建议使用 getSize()来替代。

参数是一个返回参数，用以返回分辨率的 Point，这个 Point 也比较简单，我们只需要关注 x 和 y 这两个成员就可以了。
用法如下:
```java
public void getSize(Point outSize) { 
    synchronized (this) {
        updateDisplayInfoLocked();  
        mDisplayInfo.getAppMetrics(mTempMetrics, mDisplayAdjustments);
        outSize.x = mTempMetrics.widthPixels;
        outSize.y = mTempMetrics.heightPixels;
    }
}
```
此外 `Display`又提供了另一个方法：`getRealSize()`
```java
public void getRealSize(Point outSize) { 
    synchronized (this) {
        updateDisplayInfoLocked();
        outSize.x = mDisplayInfo.logicalWidth; 
        outSize.y = mDisplayInfo.logicalHeight;
    }
}
```
从两个方法的实现上看是有区别的。那么差异究竟在哪里，下面做一些实验来验证一下。
当` Activity`的主题是全屏主题时 
```java
android:theme="@android:style/Theme.Black.NoTitleBar.Fullscreen" 
android:theme="@android:style/Theme.NoTitleBar.Fullscreen"
```
二者返回的宽高是一样的，但是当不是全屏主题是，例如 `Activity` 主题有` ToolBar/ActionBar`时，二者返回的宽高就不一样了。
测试如下：
```java
getSize():
private void getDisplaySize() {
    Point point = new Point(); getWindowManager().getDefaultDisplay().getSize (point);     
    Log.d(TAG,"the screen size is "+point.toString());
}
// 结果如下 返回的高度是去掉 toolbar 的高度
D/SecondActivity: the screen size is Point(1080, 1794)

getRealSize()
private void getDisplayInfomation() {
    Point point = new Point(); getWindowManager().getDefaultDisplay().getSize (point);
    Log.d(TAG,"the screen size is "+point.toString());
}
// 结果如下 是屏幕真实的宽高
D/SecondActivity: the screen size is Point(1080, 1920)
```
#### 屏幕尺寸，设备的物理屏幕尺寸
1. 屏幕尺寸，设备的物理屏幕尺寸
    现如今 `Android` 手机尺寸杂乱无章，有规则的，也有奇葩的尺寸，[手机屏幕尺寸](http://screensiz.es/) 这个网站，记录了一些 `Android、IOS` 屏幕的尺寸比例。
    不同的屏幕尺寸是可以通过采用相同的分辨率，它们之间的区别就在于**密度(density)**不同

2. 那些搞晕你的单位：DPI / PPI / DP / PX 
    APP 中尺寸单位和各自的意思
    | 尺寸单位  | 代表的意思   |
    | --------   | :-----  |
    | px: pixel |【像素】  电子屏幕上组成一幅图画或照片的最基本单元|
    | pt: ponit |   【点】印刷行业常用单位，等于1/72英寸   |
    | ppi: pixel per inch |【每英寸像素数】  该值越高，则屏幕越细腻 |
    | dpi: dot per inch |【每英寸像素数】  该值越高，则屏幕越细腻 |
    | dp/dip:Density-independent pixel | 安卓开发用的长度单位，1dp表示在屏幕像素点密度为160ppi时1px长度 |
    | sp: scale-independent pixel | 安卓开发用的字体大小单位|

    **相互关系**
    -  pt 和 px
    
        ```java
        1pt = (DPI / 72)px
        ```
        当在 ps 中新建画布的分辨率为 72ppi(即72dpi),1pt = 1px
    -  PPI 和 DPI
        DPI 最初适用于衡量打印物上每英寸的点数密度。DPI 值越小图片越不精细。当 DPI 的概念用在计算机屏幕上时，就称为了 PPI。同理：PPI 就是计算机屏幕上每英寸可以显示的像素点的数量。因此，在电子屏幕中提到的 PPI 和 DPI 是一致的。
        
        ```java
        可认为 DPI = PPI
        ```
    - PPI 的计算方法
		![ppi 的计算方法](http://upload-images.jianshu.io/upload_images/3303429-c7a1c80febcc85a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)    
    - px 和 dp
    dp为安卓开发时的长度单位，根据不同的屏幕分辨率，与px有不同的对应关系。
    dp的大小的定义为：
    
    ```java
    1dp=（屏幕 ppi / 160）px
    ```
    
    ![DPI 与 PPI](https://github.com/xxtlant/BlogPicture/blob/master/android%E5%AE%9E%E7%94%A8%E6%8A%80%E5%B7%A7/dpi%E4%B8%8E%E5%AF%86%E5%BA%A6%E7%9A%84%E6%8D%A2%E7%AE%97.png?raw=true)
    
    - dp 和 sp
    dp和sp类似，都会随着屏幕大小和分辨率变化而改变，不同的是，在android中，dp表示长度单位，sp表示字体大小，在多数情况下
    
    ```java
    1dp=1sp
    ```
    但是在文字尺寸是“大”或“超大”时，1sp > 1dp。
    <!--more-->