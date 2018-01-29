---
layout: page
title: 代码风格指导
site_nav_category_order: 200
is_site_nav_category: true
site_nav_category: style
---

这篇文档提供了使用 Kotlin 编程语言开发 Android 的 Google 代码标准完整定义。Google Android 风格的 Kotlin 源文件应当遵循这里的规范。

类似其它编程风格规范，所包括的问题不仅涉及格式的美观问题，也涉及了其它约定和编码标准。然而，本文主要专注于我们都要遵循的硬性规定，并避免给出不明确以执行（无论由人还是工具）的建议。

_<a href="changelog.html">上次更新时间（官方源）: {{ site.changes.last.date | date: "%Y-%m-%d" }}</a>_

# 源文件

所有源文件必须以 UTF-8 为编码。

## 命名

如果一个源文件只包含一个顶级类，文件名应为对应的区分大小写的名并加上 `.kt` 扩展名。除此以外，如果一个源文件包含多个顶级声明，选择一个可以描述文件内容的名字，应用 PascalCase 命名法，然后加上 `.kt` 扩展名。

```kotlin
// Foo.kt
class Foo { }

// Bar.kt
class Bar { }
fun Runnable.toBar(): Bar = // …

// Map.kt
fun <T, O> Set<T>.map(func: (T) -> O): List<O> = // …
fun <T, O> List<T>.map(func: (T) -> O): List<O> = // …
```

## 特殊字符

### 空白字符

除了换行符, **ASCII 横向空格字符 (0x20)** 是唯一可在源文件里出现的空格字符。这意味着：

 1. 所有字符串和字符中的其它空白字符都会被转义。
 2. Tab 字符 **不会被** 用于缩进。

### 特殊的转义符

