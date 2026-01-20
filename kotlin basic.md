# kotlin

## 语法基础

### 位运算

| 函数         | 说明                   | 等效 Java 运算符 | 逻辑描述                                           |
| ------------ | ---------------------- | ---------------- | -------------------------------------------------- |
| `shl(bits)`  | **左移** (Shift Left)  | `<<`             | 在低位补 0。                                       |
| `shr(bits)`  | **右移** (Shift Right) | `>>`             | 保持符号位，高位补符号位（正数补 0，负数补 1）。   |
| `ushr(bits)` | **无符号右移**         | `>>>`            | 无论正负，高位一律补 0。                           |
| `and(bits)`  | **按位与** (And)       | `&`              | 两位均为 1 则结果为 1。                            |
| `or(bits)`   | **按位或** (Or)        | `                | `                                                  | 两位中有一位为 1 则结果为 1。 |
| `xor(bits)`  | **按位异或** (Xor)     | `^`              | 两位不同则结果为 1。                               |
| `.inv()`     | **按位取反** (Inverse) | `~`              | 1 变 0，0 变 1（这是普通成员函数，不是中缀函数）。 |

### 区间

```kt
1..10 // [1,10]
1..<10 // [1,10)
1 until 10 // [1,10)
10 downTo 1 // 10 9 ... 1
1..10 step 2 // 2 4 6 8 10
```

```kt
in // 在区间内
!in // 不在区间内
// e.g.
x in 1..10
```

### 字符 / 字符串

#### 字符

字符 (`Char`) 可以是 ASCII 或中文。**占用 2 字节, Unicode**

`.code` 获取其 ASCII (`Char -> Int`)。

转义：`\u`

```kt
val c = '\u2713' // 对应 Unicode 10003 '√'
```

#### 字符串

字符串中内容一旦确定，**无法进行修改，只能重新创建**

换行：`\n`

多行字符串

```kt
"""
123456
qwerty
"""
```

`"""` 为原始字符串，会包含所有缩进，需要用 `.trimIndent()` 消除。

```kt
"""
123456
qwerty
""".trimIndent()
```

字符串可以加别的类型，别的类型不可以加字符串。

```kt
var a = "Hello"
var b = 2026
var c = a + b // c = "Hello 2026"
var c = b + a // 编译错误，无法拼接
```

字符串模板：`$`, `${}`

```kt
var b = 2026
var a = "Hello $b" // c = "Hello 2026"
var a = "Hello ${b}" // 消除歧义

```

如果要单独使用 `$` 需要转义 `\$`。

### 条件判断

#### if 三目运算符

```kt
val score = 2
//这里判断socre是否大于60，是就得到Yes，否就得到No，并且可以直接赋值给变量
val res = if (score > 60) "Yes" else "No"
```

可以多行：

```kt
val score = 2
val res = if (score > 60) {
    println("Great!")
    "Yes" // 代码块的最后一行作为返回值
} else {
    println("What a pity!")
    "No"
}
```

#### when

相当于 Java 的高级 `switch`。

```kt
val c = 'A'
val str = when(c) {
    'A' -> "尖子班"
    'B' -> "平行班"
    'C' -> {
        println("去职高深造")
        "慢班" // 代码块的最后一行作为返回值
    }
    else -> "不知道是什么班" 
    // when 必须是完备的
    // 如果分支没有覆盖了所有可能的取值，必须得有 else，即使 str 被声明为 String?
    // 除非为Boolean、枚举类 (enum) 或密封类 (sealed class)，且已经列出了所有定义的成员
}
```

```kt
val c: Int = 5
when(c) {
    1,3 -> println("Hello")
    2,4,6 -> println("World")
    2,5,7 -> println("123") 
    // 这里的 2 与第二个分支中的 2 重复，会被第二个分支提前拦截掉，永远无法触及
}
```

也可以用表达式

```kt
val c = 8
when(c) {
    in 1..5 -> println("123")
    in 6..10 -> println("666")
    else -> ... 
}
```

### 循环

可以遍历的有数组、区间、任何重载 `iterator` 的类。

区间：`val range: Range = 1..5`

```kt
for(i in 1..3) { //这里直接写入 1..3 表示 [1,3] 这个区间
    println("大烟杆嘴里塞，我只抽第五代：$i")
}
```

output:

```txt
大烟杆嘴里塞，我只抽第五代：1
大烟杆嘴里塞，我只抽第五代：2
大烟杆嘴里塞，我只抽第五代：3

```

## 函数_中级

### 函数

函数是**一等公民**。

`fun test()` = `fun test(): Unit`，`Unit` 相当于 Java 中的 `void`，可以省略。

默认值：

```kt
fun main() {
    test() // = test(6, 10)
    test(b = 20) // = test(6, 20)，可以单独指定实参
}

fun test(a: Int = 6, b: Int = 10): Int {
    return a + b
}
```

可以写的更简便：

```kt
fun sum(a: Int, b: Int): Int = a + b
```

形参默认为常量，不可修改。

```kt
fun test(a: Int, b: Int): Int {
    a = 10; // 无法通过编译
    return a + b
}
```

函数内部也可以定义函数：

```kt
fun test() {

    var a = 10

    fun inner() { 
        a += 10 //  内部函数可以访问外部函数中的变量
        ...
    }

    inner() // 作用域受限，只能在函数内部使用
}
```

函数重载：

同名函数的**返回值必须一致**。

```kt
fun main() {
    println(sum(1)) // 调用函数 1
    println(sum(1, 2)) // 调用函数 2
}

fun sum(a: Int): Int { // 1
    return a
}

fun sum(a: Int, b: Int): Int { // 2
    return a + b
}
```

### 变量

全局变量：

```kt
var a: Int = 10 // 可被所有函数访问

fun main() {
    test()
    println(a) 
}

fun test() {
    a += 10
}
```

```kt
var a: Int = 10
var <PropertyName>[: <PropertyType>] [= <PropertyInitializer>]
    [<getter>]
    [<setter>]
```

自定义访问器：

- `getter`: 获取变量的值
- `setter`: 修改变量的值

