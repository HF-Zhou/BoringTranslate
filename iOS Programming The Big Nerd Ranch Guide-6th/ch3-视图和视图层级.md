在接下来的五章中，您将构建一个名为 `WorldTrotter` 的应用程序。 完成后，此应用程序将转换华氏温度和摄氏温度之间的值。 在本章中，您将通过创建 `WorldTrotter` 的UI来了解视图和视图层级。 在本章结尾，您的应用程序将如图3.1所示。

**图3.1 WorldTrotter**

![](http://upload-images.jianshu.io/upload_images/1230738-585118cd08ff8be3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们先从一些视图和视图层级的理论入手。

## 视图基础知识 ##

回顾第1章，视图是用户可见的对象，如按钮，文本字段和滑块。 视图对象组成应用程序的UI。 一个视图：

- 是 **UIView** 或其子类的一个实例
- 知道如何绘制自己
- 可以处理事件，如触摸
- 存在于其根是应用程序的窗口的视图的层级中

让我们更详细地看看 `视图层级(view hierarchy)`

### 视图分层 ###

每个应用程序都有一个单一的 **UIWindow** 实例，作为应用程序中所有视图的容器。 **UIWindow** 是 **UIView** 的一个子类，所以窗口本身就是一个视图。 该窗口在应用程序启动时创建。 创建窗口后，可以添加其他视图。

当视图被添加到窗口时，它被认为是窗口的子视图。 作为窗口子视图的视图也可以具有子视图，结果是生成以窗口为根的视图对象的层级（图3.2）。

**图3.2 示例视图层级及其创建的界面**

![](http://upload-images.jianshu.io/upload_images/1230738-b83d5b06e3b63b9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一旦创建了视图层级，它将被绘制到屏幕上。 这个过程可以分为两个步骤：

- 层级中的每个视图（包括窗口）自身绘制。 它将自身渲染到其 图层(`layer`)，您可以将其视为位图图像。 （该图层是 **CALayer** 的一个实例。）
- 所有视图的图层在屏幕上合成在一起。

图3.3描绘了另一个示例视图层级和两个绘图步骤。

**图3.3 视图渲染自身，然后合成在一起**

![](http://upload-images.jianshu.io/upload_images/1230738-fbc292ff1372a7d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于 `WorldTrotter`，您将创建一个由不同视图组成的界面。 将有四个 **UILabel** 实例和一个 **UITextField** 实例，允许用户输入华氏温度。 让我们开始吧。

## 创建新项目 ##

在 `Xcode` 中，选择 `File` → `New` → `Project...` （或使用键盘快捷键 `Command-Shift-N`）。 在顶部的 `iOS` 部分下，选择 `Application` 下的 `Single View Application` 模板，然后单击 `Next`。

Project Name 输入 `WorldTrotter` 。 确保从 `Language` 下拉菜单中选择了 `Swift`，并从 `Devices` 下拉列表中选择 `Universal`。 还要确保 `Use Core Data` 没被选中（图3.4）。 单击 `Next`，接着点击 `Create`。

**图3.4 配置 WorldTrotter**

![](http://upload-images.jianshu.io/upload_images/1230738-a172060733fda40f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 视图和边框 ###

当您以编程方式初始化视图时，您可以使用指定的初始化程序 **init（frame :)** 。 该方法使用一个参数 **CGRect**，它将成为视图的 `边框(frame)`，即 **UIView** 上的一个属性。

> `var frame: CGRect`

*`视图的边框(frame)指定视图的大小及其相对于其父级视图的位置。 由于视图的大小始终由其边框指定，所以视图始终是一个矩形。`*

**CGRect** 包含成员 `origin` 和 `size`。 `origin` 是 **CGPoint** 类型的结构，并包含两个 **CGFloat** 属性：`x` 和 `y`。 `size` 是 **CGSize** 类型的结构体，并具有两个 **CGFloat** 属性：`width` 和 `height`（图3.5）。

**图3.5 CGRect**

![](http://upload-images.jianshu.io/upload_images/1230738-8f22567e36a9e31c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当应用程序启动时，初始视图控制器的视图将添加到根视图中。 该视图控制器由 `ViewController.swift` 中定义的 **ViewController** 类表示。 我们将在第 5 章讨论视图控制器，但现在只要知道一个视图控制器对应一个视图就足够了，并且与应用程序主视图控制器相关联的视图会作为一个子视图被添加到窗口下。

在创建 `WorldTrotter` 的视图之前，您将以编程方式添加一些练习视图，以查看视图及其属性，并了解如何创建应用程序界面。

打开 `ViewController.swift` 并删除模板创建的任何方法。 您的文件应如下所示：

>`import UIKit`
>
`class ViewController: UIViewController {`
>
`}`

（UIKit，您也在第1章中看到）是一个 `框架(framework)`，一个框架是相关类和资源的集合，`UIKit` 框架定义了用户看到的许多UI元素以及其他iOS特定的类。 这本书将使用几个不同的框架。）

在视图控制器的视图加载到内存之后，它的 **viewDidLoad（）** 方法被调用。 这种方法为您提供了自定义视图层级的机会，因此它是添加练习视图的好地方。

在 `ViewController.swift` 中，覆盖 **viewDidLoad()**。 创建一个将成为 **UIView** 边框的 **CGRect**。 接下来，创建一个 **UIView** 的实例，并将其 `backgroundColor` 属性设置为蓝色。 最后，将 **UIView** 添加为视图控制器视图的子视图，使其成为视图层级的一部分。 （其中大部分看起来不是很熟悉，没关系，输入代码后我们会解释一下。）

> `class ViewController: UIViewController {`
> 
  **`override func viewDidLoad() {`**
    **`super.viewDidLoad()`**
>
    **`let firstFrame = CGRect(x: 160, y: 240, width: 100, height: 150)`**
    **`let firstView = UIView(frame: firstFrame)`**
    **`firstView.backgroundColor = UIColor.blue`**
    **`view.addSubview(firstView)`**
  **`}`**
**`}`**

要创建 **CGRect**，您可以使用它的构造器并传入 `origin.x`，`origin.y`，`size.width` 和 `size.height` 的值。
 
您将使用 `UIColor` 类属性 `Blue` 来设置 `backgroundColor`。 这是一个初始化配置为 `blue` 的 **UIColor** 实例。 有很多常用的 **UIColor** 类属性，如 `green`，`black` 和 `clear`。

构建并运行应用程序（`Command-R`）。 您将看到一个蓝色矩形，它是 **UIView** 的实例。 因为 **UIView** 的边框的起点是（160,240），所以矩形的左上角为相对于其父视图 向右偏移 160 点 向下偏移 240 点。 根据 `size` 属性，视图将向右延伸100点，从 `origin` 向下延伸150点（图3.6）。

**图3.6 具有一个UIView的WorldTrotter**

![](http://upload-images.jianshu.io/upload_images/1230738-23d2dd3896fc41d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意，这些值是点而不是像素。 如果值是以像素为单位，则它们在不同分辨率的显示（如 Retina versus 与 non-Retina 屏 ）上不一致。 点是度量的相对单位; 它是不同数量的像素，这取决于显示器中有多少个像素。 大小，位置，线条和曲线总是以点表示，以便适配不同分辩率的设备。

图3.7表示您创建的视图层级。

**图3.7当前视图层级**

![](http://upload-images.jianshu.io/upload_images/1230738-fd9ceebffa10bd41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**UIView** 的每个实例都有一个 `superview` 属性。 当您将视图添加为另一个视图的子视图时，反向关系将自动建立。 在当前情况下，**UIView** 的
 `superview` 是 **UIWindow**。

我们来试验视图层级。 首先，在 `ViewController.swift` 中，使用不同的 `frame` 和背景颜色创建另一个 **UIView** 实例。

> `override func viewDidLoad() {`
  `super.viewDidLoad()`
> 
  `let firstFrame = CGRect(x: 160, y: 240, width: 100, height: 150)`
  `let firstView = UIView(frame: firstFrame)`
  `firstView.backgroundColor = UIColor.blue`
  `view.addSubview(firstView)`
>
  **`let secondFrame = CGRect(x: 20, y: 30, width: 50, height: 50)`**
  **`let secondView = UIView(frame: secondFrame)`**
  **`secondView.backgroundColor = UIColor.green`**
  **`view.addSubview(secondView)`**
`}`

构建并再次运行。 除了蓝色矩形之外，您将在窗口的左上角附近看到一个绿色的正方形。 图3.8显示了更新后的视图层级。

**图3.8 更新后的视图层级，两个子视图作为兄弟**

![](http://upload-images.jianshu.io/upload_images/1230738-f68b1fe6b8b13bfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在，您将调整视图层次结构，以使 **UIView** 的一个实例是另一个 **UIView** 的子视图，而不是视图控制器的视图。 在 `ViewController.swift` 中，添加 `secondView` 作为 `firstView` 的子视图。

> `...`
`let secondView = UIView(frame: secondFrame)`
`secondView.backgroundColor = UIColor.green`
~~`view.addSubview(secondView)`~~
**`firstView.addSubview(secondView)`**

您的视图层级现在有四层深，如图3.9所示。

**图3.9 一个UIView作为另一个的子视图**

![](http://upload-images.jianshu.io/upload_images/1230738-b08b2f26fa382686.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 请注意，`secondView` 在屏幕上的位置已更改（图3.10）。 视图的 边框(frame) 是相对与其父级视图的，所以 `secondView` 的左上角（20,30）是以 `firstView` 左上角为参照的。

**图3.10具有新层级的WorldTrotter**

![](http://upload-images.jianshu.io/upload_images/1230738-6ba43f636ed68c12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（如果 绿色的 **UIView** 实例看起来比以前更小，这只是一个幻觉，其实它的大小没有改变。）

现在您已经了解了视图和视图层级的基础知识，可以开始在 `WorldTrotter` 的界面上工作了。 但不是以编程方式构建界面，您将如第一章一样去使用 `Interface Builder` 来直观地布局界面。

在 ViewController.swift 中，首先删除练习代码。

> ~~`override func viewDidLoad() {`~~
  ~~`super.viewDidLoad()`~~
> 
  ~~`let firstFrame = CGRect(x: 160, y: 240, width: 100, height: 150)`~~
  ~~`let firstView = UIView(frame: firstFrame)`~~
  ~~`firstView.backgroundColor = UIColor.blue`~~
  ~~`view.addSubview(firstView)`~~
>
  ~~`let secondFrame = CGRect(x: 20, y: 30, width: 50, height: 50)let secondView = UIView(frame: secondFrame)`~~
  ~~`secondView.backgroundColor = UIColor.green`~~
  ~~`firstView.addSubview(secondView)`~~
~~`}`~~

现在让我们添加一些视图到界面并设置它们的边框。

打开 `Main.storyboard`。 在画布的底部，确保 `View as` 按钮配置为显示 iPhone 7 设备。

从对象库中，拖放五个 **UILabel** 的实例到画布上。 将其文本设置为图3.11所示，将它们在界面的上半部分垂直放置，并将它们水平放置。

**图3.11 添加标签到界面**

![](http://upload-images.jianshu.io/upload_images/1230738-0e4c3e4de690314e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择顶部标签，以便您可以在 `Interface Builder` 中看到它的边框。 打开其 `大小检查器(size inspector)` ——公用程序区域中的第五个选项卡。 （实用程序选项卡的键盘快捷键为 Command-Option 加上选项卡号，尺寸检查器是第五个选项卡，因此键盘快捷键为 `Command-Option-5`。）
>虚拟机中 `win+Alt+5`

在 `View` 部分下，找到 `Frame Rectangle`。 （如果没有看到，可能需要从 `show` 弹出菜单中选择它）。显示的值是视图的 边框(frame)，它们决定了屏幕上的视图位置（图3.12）。

**图3.12查看边框值**

![](http://upload-images.jianshu.io/upload_images/1230738-b52c23d467ab554e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 iPhone 7 模拟器上构建并运行应用程序。 模拟器上的界面与 `Interface Builder` 中布置的界面看起来是一样的。

### 自定义标签 ###

通过自定义视图属性，让界面看起来更漂亮。

在 `Main.storyboard` 中，选择背景视图。 打开属性检查器并给应用程序一个新的背景颜色：查找并单击 `Background` 下拉列表并单击 `Other...`。选择第二个选项卡（`Color Sliders` 选项卡），并从下拉列表中选择 `RGB Sliders`。 在底部附近的 `Hex Color #` 框中输入 `F5F4F1` （图3.13）。 这将给背景一个温暖的灰色。

**图3.13更改背景颜色**

![](http://upload-images.jianshu.io/upload_images/1230738-2bbbed6fa2ac68bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您可以同时自定义所选视图的常用属性。 您将使用它来使许多标签拥有更大的字体大小以及烧焦的橙色文本颜色。

通过在文档大纲中按住Win键单击它们来选择顶部的两个和底部两个标签。 确保属性检查器已打开，并更新文本颜色：在 `Label` 部分下，找到 `Color` 并打开弹出菜单。 再次选择 `Color Sliders` 选项卡，在 `Hex Color #` 输入 `E15829` 。

现在我们来更新字体。 选择 `212` 和 `100` 这两个标签。 在属性检查器的 `Label` 部分下，查找 `Font`，然后单击当前字体旁边的文本图标。 在出现的弹出框中，`Size` 改为 `70`（图3.14）。 选择剩余的三个标签。 打开他们的 `Font` 弹出窗口并修改 `Size` 为 36。

**图3.14 自定义标签的字体**

![](http://upload-images.jianshu.io/upload_images/1230738-fc3732aee98a209b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在字体大小较大，文本不再符合标签的范围。 您可以手动调整标签大小，但有一种更简单的方法。

选择画布上的顶部标签。 从 `Xcode` 的 `Editor` 菜单中，选择 `Size to Fit Content`（`Command-=`）。 这将调整标签的大小以完全适合其文本内容。 对其他四个标签重复此过程。 （您可以选择所有四个标签来一次性调整它们的大小。）现在移动标签，使其再次垂直对齐并水平居中（图3.15）。

**图3.15 更新标签边框**

![](http://upload-images.jianshu.io/upload_images/1230738-5793664ff087d178.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 iPhone 7 模拟器上构建并运行应用程序。 然后在 iPhone 7 Plus 模拟器上构建并运行应用程序。 请注意，标签不再居中——而是在偏左的位置。

你刚刚看到绝对边框的两个主要问题。 首先，当内容发生变化时（如改变字体大小），边框不会自动更新。 第二，视图在不同尺寸的屏幕上看起来并不是很好。

一般来说，您不应该在您的视图中使用绝对边框。相反，您应该使用自动布局，根据您为每个视图指定的约束来灵活地计算边框。例如，对于 `WorldTrotter` 来说，你真正想要的是让标签保持与屏幕顶端相同的距离，并保持水平居中在他们的父视图中。如果标签的字体或文本发生变化，它们也应该进行更新。这是在下一节中您将完成的任务。

## 自动布局系统 ##

在你可以修改标签让它们灵活地布局之前，你需要学习一些关于自动布局系统的理论。正如您在第1章中看到的，绝对坐标使您的布局变得很脆弱，因为他们假定您提前知道屏幕的大小。

使用自动布局，您可以用相对的方式描述视图的布局，以便在运行时确定它们的边框，因此边框可以按照正在运行的设备的屏幕大小来调整应用程序的布局。

### 对齐矩形和布局属性 ###

自动布局系统基于对齐矩形。 该矩形由几个布局属性定义（图3.16）。

**图3.16定义视图的对齐矩形的布局属性**

![](http://upload-images.jianshu.io/upload_images/1230738-1014b153f9e6ae95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`Width/Height`

这些值确定对齐矩形的大小。

`Top/Bottom`
`Left/Right`

这些值确定对齐矩形的给定边和层级中另一视图的对齐矩形之间的间距。

`CenterX`
`CenterY`

这些值确定对齐矩形的中心点。

`FirstBaseline`
`LastBaseline`

这些值与大多数（而不是全部）视图的底部属性相同。 例如，**UITextField** 将其基线定义为其显示的文本的底部，而不是对齐矩形的底部。 这使得“下降”（在基线下方的 “g” 和 “p” 字母的部分）不被文本字段下方的视图遮蔽。 对于多行文本标签和文本视图，第一个和最后一个基准指的是文本的第一行和最后一行。 在所有其他情况下，第一个和最后一个基线是相同的。

`Leading`
`Trailing`

这些值是语言特定的属性。 如果设备设置为从左到右读取的语言（例如，英文），则 前部(Leading) 属性与左侧属性相同， `尾部(Trailing)` 属性与右侧属性相同。 如果语言从左到右（例如阿拉伯语）读取，则前导属性在右侧，尾部属性在左侧。 界面生成器(Interface Builder) 自动优先于左和右的前导和尾随，一般来说，您也应该这样做。

默认情况下，每个视图都有一个对齐矩形，每个视图层次结构都使用自动布局。

对齐矩形与边框非常相似。 事实上，这两个矩形通常是一样的。 而边框包含整个视图，而对齐矩形仅包含您希望用于对齐目的的内容。 图3.17显示了边框和对齐矩形不同的示例。

**图3.17边框 VS 对齐矩形**

![](http://upload-images.jianshu.io/upload_images/1230738-0f7e815e9804b73d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您不能直接定义视图的对齐矩形。 你没有足够的信息（如屏幕尺寸）来做到这一点。 相反你应该提供一组约束。 总而言之，这些约束使系统能够为视图层次结构中的每个视图确定布局属性，从而确定对齐矩形。

### 约束 ###

`约束(constraint)` 定义视图层级中的特定关系，可用于确定一个或多个视图的布局属性。 例如，您可以添加一个约束，如 “这两个视图之间的垂直间距应始终为8点”，或者 “这些视图必须始终具有相同的宽度”。也可以使用约束来给视图一个固定的 大小，如 “这个视图的高度应该总是44点”。

您不需要对每个布局属性都添加约束。 一些值可能直接来自约束; 其他将由相关布局属性的值计算。 例如，如果视图的约束设置其左边缘和其宽度，则右边缘已经确定（左边缘+宽度=右边缘）。 一般来说，每个维度至少需要两个约束（水平和垂直）。

如果在考虑所有约束之后，布局属性仍然存在歧义或缺失值，那么自动布局会出现错误和警告，并且您的界面在所有设备上看起来都不会像您所期望的那样。 调试这些问题很重要，您将在本章后面的一些练习中进行实践。

你该如何去设置约束呢？ 让我们来看看如何使用您在画布上布置的标签。

首先，描述您想要的视图看起来与屏幕大小无关。例如，你可能会说你想要上面的标签是:

- 距离屏幕顶部 8 点
- 在它的父视图中水平居中
- 与文本一样宽和高

要将此描述转换为 `Interface Builder` 中的约束，它将有助于了解如何查找视图的 `最近邻居(nearest neighbor)`。 nearest neighbor 是指定方向上最接近的同级视图（图3.18）。

**图3.18最近邻居**

![](http://upload-images.jianshu.io/upload_images/1230738-a100cbfb1579d475.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果视图在指定方向上没有任何兄弟姐妹，则最近的邻居是其父级视图，也是其容器。

现在您可以明确标签的约束：

1. 标签的顶部边缘应距离其最近的邻居（这是其容器 - **ViewController** 的视图）8点。
2. 标签的中心应与其父视图中心相同。
3. 标签的宽度应等于其字体大小呈现的文本的宽度。
4. 标签的高度应等于其字体大小呈现的文本的高度。

如果你考虑第一个和第四个约束，你可以看到没有必要明确地约束标签的底边。 它将根据标签上边缘和标签高度的约束来确定。 类似地，第二和第三约束一起确定标签的右边缘和左边缘。

现在您知道如何去设计顶部标签，您将添加这些约束。 可以使用 `Interface Builder` 或代码的方式添加约束。 Apple 建议您尽可能使用 `Interface Builder` 添加约束，这就是您将在此处执行的操作。 但是，如果您的视图是以编程方式创建和配置的，则可以在代码中添加约束。 在第六章中，你会练习这种方法。

#### 在 Interface Builder 中添加约束 ####

让我们开始约束这个顶部的标签。

选择画布上的顶部标签。 在画布的右下角找到自动布局约束菜单（图3.19）。

**图3.19自动布局约束菜单**

![](http://upload-images.jianshu.io/upload_images/1230738-9c3b94d65641ee46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单击
 ![](http://upload-images.jianshu.io/upload_images/1230738-83b1f8db0755ac0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
图标（左侧第四个）以显示 `Add New Constraints` 菜单。 此菜单显示标签的当前大小和位置。

在 `Add New Constraints` 菜单的顶部有四个值，它们描述标签与画布上最近邻居的当前间距。 对于此标签，您只对 top 值感兴趣。

要将此值变为约束，请单击顶部的红色支柱，将值与中间的正方形分开。 支柱将成为一条红色实线。

在菜单中间找到标签的 `Width` 和 `Height`。 `Width` 和 `Height` 旁边的值表示当前画布值。 要将标签的宽度和高度限制为当前画布值，请选中 `Width` 和 `Height` 旁边的框。点击菜单底部的按钮 `Add 3 Constraints`。

此时，您尚未指定足够的约束来完全确定对齐矩形。 标签周围的红色轮廓表示其对齐矩形未完全定义，而 `Interface Builder` 将帮助找出问题所在。

注意 `Interface Builder` 的右上角黄色警告标志（图3.20）。 点击此图标显示问题：
`Horizontal position is ambiguous for "212".`
*`“212” 的水平位置不明确`*。

**图3.20 水平方向不确定**

![](http://upload-images.jianshu.io/upload_images/1230738-129c501ab2132e7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您添加了两个垂直约束（顶边约束和高度约束），但是您只添加了一个水平约束（宽度约束）。 只有一个约束使得标签的水平位置不明确。 您将通过在标签和其父级视图之间添加中心对齐约束来解决此问题。

选中顶部标签后，单击 
![](http://upload-images.jianshu.io/upload_images/1230738-fc587dfa7930e81d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 图标（左侧的第三个图标）显示 `对齐(Align)` 菜单。 如果您选择了多个视图，则此菜单将允许您在视图中对齐属性。 因为您只选择了一个标签，所以您唯一的选择是在其容器内对齐视图。

在 `Align` 菜单中，检查 `Horizontally in Container`（现在请勿单击 `Add 1 Constraint`）。 添加此约束后，将有足够的约束来完全确定对齐矩形。 要确保标签的边框与指定的约束匹配，请从 `Align` 菜单中打开  `Update Frames` 弹出菜单，然后选择 `Items of New Constraints`。 这将重新定位标签以匹配已添加的约束。 现在单击 `Add 1 Constraint` 以添加居中约束并重新定位标签。

标签的约束都是蓝色的，因为标签的对齐矩形已经完全指定。 此外，`Interface Builder` 右上角的警告现在已经没了。

在 iPhone 7 模拟器和 iPhone 7 Plus 模拟器上构建和运行应用程序。 顶部标签在两个模拟器中都能保持居中。

#### 内在内容大小 ####

虽然顶部标签的位置是灵活的，但它的大小不是。 这是因为您已经向标签添加了明确的宽度和高度限制。 如果文字或字体改变，您将处于与之前相同的位置。 边框的大小是绝对的，所以边框不会随内容修改而变化。

这是视图的 `内在内容大小(intrinsic content size)` 发挥作用的地方。 您可以将内在内容大小视为视图“想要”的大小。 对于标签，此大小是以给定字体呈现的文本的大小。 对于图像，就是图像本身的大小。

视图的内在内容大小作为隐式的宽度和高度限制。 如果不明确指定宽度的约束，则视图将是其固有宽度。 高度也一样。

了解这些之后，我们可以通过删除显式的宽度和高度约束，让顶部标签具有灵活的大小。

在 `Main.storyboard` 中，选择标签上的宽度约束。 您可以通过单击画布上的约束来执行此操作。 或者，在文档大纲中，您可以单击 `212` 标签旁边的倒三角形，然后列出标签的约束列表（图3.21）。

**图3.21选择宽度约束**

![](http://upload-images.jianshu.io/upload_images/1230738-cd59a8efc7c6475c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择宽度约束后，按 Delete 键。 对于高度约束也是一样的。

请注意，标签的约束仍然是蓝色。 因为宽度和高度是从标签的固有内容大小推断出来的，所以仍然有足够的约束来确定标签的对齐矩形。

### 视图错位 ###

如您所见，蓝色约束表示视图的对齐矩形是完全指定的。 橙色约束通常表示 `视图错位(misplaced view)`。 这意味着 Interface Builder 中视图的边框与自动布局系统所计算的边框不同。

视图错位很容易解决。因为在使用自动布局时， 这是一个很常见的问题。

让您的顶部标签出现错位置视图，以便您可以看到如何解决此问题。 使用调整大小控件调整画布上的顶部标签大小，并在画布的右上角查找黄色警告。 单击此警告图标以显示问题： 
`Frame for "212" will be different at run time (Figure 3.22).`
*`运行时“212”的边框将不同（图3.22）`*。

**图3.22 视图错位警告**

![](http://upload-images.jianshu.io/upload_images/1230738-5ebe18c972433ed1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如警告所述，运行时的边框与画布上指定的边框不同。 如果仔细观察，您将看到一条橙色的虚线，表示运行时边框是什么样子的。

构建并运行应用程序。 请注意，尽管您在 `Interface Builder` 中提供了新的边框，标签仍然会居中。 这可能看起来很棒——毕竟这就是你想要的结果。 但是，当您继续去构建视图时，在 `Interface Builder` 中指定的与自动布局计算出来的约束之间的脱节会导致问题的发生。 我们来解决这个问题。

回到 `故事板(storyboard)`，选择画布上的顶部标签。 单击
![](http://upload-images.jianshu.io/upload_images/1230738-c1a62901d276d805.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
图标（最左侧的图标），更新标签的边框，以匹配约束计算出来的边框。

使用自动布局时，您将非常习惯更新视图的边框。 建议：如果您尝试更新没有足够约束的视图的边框，您会得到意料之外的结果。 如果发生这种情况，请撤消更改并检查约束以查看缺失的内容。

此时，顶部标签形状良好。 它有足够的约束来确定其对齐矩形，并且视图正在布置您想要的方式。

熟练使用自动布局需要很多经验，因此在下一部分中，您将从顶部标签中删除约束，然后向所有标签添加约束。

### 增加更多的约束 ###

我们来看看其余的视图的约束。 在执行此操作之前，先删除顶部标签的现有约束。

选择画布上的顶部标签。 打开 `Resolve Auto Layout Issues` 菜单，然后从 `Selected Views` 部分中选择 `Clear Constraints`（图3.23）。

**图3.23 清除约束**

![](http://upload-images.jianshu.io/upload_images/1230738-7093c6e38b1eea7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您将通过两个步骤将约束添加到所有视图。 首先，您将在父级视图中水平放置顶部标签。 然后，您将添加约束，将每个标签的顶部定位到其最近的邻居，同时对齐所有标签的中心。

选择顶部标签。 打开 `Align` 菜单，并在容器中选择 `Horizontally in Container`，设置为常量 `0`.确保没有选中 `Update Frames`; 请记住，您不会想更新没有足够约束的视图的边框，而这个约束肯定不会提供足够的信息来计算对齐矩形。 接着点击 `Add 1 Constraint`。

现在选择画布上的所有五个标签。 同时添加多个视图的约束是非常方便的。 打开 `Add New Constraints` 菜单并进行以下选择：

1. 选择顶部支柱并确保它为 8
2. 从 `Align` 菜单中选择 `Horizontal Centers`。
3. 从 `Update Frames` 菜单中，选择 `Items of New Constraints`。

您的菜单应该会像 图3.24 一样。 完成后，单击 `Add 9 Constraints`。 这将为视图添加约束并更新其边框以响应自动布局更改。

**图3.24使用 Add New Constraints 菜单添加更多约束**

![](http://upload-images.jianshu.io/upload_images/1230738-576137e57addff9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 iPhone 7 模拟器上构建并运行应用程序。 视图将会居中。 接着在 iPhone 7 Plus 模拟器上构建并运行应用程序。 与本章之前不同，所有标签都居中在较大的屏屏幕上。

自动布局是每个iOS开发人员的关键技术。 它可以帮助您创建灵活的布局，适用于各种设备和界面大小。 掌握它也需要很多的练习。 在使用本书时，您将获得使用自动布局的丰富经验。

## 青铜挑战：更多的自动布局实践 ##

从 **ViewController** 界面中删除所有约束，然后将其重新插入。尝试在不看本书的情况下完成。