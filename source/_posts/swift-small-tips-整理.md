---
title: Swift small tips 整理
date: 2016-04-05 18:40:14
tags: Swift
categories: iOS
---


从onvcat喵神的博客中学习摘录的<!--more-->

- **Singleton** 
```
class MyManager {
	static let sharedInstance = MyManager()
	private init(){}
}
//这种写法不仅简洁，而且保证了单例的独一无二。在初始化类变量的时候，Apple 将会把这个初始化包装在一次 swift_once_block_invoke 中，以保证它的唯一性。另外，我们在这个类型中加入了一个私有的初始化方法，来覆盖默认的公开初始化方法，这让项目中的其他地方不能够通过 init 来生成自己的 MyManager 实例，也保证了类型单例的唯一性。如果你需要的是类似 defaultManager 的形式的单例 (也就是说这个类的使用者可以创建自己的实例) 的话，可以去掉这个私有的 init 方法。
```
 - **Use the raw type of Swift is more efficiency than use cocoa type**
 - **Random number generate**
```
func randomInRange(range: Range<Int>) -> Int {
    let count = UInt32(range.endIndex - range.startIndex)
    return  Int(arc4random_uniform(count)) + range.startIndex
}

for _ in 0...100 {
    print(randomInRange(1...6))
}
```
- **Typealias 是用来为已经存在的类型重新定义名字的，通过命名，可以使代码变得更加清晰。使用的语法也很简单**
```
typealias  distance = Double
typealias  loaction = CGPoint
//also for the generic types ,but it must be specified
typealias workerName = Person<String>
```
- **Conditional compilation**
```
#if <condition>
#elseif<condition> //optional
#else				//optional
#end
```

| function     |     param | 
| :-------- | --------:| 
| os()   |   OSX,iOS | 
| arch   |   x86_64,arm,arm64,i386 | 

注意这些方法和参数都是大小写敏感的。举个例子，如果我们统一我们在 iOS 平台和 Mac 平台的关于颜色的 API 的话，一种可能的方法就是配合 typealias 进行条件编译：
```
#if os(OSX)
    typealias Color = NSColor
#else
    typealias Color = UIColor
#endif
```
另一种方式是对自定义的符号进行条件编译，比如我们需要使用同一个 target 完成同一个 app 的收费版和免费版两个版本，并且希望在点击某个按钮时收费版本执行功能，而免费版本弹出提示的话，可以使用类似下面的方法：
```
@(IOS) {
    #if FREE_VERSION
        // 弹出购买提示，导航至商店等
    #else
        // 实际功能
    #endif
}
```
在这里我们用 FREE_VERSION 这个编译符号来代表免费版本。为了使之有效，我们需要在项目的编译选项中进行设置，在项目的 Build Settings 中，找到 Swift Compiler - Custom Flags，并在其中的 Other Swift Flags 加上 -D FREE_VERSION 就可以了。

- **compilation tag**
//MARK: 
//TODO:
//FIXME:

- **Variadic functions**
 写一个可变参数的函数只需要在声明参数时在类型后面加上 ... 就可以了.我们可以随意地放置可变参数的位置，而不必拘泥于最后一个参数
``` 
 func myFunc(numbers: Int..., string: String) {
    numbers.forEach {
        for i in 0..<$0 {
            print("\(i + 1): \(string)")
        }
    }
}

myFunc(1, 2, 3, string: "hello")
// 输出：
// 1: hello
// 1: hello
// 2: hello
// 1: hello
// 2: hello
// 3: hello
```
- @UIApplicationMain
Swift 的 app 也是需要 main 函数的，只不过默认情况下是 @UIApplicationMain 帮助我们自动生成了而已。和 C 系语言的 main.c 或者 main.m 文件一样，Swift 项目也可以有一个名为 main.swift 特殊的文件。在这个文件中，我们不需要定义作用域，而可以直接书写代码。这个文件中的代码将作为 main 函数来执行。比如我们在删除 @UIApplicationMain 后，在项目中添加一个 main.swift 文件，然后加上这样的代码：
```
UIApplicationMain(Process.argc, Process.unsafeArgv, nil,
    NSStringFromClass(AppDelegate))

```
现在编译运行，就不会再出现错误了。当然，我们还可以通过将第三个参数替换成自己的 UIApplication 子类，这样我们就可以轻易地做一些控制整个应用行为的事情了。比如将 main.swift 的内容换成：

```
import UIKit
//everytime sendEvent,you can listen this event
class MyApplication: UIApplication {
    override func sendEvent(event: UIEvent!) {
        super.sendEvent(event)
        print("Event sent: \(event)");
    }
}

UIApplicationMain(Process.argc, Process.unsafeArgv,
    NSStringFromClass(MyApplication), NSStringFromClass(AppDelegate))
```
- **Initialization method order**
```
class Cat {
    var name: String
    init() {
        name = "cat"
    }
}

class Tiger: Cat {
    let power: Int
    override init() {
	    power = 10     //--1--setting the subclass private properties
        super.init()   //--2--call the superclass init function,sometimes the swift will auto call
        name = "tiger"  //--3--to change the properties from the  superclass
    }
}
```

