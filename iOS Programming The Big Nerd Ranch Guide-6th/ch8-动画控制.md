“动画”一词源自拉丁语，意思是“生命的行为”。在您的应用程序中，动画可以将界面元素平滑地带入屏幕或焦点，可以吸引用户的注意力，而它们可以清楚地表明您的应用程序是如何响应用户操作的。 在本章中，您将返回到您的 `Quiz` 应用程序，并使用各种动画技术来实现。

在更新 `Quiz` 之前，我们可以通过文档来查看什么能够动画化。 要打开文档，请打开 `Xcode` 的 `Help` 菜单，然后选择 `Documentation and API Reference`。 这将在新窗口中打开文档。

打开文档后，使用顶部的搜索栏搜索 “UIView”。在搜索结果的 `API Reference` 下，单击 `UIView` 打开类引用，然后向下滚动到标题为 `Animations` 的部分。 该文档提供了一些动画建议（我们将在本书中介绍），并列出了可以动画化的 **UIView** 上的属性（图8.1）。

**图8.1 UIView Animations 文档**

![](http://upload-images.jianshu.io/upload_images/1230738-cdd0b75a2459fa4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 基本动画 ##

该文档始终是了解任何 iOS 技术的良好起点。 有了这个小小的信息，让我们继续添加一些动画到 ‘Quiz’。 您将要使用的第一种类型的动画是 `基本动画(basic animation)`。 基本动画在起始值和结束值之间产生动画（图8.2）。

**图8.2 基本动画**

![](http://upload-images.jianshu.io/upload_images/1230738-a94cc892c5739280.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您要添加的第一个动画将动画化与 **ViewController** 相关联的问题标签的 `alpha` 值（透明度）。 当用户进入下一个问题时，您将使用动画来淡出标签。 **UIView** 上有类方法可以让你完成这个。 最简单的 **UIView** 动画方法是：

`class func animate(withDuration duration: TimeInterval, animations: () -> Void)`

此类方法有两个参数：类型为 **TimeInterval**（它是 **Double** 的别名）的 `duration` 和作为闭包的 `animations` 变量。

### 闭包 ###

`闭包(closure)` 是一组离散的功能，可以围绕您的代码传递。 闭包很像函数和方法。 事实上，函数和方法是闭包的特殊情况。

闭包具有轻量级的语法，允许它们作为参数容易地传递给函数和方法。 闭包甚至可以是函数或方法的返回类型。 在本节中，您将使用闭包来指定要发生的动画。

一个闭包的语法是逗号分隔的括号中的参数列表，后跟一个返回箭头和返回类型：

`(arguments) -> return type`

请注意，此语法与函数的语法类似：

`func functionName(arguments) -> return type`

现在再看一下 `animations` 参数期望的闭包用法：

`class func animate(withDuration duration: TimeInterval, animations: () -> Void)`

这个闭包不会引用任何参数，也不会返回任何内容。 （你有时会看到这个返回类型表示为（），这与 Void 相同）

闭包用法非常简单和熟悉，但是如何在代码中声明关闭？ 闭包语法采用以下形式：

> `{ (arguments) -> return type in`
  `// code`
`}`

闭包表达式写在 大括号({}) 中。 闭包的参数在开头大括号之后的括号内列出。 闭包的返回类型来自参数，并使用常规语法。 关键字 `in` 用于将闭包的参数和返回类型与其正文中的语句分开。

打开 `Quiz.xcodeproj`。 在 `ViewController.swift` 中，添加一个新方法来处理动画，并声明一个不带参数的闭包常量，不返回任何东西。


> **`func animateLabelTransitions() {`**
> 
  **`let animationClosure = { () -> Void in`**
>
  **`}`**
**`}`**

你使用一个常数，引用了一大堆功能。 然而，目前，这个闭包实际上并没有做任何事情。 向闭包添加功能，将 `questionLabel` 的 *alpha* 设置为 `1`.然后，将此闭包作为参数传递给 **animate（withDuration：animations :)**。

> `func animateLabelTransitions() {`
> 
  `let animationClosure = { () -> Void in`
    **`self.questionLabel.alpha = 1`**
  `} `
>
  **`// Animate the alpha`**
  **`UIView.animate(withDuration: 0.5, animations: animationClosure)`**
`}`

在屏幕上，`questionLabel` 已经具有`1` 的 `alpha` 值，所以如果您构建并运行，您将看不到任何动画。 为了解决这个问题，重写 **viewWillAppear（_ :)**，以便每次 **ViewController** 的视图进入屏幕时，将 `questionLabel` 的 `alpha` 重置为 `0`。

