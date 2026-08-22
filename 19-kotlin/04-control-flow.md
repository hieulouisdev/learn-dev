# Lesson 04: Control Flow

> Module: Kotlin · Lesson 4 of 7
> Estimated time: 30–45 minutes

In Lesson 03 you learned Kotlin's type system, including the headline null-safety feature. Now we turn to control flow — the language constructs that decide which code runs and how many times. Kotlin's control flow is more powerful than Java's in several important ways. The `if` statement is an expression (returns a value), the `when` block is a far more powerful replacement for Java's `switch`, and `for` loops work over ranges and collections in a clean syntax that feels almost like Python.

You will learn how `if` returns a value, how `when` matches patterns (not just constants), how to iterate over inclusive and exclusive ranges, how to step backward, and how to break out of nested loops using labels. By the end you will be able to read and write idiomatic Kotlin control flow without reaching for Java's verbose `switch` statements or for-loop boilerplate.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use `if` as an expression that returns a value (Kotlin's equivalent of the ternary operator).
2. Replace Java's `switch` with Kotlin's `when` block, including matching ranges, multiple values, and exhaustive patterns.
3. Iterate over ranges (`1..5`, `1 until 5`, `10 downTo 1`, `1..10 step 2`) and collections (`for (item in list)`, `for ((i, x) in list.withIndex())`).
4. Use labeled `break` and `continue` to control nested loops, and explain why you cannot `break` out of a `forEach` lambda.

---

## 1. `if` as an Expression

In Java and C++, `if` is a statement — it runs code but produces no value. In Kotlin, `if` is an **expression**: it evaluates to a value. This lets you write concise conditional assignments without needing a separate ternary operator (Kotlin has no `?:` ternary — it just uses `if`).

```kotlin
fun main() {
    val a = 5
    val b = 10

    // if as an expression — returns the value of the chosen branch
    val max = if (a > b) a else b
    println(max)  // 10

    // With blocks — the last expression in each block is the value
    val result = if (a > b) {
        println("a is bigger")
        a
    } else {
        println("b is bigger or equal")
        b
    }
    println(result)  // 10

    // Plain if-statement form also works
    if (a > b) {
        println("a wins")
    } else if (a < b) {
        println("b wins")
    } else {
        println("tie")
    }
}
```

- `val max = if (a > b) a else b` — the entire `if` expression evaluates to either `a` or `b`, which is assigned to `max`. This replaces Java's `int max = a > b ? a : b;` — Kotlin has no ternary operator because `if` already serves that role.
- `if (a > b) { ... a } else { ... b }` — when you use blocks (curly braces), the **last expression** in each block is the value of that branch. Notice there is no `return` keyword — Kotlin uses the last expression automatically. This is the same pattern as Rust.
- Plain `if (...) { ... } else if (...) { ... } else { ... }` — Kotlin also supports the traditional statement form when you don't care about a value, just like Java and C++.

The key insight is that in Kotlin, `if` always produces a value when used as an expression. If you use it as a statement (no surrounding assignment), the value is just discarded. This makes Kotlin feel like a hybrid between Java's statement-style if and Rust/Swift's expression-style if.

---

## 2. `when` — The Super Switch

Kotlin's `when` is a far more powerful replacement for Java's `switch`. Where Java's `switch` only matches exact constants and falls through if you forget `break`, Kotlin's `when` matches patterns (constants, ranges, multiple values, types, arbitrary conditions), does not fall through, and can be used as an expression that returns a value.

```kotlin
fun describe(x: Any): String = when (x) {
    1          -> "one"
    2, 3       -> "two or three"
    in 4..10   -> "small number"
    in 11..100 -> "medium number"
    is String  -> "a string of length ${x.length}"
    is Int     -> "an integer"
    else       -> "unknown"
}

fun main() {
    println(describe(1))         // one
    println(describe(3))         // two or three
    println(describe(7))         // small number
    println(describe(42))       // medium number
    println(describe("hello"))  // a string of length 5
    println(describe(999L))     // unknown
}
```