- **Required and convenience init function**
与 designated 初始化方法对应的是在 init 前加上 convenience 关键字的初始化方法。这类方法是 Swift 初始化方法中的 “二等公民”，只作为补充和提供使用上的方便。所有的 convenience 初始化方法都必须调用同一个类中的 designated 初始化完成设置，另外 convenience 的初始化方法是不能被子类重写或者是从子类中以 super 的方式被调用的。
>因此进行一下总结，可以看到**初始化方法永远遵循以下两个原则**：
>初始化路径必须保证对象完全初始化，这可以通过调用本类型的 designated 初始化方法来得到保证；
>子类的 designated 初始化方法必须调用父类的 designated 方法，以保证父类也完成初始化。
对于某些我们希望子类中一定实现的 designated 初始化方法，我们可以通过添加 required 关键字进行限制，强制子类对这个方法重写实现。这样做的最大的好处是可以保证依赖于某个 designated 初始化方法的 convenience 一直可以被使用。一个现成的例子就是上面的 init(bigNum: Bool)：如果我们希望这个初始化方法对于子类一定可用，那么应当将 init(num: Int) 声明为必须，这样我们在子类中调用 init(bigNum: Bool) 时就始终能够找到一条完全初始化的路径了
```
class ClassA {
    let numA: Int
    required init(num: Int) {
        numA = num
    }

    convenience init(bigNum: Bool) {
        self.init(num: bigNum ? 10000 : 1)
    }
}

class ClassB: ClassA {
    let numB: Int

    required init(num: Int) {
        numB = num + 1
        super.init(num: num)
    }
}

```
另外需要说明的是，其实不仅仅是对 designated 初始化方法，对于 convenience 的初始化方法，我们也可以加上 required 以确保子类对其进行实现。这在要求子类不直接使用父类中的 convenience 初始化方法时会非常有帮助。
- **实现多个接口时接口内方法冲突的解决方法**

```
protocol A {
    func bar() -> Int
}

protocol B {
    func bar() -> String
}
//两个接口中 bar() 只有返回值的类型不同。我们如果有一个类型 Class 同时实现了 A 和 B，我们要怎么才能避免和解决调用冲突呢？
class Class: A, B {
    func bar() -> Int {
        return 1
    }

    func bar() -> String {
        return "Hi"
    }
}
//对于 bar()，只要在调用前进行类型转换就可以了：
let instance = Class()
let num = (instance as A).bar()  // 1
let str = (instance as B).bar()  // "Hi"
```
- **Static and class keywords**
Swift 中表示 “类型范围作用域” 这一概念有两个不同的关键字，它们分别是 static 和 class。这两个关键字确实都表达了这个意思
在非 class 的类型上下文中，我们统一使用 static 来描述类型作用域。这包括在 enum 和 struct 中表述类型方法和类型属性时。
class 关键字相比起来就明白许多，是专门用在 class 类型的上下文中的，可以用来修饰类方法以及类的计算属性。但是有一个例外，class 中现在是不能出现 class 的存储属性的
**在使用的时候，struct 或 enum 中仍然使用 static，而在 class 里我们既可以使用 class 关键字，也可以用 static，**
**现在只需要记住结论，在任何时候使用 static 应该都是没有问题的。**

-  **可选接口和接口扩展**
```
protocol OptionalProtocol {
    optional func optionalMethod()  // 可选
    func necessaryMethod()          // 必须
    optional func anotherOptionalMethod() // 可选
}
//在 Swift 2.0 中，我们有了另一种选择，那就是使用 protocol extension。我们可以在声明一个 protocol 之后再用 extension 的方式给出部分方法默认的实现。这样这些方法在实际的类中就是可选实现的了。还是举上面的例子，使用接口扩展的话，会是这个样子：
protocol OptionalProtocol {
    func optionalMethod()        // 可选
    func necessaryMethod()       // 必须
    func anotherOptionalMethod() // 可选
}

extension OptionalProtocol {
    func optionalMethod() {
        print("Implemented in extension")
    }

    func anotherOptionalMethod() {
        print("Implemented in extension")
    }
}

class MyClass: OptionalProtocol {
    func necessaryMethod() {
        print("Implemented in Class3")
    }

    func optionalMethod() {
        print("Implemented in Class3")
    }
}

let obj = MyClass()
obj.necessaryMethod() // Implemented in Class3
obj.optionalMethod()  // Implemented in Class3
obj.anotherOptionalMethod() // Implemented in extension
```

