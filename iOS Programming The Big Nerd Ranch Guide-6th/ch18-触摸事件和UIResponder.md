在接下来的两章中，您将创建 `TouchTracker`，该应用程序中用户可以通过触摸屏幕来画画。 在本章中，您将创建一个视图来使用户能通过拖动来画线（图18.1）。 使用多点触控的话，用户一次可以绘制多条线。

**图18.1 TouchTracker**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure18.1.png)

## 触摸事件 ##

作为 **UIResponder** 的子类，**UIView** 可以覆盖四种方法来处理四个不同的触摸事件：

- 一个或多个手指触摸屏幕
**`func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?)`**

- 一个或多个手指在屏幕上移动（该信息随着手指的移动而重复发送）
**`func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?)`**

- 一个或多个手指从屏幕上移除
**`func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?)`**

- 系统事件（如来电）在触摸结束之前中断它
**`func touchesCancelled(_ touches: Set<UITouch>, with event: UIEvent?)`**

让我们看一下典型的触摸生命周期。 当用户的手指触摸屏幕时，会创建一个 **UITouch** 的实例。 在手指触摸的 **UIView** 上调用 **touchesBegan(_：with :)** 方法，并通过一个 *touches* **Set** 传入 **UITouch**。

当手指围绕屏幕移动时，触摸对象被更新以包含手指在屏幕上的当前位置。 然后，最初触摸的 **UIView** 将被发送消息 **touchesMoved(_：with :)**。 作为参数传递给此方法的 **Set** 中包含的 **UITouch** 就是之前手指触摸屏幕时创建的。

当手指从屏幕上移除时，最后一次更新触摸对象以包含手指的最终位置，并且最初触摸的视图被发送消息 **touchesEnded(_：with :)**。 该方法完成执行后，**UITouch** 对象将被销毁。

从这些信息，您可以得出关于触摸对象如何工作的一些结论：

- 一个 **UITouch** 对应于屏幕上的一根手指。 只要手指在屏幕上，该触摸对象就可以生存，并且始终包含手指在屏幕上的当前位置。
- 手指最初触摸的视图将接收该手指的每个触摸事件消息。 即使手指移动超出最初触摸的 **UIView** 的边界，在该视图上仍然会调用 **touchesMoved(_：with :)** 和 **touchesEnded(_：with :)** 方法。 因此，如果触摸从一个视图开始，则该视图拥有触摸的生命周期。
- 您不必——也不应该——永远不要——引用 **UITouch** 对象。 该应用程序将允许您通过触摸生命周期中不同点调用的 **UIResponder** 方法访问触摸对象。

每次触摸都会像开始，移动或结束一样——触摸事件(`touch event`) 被添加到 **UIApplication** 对象管理的事件的队列中。 在实践中，队列很少会填满，而且事件会立即发送。 这些触摸事件的传递包括将一个 **UIResponder** 消息发送到拥有触摸的视图。

多点触摸呢？ 如果多个手指在同一视图的同一时间完成相同的事情，则所有这些触摸事件都将一次发送。 每个触摸对象——每个手指一个——包含在作为 **UIResponder** 消息中的参数传递的 **Set** 中。 然而，同一时间内触摸的位置都不相同。 因此，通常不是一个响应者消息对应所有触摸，而是多个 响应者消息对应一个或多个触摸。 本章稍后将介绍如何处理多点触摸。

## 创建 TouchTracker 应用程序 ##

现在让我们开始你的应用程序。 在 `Xcode` 中，创建一个新的 single view universal project，并将其命名为 `TouchTracker` （图18.2）。

**图18.2 创建TouchTracker**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure18.2.png)

在构建 `TouchTracker` 时，您将使用模板创建的默认视图控制器和 storyboard文件。 对于其视图和模型图层，您将要创建自定义视图类和自定义结构体。 `TouchTracker` 的主要部分如图18.3所示。

**图18.3 TouchTracker的对象图**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure18.3.png)

我们从你的自定义结构体开始。

### 创建 Line 结构体 ###

您将创建自定义 **Line** 类型。 到目前为止，您创建的所有类型都是类。 其实他们一直是 Cocoa Touch 子类; 例如，您已经创建过 **NSObject**，**UIViewController** 和 **UIView** 的子类。