- `when (x) { ... }` — Kotlin's replacement for `switch(x)`. The value `x` is matched against each branch from top to bottom; the first matching branch wins, and execution stops there. There is no fall-through, so no need for `break`.
- `1 -> "one"` — match a constant value. If `x` equals `1`, this branch fires.
- `2, 3 -> "two or three"` — multiple values, separated by commas. If `x` is `2` or `3`, this branch fires.
- `in 4..10 -> "small number"` — match against a range. The `in` keyword checks whether `x` is in the range `4..10` (inclusive both ends).
- `is String -> "a string of length ${x.length}"` — match against a type. The `is` keyword checks the runtime type of `x`. Inside the branch, the compiler smart-casts `x` to `String`, so you can call `.length` without an explicit cast.
- `else -> "unknown"` — the default branch, used when no other branch matches. Required when `when` is used as an expression and not all possible values are covered.

### `when` Without a Subject (Branch Conditions)

You can also use `when` without a subject — each branch is just a boolean expression:

```kotlin
fun classify(temp: Int): String = when {
    temp < 0           -> "freezing"
    temp in 0..15      -> "cold"
    temp in 16..25     -> "comfortable"
    temp in 26..35     -> "hot"
    temp > 35          -> "scorching"
    else               -> "out of range"
}

fun main() {
    println(classify(-5))   // freezing
    println(classify(20))   // comfortable
    println(classify(40))   // scorching
}
```

- `when { ... }` — no subject in parentheses. Each branch is just a boolean condition; the first one that evaluates to `true` wins. This is more flexible than the subject form, because the conditions can be unrelated to each other.
- `else` — still required when used as an expression.

### Exhaustiveness

When `when` is used as an expression (assigned to a variable or returned from a function), the compiler checks that **all possible cases are covered**. If you `when` on a sealed class (Lesson 06), the compiler even warns you if you forget a case — turning "did you remember to handle every type?" into a compile-time check.

### No Fall-Through

Unlike Java's `switch`, Kotlin's `when` does NOT fall through. Each branch is independent; only the first matching branch runs. You never need a `break` statement. This eliminates an entire class of bugs where a Java developer forgets `break` and multiple cases execute unintentionally.

---

## 3. `for` Loops

Kotlin's `for` loop iterates over anything that provides an iterator — ranges, collections, strings, anything. There is no C-style `for (int i = 0; i < n; i++)`. Instead, you iterate over a range or collection directly.

```kotlin
fun main() {
    // Inclusive range: 1, 2, 3, 4, 5
    for (i in 1..5) {
        print("$i ")
    }
    println()  // 1 2 3 4 5

    // Exclusive range: 1, 2, 3, 4 (5 is excluded)
    for (i in 1 until 5) {
        print("$i ")
    }
    println()  // 1 2 3 4

    // With step: 1, 3, 5, 7, 9
    for (i in 1..10 step 2) {
        print("$i ")
    }
    println()  // 1 3 5 7 9

    // Counting down: 5, 4, 3, 2, 1
    for (i in 5 downTo 1) {
        print("$i ")
    }
    println()  // 5 4 3 2 1

    // Iterating a list
    val fruits = listOf("apple", "banana", "cherry")
    for (fruit in fruits) {
        print("$fruit ")
    }
    println()  // apple banana cherry

    // Iterating with index
    for ((index, fruit) in fruits.withIndex()) {
        println("$index: $fruit")
    }
    // 0: apple
    // 1: banana
    // 2: cherry
}
```

- `for (i in 1..5) { ... }` — the `..` operator creates an **inclusive range** from 1 to 5 (1, 2, 3, 4, 5). The loop variable `i` takes each value in turn.
- `for (i in 1 until 5) { ... }` — the `until` function creates an **exclusive range** from 1 up to but not including 5 (1, 2, 3, 4). Useful when you want indices 0 through n-1, like looping over an array's indices.
- `for (i in 1..10 step 2) { ... }` — the `step` modifier sets the increment. `step 2` gives odd numbers (1, 3, 5, 7, 9).
- `for (i in 5 downTo 1) { ... }` — `downTo` creates a descending range. Useful for countdowns.
- `for (fruit in fruits) { ... }` — iterate directly over a list. No need for indices; the loop variable takes each element in turn.
- `for ((index, fruit) in fruits.withIndex())` — get both the index and the value at once. `withIndex()` returns `IndexedValue` pairs, and Kotlin destructures them into `index` and `fruit` in one line. This is the idiomatic replacement for Java's `for (int i = 0; i < list.size(); i++)`.

### `forEach` on Collections

For simple iterations, collections also have a `forEach` function that takes a lambda:

```kotlin
fun main() {
    val nums = listOf(1, 2, 3, 4, 5)

    nums.forEach { n ->
        println(n)
    }

    // With implicit `it` for single-arg lambdas
    nums.forEach {
        println(it)
    }
}
```

