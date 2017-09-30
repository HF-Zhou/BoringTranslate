在本章中，您将更新 `WorldTrotter` 以编程方式创建 **MapViewController** 的视图（图6.1）。 在这样做时，您将更多地了解视图控制器以及如何以编程方式设置约束和控件（如 **UIButtons**）。

**图6.1 具有编程式视图的 WorldTrotter**

![](http://upload-images.jianshu.io/upload_images/1230738-28171af06df59de4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

目前，**MapViewController** 的 视图 在故事板中已经定义过了。 那么第一步就是从故事板中删除这个视图，这样你就可以编程地创建它了。

在 `Main.storyboard` 中，选择与 `Map View Controller` 相关联的地图视图，然后按 Delete 键（图6.2）。

**图6.2 删除视图**

![](http://upload-images.jianshu.io/upload_images/1230738-611f502af21662f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 以编程方式创建视图 ##

您在第 5 章中了解到，您可以通过覆盖 **UIViewController** 的 **loadView()** 方法以编程方式创建视图控制器的视图。

打开 `MapViewController.swift` 并覆盖 **loadView()** 来创建一个 **MKMapView** 实例，并将其设置为视图控制器的 视图。 您稍后将需要地图视图的引用，因此也为此创建一个属性。

> `import UIKit`
**`import MapKit`**
>
**`class MapViewController: UIViewController {`**
>
  **`var mapView: MKMapView!`**
>	
  **`override func loadView() {`**
    **`// Create a map view`**
    **`mapView = MKMapView()`**
>
    **`// Set it as *the* view of this view controller`**
    **`view = mapView`**
  **`} `**
>
  `override func viewDidLoad() {`
    `super.viewDidLoad()`
>
    `print("MapViewController loaded its view.")`
  `}`
`}`

创建视图控制器后，其 `view` 属性为 `nil`。 如果视图控制器请求其 `view`，并且其 `view` 为 `nil`，则会调用 **loadView()** 方法。

构建并运行应用程序。 虽然应用程序看起来没什么变化，但是地图视图是通过编程方式创建，而不是通过 `Interface Builder`。

## 编程式约束 ##

在第3章中，您了解了自动布局约束以及如何使用 `Interface Builder` 添加它们。 在本节中，您将学习如何以编程方式向界面添加约束。

Apple 建议您尽可能在 `Interface Builder` 中创建和约束您的视图。 但是，如果您的视图是在代码中创建的，那么您将需要以编程方式来约束它们。 **MapViewController** 的界面是以编程方式创建的，因此它是编程约束的绝佳候选。

要了解编程约束，您将向 **MapViewController** 的界面添加一个 **UISegmentedControl**。 分段控件(segmented control) 允许用户在一组离散选项之间进行选择，您将使用一个允许用户在地图类型之间切换：标准(standard)，混合(hybrid) 和卫星(satellite)。

在 `MapViewController.swift` 中，更新 **loadView()** 以将分段控件添加到界面。

> `override func loadView() {`
  `// Create a map view`
  `mapView = MKMapView()`
>
  `// Set it as *the* view of this view controller`
  `view = mapView`
>
  **`let segmentedControl = UISegmentedControl(items: ["Standard", "Hybrid", "Satellite"])`**
  **`segmentedControl.backgroundColor = UIColor.white.withAlphaComponent(0.5)`**
  **`segmentedControl.selectedSegmentIndex = 0`**
>
  **`segmentedControl.translatesAutoresizingMaskIntoConstraints = false`**
  **`view.addSubview(segmentedControl)`**
`}`

（请注意，由于页面大小限制，我们将显示其中的一些声明，分为两行，您应该在单行中输入每个声明。）

将这几行代码转化为约束需要与用于缩放界面的旧系统进行关联，即 `自动调整掩码(autoresizing mask)`。， 在引入自动布局之前，iOS应用程序使用 autoresizing mask 来允许在运行时对不同大小的屏幕进行缩放。

每个视图都有一个 autoresizing mask。 默认情况下，iOS创建与 autoresizing mask 匹配的约束，并将其添加到视图。 这些转化的约束通常与布局中的显式约束冲突，并导致不可满足的约束问题。 解决方案是通过将属性 `translateAutoresizingMaskIntoConstraints` 设置为 `false` 来关闭此默认转换。 （本章末尾将展示关于自动布局和 autoresizing mask 的更多信息）

### 锚点(anchor) ###

当您以编程方式使用自动布局时，您将使用 `anchor` 来创建约束。 anchor 是视图上的属性，对应于您可能想要约束在另一个视图上的 anchor 的属性。 例如，您可能会将一个视图的 leading archor 约束到另一个视图的 leading archor。 这将有两个视图的 leading 对齐的效果。
`leading：前部  anchor：锚点`

我们创建一些约束来执行以下操作。

- 分段控件的顶部(top)锚点应该等于其父视图的顶部锚点。
- 分段控制的前部(leading)锚点应等于其父视图的前部锚点。
- 分段控件的尾部(trailing)锚点应等于其父视图的尾部锚点。

在 `MapViewController.swift` 中，在 **loadView()** 中创建这些约束。

> `let segmentedControl = UISegmentedControl(items: ["Standard", "Hybrid", "Satellite"])`
`segmentedControl.backgroundColor = UIColor.white.withAlphaComponent(0.5)`
`segmentedControl.selectedSegmentIndex = 0`
>
`segmentedControl.translatesAutoresizingMaskIntoConstraints = false`
`view.addSubview(segmentedControl)`
>
**`let topConstraint = segmentedControl.topAnchor.constraint(equalTo: view.topAnchor)`**
**`let leadingConstraint = segmentedControl.leadingAnchor.constraint(equalTo: view.leadingAnchor)`**
**`let trailingConstraint = segmentedControl.trailingAnchor.constraint(equalTo: view.trailingAnchor)`**

`Xcode` 将提醒您输入的每一行的问题。 你会在稍后修复它们。

锚点有一个方法 **constraint(equalTo:)**，它将在两个锚点之间创建约束。 **NSLayoutAnchor** 上还有一些其他约束创建方法，其中包括一个接受常量作为参数的约束创建方法：

`func constraint(equalTo anchor: NSLayoutAnchor<AnchorType>,constant c: CGFloat) -> NSLayoutConstraint`

### 激活约束 ###

你现在有三个 **NSLayoutConstraint** 实例。 但是，在将其 `isActive` 属性设置为 `true` 来显式激活它们之前，这些约束将不会对布局产生任何影响。 这将解决 Xcode 的警告。

在 `MapViewController.swift` 中，在 **loadView()** 末尾添加激活约束代码。

> `let topConstraint = segmentedControl.topAnchor.constraint(equalTo: view.topAnchor)`
`let leadingConstraint = segmentedControl.leadingAnchor.constraint(equalTo: view.leadingAnchor)`
`let trailingConstraint = segmentedControl.trailingAnchor.constraint(equalTo: view.trailingAnchor)`
>
**`topConstraint.isActive = true`**
**`leadingConstraint.isActive = true`**
**`trailingConstraint.isActive = true`**

约束需要添加到与约束相关联的视图的最近的 `共同祖先(common ancestor)` 中。 图6.3显示了一个视图层次结构以及两个视图的共同祖先。

**图6.3共同的祖先**

![](http://upload-images.jianshu.io/upload_images/1230738-0f31973833323633.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果一个约束只与一个视图相关（例如在视图中添加宽度或高度约束时），则该视图被认为是共同的祖先。

通过将约束上的 `active` 属性设置为 `true`，约束将按照层级的方式工作，以找到用于添加约束的公共祖先。 然后它将在适当的视图上调用 **addConstraint(_ :)** 方法。 设置 `active` 属性优于自己调用 **addConstraint(_ :)** 或 **removeConstraint(_ :)**。

构建并运行应用程序并切换到 **MapViewController**。 分段控件现在被固定到其父视图的顶部，前部和尾部（图6.4）。

**图6.4分段控制添加到屏幕**

![](http://upload-images.jianshu.io/upload_images/1230738-6de7945dbf04eefc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

虽然约束没问题，但这界面并不好看。 分段控件处于状态栏下方，如果分段控件从屏幕的前部和尾部插入，则它将看起来更好。 我们先来处理状态栏问题。

## Layout guide ##

视图控制器拥有两个 layout guide，以改进布局内容：`topLayoutGuide` 和 `bottomLayoutGuide`。layout guide 指示视图控制器的视图内容将可见的程度。 使用 `topLayoutGuide` 将允许您的内容不会覆盖屏幕顶部的状态栏或导航栏。

layout guide 会显示三个可用于添加约束的锚点：`topAnchor`，`bottomAnchor` 和 `heightAnchor`。 因为您希望分段控件处于状态栏之下，所以您将约束顶部 layout guide 的 底部锚点 到分段控件的 顶部锚点。

在 `MapViewController.swift` 中，更新 **loadView()** 中的分段控件的约束。 使分段控件在顶部 layout guide下方8点。

> ~~`let topConstraint = segmentedControl.topAnchor.constraint(equalTo: view.topAnchor)`~~
**`let topConstraint = segmentedControl.topAnchor.constraint(equalTo: topLayoutGuide.bottomAnchor,constant: 8)`**
>
`let leadingConstraint = segmentedControl.leadingAnchor.constraint(equalTo: view.leadingAnchor)`
`let trailingConstraint = segmentedControl.trailingAnchor.constraint(equalTo: view.trailingAnchor)`
>
`topConstraint.isActive = true`
`leadingConstraint.isActive = true`
`trailingConstraint.isActive = true`

构建并运行应用程序。 分段控件现在显示在状态栏的下方。 通过使用 layout guide 而不是硬编码的常量，视图将根据它们显示的上下文进行调整。

现在让我们更新分段控件，使其从其父视图的 前部 和 尾部 插入。

## 边距(Margin) ##

虽然您可以使用约束上的常量来插入分段控件，但使用视图控制器视图的 `边距(margin)` 更好。

每个视图都有一个 `layoutMargins` 属性，表示在布置内容时要使用的默认间距。 此属性是 **UIEdgeInsets** 的一个实例，您可以将 **UIEdgeInsets** 视为边框的一种类型。 添加约束时，您将使用 `layoutMarginsGuide`，它暴露了绑定到 `layoutMargins` 边缘的锚点。

使用边距的主要优点是边距可以根据设备类型（iPad或iPhone）以及设备的大小而改变。 使用边距可以让您的内容在任何设备上看起来不错。

在 **loadView()** 中使用边距来更新分段控件的前部和尾部约束。

> `let topConstraint = segmentedControl.topAnchor.constraint(equalTo: topLayoutGuide.bottomAnchor,constant: 8)`
~~`let leadingConstraint = segmentedControl.leadingAnchor.constraint(equalTo: view.leadingAnchor)`~~
~~`let trailingConstraint = segmentedControl.trailingAnchor.constraint(equalTo: view.trailingAnchor)`~~
>
**`let margins = view.layoutMarginsGuide`**
**`let leadingConstraint = segmentedControl.leadingAnchor.constraint(equalTo: margins.leadingAnchor)`**
**`let trailingConstraint = segmentedControl.trailingAnchor.constraint(equalTo: margins.trailingAnchor)`**
>
`topConstraint.isActive = true`
`leadingConstraint.isActive = true`
`trailingConstraint.isActive = true`

再次构建并运行该应用程序。 分段控件现在将根据视图的边距插入（图6.5）。

**图6.5更新约束后的分段控制**

![](http://upload-images.jianshu.io/upload_images/1230738-a4d6014e9eb207cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 显式约束 ##

了解之前创建约束使用的方法是有帮助的。

**NSLayoutConstraint** 具有以下 构造器：

> `convenience init(item view1: Any, `
      `attribute attr1: NSLayoutAttribute,`
      `relatedBy relation: NSLayoutRelation,`
      `toItem view2: Any?,`
      `attribute attr2: NSLayoutAttribute,`
      `multiplier: CGFloat,`
      `constant c: CGFloat)`

该初始化方法使用两个视图对象的两个布局属性创建单个约束。 multiplier 是基于比例创建约束的关键。 constant 是固定数量的点，类似于您在间距约束中使用的点数。

布局属性定义为 **NSLayoutConstraint** 类中的常量：

- `NSLayoutAttribute.left`
- `NSLayoutAttribute.right`
- `NSLayoutAttribute.leading`
- `NSLayoutAttribute.trailing`
- `NSLayoutAttribute.top`
- `NSLayoutAttribute.bottom`
- `NSLayoutAttribute.width`
- `NSLayoutAttribute.height`
- `NSLayoutAttribute.centerX`
- `NSLayoutAttribute.centerY`
- `NSLayoutAttribute.firstBaseline`
- `NSLayoutAttribute.lastBaseline`

还有其他属性可以处理与视图相关联的边距，例如 `NSLayoutAttribute.leadingMargin`。

让我们考虑一个假设的约束。 假设您希望图像视图的宽度为其高度的 1.5 倍。 您可以使用以下代码进行此操作。 （不要在你的代码中键入这个假设的约束！它会与你已经拥有的其他约束冲突）

> `let aspectConstraint = NSLayoutConstraint(item: imageView,`
                 `attribute: .width,`
                 `relatedBy: .equal,`
                 `toItem: imageView,`
                 `attribute: .height,`
                 `multiplier: 1.5,`
                 `constant: 0.0)`

要了解该初始化方法的工作原理，请将此约束视为图6.6 所示的方程式。

**图6.6 NSLayoutConstraint 方程**

![](http://upload-images.jianshu.io/upload_images/1230738-27a79fc4174f7108.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您可以使用一个 multiplier 和一个 constant 来将一个视图的布局属性与另一个视图的布局属性相关联，以定义单个约束。

## 编程式控制器 ##

现在让我们更新分段控件，以便在用户点击某个细分时更改地图类型。

**UISegmentedControl** 是 **UIControl** 的子类。 你在第一章 **UIButton** 类中使用了另一个 **UIControl** 子类。 控制器负责响应某些事件调用其目标的方法。

控制事件的类型为 **UIControlEvents**。 以下是您将使用的一些常见控制器事件：

**`UIControlEvents.touchDown`**
触摸控制。

**`UIControlEvents.touchUpInside`**
触摸开始到结束仍然在控制器的范围内。

**`UIControlEvents.valueChanged`**
导致控件值改变的触摸。

**`UIControlEvents.editingChanged`**
导致 **UITextField** 的编辑更改的触摸。

您在第1章中使用了 **.touchUpInside** 的 **UIButton**（它是您在 `Interface Builder` 中拖动连接操作时的默认事件），您在第4章中看到 **.editingChanged** 事件。对于分段控件，您将使用 **.valueChanged** 事件。

在 `MapViewController.swift` 中，更新 **loadView()** 以将目标动作对添加到分段控件，并将其与 **.valueChanged** 事件相关联。

> `override func loadView() {`
  `// Create a map view`
  `mapView = MKMapView()`
>
  `// Set it as *the* view of this view controller`
  `view = mapView`
>
  `let segmentedControl = UISegmentedControl(items: ["Standard", "Satellite", "Hybrid"])`
  `segmentedControl.backgroundColor = UIColor.white.withAlphaComponent(0.5)`
  `segmentedControl.selectedSegmentIndex = 0`
>
  **`segmentedControl.addTarget(self, action: #selector(MapViewController.mapTypeChanged(_:)),for: .valueChanged)`**
`...`

接下来，在 **MapViewController** 中实现事件触发的 action 方法。 此方法将检查哪个段被选中，并相应更新地图。

> **`func mapTypeChanged(_ segControl: UISegmentedControl) {`**
  **`switch segControl.selectedSegmentIndex {`**
  **`case 0:`**
    **`mapView.mapType = .standard`**
  **`case 1:`**
    **`mapView.mapType = .hybrid`**
  **`case 2:`**
    **`mapView.mapType = .satellite`**
  **`default:`**
    **`break`**
  **`}`**
**`}`**

构建并运行应用程序。 更改所选段，地图将更新。

## 青铜挑战：另一个标签 ##

创建一个新的视图控制器并将其添加到标签栏控制器。 该视图控制器应显示一个 **WKWebView**，它是一个用于显示Web内容的类。 网页视图应显示 www.bignerdranch.com 的内容。

## 白银挑战：用户位置 ##

向 **MapViewController** 添加一个按钮，显示和放大用户当前位置。 您将需要使用委托来完成此任务。 请参阅 **MKMapViewDelegate** 的文档。

## 黄金挑战：Dropping Pins ##

地图视图可以显示 `pin`(应该是指示针吧)，一个 **MKPinAnnotationView** 的实例。 添加三个 pin 到地图视图：一个你出生的位置，一个你当前的位置，一个你曾经访问过的有趣的位置。 向界面添加一个按钮，允许地图显示 pin 的位置。 随后的点击事件应当通过 pin 列表来进行循环。

## 更多：NSAutoresizingMaskLayoutConstraint ##

如前所述，在 Auto Layout 之前 iOS应用程序使用另一个系统进行布局管理：autoresizing mask。 每个视图都有一个 autoresizing mask，约束其与其父视图的关系，但是该掩码不能影响兄弟视图之间的关系。

默认情况下，视图根据 autoresizing mask 创建和添加约束。 但是，这些转换的约束通常与您的布局中的显式限制相冲突，这导致了不能令人满意的约束问题。

要看到这种情况，请注释掉 **loadView()** 中的行，关闭自动调整掩码的翻译。

>`//segmentedControl.translatesAutoresizingMaskIntoConstraints = false`
`view.addSubview(segmentedControl)`

现在，分段控件的自动大小掩码将被转换为约束。 构建并运行应用程序并导航到地图界面。 你不会喜欢你看到的。 控制台将报告问题及其解决方案。

>`Unable to simultaneously satisfy constraints.`
>
`Probably at least one of the constraints in the following list is one you don't want. Try this: (1) look at each constraint and try to figure out which you don't
expect; (2) find the code that added the unwanted constraint or constraints and
fix it. (Note: If you're seeing NSAutoresizingMaskLayoutConstraints that you don't
understand, refer to the documentation for the UIView property translatesAutoresizingMaskIntoConstraints)`
`(`
  `"<NSAutoresizingMaskLayoutConstraint:0x7fb6b8e0ad00
h=--& v=--& H:[UISegmentedControl:0x7fb6b9897390(212)]>",`
  `"<NSLayoutConstraint:0x7fb6b9975350 UISegmentedControl:0x7fb6b9897390.leading == UILayoutGuide:0x7fb6b9972640'UIViewLayoutMarginsGuide'.leading>",`
  `"<NSLayoutConstraint:0x7fb6b9975460 UISegmentedControl:0x7fb6b9897390.trailing
== UILayoutGuide:0x7fb6b9972640'UIViewLayoutMarginsGuide'.trailing>",`  
  `"<NSLayoutConstraint:0x7fb6b8e0b370 'UIView-Encapsulated-Layout-Width' H:[MKMapView:0x7fb6b8d237c0(0)]>",`
  `"<NSLayoutConstraint:0x7fb6b9972020 'UIView-leftMargin-guide-constraint'
H:|-(0)-[UILayoutGuide:0x7fb6b9972640'UIViewLayoutMarginsGuide'](LTR)(Names: '|':MKMapView:0x7fb6b8d237c0 )>",`
  `"<NSLayoutConstraint:0x7fb6b9974f50 'UIView-rightMargin-guide-constraint'
H:[UILayoutGuide:0x7fb6b9972640'UIViewLayoutMarginsGuide']-(0)-|(LTR)
(Names: '|':MKMapView:0x7fb6b8d237c0 )>"`
`)`
>
`Will attempt to recover by breaking constraint
<NSLayoutConstraint:0x7fb6b9975460 UISegmentedControl:0x7fb6b9897390.trailing
== UILayoutGuide:0x7fb6b9972640'UIViewLayoutMarginsGuide'.trailing>`
>
`Make a symbolic breakpoint at UIViewAlertForUnsatisfiableConstraints to catch
this in the debugger.`
`The methods in the UIConstraintBasedLayoutDebugging category on UIView listed
in <UIKit/UIView.hmay also be helpful.`

我们来看看这个输出。 自动布局报告说 `Unable to simultaneously satisfy constraints.` *`无法同时满足约束条件`。*当视图层次结构具有冲突的约束时，会发生这种情况。

然后，控制台会显示一些方便的提示和所涉及的所有约束的列表及其说明。 让我们更仔细地看一下这些约束之一的格式。

> `<NSLayoutConstraint:0x7fb6b9975350 UISegmentedControl:0x7fb6b9897390.leading
== UILayoutGuide:0x7fb6b9972640'UIViewLayoutMarginsGuide'.leading>`

该描述指示将要设置在 **UISegmentedControl** 前部的约束(位于存储器地址 `0x7fb6b9975350`) 等于 **UILayoutGuide** （在`0x7fb6b9972640`）的前部（在 `0x7fb6b9897390`）。

其中五个约束是 **NSLayoutConstraint** 的实例。 然而，一个是 **NSAutoresizingMaskLayoutConstraint** 的实例。 该约束是图像视图的 autoresizing mask 的转换的产物。

最后，自动布局告诉您如何通过列出将忽略的冲突约束来解决问题。 不幸的是，它选择地不好，忽略了 **NSLayoutConstraint** 的一个显式实例，而非 **NSAutoresizingMaskLayoutConstraint**。 这就是为什么你的界面为什么会变成这样。

在列出约束前提醒自己 "**NSAutoresizingMaskLayoutConstraint** 需要删除" 是非常有用的。 更好的是，您可以通过在 **loadView()** 中明确禁用转换来防止这种约束被添加：

> ~~`//`~~`segmentedControl.translatesAutoresizingMaskIntoConstraints = false` 
`view.addSubview(segmentedControl)`