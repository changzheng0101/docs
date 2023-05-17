 #  kotlin基础语法

## 注意

* 不支持三元运算符，倾向于用条件表达式。

* 1..3 包括1和3

* 复制对象的时候，普通的`=`只复制了引用，但是具体的行为分为'**immutable**'(不可变的)和'**mutable**'(可变的)

  * 不可变的会创建一个新的对象，所以数字什么的赋值，不用太担心了~

  * 可变的二者指向同一个对象

  * 待测试：

    * ```kotlin
      val lineOne = "Happy New Year,"
      val lineTwo = lineOne
      val lineThree = lineOne
      ```

* 自增和自减

  * ```kotlin
    var a = 10
    val b = a++
    println(a)  // a = 11
    println(b)  // b = 10
    ```

  * ```kotlin
    var a = 10
    val b = ++a
    println(a)  // a = 11
    println(b)  // b = 11
    ```

* == 和 ===

  * ==    检验状态是否相等

  * === 检测是否指向同一个对象

  * ````kotlin
    var two = 2
    var anotherTwo = 2
    println(two === anotherTwo) // true
    //二者指向相同的对象，这个对象是不可变的
    //当改变其中的一个值的时候，是将这个指向了新的对象
    ````

  * 

## 基础

### Unicode

* a standard for encoding different kinds of symbols.

* 一共有17个**planes**
  * U+0000-U+FFFF  第一个
  * U+1000-U+1FFFF 第二个
  * .....
  * 每个都有2^16个字符
* **from `U+0000` to `U+FFFF`----Basic Multilingual Plane** (**BMP**)
* **Supplementary Multilingual Plane** (**SMP**) (codes from `U+10000` to `U+1FFFF`)，包括表情等。
* UTF-8 支持所有Unicode，UTF-8 才是字符如何映射成计算机内中的字节的实现，Unicode仅仅是一个标准

### regex

* ？ 让前面的符号有0/1个

* 点符号--The only character it cannot match is the newline character `\n`

* ```kotlin
  val string = "cat" // creating the "cat" string
  val regex = string.toRegex() // creating the "cat" regex
  
  val regex = Regex("cat") // creating a "cat" regex
  ```

* ```kotlin
  val regex = Regex("cat") // creating the "cat" regex
      
  val stringCat = "cat"
  val stringDog = "dog"
  val stringCats = "cats"
  //返回是否匹配
  println(stringCat.matches(regex))   // true
  println(stringDog.matches(regex))   // false
  println(stringCats.matches(regex))  // false
  ```

* ```kotlin
  //转义 \\
  val regex = Regex("cats\\?") 
  
  val stringCat = "cat"
  val stringManyCats = "cats"
  val stringEmotionalCats = "cats?"
  
  println(stringCat.matches(regex))   // false
  println(stringManyCats.matches(regex))   // false
  println(stringEmotionalCats.matches(regex))  // true
  ```

* 

### 循环

```kotlin
for (i in 1..6) { ... }        // closed range: 1, 2, 3, 4, 5, 6
for (i in 1 until 6) { ... }   // half-open range: 1, 2, 3, 4, 5
for (x in 1..6 step 2) { ... } // step 2: 1, 3, 5
for (x in 6 downTo 1) { ... }  // closed range, backward order: 6, 5, 4, 3, 2, 1 

 for (i in 11..10) {
        print(i)
    }  //什么都不输出
```

```kotlin
for (i in "aa".."ad") {}//报错
//"aa".."ad" 其实有无穷个数据
```

```kotlin
//普通跳出循环 只能跳出一个循环 这个可以跳出多层
loop@ for (i in 1..3) { 
    for (j in 1..3) {
        println("i = $i, j = $j")   
        if (j == 3) break@loop  
    }  
}  

i = 1, j = 1
i = 1, j = 2
i = 1, j = 3
```



## 类型

### Nothing

Nothing 代表可能永远没有返回值

*   ```kotlin
  fun fail(message: String): Nothing {
      throw IllegalArgumentException(message)
  }
  ```

* 在扔出异常的时候用的多

### String

```kotlin
print("Hello".repeat(4)) // HelloHelloHelloHello
```

```kotlin
val errorString = 10 + "abc" // an error here! 不能用数字开头哦~
```

```kotlin
val largeString = """
    This is the house that Jack built.
      
    This is the malt that lay in the house that Jack built.
""".trimIndent() // removes the first and the last lines and trim indents
```