- `nums.forEach { n -> ... }` — calls the lambda once per element. `n` is the element. Equivalent to a `for` loop but in functional style.
- `nums.forEach { println(it) }` — when a lambda has exactly one parameter, you can omit the parameter declaration and use the implicit name `it`. We cover this in Lesson 05.

Note: you CANNOT use `break` or `continue` inside a `forEach` lambda — those only work in real `for` and `while` loops. To break out of a `forEach`, you either throw an exception (ugly) or convert to a `for` loop. This is a common gotcha covered in Common Mistakes.

---

## 4. `while` and `do-while`

Kotlin also has `while` and `do-while` loops, identical in form to Java's:

```kotlin
fun main() {
    // while — checks condition first, may run zero times
    var n = 5
    while (n > 0) {
        print("$n ")
        n--
    }
    println()  // 5 4 3 2 1

    // do-while — runs body first, then checks; runs at least once
    var m = 0
    do {
        print("$m ")
        m++
    } while (m < 3)
    println()  // 0 1 2
}
```

- `while (condition) { ... }` — checks the condition before each iteration. If the condition is initially false, the body runs zero times.
- `do { ... } while (condition)` — runs the body first, THEN checks the condition. The body always runs at least once, even if the condition is initially false. Useful for things like "read input until valid" — you want to ask at least once before checking whether to stop.
- `n--` — post-decrement. Decrements `n` by 1 and returns the old value. Same as Java and C++.

`while` and `do-while` are less common than `for` in idiomatic Kotlin code, because most iterations are over collections (where `for` or `forEach` is clearer). Use `while` when the loop has a natural termination condition that is not "iterate N times" — like reading lines from a file until EOF, or retrying an operation until it succeeds.

---

## 5. `break`, `continue`, and Labels

Kotlin supports `break` and `continue` just like Java. `break` exits the innermost loop; `continue` skips to the next iteration of the innermost loop. For nested loops, Kotlin adds **labels** — a way to break or continue an outer loop directly.

```kotlin
fun main() {
    // Plain break and continue
    for (i in 1..10) {
        if (i == 5) break        // exit loop when i reaches 5
        if (i % 2 == 0) continue // skip even numbers
        print("$i ")
    }
    println()  // 1 3

    // Labeled break for nested loops
    outer@ for (i in 1..3) {
        for (j in 1..3) {
            if (i == 2 && j == 2) break@outer  // break out of the outer loop
            println("i=$i, j=$j")
        }
    }
    // Output:
    // i=1, j=1
    // i=1, j=2
    // i=1, j=3
    // i=2, j=1
}
```

- `break` — exits the innermost loop immediately. Execution continues after the loop.
- `continue` — skips the rest of the current iteration and moves to the next iteration of the innermost loop.
- `outer@` — a **label** placed before a loop. Labels are named with an identifier followed by `@`.
- `break@outer` — break out of the loop labeled `outer`, not just the innermost loop. Without the label, `break` would only exit the inner `j` loop and the outer `i` loop would continue.
- `continue@outer` — same idea for `continue`: skip to the next iteration of the labeled loop.

Labels are essential when you have nested loops and need to break out of the outer one from inside the inner one. In Java you would need a flag variable (`boolean shouldBreak = false;`) plus an `if (shouldBreak) break;` check after each inner loop. In Kotlin you just write `break@outer` and you are done.

---

## 6. Ranges in Depth

Ranges in Kotlin are first-class objects. You can store them in variables, check membership with `in`, and access their bounds.

```kotlin
fun main() {
    val range = 1..10           // IntRange, inclusive: 1, 2, ..., 10
    val chars = 'a'..'e'        // CharRange, inclusive: a, b, c, d, e
    val longs = 1L..100L        // LongRange, inclusive

    println(5 in range)         // true — membership check
    println(15 in range)        // false
    println('c' in chars)       // true

    println(range.first)        // 1 — start of the range
    println(range.last)         // 10 — end of the range (inclusive)
    println(range.step)         // 1 — step value (default 1)

    val stepped = (1..10 step 3)
    println(stepped.toList())   // [1, 4, 7, 10]
    println(stepped.step)       // 3

    val reversed = (10 downTo 1)
    println(reversed.toList())  // [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
}
```

