Swift是苹果于2014年推出的一种新语言。Swfit 将替换 Objective-C，成为 iOS 和 Mac 的推荐开发语言。在本章中，您将重点学习Swift的基础知识。 你不会学到所有的知识，但是你会学到足够的知识来入门。 然后，当您继续阅读本书时，您将在学习 iOS 开发的过程中学到更多。

Swift 保持 Objective-C 的表现力，同时引入更安全，简洁和可读的语法。它强调类型安全性，并增加了高级功能，如可选项，泛型，复杂结构和枚举。最重要的是，在开发人员多年来构建的、经过测试的、高级的 iOS 框架中也能使用这些新功能。

如果你在用 Swift 进行iOS开发的时候感到困难，您可能需要从 `Swift Programming：The Big Nerd Ranch Guide` 或 Apple 的 Swift教程 开始，您可以在 `developer.apple.com/` 中找到。 但是，如果你有一些编程经验，并想在工作中运用，你可以在这里开始你的 Swift 学习。

## Swfit 的类型 ##

Swift 类型可以分为三个基本组：`结构体(structure)`，`类(class)` 和 `枚举(enumeration)`（图2.1）。 所有三个可以有：

- 属性 - 与类型相关联的值
- 构造器 - 初始化类型实例的代码
- 实例方法 - 在该类型的实例上调用的函数
- 类或静态方法 - 在类型本身上调用的函数

**图2.1 Swift 构建块**

