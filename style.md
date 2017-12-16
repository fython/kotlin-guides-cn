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

源文件通常是从上至下的阅读，这意味着顺序通常反应出上面的声明会让人们更深入地裂解这些内容。不同的文件可能选择不同的顺序。同样地，一个文件可能包含 100 个属性、10 个函数以及另外的一个类。

重要的是，每个类都是用 **_一些_** 维护者可以解释的 **逻辑顺序**。例如，新的方法不只是习惯地添加到类之后，这样会产生不合乎逻辑的“按日期添加”顺序。

### 类成员排序

类的成员顺序和顶级声明遵循同样的规则。


# 格式

## 花括号

Braces are not required for `when` branches and `if` statement bodies which have no `else if`/`else` branches and which fit on a single line.

```kotlin
if (string.isEmpty()) return

when (value) {
    0 -> return
    // …
}
```

Braces are otherwise required for any `if`, `for`, `when` branch, `do`, and `while` statements, even when the body is empty or contains only a single statement.

```kotlin
if (string.isEmpty())
    return  // WRONG!

if (string.isEmpty()) {
    return  // Okay
}
```

### 非空代码区块

Braces follow the Kernighan and Ritchie style ("Egyptian brackets") for nonempty blocks and block-like constructs:

* No line break before the opening brace.
* Line break after the opening brace.
* Line break before the closing brace.
* Line break after the closing brace, _only if_ that brace terminates a statement or terminates the body of a function, constructor, or _named_ class. For example, there is _no_ line break after the brace if it is followed by `else` or a comma.

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

