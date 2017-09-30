许多 iOS 应用程序向用户显示列表项，并允许用户选择，删除或重新排列列表项。 不管是显示用户地址簿中的人员列表的应用程序还是 App Store 上畅销产品的列表，都使用了 **UITableView**。

**UITableView** 显示具有可变数量行的单列数据。 图10.1显示了一些 **UITableView** 的例子。

**图10.1 UITableView示例**

![](http://upload-images.jianshu.io/upload_images/1230738-da717240ac08059b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 开始开发 Homepwner 应用程序 ##

在本章中，您将启动一个名为 `Homepwner` 的应用程序，该应用程序保留了所有财产的清单。 在发生火灾或其他灾难的情况下，您将有保险公司的记录。 （“Homepwner”）顺便说一下，不是打字错误，如果您需要 “pwn” 一词的定义，请访问 www.wiktionary.org。）

到目前为止，您的 iOS 项目一直很小，但是 `Homepwner` 将在八章中成长为一个很复杂的应用程序。 在本章结尾，`Homepwner` 将在 **UITableView** 中显示 **Item** 实例列表，如图10.2所示。

**图10.2 Homepwner：阶段1**

![](http://upload-images.jianshu.io/upload_images/1230738-827332f9312e6577.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

首先，请打开 `Xcode` 并创建一个新的 iOS `Single View Application` 项目。 配置如图10.3所示。

**图10.3 配置Homepwner**

![](http://upload-images.jianshu.io/upload_images/1230738-9d0e06cd657f924a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## UITableViewController ##

**UITableView** 是一个视图对象。 回想一下，每个 iOS 开发人员尽力遵循 MVC 设计模式，每个类都属于以下类别之一：

- `model`：保存数据，不了解UI
- `view`：对用户可见，对模型对象一无所知
- `controller`：保持UI和模型对象同步并控制应用程序的流程

作为视图对象，**UITableView** 不处理应用程序逻辑或数据。 使用 **UITableView** 时，您必须考虑在应用程序中使表工作所需的其他功能：

- **UITableView** 通常需要一个视图控制器来处理其在屏幕上的外观。
- **UITableView** 需要一个数据源。 **UITableView** 请求其数据源获得要显示的行数，要显示在这些行中的数据以及使 **UITableView** 成为有用 UI 的其他东西。 没有数据源，表视图只是一个空容器。 只要符合 **UITableViewDataSource** 协议，**UITableView** 的 `dataSource` 可以是任何类型的对象。
- **UITableView** 通常需要一个可以通知其他对象涉及到 **UITableView** 事件的 委托(`delegate`)。 只要符合 **UITableViewDelegate** 协议，委托可以是任何对象。

**UITableViewController** 类的实例可以满足所有三个角色：视图控制器，数据源和委托。

**UITableViewController** 是 **UIViewController** 的一个子类，因此具有一个视图。 **UITableViewController** 的视图通常是 **UITableView** 的一个实例，**UITableViewController** 处理 **UITableView** 的准备和呈现。

当 **UITableViewController** 创建其视图时，**UITableView** 的 `dataSource` 和 `delegate` 属性将自动设置为指向 **UITableViewController**（图10.4）。

**图10.4 UITableViewController-UITableView 关系**

![](http://upload-images.jianshu.io/upload_images/1230738-089b782db4ec05b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 子类化UITableViewController ###

您将在 `Homepwner` 中创建 **UITableViewController** 的子类。 创建一个名为 `ItemsViewController` 的新的 Swift 文件。 在 `ItemsViewController.swift` 中，定义一个名为 **ItemsViewController** 的 **UITableViewController** 子类。

> ~~`import Foundation`~~
**`import UIKit`**
>
**`class ItemsViewController: UITableViewController {`**
>
**`}`**

打开 `Main.storyboard`。 您希望初始视图控制器是一个表视图控制器。 在画布上选择现有的 `View Controller`，然后按 `Delete` 删除。 然后将 `Table View Controller` 从对象库拖动到画布上。 选择 `Table View Controller` 后，打开其身份检查器，将 `class` 更改为 `ItemsViewController`。 最后，打开 `Items View Controller` 的属性检查器，并选中 `Is Initial View Controller`。

构建并运行您的应用程序。 您应该看到一个空的表视图，如图10.5所示。 作为 **UIViewController** 的子类，**UITableViewController** 继承了 `view` 属性。 当第一次访问此属性时，将调用 **loadView()** 方法，该方法创建并加载视图对象。 **UITableViewController** 的视图始终是 **UITableView** 的一个实例，因此可以用 **UITableViewController** 的 `view` 属性来获得明亮，有光泽和空的表视图。

**图10.5 空的UITableView**

![](http://upload-images.jianshu.io/upload_images/1230738-533ea751f54f3951.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您不再需要模板为您创建的 *ViewController.swift* 文件。 在项目导航器中选择此文件，然后
`Delete` 删除。

## 创建 Item 类 ##

您的表视图需要显示一些行。 表格视图中的每一行将显示一个信息项，例如包含 名称(name)，序列号(serial number) 和 美元值(value in dollars)。

创建一个名为 `Item` 的新的Swift文件。 在 `Item.swift` 中，定义 **Item** 类并赋予它四个属性。

> ~~**`import Foundation`**~~
**`import UIKit`**
>
**`class Item: NSObject {`**  
  **`var name: String`**
  **`var valueInDollars: Int`**
  **`var serialNumber: String?`**
  **`let dateCreated: Date`**
`}`

**Item** 继承自 **NSObject**。 **NSObject** 是大多数 Objective-C 类继承的基类。 您所使用的所有 `UIKit` 类——**UIView**，**UITextField** 和 **UIViewController**，仅举几例——直接或间接地从 **NSObject** 继承。 当需要与运行时系统进行交互时，您自己的类通常需要从 **NSObject** 继承。

请注意，`serialNumber` 是可选的字符串，因为 item 可能没有序列号。 另外，请注意，没有一个属性具有默认值。 您将需要在指定的构造器中给它们赋值。

### 自定义构造器 ###

您在第2章中了解了结构体的构造器。在结构体上的构造器非常简单，因为结构体不支持继承。 相反，类构造器支持继承。

类可以有两种构造器：`指派构造器(designated initializers)` 和 `便利构造器(convenience initializers)`。

指派构造器 是该类的主要构造器。 每个类都至少有一个 指派构造器。 指派构造器 可确保类中的所有属性都具有值。 一旦它确保了，指派初构造器 就会在其父类(如果有的话)上调用 指派构造器。

在 **Item** 类上实现一个新的 指派构造器，它设置所有属性的初始值。

> `import UIKit`
> 
`class Item: NSObject {`
  `var name: String`
  `var valueInDollars: Int`
  `var serialNumber: String?`
  `let dateCreated: Date`
>
  **`init(name: String, serialNumber: String?, valueInDollars: Int) {`**
    **`self.name = name`**
    **`self.valueInDollars = valueInDollars`**
    **`self.serialNumber = serialNumber`**
    **`self.dateCreated = Date()`**
>
    **`super.init()`**
  **`}`**
`}`

该构造器接收 `name`，`serialNumber` 和 `valueInDollars` 参数。 由于参数名称和属性名称相同，因此您必须使用 `self` 来区分属性和参数。

既然您已经实现了自己的自定义构造器，那么您将丢失该类的默认构造器 **init()**。 当您的所有类的属性都具有默认值，而不需要额外的工作来创建新实例时，默认的构造器很有用。 **Item** 类不符合此条件，因此您已经为该类声明了一个自定义初始化器。

每个类必须至少有一个 指派构造器，但 便利构造器 是可选的。 你可以将 便利构造器 视为助手。 一个 便利构造器 总是在同一个类上调用另一个初始化器。 初始化器名称前面的 *convenience* 关键字表示 便利构造器。

添加一个 便利构造器 到 **Item** 类中，用于随机生成 item。

> **`convenience init(random: Bool = false) {`**
  **`if random {`**
    **`let adjectives = ["Fluffy", "Rusty", "Shiny"]`**
    **`let nouns = ["Bear", "Spork", "Mac"]`**
>
    **`var idx = arc4random_uniform(UInt32(adjectives.count))`**
    **`let randomAdjective = adjectives[Int(idx)]`**
>
    **`idx = arc4random_uniform(UInt32(nouns.count))`**
    **`let randomNoun = nouns[Int(idx)]`**
>
    **`let randomName = "\(randomAdjective) \(randomNoun)"`**
    **`let randomValue = Int(arc4random_uniform(100))`**
    **`let randomSerialNumber = UUID().uuidString.components(separatedBy: "-").first!`**
>
    **`self.init(name: randomName,serialNumber: randomSerialNumber, valueInDollars: randomValue)`**
  **`} else {`**
      **`self.init(name: "", serialNumber: nil, valueInDollars: 0)`**
  **`}`**
**`}`**

如果 `random` 为 `true`，则实例配置有随机名称，序列号和值。 （**arc4random_uniform** 函数返回 0(包括0) 和 作为参数传递的值(不包括该值)之间的随机值。）请注意，在条件的两个分支的末尾，您正在调用到 **Item** 的 指派构造器。 便利构造器 必须调用相同类型的另一个构造器，而 指派构造器 必须在其父类上调用 指派构造器。

**Item** 类已准备就绪了。 在下一部分中，您将在表视图中显示一个 **Item** 的实例数组。

## UITableView 的数据源 ##

在 Cocoa Touch（用于构建iOS应用程序的框架集合）中为 **UITableView** 提供数据行的过程与典型的 过程编程 不同。 在过程编程中，您可以告诉表格视图应该显示什么。 而在 Cocoa Touch 中，表视图会询问另一个对象——它的 `dataSource`——它应该显示什么。 在当前情况下， **ItemsViewController** 是数据源，因此需要一种存储 item 数据的方法。

您将使用一个数组来存储 **Item** 实例，但是要注意。 保存 **Item** 实例的数组将被抽象为另一个对象 - 一个 **ItemStore**（图10.6）。

**图10.6 Homepwner 对象图**

![](http://upload-images.jianshu.io/upload_images/1230738-6761bfc03b2e2235.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果一个对象想要查看所有的 item，它会询问包含它们的数组的 **ItemStore**。 在将来的章节中，store 将负责对数组执行操作，如重新排序，添加和删除项目。 它还将负责从磁盘保存和加载 item。

创建一个名为 **ItemStore** 的新的 Swift 文件。 在 `ItemStore.swift` 中，定义 **ItemStore** 类并声明一个属性来存储 **Item** 列表。

> ~~`import Foundation`~~
**`import UIKit`**
>
**`class ItemStore {`**
  **`var allItems = [Item]()`**
**`}`**

**ItemStore** 是一个 Swift 基类(`base class`) - 它不会继承任何其他类。 与之前定义的 **Item** 类不同，**ItemStore** 不需要 **NSObject** 提供的任何行为。

**ItemViewController** 将在创建一个新的 **Item** 时 调用 **ItemStore** 的方法。 **ItemStore** 将强制创建对象并将其添加到 **Item** 的实例数组中。

在 `ItemStore.swift` 中，实现 **createItem()** 来创建并返回一个新的 **Item**。

> **`@discardableResult func createItem() -> Item {`**
  **`let newItem = Item(random: true)`**
>
  **`allItems.append(newItem)`**
>
  **`return newItem`**
**`}`**

`@discardableResult` 注释意味着此函数的调用者可以自由地忽略调用此函数的结果。 看看下面的代码列表来说明这个效果。

> `// This is OK`
`let newItem = itemStore.createItem()`
>
`// This is also OK; the result is not assigned to a variable`
`itemStore.createItem()`

### 让控制器访问 ItemStore ###

在 ItemsViewController.swift 中， 添加一个 **ItemStore** 属性。

> `class ItemsViewController: UITableViewController {`
> 
  **`var itemStore: ItemStore!`**
`}`

现在，您应该在 **ItemsViewController** 实例哪个位置设置此属性呢？ 应用程序首次启动时，**AppDelegate** 的 **application(_：didFinishLaunchingWithOptions :)** 方法被调用。 **AppDelegate** 在 `AppDelegate.swift` 中声明，顾名思义，该应用程序用作应用程序本身的委托。 它负责处理应用程序所经历的状态的变化。 您将在第16章中了解 **AppDelegate** 和应用程序所经历的更多信息。

打开 `AppDelegate.swift`。 访问 **ItemsViewController** （它将是窗口的 根视图控制器(rootViewController)），并将其 `ItemStore` 属性设置为 **ItemStore** 的新实例。

> `func application(_ application: UIApplication, didFinishLaunchingWithOptions`
    `launchOptions: [UIApplicationLaunchOptionsKey : Any]?) -> Bool {`
  `// Override point for customization after application launch.`
>
  **`// Create an ItemStore`**
  **`let itemStore = ItemStore()`**
>
  **`// Access the ItemsViewController and set its item store`**
  **`let itemsController = window!.rootViewController as! ItemsViewController`**
  **`itemsController.itemStore = itemStore`**
>
  `return true`
`}`

最后，在 `ItemStore.swift` 中，实现 指派构造器 来添加五个随机 item。

> **`init() {`**
  **`for _ in 0..<5 {`**
    **`createItem()`**
  **`}`**
**`}`**

如果 **createItem()** 没有用 `@discardableResult` 注释，那么这个函数的调用就必须像这样：

> `// Call the function, but ignore the result`
`let _ = createItem()`

此时您可能会想知道为什么 `itemStore` 要在 **ItemsViewController** 外部设置。 为什么 **ItemsViewController** 实例本身没有创建一个 store 的实例？ 这种方法的原因是基于一个相当复杂的内容叫做 `依赖反转原则(dependency inversion principle)`。 这个原则的基本目标是通过反转它们之间的某些依赖关系来解耦应用程序中的对象。 这会生成更强大和可维护的代码。

依赖反转原则指出：

1. 高级对象不应该依赖于低级对象。 两者都应该依赖于抽象。
2. 抽象不应该依赖细节。 细节应该取决于抽象。

`Homepwner` 中的依赖反转原则所要求的抽象是 store 的概念。一个 store 是一个较低级别的对象，它通过只有该类知道的细节来检索和保存 **Item** 实例。 **ItemsViewController** 是一个更高级别的对象，只知道它将被提供一个实用程序对象（store），从该对象可以获取 **Item** 实例的列表，并且它可以通过新的或更新的 **Item** 实例来持久存储。 这将导致解耦，因为 **ItemsViewController** 不依赖于 **ItemStore**。 实际上，只要 store 是抽象的，**ItemStore** 可以被另外一个不同的 **Item** 对象取代（例如通过使用 Web服务），而 **ItemViewController** 不需要任何的修改。

实现依赖反转原则时使用的常用模式是 `依赖注入(dependency injection)`。 在最简单的形式中，较高级别的对象不会假定他们需要使用哪些较低级的对象。 相反，它们是通过构造器或属性来传递的。 在 **ItemsViewController** 的实现中，您通过一个属性来注入它来给它一个 store。

## 实现数据源方法 ##

现在 store 里有一些 item，你需要在 **ItemViewController** 中将这些 item 转换成 **UITableView** 可以显示的行。 当 **UITableView** 想知道要显示的内容时，它会从 **UITableViewDataSource** 协议中声明的一组方法中调用方法。

打开文档并搜索 **UITableViewDataSource** 协议参考。 向下滚动到 `Configuring a Table View` 部分（图10.7）。

**图10.7 UITableViewDataSource协议文档**

![](http://upload-images.jianshu.io/upload_images/1230738-329d25603ff9e142.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 `Configuring a Table View` 部分中，请注意，其中两个方法标记为 `Required`。 因为 **ItemsViewController** 符合 **UITableViewDataSource**，所以它必须实现 **tableView(_：numberOfRowsInSection :)** 和 **tableView(_：cellForRowAt :)**。 这些方法告诉表视图应该显示多少行以及在每行中显示的内容。

每当 **UITableView** 显示时，它会在其 `dataSource` 上调用一系列方法（必需的方法以及已经实现的任何可选的方法）。 必需的方法 **tableView(_：numberOfRowsInSection :)** 返回 **UITableView** 应显示的行数的整数值。 在 `Homepwner` 的表视图中，store 中的每个 item 应该为一行。

在 `ItemsViewController.swift` 中，实现 **tableView(_:numberOfRowsInSection:)**。

> **`override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {`**
  **`return itemStore.allItems.count`** 
**`}`**

想知道关于这个方法所指的 `section` 吗？ 表视图可以分为几个部分，每个部分都有自己的一组行。 例如，在通讯录中，以 “C” 开头的所有名称在一个 section 中。 默认情况下，一个表视图会有一个 section，在本章中，您将只使用一个。 一旦您了解表视图的工作原理，就不难使用多个 section。 实际上，使用多个 section 是本章结尾的第一个挑战。

**UITableViewDataSource** 协议中的第二个必需方法是 **tableView(_：cellForRowAt :)**。 要实现此方法，您需要了解另一个类——**UITableViewCell**。

## UITableViewCells ##

表视图的每一行都是一个视图。 这些视图是 **UITableViewCell** 的实例。 在本节中，您将创建 **UITableViewCell** 的实例来填充表视图。

一个 单元(cell) 本身有一个子视图——它的 `contentView`（图10.8）。 `contentView` 是 cell 内容的父视图。 cell 还可以具有 附件视图(accessory view)。

**图10.8 UITableViewCell 布局**

![](http://upload-images.jianshu.io/upload_images/1230738-f242fcb8334f4007.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

附件视图显示面向操作的图标，例如复选标记，公开图标或信息按钮。 这些图标可通过预定义的常数来修改附件视图的外观。 默认值为 **UITableViewCellAccessoryType.none**，这是本章要使用的。 您将在第23章再次看到附件视图（现在感兴趣的话请参阅 **UITableViewCell** 的文档以了解更多详细信息。）

一个 **UITableViewCell** 的真正有用的是 `contentView`，它有三个子视图（图10.9）。 其中两个子视图是 **UILabel** 实例，它们是名为 `textLabel` 和 `detailTextLabel` 的 **UITableViewCell** 的属性。 第三个子视图是一个称为 `imageView` 的 **UIImageView**。 在本章中，您将使用 `textLabel`和 `detailTextLabel`。

**图10.9 UITableViewCell层级**

![](http://upload-images.jianshu.io/upload_images/1230738-61108310a01b44ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每个 cell 还有一个 **UITableViewCellStyle**，它决定了哪些子视图被使用，以及它们在 `contentView` 中的位置。 这些样式及其常量的示例如图10.10所示。

**图10.10 UITableViewCellStyle：样式和常量**

![](http://upload-images.jianshu.io/upload_images/1230738-6ddff6da6c34827c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 创建和检索 UITableViewCells ###

现在，每个 cell 将以 `textLabel` 的形式显示 **Item** 的 `name`，并将 **Item** 的 `itemInDollars` 作为 `detailTextLabel` 显示。 为了实现这一点，您需要实现 **UITableViewDataSource** 协议第二个必需的方法 **tableView(_：cellForRowAt :)**。 此方法将创建一个 cell，将其 `textLabel` 设置为 **Item** 的 `name`，将其 `detailTextLabel` 设置为 **Item** 的 `valueInDollars`，并将其返回到 **UITableView**（图10.11）。

**图10.11 检索 UITableViewCell**

![](http://upload-images.jianshu.io/upload_images/1230738-1e1971d6bd2e387c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你如何决定哪一个 Item 对应哪一个 cell？ 发送给 **tableView(_：cellForRowAt :)** 的参数之一是 **IndexPath**，它具有两个属性： `section` 和 `row`。 当在数据源上调用此方法时，表视图会询问 “我可以在X，Y行中显示一个单元格吗？”，因为本练习中只有一个部分，您的实现只会涉及到索引路径的行。

在 `ItemsViewController.swift` 中，实现 **tableView（_：cellForRowAt :)**，使第 `n` 行显示 `allItems` 数组中的第 `n` 个 item。

> **`override func tableView(_ tableView: UITableView,cellForRowAt indexPath: IndexPath) -> UITableViewCell {`**
  **`// Create an instance of UITableViewCell, with default appearance`**
  **`let cell = UITableViewCell(style: .value1, reuseIdentifier: "UITableViewCell")`**
>
  **`// Set the text on the cell with the description of the item`**
  **`// that is at the nth index of items, where n = row this cell`**
  **`// will appear in on the tableview`**
  **`let item = itemStore.allItems[indexPath.row]`**
>
  **`cell.textLabel?.text = item.name`**
  **`cell.detailTextLabel?.text = "$\(item.valueInDollars)"`**
>
  **`return cell`** 
**`}`**

现在构建并运行应用程序，您将看到一个 **UITableView**，其中包含了一个 item 列表。

### 重用 UITableViewCells ###

iOS 设备的内存量有限。 如果您在 **UITableView** 中显示了数千个 item 的列表，那么您将有数千个 **UITableViewCell** 的实例。 大多数这些 cell 将不必要地占用内存。 毕竟，如果用户无法在屏幕上看到一个 cell，那么该 cell 没有理由占用内存。

为了节省内存并提高性能，您可以重用表视图 cell。 当用户滚动表格时，某些 cell 将在屏幕外移动。 将屏幕外的 cell 放入可用于再利用的 cell 中。 然后，数据源首先检查 cell 池，而不是为每个请求创建一个全新的单元。 如果有一个未使用的 cell，数据源将使用新数据进行配置并将其返回给表视图（图10.12）。

**图10.12 UITableViewCell的可重用实例**

![](http://upload-images.jianshu.io/upload_images/1230738-db03a7fe46719081.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有一个问题需要注意：有时 **UITableView** 会有不同类型的 cell。 有时候，您会将 **UITableViewCell** 子类化以创建特殊的外观或行为。 然而，在可重用 cell 池周围浮动的不同子类有可能会导致返回错误类型的单元格。 您必须确保返回的 cell 的类型，以便您可以确定其具有的属性和方法。

请注意，您不需要关心从池中取出的 cell 是什么类型的，因为您将要更改 cell 内容。 您需要的是特定类型的 cell。 好消息是每个单元格都有一个类型为 **String** 的 `reuseIdentifier` 属性。 当数据源向表视图询问可重用的 cell 时，它传递一个字符串，并说：“我需要一个具有这种重用标识符的 cell。”按照惯例，重用标识符通常是单元类的名称。

要重用 cell，您需要使用表视图注册 cell 原型或类，以获取特定的重用标识符。 您将注册默认的 **UITableViewCell** 类。 你告诉表格视图，“嘿，任何时候，我要求一个这个重用标识符的 cell，给我一个这个特定类的 cell。” 表视图将从重用池给你一个 cell 或如果在重用池中没有该类型的 cell 则实例化一个新的。

打开 `Main.storyboard`。 请注意，在表格视图中有一个 `Prototype Cells` 的部分（图10.13）。

**图10.13原型单元格**

![](http://upload-images.jianshu.io/upload_images/1230738-997a8aff05ac4b32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这里，您可以配置相关表格视图所需的不同类型的 cell。 如果您正在创建自定义 cell，那么您将在其中设置 cell 的界面。 **ItemsViewController** 只需要一种 cell，并且现在使用其中一种内置样式就足够了，因此您只需要在已经在画布上的 cell 上配置一些属性。

选择原型 cell 并打开其属性检查器。 将 `Style` 更改为 `Right Detail`（对应于 **UITableViewCellStyle.value1**），并给它一个 `UITableViewCell` 的 `Identifier`（图10.14）。

**图10.14表视图 cell 属性**

![](http://upload-images.jianshu.io/upload_images/1230738-14d648fd85ebfbc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来，在 `ItemsViewController.swift` 中，更新 **tableView（_：cellForRowAt :)** 来重用单元格。

> `override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {`
  ~~`// Create an instance of UITableViewCell, with default appearance`~~
  ~~`//let cell = UITableViewCell(style: .value1, reuseIdentifier: "UITableViewCell")`~~
>
  **`// Get a new or recycled cell`**
  **`let cell = tableView.dequeueReusableCell(withIdentifier: "UITableViewCell", for: indexPath)`**
>
  `...`
`}`

**dequeueReusableCell(withIdentifier：for :)** 方法将检查 cell 的池或队列，以查看具有正确重用标识符的 cell 是否已存在。 如果是这样，它会 “出队(dequeue)” 那个 cell。 如果没有现有 cell，将创建并返回一个新的 cell。

构建并运行应用程序。 应用程序的行为应该保持不变。 重新使用 cell 意味着您只需要创建少量的cell，这对内存的要求较低。 您的应用程序的用户(及其设备)将会感谢您。

## Content Insets ##

正如您在本章中一直运行应用程序一样，您可能已经注意到，第一个表视图 cell 处于状态栏下方（图10.15）。 您创建的应用程序的界面填满了设备的整个窗口。 状态栏(如果可见)都会放置在界面顶部，因此您的界面必须考虑状态栏的位置。

**图10.15 表视图 cell 与状态栏重叠**

![](http://upload-images.jianshu.io/upload_images/1230738-b79f788e499db4cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

要使表格视图 cell 不会覆盖状态栏，您将在表视图的顶部添加一些填充。 **UITableView** 是 **UIScrollView** 的子类，它从中继承 `contentInset` 属性。 您可以将 content insets 想象为滚动视图的四条边的 填充(padding)。

在 `ItemsViewController.swift` 中，覆盖 **viewDidLoad()** 来更新表视图 content inset。

> **`override func viewDidLoad() {`**
> 
  **`super.viewDidLoad()`**
  **`// Get the height of the status bar`**
  **`let statusBarHeight = UIApplication.shared.statusBarFrame.height`**
>	
  **`let insets = UIEdgeInsets(top: statusBarHeight, left: 0, bottom: 0, right: 0)`**
  **`tableView.contentInset = insets`**
  **`tableView.scrollIndicatorInsets = insets`**
 **`}`**

表视图的顶部被赋予了与状态栏高度相等的 content inset。 当表视图滚动到顶部时，这将使内容显示在状态栏的下方。 滚动指示器也会显示在状态栏下方，因此您可以给他们相同的 inset，让它们出现在状态栏的正下方。

请注意，您可以访问 **ItemsViewController** 上的 `tableView` 属性以获取表视图。 该属性从 **UITableViewController** 继承，并返回控制器的表视图。 虽然您可以通过访问 **UITableViewController** 的视图来获取相同的对象，但是使用 `tableView` 会告诉编译器返回的对象将是 **UITableView** 的一个实例。 因此，调用特定于 **UITableView** 的方法或访问特定于 **UITableView** 的属性不会产生错误。

构建并运行应用程序。 当表视图滚动到顶部时，表视图单元格内容不再和状态栏重叠（图10.16）。

**图10.16 具有合适的 content inset 的表视图**

![](http://upload-images.jianshu.io/upload_images/1230738-46c0c6fb5f1913da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 青铜挑战：多个 section ##

让 **UITableView** 显示两个 section—— 一个用于价值超过 $50 的 item，另一个用于剩余的 item。 在开始此挑战之前，请复制包含项目的文件夹及其所有源文件。 然后在复制的项目中完成挑战; 原项目要保留到以后的章节用。

## 白银挑战：固定行 ##

使 **UITableView** 的最后一行显示为文本 “No more items!” 且无论 store 中的 item 数量是多少（包括0项），都会显示。

## 黄金挑战：自定义表 ##

使每一行的高度达到 60 点(point)，除了白银挑战中最后一行应该保持 44 point。 然后，将除最后一行之外的每行的字体大小更改为 20 点。 最后，使 **UITableView** 的背景显示一个图像。 （要使此像素完美，您将需要一个正确大小的图像，具体取决于您的设备，请参见第1章中的图表）