- **多类型和容器**
绝大多数情况下，我们想要放入一个容器中的元素或多或少会有某些共同点，这就使得用接口来规定容器类型会很有用。比如上面的例子如果我们希望的是打印出容器内的元素的 description，可能我们更倾向于将数组声明为 [CustomStringConvertible] 的：
```
import Foundation
let mixed: [CustomStringConvertible] = [1, "two", 3]

for obj in mixed {
    print(obj.description)
}
```
这种方法虽然也损失了一部分类型信息，但是相对于 Any 或者 AnyObject 还是改善很多，在对于对象中存在某种共同特性的情况下无疑是最方便的。另一种做法是使用 enum 可以带有值的特点，将类型信息封装到特定的 enum 中。下面的代码封装了 Int 或者 String 类型：
```
import Foundation
enum IntOrString {
    case IntValue(Int)
    case StringValue(String)
}

let mixed = [IntOrString.IntValue(1),
             IntOrString.StringValue("two"),
             IntOrString.IntValue(3)]

for value in mixed {
    switch value {
    case let .IntValue(i):
        print(i * 2)
    case let .StringValue(s):
        print(s.capitalizedString)
    }
}

// 输出：
// 2
// Two
// 6
```
- **内存管理，WEAK 和 UNOWNED**
Swift 是自动管理内存的,Swift 会替我们管理和分配内存。而释放的原则遵循了自动引用计数 (ARC) 的规则：当一个对象没有引用的时候，其内存将会被自动回收。但是，所有的自动引用计数机制都有一个从理论上无法绕过的限制，那就是循环引用 (retain cycle) 的情况。
就是 unowned 设置以后即使它原来引用的内容已经被释放了，它仍然会保持对被已经释放了的对象的一个 "无效的" 引用，它不能是 Optional 值，也不会被指向 nil。如果你尝试调用这个引用的方法或者访问成员属性的话，程序就会崩溃。而 weak 则友好一些，在引用的内容被释放后，标记为 weak 的成员将会自动地变成 nil (因此被标记为 @weak 的变量一定需要是 Optional 值)。关于两者使用的选择，Apple 给我们的建议是如果能够确定在访问时不会已被释放的话，尽量使用 unowned，如果存在被释放的可能，那就选择用 weak。
>日常工作中一般使用弱引用的最常见的场景有两个：
>设置 delegate 时
>在 self 属性存储为闭包时，其中拥有对 self 引用时

```
//比如我们有一个负责网络请求的类，它实现了发送请求以及接收请求结果的任务，其中这个结果是通过实现请求类的 protocol 的方式来实现的，这种时候我们一般设置 delegate 为 weak：
// RequestManager.swift
class RequestManager: RequestHandler {

    @objc func requestFinished() {
        print("请求完成")
    }

    func sendRequest() {
        let req = Request()
        req.delegate = self

        req.send()
    }
}

// Request.swift
@objc protocol RequestHandler {
    optional func requestFinished()
}

class Request {
    weak var delegate: RequestHandler!;

    func send() {
        // 发送请求
        // 一般来说会将 req 的引用传递给网络框架
    }

    func gotResponse() {
        // 请求返回
        delegate?.requestFinished?()
    }
}
//req 中以 weak 的方式持有了 delegate，因为网络请求是一个异步过程，很可能会遇到用户不愿意等待而选择放弃的情况。这种情况下一般都会将 RequestManager 进行清理，所以我们其实是无法保证在拿到返回时作为 delegate 的 RequestManager 对象是一定存在的。因此我们使用了 weak 而非 unowned，并在调用前进行了判断。

```
另一种闭包的情况稍微复杂一些：我们首先要知道，闭包中对任何其他元素的引用都是会被闭包自动持有的。如果我们在闭包中写了 self 这样的东西的话，那我们其实也就在闭包内持有了当前的对象。这里就出现了一个在实际开发中比较隐蔽的陷阱：如果当前的实例直接或者间接地对这个闭包又有引用的话，就形成了一个 self -> 闭包 -> self 的循环引用。最简单的例子是，我们声明了一个闭包用来以特定的形式打印 self 中的一个字符串：
```
class Person {
    let name: String
    lazy var printName: ()->() = {
        print("The name is \(self.name)")
    }

    init(personName: String) {
        name = personName
    }

    deinit {
        print("Person deinit \(self.name)")
    }
}

var xiaoMing: Person? = Person(personName: "XiaoMing")
xiaoMing!.printName()
xiaoMing = nil
// 输出:
// The name is XiaoMing，没有被释放

//printName 是 self 的属性，会被 self 持有，而它本身又在闭包内持有 self，这导致了 xiaoMing 的 deinit 在自身超过作用域后还是没有被调用，也就是没有被释放。为了解决这种闭包内的循环引用，我们需要在闭包开始的时候添加一个标注，来表示这个闭包内的某些要素应该以何种特定的方式来使用。可以将 printName 修改为这样：

lazy var printName: ()->() = {
    [weak self] in
    if let strongSelf = self {
        print("The name is \(strongSelf.name)")
    }
}
// 输出:
// The name is XiaoMing
// Person deinit XiaoMing

```
//skip模式匹配 正则表达式

