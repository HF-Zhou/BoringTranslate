# 调试 #

当你写一个应用程序，你将不可避免地犯错误。 更糟糕的是，您的应用程序设计中会时不时地出现错误。 `Xcode` 的调试器（称为 `LLDB`）是帮助您找到这些错误并修复它们的基本工具。 本章介绍了 `Xcode` 的调试器及其基本功能。

## 一个 Buggy 项目 ##

您将使用一个简单的项目来引导你使用 Xcode 调试器。 打开 Xcode 并用 iOS `single view application` 创建一个新项目。 将项目命名为 `Buggy`，并确保将语言设置为 `Swift`，设备设置为 `iPhone`，并且 `Use Core Data`，`Include Unit Tests` 和 `Include UI Tests` 均选中（图9.1）。 点击 `Next`。

**图9.1 配置Buggy**

![](http://upload-images.jianshu.io/upload_images/1230738-db0e2936ef538029.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当你写这个应用程序的代码时，请记住它是一个错误的项目。 您可能会被要求输入您认为不正确的代码。 在您输入时不要修复它; 这些错误将帮助您了解调试技巧。

现在就开始吧，打开 `Main.storyboard` 并将 **UIButton** 拖动到 `View Controller Scene` 上。 双击新按钮，将其标题更改为 `Tap me!`。仍然选择此按钮，打开自动布局 `Align` 菜单。 选择 `Horizontally in Container`，然后单击 `Add 1 Constraint`。 接下来，打开 `Add New Constraints` 菜单。点击到容器顶部的线，选择 `Width` 和 `Height`，然后单击 `Add 3 Constraints`。

您的结果应如图9.2 所示，如果您的实际尺寸和间距有所不同，不要担心。

**图9.2 Tap me 按钮的自动布局约束！**

![](http://upload-images.jianshu.io/upload_images/1230738-aff01b5e88776e24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在，您需要实现一个方法来触发此按钮，然后将其连接到故事板中的按钮。

打开 ViewController.swift 并实现一个使按钮触发的动作方法。

> **`@IBAction func buttonTapped(_ sender: UIButton) {`**
> 
**`}`**

现在打开 `Main.storyboard` 并 右击从按钮拖动到 `View Controller` 并将其连接到 `buttonTapped：` 选项。

返回到 `ViewController.swift` 中，将一个 **print()** 语句添加到 **buttonTapped(_ :)** 方法中，以确认该方法是否响应于按钮点击而被调用。

> `@IBAction func buttonTapped(_ sender: UIButton) {`
  **`print("Called buttonTapped(_:)")`**
`}`

构建并运行应用程序。 确保按钮在屏幕上正确显示，您可以点击它。 同时确认当您点击按钮时，`buttonTapped(_ :)` 被调用并且有消息打印到控制台中。

## 调试基础 ##

最简单的调试是使用控制台。 当应用程序崩溃或需要将信息记录到控制台时，解释控制台中提供的信息可以让您观察并了解代码的问题。 我们用一些例子来说明控制台是如何支持你的无 bug 代码追求的。

### 解释控制台消息 ###

是时候给 Buggy 项目添加一些错误。 假设在考虑了一段时间之后，你觉得使用一个开关将比一个按钮更好。 打开 `ViewController.swift` 并对 **buttonTapped（_ :)** 方法进行以下更改。

> ~~**`@IBAction func buttonTapped(_ sender: UIButton) {`**~~
**`@IBAction func switchToggled(_ sender: UISwitch) {`**
  `print("Called buttonTapped(_:)")`
`}`

随着控件的更改，您重命名了 action 的名字，并将 `sender` 的类型更改为 **UISwitch**。

不幸的是，你忘了更新 `Main.storyboard` 中的界面。 构建并运行应用程序，然后点击按钮。 应用程序将崩溃，您将看到一个消息记录到控制台，类似于下面的内容。 （我们已经截断了一些信息以适应页面。）

> `2016-08-24 12:52:38.463 Buggy[1961:47078] -[Buggy.ViewController buttonTapped:]: `
`unrecognized selector sent to instance 0x7ff6db708870`
`2016-08-24 12:52:38.470 Buggy[1961:47078] *** Terminating app due to uncaught`
`exception 'NSInvalidArgumentException',`
`reason: '-[Buggy.ViewController buttonTapped:]: unrecognized selector sent to`
`instance 0x7ff6db708870'`
` *** First throw call stack: `
` ( `
`0 CoreFoundation [...] __exceptionPreprocess + 171`
`1 libobjc.A.dylib [...] objc_exception_throw + 48`
`2 CoreFoundation [...] -[NSObject(NSObject) doesNotRecognizeSelector:] + 132`
`3 CoreFoundation [...] ___forwarding___ + 1013`
`4 CoreFoundation [...] _CF_forwarding_prep_0 + 120`
`5 UIKit [...] -[UIApplication sendAction:to:from:forEvent:] + 83`
`6 UIKit [...] -[UIControl sendAction:to:forEvent:] + 67`
`7 UIKit [...] -[UIControl _sendActionsForEvents:withEvent:] + 444`
`8 UIKit [...] -[UIControl touchesEnded:withEvent:] + 668`
`9 UIKit [...] -[UIWindow _sendTouchesForEvent:] + 2747`
`10 UIKit [...] -[UIWindow sendEvent:] + 4011`
`11 UIKit [...] -[UIApplication sendEvent:] + 371`
`12 UIKit [...] __dispatchPreprocessedEventFromEventQueue + 3248`
`13 UIKit [...] __handleEventQueue + 4879`
`14 CoreFoundation [...] __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION`
`15 CoreFoundation [...] __CFRunLoopDoSources0 + 556`
`16 CoreFoundation [...] __CFRunLoopRun + 918`
`17 CoreFoundation [...] CFRunLoopRunSpecific + 420`
`18 GraphicsServices [...] GSEventRunModal + 161`
`19 UIKit [...] UIApplicationMain + 159`
`20 Buggy [...] main + 111`
`21 libdyld.dylib [...] start + 1`
`) `
`libc++abi.dylib: terminating with uncaught exception of type NSException`

控制台中的信息看起来很可怕，难以理解，但它并不像第一次看起来那么槽糕。 真正有用的信息在最顶端。 从第一行开始吧。

> `2016-08-24 12:52:38.463 Buggy[1961:47078] -[Buggy.ViewController buttonTapped:]:` 
`unrecognized selector sent to instance 0x7ff6db708870`

这句话表明了一个时间戳，应用程序的名称，以及 `unrecognized selector sent to instance 0x7ff6db708870`
 (发送到实例 0x7ff6db708870 的无法识别的选择器) 语句。 要了解这些信息，请记住，一个 iOS 应用程序可能是用 Swift 编写的，但它也有可能是构建在 Cocoa Touch 之上，Cocoa Touch 是用 Objective-C 编写的一系列框架。 Objective-C 是一种动态语言，当一个消息发送到一个实例时，Objective-C 运行时会根据它的选择器（一种ID）在那个精确的时间点找到要调用的实际方法。

因此，`unrecognized selector [was] sent to instance 0x7ff6db708870` 的语句意味着应用程序尝试调用在实例上不存在的方法。

哪一个实例呢？ 你有两条信息。 首先，它是一个 **Buggy.ViewController**。 （为什么不只是 **ViewController**？ Swift 命名空间包括模块的名称，在这种情况下是应用程序的名称）。其次，它位于内存地址 `0x7ff6db708870`（您的实际地址可能会不同）。

表达式 - `[Buggy.ViewController buttonTapped：]` 是 Objective-C 代码。 Objective-C 中的消息始终以[recipientselector] 的形式括在方括号中。 接收者(`receiver`) 是发送消息的类或实例。 开始方括号前的破折号（ - ）表示接收器是 **ViewController** 的一个实例。 （加号（+）表示接收者是本身。）

简而言之，控制台的这一行告诉你，选择器 `buttonTapped：` 被发送到 `Buggy.ViewController` 的一个实例，但是选择器没有被识别。

消息的下一行由于 “非捕获的异常” 而添加应用程序终止的信息，并将异常的类型指定为 **NSInvalidArgumentException**。

控制台消息的大部分是 堆栈跟踪(`stack trace`)，这是调用到应用程序崩溃点的所有函数或方法的列表。 了解应用程序在崩溃之前采用的逻辑路径可以帮助您解决和修复错误。 堆栈跟踪中的任何一个调用都没有机会返回，并且它们列在最上面的最近一次调用中。 这就是堆栈跟踪：

> `*** First throw call stack: `
`(`
 `0 CoreFoundation [...] __exceptionPreprocess + 171`
 `1 libobjc.A.dylib [...] objc_exception_throw + 48`
 `2 CoreFoundation [...] -[NSObject(NSObject) doesNotRecognizeSelector:] + 132`
 `3 CoreFoundation [...] ___forwarding___ + 1013`
 `4 CoreFoundation [...] _CF_forwarding_prep_0 + 120`
 `5 UIKit [...] -[UIApplication sendAction:to:from:forEvent:] + 83`
 `6 UIKit [...] -[UIControl sendAction:to:forEvent:] + 67`
 `7 UIKit [...] -[UIControl _sendActionsForEvents:withEvent:] + 444`
 `8 UIKit [...] -[UIControl touchesEnded:withEvent:] + 668`
 `9 UIKit [...] -[UIWindow _sendTouchesForEvent:] + 2747`
 `10 UIKit [...] -[UIWindow sendEvent:] + 4011`
 `11 UIKit [...] -[UIApplication sendEvent:] + 371`
 `12 UIKit [...] __dispatchPreprocessedEventFromEventQueue + 3248`
 `13 UIKit [...] __handleEventQueue + 4879`
 `14 CoreFoundation [...] __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION`
 `15 CoreFoundation [...] __CFRunLoopDoSources0 + 556`
 `16 CoreFoundation [...] __CFRunLoopRun + 918`
 `17 CoreFoundation [...] CFRunLoopRunSpecific + 420`
 `18 GraphicsServices [...] GSEventRunModal + 161`
 `19 UIKit [...] UIApplicationMain + 159`
 `20 Buggy [...] main + 111`
 `21 libdyld.dylib [...] start + 1`
`) `

列表中的第一行包括一个调用的数字，模块名称，一个内存地址（我们已经删除其余部分以适应页面），以及表示该函数或方法的符号。 如果从底部向上扫描堆栈跟踪，您可以感觉到应用程序从 Buggy 的 **main** 方法开始，在标有 20 号调用的线路上，接收到一个在调用 9 识别为触摸的事件，然后尝试 在调用 7 将相应的动作发送到按钮的目标。没有找到动作的选择器（调用2： - `[NSObject（NSObject）doesNotRecognizeSelector：]`），导致引发异常（调用1：`objc_exception_throw`）。

尽管控制台消息的这种缺点是只特定于一种错误类型，但是从理解这些消息的基本结构可以帮助您了解将来遇到的错误消息。 随着您开发经验的积累，您会开始将错误消息与问题类型相关联，并且您将更熟练调试代码。

### 修复第一个bug ###

查看 `ViewController.swift`，您发现您将您的动作方法从 **buttonTapped(_ :)** 更改为 **switchToggled(_ :)**，这就是选择器 `buttonTapped：` 未被识别的原因。

要修复错误，您有两个选择。 您可以更新连接到 `Main.storyboard`上按钮的 动作，以匹配您的新动作方法。 或者您可以在 **switchToggled（_ :)** 方法上还原名称更改。 你决定反正不一定要用 switch，所以打开 `ViewController.swift` 并将你的方法改回它的早期实现。 （记住我们告诉你的是：尽可能完整地进行更改，即使您看到问题。）