相当于：

```java
String name = "Hello";

String getName() { // 编译时自动生成
    return this.name;
}

void setName(String name) { // 编译时自动生成
    this.name = name;
}
```

```kt
var a: Int = 5
    get() = field * 10 // field 相当于变量本身
    set(value) {
        println("我被赋值了")
        field = value
        // 不可以写 a = value，会导致递归调用 set()
    }


fun main() {
    println(a) // 输出 50
    a = 10 // 赋值并输出 "我被赋值了"
}
```

### 实用函数

输出：

```kt
println("123")
```

输入：

```kt
val str = readln() // str 必须为 String，Int 通不过编译
println("输入的结果是$str")
```

数学函数：

```kt
import kotlin.math.*

fun main() {
    println(2.0.pow(3)) // 乘方：2^{3}
    println(abs(-1)) // 绝对值：|-1|
    println(max(1, 3)) // 最大值
    println(min(1, 3)) // 最小值
    
    //三角函数 (弧度制)
    println(sin(PI))
    ...
}

```

### 函数类型变量

函数也可以被变量存储。

```kt
var func0: (String) -> Int // 接受一个 String 形参，返回 Int
var func1: (Double, Double) -> Unit // 接受两个 Double 形参，无返回值 
```

还可以套娃：

```kt
var func: (String) -> ((String) -> Double)
```

可以作为函数参数 (高阶函数)：

```kt
fun test(other: (Int) -> String) { // 接受函数作为参数的称为“高阶函数”
    println(other(1))
}
```

PS: 起别名

```kt
typealias Test = (Int) -> String
var func: Test // = var func: (Int) -> String
```

可以直接引用：

```kt
fun main() {
    var func: (String) -> Int = ::test
    println(func(""))
}

fun test(str: String): Int {
    return 666
}
```

可以直接写：

```kt
fun main() {
    var func(): (String) -> Int = fun(str: String): Int {
        return 10
    }
    println(func("")) // 输出 10
}
```

### Lambda 表达式

```kt
fun main() {
    var func(): (String) -> Int = { // 函数名都不用写了
        println(it)
        10 // 最后一行作返回值
    }
    println(func("Hello world"))
}
```

输出：

```text
Hello world
10

```

如果有多个参数：

```kt
fun main() {
    var func(): (String, String) -> Int = { a, b 
    // 超过一个参数时不可以用 it，应指定 a, b 作为两个参数
        println(a)
        println(b)
        10 // 最后一行作返回值
    }
    func("A", "B")
}
```

```text
A
B

```

对于高阶函数，可以这样写：

```kt
fun main(){
    test {
        println(it)
        20
    }
}

fun test(func: (String) -> Int) {
    println(func("Hello, world"))
}
```

输出：

```text
Hello world
20

```

如果是普通参数 + Lambda 参数：

```kt
fun main(){
    test(10, 20) {
        ...
    }
}

fun test(a: Int, b: Int, func: (String) -> Int) {
    println(func("Hello, world"))
}
```

如果需要局部返回，需要用到标签：

```kt
fun main() {
    val func: (Int) -> String = test@{ // 打一个标签
        if(it > 10) return@test "我是提前返回的结果"
        println("我是正常情况")
        "收到的参数为$it"
    }
    test(func)
}

fun test(func: (Int) -> String) {
    println(func(66))
}
```

如果函数调用的尾随 Lambda 表达式，那么标签名字就是函数名字。

```kt
fun main() {
    testName {
        if(it > 10) return@testName "我是提前返回的结果"
        println("我是正常情况")
        "收到的参数为$it"
    }
}

fun test(func: (Int) -> String) {
    println(func(66))
}
```

### 内联函数

`inline fun`

```kt
fun main() {
    test()
}

inline fun test() {
    println("这是一个内联函数")
    println("这是一个内联函数")
    println("这是一个内联函数")
}
```

相当于：

```java
public final class MainKt {
    public static final void main(）{
        int $i$f$test =0;
        // 直接替换了 test() 中的内容
        System.out.println("这是一个内联团数"); 
        System.out.println("这是一个内联函数");
        System.out.println("这是一个内联函数");
    }
    public static final void test（）{
        int $i$f$test =0;
        System.out.println("这是一个内联团数");
        System.out.println("这是一个内联函数");
        System.out.println("这是一个内联函数");
    }    
}
```

不内联：

```java
public final class MainKt {
    public static final void main(）{
        test();
    }
    public static final void test（）{
        System.out.println("这是一个内联团数");
        System.out.println("这是一个内联函数");
        System.out.println("这是一个内联函数");
    }    
}

```

高阶函数也可以内联：

```kt
fun main() {
    test { println("打印$it") }
}

inline fun test(func: (String) -> Unit) {
    println("这是一个内联函数")
    func("Hello World")
}
```

相当于：

```kt
fun main() {
    println("这是一个内联函数")
    val it = "Hello World"
    println("打印$it")
}
```

注意：内联函数中的函数形参，无法作为值给到变量，**只能调用**。

>当你使用了 `inline`：
>
> - 编译器会把 `func("Hello World")` 直接替换成 `println("打印Hello World")`。
> - **它不存在一个实际的函数对象实例**。
> - 如果你试图执行 `val a = func`，编译器就为难了：`a` 应该指向什么呢？因为在生成的字节码里，`func` 已经“消失”并融合进主代码逻辑中了。

```kt
fun main() {
    test { println("打印$it") }
}

inline fun test(func: (String) -> Unit) {
    val a = func // 无法通过编译
    println("这是一个内联函数")
    func("Hello World")
}
```

除非让参数不内联，`noinline`。

```kt
inline fun test(noinline func: (String) -> Unit) {
    val a = func
    println("这是一个内联函数")
    func("Hello World")
}
```

内联以后，`return` 时不用单独打标签，因为代码已嵌入 `main()` 中 (非局部返回)：

```kt
fun main() {
    test { return }
    // 会导致 main() 结束，不会输出任何内容
    // 相当于 return@main
    println("调用上面的方法后")
}

inline fun test(func: (String) -> Unit) {
    func("Hello World")
    println("调用内联函数后")
}
```