![](http://upload-images.jianshu.io/upload_images/1230738-5576e9df5129909d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Swift 的结构体和枚举比大多数语言更加强大。 除了支持属性，构造器和方法之外，它们还可以符合协议并可以扩展。

Swift 原始数据类型（如数字和布尔值）的实现可能会让您感到惊讶：它们都是结构体。 事实上，所有这些 Swift 类型都是结构体：

数字类型: **Int, Float, Double**

布尔类型: **Bool**

文本类型: **String, Character**

集合类型: **Array<Element>, Dictionary<Key:Hashable,Value>,Set<Element:Hashable>**

这意味着基础类型也具有属性，构造器和自己的方法。 它们也可以符合协议并扩展。

最后，Swift 的一个关键特性是 `可选项(optional)`。 可选项允许您存储特定类型的值或根本没有值。 您将在本章后面更多地了解可选项及其在 Swift 中的作用。

## 使用标准类型 ##

在本节中，您将在 `Xcode playground` 中尝试使用标准类型。 `playground` 可让您编写代码并查看结果，而无需手动运行应用程序和检查输出。

在 `Xcode` 中，选择 `File` → `New` → `Playground...` 方便起见，你可以使用此文件的默认名称。 确保 platform 是 `iOS`（图2.2）。

**图2.2配置playground**

![](http://upload-images.jianshu.io/upload_images/1230738-9bee75d9e18bb22f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

单击 `Next` 并将其保存在方便的位置。

当文件打开时，请注意，playground 分为两个部分（图2.3）。 左侧较大的白色区域是您编写代码的编辑器。 右侧的灰色列是侧边栏。 playground 在每行后编译并执行您的代码，并在侧边栏中显示结果。

**图2.3一个playground**

![](http://upload-images.jianshu.io/upload_images/1230738-a239647cbd991b17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在示例代码中，*var* 关键字表示一个变量，因此 str 的值可以从其初始值更改。 键入以下代码以更改 str 的值，您将看到结果显示在右侧的边栏中。

> `var str = "Hello, playground" `    "Hello, playground"
> **`str = "Hello, Swift" `**        "Hello, Swift"

（请注意，我们会在右边显示侧边栏的结果，给那些没有积极地进行练习的读者看。）

`let` 关键字表示不能更改的常量值。 在您的 Swift 代码中，您应该多使用 let，除非您期望该值需要更改。 添加一个常量：

> `var str = "Hello, playground" `    "Hello, playground"
> `str = "Hello, Swift" `        "Hello, Swift"
> **`let constStr = str`**         "Hello, Swift"

因为constStr是一个常量，所以尝试改变它的值会导致一个错误。

> `var str = "Hello, playground" `    "Hello, playground"
`str = "Hello, Swift" `        "Hello, Swift"
`let constStr = str`         "Hello, Swift"
**`constStr = "Hello, world"`**

出现错误的话，会在错误行左侧用红色符号表示。 单击符号以获取有关错误的更多信息。 在这种情况下，错误是
> `Cannot assign to value: 'constStr' is a 'let' constant.`
*`无法分配给值：'constStr' 是一个 'let' 常量。`*

playground 代码中的错误将阻止您在侧边栏中看到任何进一步的结果，因此您通常希望立即解决问题。 删除尝试更改 constStr 的值的行。

> `var str = "Hello, playground" `    "Hello, playground"
> `str = "Hello, Swift" `        "Hello, Swift"
> `let constStr = str`         "Hello, Swift"
> ~~`constStr = "Hello, world"`~~

### 类型推断 ###

在这一点上，您可能已经注意到，constStr 常量和 str 变量都不具有指定的类型。 这并不意味着他们是没有类型的！ 编译器会从初始值推断出它们的类型。 这称为类型推断。

您可以使用 *Xcode* 的快速帮助找出推断的类型。 按住 Alt 在 playground 上单击 constStr 以查看该常量的快速帮助信息(译者注：此处虚拟机中按 Alt，Mac 是 Option 键 )，如图2.4所示。

**图2.4 constStr是String类型**

![](http://upload-images.jianshu.io/upload_images/1230738-895c6625295a41c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Alt-单击显示快速帮助将适用于任何对象。

### 指定类型 ###

如果常数或变量具有初始值，则可以依赖类型推断。 如果常数或变量没有初始值，或者如果要确保它是某种类型，则可以在声明中指定类型。

添加指定类型的更多变量：

>`var str = "Hello, playground" `    "Hello, playground"
`str = "Hello, Swift" `        "Hello, Swift"
`let constStr = str`         "Hello, Swift"<br>
**`var nextYear: Int`**
**`var bodyTemp: Float`**
**`var hasPet: Bool`**

请注意，边栏没有报告任何结果，因为这些变量还没有值。

我们来看看这些新的类型以及它们的使用方式。

### 数字和布尔类型 ###

整数最常见的类型是 **Int**。 还有基于数字长度和符号类型的整数类型，但是苹果建议使用 **Int**，除非你真的有使用别的类型的理由。

对于浮点数，Swift提供了三种不同精度的类型：

**Float** 为32位，**Double** 为64位，**Float80** 为80位。

### 集合类型 ###

Swift 标准库提供三个集合：*数组(array)*，*字典(dictionary)* 和 *集合(set)*。

数组是元素的有序集合。 数组类型写为 Array <T>，其中 T 是数组将包含的元素的类型。 数组可以包含任何类型的元素：标准类型，结构体或类。

为整数数组添加一个变量：

>`var hasPet: Bool`
**`var arrayOfInts: Array<Int>`**

数组是强类型的。 一旦将数组声明为包含 **Int** 的元素，就不能向其添加一个 **String**。

有一个用于声明数组的简写语法：您可以简单地在数组将包含的类型周围使用方括号。 更新 *arrayOfInts* 的声明以使用简写：

>`var hasPet: Bool`
~~`var arrayOfInts: Array<Int>//删除该行`~~
**`var arrayOfInts: [Int]`**

字典是键值对的无序集合。 值(value)可以是任何类型，包括结构体和类。 键(key)也可以是任何类型，但它们必须是唯一的。 具体来说，key 必须是 *可哈希(hastable)* 的，这允许字典保证 key 是唯一的，并且更有效地访问给定 key 的值。 基本的 Swift 类型，如 **Int**，**Float**，**Character** 和 **String** 都是可哈希的。

像 Swift 数组一样，Swift 字典也是强类型的，只能包含声明类型的 键(key) 和 值(value)。 例如，您可能有一个字典，按国家/地区存储首都。 这个字典的 键(key) 将是国名，值将是城市名称。 键和值都将是字符串，您将无法添加任何其他类型的键或值。

为这样的字典添加一个变量：

> `var arrayOfInts: [Int]`
**`var dictionaryOfCapitalsByCountry: Dictionary<String,String>`**

还有一个用于声明字典的简写语法。 更新 *dictionaryOfCapitalsByCountry* 使用简写：

>`var arrayOfInts: [Int]`
~~`var dictionaryOfCapitalsByCountry: Dictionary<String,String>`~~
**`var dictionaryOfCapitalsByCountry: [String:String]`**

一个集合类似于一个数组，因为它包含一些特定类型的元素。 然而，集合是无序的，成员必须是唯一的，并且是 *可哈希的(hashable)* 。 当您只需要确定某个东西是否是一个集合的成员时，集合的无序性使得它们更快。 为集合添加一个变量：

>**`var winningLotteryNumbers: Set<Int>`**

与数组和字典不同，集合没有缩写语法。

### 字面值和下标 ###

可以为标准类型分配字面值。 例如，*str* 被赋予字符串字面值。 字符串字面值用双引号形成。 将分配给 *str* 的字面值与分配给 *constStr* 的非字面值进行对比：

>`var str = "Hello, playground" `    "Hello, playground"
`str = "Hello, Swift" `        "Hello, Swift"
`let constStr = str`         "Hello, Swift"

在 playground 上添加两个数字字面值：

>**`let number = 42`**            42
**`let fmStation = 91.1`**          91.1

数组和字典也可以分配字面值。 创建文字数组和字典的语法类似于用于指定这些类型的简写语法。

>**`let countingUp = ["one", "two"] `**                ["one", "two"]
**`let nameByParkingSpace = [13: "Alice", 27: "Bob"] `**       [13: "Alice", 27: "Bob"]

Swift 还提供下标作为访问数组的速记。 要检索数组中的元素，您可以在数组名后面的方括号中提供元素的索引。

> `let countingUp = ["one", "two"]`          ["one", "two"]
**`let secondElement = countingUp[1]`**         "two"
`...`

请注意， 数组的下标始终从 0 开始，下标 1 检索第二个元素。

检索数组时，请确保使用有效的下标。 尝试访问超出范围的下标会导致 *trap*(程序中断)。 *trap* 是运行时错误，在程序进入未知状态之前终止程序。

下标也适用于字典——在本章后面讲解。

## 构造器 ##

到目前为止，您已经使用字面值初始化了常量和变量。 在这样做时，您创建了一个特定类型的实例。 实例(instance) 是类型的特定实例。 从历史上看，这个术语只用于课堂，但在 Swift 中，它也用于描述结构体和枚举。 例如，常量 *secondElement* 包含一个 **String** 的实例。

创建实例的另一种方法是在类型上使用 *构造器(initializer)*。 构造器负责准备新类型实例的内容。构造器执行完成后，该实例已准备就绪。 要使用构造器创建新实例，可以使用类型名称后跟一对括号，如果需要，则加上参数。 该类型和参数的组合对应于特定的构造器。

一些标准类型也具有构造器，当没有提供参数时返回空文本。 向 playground 添加一个空字符串，一个空数组和一个空集。

> **`let emptyString = String()`**          ""
**`let emptyArrayOfInts = [Int]()`**         0 elements
**`let emptySetOfFloats = Set<Float>()`**      0 elements

其他类型具有默认值：

> **`let defaultNumber = Int()`**      0
**`let defaultBool = Bool()`**      false

类型可以有多个构造器。 例如，**String** 具有接受 **Int** 的构造器，并根据该值创建一个字符串。

>`let number = 42`               42
**`let meaningOfLife = String(number) `**      "42"

要创建一个集合，可以使用接受数组字面量的 **Set** 构造器：

>**`let availableRooms = Set([205, 411, 412]) `**     {412, 205, 411}

**Float** 有几个构造器。 无参数构造器返回具有默认值的 **Float** 实例。 还有一个接受浮点数字的构造器。

>**`let defaultFloat = Float()`**       0.0
**`let floatFromLiteral = Float(3.14)	`**    3.14

如果对浮点文字使用类型推断，则类型默认为 **Double**。 使用浮点文字创建以下常量：

>**`let easyPi = 3.14`**         3.14

使用接受 **Double** 的 **Float** 构造器，从此 **Double** 创建一个 **Float**：

>`let easyPi = 3.14`             3.14
**`let floatFromDouble = Float(easyPi)`**     3.14

您可以通过在声明中指定类型来实现相同的结果。

>`let easyPi = 3.14`          3.14
`let floatFromDouble = Float(easyPi)`  3.14
**`let floatingPi: Float = 3.14`**     3.14

## 属性 ##

属性是与类型实例相关联的值。 例如，**String** 具有属性 *isEmpty*，它是一个 **Bool**，它告诉您字符串是否为空。 **Array <T>** 具有属性 *count*，它是数组中的元素数量，为 **Int**。 在 playground 上访问这些属性：

> `let countingUp = ["one", "two"]`    ["one", "two"]
`let secondElement = countingUp[1]`   "two"
**`countingUp.count`**           2
`...`
`let emptyString = String()`
**`emptyString.isEmpty`**          true

## 实例方法 ##

实例方法是特定于特定类型的函数，可以被该类型的实例调用。 从 **Array <T>** 中尝试 **append（_ :)** 实例方法。 您将首先需要将 **constantUp** 数组从常量更改为变量。

>~~let countingUp = ["one", "two"]~~
**`var countingUp = ["one", "two"]`**     ["one", "two"]
`let secondElement = countingUp[1]`    "two"
`countingUp.count`
**`countingUp.append("three")`**       ["one", "two", "three"]

append（_ :)方法接受数组类型的元素，并将其添加到数组的末尾。 我们将在第3章讨论方法，包括命名。

## 可选值(Optional) ##

Swift 类型可以是 可选(*optional*) 的，这通过附加 ？到一个类型名称来表示 。

>`var anOptionalFloat: Float?`
`var anOptionalArrayOfStrings: [String]?`
`var anOptionalArrayOfOptionalStrings: [String?]?`

可选值(optional) 可以表示变量可能根本不存储值。 可选的值将是指定类型的实例或 *nil*。

在这本书中，您将有很多机会使用可选值。 以下是一个让您熟悉语法的示例，以便您以后可以专注于使用可选值。

想象一下乐器读数：

>`var reading1: Float`
`var reading2: Float`
`var reading3: Float`

有时，仪器可能发生故障，因而不能返回一个 reading。 您不希望这种故障显示为 *0.0*。 你希望它是完全不同的东西，告诉你检查你的乐器或采取一些其他的行动。

您可以通过将读数声明为可选来执行此操作。 将这些声明添加到您的 playground。

>**`var reading1: Float?`**    nil
**`var reading2: Float?`**    nil
**`var reading3: Float?`**    nil

作为可选的浮点数，每个读数可以包含 **Float** 或 *nil*。 如果没有给出初始值，则该值默认为 *nil*。

您可以像任何其他变量一样将值分配给可选项。 为 reading 变量 分配浮点数字：

>**`reading1 = 9.8`**    9.8
**`reading2 = 9.2`**    9.2
**`reading3 = 9.7`**     9.7

但是，您不能像非可选浮点数那样使用这些可选浮点，即使它们已被赋值为 **Float**。 在读取可选变量的值之前，必须解决其值为 *nil* 的可能性。 这被称为 解包(unwrap) 可选值。

您将尝试解开一个可选变量的两种方式：可选绑定和强制解包。 您将首先尝试执行强制展开。 这不是因为它是更好的选择——其实这是不太安全的选择。 但是我们先实施强制解包将让您看到危险，并了解为什么可选绑定通常更好。

要强行解开一个可选项，你可以附加一个！ 到它的后面。 首先，尝试 avgReading，就像它们是非可选变量一样：

>`reading1 = 9.8`                9.8
`reading2 = 9.2`                9.2
`reading3 = 9.7`                9.7
**`let avgReading = (reading1 + reading2 + reading3) / 3`**

这会导致错误，因为可选值需要先解开。 因此我们强制解开 reading 以解决错误：

>~~**`let avgReading = (reading1 + reading2 + reading3) / 3`**~~
**`let avgReading = (reading1! + reading2! + reading3!) / 3`**    9.566667

一切都看起来不错，能看到边栏中的正确平均值。 但危险潜伏在于您的代码中。 当你强行展开一个可选值，你告诉编译器你确定这个可选值不会是 *nil*，因而它可以被看作是一个正常的 **Float**。 但如果不是浮点数呢？ 试着注释掉 reading3 的赋值语句，这将返回默认值 *nil*。

>`reading1 = 9.8`      9.8
`reading2 = 9.2`      9.2
~~**`reading3 = 9.7`**~~
**`// reading3 = 9.7`**

现在出现了一个错误。 *Xcode* 可能会在 playground 底部打开其调试区域，并提供有关错误的信息。 如果没有，请选择 *View* → *Debug Area* → *Show Debug Area*。 错误如下：

>`fatal error: unexpectedly found nil while unwrapping an Optional value`
`致命错误：在解开可选值时意外发现为零`

如果你强行展开一个可选值，而且该可选值为 *nil*，那么会导致出现 中断(trap) 并终止应用程序。

解开可选值的更安全的方法是 可选绑定(*optional binding*)。 可选绑定在条件 *if-let* 语句中工作：将可选项分配给相应非可选类型的临时常量。 如果您的可选值具有值，则分配有效，然后继续使用非可选常量。 如果可选是 *nil*，那么可以使用 *else* 子句来处理这种情况。

更改您的代码以使用 *if-let* 语句来测试所有三个变量中的有效值。

>~~**`let avgReading = (reading1! + reading2! + reading3!) / 3`**~~ 
**`if let r1 = reading1,`**
  **`let r2 = reading2,`**
**  `let r3 = reading3 {`**
**    `let avgReading = (r1 + r2 + r3) / 3`**
**`} else {`**
  **`let errorString = "Instrument reported a reading that was nil."`**
**`}`**

reading3 目前为 *nil*，所以其对 *r3* 的赋值失败，侧栏显示错误字符串。

要查看其他情况下的操作，请将将值分配给 *reading3* 的语句恢复。 现在所有三个读数都有值，所有三个变量都是有效的，边栏更新显示三个 reading 变量的平均值。

## 检索字典 ##

回想一下，数组超出它的范围会导致一个 异常(trap)。 而字典则不一样，检索字典的结果是可选的：

>`let nameByParkingSpace = [13: "Alice", 27: "Bob"]`    [13: "Alice", 27: "Bob"]
**`let space13Assignee: String? = nameByParkingSpace[13]`**  "Alice"
**`let space42Assignee: String? = nameByParkingSpace[42]`**  nil

如果 键(key) 不在字典中，结果将为零。 与其他可选值一样，当检索字典时，通常使用 *if-let*：

>~~**`let space13Assignee: String? = nameByParkingSpace[13]`**~~
**`if let space13Assignee = nameByParkingSpace[13] {`**
**  `print("Key 13 is assigned in the dictionary!")`**
**`}`**

## 循环和字符串插值 ##

Swift 具有您可能熟悉的其他语言的所有控制流程语句：*if-else*，*while*，*for*，*for-in*，*repeat-while* 和 *switch*。 然而，即使如此，它们与你习惯的有一些区别。 Swift 和 C语言风格的这些语句之间的关键区别在于，在这些语句表达式上不需要括号，Swift 只要求对子句使用大括号。

此外，*if* 和 *while* 类语句的表达式必须为 **Bool**。

Swift 没有传统的 C 风格的循环，你可能习惯了。 相反，您可以使用 Swift 的 **Range** 类型和 for-in 语句更简单地完成相同的事情：

>`let range = 0..<countingUp.count`
`for i in range {`
  `let string = countingUp[i]`
  `// Use 'string'`
`}`

枚举数组中的项是最直观的：

>`for string in countingUp {`
  `// Use 'string'`
`}`

如果你想要数组中的每一项的索引呢？ Swift的 **enumerated（）** 函数从其参数返回整数和值的序列：

>`for (i, string) in countingUp.enumerated() {`
  `// (0, "one"), (1, "two")`
`}`

你会问那些括号是什么？ **enumerated（）** 函数返回一个 *元组(tuple)* 序列。 元组是与数组类似的值的有序分组，但元组的每个成员可能有不同的类型。 在这个例子中，元组是 **(Int，String)** 类型。 在这本书中我们不会花太多时间在元组上，它们不会在iOS API中使用，因为 Objective-C 不支持它们。 但是，它们可以在您的 Swift 代码中使用。

元组的另一个应用是枚举字典的内容：

>`let nameByParkingSpace = [13: "Alice", 27: "Bob"]`<br>
**`for (space, name) in nameByParkingSpace {`**
  **`let permit = "Space \(space): \(name)"`**
**`}`**

你注意到字符串中的奇怪的标记吗？ 那就是Swift的 *字符串插值(string interpolation)*。 \(  和  ) 之间的表达式在运行时被检测并插入到字符串中。 在本示例中，您用的是局部变量，然而这同样可以在任何有效的 Swift 表达式，如方法调用中使用。

要查看循环每次迭代的 *permit* 变量的值，首先单击行 `let allow = Space\(space):\(name)` 结果侧边栏最右端的循环 *Show Result* 指示符。 您将看到代码下的 *permit* 的当前值。 按住 Control 键并点击结果并选择* Value History*（图2.5）。 这可以对观察循环代码中发生的情况非常有用。

**图2.5使用 Value History 来查看字符串插值的结果**

![](http://upload-images.jianshu.io/upload_images/1230738-735fcbc3f540de1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 枚举和Switch语句 ##

枚举是具有离散值集的类型。 定义一个描述 pie  的枚举：

>**`enum PieType {`**
  **`case apple`**
  **`case cherry`**
  **`case pecan`**
**`}`**<br>
**`let favoritePie = PieType.apple`**

Swift 有一个强大的 switch 语句，除了别的以外，它非常适合使用在枚举值的匹配：
	
>**`let name: String`**
**`switch favoritePie {`**
**`case .apple:`**
  **`name = "Apple"`**
**`case .cherry:`**
  **`name = "Cherry"`**
**`case .pecan:`**
  **`name = "Pecan"`**
**`}`**	

switch 语句的 case 必须是详尽的：必须明确地或通过 default：case 来考虑 switch 表达式的每个可能的值。 与 C 不同的是，Swift switch case 的情况不会落空——它仅执行匹配的情况下的代码。 （如果您需要C的 fall-through 行为，则可以使用 *fallthrough* 关键字显式请求它。）(译：也就是Swift 的 Switch 的 case 语句匹配后只执行当前 case 后的语句，不会像 C 一样没遇到 break 则继续执行下去)

Switch语句可以匹配许多类型，甚至范围：

>`let macOSVersion: Int = ...`
`switch macOSVersion {`
`case 0...8:`
  `print("A big cat")`
`case 9:`
  `print("Mavericks")`
`case 10:`
  `print("Yosemite")`
`case 11:`
  `print("El Capitan")`
`case 12:`
  `print("Sierra")`
`default:`
  `print("Greetings, people of the future! What's new in 10.\(macOSVersion)?")`
`}`

有关 switch 语句及其模式匹配功能的更多信息，请参阅 Apple 的 *Swift编程语言(Swift Programming Language)* 指南中的 *控制流程(Control Flow)* 部分。 （多看几遍）

## 枚举和原始值 ##

Swift枚举的原始值可以用 case 关联：

>`enum PieType:` **`Int`** `{`
  `case apple =` **`0`**
  `case cherry`
  `case pecan`
`}`

使用指定的类型，*rawValue* 可以是 **PieType** 的实例 ，然后使用该值初始化枚举类型。 这会返回一个可选的值，因为原始值可能与枚举的实际情况不符，因此它是可选绑定的绝佳候选。

>**`let pieRawValue = PieType.pecan.rawValue`**
 **`// pieRawValue is an Int with a value of 2`**
**` `**
**`if let pieType = PieType(rawValue: pieRawValue) {`**
  **`// Got a valid 'pieType'!`**
**`}`**

枚举的原始值通常是 **Int**，但它同样可以是任何整数或浮点数类型以及 **String** 和 **Character** 类型。

当原始值为整数类型时，如果未给出显式值，则值将自动递增。 对于 **PieType**，只有 **apple** case 被赋予了一个明确的值。 **cherry** 和 **pecan** 的值分别自动分配为 1 和 2 的原始值。

还有更多关于枚举的知识。 枚举的每个 case 都可以具有相关联的值。 您将在第20章中了解有关相关值的更多信息。

## 探索苹果的Swift文档 ##

要了解 Apple 的 Swift 文档，请从 *developer.apple.com/swift* 开始。 这里有两个特殊的资源来寻找。 我们建议将其加入书签并访问它们，以便您可以查看特定的概念或深入挖掘。

*The Swift Programming Language*

本指南介绍了Swift的许多功能。 它从基础开始，包括示例代码和大量细节。 它还包含 Swift 的语言参考和正式语法。

*Swift Standard Library Reference*

标准库参考文献列出了Swift类型，协议和全局（或免费）功能的细节。

您的作业是浏览 *Swift标准库参考(Swift Standard Library Reference)* 的 类型(Types) 部分 以及 *Swift编程语言指南(The Swift Programming Language guide)* 中的 *基础知识(The Basics)* ，*字符串(Strings)* 和 *字符(Characters)* 以及 *集合类型( Collection Types)*。 巩固您在本章中学到的知识，并熟悉这些资源提供的信息。 如果您在需要时知道在何处查找细节，那么您将会减少要记住它们的压力——这让您可以专注于 iOS 开发。