> **`override func viewWillAppear(_ animated: Bool) {`**
  **`super.viewWillAppear(animated)`**
>
  **`// Set the label's initial alpha`**
  **`questionLabel.alpha = 0`**
**`}`**

以上代码非常好，但您可以使其更简洁。 更新代码。

> `func animateLabelTransitions() {`
> 
  ~~`let animationClosure = { () -> Void in`~~
    ~~`self.questionLabel.alpha = 1`~~
  ~~`}`~~
> 
  `// Animate the alpha`
  ~~`UIView.animate(withDuration: 0.5, animations: animationClosure)`~~
>
  **`UIView.animate(withDuration: 0.5, animations: {`**
    **`self.questionLabel.alpha = 1`**
  **`})`**
`}`

你有两个改变：首先，你匿名传递闭包（即将它直接传递给方法，而不是将其分配给变量或常量）。 其次，您已经删除了类型信息，因为闭包可以从上下文推断出这一点。

当用户点击 `Next Question` 按钮时，调用 **animateLabelTransitions（）** 方法。

> `@IBAction func showNextQuestion(_ sender: UIButton) {`
  `currentQuestionIndex += 1`
  `if currentQuestionIndex == questions.count {`
    `currentQuestionIndex = 0`
  `} `
>
  `let question: String = questions[currentQuestionIndex]`
  `questionLabel.text = question`
  `answerLabel.text = "???"`
>
  **`animateLabelTransitions()`**
`}`

构建并运行应用程序。 当您点击 `Next Question` 按钮时，标签将淡入视图。 动画提供了令人震惊的用户体验，而不只是简单地让视图直接出现。

### 下一个标签 ###

第一次按下 `Next Question` 按钮时，动画效果很好，但是随后的按钮按下时，没有可见的动画，因为标签的 alpha 值为 1.在本节中，您将要添加另一个标签。 当按下 “下一个问题” 按钮时，现有标签将淡出，而新标签（下一个问题的文本）将淡入。

在 `ViewController.swift` 的顶部，用两个标签替换单个标签的声明。

> ~~`@IBOutlet var questionLabel: UILabel!`~~
**`@IBOutlet var currentQuestionLabel: UILabel!`**
**`@IBOutlet var nextQuestionLabel: UILabel!`**
`@IBOutlet var answerLabel: UILabel!`

`Xcode` 标记四个地方，您需要用其中一个新标签替换 `questionLabel`。 更新 **viewDidLoad()** 以使用 `currentQuestionLabel`。 更新 **viewWillAppear(_ :)** 和 **showNextQuestion(_ :)** 以使用 `nextQuestionLabel`。

> `func viewDidLoad() {`
  `super.viewDidLoad()`
  ~~`questionLabel.text = questions[currentQuestionIndex]`~~
  **`currentQuestionLabel.text = questions[currentQuestionIndex]`**
`}`
> 
`override func viewWillAppear(_ animated: Bool) {`
  `super.viewWillAppear(animated)`
>
  `// Set the label's initial alpha`
  ~~`questionLabel.alpha = 0`~~
  **`nextQuestionLabel.alpha = 0`**
`}`
>
`@IBAction func showNextQuestion(_ sender: UIButton) {` `currentQuestionIndex += 1`
  `if currentQuestionIndex == questions.count {`
    `currentQuestionIndex = 0`
  `}`
