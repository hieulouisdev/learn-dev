# Lesson 04: Control Flow

> Module: Swift · Lesson 4 of 7
> Estimated time: 30–45 minutes

In Lesson 03 you learned how to declare variables and constants and how Swift's type system handles different value types. In this lesson we cover the control flow constructs that decide *when* and *how often* blocks of code run: `if`/`else` (and `if` as an expression in Swift 5.9+), the powerful `switch` statement with pattern matching on ranges and tuples, `for` loops with the `...` and `..<` range operators, `while` and `repeat-while` loops, and labeled `break`/`continue` for nested loops.

Swift's control flow has some distinctive features. The conditions in `if` and `while` must be `Bool`-typed — no truthiness, unlike Python or JavaScript. The `switch` statement is *exhaustive* — the compiler refuses to compile it if you have not handled every possible case, and there is no implicit fall-through between cases. The `for` loop uses range operators (`1...5` inclusive, `1..<5` exclusive) rather than the traditional C `for (int i = 0; i < n; i++)`. By the end of this lesson you will be writing idiomatic Swift control flow that is concise, safe, and reads almost like prose.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use `if`/`else if`/`else` correctly and explain why Swift requires the condition to be a `Bool` (no truthiness).
2. Write a `switch` statement with pattern matching on integers, ranges, tuples, and bound values, and explain the difference between Swift's `switch` and C's.
3. Use `for` loops with closed ranges (`1...5`), half-open ranges (`1..<5`), array iteration, `enumerated()`, and string iteration.
4. Use `while`, `repeat-while`, labeled `break`/`continue`, and the range operators correctly.

---

## 1. `if` and `else`

Swift's `if` statement is similar to C, Java, and JavaScript with three key differences. **First, no parentheses are required around the condition** — `if score > 80 { ... }` is fine, and `(score > 80)` is also accepted but discouraged by the style guide. **Second, braces are required** — you cannot omit them for single-statement bodies like C or Java (`if (x) print(y)` is a syntax error in Swift). **Third, the condition must be a `Bool`** — no truthiness, no implicit conversion from `Int` or `String` to `Bool`.

```swift
let score = 85

if score >= 90 {
    print("A")
} else if score >= 80 {
    print("B")
} else if score >= 70 {
    print("C")
} else {
    print("F")
}
```

Line by line:

- `let score = 85` — declares a constant `score` inferred as `Int`.
- `if score >= 90 { ... }` — the condition is `score >= 90`, which evaluates to a `Bool`. The braces open the body. No parentheses needed around the condition.
- `else if score >= 80 { ... }` — second condition, evaluated only if the first was false.
- `else { ... }` — final fallback. Prints `F` if none of the above were true.

Because `score` is `85`, this prints `B`.

The "no truthiness" rule trips up developers coming from Python, JavaScript, or Ruby:

```swift
let name = "Hieu"
// if name {  // ERROR — Swift requires an explicit Bool
//     print("non-empty")
// }
if !name.isEmpty {
    print("non-empty")   // OK — .isEmpty returns Bool
}

let count = 5
// if count {  // ERROR — Int is not Bool
//     print("non-zero")
// }
if count != 0 {
    print("non-zero")    // OK
}
```

- `if name` — does not compile. `name` is `String`, not `Bool`. The error is `type 'String' cannot be used as a boolean condition`.
- `if !name.isEmpty` — correct. `.isEmpty` is a `Bool`-returning property on `String`. `!` negates it.
- `if count` — does not compile. `count` is `Int`. The error is `type 'Int' cannot be used as a boolean condition`.
- `if count != 0` — correct. The comparison `count != 0` returns a `Bool`.

This is a deliberate design choice. Truthiness (`if 0` is false, `if 1` is true, `if "non-empty"` is true) is a frequent source of bugs in dynamic languages — the developer meant to check a specific value but instead checked whether the value was non-zero or non-empty. Swift forces you to be explicit.

---

## 2. `if` as an Expression

Starting in **Swift 5.9** (September 2023), `if` can be used as an *expression* — meaning it produces a value you can assign to a variable:

```swift
let isDark = true
let color = if isDark { "black" } else { "white" }
print(color)   // "black"
```

- `let isDark = true` — a boolean flag.
- `let color = if isDark { "black" } else { "white" }` — the `if` evaluates and produces a value. Because `isDark` is `true`, the value is `"black"`. `color` is inferred as `String`.

Both branches of the `if`-expression must produce the same type. This is the same as Kotlin's `if` expression and Python's conditional expression.

The classic **ternary operator** `?:` still works and is preferred for very short expressions:

```swift
let color = isDark ? "black" : "white"
```

- `isDark ? "black" : "white"` — if `isDark` is `true`, evaluates to `"black"`; otherwise `"white"`. Functionally identical to the `if`-expression above.

Use the ternary for simple two-branch value selection. Use the `if`-expression when the branches are more complex (e.g., multi-line bodies). Both produce the same compiled code.

---

## 3. `switch`

Swift's `switch` statement is far more powerful than C's or Java's. Three differences matter most:

1. **`switch` must be exhaustive.** If you have not handled every possible case, the compiler refuses to compile. For an `Int`, that means you must have a `default` case (since `Int` has 2^64 possible values). For an `enum`, you must handle every case (or have a `default`).
2. **No implicit fall-through.** Each case's body ends after its last statement — there is no need to write `break`. If you actually want fall-through behavior, you must write `fallthrough` explicitly.
3. **Pattern matching.** Cases can match on ranges, tuples, type patterns, and bound values.

```swift
let day = 3

switch day {
case 1:
    print("Monday")
case 2:
    print("Tuesday")
case 3:
    print("Wednesday")
case 4:
    print("Thursday")
case 5:
    print("Friday")
case 6, 7:
    print("Weekend")
default:
    print("Invalid day")
}
```

- `switch day { ... }` — switches on the value of `day`.
- `case 1: print("Monday")` — matches when `day == 1`. The body is `print("Monday")`. After this, the `switch` ends — no fall-through, no `break` needed.
- `case 6, 7: print("Weekend")` — multiple values separated by commas. Matches Saturday or Sunday.
- `default: print("Invalid day")` — the catch-all. Required because `day` is an `Int` with far more than 7 possible values.
- Without the `default`, this would not compile: `switch must be exhaustive`.

Because `day` is `3`, this prints `Wednesday`.

**Ranges in switch:**

```swift
let score = 85

switch score {
case 90...100:
    print("A")
case 80..<90:
    print("B")
case 70..<80:
    print("C")
case 60..<70:
    print("D")
default:
    print("F")
}
```

- `case 90...100:` — closed range, matches 90 through 100 inclusive.
- `case 80..<90:` — half-open range, matches 80 through 89 inclusive (excludes 90).
- `case 70..<80:` — half-open range, 70-79.
- `case 60..<70:` — half-open range, 60-69.
- `default:` — anything else (below 60, or above 100).

Because `score` is `85`, this matches `80..<90` and prints `B`.

**Tuples in switch (pattern matching):**

```swift
let point = (3, 0)

switch point {
case (0, 0):
    print("origin")
case (_, 0):
    print("on x-axis")
case (0, _):
    print("on y-axis")
case let (x, y) where x == y:
    print("on the diagonal, value \(x)")
case let (x, y):
    print("at (\(x), \(y))")
}
```

- `case (0, 0):` — matches when both components are zero. Prints `origin`.
- `case (_, 0):` — `_` is a wildcard matching any value. Matches when the second component is `0`. Prints `on x-axis`.
- `case (0, _):` — matches when the first component is `0`. Prints `on y-axis`.
- `case let (x, y) where x == y:` — binds the tuple's components to `x` and `y`, then matches only when `x == y`. The `where` clause adds a guard. Prints `on the diagonal, value \(x)`.
- `case let (x, y):` — the catch-all, binding both components. Prints `at (\(x), \(y))`.

Because `point` is `(3, 0)`, this matches `(_, 0)` and prints `on x-axis`. Cases are evaluated top-to-bottom, so order matters — the first matching case wins.

This pattern-matching power is one of Swift's standout features. Combined with enums (Lesson 06), `switch` becomes a concise way to handle complex branching.

---

## 4. `for` Loops

Swift's `for` loop uses the `for ... in` syntax with ranges or collections. There is no traditional C-style `for (int i = 0; i < n; i++)` — Swift removed it in Swift 3 (2016) in favor of `for ... in`.

```swift
// Closed range: 1, 2, 3, 4, 5
for i in 1...5 {
    print(i)
}

// Half-open range: 1, 2, 3, 4
for i in 1..<5 {
    print(i)
}

// Iterate an array
let fruits = ["apple", "banana", "cherry"]
for fruit in fruits {
    print(fruit)
}

// Iterate with index
for (index, fruit) in fruits.enumerated() {
    print("\(index): \(fruit)")
}

// Iterate characters of a string
for char in "Swift" {
    print(char)
}
```

Line by line:

- `for i in 1...5 { ... }` — `1...5` is a *closed* range from 1 to 5 inclusive. The loop body runs 5 times with `i` taking values 1, 2, 3, 4, 5.
- `for i in 1..<5 { ... }` — `1..<5` is a *half-open* range from 1 to 5, excluding 5. The loop body runs 4 times with `i` taking values 1, 2, 3, 4.
- `for fruit in fruits { ... }` — iterates over the array's elements. `fruit` takes the value `"apple"`, then `"banana"`, then `"cherry"`.
- `for (index, fruit) in fruits.enumerated() { ... }` — `.enumerated()` returns a sequence of `(offset, element)` tuples. Destructured into `index` and `fruit`. Prints `0: apple`, `1: banana`, `2: cherry`.
- `for char in "Swift" { ... }` — iterates over the `Character`s of a string. Prints `S`, `w`, `i`, `f`, `t` on separate lines.

**Stride for custom steps:**

```swift
for i in stride(from: 0, to: 10, by: 2) {
    print(i)   // 0, 2, 4, 6, 8 (excludes 10)
}
for i in stride(from: 10, through: 0, by: -2) {
    print(i)   // 10, 8, 6, 4, 2, 0 (includes 0)
}
```

- `stride(from: 0, to: 10, by: 2)` — half-open; goes from 0 up to (but not including) 10, stepping by 2. Produces 0, 2, 4, 6, 8.
- `stride(from: 10, through: 0, by: -2)` — closed; goes from 10 down to and including 0, stepping by -2. Produces 10, 8, 6, 4, 2, 0.

Use `stride` when you need a step other than 1, or when you need to count backward (ranges like `10...1` are empty — they only work going upward).

---

## 5. `while` and `repeat-while`

Swift has two `while`-style loops:

```swift
// while — check condition FIRST, then run body
var n = 5
while n > 0 {
    print(n)
    n -= 1
}
// Prints: 5, 4, 3, 2, 1

// repeat-while — run body FIRST, then check condition (Swift's name for do-while)
var i = 0
repeat {
    print(i)
    i += 1
} while i < 3
// Prints: 0, 1, 2
```

- `while n > 0 { ... }` — checks the condition *before* running the body. If `n` is already `0` or negative when the loop starts, the body never runs.
- `repeat { ... } while i < 3` — runs the body *first*, then checks the condition. The body always runs at least once, even if the condition is initially false. This is what C, C++, and Java call `do { ... } while (...)` — Swift chose `repeat` instead of `do` because `do` is used for `do-catch` error handling (Lesson 06).

The difference matters when the condition is initially false:

```swift
let neverTrue = false
while neverTrue {
    print("this never runs")
}
repeat {
    print("this runs once")
} while neverTrue
```

- The `while` loop checks `neverTrue` first — it is `false`, so the body never runs.
- The `repeat-while` loop runs the body first (prints `this runs once`), then checks `neverTrue` — it is `false`, so the loop ends.

---

## 6. `break` and `continue`

`break` exits the innermost loop immediately. `continue` skips the rest of the current iteration and jumps to the next iteration:

```swift
for i in 1...10 {
    if i == 5 {
        break   // exit the loop entirely
    }
    if i % 2 == 0 {
        continue   // skip to next iteration
    }
    print(i)
}
// Prints: 1, 3 (5 is break, evens are continue)
```

- `for i in 1...10 { ... }` — loop over 1 to 10.
- `if i == 5 { break }` — when `i` is 5, exit the loop entirely. No more iterations.
- `if i % 2 == 0 { continue }` — when `i` is even (2, 4, 6, ...), skip the rest of this iteration and jump to the next `i`.
- `print(i)` — only reached for odd `i` values where `i != 5`.

The output is `1`, `3` (then `i = 5` triggers `break`, ending the loop).

**Labeled statements** for breaking out of nested loops:

```swift
outerLoop: for i in 1...3 {
    for j in 1...3 {
        if i == 2 && j == 2 {
            break outerLoop   // exits BOTH loops
        }
        print("i=\(i), j=\(j)")
    }
}
// Prints: i=1, j=1 / i=1, j=2 / i=1, j=3 / i=2, j=1
```

- `outerLoop:` — a label on the outer `for` loop. Labels go before any loop or `switch` statement, followed by a colon.
- `break outerLoop` — breaks out of the loop *labeled* `outerLoop`, not just the innermost one. Without the label, `break` would only exit the inner `for j in 1...3` loop.
- The output skips `i=2, j=2` and stops entirely — `break outerLoop` exits both loops.

Labeled `continue` works the same way:

```swift
outerLoop: for i in 1...3 {
    for j in 1...3 {
        if j == 2 {
            continue outerLoop   // skip rest of inner loop, advance outer i
        }
        print("i=\(i), j=\(j)")
    }
}
// Prints: i=1, j=1 / i=2, j=1 / i=3, j=1 (j=2 and j=3 never printed)
```

- `continue outerLoop` — skips the rest of the inner loop and advances the *outer* loop. So when `j == 2`, we skip to the next `i`. Only `j=1` of each `i` is printed.

---

## 7. Range Operators

Swift has three range operators, all of which you have already seen in this lesson. Let's recap:

```swift
let r1 = 1...5       // ClosedRange<Int>: 1, 2, 3, 4, 5
let r2 = 1..<5       // Range<Int>: 1, 2, 3, 4
let r3 = ...5        // PartialRangeThrough<Int>: matches anything <= 5
let r4 = ..<5        // PartialRangeUpTo<Int>: matches anything < 5
let r5 = 5...        // PartialRangeFrom<Int>: matches anything >= 5

let arr = [10, 20, 30, 40, 50]
print(arr[2...])     // [30, 40, 50] — from index 2 to the end
print(arr[..<2])     // [10, 20] — up to but not including index 2
print(arr[1...3])    // [20, 30, 40] — inclusive of both ends
```

- `1...5` — **closed range**, includes both endpoints. Used for `for` loops and `switch` ranges.
- `1..<5` — **half-open range**, includes the start, excludes the end. The most common form for array indexing because arrays have indices `0..<count`.
- `...5` — **one-sided (partial) range** matching anything up to and including 5. Useful for `switch` cases like `case ...50:` (matches any number 50 or below).
- `..<5` — one-sided range matching anything strictly less than 5.
- `5...` — one-sided range matching anything 5 or greater. Useful for `switch` cases like `case 100...:` (matches 100 and above).
- `arr[2...]` — array slice from index 2 to the end. The result is `ArraySlice<Int>`, not `Array<Int>` — slices share storage with the original array.
- `arr[..<2]` — array slice up to (but not including) index 2.
- `arr[1...3]` — array slice from index 1 to 3 inclusive.

Range operators are one of Swift's most-loved features — they make `for` loops and array slicing read almost like mathematics.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: FizzBuzz with `switch`

Write a `for` loop from 1 to 30 that prints "Fizz" if the number is divisible by 3, "Buzz" if divisible by 5, "FizzBuzz" if divisible by both, and the number otherwise. Use a `switch` statement on a tuple `(n % 3, n % 5)` with pattern matching — this is the idiomatic Swift way to solve FizzBuzz. Hint: `case (0, 0): print("FizzBuzz")` handles both divisors.

### Exercise 2: Range Slicing

Declare `let arr = [10, 20, 30, 40, 50, 60, 70]`. Use range subscripts to print: (a) the first three elements (`arr[..<3]`), (b) the last three elements (`arr[4...]`), (c) the middle two elements (`arr[2...3]`). Confirm the output. Try `arr[3..<5]` and notice you get `[40, 50]` — half-open range, excludes index 5.

### Exercise 3: Labeled `break`

Write nested `for` loops over `i in 1...5` and `j in 1...5`. Use a labeled `break` to exit both loops as soon as you find a pair where `i * j == 12`. Print the pair you found (`Found: i=X, j=Y`). Without the label, `break` would only exit the inner loop — confirm this by first writing it without a label and seeing the loop continue.

---

## Common Mistakes

### Mistake 1: Forgetting `default` in `switch`

```swift
// WRONG — switch on Int must be exhaustive
let day = 3
switch day {
case 1: print("Monday")
case 2: print("Tuesday")
// ERROR — switch must be exhaustive, consider adding a default clause
}
```

```swift
// RIGHT — add a default case
let day = 3
switch day {
case 1: print("Monday")
case 2: print("Tuesday")
default: print("Some other day")
}
```

Swift's `switch` must be exhaustive — every possible value of the switched type must be covered. For an `Int` with 2^64 values, you cannot list them all, so you must have a `default` case. For an `enum` with a fixed set of cases, you must handle each case (or have a `default`). The compiler error is `switch must be exhaustive`. This is a deliberate safety feature: it ensures that when a new case is added to an enum later, the compiler forces you to handle it in every `switch` rather than silently falling through.

### Mistake 2: Using `switch` where `if` would be clearer

```swift
// AWKWARD — switch with a single boolean condition
let isLoggedIn = true
switch isLoggedIn {
case true:
    print("Welcome")
case false:
    print("Please log in")
}
```

```swift
// RIGHT — use if/else for boolean conditions
let isLoggedIn = true
if isLoggedIn {
    print("Welcome")
} else {
    print("Please log in")
}
```