**Line** 将是一个 *结构体(struct)*。 您在本书中使用了结构体-- **CGRect**，**CGSize** 和 **CGPoint** 都是结构体。 **String**，**Int**，**Array** 和 **Dictionary** 也是如此。 现在你要创建一个你自定义的结构体。

创建一个名为 `Line` 的新的 Swift 文件。

在 `Line.swift` 中，导入 `CoreGraphics` 并声明 **Line** 结构体。 声明两个 **CGPoint** 属性，用于确定该线的起点和终点。

> `import Foundation`
**`import CoreGraphics`**
>
**`struct Line {`**
&emsp;&emsp;**`var begin = CGPoint.zero`**
&emsp;&emsp;**`var end = CGPoint.zero`**
`}`

#### 结构体 ####

结构体与类有很多不同：

- 结构体不支持继承。
- 如果没有声明其他构造器，Struct 将获得 成员构造器(`member-wise initializer`)。 成员构造器接受类型中每个属性的参数。 例如，**Line** struct 具有成员构造器 **init(begin：CGPoint，end：CGPoint）**。
- 如果所有属性都具有默认值，并且没有声明其他初始值设置，那么结构体也将获得一个空的构造器（**init()**），它创建一个实例并将所有属性设置为其默认值。
- 也许最重要的是，结构体（和枚举）是 值类型(`value type`) —— 而不是类(它们是引用类型(`reference type`)) 。

#### 值类型与引用类型 ####

值类型是将值分配给另一个实例或传递给函数的参数时将其值复制的类型。 这意味着将值类型的实例分配给另一个实例会将第一个实例的副本分配给第二个实例。 值类型在 Swift 中起着重要作用。 例如，数组和字典都是值类型。 你写的所有枚举和结构体也是值类型。

当引用类型被分配给一个实例或传递给一个函数的参数时，它们不被复制。 而是传递对同一个实例的引用。 类和闭包是引用类型。

那么你该用哪个呢？ 一般来说，我们建议您使用值类型（如结构体），除非您完全知道您使用引用类型会更有益。 值类型更容易理解，因为您不需要担心在更改副本上的值时实例会发生什么。 如果您想对此主题进行更深入的讨论，请查看 `Swift Programming：The Big Nerd Ranch Guide`。

### 创建 DrawView ###

除了自定义结构体之外，`TouchTracker` 还需要自定义视图。

创建一个名为 `DrawView` 的新 Swift 文件。 在 `DrawView.swift` 中，定义 **DrawView** 类。 添加两个属性：一个可选的 **Line** 来跟踪可能被绘制的线，以及一个 **Line** 数组来跟踪已经绘制的线。

> ~~`import Foundation`~~
**`import UIKit`**
> 
**`class DrawView: UIView {`**
>
&emsp;&emsp;**`var currentLine: Line?`**
&emsp;&emsp;**`var finishedLines = [Line]()`**
>
`}`

**DrawView** 的一个实例将是应用程序的 `rootViewController` 的视图，项目中已经存在一个 **ViewController**。 视图控制器需要知道它的视图将是 **DrawView** 的一个实例。

打开 `Main.storyboard`。 选择 `View` 并打开身份检查器（`Command-Option-3`）。 在 `Custom Class` 下，将 `Class` 更改为 `DrawView`（图18.4）。

**图18.4 更改视图类**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure18.4.png)

#### 使用 DrawView绘制

**DrawView** 的实例需要能够绘制线。 您将编写一种使用 **UIBezierPath** 根据给定 **Line** 的属性创建和描绘路径的方法。 然后，您将覆盖 **draw(_ :)** 来绘制完成线数组中的线以及当前的线（如果有）。

在 `DrawView.swift` 中，实现用于画线的方法，并覆盖 **draw(_ :)**。

> `var currentLine: Line?`
`var finishedLines = [Line]()`
>
**`func stroke(_ line: Line) {`**
&emsp;&emsp;**`let path = UIBezierPath()`**
&emsp;&emsp;**`path.lineWidth = 10`**
&emsp;&emsp;**`path.lineCapStyle = .round`**<br>
&emsp;&emsp;**`path.move(to: line.begin)`**
&emsp;&emsp;**`path.addLine(to: line.end)`**
&emsp;&emsp;**`path.stroke()`**
**`} `**
>
**`override func draw(_ rect: CGRect) {`**
&emsp;&emsp;**`// Draw finished lines in black`**
&emsp;&emsp;**`UIColor.black.setStroke()`**
&emsp;&emsp;**`for line in finishedLines {`**
&emsp;&emsp;&emsp;&emsp;**`stroke(line)`**
&emsp;&emsp;**`} `**
>
&emsp;&emsp;**`if let line = currentLine {`** 
&emsp;&emsp;&emsp;&emsp;**`// If there is a line currently being drawn, do it in red`** 
&emsp;&emsp;&emsp;&emsp;**`UIColor.red.setStroke()`**
&emsp;&emsp;&emsp;&emsp;**`stroke(line)`**
&emsp;&emsp;**`}`**
**`}`**


