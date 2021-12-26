翻译自：[A Developer's Guide to Flutter - DEV Community](https://dev.to/solutelabs/a-developer-s-guide-to-flutter-37f1)



从发布开始，Flutter 就备受关注。我们也很兴奋。我希望大量的非游戏之外的项目，可以转换到 Flutter 上，因此，我们也对团队进行相关培训。



由于 Flutter 的最新相关学习教程，分散在互联网的角落里，我们编写了我们自己的 Flutter 教程，来节省开发者的时间，基于此，可以快速的开发 Flutter 应用。



对于初学者，在本教程的内容中，你将学会：

- Fluuter：是什么以及为什么
- 设置 Flutter
- Dart 基础
- Flutter 基础
- Widgets
- Layout
- Widget 的响应
- 设计一个App：表单，手势，以及图片
- 列表&导航
- 网络工作
- JSON 和解析
- 依赖管理
- 状态管理
- 测试（单元测试及集成）



#### Flutter：是什么，如何工作以及为什么出现？

什么是 Flutter，它有什么不同？关于这一点，只需要知道，Flutter 可在任何带有屏幕的设备上工作，包括：



- iOS 和 Android
- Web 和桌面（Mac，Windows，以及 Ubuntu） - 甚至支持PWA
- 骑车
- Raspberry Pi（POC 平台）



有关这些内容，请查看下面的视频，这是一个不错的内容，与本地应用、混合应用做了比较，最后，该视频介绍了使用 Flutter 进行应用开发。



https://youtu.be/l-YO9CmaSUM



#### 设置 Flutter



Flutter 的设置相对简单，具体如何设置，取决于您所使用的系统；您可以通过 Flutter 官方教程了解如何配置。



https://flutter.dev/docs/get-started/install



但是如果您遇到一些问题，请查看以下链接：

https://github.com/flutter/floutt/wiki/workarounds-for-common-issuest#flutter-installation



我们要求在DART之前设置的原因是因为安装扑振时，您也可以安装DART，而且可以单独安装DART，这将是一个不必要的步骤。Flutter将决定将使用哪个DART版本，因此安装不同的DART版本也将是暧昧的。



一旦下载和解压缩扑动，您应该在控制台运行浮动命令时看到这样的内容：



![flutter development](https://res.cloudinary.com/practicaldev/image/fetch/s--Z0NRoy7O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AaIAn1ORPEBwUY1krrd8iDA.png)





如果您是新的移动开发，通常需要下载Xcode和Android Studio（和Toolchain）。一旦设置了扑动，脚手架一个新项目只是一个命令。



#### Dart 基础



Flutter 使用 Dart 语言来开发 App。要想了解选择使用 Dart 构建 Flutter 背后的动机，请查看上面的日志链接，和下面的章节： `Flutter 如何诞生`



https://blog.solutelabs.com/flutter-for-your-next-product-idea-everything-you-need-to-know-f5179a925524



Flutter 和 Chrome 使用同一个渲染引擎 - SKIA。与使用原生 API 不同，SKIA 控制屏幕上的每一个像素，这使得它有很大的自由度，以及性能。



阅读一下 [官方文档](https://flutter.dev/docs/resources/faq#why-did-flutter-choose-to-use-dart)，下面是我筛选出的对使用 Dart 这块阐述的比较好的文章：



[为什么 Flutter 使用 Dart](https://hackernoon.com/why-flutter-uses-dart-dd635a054ebf)



另外，你也可以在你的 medium 账号上，关注 Dart 的信息。



此时，你大概已经弄清楚为什么 Google 选择 Dart，接下来，我们来实干一波。



#### 学习Dart

查看 Dart 语言的官方文档，[手册](https://dart.dev/guides/language/language-tour)，以及 [语言示例](https://dart.dev/samples)。



您有了大概的了解之后，请访问 http://jpryan.me/dartbyexample/，并执行所有示例。



#### 练习，练习，练习！



在 [DartPad](https://dartpad.dartlang.org/) 上编辑您的代码，对于初学者而言，这是很好的提升能力的机会。我相信你会忙到没时间跑步。



![My First Hello, World! in Dart](https://res.cloudinary.com/practicaldev/image/fetch/s--mdZmTXeW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aj5k-sQWAqf0LHl_SUR3_Gg.png)



之后，您完成了Dartbyexample，始终转向行业，并完成他们的飞镖轨道。这是时尚的，所以如果它完全满了，你也可以做实践轨道。



[Dart | Exercism](https://exercism.io/tracks/dart)



#### Flutter 基本



现在，你已经熟悉 Dart 啦，是时候学习 Flutter 了。



先来看一个预览：



[Technical overview](https://flutter.dev/docs/resources/technical-overview)



使用脚手架创建一个 flutter app：



```
flutter create app_name
```



会有如下输出：



![android studio](https://res.cloudinary.com/practicaldev/image/fetch/s--nV911Awg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A3KBHP-3nKyS3XP8bTma2Yw.png)



使用 Android Studio 打开这个项目，如果没有成功运行，则需要先下载模拟器。



![Flutter demo](https://res.cloudinary.com/practicaldev/image/fetch/s--qCyMYCjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AdA0ntm5vz0xGyDYsp2-f7g.png)



接下来，需要了解项目的目录结构，以及不同文件的用处。



[Flutter Project Structure](https://dev.to/jay_tillu/flutter-project-structure-1lhe)



现在，你已经完成初始化 flutter，可以开始做一些开发人员的事情了：使用第三方代码。具体是指设置包文件，pubspec，使用 yaml 编写。



[The pubspec file](https://dart.dev/tools/pub/pubspec)



#### 组件



> 记住，在 Flutter 中，一切都是组件。



如果您没有阅读我们之前提到的技术概述，请返回去阅读一下。您会了解一些关于组件的概念。组件有两种：无状态组件和有状态组件。



无状态组件是指那些状态不会像按钮或图像那样，更改自身状态的组件。至于状态这个名字，它是指不会在屏幕上执行操作时更改其状态。



查看短视频系列，并通过谷歌的文档深入探索（我正在附上系列中的第一个视频）。



当窗口小部件需要在PAPEVIEW中像当前页面时保持某些状态时，底部显示的当前选中的选项卡，有状态小部件是正确的选择。





StatefulWidgets可以持有小部件的当前状态。代替窗口小部件构建方法，有状态窗口小部件具有一个状态构建方法，每次调用我们明确调用setState时调用。



同样，在此处查看文档（它有视频内部）：

[StatefulWidget class](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html)



Flutter 1.9 在GDD中国发布了一系列新的功能和社区乘以的标志（并且你现在不能忽视中国）



#### Flutter 中的布局



如前文所述，在 Flutter 中，一切都是 widget，包括布局组件。



查看此处的文档：



[Layouts in Flutter](https://flutter.dev/docs/development/ui/layout)



诸如行，列和网格之类的小部件是布局小部件（我们在屏幕上没有看到），帮助其他可见小部件排列，约束和对齐。



> ⏰ 是时候写一些 Flutter 代码了：[*编写你的第一个 Flutter App : Part-1*](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1/#0)



#### 以及更多的组件！



Flutter 包含了一揽子基础组件，如 Text，Column，Row，Stack 和 Container。基础组件可以帮助您根据您的需要，构建自定义组件。



如果你的 App 遵循 [material design 标准](https://material.io/design/guidelines-overview/)，Flutter 包含许多默认内容。Flutter 提供数个组件，支持 Material 设计，包括一些组件： [MaterialApp](https://api.flutter.dev/flutter/material/MaterialApp-class.html), [AppBar](https://api.flutter.dev/flutter/material/AppBar-class.html), [Scaffold](https://api.flutter.dev/flutter/material/Scaffold-class.html) 及其他。



![Material Navigation Drawer (https://material.io/components/navigation-drawer/#)](https://res.cloudinary.com/practicaldev/image/fetch/s--3tEXyTQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ANibO5ZD4c_QJMAHR.png)



Flutter 也包含了 iOS 风格的组件包： [Cupertino component](https://flutter.dev/docs/development/ui/widgets/cupertino) 。该组件包覆盖了包括  [CupertinoApp](https://api.flutter.dev/flutter/cupertino/CupertinoApp-class.html)，[CupertinoNavigationBar](https://api.flutter.dev/flutter/cupertino/CupertinoNavigationBar-class.html) 之类的组件。



![Cupertino NavigationBar (https://developer.apple.com/design/human-interface-guidelines/ios/bars/navigation-bars/)](https://res.cloudinary.com/practicaldev/image/fetch/s--8DrYiFpv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A3GR_8PDpRyl4Tvmv.png)



#### 响应式组件



到目前为止，我们已经看到了在屏幕上显示信息或安排其他小部件的小部件。对于真实的应用程序，使应用程序交互式并以各种形式获取用户的输入，同样重要的是，以手势，水龙头等。



为了实现这一目标，Flutter 拥有一些状态组件，例如 Checkbox，Radio，Slider，Inkwell，表单和输入框等。这些小部件足够可维持其状态（例如，我们在 TextField 中输入的文本，判断 Checkbox 是否被选中等。）



请查看以下示例以向您的应用添加收藏夹/非收藏夹功能。



[向你的 Flutter app 增加响应](https://flutter.dev/docs/development/ui/interactive)。



练习时间到了：[*Write your First Flutter App : Part-2*](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/#0)



> 正如您到达这里的那样，您应该清楚的是小部件？和小部件的类型



现在你一定是奇怪的是 Flutter 的所有小部件？



所以这里是一个小部件目录，检查所有 Flutter 的小部件放松和乐趣😍



[Widget catalog](https://flutter.dev/docs/development/ui/widgets)



#### Flutter Cookbook



在这里，我们走了，是时候学习真实应用程序的Flutter了。我的意思是具有多个屏幕，图像，网络依赖和全部的应用程序。



我们开始吧。



#### 设计一个 App



检查下面的app图。



![-----------------](https://res.cloudinary.com/practicaldev/image/fetch/s--5a56KpL---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AFVbUPfA8Q03E7_old_Yajw.gif)



这个简单的寻找应用程序具有这些功能👇



1. 导航
   https://flutter.dev/docs/cookbook/design/drawer
2. SnackBar
   https://flutter.dev/docs/cookbook/design/snackbars
3. 自定义字体
   https://flutter.dev/docs/cookbook/design/package-fonts
4. 基于文本的横向
   https://flutter.dev/docs/cookbook/design/orientation
5. 多个 Tabs
   https://flutter.dev/docs/cookbook/design/tabs