```kotlin
val str='hello' //error must use ""
//'' is character 
```

subString左闭右开

replace 不会改变原来的字符串

```kotlin
val sentence = "a long text"
val wordsList: List<String> = sentence.split(" ") // ["a", "long", "text"]
//List无法进行赋值和改变大小！！！

val mutableWordList = sentence.split(" ").toMutableList() // MutableList ["a", "long", "text"]
```

```kotlin
val scientistName = "Isaac Newton"

for (i in 0 until scientistName.length) {
    print("${scientistName[i]} ") // print the current character
}
//I s a a c   N e w t o n 
```



### Char

* 用单引号声明

* ```kotlin
  val ch = 'a'
  println(ch.toInt())   // 97
  val num = 97
  println(num.toChar()) // a
  ```

* ```kotlin
  val ch1 = 'b'
  val ch2 = ch1 + 1 // 'c'
  val ch3 = 1 + ch1 // Error
  val charsSum = 'a' + 'b' // Error
  ```


### number

![image-20211130195121453](kotlin.assets/image-20211130195121453.png)

从左到右 -- 默认转化顺序

### Collections

##### MutableList

* 存储不定长度的数据,add添加数据

* 存储相同类型

* 注意是mutableListOf还是MutableList

* ```kotlin
  val numbers = mutableListOf(10.8, 14.3, 13.5, 12.1, 9.7) 
  
  println(numbers) // [10.8, 14.3, 13.5, 12.1, 9.7]
  //declaring a mutable list of integers
  val mutableListA = mutableListOf<Int>(1, 2, 3, 4, 3)
  println(mutableListA)
    
  //declaring a mutable list of strings
  val mutableListB = mutableListOf<String>("Kotlin", "JetBrains")
  println(mutableListB)
    
  //declaring an empty mutable list of booleans
  val mutableListC = mutableListOf<Boolean>()
  println("Empty list $mutableListC")
  ```

* ```kotlin
  val numbers = MutableList(5) { readLine()!!.toInt() } // on each line single numbers from 1 to 5
  println(numbers) // [1, 2, 3, 4, 5]
  ```

* ```kotlin
  val list = MutableList(5) {0}
  
  println(list) // [0, 0, 0, 0, 0]
  ```

* 不支持负的索引值

* 

## 常用关键字

* val

  * 指向某个对象之后不可以改变
  * 并非指向的是不可变对象

* var

* when 类似于switch case

  * ```kotlin
    val answerString = when {
        count == 42 -> "I have the answer."
        count > 35 -> "The answer is close."
        else -> "The answer eludes me."
    }
    
    println(answerString)
    ```

  * 满足前面的条件会跳到后面

* lateinit关键字

  ```kotlin
  class LoginFragment : Fragment() {
  
      private lateinit var usernameEditText: EditText
      private lateinit var passwordEditText: EditText
      private lateinit var loginButton: Button
      private lateinit var statusTextView: TextView
  
      override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
          super.onViewCreated(view, savedInstanceState)
  
          usernameEditText = view.findViewById(R.id.username_edit_text)
          passwordEditText = view.findViewById(R.id.password_edit_text)
          loginButton = view.findViewById(R.id.login_button)
          statusTextView = view.findViewById(R.id.status_text_view)
      }
  }
  ```

* !! 将某个变量视为非null

* **GRASP** 

* Don't Repeat Yourself (**DRY**)

* You Ain't Gonna Need It (**YAGNI**)

* Keep It Simple, Stupid (**KISS**)

* sealed 可以在when中不使用else 如果使用了这个关键字

  * ```kotlin
    fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> expr.value
            is Sum -> eval(expr.left) + eval(expr.right)
        }
    
    sealed interface Expr
    class Num(val value: Int) : Expr
    class Sum(val left: Expr, val right: Expr) : Expr
    
    fun main() {
        println(eval(Num(8)))
        println(eval(Sum(Num(8),Num(8))))
    
    }
    ```