## 触屏画线 ##

一条线由两点定义。 您的 **Line** 将这些点存储为名为 `begin` 和 `end` 的属性。 触摸开始时，您将创建一个 **Line**，并将其属性设置为触摸开始点。 触摸移动时，您将更新 **Line** 的 `end`。 当触摸结束时，您将拥有完整的一条线。

在 `DrawView.swift` 中，实现 **touchesBegan(_：with :)** 创建一条新的线。

> **`override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {`** 
&emsp;&emsp;**`let touch = touches.first!`**
> 
&emsp;&emsp;**`// Get location of the touch in view's coordinate system`**
&emsp;&emsp;**`let location = touch.location(in: self)`**
>
&emsp;&emsp;**`currentLine = Line(begin: location, end: location)`**
>
&emsp;&emsp;**`setNeedsDisplay()`**
**`}`**

该代码首先在视图的坐标系统中找出触摸的位置。 然后它调用 **setNeedsDisplay()** 方法，该方法标记在运行循环结束时重绘的视图。

接下来，还可以在 `DrawView.swift` 中实现 **touchesMoved(_：with :)**，以便更新 `currentLine` 的 `end` 属性。

> **`override func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?) {`**
&emsp;&emsp;**`let touch = touches.first!`**
&emsp;&emsp;**`let location = touch.location(in: self)`**
>
&emsp;&emsp;**`currentLine?.end = location`**
>
&emsp;&emsp;**`setNeedsDisplay()`**
**`}`**

最后，仍然在 `DrawView.swift` 中，更新 `currentLine` 的结束位置，并在触摸结束时将其添加到 `finishedLines` 数组。

> **`override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {`**
&emsp;&emsp;**`if var line = currentLine {`**
&emsp;&emsp;&emsp;&emsp;**`let touch = touches.first!`**
&emsp;&emsp;&emsp;&emsp;**`let location = touch.location(in: self)`**
&emsp;&emsp;&emsp;&emsp;**`line.end = location`**
>
&emsp;&emsp;&emsp;&emsp;**`finishedLines.append(line)`**
&emsp;&emsp;**`}`**
&emsp;&emsp;**`currentLine = nil`**
>	
&emsp;&emsp;**`setNeedsDisplay()`**
**`}`**

构建并运行应用程序并在屏幕上绘制一些线。 在绘制时，线将以红色显示。 一旦完成，它们将以黑色显示。

### 处理多点触控 ###

绘制线时，您可能已经注意到，屏幕上有多个手指画线并没有任何作用——也就是说，您一次只能画一条线。 让我们更新 **DrawView**，以便您可以用多个手指来绘制尽可能多的线条。

默认情况下，视图一次只能接受一个触摸。 如果一个手指已经触发了 **touchesBegan(_：with :)** 但尚未完成， 也就是没有触发 **touchesEnded(_：with :)**， 所有后续的触摸被忽略。 在这种情况下，“忽略” 意味着在 **DrawView** 上将不会调用 **touchesBegan(_：with :)** 和任何其他 **UIResponder** 方法与额外的触摸相关的方法。

在 `Main.storyboard` 中，选择 `Draw View` 并打开属性检查器。 选中标有 `Multiple Touch` 的框（图18.5），以使 **DrawView** 实例的 `multiTouchesEnabled` 属性设置为 `true`。

**图18.5 多点触控启用**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure18.5.png)

现在，**DrawView** 将接受多点触控，每次手指触摸屏幕，移动或从屏幕上移除时，相应的 **UIResponder** 将在视图中被调用。 但是，您现在有一个问题：您的 **UIResponder** 代码原本假定一次只会触发一个触摸和绘制一条线。

