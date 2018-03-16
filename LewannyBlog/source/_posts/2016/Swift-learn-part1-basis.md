---
title: Swift 学习 – 基础部分
date: 2016-11-21 20:17:39
tags: Swift
categories:  Swift 学习
---

`Swift` 是一门新的编程语言，用于编写 iOS 和 OS X 应用程序。Swift 结合了 C 和 Objective-C 的优点并且不受 C 兼容性的限制。Swift 使用安全的编程模式并添加了很多新特性，这将使编程更简单，扩展性更强，也更有趣。Swift 支持 Cocoa 和 Cocoa Touch 框架。以下是 Swift 部分学习笔记整理。

#### 常量和变量
常量的值一旦设定就不能改变，而变量的值可以随意更改。常量和变量必须在使用前声明，用`let`来声明常量，用`var`来声明变量。
```swift
let maxNum = 10  // 声明一个常量
var currentNum = 0 //声明一个变量
var x = 0.0, y = 0.0, z = 0.0 // 可以在一行中声明多个常量或者多个变量
```

<!--more-->


#### 类型标注
如果要添加类型标注，需要在常量或者变量名后面加上一个`冒号和空格`，然后加上类型名称。
```swift
var messageHellow: String // 声明一个类型为 String ，名字为 welcomeMessage 的变量
messageHellow = "Hello world"
var red, grenn, blue: Double
```

> 一般来讲类型标注可以省略，Swift可以推断出这个常量或者变量的类型


#### 常量和变量的命名
你可以用任何你喜欢的字符作为常量和变量名，包括 Unicode 字符，常量与变量名不能包含数学符号，箭头,保留的Unicode 码位。也不能以数字开头，但是可以在常量与变量名的其他地方包含数字。
```swift
let π = 3.1415926
let 你好 = "你好啊"
let 🐶 = "Dog"
var 'let' = "保留关键字命名"
```