>
  `let question: String = questions[currentQuestionIndex]`
  ~~`questionLabel.text = question`~~
  **`nextQuestionLabel.text = question`**
  `answerLabel.text = "???"`
>
  `animateLabelTransitions()`
`}`

现在更新 **animateLabelTransitions（）** 以动画化两个标签的透明度。 您将淡出 `currentQuestionLabel` 并同时淡入 `nextQuestionLabel`。

> `func animateLabelTransitions() {`<br>
  `// Animate the alpha`
  `UIView.animate(withDuration: 0.5, animations: {`
    ~~`self.questionLabel.alpha = 1`~~
    **`self.currentQuestionLabel.alpha = 0`**
    **`self.nextQuestionLabel.alpha = 1`**
  `})`
`}`

打开 `Main.storyboard`。 现在，这两个标签的代码已被更新，您需要进行连接。 右键单击 `View Controller` 以查看连接列表。 请注意，现有的 `questionLabel` 在其旁边仍然存在黄色警告标志（图8.3）。 单击 `x` 删除此连接。

**图8.3 缺少连接**

![](http://upload-images.jianshu.io/upload_images/1230738-d3a661d3065147d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过从`currentQuestionLabel` 标签旁边的圆圈拖到画布上的标签，将 `currentQuestionLabel` outlet 连接到问题标签。

现在将一个新的 `Label` 拖到界面上，并将其放在 `currentQuestionLabel` 标签旁。 将 `nextQuestionLabel` 连接到此新标签。

您希望此标签与现有问题标签处于相同的位置。 正如你可能猜到的那样，实现这一目标的最好方法是通过约束。 右击从 `nextQuestionLabel` 拖动到 `currentQuestionLabel` 并选择 `Top`。 然后右击从
 `nextQuestionLabel` 向上拖动到其父视图，并选择 `Center Horizontally in Container`。

现在，`nextQuestionLabel` 放错了。 选择它，打开 `Resolve Auto Layout Issues` 菜单，然后选择 `Update Frames`。 标签现在将出现在另一个的上面。

构建并运行应用程序。 点击 `Next Question` 按钮，您将看到两个标签优雅地淡入淡出。

如果再次点击它，则不会再出现淡入淡出，因为 *nextQuestionLabel* 已经具有 `1` 的透明度。要解决此问题，您将交换对两个标签的引用。 当动画完成时，需要将 `currentQuestionLabel` 设置为屏幕标签，并将 `nextQuestionLabel` 设置为屏幕外标签。 您将使用 `动画的完成者(completion handler)` 来完成此操作。

## 动画完成 ##

方法 **animate(withDuration：animations :)** 很快就会结束并返回。 也就是说，它开始动画，但是并不等待动画完成。 如果你想知道什么时候动画完成怎么办？ 例如，您可能希望将动画链接在一起，或者在动画完成时更新另一个对象。 要知道动画的完成时间，可以使用闭包中的 `completion` 参数。 您将利用此机会交换两个标签引用。

在 `ViewController.swift` 中，更新 **animateLabelTransitions()** 以使用具有最多参数的 **UIView** 动画方法，其中包括一个 completion 闭包的动画方法。

> `func animateLabelTransitions() {`
> 
  `// Animate the alpha`
  ~~`UIView.animate(withDuration: 0.5, animations: {`~~
  &emsp;&emsp;~~`self.currentQuestionLabel.alpha = 0`~~
  &emsp;&emsp;~~`self.nextQuestionLabel.alpha = 1`~~
  ~~`})`~~
  **`UIView.animate(withDuration: 0.5,`**
    **`delay: 0,`** 
    **`options: [],`** 
    **`animations: {`**
    **`self.currentQuestionLabel.alpha = 0`**
      **`self.nextQuestionLabel.alpha = 1`**
    **`},`**
    **`completion: { _ in`**
    **`swap(&self.currentQuestionLabel,`**
    **`&self.nextQuestionLabel)`**
  **`})`**
}

