# Lesson 03: Variables and Types

> Module: Kotlin · Lesson 3 of 7
> Estimated time: 30–45 minutes

In Lesson 02 you wrote your first Kotlin program, learned how to compile and run it, and saw how string templates make printing values easy. In this lesson we go deep on the most important topic in Kotlin: variables, types, and the headline feature that makes Kotlin famous — **null safety**. Kotlin's type system distinguishes nullable types from non-nullable types at the language level, and the compiler refuses to compile code that might dereference a null. This single feature eliminates the entire category of `NullPointerException` crashes that plague Java codebases, and once you have used it for a week, you will wonder how you ever wrote code without it.

You will also learn the critical difference between `val` (read-only) and `var` (mutable), how Kotlin's basic types compare to Java's, and how to write clean, idiomatic code that takes advantage of safe calls (`?.`), the Elvis operator (`?:`), and the not-null assertion (`!!`). By the end of this lesson, you will understand why Kotlin developers say their code "just works" — because the compiler caught the bugs before they could ever run.

---

## Learning Objectives

After this lesson, you will be able to:

1. Choose between `val` (read-only) and `var` (mutable) and explain why `val` is the default in idiomatic Kotlin code.
2. Use type inference (`val x = 5` infers `Int`) and explicit type annotations (`val x: Long = 5`) appropriately.
3. Distinguish nullable types (`String?`) from non-nullable types (`String`) and explain why Kotlin's null-safety feature eliminates NullPointerException at compile time.
4. Use safe calls (`?.`), the Elvis operator (`?:`), safe casts (`as?`), and the not-null assertion (`!!`) correctly to handle nullable values without crashing.

---

## 1. `val` vs `var` — The Key Kotlin Distinction

Kotlin has two keywords for declaring variables: `val` and `var`. They look similar but behave very differently, and choosing the right one is the first big decision every Kotlin programmer makes.

```kotlin
fun main() {
    val name = "Hieu"   // val — read-only, assigned once
    var age = 25        // var — mutable, can be reassigned

    age = 26            // OK — var can be reassigned
    // name = "Bob"    // COMPILE ERROR — val cannot be reassigned

    println("$name is $age")
}
```

- `val` — short for "value". Declares a **read-only** variable. You can assign it exactly once (at declaration, or later if you leave it uninitialized and assign before first read). After that, the compiler refuses any reassignment. `val` is Kotlin's equivalent of Java's `final` and JavaScript's `const`.
- `var` — short for "variable". Declares a **mutable** variable. You can reassign it as many times as you want. `var` is Kotlin's equivalent of a normal Java variable (without `final`).

The single most important habit to develop in Kotlin is **defaulting to `val`**. Use `val` everywhere unless you genuinely need to reassign the variable later — and most local variables do not need to be reassigned. This is the opposite of Java's default (which is mutable), and it takes a few days to get used to. The reason is simple: read-only variables are easier to reason about. If you see `val total = computeTotal()`, you know `total` will never change for the rest of its scope — no function call later in the function can mutate it, no threading bug can overwrite it. `val` is the foundation of safer, more predictable code.

One subtle point: `val` is read-only, not truly immutable. If `val` holds a mutable object (like a `MutableList`), the object's contents can still change — `val` only locks the reference, not the data. Use immutable collections (`List`, `Set`, `Map` instead of `MutableList`, `MutableSet`, `MutableMap`) when you want truly immutable data. We cover collections in Lesson 07.

```kotlin
fun main() {
    val list = mutableListOf(1, 2, 3)  // val reference, but mutable content
    list.add(4)                         // OK — mutating the list, not the reference
    println(list)                       // [1, 2, 3, 4]

    // list = mutableListOf(5, 6)        // COMPILE ERROR — can't reassign val
}
```

---

## 2. Type Inference

Kotlin is statically typed — every variable has a type known at compile time — but you rarely need to write the type. The compiler infers it from the value:

```kotlin
fun main() {
    val name = "Hieu"     // String
    val age = 25          // Int
    val pi = 3.14         // Double
    val flag = true       // Boolean
    val letter = 'A'       // Char
    val items = listOf(1, 2, 3)  // List<Int>

    // You can be explicit when you want
    val count: Long = 1_000_000
    val ratio: Float = 0.5f
    val empty: String? = null
}
```

