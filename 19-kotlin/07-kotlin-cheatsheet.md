# Lesson 07: Kotlin Cheatsheet

> Module: Kotlin · Lesson 7 of 7
> Estimated time: 30–45 minutes

This is the Kotlin reference page. It collects the syntax, idioms, and standard-library functions you have learned in the previous six lessons into one quick-reference document. Use it as a memory aid while you write Kotlin, and as a checklist of what to study next. The structure mirrors a typical Kotlin file from top to bottom — declarations, types, control flow, functions, classes, collections, scope functions — and ends with a brief preview of coroutines and a "common pitfalls" section that catches the bugs most beginners hit.

Unlike the previous lessons, this page is not a tutorial — it is a reference. Read it through once to refresh your memory, then bookmark it and come back as needed. The examples are intentionally minimal — each one shows the syntax without the long explanations from earlier lessons.

---

## Learning Objectives

After this lesson, you will be able to:

1. Look up any Kotlin syntax construct quickly — from `val`/`var` to `sealed class` to `companion object`.
2. Identify and avoid the most common Kotlin pitfalls (overuse of `!!`, `var` everywhere, confusing `==` and `===`).
3. Use the most important standard-library packages (`kotlin.collections`, `kotlin.text`, `kotlin.coroutines`, etc.) without having to look them up.
4. Know what comes next in your programming journey — the natural follow-up to Kotlin is Swift, which shares many of Kotlin's design ideas.

---

## 1. Hello, World! — Minimal Program

```kotlin
fun main() {
    println("Hello, World!")
}
```

- `fun main()` — entry point. No `args` parameter needed in modern Kotlin (1.3+).
- `println(...)` — top-level function, no `System.out.` prefix needed.

---

## 2. Commands