-  **值类型和引用类型**
Swift 的类型分为值类型和引用类型两种，值类型在传递和赋值时将进行复制，而引用类型则只会使用引用对象的一个 "指向"。
swift中除了class为引用类型外，包括Dictionary，struct，enum，Array的其他所有类型都是值类型。
在使用数组合字典时的最佳实践应该是，按照具体的数据规模和操作特点来决定到时是使用值类型的容器还是引用类型的容器：
>在需要处理大量数据并且频繁操作 (增减) 其中元素时，选择 NSMutableArray 和 >NSMutableDictionary 会更好，而对于容器内条目小而容器本身数目多的情况，应该使用 >Swift 语言内建的 Array 和 Dictionary。
- **... 和 ..<**
我们可以通过 ... 或者 ..< 来连接两个字符串。一个常见的使用场景就是检查某个字符是否是合法的字符。比如想确认一个单词里的全部字符都是小写英文字母的话，可以这么做：
```
let test = "helLo"
let interval = "a"..."z"
for c in test.characters {
    if !interval.contains(String(c)) {
        print("\(c) 不是小写字母")
    }
}

// 输出
// L 不是小写字母
```
在日常开发中，我们可能会需要确定某个字符是不是有效的 ASCII 字符，和上面的例子很相似，我们可以使用 \0...~ 这样的 ClosedInterval 来进行 (\0 和 ~ 分别是 ASCII 的第一个和最后一个字符)。
//skip ANYCLASS，元类型和 .SELF

- **自省**
向一个对象发出询问，以确定它是不是属于某个类，这种操作就称为自省，-isKindOfClass: 判断 obj1 是否是 ClassA 或者其子类的实例对象；而 isMemberOfClass: 则对 obj2 做出判断，当且仅当 obj2 的类型为 ClassB 时返回为真
>iskindofClass,isMemberOfClass can be directly used in Swift
>the keyword 'is' ,为了快速确定类型，Swift 提供了一个简洁的写法：对于一个不确定的类型，我们现在可以使用 is 来进行判断。is 在功能上相当于原来的 isKindOfClass，可以检查一个对象是否属于某类型或其子类型。is 和原来的区别主要在于亮点，首先它不仅可以用于 class 类型上，也可以对 Swift 的其他像是 struct 或 enum 类型进行判断。使用起来是这个样子的：
```
class ClassA { }
class ClassB: ClassA { }

let obj: AnyObject = ClassB()

if (obj is ClassA) {
    print("属于 ClassA")
}

if (obj is ClassB) {
    print("属于 ClassB")
}
```
- **属性观察**
```
class A {
    var number :Int {
        get {
            print("get")
            return 1
        }

        set {print("set")}
    }
}

class B: A {
    override var number: Int {
        willSet {print("willSet")}
        didSet {print("didSet")}
    }
}
//
let b = B()
b.number = 0

// 输出
// get
// willSet
// set
// didSet
```

- **局部Scope**
>首先是超过作用域后里面的临时变量就将失效，可以稍微提高一些代码的效率
>在合适的位置插入括号也利于方法的梳理，对于那些不太方便提取为一个单独方法，但是又应该和当前方法内的其他部分进行一些区分的代码，使用大括号可以将这样的结构进行一个相对自然的划分。
```
//在 Swift 2.0 中，为了处理异常，Apple 加入了 do 这个关键字来作为捕获异常的作用域。这一功能恰好为我们提供了一个完美的局部作用域，现在我们可以简单地使用 do 来分隔代码了：
do {
    let textLabel = UILabel(frame: CGRectMake(150, 80, 20, 40))
    //...
}
Swift 里当然没有 GNU C 的扩展，但是使用匿名的闭包的话，写出类似的代码也不是难事：
titleLabel = {
    let label = UILabel(frame: CGRectMake(150, 30, 20, 40))
    label.textColor = UIColor.redColor()
    label.text = "Title"
    self.view.addSubview(label)
    return label
}()
这也是一种隔离代码的很好的方式。

```
- For the object such as struct ,class ,if you want to debug it ,you can define a variable description to make the fuction print to print the infomation you expected

- Use the assert merits:
> when occured the error duriing runing,you can easily debug it by the stack information ,
>the asseert func only be compiled when dubug,when the app be bundled ,it willnot be compiled ,so you dont need to clear it,