> ~~`@IBAction func switchToggled(_ sender: UISwitch) {`~~
**`@IBAction func buttonTapped(_ sender: UISwitch) {`**
  `print("Called buttonTapped(_:)")`
`}`

构建并运行应用程序。 它运行正常吗？还是有问题？ 其实这里有一个问题，你将在下一节中解决。

## Caveman 调试 ##

**ViewController** 的 **buttonTapped(_ :)** 方法的当前实现只是将一个语句记录到控制台。 这是一个被称为 caveman调试(`caveman debugging`) 的技术的例子：在代码中策略性地放置 `print()` 调用，以验证函数和方法是否被调用（并以正确的顺序调用），并将变量值记录到控制台以关注重要数据。

Caveman 调试 并不像名字(译：穴居人)所暗示的那样过时，现代开发人员仍然依赖于记录到控制台的消息。

为了探索 caveman调试 可以为您做什么，请在 `ViewController.swift` 中调用 **buttonTapped(_ :)** 时记录 sender 控件的状态。

> `@IBAction func buttonTapped(_ sender: UISwitch) {`
  `print("Called buttonTapped(_:)")`
  **`// Log the control state:`**
  **`print("Is control on? \(sender.isOn)")`**
`}`

在本书中所写的 @IBAction 方法中，您已经传递了一个称为 *sender* 的参数。 此参数是发送消息的
 控件(`control`) 的引用。控件是 **UIControl** 的一个子类; 您使用过几个 **UIControl** 子类了，包括 **UIButton**，**UITextField** 和 **UISegmentedControl**。 在 **buttonTapped(_ :)** 的用法中可以看到，在这种情况下，`sender` 是 **UISwitch** 的一个实例。 **isOn** 属性是一个布尔值，指示 switch 实例是否处于 on 状态。