- `1..10` — creates an `IntRange`. The `..` operator is inclusive on both ends. Range objects are lightweight; creating one does not allocate a list of all elements.
- `'a'..'e'` — ranges work on `Char` too, and on `Long` (`1L..100L`). They work on any `Comparable` type via `rangeTo()`.
- `5 in range` — the `in` operator checks membership. Equivalent to `range.contains(5)`, but reads more naturally.
- `range.first`, `range.last`, `range.step` — properties of a range. `first` is the start, `last` is the (inclusive) end, `step` is the increment (default 1).
- `range.toList()` — materializes the range into a `List<Int>`. Useful for inspection but not needed for iteration.
- `1..10 step 3` — returns a new `IntProgression` with the given step. Note that `step` produces a `Progression`, not a `Range` (because a stepped sequence does not have a meaningful `.last` in the same way).
- `10 downTo 1` — produces a descending progression from 10 down to 1.

Ranges are extremely common in Kotlin code. You will see them in `for` loops, in `if (x in 0..100)` checks, and in `when` branches (`in 0..100 -> "small"`). They are zero-cost when you only iterate — the iterator computes each value lazily instead of allocating an array.

---

## 7. `return`, `break`, `continue` in `when`

Inside a `when` block used as a statement (not as an expression), you can use `return`, `break`, and `continue` to control flow:

```kotlin
fun classify(n: Int): String {
    when {
        n < 0 -> return "negative"        // exit the function
        n == 0 -> return "zero"
        n < 100 -> return "small positive"
        else -> {
            // fall through to the println below
        }
    }
    return "large positive"
}

fun main() {
    println(classify(-5))  // negative
    println(classify(0))   // zero
    println(classify(50))  // small positive
    println(classify(999)) // large positive
}
```

- When used as a statement, `when` branches can contain `return`, `break`, `continue`, or just code blocks. The compiler does not require a value.
- When used as an expression, the last expression in each branch is the value, and you cannot `return` from inside (because the expression needs to produce a value, not exit).

In idiomatic Kotlin, you usually use `when` as an expression (`val result = when (x) { ... }`) rather than as a statement with side effects. Expression-style `when` is more functional and easier to reason about.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Classify a Number

Write a function `fun classify(n: Int): String` that returns:
- `"negative"` if `n < 0`
- `"zero"` if `n == 0`
- `"small"` if `n` is in `1..9`
- `"medium"` if `n` is in `10..99`
- `"large"` otherwise

Use a single `when` expression. Call it with `-5`, `0`, `7`, `42`, and `1000`, and print all results. Notice how much shorter this is than the equivalent Java `switch` with multiple `if/else if` chains.

### Exercise 2: Multiplication Table

Write a program with two nested `for` loops that prints a 5×5 multiplication table. The outer loop iterates `i` from 1 to 5, the inner loop iterates `j` from 1 to 5, and you print `i * j` followed by a tab character (`\t`). After each row (after the inner loop), print a newline. The output should look like:

```text
1	2	3	4	5
2	4	6	8	10
3	6	9	12	15
4	8	12	16	20
5	10	15	20	25
```

### Exercise 3: Labeled Break

Modify the multiplication table so it stops as soon as the product exceeds 15. Use a labeled `break@outer` to exit both loops when this happens. Confirm your output stops at the right place. This is the classic Kotlin idiom for "find the first match in a 2D search".

---

## Common Mistakes

### Mistake 1: Using `switch` instead of `when`

```kotlin
// WRONG — Kotlin has no switch keyword
fun describe(x: Int): String {
    switch (x) {  // COMPILE ERROR — switch does not exist in Kotlin
        case 1: return "one"
        case 2: return "two"
        default: return "many"
    }
}
```

```kotlin
// RIGHT — Kotlin uses when instead of switch
fun describe(x: Int): String = when (x) {
    1    -> "one"
    2    -> "two"
    else -> "many"
}
```

If you are coming from Java (Module 17), your fingers will type `switch`. Kotlin does not have a `switch` keyword — it has `when`, which is more powerful. `when` does not require `break` (no fall-through), supports ranges (`in 1..5 -> ...`), multiple values (`1, 2, 3 -> ...`), type checks (`is String -> ...`), and arbitrary boolean branches (`when { cond1 -> ...; cond2 -> ... }`). Once you get used to `when`, you will miss it every time you go back to Java.

### Mistake 2: Forgetting `when` must be exhaustive when used as an expression

