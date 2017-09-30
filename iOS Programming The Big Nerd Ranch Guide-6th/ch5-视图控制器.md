视图控制器是 **UIViewController** 的子类的一个实例。 视图控制器管理视图层级。 它负责创建构成层级的视图对象，并负责处理与其层级中的视图对象相关联的事件。

到目前为止，`WorldTrotter` 只有一个视图控制器 **ConversionViewController**。 在本章中，您将更新它以使用多个视图控制器。 用户将能够在两个视图层次结构之间切换 - 一个用于查看 **ConversionViewController**，另一个用于显示地图（图5.1）。

**图5.1 两个视图的WorldTrotter**

![](http://upload-images.jianshu.io/upload_images/1230738-3e1bcbeea389d06e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 视图控制器的视图 ##

作为 **UIViewController** 的子类，所有视图控制器都继承了一个重要的属性：

`var view: UIView!`

该属性指向作为视图控制器视图层级的根的 **UIView** 实例。 当视图控制器的 `view` 作为窗口的子视图添加时，将添加视图控制器的整个视图层级，如图5.2所示。

**图5.2 WorldTrotter的对象图**

![](http://upload-images.jianshu.io/upload_images/1230738-12baee3bee6fed7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

视图控制器的 `view` 直到它需要出现在屏幕上才会被创建。 这种优化被称为 `延迟加载(lazy loading)`，它可以节省内存并提高性能。

视图控制器可以通过两种方式创建其视图层级：

- 在 `Interface Builder` 中，使用界面文件（如故事板）
- 以编程方式，重写 **UIViewController** 方法 **loadView()**

您在第3章中看到了第一种方法。首先，您以编程方式创建了一个示例视图层级，然后切换到 `Interface Builder` 以使用 storyboard 文件创建 **ConversionViewController** 的界面。 当您进一步浏览视图控制器时，您将在本章中继续使用 `Interface Builder`。 在第6章中，您将使用 **loadView()** 创建编程式的视图。

## 设置初始视图控制器 ##

虽然故事板可以有多个视图控制器，但每个故事板文件只有一个 `初始视图控制器(initial view controller)`。 初始视图控制器作为故事板中的入口点。 您将添加并配置另一个视图控制器到画布，并将其设置为故事板的初始视图控制器。

打开 `Main.storyboard`。 从对象库中，将 `View Controller` 拖到画布上（图5.3）。 （要在画布上放置空间，可以点击背景，使用画布底部的缩放控件，或使用触控板上的手势）。

**图5.3将视图控制器添加到画布**

![](http://upload-images.jianshu.io/upload_images/1230738-e431a8c6825cf3ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您希望此视图控制器显示 **MKMapView** —— 一个显示地图的类，而不是现有的白色 **UIView**。

选择 `View Controller` 的视图 - 而不是 `View Controller` 本身！ - 然后按 Delete 键从画布中删除此视图。 然后将 `Map Kit View` 从对象库拖动到 `View Controller` 上，将其设置为此视图控制器的 `View`（图5.4）。

**图5.4将地图视图添加到画布**

![](http://upload-images.jianshu.io/upload_images/1230738-0a423f4bb50fb6b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在选择 `View Controller` 并打开其属性检查器。 在 `View Controller` 部分下，选中 `Is Initial View Controller`旁边的框（图5.5）。 你注意到画布上指向 `Conversion View Controller` 的灰色箭头现在指向 `View Controller` 了吗？ 箭头，如你推测一样，它用于表示初始视图控制器。 分配初始视图控制器的另一种方法是将该箭头从一个视图控制器拖动到画布上的另一个。

**图5.5设置初始视图控制器**

![](http://upload-images.jianshu.io/upload_images/1230738-8302467f123c1f6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果您现在要构建并运行该应用程序，那么会有一个奇怪的问题。 （你可以去试一下。）**MKMapView** 是一个当前没有被加载到应用程序的 `框架`。 一个框架是一个共享的代码库，其中包括关联的资源，如界面文件和图像。 您简要地了解了第3章中的框架，并且您已经使用了几个框架：`UIKit` 和 `Foundation` 都是框架。

到目前为止，您已经通过使用 `import` 关键字在应用程序中包含框架，如下所示：

`import UIKit`

现在，您需要导入 `MapKit` 框架，以便加载 **MKMapView**。 但是，如果只使用 `import` 关键字导入 `MapKit`框架，而不去使用该框架的任何代码，编译器将忽略它——即使您在故事板中使用地图视图。

因此，您需要手动将 `MapKit` 框架链接到应用程序。

打开项目导航器，单击列表顶部的 `WorldTrotter` 项目以打开项目设置。 在设置中查找并打开 `General` 选项卡。 向下滚动到底部，找到标记为 `Linked Frameworks and Libraries` 的部分。 点击底部的 `+` 搜索 `MapKit.framework`。 选择此框架，然后单击 `Add`（图5.6）。

**图5.6添加MapKit框架**

![](http://upload-images.jianshu.io/upload_images/1230738-52f0f5be3cf5108d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在您可以构建和运行应用程序。 因为您更改了初始视图控制器，所以显示的是地图而不是 **ConversionViewController** 的视图。

如上所述，只能有一个初始视图控制器与给定的故事板相关联。 当您将 `View Controller` 设置为初始视图控制器时，你会看到之前的界面。 此时，`Conversion View Controller` 不再是此故事板的初始视图控制器。 我们来看看这个要求如何与根级 **UIWindow** 一起使用，以便将初始视图控制器的视图添加到窗口层级中。

**UIWindow** 有一个 `rootViewController` 属性。 当一个视图控制器被设置为窗口的 `rootViewController` 时，该视图控制器的视图将添加到窗口的视图层级中。 设置此属性时，将删除窗口上的任何现有子视图，并将视图控制器的视图添加到具有相应自动布局约束的窗口中。

每个应用程序都有一个 `主界面(main interface)` ，并引用一个 `故事板(storyboard)` 文件。 当应用程序启动时，主界面的初始视图控制器被设置为窗口的 `rootViewController`。

应用程序的主界面在项目设置中设置。 仍然在项目设置的 `General` 选项卡中，找到 `Deployment Info` 部分。 在这里，您将看到 `Main Interface` 设置（图5.7）。 这里设置为 `Main`，以对应 `Main.storyboard`。

**图5.7应用程序的主界面**

![](http://upload-images.jianshu.io/upload_images/1230738-31c85700926b0a26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## UITabBarController ##

当用户有一种方式在他们之间切换时，视图控制器变得更加有趣。在这本书中，您将学习许多方法来呈现视图控制器。在本章中，您将创建一个 **UITabBarController**，它允许用户在 **ConversionViewController** 和显示地图的 **UIViewController** 之间进行切换。

**UITabBarController** 有一个视图控制器的数组。 它还在屏幕底部保留了一个标签栏，并在其阵列中显示每个视图控制器的选项卡。 点击选项卡可以显示与该选项卡关联的视图控制器的视图。

打开 `Main.storyboard` 并选择 `View Controller`。 从 `Editor` 菜单中，选择 `Embed In` → `Tab Bar Controller`。 这将将 `View Controller` 添加到 `Tab Bar Controller` 的视图控制器数组中。 您可以看到表示由 `Tab Bar Controller` 指向 `View Controller` 的关系箭头（图5.8）。 另外，`Interface Builder` 会将 `Tab Bar Controller` 设置为故事板的初始视图控制器。

**图5.8带有一个视图控制器的标签栏控制器**

![](http://upload-images.jianshu.io/upload_images/1230738-a5091c3aa083bff8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

标签栏控制器对于仅有一个视图控制器来说没什么用。 因此我们会将 `Conversion View Controller` 添加到 `Tab Bar Controller` 的视图控制器数组。

选中 `Tab Bar Controller` 右键拖动到 `Conversion View Controller`。 从 `Relationship Segue` 部分，选择 `view controllers`（图5.9）。

**图5.9将视图控制器添加到标签栏控制器**

![](http://upload-images.jianshu.io/upload_images/1230738-2f66b46d96f97e72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 点击底部的两个选项卡在两个视图控制器之间切换。 目前，标签只是显示为 `Item`，这不是很有用。 在下一部分中，您将更新选项卡栏项目，使选项卡更加明显和具有描述性。

**UITabBarController** 本身是 **UIViewController** 的子类。 **UITabBarController** 的 `view` 是具有两个子视图的 **UIView**：选项卡栏和所选视图控制器的 `view`（图5.10）。

**图5.10 UITabBarController 示意图**

![](http://upload-images.jianshu.io/upload_images/1230738-fa77fabb5a32099b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 标签栏项 ###

选项卡栏上的每个选项卡都可以显示标题和图像，并且每个视图控件都会为此目的维护一个 `tabBarItem` 属性。 当 **UITabBarController** 包含视图控制器时，其标签栏项目将显示在标签栏中。 图5.11显示了 iPhone `Phone` 应用程序中此关系的示例。

**图5.11 UITabBarItem示例**

![](http://upload-images.jianshu.io/upload_images/1230738-e050c979a1c62c3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

首先，您需要将几个文件添加到项目中，这些文件将是选项卡栏项目的图像。 在项目导航器中，打开 Asset 目录，打开 *Assets.xcassets*。

*asset* 是一组文件，根据用户的设备配置，在运行时被选择（更多在本章结尾）。 您将添加一个 *ConvertIcon* asset 和一个 *MapIcon* asset，每个都有三种不同分辨率的图像。

在您之前下载的文件（www.bignerdranch.com/solutions/iOSProgramming6ed.zip）的 *0-Resources* 目录中，找到 *ConvertIcon.png*，*ConvertIcon@2x.png*，*ConvertIcon@3x.png*，*MapIcon.png*，*MapIcon @ 2x.png* 和 *MapIcon@3x.png*。 将这些文件拖到 Asset 目录左侧的图像集列表中（图5.12）。

**图5.12将图像添加到 Asset 目录**

![](http://upload-images.jianshu.io/upload_images/1230738-f676428cad5aa932.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

标签栏项目属性可以以编程方式设置，也可以在故事板中设置。 因为您的数据是静态的，故事板将是设置标签栏项属性的最佳选择。

在 `Main.storyboard` 中，找到 `View Controller`（现在标记为 `Item`）。 请注意，其中具有标签栏项目的选项卡栏已添加到该界面中，因为视图控制器将显示在选项卡栏控制器中。 这在布置你的界面时非常有用。

选择此选项卡栏项目并打开其属性检查器。 在 `Bar Item` 下，将 `Title` 更改为 `Map`，然后从 `Image` 菜单中选择 `MapIcon`。 您也可以通过双击画布上的文本来更改标签栏项的文本。 选项卡栏将随这些值变化而被更新（图5.13）。

**图5.13 View Controller 的标签栏项**

![](http://upload-images.jianshu.io/upload_images/1230738-fe82a6226b2ee3c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在找到 `Conversion View Controller` 并选择其标签栏项。 将 `Title` 设置为 `Convert`，将 `Image` 设置为 `ConvertIcon`。

我们还将第一个选项卡更改为 `Convert View Controller`。 选项卡的顺序由选项卡栏控件的 `viewControllers` 数组中的视图控制器的顺序确定。 您可以通过拖动 `Tab Bar Controller` 底部的选项卡来更改故事板中的顺序。
 
在画布上找到 `Tab Bar Controller`。 将 `Convert` 选项卡拖动到第一个位置。

构建并运行应用程序。 底部的标签栏不仅仅是用来看的，还可以用来切换视图，而 **ConvertViewController** 现在是第一个显示控制器（图5.14）。

**图5.14带有标签和图标的标签栏项**

![](http://upload-images.jianshu.io/upload_images/1230738-4bfabcb17823ac54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 加载和显示视图 ##

现在你有两个视图控制器，前面提到的延迟加载很重要。 应用程序启动时，标签栏控制器默认加载其数组中的第一个视图控制器的视图，即 **ConvertViewController**。 **MapViewController** 的视图不是必需的，只有当用户点击选项卡才能看到它。

你可以自己测试这个行为。 视图控制器完成加载视图时，将调用 **viewDidLoad（）**，并且您可以覆盖此方法，使其打印到控制台的消息，让您看到它被调用。

您将要添加代码到两个视图控制器。 但是，由于所有内容都使用故事板进行了配置，所以目前没有与视图控制器显示地图关联的代码。 现在您要向该视图控制器添加代码，您将要创建一个视图控制器子类并将其与该界面相关联。

创建一个新的 Swift 文件（`Command-N`）并将其命名为 `MapViewController`。 打开 `MapViewController.swift` 并定义一个名为 **MapViewController** 的 **UIViewController** 子类。

> ~~`import Foundation`~~
**`import UIKit`**
>
**`class MapViewController: UIViewController {`**
>
**`}`**

现在打开 `Main.storyboard` 并选择地图的视图控制器。 打开其身份(identity)检查器，并将 `Class` 更改为 `MapViewController`。

现在，您已将 **MapViewController** 类与画布上的视图控制器相关联，您可以在调用其 **ViewDidLoad()** 方法时将代码添加到 **ConversionViewController** 和 **MapViewController** 以打印到控制台。

在 `ConversionViewController.swift` 中，更新 **viewDidLoad()** 以将语句打印到控制台。

> `override func viewDidLoad() {`
  `super.viewDidLoad()`
>
  **`print("ConversionViewController loaded its view.")`**
>
  `updateCelsiusLabel()`
`}`

在 *MapViewController.swift* 中，覆盖相同的方法。

> **`override func viewDidLoad() {`**
  **`super.viewDidLoad()`**
>
  **`print("MapViewController loaded its view.")`**
**`}`**

构建并运行应用程序。 控制台结果说明，`ConversionViewController` 立即加载了它的视图。 点击 **MapViewController** 的选项卡，控制台将报告其视图现在加载。 在这一点上，这两个视图都被加载了，所以现在在这两个选项卡之间切换将不再触发 **viewDidLoad()**。 （尝试看看。）

## 访问子视图 ## 

通常，您需要在 `界面生成器(Interface Builder)` 中定义的子视图进行一些额外的初始化或配置，然后才能向用户显示。 那么在哪里可以访问子视图？ 有两个主要的选择，取决于你需要做什么。 第一个选项是 **viewDidLoad()** 方法，您可以忽略它来查看延迟加载。 在加载视图控制器的界面文件之后调用此方法，此时所有视图控制器的 outlets 都将引用相应的对象。 第二个选项是另一个 **UIViewController** 方法，**viewWillAppear(_ :)**。在将视图控制器的 `view` 添加到窗口之前会调用此方法。

你应该选哪个？ 如果配置只需要在应用程序运行期间完成一次，那就覆盖 **viewDidLoad()** 。 如果您需要在视图控制器的 视图 每次出现在屏幕上时完成配置， 那就覆盖 **viewWillAppear(_ :)**。

## 与视图控制器及其视图进行交互 ## 

我们来看一下在视图控制器的生命周期中调用的方法及其视图。 您已经看到过一些，而有些是新的。

- **init(coder :)** 是从故事板创建的 **UIViewController** 实例的。
 当从故事板创建视图控制器实例时，其 **init(coder:)** 被调用一次。 您将在第16章中了解有关此方法的更多信息。
- **init(nibName：bundle :)** 是 **UIViewController** 的指派构造器。 
当不使用故事板创建视图控制器实例时，其 **init(nibName：bundle :)** 被调用一次。 请注意，在某些应用程序中，您可能会最终创建同一个视图控制器类的多个实例。 每次创建该视图控制器时，此方法都将被调用一次。
- **loadView()** 以编程方式创建视图控制器的视图时，需要重写(覆盖)。
- **viewDidLoad()** 配置通过加载界面文件创建的视图时，需要重写。 在创建视图控制器的视图后，将调用此方法。
- **viewWillAppear(_ :)** 配置通过加载界面文件创建的视图时，需要重写。 每当您的视图控制器在屏幕上移动时，此方法和 **viewDidAppear(_ :)** 将被调用。
** viewWillDisappear(_ :)** 和 **viewDidDisappear（_ :)** 在每次视图控制器移出屏幕时都会被调用。

## 白银挑战：夜间模式 ##

每当显示 **ConversionViewController** 时，根据一天中的时间更新其背景颜色。 晚上，背景应该是深色。 反之，背景应该是浅色。 您将需要覆盖 **viewWillAppear(_ :)** 来完成此操作。 （你也可以在每次视图控制器视图显示时更改背景颜色。）

## 更多：Retina显示屏(Retina Display) ##

随着 iPhone 4 的发布，苹果推出了用于 iPhone 和 iPod touch 的 Retina 显示器。 Retina 显示屏与以前的设备相比具有更高的分辨率。 让我们看看你应该做什么，使图形在两个显示器上看起来最好。

对于矢量图形，您不需要做任何事情; 您的代码将适应设备而清晰地显示。 但是，如果您使用 Core Graphics 功能绘制，则这些图形在不同的设备上显示不同。 在 Core Graphics（也称为 Quartz）中，线，曲线，文本等以 点(point) 来描述。 在非 Retina 显示屏上，点为 1x1 像素。 在大多数 Retina显示屏 上，一个点为 2x2 像素（图5.15）。 例外是 5.5 英寸的 iPhone，它具有更高分辨率的 Retina 显示屏，点数为3x3像素。

**图5.15渲染不同的分辨率**

![](http://upload-images.jianshu.io/upload_images/1230738-0fe26207d3afcf3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

鉴于这些差异，如果图像不适合设备的屏幕类型，则位图图像（如JPEG或PNG文件）将不好看。 例如你的应用程序包含一个 25x25 像素的小图像。 如果此图像显示在 2x Retina 显示屏上，则图像必须被拉伸以覆盖 50x50 像素的区域。 在这一点上，系统进行了一种称为反锯齿的技术，以保持图像看起来有锯齿状。 结果会是一个非锯齿状的图像——但它是模糊的（图5.16）。

**图5.16拉伸图像的模糊性**

![](http://upload-images.jianshu.io/upload_images/1230738-fe7c914acc4defbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您可以使用较大的文件，但是当图像缩小以进行非 Retina 显示时，平均值会导致另一方向的问题。 唯一的解决方案是将两个图像文件与您的应用程序捆绑在一起：一个像素分辨率等于非 Retina 显示屏的屏幕上的点数，而 Retina 显示屏的像素数则为两倍。

幸运的是，您不必编写任何额外的代码来处理在哪个设备上加载哪个图像。 所有您需要做的是将 Asset 目录中的不同分辨率图像与单个 asset 相关联。 然后，当您使用 **UIImage** 的 **init(named:)** 初始化程序加载图像时，此方法查找该包，并获取特定设备的适当文件。