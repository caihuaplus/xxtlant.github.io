---
title: 如何做到在子线程更新 UI？
date: 2020-05-13 15:17:32
id: 7
urlname: thread_update_ui
tags:
	- Android
categories: Android
cover: https://pic.downk.cc/item/5ebba428c2a9a83be51d9b28.jpg
---

一般来讲，子线程是不能更新 UI 的，如果在子线程更新 UI，会报错。

但在某种情况下直接开启线程更新 UI 是不会报错的。

比如，在 onCreate 方法中，直接开启子线程更新 UI，这样是不会报错的。

```java
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    textView = findViewById(R.id.tv)
    thread {
        textView.text = "哈哈哈哈"
    }
}
```

如果在子线程中假如延时，比如加一行`Thread.sleep(2000)`就会报错。

这是为什么呢？

有人会说，因为睡眠了 2 s，因此 UI 的线程检查机制就已经建立了，所以在子线程更新就会报错。

## 更新 UI 的线程检测是什么时候开始的

![子线程更新 UI 的错误详情](https://pic.downk.cc/item/5ebba4b3c2a9a83be51dfc07.jpg)

子线程更新的错误定位是 `ViewRootImpl` 中的 `checkThread` 方法和 `requestLayout` 方法。

```java
// ViewRootImpl 下 checkThread 的源码
void checkThread() {
    if (mThread != Thread.currentThread()) {
        throw new CalledFromWrongThreadException(
                "Only the original thread that created a view hierarchy can touch its views.");
    }
}

//ViewRootImpl 下 requestLayout 的源码
@Override
public void requestLayout() {
    if (!mHandlingLayoutInLayoutRequest) {
        checkThread();
        mLayoutRequested = true;
        scheduleTraversals();
    }
}
```

从源码中可以看出，`checkThread` 就是进行线程检测的方法，而调用是在 `requestLayout` 方法中。

要想知道 `requestLayout` 是何时调用的，就要知道 `ViewRootImpl` 是如何创建的？

**因为在 `onCreate` 中创建子线程访问 UI，是不报错的，这也说明在 `onCreate` 中，`ViewRootImpl` 还未创建。**

## ViewRootImpl 是何时创建的。

在 `ActivityThread` 的 `handleResumeActivity` 中调用了 `performResumeActivity` 进行 `onResume` 的回调。

```java
@Override
public void handleResumeActivity(IBinder token, boolean finalStateRequest, boolean isForward,String reason) {
    // 代码省略...
    
    // performResumeActivity 最终会调用 Activity 的 onResume方法
    // 调用链如下： 会调用 r.activity.performResume。
    // performResumeActivity -> r.activity.performResume -> Instrumentation.callActivityOnResume(this) -> activity.onResume();
    final ActivityClientRecord r = performResumeActivity(token, finalStateRequest, reason);
    
    // 代码省略...
        
    if (r.window == null && !a.mFinished && willBeVisible) {
        r.activity.mVisibleFromServer = true;
        mNumVisibleActivities++;
        if (r.activity.mVisibleFromClient) {
            // 注意这句，让 activity 显示，并且会最终创建 ViewRootImpl
            r.activity.makeVisible();
        }
    }
}
```

进一步跟进 `activity.makeVisible()`。

```java
void makeVisible() {
    if (!mWindowAdded) {
        ViewManager wm = getWindowManager();
        // 往 WindowManager 中添加 DecorView
        wm.addView(mDecor, getWindow().getAttributes());
        mWindowAdded = true;
    }
    mDecor.setVisibility(View.VISIBLE);
}
```

`WindowManager` 是一个接口，它的实现类是 `WindowManagerImpl`。

```java
// WindowManagerImpl 的 addView 方法
@Override
public void addView(@NonNull View view, @NonNull ViewGroup.LayoutParams params) {
    applyDefaultToken(params);
    // 最终调用了 WindowManagerGlobal 的 addView 
    mGlobal.addView(view, params, mContext.getDisplay(), mParentWindow);
}

// WindowManagerGlobal 的 addView
public void addView(View view, ViewGroup.LayoutParams params,
                    Display display, Window parentWindow) {
    // 省略部分代码
	
    // ViewRootImpl 对象的声明
    ViewRootImpl root;
    View panelParentView = null;

    synchronized (mLock) {
        // 省略部分代码

        // ViewRootImpl 对象的创建
        root = new ViewRootImpl(view.getContext(), display);
        view.setLayoutParams(wparams);
        mViews.add(view);
        mRoots.add(root);
        mParams.add(wparams);
        
        try {
            // 调用 ViewRootImpl 的 setView 方法
            root.setView(view, wparams, panelParentView);
        } catch (RuntimeException e) {
            // BadTokenException or InvalidDisplayException, clean up.
            if (index >= 0) {
                removeViewLocked(index, true);
            }
            throw e;
        }
    }
}
```

由此可以看出，`ViewRootImpl` 是在 `activity` 的 `onResume` 方法调用后才由 `WindowManagerGlobal` 的 `addView` 方法创建。

## 那 `requestLayout` 是如何调用的呢？

在上面 `WindowManagerGlobal` 的 `addView` 方法中，创建完 `ViewRootImpl` 后，会调用它的 `setView` 的方法，在 `setView` 方法内部会调用 `requestLayout`。

此时就会去检测 UI 更新时调用的线程了。

```java
// ViewRootImpl 的 setView
public void setView(View view, WindowManager.LayoutParams attrs, View panelParentView) {
    synchronized (this) {
        if (mView == null) {
            mView = view;

            // 省略无关代码...
            // requestLayout 的调用
            requestLayout();
        
          // 省略无关代码...
        }
}

// requestLayout 方法
@Override
public void requestLayout() {
    if (!mHandlingLayoutInLayoutRequest) {
        checkThread();
        mLayoutRequested = true;
        scheduleTraversals();
    }
}
```

而在 `SheduleTranversals` 方法中，会调用 `TraversalRunnable` 的 `run `方法，最终会在 `performTraversals` 方法中，调用 `performMeasure` `performLayout` `performDraw` 去开始 View 的绘制流程。

```java
void scheduleTraversals() {
    if (!mTraversalScheduled) {
        mTraversalScheduled = true;
        mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();
        // TraversalRunnable 的 run 方法中，会开启 UI 的measure、layout、draw
        mChoreographer.postCallback(
            Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);
       // 省略无关代码...
    }
}

final class TraversalRunnable implements Runnable {
    @Override
    public void run() {
        doTraversal();
    }
}
void doTraversal() {
    if (mTraversalScheduled) {
        // 省略部分代码
        performTraversals();
    }
}

private void performTraversals() {
    // Ask host how big it wants to be
    // 省略部分代码
    performMeasure(childWidthMeasureSpec, childHeightMeasureSpec);
    performLayout(lp, mWidth, mHeight);
    performDraw();
}
```

## 子线程更新 UI 实战

既然知道了子线程更新 UI 的检测是在 `checkThread` 方法中，那么有没有什么方法可以绕过呢？能否做到子线程更新 UI 呢？

答案是可以的。

我以一个简单的 demo 实验一下，下面先看效果。

![](https://pic.downk.cc/item/5ebb9b5dc2a9a83be5155f05.gif)

代码如下：
```java
// MainActivity
public class MainActivity extends AppCompatActivity {
    private View containerView;
    private ViewTreeObserver.OnGlobalLayoutListener globalLayoutListener;
    private TextView mTv2;
    private TextView mTv1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        containerView = findViewById(R.id.container_layout);
        mTv1 = findViewById(R.id.text);
        mTv2 = findViewById(R.id.text2);

        // 开启线程，启动 GlobalLayoutListener
        Executors.newSingleThreadExecutor().execute(() -> initGlobalLayoutListener());
    }

    private void initGlobalLayoutListener() {
        globalLayoutListener = () -> {
            Log.e("caihua", "onGlobalLayout : " + Thread.currentThread().getName());
            ViewGroup.LayoutParams layoutParams = containerView.getLayoutParams();
            containerView.setLayoutParams(layoutParams);
        };
        this.getWindow().getDecorView().getViewTreeObserver().addOnGlobalLayoutListener(globalLayoutListener);
    }


    public void updateUiInMain(View view) {
        mTv1.setText("主线程更新 UI");
    }

    public void updateUiInThread(View view) {
        new Thread(){
            @Override
            public void run() {
                SystemClock.sleep(2000);
                mTv2.setText("子线程更新 UI ：" + Thread.currentThread().getName());
            }
        }.start();
    }

}
```

原理：通过 `ViewTreeObserver.OnGlobalLayoutListener` 设置全局的布局监听，然后在 `onGlobalLayout` 方法中，调用 `view` 的 `setLayoutParams` 方法，`setLayoutParams` 方法内部会调用 `requestLayout`，这样就可以绕过线程检测。

为什么能绕过呢？

**因为 `setLayoutParams` 中调用的 `requestLayout` 方法并不是 `ViewRootImpl` 中 `requestLayout`.**

**而 `View` 的 `requestLayout` 并不调用 `checkThread` 方法去检测线程。**

源码如下↓
```java
// view.setLayoutParams 源码
public void setLayoutParams(ViewGroup.LayoutParams params) {
    if (params == null) {
        throw new NullPointerException("Layout parameters cannot be null");
    }
    mLayoutParams = params;
    resolveLayoutParams();
    if (mParent instanceof ViewGroup) {
        ((ViewGroup) mParent).onSetLayoutParams(this, params);
    }
    // 调用 requestLayout 方法。
    requestLayout();
}
// View 的 requestLayout 方法
public void requestLayout() {
    if (mMeasureCache != null) mMeasureCache.clear();

    if (mAttachInfo != null && mAttachInfo.mViewRequestingLayout == null) {
        ViewRootImpl viewRoot = getViewRootImpl();
        if (viewRoot != null && viewRoot.isInLayout()) {
            if (!viewRoot.requestLayoutDuringLayout(this)) {
                return;
            }
        }
        mAttachInfo.mViewRequestingLayout = this;
    }

    mPrivateFlags |= PFLAG_FORCE_LAYOUT;
    mPrivateFlags |= PFLAG_INVALIDATED;

    if (mParent != null && !mParent.isLayoutRequested()) {
        mParent.requestLayout();
    }
    if (mAttachInfo != null && mAttachInfo.mViewRequestingLayout == this) {
        mAttachInfo.mViewRequestingLayout = null;
    }
}

```