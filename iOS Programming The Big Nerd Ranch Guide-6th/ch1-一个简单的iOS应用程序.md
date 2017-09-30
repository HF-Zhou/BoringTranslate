在本章中，您将要编写一个名为 `Quiz` 的 iOS应用程序。 此应用程序将显示一个问题，然后在用户点击按钮时显示答案。 点击另一个按钮将向用户显示一个新问题（图1.1）。

**图1.1您的第一个应用程序：Quiz**

![](http://upload-images.jianshu.io/upload_images/1230738-86cc002d2ba018a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当您编写iOS应用程序时，您必须想到两个基本问题：

1. 如何正确创建和配置对象？ （例如：“我想要一个 `Next Question` 按钮”。）
2. 如何使我的应用程序响应用户交互？ （例如：当用户点击按钮时，我希望这段代码被执行。“）

这本书大部分都是为了回答这些问题。

## 创建一个Xcode项目 ##

打开 `Xcode`，然后从 `File` 菜单中选择 `New`→ `Project....`（如果 `Xcode` 自动打开到欢迎界面，请直接选择 `Create a new Xcode project`。

将出现一个新的工作区窗口，工作表将从工具栏向下滑动。 在顶部，找到 `iOS` 部分，然后找到 `Application` 区域（图1.2）。 被有好几个应用模板供您选择。 选择 `Single View Application`。

**图1.2创建项目**

![](http://upload-images.jianshu.io/upload_images/1230738-ef5690fbd8bd1dbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这本书是为 `Xcode 8.1` 创建的。 这些模板的名称可能随着新的 `Xcode` 版本而改变。 如果您没有看到 `Single View Application` 模板，请使用看起来最简单的模板。 

单击 `Next`，然后在下一个工作表  `Product Name` 中输入 `Quiz`（图1.3）。接着是组织名称和标识符。 您可以使用 `Big Nerd Ranch` 或任何您想要的组织名称。 对于组织标识符，您可以使用 `com.bignerdranch* 或 *com.yourcompanynamehere`。
 
从 `Language` 弹出菜单中，选择 `Swift`，然后从 `Devices` 弹出菜单中选择 `Universal`。 确保未选中 `Use Core Data` 复选框。

**图1.3配置新项目**

![](http://upload-images.jianshu.io/upload_images/1230738-92d729b667e9a138.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单击 `Next`，在最后的工作表中，将项目保存到你计划好的存储本书中例子的目录中。 单击 `Create` 以创建 `Quiz` 项目。

您的新项目将在Xcode工作区窗口中打开（图1.4）。

**图1.4 Xcode工作区窗口**

![](http://upload-images.jianshu.io/upload_images/1230738-f24c56896b8086b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

工作区窗口的左侧是 `导航区域(navigator area)` 。 该区域显示不同的导航器 - 显示项目不同部分的工具。 您可以通过选择导航器选择器中的一个图标来打开导航器，导航器选择器是导航器区域上方的栏。

当前打开的导航器是项目导航器。 项目导航器显示构成项目的文件（图1.5）。 您可以选择其中一个文件，以在导航区域右侧的编辑器区域中打开它们。

项目导航器中的文件可以分组到文件夹中，以帮助您组织项目。 模板为您创建了几个组。如果需要，您可以重命名它们，或者添加新组。 这些组只是用于组织文件，并且不以任何方式与文件系统相关。

**图1.5项目导航器中的Quiz应用程序文件**

![](http://upload-images.jianshu.io/upload_images/1230738-a067ff9e0773e03f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 模型 - 视图 - 控制器 ##

在开始应用程序之前，让我们来讨论应用程序架构中的一个关键概念： `Model-View-Controller` 或 `MVC`。 `MVC` 是iOS开发中使用的一种设计模式。 在 `MVC` 中，每个实例都属于模型层，视图层或控制器层。 （这里的层简单地指一个或多个符合相同规则的对象。）

- `模型层(model layer)` 保存数据，不了解用户界面或 UI。 在 `Quiz` 中，模型将由两个有序的字符串列表组成：一个用于提问，另一个用于解答。通常，模型层中的实例代表用户世界中的真实事物。 例如，当您为保险公司编写应用程序时，您的模型几乎肯定会包含一个名为 **InsurancePolicy** 的自定义类型。
- `视图层(view layer)` 包含对用户可见的对象。按钮，文本字段和滑块就是视图对象或视图的示例。 视图对象(view objects) 组成应用程序的UI。 在 *Quiz* 中，显示问题和答案的标签及其下的按钮都是视图对象。
- `控制器层(controller layer)` 是应用程序管理的地方。 控制器对象或控制器是应用程序的管理器。 控制器配置用户看到的视图，并确保视图和模型对象保持同步。 一般来说，控制器通常处理“然后呢？”问题。 例如，当用户从列表中选择一个项目时，控制器确定用户接下来看到什么。

图1.6显示了响应于用户输入的应用程序中的控制流程，例如用户点击按钮。

**图1.6 MVC模式**

![](http://upload-images.jianshu.io/upload_images/1230738-1ffad1823b4cba53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意，模型和视图不直接相互交流; 控制器正好位于它们的中间，接收消息和调度指令。

## 设计Quiz程序 ##

您将使用MVC模式编写 `Quiz` 应用程序。 以下是您将要创建和使用的实例的清单：

- 模型层将由 **[String]** 的两个实例组成。
- 视图层将由 **UILabel** 的两个实例和 **UIButton** 的两个实例组成。
- 控制器层将由 **ViewController** 的一个实例组成。

**图1.7Quiz对象图**

![](http://upload-images.jianshu.io/upload_images/1230738-82e9e685a8787d9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图1.7是 `Quiz` 应用程序如何工作的大图。 例如，当点击 `Next Question` 按钮时，它将触发 **ViewController** 中的一个方法。 一个方法就是一个函数——要执行的指令列表。 该方法将从问题阵列中检索一个新问题，并要求顶部标签显示该问题。

这图表在这章结整前看起来没多大意义，这并不影响后面的学习。 在构建应用程序的时候，请返回这参考这张图来查看应用程序的流程。

您将逐步构建 `Quiz` 程序，先从应用程序的可视化界面开始。

### 界面生成器 ###

您正在使用 `Single View Application` 模板，因为它是 `Xcode` 提供的最简单的模板。 不过，这个模板有很大的魔力，因为已经为您设置了一些关键组件。 现在，您只需使用这些组件，而不用深入了解它们的工作原理。 本书的其余部分将会关注这些细节。

在项目导航器中，单击一次 **Main.storyboard** 文件。 `Xcode` 将打开其称为 `Interface Builder` 的图形样式编辑器。

`Interface Builder` 将编辑器区域分为两部分：左侧的 文档概览(document outline) 和右侧的 画布(canvas)。

如图1.8所示。 如果您在编辑区域看到的内容与图形不符，可能需要单击 `Show Document Outline` 按钮。 （如果您有其他区域显示，请不用担心。）您还可能需要单击文档轮廓中的倒三角以显示内容。

**图1.8显示Main.storyboard的Interface Builder**

![](http://upload-images.jianshu.io/upload_images/1230738-077e01ff2c9ae5ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您在 `Interface Builder` 画布中看到的矩形称为 `scene`，并表示目前唯一的“屏幕”或视图（请注意，创建此项目时你使用的是 Single View Application 模板）。

在下一节中，您将了解如何使用 `Interface Builder` 为应用程序创建UI。`Interface Builder` 允许您将库中的对象拖动到画布上以创建实例，还可以在这些对象和代码之间建立连接。 这些连接可以转换成代码被用户交互调用。

`Interface Builder` 的一个关键特性是它在其它文件上并没有生成代码。 `Interface Builder` 是一个对象编辑器，可以创建对象的实例并操纵其属性。 完成编辑界面后，它不会生成与您所做的工作相对应的代码。 在必要时 .storyboard 文件会加载到内存中的对象实例的归档。

### 构建界面 ###

让我们开始你的界面。 您已经选择 `Main.storyboard` 在画布中显示的单个 scene（图1.9）。

**图1.9 Main.storyboard中的场景**

![](http://upload-images.jianshu.io/upload_images/1230738-d0f4b9759b697dcd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

要开始，请确保您的场景的尺寸适用于 iPhone 7。在画布底部，找到 `View as` 按钮。 它可能会像这样：`View as: iPhone 7 (wC hR)`。 （wC hR现在来说没有意义，我们将在第17章中解释。）如果iPhone 7 已经显示，那么表明已经设置好了。 如果没有，请单击 `View as` 按钮，然后从左侧选择第四个对应于 iPhone 7 的设备（图1.10）。

**图1.10查看 iPhone 7 的 scene**

![](http://upload-images.jianshu.io/upload_images/1230738-fb1332ffd1bf57ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在是将视图对象添加到该空白画板的时候了。

### 创建视图对象 ###

确保 `Xcode` 窗口中的实用程序区域可见。 您可能需要单击窗口右上角 ![](http://upload-images.jianshu.io/upload_images/1230738-f0c1787c1744e796.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 控件的最右边的按钮。 实用区域在编辑区右侧，有两个部分：检查器( `inspector` ) 和 库( `library` )。 顶部是检查器，它显示在编辑器区域中选择的文件或对象的设置。 底部是库，它列出可以添加到文件或项目的项。

在实用程序区域的每个部分的顶部是各种检查器和库的选择器（图1.11）。

**图1.11 Xcode实用程序区**

![](http://upload-images.jianshu.io/upload_images/1230738-b85afbc2540837a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您的应用程序界面需要四个视图对象：两个按钮来接受用户输入和两个文本标签来显示信息。 要添加它们，首先请确保您可以通过从库选择器中选择 ![](http://upload-images.jianshu.io/upload_images/1230738-228bd2d53e42d170.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 选项卡来查看对象库，如图1.11所示。

对象库包含可以添加到故事板文件中以组成界面的对象。 通过向下滚动列表或使用库底部的搜索栏来查找 `Label` 对象。 在库中选择此对象并将其拖动到画布上的视图对象上。 将标签拖到画布上并注意当标签靠近画布中心时出现的虚蓝色线条（图1.12）。 这些辅助线将帮助您部署您的界面。

**图1.12将标签(label)添加到画布**

![](http://upload-images.jianshu.io/upload_images/1230738-8d575a47c3d90c41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

根据辅助线的指引，将标签放置在视图的水平中心，靠近顶部，如图1.12所示。 最终，此标签将向用户显示问题。 将第二个标签拖到视图上并将其放置在水平中心，靠近中间。 此标签将显示答案。

接下来，在对象库中找到 `Button` 并将两个按钮拖到视图上。 在每个标签下方放置一个。

您现在已将四个视图对象添加到 **ViewController** 的UI中。 请注意，它们也出现在文档大纲中。 您的界面应如图1.13所示。

**图1.13构建Quiz界面**

![](http://upload-images.jianshu.io/upload_images/1230738-a58b48366cfdd978.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**配置视图对象**

现在您已经创建了视图对象，可以配置其属性了。 视图的某些属性（如大小，位置和文本）可以直接在画布上更改。 例如，您可以通过在画布或文档轮廓中选择对象，然后在画布中拖动其角落和边缘来调整对象的大小。

首先重命名标签和按钮。 双击每个标签并用 `???` 替换文本。 然后双击上方的按钮并将其名称更改为 `Next Question`。 重命名下面的按钮为 `Show Answer`。 结果如图1.14所示。

**图1.14重命名标签和按钮**

![](http://upload-images.jianshu.io/upload_images/1230738-2ce98b7c9f52a76c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您可能已经注意到，因为您更改了标签和按钮中的文本，因此它们的宽度已经不再整齐地居中在 scene 中。 点击并拖动它们以使它们再次居中，如图1.15所示。

**图1.15定位标签和按钮**

![](http://upload-images.jianshu.io/upload_images/1230738-8baeba4699719f23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 在模拟器上运行 ##

要测试您的UI，您将要在 `Xcode` 的 iOS模拟器 上运行 `Quiz`。

要准备在模拟器上运行 `Quiz`，请在 `Xcode` 工具栏上找到当前的方案弹出菜单（图1.16）。

**图1.16选择了iPhone 7方案**

![](http://upload-images.jianshu.io/upload_images/1230738-70cdb3fb0d16e3f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果它显示了 `iPhone 7` 一样的东西，那么这个项目就被设置为在模拟器上运行。 如果它显示 `Christian's iPhone`，那就要点击并从弹出菜单中选择 iPhone 7。 在本书中，iPhone 7 方案将是您的模拟器默认设置。

单击工具栏中的三角形播放按钮。 这将构建（编译）然后运行应用程序。 你会经常做这个，键盘快捷方式为 Command-R。

模拟器启动后，您将看到界面具有您添加的所有视图，与你在 `Interface Builder` 中看到的一样。

现在回到当前的方案弹出菜单，选择 `iPhone 7 Plus` 作为您的模拟器。 再次运行应用程序，您会注意到，您添加的视图仍然存在，但它们不像在 `iPhone 7` 一样居中。这是因为标签和按钮目前在屏幕上具有固定位置，并且不会在主视图保持居中。 要纠正此问题，您将使用一种称为 `自动布局(Auto Layout)` 的技术。

## 自动布局简介 ##

到目前为止，您的界面在 Interface Builder 画布中看起来不错。 但是，iOS设备的屏幕尺寸越来越大，而我们希望应用程序能支持所有屏幕尺寸和方向，甚至支持多种设备类型。 无论运行应用程序的设备的屏幕尺寸或方向如何，都需要保证视图对象的布局正确。 解决方法就是使用自动布局。

自动布局通过为 scene 中的每个视图对象指定位置和大小约束来工作。 这些约束可以相对应于邻近视图或容器视图。 容器视图只是一个视图对象，顾名思义，它包含另一个视图。 例如，查看 `Main.storyboard` 的文档大纲（图1.17）。

**图1.17带容器视图的文档布局**

![](http://upload-images.jianshu.io/upload_images/1230738-231a969a7f17cb13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您可以在文档大纲中看到，您添加的标签和按钮对于一个 View 对象是缩进的。 此视图对象是标签和按钮的容器，并且对象可以相对于此视图进行定位和缩放。

要开始指定自动布局约束，请通过在画布上或文档大纲中单击顶部标签来选择顶部标签。 在画布底部，注意如图1.18所示的自动布局菜单，。

**图1.18自动布局菜单**

![](http://upload-images.jianshu.io/upload_images/1230738-32a4a9c86f0fa411.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在顶部标签被选中的情况下，点击 ![](http://upload-images.jianshu.io/upload_images/1230738-37f012c4fea831ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 图标显示如图1.19所示的 对齐菜单(Align menu)。

**图1.19将容器中的顶部标签居中**

![](http://upload-images.jianshu.io/upload_images/1230738-365e522570c11946.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 `Align` 菜单中，选中 `Horizontally in Container` 复选框，以将标签居中。 然后单击 `Add 1 Constraint` 按钮。 该约束能保证在任何尺寸的屏幕上，以任何方向，标签将水平居中。

现在，您需要添加更多的约束来使下面的标签和按钮相对于顶部标签居中，并锁定它们之间的间距。 通过按住 Command 键单击选中这四个视图对象，然后单击图标 ![](http://upload-images.jianshu.io/upload_images/1230738-4b65492be2fa1f8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 打开 `Add New Constraints` 菜单，如图1.20所示。

**图1.20 添加约束以居中并修复视图之间的间距**

![](http://upload-images.jianshu.io/upload_images/1230738-dd00df2d827708a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击菜单顶部附近的红色垂直虚线段。 当您点击该段时，它将变为实心红色（如图1.20所示），表示每个视图的距离固定在最近的顶层邻居处。 另外，检查 `Align` 框，然后从弹出菜单中选择 `Horizontal Centers`。 对于 `Update Frames`，请确保已选择 `Items of New Constraints`。 最后点击菜单底部的 `Add 7 Constraints` 按钮。

如果您在添加约束时发生任何错误，您可能会在画布上看到红色或橙色的约束和边框，而不是正确的蓝线。 如果是这种情况，您将需要清除现有的约束，然后再次执行上述步骤。 要清除约束，首先选择背景（容器）视图。 然后单击该图标 ![](http://upload-images.jianshu.io/upload_images/1230738-e1f14c8aff6ca579.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 以打开 `Resolve Auto Layout Issues` 菜单。 在 `All Views in View Controller` 部分下选择 `Clear Constraints` 部分（图1.21）。 这将清除您添加的任何限制，并让您重新添加约束。

**图1.21清除约束**

![](http://upload-images.jianshu.io/upload_images/1230738-58352de47476d2b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

自动布局可能是一个难以掌握的工具，这就是为什么您在本书第一章开始使用它。 越早开始接触使用它，你将有更多的机会使用它，并习惯它。 另外，在应用程序变得复杂之前处理自动布局将有助于你更容易地去调试布局问题。

要确认您的界面正确展示，请在 iPhone 7 Plus 模拟器上构建并运行应用程序。 确认界面看起来正确后，在iPhone 7 模拟器上构建并运行应用程序。 标签和按钮在两者皆居中。

## 创建连接 ##

连接(connection) 允许一个对象知道另一个对象在内存中的位置，以便两个对象可以进行通信。 您可以在 `Interface Builder` 中进行两种连接：`outlet` 和 `action`。 `outlet` 是对象的引用。`action` 是一种方法，由用户通过按钮或滑块或选择器触发。

我们先来创建引用 **UILabel** 实例的 outlet。 是时候离开 `Interface Builder` 去编写一些代码了。

### 声明 outlet ###

在项目导航器中，找到并选中名为 `ViewController.swift` 的文件。 编辑器区域将从 `Interface Builder` 更改为 `Xcode` 的代码编辑器。

在 `ViewController.swift` 中，首先删除模板在 `class ViewController ：UIViewController {` 和 ` }`  之间自动生成的代码，使该文件如下所示：

 ```swift
import UIKit

class ViewController: UIViewController {
 	
}
 ```

（为了简方使起见，我们不会再显示该文件的 `import UIKit` 这一行 。）

接下来，添加声明两个属性的代码。 （在本书中，您将添加的新代码将以粗体显示，您将删除的代码将被删除线显示。）不用担心现在就要了解代码或属性; 这才刚刚开始


> `class ViewController: UIViewController {`

>   **`@IBOutlet var questionLabel: UILabel!`**
>   **`@IBOutlet var answerLabel: UILabel!`**

> `}`

此代码为 **ViewController** 的每个实例提供一个名为 `questionLabel` 的 `outlet` 和一个名为 `answerLabel` 的 `outlet`。 视图控制器可以使用每个 `outlet` 来引用特定的 **UILabel** 对象（即您视图中的一个标签）。 @IBOutlet 关键字告诉 `Xcode`，您将使用 `Interface Builder` 将这些 outlet 连接到标签对象。

### 设置 outlet ###

在项目导航器中，选择 `Main.storyboard` 重新打开 `Interface Builder`。

你想让 `questionLabel outlet` 指向 UI 顶部的 **UILabel** 实例。

在文档大纲中，找到其中的 `View Controller Scene` 部分和 `View Controller` 对象。 在当前的情况下，`View Controller` 代表 **ViewController** 的一个实例，它是负责管理 `Main.storyboard` 中定义的界面的对象。

右键 从文档轮廓中的 `View Controller` 拖动到场景中的顶部标签。 当标签突出显示时，松开鼠标和键盘; 将出现黑色面板。 选择 `questionLabel` 来设置 `outlet`，如图1.22所示。

**图1.22设置questionLabel**

![](http://upload-images.jianshu.io/upload_images/1230738-6063c774eea2534c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（如果在连接面板中没有看到 `questionLabel`，请仔细检查您的 `ViewController.swift` 文件以进行修改）。

现在，当加载了故事板文件时，**ViewController** 的 `questionLabel` outlet 将自动引用屏幕顶部的 **UILabel** 实例，这将允许 **ViewController** 告诉标签显示什么问题。

以相同的方式设置 `answerLabel` outlet： 右键从 `ViewController` 拖动到底部的 **UILabel** 并选择 `answerLabel`（图1.23）。

**图1.23设置answerLabel**

![](http://upload-images.jianshu.io/upload_images/1230738-2e80e34ee0332c81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意，是从要设置的 outlet 的对象拖动到要将该 outlet 指向的对象。

您的 outlet 全部设置好后，您需要的下一个连接涉及到两个按钮。

### 定义动作方法 ###

当 **UIButton** 被点击时，它会调用另一个对象的方法。 该对象称为 目标(`target`)。 触发的方法称为动作(`action`)。 该动作是该方法的名称，该方法包含要在按钮被点击时执行的代码。

在您的应用程序中，这两个按钮的目标将是 **ViewController** 的实例。 每个按钮都有自己的动作。 我们首先定义两个动作方法：**showNextQuestion（_ :)** 和 **showAnswer（_ :)**。

重新打开 `ViewController.swift`，并在 outlet 后添加两种动作方法。

> `class ViewController: UIViewController {`
>
>   `@IBOutlet var questionLabel: UILabel!`
>   `@IBOutlet var answerLabel: UILabel!`
>
>   **`@IBAction func showNextQuestion(_ sender: UIButton) {`**
>	
>    **`}`**
>	
>   **`@IBAction func showAnswer(_ sender: UIButton) {`**
>		
>   **`}`**
> `}`

在进行目标和动作连接后，您将会使用这些方法。`@IBAction` 关键字告诉 `Xcode` 您将在 `Interface Builder` 中进行这些连接。

### 设定目标和动作 ###

切换回 `Main.storyboard`。 我们先从 `Next Question` 按钮开始。 您希望将其目标设为 **ViewController**，其动作为 **showNextQuestion（_ :)**。

要设置一个对象的目标，请右键从该对象拖动到它的目标。 当您释放鼠标时，目标将被设置，并出现一个弹出菜单，让您选择一个动作。

在画布中选择 `Next Question` 按钮，然后拖动到在文档大纲的 `View Controller`。 当 `View Controller` 突出显示时，释放鼠标按钮并在弹出菜单中的 `Sent Events` 下选择 **showNextQuestion**：如图1.24所示。

**图1.24设置 Next Question 目标/动作**

![](http://upload-images.jianshu.io/upload_images/1230738-05dfe4567a170ad0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在轮到 `Show Answer` 按钮。 选择按钮，然后从按钮控制拖动到 `View Controller`。 从弹出菜单中选择 **showAnswer**：

### 连接摘要 ###

**ViewController** 和视图对象之间现在有五个连接。 您已设置属性 `answerLabel` 和 `questionLabel` 来引用标签对象——这是其中两个。 **ViewController** 是两个按钮的目标——这是另外两个。 项目的模板创建了一个附加连接： **ViewController** 的 `view` 属性连接到表示应用程序背景的 View 对象。 这就是所有的五个连接。

您可以在连接检查器中检查这些连接。 在文档大纲中选择 *View Controller*。 然后，在公用程序区域中，单击选项卡 ![](http://upload-images.jianshu.io/upload_images/1230738-cb72e66cc47a5160.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 以显示连接检查器（图1.25）。

**图1.25检查连接检查器中的连接**

![](http://upload-images.jianshu.io/upload_images/1230738-058fad34c68d60e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您的故事板文件已完成。 视图对象已创建和配置，并已对控制器对象进行了所有必要的连接。 我们继续创建和连接你的模型对象。

## 创建模型层 ##

视图对象组成UI，因此开发人员通常使用 `Interface Builder` 创建，配置和连接视图对象。 另一方面，模型层的部分通常以代码形式设置。

在项目导航器中，选择 `ViewController.swift`。 添加以下代码，声明两个字符串数组和一个整数。

> `class ViewController: UIViewController {`
>   `@IBOutlet var questionLabel: UILabel!`
>   `@IBOutlet var answerLabel: UILabel!`
> 
>   **`let questions: [String] = [`**
>     **`"What is 7+7?",`**
>     **`"What is the capital of Vermont?",`**
>     **`"What is cognac made from?"`**
>   **`]`**
>   **`let answers: [String] = [`**
>     **`"14",`**
>     **`"Montpelier",`**
>     **`"Grapes"`**
>   **`]`**
>   **`var currentQuestionIndex: Int = 0`**
> `...`
>  `}`

数组是包含问题和答案的有序列表。 整数用于跟踪用户当前的问题。

请注意，使用 `let` 关键字声明数组，而使用 `var` 关键字声明整数。 常数用 let 关键字表示; 其值不能改变。 问题和答案数组是常数。 本程序中的问题和答案不会改变，实际上只是不能从初始值改变。

另一方面，变量由 `var` 关键字表示; 它的值被允许改变。 你将 `currentQuestionIndex` 属性变成一个变量，因为它的值必须能够随着用户循环的问题和答案而改变。

### 实现动作方法 ###

现在你有问题和答案，你可以来实现它们的动作方法。 在 `ViewController.swift` 中，更新 **showNextQuestion（_ :)** 和 **showAnswer（_ :)**。

> `...`
`@IBAction func showNextQuestion(_ sender: UIButton) {`
  **`currentQuestionIndex += 1`**
  **`if currentQuestionIndex == questions.count {`**
    **`currentQuestionIndex = 0`**
  **`}`**
  **`let question: String = questions[currentQuestionIndex]`**
  **`questionLabel.text = question`**
  **`answerLabel.text = "???"`**
`}`<br>
`@IBAction func showAnswer(_ sender: UIButton) {`
  **`let answer: String = answers[currentQuestionIndex]`**
  **`answerLabel.text = answer`**
`}.`
`..`

## 加载第一个问题 ##

应用程序启动后，您将要从数组中加载第一个问题，并使用它来替换 `???` 问题标签中的占位符。 一个很好的方法是重写 **ViewController** 的 **viewDidLoad（）** 方法。 （“覆盖”表示您正在为方法提供自定义实现。）将方法添加到 `ViewController.swift`。

>`class ViewController: UIViewController {`
  `...`
  **`override func viewDidLoad() {`**
  **`super.viewDidLoad()`**
    **`questionLabel.text = questions[currentQuestionIndex]`**
  `}`
`}`

您的应用程序的所有代码现在已经完成！

## 构建应用程序 ##

如前所述，在 iPhone 7 模拟器上构建并运行应用程序。

如果构建出现任何错误，您可以通过选择导航器区域中的选项卡 ![](http://upload-images.jianshu.io/upload_images/1230738-1b61e18d143d6560.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)，在问题导航器中查看它们（图1.26）。

**图1.26带有示例错误和警告的问题导航器**

![](http://upload-images.jianshu.io/upload_images/1230738-f9e264034b87d842.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单击问题导航器中的任何错误或警告将被带到文件和发生问题的代码行。 通过将代码与本章中的代码进行比较，找出并解决任何问题（即代码拼写错误！）。 然后再次尝试运行应用程序。 重复此过程直到您的应用程序编译成功。

在您的应用程序编译完成后，它将在iOS模拟器中启动。 测试 `Quiz` 应用程序。 您应该能够点击 `Next Question` 按钮，并在顶部标签中看到一个新问题; 点击 `Show Answer*` 应该显示正确的答案。 如果您的应用程序无法正常工作，请检查 `Main.storyboard` 中的连接。

你已经建立了一个可运行的iOS应用程序，花点时间享受你的成果。

好的,享受够了。你的应用程序可以运行，但它需要一些美化和改进。

## 应用图标 ##

运行 `Quiz` 时，从模拟器菜单中选择 `Hardware` → `Home`。 你会看到 `Quiz` 图标是一个无聊的默认磁贴。 让我们给 `Quiz` 一个更好的图标。

应用程序图标是表示iOS主屏幕上的应用程序的简单图像。 不同的设备需要不同大小的图标，其中一些显示在表1.1中。

**表1.1不同设备的应用程序图标**

![](http://upload-images.jianshu.io/upload_images/1230738-e3be4307d14ef2f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们已经为 `Quiz` 应用准备了一个图标图像文件（大小为120x120）。 您可以从 `www.bignerdranch.com/solutions/iOSProgramming6ed.zip` 下载此图标（以及其他章节的资源）。 解压缩iOSProgramming6ed.zip并在解压缩的文件夹的 `0- Resources/Project App Icons` 目录中找到 `Quiz-120.png` 文件。

您将将此图标作为资源添加到应用程序包中。 一般来说，应用程序中有两种文件：代码和资源。 代码（如 `ViewController.swift`）用于创建应用程序本身。 资源是应用程序在运行时使用的图像和声音。

在项目导航器中，找到 `Assets.xcassets`。 选择此文件打开它，然后从左侧的资源列表中选择 `AppIcon`（图1.27）。

**图1.27显示Asset目录**

![](http://upload-images.jianshu.io/upload_images/1230738-5ba8ef7d0a465b56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此面板是 `Asset` 目录，您可以在其中管理应用程序所需的所有图像。

将 `Quiz-120.png` 文件从 `Finder` 拖到 `iPhone App` 部分的 `2x` 插槽上（图1.28）。 这将将文件复制到文件系统中的项目目录中，并在 Asset 目录中添加对该文件的引用。 （您可以按住 Control 键并单击 Asset 目录中的文件，然后选择在 `Show in Finder` 以确认此选项。）

**图1.28将应用图标添加到 Asset 目录中**

![](http://upload-images.jianshu.io/upload_images/1230738-30c5f31c5135119d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再次构建并运行应用程序。 通过单击 `Hardware` → `Home`，像之前一样，或使用键盘快捷键 Command-Shift-H 切换到模拟器的主屏幕(在虚拟机中 Command 键即 Win 键)。 你应该看到新的图标。

(如果没有看到图标，请关闭应用程序，然后重新构建并重新运行）。为此，最简单的选项是通过单击 `Simulator` → `Reset Content and Settings...` 这将删除所有应用程序并将模拟器重置为默认设置，您应该在下次运行应用程序时看到应用程序图标。）

## 启动屏幕 ##

现在该为项目设置 `启动图片(launch image)` 了，它在应用程序加载时显示。 launch image 在iOS中具有特定的作用：它表示应用程序正在启动，并描述了用户在应用程序加载后将进行交互的UI。 因此，良好的 launch image 是应用程序的无内容屏幕截图。 例如，`Clock` 应用程序的 launch image 显示底部的四个选项卡，全部处于未选择的状态。 一旦应用程序加载，将选择正确的选项卡，内容变得可见。 （请注意，启动图片在应用程序启动后被替换;它不会成为应用程序的背景图像。）

完成此操作的一个简单方法是允许 `Xcode` 使用 `launch screen file` 为您生成可能的 启动屏幕图像。

通过单击项目导航器中的最顶部的 Quiz 来打开项目设置。 在 `App Icons and Launch Images` 下，从 `Launch Screen File` 下拉列表中选择 `Main.storyboard`（图1.29）。 现在将从 `Main.storyboard` 生成启动图片。

**图1.29设置启动屏幕文件**

![](http://upload-images.jianshu.io/upload_images/1230738-37bbae7eb970606c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

很难看到这种更改的结果，因为 启动图片 通常只在很短的时间内显示。然而，尽管它的角色是如此的短暂，但它仍然是一个良好的实践。

恭喜！ 你已经写了你的第一个应用程序，甚至添加了一些细节来改进它。 您将在本书后再次用到该 *Quiz* 应用程序。 下一章将为你介绍一些 Swift 编程的基础知识。
