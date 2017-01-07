# Swift 3 学习笔记

### Everything is an object!
- `1` 也是对象，不过是`struct`对象，可以接收**message**。可以接收**message**的还包括`enum`
- 因此**Swift**有3种对象类型：`class`, `struct`, `enum`

### Swift文件结构
- Module `import` 声明
- 变量声明：文件顶层声明的变量为全局变量
- 函数声明：文件顶层声明的函数也是全局函数
- 对象类型声明：`class`, `struct`和`enum`的声明

### 函数
- 忽略external parameter name: `func sum(_ x:Int, _ y:Int) -> Int {...}`，调用时即:`sum(1,2)`;
如果不忽略：`func sum(x:Int, y:Int) -> Int {...}`，调用时即：`sum(x:1, y:2)`

- 不一样的external和internal parameter name: `func sum(num1 x:Int, num2 y:Int) -> Int {...}`，函数内部使用`x`和`y`， 调用时用：`sum(num1:1, num2:2)`

- 初始化参数，即调用者没有给参数时，函数内部就使用初始化值：`func sum(x:Int, y:Int = 2)`，如果调用`sum(1)`，其实是`sum(1, 2)`

- 变参：在类型后面跟`...`，函数内部接收到的是一个`array`：`func sayStrings(arrayOfStrings:String ...) {...}`

- 修改参数对应的对象(完全修改handle，若只修改`class`对象的属性，不需要以下条件)所需条件：
  - 参数声明为`inout`，如：`func removeCharacter(_ c:Character, from s: inout String) -> Int {...}`
  - 调用函数时传递的变量必须是`var`
  - 传递变量的地址，即加上`&`，如`&myString`

- 函数在Swift里是第一公民：
  - 函数可以assign给变量
  - 函数可以作为函数的参数传递
  - 函数可以作为函数的返回值返回

- 匿名函数：
  - 仅写函数体，并使用花括号`{}`包裹
  - 所需参数以及返回值在开始花括号`{`下的第一行声明，并跟着关键字`in`
  - 声明例子：
```swift
{
  () -> () in
  self.myButton.frame.origin.y += 30
}
```
  - 直接使用：
  ```swift
  UIView.animate(withDuration:0.4,
    animations: {
    () -> () in
    self.myButton.frame.origin.y += 20
    },
    completion: {
      (finished:Bool) -> () in
      print("finished: \(finished)")
      }
  )
  ```

  - 更简洁的写法：
    - 省略箭头与返回类型：当返回类型对于complier是明确时（e.g.Cocoa API），如：`() in`
    - 省略整个第一行`() -> () in`：当没有参数以及没有返回值时
    - 省略参数类型：当参数类型对compiler是明确时，如：`(isFinished) in`
    - 省略括号：如果参数类型可以省略，括号也可以省略，如：`isFinished in`
    - 即使有参数也可以照样省略：前提是返回值可以省略，引用参数可以用`$0`，`$1`，以此类推
    - 省略参数名：当函数体不需要用到参数时，如`_ in`
    - 省略函数参数标签：当匿名函数作为最后一个参数传递时，调用函数可以用右括号`)`关闭，然后空格，紧跟匿名函数 (也称为trailing function)，如：
    ```swift
    UIView.animate(withDuration:0.4, animations: {
        self.myButton.frame.origin.y += 20
        }) {
            _ in
            print("finished!")
    }
    ```
    - 省略调用函数的括号：当使用**省略函数参数标签**时，如果传递的匿名函数是调用函数的唯一参数，此时可以省略调用函数的括号。（这种激活条件我是暂时get不到有什么好了= =），如：
    ```swift
    func doThis(_ f:()->()) {
            f()
        }
        doThis { // no parentheses!
            print("Howdy")
        }
    ```
    - 省略关键字`return`：当匿名函数仅包含一句声明且包含返回声明时，返回关键字`return`可以省略。换句话说，假设在一个要求函数返回某个值的情况，如果匿名函数仅包含一句声明，Swift就会认为此声明是一个带有返回值的表达式，如：
    ```swift
    func greeting() -> String {
        return "Howdy"
    }
    func performAndPrint(_ f:()->String) {
        let s = f()
        print(s)
    }
    performAndPrint {
        greeting() // meaning: return greeting()
    }
    ```
  - 一个不错的匿名函数简化例子，简化前：
  ```swift
  let arr = [2, 4, 6, 8]
  let arr2 = arr.map ({
      (i:Int) -> Int in
      return i*2 })
  ```
  简化后：
  ```swift
  let arr = [2, 4, 6, 8]
  let arr2 = arr.map {$0*2}
  ```