构建并运行应用程序。 尝试点击按钮。哎呀！ 您再次出现无法识别的选择器错误。

> `Called buttonTapped(_:)`
`2016-08-30 09:30:57.730 Buggy[9738:1177400] -[UIButton isOn]: `
`unrecognized selector sent to instance 0x7fcc5d104cd0 `
`2016-08-30 09:30:57.734 Buggy[9738:1177400] *** Terminating app due to uncaught ` 
`exception 'NSInvalidArgumentException', reason: '-[UIButton isOn]: unrecognized `
`selector sent to instance 0x7fcc5d104cd0' `

控制台消息以被调用的 **buttonTapped（_ :)** 行开头，表示该操作确实被调用。 但是应用程序崩溃，因为 **isOn** 选择器被发送到 **UIButton** 的实例。

你可能会看到这个问题：`sender` 在 **buttonTapped(_ :)** 中键入一个 **UISwitch**，但该操作实际上是附加到 `Main.storyboard` 中的 **UIButton** 实例。

要确认此假设，请在调用 **isOn** 属性之前记录 `sender` 在 `ViewController.swift` 中的地址。

> `@IBAction func buttonTapped(_ sender: UISwitch) {`
  `print("Called buttonTapped(_:)")`
  **`// Log sender:`**
  **`print("sender: \(sender)")`**
  `// Log the control state:`
  `print("Is control on? \(sender.isOn)")`