对于任何有 [特殊转义符](https://kotlinlang.org/docs/reference/basic-types.html#characters) 的字符应该使用对应的字符串 (`\b`, `\n`, `\r`, `\t`, `\'`, `\"`, `\\`, and `\$`) 而不是相应的 Unicode (例如 `\u000a`) 转义符。

### 非 ASCII 字符

对于其余的非 ASCII 字符，使用实际的 Unicode 字符 (例如 `∞`) 或者同义的 Unicode 转义符 (例如 `\u221e`)。选择取决于哪种让代码 **更易于阅读理解** 。强烈建议除了字符串和注释以外，可显示的字符都不使用 Unicode 转义符。

| **例子**                           | **评论**                                        |
|------------------------------------|-------------------------------------------------|
| `val unitAbbrev = "μs"`            | 最好: 即便没有标注也很清晰                      |
| `val unitAbbrev = "\u03bcs" // μs` | 不建议: 这里没有理由为一个可显示的字符进行转义。|
| `val unitAbbrev = "\u03bcs"`       | 不建议: 读者不知道这是什么字符                  |
| `return "\ufeff" + content`        | 可以: 为不显示的字符使用转义，有必要的时候标注  |


## 结构

一个 `.kt` 文件按照顺序包含下列内容：

1. 版权和/或许可头部 (可选)
2. 文件级注释
3. 包声明
4. 引入声明
5. 顶级声明

恰好一行空白行来分割这些内容。

### 版权 / 许可

如果文件中包含版权或许可证标题，则应该将它们放在多行注释的顶部。

```kotlin
/*
 * Copyright 2017 Google, Inc.
 *
 * ...
 */
 ```

不要使用 [KDoc 风格](https://kotlinlang.org/docs/reference/kotlin-doc.html) 或者 单行风格 注释。

```kotlin
/**
 * Copyright 2017 Google, Inc.
 *
 * ...
 */
```
```kotlin
// Copyright 2017 Google, Inc.
//
// ...
```

### 文件级别注解

注释和 'file' [use-site target](https://kotlinlang.org/docs/reference/annotations.html#annotation-use-site-targets) 应放置在包声明和任意头部注释。

### 包声明

包声明不受任何列的限制且永不换行。

### 引入声明

为类、方法、属性的引入应组合到单个 ASCII 顺序排列的列表。

任意类型的通配符在引入中是 **不允许的**。

和包声明一样，引入声明也不受任何列的限制且永不换行。

### 顶级声明

一个 `.kt` 文件可以在顶级声明一个或多个类型、方法、属性和类型别名。

一个文件的内容应该专注于同一个主题，这个例子可以是单个公开类或者一组扩展函数在多个接收器（Receivers）类型上执行相同的操作。不相关的声明应该被分到它们对应的文件，并应当减少单个文件的公开声明。

对文件内容的数量和顺序没有明确的限制。

源文件通常是从上至下的阅读，这意味着顺序通常反应出上面的声明会让人们更深入地了解这些内容。不同的文件可能选择不同的顺序。同样地，一个文件可能包含 100 个属性、10 个函数以及另外的一个类。

重要的是，每个类都是用 **_一些_** 维护者可以解释的 **逻辑顺序**。例如，新的方法不只是习惯地添加到类之后，这样会产生不合乎逻辑的“按日期添加”顺序。

### 类成员排序

类的成员顺序和顶级声明遵循同样的规则。


# 格式

## 花括号

对 `when` 的分支和没有 `else if` 或 `else` 分支的 `if` 语句来说，写成单行时花括号不是必需的。

```kotlin
if (string.isEmpty()) return

when (value) {
    0 -> return
    // …
}
```

对任何 `if`、`for`、`when` 的分支和 `do`、`while` 的表达式中，即便代码体是 空的或仅包含单行语句时，花括号也是必需的。

```kotlin
if (string.isEmpty())
    return  // 错误的!

if (string.isEmpty()) {
    return  // 正确
}
```

### 非空代码区块

非空代码区块和区块型构造花括号遵循 Kernighan 和 Ritchie 风格 ("Egyptian brackets")：

* 头部花括号不需要换行
* 在头部花括号后换行
* 在尾部花括号前换行
* 只有在花括号结束一个语句、一个方法的代码体或者一个命名类时，才在尾部花括号后换行。例如花括号后面有 `else` 或者逗号时不用换行。

```kotlin
return Runnable {
    while (condition()) {
        foo()
    }
}

return object : MyClass() {
    override fun foo() {
        if (condition()) {
            try {
                something()
            } catch (e: ProblemException) {
                recover()
            }
        } else if (otherCondition()) {
            somethingElse()
        } else {
            lastThing()
        }
    }
}
```

一些 [常量方法](#enum-classes) 的例外会在下面给出。

### 空的代码区块

一个空的代码区块或者区块型构造必须是 K&R 风格。

```kotlin
try {
    doSomething()
} catch (e: Exception) {} // 错误的!
```
```kotlin
try {
    doSomething()
} catch (e: Exception) {
} // 可以
```

### 表达式

用作表达式的 `if`/`else` 可能会省略花括号，但仅在整个表达式只有一行时适用。

```kotlin
val value = if (string.isEmpty()) 0 else 1  // 可以
```
```kotlin
val value = if (string.isEmpty())  // 错误的!
                0
            else
                1
```
```kotlin
val value = if (string.isEmpty()) { // 可以
    0
} else {
    1
}
```


## 缩进

每次打开一个新的代码区块或者区块型构造，缩进增加四个空格。当区块结束时，缩进回到前一个级别。缩进级别适用于整个区块的代码和注释。


## 一行一句

每句都断行，并且不使用分号。


## 行宽

一列的代码被限制在 100 个字符内。除了下面的情况，任何超过这个限制的行，都必须按照下面的解释进行换行。

例外:

* 不可能遵循列限制的行 (比如在 KDoc 的一条长链接地址)
* `package` 和 `import` 声明
* 可能会被拷贝到 Shell 中使用的命令行

### 换行位置

换行的主要指示：优先在更高的句式级别上换行。包括：

 1. 当一行在 _非赋值_ 运算符换行时，在符号 _之前_ 断行。
    * 这也适用于 "类似运算符" 的符号:
      * 分割符：句号 (`.`)
      * 方法引用：两个冒号 (`::`)
 2. 当一行在 _赋值_ 运算符换行时，在符号 _之后_ 断行。
 3. 一个方法或构造名要紧跟着它的头部括号 (`(`)。
 4. 一个逗号 (`,`) 要紧跟着它前面的标志（Token）。
 5. 一个 Lambda 箭头 (`->`) 要紧跟它前面的参数列表。

注意: 换行的主要目的是拥有更清晰的代码，_不一定_ 要让代码有最少的行数。


### 继续缩进

当换行后，每一行 (每个 _连续行_) 之后对原来的行至少缩进 +8。

当有多个连续行时，缩进可能根据需求增加到 +8 以上。通常来说，当且仅当两个连续行在语法上并行的元素开始时，使用相同的缩进级别。

### 函数

当一个函数签名不在一行内时，每个参数声明都断出自己的行。在这种格式定义的参数应该使用单缩进（+4）。尾部括号 (`)`) 和返回类型放在独自的行且没有额外的缩进。

```kotlin
fun <T> Iterable<T>.joinToString(
    separator: CharSequence = ", ",
    prefix: CharSequence = "",
    postfix: CharSequence = ""
): String {
    // …
}
```

#### 表达式函数

当一个函数只包含一行表达式时它代表了一个 [表达式函数](https://kotlinlang.org/docs/reference/functions.html#single-expression-functions).

```kotlin
override fun toString(): String {
    return "嘿"
}
```
```kotlin
override fun toString(): String = "嘿"
```

表达式函数不应该换到两行，如果一个表达式很长以至于需要换行，使用正常的函数体、`return` 声明和正常的表达式换行规则代替。

### 属性

当一个属性的初始化不在一行内，在等号 (`=`) 后换行并使用继续缩进。

```kotlin
private val defaultCharset: Charset? =
        EncodingRegistry.getInstance().getDefaultCharsetForPropertiesFiles(file)
```

声明了 `get` 和/或 `set` 方法的属性应当在它们各自的行内且有一个正常缩进（+4）。使用和函数一样的规则格式。

```kotlin
var directory: File? = null
    set(value) {
        // …
    }
```

只读属性可以在一行内时可以使用更简短的句式。

```kotlin
val defaultExtension: String get() = "kt"
```


## 空白

### 纵向

一个单空行应该出现在:

 1. 在类的连续成员 _之间_：属性、构造函数、方法、嵌套类等等。

     * **例外**: 两个连续属性之间（没有其它代码）的空行是可选的。如果需要，根据创建属性的逻辑分类的需求使用这种空白行，将属性和其后面的属性相连。

     * **例外**: 枚举常量之间的空白行如下所示。

 2. 表达式之间，_根据需要_ 用来组织代码成一节逻辑集。

 3. _可选_ ：在 方法的第一行/类的第一个成员 前或 类的最后一个成员 后使用空行。

 4. 按照本文档其它部分的要求（例如 ["结构"](#结构) 部分).

允许多行连续的空白行，既不会鼓励也不要求。

### 横向

除了语言或其它风格规范的要求外，除文字、注释和 KDoc 外，一个 ASCII 空格字符也应该只出现在以下位置：

 1. 将任何一个保留字（如 `if`、`for` 或 `catch`）和同一行内一个头部括号 (`(`) 分隔开来。

    ```kotlin
    // 错误的!
    for(i in 0..1) {
    }
    ```
    ```kotlin
    // 可以
    for (i in 0..1) {
    }
    ```

 2. 将任何一个保留字（如 `else` 或 `catch`）和同一行内一个尾部花括号 (`}`) 分割开来。

    ```kotlin
    // 错误的!
    }else {
    }
    ```
    ```kotlin
    // 可以
    } else {
    }
    ```

 3. 在任何头部花括号前 (`{`).

    ```kotlin
    // 错误的!
    if (list.isEmpty()){
    }
    ```
    ```kotlin
    // 可以
    if (list.isEmpty()) {
    }
    ```

 4. 任何二元运算符的两边

    ```kotlin
    // 错误的!
    val two = 1+1
    ```
    ```kotlin
    // 可以
    val two = 1 + 1
    ```

    这也适用于下面的 "运算符型" 符号：

     *  一个 Lambda 表达式的箭头 (`->`)

        ```kotlin
        // 错误的!
        ints.map { value->value.toString() }
        ```
        ```kotlin
        // 可以
        ints.map { value -> value.toString() }
        ```

    但不适用于：

     *  方法引用的两个冒号 (`::`)

        ```kotlin
        // 错误的!
        val toString = Any :: toString
        ```
        ```kotlin
        // 可以
        val toString = Any::toString
        ```

     *  分隔符句号 (`.`).

        ```kotlin
        // 错误的!
        it . toString()
        ```
        ```kotlin
        // 可以
        it.toString()
        ```

    *  范围运算符 (`..`).

        ```kotlin
        // 错误的!
        for (i in 1 .. 4) print(i)
        ```
        ```kotlin
        // 可以
        for (i in 1..4) print(i)
        ```

 5. 只有在用于基类/接口的类声明或在 [泛型约束](https://kotlinlang.org/docs/reference/generics.html#generic-constraints) 的 `where` 子句中使用时的冒号前

    ```kotlin
    // 错误的!
    class Foo: Runnable
    ```
    ```kotlin
    // 正确
    class Foo : Runnable
    ```

    ```kotlin
    // 错误的
    fun <T: Comparable> max(a: T, b: T)
    ```
    ```kotlin
    // 正确
    fun <T : Comparable> max(a: T, b: T)
    ```

    ```kotlin
    // 错误的
    fun <T> max(a: T, b: T) where T: Comparable<T>
    ```
    ```kotlin
    // 正确
    fun <T> max(a: T, b: T) where T : Comparable<T>
    ```

 6. 在一个逗号 (`,`) 或冒号 (`:`) 之后.

    ```kotlin
    // 错误的!
    val oneAndTwo = listOf(1,2)
    ```
    ```kotlin
    // 正确
    val oneAndTwo = listOf(1, 2)
    ```

    ```kotlin
    // 错误的!
    class Foo :Runnable
    ```
    ```kotlin
    // 正确
    class Foo : Runnable
    ```

 7. 开始行尾注释的双斜杠 (`//`) 的两边，这里允许多个空格但不作要求。

    ```kotlin
    // 错误的!
    var debugging = false//默认禁用
    ```
    ```kotlin
    // 正确
    var debugging = false // 默认禁用
    ```

这条规则不能解释为要求或禁止行首/行末的额外空格，它只涉及行内的空格。


## 具体构造

### 枚举常量类

没有函数和文档的枚举里它的常量可以选择单行格式。

```kotlin
enum class Answer { YES, NO, MAYBE }
```

当一个枚举常量放在不同的行时，它们之间不需要空行，除非定义了一个代码体。

```kotlin
enum class Answer {
    YES,
    NO,

    MAYBE {
        override fun toString() = """¯\_(ツ)_/¯"""
    }
}
```

因为枚举类也是类，其它用于格式类的规范也适用。

### 注解

成员或类型的注解要放置在注解结构前的不同行中。

```kotlin
@Retention(SOURCE)
@Target(FUNCTION, PROPERTY_SETTER, FIELD)
annotation class Global
```

没有参数的注解可以放在单行内。

```kotlin
@JvmField @Volatile
var disposable: Disposable? = null
```

只有单个没有参数的注释可以和声明放在同一行。

```kotlin
@Volatile var disposable: Disposable? = null

@Test fun selectAll() {
    // …
}
```

### 隐式返回/属性类型

如果一个表达式函数体或者一个属性初始化是一个标量值（Scalar value）或者返回类型可以从函数体中清楚地推断出来时，可以忽略类型。

```kotlin
override fun toString(): String = "嘿"
// becomes
override fun toString() = "嘿"
```
```kotlin
private val ICON: Icon = IconLoader.getIcon("/icons/kotlin.png")
// becomes
private val ICON = IconLoader.getIcon("/icons/kotlin.png")
```

在编写库时，在公开 API 部分中保持显式类型声明。


# 命名

标识符只使用 ASCII 字母、数字和在下面指出的少数情况。因此每个有效的标识符名称都由 `\w+` 正则表达式匹配。

除了["返回值属性"](#返回值属性)以外，不使用类似 `name_`、`mName`、`s_name`、`kName` 这样的特殊前缀或后缀。


## 包名

包名应该全部都是小写，且连续的单词连在一起（不使用下划线）。

```kotlin
// 正确
package com.example.deepspace
// 错误的!
package com.example.deepSpace
// 错误的!
package com.example.deep_space
```


## 类名

类名使用 PascalCase 法命名且通常为名词或名词短语。比如，`Character` 或 `ImmutableList`。接口名字可以是名词或名词短语（比如 `List`），但有时也可以是形容词或者形容词短语（比如 `Readable`）。

测试类使用要测试的类的名字作为开头，并以 `Test` 结尾。比如 `HashTest` 或 `HashIntegrationTest`。


## 函数名

函数名使用 camelCase（驼峰）法命名且通常为动词或者动词短语。比如 `sendMessage` 或 `stop`。

下划线被允许在测试函数的名字中出现用于分隔逻辑组件的名称。

```kotlin
@Test fun pop_emptyStack() {
    // …
}
```


## 常量名

常量名使用 UPPER_SNAKE_CASE 命名法: 全部大写、用下划线分隔。但究竟什么 _才是_ 一个常量？

常量是一个没有自定义 `get` 方法的 `val` 属性，且它的值完全不可变、它的方法没有可检测的副作用。这包括了不可变类型、使用不可变类型的不可变集合和被标记为 `const` 的标量/字符串。如果任何一个实例可观察到的状态能够被改变，就不是常量了，仅在意愿上不改变对象是不足以符合条件的。

```kotlin
const val NUMBER = 5
val NAMES = listOf("艾莉丝", "鲍勃")
val AGES = mapOf("艾莉丝" to 35, "鲍勃" to 32)
val COMMA_JOINER = Joiner.on(',') // Joiner 是不可变的
val EMPTY_ARRAY = arrayOf<SomeMutableType>()
```

这些名字通常是名词或名词短语。

常量值只可以在一个 `object` 或者顶级中声明定义。否则，满足常量定义的值在类中定义必须使用非常量的名字。

作为标量的常量必须使用 [`const` 标识符](http://kotlinlang.org/docs/reference/properties.html#compile-time-constants).


## 变量（非常量）名

非常量名字用 camelCase（驼峰）法命名。这些适用于实例属性、本地属性和参数名。

```kotlin
val variable = "var"
val nonConstScalar = "non-const"
val mutableCollection: MutableSet<String> = HashSet()
val mutableElements = listOf(mutableInstance)
val mutableValues = mapOf("電" to mutableInstance, "天津風" to mutableInstance2)
val logger = Logger.getLogger(MyClass::class.java.name)
val nonEmptyArray = arrayOf("这些", "可以", "改变")
```

这些名字通常是名词或名词短语。

### 返回值属性

当一个[返回值属性](https://kotlinlang.org/docs/reference/properties.html#backing-properties)需要时，其名字应该和实际属性的名字完全匹配，除了前缀是下划线。

```kotlin
private var _table: Map<String, Int>? = null

val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap()
        }
        return _table ?: throw AssertionError()
    }
```


## 类型变量名

每个类型变量都以两种风格其中之一进行命名：

 1. 一个大写字母，可以跟着一个数字。（比如 `E`、`T`、`X`、`T2`）
 2. 用于类的形式的名字跟随着一个大写字母 `T` （比如 `RequestT`、`FooBarT`）


## 驼峰命名

有时，有多种合理的方法将英文词语转换为驼峰命名，比如缩略词或像 "IPv6" 或 "iOS" 等不寻常的词语结构。为了提高可预测性、降低理解难度，使用以下的方案。

从名字的散文形式（the prose form）开始：

 1. 将词语转换到简单的 ASCII 并移除任何撇号。比如 "Müller's algorithm" 可以变成 "Muellers algorithm"。

 2. 将这个结果分割成单词，分割空格和任何剩下的标点符号（通常是连接字符）。

    * _推荐_: 如果任何单词已经有了广泛使用的公认驼峰命名案例，则将其分割成它的组成部分（比如 "AdWords" 变成 "ad words"）。请注意像 "iOS" 这样的单词本身并不是真正的驼峰命名例子，其违背了 _任何_ 案例，因此这个建议不适用。

 3. 现在全部字母小写（包括首字母缩略词），然后只对第一个字符大写：

    * ...每个单词都产生驼峰，或

    * ...除了第一个，每个单词都产生驼峰

 4. 最终，组合所有词成一个标识符。

请注意，原词语的原样式几乎完全被忽略。

| **散文形式（Prose form）** | **正确**                                | **错误**            |
|----------------------------|-----------------------------------------|---------------------|
| "XML Http Request"         | `XmlHttpRequest`                        | `XMLHTTPRequest`    |
| "new customer ID"          | `newCustomerId`                         | `newCustomerID`     |
| "inner stopwatch"          | `innerStopwatch`                        | `innerStopWatch`    |
| "supports IPv6 on iOS"     | `supportsIpv6OnIos`                     | `supportsIPv6OnIOS` |
| "YouTube importer"         | `YouTubeImporter`<br>`YoutubeImporter`* |                     |

(_*可接受使用，但不会推荐。_)

**注意**: 有些英文单词是不明确地使用连接字符：比如 "nonempty" 和 "non-empty" 都是对的，所以方法名 `checkNonempty` 和 `checkNonEmpty` 都似乎是正确的。


# 文档


## 格式

KDoc 区块的基本格式如下：

```kotlin
/**
 * 多行 KDoc 文本在这里写，
 * 通常这样包裹着……
 */
fun method(arg: String) {
    // …
}
```

...或者像这样的单行注释:

```kotlin
/** 一个非常短的 KDoc。 */
```

基本格式总是可以接受的。当整个 KDoc 区块（包括注释标记）可以放在一行上时，可以代替单行形式。请注意，这只适用于没有区块注释（例如 `@return`）的情况。

### 段落

一个空白行——即只包括对齐的前置星号（`*`）的行在段落之间出现，并在区块标记组（如果存在）前出现。

### 区块标签

任何使用的标准“区块标签”都按照 `@constructor`、`@receiver`、`@param`、`@property`、`@return`、`@throws`、`@see` 的顺序且都不会空的描述出现。当一个区块标记不适合在单行时，连续行将从 `@` 的位置缩进 8 个空格。


## 概要片段

每个 KDoc 区块以一个简短的概要片段开始，这个片段非常重要：它是文本的唯一部分出现在某些上下文中，例如类和方法索引。

它是一个片段——一个名词短语或动词短语，而不是一个完整的句子。其不以 "``A `Foo` is a…``" 或 "`This method returns…`" 开头，也不必形成一个完整的祈使句，如 "`Save the record.`"。然而，这个片段是大写开头且有标点，就好像是一个完整的句子一样。


## 用法

每个 `public` 类型、`public` 或 `protected` 的成员都至少有 KDoc，除了下面举出的例子。

### 例外: 无需解释的方法

对于像 `getFoo` 这样“简单、明显”的方法和像 `foo` 这样的属性，KDoc 是可选的。在真的没有什么可说的时候，就是 "Returns the foo"。

引用这个例外是不恰当的，因为我们可能忽略读者通常需要知道的相关信息。例如一个叫 `getCanonicalName` 的方法或者叫 `canonicalName` 的属性，如果通常读者可能不知道术语的 `canonical name` 的意思的话，就不要忽略它的文档（合理地是解释 `/** Returns the canonical name. */`）！

### 例外: 重载

KDoc 并不是总出现在重载的 super 类型方法。