- 闭包（closure）：Swift里，函数都是闭包。闭包指函式及其所处的参数环境所组成的实体。换一种说法，就是函数主体可以捕获（capture）主体内所使用的外部变量引用。

- 返回函数体：
  - 例子：
    ```swift
    func makeRoundedRectangleMaker(_ sz:CGSize) -> () -> UIImage {
            func f () -> UIImage {
                let im = imageOfSize(sz) {
                    let p = UIBezierPath(
                        roundedRect: CGRect(origin:CGPoint.zero, size:sz),
                        cornerRadius: 8)
                    p.stroke()
                }
                return im
            }
            return f
        }
    ```
  - 箭头标志后的类型都是返回值类型

- 函数作为参数，在调用函数体内捕获调用环境：
  - 需要开启此功能，调用函数体需要指明：`@escaping`
  ```swift
  func returnFuncToBeCalledLater(_ f:@escaping () -> ()) -> () -> () {...}
  ```

- 函数引用
  - 函数的简单名称(bare name)称为函数引用，如：`bark()`和`bark(loudly:Bool)`的函数引用都是`bark`
  - 为确保确定性，有**全名(full name)**和**签名(signature)**两种方式：
    - 全名：类似OC，简单名称(参数括号前的名字)加上参数外部名字与冒号，如：`say(_ s:String, times:Int)`全名是`say(_:times:)`
    - 签名：简单名称+`as`+参数与返回类型，如：`say(_ s:String, times:Int)`签名是`say as (String, Int) -> Void` （个人意见：一般来说签名比全名更精确，例如函数重载的话，因为简单名称和参数数量相同，所以只有签名才能区别开）
  - **Selector: **在添加action的参数位置使用`#selector()`传入函数名，函数名使用全名，如:`#selector(didClickOnSendButton(_:params2:params3:))`。使用`#selector()`可以让compiler检查target是否有此名字的函数，避免因为名字错误而出现的runtime错误


### 变量
- 变量范围
  - **全局变量**：文件顶层声明，可以被**文件内部**以及**同一个module的其他文件**看到
  - **属性**：对象类型(`class`, `enum`, `struct`)的顶层变量，分为对象属性和类属性
    - 对象属性：默认情况下是对象属性
    - 类属性：需要在声变量明前添加`static`或`class`，如：`static let myNum: Int = 123`
  - **本地变量**：函数体内的变量

- 变量声明
  - `let`：只能分配一次值；`var`
  - 声明与初始化同时进行：`var num: Int = 0`
  - 虽然初始化可以省略变量类型，但显式表明类型是好习惯，因为更清楚，而且compiler可能会选错类型或者无法推断类型。
  - 当一个变量的地址要传递给一个函数时，此变量需要事先初始化，即使初始化值是假的。

- 计算(computed)初始化：匿名函数，define-and-call

- 计算变量值：变量所指是一个函数，而非一个变量值。包括`getter`和`setter`：
  - 必须是`var`，类型必须显式声明，类型后紧跟花括号`{`
  - `get`必须返回同类型的变量值
  - `set`行为就如一个接受一个参数的函数。参数会自动保存为本地变量`newValue`
  - `set`的参数可以自定义：`set (val) {...}`
  - `set`可以省略。如此的话，变量变成read-only
  - `set`省略的话，`get`也不需要注明，计算体自动识别为`get`的代码体
```swift
var now : String {
    get {
        return Date().description
    }
    set {
        print(newValue)
    }
}
```
- `setter`观察者
  - 不同于计算变量值的`setter`需要另外一个变量储存变量值，`setter`观察者用在一个赋值变量上，并且提供`willSet`和`didSet`两个回调函数，如：
  ```swift
  var s = "whatever" {
      willSet {
          print(newValue)
      }
      didSet {
          print(oldValue)
          // self.s = "something else"
      }
  }
  ```
  - `willSet`：接受到的是新的值，默认是`newValue`，可以改名。旧值此时还在变量中并且可以引用
  - `didSet`：接受到的是旧的值，默认是`oldValue`，可以改名。新值此时已经在变量中并且可以引用
  - 计算变量值不能添加上述观察者因为不需要：在`set`里可以自由在设定变量值前后添加逻辑