## 类与对象

### 类

`class` 关键字声明。

```kt
class Student { // 类通常首字母大写
    ...
}
```

构造函数：

```kt
class Student constructor(name: String, age: Int) { 
    // 主构造函数的名字和年龄
    // 若构造函数没有修饰符可以省略 constructor
    ...
}
```

可以添加 `var` 和 `val` 使其成为类所具有的属性。

```kt
class Student(val name: String, var age: Int = 10) {
    //也可以给初始值
    ...
}
```

属性必须需要有初始值：

```kt
class Student {
    var name: String = ""
    val age: Int // 编译不通过
}
```

可以这样赋值：

```kt
class Student(name: String, age: Int) {
    // 好处是可以自定义 get() 和 set()
    var name = name
    val age = age
}
```

如果一开始不一定需要用，但是用之前一定可以初始化，可以用 `lateinit` 修饰。

`lateinit` 不可以用于 `val` 变量，与设计理念相悖。

```kt
class Student {
    lateinit var name: String
    lateinit var age: Int // 这一行编译不通过，基本类型不能用 lateinit
}
```

### 对象

用 `.` 访问属性。

```kt
fun main() {
    var stu = Student("小明", 18)
    var stu2 = Student("小红", 17)
    println(stu.name)
    println(stu.age)
    println(stu2.name)
    println(stu2.age)
}

class Student(var name: String) { ... }
```

`=` 相当于引用了这个对象。

```kt
fun main() {
    var stu = Student("小明", 18)
    var stu2 = stu
    println(stu === stu2) // === 判断是否为同一对象，输出 true
}

class Student(var name: String) { ... }
```

此时 `stu` 和 `stu2` 均指向 `Student("小明", 18)` 这个对象。

```kt
fun main() {
    var stu = Student("小明", 18)
    var stu2 = Student("小明", 18) // 重复调用构造函数，创建新对象
    println(stu === stu2) // 同名同姓不同人，输出 false
}

class Student(var name: String) { ... }
```

除了直接使用主构造函数，还可以定义多个次要构造函数。

```kt
class Student(var name: String, val age: Int) {
    // 必须指定一个主构造函数
    constructor(var name: String): this(name, 18)
    constructor(): this("没有名字") // 可以调用主构造函数或者先前的次构造函数
}
```

如果没有主构造函数，那么不需要委托主构造函数。

```kt
class Student {
    constructor(name: String) { // 这里只是一个形参，不能转换为属性
        // 不建议这样写
        println("我的名字是$name")
    }
}
```

### 对象初始化

`init` 进行初始化工作。

`init` 作为**主构造函数的补充**。

```kt
class Person {
    init {
        println("我出生了!")
    }
}

fun main() {
    val student = Person()
    println("Hello World")
}
```

输出：

```text
我出生了!
Hello World

```

若有多个 `init`，会按顺序执行：

```kt
class Person {
    init {
        println("我出生了!")
    }

    init {
        println("我真的要出生了!")
    }

    init {
        println("我出生")
    }
}

fun main() {
    val student = Person()
    println("Hello World")
}
```

输出：

```text
我出生了!
我真的要出生了!
我出生
Hello World

```

使用的成员函数必须已经在前面定义了，受顺序影响：

```kt
class Student {
    init {
        println("我是$name") // 编译错误，找不到 name
    }
    val name: String = ""
}
```

先执行**初始化代码块**，再执行**构造函数**：

```kt
class Student(var name: String, var age: Int) {
    init {
        println("我是初始化代码块")
    }

    constructor(name: String): this(name, 18) {
        println("我是次要构造函数")
    }
}

fun main() {
    val student = Person("小明")
}

```

输出：

```text
我是初始化代码块
我是次要构造函数
```

### 成员函数

对象也需要有行为。

```kt
fun main() {
    val ming = Student("小明", 18)
    val hong = Student("小红", 17)
    ming.sayHello() // 输出 大家好啊，我是小明，今年18岁了!
    hong.sayHello() // 输出 大家好啊，我是小红，今年17岁了!
}

class Student(var name: String, var age: Int) {
    fun sayHello() {
        println("大家好啊，我是$name，今年$age岁了!")
    }
}
```

如果形参表里面有与属性名相同的形参，方法内会默认引用形参变量。

```kt
fun main() {
    val ming = Student("小明", 18)
    ming.sayHello("小强") // 输出 大家好啊，我是小强，今年18岁了!
}

class Student(var name: String, var age: Int) {
    fun sayHello(name: String) {
        println("大家好啊，我是$name，今年$age岁了!") // 引用的形参的 name
    }
}
```

若避免歧义，明确指定类中成员，需要用 `this` 指代：

```kt
fun main() {
    val ming = Student("小明", 18)
    ming.sayHello("小强") // 输出 大家好啊，我是小明，今年18岁了!
}

class Student(var name: String, var age: Int) {
    fun sayHello(name: String) {
        println("大家好啊，我是${this.name}，今年$age岁了!")
    }
}
```

### 重载函数

```kt
class Student(var name: String, var age: Int) {
    // 二元运算符必须携带一个形参
    // a.plus(b) 相当于 a + b
    // 类型也可以不是 Student，可以多次重载以支持不同类型
    operator fun plus(another: Student): Student {
        // 假设学生相加相当于名称和年龄分别相加
        return Student(this.name + another.name, this.age + another.age)
    }
}

fun main() {
    val stu1 = Student("小米", 18)
    val stu2 = Student("华为", 17)
    val stu3 = stu1 + stu2 // Student("小米华为", 35)
}
```

补充：

- `a++` -> `a.inc()`

- `a--` -> `a.dec()`

其中 `inc`和 `dec` 函数比较特殊，它们必须返回一个值，该值将分配给使用 `++` 或 `--` 操作的变量，而不是改变执行 `inc` 或 `dec` 操作的对象，意思就是执行后应该得到一个新生成的对象，然后变量的值直接引用到这个新的对象，因为 `Int` 类型就是这样的。

