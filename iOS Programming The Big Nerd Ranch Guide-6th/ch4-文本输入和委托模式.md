`WorldTrotter` 看起来不错，但到目前为止它并没有做任何事情。 在本章中，您将向 `WorldTrotter` 添加一个 **UITextField** 实例。 文本字段将允许用户键入华氏温度，然后将其转换为摄氏温度并显示在界面上（图4.1）。

**图4.1 具有 UITextField 的 WorldTrotter**

![](http://upload-images.jianshu.io/upload_images/1230738-abeec79c43f357f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您要做的第一件事是向界面添加一个 **UITextField**，并为该文本字段设置约束。 此文本字段将替换当前界面中文本为 “212” 的顶部标签。

打开 `Main.storyboard`。 选择顶部标签，然后按 `Delete` 键删除此子视图。 所有其他标签的限制将变为红色，因为它们都直接或间接地锚定到该顶部标签（图4.2）。 这无所谓， 你会很快修复它。

**图4.2 标签的不明确边框**

![](http://upload-images.jianshu.io/upload_images/1230738-102b5345ac31196c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开对象库，然后将 `Text Field` 拖动到画布顶部之前放置标签的位置。

现在为此文本字段设置约束。 选中文本字段后，打开 `Align` 菜单，并将 `Horizontally in Container` 设置为 `0`.确保 `Update Frames` 设置为 `None`，然后点击 `Add 1 Constraint`。

现在打开 `Add New Constraints` 菜单。 给文本字段提供 8 点的顶边约束，8 点的底边约束，250 的宽度（图4.3）。 添加这三个约束。

**图4.3 文本字段 Add New Constraints 菜单**

![](http://upload-images.jianshu.io/upload_images/1230738-92b7c05e35e1167b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后，选择文本字段下面的标签。 打开 `Align` 菜单，设置 `Horizontal Centers` 为 0，点击 `Update Frames` 菜单的 `All Frames in Container` ，最后点击 `Add 1 Constraint`（图4.4）。

**图4.4 对齐文本字段**

![](http://upload-images.jianshu.io/upload_images/1230738-1aed4b337f962f97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来，自定义一些文本字段属性。 打开文本字段的属性检查器，并进行以下更改：

- 将文本颜色（从 `Color` 菜单）设置为烧橙色。
- 将字体大小设置为 `System` `70`。
- 将 `Alignment` 设置为居中。
- 将占位符文本设置为 **value**。这是当用户没有输入任何文本时将显示的文本。
- 将带有虚线的分段控件的第一个元素的 `Border Style` 设置为 `none`。

文本字段的属性检查器应如图 4.5 所示。

**图4.5 文本字段属性检查器**

![](http://upload-images.jianshu.io/upload_images/1230738-bdb8a58bb0095e62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为文本字段的字体改变了，画布上的视图现在错位了。 选择灰色背景视图，打开 `Resolve Auto Layout Issues` 菜单，然后从 `All Views in View Controller` 部分中选择 `Update Frames`。 文本字段和标签将重新定位以匹配其约束（图4.6）。

**图4.6 更新边框**

![](http://upload-images.jianshu.io/upload_images/1230738-c3721345c8c4d023.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 点击文本字段并输入一些文本。 如果没有看到键盘，请单击模拟器的 `Hardware` 菜单，然后选择 `Keyboard` → `Toggle Software Keyboard` 或使用键盘快捷键 `Command-K`。 默认情况下，模拟器将计算机的键盘视为连接到模拟器的蓝牙键盘。 这通常不是你想要的。 反而您希望模拟器使用的是不附带任何附件的IOS设备屏幕键盘。

### 键盘属性 ###

点击文本字段时，键盘自动向上滑到屏幕上。 （您将在本章后面看到这为什么会发生。）键盘的外观由一组名为 **UITextInputTraits** 的 **UITextField** 属性决定。 这些属性之一是显示的键盘类型。 对于这个应用程序，你想使用数字键盘。

在文本字段的属性检查器中，找到名为 `Keyboard Type` 的属性，然后选择 `Decimal Pad`。 在同一部分，您可以看到您可以自定义键盘的其他一些文本输入特征。 将 `校正(Correction)` 和 `拼写检查(Spell Checking)` 更改为 `No`（图4.7）。

**图4.7 键盘文本输入特征**

![](http://upload-images.jianshu.io/upload_images/1230738-84211f30ddbfd643.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 点击文本字段将输入数字。

### 响应文本字段更改 ###

项目的下一步将是在文本字段中键入文本时更新摄氏度标签。 您将需要编写一些代码来执行此操作。 具体来说，这个代码将写在与该界面关联的视图控制器子类中。

当前界面对应于 `ViewController.swift` 中定义的 **ViewController** 类。 但是，对于管理华氏和摄氏之间转换的视图控制器，**ViewController** 不是一个非常具有描述性的名称。 具有描述性的类型名称可以让您在项目越来越大时更轻松地维护。

你将删除这个文件，并用更具描述性的类替换它。

在项目导航器中，找到 `ViewController.swift` 并将其删除。 然后通过选择 `File` → `New` → `File...` （或按Command-N）创建一个新文件。 选择顶部的 `iOS` 后，在 `Source` 标签下选择 `Swift File`，然后单击 `Next`。

在下一个窗格中，将此文件命名为 `ConversionViewController`。 将文件保存在 `WorldTrotter` 项目中的 `WorldTrotter` 组中，并确保选中了 `WorldTrotter` 目标，如图4.8所示。 单击 `Create`，`Xcode` 将在编辑器中打开 `ConversionViewController.swift`。

**图4.8保存Swift文件**

![](http://upload-images.jianshu.io/upload_images/1230738-adc3bc3d30f1e207.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 `ConversionViewController.swift` 中，导入 **UIKit** 并定义一个名为 **ConversionViewController** 的新视图控制器。

> ~~`import Foundation`~~
**`import UIKit`**
>
**`class ConversionViewController: UIViewController {`**
**`}`**

现在，您需要将您在 `Main.storyboard` 中创建的界面与此新的视图控制器相关联。

打开 `Main.storyboard`，然后在文档大纲中或通过单击界面上方的黄色圆圈选择 View Controller。

打开 身份(identity) 检查器，这是实用程序视图（`Command-Option-3`）中的第三个选项卡。 在顶部，找到 `Custom Class`，并将类更改为 `ConversionViewController`（图4.9）。 （您将在第5章中了解这些）

**图4.9 更改自定义类**

![](http://upload-images.jianshu.io/upload_images/1230738-ec24d16a557814f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您在第1章中看到，当按钮被点击时，按钮可以将事件发送到控制器。 文本字段是另一个控件（**UIButton** 和 **UITextField** 都是 **UIControl** 的子类），并且可以在文本更改时发送事件。

要使这一切正常工作，您将需要创建一个 outlet 到摄氏文本标签，并为文本字段创建一个动作，以便在文本更改时调用。

打开 `ConversionViewController.swift` 并定义此 outlet 和 action。 现在，标签将随用户输入文本字段的任何文本而更新。

> `class ConversionViewController: UIViewController {`
> 
  **`@IBOutlet var celsiusLabel: UILabel!`**
>
  **`@IBAction func fahrenheitFieldEditingChanged(_ textField: UITextField){`**
    **`celsiusLabel.text = textField.text`** 
  **`}`**
`}`

打开 `Main.storyboard` 将它们连接起来。 outlet 将像第1章一样连接。右键从 `Conversion View Controller` 拖动到摄氏标签（当前显示为 “100”），并将其连接到 `celsiusLabel`。

连接动作会有所不同，因为您希望在编辑更改时触发动作。

选择画布上的文本字段，然后从实用程序窗格（最右边的选项卡或`Command-Option-6`）中打开其连接检查器。 连接检查器允许您进行连接并查看已经建立了哪些连接。

您将对文本字段进行更改，触发您在 **ConversionViewController** 中定义的操作。 在连接检查器中，找到 `Sent Events` 部分和 `Editing Changed`。 单击并拖动 `Editing Changed` 到 `Conversion View Controller` 的右侧的圆圈，然后单击弹出菜单中的 `fahrenheitFieldEditingChanged：`动作（图4.10）。

**图4.10连接编辑更改的事件**

![](http://upload-images.jianshu.io/upload_images/1230738-58d859446c3cab67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 点击文本字段并键入一些数字。 摄氏标签将模拟输入的文本。现在删除文本字段中的文本，并注意标签是如何消失的。 没有文字的标签的内容内容宽度和高度为 0，因此下方的标签会向上移动。 我们来解决这个问题。

在 `ConversionViewController.swift` 中，如果文本字段为空，更新 **fahrenheitFieldEditingChanged（_ :)** 显示为 “???”。

> `@IBAction func fahrenheitFieldEditingChanged(_ textField: UITextField) {`
  **~~`//celsiusLabel.text = textField.text`~~**
>
  **`if let text = textField.text, !text.isEmpty {`** 
    **`celsiusLabel.text = text`**
  **`} else {`**
    **`celsiusLabel.text = "???"`**
  **`}`**
`}`

如果文本字段有文本，文本不为空，它将在 celsiusLabel 上显示。 如果这些条件中的任何一个都不为真，那么 `celsiusLabel` 将被赋予字符串“???”。

构建并运行应用程序。 添加一些文本，删除它，并在文本字段为空时确认摄氏标签填充有“???”。

### 取消键盘 ###

目前，没有办法解除键盘。 我们来补充一下这个功能。 这样做的一个常见方法是检测用户何时点击 Return 键并使用该动作来关闭键盘; 您将在第14章中使用此方法。由于数字小键盘没有Return键，您将允许用户点击背景视图以触发解除。

当文本字段被点击时，`FirstFirstResponder()` 将被调用 。 这是除了别的以外，使键盘出现的方法。 要关闭键盘，可以在文本字段中调用 `resignFirstResponder()` 方法。 您将在第14章中更多地了解这些方法。

对于 `WorldTrotter`，您将需要一个文本字段的 outlet 和当后台视图被轻触时触发的方法。 此方法将在文本字段插槽上调用 `resignFirstResponder()`。 我们先来看看代码。

打开 `ConversionViewController.swift` 并在文本字段引用附近声明一个 outlet 。

> `@IBOutlet var celsiusLabel: UILabel!`
**`@IBOutlet var textField: UITextField!`**

现在实现一个在调用时会关闭键盘的动作方法。

（在上面的代码中，我们加上了现有的代码，以便您可以正确定位新的代码的位置。在下面的代码中，我们不提供该上下文，因为新代码的位置不重要，只要它在大括号内 在这种情况下，实现的类型是 **ConversionViewController** 类，当一个代码块包含所有新的代码时，我们建议你把它放在类型的实现的末尾，就在最后的大括号里面，在第15章你会看到当您的文件变得更长，更复杂时，如何轻松地在实现文件中导航。）

> **`@IBAction func dismissKeyboard(_ sender: UITapGestureRecognizer)`**
**`{`**
  **`textField.resignFirstResponder()`**
**`}`**

仍然需要完成以下：`textField` outlet 需要连接在故事板文件中，您需要一个触发您添加的 `dismissKeyboard(_ :)` 方法的方法。

要处理第一项，请打开 `Main.storyboard` 并选择 `Conversion View Controller`。右键从 `Conversion View Controller` 拖动到画布上的文本字段，并将其连接到 `textField`。

现在您需要一种方式来触发您实现的方法。 您将使用手势识别器来完成此操作。

手势识别器是 **UIGestureRecognizer** 的子类，它检测特定的触摸序列，并在检测到该序列时对其目标调用动作。 有手势识别器检测触击，滑动，长按等等。 在本章中，您将使用 **UITapGestureRecognizer** 来检测用户何时触击背景视图。 您将在第19章中更多地了解手势识别器。

在 `Main.storyboard` 中，在对象库中找到 `Tap Gesture Recognizer`。 将此对象拖动到 `Conversion View Controller` 的背景视图中。 您将在 scene 底座，也就是画布上方的图标行中的看到对该手势识别器的引用。

右键从 `Tap Gesture Recognizer` 拖动到 `Conversion View Controller`，并将其连接到 `dismissKeyboard：` 方法（图4.11）。

**图4.11连接手势识别器动作**

![](http://upload-images.jianshu.io/upload_images/1230738-f364a733690e44df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 实现温度转换 ##

了解了界面的基本原理后，让我们来实现从华氏温度转为摄氏温度。 您将要存储当前的华氏值，并在文本字段更改时计算摄氏值。

在 `ConversionViewController.swift` 中，为华氏值添加一个属性。 这将是温度的可选测量 （**Measurement<UnitTemperature>?**）。

> `@IBOutlet var celsiusLabel: UILabel!`
**`var fahrenheitValue: Measurement<UnitTemperature>?`**

该属性是可选的原因是因为用户可能没有键入一个数字，类似于您之前修复的空字符串问题。

现在为 Celsius 值添加一个用于计算的属性。 该值将根据华氏值计算。

> `var fahrenheitValue: Measurement<UnitTemperature>?`
> 
**`var celsiusValue: Measurement<UnitTemperature>? {`**
  **`if let fahrenheitValue = fahrenheitValue {`**
    **`return fahrenheitValue.converted(to: .celsius)`**
  **`} else {`**
    **`return nil`**
  **`}`**
**`}`**

首先检查一下是否有华氏值。 如果有，您将该值转换为等值的摄氏度。 如果没有华氏值，那么您不能计算摄氏度值，因此您返回 `nil`。

 接下来完成：每当华氏值变化时，更新摄氏标签。

向 **ConversionViewController** 添加一个方法来更新 `celsiusLabel`。

> **`func updateCelsiusLabel() {`**
  **`if let celsiusValue = celsiusValue {`**
    **`celsiusLabel.text = "\(celsiusValue.value)"`**
  **`} else {`**
    **`celsiusLabel.text = "???"`**
  **`}`**
**`}`**

您想要在华氏值变化时调用此方法。 为此，您将使用 `属性观察者(property observer)`，它是一个代码块，当属性的值更改时，该代码将被调用。

在属性声明之后立即使用花括号来声明属性观察者。 在大括号内，您可以使用 `willSet` 或 `didSet` 来声明您的观察者，具体取决于是否要在属性值更改之前或之后立即通知属性值更改。

添加一个属性观察器，使得当属性值更改后调用 `fahrenheitValue` 。

> `var fahrenheitValue: Measurement<UnitTemperature>?` **`{`**
  **`didSet {`**
    **`updateCelsiusLabel()`**
  **`}`**
**`}`**

（一个小笔记：当属性值从构造方法中更改时，不会触发属性观察者。）

有了这个逻辑，您现在可以在文本字段更改时更新华氏值（这又会触发更新摄氏标签）。

在 **fahrenheitFieldEditingChanged(_ :)** 中，删除以前的非转换实现，而没有正确更新华氏值。

> `@IBAction func fahrenheitFieldEditingChanged(_ textField: UITextField) {`
  ~~`if let text = textField.text, !text.isEmpty {`~~
    ~~`celsiusLabel.text = text`~~
  ~~`} else {`~~
    ~~`celsiusLabel.text = "???"`~~
  ~~`}`~~
>
  **`if let text = textField.text, let value = Double(text) {`**
    **`fahrenheitValue = Measurement(value: value, unit: .fahrenheit)`**
  **`} else {`**
    **`fahrenheitValue = nil`**
  **`}`**
`}`

首先，您检查文本字段是否有文本。 如果有，检查该文本是否可以由 **Double** 表示。 例如，“3.14” 可以由 **Double** 表示，但 “three” 和 “1.2.3” 不能。 如果这两个检查都通过了，那么华氏温度值被设置为用该 **Double** 值初始化的 **Measurement**。 如果这些检查中的任何一个失败，则华氏值设置为 `nil`。

构建并运行应用程序。 华氏和摄氏之间的转换效果非常好——只要您输入有效的数字。 （它显示的数字有些你并不想要(小数点后的数字过多)，接下来我们来解决它）

如果应用程序首次启动时就更新 `celsiusLabel`，而不是仍然显示 “100”，那就更好了。

覆盖 **viewDidLoad()** 以设置初始值，类似于第1章中的操作。

> **`override func viewDidLoad() {`**
  **`super.viewDidLoad()`**
>
  **`updateCelsiusLabel()`**
**`}`**

在本章的剩余部分中，您将更新 `WorldTrotter` 以解决两个问题：您将格式化 Celsius 值以显示最多一个小数位的精度，并且不允许用户键入多个小数分隔符。

您的应用程序还有其他几个问题，但现在您将重点关注这两个问题。在本章结尾处，其他问题将作为挑战呈现。 我们开始更新摄氏度值的精度。

### 数字格式化 ###

您使用 数字格式化器(`number formatter`) 自定义数字的显示。 还有其他格式化器用于格式化日期，能量，质量，长度，测量等。

在 `ConversionViewController.swift` 中创建一个 number formatter。

> **`let numberFormatter: NumberFormatter = {`**
  **`let nf = NumberFormatter()`**
  **`nf.numberStyle = .decimal`**
  **`nf.minimumFractionDigits = 0`**
  **`nf.maximumFractionDigits = 1`**
  **`return nf`**
**`}()`**

在这里，您使用闭包来实例化数字格式化程序。 您正在创建具有 `.decimal` 样式的 **NumberFormatter**，并将其配置为显示不超过一个小数位数。 您将在第16章中了解更多关于声明属性的新语法。

现在修改 **updateCelsiusLabel（）** 来使用这个格式化程序。

> `func updateCelsiusLabel() {`
  `if let celsiusValue = celsiusValue {`
    ~~`celsiusLabel.text = "\(celsiusValue.value)"`~~
    **`celsiusLabel.text =numberFormatter.string(from: NSNumber(value: celsiusValue.value))`** 
  `} else {`
    `celsiusLabel.text = "???"`
  `}`
`}`

构建并运行应用程序。 输入多个华氏温度观察格式化方法是否有效。 您将不会在摄氏标签上看到多于一位的小数出现。

在下一节中，您将更新应用程序，实现在文本字段中接受最多一个小数分隔符。 为此，您将使用一种常见的 iOS 设计模式，称为 `委托模式(delegation)`。

## 委托模式 ##

委托是一个面向对象的 `回调(callback)` 方法。 回调是在事件发生前提供的一个函数，每次事件发生时都会调用它。 一些对象需要对多个事件进行回调。 例如，当用户输入文本以及用户按 Return 键时，文本字段都需要“回调”。

然而，两个（或多个）回调函数之间没有内置的方式来协调和共享信息。 这是委托所解决的问题——你提供一个委托来接收特定对象的所有与事件有关的回调。 然后，该委托对象可以进行存储，操作等，并在它认为合适的时候从回调中传递信息。

当用户文本字段输入时，该文本字段将询问其委托是否接受用户所做的更改。 对于 `WorldTrotter`，如果用户尝试输入第二个小数分隔符，则要拒绝该更改。 文本字段的委托将是 **ConversionViewController** 的实例。

### 符合协议 ###

第一步是通过声明 **ConversionViewController** 符合 **UITextFieldDelegate** 协议，使 **ConversionViewController** 类的实例成为 **UITextField** 的委托角色。 对于每个委托角色，都有一个相应的协议，声明对象可以调用它的委托的方法。

**UITextFieldDelegate** 协议如下所示：

> `protocol UITextFieldDelegate: NSObjectProtocol {`
  `optional func textFieldShouldBeginEditing(_ textField: UITextField) -> Bool`
  `optional func textFieldDidBeginEditing(_ textField: UITextField)`
  `optional func textFieldShouldEndEditing(_ textField: UITextField) -> Bool`
  `optional func textFieldDidEndEditing(_ textField: UITextField)`
  `optional func textField(_ textField: UITextField,`
            `shouldChangeCharactersIn range: NSRange,`
            `replacementString string: String) -> Bool`
  `optional func textFieldShouldClear(_ textField: UITextField) -> Bool`
  `optional func textFieldShouldReturn(_ textField: UITextField) -> Bool`
`}`

**UITextFieldDelegate** ，与其它协议一样，使用关键字 `protocol` 来声明，其后面是它的名字 。 冒号之后的 **NSObjectProtocol** 是指 **NSObject** 协议，并告诉您 **UITextFieldDelegate** 继承 **NSObject** 协议中的所有方法。 接下来声明特定于 **UITextFieldDelegate** 的方法。

您无法创建协议实例; 它只是方法和属性的列表。 相反，实现方法被保留到符合协议的每一种类型。

在类的声明中，类遵循的协议列表跟在父类（如果有的话）之后并以逗号分隔的。 在 `ConversionViewController.swift` 中，声明 **ConversionViewController** 符合 **UITextFieldDelegate** 协议。

`class ConversionViewController: UIViewController, UITextFieldDelegate {`

用于委托的协议称为 `委托协议(delegate protocol)`，委托协议的命名约定是委派类的名称加上 **Delegate** 一词。 然而并不是所有的协议都是委托协议，第16章中将会看到一种不同类型协议的例子。目前为止我们提到的协议是 iOS SDK 的一部分，您也可以编写自己的协议。

### 使用委托 ###

现在您已将 **ConversionViewController** 声明为符合 **UITextFieldDelegate** 协议，您可以去设置文本字段的 `delegate*` 属性了。

打开 `Main.storyboard`，然后从文本框拖动到 `Control View Controller`。 从弹出菜单中选择 `delegate` 并将文本字段的 `delegate` 属性连接到 **ConversionViewController**。

接下来，您将实现您感兴趣的 **UITextFieldDelegate** 方法 —— **textField(_：shouldChangeCharactersIn：replacementString :)**。 因为文本字段在其委托中调用此方法，所以必须在 `ConversionViewController.swift` 中实现它。

在 `ConversionViewController.swift`,实现 **textField(_:shouldChangeCharactersIn:replacementString:)** 打印文本字段的当前文本以及替换字符串。现在，只要这个方法返回 `true`即可。

> **`func textField(_ textField: UITextField,`**
      **`shouldChangeCharactersIn range: NSRange,`**
      **`replacementString string: String) -> Bool {`**
>
  **`print("Current text: \(textField.text)")`**
  **`print("Replacement text: \(string)")`**
  **`return true`**
**`}`**

注意，Xcode 能够自动完成这个方法，因为 **ConversionViewController** 符合 **UITextFieldDelegate**。在实现协议的方法之前，先声明一个协议是一个好主意，这样 `Xcode` 才会提供这种支持。

构建并运行应用程序。 在文本字段中输入几位数字，并观看 `Xcode` 控制台（图4.12）。 它打印文本字段的当前文本以及替换字符串。

**图4.12打印到控制台**

![](http://upload-images.jianshu.io/upload_images/1230738-20db3df06b63a11e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您的目标是防止多个小数分隔符，考虑这个 `当前文本(current text)` 和 `替换文本(replacement text)` 。 逻辑上，如果现有的字符串有一个小数分隔符，替换字符串有一个小数分隔符，那么更改应该被拒绝。

在 `ConversionViewController.swift` 中，更新 **textField(_：shouldChangeCharactersIn：replacementString :)** 以使用此逻辑。

> **`func textField(_ textField: UITextField,`**
      **`shouldChangeCharactersIn range: NSRange,`**
      **`replacementString string: String) -> Bool {`**
>
  ~~`print("Current text: \(textField.text)")`~~
  ~~`print("Replacement text: \(string)")`~~
  ~~`return true`~~ 
>
  **`let existingTextHasDecimalSeparator = textField.text?.range(of: ".")`**
  **`let replacementTextHasDecimalSeparator = string.range(of: ".")`**
>
  **`if existingTextHasDecimalSeparator != nil,`**
    **`replacementTextHasDecimalSeparator != nil {`**
    **`return false`**
  **`} else {`**
    **`return true`**
  **`}`**
**`}`**

构建并运行应用程序。 尝试输入多个小数分隔符; 应用程序将拒绝您输入的第二个小数分隔符。

### 协议其他 ###

在 **UITextFieldDelegate** 协议中，有两种方法：处理信息更新的方法和处理输入请求的方法。 例如，如果文本字段的代理想知道用户在文本字段上点击的时候，文本字段的委托应该实现 **textFieldDidBeginEditing(_ :)** 方法。

另一方面，**textField(_：shouldChangeCharactersIn：replacementString :)** 是一个输入请求。 文本字段在其委托中调用此方法来询问替换字符串是否应被接受或拒绝。 该方法返回一个 **Bool**，它是委托者的回复。

在协议中声明的方法可以是必需的或可选的。 默认情况下，协议方法是必要的，这意味着符合协议的类必须具有这些方法的实现。 如果一个协议有可选的方法，那么它们前面会有 `optional`。 回顾 **UITextFieldDelegate** 协议，您可以看到所有的方法都是可选的。 委托协议通常是这样的。

## 青铜挑战：禁止字母字符 ##

目前，用户可以通过使用蓝牙键盘或将复制的文本粘贴到文本字段中来输入字母字符。 解决这个问题。 提示：您需要使用到 **NSCharacterSet** 类。