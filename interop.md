---
layout: page
title: 互通指导
site_nav_category: interop
is_site_nav_category: true
site_nav_category_order: 300
---

一套用于编写 Java 与 Kotlin 间使用公共 API 的规则，目的是让代码在其它语言也能使用到感到习惯的用法。

_<a href="changelog.html">Last update: {{ site.changes.last.date | date: "%Y-%m-%d" }}</a>_


# Java (为 Kotlin 使用时)

## 没有硬性关键字

不要使用 Kotlin 的 [硬性关键字（Hard keywords）](https://kotlinlang.org/docs/reference/keyword-reference.html#hard-keywords) 作为方法或者成员的名字。这些会令 Kotlin 在调用时要求使用反引号。[非硬性关键字（Soft keywords）](https://kotlinlang.org/docs/reference/keyword-reference.html#soft-keywords)、[修饰符关键字](https://kotlinlang.org/docs/reference/keyword-reference.html#modifier-keywords) 和 [特殊标识符](https://kotlinlang.org/docs/reference/keyword-reference.html#special-identifiers) 则允许。

例如，Mockito 的 `when` 方法当在 Kotlin 调用时需要反引号：

```kotlin
val callable = Mockito.mock(Callable::class.java)
Mockito.`when`(callable.call()).thenReturn(/* … */)
```


## Lambda 参数放最后

适用于 [SAM 转换](https://kotlinlang.org/docs/reference/java-interop.html#sam-conversions) 的参数类型应该放到最后。

例如，RxJava 2 的 `Flowable.create()` 方法签名被定义为：

```java
public static <T> Flowable<T> create(
    FlowableOnSubscribe<T> source,
    BackpressureStrategy mode) { /* … */ }
```

因为 `FlowableOnSubscribe` 适用于 SAM 转换，Kotlin 中调用这个方法看起来像这样：

```kotlin
Flowable.create({ /* … */ }, BackpressureStrategy.LATEST)
```

如果方法签名中的参数被颠倒过来，函数调用可以使用尾部 Lambda 语法：

```kotlin
Flowable.create(BackpressureStrategy.LATEST) { /* … */ }
```


## 属性前缀

对于在 Kotlin 中要表示为属性的方法，必须使用严格的 Bean 风格前缀。

属性访问方法需要一个 `get` 前缀，返回类型为 `boolean` 的方法可以使用 `is` 作为前缀。

```java
public final class User {
  public String getName() { /* … */ }
  public boolean isActive() { /* … */ }
}
```
```kotlin
val name = user.name // Invokes user.getName()
val active = user.active // Invokes user.isActive()
```

对应的改值方法需要 `set` 前缀。

```java
public final class User {
  public String getName() { /* … */ }
  public void setName(String name) { /* … */ }
}
```
```kotlin
user.name = "Bob" // 会调用 user.setName(String)
```

如果你想让方法公开为属性，不要使用不标准的前缀，如 `has`/`set` 或者非 `get` 前缀的访问方法。带非标准前缀的方法仍然可以调用，能否接受取决于它的行为。


## 操作符重载

注意在 Kotlin 语法中允许“特殊语法”调用的方法名 (即 [操作符重载](https://kotlinlang.org/docs/reference/operator-overloading.html))。保证方法名和缩短的语法一起使用是有意义的。

```java
public final class IntBox {
  private final int value;
  public IntBox(int value) {
    this.value = value;
  }
  public IntBox plus(IntBox other) {
    return new IntBox(value + other.value);
  }
}
```
```kotlin
val one = IntBox(1)
val two = IntBox(2)
val three = one + two // 会调用 one.plus(two)
```


## 可空性注释

在公开的 API 里所有的非原生类型参数、返回值和字段类型都应该有可空性注释。未标注的类型会被转译为带含糊的可空性的 [“平台”类型](https://kotlinlang.org/docs/reference/java-interop.html#null-safety-and-platform-types)。

JSR 305 包注释可以用来设定合理的默认值但目前不鼓励。他们要求编译器承认 Opt-in 标记且与 Java 9 的模块系统冲突。


# Kotlin (为 Java 使用时)

## 文件名

当一个文件包含顶级方法或属性时，*始终* 要用 `@file:JvmName("Foo")` 来标注它以提供一个好听的名字。

在默认情况，一个文件 `Foo.kt` 中的顶级成员最终会到一个叫 `FooKt` 的类里，这很没有吸引力同时也泄露了语言作为实现细节。

考虑添加 `@file:JvmMultifileClass` 注释将多个文件中的顶级成员合并为一个类。


## Lambda 参数

被 Java 使用的 [方法类型](https://kotlinlang.org/docs/reference/lambdas.html#function-types) 应该避免返回 `Unit`。这么做的话就要指定一条明确的表达式： `return Unit.INSTANCE;` 。

```kotlin
fun sayHi(callback: (String) -> Unit) = /* … */
```
```kotlin
// Kotlin 调用者:
greeter.sayHi { Log.d("打招呼", "你好，$it！") }
```
```java
// Java 调用者:
greeter.sayHi(name -> {
    Log.d("打招呼", "你好，" + name + "！");
    return Unit.INSTANCE;
});
```

这个语法也不允许提供一个语义命名的类型，以便能在其它类型上实现。

在 Kotlin 中为 Lambda 类型定义命名一个单抽象（SAM）方法可以纠正 Java 的问题，但会阻止 Kotlin 中使用 Lambda 语法。

```kotlin
interface GreeterCallback {
    fun greetName(name: String): Unit
}

fun sayHi(callback: GreeterCallback) = /* … */
```
```kotlin
// Kotlin 调用者:
greeter.sayHi(object : GreeterCallback {
    override fun greetName(name: String) {
        Log.d("打招呼", "你好，$name！")
    }
})
```
```java
// Java 调用者:
greeter.sayHi(name -> Log.d("打招呼", "你好，" + name + "！"))
```

在 Java 中定义命名一个单抽象（SAM）接口允许 Kotlin 使用较低级的 Lambda 语法，其中接口类型必须明确指定。

```java
// 在 Java 中定义:
interface GreeterCallback {
    void greetName(String name);
}
```
```kotlin
fun sayHi(greeter: GreeterCallback) = /* … */
```
```kotlin
// Kotlin 调用者:
greeter.sayHi(GreeterCallback { Log.d("打招呼", "你好，$it！") })
```
```java
// Java 调用者:
greeter.sayHi(name -> Log.d("打招呼", "你好，" + name + "！"));
```

目前还没有办法在 Java 和 Kotlin 中定义一个参数类型作为 Lambda，这可以感受到两种语言的习惯。目前的建议是选用 [方法类型](https://kotlinlang.org/docs/reference/lambdas.html#function-types)，尽管返回类型是 `Unit` 时 Java 上体验不佳。

_注明: 这个建议未来可能会改变，请看 [KT-7770](https://youtrack.jetbrains.com/issue/KT-7770) 和 [KT-21018](https://youtrack.jetbrains.com/issue/KT-21018)。_


## 避免 `Nothing` 泛型

A type whose generic parameter is `Nothing` is exposed as raw types to Java. Raw types are rarely used in Java and should be avoided.


## 记载 Exceptions

Functions which can throw checked exceptions should document them with `@Throws`. Runtime exceptions should be documented in KDoc.

Be mindful of the APIs a function delegates to as they may throw checked exceptions which Kotlin otherwise silently allows to propagate.


## 预防性复制副本

When returning shared or unowned read-only collections from public APIs, wrap them in an unmodifiable container or perform a defensive copy. Despite Kotlin enforcing their read-only property, there is no such enforcement on the Java side. Without the wrapper or defensive copy, invariants can be violated by returning a long-lived collection reference.


## 伴随方法

Public functions in a `companion object` must be annotated with `@JvmStatic` to be exposed as a static method.

Without the annotation, these functions are only available as instance methods on a static `Companion` field.

_Incorrect: no annotation_

```kotlin
class KotlinClass {
    companion object {
        fun doWork() {
            /* … */
        }
    }
}
```
```java
public final class JavaClass {
    public static void main(String... args) {
        KotlinClass.Companion.doWork();
    }
}
```

_Correct: `@JvmStatic` annotation_


```kotlin
class KotlinClass {
    companion object {
        @JvmStatic fun doWork() {
            /* … */
        }
    }
}
```
```java
public final class JavaClass {
    public static void main(String... args) {
        KotlinClass.doWork();
    }
}
```


## 伴随常量

Public, non-`const` properties which are [_effective constants_](TODO) in a `companion object` must be annotated with `@JvmField` to be exposed as a static field.

Without the annotation, these properties are only available as oddly-named instance 'getters' on the static `Companion` field. Using `@JvmStatic` instead of `@JvmField` moves the oddly-named 'getters' to static methods on the class which is still incorrect.

_Incorrect: no annotation_

```kotlin
class KotlinClass {
    companion object {
        const val INTEGER_ONE = 1
        val BIG_INTEGER_ONE = BigInteger.ONE
    }
}
```
```java
public final class JavaClass {
    public static void main(String... args) {
        System.out.println(KotlinClass.INTEGER_ONE);
        System.out.println(KotlinClass.Companion.getBIG_INTEGER_ONE());
    }
}
```

_Incorrect: `@JvmStatic` annotation_

```kotlin
class KotlinClass {
    companion object {
        const val INTEGER_ONE = 1
        @JvmStatic val BIG_INTEGER_ONE = BigInteger.ONE
    }
}
```
```java
public final class JavaClass {
    public static void main(String... args) {
        System.out.println(KotlinClass.INTEGER_ONE);
        System.out.println(KotlinClass.getBIG_INTEGER_ONE());
    }
}
```

_Correct: `@JvmField` annotation_

```kotlin
class KotlinClass {
    companion object {
        const val INTEGER_ONE = 1
        @JvmField val BIG_INTEGER_ONE = BigInteger.ONE
    }
}
```
```java
public final class JavaClass {
    public static void main(String... args) {
        System.out.println(KotlinClass.INTEGER_ONE);
        System.out.println(KotlinClass.BIG_INTEGER_ONE);
    }
}
```

## 习惯命名

Kotlin has different calling conventions than Java which can change the way you name functions. Use `@JvmName` to design names such that they'll feel idiomatic for both language's conventions or to match their respective standard library naming.

This most frequently occurs for extension functions and extension properties because the location of the receiver type is different.

```kotlin
sealed class Optional<T : Any>
data class Some<T : Any>(val value: T): Optional<T>()
object None : Optional<Nothing>()

@JvmName("ofNullable")
fun <T> T?.asOptional() = if (this == null) None else Some(this)
```
```kotlin
// FROM KOTLIN:
fun main(vararg args: String) {
    val nullableString: String? = "foo"
    val optionalString = nullableString.asOptional()
}
```
```java
// FROM JAVA:
public static void main(String... args) {
    String nullableString = "Foo";
    Optional<String> optionalString =
          Optionals.ofNullable(nullableString);
}
```

## 默认参数的方法重载

Functions with parameters having a default value must use `@JvmOverloads`. Without this annotation it is impossible to invoke the function using any default values.

When using `@JvmOverloads`, inspect the generated methods to ensure they each make sense. If they do not, perform one or both of the following refactorings until satisfied:

 1. Change the parameter order to prefer those with defaults being towards the end.
 2. Move the defaults into manual function overloads.

_Incorrect: No `@JvmOverloads`_

```kotlin
class Greeting {
    fun sayHello(prefix: String = "Mr.", name: String) {
        println("Hello, $prefix $name")
    }
}
```
```java
public class JavaClass {
    public static void main(String... args) {
        Greeting greeting = new Greeting();
        greeting.sayHello("Mr.", "Bob");
    }
}
```

_Correct: `@JvmOverloads` annotation._

```kotlin
class Greeting {
    @JvmOverloads
    fun sayHello(prefix: String = "Mr.", name: String) {
        println("Hello, $prefix $name")
    }
}
```
```java
public class JavaClass {
    public static void main(String... args) {
        Greeting greeting = new Greeting();
        greeting.sayHello("Bob");
    }
}
```