- `val name = "Hieu"` — the compiler sees a string literal, infers `String`, and gives `name` the type `String`. You never need to write `: String` here.
- `val age = 25` — integer literal, infers `Int` (Kotlin's default integer type).
- `val pi = 3.14` — floating-point literal, infers `Double` (Kotlin's default floating-point type).
- `val items = listOf(1, 2, 3)` — the `listOf` function returns `List<Int>` because the elements are `Int`. Kotlin can also infer generic type arguments.
- `val count: Long = 1_000_000` — explicit type annotation. Use this when you want a specific type (here, `Long` instead of the inferred `Int`), or when you declare without initialization (`val x: Int`).
- Underscores in numeric literals (`1_000_000`) are a readability feature — they are ignored by the compiler but make large numbers easier to read.

---

## 3. Basic Types

Kotlin has a fixed set of built-in types. Unlike Java, which has both primitive types (`int`, `double`, `boolean`) and wrapper classes (`Integer`, `Double`, `Boolean`), Kotlin treats everything as an object in the language — but the compiler is smart enough to compile to JVM primitives when possible for performance.

```text
   Kotlin types                  Java equivalent
   ─────────────────────────    ──────────────────
   Byte       (8-bit signed)    byte / java.lang.Byte
   Short     (16-bit signed)    short / java.lang.Short
   Int       (32-bit signed)    int / java.lang.Integer
   Long      (64-bit signed)    long / java.lang.Long
   Float     (32-bit float)     float / java.lang.Float
   Double    (64-bit float)     double / java.lang.Double
   Char     (16-bit Unicode)    char / java.lang.Character
   Boolean    (true / false)    boolean / java.lang.Boolean
   String   (immutable chars)   java.lang.String
```

The numeric types are all signed (they can be positive or negative). Their sizes match Java's: `Byte` is 8 bits, `Short` is 16, `Int` is 32, `Long` is 64, `Float` is 32, `Double` is 64. There are no `unsigned` keywords in the language, but the standard library provides unsigned counterparts (`UByte`, `UInt`, `ULong`, `UShort`) for the rare cases you need them. The `Char` type holds a single 16-bit Unicode character (Kotlin uses UTF-16 internally, like Java).

Every type in Kotlin is a class with methods. You can call methods on `Int`s, `Boolean`s, and even `Char`s as if they were objects:

```kotlin
fun main() {
    val n = 42
    println(n.toString())        // "42" — Int has toString()
    println(n.toLong())          // 42L — convert to Long
    println(n.coerceIn(0, 100))  // 42 — clamp to range

    val s = "Hello"
    println(s.length)            // 5
    println(s.uppercase())       // "HELLO"

    val c = 'A'
    println(c.code)              // 65 — the Unicode code point
    println(c.lowercaseChar())   // 'a'
}
```

- `n.toString()` — even an `Int` has methods. The Kotlin compiler optimizes this to use Java primitives under the hood where possible, so there is no performance penalty.
- `n.toLong()` — Kotlin provides explicit conversion functions between numeric types. There is no implicit widening like Java's `int` → `long` — you must call `.toLong()` explicitly. This avoids subtle bugs.
- `s.length` — `String` is immutable. `.length` is a property, not a method call (no parentheses).
- `c.code` — gives the Unicode code point of a `Char` as an `Int`. `'A'.code` is `65`.

---

## 4. Numbers — Literals, Suffixes, and Underscores

Numeric literals in Kotlin support several suffixes and forms:

```kotlin
fun main() {
    val decimal = 42            // Int
    val long = 42L              // Long — suffix 'L' or 'l'
    val float = 3.14f           // Float — suffix 'f' or 'F'
    val double = 3.14           // Double — default for floating-point
    val hex = 0xFF              // Int — 255, hexadecimal
    val bin = 0b1010            // Int — 10, binary
    val big = 1_000_000         // Int — underscores ignored, just for readability
    val bigLong = 1_000_000L    // Long with underscores

    println("$decimal $long $float $double $hex $bin $big $bigLong")
}
```

- `42L` — the `L` (or lowercase `l`, but capital `L` is preferred because lowercase `l` looks like `1`) suffix tells the compiler this is a `Long`.
- `3.14f` — the `f` suffix marks a `Float`. Without `f`, the literal `3.14` is a `Double`.
- `0xFF` — hexadecimal literal. The `0x` prefix means base-16. `0xFF` equals 255.
- `0b1010` — binary literal. The `0b` prefix means base-2. `0b1010` equals 10.
- `1_000_000` — underscores can be inserted anywhere in a numeric literal for readability. They are ignored by the compiler. Use them to group digits in large numbers, like you would write `1,000,000` in everyday life.

---

## 5. Strings — Escaped and Raw

The `String` type in Kotlin is immutable (just like Java's). Strings come in two flavors:

```kotlin
fun main() {
    // Escaped string — uses \\n, \\t, \\\\ escape sequences
    val escaped = "Line 1\nLine 2\tTabbed"

    // Raw string — three double quotes, multi-line, no escapes needed
    val raw = """
        Line 1
        Line 2  Tabbed (literal tab character)
        Backslashes \ need no escaping
    """.trimIndent()

    println(escaped)
    println(raw)
}
```

- `"..."` — **escaped string**. Supports escape sequences like `\n` (newline), `\t` (tab), `\\` (literal backslash), `\$` (literal dollar sign — needed because `$` starts a string template), `\"` (literal double quote). Single line only.
- `"""..."""` — **raw string**. Can span multiple lines, supports string templates (`$name`), and does NOT require escape sequences (a backslash is just a backslash). Perfect for embedding JSON, SQL, HTML, or multi-line configuration.
- `.trimIndent()` — a standard-library extension function that detects the common leading whitespace of all non-blank lines and removes it, so your raw string lines up cleanly when printed. Use this with raw strings almost always.

### Common String Methods

```kotlin
fun main() {
    val s = "Hello, Kotlin"

    println(s.length)               // 13
    println(s.uppercase())          // "HELLO, KOTLIN"
    println(s.lowercase())          // "hello, kotlin"
    println(s.substring(7))          // "Kotlin"
    println(s.substring(0, 5))      // "Hello"
    println(s.replace(",", "!"))    // "Hello! Kotlin"
    println(s.split(", "))          // ["Hello", "Kotlin"]
    println(s.startsWith("Hello"))  // true
    println(s.contains("Kot"))      // true
    println("  hi  ".trim())        // "hi"
}
```

- `.length` — property (no parentheses) giving the number of characters.
- `.uppercase()`, `.lowercase()` — return new strings (original is unchanged, since `String` is immutable).
- `.substring(start)` and `.substring(start, end)` — extract a slice. The end index is exclusive.
- `.split(", ")` — splits on a delimiter, returns `List<String>`.
- `.startsWith()`, `.contains()`, `.trim()` — common utility methods. The Kotlin standard library is enormous; you will discover dozens more.

---

## 6. Booleans

The `Boolean` type has two values: `true` and `false` (both lowercase, like Python and Java):

```kotlin
fun main() {
    val isAdult = true
    val isStudent = false

    // Logical operators
    println(isAdult && isStudent)   // false — logical AND (both must be true)
    println(isAdult || isStudent)   // true  — logical OR (either must be true)
    println(!isAdult)                // false — logical NOT (inverts)

    // Comparison operators return Boolean
    val age = 25
    println(age > 18)                // true
    println(age == 18)               // false
    println(age in 18..65)           // true — range check

    // Booleans in if/when conditions
    if (isAdult && !isStudent) {
        println("Working adult")
    }
}
```

- `true`, `false` — Boolean literals, both lowercase. (Contrast with Python's `True` and `False`.)
- `&&` — logical AND. Short-circuits: if the left side is `false`, the right side is not evaluated.
- `||` — logical OR. Short-circuits: if the left side is `true`, the right side is not evaluated.
- `!` — logical NOT (prefix operator).
- `in 18..65` — checks if `age` is in the range 18 to 65 inclusive (Lesson 04 covers ranges in depth).

---

## 7. Null Safety — THE Kotlin Feature

This is the section that makes Kotlin famous. In Java, any reference type can be `null`. The compiler cannot help you — `String name = ...` might be `null` or might not, and you only find out at runtime when `name.length()` throws `NullPointerException`. Java's answer is to defensively check `if (name != null)` everywhere, but developers forget, and bugs slip through to production. **NullPointerException is the #1 cause of crashes in Java applications** — by a wide margin.

Kotlin's solution is to make nullability part of the type system itself. A `String` variable CANNOT hold `null`. If you want a nullable string, you must explicitly opt in by writing `String?` (with a question mark):

```kotlin
fun main() {
    var name: String = "Hieu"
    // name = null  // COMPILE ERROR — String cannot be null

    var maybeName: String? = "Hieu"
    maybeName = null   // OK — String? can hold null

    println(name.length)        // OK — String is non-null, safe to dereference
    // println(maybeName.length) // COMPILE ERROR — maybeName might be null
    println(maybeName?.length)  // OK — safe call, returns null if maybeName is null
}
```

- `var name: String = "Hieu"` — non-nullable `String`. The compiler guarantees this can never be `null`. Assigning `null` to it is a compile error.
- `var maybeName: String? = "Hieu"` — nullable `String?` (note the `?`). This can hold a `String` OR `null`. The `?` is part of the type — `String` and `String?` are different types.
- `name.length` — direct property access is fine because `name` is non-null.
- `maybeName.length` — compile error. The compiler refuses because `maybeName` might be null and dereferencing null throws NPE.
- `maybeName?.length` — **safe call**. Returns `maybeName.length` if `maybeName` is non-null, or `null` otherwise. The result type is `Int?` (nullable `Int`).

The compiler does this analysis at compile time, tracking which variables are nullable and which are not. If a function returns `String`, you can use it directly. If it returns `String?`, the compiler forces you to handle the null case before using the value. This eliminates an entire category of runtime crashes — no more `NullPointerException` surprises in production.

### Why This Matters in Practice

Consider this Java code, which compiles fine but crashes at runtime (shown in plain text so the focus stays on the structure):

```text
// Java — compiles, crashes at runtime
public String getName(User user) {
    return user.getName().toLowerCase();  // NPE if user is null or getName returns null
}
```

The equivalent Kotlin code would not even compile, because the compiler tracks that `User` might be null and that `getName()` might return null. You are forced to handle both cases explicitly. The result is code that "just works" — if it compiles, the most common cause of runtime crashes (null dereferences) has been eliminated.

---

## 8. Safe Calls, Elvis, and `!!`

Kotlin gives you four tools for working with nullable values. Each has a specific purpose.

### Safe Call `?.`

```kotlin
fun main() {
    var name: String? = "Hieu"
    println(name?.length)  // 4 — returns name.length

    name = null
    println(name?.length)  // null — returns null instead of crashing

    // Chained safe calls
    val user: User? = getUser()
    println(user?.name?.length)  // null if user is null OR user.name is null
}

class User(val name: String)

fun getUser(): User? = null
```

- `name?.length` — the **safe call operator**. If `name` is null, the whole expression returns `null`. If `name` is not null, it returns `name.length`. The result type is `Int?`.
- `user?.name?.length` — safe calls chain together. If `user` is null, the whole chain returns null. If `user` is not null but `user.name` is null, the chain returns null. Only if everything along the way is non-null do you get a real length. This replaces dozens of lines of nested `if (x != null)` checks in Java.

### Elvis Operator `?:`

```kotlin
fun main() {
    var name: String? = null

    // If name is null, use "Guest" instead
    val displayName = name ?: "Guest"
    println(displayName)  // "Guest"

    name = "Hieu"
    val name2 = name ?: "Guest"
    println(name2)  // "Hieu"

    // Elvis can also throw or return early
    val user = getUser() ?: return  // if null, exit function
}
```

- `name ?: "Guest"` — the **Elvis operator** (looks like Elvis Presley's hairstyle if you squint: `?:`). If the left side is not null, returns the left side. If the left side is null, returns the right side. The result type is `String` (non-null) — Elvis converts a nullable value into a non-nullable one with a default.
- `getUser() ?: return` — common idiom. If `getUser()` returns null, the function returns early. This pattern is used heavily in Kotlin codebases to fail fast at the top of a function.

### Not-Null Assertion `!!`

```kotlin
fun main() {
    var name: String? = "Hieu"
    println(name!!.length)  // 4 — asserts non-null, returns length

    name = null
    // println(name!!.length)  // RUNTIME CRASH: NullPointerException
}
```

- `name!!` — the **not-null assertion**. Tells the compiler "I promise this is not null, trust me". If `name` is actually null at runtime, Kotlin throws a `NullPointerException`. Use this ONLY when you are 100% sure the value is not null and you have a good reason to bypass the null-safety check. Most experienced Kotlin developers treat `!!` as a code smell — there is usually a better way to express what you want.

### `let` for "if not null, run this block"

```kotlin
fun main() {
    var name: String? = "Hieu"

    // Run a block only if name is not null
    name?.let {
        println("Name length is ${it.length}")
    }

    name = null
    name?.let {
        println("This will not print")
    }
}
```

- `name?.let { ... }` — calls the `let` function on `name` ONLY if `name` is not null. Inside the lambda, `it` refers to the non-null value. This is the idiomatic Kotlin replacement for `if (name != null) { ... }`. Lesson 07 covers `let` and the other scope functions in detail.

### Putting It All Together

```kotlin
fun greet(name: String?): String {
    // Elvis for default
    val displayName = name ?: "Guest"
    return "Hello, $displayName!"
}

fun printLength(name: String?) {
    // Safe call + Elvis
    val length = name?.length ?: 0
    println("Length: $length")
}

fun main() {
    println(greet(null))        // Hello, Guest!
    println(greet("Hieu"))      // Hello, Hieu!
    printLength(null)           // Length: 0
    printLength("Kotlin")       // Length: 6
}
```

This is idiomatic Kotlin: nullable at the boundaries (function parameters that might come from external sources), then immediately converted to non-nullable values with Elvis or safe calls. Inside the function body, everything is non-null and the compiler helps you, not hinders you.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Practice `val` vs `var`

Write a program with three variables: a `val` for your name, a `val` for your birth year (an `Int`), and a `var` for your current age. Compute your age next year by reassigning the `var`. Try to reassign the `val` and watch the compiler refuse. Notice how the compiler error message tells you exactly what you did wrong ("Val cannot be reassigned"). Add a `println` showing your name, birth year, current age, and age next year.

### Exercise 2: Convert Nullable to Non-Nullable

Write a function `fun displayName(name: String?): String` that returns `"Anonymous"` if `name` is null, otherwise returns the name uppercased. Call it with three arguments: `"hieu"`, `null`, and `"World"`. Print all three results. Notice how the Elvis operator `?:` lets you write this in one line — no `if` statement needed.

### Exercise 3: Safe Calls and `let`

Write a program where a variable `email: String?` starts as `null`. Use `email?.let { println("Sending email to $it") }` and observe that nothing prints. Then assign `email = "hieu@example.com"` and call `let` again — observe the message prints. Add an Elvis fallback that prints "No email on file" when `email` is null. This pattern (`?.let { ... } ?: run { ... }`) is extremely common in Kotlin codebases for "do this if present, otherwise do that".

---

## Common Mistakes

### Mistake 1: Using `var` where `val` would do

```kotlin
// BAD — var when the value never changes
fun main() {
    var greeting = "Hello"
    var name = "Hieu"
    println("$greeting, $name!")
}
```

```kotlin
// GOOD — default to val
fun main() {
    val greeting = "Hello"
    val name = "Hieu"
    println("$greeting, $name!")
}
```

Beginners (especially those coming from Java or Python) tend to use `var` everywhere out of habit. In Kotlin the convention is the opposite: **start with `val` and only switch to `var` if the compiler tells you that you need to reassign**. Read-only variables make code easier to reason about — once you see `val x = computeX()`, you know `x` never changes for the rest of its scope. If you find yourself writing `var x = ...` and never reassigning it, change it to `val`. IntelliJ will even warn you about this with a yellow inspection.

### Mistake 2: Using `!!` to silence null-safety warnings

```kotlin
// BAD — using !! everywhere to silence the compiler
fun process(name: String?) {
    println(name!!.length)  // Crashes at runtime if name is null
    println(name!!.uppercase())
    println(name!!.substring(0, 3))
}
```

```kotlin
// GOOD — handle null once, then work with a non-null value
fun process(name: String?) {
    val safeName = name ?: return  // Exit early if null
    println(safeName.length)       // safeName is now String (non-null)
    println(safeName.uppercase())
    println(safeName.substring(0, 3))
}
```

`!!` is a "get out of jail free" card that defeats the purpose of Kotlin's null safety. Every `!!` in your code is a potential `NullPointerException` waiting to happen. The fix is usually to handle the null case explicitly at the top of the function — use Elvis with `return` or `throw`, use a safe call `?.`, or assign to a non-null local variable via `?:`. Once you have a non-null value, the rest of the function flows naturally without any `!!`. Treat `!!` as a code smell; if you find yourself reaching for it, ask "what should happen if this is actually null?" and handle that case explicitly.

### Mistake 3: Comparing strings with `===` (you usually want `==`)

```kotlin
// WRONG — usually wrong intent; === compares references, not values
fun main() {
    val a = "Hello"
    val b = "Hello"
    println(a === b)  // might be true (string interning) or false — unreliable
}
```

```kotlin
// RIGHT — == compares values (calls .equals())
fun main() {
    val a = "Hello"
    val b = "Hello"
    println(a == b)  // true — structural equality
}
```

In Kotlin, `==` calls `.equals()` (structural/value equality) and `===` compares references (identity). This is the OPPOSITE of Java, where `==` compares references. The practical rule: use `==` for almost all comparisons — comparing strings, numbers, lists, data class instances. Reach for `===` only when you genuinely need to know whether two variables point at the same object in memory (which is rare). If you find yourself writing `===`, ask "do I really care about identity, or do I just want to compare values?".

### Mistake 4: Forgetting that `Int?` and `Int` are different types

```kotlin
// WRONG — passing Int? where Int is required
fun double(n: Int): Int = n * 2

fun main() {
    val maybeN: Int? = readln().toIntOrNull()  // returns Int? because parsing can fail
    println(double(maybeN))  // COMPILE ERROR — type mismatch
}
```

```kotlin
// RIGHT — handle the null case first
fun double(n: Int): Int = n * 2

fun main() {
    val maybeN: Int? = readln().toIntOrNull()
    val n = maybeN ?: 0  // default to 0 if parsing failed
    println(double(n))  // OK — n is now Int (non-null)
}
```

`Int` and `Int?` are different types in Kotlin's type system. You cannot pass an `Int?` where an `Int` is required — the compiler refuses. This is by design: it forces you to handle the null case at the boundary, then work with non-null values everywhere else. The fix is to use Elvis (`?:`), safe call (`?.`), or a smart-cast `if (x != null) { ... }` block to convert the nullable value to a non-nullable one before passing it. The same applies to `String`/`String?`, `List<T>`/`List<T>?`, and every other type — the `?` is part of the type.

---

## Summary

- Kotlin has two variable keywords: `val` (read-only, assigned once) and `var` (mutable, reassignable). **Default to `val`** in idiomatic Kotlin code.
- Type inference means you rarely write the type — `val x = 5` infers `Int`. Use explicit annotations (`val x: Long = 5`) when you want a specific type or are declaring without initialization.
- Basic types: `Byte`, `Short`, `Int`, `Long`, `Float`, `Double`, `Char`, `Boolean`, `String`. All are objects in the language, but compiled to JVM primitives when possible. No `unsigned` keyword (use `UInt`, `ULong`, etc.).
- Numeric literals: `42L` (Long), `3.14f` (Float), `0xFF` (hex), `0b1010` (binary), `1_000_000` (underscores for readability).
- Strings are immutable and come in two forms: `"..."` escaped (single-line, with `\n` escapes) and `"""..."""` raw (multi-line, no escapes, use `.trimIndent()` to clean up indentation).
- Null safety is Kotlin's headline feature: `String` cannot be null, `String?` can. The compiler refuses to compile code that dereferences a nullable value without a null check.
- Four tools for nullable values: `?.` (safe call), `?:` (Elvis, returns default if null), `!!` (force unwrap, throws NPE if null — use sparingly), `?.let { }` (run block if not null).
- `Int?` and `Int` are different types — you cannot pass one where the other is required. Convert with `?:`, `?.`, or a null check at the boundary.

You now understand Kotlin's type system and null safety. In Lesson 04 we cover control flow: how `if` is an expression, the powerful `when` block (Kotlin's super-switch), `for` loops over ranges, and labeled `break` and `continue` for nested loops.

---

**Next:** [Lesson 04: Control Flow →](./04-control-flow.md)
