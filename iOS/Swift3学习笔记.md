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