`}`

构建并运行应用程序一次。 点击按钮并崩溃应用程序后，请检查控制台日志的前几行，其内容如下所示：

> `Called buttonTapped(_:) `
`sender: <UIButton: 0x7fcf8c508bb0; frame = (160 84; 55 30); opaque = NO;`
`autoresize = RM+BM; layer = <CALayer: 0x618000220ea0>> `
`2016-08-30 09:45:00.562 Buggy[9946:1187061] -[UIButton isOn]: unrecognized selector `
`sent to instance 0x7fcf8c508bb0`
`2016-08-30 09:45:00.567 Buggy[9946:1187061] *** Terminating app due to uncaught `
`exception 'NSInvalidArgumentException', reason: '-[UIButton isOn]: unrecognized`
`selector sent to instance 0x7fcf8c508bb0' `

在调用 **buttonTapped(_ :)** 后的行中，您将获得有关 `sender` 的信息。 如预期的那样，它是 **UIButton** 的一个实例，它存在于地址为 `0x7fcf8c508bb0` 的内存中。 进一步下来，您可以确认这是您发送 **isOn** 消息的相同实例。 按钮无法响应 **UISwitch** 属性，因此应用程序崩溃。

要解决此问题，请更正 `ViewController.swift` 中的 **buttonTapped(_ :)** 定义。 在那个方法中，删除额外的你不需要再次调用的 **print()** 方法。

> ~~`@IBAction func buttonTaped(_ sender: UISwitch) {`~~
**`@IBAction func buttonTaped(_ sender: UIButton) {`**
  `print("Called buttonTapped(_:)")`
  ~~`// Log sender:`~~
  ~~`//print("sender: \(sender)")`~~
  ~~`// Log the control state:`~~
  ~~`//print("Is control on? \(sender.isOn)")`~~
	}