比如 `a++` 的操作步骤如下：

- 将 `a` 的初始值存储到临时存储 `a0` 。
- 将 `a0.inc()` 的结果分配给a。
- 返回 `a0` 作为表达式的结果。
  
同样的， `++a` 的操作步骤如下:

- 将 `a.inc()` 的结果分配给 `a`。
- 作为表达式的结果返回 `a` 的新值。

```kt
class Student(var name: String, var age: Int) {
    operator fun inc(): Student { // 自增只能是同类型
        return Student("小红", age++) // 新对象中为原值，自增后的对象被丢弃了
    }
}

fun main() {
    var stu = Student("小明", 19)
    stu++
    println(stu.name)
    println(stu.age)
}
```

输出：

```text
小红
19
```

- `a in b` -> `b.contains(a)`

`plus` 与 `plusAssign` 尽量避免同时定义，避免发生歧义。

编译器可以把 `+` 转为 `+=`。

比较符号 `compareTo` 必须返回 `Int`，因为包含 `>`、`<`、`>=`、`<=`。与 0 比较。

```kt
class Student(var name: String, var age: Int) {
    operator fun compareTo(another: Student): int {
        /*
        a > b ---> a.compareTo(b) > 0
        a < b ---> a.compareTo(b) < 0
        a >= b ---> a.compareTo(b) >= 0
        a <= b ---> a.compareTo(b) <= 0
        */
        return this.age - another.age
    }
}
```

### 中缀函数

`infix`

```kt
println(i shl 1) // 这里的 shl 就是一个中缀函数，相当于 i*2
```

- 必须是成员函数。
- 只能有一个参数。
- 参数不能有默认值

```kt
class Student(var name: String, var age: Int) {
    infix fun test(string: String): String {
        return name + string
    }
}

fun main() {
    val student = Student("小明", 18)
    println(student test "我爱你")
}
```

中缀函数优先级**低于**算术运算符！！！
中缀函数优先级**高于**布尔运算符 (比较与命名检查)。

```kt
1 shl 2 + 3 // 相当于 1 << (2 + 3) = 2^{5} = 32
0 until n * 2 //  相当于 [0, n * 2)
a xor b in c // 相当于 (a xor b) in c
```

建议手动加上括号以增强可读性： `1 shl (2 + 3)`

## 空值与空类型

可空：尾部加 `?`。

```kt
fun main() {
    val str: String? = null
    println(str.length) // 编译失败，空不安全
}
```

可以智能转换：

```kt
fun main() {
    val str: String? = null
    if(str != null) {
        println(str.length) // 只能转换为 String
    }
}
```

如果非常明确非空，想要强制用：结尾加 `!!`。

```kt
fun main() {
    val str: String!! = null
    println(str.length) // 运行时抛出 NPE
}
```

尽量少用避免用。

如果想要安全访问，用 `?.`。在某一步为空时不会报错，而是直接返回 `null`。

```kt
fun main() {
    val str: String? = null
    println(str?.length) // 输出 null
}
```

## 解构声明

```kt
fun main() {
    val student = Student("小明", 18)
    val (a, b) = student // 从 Student 对象中将其属性解构出来
    println("名字：$a，年龄：$b")
}
```

需要解构函数：

```kt
class Student(var name: String, var age: Int) {
    // 解构函数以 component 开头
    operator fun component1() = name
    operator fun component2() = age
    ...
}
```

如果不需要某个参数，可以用 `_` 替代了。(约定俗成)

解构函数可以用在 Lambda 里：

```kt
fun main() {
    v al student = Student("小明", 18)
    val func: (Student) -> Unit = { (a, b) ->
        println(a)
        println(b)
    }
    func(student)
}
```

## 包与访问权限

### 包和导入

原理和 Java 基本一致。

Kotlin 默认会导入一些一些包，无需手动导入。  

如果

```kt

// com.test.Test.kt
package com.test

var a = 20

fun message() {
    println("我是测试方法")
}

// Main1.kt
import com.test.message

fun main() {
    message() 
    // 调用明确导入的方法，输出 我是测试方法
}

fun message() {
    println("我是自带的")
}

// Main2.kt
import com.test.*

fun main() {
    message() 
    // 没有明确导入，输出 我是自带的
}

fun message() {
    println("我是自带的")
}
```

为避免歧义，可以使用 `as` 关键字换名：

```kt
// Main1.kt
import com.test.message as test

fun main() {
    test() 
    // 调用 com.test.message
    // 输出 我是测试方法
}

fun message() {
    println("我是自带的")
}
```

只要是不同的包，就需要导入。

### 访问权限控制

用可见性修饰符来控制可见性。

`private`、`protected`、`internal`、`public`

默认 `public`。

“模块”指编译在一起的一组 Kotlin 文件（通常为一个项目）。

| 修饰符          | 在当前类中 | 在子类中 | 在同模块其他地方 | 在模块外部 |
| --------------- | ---------- | -------- | ---------------- | ---------- |
| **`public`**    | ✅          | ✅        | ✅                | ✅          |
| **`internal`**  | ✅          | ✅        | ✅                | ❌          |
| **`protected`** | ✅          | ✅        | ❌                | ❌          |
| **`private`**   | ✅          | ❌        | ❌                | ❌          |

顶级声明不支持使用 `protected` 修饰符。

## 面向对象

### 类的封装

重在思想。

不封装的话类中实例可以被随意查看修改，不安全。我的事情应该我自己做，不应该由外部来实现。

比如一个 age 属性，你不能让用户设置为 -5 岁。

```kt
fun main() {
    var student = Student("小明", 18)
    student.name = 小红
}

class Student(private var _name: String, private var _age: Int) {
    var age: Int = _age
        set(value) {
            require(value >= 0) { "年龄不能为负数: $value" }
            field = value
        }

    init {
        // 在对象初始化时强制校验
        require(_age >= 0) { "初始化失败：年龄不能为负数" }
        println("学生 $_name 已创建")
    }
}
```

甚至可以把主构造函数设置成私有的：

