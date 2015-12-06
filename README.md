# Titanium 5.1.0 应用范例

> **注意:** 此应用范例需要 Titanium 5.1.0, Alloy 1.7.26 and AppC CLI Core Package 5.1.0 或之后的版本才可正常运行。

[Titanium 5.1.0 应用范例](https://github.com/appcelerator-developer-relations/appc-sample-ti500) 演示了 Titanium 5.1.0 中大部分的新功能，包括了重构运行时权限模型，新 Material Design 元素，SafariDialog 以及一些 iOS 下的主要改变。

其中一个最令人兴奋的功能是 iPhone 6S 3D Touch 的支持与主屏快捷操作。我们很高兴的提供了一个单独的 [3D Touch 应用范例](https://github.com/appcelerator-developer-relations/appc-sample-3dtouch)。

所有功能改变列表请参照 [Release Notes](http://docs.appcelerator.com/platform/release-notes/?version=5.1.0.GA)。

## 设备权限
这个版本重构了一个应用请求设备权限的方式为的是支持新的 Android 6.0 在运行时请求设备权限的模型，以及保持 Android 和 iOS 平台的一致性。Windows 平台的支持将会在以后的版本中添加。

### 例子
*Permissions* 页有4个按钮为 `Ti.Calendar`，`Ti.Contacts`，`Ti.Geolocation`和`Ti.Media` 供测试新的 `has*Permissions()` 以及 `request*Permissions` 方法。可以参考 [permissions.js](app/controllers/permissions.js) 我们是如何同时使用新的方法和一些现存的方法来检查和申请权限的。轻按左上角的 *Edit* 按钮以打开一个设置的窗口去改变你的权限请求的初始值。

### Android
Android 如果要在运行时请求一些权限的话，需要添加 [uses-permission](http://docs.appcelerator.com/platform/latest/#!/guide/tiapp.xml_and_timodule.xml_Reference-section-29004921_tiapp.xmlandtimodule.xmlReference-uses-permission) 元素到 `AndroidManifest.xml`。不过 Titanium 会帮你自动完成这个配置如果它在你的代码中找到一个例如 `Ti.Media.showCamera()` 的请求的话，但如果你想使用新的 has- 和权限申请方法的话 [它并不会这么做](https://jira.appcelerator.org/browse/TIMOB-19933)。一个你可能会碰到这个问题的情况是权限申请的代码不在你得 JavaScript 代码里，而是在模块里。如果是这样的话你就不得不手动地 [在 tiapp.xml 中申明权限](http://docs.appcelerator.com/platform/latest/#!/guide/tiapp.xml_and_timodule.xml_Reference-section-29004921_tiapp.xmlandtimodule.xmlReference-uses-permission)。

### iOS
在 Android 平台上，你可以很容易的删除和重装应用以获得一个空白状态。在 iOS 平台上，即使这个应用被删除了还是会记住之前的权限状态。如果你想在 iOS 平台上获得一个空白状态用于测试时，你可以改变 `tiapp.xml` 中的 APP ID 或者在 *设置 > 通用 > 还原 > 还原位置与隐私* 重置你 **所有** 应用的权限。

## 限定平台的 DefaultIcon
在 Titanium 5.0 中我们引入了 `DefaultIcon.png`，基于一个源文件就可以自动生成所有 iOS 应用图标。在 5.1 中我们也支持了 Windows 平台。和这个一起的是我们引入了限定平台的 DefaultIcon。iOS 需要图标是24-比特并且没有无透明通道。其他平台则支持或甚至推荐32-比特并且带有透明通道。为了适配，这个应用范例还有个24-比特的 [DefaultIcon-ios.png](DefaultIcon-ios.png) 以及一个用于 Windows 应用的32-比特的 [DefaultIcon.png](DefaultIcon.png)。

![DefaultIcon](docs/defaulticon.png)

> **注意:** 此应用范例在 Windows 平台上并做不了什么除了展现 DefaultIcon。我们将很快会有另外一个例子，展示你可以混合使用 Titanium 视图以及由 [Runtime Direct API Access](http://docs.appcelerator.com/platform/latest/#!/guide/Windows_Runtime_Direct_API_Access) 创建的视图。

## iOS
这个版本包含了诸多 iOS 的新功能。

### Safari Dialog
伴随着 iOS 9， [Apple 引入了](https://developer.apple.com/videos/play/wwdc2015-504/) [SFSafariViewController](https://developer.apple.com/library/prerelease/ios/documentation/SafariServices/Reference/SFSafariViewController_Ref/index.html)。对于两种情况，1.用户离开你的应用而在 Safari 中打开一个 URL 和 2.在你的应用中通过使用 [Ti.UI.WebView](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.WebView) 来重造一个浏览器体验，来说是一个不错的中间道路。就把它当做是一个弹出窗口，有着 webview 并且还有一些 Safari 的特性，诸如阅读列表，自动完成等功能。

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
还记得 [交互式通知中心例子](https://github.com/appcelerator-developer-relations/appc-sample-notifywatch) 吗？ With this release you can set an action's new [behavior](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.App.iOS.UserNotificationAction-property-behavior) property to [TEXTINPUT](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.App.iOS-property-USER_NOTIFICATION_BEHAVIOR_TEXTINPUT) to to have the user type and send a message directly from the notification:

![textinput](docs/textinput.png)

I've updated the [Interactive Notifications Sample](https://github.com/appcelerator-developer-relations/appc-sample-notifywatch) so that you can reply with a custom chat message directly from the notification or even dictate it from your Apple Watch:

![textinput_watch](docs/textinput_watch.png)

The *iOS* tab of our 5.1.0 sample allows you to test this small but significant new feature as well. Just hit the *TEXTINPUT* button and quickly lock your phone (`⌘L` in Simulator) or press the home button (`⇧⌘H`) to move the app to the background. The `localnotificationaction` event we listen to in [ios.js](app/controllers/ios.js) will have the input in its new `typedText` property.

## Under the hood
Also under the hood this release has a few major improvements for iOS. All three of them are optional for now.

### App Thinning
Since Titanium 5.0 we generate asset catalogs for iOS app icons and launch images. This was the first step to support [app thinning](https://developer.apple.com/library/tvos/documentation/IDEs/Conceptual/AppDistributionGuide/AppThinning/AppThinning.html), a process in which the App Store generates a unique app with only the resources appropriate for the device which is downloading your app.

With 5.1 we introduce the *option* to enable app thinning for regular image assets as well. We use a hash of the original image path as the asset catalog name so that you can use them in ImageViews and backgrounds as usual. But you will no longer be able to access images via `Ti.Filesystem`. Because of this breaking change it is optional for now and can be enabled via [tiapp.xml](tiapp.xml).

#### Sample
Build the app with and without app thinning (and clean in between builds: [TIMOB-19968](https://jira.appcelerator.org/browse/TIMOB-19968)) to see that the ImageView on the *iOS* tab will always work, but `Ti.Filesystem` in [ios.js](app/controllers/ios.js) won't be able to find the file when it's enabled.

### JS Main Thread
For several reasons, we used to run the JavaScript engine on a dedicated *KrollThread*. We now give you the option to run it on the Main Thread instead. This will allow us to get rid of many workarounds in our SDK and improve its performance and stability. However, it can also produce [unexpected behavior](https://jira.appcelerator.org/browse/TIMOB-19760). Some of this are issues we need to solve, but a lot of is also a matter of understanding threads and callstacks and optimizing your code to run on a single thread with the UI.

#### Making the most of your Single Thread
Titan [Ronald Treur](https://twitter.com/ronaldtreur) gave an excellent talk about [making the most of your Single Thread](http://www.slideshare.net/ronaldtreur/titanium-making-the-most-of-your-single-thread) at tiConf EU 2014. As you go through his slides, remember that calls to Titanium proxies will now be on the same callstack and will have to wait till the stacked codeblocks have been executed before they run.

[![callstack](docs/callstack.png)](http://www.slideshare.net/ronaldtreur/titanium-making-the-most-of-your-single-thread)

#### Sample
The last test on the *iOS* tab will loop over an array to generate thousands of UUIDs. Test with and without main thread enabled in tiapp.xml to see how with unoptimized code found in the `testThread()` callback in [ios.js](app/controllers/ios.js) the progress bar won't update until all operations are finished if main thread is enabled. This is because the calls to update the progress bar are stacked until after all of `testThread()` is done. To the user it will look like the progress bar fills up at once after waiting for all operations to be finished.

In both of the two optimized versions each iteration on the array is pushed to the callstack separately. This means that the updates to the progress bar can be executed after each iteration. Still not inmediately as with main thread disabled, but with similar result. As Ronald explains this can be achieved with JavaScript's [setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/Window/setTimeout) with no (`0ms`) delay. Both Underscore's [defer()](http://underscorejs.org/#defer) bundeled with Alloy and the well-known [async package on NPM](https://www.npmjs.com/package/async) wrap this method. As you will see async's `eachSeries()` is faster (and also cleaner in code) then Underscore's `defer()`.

### Auto Layout
With this release you also have the option to use Apple's Auto Layout. This will allow us to support [Slide Over and Split View](https://developer.apple.com/library/prerelease/ios/documentation/WindowsViews/Conceptual/AdoptingMultitaskingOniPad/QuickStartForSlideOverAndSplitView.html) in a future release.

We hide the complex API for [Programmatically Creating Constrains](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html) so you can continue to use `top`, `left` etc and leave the rest up to us. Since this is quite a dramatic revision of our layout system it is optional for now and can be enabled via [tiapp.xml](tiapp.xml). Please test it with your apps and [report any issues](https://jira.appcelerator.org/).

#### Sample
The *Auto Layout* tab demonstrates one of the benefits of this change. Run the app in the Simulator both with and without the option enabled in [tiapp.xml](tiapp.xml), activate *Debug > Slow Animations* (`⌘T`) and change the orientation of the device (`⌘→`) to see how with Auto Layout enabled the views slowly transition as the device rotates and also fixes a bug in the old layout system where the bottom percentage is not correctly calculated:

![autolayout](docs/autolayout.gif)

## Android: Material Design
This release introduces a few new Material Design components and updates buttons and preference panes to use the [AppCompat library](http://developer.android.com/tools/support-library/features.html#v7-appcompat) for them to look identical on older Android versions as well.

### CardView
Android [Cards](https://www.google.com/design/spec/components/cards.html) are meant to display heterogeneous (unique) content blocks and displayed with rounded corners and elevation. Create them with [Ti.UI.Android.createCardView](https://appcelerator.github.io/appc-docs/latest/#!/api/Titanium.UI.Android.CardView) or `<CardView>` since Alloy 1.7.20.

![cardview](docs/cardview.png)

> **NOTE:** There are a few [known issues](https://jira.appcelerator.org/issues/?filter=17132) with CardView, most prominently of which is that at the moment contentPadding and cardCornerRadius ignore the default unit and use pixels. This will be addressed in the next patch release.

#### Sample
The *CardView* tab demonstrates all of the different properties that you can use. It also discusses how `cardUserCompatPadding`, `cardPreventCornerOverlap` and `cardMaxElevation` determine how the cards look on Android 4 and older using the AppCompat library. Read more about these important properties in the [Android Reference](http://developer.android.com/reference/android/support/v7/widget/CardView.html).

### Reveal effect
Another new Material Design element is the reveal effect for Android 5 and later. Go to the *Android* tab and tap the first button to see how the square uses the reveal effect to hide and show when you pass `animated:true` to the methods:

![reveal](docs/reveal.gif)

### ProgressBar Color
As the *Android* tab also demonstrates you can now set the color for the ProgressBar. Tap the bar to see it fill up in our primary brand color.

### AppCompat Button & Preferences Dialog
Thanks to the AppCompat library the buttons throughout the app no longer look different between Android versions like they used to:

![buttons](docs/buttons.png)

The same is true for the [Preferences Dialog](http://developer.android.com/guide/topics/ui/settings.html#DefiningPrefs), which now has an Action Bar on older Android versions as well. If you're not familiar with the Preferences Dialog, check [our documentation](http://docs.appcelerator.com/platform/latest/#!/api/Titanium.UI.Android-method-openPreferences) and see how we define the dialog in [preferences.xml](platform/android/res/xml/preferences.xml).

#### Testing
To see the differences build the app with Titanium 5.1 for several Android versions to see the buttons always look the same. Then open [tiapp.xml](tiapp.xml), remove the comments around `<uses-sdk>` and change the `<sdk-version>` to 4.1.1 or older. Build again for different Android versions to see that the buttons are like the above screenshot.