例如，每个触摸处理方法要求它接收的一组 `touches` 集合中的 `first` 元素。 在单触摸视图中，集合中只会有一个对象，所以要请求何对象都总是返回一个触发事件的对象。 在多点触摸视图中，该集可以包含多个触摸。 此外，**DrawView** 只有一个属性(`currentLine`)关联到正在进行的线。 显然，您将需要保留当前屏幕上触摸的线的数量。 虽然您可以创建更多的属性，如 `currentLine1` 和 `currentLine2`，但是管理哪个属性对应于哪个触摸将是一件麻烦事。

您只需要用包含一个字典的实例的 **Line** 来替换单个 **Line** 而不用添加更多的属性。 在 `DrawView.swift` 中，添加一个新属性来替换 `currentLine`。

> `class DrawView: UIView {`<br>
&emsp;&emsp;~~`var currentLine: Line?`~~
&emsp;&emsp;**`var currentLines = [NSValue:Line]()`**

该线存储在字典中的 key 将来自该线对应的 **UITouch** 对象。 随着更多的触摸事件发生，您可以使用相同的算法从触发事件的 **UITouch** 导出 key，并使用它在字典中查找适当的 **Line**。

现在，您需要更新 **UIResponder** 方法来添加当前正在绘制到此字典的行。 在 `DrawView.swift` 中，更新 **touchesBegan(_：with :)** 中的代码。

> `override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {`
&emsp;&emsp;~~`let touch = touches.first!`~~
>
&emsp;&emsp;~~`// Get location of the touch in view's coordinate system`**~~
&emsp;&emsp;~~`let location = touch.location(in: self)`~~
>
&emsp;&emsp;~~`currentLine = Line(begin: location, end: location)`~~
>
&emsp;&emsp;**`// Log statement to see the order of events`**
&emsp;&emsp;**`print(#function)`**
>
&emsp;&emsp;**`for touch in touches {`**
&emsp;&emsp;&emsp;&emsp;**`let location = touch.location(in: self)`**
>	
&emsp;&emsp;&emsp;&emsp;**`let newLine = Line(begin: location, end: location)`**
>
&emsp;&emsp;&emsp;&emsp;**`let key = NSValue(nonretainedObject: touch)`**
&emsp;&emsp;&emsp;&emsp;**`currentLines[key] = newLine`**
&emsp;&emsp;**`} `**
>
&emsp;&emsp;`setNeedsDisplay()`
`}`

在此代码中，您首先使用 `#function` 表达式打印出方法的名称。 其次，您列举了所有触摸的开始，因为可以同时开始多个触摸。 （通常，触摸在不同的时间开始，并且触摸在每个触摸的 **DrawView** 上多次调用 **Bone(_：with :)**。但你必须做好准备，即使它不太可能发生）

接下来，请注意使用 **NSValue(nonretainedObject :)** 来导出存储 **Line** 的 key。 此方法创建一个 **NSValue** 实例，该实例将持有与该线关联的 **UITouch** 对象的地址。 因为 **UITouch** 在触摸开始时被创建，在其整个生命周期内被更新，并且在触摸结束时被破坏，所以通过每个触摸事件处理方法该对象的地址将是不变的。 图18.6显示了新的事态。

**图18.6 多点触控的 TouchTracker 的对象图**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure18.6.png)

你可能会想：为什么不使用 **UITouch** 本身作为 key？ 为什么要通过创建 **NSValue** 来导出？ **UITouch** 的文档说，你不应该对 **UITouch** 对象有很强的引用。 内存管理的细节超出了本书的范围，但是为了避免创建对触控对象的强大引用，您可以使用其 **init(nonretainedObject :)** 构造器将 **UITouch** 的内存地址包装在 **NSValue** 的实例中。 该方法的文档说明：“如果要将一个对象添加到集合中但是不希望集合创建一个强大的引用，这个方法很有用，” 这正是你想要的。 因为相同的 **UITouch** 对象被重复用于整个触控的生命周期（因此具有相同的内存地址），您可以使用相同的 **UITouch** 重新创建相同的 **NSValue**。

接下来，在 `DrawView.swift` 中更新 **touchesMoved(_：with :)**，以便它可以查找正确的线。