```kt
// 若设置为 private，constructor 不可省
class Student private constructor(private var name: String, pricate var age: Int) {
    // 此时只能用其他的构造函数来构造
    constructor(): this("", 18)
}

fun main() {
    val stu1 = Student("小明", 18) // 编译错误，越权
    val stu2 = Student() // 相当于 Student("", 18)
}
```

### 类的继承

Kotlin 中类默认为终态，无法被继承。

可以打 `open` 关键字使其可以被继承。

```kt
open class Student() {

}

// : 表继承，以调用主构造函数的方式声明
open class ArtStudent: Student() {
    
}

// 继承可以嵌套
class SuperArtStudent: ArtStudent() {

}
```

一个类继承另一个类是，属性会被继承，可以直接访问父类中定义的属性，除非父类中将属性的访问权限修改为 `private`，那么子类将无法访问（但是依然是继承了的）：

```kt
open class Student() {
    var name: String = "小明"
    
}

// : 表继承，以调用主构造函数的方式声明
open class ArtStudent: Student() {
    fun test() {
        println(name) // 可以正常访问
    }
}

// 继承可以嵌套，儿子的儿子
class SuperArtStudent: ArtStudent() {

}
```

`super` 可以调用父类的方法，`super()` 调用父类构造函数：

```kt
open class Student() {
    init { println("父类初始化") }
    fun hello() = println("大家好")
}

// 或是类中指定 constructor
open class ArtStudent: Student {
    constructor(name: String): super(name, 18) // 父类构造函数
    fun draw() = println("我会画画")
}
```

子类初始化时，必须先初始化父类。

### 属性的覆盖

重写前，需要加入 `override`关键字，相当于 Java 中 `@override` 注解。

```kt
open class Student {
    fun hello() = println("大家好")
}

class ArtStudent: Student() {
    override fun hello() = println("大家早上好")
}

class SportStudent: Student() {
    override fun hello() {
        println("大家下午好")
        super.hello() // 也可以调用父类方法
    }
}

fun main() {
    var stu1 = ArtStudent()
    var stu2 = SportStudent()
    stu1.hello() // 输出 大家早上好
    stu2.hello() // 输出 大家早上好\n早上好
}
```

成员变量也可以被覆盖，需要给变量加 `open` 关键字

```kt
open class Student {
    open val name: String = "小明"
}

class ArtStudent: Student() {
    // 覆盖父类变量，类型和名字必须一致
    override val name: String = "小红"
        get() = ...
        set() = ...
}
```

甚至可以在主构造函数中覆盖：

```kt
open class Student {
    open val name: String = "小明"
}

class ArtStudent(override val name: String): Student() {
    
}
```

父类的**初始化逻辑运行在子类的字段赋值之前**：

> Kotlin 的非空类型仍然可能导致 NPE。

```kt
fun main() {
    var stu = ArtStudent()
}

open class Student {
    open val name: String = "小明"
    init { println("我的名字是：$name") } // 会报 NPE
}

class ArtStudent: Student() {
    override val name = "大明"
}

```

这段代码的执行顺序：

1. 系统为 `ArtStudent` 对象分配内存，此时所有的属性（包括 `name`）都会被设置为该类型的默认初始值（对于引用类型 `String` 来说，这个初始值就是 `null`）。
2. 调用父类构造函数：
   - 执行 `Student` 的初始化代码。
   - 执行到 `init { println("我的名字是：$name") }`。
3. 因为你重写（`override`）了 `name`，此时父类 `init` 访问的 `name` 实际上是指向子类的实现。
   此时子类的 `name = "大明"` 这行代码**还没有执行**（子类的属性初始化在父类构造函数返回之后才会发生）。
4. 父类尝试读取子类还没初始化的 `name` 属性，读到了底层的 `null`，并试图将其传递给 `println`，从而触发了 NPE。

编译器会**给出警告**。

初始化时的执行顺序：**先父后子，从上到下**。

### 多态

```kt
fun main() {
    var stu: Student = ArtStudent() // 都是学生，但是还是美术生
    stu.hello() // 输出 大家早上坏
}

open class Student {
    open fun hello() = println("大家好")
}

class ArtStudent: Student() {
    override fun hello() = println("大家早上坏")
    fun test() = println("测试")
}
```

如果需要确认是哪个Student，用 `is` 判断是哪个类型：

```kt
fun main() {
    test(ArtStudent()) // true true false
    test(SportStudent()) // true false true
     
}
fun test(stu: Student) {
    println(student is Student) // 一定是一个学生
    println(student is ArtStudent)
    println(student is SportStudent)
}
class ArtStudent: Student() {
    fun draw() = println("我画画")
}

class SportStudent: Student() {
    fun run() = println("我跑步")
}

open class Student {
    fun hello() = println("大家好")
}
```

可以用 `as` 进行类型强转，使其强制变成子类：

这样存在一定的风险。**子类间不可以强转**。

```kt
fun test(stu: Student) {
    val artStu: ArtStudent = stu as ArtStudent
}
```

处理可空类型时，为防止出现异常，可以用 `as?` 运算符。

```kt
fun main() {
    val stu: Student? = ArtStudent()
    val artStu: ArtStudent? = stu as? ArtStu
}
```

### 顶层 Any 类

Kotlin 中所有类都直接或间接继承自 `Any` 类。

```kt
class Student
var stu = Student()
```

相当于：

```kt
class Student: Any
var stu: Any = Student()
```

`Any` 类定义了一些基本方法，按需重写：

```kt
public open operator fun equals(other: Any?): Boolean // ==
public open fun hashCode(): Int
public open fun toString(): String
```

`equals()` 重写模板：

```kt
override fun equals(other: Any?): Boolean {
    // 1. 检查是否为同一个引用（性能优化）
    if (this === other) return true
       
    // 2. 检查 null 和 类型（如果 other 是 null，is 检查会直接返回 false）
    if (other !is User) return false
        
    // 3. 比较属性
    return this.id == other.id && this.name == other.name ...
}
```

### 抽象类