| Command | Purpose |
|---------|---------|
| `kotlin -version` | Check the Kotlin runtime version |
| `kotlinc hello.kt -include-runtime -d hello.jar` | Compile a `.kt` file into a self-contained `.jar` |
| `java -jar hello.jar` | Run a compiled Kotlin `.jar` (uses Java's `java` command) |
| `kotlinc hello.kt -d out/` | Compile to `.class` files in `out/` (no runtime bundled) |
| `kotlinc -script hello.kts` | Run a Kotlin script (`.kts`) without explicit compilation |
| `kotlinc` | Start the interactive Kotlin REPL |

---

## 3. `val` vs `var`

```kotlin
val x = 5       // read-only — assigned once, like Java's final
var y = 10      // mutable — can be reassigned
y = 20           // OK
// x = 10        // COMPILE ERROR — val cannot be reassigned

// Always prefer val; switch to var only if you must reassign.
```

| Keyword | Mutability | Java equivalent | Use when |
|---------|------------|-----------------|----------|
| `val` | Read-only (assigned once) | `final` | By default, almost always |
| `var` | Mutable (reassignable) | regular variable | You genuinely need to reassign |

---

## 4. Basic Types

| Type | Size | Range / Notes |
|------|------|--------------|
| `Byte` | 8 bits | -128 to 127 |
| `Short` | 16 bits | -32768 to 32767 |
| `Int` | 32 bits | -2^31 to 2^31-1 (default for integers) |
| `Long` | 64 bits | -2^63 to 2^63-1, suffix `L` |
| `Float` | 32 bits | IEEE 754 single, suffix `f` |
| `Double` | 64 bits | IEEE 754 double (default for floats) |
| `Char` | 16 bits | Single Unicode character, `'A'` |
| `Boolean` | 1 bit (JVM-dependent) | `true` or `false` |
| `String` | variable | Immutable sequence of `Char`, `"..."` or `"""..."""` |

All types are objects in the language, but compiled to JVM primitives when possible. There are no `unsigned` keywords — use `UByte`, `UInt`, `ULong`, `UShort` from the standard library for unsigned variants.

### Numeric Literals

```kotlin
val decimal = 42
val long = 42L
val float = 3.14f
val double = 3.14
val hex = 0xFF
val binary = 0b1010
val readable = 1_000_000
```

---

## 5. Null Safety

| Construct | Meaning | Example |
|-----------|---------|---------|
| `String` | Non-nullable — cannot hold null | `var a: String = "hi"` |
| `String?` | Nullable — can hold null | `var b: String? = null` |
| `?.` | Safe call — returns null if receiver is null | `name?.length` (returns `Int?`) |
| `?:` | Elvis — left if non-null, else right | `name ?: "Guest"` |
| `!!` | Not-null assertion — throws NPE if null | `name!!` (use sparingly!) |
| `as?` | Safe cast — returns null if cast fails | `x as? String` |
| `?.let { }` | Run block only if not null | `name?.let { println(it) }` |

```kotlin
fun greet(name: String?): String {
    val displayName = name ?: "Guest"  // default to Guest if null
    return "Hello, $displayName"
}

fun printLength(s: String?) {
    println(s?.length ?: 0)  // safe call + Elvis
}

fun process(s: String?) {
    val safe = s ?: return  // early exit if null
    println(safe.length)    // safe is now String (non-null)
}
```

---

## 6. Strings

```kotlin
val escaped = "Line 1\nLine 2\tTabbed"
val raw = """
    Multi-line
    string, no escapes needed.
    $variable works.
""".trimIndent()

// Templates
val name = "Hieu"
println("Hello, $name!")              // Hello, Hieu!
println("Length: ${name.length}")    // Length: 4

// Common methods
"hello".length            // 5
"hello".uppercase()       // HELLO
"hello".lowercase()       // hello
"hello"[1]                // 'e' — index access
"hello".substring(1, 3)   // "el"
"hello".replace("l", "L") // "heLLo"
"hello".split("l")        // ["he", "", "o"]
"  hi  ".trim()           // "hi"
"hello".startsWith("he")   // true
"hello".contains("ell")    // true
"hello".isEmpty()         // false
```

---

## 7. Control Flow

### `if` (Expression)

```kotlin
val max = if (a > b) a else b

// With blocks (last expression is the value)
val result = if (a > b) {
    println("a wins")
    a
} else {
    println("b wins")
    b
}
```

### `when` (Super Switch)

```kotlin
// With subject
fun describe(x: Any): String = when (x) {
    1          -> "one"
    2, 3       -> "two or three"
    in 4..10   -> "small"
    is String  -> "string of length ${x.length}"
    else       -> "unknown"
}

// Without subject — boolean branches
fun classify(n: Int) = when {
    n < 0      -> "negative"
    n == 0     -> "zero"
    n in 1..9  -> "small"
    else       -> "large"
}
```

### `for` Loops

```kotlin
for (i in 1..5) { ... }        // inclusive: 1, 2, 3, 4, 5
for (i in 1 until 5) { ... }  // exclusive: 1, 2, 3, 4
for (i in 1..10 step 2) { ... } // 1, 3, 5, 7, 9
for (i in 5 downTo 1) { ... }  // 5, 4, 3, 2, 1
for (item in list) { ... }
for ((index, item) in list.withIndex()) { ... }
```

### `while` and `do-while`

```kotlin
while (condition) { ... }      // checks first
do { ... } while (condition)   // checks after, runs at least once
```

### `break`, `continue`, Labels

```kotlin
outer@ for (i in 1..3) {
    for (j in 1..3) {
        if (i == 2 && j == 2) break@outer  // exits outer loop
        println("i=$i j=$j")
    }
}
```

### Ranges

```kotlin
val r = 1..10
println(5 in r)        // true
println(r.first)       // 1
println(r.last)        // 10
val stepped = 1..10 step 3
val descending = 10 downTo 1
```

---

## 8. Functions

### Declaration

```kotlin
// Full form
fun add(a: Int, b: Int): Int {
    return a + b
}

// Single-expression form (return type inferred)
fun double(x: Int) = x * 2

// Default arguments
fun greet(name: String, greeting: String = "Hello") = "$greeting, $name!"

// Named arguments (order does not matter)
greet(name = "Hieu", greeting = "Hi")
greet("Hieu", greeting = "Hi")

// Vararg
fun sum(vararg nums: Int): Int = nums.sum()
val arr = intArrayOf(1, 2, 3)
sum(*arr)  // spread operator

// Extension function
fun String.shout(): String = this.uppercase() + "!!!"
"hello".shout()  // HELLO!!!

// Higher-order function
fun apply(f: (Int) -> Int, x: Int): Int = f(x)

// Function reference
apply(::double, 5)
```

### Lambdas

```kotlin
val add = { a: Int, b: Int -> a + b }
add(2, 3)  // 5

// Single-arg lambda — use implicit `it`
nums.map { it * 2 }
nums.filter { it > 0 }
nums.forEach { println(it) }

// Trailing lambda syntax — last lambda outside parens
nums.fold(0) { acc, n -> acc + n }
nums.sortedBy { it.name }
```

---

## 9. Classes

### Primary Constructor and Properties

```kotlin
class Person(val name: String, var age: Int)
// `val name` — read-only property
// `var age` — mutable property

// Constructor with default args
class User(
    val name: String,
    val email: String = "",
    val age: Int = 0
)
```

### Class Body — Methods, Init, Custom Properties

```kotlin
class BankAccount(initial: Double) {
    var balance: Double = initial
        private set              // setter is private

    init {
        require(initial >= 0) { "Cannot start negative" }
    }

    fun deposit(amount: Double) {
        balance += amount
    }

    // Derived property
    val isOverdrawn: Boolean
        get() = balance < 0
}
```

### Secondary Constructor

```kotlin
class Person(val name: String) {
    var age: Int = 0

    constructor(name: String, age: Int) : this(name) {
        this.age = age
    }
}
```

### Inheritance

```kotlin
open class Animal(val name: String) {
    open fun speak(): String = "$name makes a sound"
}

class Dog(name: String) : Animal(name) {
    override fun speak(): String = "$name says Woof"
}
```

### Interfaces

```kotlin
interface Greeter {
    fun greet(): String                         // abstract
    fun politeGreet(): String = "Good day! ${greet()}"  // default
}

class Friendly : Greeter {
    override fun greet(): String = "Hi"
}
```

### Data Class

```kotlin
data class User(val name: String, val age: Int)

val u1 = User("Hieu", 25)
val u2 = User("Hieu", 25)
println(u1 == u2)        // true — auto-generated equals
println(u1)              // User(name=Hieu, age=25) — auto-generated toString
val older = u1.copy(age = 26)
val (name, age) = u1     // destructuring via componentN
```

### Sealed Class

```kotlin
sealed class Result {
    data class Success(val value: String) : Result()
    data class Error(val message: String) : Result()
    object Loading : Result()
}

fun handle(r: Result): String = when (r) {
    is Result.Success -> "Got: ${r.value}"
    is Result.Error    -> "Failed: ${r.message}"
    Result.Loading     -> "Loading..."
    // No else needed — exhaustive over sealed class
}
```

### Object (Singleton)

```kotlin
object Database {
    fun query(sql: String): List<String> = listOf("row1")
}

Database.query("SELECT *")  // accessed via class name
```

### Companion Object (Static-like)

```kotlin
class User(val name: String) {
    companion object {
        const val MAX_LENGTH = 100
        fun create(name: String) = User(name)
    }
}

println(User.MAX_LENGTH)         // via class name
val u = User.create("Hieu")      // factory method
```

---

## 10. Collections

```kotlin
// Read-only (immutable interface)
val list: List<Int> = listOf(1, 2, 3)
val set: Set<String> = setOf("a", "b")
val map: Map<String, Int> = mapOf("x" to 1, "y" to 2)

// Mutable
val mList = mutableListOf(1, 2, 3)
val mSet = mutableSetOf("a")
val mMap = mutableMapOf("x" to 1)

mList.add(4)
mList[0] = 10
mMap["z"] = 3
```

### Common Operations

```kotlin
val nums = listOf(1, 2, 3, 4, 5)

// Transformation
nums.map { it * 2 }              // [2, 4, 6, 8, 10]
nums.mapIndexed { i, v -> i + v } // [1, 3, 5, 7, 9]

// Filtering
nums.filter { it % 2 == 0 }       // [2, 4]
nums.filter { it > 2 }.map { it * 10 }  // [30, 40, 50]

// Aggregation
nums.sum()                        // 15
nums.sumOf { it * it }            // 55
nums.count()                      // 5
nums.count { it > 2 }            // 3
nums.minOrNull()                  // 1 (returns Int? because list might be empty)
nums.maxOrNull()                  // 5
nums.average()                    // 3.0
nums.fold(0) { acc, n -> acc + n } // 15
nums.reduce { acc, n -> acc + n }  // 15 (no initial value)

// Iteration
nums.forEach { println(it) }
nums.forEachIndexed { i, v -> println("$i: $v") }

// Grouping
val words = listOf("a", "bb", "ccc", "dd")
words.groupBy { it.length }       // {1=[a], 2=[bb, dd], 3=[ccc]}
words.partition { it.length > 1 } // ([bb, ccc, dd], [a])

// Sorting
nums.sorted()                     // [1, 2, 3, 4, 5]
nums.sortedDescending()          // [5, 4, 3, 2, 1]
nums.sortedBy { -it }             // [5, 4, 3, 2, 1]
nums.reversed()                   // [5, 4, 3, 2, 1]

// Finding
nums.find { it > 3 }              // 4 (returns Int?)
nums.firstOrNull { it > 3 }       // 4
nums.any { it > 4 }               // true
nums.all { it > 0 }               // true
nums.none { it < 0 }              // true
nums.contains(3)                  // true (or `3 in nums`)

// Conversion
nums.toSet()                      // {1, 2, 3, 4, 5}
nums.toList()                    // [1, 2, 3, 4, 5]
nums.joinToString(", ")          // "1, 2, 3, 4, 5"
nums.chunked(2)                  // [[1, 2], [3, 4], [5]]
```

---

## 11. Scope Functions

Kotlin has five scope functions — `let`, `run`, `with`, `apply`, `also` — for working with objects in a scoped block. They differ in how they refer to the object (as `it` or `this`) and what they return (the object or the lambda result).

| Function | Receiver | Returns | Common use |
|----------|----------|---------|-----------|
| `let` | `it` | Lambda result | Null checks, transformations |
| `run` | `this` | Lambda result | Computed initialization |
| `with` | `this` | Lambda result | Operating on the same object repeatedly |
| `apply` | `this` | The object | Builder-style configuration |
| `also` | `it` | The object | Side effects (logging, debugging) |

```kotlin
// let — null check pattern
name?.let {
    println("Name length: ${it.length}")
}

// apply — builder-style initialization
val user = User("", 0).apply {
    name = "Hieu"
    age = 25
}

// also — side effects
val result = compute().also { println("Got: $it") }

// run — computed scope
val greeting = "Hieu".run {
    println("Processing $this")
    "Hello, $this!"
}

// with — operate on the same object (no receiver chain)
val sb = StringBuilder()
with(sb) {
    append("Hello")
    append(", ")
    append("World")
}
println(sb.toString())
```

---

## 12. Coroutines (Brief Preview)

Coroutines are Kotlin's approach to asynchronous programming. They let you write async code that looks sequential — no callback hell, no `CompletableFuture.thenCompose` chains. Coroutines are part of the `kotlinx.coroutines` library (not the standard library itself). Here is a small preview:

```kotlin
import kotlinx.coroutines.*

// A suspend function can be paused and resumed
suspend fun fetchUser(id: Int): String {
    delay(1000)  // suspends for 1 second without blocking the thread
    return "User-$id"
}

fun main() = runBlocking {
    // launch — fire-and-forget coroutine
    launch {
        delay(500)
        println("First done")
    }

    // async — coroutine that returns a value
    val deferred = async { fetchUser(1) }
    val user = deferred.await()  // suspends until ready
    println(user)
}
// Output (after ~1 second):
// First done
// User-1
```

- `suspend fun` — a function that can be paused and resumed. Inside a suspend function you can call other suspend functions (like `delay`).
- `runBlocking { ... }` — starts a coroutine and blocks the current thread until it finishes. Used at the top level (like `main`).
- `launch { ... }` — starts a fire-and-forget coroutine. Returns a `Job` you can cancel or wait for.
- `async { ... }` — starts a coroutine that returns a value. Returns a `Deferred<T>`. Call `.await()` to get the result.
- `delay(ms)` — suspends the coroutine for the given milliseconds. Does NOT block the underlying thread, so other coroutines can run.

Coroutines are a large topic — a deep dive is beyond this 7-lesson module. The natural next steps after this module are: (1) the official Kotlin Coroutines Guide, (2) building a small Android app or a backend with Ktor, and (3) learning about flows (`Flow<T>`) for cold streams of values.

---

## 13. Common Idioms

```kotlin
// Null-safe default
val name = input ?: "Anonymous"

// Run if not null
user?.let { sendEmail(it.email) }

// Early return on null
val data = loadData() ?: return

// Exhaustive when with sealed class
fun handle(state: State) = when (state) {
    State.Loading       -> showSpinner()
    is State.Success   -> showData(state.data)
    is State.Error     -> showError(state.message)
}

// Data class for DTOs
data class UserDto(val id: Int, val name: String)

// apply for builder-style configuration
val config = Config().apply {
    host = "localhost"
    port = 8080
    timeout = 5000
}

// if-null-throw
val user = repository.find(id) ?: throw NotFoundException("User $id")

// Lazy property
val cachedValue: String by lazy {
    println("Computing...")
    "computed"
}

// Map/filter chain
val adults = people
    .filter { it.age >= 18 }
    .sortedBy { it.name }
    .map { it.name }
```

---

## 14. Common Pitfalls

| Pitfall | What happens | Fix |
|---------|--------------|-----|
| Using `!!` everywhere | Throws `NullPointerException` if the value is null at runtime | Use `?.` or `?:` to handle null explicitly |
| Using `var` everywhere | Code is harder to reason about; mutable state is a source of bugs | Default to `val`; switch to `var` only if you must reassign |
| `==` for arrays | Returns `false` because arrays use reference equality | Use `.contentEquals()` or prefer `List<T>` |
| Confusing `Int` and `Int?` | Cannot pass `Int?` where `Int` is required | Use `?:` or `?.` to convert nullable to non-null at the boundary |
| Forgetting `override` keyword | Compiler error: "hides member of supertype" | Add the `override` keyword explicitly |
| Trying to inherit from a non-`open` class | Compiler error: "This type is final" | Add `open` to the parent class declaration |
| Using `switch` (Java habit) | Compiler error — Kotlin has no `switch` | Use `when` instead |
| Off-by-one with `..` vs `until` | `0..5` has 6 iterations (inclusive) | Use `0 until 5` for exclusive upper bound |
| `forEach { break }` | Compile error — `break` only works in real loops | Use a `for` loop instead |
| Forgetting `fun` keyword | Compiler error: "expecting member declaration" | Add `fun` before the function name |
| Java-style parameters `Type name` | Compiler error: "expecting parameter name" | Use Pascal-style: `name: Type` |
| Using `System.out.println` | Compiles but is unidiomatic | Use top-level `println(...)` |
| Single quotes for strings | Compiler error: "too many characters in character literal" | Use double quotes `"..."` for `String` |
| Forgetting `$` in string templates | Prints literally `Hello, name!` | Use `Hello, $name!` |

---

## 15. The 10 Most-Used Standard Library Packages

| Package | What it contains | Example use |
|---------|------------------|-------------|
| `kotlin.collections` | `List`, `Set`, `Map`, `MutableList`, plus `map`/`filter`/`forEach`/`fold` extensions | `listOf(1,2,3).map { it * 2 }` |
| `kotlin.text` | String functions: `split`, `replace`, `trim`, `substring`, `uppercase` | `"hi".uppercase()` |
| `kotlin.io` | `println`, `print`, `readln`, file convenience | `println("hi")` |
| `kotlin.coroutines` | Basic coroutine intrinsics (most users import `kotlinx.coroutines`) | `suspend fun` |
| `kotlin.sequences` | `Sequence<T>` — lazy streams, like Java's `Stream` | `nums.asSequence().map { }.filter { }.toList()` |
| `kotlin.ranges` | `IntRange`, `LongRange`, `until`, `downTo`, `step` | `for (i in 1..10 step 2)` |
| `kotlin.properties` | Property delegates (`by lazy`, `by observable`, `by map`) | `val x: Int by lazy { compute() }` |
| `kotlin.reflect` | Reflection APIs (full KClass reflection, needs `kotlin-reflect` artifact) | `User::class.memberProperties` |
| `kotlin.math` | Math functions: `abs`, `min`, `max`, `sqrt`, `pow`, `round` | `kotlin.math.sqrt(2.0)` |
| `kotlin.time` | `Duration`, `measureTime`, `measureTimedValue`, `DurationUnit` | `val t = measureTime { work() }` |

---

## 16. What's Next?

You have completed the Kotlin module. You can now read and write idiomatic Kotlin — concise `data class` declarations, null-safe code, expressive `when` expressions, extension functions, sealed classes, and the functional collection pipeline (`map`/`filter`/`fold`). You understand the JVM toolchain, how Kotlin relates to Java, and the basics of coroutines. With this foundation, the natural next steps are:

1. **Build something.** Pick a small project — a TODO app, a CLI tool, a small web server with [Ktor](https://ktor.io) — and write it in Kotlin. Real code beats tutorial code by 10×.
2. **Learn Android development.** Android is Kotlin's biggest use case. The official Android developers' site (developer.android.com) has free, high-quality Kotlin-first tutorials. The Android Jetpack libraries (ViewModel, Room, Navigation) are written in Kotlin and idiomatic to use.
3. **Dive into coroutines.** Once you have written a few small Kotlin programs, learn coroutines in depth. Start with the [official Coroutines Guide](https://kotlinlang.org/docs/coroutines-guide.html) and then practice with `Flow<T>` for reactive streams.
4. **Try Kotlin Multiplatform.** If you want to share business logic between Android and iOS (or web), Kotlin Multiplatform is one of the most exciting things happening in cross-platform development today. The official KMP tutorial walks through building a small cross-platform app.

The next module in this curriculum is **Swift** — Apple's modern language for iOS, macOS, watchOS, and tvOS development. Swift shares a surprising amount of design DNA with Kotlin: optionals (Kotlin's `?` is Swift's `Optional`), `if let` (Kotlin's `?.let`), `guard` (Kotlin's `?: return`), value types (`struct`), and protocol-oriented programming. If you enjoyed Kotlin's null safety and modern syntax, Swift will feel familiar — and learning Swift opens the door to the entire Apple ecosystem.

Open the next module at:

**→ [/home/z/my-project/learn-dev/20-swift/](/home/z/my-project/learn-dev/20-swift/)**

Happy coding, and welcome to the Kotlin community.
