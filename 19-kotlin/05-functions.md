# Lesson 05: Functions

> Module: Kotlin · Lesson 5 of 7
> Estimated time: 30–45 minutes

In Lesson 04 you learned Kotlin's control flow — `if` as an expression, the powerful `when` block, `for` loops over ranges and collections, and labeled `break`/`continue`. In this lesson we go deep on functions, where Kotlin's modern design shines. Kotlin functions support **default arguments** (no need for overloading), **named arguments** (callers can specify arguments by name in any order), **vararg** (variadic arguments), and **single-expression syntax** for short functions. The killer feature, though, is **extension functions** — the ability to add methods to existing classes without modifying them, even to Java's `String` or third-party library classes.

You will also learn about higher-order functions (functions that take or return other functions), lambdas (anonymous function literals), and the implicit `it` parameter for single-argument lambdas. By the end you will be writing concise, expressive Kotlin code that takes full advantage of the language's functional features.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define functions with default arguments, named arguments, and `vararg` — and explain why Kotlin rarely needs function overloading.
2. Use single-expression function syntax (`fun double(x: Int) = x * 2`) for short functions.
3. Write extension functions to add methods to existing classes (including Java's `String` and standard-library types) without modifying them.
4. Use higher-order functions and lambdas (including the implicit `it` parameter) to write functional-style code.

---

## 1. Defining Functions

A Kotlin function declaration starts with `fun`, followed by the function name, a parameter list in parentheses, an optional return type after a colon, and a body in braces:

```kotlin
fun add(a: Int, b: Int): Int {
    return a + b
}

fun greet(name: String): String {
    return "Hello, $name!"
}

// Function returning no value (uses Unit, like Java's void)
fun printGreeting(name: String): Unit {
    println("Hello, $name!")
}

// Unit return type can be omitted
fun printBye(name: String) {
    println("Bye, $name!")
}

fun main() {
    println(add(2, 3))           // 5
    println(greet("Hieu"))       // Hello, Hieu!
    printGreeting("Kotlin")      // Hello, Kotlin!
    printBye("World")            // Bye, World!
}
```

- `fun add(a: Int, b: Int): Int { return a + b }` — full form. `fun` is the keyword that declares a function. Parameters use **Pascal-style** syntax: `name: Type` (NOT `Type name` like Java). Multiple parameters are separated by commas. The return type comes after a colon, BEFORE the body braces. The body uses `return` to return a value.
- `fun greet(name: String): String { return "Hello, $name!" }` — same form, returning a `String`.
- `fun printGreeting(name: String): Unit { ... }` — `Unit` is Kotlin's equivalent of Java's `void`. It means "no meaningful value". The body uses no `return` (or `return` with no value).
- `fun printBye(name: String) { ... }` — when the return type is `Unit`, you can OMIT the `: Unit` annotation. The compiler infers it.

### Parameter Type Syntax

The biggest syntax change for Java developers: parameter types come AFTER the name (Pascal-style), not before. Java: `int add(int a, int b)`. Kotlin: `fun add(a: Int, b: Int): Int`. This is the same convention used by Swift, Rust, and TypeScript (with type annotations). It also makes the parameter name and type visually distinct, which improves readability when types are long.

---

## 2. Single-Expression Functions

When a function's body is a single expression, Kotlin lets you omit the braces and use `=` instead. This is the concise form for short functions:

```kotlin
fun add(a: Int, b: Int): Int = a + b

// Return type can be inferred — no need to write Int
fun double(x: Int) = x * 2

fun isAdult(age: Int) = age >= 18

fun greet(name: String) = "Hello, $name!"

fun main() {
    println(add(2, 3))        // 5
    println(double(7))        // 14
    println(isAdult(25))      // true
    println(greet("Hieu"))   // Hello, Hieu!
}
```

- `fun add(a: Int, b: Int): Int = a + b` — the body is the expression after `=`. The function returns the value of `a + b`. No `return` keyword, no braces.
- `fun double(x: Int) = x * 2` — when the return type can be inferred (which is almost always), you can omit the explicit `: Int`. The compiler infers `Int` from the expression `x * 2`.
- `fun isAdult(age: Int) = age >= 18` — returns a `Boolean` because `>=` produces a `Boolean`.
- `fun greet(name: String) = "Hello, $name!"` — returns a `String`.

Single-expression functions are extremely common in idiomatic Kotlin. They make simple functions look almost like definitions in a math textbook: `fun double(x) = x * 2`. Use them whenever the body is one line. Use the block-body form (`{ ... }`) when you need multiple statements, local variables, or `when` expressions inside the body.

---

## 3. Default Arguments

Kotlin supports **default arguments** — parameter values that are used when the caller does not provide them. This eliminates the need for the function overloading that Java requires (writing three versions of `greet` with different parameter counts):

```kotlin
fun greet(name: String, greeting: String = "Hello", punctuation: String = "!"): String {
    return "$greeting, $name$punctuation"
}

fun main() {
    println(greet("Hieu"))                        // Hello, Hieu!
    println(greet("Hieu", "Hi"))                  // Hi, Hieu!
    println(greet("Hieu", "Hey", "?"))            // Hey, Hieu?
    println(greet("Hieu", punctuation = "!!!"))    // Hello, Hieu!!!
}
```

- `greeting: String = "Hello"` — the parameter `greeting` has a default value of `"Hello"`. If the caller does not provide `greeting`, the default is used.
- `punctuation: String = "!"` — same idea, default `"!"`.
- `greet("Hieu")` — uses all defaults. Equivalent to `greet("Hieu", "Hello", "!")`.
- `greet("Hieu", "Hi")` — overrides `greeting` with `"Hi"`, keeps `punctuation` default.
- `greet("Hieu", punctuation = "!!!")` — overrides only `punctuation`, keeps `greeting` default. This is the **named argument** syntax (Section 4).

In Java, you would need three separate functions to achieve this: `greet(String name)`, `greet(String name, String greeting)`, and `greet(String name, String greeting, String punctuation)`. Kotlin's default arguments collapse all three into one declaration. Default arguments must come at the end of the parameter list — you cannot have a non-default parameter after a default one.

---

## 4. Named Arguments

When calling a function, you can specify arguments by name instead of by position. This is especially useful for functions with many parameters or boolean flags:

```kotlin
fun sendEmail(
    to: String,
    subject: String = "No subject",
    body: String = "",
    cc: String? = null,
    isHtml: Boolean = false,
    priority: Int = 3
) {
    println("To: $to | Subject: $subject | HTML: $isHtml | Priority: $priority")
}

fun main() {
    // Positional — must remember the order
    sendEmail("alice@example.com", "Hello", "Hi Alice", null, false, 2)

    // Named — clearer, order does not matter for named args
    sendEmail(
        to = "alice@example.com",
        subject = "Hello",
        body = "Hi Alice",
        priority = 2
    )

    // Mix positional and named (positional must come first)
    sendEmail("alice@example.com", subject = "Hello", isHtml = true)
}
```

- `to = "alice@example.com"` — named argument. The argument's value is bound to the `to` parameter by name, not by position.
- Order does not matter for named arguments — you can specify them in any order (as long as positional arguments come first).
- `isHtml = true` — named arguments are particularly valuable for boolean flags, because `true` alone at the call site is meaningless (true for what?).
- Mixing positional and named arguments is allowed, but positional must come first. Once you start using named, all remaining arguments must also be named.

Named arguments dramatically improve code readability. Compare `sendEmail("alice@example.com", true, false, 3, "Hi", "Hello")` (which of those booleans means what?) with `sendEmail(to = "alice@example.com", isHtml = true, priority = 3, body = "Hi", subject = "Hello")`. The latter is self-documenting.

---

## 5. Vararg

Sometimes you want a function to accept any number of arguments — like `printf` in C or `String.format` in Java. Kotlin supports this with `vararg`:

```kotlin
fun sum(vararg nums: Int): Int {
    var total = 0
    for (n in nums) {
        total += n
    }
    return total
}

fun main() {
    println(sum(1, 2, 3))           // 6
    println(sum(10, 20, 30, 40))    // 100
    println(sum())                   // 0 — no args is fine

    // Passing an existing array with the spread operator *
    val arr = intArrayOf(5, 10, 15)
    println(sum(*arr))               // 30
}
```

- `vararg nums: Int` — declares a parameter that accepts zero or more `Int` arguments. Inside the function, `nums` is treated as an `IntArray` — you can iterate it, access by index, or call `.size`.
- `sum(1, 2, 3)` — pass any number of arguments; they are packed into the array.
- `sum()` — no arguments is valid; `nums` is an empty array.
- `sum(*arr)` — the **spread operator** `*` unpacks an existing array into individual arguments. Without `*`, you would be passing the array itself as a single argument (which would not match the `vararg Int` parameter type).

There can be at most one `vararg` parameter in a function. If other parameters follow it, they must be called with named arguments (because positional binding would be ambiguous).

---

## 6. Extension Functions — Kotlin's Killer Feature

Extension functions let you **add methods to existing classes without modifying them**. You can extend Kotlin's own classes (`String`, `Int`, `List`), Java's classes (`java.util.ArrayList`, `String` from Java), or any third-party library class. The extended function appears as if it were a native method on the class, but at the bytecode level it is a static utility function.

```kotlin
// Add a shout() method to String
fun String.shout(): String = this.uppercase() + "!!!"

// Add an isPositive method to Int
fun Int.isPositive(): Boolean = this > 0

// Add a method to List<Int>
fun List<Int>.sumDoubled(): Int = this.sumOf { it * 2 }

fun main() {
    println("hello".shout())           // HELLO!!!
    println(42.isPositive())           // true
    println((-5).isPositive())         // false
    println(listOf(1, 2, 3).sumDoubled())  // 12
}
```

- `fun String.shout(): String = this.uppercase() + "!!!"` — declares an extension function called `shout` on the `String` class. The class name and function name are separated by a dot. Inside the function, `this` refers to the receiver string (the value the function was called on).
- `"hello".shout()` — calling the extension function looks identical to calling a regular method on `String`. The compiler translates this to a static call `ShoutKt.shout("hello")` in the bytecode.
- `fun Int.isPositive(): Boolean = this > 0` — extending `Int`. Inside, `this` is the integer.
- `fun List<Int>.sumDoubled(): Int = ...` — you can extend generic types with specific type arguments. This extension only applies to `List<Int>`, not `List<String>`.
- Extensions are resolved **statically** at compile time — they do not actually modify the class. If a real member method exists with the same name, the member wins. This means extensions cannot override member functions, but they are perfect for adding utility helpers.

### Why Extensions Are Powerful

Consider the alternative: in Java you would write a `StringUtils` class with `static String shout(String s)` and call `StringUtils.shout("hello")`. In Kotlin you write `fun String.shout() = ...` and call `"hello".shout()` — the call site reads naturally, with the receiver first and the operation second, just like a real method. This is why Kotlin's standard library is so extensive — most "methods" on `String`, `Int`, `List`, etc. are actually extension functions defined in `kotlin-stdlib`.

A practical example: adding a `truncate` extension to `String`:

```kotlin
fun String.truncate(maxLength: Int): String =
    if (this.length <= maxLength) this else this.substring(0, maxLength - 1) + "…"

fun main() {
    println("Hello, World!".truncate(8))  // Hello, …
    println("Hi".truncate(8))              // Hi (no truncation needed)
}
```

Now every `String` in your project has a `.truncate(maxLength)` method you can call anywhere — no wrapper utility class needed.

---

## 7. Higher-Order Functions

A **higher-order function** is a function that takes another function as a parameter, or returns a function. Kotlin treats functions as first-class values — you can store them in variables, pass them as arguments, and return them from functions.

```kotlin
// Takes a function as a parameter
fun apply(f: (Int) -> Int, x: Int): Int = f(x)

// Takes two functions and returns one
fun compose(f: (Int) -> Int, g: (Int) -> Int): (Int) -> Int =
    { x -> f(g(x)) }

fun double(x: Int) = x * 2
fun inc(x: Int) = x + 1

fun main() {
    println(apply(::double, 5))          // 10 — :: is a function reference
    println(apply(::inc, 5))             // 6

    val doubleThenInc = compose(::inc, ::double)
    println(doubleThenInc(5))            // 11 — double(5) = 10, then inc(10) = 11
}
```

- `f: (Int) -> Int` — a parameter type meaning "a function that takes an `Int` and returns an `Int`". The arrow `->` separates parameter types from the return type.
- `apply(f: (Int) -> Int, x: Int): Int = f(x)` — `apply` takes a function `f` and an integer `x`, and calls `f(x)`.
- `::double` — a **function reference**. Converts a regular named function `double` into a value that can be passed around. Without `::`, you would be trying to call `double` (with no arguments) and pass the result.
- `compose(f, g) = { x -> f(g(x)) }` — returns a new lambda that composes `f` and `g`. The lambda is a value that can be stored in a variable and called later.

Higher-order functions are the foundation of Kotlin's functional style. The collections library is built around them — `map`, `filter`, `forEach`, `fold`, `groupBy`, `sortedBy` all take lambdas.

---

## 8. Lambdas

A **lambda** is an anonymous function literal — a function you write inline without giving it a name. Lambdas are how you pass small pieces of behavior to higher-order functions.

```kotlin
fun main() {
    // Full lambda syntax: { params -> body }
    val add = { a: Int, b: Int -> a + b }
    println(add(2, 3))  // 5

    // Lambda passed to a higher-order function
    val nums = listOf(1, 2, 3, 4, 5)
    val doubled = nums.map { it * 2 }
    println(doubled)  // [2, 4, 6, 8, 10]

    val evens = nums.filter { it % 2 == 0 }
    println(evens)  // [2, 4]

    nums.forEach { println(it) }

    // Multi-line lambda — last expression is the return value
    val sum = nums.fold(0) { acc, n ->
        println("adding $n to $acc")
        acc + n   // this is the return value of the lambda
    }
    println(sum)  // 15
}
```

- `{ a: Int, b: Int -> a + b }` — full lambda syntax. Parameters are listed before `->`, body comes after. The last expression in the body (`a + b`) is the return value. No `return` keyword.
- `nums.map { it * 2 }` — the `map` function applies a lambda to each element and returns a new list. The lambda `{ it * 2 }` doubles each element. Notice `it` is used instead of an explicit parameter name.
- `nums.filter { it % 2 == 0 }` — keeps only elements where the lambda returns true.
- `nums.forEach { println(it) }` — runs the lambda for each element, returns nothing.
- `nums.fold(0) { acc, n -> ... acc + n }` — folds the list starting from `0`, accumulating a sum. The lambda takes the accumulator and the current element, and the last expression is the new accumulator value.

### The Implicit `it` Parameter

When a lambda has exactly one parameter, Kotlin lets you omit the parameter declaration and use the implicit name `it`:

```kotlin
fun main() {
    val nums = listOf(1, 2, 3, 4, 5)

    // These two are equivalent
    val a = nums.map { x -> x * 2 }   // explicit parameter name
    val b = nums.map { it * 2 }        // implicit `it`

    println(a)  // [2, 4, 6, 8, 10]
    println(b)  // [2, 4, 6, 8, 10]
}
```

- `{ x -> x * 2 }` — explicit parameter named `x`.
- `{ it * 2 }` — implicit parameter named `it`. The compiler knows `map`'s lambda takes one `Int` parameter, so it lets you use `it` without declaring it. This is a convention adopted from Groovy and other languages.

`it` is extremely common in idiomatic Kotlin. Use it for short lambdas where the meaning is obvious (`nums.map { it * 2 }`). For longer lambdas or where the parameter has a meaningful name, use an explicit name (`employees.map { employee -> employee.fullName }`).

### Trailing Lambda Syntax

When the last parameter of a function is a function type, you can move the lambda outside the parentheses. If the lambda is the ONLY argument, you can omit the parentheses entirely:

```kotlin
fun main() {
    val nums = listOf(1, 2, 3, 4, 5)

    // These are all equivalent
    nums.fold(0, { acc, n -> acc + n })           // lambda inside parens
    nums.fold(0) { acc, n -> acc + n }             // trailing lambda (cleaner)

    // Single lambda argument — parens omitted entirely
    nums.forEach({ println(it) })                   // full form
    nums.forEach() { println(it) }                  // empty parens
    nums.forEach { println(it) }                    // parens omitted (idiomatic)
}
```

Trailing lambda syntax is one of Kotlin's most beloved features. It makes chains of operations like `list.map { ... }.filter { ... }.sortedBy { ... }` read naturally, almost like English.

---

## 9. `inline` Functions (Brief)

For performance-critical code, Kotlin lets you mark higher-order functions as `inline`. The compiler inlines the function body and the lambda body directly at the call site, eliminating the runtime overhead of creating a function object for the lambda:

```kotlin
inline fun repeat(times: Int, action: (Int) -> Unit) {
    for (i in 0 until times) {
        action(i)
    }
}

fun main() {
    repeat(3) {
        println("Iteration $it")
    }
}
```

- `inline fun repeat(...)` — the `inline` keyword tells the compiler to copy the body of `repeat` (and the lambda passed in) directly into the call site. At runtime, this is equivalent to writing the `for` loop directly in `main`, with no lambda allocation.
- The standard library's `repeat`, `with`, `let`, `run`, `apply`, `also`, `forEach`, `map`, `filter` and many other higher-order functions are marked `inline`. This is why Kotlin's functional style has no performance penalty compared to writing the equivalent `for` loops by hand.

You will rarely need to write `inline` yourself. Just know that the standard library functions are already inlined, so using `map`, `filter`, `forEach`, etc. has no overhead. Lesson 07 (cheatsheet) covers the scope functions (`let`, `run`, `with`, `apply`, `also`) in detail.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Default and Named Arguments

Write a function `fun makeCoffee(size: String = "medium", shots: Int = 1, decaf: Boolean = false, syrup: String? = null): String` that returns a string describing the coffee, like `"medium coffee, 1 shot, regular, no syrup"` or `"large coffee, 2 shots, decaf, vanilla syrup"`. Call it four different ways: with all defaults, with `size = "large"`, with `shots = 3, decaf = true`, and with `syrup = "caramel"`. Notice how named arguments let you skip middle parameters — something Java cannot do.

### Exercise 2: Write an Extension Function

Write an extension function `fun String.repeat(n: Int): String` that returns the string repeated `n` times. (Yes, the standard library already has one — write your own to practice.) For example, `"ab".repeat(3)` should return `"ababab"`. Hint: use a `StringBuilder` and a `for` loop, or use the `joinToString` function. Test with `"ha".repeat(5)` and `"x".repeat(0)`.

### Exercise 3: Higher-Order Function with Lambda

Write a higher-order function `fun List<Int>.customSum(transform: (Int) -> Int): Int` that sums the transformed values of each element. For example, `listOf(1, 2, 3).customSum { it * it }` should return `1 + 4 + 9 = 14`. Test it with three different lambdas: identity (`{ it }`), square (`{ it * it }`), and double-plus-one (`{ it * 2 + 1 }`). This is the same pattern as `sumOf` in the standard library.

---

## Common Mistakes

### Mistake 1: Forgetting the `fun` keyword

```kotlin
// WRONG — no fun keyword
add(a: Int, b: Int): Int {
    return a + b
}
```

```kotlin
// RIGHT — every function starts with fun
fun add(a: Int, b: Int): Int {
    return a + b
}
```

Every Kotlin function declaration starts with `fun`. Without it, the compiler stops with `expecting member declaration` or `expecting a top-level declaration`. The `fun` keyword is Kotlin's equivalent of Go's `func`, Rust's `fn`, Python's `def`, and JavaScript's `function`. If you forget it (a common mistake if you are switching back and forth between Kotlin and Java, where methods are declared without a keyword), just add `fun` and the error goes away.

### Mistake 2: Putting types before names (Java-style)

```kotlin
// WRONG — Java style, does not compile in Kotlin
fun add(Int a, Int b): Int {
    return a + b
}
```

```kotlin
// RIGHT — Kotlin style: name first, then type, separated by colon
fun add(a: Int, b: Int): Int {
    return a + b
}
```

Kotlin uses **Pascal-style** parameter syntax: `name: Type`. Java uses C-style: `Type name`. If you accidentally write Java-style parameters, the compiler stops with `expecting parameter name` or `name expected`. The fix is to swap the order — name first, then a colon, then the type. The same convention applies to variable declarations (`val x: Int = 5`, not `Int x = 5`) and to function return types (`fun f(): Int`, not `Int fun f()`). After a day or two, this becomes muscle memory.

### Mistake 3: Overloading instead of using defaults

```kotlin
// UNIDIOMATIC — three overloaded functions where one would do
fun greet(name: String): String = "Hello, $name!"
fun greet(name: String, greeting: String): String = "$greeting, $name!"
fun greet(name: String, greeting: String, punctuation: String): String = "$greeting, $name$punctuation"
```

```kotlin
// IDIOMATIC — single function with default arguments
fun greet(name: String, greeting: String = "Hello", punctuation: String = "!"): String =
    "$greeting, $name$punctuation"
```

If you are coming from Java, your instinct is to write overloaded functions for each parameter combination. In Kotlin, you should almost always use **default arguments** instead — one function declaration can serve all the use cases. Defaults make your API smaller, easier to maintain, and easier to extend (adding a new parameter with a default does not break existing callers). Reach for actual overloading only when the overloads have meaningfully different behavior, not just different parameter counts.

### Mistake 4: Using `it` when there are multiple parameters

```kotlin
// WRONG — `it` only works for single-argument lambdas
fun main() {
    val nums = listOf(1, 2, 3)
    val sum = nums.fold(0) {
        // COMPILE ERROR — fold's lambda takes two params (acc, n)
        // but `it` only refers to the first one
        it + it
    }
}
```

```kotlin
// RIGHT — use explicit parameter names for multi-arg lambdas
fun main() {
    val nums = listOf(1, 2, 3)
    val sum = nums.fold(0) { acc, n -> acc + n }
    println(sum)  // 6
}
```

The implicit `it` parameter only works when a lambda has exactly ONE parameter. For lambdas with two or more parameters (like `fold`'s `(acc, n) -> ...`), you must declare the parameter names explicitly. The compiler error you get when you try to use `it` in a multi-parameter lambda is confusing — it might say something like "type mismatch: inferred type is Int but (Int, Int) -> Int was expected". The fix is always to spell out the parameter names.

---

## Summary

- Functions are declared with `fun name(params): ReturnType { body }`. Parameter syntax is **Pascal-style**: `name: Type` (not Java's `Type name`).
- Single-expression functions: `fun double(x: Int) = x * 2`. The body is the expression after `=`; the return type can be inferred.
- **Default arguments**: `fun greet(name, greeting = "Hello") = ...`. Eliminates the need for overloading in most cases. Defaults must be at the end of the parameter list.
- **Named arguments**: `greet(name = "Hieu", greeting = "Hi")`. Order does not matter for named args; positional must come first. Excellent for booleans and skipping defaults.
- **Vararg**: `fun sum(vararg nums: Int)`. Inside, `nums` is an `IntArray`. Spread operator `*arr` unpacks an existing array into individual args.
- **Extension functions**: `fun String.shout() = this.uppercase() + "!!!"`. Adds methods to existing classes without modifying them. The standard library is full of extensions.
- **Higher-order functions** take or return functions. Function types look like `(Int) -> Int` (a function taking `Int` and returning `Int`). `::functionName` creates a function reference.
- **Lambdas**: `{ a, b -> a + b }`. Last expression is the return value. Single-arg lambdas can use `it` instead of naming the parameter. Trailing-lambda syntax lets you move the last lambda outside the parens.

You now have the full functional toolkit. In Lesson 06 we cover classes, properties, constructors, inheritance, interfaces, and the most-loved Kotlin feature: **data classes** and **sealed classes**.

---

**Next:** [Lesson 06: Classes and Data Classes →](./06-classes-and-data-classes.md)