> `override func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?) {`
&emsp;&emsp;~~`let touch = touches.first!`~~
&emsp;&emsp;~~`let location = touch.location(in: self)`~~
>
&emsp;&emsp;~~`currentLine?.end = location`~~
>
&emsp;&emsp;**`// Log statement to see the order of events`**
&emsp;&emsp;**`print(#function)`**
&emsp;&emsp;**`for touch in touches {`**
&emsp;&emsp;&emsp;&emsp;**`let key = NSValue(nonretainedObject: touch)`**
&emsp;&emsp;&emsp;&emsp;**`currentLines[key]?.end = touch.location(in: self)`**
&emsp;&emsp;**`}`** <br>
&emsp;&emsp;`setNeedsDisplay()`
`}`

现在，更新 **touchesEnded(_：with :)** 将任何完成的线添加到 `finishedLines` 数组。

> `override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {`
&emsp;&emsp;~~`if var line = currentLine {`~~
&emsp;&emsp;&emsp;&emsp;~~`let touch = touches.first!`~~
&emsp;&emsp;&emsp;&emsp;~~`let location = touch.location(in: self)`~~
&emsp;&emsp;&emsp;&emsp;~~`line.end = location`~~
>
&emsp;&emsp;&emsp;&emsp;~~`finishedLines.append(line)`~~
&emsp;&emsp;~~`}`~~
&emsp;&emsp;~~`currentLine = nil`~~
>
&emsp;&emsp;**`// Log statement to see the order of events`**
&emsp;&emsp;**`print(#function)`**
>
&emsp;&emsp;**`for touch in touches {`**
&emsp;&emsp;&emsp;&emsp;**`let key = NSValue(nonretainedObject: touch)`**
&emsp;&emsp;&emsp;&emsp;**`if var line = currentLines[key] {`**
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;**`line.end = touch.location(in: self)`**
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;**`finishedLines.append(line)`**
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;**`currentLines.removeValue(forKey: key)`**
&emsp;&emsp;&emsp;&emsp;**`}`**
&emsp;&emsp;**`} `**
>
&emsp;&emsp;`setNeedsDisplay()`
`}`

最后，更新 **draw(_ :)** 来绘制 `currentLines` 中的每一条线。

> `override func draw(_ rect: CGRect) {`
&emsp;&emsp;`// Draw finished lines in black`
&emsp;&emsp;`UIColor.black.setStroke()` 
&emsp;&emsp;`for line in finishedLines {`
&emsp;&emsp;&emsp;&emsp;`stroke(line)`
&emsp;&emsp;`} `<br>	
&emsp;&emsp;~~`if let line = currentLine {`~~
&emsp;&emsp;&emsp;&emsp;~~`If there is a line currently being drawn, do it in red`~~
&emsp;&emsp;&emsp;&emsp;~~`UIColor.red.setStroke()`~~
&emsp;&emsp;&emsp;&emsp;~~`stroke(line)`~~
&emsp;&emsp;~~`}`~~
>
&emsp;&emsp;**`// Draw current lines in red`**
&emsp;&emsp;**`UIColor.red.setStroke()`**
&emsp;&emsp;**`for (_, line) in currentLines {`**
&emsp;&emsp;&emsp;&emsp;**`stroke(line)`**
&emsp;&emsp;**`}`**
`}`

构建并运行应用程序，并用多个手指开始绘制线。 （在模拟器上，按住 Option(alt) 键的同时拖动以模拟多根手指。）请注意控制台中日志消息的顺序。

您应该知道，当在视图上调用诸如 **touchesMoved(_：with :)** 之类的 **UIResponder** 方法时，只有已移动的触摸将位于 *touches* 中。 因此，三个触摸可以在一个视图上，但是在该组触摸中只有一个触摸被传递到这些方法之一中。 另外，一旦 **UITouch** 从视图开始，即使该触摸从其开始的视图移开，所有触摸事件方法都会在触摸的生命周期中同一视图中被调用。

`TouchTracker` 的基础知识的最后一件事是处理取消触摸时会发生什么。 当应用程序被操作系统中断时（例如，当电话进入时），当触摸当前在屏幕上时，可以取消触摸。 当触摸被取消时，其设置的任何状态都应该被恢复。 在这种情况下，您应该删除正在绘制的任何线。

在 `DrawView.swift` 中，实现 **touchesCancelled(_：with :)**。

> **`override func touchesCancelled(_ touches: Set<UITouch>, with event: UIEvent?) {`**
&emsp;&emsp;**`// Log statement to see the order of events`**
&emsp;&emsp;**`print(#function)`**
>
&emsp;&emsp;**`currentLines.removeAll()`**
>
&emsp;&emsp;**`setNeedsDisplay()`**
**`}`**