* object 用于声明匿名类

  * 不同的返回类型和访问性

    ```kotlin
    interface A {
        fun funFromA() {}
    }
    interface B
    
    class C {
        // The return type is Any. x is not accessible
        fun getObject() = object {
            val x: String = "x"
        }
    
        // The return type is A; x is not accessible
        fun getObjectA() = object: A {
            override fun funFromA() {}
            val x: String = "x"
        }
    
        // The return type is B; funFromA() and x are not accessible
        fun getObjectB(): B = object: A, B { // explicit return type is required
            override fun funFromA() {}
            val x: String = "x"
        }
    }
    ```

  * 声明单例类很好用

    * ```kotlin
      object PlayingField {
      
          fun getAllPlayers(): Array<Player> {
              /* ... */
          }
          
          fun isPlayerInGame(player: Player): Boolean {
              /* ... */
          }
      
      }
      ```

  * 静态变量

    * ```kotlin
      class Player(val id: Int) {
          object Properties {
              /* Default player speed in playing field – 7 cells per turn */
              val defaultSpeed = 7
      
              fun calcMovePenalty(cell: Int): Int {
                  /* calc move speed penalty */
              }
          }
      
          /* creates a new instance of Player */
          object Factory {
              fun create(playerId: Int): Player {
                  return Player(playerId)
              }
          }
      }
      
      /* prints 7 */
      println(Player.Properties.defaultSpeed)
      
      
      /* prints 13 */
      println(Player.Factory.create(13).id)
      ```

  * ```kotlin
    //全局数据放在这里，类似于java中的接口
    object Game {
        object Properties {
            val maxPlayersCount = 13
            val maxGameDurationInSec = 2400
        }
    
        object Info {
            val name = "My super game"
        }
    }
    ```

  * 

* repeat

  * ```kotlin
    repeat(times){
    	functionBody()
    }
    ```

* in

  * ```kotlin
    println('b' in 'a'..'c') // true
    println('k' in 'a'..'e') // false
    
    println("hello" in "he".."hi") // true
    println("abc" in "aab".."aac") // false
    ```

  * ```kotlin
    val range = 100..200
    println(300 in range) // false
    //语法正确 但是结果为false
    ```

* when

  * ```kotlin
    when (op) {
        "+", "plus" -> {
            val sum = a + b
            println(sum)
        }
        "-", "minus" -> {
            val diff = a - b
            println(diff)
        }
        "*", "times" -> {
            val product = a * b
            println(product)
        }
        else -> println("Unknown operator")
    }
    ```

  * ```kotlin
    fun main(){
        val n = readLine()!!.toInt()
        
        when {
            n == 0 -> println("n is zero")
            n in 100..200 -> println("n is between 100 and 200")
            n > 300 -> println("n is greater than 300")
            n < 0 -> println("n is negative")
            // else-branch is optional here
        }
    }
    ```

  * 



  

## 函数

### 简化一个函数的过程

```kotlin
fun generateAnswerString(countThreshold: Int): String {
    val answerString = if (count > countThreshold) {
        "I have the answer."
    } else {
        "The answer eludes me."
    }

    return answerString
}
```

```kotlin
fun generateAnswerString(countThreshold: Int): String {
    return if (count > countThreshold) {
        "I have the answer."
    } else {
        "The answer eludes me."
    }
}
```

return 自动省略

```kotlin
fun generateAnswerString(countThreshold: Int): String = if (count > countThreshold) {
        "I have the answer"
    } else {
        "The answer eludes me"
    }
```

匿名函数的特殊之处：在运行时创建，所以可以访问**已经声明的其他变量**

```kotlin

```



### 匿名函数

```kotlin
val stringLengthFunc: (String) -> Int = { input ->
    input.length
}
```

stringLengthFunc是对匿名函数的引用，可以没有该引用,该函数作用返回字符串的长度

### 高阶函数

一个函数可以将另一个函数当作参数

```kotlin
fun stringMapper(str: String, mapper: (String) -> Int): Int {
    // Invoke function
    return mapper(str)
}
```

使用例子：

```kotlin
stringMapper("Android", { input ->
    input.length
})
```

如果匿名函数是在某个函数上定义的最后一个参数，则您可以在用于调用该函数的圆括号之外传递它

```kotlin
stringMapper("Android") { input ->
    input.length
}
```

### 作用域函数

Kotlin 专门创建了 5 个作用域函数，即：`let`、`apply`、`with`、`run` 和 `also`。这些函数虽简洁但功能强大，它们均具有接收器 (`this`)，可以带有参数 (`it`)，还有可能返回值。您可根据自己想要实现的目标，以决定使用哪个函数。