`switch` is designed for *pattern matching* — matching against specific values, ranges, tuples, or enum cases. For a plain boolean condition, `if`/`else` is clearer. Use `switch` when you are matching against multiple possible values of an integer, string, enum, or tuple. Use `if` when you are evaluating boolean conditions. Mixing the two — using `switch true { case condition1: ... }` — is technically legal but considered an anti-pattern.

### Mistake 3: Expecting fall-through between `switch` cases

```swift
// WRONG — C/Java habit; Swift cases do NOT fall through
let n = 1
switch n {
case 1:
    print("one")
    // No break needed — Swift stops here automatically
case 2:
    print("two")   // This does NOT run when n == 1
default:
    print("other")
}
```

```swift
// If you DO want fall-through, write "fallthrough" explicitly
let n = 1
switch n {
case 1:
    print("one")
    fallthrough       // explicitly fall into case 2
case 2:
    print("two")      // Now this DOES run when n == 1
default:
    print("other")
}
```

In C, C++, and Java, `switch` cases fall through by default — execution continues into the next case unless you write `break`. This caused countless bugs. Swift inverts this: cases do *not* fall through by default. Each case's body ends after its last statement. If you actually want fall-through behavior, you must write `fallthrough` explicitly. In practice, `fallthrough` is rarely used — most Swift developers never write it.

### Mistake 4: Using `...` when you want `..<`

```swift
// BUG — using closed range when iterating an array
let arr = [10, 20, 30]
for i in 0...arr.count - 1 {   // WORKS but awkward, off-by-one prone
    print(arr[i])
}

// CRASH — closed range includes arr.count, which is out of bounds
for i in 0...arr.count {
    print(arr[i])   // EXC_BAD_ACCESS when i == 3
}
```

```swift
// RIGHT — use half-open range 0..<arr.count
let arr = [10, 20, 30]
for i in 0..<arr.count {
    print(arr[i])
}

// EVEN BETTER — iterate the array directly
for value in arr {
    print(value)
}
```

This is the classic off-by-one error. `0...arr.count` includes `arr.count` itself, which is one past the last valid index (`arr.count - 1`). Accessing `arr[arr.count]` is a runtime crash. The fix is `0..<arr.count` (half-open, excludes `arr.count`). Even better, skip the index entirely and use `for value in arr` to iterate the array directly — only use `for i in 0..<arr.count` when you genuinely need the index (e.g., to access two arrays in parallel). For indexed iteration, `for (i, value) in arr.enumerated()` is even cleaner.

---

## Summary

- `if`/`else if`/`else` requires **braces** (no single-line `if` without them) and the condition must be a **`Bool`** — no truthiness. `if 0` and `if "non-empty"` do not compile.
- Starting in **Swift 5.9**, `if` can be used as an expression: `let color = if isDark { "black" } else { "white" }`. The ternary `?:` still works for short expressions.
- **`switch` must be exhaustive** — you must handle every possible value (or have `default`). The compiler enforces this.
- **No implicit fall-through** in Swift's `switch` — each case's body ends after its last statement. Write `fallthrough` explicitly if you actually want fall-through.
- `switch` supports **pattern matching** on values (`case 1:`), multiple values (`case 6, 7:`), ranges (`case 80..<90:`), tuples (`case (0, 0):`), wildcards (`case (_, 0):`), and bound values with guards (`case let (x, y) where x == y:`).
- `for i in 1...5` uses a **closed range** (includes 5). `for i in 1..<5` uses a **half-open range** (excludes 5). Use `stride(from:to:by:)` for custom steps.
- Iterate arrays with `for x in arr`, get indices with `for (i, x) in arr.enumerated()`, iterate strings with `for char in "string"`.
- `while cond { ... }` checks first; `repeat { ... } while cond` runs first (Swift's name for `do-while`). `break` exits the innermost loop, `continue` skips to the next iteration. Labeled loops (`outerLoop: for ...`) let you `break outerLoop` to exit nested loops.
- Range operators: `1...5` (closed), `1..<5` (half-open), `...5`, `..<5`, `5...` (one-sided). Array slicing uses these: `arr[2...]`, `arr[..<2]`, `arr[1...3]`.

You now have all the control flow tools you need. In Lesson 05 we cover Swift functions: the `func` keyword, **argument labels** (a feature unique to Swift), default values, variadic parameters, `inout` parameters, function types, closures, trailing closure syntax, and throwing functions with `throws`/`try`/`catch`.

---

**Next:** [Lesson 05: Functions →](./05-functions.md)
