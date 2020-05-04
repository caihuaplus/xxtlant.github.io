---
title: Android Studio 常用插件整理
id: 1
date: 2017-08-19 16:23:56
urlname: AS_plug
tags: 
	- Android
	- Android Studio
categories: Android
cover: https://pic.downk.cc/item/5eaeda37c2a9a83be5dd2581.jpg
---
最近改用 Android Studio 3.0 preview ,顺便整理一下，常用的插件。

#### <span style="font-size: 12pt;">[GsonFormat](https://plugins.jetbrains.com/plugin/7654-gsonformat)</span>
 将 Json 字符串快速转成 JavaBean 对象，免去我们根据 Json 字符串手写对应 Java Bean 的过程.

![gsonFormat](https://plugins.jetbrains.com/files/7654/screenshot_15729.png)
Tips: 可以使用快捷键 alt + s (windows)  / option + s (mac) 

<!--more-->

#### <span style="font-size: 12pt;">[Android ButterKnife Zelezny](https://plugins.jetbrains.com/plugin/7369-android-butterknife-zelezny)</span>

配合 [butterknife](https://github.com/JakeWharton/butterknife) 实现注解，不用再手动实现 findViewById 了。

![Android ButterKnife Zelezny](https://plugins.jetbrains.com/files/7369/screenshot_14384.png)

#### <span style="font-size: 12pt;">[Android Methods Count](https://plugins.jetbrains.com/plugin/8076-android-methods-count)</span>

 显示依赖库中得方法数
![Android Methods Count](https://plugins.jetbrains.com/files/8076/screenshot_15509.png)

#### <span style="font-size: 12pt;">[Lifecycle Sorter](https://plugins.jetbrains.com/plugin/7742-lifecycle-sorter)

 可以根据Activity或者fragment的生命周期对其生命周期方法位置进行先后排序， windows 快捷键Ctrl + alt + K . Mac 快捷键 option + command + K.
![Lifecycle Sorter](https://plugins.jetbrains.com/files/7742/screenshot_15070.png)

#### <span style="font-size: 12pt;">[Android Code Generator](https://plugins.jetbrains.com/plugin/7595-android-code-generator)</span>

 根据布局文件快速生成对应的Activity，Fragment，Adapter，Menu。
![Android Code Generator](https://plugins.jetbrains.com/files/7595/screenshot_14834.png)

#### <span style="font-size: 12pt;">[Android Parcelable code generator](https://plugins.jetbrains.com/plugin/7332-android-parcelable-code-generator)</span>

 JavaBean 序列化，快速实现 Parcelable 接口。
![Android Parcelable code generator](https://segmentfault.com/image?src=http://img.blog.csdn.net/20160416104459926&objectId=1190000005092842&token=ab29ed79d41be9e42b3a3d2ed1ec3bef)

#### <span style="font-size: 12pt;">[CodeGlance](https://plugins.jetbrains.com/plugin/7275-codeglance)</span>

 在右边实现代码预览，类似于 sublime ，快速定位。
![CodeGlance](https://plugins.jetbrains.com/files/7275/screenshot_16821.png)

#### <span style="font-size: 12pt;">[FindBugs-IDEA](https://plugins.jetbrains.com/plugin/3847-findbugs-idea)</span>

 查找 bug 的插件。具体使用可见 freddyyao 的简书文章 -> [代码缺陷扫描神器——FindBugs](http://www.jianshu.com/p/bc27857c89e4)
![FindBugs-IDEA](https://plugins.jetbrains.com/files/3847/screenshot_2542.png)

#### <span style="font-size: 12pt;">[ADB WIFI](https://plugins.jetbrains.com/plugin/7856-adb-wifi)</span>

 使用wifi无线调试你的app，无需root权限
![ADB WIFI](https://plugins.jetbrains.com/files/7856/screenshot_15153.png)

#### <span style="font-size: 12pt;">[JSONOnlineViewer](https://plugins.jetbrains.com/plugin/7838-jsononlineviewer)</span>

 在 Android Studio 中，请求、调试接口
![JSONOnlineViewer](https://plugins.jetbrains.com/files/7838/screenshot_15113.png)

#### <span style="font-size: 12pt;">[Android Styler](https://plugins.jetbrains.com/plugin/7972-android-styler)</span>

 根据 xml 自动生成 style 代码的插件。 需要把要生成 style 的代码 copy 到 styles.xml 中，选中进行设置。
![](https://plugins.jetbrains.com/files/7972/screenshot_15340.png)
![](https://plugins.jetbrains.com/files/7972/screenshot_15339.png)
![Android Styler](https://plugins.jetbrains.com/files/7972/screenshot_15338.png)

#### <span style="font-size: 12pt;">[Android Drawable Importer](https://plugins.jetbrains.com/plugin/7658-android-drawable-importer)</span>

 这是一个非常强大的图片导入插件。它导入Android图标与Material图标的Drawable ，批量导入Drawable ，多源导入Drawable（即导入某张图片各种dpi对应的图片）
![](https://plugins.jetbrains.com/files/7658/screenshot_15533.png)
![](https://plugins.jetbrains.com/files/7658/screenshot_15532.png)
![](https://plugins.jetbrains.com/files/7658/screenshot_15534.png)
![](https://plugins.jetbrains.com/files/7658/screenshot_15536.png)
![Android Drawable Importer](https://plugins.jetbrains.com/files/7658/screenshot_15351.png)

#### <span style="font-size: 12pt;">[Genymotion](https://plugins.jetbrains.com/plugin/7269-genymotion)</span>

 一款速度较快的 Android 模拟器，可以在 Android Studio 中直接开启。
![Genymotion](https://plugins.jetbrains.com/files/7269/screenshot_14278.png)

#### <span style="font-size: 12pt;">[SQLScout](https://plugins.jetbrains.com/plugin/8322-sqlscout-sqlite-support-)</span>

 在 Android Studio 中调试数据库 (SQLite)

详细使用参考：[在 Android Studio 上调试数据库 ( SQLite )](https://juejin.im/post/58e0d781a0bb9f0069ec08d3)
![SQLScout](https://plugins.jetbrains.com/files/8322/screenshot_15823.png)

#### <span style="font-size: 12pt;">[GradleDependenciesHelperPlugin](https://github.com/ligi/GradleDependenciesHelperPlugin)</span>

 maven gradle 依赖支持自动补全
![GradleDependenciesHelperPlugin](http://upload-images.jianshu.io/upload_images/3303429-15b6eed60db1bbde?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### <span style="font-size: 12pt;">[RemoveButterKnife](https://github.com/u3shadow/RemoveButterKnife)</span>

 ButterKnife这个第三方库每次更新之后，绑定view的注解都会改变，从bind,到inject，再到bindview，搞得很多人都不敢升级，一旦升级，就会有巨量的代码需要手动修改，非常痛苦当我们有一些非常棒的代码需要拿到其他项目使用，但是我们发现，那个项目对第三方库的使用是有限制的，我们不能使用butterknife，这时候，我们又得从注解改回findviewbyid针对上面的两种情况，如果view比较少还好说，如果有几十个view，那么我们一个个的手动删除注解，写findviewbyid语句，简直是一场噩梦（别问我为什么知道这是噩梦）所以，这种有规律又重复简单的工作为什么不能用一个插件来实现呢？于是RemoveButterKnife的想法就出现了。
[具体介绍](http://www.u3coding.com/2016/06/24/androidstudio-plugin-removebutterknife-di/)

![RemoveButterKnife](http://upload-images.jianshu.io/upload_images/3303429-43ff9b5a2e3a1405?imageMogr2/auto-orient/strip)

#### <span style="font-size: 12pt;">[AndroidProguardPlugin](https://github.com/zhonghanwen/AndroidProguardPlugin)</span>

 一键生成项目混淆代码插件，值得你安装~
![AndroidProguardPlugin](https://camo.githubusercontent.com/adef227c0dc014f53b6e2a46977ef9ff2ceeb5a4/687474703a2f2f3778726e6b6f2e636f6d312e7a302e676c622e636c6f7564646e2e636f6d2f616e64726f696470726f6775617264312e676966)

#### <span style="font-size: 12pt;">[EventBus3 Intellij Plugin](https://plugins.jetbrains.com/plugin/8603-eventbus3-intellij-plugin)</span>

 为 EventBus 提供快速索引和跳转（目前只支持 EventBus 3.x 版本）
从 EventBus.post 到 @Subscribe 或者 onEventMainThread
从 @Subscribe 到 EventBus.post

![EventBus3 Intellij Plugin](https://plugins.jetbrains.com/files/8603/screenshot_16147.png)

#### <span style="font-size: 12pt;">[Android Studio Prettify](https://plugins.jetbrains.com/plugin/7405)</span>

 可以将代码中的字符串写在string.xml文件中
选中字符串鼠标右键选择图中所示
![Android Studio Prettify](https://plugins.jetbrains.com/files/7405/screenshot_14418.png)

#### <span style="font-size: 12pt;">[.ignore](https://plugins.jetbrains.com/plugin/7495--ignore)</span>

 在Git 中想要过滤掉一些不想提交的文件，可以把相应的文件添加到.gitignore 中，而.gitignore 这个Android Studio 插件根据不同的语言来选择模板，就不用自己在费事添加一些文件了，而且还有自动补全功能，过滤文件再也不要复制文件名了。
我们做项目的时候，并不是所有文件都是要提交的，比如构建的build 文件夹，本地配置文件，每个Module 生成的iml 文件，但是我们每次add，commit 都会不小心把它们添加上去，而gitignore 就是解决这种痛点的，如果你不想提交的文件，就可以在创建项目的时候将这个文件中添加即可，将一些通用的东西屏蔽掉。

![.ignore](https://plugins.jetbrains.com/files/7495/screenshot_14959.png)

#### <span style="font-size: 12pt;">[Markdown Navigator](https://plugins.jetbrains.com/plugin/7896-markdown-navigator)</span>

 markdown插件

![Markdown Navigator](https://plugins.jetbrains.com/files/7896/screenshot_15818.png)

#### <span style="font-size: 12pt;">[ECTranslation](https://plugins.jetbrains.com/plugin/8469-ectranslation)</span>

 Android Studio 翻译插件,可以将英文翻译为中文.

![ECTranslation](https://github.com/Skykai521/ECTranslation/raw/master/img/translation_img.png)

#### <span style="font-size: 12pt;">[Codota](http://www.codota.com/)</span>

 搜索最好的Android代码.

#### <span style="font-size: 12pt;">[Exynap](https://plugins.jetbrains.com/plugin/8600-exynap)</span>

 Exynap 一个帮助开发者自动生成样板代码的 AndroidStudio 插件
![Exynap](https://plugins.jetbrains.com/files/8600/screenshot_16141.png)

#### <span style="font-size: 12pt;">[MVPHelper](https://plugins.jetbrains.com/plugin/8507-mvphelper)</span>

 一款Intellj IDEA 和Android Studio的插件，可以为MVP生成接口以及实现类，解放双手。
具体请查看[Android Studio插件之MVPHelper，一键生成MVP代码](http://androidwing.net/index.php/27)一文
![MVPHelper](https://github.com/githubwing/MVPHelper/raw/master/img/mvp_presenter.gif)

#### <span style="font-size: 12pt;">[ADB Idea](https://plugins.jetbrains.com/plugin/7380-adb-idea)</span>

 一键清理缓存、卸载，重启 APP

![adb-idea.gif](http://upload-images.jianshu.io/upload_images/3303429-2989b94e026cc0cd.gif?imageMogr2/auto-orient/strip)
<!--more-->