![img](https://developer.android.google.cn/codelabs/java-to-kotlin/img/1a70df5d611a5e78.png)

### 特殊函数

`any` function gets a predicate as an argument and returns true if at least one element satisfies the predicate.

判断集合中是否有偶数

```kotlin
fun containsEven(collection: Collection<Int>): Boolean =
        collection.any { it%2==0 }
```



### 运算符--函数

运算符实际上是调用了函数

可以对其进行重载

实现Comparable<> 接口

具体细节见官网：https://kotlinlang.org/docs/operator-overloading.html#comparison-operators

### 输入

```kotlin
val a = readLine()!!
val b = readLine()!!.toInt()
//用enter作为间隔
```

```kotlin
//用空格作为间隔
val (a, b) = readLine()!!.split(" ")
println(a)
println(b)
```

### 函数也是对象

```kotlin
fun sum(a: Int, b: Int): Int = a + b
//sum has a type of (Int, Int) -> Int.

val sumObject = ::sum //sumObject现在是对函数的引用
//区别
val sumResult = sum(1, 2)
```

```kotlin
fun applyAndSum(a: Int, b: Int, transformation: (Int) -> Int): Int {
    return transformation(a) + transformation(b)
}

fun same(x: Int) = x
fun square(x: Int) = x * x
fun triple(x: Int) = 3 * x

applyAndSum(1, 2, ::same)    // returns 3 = 1 + 2
applyAndSum(1, 2, ::square)  // returns 5 = 1 * 1 + 2 * 2
applyAndSum(1, 2, ::triple)  // returns 9 = 3 * 1 + 3 * 2
```

### Lambda expressions

不需要函数名的两种方式：

- `fun(arguments): ReturnType { body }` – this one is commonly called an "anonymous function".
- `{ arguments -> body }` – this one is commonly called a "lambda expression".

没有函数名如何使用函数

```kotlin
val mul1 = fun(a: Int, b: Int): Int {
    return a * b
}

val mul2 = { a: Int, b: Int -> a * b }

println(mul1(2, 3))  // prints "6"
println(mul2(2, 3))  // prints "6" too
```

```kotlin
//再谈函数的简化过程
originalText.filter({ c -> c != '.' })
originalText.filter() { c -> c != '.' }
originalText.filter { c -> c != '.' }
```



## 类

### 构造函数

```kotlin
class Size(_width: Int, _height: Int) {
    var width: Int = 0
    var height: Int = 0

    //这里每次都会被执行
    init {
        width = if (_width >= 0) _width else {
            println("Error, the width should be a non-negative value")
            0
        }
        height = if (_height >= 0) _height else {
            println("Error, the height should be a non-negative value")
            0
        }
    }
}
```



### 继承

```kotlin
class LoginFragment : Fragment()
```

override

```kotlin
override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
): View? {
    return inflater.inflate(R.layout.login_fragment, container, false)
}
```

使用父类函数

```kotlin
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
    super.onViewCreated(view, savedInstanceState)
}
```

SAM转换

`setOnClickListener()` 始终接受 `OnClickListener` 作为参数，又因为 `OnClickListener` 始终都有相同的单一抽象方法,此过程称为[单一抽象方法转换](https://kotlinlang.org/docs/reference/java-interop.html#sam-conversions)，简称 SAM 转换。

```kotlin
loginButton.setOnClickListener {
    //do something...
}
```

伴生对象 类似于java中的static关键字，本质上仍然是类的成员，要想真正变成java的静态类，需要@JvmStatic注解

```kotlin
class LoginFragment : Fragment() {
    companion object {
        private const val TAG = "LoginFragment"
    }
}
```

### 内部类

```kotlin
class Cat(val name: String) {
    fun sayMeow() {
        println("$name says: \"Meow\".")
    }

    inner class Bow(val color: String) {
        fun putOnABow() {
            sayMeow()
            println("The bow is on!")
        }

        fun printColor() {
            println("The cat named $name has a $color bow.")
        }
    }
}
```

- A class declared inside another class is called nested.
- An **inner class** is a special case of a nested class that **can access the members of its outer class**.
- Inner classes carry a reference to an object of an outer class, so to use inner classes, we must instantiate an outer class first.
- The main idea of inner classes is to hide some code from other classes and increase encapsulation.

## java互调

* String! 代表不知道映射过来是String还是String？



## 空指针相关

?: 代表前面结果为空的默认值

```kotlin
val account = Account("name", "type")
val accountName = account.name?.trim() ?: "Default name"
```

## 未懂部分

### 属性委托

```
private val viewModel: LoginViewModel by viewModels()
```

## 样式指南

| 选项     | 规范                               |
| -------- | ---------------------------------- |
| 编码     | utf-8                              |
| 类命名   | //MyCar.kt   MyCar                 |
| 函数名称 | camelCase     e.g sendMessage      |
| 常量     | UPPER_SNAKE_CASE  e.g COMMA_JOINER |
| 注释     | /* */ 用于文件头  /** */ 用于函数  |

**新函数不应直接习惯性地添加到类的末尾，因为这样会产生“按添加日期先后顺序”排序，而这不是逻辑排序**

## Log

用于记录过程中的关键信息

一般记录用户的输入输出

并发环境下还要记录线程

level：Debug, Info, Warn, Error, Fatal (from the least critical level to the most critical one).

* **Debug** 用于输出调试信息，函数的输入输出等
* **Info**  关于应用的重要信息  --- log service start, service stop, configurations, assumptions.
* **Warn**  不会影响用户，但是可能有资源找不见等
* **Error**  影响操作结果，但不会使程序立刻崩溃
* **Fatal**  造成程序崩溃

**格式**    `[date time][log level][message]`





## java2Kotlin

* 声明为data会派生 `equals()`、`hashCode()` 和 `toString()` 函数

* 在 Kotlin 中，主构造函数无法包含任何代码，因此初始化代码会置于 `init` 块中

* `object` 用于声明单例类，用 `object` 类时，我们直接在对象上调用函数和属性，如下所示：

  * ```kotlin
    val users = Repository.users
    ```

* 支持数据解构，直接提取数据中的内容

  * ```kotlin
    for ((firstName, lastName) in users) {
           val name: String?
    
           if (lastName != null) {
              if (firstName != null) {
                    name = "$firstName $lastName"
              } 
    ```

* 在 Kotlin 中，`if`、`when`、`for` 和 `while` 均为表达式，它们有返回值。

* [`map`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map.html) 函数。该函数会返回一个新列表，包含对原数组中每个元素调用指定转换函数后的结果

* java不支持默认的构造函数

## java虚拟机

执行程序时，先将java代码编译为.class文件(bytecode)

之后JVM主要完成如下工作

* loads bytecode
* verify bytecode
* execute bytecode
* provide the runtime environment

### JVM构造

![img](https://ucarecdn.com/1e15b25e-8cb4-42d4-9c3f-8a697c9458b4/)

class loader

​	完成基本的校验工作



- **PC register** holds the address of the currently executing instruction;
- **stack area** is a memory place where methods' calls and local variables are stored;
- **native method stack** stores native method information;
- **heap** stores all created objects (instances of classes);
- **method area** stores all the class level information like class name, immediate parent class name, method information and all static variables.

Every thread has its own **PC register**, **stack**, and **native method stack**, but all threads share the same **heap** and **method area.**

##### Execution engine

- **bytecode interpreter** interprets the bytecode line by line and executes it (rather slowly);

- **just-in-time compiler** (JIT compiler) translates bytecode into native machine language while executing the program (it executes the program faster than the interpreter);

- **garbage collector** cleans unused objects from the heap.

  - 调用
    - Runtime.getRuntime().gc()
    - System.gc()
    - 请求垃圾收集器执行，但是不一定会执行

- GC两种策略

  - **Reference counting** 没创建一个对象就会有指针指向他，当指针为0时候，该对象被清除，但是无法辨别AB互相指向对方的情况。
  - **Tracing**一般JVM都用这种方法，**local variables** and **method parameters**, **threads**, **static variables**.作为节点，之后不断延伸，如果没有连接，就是不用的，但是必须全部遍历，才能进行删除操作

  * 缺点
    * 自动的垃圾收集机制消耗大量资源
    * 需要程序暂停一段时间执行垃圾回收机制
    * 内存不连续，每次删除掉对象，都需要复写对象让对象在内存中挨在一起

## 特殊程序

```kotlin
fun main() {
//    write yor code here
    var next= readLine()!! //这里换成val  区别很大  exit 无法退出了就？
    while (next!="exit"){
        println(next.toInt())
        next= readLine()!!
    }
    println("See you later!")
}
```

```kotlin
println('1'+2+"3") // 33
//解释
//'1'+2--->生成'1'd的Unicode码加2的，也是一个char
```

```kotlin
//判断是不是0-9
val ch = '2' 
val isDigit = ch in '\u0030'..'\u0039'
println(isDigit)
```

```kotlin
println(readLine()!!.toInt() in readLine()!!.toInt()..readLine()!!.toInt())
//会把先输入的两个数作为范围 也就是后面的参数
```

