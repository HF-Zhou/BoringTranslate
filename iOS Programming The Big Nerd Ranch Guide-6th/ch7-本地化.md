# 本土化 #

iOS 的吸引力是全球性的——iOS 用户生活在许多国家，并使用多种语言。 您可以通过 `国际化(internationalization)` 和 `本土化/本地化(localization)` 流程确保您的应用程序可以被全球用户使用。

国际化确保您的本地文化信息（如语言，货币，日期格式，数字格式等）不会硬编码到您的应用程序中。 本土化是根据用户的 `语言(Language)` 和 `区域格式(Region Format)` 设置在应用程序中提供适当数据的过程。 您可以在 iOS `设置(Settings)` 应用程序中找到这些设置（图7.1）。 选择 `常规(General)` 行，然后选择 `语言和区域(Language & Region)` 行。

**图7.1语言和区域设置**

![](http://upload-images.jianshu.io/upload_images/1230738-6078e6a26a3c0a7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这里，用户可以设置他们的地区，如美国或英国。 （为什么苹果使用“地区”而不是“国家”？一些国家有不止一个地区有不同的设置，滚动 *地区(Region)* 中的选项来查看。）

苹果使国际化和本地化相对简单。 一个具有本地化API的优点的应用程序甚至不需要重新编译成其他语言或区域。 （顺便说一句，因为 `国际化(internationalization)` 和 `本地化(localization)` 太长，有时会分别将它们简称为 `i18n` 和 `L10n`）

在本章中，您将首先将 `WorldTrotter` 应用程序国际化，然后将其本土化为西班牙语（图7.2）。

**图7.2 本土化 WorldTrotter**

![](http://upload-images.jianshu.io/upload_images/1230738-19ac6203187e44a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 国际化 ##

在第一部分中，您将使用 **NumberFormatter** 和 **NSNumber** 类将 **ConversionViewController** 进行国际化。

### 格式化器 ###

在第4章中，您使用 **NumberFormatter** 的一个实例来设置 **ConversionViewController** 中的 Celsius 标签的文本。 **NumberFormatter** 具有一个 **locale** 属性，该属性设置为设备的当前区域设置。 每当使用 **NumberFormatter** 创建一个数字时，它将检查其 **locale** 属性并相应地设置格式。 所以 Celsius 标签的文字从一开始就已经国际化了。

**Locale** 知道不同地区如何显示符号，日期和小数，以及它们是否使用度量系统。 **Locale** 的实例表示一个地区对应的这些变量的设置。 当您访问 **Locale** 上的 `current` 属性时，会返回表示用户地区设置的 **Locale** 实例。 一旦你有这个 **Locale** 实例，你可以知道一些信息，如这个地区是否使用公制或者这个地区的货币符号是什么。

> `let currentLocale = Locale.current`
`let isMetric = currentLocale.usesMetricSystem`
`let currencySymbol = currentLocale.currencySymbol`

即使 Celsius 标签已经国际化，但仍然存在问题。 将系统区域更改为西班牙。 选择活动方案弹出窗口，然后选择 `Edit Scheme...`（图7.3）。

**图7.3 编辑方案**

![](http://upload-images.jianshu.io/upload_images/1230738-51181ccffcd3f43f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

确保在左侧选择了 `Run`，然后选择顶部的 `Options` 选项卡。 在 `Application Region` 弹出窗口中，选择 `Europe`，然后选择 `Spain`（图7.4）。 最后 `Close` 关闭活动方案窗口。

**图7.4 选择不同的地区**

![](http://upload-images.jianshu.io/upload_images/1230738-3360d4ba74bdf63c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

构建并运行应用程序。 在 **ConversionViewController** 上，点击文本字段，确保软件键盘可见。 您可能已经注意到一个区别：在西班牙，小数分隔符是一个逗号而不是一个句点（而且千位分隔符是一个句点，而不是一个逗号），所以在美国写的 `123,456.789` 的数字在西班牙将被写成 `123.456,789`。

尝试键入多个十进制分隔符（逗号），并注意到应用程序愉快地接受它。哎呦！ 您之前禁止多个十进制分隔符的代码检查的是一个小数点，而不是使用特定于区域设置的小数分隔符。 我们来解决这个问题。

打开 `ConversionViewController.swift` 并更新 **textfield（_：shouldChangeCharactersIn：replacementString :)** 以使用特定于区域设置的小数分隔符。

> `func textField(_ textField: UITextField,`
      `shouldChangeCharactersIn range: NSRange,`
      `replacementString string: String) -> Bool {`
>
  ~~`let existingTextHasDecimalSeparator = textField.text?.range(of: ".")`~~
  ~~`let replacementTextHasDecimalSeparator = string.range(of: ".")`~~
>
  **`let currentLocale = Locale.current`**
  **`let decimalSeparator = currentLocale.decimalSeparator ?? "."`**
>
  **`let existingTextHasDecimalSeparator = textField.text?.range(of: decimalSeparator)`**
  **`let replacementTextHasDecimalSeparator = string.range(of: decimalSeparator)`**
>
  `if existingTextHasDecimalSeparator != nil,replacementTextHasDecimalSeparator != nil {`
    `return false`
  `} else {`
    `return true`
  `}`
`}`

构建并运行应用程序。 该应用程序不再允许您键入多个十进制分隔符，它以独立于用户区域选择的方式执行此操作。

但还是有一个问题。 如果您输入的小数分隔符不是数字的句点，则转换为摄氏不会发生——摄氏标签显示 “???”。 这里发生了什么？ 在 **fahrenheitFieldEditingChanged(_ :)** 中，您正在使用 **Double** 类型的初始化器，它将一个字符串作为参数。 此初始化方法不知道如何处理使用十进制分隔符以外的其他字符串。

让我们使用 **NumberFormatter** 类修复这个代码。 在 `ConversionViewController.swift` 中，更新 **fahrenheitFieldEditingChanged(_ :)** 将文本字段的字符串转换为与区域设置无关的数字。

> `@IBAction func fahrenheitFieldEditingChanged(_ textField: UITextField) {`
> 
  ~~`if let text = textField.text, let value = Double(text) {`~~
    ~~`fahrenheitValue = Measurement(value: value, unit: .fahrenheit)`~~
>
  **`if let text = textField.text, let number = numberFormatter.number(from: text) {`**
    **`fahrenheitValue = Measurement(value: number.doubleValue, unit: .fahrenheit)`**
  `} else {`
    `fahrenheitValue = nil`
  `}`
`}`

使用 数字格式化器 的实例方法 **number(from:)** 将字符串转换为数字。 因为 数字格式化器 知道语言环境，所以它能够将字符串转换成一个数字。 如果该字符串包含有效的数字，该方法返回一个 **NSNumber** 的实例。 **NSNumber** 是一个可以表示各种数字类型的类，包括 **Int**，**Float**，**Double** 等。 您可以向 **NSNumber** 的实例请求其值表示为其中一个值。 你在这里做的是获得`doubleValue` 的数字。

构建并运行应用程序。 现在，您正在以 与地区设置无关 的方式转换字符串，文本字段的值正确转换为其摄氏度值（图7.5）。

**图7.5 用逗号分隔符转换**

![](http://upload-images.jianshu.io/upload_images/1230738-24c8631743ac9923.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 基本国际化 ###

国际化时，您会询问关于 **Locale** 实例的问题。 其实，**Locale** 只有一些 区域特定 的变量。 这就是本地化——为 不同区域和语言设置 创建特定于应用程序的替换。 本地化通常涉及为不同的区域和语言生成多个资源副本（如图像，声音和界面文件），或者创建和访问根据不同的语言而创建的 `字符串表(strings tables)` （本章稍后将会看到）。

在您进行本土化资源的过程之前，您必须了解一个 iOS 应用程序是如何处理本土化资源的。

当您在 `Xcode` 中构建目标时，将创建一个应用程序包。 您添加到 `Xcode` 中的目标的所有资源都将与可执行文件本身一起复制到此包中。

这个 bundle 在运行时被 **Bundle** 的一个实例表示为 `main bundle` 。 许多类与 **Bundle** 一起加载资源。

本土化资源将资源的另一个副本放在应用程序包中。 这些资源被组织成特定于语言的目录，称为 `lproj` 目录。 这些目录中的每一个都是后缀为 `lproj` 的本地名称。 例如，美国英语本地化是 `en_US`，其中 `en` 是英文代码，`US` 是美国的美国区域代码，所以美国英语资源的目录是 `en_US.lproj`。 （如果您不需要在资源文件中进行区域区分，则可以省略该区域。）这些语言和区域代码在所有平台上都是标准化的，而不仅仅是 iOS。

当一个软件包被请求资源文件的路径时，它首先查看该文件夹的根级别。 如果没有找到它，它会查看设备的区域设置和语言设置，找到相应的 `lproj` 目录，并在那里查找文件。 因此，只需通过本土化资源文件，您的应用程序将自动加载正确的文件。

本土化资源文件的一种做法是创建单独的故事板文件，并手动编辑每个文件中的每个字符串。 但是，如果您计划多个本土化，则此方法不能很好地扩展。 当您向本土化的故事板添加新的标签或按钮时会发生什么？ 您必须将此视图添加到每个语言的故事板。这很不好。

为了简化本土化界面文件的过程，`Xcode` 有一个称为 `基本国际化(base internationalization)` 的功能。 基本国际化 创建了包含主界面文件的 `Base.lproj` 目录。 然后可以通过仅创建 `Localizable.strings` 文件来完成本土化各个界面文件。 仍然可以创建完整的界面文件，以防单独更改字符串无法进行本土化。 然而，借助自动布局，字符串替换就足以满足大多数本地化需求。 在下一节中，您将使用自动布局来准备您的布局进行本地化。

### 本地化准备 ###

打开 *Main.storyboard* 并通过单击 `View` → `Assistant Editor` → `Show Assistant Editor` 或使用键盘快捷键 Option-Command-Return 显示助理编辑器。 从跳转栏下拉列表中，选择 `Preview`（图7.6）。 `preview assistant` 可让您轻松了解界面如何在屏幕尺寸和方向以及不同的本地化语言之间查看。

**图7.6 打开预览助手**

![](http://upload-images.jianshu.io/upload_images/1230738-69fc8edacf20c586.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在故事板中，选择 `Conversion View Controller` 以查看其预览（图7.7）。

**图7.7预览助手**

![](http://upload-images.jianshu.io/upload_images/1230738-a3b19e4580ab989e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意预览助手的下方的控件。 左侧的 + 按钮允许您向预览画布添加其他屏幕尺寸。 这样您就可以轻松地看到界面的变化如何在屏幕尺寸和方向同时传播。 右侧的按钮允许您选择一种语言来预览此界面。

（如果您的预览是针对 iPhone 7 以外的配置，请使用 + 按钮添加此配置，然后单击默认打开的任何预览，然后按 Delete 键将其删除。）

您尚未将应用程序本地化为另一种语言，但 *Xcode* 提供了一种 `伪语言(pseudolanguage)` 供您使用。 在您收到所有字符串和 assets 的翻译之前，Pseudolanguages 可以帮助您将应用程序国际化。 内置的伪语言，`双长度伪语言(Double-Length Pseudolanguage)`，通过重复文本元素中的任何文本字符串来模拟更详细的语言。 那么，比如说 “is really” 变成 “is really is really”。

在 `Language` 弹出窗口中，选择 `Double-Length Pseudolanguage`。 标签都将其文本翻倍（图7.8）。

**图7.8 双字文本字符串**

![](http://upload-images.jianshu.io/upload_images/1230738-46b521b65107c9b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

双长度伪语言表明了一些问题：标签在屏幕的左右两边都会关闭，您无法读取整个字符串。 解决方法是限制所有的标签，使其前后边缘保持在他们的父级视图的边缘。 然后，将标签的行数更改为 0，它表明其文本有需要的话会自动换行。 首先我们会修复一个标签，然后在其余标签上重复步骤。
 
在画布上，选择 `degrees Fahrenheit` 标签。 您将以新的方式为此标签添加约束。 选中标签右键拖动到父级视图的左侧。 当您这样做时，会出现一个上下文相关的弹出窗口，为您提供有意义的约束条件（图7.9）。 从列表中选择 `Leading Space to Container Margin`。

**图7.9 通过右键拖动创建约束**

![](http://upload-images.jianshu.io/upload_images/1230738-ac7a29f86b23ff92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您拖动的方向会影响可能显示的约束。 水平拖动将显示水平约束，垂直拖动将显示垂直约束。 对角拖动将显示水平和垂直约束，这对于同时设置许多约束是有用的。

现在选中 `degrees Fahrenheit`  标签右键拖动到父级视图的右侧，并选择 `Trailing Space to Container Margin`。

对它们来说，这些约束不是很好。 它们会保持标签前后边缘之间的现有固定距离，如预览助手所示（图7.10）。

**图7.10 具有新约束的预览助手**

![](http://upload-images.jianshu.io/upload_images/1230738-e54a2409c31413f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你真正想要的是标签和边距之间的距离大于或等于 0.你可以用 `不等式约束(inequality constraints)` 来做到这一点。

通过单击标签左侧的 指示线 来选择主要约束。 打开其属性检查器，将 `Relation` 更改为 `Greater Than or Equal`，并将 `Constant` 更改为 0（图7.11）。

**图7.11 不等式约束**

![](http://upload-images.jianshu.io/upload_images/1230738-9847ace348044423.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对 `尾部(trailing)` 约束执行相同操作。 看看预览助手; 界面看起来更好，但是标签仍然被截断。

选择标签并打开其属性检查器。 将 `Lines` 更改为 0.现在来看看预览助手; 该标签不再被截断，而是文本流向第二行。 因为其他标签每个都与他们上面的标签相关，所以它们已经被自动下移。

对其他标签重复上述步骤。 您将需要：

- 向每个标签添加一个前部和尾部约束。
- 将约束的关系设置为 `Greater Than or Equal`，常数为 0.（编辑约束的快捷方式是双击它。）
- 将标签的 `Lines` 更改为0。

完成后，双长度伪语言的预览助手将如图7.12所示。

**图7.12 具有最终约束的预览助手**

![](http://upload-images.jianshu.io/upload_images/1230738-f785ff9b6eaeca49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，您会用预览助手了。 您可以使用右上角的 `x` 关闭助理编辑器。

## 本地化 ##

*WorldTrotter* 现在已经国际化——其界面能够适应各种语言和地区。 现在是时候本地化应用程序——也就是更新不同地区和语言下应用程序的字符串和资源。 在本节中，您将本地化 `WorldTrotter` 的界面：`Main.storyboard` 文件。 您将创建英语和西班牙语本地化，除了基础的一个之外，还将创建两个 `lproj` 目录。

从本地化一个故事板文件开始。 在项目导航器中选择 `Main.storyboard`。

通过单击检查器选择器中的 
![](http://upload-images.jianshu.io/upload_images/1230738-46fed480d58c8e34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 选项卡或使用键盘快捷键 `Option-Command-1` 打开 `文件检查器(file inspector)`。 查找此检查器中名为 `Localization` 的部分。 检查 `English` 框，并确保下拉列表显示 `Localizable Strings`（图7.13）。 这将创建一个字符串表，您稍后将使用它来本地化应用程序。

**图7.13 本地化为英文**

![](http://upload-images.jianshu.io/upload_images/1230738-b92ba91da549302b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来，在项目导航器中，选择顶部的 `WorldTrotter` 项目。 然后在侧面列表中的 `Project` 部分下选择 `WorldTrotter`，并确保 `Info` 选项卡已打开。 （如果看不到侧面列表，可以使用左上角的 `Show projects and targets list` 列表按钮打开它（图7.14）。）

**图7.14 显示项目设置**

![](http://upload-images.jianshu.io/upload_images/1230738-96e2b1c90f4b63df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单击 `Localizations` 下的 `+` 按钮，然后选择 `Spanish (es)`。 在对话框中，您可以取消选中 `LaunchScreen.storyboard` 文件; 确保 `Main.storyboard` 文件被选中。 确保参考语言为 `Base`，文件类型为 `Localizable Strings`。 单击 `Finish`。 这将创建一个 `es.lproj` 文件夹，并生成包含从基本界面文件的所有字符串的 `Main.strings` 文件。 `Localizations` 配置应如图7.15所示。

**图7.15 Localizations**

![](http://upload-images.jianshu.io/upload_images/1230738-6da26246eaaa6afa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

查看项目导航器。 单击 `Main.storyboard` 旁边的闭合按钮（图7.16）。`Xcode` 将 `Main.storyboard` 文件移动到 `Base.lproj` 目录，并在 `es.lproj` 目录中创建 `Main.strings` 文件。

**图7.16 项目导航器中的本地化storyboard文件**

![](http://upload-images.jianshu.io/upload_images/1230738-060433ae7a0fed94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击 `Spanish` 版本的 `Main.strings`。 当此文件打开时，文本不是西班牙文。 你必须自己翻译本地化的文件; Xcode 并不会这么智能

根据以下文本编辑此文件。 您的文件中的数字和顺序可能不同，但您可以利用注释中的 `text` 和 `title` 字段来匹配翻译。


> `/* Class = "UITabBarItem"; title = "Map"; ObjectID = "6xh-o5-yRt"; */`
`"6xh-o5-yRt.title" = ` ~~`"Map"`~~ `"Mapa";`
>
`/* Class = "UILabel"; text = "degrees Celsius"; ObjectID = "7la-u7-mx6"; */`
`"7la-u7-mx6.text" = ` ~~`"degrees Celsius"`~~ `"grados Celsius";`
>
`/* Class = "UILabel"; text = "degrees Fahrenheit"; ObjectID = "Dic-rs-P0S"; */`
`"Dic-rs-P0S.text" = ` ~~`"degrees Fahrenheit"`~~ `"grados Fahrenheit";`
>
`/* Class = "UILabel"; text = "100"; ObjectID = "Eso-Wf-EyH"; */`
`"Eso-Wf-EyH.text" = "100";`
>
`/* Class = "UITextField"; placeholder = "value"; ObjectID = "On4-jV-YlY"; */`
`"On4-jV-YlY.placeholder" = ` ~~`"value"`~~ `"valor";`
>
`/* Class = "UILabel"; text = "is really"; ObjectID = "wtF-xR-gbZ"; */`
`"wtF-xR-gbZ.text" = ` ~~`"is really"`~~ `"es realmente";`
>
`/* Class = "UITabBarItem"; title = "Convert"; ObjectID = "zLY-50-CeX"; */`
`"zLY-50-CeX.title" = `  ~~`"Convert"`~~ `"Convertir";`

现在你已经完成本地化这个故事板文件，让我们来测试一下。 首先，有一点 Xcode 的小问题要注意：有时 Xcode 会在构建应用程序时忽略资源文件的更改。 为确保您的应用程序从零开始构建，请先从设备或模拟器中卸载。 （按住启动器中的图标，当它开始摆动时，点击删除图标。）重新启动 Xcode。然后，从 `Product` 菜单中选择 `Clean`。 最后，在打开 `Product` 菜单时按住 `Option` 键，然后选择 `Clean Build Folder ....` 这将强制应用程序被完全重新编译，重新绑定并重新安装。

打开活动方案弹出窗口并选择 `Edit Scheme`。 确保在左侧选择 `Run`，然后打开 `Options` 选项卡。 打开 `Application Language` 弹出窗口并选择 `Spanish`。 最后，确认在 `Application Region` 弹出窗口中选择 `Spain` 。 关闭窗口。

构建并运行应用程序。 确保当你查看 **ConversionViewController** 时，您看到的是西班牙语界面。 因为您在标签上设置约束以适应不同长度的文本，因此他们会自己调整大小（图7.17）。

**图7.17 西班牙语版的 ConversionViewController**

![](http://upload-images.jianshu.io/upload_images/1230738-ae9c05a27ff3f53d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### NSLocalizedString 和字符串表 ###

在应用程序的许多地方，您可以动态创建 **String** 实例，或向用户显示字符串文字。 要显示这些字符串的翻译版本，您必须创建一个字符串表。 字符串表是包含应用程序使用的所有字符串及其关联翻译的键值对列表的文件。 它是一个添加到您的应用程序的资源文件，但您不需要做很多工作来获取数据。

您可以在代码中使用一个字符串，如下所示：

`let greeting = "Hello!"`

要使您的代码中的字符串国际化，您可以使用函数 **NSLocalizedString(_：comment :)** 替换文字字符串。

`let greeting = NSLocalizedString("Hello!", comment: "The greeting for the user")`

此函数有两个参数：一个 key 和一个描述字符串使用的 注释(`comment`)。 key 是字符串表中的查找值。 在运行时，**NSLocalizedString(_：comment :)** 将查看与您的应用程序绑定的字符串表，该表与用户的语言设置相匹配。 然后，在该表中，该函数获取与该 key 相匹配的翻译字符串。

现在，您将使 **MapViewController** 在其分段控件中显示的字符串国际化。 在 `MapViewController.swift` 中，找到 **loadView()** 方法，并更新分段控件的初始化方法以使用本地化字符串。

>`override func loadView() {`
  `// Create a map view`
  `mapView = MKMapView()`
>
  `// Set it as *the* view of this view controller`
  `view = mapView`
>
  ~~`let segmentedControl = UISegmentedControl(items: ["Standard", "Satellite", "Hybrid"])`~~
>
  **`let standardString = NSLocalizedString("Standard", comment: "Standard map view")`**
  **`let satelliteString = NSLocalizedString("Satellite", comment: "Satellite map view")`**
  **`let hybridString = NSLocalizedString("Hybrid", comment: "Hybrid map view")`**
  **`let segmentedControl = UISegmentedControl(items: [standardString, satelliteString, hybridString])`**

一旦拥有 **NSLocalizedString(_：comment :)** 函数进行国际化的文件，就可以使用命令行命令来生成字符串表。

打开终端应用程序 这是一个 Unix 终端 它用于运行命令行工具。 您要导航到 `MapViewController.swift` 的位置。 如果你以前从未使用过终端应用程序，这里是一个方便的技巧。 在终端中，键入以下内容：

`cd`

其次是空格。 （现在不要按 Return 或 回车）

接下来，打开 `Finder` 并找到 `MapViewController.swift` 和包含它的文件夹。 将该文件夹的图标拖到终端窗口。 终端将为您填写路径。 它看起来像这样：

`cd /Users/cbkeur/iOSDevelopment/WorldTrotter/WorldTrotter/`

按回车。 `Terminal` 的当前工作目录现在是这个目录。

使用终端命令 `ls` 打印工作目录的内容，并确认 `MapViewController.swift` 在该列表中。

要生成字符串表，请在终端中输入以下内容，然后按 Return：

`genstrings MapViewController.swift`

生成的文件 `Localizable.strings` 包含 **MapViewController** 中的字符串。 将此新文件从 `Finder` 拖到项目导航器中（或使用 `File` → `Add Files to "WorldTrotter"...` 菜单项）。 编译应用程序时，该资源将被复制到主包中。

打开 `Localizable.strings`。 文件应该是这样的：

> `/* Hybrid map view */`
`"Hybrid" = "Hybrid";`
>
`/* Satellite map view */`
`"Satellite" = "Satellite";`
>
`/* Standard map view */`
`"Standard" = "Standard";`

请注意，您的字符串上方的注释是您提供给 **NSLocalizedString** 函数的第二个参数。 即使该comment参数不是必要的，但它将使您的本地化更轻松。

现在你已经创建了 `Localizable.strings`，你需要在 `Xcode` 中进行本地化。 打开其文件检查器，然后单击实用程序区域中的 `Localize...` 按钮。 确保从弹出窗口中选择 `Base`，然后单击 `Localize`。 选中每种语言旁边的框，添加 Spanish 和 English localization。

在项目导航器中，单击 `Localizable.strings` 旁边的闭合三角形。 打开西班牙语版本。 左侧的字符串是传递给 **NSLocalizedString（_：comment :)** 函数的 key，右侧的字符串是返回值。 将右侧的文本更改为下面显示的西班牙语翻译。 （要键入重音字符，例如 `é`，请按住键盘上的相应字符，然后从弹出窗口中按相应的数字。）

> `/* Hybrid map view */`
`"Hybrid" = ` ~~**`Hybrid`**~~ **`"Híbrido"`**;
>
`/* Satellite map view */`
`"Satellite" = ` ~~**`Satellite`**~~ **`"Satélite"`**;
>
`/* Standard map view */`
`"Standard" = ` ~~**`Standard`**~~ **`"Estándar"`**;

再次构建并运行该应用程序。 现在，所有这些字符串（包括分段控件中的标题）将以西班牙语显示（图7.18）。 如果没有，您可能需要删除应用程序，清理项目并重建。 （或检查您的 scheme 语言设置。）

**图7.18 西班牙语的 MapViewController**

![](http://upload-images.jianshu.io/upload_images/1230738-ce8968eaf579a1d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

国际化和本地化对于您的应用来说是非常重要的，这样可以吸引更多的用户。 另外，正如你在本章的初期看到的那样，如果你的应用程序没有适当的国际化，你的应用程序可能不适用于某些用户。 您将在本书其余部分中将您的项目国际化（但不是本地化）。

在过去五章中，您已经构建了一个令人印象深刻的应用程序，允许用户在摄氏和华氏之间进行转换，并以几种不同的方式显示地图。 这个应用程序不仅在所有 iPhone 屏幕尺寸上都很好地扩展，而且还被本地化为另一种语言。

## 青铜挑战：另一个本地化版本 ##

实践： 将 `WorldTrotter` 本地化为另一种语言。 如果您需要帮助语言，请使用翻译网站。

## 更多：包在国际化中的作用 ##

利用本地化的实际工作是由 **Bundle** 类完成的。 bundle 表示文件系统上将编译的代码和资源组合在一起的位置。 `主包(main bundle)` 是应用程序包的另一个名称，它包含应用程序的所有资源和可执行文件。 您将在第 16 章中了解有关应用程序包的更多信息。

当构建应用程序时，所有 `lproj` 目录都将被复制到主包中。 图7.19显示了 `WorldTrotter` 的主包（添加了一些其他图像）。

**图7.19 应用程序包**

![](http://upload-images.jianshu.io/upload_images/1230738-51ffbd8bcbd06919.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Bundle** 知道如何使用实例方法 **url（forResource：withExtension :)** 来搜索每种资源类型的本地化目录。 当您想要通过应用程序捆绑的资源的路径时，您可以在主包上调用此方法。 这里是一个使用资源文件 `Boo.png` 的例子：

`let path = Bundle.main.url(forResource:"Boo", withExtension: "png")`

当尝试找到资源时，包首先检查资源是否存在于应用程序包的顶层。 如果是这样，它会将完整的URL返回给该文件。 如果没有，该包将获取设备的语言和区域设置，并在相应的 `lproj` 目录中查找以构造URL。 如果仍然找不到它，它会在 `Base.lprojd` 中查找。 最后，如果没有找到文件，则返回 `nil`。

在图7.19所示的应用程序包中，如果用户的语言设置为西班牙语，则 **Bundle** 将在顶级查找 `Boo.png`， 接着是 `es.lproj` 中的Tom.png，最后是 `Base.lproj` 中的 `Hat.png`。

当您向项目添加新的本地化时，`Xcode` 不会自动从顶级目录中删除资源。 这就是为什么您在本地化文件时必须删除和清理应用程序的原因 - 否则，以前的未本地化文件仍将处于应用程序包的根级别。 即使应用程序包中有 `lproj` 文件夹，`bundle` 也会首先找到顶级文件并返回其URL。

## 更多：导入和导出为XLIFF ##

本地化数据的行业标准格式是 XLIFF 数据类型，代表 XML 本地化交换文件格式（XML代表可扩展标记语言）。 当使用翻译器时，您将经常向他们发送一个包含应用程序中数据的 XLIFF 文件进行本地化，并将返回一个本地化的 XLIFF 文件供您导入。

`Xcode` 本身支持在 XLIFF 中导入和导出本地化数据。 导出过程将负责查找和导出项目中使用 `genstrings` 工具手动生成的本地化字符串。

要导出XLIFF中的本地化字符串，请在项目导航器中选择项目（`WorldTrotter`）。 然后选择 `Editor` 菜单，然后 `Export For Localization....` 。在下一个屏幕上，您可以选择是否导出现有的翻译（这可能是一个好主意，因此翻译不会进行冗余工作）以及您想要的语言导出（图7.20）。

**图7.20 将本地化数据导出为 XLIFF**

![](http://upload-images.jianshu.io/upload_images/1230738-2239e5c02477a8fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

要导入本地化，请在项目导航器中选择项目（`WorldTrotter`）。 然后选择 `Editor` → `Import Localizations...`。 选择文件后，您将能够在导入之前确认是否更新。