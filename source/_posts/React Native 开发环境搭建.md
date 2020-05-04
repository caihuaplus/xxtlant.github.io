---
title: React Native 开发环境搭建
id: 3
date: 2017-11-05 15:45:48
urlname: RN_develop_environment_building
tags: 
	- React Native
categories: React Native
cover: http://upload-images.jianshu.io/upload_images/281665-226ddf6afab5f41b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
---

## 什么是 React Native
> React Native (简称 RN)是 Facebook 于 2015 年开源的一套跨平台，动态更新的 JavaScript 框架。
> 口号是：Learn once, write anywhere: Build mobile apps with React
> 着力于提高多平台开发的开发效率 —— 仅需学习一次，编写任何平台。

 <!--more-->

## 搭建开发环境
我用的电脑是 MacBook,搭建的环境是 MacOS + Android 。
其他例如搭建 iOS, Windows, Linux 的环境，请参考 [React Native 中文网的搭建开发环境](http://reactnative.cn/docs/0.49/getting-started.html#content) , [React Native 官方的 Getting Started](http://facebook.github.io/react-native/docs/getting-started.html) 这两篇文章。

### 安装
#### 安装必须的软件 
> 需要安装 Node.js , Watchman, React Native命令行界面, JDK 和 Android Studio

##### HomeBrew
[Homebrew](https://brew.sh/), Mac 系统的包管理器，用于安装 NodeJS 和其他一些必需的工具软件
> /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

直接复制上述命令，在终端运行就可以了。
译注：在Max OS X 10.11（El Capitan)版本中，homebrew在安装软件时可能会碰到/usr/local目录不可写的权限问题。可以使用下面的命令修复：
> sudo chown -R \`whoami\` /usr/local

##### Node
使用 HomeBrew 来安装[Node.js](https://nodejs.org/en/)
> brew install node

安装完 node 后建议设置 npm 镜像以加速后面的过程（或使用科学上网工具）。注意：不要使用 cnpm！cnpm 安装的模块路径比较奇怪，packager不能正常识别！
>npm config set registry https://registry.npm.taobao.org --global
>npm config set disturl https://npm.taobao.org/dist --global

##### Yarn、React Native的命令行工具（react-native-cli）
[Yarn](https://yarnpkg.com/zh-Hans/) 是 Facebook 提供的替代 npm 的工具，可以加速 node 模块的下载。React Native 的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。
> npm install -g yarn react-native-cli

安装完 yarn 后也要设置镜像源:
>yarn config set registry https://registry.npm.taobao.org --global
>yarn config set disturl https://npm.taobao.org/dist --global

如果你看到 **`EACCES: permission denied`** 这样的权限报错，那么请参照上文的 homebrew 译注，修复 `/usr/local` 目录的所有权：
> sudo chown -R \`whoami\` /usr/local

安装完yarn之后就可以用 `yarn` 代替 `npm` 了，例如用 `yarn` 代替 `npm install` 命令，用 `yarn add` 某第三方库名代替 `npm install --save` 某第三方库名。 

##### Java 开发工具包
React Native需要最新版本的Java SE开发工具包（JDK）。如果需要，[下载并安装JDK 8或更高版本](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。
##### Android 开发环境
###### 1. 安装 Android Studio
下载并安装Android Studio 。如果可以科学上网，可以在[Android Developers](https://developer.android.google.cn/index.html) 下载，也可以在 [Android Studio 中文社区](http://www.android-studio.org/) 进行下载。
Android Studio包含了运行和测试React Native应用所需的Android SDK和模拟器。
>除非特别注明，请不要改动安装过程中的选项。比如Android Studio默认安装了 Android Support Repository，而这也是React Native必须的（否则在react-native run-android时会报appcompat-v7包找不到的错误）。

安装过程中需要改动的选项:

- 选择 `Custom` 选项:
    ![选择 Custom](http://reactnative.cn/static/docs/0.49/img/react-native-android-studio-custom-install.png)
- 勾选 `Performance` 和 `Android Virtual Device`
    ![选择](http://reactnative.cn/static/docs/0.49/img/react-native-android-studio-additional-installs.png)
- 安装完成后，在 Android Studio 的启动欢迎界面中选择 **Configure | SDK Manager**。
    ![选择 SDK Manager](http://reactnative.cn/static/docs/0.49/img/react-native-android-studio-configure-sdk.png)
- 在 `SDK Platforms` 窗口中，选择 `Show Package Details`，然后在Android 6.0 (Marshmallow)中勾选
    - **Google APIs**
    - **Android SDK Platform 23**
    - **Intel x86 Atom_64 System Image**
    - **Google APIs Intel x86 Atom_64 System Image**
![勾选指定项](http://reactnative.cn/static/docs/0.49/img/react-native-android-studio-android-sdk-platforms.png)
- 在 **SDK Tools** 窗口中，选择 Show Package Details，然后在 Android SDK Build Tools 中勾选 **Android SDK Build-Tools 23.0.1（必须是这个版本）**。然后还要勾选最底部的 **Android Support Repository**.
    ![勾选 SDK Tools](http://reactnative.cn/static/docs/0.49/img/react-native-android-studio-android-sdk-build-tools.png)

###### 2. ANDROID_HOME 环境变量
确保 **ANDROID_HOME** 环境变量正确地指向了你安装的 Android SDK 的路径。具体的做法是把下面的命令加入到 `~/.bash_profile` 文件中：(**译注**：~ 表示用户目录，即`/Users/你的用户名/`，而小数点开头的文件在 Finder 中是隐藏的，并且这个文件有可能并不存在。请在终端下使用 `vi ~/.bash_profile` 命令创建或编辑。如不熟悉 vi 操作，请点击[这里](http://www.eepw.com.cn/article/48018.htm) 学习）。如果你的命令行不是bash，而是例如 zsh 等其他，请使用对应的配置文件。
> 如果你不是通过Android Studio安装的sdk，则其路径可能不同，请自行确定清楚。
>export ANDROID_HOME=~/Library/Android/sdk

然后使用下列命令使其立即生效（否则重启后才生效）：
> source ~/.bash_profile

可以使用 **`echo $ANDROID_HOME`** 检查此变量是否已正确设置。

##### Watchman
[Watchman](https://facebook.github.io/watchman/docs/install.html)是由 Facebook 提供的监视文件系统变更的工具。安装此工具可以提高开发时的性能（packager可以快速捕捉文件的变化从而实现实时刷新）。

> brew install watchman

##### 将Android SDK的Tools目录添加到PATH变量中
把 Android SDK 的 tools 和 platform-tools 目录添加到 **PATH** 变量中，以便在终端中运行一些 Android 工具，例如 `android avd` 或是 `adb logcat` 等。具体做法仍然是在 `~/.bash_profile` 中添加：
> export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools

#### 其他可选的安装项
##### Git 
Git 版本控制。如若没有，则使用下列命令安装：
> brew install git

##### Nuclide
[Nuclide](https://nuclide.io/) （此链接需要科学上网）是由 Facebook 提供的基于 **atom** 的集成开发环境，可用于编写、[运行](http://nuclide.io/docs/platforms/react-native/#running-applications)和 [调试](https://nuclide.io/docs/platforms/react-native/#debugging)React Native应用。

点击这里阅读[Nuclide的入门文档](http://nuclide.io/docs/quick-start/getting-started/)。

##### Genymotion
Genymotion是一个性能更好的选择，但它只对个人用户免费。

1. 下载和安装[Genymotion](https://www.genymotion.com/download)（genymotion需要依赖VirtualBox虚拟机，下载选项中提供了包含VirtualBox和不包含的选项，请按需选择）。
2. 打开Genymotion。如果你还没有安装VirtualBox，则此时会提示你安装。
3. 创建一个新模拟器并启动。
4. 启动React Native应用后，可以按下⌘+M来打开开发者菜单

##### Gradle Daemon
开启 [Gradle Daemon](https://docs.gradle.org/2.9/userguide/gradle_daemon.html) 可以极大地提升java代码的增量编译速度。
> touch ~/.gradle/gradle.properties && echo "org.gradle.daemon=true" >> ~/.gradle/gradle.properties

## 测试安装
> react-native init AwesomeProject

> cd AwesomeProject

> react-native run-android

> 提示：你可以使用--version参数创建指定版本的项目。例如 react-native init MyApp --version 0.39.2。注意版本号必须精确到两个小数点.

你也可以在 [Nuclide](https://nuclide.io/) 中打开AwesomeProject文件夹然后[运行](http://nuclide.io/docs/platforms/react-native/#running-applications)。

> 注意，react-native run-android 时，需要打开一个 Android 设备的模拟器，不然会报错

### 其他问题。
如果看完这些，还有其他问题，请查阅 [React Native 中文网](http://reactnative.cn/)，里面有详细的使用文档。
或者参阅 [Facebook 的 React Native 的官方文档](http://facebook.github.io/react-native/).

我也是一个初学者，谢谢。

 <!--more-->