只能被继承使用，不能被实例化。

```kt
abstract class Student {
    abstract val type: String // 初始值可以不要，只是说明有这个属性
    abstract fun hello()
    // 不能设 private，要不重写不了
    var xxx: String = "" // 也可以有不抽象的东西
}

// 抽象属性必须被重写
class ArtStudent: Student(override) {
    override var name: String = "小明"
    override fun test() {
        println("测试")
    }
}
```

抽象类也可以继承抽象类，抽象类也可以继承非抽象类。

### 接口

说明有哪些功能而非某种类型，比抽象类还抽象。

```kt
interface A {
    // 默认 abstract
    val x: String
    fun sleep()
}

interface B {
    fun game()
}

// 类中应该实现 A, B
class Student: A, B {
    override val x: String = "测试"
    override fun sleep() = println("管他什么早八不早八的，睡舒服再说")
    override fun game() = println("读大学就该玩游戏玩到爽")
}
```

可以有默认 `get()`、`set()`，不能有 `field` 字段：

```kt
interface Rectangle {
    val width: Double
    val height: Double

    // 这个属性不需要 field，因为它是由 width 和 height 推导出来的
    val area: Double
        get() = width * height 
}

class BasicRect(override val width: Double, override val height: Double) : Rectangle {
    // 我不需要写 area，它已经自动拥有了 width * height 的能力
}

val rect = BasicRect(10.0, 5.0)
println(rect.area) // 输出 50.0
```

可以有默认操作：

```kt
interface A {
    var x: String
    fun sleep() {
        x = ""
    } 
}
```

如果两个接口有相同实现，需要手动解决：

```kt
interface A {
    fun sleep() = println("他什么早八不早八的，睡舒服再说")
}

interface A {
    fun sleep() = println("爽睡")
}

class Student: A, B {
    // 手动指定
    override fun sleep() = super<B>.sleep()
}
```

实现类可以被视为接口的类型实例，接口支持多态特性，可以用 `is`、`as`：

```kt
fun main() {
    var student = Student()
    var a: A = student
    println(a is B) // 输出 true
}
```

### 类的扩展

`.` 扩展某个类。

```kt
fun main() {
    var stu = Student()
    stu.hello()
    stu.test() // 可以调用，输出 我是测试函数
}

class Student {
    fun hello() = println("Hello world")
}

// 无需继承，给谁扩展就把谁放在 `.` 前面。
fun Student.test = println("我是测试函数")
```

对于一些现成的类特别有用：

```kt
fun main() {
    var str = "A"
    str.test() // 输出 我是测试函数
}

fun String.test() {
    println("Hello world")
}
```

类的扩展是静态的，实际上并不会修改它们原本的类，也不会将新成员插入到类中，仅仅是将我们定义的功能变得可调用，使用起来就像真的有一样。同时，在编译时也会明确具体调用的扩展函数：

```kt
open class Shape
class Rectangle: Shape()

fun Shape.getName() = "Shape"
fun Rectangle.getName() = "Rectangle"

fun printClassName(s: Shape) {
    println(s.getName())
}

fun main() {
    printClassName(Rectanle()) // 输出 Shape
    // 既然参数声明的是 Shape，那就调用绑定在 Shape 类上的那个扩展函数
}
```

编译后的伪代码:

```java
public static void printClassName(Shape s) {
    // 扩展函数其实是静态方法，直接通过类名调用
    System.out.println(ShapeKt.getName(s)); 
}
```

函数的优先级高于扩展函数：

```kt
class Test {
    fun hello() = println("你干嘛")
}

// 编辑器直接标灰
fun Test.hello() = println("哎呦~")

fun main() {
    Test().hello() // 输出 你干嘛
}
```

但是可以重载：

```kt
class Test {
    fun hello() = println("你干嘛")
}

// 编辑器直接标灰
fun Test.hello(str: String) = println(str)

fun main() {
    Test().hello("哎呦~") // 输出 哎呦~
}
```

扩展类受访问控制限制：

```kt
class Test {
    private var name: String = ""
}

fun Test.test() {
    this.name // 编译错误，访问不了
}
```

类中定义的扩展函数仅限于类中使用：

```kt
class A {
    fun hello() = "Hello world"
}

class B(private val a: A) {
    private fun A.test() = hello() + "!!!"
    fun world() = println(a.test()) //只能在类中通过A的实例使用扩展函数
}

fun main() = B(A()).world() // 创建一个 B，给它一个 A，然后让 B 去执行它的逻辑
```

扩展函数不支持 `protected` 修饰符。因为扩展函数实际上是在类外部定义的**静态**方法，它并不属于类继承体系的一环。

如果成员同名：

```kt
class A {
    fun foo() = "A 的 foo"
}

class B {
    fun foo() = "B 的 foo"

    fun A.test() {
        println(foo())          // 优先找 A 的，输出 "A 的 foo"
        println(this@B.foo())   // 显式调用 B 的，输出 "B 的 foo"
    }
}
```

如果将一个扩展函数作为参数给到一个函数类型变量，那么同样需要再具体操作之前增加类型名称才可以：

```kt
fun main() {
    val func: String.() -> Int = {
        this.length
    }
    println("Hello".func())
    println(func("Hello")） // 等价的
}
```

DSL：

```kt
fun main() {
    val stuJson = json {
        "name" to "小明"
        "age" to 18
        "grades" {
            "Chinese" to 110
            "Math" to 140 
        }
        /*
            相当于：
            "grades".invoke({
                "Chinese" to 110
            })
        */
    }
}

class JsonObject {
    private val data = mutableMapOf<String, Any>()

    // 这个中缀函数让我们可以写 "key" to value
    infix fun String.to(value: Any) {
        data[this] = value
    }

    // 处理嵌套对象，再次用到 T.() -> Unit
    operator fun String.invoke(init: JsonObject.() -> Unit) {
        val nested = JsonObject()
        nested.init() // 在新对象上执行脚本
        data[this] = nested // 存入当前对象
    }

    override fun toString(): String = 
        data.entries.joinToString(prefix = "{", postfix = "}") { 
            "\"${it.key}\": ${if(it.value is String) "\"${it.value}\"" else it.value}" 
        }
}

// 创建一个 JsonObject 并把它的能力借给大括号
fun json(init: JsonObject.() -> Unit): JsonObject {
    val jsonObject = JsonObject()
    jsonObject.init() // 这里的 init 就是你在 main 里写的大括号
    return jsonObject
}
```