- 懒初始化(lazy initialization)：如果一个变量在声明中同时有声明初始化，在懒初始化下，只有当
变量被取用(access)时才会计算或赋初始值
  - 默认懒初始化：全局变量(global)和静态变量(static)
  - 需要显式声明初始化：对象属性。如：`lazy var name = "Desmond"`
  - 可用于创建singleton
  - 对象属性的懒初始化可用于创建成本高的变量。因为在需要时才创建，所以提高了效率
  - NOTE：全局变量和静态变量的懒初始化变量默认是线程安全和atomic的，但对象属性则不是

### 内建变量类型
- `Bool`：`struct`类型，取值`true`或`false`
  - 仅具有`true`或`false`的值，不像其他语言(Java或C)，0可以代表false
- `Optional`：可以看作一种数据类型，形象上可以理解为盒子，里面可以装着某个特定类型的数据，也可以是空的
  - 声明方式：1)`var stringMayBe = Optional("howdy")` 2)`var stringMayBe: String? = "howdy"`
  - `Optional`包了一种数据类型之后，就不能被赋值其他类型的值
  - 正式来说，`Optional`是一种泛型，如包了`String`的`Optional`是Optional<String>
  - 把一个同类型的值（即`Optional`包着的类型的其他值）复制给`Optional`时，编译器会隐式将新值包成`Optional`
  - 要取得`Optional`里的数据值，需要解包，如：`var stringInside = stringMayBe!`（`!`强制解包）
  - 隐式`Optional`：取用时不需要使用`!`来解包，编译器隐式添加（当然显式添加`!`也没问题），如：`var stringMayBe: String! = "howdy"; var stringInside = stringMayBe`。
  本质上讲，隐式`Optional`依旧是`Optional`，只是多了隐式解包数值。并且在Swift3里，隐式`Optional`在赋值中不会传递，即赋值后会成为普通`Optional`，需要显式解包符号`!`
  - `Optional`**的其中一个重点是没有包数据时的处理。** `nil`用于指代一个`Optional`没有包着数据，有以下两种情况：
    - 检验一个`Optional`是否包着数据：检查`Optional`是否等于`nil`，如：`stringMayBe == nil`，返回`true`的话说明`stringMayBe`是空的
    - 指代一个`Optional`是空的：把`nil`赋值给一个`Optional`，如：`stringMayBe = nil`，结果是`Optional`包着相同数据类型，但没有值
  - `nil`的意思是说一个`Optional`包着同样数据类型，但没有数据实体。
  - 一个`Optional`的`var`如果没有初始化的话，默认是`nil`
  - **一个没有包裹数据实体的`Optional`不能被解包。** 如果解包这种等于`nil`的`Optional`，程序会崩溃。 --> 在解包`Optional`时，需要添加判断是否为`nil`的逻辑：
    - 最简单的方式是判断是否等于`nil`
  - **Optional chains:** 因为用`!`解包在`Optional`数据为空时会让程序崩溃，因此在需要发送信息(message)给所包的数据时，使用`?`来“选择性”地解包（unwrap the `Optional` *optionally*）
    - `?`选择性解包在发送信息给一个所包数据对象时的意思是：如果所包数据不为空，即解包并且发送信息；否则**不解包并且不发送信息**
    - `?`在发送信息后的返回值上同样返回`Optional`并且包着返回数据。并且只要有`?`解包，整个表达式就会返回`Optional`
  - `Optional`的比较：如果两个`Optional`使用`==`进行非`nil`的比较，是比较所包数据值，编译器会自动安全地解包。但是不能使用除`==`意外的比较（Swift2可以，3不行）。如：`<`和`>`。此时需要先确保不是`nil`后再解包进行比较。
  - `Optional`**的好处：**
    - 提供与Objective-C数据类型互换的方式（因为需要处理OC的`nil`）。
    - 推迟对象属性的初始化，如outlet：`@IBOutlet var myButton: UIButton!`。当viewController创建时，`myButton`并没有，因此`Optional`方便地给予其初始值为`nil`；接着当views被加载后`myButton`就指向一个`UIButton`对象，因此隐式`Optional`方便我们直接使用数据体。
    - 相类似的，用于实质数据需要时间生成的对象属性。
    - 允许一个变量表明自己是空的（可以理解为错误情况）还是有数值。
