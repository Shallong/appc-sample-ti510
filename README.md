# Titanium 5.1.0 应用范例

> **注意:** 此应用范例需要 Titanium 5.1.0, Alloy 1.7.26 and AppC CLI Core Package 5.1.0 或之后的版本才可正常运行。

[Titanium 5.1.0 应用范例](https://github.com/appcelerator-developer-relations/appc-sample-ti500) 演示了 Titanium 5.1.0 中大部分的新功能，包括了重构运行时权限模型，新 Material Design 元素，SafariDialog 以及一些 iOS 下的主要改变。

其中一个最令人兴奋的功能是 iPhone 6S 3D Touch 的支持与主屏快捷操作。我们很高兴的提供了一个单独的 [3D Touch 应用范例](https://github.com/appcelerator-developer-relations/appc-sample-3dtouch)。

所有功能改变列表请参照 [发布说明](http://docs.appcelerator.com/platform/release-notes/?version=5.1.0.GA)。

## 设备权限
这个版本重构了一个应用请求设备权限的方式，为的是支持新的 Android 6.0 在运行时请求设备权限的模型，以及保持 Android 和 iOS 平台的一致性。
Windows 平台的支持将会在以后的版本中添加。

### 例子
*Permissions* 页有4个按钮分别为 `Ti.Calendar`，`Ti.Contacts`，`Ti.Geolocation` 和 `Ti.Media` 供测试新的 `has*Permissions()` 以及 `request*Permissions` 方法。
可以参考 [permissions.js](app/controllers/permissions.js) 查看我们是如何同时使用新的方法和一些现存的方法来检查和申请权限的。
轻按右上角的 *Edit* 按钮以打开一个设置的窗口去改变你的权限请求的初始值。

### Android
Android 如果要在运行时请求一些权限的话，需要添加 [uses-permission](http://docs.appcelerator.com/platform/latest/#!/guide/tiapp.xml_and_timodule.xml_Reference-section-29004921_tiapp.xmlandtimodule.xmlReference-uses-permission) 元素到 `AndroidManifest.xml` 文件。不过，如果 Titanium 在你的代码中找到一个例如 `Ti.Media.showCamera()` 的请求的话，它会帮你自动完成这个配置，但如果你想使用新的 has- 和权限申请方法的话 [它并不会这么做](https://jira.appcelerator.org/browse/TIMOB-19933)。
一个你可能会碰到这个问题的情况是权限申请的代码不在你的 JavaScript 代码里，而是在模块里。如果是这样的话你就不得不手动地 [在 tiapp.xml 中申明权限](http://docs.appcelerator.com/platform/latest/#!/guide/tiapp.xml_and_timodule.xml_Reference-section-29004921_tiapp.xmlandtimodule.xmlReference-uses-permission)。

### iOS
在 Android 平台上，你可以很容易的通过删除和重装应用以获得一个空白的初始状态。
在 iOS 平台上，即使这个应用被删除了还是会记住之前的权限状态。
如果你想在 iOS 平台上获得一个空白的初始状态用于测试时，你可以修改 `tiapp.xml` 中的 APP ID 或者在 *设置 > 通用 > 还原 > 还原位置与隐私* 重置你 **所有** 应用的权限。

## 限定平台的 DefaultIcon
在 Titanium 5.0 中我们引入了 `DefaultIcon.png`，基于一个源文件就可以自动生成所有 iOS 应用图标。在 5.1 中我们也支持了 Windows 平台。伴随这个一起的是，我们引入了限定平台的 DefaultIcon。
iOS 需要的图标是24比特并且没有无透明通道。其他平台则支持甚至推荐32比特并且带有透明通道。
为了适配，这个应用范例还有个24比特的 [DefaultIcon-ios.png](DefaultIcon-ios.png) 以及一个用于 Windows 应用的32比特的 [DefaultIcon.png](DefaultIcon.png)。

![DefaultIcon](docs/defaulticon.png)

> **注意:** 此应用范例在 Windows 平台上并做不了什么除了展现 DefaultIcon。我们将很快会有另外一个例子，展示你可以混合使用 Titanium 视图以及由 [Runtime Direct API Access](http://docs.appcelerator.com/platform/latest/#!/guide/Windows_Runtime_Direct_API_Access) 所创建的视图。

## iOS
这个版本包含了诸多 iOS 的新功能。

### Safari Dialog
伴随着 iOS 9 的到来， [Apple 引入了](https://developer.apple.com/videos/play/wwdc2015-504/) [SFSafariViewController](https://developer.apple.com/library/prerelease/ios/documentation/SafariServices/Reference/SFSafariViewController_Ref/index.html)。
对于两种情况，1.用户离开你的应用而在 Safari 中打开一个 URL 和 2.在你的应用中通过使用 [Ti.UI.WebView](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.WebView) 来重造一个浏览器体验来说，这是一个不错的中间道路。你就把它当做是一个有着 webview 的骨子并且还带有一些 Safari 的特性，诸如阅读列表，自动完成等功能的弹出窗口好了。

![safaridialog](docs/safaridialog.png)

#### 例子
*iOS* 页前两个按钮分别在 Safari - 通过 [Ti.Platform.openURL](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.Platform-method-openURL) 打开，以及新的 Safari Dialog 中打开同一个 URL。好了，大家来“找茬”吧！

#### 使用
Safari Dialog 并不是 Titanium SDK 的一部分，但它被绑定在一个分开的模块中。这就意味着我们需要在 [tiapp.xml](tiapp.xml#L81) 中添加这个模块并且在 [ios.js](app/controllers/ios.js) 中引入使用。你可以发现它有一个 `open` 以及一个 `close` 事件，并且有方法检查是否支持当前的 iOS 版本，和是否对话框已经打开了。你也可以使用编程的方式来关闭对话框，就像我们做的那样，5秒钟之后关闭。你可以设置对话框中按钮的 tintColor，以适配你的应用，最后如果需要的话可以自动打开阅读列表。

### Picker
你现在可以在创建时就改变 [Ti.UI.Picker](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.Picker-property-height) 的高度了。在我们的例子中，有一个 [设置一个高50的 date-picker](app/views/ios.xml) 以至于它每次只显示一行。

### AlertDialog
从这个版本开始，当使用提示对话框类型为 `PLAIN_TEXT_INPUT` 或 `SECURE_TEXT_INPUT` 时，你可以设置 [placeholder](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.AlertDialog-property-placeholder)，[keyboardType](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.AlertDialog-property-keyboardType) 以及 [returnKeyType](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.AlertDialog-property-returnKeyType) 属性，这些属性你可能已经在 `Ti.UI.TextField` 和 `Ti.UI.TextArea` 中见过了。对于 `LOGIN_AND_PASSWORD_INPUT` 属性来说，你需要在这些属性前加上 `login` 和 `password` 的前缀。

我们例子中的 *iOS* 页演示了所有这3个属性。参考 [ios.js](app/controllers/ios.js) 中 `alertDialog()` 回调实现吧。

### UserNotificationAction TEXTINPUT
还记得 [交互式通知中心例子](https://github.com/appcelerator-developer-relations/appc-sample-notifywatch) 吗？
在这个版本中你可以把新的属性 [behavior](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.App.iOS.UserNotificationAction-property-behavior) 设置到 [TEXTINPUT](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.App.iOS-property-USER_NOTIFICATION_BEHAVIOR_TEXTINPUT) 上让用户可以在通知中心里面直接输入并发送消息：

![textinput](docs/textinput.png)

我（指原作者Fokke Zandbergen）已经更新了 [交互式通知中心例子](https://github.com/appcelerator-developer-relations/appc-sample-notifywatch) 例子，你现在可以通过通知中心直接回复一个自定义的消息，也可以从 Apple Watch 中回复：

![textinput_watch](docs/textinput_watch.png)

在我们5.1.0例子中， *iOS* 页也可以让你测试这个虽然看起来不大，但却很有意义的新功能。只要点击 *TEXTINPUT* 按钮以及快速的锁定你的设备 (模拟器中按 `⌘L`) 或者按 Home 键 (`⇧⌘H`) 回到主页上。我们在 [ios.js](app/controllers/ios.js) 中监听的 `localnotificationaction` 事件将会在 input 中有一个新的 `typedText` 属性。

## 更新版本之中…
另外包含在这个版本中的还有一些对 iOS 的主要更新。

### 应用瘦身
自从 Titanium 5.0 以来，我们为 iOS 应用图标及启动图片生成了资源目录 这是为了支持 [应用瘦身](https://developer.apple.com/library/tvos/documentation/IDEs/Conceptual/AppDistributionGuide/AppThinning/AppThinning.html) 的第一步，应用瘦身指的是在 App Store 里面，只有适合一台设备的资源会被打包成一个唯一的应用并提供下载。

在 5.1 中，我们引入了也为常规图片资源提供应用瘦身的 *选项* 。
我们使用了原始图片资源名称的哈希值以便你可以在图片视图和背景中如往常一样使用他们。但你将不能在通过 `Ti.Filesystem` 访问到它们。
因为这样一个突发改动，它在 [tiapp.xml](tiapp.xml) 中是可选的，你也可以选择使用这个新特性。

#### 例子
用或不用应用瘦身来创建的应用 (构建与构建间是保持干净的: [TIMOB-19968](https://jira.appcelerator.org/browse/TIMOB-19968)) 在 *iOS* 页上都可以使图片视图正常工作，但 [ios.js](app/controllers/ios.js) 中的 `Ti.Filesystem` 却不能在激活应用瘦身的情况下找到相应的资源了。

### JS 主线程
出于几个理由，我们之前只在一个专用的 *KrollThread* 线程上运行 JavaScript 引擎。现在我们提供了可以跑在主线程上的选项了。这会让我们摒弃掉当前 SDK 中很多工作方式，并且可以改善效率和稳定性。然后，这也会产生 [不可预期的行为](https://jira.appcelerator.org/browse/TIMOB-19760)。一些是我们需要解决的问题，但更多的是你需要理解线程与回调并优化你的代码运行在 UI 的单线程上。

#### Making the most of your Single Thread
Titan [Ronald Treur](https://twitter.com/ronaldtreur) 在 tiConf EU 2014 上给了一个出色的演讲，关于 [making the most of your Single Thread](http://www.slideshare.net/ronaldtreur/titanium-making-the-most-of-your-single-thread)。你可以从他的 PPT 中看到，Titanium 代理的请求现在也会在相同的请求栈中，并且会在他们运行前等待栈中的代码块执行完毕之后。

[![callstack](docs/callstack.png)](http://www.slideshare.net/ronaldtreur/titanium-making-the-most-of-your-single-thread)

#### 例子
*iOS* 页上最后一个测试将会循环产生数千条 UUIDs 在一个数组中。测试 tiapp.xml 中在激活和不激活主线程的情况下，查看 [ios.js](app/controllers/ios.js) 里 `testThread()` 回调方法中 unoptimized（未经优化） 代码的表现，结果显示如果激活了主线程的话，进度条在所有操作都完成前是不会更新的。
这是因为更新进度条的请求被放在了栈里，直到所有的 `testThread()` 都运行完成了才会更新，这就使得用户看起来像所有操作都完成后进度条才一下子更新一样。

在两个 optimized（经过优化）的版本中，数组每一次的迭代都会被分别推到回调栈中。这就意味着在每次迭代后，进度条都会更新。不过在主线程禁用的情况下仍然不是立即更新的，但是还是有相似的结果。就像 Ronald 解释的那样，也可以使用 JavaScript 的 [setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/Window/setTimeout) 方法不带 (`0ms`) 的延迟达成这个效果。与 Alloy 捆绑在一起的 Underscore 中的 [defer()](http://underscorejs.org/#defer) 方法和总所周知的 [NPM上的异步请求包](https://www.npmjs.com/package/async) 都可以包住这个方法。你可以发现异步的 `eachSeries()` 比 Underscore 的 `defer()` 来更快，代码也更加干净。

### Auto Layout
在这个版本中你也可以选择使用 Apple 的 Auto Layout 功能。
这将会在以后的版本中允许我们支持 [Slide Over and Split View](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForSlideOverAndSplitView.html) 效果。

我们隐藏了 [编程式创建约束](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html) 的复杂的 API，这样你就可以继续使用 `top`，`left` 等简单的设置，并把剩下的都交给我们来处理。
由于这个对我们的布局系统是一个相当戏剧性的版本，所以现在是可选的，可以通过 [tiapp.xml](tiapp.xml) 来激活它。
请测试你的应用以及 [报告任何可能的问题](https://jira.appcelerator.org/)。

#### 例子
*Auto Layout* 页演示了这个改动的好处。
可以运行模拟器中的应用，激活或不激活 [tiapp.xml](tiapp.xml) 中 Auto Layout 的选项，设置 *调试 > 慢动画效果* (`⌘T`) 和使用 (`⌘→`) 来改变设备的方向来观察在设备旋转的情况下激活 Auto Layout 的视图是怎样慢慢转变的，我们也修复了旧布局系统中当底部百分比没有被正确计算时的一个 bug：

![autolayout](docs/autolayout.gif)

## Android: Material Design
这个版本引入了一些新的 Material Design 的组件，更新了按钮及设置面板来使用 [AppCompat库](http://developer.android.com/tools/support-library/features.html#v7-appcompat)，这样在旧版本上也可以看到一致的样式了。

### 卡片视图
Android [卡片](https://www.google.com/design/spec/components/cards.html) 用在显示多样化（独一无二）的内容块，显示为圆角以及俯视图效果。用 [Ti.UI.Android.createCardView](https://appcelerator.github.io/appc-docs/latest/#!/api/Titanium.UI.Android.CardView) 或 Alloy 1.7.20以后的 `<CardView>` 来创建它们。

![cardview](docs/cardview.png)

> **注意:** 有一些卡片视图的 [已知问题](https://jira.appcelerator.org/issues/?filter=17132)，当前最显著的一个问题是 contentPadding 和 cardCornerRadius 会忽略默认单位而使用像素。这将会在下一个补丁版本中修复。

#### 例子
*CardView* 页演示了所有你可以使用的不同的属性。它也讨论了 `cardUserCompatPadding`，`cardPreventCornerOverlap` 和 `cardMaxElevation` 是怎样决定 Android 4 上的效果的以及在旧版本中使用 AppCompat 库。参考 [Android Reference](http://developer.android.com/reference/android/support/v7/widget/CardView.html) 了解更多重要的属性吧。

### 揭露效果
另一个新的 Material Design 元素是 Android 5 及以后版本中的揭露效果。在 *Android* 页中按第一个按钮去观察当你给方法传入 `animated:true` 参数时，方块元素是怎样使用揭露效果显示和隐藏的：

![reveal](docs/reveal.gif)

### 进度条颜色
*Android* 页也会演示现在你可以给进度条设置颜色了。点击进度条就可以看到它填充了（Appcelerator）的主色调。

### AppCompat 按钮 & Preferences Dialog
多亏了 AppCompat 库，不同 Android 版本中的按钮不再像它们之前一样看起来都不一样了：

![buttons](docs/buttons.png)

对于 [Preferences Dialog](http://developer.android.com/guide/topics/ui/settings.html#DefiningPrefs) 来说也是一样，现在也可以在旧 Android 版本中有工具栏了。
如果你不是很熟悉 Preferences Dialog 的话，可以查看 [我们的文档](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.Android-method-openPreferences) 以及查看我们是怎么在 [preferences.xml](platform/android/res/xml/preferences.xml) 文件中定义对话框的。

#### 测试
为了查看在 Titanium 5.1 中构建不同 Android 版本以查看相同按钮效果的话，打开 [tiapp.xml](tiapp.xml) 文件，删除 `<uses-sdk>` 周围的注释，修改 `<sdk-version>` 到4.1.1或更老的版本。再次构建，就可以看到像上面截图那样不同 Android 版本中的按钮样式的效果了。