> 如需要使用与Swift保留关键字相同的名称作为常量或者变量名，你可以使用反引号（`）将关键字包围的方式将其作为名字使用，如上所示。


#### 输出常量和变量
使用 `print(_:separator:terminator:)` 函数来输出当前常量或变量的值。
```swift
print(π)
// 3.1415926
```

Swift 用字符串插值（string interpolation）的方式把常量名或者变量名当做占位符加入到长字符串中 `\()`：
```swift
print("π的值大约是\(π)"...)
// 输出 "π的值大约是3.1415926...
```

#### 注释
和OC一样，Swift支持单行注释，多行注释 和行注释可以嵌套在其它的多行注释之中。


#### 关于分号
Swift 并不强制要求你在每条语句的结尾处使用分号，也可以按照自己的习惯添加分号。
```swift
let 🐟 = "Fish", print(🐟)
// 输出 Fish
```

> 有一种情况下必须要用分号，即你打算在同一行内写多条独立的语句。


#### 整数和整数范围
整数就是没有小数部分的数字，比如 42 和 -23 。整数可以是有符号（正、负、零）或者 无符号（正、零）。

> Swift 提供了8，16，32和64位的有符号和无符号整数类型，8位无符号整数类型是UInt8，32位有符号整数类型是 Int32 。就像 Swift 的其他类型一样，整数类型采用 大写命名法
 
你可以访问不同整数类型的 min 和 max 属性来获取对应类型的最小值和最大值：
```swift
let minValue = Unit8.min  // minValue 为 0，是 UInt8 类型
let maxvalue = Unit8.max  // maxValue 为 255，是 UInt8 类型
```


#### Int
Swift 提供了一个特殊的整数类型Int，一般来说，你不需要专门指定整数的长度。

- 在32位平台上，Int 和 Int32 长度相同。
- 在64位平台上，Int 和 Int64 长度相同。


#### UInt
Swift 也提供了一个特殊的无符号类型 UInt，长度与当前平台的原生字长相同。

- 在32位平台上，UInt 和 UInt32 长度相同。
- 在64位平台上，UInt 和 UInt64 长度相同。

> 尽量不要使用UInt，除非你真的需要存储一个和当前平台原生字长相同的无符号整数。


#### 浮点型
浮点数是有小数部分的数字，比如 3.14159 ，0.1 和 -273.15。Swift 提供了两种有符号浮点数类型。

- `Double` 表示64位浮点数。当你需要存储很大或者很高精度的浮点数时请使用此类型。
- `Float` 表示32位浮点数。精度要求不高的话可以使用此类型。


#### 类型安全和类型推断
Swift 是一个 `类型安全` （type safe）的语言，它会在编译你的代码时进行类型检查（type checks），并把不匹配的类型标记为错误。这可以让你在开发的时候尽早发现并修复错误。

因为有类型推断，和 C 或者 Objective-C 比起来 Swift 很少需要声明类型。
```swift
let meaningOfLife = 42
// meaningOfLife 会被推测为 Int 类型
```

当推断浮点数的类型时，Swift 总是会选择 Double 而不是Float。
```swift
let anotherPi = 3 + 0.14159
// anotherPi 会被推测为 Double 类型
```


#### 数值型字面量
下面的所有整数字面量的十进制值都是17，整数字面量可以被写作二、八、十、十六进制。
```swift
let decimalInteger = 17 // 一个十进制数，没有前缀
let binaryInteger = 0b10001 // 一个二进制数，前缀是 0b
let octalInteger = 0o21 // 一个八进制数，前缀是 0o
let hexadecimalInteger = 0x11 // 一个十六进制数，前缀是 0x
```

浮点字面量可以是十进制（没有前缀）或者是十六进制（前缀是 0x ）。十进制浮点数也可以有一个可选的指数（exponent)，通过大写或者小写的 e 来指定；十六进制浮点数必须有一个指数，通过大写或者小写的 p 来指定。

如果一个十进制数的指数为 exp，那这个数相当于基数和10^exp的乘积。
```swift
1.25e2 表示 1.25 × 10^2，等于 125.0。
1.25e-2 表示 1.25 × 10^-2，等于 0.0125。
```

如果一个十六进制数的指数为exp，那这个数相当于基数和2^exp的乘积。
```swift
0xFp2 表示 15 × 2^2，等于 60.0。
0xFp-2 表示 15 × 2^-2，等于 3.75。
```

下面的这些浮点字面量都等于十进制的 12.1875 。
```swift
let decimalDouble = 12.1875
let exponentDouble = 1.21875e1
let hexadecimalDouble = 0xC.3p0
```

为增强可读性，整数和浮点数都可以添加额外的零并且包含下划线，并不会影响字面量。
```swift
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1
```


#### 数值型类型转换
通常来讲，即使代码中的整数常量和变量已知非负，也请使用 `Int` 类型。


#### 整数转换
不同整数类型的变量和常量可以存储不同范围的数字。 Int8 类型的常量或者变量可以存储的数字范围是 -128~127 ，而 UInt8 类型的常量或者变量能存储的数字范围是 0~255 ，如果数字超出了常量或者变量可存储的范围，编译的时候会报错。
```swift
let cannotBeNegative: UInt8 = -1
// UInt8 类型不能存储负数，所以会报错
let tooBig: Int8 = Int8.max + 1
// Int8 类型不能存储超过最大值的数，所以会报错
```

要将一种数字类型转换成另一种，你要用当前值来初始化一个想要类型的新数字，如下面调用 `UInt16(one)` 来创建一个新的UInt16数字并用one的值来初始化，将one转换成UInt16类型来完成运算。
```swift
let twoThousand: Unit16 = 2_000
let one: Unit8 = 1
let twoThousandAndOne = twoThousand + Unit16(one)
```

> SomeType(ofInitialValue) 是调用 Swift 构造器并传入一个初始值的默认方法。注意，你并不能传入任意类型的值，只能传入 UInt16 内部有对应构造器的值。不过你可以扩展现有的类型来让它可以接收其他类型的值。
 
 
#### 整数和浮点数转换
整数和浮点数的转换必须显式指定类型
```swift
let three = 3
let pointValue = 2.14159
let sumValue = three + Int(pointValue)
// 结果 5
```

当用这种方式来初始化一个新的整数值时，浮点值会被截断。也就是说 4.75 会变成 4，-3.9 会变成 -3。


#### 类型别名
类型别名（type aliases）就是给现有类型定义另一个名字，使用 `typealias` 关键字来定义类型别名，类似于OC中的宏定义。
```swift
typealias AudioSample = Unit16
var max = AudioSample.min
// max = 0
```


#### 布尔值
Swift 有一个基本的布尔类型 `Bool`，布尔值指逻辑上的值，因为它们只能是真或者假。Swift 有两个布尔常量，`true 和 false` 。
```swift
let isSuccess = true
if isSuccess {
    print("Success")
} else {
    print("Failed")
}
```

如果你在需要使用 Bool 类型的地方使用了非布尔值，Swift 的类型安全机制会报错。
```swift
let i = 1
if i == 1 {
    // 这个例子会编译成功
}
```


#### 元组类型
一种全新的数据类型，`元组（tuples）`把多个值组合成一个复合值。元组内的值可以是任意类型，并不要求是相同类型。
```swift
let http404Err = (404, "not found")
// http404Error 的类型是 (Int, String)，值是 (404, "Not Found")
```

你可以将一个元组的内容`分解（decompose）`成单独的常量和变量来使用，分解的时候可以把要忽略的部分用下划线 `_` 标记。
```swift
let(code, message) =  http404Err
print("code = \(code), message = \(message))
// 输出 code = 404, message = not found
```

可以在定义元组的时候给单个元素命名。
```swift
let http200Status = (code: 200, description: "OK")
```

使用时可以使用名字或者下标老获取对应的值。
```swift
print("code = http200Status.0, description = http200Status.1")
// 输出 code = 200, description = OK
print("code = http200Status. code, description = http200Status. description")
// 输出同上
```

> 作为函数返回值时，元组非常有用。但是并不适合创建复杂的数据结构。
元组在临时组织值的时候很有用，但是并不适合创建复杂的数据结构。如果你的数据结构并不是临时使用，请使用类或者结构体而不是元组。


#### 可选类型
使用`可选类型（optionals）`来处理值可能缺失的情况，可选类型可表示为有值 或者 无值两种情况。
如：将一个 String 类型转换为 In t，若 String 为 “123” 则可以，为 “Hello world” 则会失败。

下面的例子使用这种构造器来尝试将一个 String 转换成 Int：
```swift
let possibleNum = 123
let convertNum = Int(possibleNum)
```

因为该构造器可能会失败，所以它返回一个可选类型 Int? 而不是 Int ，问号暗示包含的值是可选类型，也就是说可能包含 Int 值也可能不包含值。


#### nil
你可以给可选变量赋值为 nil 来表示它没有值:
```swift
var errorCode: Int? = 404
errorCode = nil
// 只有可选类型才可赋空值， nil 不能用于非可选的常量和变量
```

如果你声明一个可选常量或者变量但是没有赋值，它们会自动被设置为 nil。
```swift
var someNilStr: String?
// someNilStr 被自动设为 nil
```

> Swift 的 nil 和 Objective-C 中的 nil 并不一样。Objective-C 中，nil 是一个指向不存在对象的指针。在 Swift 中，nil 不是指针——它是一个确定的值，用来表示值缺失。任何类型的可选状态都可以被设置为 nil，不只是对象类型


#### if 语句以及强制解析
你可以使用 if 语句和 nil 比较来判断一个可选值是否包含值。你可以使用“相等”(==)或“不等”(!=)来执行比较
```swift
if someNilStr == nil {
    print("someNilStr 为 nil")
}
// 输出：someNilStr 为 nil
```

当你确定可选类型确实包含值之后，可以在可选的名字后面加一个感叹号（!）来获取值，这被称为可选值的强制解析（forced unwrapping）
```swift
var someNum: Int? = 123
if someNum != nil {
	print("someNum 的值为：\(someNum!)")
}
// 输出：someNum 的值为：123
```

#### 可选绑定
使用可选绑定（optional binding）来判断可选类型是否包含值，如果包含就把值赋给一个临时常量或者变量。可选绑定可以用在 if 和 while 语句中。
```swift
var errorCode: Int? = 404
if let newNum = Int(errorCode) {
	print("errorCode 值为 \(newNum)")
} else {
	print("errorCode 无值")
}
// 输出 errorCode 值为 404
```

如果转换成功，errorCode 常量可以在 if 语句的第一个分支中使用。它已经被可选类型包含的值初始化过，所以不需要再使用 ! 后缀来获取它的值。可以在可选绑定中使用常量和变量，可将 let newNum = Int(errorCode) 改为 var newNum = Int(errorCode) 并在 if 语句的第一个分支中操作 newNum 的值。

可以包含多个可选绑定或多个布尔条件在一个 if 语句中，使用逗号分开。只要有任意一个可选绑定的值为 nil，或者任意一个布尔条件为 false，则整个if条件判断为false。(也可使用嵌套 if 语句来处理)
```swift
if let firstNumber = Int("4"), let secondNumber = Int("42"), firstNumber < secondNumber && secondNumber < 100 {
    print("\(firstNumber) < \(secondNumber) < 100")
}
// 输出 "4 < 42 < 100"
```


#### 隐式解析可选类型
当可选类型被第一次赋值之后就可以确定之后一直有值，这种类型的可选状态被定义为隐式解析可选类型（implicitly unwrapped optionals）。把想可选的类型的后面的问号（ String? ）改成感叹号（ String! ）来声明一个隐式解析可选类型。

隐式解析并不需要每次都使用解析来获取可选值。下面的例子展示了可选类型 String 和隐式解析可选类型 String 之间的区别。

```swift
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // 需要感叹号来获取值
let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString  // 不需要感叹号
```

> 隐式解析相当于一个特殊的可选类型，如果一个变量之后可能变成nil的话不能使用隐式解析，应使用可选类型。如果你在隐式解析可选类型没有值的时候尝试取值，会触发运行时错误。可使用 if 语句来把隐式解析可选类型当做普通可选类型来判断它是否包含值，也可以在可选绑定中使用隐式解析可选类型来检查并解析它的值。


#### 错误处理
可以使用 错误处理（error handling） 来应对程序执行中可能会遇到的错误条件。当一个函数遇到错误条件，它能报错。调用函数的地方能抛出错误消息并合理处理。
```swift
func canThrowAnError() throws {
    // 这个函数有可能抛出错误
}
```

一个函数可以通过在声明中添加 throws 关键词来抛出错误消息。当你的函数能抛出错误消息时, 你应该在表达式中前置 try 关键词。
```swift
do {
    try canThrowAnError()
    // 没有错误消息抛出
} catch {
    // 有一个错误消息抛出
}
```

一个 do 语句创建了一个新的包含作用域,使得错误能被传播到一个或多个 catch 从句。例子：
```swift
func makeASandwich() throws {
    // ...
}
do {
    try makeASandwich()
    eatASandwich()
} catch SandwichError.outOfCleanDishes {
    washDishes()
} catch SandwichError.missingIngredients(let ingredients) {
    buyGroceries(ingredients)
}
```


#### 使用断言进行调试
断言（assertion）可以结束代码运行并通过调试来找到值缺失的原因。断言会在运行时判断一个逻辑条件是否为 true，如果条件判断为 true，代码运行会继续进行；如果条件判断为 false，代码执行结束，你的应用被终止。
使用断言你可以清楚地看到不合法的状态发生在哪里并检查断言被触发时你的应用的状态。此外，断言允许你附加一条调试信息。

你可以使用全局 assert(::file:line:) 函数来写一个断言。向这个函数传入一个结果为 true 或者 false 的表达式以及一条信息，当表达式的结果为 false 的时候这条信息会被显示。
```swift
let age = -3
assert(age >= 0, "A person's age cannot be less than zero")
// -3 小于 0 ，所以断言会触发
// 如果不需要断言信息 也可省略 assert(age >= 0)
```

> 当代码使用优化编译的时候，断言将会被禁用，例如在 Xcode 中，使用默认的 target Release 配置选项来 build 时，断言会被禁用


#### 何时使用断言
当条件可能为假时使用断言，但是最终一定要保证条件为真，这样你的代码才能继续运行：

1. 整数类型的下标索引被传入一个自定义下标实现，但是下标索引值可能太小或者太大。
2. 需要给函数传入一个值，但是非法的值可能导致函数不能正常执行。
3. 一个可选值现在是 nil，但是后面的代码运行需要一个非 nil 值。

断言可能导致你的应用终止运行，所以你应当仔细设计你的代码来让非法条件不会出现，但调试时使用断言可以快速发现问题。

持续更新中…

