通常，您希望应用程序的界面根据屏幕的尺寸和方向而显示不同的布局。 在本章中，您将修改 `Homepwner` 中的 **DetailViewController** 的界面，以便当它出现在具有相对较小高度的屏幕上时，文本字段集合和图像视图并排而不是堆叠在一起 图17.1）。

**图17.1 Homepwner 的 DetailViewController 的两个布局**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.1.png)

屏幕的相对大小以 `size class` 定义。 size class 表示给定维度中屏幕空间的相对数量。 每个尺寸（宽度和高度）可以是紧凑的或正常的，因此有四种可能的 size class 组合：

> `Compact Width | Compact Height`
> `紧凑的宽度|紧凑的高度`
3.5 英寸或 4 英寸 或4.7英寸 iPhone 横向 

> `Compact Width | Regular Height`
> `紧凑的宽度|正常的高度`
所有尺寸的 iPhone  纵向 

> `Regular Width | Compact Height`
>`正常宽度 紧凑的高度`
5.5英寸屏幕 iPhone 横向 

> `Regular Width | Regular Height`
> `正常的宽度|正常的高度`
所有尺寸的 iPad 任意方向

请注意，size class 涵盖屏幕尺寸和方向。 不要单单考虑设备或界面的方向，最好是考虑 size class。

## 修改特定 size class 的特征 ##

编辑特定 size class 组合的界面时，你可以更改：

- 一些视图属性
- 是否包含了特定的子视图
- 是否包含了特定的约束
- 约束
- 用于显示文字的子视图的字体

在 `Homepwner` 中，您将重点关注该列表中的第一项——根据 size class 配置调整视图属性。 目标是使
 image view 在紧凑的高度环境中位于标签和文本字段的右侧。 在 正常 的高度环境中，image view 将在标签和文本字段之下（如当前所示）。 使用栈视图很非常容易就能做到。

首先，您将将现有的垂直栈视图嵌入到另一个栈视图中。 这样可以轻松地将 image view 添加到标签和文本字段的右侧。

打开 `Homepwner.xcodeproj` 和 `Main.storyboard`。 选择 `vertical stack view`，然后单击![](http://upload-images.jianshu.io/upload_images/1230738-2d627ac6e008aa93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)图标将此堆栈视图嵌入到另一个堆栈视图中。 选择此新的堆栈视图后，打开 `Add New Constraints` 菜单，如图17.2所示进行配置，并添加约束。

**图17.2 堆栈视图约束**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.2.png)

接下来，打开新的堆栈视图的属性检查器。 增加 `Spacing` 为 8。

现在，您将要将图像视图从内部堆栈视图移动到刚创建的外部堆栈视图。 这就是您将如何使 image view 显示在界面的右侧：在紧凑的高度环境中，堆栈视图将被设置为水平，image view 将填充右侧的界面。

将 image view 从一个堆栈视图移动到另一个堆栈视图可能有点棘手，因此您将在几个步骤中执行此操作。

打开文档大纲并展开 `Detail View Controller Scene` 部分。 展开外部两个堆栈视图，如图17.3所示。

**图17.3 扩展文档大纲**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.3.png)

将 `Image View` 拖动到当前包含的堆栈视图的上方（图17.4）。 这将从内部堆栈视图移动到外部堆叠视图。

**图17.4 将图像视图移动到外部堆叠视图**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.4.png)

最后，折叠内部堆栈视图并将 `Image View` 拖动到堆栈中（图17.5）。 确保 `Image View` 缩进在与内部堆栈视图相同级别上。

您可能需要 `update frames`，以摆脱任何警告。

**图17.5 将图像视图移动到内部堆栈视图下方**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.5.png)

构建并运行应用程序。 确认堆栈视图的行为不变。

在这一点上，您已经更新了所有 size class 通用的所有内容。 接下来，您将修改特定的 size class 以更改内容的布局。

在 `Interface Builder` 的底部，单击 `View as: iPhone 7(wC hR)` 以展开视图选项。 然后选择 `Orientation` 方向（图17.6）。 让 `Device` 为 iPhone 7。

**图17.6 DetailViewController 在 iPhone 7 中显示**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.6.png)

接下来，您将更新外部堆栈视图的属性，以使 Image View 位于右侧。

选择外部堆栈视图并打开其属性检查器。 在 `Stack View` 部分下，找到 `Axis` 属性，然后单击其左侧的 `+` 按钮。 从弹出式菜单中，为 `Width` 变化选择 `Any`，对于 `Height` 变化选择 `Compact`（图17.7）。 单击 `Add Variation`。 这将允许您自定义所有 iPhone 的轴属性。

**图17.7添加 size class 选项**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.7.png)

对于新选项（`hC`），选择 `Horizontal`（图17.8）。 现在，每当界面具有紧凑的高度时，外层堆栈视图将配置为水平。 当界面具有正常高度时，外部堆栈视图将配置为垂直。

**图17.8 自定义 轴(axis)**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.8.png)

你想要做的最后一个变化是内部堆栈视图和 Image View 平均地填充外层堆栈视图。 为此，您将自定义外部堆栈视图的 分布(Distribution)。

对于外部堆栈视图，属性检查器仍然打开，单击 `Distribution` 旁边的 `+`，然后从弹出菜单中再次选择 `Width` 属性为 `Any` 和 `Height` 属性为 `Compact`。 将此 size class 的 分布 更改为 `Fill Equally`（图17.9）。

**图17.9 定制 distribution**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.9.png)

构建并运行应用程序。 选择一个 item 并显示其详细信息以添加一张照片（如果没有）。 在纵向和横向之间旋转（在模拟器上，您可以使用 Command 加上向左或向右箭头键进行旋转），并注意界面在各种情况下是如何显示的。

您的 `Homepwner` 应用程序已完成。 您已经构建了一个具有灵活界面的应用程序，可以拍照和存储数据，我们希望您为您的成就感到自豪！ 值得庆祝！

## 青铜挑战：堆栈的文本字段和标签 ##

在紧凑的高度环境中，使堆村的文本字段和标签垂直而不是水平（图17.10）。

**图17.10堆栈的文本字段和标签**

![](http://git.oschina.net/JoeHongfa/images/raw/master/ios%20Programming%206th/figure17.10.png)