A few exceptions for [enum classes](#enum-classes) are given below.

### 空的代码区块

An empty block or block-like construct must be in K&R style.

```kotlin
try {
    doSomething()
} catch (e: Exception) {} // WRONG!
```
```kotlin
try {
    doSomething()
} catch (e: Exception) {
} // Okay
```

### 表达式

An `if`/`else` conditional that is used as an expression may omit braces _only_ if the entire expression fits on one line.

```kotlin
val value = if (string.isEmpty()) 0 else 1  // Okay
```
```kotlin
val value = if (string.isEmpty())  // WRONG!
                0
            else
                1
```
```kotlin
val value = if (string.isEmpty()) { // Okay
    0
} else {
    1
}
```


## 缩进

Each time a new block or block-like construct is opened, the indent increases by four spaces. When the block ends, the indent returns to the previous indent level. The indent level applies to both code and comments throughout the block.


## 一行一句

Each statement is followed by a line break. Semicolons are not used.


## 行宽

Code has a column limit of 100 characters. Except as noted below, any line that would exceed this limit must be line-wrapped, as explained below.

Exceptions:

* Lines where obeying the column limit is not possible (for example, a long URL in KDoc)
* `package` and `import` statements
* Command lines in a comment that may be cut-and-pasted into a shell

### 断句位置

The prime directive of line-wrapping is: prefer to break at a **higher syntactic level**. Also:

 1. When a line is broken at a _non-assignment_ operator the break comes _before_ the symbol.
    * This also applies to the following "operator-like" symbols:
      * the dot separator (`.`)
      * the two colons of a member reference (`::`)
 2. When a line is broken at an _assignment_ operator the break comes _after_ the symbol.
 3. A method or constructor name stays attached to the open parenthesis (`(`) that follows it.
 4. A comma (`,`) stays attached to the token that precedes it.
 5. A lambda arrow (`->`) stays attached to the argument list that precedes it.

Note: The primary goal for line wrapping is to have clear code, _not necessarily_ code that fits in the smallest number of lines.


### 继续缩进

When line-wrapping, each line after the first (each _continuation line_) is indented at least +8 from the original line.

When there are multiple continuation lines, indentation may be varied beyond +8 as desired. In general, two continuation lines use the same indentation level if and only if they begin with syntactically parallel elements.

### 函数

When a function signature does not fit on a single line, break each parameter declaration onto its own line. Parameters defined in this format should use a single indent (+4). The closing parenthesis (`)`) and return type are placed on their own line with no additional indent.

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

When a function contains only a single expression it can be represented as an [expression function](https://kotlinlang.org/docs/reference/functions.html#single-expression-functions).

```kotlin
override fun toString(): String {
    return "Hey"
}
```
```kotlin
override fun toString(): String = "Hey"
```

Expression functions should not wrap to two lines. If an expression function grows to require wrapping, use a normal function body, a `return` declaration, and normal expression wrapping rules instead.

### 属性

When a property initializer does not fit on a single line, break after the equals sign (`=`) and use a continuation indent.

```kotlin
private val defaultCharset: Charset? =
        EncodingRegistry.getInstance().getDefaultCharsetForPropertiesFiles(file)
```

Properties declaring a `get` and/or `set` function should place each on their own line with a normal indent (+4). Format them using the same rules as functions.

```kotlin
var directory: File? = null
    set(value) {
        // …
    }
```

Read-only properties can use a shorter syntax which fits on a single line.

```kotlin
val defaultExtension: String get() = "kt"
```


## 空白

### 纵向

A single blank line appears:

 1. _Between_ consecutive members of a class: properties, constructors, functions, nested classes, etc.

     * **Exception**: A blank line between two consecutive properties (having no other code between them) is optional. Such blank lines are used as needed to create logical groupings of properties and associate properties with their backing property, if present.

     * **Exception**: Blank lines between enum constants are covered below.

 2. Between statements, as _needed_ to organize the code into logical subsections.

 3. _Optionally_ before the first statement in a function, before the first member of a class, or after the last member of a class (neither encouraged nor discouraged).

 4. As required by other sections of this document (Such as the ["Structure"](#structure) section).

Multiple consecutive blank lines are permitted, but not encouraged or ever required.

### 横向

Beyond where required by the language or other style rules, and apart from literals, comments, and KDoc, a single ASCII space also appears in the following places only:

 1. Separating any reserved word, such as `if`, `for`, or `catch` from an open parenthesis (`(`) that follows it on that line.

    ```kotlin
    // WRONG!
    for(i in 0..1) {
    }
    ```
    ```kotlin
    // Okay
    for (i in 0..1) {
    }
    ```

 2. Separating any reserved word, such as `else` or `catch`, from a closing curly brace (`}`) that precedes it on that line.

    ```kotlin
    // WRONG!
    }else {
    }
    ```
    ```kotlin
    // Okay
    } else {
    }
    ```

 3. Before any open curly brace (`{`).

    ```kotlin
    // WRONG!
    if (list.isEmpty()){
    }
    ```
    ```kotlin
    // Okay
    if (list.isEmpty()) {
    }
    ```

 4. On both sides of any binary operator.

    ```kotlin
    // WRONG!
    val two = 1+1
    ```
    ```kotlin
    // Okay
    val two = 1 + 1
    ```

    This also applies to the following "operator-like" symbols:

     *  the arrow in a lambda expression (`->`).

        ```kotlin
        // WRONG!
        ints.map { value->value.toString() }
        ```
        ```kotlin
        // Okay
        ints.map { value -> value.toString() }
        ```

    But not:

     *  the two colons (`::`) of a member reference.

        ```kotlin
        // WRONG!
        val toString = Any :: toString
        ```
        ```kotlin
        // Okay
        val toString = Any::toString
        ```

     *  the dot separator (`.`).

        ```kotlin
        // WRONG
        it . toString()
        ```
        ```kotlin
        // Okay
        it.toString()
        ```

    *  the range operator (`..`).

        ```kotlin
        // WRONG
        for (i in 1 .. 4) print(i)
        ```
        ```kotlin
        // Okay
        for (i in 1..4) print(i)
        ```

 5. Before a colon (`:`) only if used in a class declaration for specifying a base class / interfaces or when used in a `where` clause for [generic constraints](https://kotlinlang.org/docs/reference/generics.html#generic-constraints).

    ```kotlin
    // WRONG!
    class Foo: Runnable
    ```
    ```kotlin
    // Okay
    class Foo : Runnable
    ```

    ```kotlin
    // WRONG
    fun <T: Comparable> max(a: T, b: T)
    ```
    ```kotlin
    // Okay
    fun <T : Comparable> max(a: T, b: T)
    ```

    ```kotlin
    // WRONG
    fun <T> max(a: T, b: T) where T: Comparable<T>
    ```
    ```kotlin
    // Okay
    fun <T> max(a: T, b: T) where T : Comparable<T>
    ```

 6. After a comma (`,`) or colon (`:`).

    ```kotlin
    // WRONG!
    val oneAndTwo = listOf(1,2)
    ```
    ```kotlin
    // Okay
    val oneAndTwo = listOf(1, 2)
    ```

    ```kotlin
    // WRONG!
    class Foo :Runnable
    ```
    ```kotlin
    // Okay
    class Foo : Runnable
    ```

 7. On both sides of the double slash (`//`) that begins an end-of-line comment. Here, multiple spaces are allowed, but not required.

    ```kotlin
    // WRONG!
    var debugging = false//disabled by default
    ```
    ```kotlin
    // Okay
    var debugging = false // disabled by default
    ```

This rule is never interpreted as requiring or forbidding additional space at the start or end of a line; it addresses only interior space.


## 具体构造

### 常量类

An enum with no functions and no documentation on its constants may optionally be formatted as a single line.

```kotlin
enum class Answer { YES, NO, MAYBE }
```

When the constants in an enum are placed on separate lines, a blank line is not required between them except in the case where they define a body.

```kotlin
enum class Answer {
    YES,
    NO,

    MAYBE {
        override fun toString() = """¯\_(ツ)_/¯"""
    }
}
```

Since enum classes are classes, all other rules for formatting classes apply.

### 注解

Member or type annotations are placed on separate lines immediately prior to the annotated construct.

```kotlin
@Retention(SOURCE)
@Target(FUNCTION, PROPERTY_SETTER, FIELD)
annotation class Global
```

Annotations without arguments can be placed on a single line.

```kotlin
@JvmField @Volatile
var disposable: Disposable? = null
```

When only a single annotation without arguments is present it may be placed on the same line as the declaration.

```kotlin
@Volatile var disposable: Disposable? = null

@Test fun selectAll() {
    // …
}
```

### 隐式返回/属性类型

If an expression function body or a property initializer is a scalar value or the return type can be clearly inferred from the body then it can be omitted.

```kotlin
override fun toString(): String = "Hey"
// becomes
override fun toString() = "Hey"
```
```kotlin
private val ICON: Icon = IconLoader.getIcon("/icons/kotlin.png")
// becomes
private val ICON = IconLoader.getIcon("/icons/kotlin.png")
```

When writing a library, retain the explicit type declaration when it is part of the public API.


# 命名

Identifiers use only ASCII letters and digits, and, in a small number of cases noted below, underscores. Thus each valid identifier name is matched by the regular expression `\w+`.

Special prefixes or suffixes, like those seen in the examples `name_`, `mName`, `s_name`, and `kName`, are not used except in the case of backing properties (see ["Backing properties"](#backing-properties)).


## 包名

Package names are all lowercase, with consecutive words simply concatenated together (no underscores).

```kotlin
// Okay
package com.example.deepspace
// WRONG!
package com.example.deepSpace
// WRONG!
package com.example.deep_space
```


## 类型名

Class names are written in PascalCase and are typically nouns or noun phrases. For example, `Character` or `ImmutableList`. Interface names may also be nouns or noun phrases (for example, `List`), but may sometimes be adjectives or adjective phrases instead (for example `Readable`).

Test classes are named starting with the name of the class they are testing, and ending with `Test`. For example, `HashTest` or `HashIntegrationTest`.


## 函数名

Function names are written in camelCase and are typically verbs or verb phrases. For example, `sendMessage` or `stop`.

Underscores are permitted to appear in test function names to separate logical components of the name.

```kotlin
@Test fun pop_emptyStack() {
    // …
}
```


## 常量名

Constant names use UPPER_SNAKE_CASE: all uppercase letters, with words separated by underscores. But what _is_ a constant, exactly?

Constants are `val` properties with no custom `get` function, whose contents are deeply immutable, and whose functions have no detectable side-effects. This includes immutable types and immutable collections of immutable types as well as scalars and string if marked as `const`. If any of an instance's observable state can change, it is not a constant. Merely intending to never mutate the object is not enough.

```kotlin
const val NUMBER = 5
val NAMES = listOf("Alice", "Bob")
val AGES = mapOf("Alice" to 35, "Bob" to 32)
val COMMA_JOINER = Joiner.on(',') // Joiner is immutable
val EMPTY_ARRAY = arrayOf<SomeMutableType>()
```

These names are typically nouns or noun phrases.

Constant values can only be defined inside of an `object` or as a top-level declaration. Values otherwise meeting the requirement of a constant but defined inside of a `class` must use a non-constant name.

Constants which are scalar values must use the [`const` modifier](http://kotlinlang.org/docs/reference/properties.html#compile-time-constants).


## 变量（非常量）名

Non-constant names are written in camelCase. These apply to instance properties, local properties, and parameter names.

```kotlin
val variable = "var"
val nonConstScalar = "non-const"
val mutableCollection: MutableSet<String> = HashSet()
val mutableElements = listOf(mutableInstance)
val mutableValues = mapOf("Alice" to mutableInstance, "Bob" to mutableInstance2)
val logger = Logger.getLogger(MyClass::class.java.name)
val nonEmptyArray = arrayOf("these", "can", "change")
```

These names are typically nouns or noun phrases.

### 返回值属性

When a [backing property](https://kotlinlang.org/docs/reference/properties.html#backing-properties) is needed, its name should exactly match that of the real property except prefixed with an underscore.

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

Each type variable is named in one of two styles:

 1. A single capital letter, optionally followed by a single numeral (such as `E`, `T`, `X`, `T2`)
 2. A name in the form used for classes, followed by the capital letter `T` (such as `RequestT`, `FooBarT`)


## 驼峰命名

Sometimes there is more than one reasonable way to convert an English phrase into camel case, such as when acronyms or unusual constructs like "IPv6" or "iOS" are present. To improve predictability, use the following scheme.

Beginning with the prose form of the name:

 1. Convert the phrase to plain ASCII and remove any apostrophes. For example, "Müller's algorithm" might become "Muellers algorithm".

 2. Divide this result into words, splitting on spaces and any remaining punctuation (typically hyphens).

    * _Recommended_: if any word already has a conventional camel-case appearance in common usage, split this into its constituent parts (e.g., "AdWords" becomes "ad words"). Note that a word such as "iOS" is not really in camel case _per se_; it defies _any_ convention, so this recommendation does not apply.

 3. Now lowercase everything (including acronyms), then uppercase only the first character of:

    * ...each word, to yield pascal case, or

    * ...each word except the first, to yield camel case

 4. Finally, join all the words into a single identifier.

Note that the casing of the original words is almost entirely disregarded.

| **Prose form**         | **Correct**                             | **Incorrect**       |
|------------------------|-----------------------------------------|---------------------|
| "XML Http Request"     | `XmlHttpRequest`                        | `XMLHTTPRequest`    |
| "new customer ID"      | `newCustomerId`                         | `newCustomerID`     |
| "inner stopwatch"      | `innerStopwatch`                        | `innerStopWatch`    |
| "supports IPv6 on iOS" | `supportsIpv6OnIos`                     | `supportsIPv6OnIOS` |
| "YouTube importer"     | `YouTubeImporter`<br>`YoutubeImporter`* |                     |

(_*Acceptable, but not recommended._)

**Note**: Some words are ambiguously hyphenated in the English language: for example "nonempty" and "non-empty" are both correct, so the method names `checkNonempty` and `checkNonEmpty` are likewise both correct.


# 文档


## 格式

The basic formatting of KDoc blocks is seen in this example:

```kotlin
/**
 * Multiple lines of KDoc text are written here,
 * wrapped normally…
 */
fun method(arg: String) {
    // …
}
```

...or in this single-line example:

```kotlin
/** An especially short bit of KDoc. */
```

The basic form is always acceptable. The single-line form may be substituted when the entirety of the KDoc block (including comment markers) can fit on a single line. Note that this only applies when there are no block tags such as `@return`.

### 段落

One blank line—that is, a line containing only the aligned leading asterisk (`*`)—appears between paragraphs, and before the group of block tags if present.

### 区块标签

Any of the standard "block tags" that are used appear in the order `@constructor`, `@receiver`, `@param`, `@property`, `@return`, `@throws`, `@see`, and these never appear with an empty description. When a block tag doesn't fit on a single line, continuation lines are indented 8 spaces from the position of the `@`.


## 概要片段

Each KDoc block begins with a brief summary fragment. This fragment is very important: it is the only part of the text that appears in certain contexts such as class and method indexes.

This is a fragment–a noun phrase or verb phrase, not a complete sentence. It does not begin with "``A `Foo` is a…``", or "`This method returns…`", nor does it have to form a complete imperative sentence like "`Save the record.`". However, the fragment is capitalized and punctuated as if it were a complete sentence.


## 用法

At the minimum, KDoc is present for every `public` type, and every `public` or `protected` member of such a type, with a few exceptions noted below.

### 例外: 无需解释的方法

KDoc is optional for "simple, obvious" functions like `getFoo` and properties like `foo`, in cases where there really and truly is nothing else worthwhile to say but "Returns the foo".

It is not appropriate to cite this exception to justify omitting relevant information that a typical reader might need to know. For example, for a function named `getCanonicalName` or property named `canonicalName`, don't omit its documentation (with the rationale that it would say only `/** Returns the canonical name. */`) if a typical reader may have no idea what the term "canonical name" means!

### 例外: 重载

KDoc is not always present on a method that overrides a supertype method.