delay 指示系统在触发动画之前等待多长时间。 我们将在本章后面讨论这些选项。 现在，你将传递一个空的数组。

在 completion 闭包中，您需要告诉系统，以前是 `currentQuestionLabel` 的现在是 `nextQuestionLabel`，而以前的 `nextQuestionLabel` 现在是 `currentQuestionLabel`。 要实现这一点，您可以使用 **swap(_：_ :)** 函数，它接受两个引用并交换它们。

构建并运行应用程序。 现在你可以在所有的问题之间转换。

## 动画化约束 ##

在本节中，您将扩展您的动画，使 `nextQuestionLabel` 从屏幕左侧飞入，当用户按下 `Next Question` 按钮时， `currentQuestionLabel` 会从右侧飞出。 在这样做的时候，你需要学习如何动画化约束。

首先，您需要参考需要修改的约束。 到目前为止，所有 `@IBOutlet` 都是视图对象。 但是，outlet 不仅限于视图——实际上，您的界面文件中的任何对象都可以有 outlet，包括约束。

在 `ViewController.swift` 的顶部，为两个标签的中心约束声明两个 outlet。

> `@IBOutlet var currentQuestionLabel: UILabel!`
**`@IBOutlet var currentQuestionLabelCenterXConstraint: NSLayoutConstraint!`**
`@IBOutlet var nextQuestionLabel: UILabel!`
**`@IBOutlet var nextQuestionLabelCenterXConstraint: NSLayoutConstraint!`**
`@IBOutlet var answerLabel: UILabel!`

现在打开 `Main.storyboard`。 您想将这两个 outlet 连接到各自的约束。 最简单的方法是使用文档大纲。 单击文档大纲 `Constraints` 旁边的三角形，并找到 `Current Question Label CenterX Constraint`。 右击 `View Controller` 拖动到该约束（图8.4）并选择正确的 outlet。 对 `Next Question Label CenterX Constraint` 进行同样的操作。

**图8.4连接约束outlet**