Swift 有四个文字表达式，可以帮助您将信息记录到控制台（表9.1）：

**表9.1用于调试的文字表达式**

值|类型|说明
-|-|-|
|#file|String|表达式所在的文件名称。
|#line|Int|表达式所在的行号。
|#column|Int|表达式开始的列号。
|#function|String|表达式所在的方法名称。

为了说明这些文字表达式的使用，请在 `ViewController.swift` 中的 **buttonTapped(_ :)** 方法中更新对 **print()** 的调用。

> `@IBAction func buttonTapped(_ sender: UIButton) {`
  ~~`print("Called buttonTapped(_:)")`~~
  **`print("Method: \(#function) in file: \(#file) line: \(#line) called.")`**
`}`

构建并运行应用程序。 当您点击按钮时，您将看到一条记录到控制台的消息，类似于下面。

> `Method: buttonTapped in file: /Users/juampa/Desktop/Buggy/Buggy/ViewController.swift at line: 13 was called.`

虽然 caveman 调试是有用的，但请注意，在构建您的项目以进行发布时，`print()` 语句依然在代码中。

## Xcode 调试器：LLDB ##

要继续进行调试实验，您将为应用程序添加另一个错误。 将下面的代码添加到 `ViewController.swift`。 请注意，您将使用一个 **NSMutableArray**，即 Objective-C 中类似于 Swift 的 **Array** 的对象，使错误更难找到。

> `@IBAction func buttonTapped(_ sender: UIButton) {`
  `print("Method: \(#function) in file: \(#file) line: \(#line) called.")`<br>
  **`badMethod()`**
`} `
>
**`func badMethod() {`**
  **`let array = NSMutableArray()`**
>
  **`for i in 0..<10 {`**
    **`array.insert(i, at: i)`**
  **`} `**
>
  **`// Go one step too far emptying the array (notice the range change):`**
  **`for _ in 0...10 {`**
    **`array.remove(at: 0)`**
  **`}`**
**`}`**

构建并运行应用程序以确认点击按钮会导致应用程序崩溃，并导致未知的 **NSRangeException** 异常。 使用您新获得的知识尽可能多地学习和解释错误信息。

如果您使用 Swift **Array** 类型来创建此 bug，Xcode将能够突出显示导致异常的代码行。 因为你使用了一个 **NSMutableArray**，引发异常的代码在 `Cocoa Touch` 框架之内。 调试时通常是这种情况; 问题不是很明显，你需要做一些调查工作。

## 设置断点 ##

假设你不知道崩溃的直接原因。 您只需知道在点击应用程序的按钮后才会发生。 一个合理的方法是在点击按钮并逐步执行代码直到应用程序终止，直到找到问题的线索。

打开 `ViewController.swift`。 要停止代码中指定位置的应用程序，您可以设置 `断点(breakpoint)`。 设置断点的最简单的方法是单击编辑器窗格左边的要在执行停止的行旁边的沟槽。 尝试一下：点击 `@IBAction func buttonTapped（_ sender：UIButton）{ ` 行的左边 。 将出现一个指示新断点的蓝色标记（图9.3）。

**图9.3设置断点**

