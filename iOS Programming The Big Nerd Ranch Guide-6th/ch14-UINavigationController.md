在第五章中，您了解了 **UITabBarController** 以及它如何允许用户访问不同的屏幕。 标签栏控制器非常适合彼此独立的屏幕，但如果您有多个提供相关信息的屏幕怎么办？

例如，`Settings` 应用程序具有多个相关信息屏幕：一个设置列表（如`Sounds`），每个设置的详细页面以及每个详细信息的选择页面（图14.1）。 这种类型的界面称为 向下钻取界面(`drill-down interface`)。

**图14.1“Settings” 中的向下钻取界面**

![](http://upload-images.jianshu.io/upload_images/1230738-312bee9d306d770d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在本章中，您将使用 **UINavigationController** 向 `Homepwner` 添加一个 向下钻取界面，该界面允许用户查看和编辑 **Item** 的详细信息。 这些详细信息将由您在第十三章（图14.2）中创建的 **DetailViewController** 显示。

**图14.2 使用 UINavigationController 的 Homepwner**

![](http://upload-images.jianshu.io/upload_images/1230738-9fd8307394c70a15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## UINavigationController ##

**UINavigationController** 包含一个视图控制器数组，用于在栈中呈现相关信息。 当 **UIViewController** 位于栈顶部时，其视图是可见的。

你将使用 **UIViewController** 初始化一个 **UINavigationController** 的实例。 该 **UIViewController** 被添加到导航控制器的 `viewControllers` 数组，并成为导航控制器的根视图控制器。 根视图控制器始终位于栈底。 （请注意，尽管该视图控制器被称为导航控制器的 “根视图控制器”，然而 **UINavigationController** 没有 `rootViewController` 这个属性。）

更多的视图控制器可以在应用程序运行时被  push  到 **UINavigationController** 的栈顶。 这些视图控制器被添加到对应于栈顶的 `viewControllers` 数组的末尾。

**UINavigationController** 的 `topViewController` 属性保留对堆栈顶部的视图控制器的引用。

当一个视图控制器 压栈(push) 时，它的视图就会从右边移到屏幕上。当 弹栈(pop) 时(例:最后一项被移除时)，顶部视图控制器从栈中移开，它的视图滑到右边，将下一个视图控制器的视图暴露在栈上并将成为顶部视图控制器。图14.3显示了一个带有两个视图控制器的导航控制器。`topViewController` 的视图是用户所看到的。

**图14.3 UINavigationController 的栈**

![](http://upload-images.jianshu.io/upload_images/1230738-cc6548476e416cd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**UINavigationController** 是 **UIViewController** 的子类，因此它具有自己的视图。它的视图总是有两个子视图：一个 **UINavigationBar** 和 `topViewController` 的视图（图14.4）。

**图14.4 UINavigationController 的视图**

![](http://upload-images.jianshu.io/upload_images/1230738-a10d7a7372eebed2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在本章中，您将向 `Homepwner` 应用程序添加一个 **UINavigationController**，并使 **ItemsViewController** 成为    **UINavigationController** 的根视图控制器。 当选择 **Item** 时， **DetailViewController** 将被 push 到 **UINavigationController** 的栈上。该视图控制器将允许用户查看和编辑在 **ItemsViewController** 的表视图中选中的 **Item** 的属性。 更新的 `Homepwner` 应用程序的对象图如图14.5所示。

**图14.5 Homepwner 对象图**

![](http://upload-images.jianshu.io/upload_images/1230738-d6a7f5fcb02669d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个应用程序变得相当大，你可以看到。 幸运的是，视图控制器和 **UINavigationController** 知道如何处理这种复杂的对象图。 在编写iOS应用程序时，将每个 **UIViewController** 视为自己的小世界很重要。 在 Cocoa Touch 中已经实施的内容将会起到举足轻重的作用。

首先重新打开 `Homepwner` 项目，给 `Homepwner` 添加一个导航控制器。 使用 **UINavigationController** 的唯一要求是您给它一个根视图控制器，并将其视图添加到窗口。

打开 `Main.storyboard` 并选择 `Items View Controller`。 然后，从 `Editor` 菜单中选择
 `Embed In` → `Navigation Controller`。 这将将 **ItemsViewController** 设置为 **UINavigationController** 的根视图控制器。 它还将更新故事板，将 `Navigation Controller` 设置为初始视图控制器。

您的 `Detail View Controller` 界面现在可能会放错位置，因为它被包含在导航控制器中。 如果是，请选择栈视图，然后单击自动布局约束菜单中的 `Update Frames` 按钮。

构建并运行应用程序，然后应用程序崩溃了。 发生了什么？ 您之前与 **AppDelegate**
 创建 关联( `contract` )的是 **ItemsViewController** 的实例，这将会是窗口的
 `rootViewController`:
	
`let itemsController = window!.rootViewController as! ItemsViewController`

您现在通过将 **ItemsViewController** 嵌入到 **UINavigationController** 中来破坏此关联。 您需要更新此关联。

打开 `AppDelegate.swift` 并更新 **application(_：didFinishLaunchingWithOptions :)** 以反映新的视图控制器层级。

> `func application(_ application: UIApplication, didFinishLaunchingWithOptions 
 launchOptions: [UIApplicationLaunchOptionsKey : Any]?) -> Bool {`
  `// Override point for customization after application launch.`
>
  `// Create an ItemStore`
  `let itemStore = ItemStore()`
>	
  `// Access the ItemsViewController and set its item store`
>
  ~~`let itemsController = window!.rootViewController as! ItemsViewController`~~
  **`let navController = window!.rootViewController as! UINavigationController`**
  **`let itemsController = navController.topViewController as! ItemsViewController`**
  `itemsController.itemStore = itemStore`<br>	
  `return true`
`}`

再次构建并运行应用程序。 `Homepwner` 再次正常运行，在屏幕顶部有一个非常好看的(虽然是空的) **UINavigationBar** （图14.6）。

**图14.6具有空导航栏的Homepwner**

![](http://upload-images.jianshu.io/upload_images/1230738-c4cd7ce86c9e467c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意屏幕是如何调整来适应 **ItemsViewController** 的视图以及新的导航栏的。**UINavigationController** 为您做了这样的事情:虽然 **ItemsViewController** 的视图实际上是在导航栏上进行的，但 **UINavigationController** 在顶部添加了 `padding`，使一切都很适合。这是因为视图控制器的顶部布局指南被调整了，以及所有视图都被约束在顶部的布局指南中——就像栈视图一样。

### UINavigationController 导航 ###

在应用程序仍在运行的情况下，创建一个新 item 并从 **UITableView** 中选择该行。你不仅被带到 **DetailViewController** 的视图中，而且你还可以在 **UINavigationBar** 中获得一个自带的动画和一个 `Back` 按钮。点击这个按钮，你将回到 **ItemsViewController**。

注意，你不需要改变你在第13章创建的 show segue 来得到这个行为。正如在那一章中所提到的，show segue 以一种有意义的方式呈现了目标视图控制器。当一个 show segue 从嵌入在导航控制器中的视图控制器触发时，目标视图控制器被 push 到导航控制器的视图控制器栈中。

因为 **UINavigationController** 的栈是一个数组，它将拥有对添加到它的任何视图控制器的所有权。因此，**DetailViewController** 只在 segue 完成之后由 **UINavigationController** 拥有。当栈被弹出时， **DetailViewController** 会被销毁。下一次当一行被点击时，会创建一个 **DetailViewController** 的新实例。

使用一个视图控制器来 `push` 下一个视图控制器是一个常见的模式。根视图控制器通常创建下一个视图控制器，下一个视图控制器创建后一个视图控制器，等等。有些应用程序可能可以根据用户输入来 push 不同的视图控制器。例如，根据选择的媒体类型，`Photos` 应用程序将视频视图控制器或图像视图控制器 push 到导航栈上。

请注意，item 的详细信息视图包含所选 **Item** 的信息。然而，当您可以编辑这些数据时， **UITableView** 在返回时不会反映这些更改。为了解决这个问题，您需要实现代码来更新正在编辑的 **Item** 的属性。在下一节中，您将看到何时执行此操作。

## 显示和消失的视图 ##

每当一个 **UINavigationController** 即将交换视图时，它会调用两个方法： **viewWillDisappear(_ :)** 和 **viewWillAppear(_ :)**。 当 **viewWillDisappear(_ :)** 被调用，**UIViewController** 将从栈弹出。 然后，当 **viewWillAppear(_ :)**  被调用，**UIViewController** 将处在该栈顶。

为了保持数据更改，当 **DetailViewController** 从栈中弹出时，您将将其  item 的属性设置为文本字段的内容。 在实现这些视图显示和消失的方法时，重要的是调用超类的实现——它可能要做一些事，或者需要机会去做一些事。 在 `DetailViewController.swift` 中，实现 **viewWillDisappear(_ :)** 。

> **`override func viewWillDisappear(_ animated: Bool) {`**
  **`super.viewWillDisappear(animated)`**
>
  **`// "Save" changes to item`**
  **`item.name = nameField.text ?? ""`**
  **`item.serialNumber = serialNumberField.text`**
>
  **`if let valueText = valueField.text, let value = numberFormatter.number(from: valueText) {`**
    **`item.valueInDollars = value.intValue`**
  **`} else {`**
    **`item.valueInDollars = 0`** 
  **`}`**
**`}`**

现在当用户点击 **UINavigationBar** 上的 `back` 按钮时，**Item** 的值将被更新。 当 **ItemsViewController** 出现在屏幕上时，方法 **viewWillAppear(_ :)** 被调用。 借此机会重新加载 **UITableView** ，以便用户立即看到更改。

在 `ItemsViewController.swift` 中，重写 **viewWillAppear(_ :)** 来重新加载表视图。

> **`override func viewWillAppear(_ animated: Bool) {`**
  **`super.viewWillAppear(animated)`**
>
  **`tableView.reloadData()`**
**`}`**

再次构建并运行应用程序。 现在，您可以在创建的视图控制器之间来回移动，并轻松更改数据。

## 取消键盘 ##

运行应用程序，添加并选择一个 item，然后触摸 item 的 Name 文本字段。 当您触摸文本字段时，屏幕上会显示一个键盘(图14.7），如您在第4章中的 `WorldTrotter` 应用程序中所看到的。(如果您使用的是模拟器，键盘没有出现，请记住可以按 `Command-K` 切换设备键盘。)

**图14.7 触摸文本字段时出现键盘**

![](http://upload-images.jianshu.io/upload_images/1230738-80b28f50505098b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**UITextField** 类以及 **UITextView** 内置了键盘响应触摸的外观，因此您无需为键盘出现做任何额外的操作。但是，有时您会希望确保键盘的行为符合您的要求。

举个例子，注意到键盘覆盖了屏幕的三分之一以上。 现在，它并没有遮住任何东西，但是很快你会添加更多的详细信息，扩展到屏幕的底部，当用户不需要键盘时需要一种方法来隐藏它。 在本节中，您将给用户两种方法来关闭键盘：按下键盘的 Return 键，或者点击详情视图控制器视图上的其他任何位置。 但首先，我们来看看使文本可编辑的事件组合。

### 事件处理基础知识 ###

触击视图时，会创建一个事件。 此事件(称为“触摸事件”)与视图控制器视图中的特定位置相关联。 该位置确定触摸事件将传递到层级中的哪个视图。

例如，当您在其边界内点击一个 **UIButton** 时，它会收到触摸事件并以按钮的形式进行响应——通过在其目标上调用动作方法。 当您的应用程序中的视图被触摸时，该视图会接收到触摸事件，并且可以选择对该事件做出反应或忽略它。 但是，您的应用程序中的视图也可以响应非触摸事件。 一个很好的例子是摇一摇。 如果您在运行应用程序时晃动设备，您的其中一个视图就可以响应。 但是是哪一个呢？ 另一个有趣的情况是响应键盘。 **DetailViewController** 的视图包含三个 **UITextFields**。 用户输入时哪个会接收到文本？

对于震动和键盘事件，在视图层级中没有事件位置来确定哪个视图将接收该事件，因此必须使用另一个机制。这个机制是 第一响应者(`first responder`) 状态。许多视图和控件可以是视图层级中的第一响应者，但一次只能有一个响应者。可以把它看作可以在视图中传递的标志。无论哪个视图持有该标志，都将接收震动或键盘事件。

**UITextField** 和 **UITextView** 的实例对触摸事件有一个不寻常的响应。 触摸时，文本字段或文本视图将成为第一响应者，反而会触发系统将键盘显示在屏幕上，并将键盘事件发送到文本字段或视图。 键盘和文本字段或视图没有直接的连接，但它们通过第一响应者状态协同工作。

这是确保将键盘输入传递到正确文本字段的整洁方法。 第一响应者的概念只是 Cocoa Touch 编程中包含 **UIResponder** 类和 响应者链(`responder chain`) 的更广泛的事件处理主题的一部分。 当您处理第18章中的触摸事件时，您将了解更多信息，您还可以访问Apple的 `Event Handling Guide for iOS* 了解更多信息。

### 按Return键退出 ###

现在让我们回到允许用户关闭键盘。 如果您触摸应用程序中的另一个文本字段，则该文本字段将成为第一个响应者，键盘将保留在屏幕上。 当没有文本字段（或文本视图）是第一个响应者时，键盘将被放弃并离开。 要关闭键盘，那么您需要在第一个响应者的文本字段上调用 **resignFirstResponder()**。

要使文本字段响应于按下Return键，您将要实现 **UITextFieldDelegate** 方法 **textFieldShouldReturn(_ :)**。 只要按下Return键，就会调用此方法。

首先，在 `DetailViewController.swift` 中，使 **DetailViewController** 符合 **UITextFieldDelegate** 协议。

`class DetailViewController: UIViewController, ` **`UITextFieldDelegate {`**

接下来，在传入的文本字段上实现 **textFieldShouldReturn(_ :)** 来调用 **resignFirstResponder()**。

> **`func textFieldShouldReturn(_ textField: UITextField) -> Bool {`**
  **`textField.resignFirstResponder()`**
  **`return true`**
**`}`**

最后，打开 `Main.storyboard` 并将每个文本字段的 `delegate` 属性连接到 `Detail View Controller` (图14.8)。(右键从每个 **UITextField** 拖动到 `Detail View Controller` 并选择 `delegate`。）

**图14.8 连接文本字段的委托属性**

![](http://upload-images.jianshu.io/upload_images/1230738-d5ef5f74095426e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 点击文本字段，然后按键盘上的 Return 键。 键盘将消失。 要使键盘返回，请触摸任何文本字段。

### 点击其他地方返回 ###

如果用户在 **DetailViewController** 的视图上轻触其他任何地方，也应该会关闭键盘。 为了做到这一点，当视图被触摸时，您将使用手势识别器，就像在 `WorldTrotter` 应用程序中一样。 在动作方法中，您将在文本字段上调用 **resignFirstResponder()** 。

打开 `Main.storyboard` 并在对象库中找到 `Tap Gesture Recognizer`。 将此对象拖动到 `Detail View Controller` 的背景视图上。 您将在 scee 底部中看到此手势识别器的引用。

在项目导航器中，右击 *DetailViewController.swift* 在 `assistant editor` 中打开它。 右键从故事板中的 tap gesture recognizer 拖动到 **DetailViewController** 的实现类。

在出现的弹出窗口中，从 `Connection` 菜单中选择 `Action`。 命名动作 **backgroundTapped** 。 对于 `Type`，选择 `UITapGestureRecognizer`（图14.9）。

**图14.9 配置UITapGestureRecognizer操作**

![](http://upload-images.jianshu.io/upload_images/1230738-065975696223fcb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单击 `Connect`，动作方法的存根将显示在 `DetailViewController.swift` 中。 在 **DetailViewController** 的视图上更新调用 **endEditing(_ :)** 的方法。

> `@IBAction func backgroundTapped(_ sender: UITapGestureRecognizer) {`
  **`view.endEditing(true)`**
`}`

调用 **endEditing(_ :)** 是一种方便的方式来解除键盘，你不必知道(或关心)哪个文本字段是第一个响应者。 当视图获得此调用时，它将检查其层级中的任何文本字段是否是第一个响应者。 然后会在该特定的视图上调用 **resignFirstResponder()**。

构建并运行您的应用程序。 点击文本字段以显示键盘。 点击文本字段外的视图，键盘也将消失。

最后一个需要关掉键盘的情况。 当用户点击后退按钮时，在将其从弹出的堆栈之前的 **DetailViewController** 上调用 **viewWillDisappear（_ :)**，并且键盘立即消失，没有动画。 要更顺利地关闭键盘，请在 `DetailViewController.swift` 中更新 **viewWillDisappear(_ :)** 的实现，以调用 **endEditing（_ :)** 。

> `override func viewWillDisappear(_ animated: Bool) {`
  `super.viewWillDisappear(animated)`
>
  **`// Clear first responder`**
  **`view.endEditing(true)`**
>
  `// "Save" changes to item`
  `item.name = nameField.text ?? ""`
  `item.serialNumber = serialNumberField.text`
>
  `if let valueText = valueField.text, let value = numberFormatter.number(from: valueText) {`
    `item.valueInDollars = value.integerValue` 
  `} else {`
    `item.valueInDollars = 0`
  `}`
`}`

## UINavigationBar ##

在本节中，您将声明 **UINavigationBar**， 一个 **UIViewController** 的描述性标题，并正好位于 **UINavigationController** 的栈顶。 

每个 **UIViewController** 都有一个类型为 **UINavigationItem** 的 `navigationItem` 属性。 但是，与 **UINavigationBar** 不同的是，**UINavigationItem** 不是 **UIView** 的子类，所以它不能出现在屏幕上。 相反，导航项目为导航栏提供了需要绘制的内容。 当一个 **UIViewController** 到达 **UINavigationController** 的栈顶时，**UINavigationBar** 使用 **UIViewController** 的 `navigationItem` 进行配置，如图14.10所示。

**图14.10 UINavigationItem**

![](http://upload-images.jianshu.io/upload_images/1230738-167e38b75e4b44ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

默认情况下，**UINavigationItem** 为空。 在最基本的层次上， **UINavigationItem** 有一个简单的 `title` 字符串。 当 **UIViewController** 移动到导航栈顶，并且其 `navigationItem` 具有有效字符串的 `title` 属性时，导航栏将显示该字符串（图14.11）。

**图14.11 带有标题的UINavigationItem**

![](http://upload-images.jianshu.io/upload_images/1230738-162d29766ae5cd6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**ItemsViewController** 的标题将始终保持不变，因此您可以在故事板本身中设置其导航项的标题。

打开 `Main.storyboard`。 双击 `Items View Controller` 上方导航栏的中央以编辑其标题。 给它一个标题 “Homepwner”（图14.12）。

**图14.12 在故事板中设置标题**

![](http://upload-images.jianshu.io/upload_images/1230738-4bc27f7b7fe81827.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 注意导航栏上的字符串 `Homepwner`。 创建并点按一行，并注意导航栏不再具有标题。 将 **DetailViewController** 的导航项标题作为它正在显示的 **Item** 的名称是很好的。 因为标题将取决于正在显示的 **Item**，您需要在代码中动态设置 `navigationItem` 的标题。

在 `DetailViewController.swift` 中，将属性观察器添加到更新 `navigationItem` 标题的 `item` 属性中。

> `var item: Item!` **`{`**
  **`didSet {`**
    **`navigationItem.title = item.name`**
  **`}`**
**`}`**
　　
构建并运行应用程序。 创建并点按一行，您将看到导航栏的标题是您选择的 **Item** 的名称。

导航项可以保存多个标题字符串，如图14.13所示。 每个 **UINavigationItem** 有三个可自定义的区域：一个 `leftBarButtonItem`，一个 `rightBarButtonItem` 和一个 `titleView`。 左和右栏的按钮项是对 **UIBarButtonItem** 的实例的引用，它包含仅可以在 **UINavigationBar** 或 **UIToolbar** 上显示的按钮的信息。

**图14.13 UINavigationItem**

![](http://upload-images.jianshu.io/upload_images/1230738-c657c283ff56834b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

回想一下，**UINavigationItem** 不是 **UIView** 的子类。 相反， **UINavigationItem** 封装了 **UINavigationBar** 用于配置自身的信息。 类似地，**UIBarButtonItem** 不是视图，而是保存有关如何显示 **UINavigationBar** 上单个按钮的信息。( **UIToolbar** 还使用 **UIBarButtonItem** 的实例配置自身。)

**UINavigationItem** 的第三个可定制区域是它的 `titleView`。 您可以使用基本字符串作为标题，也可以将 **UIView** 的子类置于导航项目的中心。 你不能同时拥有这两个。 如果它适合特定视图控制器的上下文以具有自定义视图（例如分段控件或文本字段），则可以将导航项的 `titleView` 设置为该自定义视图。 图14.13显示了具有自定义视图作为其 `titleView` 的 **UINavigationItem** 的内置 `Maps` 应用程序的示例。 然而，通常，标题字符串就足够了。

### 将按钮添加到导航栏 ###

在本节中，当 `ItemsViewController` 位于栈顶时，您将使用两个按钮项替换表头视图中的两个按钮，这些按钮项将出现在 **UINavigationBar** 中。 导航条按钮项(`Bar Button Item`)具有像 **UIControl** 的目标动作机制一样的目标动作对：当点击时，它将该动作消息发送到目标。

首先，我们来处理一个添加新 item 的按钮项。 当 **ItemsViewController** 位于栈顶时，此按钮将位于导航栏的右侧。 点击后，它将添加一个新的 **Item**。

在更新故事板之前，需要更改 **addNewItem(_ :)** 的方法声明。 目前这种方法是由 **UIButton** 触发的。 现在您正在将发件人更改为 **UIBarButtonItem**，您需要更新声明。

在 `ItemsViewController.swift` 中，更新 **addNewItem(_ :)** 的方法声明。

> ~~**`@IBAction func addNewItem(_ sender: UIButton) {`**~~
**`@IBAction func addNewItem(_ sender: UIBarButtonItem) {`**
  `...`
`}`

现在打开 `Main.storyboard` 然后打开对象库。 将 导航条按钮项 拖动到 `Items View Controller` 导航栏的右侧。 选择此 导航条按钮项 并打开其属性检查器。 将 `System Item` 更改为 `Add` （图14.14）。

**图14.14系统栏按钮项**

![](http://upload-images.jianshu.io/upload_images/1230738-7d403a6bdc32599e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

右键从此 导航条按钮项 拖动到 `Items View Controller` 并选择 `addNewItem`：（图14.15）。

**图14.15连接 addNewItem：action**

![](http://upload-images.jianshu.io/upload_images/1230738-c10cec03ca5aa26c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 点击 `+` 按钮，一个新行将出现在表格中。 现在我们来更换`Edit` 按钮。 视图控制器包含的导航条按钮项会自动切换其编辑模式。 没有办法通过 `Interface Builder` 访问它，因此您需要以编程方式添加该按钮项。

在 `ItemsViewController.swift` 中，重写 **init(coder :)** 方法来设置左边导航条按钮项。

> **`required init?(coder aDecoder: NSCoder) {`**
> 
  **`super.init(coder: aDecoder)`**
>
  **`navigationItem.leftBarButtonItem = editButtonItem`**
**`}`**

构建并运行应用程序，添加一些 item，然后点击 `Edit` 按钮。 **UITableView** 进入编辑模式！`editButtonItem` 属性创建了一个标题为 `Edit` 的 **UIBarButtonItem**。 更好的是，这个按钮带有一个目标动作对：在点击时调用它的 **UIViewController** 上的**setEditing(_：animated :)**方法。

打开 `Main.storyboard`。 现在，`Homepwner` 具有全功能的导航栏，您可以摆脱标题视图和相关代码。 在表视图上选择标题视图，然后按 `Delete`。

此外，**UINavigationController** 将处理更新表视图的插值。 在 `ItemsViewController.swift` 中，修改为以下。

> `override func viewDidLoad() {`
  `super.viewDidLoad()`
>
  ~~`// Get the height of the status bar`~~
  ~~`let statusBarHeight = UIApplication.shared.statusBarFrame.height`~~
>
  ~~`let insets = UIEdgeInsets(top: statusBarHeight, left: 0, bottom: 0, right: 0)`~~
  ~~`tableView.contentInset = insets`~~
  ~~`tableView.scrollIndicatorInsets = insets`~~
>
  `tableView.rowHeight = UITableViewAutomaticDimension`
  `tableView.estimatedRowHeight = 65`
`}`

最后，删除 **toggleEditingMode(_ :)** 方法。

> ~~`@IBAction func toggleEditingMode(_ sender: UIButton) {`~~
  ~~`// If you are currently in editing mode...`~~
  ~~`if isEditing {`~~
    ~~`// Change text of button to inform user of state`~~
    ~~`sender.setTitle("Edit", for: .normal)`~~
    ~~`// Turn off editing mode`~~
    ~~`setEditing(false, animated: true)`~~
  ~~`} else {`~~
    ~~`// Change text of button to inform user of state`~~
    ~~`sender.setTitle("Done", for: .normal)`~~
    ~~`// Enter editing mode`~~
    ~~`setEditing(true, animated: true)`~~
  ~~`}`~~ 
~~`}`~~

建立并再次运行。 旧的 `Edit` 和 `Add` 按钮已经消失，留下了一个好看的 **UINavigationBar**（图14.16）。

**图14.16 带导航栏的Homepwner**

![](http://upload-images.jianshu.io/upload_images/1230738-cce19dd1e2c0e033.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**青铜挑战：显示数字键盘**

显示 **Item** 的 `valueInDollars` 的 **UITextField** 的键盘是一个QWERTY键盘。 如果它是一个数字键盘会更好。 将 **UITextField** 的 `Keyboard Type` 更改为 `Number Pad`。 （提示：您可以使用属性检查器在 storyboard 文件中执行此操作。）

**白银挑战：自定义 UITextField**

创建 **UITextField** 的子类，并覆盖 **getsFirstResponder()** 和 **resignFirstResponder()** 方法（继承自 **UIResponder**），以使其边框样式在成为第一个响应者时更改。 您可以使用 **UITextField** 的 **borderStyle** 属性来完成此操作。 在 **DetailViewController** 中使用您自定义的文本字段。

**黄金挑战：推动更多视图控制器**

目前，**Item** 的实例不能更改其 `dateCreated` 属性。 更改 **Item** ，使他们可以，然后在 **DetailViewController** 中的 `dateLabel` 下面添加一个带有 "Change Date" 标题的按钮。当点击此按钮时，将另一个视图控制器实例 push 到导航堆栈。 此视图控制器应包含一个要修改的所选 **Item** 的 `dateCreated` 属性的 **UIDatePicker** 实例。

　　