## @IBInspectable ##

在 `Interface Builder` 中工作时，您可以修改添加到画布的视图的属性。 例如，您可以在视图上设置背景颜色，标签上的文本以及滑块上的当前进度。 您可以将相同的行为添加到您自己的某些类型的 **UIView** 子类。 我们可以通过 `Interface Builder` 增加 **DrawView** 当前线条颜色，最终线条颜色和线条厚度的功能。

在 `DrawView.swift` 中，声明三个属性来引用这些值。 给它们默认值，并且只要这些属性改变，都有自己的 view 标志来重新绘制。

> `var currentLines = [NSValue:Line]()`
`var finishedLines = [Line]()`
>
**`@IBInspectable var finishedLineColor: UIColor = UIColor.black {`**
&emsp;&emsp;**`didSet {`**
&emsp;&emsp;&emsp;&emsp;**`setNeedsDisplay()`**
&emsp;&emsp;**`}`**
**`} `**
>
**`@IBInspectable var currentLineColor: UIColor = UIColor.red {`**
&emsp;&emsp;**`didSet {`**
&emsp;&emsp;&emsp;&emsp;**`setNeedsDisplay()`**
&emsp;&emsp;**`}`**
**`} `**
>
**`@IBInspectable var lineThickness: CGFloat = 10 {`**
&emsp;&emsp;**`didSet {`**
&emsp;&emsp;&emsp;&emsp;**`setNeedsDisplay()`**
&emsp;&emsp;**`}`**
**`}`**

`@IBInspectable` 关键字让 `Interface Builder` 知道这是您要通过属性检查器进行自定义的属性。 许多常见类型由 `@IBInspectable` 支持：布尔值，字符串，数字，**CGPoint**，**CGSize**，**CGRect**，**UIColor**，**UIImage** 等等。

现在更新 **stroke(_ :)** 和 **drawView(_ :)** 来使用这些新属性。

> `func stroke(_ line: Line) {`
&emsp;&emsp;`let path = UIBezierPath()`
&emsp;&emsp;~~`path.lineWidth = 10`~~
&emsp;&emsp;`path.lineWidth = lineThickness`
&emsp;&emsp;`path.lineCapStyle = .round`
>
&emsp;&emsp;`path.move(to: line.begin)`
&emsp;&emsp;`path.addLine(to: line.end)`
&emsp;&emsp;`path.stroke()`
`} `
> 
`override func draw(_ rect: CGRect) {`
&emsp;&emsp;~~`// Draw finished lines in black`~~
&emsp;&emsp;~~`UIColor.black.setStroke()`~~
&emsp;&emsp;`finishedLineColor.setStroke()`
&emsp;&emsp;`for line in finishedLines {`
&emsp;&emsp;&emsp;&emsp;`stroke(line)`
&emsp;&emsp;`} `
>
&emsp;&emsp;~~`// Draw current lines in red`~~
&emsp;&emsp;~~`UIColor.red.setStroke()`~~
&emsp;&emsp;`currentLineColor.setStroke()`
&emsp;&emsp;`for (_, line) in currentLines {`
&emsp;&emsp;&emsp;&emsp;`stroke(line)`
&emsp;&emsp;`}`
`}`

当您在 `Interface Builder` 中将 **DrawView** 添加到画布中时，可以在属性检查器中自定义这三个属性来改变实例（图18.7）。

**图18.7 自定义DrawView**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure18.7.png)

## 白银挑战：颜色 ##

一旦线被添加到 `currentLines`，按画线的角度来决定它的颜色。

## 黄金挑战：圆圈 ##

用两根手指画圆。 尝试让每个手指代表围绕圆的边框的一个角。 回想一下，您可以通过按住 Option 键在模拟器上模拟两根手指。 （提示：如果您在单独的字典中跟踪在一个圆圈上工作的触摸，这会更容易）

## 更多：响应者链 ##

在第14章中，我们简要介绍了第一响应者。 **UIResponder** 可以是第一响应者并且接收触摸事件。 **UIView** 是 **UIResponder** 子类的一个例子，但还有很多其他的，包括 **UIViewController**，**UIApplication** 和 **UIWindow**。 你可能在想，“你不能触摸 **UIViewController**。 它不是一个屏幕上的对象。” 你是对的——你不能直接向 **UIViewController** 发送触摸事件，但视图控制器可以通过 响应者链(`responder chain`) 接收事件。