```kotlin
// WRONG — missing else branch, compiler error
fun describe(x: Int): String = when (x) {
    1 -> "one"
    2 -> "two"
    // COMPILE ERROR: when expression must be exhaustive
    // (you didn't cover all possible Int values)
}
```

```kotlin
// RIGHT — add an else branch to cover all other cases
fun describe(x: Int): String = when (x) {
    1    -> "one"
    2    -> "two"
    else -> "many"
}
```

When `when` is used as an expression (assigned to a value, returned from a function), the compiler requires that all possible values are covered. For an `Int` argument, that means an `else` branch is mandatory because you cannot enumerate every `Int`. For sealed classes (Lesson 06), the compiler checks exhaustiveness against the sealed hierarchy — if you forget a case, it warns you. When `when` is used as a statement (not producing a value), `else` is optional. The fix is to add an `else -> ...` branch covering all remaining cases.

### Mistake 3: Off-by-one with `..` (inclusive) vs `until` (exclusive)

```kotlin
// BUG — using .. when you meant until, includes 5
fun main() {
    for (i in 0..5) {
        print("$i ")
    }
}
// Output: 0 1 2 3 4 5  (six iterations, but you wanted five)
```

```kotlin
// RIGHT — use until for exclusive upper bound, like classic for (int i = 0; i < 5; i++)
fun main() {
    for (i in 0 until 5) {
        print("$i ")
    }
}
// Output: 0 1 2 3 4  (five iterations, as intended)
```

`..` is **inclusive** on both ends — `0..5` produces 0, 1, 2, 3, 4, 5 (six values). `until` is **exclusive** on the upper end — `0 until 5` produces 0, 1, 2, 3, 4 (five values). When you are iterating over array indices or implementing "do this N times", use `until` to match the conventional `<` semantics of Java's `for (int i = 0; i < n; i++)`. When you genuinely want both endpoints (like looping over days 1 to 31 of a month), use `..`.

### Mistake 4: Trying to `break` out of a `forEach` lambda

```kotlin
// WRONG — break does not work inside forEach
fun main() {
    val nums = listOf(1, 2, 3, 4, 5)
    nums.forEach {
        if (it == 3) break  // COMPILE ERROR — break is not allowed here
        println(it)
    }
}
```

```kotlin
// RIGHT — use a for loop if you need to break
fun main() {
    val nums = listOf(1, 2, 3, 4, 5)
    for (n in nums) {
        if (n == 3) break  // OK — break works in for loops
        println(n)
    }
}
// Output: 1 2
```

`forEach` is a function, not a loop. It takes a lambda and calls it once per element. The `break` and `continue` keywords only work inside actual `for` and `while` loops — they do not work inside lambdas. If you need to break out of an iteration early, switch to a `for` loop. (Alternatively, you can use `return@forEach` to skip to the next iteration — equivalent to `continue` — but this is rarely idiomatic.) The lesson: when in doubt about whether you will need to break early, use `for` instead of `forEach`.

---

## Summary

- `if` is an **expression** in Kotlin — `val max = if (a > b) a else b`. There is no separate ternary operator; `if` serves both roles.
- `when` is Kotlin's powerful replacement for `switch`. Matches constants, multiple values (`1, 2, 3 ->`), ranges (`in 1..10 ->`), types (`is String ->`), and arbitrary boolean conditions (`when { cond -> ... }`). No fall-through — no `break` needed.
- When `when` is used as an expression, it must be exhaustive (cover all cases). For `Int`, this means an `else` branch is required.
- `for` loops iterate over ranges (`1..5` inclusive, `1 until 5` exclusive, `1..10 step 2`, `5 downTo 1`) and collections (`for (item in list)`, `for ((i, item) in list.withIndex())`).
- `while` checks before each iteration (zero or more runs); `do-while` checks after (one or more runs). Identical to Java.
- `break` exits the innermost loop; `continue` skips to the next iteration. **Labels** (`outer@` + `break@outer`) let you break out of nested loops directly.
- Ranges are first-class objects: `1..10` creates an `IntRange`, `5 in range` checks membership, `range.first`/`.last`/`.step` give bounds.
- `break` and `continue` only work in real `for`/`while` loops — not in `forEach` lambdas. Use a `for` loop when you need early exit.

You now control the flow of your Kotlin programs. In Lesson 05 we cover functions in depth: default arguments, named arguments, vararg, single-expression functions, Kotlin's killer feature **extension functions**, and higher-order functions with lambdas.

---

**Next:** [Lesson 05: Functions →](./05-functions.md)
