在第十八章中，您通过实现 **UIResponder** 的方法来处理原始触摸。 有时你想检测一个特定的触摸模式——手势，如捏或滑动。 您可以使用 **UIGestureRecognizer** 的实例而不用自己写代码来检测常用手势。

**UIGestureRecognizer** 拦截由视图处理的触摸。 当它识别出一个特定的手势时，它就会根据你选择的对象调用一个方法。 SDK 中内置了多种类型的手势识别器。 在本章中，您将使用其中三个来允许 **TouchTracker** 用户选择，移动和删除线（图19.1）。 您还将看到如何使用另一个有趣的 iOS 类，**UIMenuController**。

**图19.1 本章末尾的TouchTracker**

![](http://upload-images.jianshu.io/upload_images/1230738-ec4b5929bbc2e9ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## UIGestureRecognizer子类 ##

您不需要自己去实例化 **UIGestureRecognizer**。 相反, **UIGestureRecognizer** 有很多子类，每个子类负责识别一个特定的手势。

要使用 **UIGestureRecognizer** 子类的实例，请给它一个 目标动作对 并将其与视图相关联。 每当手势识别器在视图上识别其手势时，它将向其目标发送动作消息。 所有 **UIGestureRecognizer** 动作消息具有相同的形式：

> `func action(_ gestureRecognizer: UIGestureRecognizer) { }`

当识别手势时，手势识别器截取视图内指定的触摸（图19.2）。 因此，在使用了手势识别器的视图上可能不会调用像 **touchesBegan(_：with :)** 这样的典型的 **UIResponder** 方法。

**图19.2 手势识别器拦截**

![](http://upload-images.jianshu.io/upload_images/1230738-287be7bd31fdbbd4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 用 UITapGestureRecognizer 检测点击 ##

您将使用的第一个 **UIGestureRecognizer** 子类是 **UITapGestureRecognizer**。 当用户点击屏幕两次时，屏幕上的所有线将被清除。

打开 `TouchTracker.xcodeproj` 和 `DrawView.swift`。 添加一个 **init?(coder :)** 方法并实例化一个 **UITapGestureRecognizer**，需要两次点击来触发并调用其目标上的动作方法。

> **`required init?(coder aDecoder: NSCoder) {`**
  **`super.init(coder: aDecoder)`**
>
  **`let doubleTapRecognizer = UITapGestureRecognizer(target: self, action: #selector(DrawView.doubleTap(_:)))`**
  **`doubleTapRecognizer.numberOfTapsRequired = 2`**
  **`addGestureRecognizer(doubleTapRecognizer)`**
**`}`**

现在当在 **DrawView** 的一个实例上发生双击时，将在该实例上调用 **doubleTap(_ :)** 方法。 在 `DrawView.swift` 中实现此方法。

> **`func doubleTap(_ gestureRecognizer: UIGestureRecognizer) {`**
  **`print("Recognized a double tap")`**
>  
  **`currentLines.removeAll()`**
  **`finishedLines.removeAll()`**
  **`setNeedsDisplay()`**
**`}`**

请注意，手势识别器的动作方法的参数是调用该方法的 **UIGestureRecognizer** 的实例。 在双击的情况下，您不需要识别器的任何信息，但在本章后面您将需要用到其他识别器的信息。

构建并运行应用程序，绘制几条线，然后双击屏幕以清除它们。

您可能已经注意到（特别是在模拟器上），第一次点击双击会导致绘制一个小红点。 之所以出现这个点，是因为在第一次点击时，我们在 **DrawView** 上调用了 **touchesBegan(_：with :)**，创建了一条很短的线。 检查控制台，您将看到以下事件序列：

> `touchesBegan(_:with:)`
`Recognized a double tap`
`touchesCancelled(_:with:)`

手势识别器通过检查触摸事件来确定他们的特定手势是否发生。 在识别手势之前，手势识别器拦截所有的 **UIResponder** 方法调用。 如果它没有识别出是某个手势，那么每个调用都将转发回到视图中去。

识别点击需要触摸的开始和结束。 这意味着当最初调用 **touchesBegan(_：with :)** 时，**UITapGestureRecognizer** 不知道该触摸是否是点击，因此还是会在视图上调用该方法。 当触摸结束时，识别出为点击，手势识别器会将该触摸声明为自己所有。 它通过在视图上调用 **touchesCancelled(_：with :)** 来实现。 之后，其它的 **UIResponder** 方法将无法在该视图中调用。

要防止这个红点暂时出现，您必须防止在视图中调用 **touchesBegan(_：with :)**。 您可以告诉  **UIGestureRecognizer** 在其视图上延迟调用 **touchesBegan(_：with :)**，因为该触摸有可能会被识别为手势。

在 `DrawView.swift` 中，修改 **init?(coder :)** 来实现。

> `required init?(coder aDecoder: NSCoder) {`
  `super.init(coder: aDecoder)`
>
  `let doubleTapRecognizer = UITapGestureRecognizer(target: self, action: #selector(DrawView.doubleTap(_:)))`
  `doubleTapRecognizer.numberOfTapsRequired = 2`
  **`doubleTapRecognizer.delaysTouchesBegan = true`**
  `addGestureRecognizer(doubleTapRecognizer)`
`}`

构建并运行应用程序，绘制一些线，然后双击以清除它们。 双击时，您将不会再看到红点。

## 多种手势识别器 ##

下一步是添加另一个允许用户选择一条线的手势识别器。 （稍后，用户将能够删除所选行。）您将在 **DrawView** 上添加只需要一次点击就触发的另一个 **UITapGestureRecognizer**。

在 `DrawView.swift` 中，修改 **init?(coder :)** 来添加这个手势识别器。

> `required init?(coder aDecoder: NSCoder) {`
  `super.init(coder: aDecoder)`
>
  `let doubleTapRecognizer = UITapGestureRecognizer(target: self, action: #selector(DrawView.doubleTap(_:)))`
  `doubleTapRecognizer.numberOfTapsRequired = 2`
  `doubleTapRecognizer.delaysTouchesBegan = true`
  `addGestureRecognizer(doubleTapRecognizer)` 
>
  **`let tapRecognizer = UITapGestureRecognizer(target: self, action: #selector(DrawView.tap(_:)))`**
  **`tapRecognizer.delaysTouchesBegan = true`**
  **`addGestureRecognizer(tapRecognizer)`**
`}`

现在，在 `DrawView.swift` 中实现 **tap(_ :)**，将点击记录到控制台。

> **`func tap(_ gestureRecognizer: UIGestureRecognizer) {`**
  **`print("Recognized a tap")`**
**`}`**

构建并运行应用程序。 尝试点击和双击。 点击一次将适当的消息记录到控制台。 然而，双击可以触发 **tap(_:)** 和 **doubleTap（_ :)** 两个方法。

在具有多个手势识别器的情况下，一个手势识别器识别到该触摸并处理，但其实你想要让另一个手势识别器去处理这个手势，这是很常见的。 在这些情况下，您可以在识别器之间设置依赖关系，这些依赖关系就像：“等等你先别急，这个触摸可能是我的！”

在 **init?(coder:)** 中，使 `tapDecognizer` 等待直到 `doubleTapRecognizer` 无法识别双击才能声明为其自身的点击。

> `required init?(coder aDecoder: NSCoder) {`
  `super.init(coder: aDecoder)`
>
  `let doubleTapRecognizer = UITapGestureRecognizer(target: self, action: #selector(DrawView.doubleTap(_:)))`
  `doubleTapRecognizer.numberOfTapsRequired = 2`
  `doubleTapRecognizer.delaysTouchesBegan = true`
  `addGestureRecognizer(doubleTapRecognizer)`
>
  `let tapRecognizer = UITapGestureRecognizer(target: self, action: #selector(DrawView.tap(_:)))`
  `tapRecognizer.delaysTouchesBegan = true`
  **`tapRecognizer.require(toFail: doubleTapRecognizer)`**
  `addGestureRecognizer(tapRecognizer)`
`}`

再次构建并运行应用程序，并尝试一些点击。 点击发生后，单击现在需要少量时间去触发，但双击不再触发 **tap(_:)** 消息。

接下来，让我们在 **DrawView** 上构建，以便用户可以在点击时选择一条线。 首先，在 `DrawView.swift` 的顶部添加一个属性来保存所选线的索引。

> `class DrawView: UIView {`
  `var currentLines = [NSValue:Line]()`
  `var finishedLines = [Line]()`
  **`var selectedLineIndex: Int?`**

现在修改 **draw(_ :)** 将所选线画成绿色。

> `override func draw(_ rect: CGRect) {`
  `finishedLineColor.setStroke()`
  `for line in finishedLines {`
    `stroke(line)`
  `} `
>
  `currentLineColor.setStroke()`
  `for (_,line) in currentLines {`
    `stroke(line)`
  `} `
>
  **`if let index = selectedLineIndex {`**
    **`UIColor.green.setStroke()`**
    **`let selectedLine = finishedLines[index]`**
    **`stroke(selectedLine)`**
  **`}`**
`}`

仍然在 `DrawView.swift` 中，添加一个 **indexOfLine(at :)** 方法返回离给定点最接近的 **Line** 的索引。

> **`func indexOfLine(at point: CGPoint) -> Int? {`**
  **`// Find a line close to point`**
  **`for (index, line) in finishedLines.enumerated() {`**
    **`let begin = line.begin`**
    **`let end = line.end`**
>
    **`// Check a few points on the line`**
    **`for t in stride(from: CGFloat(0), to: 1.0, by: 0.05) {`**
      **`let x = begin.x + ((end.x - begin.x) * t)`**
      **`let y = begin.y + ((end.y - begin.y) * t)`**
>
      **`// If the tapped point is within 20 points, let's return this line`**
      **`if hypot(x - point.x, y - point.y) < 20.0 {`**
        **`return index`**
      **`}`**
    **`}`**
  **`} `**
>
  **`// If nothing is close enough to the tapped point, then we did not select a line`**
  **`return nil`**
**`}	`**

**stride(from:to:by:)** 方法将允许 t 从 `from` 值开始，并且增加到（但不达到） `to` 值，每次增量为 `by`。

还有其他更好的方法可以确定最接近某个点的线条，但是这个简单的实现已经达到目的了。

要传递的 `point` 是手指点击的点。 您可以轻松获取此信息。 每个 **UIGestureRecognizer** 都有一个 **location(in :)** 方法。 在手势识别器上调用此方法将给出在作为参数传递的视图的坐标系中触发手势的坐标。

在 `DrawView.swift` 中，更新 **tap(_ :)** 来调用手势识别器的 **location(in:)**，将结果传递给 **indexOfLine(in:)**，并将返回的索引赋给 `selectedLineIndex`。

> `func tap(_ gestureRecognizer: UIGestureRecognizer) {`
  `print("Recognized a tap")`
>
  **`let point = gestureRecognizer.location(in: self)`**
  **`selectedLineIndex = indexOfLine(at: point)`**
>
  **`setNeedsDisplay()`**
`}`

如果用户在选择一条线时使用了双击来清除所有线，应用程序将崩溃。 要解决这个问题，请更新 **doubleTap(_ :)** 将 `selectedLineIndex` 设置为 `nil`。

> `func doubleTap(_ gestureRecognizer: UIGestureRecognizer) {`
  `print("Recognized a double tap")`
>
  **`selectedLineIndex = nil`**
  `currentLines.removeAll()`
  `finishedLines.removeAll()`
  `setNeedsDisplay()`
`}`

构建并运行应用程序。 画几条线，然后点击其中一条。 点击的线应显示为绿色（请记住，在点击而不是双击被识别出来之前需要一些时间）。

## UIMenuController ##

接下来，您需要做到当用户选择一条线时，具有删除该行的选项的菜单将显示在用户点击的位置。 有一个内置的类用于提供这种菜单，称为 **UIMenuController**（图19.3）。 菜单控制器具有 **UIMenuItem** 对象的列表，并且在现有视图中呈现。 每个 item 都有一个标题（菜单中显示的内容）和一个动作（它发送到窗口的第一响应者的消息）。

**图19.3 UIMenuController**

![](http://upload-images.jianshu.io/upload_images/1230738-2b1ee3ec6318bd45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每个应用程序只有一个 **UIMenuController**。 当你想呈现这个实例时，你可以用菜单项来填充它，给它一个矩形来呈现，并将其设置为可见。

在 `DrawView.swift` 的 **tap(_ :)** 方法中执行如下操作，如果用户已经点击了一条线。 如果用户点击不在该线附近的某处，则当前选中的行将被取消选择，并且菜单控制器将被隐藏。

> `func tap(_ gestureRecognizer: UIGestureRecognizer) {`
  `print("Recognized a tap")`
>
  `let point = gestureRecognizer.location(in: self)`
  `selectedLineIndex = indexOfLine(at: point)`
>
  **`// Grab the menu controller`**
  **`let menu = UIMenuController.shared`**
>
  **`if selectedLineIndex != nil {`**
>
    **`// Make DrawView the target of menu item action messages`**
    **`becomeFirstResponder()`**
>
    **`// Create a new "Delete" UIMenuItem`**
    **`let deleteItem = UIMenuItem(title: "Delete",action: #selector(DrawView.deleteLine(_:)))`**
    **`menu.menuItems = [deleteItem]`**
>
    **`// Tell the menu where it should come from and show it`**
    **`let targetRect = CGRect(x: point.x, y: point.y, width: 2, height: 2)`**
    **`menu.setTargetRect(targetRect, in: self)`**
    **`menu.setMenuVisible(true, animated: true)`**
  **`} else {`**
    **`// Hide the menu if no line is selected`**
    **`menu.setMenuVisible(false, animated: true)`**
  **`} `**
>
  `setNeedsDisplay()`
`}`

要显示菜单控制器，在 **UIMenuController** 的菜单项中响应至少一个动作消息的视图必须是窗口的第一响应者——这就是为什么在设置菜单控制器之前，要在 **DrawView** 上调用方法 **becomeFirstResponder()**。

如果您的自定义视图类要成为第一响应者，则还必须覆盖 `canBecomeFirstResponder`。 在 `DrawView.swift` 中，覆盖此属性以返回 `true`。

> **`override var canBecomeFirstResponder: Bool {`**
  **`return true`**
**`}`**

最后，在 `DrawView.swift` 中实现 **deleteLine(_ :)**。

> **`func deleteLine(_ sender: UIMenuController) {`**
  **`// Remove the selected line from the list of finishedLines`**
  **`if let index = selectedLineIndex {`**
    **`finishedLines.remove(at: index)`**
    **`selectedLineIndex = nil`**
>
    **`// Redraw everything`**
    **`setNeedsDisplay()`**
  **`}`**
**`}`**

当被呈现时，菜单控制器通过每个菜单项并且询问第一响应者是否实现该项的动作方法。 如果第一响应者没有实现该方法，则菜单控制器将不会显示相关的菜单项。 如果菜单项没有由第一响应者实现的动作方法，则完全不显示该菜单。

构建并运行应用程序。 画一条线，点击它，然后从菜单项中选择 `Delete`。

选择一条线，然后双击以清除所有行，而菜单控制器仍然可见。 如果 `selectedLineIndex` 为 `nil`，菜单控制器应该为不可见才对。

在 `DrawView.swift` 中向 `propertiesLineIndex` 添加属性观察器，如果索引设置为 `nil`，则将菜单控制器设置为不可见。

> `var selectedLineIndex: Int? {`
  **`didSet {`**
    **`if selectedLineIndex == nil {`**
      **`let menu = UIMenuController.shared`**
      **`menu.setMenuVisible(false, animated: true)`**
    **`}`**
  **`}`**
`}`

构建并运行应用程序。 画一条线，选择它，然后双击背景。 线和菜单控制器将不再可见。

## 更多手势识别器 ##

在本节中，您将添加用户通过长按来选择线的功能，然后通过平移移动所选线。 这将需要用到另外两个 **UIGestureRecognizer** 子类：

**UILongPressGestureRecognizer** 和 **UIPanGestureRecognizer**。

### UILongPressGestureRecognizer ###

在 `DrawView.swift` 中，在  **init?(coder:)** 中实例化一个 **UILongPressGestureRecognizer** 并将其添加到 **DrawView**。

>   `...`
  `addGestureRecognizer(tapRecognizer)`
>
  **`let longPressRecognizer = UILongPressGestureRecognizer(target: self, action: #selector(DrawView.longPress(_:)))`**
  **`addGestureRecognizer(longPressRecognizer)`**
`}`

当用户按住 **DrawView** 时，将会调用 **longPress(_ :)** 方法。 默认情况下，触摸必须持续 0.5 秒才被视为长按，但如果你想修改的话，可以更改手势识别器的 `minimumPressDuration`。

到目前为止，您已经使用过手势了。 点击 是一个 独立(`discrete`) 的手势。 当被识别时，手势已经结束，并且已经传送了动作消息。 另一方面，长按是一个 `持续(`continuous`) 的手势。 持续的手势随着时间推移而发生，为了跟踪持续的手势发生了什么，您可以检查识别器的 `state` 属性。

例如，考虑一个典型的长按：

- 当用户触摸视图时，长按识别器会注意到 可能(`possible`) 是一个长按，但是它必须等待观看触摸是否持续足够长以成为长按手势。 识别器的状态是 `UIGestureRecognizerState.possible`。
- 一旦用户持续足够长的时间，长按可以被识别并且手势已经 开始(`began`)。 识别器的状态是 `UIGestureRecognizerState.began`。
- 当用户移除手指时，手势已经 结束(`ended`)。 识别器的状态是 `UIGestureRecognizerState.ended`。

当长按手势识别器从 possible 转移到 began 和从 began 到 ended 时，它将其动作消息发送到其目标。 要确定在哪个转换中触发动作，可以查看手势识别器的 `state`。

请记住，长按是较大功能的一部分。 在下一节中，您将使用户可以通过用长按开始的同一个手指拖动所选直线来移动所选直线线。 所以这里是实现 **longPress(_ :)** 动作方法的计划：当识别器处于 began 状态时，您将选择距离手势发生的最近的直线。 当识别器处于 ended 状态时，您将取消选择该线。

在 `DrawView.swift` 中，实现 **longPress(_ :)**。

> **`func longPress(_ gestureRecognizer: UIGestureRecognizer) {`**
  **`print("Recognized a long press")`**
>
  **`if gestureRecognizer.state == .began {`**
    **`let point = gestureRecognizer.location(in: self)`**
    **`selectedLineIndex = indexOfLine(at: point)`**
>
    **`if selectedLineIndex != nil {`**
      **`currentLines.removeAll()`**
    **`}`**
  **`} else if gestureRecognizer.state == .ended {`**
    **`selectedLineIndex = nil`**
  **`} `**
>
  **`setNeedsDisplay()`**
**`}`**

构建并运行应用程序。 画一条直线，然后按住它; 该行将变为绿色并成为所选行。 当您放开时，该行将恢复为其之前的颜色，并且将不再是所选行。

### UIPanGestureRecognizer 和 同时识别器 ###

在 `DrawView.swift` 中，声明一个 **UIPanGestureRecognizer** 作为一个属性，以便您可以在所有方法中访问它。	

> `class DrawView: UIView {`
> 
  `var currentLines = [NSValue:Line]()`
  `var finishedLines = [Line]()`
  `var selectedLineIndex: Int? {`
    `...`
  `}`
  **`var moveRecognizer: UIPanGestureRecognizer!`**

接下来，在 `DrawView.swift` 中，添加代码到 **init?(coder :)** 来实例化一个 **UIPanGestureRecognizer**，设置其中的一个属性，并将其添加到 **DrawView**。

>   `let longPressRecognizer = UILongPressGestureRecognizer(target: self, action: #selector(DrawView.longPress(_:)))`
  `addGestureRecognizer(longPressRecognizer)`
>
  **`moveRecognizer = UIPanGestureRecognizer(target: self, action: #selector(DrawView.moveLine(_:)))`**
  **`moveRecognizer.cancelsTouchesInView = false`**
  **`addGestureRecognizer(moveRecognizer)`**
`}`

`cancelsTouchesInView` 是什么？ 每个 **UIGestureRecognizer** 都具有此属性，默认为 `true`。 当 `cancelsTouchesInView` 为 `true` 时，手势识别器将 “吃” 掉任何可识别的触摸，并且视图将无法通过传统的 **UIResponder** 方法，例如 **touchesBegan(_：with :)** 来处理触摸。

通常，这是你想要的，但不总是。 在这种情况下，如果平移手势识别器要触摸它，则用户将无法绘制线条。 当您将 `cancelsTouchesInView` 设置为 `false` 时，您确保手势识别器识别的任何触摸也将通过 **UIResponder** 方法传递到视图。

在 `DrawView.swift` 中，为动作方法添加一个简单的实现：

> **`func moveLine(_ gestureRecognizer: UIPanGestureRecognizer) {`**
  **`print("Recognized a pan")`**
**`}`**

构建并运行应用程序并绘制一些直线。 因为 `cancelsTouchesInView` 为 `false`，所以可以识别平移手势，也可以绘制线条。 您可以注释掉设置 `cancelsTouchesInView` 的代码行，再次运行以查看差异。

接下来，当用户的手指移动到屏幕上时，您将更新 **moveLine(_ :)** 来重绘所选线。 但首先，您需要两个手势识别器才能处理相同的触摸。 通常，当手势识别器识别出其手势时，它会吃掉它，而其他识别器没有机会处理该触摸。 尝试一下：运行应用程序，画一条线，按住选择该线，然后移动你的手指。 控制台报告是长按而不是平移。

在这种情况下，默认行为是有问题的：为了按住并选择一条线，然后平移以移动线，在这过程中您的用户是不会将手指抬起的。 因此，两个手势应该同时发生，并且即使长按手势已经识别长按，也必须允许平移手势识别器识别平移。

为了允许手势识别器与其他手势识别器同时识别其手势，您可以从 **UIGestureRecognizerDelegate** 协议实现一种方法：

> `optional func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, shouldRecognizeSimultaneouslyWith otherGestureRecognizer: UIGestureRecognizer) -> Bool`

第一个参数是要求引导的手势识别器。 它对其 委托 说：“如果在我和另一个的识别器之中的某一个刚刚识别出了一个手势。 那那个不识别的应该保持在 possible 的状态，并继续追踪这个触摸吗？”

请注意，调用本身并没有告诉您两个识别器中哪一个已经识别出它的手势，因此，有的可能被剥夺了识别其手势的机会。

默认情况下，该方法返回 `false`，并且手势识别器仍处于 possible 状态，使手势中的触摸处于 recognized 状态。 您可以实现返回 `true` 的方法，以允许两个识别器在相同的触摸中识别各自的手势。 （如果您需要确定两个识别器中的哪一个识别其手势，则可以检查它们的 state 属性。）

要长时间启用平移功能，您将要给平移手势识别器一个委托（**DrawView**）。 然后，当长按识别器识别其手势时，平移手势识别器将在其委托上调用同时识别方法。 您将在 **DrawView** 中实现此方法返回 `true`。 这将允许平移手势识别器识别在长按进行过程中发生的任何平移。

首先，在 `DrawView.swift` 中，声明 **DrawView** 符合 **UIGestureRecognizerDelegate** 协议。

> `class DrawView: UIView, ` **`UIGestureRecognizerDelegate {`**<br>
  `var currentLines = [NSValue:Line]()`
  `var finishedLines = [Line]()`
  `var selectedLineIndex: Int? {`
  `...`
`}`
`var moveRecognizer: UIPanGestureRecognizer!`

接下来，在 **init?(coder :)** 中，将 **DrawView** 设置为 **UIPanGestureRecognizer** 的委托。

>   `let longPressRecognizer = UILongPressGestureRecognizer(target: self, action: #selector(DrawView.longPress(_:)))`
  `addGestureRecognizer(longPressRecognizer)`
>
  `moveRecognizer = UIPanGestureRecognizer(target: self, action: #selector(DrawView.moveLine(_:)))`
  **`moveRecognizer.delegate = self`**
  `moveRecognizer.cancelsTouchesInView = false`
  `addGestureRecognizer(moveRecognizer)`
`}`

最后，在 `DrawView.swift` 中，实现委托方法并返回 `true`。

> **`func gestureRecognizer(_ gestureRecognizer: UIGestureRecognizer, shouldRecognizeSimultaneouslyWith otherGestureRecognizer: UIGestureRecognizer) -> Bool {`**
  **`return true`**
**`}`**

对于这种情况，只有您的平移手势识别器有一个委托，除了返回 `true` 则不需要做更多的事情。 在更复杂的情况下，您可以使用传入的手势识别器来更仔细地控制同时识别。

现在，当长按开始时，**UIPanGestureRecognizer** 将继续跟踪触摸，如果用户的手指开始移动，则识别器将识别平移。 要看到差异，运行应用程序，画一条线，选择它，然后平移。 控制台将报告两种手势。（**UIGestureRecognizerDelegate** 协议包括其他方法来帮助您调整手势识别器的行为。访问协议参考页面了解更多信息。）

除了您已经看到的状态之外，平移手势识别器支持 `changed` 状态。 当手指开始移动时，识别器进入 began 状态并调用其目标上的方法。 当手指围绕屏幕移动时，识别器转换到 changed 的状态，并重复地在其目标上调用动作方法。 当手指离开屏幕时，识别器的状态被设置为 ended，并且最终在目标上调用该方法。

下一步是实现平移识别器在其目标上调用的 **moveLine(_ :)** 方法。 在这个实现中，您将在平移识别器上调用 **translationInView(_ :)** 方法。 该 **UIPanGestureRecognizer** 方法返回在作为参数传递的视图的坐标系中，平移点作为 **CGPoint** 移动的距离。 当平移手势开始时，该属性设置为零点（其中 `x` 和 `y` 为0）。 随着不断的平移，这个值被更新——如果向右平移，它具有高 x 值; 如果平移返回到它开始的位置，它的 translation 将回到零点。

在 `DrawView.swift` 中，实现 **moveLine(_ :)**。 请注意，因为您将从 **UIPanGestureRecognizer** 类发送手势识别器方法，所以该方法的参数必须是 **UIPanGestureRecognizer** 的实例的引用，而不是 **UIGestureRecognizer** 。

> `func moveLine(_ gestureRecognizer: UIPanGestureRecognizer) {`
  `print("Recognized a pan")`
>
  **`// If a line is selected...`**
  **`if let index = selectedLineIndex {`**
    **`// When the pan recognizer changes its position...`**
    **`if gestureRecognizer.state == .changed {`**
      **`// How far has the pan moved?`**
      **`let translation = gestureRecognizer.translation(in: self)`**
>
      **`// Add the translation to the current beginning and end points of the line`**
      **`// Make sure there are no copy and paste typos!`**
      **`finishedLines[index].begin.x += translation.x`**
      **`finishedLines[index].begin.y += translation.y`**
      **`finishedLines[index].end.x += translation.x`**
      **`finishedLines[index].end.y += translation.y`**
>
      **`// Redraw the screen`**
      **`setNeedsDisplay()`**
    **`}`**
  **`} else {`**
    **`// If no line is selected, do not do anything`**
    **`return`**
  **`}`**
`}`

构建并运行应用程序。 触摸并按住一条线并开始拖动——您会发现该线和您的手指不同步。 这到底是怎么回事？

您正在将该当前的 translation 重复添加到该线的原始终点。 当您最后一次调用此方法时，您实际上需要让手势识别器来报告 translation 中的更改。 幸运的是，你可以做到这一点。 每次报告更改时，您可以将平移手势识别器的 translation 设置回零点。 然后，下一次报告更改时，它将获得自上次发生以来的 translation。

在 `DrawView.swift` 中的 **moveLine(_ :)** 底部附近添加以下代码行。

> `finishedLines[index].end.x += translation.x`
`finishedLines[index].end.y += translation.y`
>
**`gestureRecognizer.setTranslation(CGPoint.zero, in: self)`**
>
`// Redraw the screen`
`setNeedsDisplay()`

构建并运行应用程序并移动一条线。 效果还不错

## 关于 UIGestureRecognizer 的更多信息##

你只是用到了 **UIGestureRecognizer** 的一些基础知识。 它还有更多的子类，更多的属性和更多的委托方法——你甚至可以创建自己的识别器。 本节将为讲解关于 **UIGestureRecognizer** 其它的信息。 您可以查看文档以了解更多信息。

当手势识别器在视图中时，它确实会为您处理所有的 **UIResponder** 方法，如 **touchesBegan(_：with :)**。 手势识别器是非常贪心的，所以他们通常不会让视图接触到触摸事件，或者他们至少延迟交付这些事件。 您可以在识别器上设置属性，如 `delayedTouchesBegan`，`delaysTouchesEnded`，`cancelsTouchesInView` 以更改此行为。 如果您需要比这种全无变化的方法更好的控制，您可以为识别器实现委托方法。

有时，您可能会有两个手势识别器寻找非常相似的手势。 您可以将识别器链接在一起，以便一个失败后下一个才能开始使用 **require(toFail :)** 方法。 你在 **init?(coder :)** 中使用了这个方法，使得点击识别器等待双击识别器失败。

掌握手势识别器必须了解的一件事是他们如何解释他们的状态。 总体来说，识别器可以输入七个状态：

- `UIGestureRecognizerState.possible`
- `UIGestureRecognizerState.failed`
- `UIGestureRecognizerState.began`
- `UIGestureRecognizerState.cancelled`
- `UIGestureRecognizerState.changed`
- `UIGestureRecognizerState.recognized`
- `UIGestureRecognizerState.ended`

识别器花费大部分时间在 possible 状态。 当手势转换到 possible 状态或 failed 状态之外的任何状态时，识别器的动作消息被发送，并且可以查看其 `state` 属性以了解原因。

failed 状态用于识别器等待多点触控手势。 在某些时候，用户的手指可能会达到一个位置，从这个位置他们再也不能够识别识别器的手势。 那么手势识别器识别失败。 识别器在中断时进入 canceled 的状态，例如通过来电。

如果手势是连续的如平移，那手势识别器将进入 began 状态，然后进入 changed 状态，直到手势结束。 当手势结束（或 canceled 状态 ）时，识别器进入 ended（或 canceled）状态，并在返回到 possible 状态之前最后发送其动作消息。

对于识别离散手势（如点击）手势识别器，您只能看到 recognized 状态（与 ended 状态具有相同的值）。

您在本章中未实现的四个内置识别器是 **UIPinchGestureRecognizer**，**UISwipeGestureRecognizer**，**UIScreenEdgePanGestureRecognizer** 和 **UIRotationGestureRecognizer**。 每个都具有允许您微调其行为的属性。 文档将向您展示怎么做。

最后，如果您想要识别的手势不是由 **UIGestureRecognizer** 的内置子类实现的，那么您可以自己对 **UIGestureRecognizer** 进行子类化。 这不在本书的范围之内。 您可以阅读 **UIGestureRecognizer** 文档的 `Methods for Subclassing` 部分，了解需要的内容。

## 白银挑战：神秘线 ##

应用程序中有一个 bug。 如果您点击一条线，然后在菜单可见时开始绘制新的，您将拖动所选行并同时绘制新线。 修复这个bug。

## 黄金挑战：速度和大小 ##

当你在画一条线的时候，你可以用手势识别器来记录下平移的速度。根据这个速度调整线的厚度。不要只靠猜测 pan 识别器的速度值可以设为多大或能有多大。(换句话说，将各种速度记录到控制台。)

## 白金挑战：颜色 ##

用三指向上滑动可以弹出一个显示颜色的面板。 选择这些颜色之一应该使您以后绘制的任何线条以该颜色显示。 通过放置该面板不应该画出额外的线，或者当应用程序意识到处理三指滑动时，应立即删除任何绘制的线。

## 更多：UIMenuController 和 UIResponderStandardEditActions ##

**UIMenuController** 通常负责在显示时向用户展示一个“编辑”菜单。 （当您按住文本字段或文本视图时。）因此，未修改的菜单控制器（您未设置菜单项的控件）已经具有其所呈现的默认菜单项，如 剪切(`Cut`)，复制(`Copy`) 和其他熟悉的选项。 每项都有一个相关联的动作消息。 例如，当 `Cut` 菜单项被点击时，**cut:** 被发送到呈现菜单控制器的视图。

**UIResponder** 的所有实例都会实现这些方法，但是默认情况下，这些方法不会执行任何操作。 像 **UITextField** 这样的子类会覆盖这些方法，以便对其上下文进行适当的操作，例如剪切当前选择的文本。 这些方法都在 **UIResponderStandardEditActions** 协议中声明。

如果您在视图中覆盖了 **UIResponderStandardEditActions** 的方法，则其菜单项将自动显示在您为该视图显示的任何菜单中。 这是因为菜单控制器在其视图上调用 **canPerformAction(_：withSender :)** 方法，它根据视图是否实现此方法返回 `true` 或 `false`。

如果要实现这些方法之一，但不希望它出现在菜单中，可以覆盖 **canPerformAction(_：withSender :)** 返回 `false`：

> `override func canPerformAction(_ action: Selector, withSender sender: Any?) -> Bool {`
> 
  `if action == #selector(copy(_:)) {`
    `return false`
  `} else {`
    `// Else return the default behavior`
    `return super.canPerformAction(action, withSender: sender)`
  `}`
`}`