每个 **UIResponder** 可以通过其 `next` 属性来引用另一个 **UIResponder**，并且这些对象组成响应者链（图18.8）。 触摸事件从被触动的视图开始。 视图的 `next` 响应者通常是它的 **UIViewController**（如果它有）或它的父视图（如果没有）。 视图控制器的 `next` 响应者通常是其视图的父视图。 最顶级的父视图是窗口。 窗口的 `next` 响应者是 **UIApplication** 的单例实例。

**图18.8 响应者链**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure18.8.png)

**UIResponder** 如何处理一个事件？ 它在 `next` 响应者上调用相同的方法。 那就像 **touchesBegan(_：with :)** 这样的方法的默认实现。 所以如果一个方法不被覆盖，其 `next` 响应者将尝试处理触摸事件。 如果应用程序（响应者链中的最后一个对象）不处理该事件，则它将被丢弃。

您也可以在 `next` 响应者上显式调用方法。 假如在一个视图中，发生双击事件，它的 `next` 响应者应该处理它。 代码将会如下所示：

> `override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {`
&emsp;&emsp;`let touch = touches.first!`
&emsp;&emsp;`if touch.tapCount == 2 {`
&emsp;&emsp;&emsp;&emsp;`next?.touchesBegan(touches, with: event)`
&emsp;&emsp;`} else {`
&emsp;&emsp;&emsp;&emsp;`// Go on to handle touches that are not double-taps`
&emsp;&emsp;`}` 
`}`

## 更多：UIControl ##

**UIControl** 类是 Cocoa Touch 中很多类的父类，包括 **UIButton** 和 **UISlider**。 您已经了解了如何设置这些控件的 目标 和 动作。 现在我们可以仔细看看 **UIControl** 如何覆盖在本章中实现的相同的 **UIResponder** 方法。

在 **UIControl** 中，每个可能的 *控制事件(control event)* 与一个常数相关联。 例如，按钮通常会在 `UIControlEvents.touchUpInside` 控件事件上发送动作消息。 注册该控制事件的目标只有在用户触摸控制然后将手指从控件边框内的屏幕提起时才会收到其动作消息。

但是，对于一个按钮，您还可以对其他事件类型执行动作。 例如，如果用户在边框内部或外部移除手指，也可以触发方法。 以编程方式分配目标和动作将如下所示：

> `button.addTarget(self, action: #selector(Thermostat.resetTemperature(_:)), for: [.touchUpInside, .touchUpOutside])`

现在考虑 **UIControl** 如何处理 `UIControlEvents.touchUpInside`。

> `// Not the exact code. There is a bit more going on!`
`override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {`
>	
&emsp;&emsp;`// Reference to the touch that is ending`
&emsp;&emsp;`let touch = touches.first!`
>
&emsp;&emsp;`// Location of that point in this control's coordinate system`
&emsp;&emsp;`let touchLocation = touch.location(in: self)`
>
&emsp;&emsp;`// Is that point still in my viewing bounds?`
&emsp;&emsp;`if bounds.contains(touchLocation) {`
&emsp;&emsp;&emsp;&emsp;`// Send out action messages to all targets registered for this event!`
&emsp;&emsp;&emsp;&emsp;`sendActions(for: .touchUpInside)`
&emsp;&emsp;`}`
&emsp;&emsp;`else {`
&emsp;&emsp;&emsp;&emsp;`// The touch ended outside the bounds: different control event`
&emsp;&emsp;&emsp;&emsp;`sendActions(for: .touchUpOutside)`
&emsp;&emsp;`}`
`}`

那么这些动作如何发送到正确的目标呢？ 在 **UIResponder** 方法实现的末尾，控件调用了 **sendActions(for :)** 方法。 该方法查看控件具有的所有目标动作对。 如果其中任何一个注册为作为参数传递的控制事件，则对这些目标调用相应的动作方法。

然而，控件不直接在其目标上调用方法。 而是通过 **UIApplication** 对象来引导这些方法调用。 为什么控件不直接在目标上调用动作方法呢？ 控件也可以具有 空目标(nil-targeted) 的动作。 如果 **UIControl** 的目标为 `nil`，则 **UIApplication** 会找到其 **UIWindow** 的 第一响应者，并调用该方法。