## 泛型

```kt
fun main() {
    val score1 = Score("高等数学", "1234", 90)
    val score2 = Score("计算机网络", "5678", "优秀")
}

class Score(var name: String, var id: String, var value: Any) {
    ...
}
```

`Any` 可以指代任何类型，但是类型推断能力不好，健壮性欠缺：

```kt
fun main() {
    val score = Score("高等数学", "1234", 90)
    val a: Int = score.value as Int // 只能强转
}
```

引入泛型类 `<T>`：

```kt
fun main() {
    val score1 = Score("高等数学", "1234", 90)
    val value1 = score1.value // Int
    val score2 = Score("计算机网络", "5678", "优秀")
    val value2 = score2.value // String
}

// 一般用 T, K, V
class Score<T>(var name: String, var id: String, var value: T) { // 编译时确定

}
```

在创建对象时可以用 `<>` 给定类型：

```kt
val score = Score<Int>("高等数学", "1234", "优秀") // 编译失败，Int != String
val score: Score<String> = ("高等数学", "1234", 90) // 编译失败
```

在类里面泛型变量只能先当 `Any` 用：

```kt
class Score<T>(var name: String, var id: String, var value: T) {
    fun test() {
        var v: Any? = value
        val t = T() // 编译失败，不能调泛型的构造函数
    }
}
```

可以指定不只一个泛型：

```kt
class Test<K, V>(val key: K, val value: V)
```

### 协变与逆变

```kt
class Test<T>(var data: T)

fun main() {
    val test1: Test<Int> = Test(10)
    val test2: Test<Number> = Test(10) // 编译失败
}
```

- 协变：因为 `Int` 是 `Number` 的子类，所以 `TRest>` 同样是 `Test<Number>` 的子类，可以正常转换。
- 逆变：`Test<Number>` 可以直接转换为 `Test<Int>`，前者是后者的子类。
- 抗变：`Test<Int>` 与 `Test<Number>` 没关系，无法转换。

Kotlin 中，泛型默认**抗变**。

- `out` 标记协变，实现子类到父类的转换 (生产者，强行关闭所有写入功能)：

```kt
fun main() {
    val test1: Test<Int> = Test(10)
    val test2: Test<out Number> = Test(10) // Number 协变，可行
    var data: Number = test2.data  // 协变类型在使用时会变成上界
}
```

- `in` 标记逆变，实现父类到子类的转换 (消费者，放弃了读取精确类型的权利)：

```kt
fun main() {
    val test1: Test<Any> = Test(10)
    val test2: Test<in Number> = Test(10) // Number 协变，可行
    var data: Any? = test2.data // 具体用的哪个父类不清楚，只能是 Any? 类型
}
```

```kt
class Test<T>(var data: T)
open class A
class B: A()
class C: A()

fun main() {
    val test1: Test<B> = Test(B()) // 这里存放的都是 B 类型的数据
    val test2: Test<out A> = test1 // 此时test2与test1是同-个对象，但是 test2 是 outA
    test2.data = C() // 由于 C 是 A 的子类，按照正常情况来说可以直接用 (但实际上这句会报错，协变只准读，不准写)
    val data: B = test1.data // 这下搞笑了，拿到的类型应该是 C，结果接收的类型是 B
}
```

```kt
// 假设这里可以这么写（实际上 Test 类定义必须符合 in 的规则）
val test3: Test<in C> = ... 
test3.data = C() // 允许写入 C
// val d: C = test3.data // 报错！你不知道里面取出来的是 C 还是 A 还是 Object
```

### 泛型的界限

若想要把类型限制为数字，需要限制泛型上界：

```kt
// 不指定默认为 Any?
// 此时 T 只能为 Number 的子类或者 Number 本身
class Score<T: Number>(val name: String, val id: String, val value: T)
```

如果需要设定多个上界，比如必须同时是某两个类型的子类或接口实现，需要使用 `where` 关键字：

```kt
class Score<T>(val name: String, val id: String, val value: T)
    where T: A, T: B

open class A
interface B
class C: A(), B
```

可以直接把类型参数指定为 `in` 或 `out`：

```kt
class Score<out T>(val name: String, val id: String, val value: T) {
    fun test(): T
}
```

`*` 投影可以表示不确定 (我不确定这里面具体是什么类型，但我必须保证操作是绝对安全的)：

```kt
classTest<out T: Number> (val data: T) // 因为限制了 out，因此作为生产者，这里只能使用 val
fun main() {
    val test: Test<*> = Test(10) // 虽然使用了 * 表示不确定，但是由于类型参数本身存在上界
    var data: Number = test.data // 所以类型读取后可以直接当做上界类型 Number 使用
```

```kt
class Test<in T> {
    fun set(t: T) { } // 因为限制了 in，只能作为消费者
}

fun main() {
    val test: Test<*> = Test<Int>()
    test.set(10) // 编译错误，set 中参数类型为 Nothing，不允许任何值
}
```

### 类型擦除

编译后类型会自动擦除。

存在上界，则类型变成上界类型。

```kt
class Test<T: Number>(privatevar data: T)
class Test(peivate var data: Number) // 擦除后变成上界类型
```

运行时无泛型，类型判断操作不允许：

```kt
class Test<T: Number>(privatevar data: T) {
    fun isType(obj: Any): Boolean {
        return obj is T // 编译错误
    }
}

```

对于内联函数，`reified` 关键字可以具化参数类型：

```kt
inline fun <reified T> isType(value: Any): Boolean {
    return value is T // 这个时候可以 is 了
}

fun main() {
    println(isType<String>("666"))
}
```

## 作用域函数

- `.apply { }` 返回**对象**本身 (**`this`**)，用于初始化对象，设置属性：