![](http://upload-images.jianshu.io/upload_images/1230738-797b15b856675eb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

目前，`Next Question` 按钮和答案子视图的中心 X 约束在 `currentQuestionLabel` 的中心 X。 当您实现此标签的动画以在屏幕外滑动时，其他子视图将随之而来。 这不是你想要的。

选中 `Next Question` 按钮的 X 值居中到 `currentQuestionLabel` 的约束并将其删除它。 然后从右击 `Next Question` 按钮向上拖动到其父视图，然后选择 `Center Horizontally in Container`。

接下来，您希望两个问题标签在一个屏幕宽度中分开。 `nextQuestionLabel` 的中心将是距视图左侧的屏幕宽度的一半。 `currentQuestionLabel` 的中心将处于当前位置，居中在当前屏幕。

当动画被触发时，两个标签将向右移动全屏宽度，将 `nextQuestionLabel` 放置在屏幕的中心，将 `currentQuestionLabel` 放在屏幕右侧的屏幕宽度的一半（图8.5）。

**图8.5滑动标签**

![](http://upload-images.jianshu.io/upload_images/1230738-e984c98846b102aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了实现这一点，当加载 **ViewController** 的视图时，您需要将 `nextQuestionLabel` 移动到其屏幕外的位置。

在 `ViewController.swift` 中，添加一个新方法，并从 **viewDidLoad()** 中调用它。

> `func viewDidLoad() {`
  `super.viewDidLoad()`
  `currentQuestionLabel.text = questions[currentQuestionIndex]`
>
  **`updateOffScreenLabel()`**
`} `
>
**`func updateOffScreenLabel() {`**
  **`let screenWidth = view.frame.width`**
  **`nextQuestionLabelCenterXConstraint.constant = -screenWidth`**
`}`

你想要动画化标签从左到右地显示。 动画化约束与动画化其他属性有所不同。 如果修改动画块中的约束的常量，则不会发生动画。 为什么？ 修改约束后，系统需要重新计算层中所有相关视图的边框以适应此更改。 任何约束更改都会自动触发，这将是非常浪费的。 （想象一下，如果您更新了相当多的约束条件，你不会想让它在每次更改后都重新计算一次这些边框）。所以您必须要求系统在修改完成后重新计算边框。 为此，您可以在视图中调用 **layoutIfNeeded()** 方法。 这将迫使该视图根据最新的约束来布局其子视图。

在 `ViewController.swift` 中，更新 **animateLabelTransitions()** 以更改约束常量，然后强制更新视图的布局。

> `func animateLabelTransitions() {`
> 
  `// Animate the alpha`
  **`// and the center X constraints`**
  **`let screenWidth = view.frame.width`**
  **`self.nextQuestionLabelCenterXConstraint.constant = 0`**
  **`self.currentQuestionLabelCenterXConstraint.constant += screenWidth`**
>
  `UIView.animate(withDuration: 0.5,`
    `delay: 0,`
    `options: [],`
    `animations: {`
      `self.currentQuestionLabel.alpha = 0`
      `self.nextQuestionLabel.alpha = 1`
>
      **`self.view.layoutIfNeeded()`**
    `},`
    `completion: { _ in`
      `swap(&self.currentQuestionLabel,`
        `&self.nextQuestionLabel)`
  `})`
`}`

最后，在完成处理程序中，您需要交换两个约束 outlet，并将 `nextQuestionLabel` 重置为屏幕左侧。

> `func animateLabelTransitions() {`
> 
  `// Animate the alpha`
  `// and the center X constraints`
  `let screenWidth = view.frame.width`
  `self.nextQuestionLabelCenterXConstraint.constant = 0`
  `self.currentQuestionLabelCenterXConstraint.constant += screenWidth`
>
  `UIView.animate(withDuration: 0.5,`
    `delay: 0,`
    `options: [],`
    `animations: {`
      `self.currentQuestionLabel.alpha = 0`
      `self.nextQuestionLabel.alpha = 1`
>
      `self.view.layoutIfNeeded()`
    `},`
    `completion: { _ in`
      `swap(&self.currentQuestionLabel,`
        `&self.nextQuestionLabel)`
      **`swap(&self.currentQuestionLabelCenterXConstraint,`**
        **`&self.nextQuestionLabelCenterXConstraint)`**
>
      **`self.updateOffScreenLabel()`**
  `})`
`}`

构建并运行应用程序。 动画几乎完美。 标签在屏幕上滑动和关闭，alpha 值也适当地动画化。

有一个小问题要解决，但可能会有点难发现。 要发现它，请从模拟器（`Command-T`）的 `Debug` 菜单中打开 `Slow Animations`。 所有标签的宽度都会被动画化（要在 `answerLabel` 上看到这一点，您需要点击 `Show Answer` 按钮）。 这是因为内在内容大小会在文本更改时发生变化。 解决方法是强制视图在动画开始之前布局其子视图。 这将在 alpha 和滑动动画开始之前更新所有三个标签的边框，以适应下一个文本。

更新 **animateLabelTransitions（）** 以强制视图在动画开始之前布局其子视图。

> `func animateLabelTransitions() {`
> 
  **`// Force any outstanding layout changes to occur`**
  **`view.layoutIfNeeded()`**
>
  `// Animate the alpha`
  `// and the center X constraints`
  `let screenWidth = view.frame.width`
  `self.nextQuestionLabelCenterXConstraint.constant = 0`
  `self.currentQuestionLabelCenterXConstraint.constant += screenWidth`
>
  `UIView.animate(withDuration: 0.5,`
    `delay: 0,`
    `options: [],`
    `animations: {`
      `self.currentQuestionLabel.alpha = 0`
      `self.nextQuestionLabel.alpha = 1`
>
      `self.view.layoutIfNeeded()`
    `},`
    `completion: { _ in`
      `swap(&self.currentQuestionLabel,`
        `&self.nextQuestionLabel)`
      `swap(&self.currentQuestionLabelCenterXConstraint,`
        `&self.nextQuestionLabelCenterXConstraint)`
>
      `self.updateOffScreenLabel()`
  `})`
`}`

构建并运行应用程序并循环浏览一些问题和答案。 动画小问题现已解决。	

## 定时功能 ##

动画的加速由其定时功能控制。 默认情况下，动画使用 `ease-in/ease-out` 的定时功能。 用驾驶来类比，这意味着司机从休息时间平稳地加速到恒定的速度，然后在最后逐渐减速，休息。

其他定时功能包括 `线性(linear)`（从头到尾的恒定速度），`缓和(ease-in)`（加速到恒定速度，然后突然终止）和 `缓解(ease-out)`（从全速开始，然后最终减慢）。

在 `ViewController.swift` 中，更新 **AnimateLabelTransitions（）** 中的动画以使用线性定时函数。

> `UIView.animate(withDuration: 0.5,`
  `delay: 0,`
  `options: [` **`.curveLinear`** `],`
  `animations: {`
    `self.currentQuestionLabel.alpha = 0`
    `self.nextQuestionLabel.alpha = 1`
>
    `self.view.layoutIfNeeded()`
  `},`
  `completion: { _ in`
  `swap(&self.currentQuestionLabel, &self.nextQuestionLabel)`
  `swap(&self.currentQuestionLabelCenterXConstraint, &self.nextQuestionLabelCenterXConstraint)`
>
  `self.updateOffScreenLabel()`
`})`

现在，与使用默认的 easy-in/ease-out 动画曲线相反，动画将具有线性动画曲线。 构建并运行应用程序。 差异是微妙的，但如果你注意它也是能发现的。

options 参数接受 **UIViewAnimationOptions** 参数。 为什么这个参数在方括号内？ 除了定时功能之外，动画还有很多选择。 因此，您需要一种指定多个选项（数组）的方法。 **UIViewAnimationOptions** 符合 **OptionSet** 协议，允许您使用数组对多个值进行分组。

以下是可以传入options 参数的一些可能的动画选项。

动画曲线选项控制动画的加速。 可能的值是：

- `UIViewAnimationOptions.curveEaseInOut`
- `UIViewAnimationOptions.curveEaseIn`
- `UIViewAnimationOptions.curveEaseOut`
- `UIViewAnimationOptions.curveLinear`

`UIViewAnimationOptions.allowUserInteraction`
默认情况下，视图无法与动画相互影响。 指定此选项将覆盖默认值。 这可以用于重复动画，例如脉冲视图。

`UIViewAnimationOptions.repeat`
无限期重复动画; 经常与 `UIViewAnimationOptions.autoreverse` 选项配对。

`UIViewAnimationOptions.autoreverse`
向前运行动画，然后向后，将视图返回到其初始状态。

请务必查看 `UIView Class Reference` 中的 `Constants` 部分，以查看所有可能的选项。

## 青铜挑战：弹簧动画 ##

iOS内置了一个强大的物理引擎。利用这种动力的一个简单的方法是使用弹簧动画。

> `// UIView`
> 
`class func animate(withDuration duration: TimeInterval,`
  `delay: TimeInterval,`
  `usingSpringWithDamping dampingRatio: CGFloat,`
  `initialSpringVelocity velocity: CGFloat,`
  `options: UIViewAnimationOptions,`
  `animations: () -> Void,`
  `completion: ((Bool) -> Void)?)`

使用这种方法使两个标签以弹簧的形式在屏幕上弹入和弹出。 参考 **UIView** 文档来了解每个参数。

## 白银挑战：Layout Guides ##

如果您旋转为横向，则 `nextQuestionLabel` 变得可见。 不要使用硬编码间距约束的常量，使用 **UILayoutGuide** 的实例将两个标签分开。 该 layout guide 应该具有等于 **ViewController** 视图的宽度约束，以确保在不动画时 `nextQuestionLabel` 保持在屏幕外。