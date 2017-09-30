UITableView 显示一个 **UITableViewCell** 对象的列表。 对于许多应用程序，具有 `textLabel`，`detailTextLabel` 和 `imageView` 的基本 cell 就足够了。 但是，当您需要具有更多细节或不同布局的单元格时，可以将 **UITableViewCell** 子类化。

在本章中，您将创建一个名为 **ItemCell** 的 **UITableViewCell** 子类，将更有效地显示 **Item** 实例。 每个 cell 都将显示 **Item** 的名称，美元值 和 序列号（图12.1）。

**图12.1 具有子类化的表视图 cell 的 Homepwner**

![](http://upload-images.jianshu.io/upload_images/1230738-01a24791bf3b8265.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您可以通过将子视图添加到其 `contentView` 来自定义 **UITableViewCell** 子类的外观。 将子视图添加到 `contentView` 而不是直接到 cell 本身很重要，因为 cell 会在某些时候调整其 `contentView` 的大小。 例如，当表视图进入编辑模式时，`contentView` 将自己调整大小，为编辑控件腾出空间（图12.2）。 如果您将子视图直接添加到 **UITableViewCell**，则编辑控件会掩盖子视图。 进入编辑模式时，cell 不能调整其大小（它必须保持表视图的宽度），但是 `contentView` 可以。

**图12.2 标准和编辑模式下的表视图 cell 布局**

![](http://upload-images.jianshu.io/upload_images/1230738-f2f0ec2753b7f221.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 创建 ItemCell ##

创建一个名为 *ItemCell* 的新 Swift 文件。 在 `ItemCell.swift` 中，将 **ItemCell** 定义为 **UITableViewCell** 子类。

> ~~`import Foundation`~~
**`import UIKit`**
>
**`class ItemCell: UITableViewCell {`**
>
**`}`**

最简单的配置 **UITableViewCell** 子类的方法是通过故事板。 在第10章中，您看到表视图控制器的故事板具有 `Prototype Cells` 部分。 这是您将为 **ItemCell** 制作内容的地方。

打开 `Main.storyboard` 并在文档大纲中选择 `UITableViewCell`。 打开属性检查器，将 `Style` 更改为 `Custom`，并将 `Identifier` 更改为 **ItemCell**。

现在打开它的身份检查器，图标
![](http://upload-images.jianshu.io/upload_images/1230738-ed2c4996ffe24bbb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
。 在 `Class` 字段中，输入 **ItemCell**（图12.3）。

**图12.3更改 cell 的类**

![](http://upload-images.jianshu.io/upload_images/1230738-d995e30aa2243d60.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将 prototype cell 的高度更改为约 65 点。 您可以在画布上或通过选择表视图 cell 并从其大小检查器更改 `Row Height` 来进行更改。

**ItemCell** 将显示三个文本元素，因此将三个 **UILabel** 对象拖动到单元格上。 配置如图12.4所示。 将底部标签的文字设为稍微较小的灰色字体。 确保标签一点也不重叠。

**图12.4 ItemCell 的布局**

![](http://upload-images.jianshu.io/upload_images/1230738-7bb79888167dd474.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对这三个标签添加约束如下。

1. 选择左上角的标签并打开 `Add New Constraints` 菜单。 选择顶部和左侧支柱，然后单击 `Add 2 Constraints`。
2. 您希望左下角的标签始终与左上角的标签对齐。  右键从左下角的标签拖动到左上角的标签，然后选择 `Leading`。
3. 选中左下方的标签，打开 `Add New Constraints` 菜单，选择底部的支柱，然后单击 `Add 1 Constraint`。
4. 选择正确的标签，然后右击从右侧的控件拖动到其父视图。 选择 `Trailing Space to Container Margin` 和 `Center Vertically in Container`。
5. 选择左下角的标签并打开其尺寸检查器。 查找 `Vertical Content Hugging Priority` 并将其降低到 250.将 `Vertical Content Compression Resistance Priority` 降低到749.您将在第13章中了解这些自动布局属性的功能。
6. 您的边框可能显示在错误的位置，因此请选中三个标签，然后单击 *Update Frames* 按钮。

## 暴露 ItemCell的属性 ##

要通过 **ItemsViewController** 来配置 **TableView(_：cellForRowAt :)** 中的 **ItemCell** 的内容，cell 必须有暴露这三个标签的属性。 这些属性将通过 `Main.storyboard` 中的 outlet 连接来设置。

那么下一步就是在 **ItemCell** 上为每个子视图创建和连接 outlet。 

打开 `ItemCell.swift` 并为 outlet 添加三个属性。

> `import UIKit`
> 
`class ItemCell: UITableViewCell {`
>
  **`@IBOutlet var nameLabel: UILabel!`**
  **`@IBOutlet var serialNumberLabel: UILabel!`**
  **`@IBOutlet var valueLabel: UILabel!`**
>
`}`

你将连接三个视图的 outlet 到 **ItemCell**。 当在书中较早连接 outlet 时，您可以从故事板中的视图控制器拖动到相应的视图。 但连接 **ItemCell** 的 outlet 不是控制器上的 outlet。 它们是另一个视图中的 outlet：自定义的 **UITableViewCell** 子类。

因此，要连接 **ItemCell** 的 outlet，您将要连接到 **ItemCell**。

打开 `Main.storyboard`。 右击文档轮廓中的 *ItemCell*，并使三个 outlet 连接如图12.5所示。

**图12.5连接 outlet**

![](http://upload-images.jianshu.io/upload_images/1230738-3c249d1049bc0f9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 使用 ItemCell ##

让我们在屏幕上看到你的自定义单元格。 在 **ItemViewController** 的 **tableView(_：cellForRowAt :)** 方法中，您将在表中的每一行显示一个 **ItemCell** 的实例。

现在您正在使用自定义 **UITableViewCell** 子类，表视图需要知道每行的高度。 有几种方法可以实现此目的，但最简单的方法是将表视图的 `rowHeight` 属性设置为常量值。 本章稍后将会看到另一种方式。

打开 `ItemsViewController.swift` 并更新 **viewDidLoad()** 来设置表视图 cell 的高度。

> `override func viewDidLoad() {`
  `super.viewDidLoad()`
>
  `// Get the height of the status bar`
  `let statusBarHeight = UIApplication.shared.statusBarFrame.height`
>
  `let insets = UIEdgeInsets(top: statusBarHeight, left: 0, bottom: 0, right: 0)`
  `tableView.contentInset = insets`
  `tableView.scrollIndicatorInsets = insets`
>
  **`tableView.rowHeight = 65`**
`}`

请注意，您已经使用表格视图（使用故事板中的 prototype cell）注册了 **ItemCell**，您可以要求表格视图将具有标识符 “ItemCell” 的 cell 列出。	

在 ItemsViewController.swift 中，修改 **tableView（_：cellForRowAt :)**。

> `override func tableView(_ tableView: UITableView, cellForRowAt indexPath: NSIndexPath) -> UITableViewCell {`
  `// Get a new or recycled cell`
  ~~`let cell = tableView.dequeueReusableCell(withIdentifier: "UITableViewCell",for: indexPath)`~~
>
  **`let cell = tableView.dequeueReusableCell(withIdentifier: "ItemCell", for: indexPath) as! ItemCell`**
>
  `// Set the text on the cell with the description of the item`
  `// that is at the nth index of items, where n = row this cell`
  `// will appear in on the tableview`
  `let item = itemStore.allItems[indexPath.row]`
>
  ~~`//cell.textLabel?.text = item.name`~~
  ~~`//cell.detailTextLabel?.text = "$\(item.valueInDollars)"`~~
> 
  **`// Configure the cell with the Item`**
  **`cell.nameLabel.text = item.name`**
  **`cell.serialNumberLabel.text = item.serialNumber`**
  **`cell.valueLabel.text = "$\(item.valueInDollars)"`**
>
  `return cell`
`}`

首先，更新重用标识符以反映您的新子类。 该方法结束时的代码是相当明显的——对于单元格上的每个标签，将其 `text` 设置为相应的 **Item** 的某些属性。

构建并运行应用程序。 现在，新的 cell 加载了它们的标签，并填充了每个 **Item** 的值。

## 动态单元格高度 ##

目前，cell 的固定高度为 65 点。 更好的是根据 cell 的内容来修改它的高度。 这样，如果内容更改，表视图 cell 的高度可以自动更改。

您可以通过自动布局实现这一目标，就像您已经猜到的那样。 **UITableViewCell** 需要具有垂直约束，以确定 cell 的高度。 目前，**ItemCell** 没有足够的限制。 您需要在两个左侧标签之间添加一个约束，这些约束修正了它们之间的垂直间距。

打开 `Main.storyboard`。 右击从 `nameLabel` 拖动到 `serialNumberLabel` 并选择 `Vertical Spacing`。

现在打开 `ItemsViewController.swift` 并更新 **viewDidLoad()** 来告诉表视图它应该基于约束来计算 cell 高度。

> `override func viewDidLoad() {`
  `super.viewDidLoad()`
>
  `// Get the height of the status bar`
  `let statusBarHeight = UIApplication.shared.statusBarFrame.height`
>
  `let insets = UIEdgeInsets(top: statusBarHeight, left: 0, bottom: 0, right: 0)`
  `tableView.contentInset = insets`
  `tableView.scrollIndicatorInsets = insets`
>
  ~~`//tableView.rowHeight = 65`~~
  **`tableView.rowHeight = UITableViewAutomaticDimension`**
  **`tableView.estimatedRowHeight = 65`**
`}`

**UITableViewAutomaticDimension** 是 `rowHeight` 的默认值，所以这其实没有必要添加，这里是为了让你理解。 在表视图中设置 `estimateRowHeight` 属性可以提高性能。设置此属性可以节省某些性能成本，直到用户开始滚动而不是表视图加载时才请求每个 cell 的高度。

构建并运行应用程序。 该应用程序将看起来像以前一样。 在下一节中，您将了解一种称为 `动态类型(Dynamic Type)` 的技术，该技术适用于自动调整大小的表视图单元格。

## 动态类型 ##

创建一个能吸引每一个人的界面可能是很难的。 有些人喜欢更紧凑的界面，因此他们可以一次看到更多的信息。 其他人可能希望能够一目了然地轻松查看信息，也许他们的视力较差。 总之：人们有不同的需求。 良好的开发人员力求使应用程序能够满足这些需求。

动态类型(`Dynamic Type`) 是一种通过提供专门设计的文本样式来帮助实现这一目标的技术，这些文本样式被优化为可读性。 用户可以从 Apple 的 `设置(Settings)` 应用程序中选择七种首选文本大小之一（另外还有一些可扩展性部分中的其他更大的尺寸），支持动态类型的应用程序的字体可以适当地缩放。 在本节中，您将更新 **ItemCell** 以支持动态类型。 图12.6 显示了以最小和最大用户可选动态类型大小呈现的应用程序。

**图12.6支持动态类型的ItemCell**

![](http://upload-images.jianshu.io/upload_images/1230738-accd7e7c1c945a9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

动态类型系统以文本样式为中心。 当为给定的文本样式请求字体时，系统将考虑与文本样式相关联的用户首选文本大小以返回适当配置的字体。 图12.7显示了不同的文字样式。

**图12.7 文字样式**

![](http://upload-images.jianshu.io/upload_images/1230738-3abb23a8f32fe4bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
打开 Main.storyboard。 让我们更新标签以使用文本样式，而不是固定的字体。 选择 `nameLabel` 和 `valueLabel` 并打开属性检查器。 点击 `Font` 右边的文本图标。 对于 `Font`，选择 `Text Styles - Body` （图12.8）。 对 `serialNumberLabel` 重复相同的步骤，选择 `Caption 1` 文本样式。

**图12.8 更改文本样式**

![](http://upload-images.jianshu.io/upload_images/1230738-4ceb1094c4b2297f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在我们来更改首选的字体大小。 您可以通过 `设置(Settings)` 应用程序来执行

构建并运行应用程序。 从模拟器的 `Hardware` 菜单中，选择 `Home`。 接下来，在模拟器的主屏幕上，打开 `Settings` 应用程序。 选择 `General`，然后选择 `Accessibility`，然后选择 `Larger Text`。 （在实际的设备上，也可以通过 `Display & Brightness` → `Text Size` 在 `Settings` 中访问此菜单。）将滑块一直向左拖动以将字体大小设置为最小值（图12.9）。

**图12.9 文字大小设置**

![](http://upload-images.jianshu.io/upload_images/1230738-fb8451e9cabc162b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 （如果您切换回应用程序，使用任务切换器或通过主屏幕，将不会看到更改，您将在下一节中进行修复。）将一些 item 添加到表视图中，您将看到新的较小的字体尺寸。

## 响应用户更改 ##

当用户更改首选文本大小并返回到应用程序时，表视图将被重新加载。 不幸的是，标签不会知道新的首选文本大小。 要解决这个问题，您需要将标签根据内容大小的更改而自动调整。

打开 `ItemCell.swift` 并覆盖 **awakeFromNib()** 使标签自动调整。

> **`override func awakeFromNib() {`**
  **`super.awakeFromNib()`**
>
  **`nameLabel.adjustsFontForContentSizeCategory = true`**
  **`serialNumberLabel.adjustsFontForContentSizeCategory = true`**
  **`valueLabel.adjustsFontForContentSizeCategory = true`**
**`}`**

 从一个归档文件中加载之后，方法 **awakeFromNib()** 会被一个对象所调用，在这种情况下，它就是故事板文件。 到这个方法被调用的时候，所有的 outlet 都有值，可以被使用。

构建并运行应用程序并添加几行。 进入 `Settings`，将首选阅读尺寸更改为最大尺寸。 与以前不同，现在可以通过打开任务切换器或通过主屏幕切换回 `Homepwner`，并且表视图将更新以反映新的首选文本大小。

## 青铜挑战：cell 颜色 ##

如果值小于50，则更新 **ItemCell** 以将  `valueInDollars` 显示为绿色，如果值大于或等于 50，则显示为红色。