```kt
fun test(stu: Student?): Student? {
    stu?.name = "小明"
    stu?.age = 18
    stu?.hello()
    retutrn stu
}
```

可以改为：

```kt
fun test(stu: Student?): Studnet = stu?.apply {
    this.name = "小明" // this 代表 stu 本身
    this.age = 18
}
```

- `.also { }` 返回**对象**本身 (**`it`**)，用于附加操作（如打印日志、数据验证）。

- `.run { }` 把**结果**作为返回值返回 (**`this`**)，用于对象配置并计算结果、或者只为了运行代码。

- `.let { }` 把**结果**作为返回值返回 (**`it`**)，用于空检查 (`?.let`)、将对象转换/映射为新类型：

```kt
fun main() {
    var a: Any? = ...
    val str: String = a?.let {
        porintln(it)
        "Hello world" // 作为返回值
    }
}
```

- `.with() { }` 将对象作为参数传入，并返回代码块的最后一行结果：

```kt
val stu = Student("小明", 18)
val bio = with(stu) {
    // 直接访问属性，不需要 it
    "Name: $name, Age: $age" 
} // bio: String = "Name: 小明, Age: 18"
```

>- `run`: 在这个对象上执行操作。
>- `with`: 使用这个对象，完成以下任务。

- `.takeIf { }` 如果满足括号内的条件，则返回对象本身；否则返回 null：

```kt
val input = ...
// 只有当字符串长度大于 5 时才处理
val result = input.takeIf { it.length > 5 }?.let {
    "长度合法: $it"
} ?: "输入过短"

println(result) 
```

`.takeUnless { }` 与 `takeIf` 相反，如果不满足条件，则返回对象本身。

## 特殊类型

### 数据类

```kt
data class User(val name: String, val age: Int)
```

自动生成 `equals()`、`copy()`、`componentN()` 等方法。

不能是抽象的、开放的、密封的、内部的。

可以显示实现自动生成的函数：

```kt
data class User(val name: String, val age: Int) {
    override fun toString(): String = "我是自动以的toString"
}
```

如果父类可继承，那么子类会覆盖超类型的函数：

```kt
abstract class AbstractUser {
    open operator fun component1() = "卢本伟牛逼"
}

data class User(val name: String, val age: Int): AbstractUser() // 默认实现

```

不能显式实现 `componentN()` 和 `copy()`。

如果不想将某个属性生成函数，可以移出构造函数：

```kt
data class Student(var name: String) {
    var age: Int // 此时 .equals() 只会比较 name
}
```

### 枚举类

```kt
enum class LightState {
    RED, YELLOW, GREEN // 命名规范全大写
}
```

`.name` 返回名字，`.ordinal` 返回下标(0开始)：

```kt
fun main() {
    val state = LightState.RED
    println(state.name) // 输出 RED
    println(state.ordinal) // 输出 0
}
```

枚举类也可以有成员：

```kt
enum class LightState(var color: String) {
    RED("红灯"), YELLOW("黄灯"), GREEN("绿灯")
}
```

可以有函数：

```kt
enum class LightState(var color: String) {
    RED("红灯"), YELLOW("黄灯"), GREEN("绿灯"); // 需要带上分号

    fun isGreen = this == LightState.GREEN
}
```

枚举类封闭，使用 `when` 时无需写 `default`。

枚举类也可以写抽象函数：

```kt
enum class LightState(var color: String) {
    // 行为跟着数据走
    RED("红灯") {
        override fun test() = println("禁止通行")
    }, YELLOW("黄灯") {
        override fun test() = println("请减速")
    }, GREEN("绿灯") {
        override fun test() = println("可以通过")
    }; 

    abstract fun test()
}

fun main() {
    val state: LightState = LightState.RED
    state.test()
}
```

枚举类也可以实现接口：

```kt
interface Message {
    test()
}

enum class LightState(var color: String): Message {
    ...
```

通过名字可以获取枚举元素：

```kt
var light = LightState.valueOf("RED")
var light = enumValueOf("RED")
```

获取所有所有枚举元素：

```kt
val emtries: EnumEntries<LightState> = LightState.entries
val values: Array<LightState> = enumValues<LightState>()
```

## 匿名类

没有名字的类。

```kt
fun main() {
    var obj = object {
        fun test() { }
        var age: Int = 10
    }
}
```

不能定义任何构造函数。

可以继承类或者实现接口：

```kt
interface Person {
    fun chat()
}

open class Human(val name: String)

fun main() {
    var obj = object: Human("小明"), Person {
        override fun chat() = println("牛逼")
    }
    obj.chat()
}
```

对于只存在一个抽象函数的接口成为函数式接口或者单一抽象方法接口，可以有 N 个非抽象成员和一个抽象成员：

```kt
fun interface KRunnable {
    fun invoke()
}

fun main() {
    val runnable = KRunnable {
        println("我是实现")
    }
    runnable.invoke()
}
```

匿名对象是从一个公有函数或非局部作用域返回的，它的类型会被自动视为它的父类：

```kt
open class Human(val name: String)

fun test() = object: Human("小明") {
    val age: Int = 10
    override fun toString() = "我叫$name"
}

fun main() {
    println(test().name)
    println(test().age) // 编译错误
}
```

`object` 还可用于声明单例类 (只能存在一个对象)：

```kt
object Singleton {
    private var name = "你干嘛"
    override fun toString = "我叫$name"
}

fun main() {
    val singleton = Singleton() // 编译错误，不能用构造函数创建
    val singleton = Singleton // 可以通过名字直接拿到单例对象
    println(singleton) // 输出 我叫你干嘛
}
```

若希望一个类型既支持单例类那样直接调用，又支持普通类的用法，可以用伴生对象 (单例类写在某个类内部)：

```kt
class Student(val name: String, val age: Int) {
    companion object Tools {
        fun create(name: String, age: Int) = Student(name, age)
    }
}

fun main() {
    val stu1 = Student.create("小明", 18)
    val stu2 = Student("小红", 18)
}
```