![](http://upload-images.jianshu.io/upload_images/1230738-ca2dbc329ef62ad0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

设置断点后，可以直接点击蓝色标记来切换。 如果您点击标记一次，它将被禁用，由较浅的蓝色指示（图9.4）。

**图9.4禁用断点**

![](http://upload-images.jianshu.io/upload_images/1230738-44a784725e3b234f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再一次点击将重新启用断点。 您还可以通过右击标记来启用，禁用，删除或编辑断点。 将出现一个上下文菜单，如图9.5所示。

**图9.5修改断点**

![](http://upload-images.jianshu.io/upload_images/1230738-1153ef4d995ad368.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在Xcode的左窗格中打开断点导航器选择 `Reveal in Breakpoint Navigator`，其中包含应用程序中所有断点的列表（图9.6）。 您还可以通过单击导航器选择器中的图标来打开断点导航器。

**图9.6断点导航器**

![](http://upload-images.jianshu.io/upload_images/1230738-77d3a41d6fef75b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 单步调试代码  ###

确保在 **buttonTapped(_ :)** 方法中设置了断点并且在所有在上一节修改后所有断点都处于启用状态。 运行应用程序，然后点击按钮。

您的应用程序命中断点并停止执行，`Xcode` 会将您转到接下来要执行的代码行，以绿色突出显示。 它还会打开一些新的信息区域（图9.7）。

**图9.7 Xcode 在断点处停止**

![](http://upload-images.jianshu.io/upload_images/1230738-0966c107100bfc6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您熟悉控制台，并已看到调试导航器。 这里的新区域是变量视图和调试栏，它们与控制台一起组成调试区域。 （如果看不到变量视图，请单击调试区域右下角的 
![](http://upload-images.jianshu.io/upload_images/1230738-4e4b499cdc9cb29e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 图标。）

变量视图可以帮助您发现断点范围内的变量和常量的值。 然而，试图找到一个特定的值可能需要大量的挖掘。

首先，您将在变量视图中列出的所有信息中看到传递给 **buttonTapped(_ :)** 方法的 `sender` 和 `self` 参数。 单击 `sender` 的闭合三角形，您将看到它包含一个 `UIKit.UIControl` 属性。 其中有一个 `_targetActions` 数组，其中包含按钮的附加目标动作对。

打开 `_targetActions` 数组，打开第一项(`[0]`)，然后选择 `_target` 属性。 在选择 `_target` 的同时敲击空格键，然后会打开快速查看窗口，显示变量的预览（这是 **ViewController** 的一个实例）。 快速查看如图9.8所示。

**图9.8 检查变量视图中的变量**

![](http://upload-images.jianshu.io/upload_images/1230738-163d57e2ea57253c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在与  `_target` 相同的部分中，您将看到 `_selector`。 旁边，你会看到 `(SEL)“buttonTapped：”`。 `(SEL)` 表示这是一个选择器， `“buttonTapped：”` 是选择器的名称。

在这个例子中，它并没有帮助你挖掘找到 `_target` 和 `_action`; 然而，一旦开始使用更大，更复杂的应用程序，使用变量视图就可能特别有用。 您需要知道您正在寻找的内容，例如 `_target` 和 `_action`，找到您感兴趣的值可以帮助您跟踪错误。

现在是开始调试代码的时候了。 您可以使用调试栏上的按钮进行此操作，如图9.9所示。

**图9.9 调试栏**

![](http://upload-images.jianshu.io/upload_images/1230738-1da89036745d53d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

调试栏中的重要按钮有：

- `Continue program execution` 
![](http://upload-images.jianshu.io/upload_images/1230738-553179c020634ae3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
——恢复程序的正常执行
- `Step over` 
![](http://upload-images.jianshu.io/upload_images/1230738-81faffec6b697b44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
——执行一行代码，而不需要输入任何函数或方法调用
- `Step into` 
![](http://upload-images.jianshu.io/upload_images/1230738-cba75103052fafc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
——执行下一行代码，包括输入函数或方法调用
- `Step out` 
![](http://upload-images.jianshu.io/upload_images/1230738-c2836ec719d18f77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
——继续执行，直到当前的函数或方法退出

单击按钮![](http://upload-images.jianshu.io/upload_images/1230738-81faffec6b697b44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)，直到突出显示 `badMethod()` 行（不执行此行）。 请注意，您不要进入 **print()** 方法——因为它是一个 Apple 编写的方法，那里不会出现问题。

当 **badMethod()** 突出显示时，单击按钮 ![](http://upload-images.jianshu.io/upload_images/1230738-cba75103052fafc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 进入 **badMethod()** 方法，并 ![](http://upload-images.jianshu.io/upload_images/1230738-cba75103052fafc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 继续逐步执行代码直到应用程序崩溃。 这将需要您几次点击，它将看起来像是通过相同的代码行一遍又一遍——实际上，确实如此，因为代码循环遍历。

当您逐步执行代码时，您可以暂停鼠标悬停在 `i` 和 `array.remove` 以查看其值更新（图9.10）。

**图9.10 检查变量的值**

![](http://upload-images.jianshu.io/upload_images/1230738-02451906639ee1ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一旦应用程序崩溃，您将确认是在 **badMethod()** 方法中发生崩溃。 有了这个，您现在可以在 `func buttonTapped(_ sender: UIButton)` 行上删除或禁用断点。

要删除断点，右击它并选择 `Delete Breakpoint`。 您还可以通过将蓝色标记拖出沟槽来删除断点，如图9.11所示。

**图9.11 拖动标记以删除断点**

![](http://upload-images.jianshu.io/upload_images/1230738-15c4212925a5c158.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有时候，您希望在触发代码行时收到通知，但您不需要任何其他信息或应用程序在遇到该行时暂停。 要完成此操作，您可以向断点添加声音，并在触发后自动继续执行。

在 **badMethod()** 方法的 `array.insert(i，at：i)` 行中添加一个新的断点。 然后右击，然后选择 `Edit Breakpoint...`。 单击 `Add Action` 按钮，然后从弹出菜单中选择 `Sound`。 最后，勾选 `Automatically continue after evaluating actions`（图9.12）。

**图9.12 启用特殊操作**

![](http://upload-images.jianshu.io/upload_images/1230738-f4dc1d789c156e10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您已配置断点来发出警报声音，而不是每次遇到断点就停止执行。 再次运行应用程序，然后点击按钮。 你应该听到一连串的声音，然后应用程序将崩溃。

似乎应用程序正在安全地完成 for 循环，但是您需要确定。 找到并右击断点标记，选择 `Edit Breakpoint...`。 在编辑器弹出窗口中，单击声音操作右侧的 `+` 添加新动作。

从弹出窗口中选择 `Log Message`。 在 `Text` 字段中， 输入 `Pass number ％H` （％H是 断点命中计数(`breakpoint hit count`)，对遇到断点次数的引用）。 最后，确保选中 `Log message to console`（图9.13）。

**图9.13 将多个动作分配给断点**

![](http://upload-images.jianshu.io/upload_images/1230738-aab950ef865118fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再次运行应用程序，然后点击按钮。 您将再次听到一 连串的声音，并且应用程序将像以前一样崩溃。 但是这一次，如果您观看控制台（或者在应用程序崩溃后向上滚动），您将看到遇到断点 10 次。 这证实您的代码正确地完成循环。

删除当前的断点，并在 `array.remove(at：0)` 行上添加一个新的断点。 编辑断点以记录 pass number，并像以前一样自动继续（图9.14）。

**图9.14 添加日志断点**

![](http://upload-images.jianshu.io/upload_images/1230738-145c7a0fdb5a7bf2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行应用程序并点击按钮。 当它崩溃时，在控制台中向上滚动，您将看到第二个断点执行了 11 次。 它还解释了在应用程序崩溃时在控制台上记录的 **NSRangeException**。 仔细阅读控制台上的崩溃日志，尽可能多地了解它。

在解决问题之前，花点时间探索几个更多的调试策略。 首先，禁用或删除应用程序中的任何剩余断点。

在这些简单的例子中，您已经知道在哪里找到代码中的错误，但是在现实世界的开发中，您通常不会知道应用程序中错误隐藏在哪里。 如果您可以知道哪一行代码导致崩溃的未捕获异常，那就很 牛叉 了。

异常断点(`exception breakpoint`) 是很有用的，你可以做到这一点。 打开断点导航器，然后单击窗口左下角的 `+`。 从上下文菜单中 `Exception Breakpoint...`。 创建一个新的异常断点，并显示一个弹出窗口。 确保它捕获抛出的所有异常，如图9.15所示。

**图9.15 添加异常断点**

![](http://upload-images.jianshu.io/upload_images/1230738-b1f94c5218e23962.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行应用程序，再次点击按钮。 应用程序会自动停止，Xcode 会将您引导到直接导致引发异常的行。 但是请注意，没有控制台日志。 那是因为应用程序还没有崩溃。 要查看崩溃并阅读原因，请单击调试栏上的![](http://upload-images.jianshu.io/upload_images/1230738-553179c020634ae3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)按钮，直到崩溃。

这个策略是开始处理一个新错误的策略。 事实上，许多程序员在开发过程中始终保持异常断点激活。 为什么我们让你等待这么久才能使用？ 因为如果您已经开始使用异常断点，那么您就不需要了解其他调试策略，也可以使用它们。 现在你可以把之前的断点都删了，你已经不再需要它们了。

你将尝试一个最终的技术：`symbolic breakpoint`(标志断点?)。 这不是由行号指定的断点，而是由函数或方法的名称——(标志)`symbol` 所指定。 当标志被调用时，标志断点被触发——无论标志在代码中还是在没有代码的框架中。

在断点导航器中添加一个新的标志断点，方法是单击左下角的 `+` 按钮，从上下文菜单中选择 `Symbolic Breakpoint...`。 在弹出窗口中，指定 “badMethod” 作为符号，如图9.16所示。 这意味着每次调用 **badMethod()** 时，应用程序都将停止。

**图9.16 添加标志断点**

![](http://upload-images.jianshu.io/upload_images/1230738-b0c628da25491797.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行应用程序来测试断点。 应用程序应该在你点击 Tap Tap！按钮后在 **badMethod()** 中停止。

在现实世界的应用程序中，您很少会在方法中使用标志断点; 你可能会添加一个正常的断点，就像你在本章前面看到的一样。 标志断点对于中止非你编写的方法最为有用，例如 Apple 的框架之一。 例如，当您想知道应用程序中的任何视图控制器是否都能触发方法 **loadView（）**。

最后修复bug。

> `func badMethod() {`
  `let array = NSMutableArray()`
>
  `for i in 0..<10 {`
    `array.insert(i, at: i)`
  `} `
>
  ~~`Go one step too far emptying the array (notice the range change):`~~
  ~~`for _ in 0...10 {`~~
  **`for _ in 0..<10 {`**
    `array.remove(at: 0)`
  `}`
`}`

### LLDB 控制台 ###

Xcode 的 LLDB调试器 的一个很大的特点是它有一个命令行界面。 控制台区域不仅用于读取消息，还可用于键入 LLDB 命令。 只要您在控制台上看到蓝色 `(lldb)` 提示，调试器命令行界面就会处于活动状态。

确保 **badMethod()** 上的标志断点仍然处于活动状态，运行该应用程序，然后点击该按钮即可在该点断开。 看看控制台，你会看到 `(lldb)` 提示符（图9.17）。 单击提示旁边，然后键入命令。

**图9.17 控制台上的（lldb）提示符**

![](http://upload-images.jianshu.io/upload_images/1230738-17825fc4c74f05ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最有用的 LLDB 命令之一是 `print-object`，缩写为 `po`。 此命令用于打印实例。 尝试通过在控制台上输入。

> `(lldb)` **`po self`**
`<Buggy.ViewController: 0x7fae9852bf20>`

对命令响应的 `self` 是 **ViewController** 的一个实例。 现在用命令 `step;` 执行一行代码; 这将初始化数组常量引用。 用 `po` 打印引用的值。

> `(lldb)` **`step`**
`(lldb)` **`po array`**
`0 elements`

`0 elements` 响应不是很有用，因为它不给你详细信息。 `print` 命令缩写为 `p`，可以更详细。 尝试一下。

> `(lldb)` **`p array`**
`(NSMutableArray) $R3 = 0x00007fae98517c00 "0 values" {}`

通常，使用带有 `print` 或 `print-object` 的控制台来检查变量比 Xcode 的变量视图窗格更加方便。

另一个有用的LLDB命令是 `expression`，缩写为 `expr`。 此命令允许您输入 Swift 代码来修改变量。 例如，将一些数据添加到数组，查看内容，然后继续执行。

> `(lldb)` **`expr array.insert(1, at: 0)`**
`(lldb)` **`p array`**
`(NSMutableArray) $R5 = 0x00007fae98517c00 "1 value" { `
 `[0] = 0xb000000000000013 Int64(1)`
`} `
`(lldb)` **`po array`**
`▿ 1 element`
 `- [0] : 1 `
 `(lldb)` **`continue`**

也许更令人惊讶的是，您还可以使用 LLDB 表达式更改 UI。 尝试将按钮的 `tintColor` 更改为红色。

> `(lldb)` **`expr self.view.tintColor = UIColor.red`**
`(lldb)` **`continue`**

还有很多 LLDB 命令。 要了解更多信息，请在 `(lldb)` 提示符下输入 `help` 命令。