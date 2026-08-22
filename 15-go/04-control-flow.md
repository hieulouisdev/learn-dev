# Lesson 04: Control Flow

> Module: Go · Lesson 4 of 7
> Estimated time: 30–45 minutes

In Lesson 03 you learned Go's type system. Now we move to control flow — how Go decides what to run and how often. The headline surprises many beginners: **Go has only one loop keyword (`for`)**, no `while`, no `do-while`, no `foreach`. Go's `if` has no parentheses around the condition but does require braces. And Go's `switch` does not fall through by default, unlike C and Java.

This lesson is short on vocabulary but heavy on conventions. Take your time, type every example, and notice how Go removes features you might be used to — that removal is intentional and is part of what makes Go codebases easy to read.

---

## Learning Objectives

After this lesson, you will be able to:

1. Write `if`/`else`/`else if` in Go — without parentheses, with mandatory braces, and with the init-statement form.
2. Use `for` in all three of its forms: C-style, while-style, and infinite.
3. Iterate slices, arrays, maps, and strings with `for ... range`.
4. Use `switch` correctly — knowing that cases do **not** fall through by default and that `fallthrough` is opt-in.
5. Use `break`, `continue`, and labeled loops for nested control flow.
6. Use `defer` to schedule cleanup code that runs when a function returns.

---

## 1. `if` / `else`

Go's `if` looks like C's or Java's, with two important differences: **no parentheses** around the condition, and **mandatory braces** around the body.

```go
package main

import "fmt"

func main() {
    age := 18
    if age >= 18 {
        fmt.Println("Adult")
    } else if age >= 13 {
        fmt.Println("Teenager")
    } else {
        fmt.Println("Child")
    }
}
```

- `if age >= 18 {` — note there is no `(` before `age` and no `)` after `18`. Go's `gofmt` actually removes parentheses from `if` conditions if you add them.
- The `{` must be on the **same line** as the `if` keyword. If you put it on the next line, the compiler stops with `syntax error: unexpected semicolon or newline before {`. This is because Go automatically inserts semicolons at the end of lines ending in certain tokens, and an `if` keyword followed by a newline triggers that inserter.
- The `}` of the body must be on its own line, and `else` must be on the same line as the closing `}`. The same rule applies: a newline before `else` would trigger a semicolon insertion.

Go's `if` also supports an **init statement**, separated from the condition by a semicolon. This is unique to Go and is incredibly useful for limiting variable scope:

```go
package main

import "fmt"

func main() {
    if x := computeValue(); x > 10 {
        fmt.Println("big:", x)
    } else {
        fmt.Println("small:", x)
    }
    // x is NOT in scope here — it only existed inside the if/else
}

func computeValue() int {
    return 42
}
```

- `if x := computeValue(); x > 10 {` — the part before `;` is the init statement; it runs once, before the condition is evaluated.
- `x` is in scope **only inside the `if` and `else` blocks**. It is not accessible after the closing `}`.
- This pattern is idiomatic Go for "compute a value, then decide what to do with it" — it keeps the variable scoped to where it is used, which makes refactoring safer.

One important rule: **the condition must be exactly `bool`**. Go has no truthiness, unlike Python or JavaScript. You cannot write `if x { ... }` where `x` is an integer or a string — the compiler stops with `non-bool x (type int) used as if condition`. If you want "x is non-zero", write `if x != 0 { ... }`. If you want "s is non-empty", write `if s != "" { ... }`.

---

## 2. `for` is the Only Loop

Go has **one** loop keyword: `for`. There is no `while`, no `do-while`, no `foreach`. Whatever kind of loop you want, you write it with `for`. This sounds limiting until you realize that `for` does the job of all three.

### C-style `for`

```go
package main

import "fmt"

func main() {
    for i := 0; i < 5; i++ {
        fmt.Println("iteration", i)
    }
}
```

This is the C-style form: init (`i := 0`), condition (`i < 5`), post-step (`i++`). All three parts are optional, but the semicolons are required if you omit any part.

### While-style `for`

```go
package main

import "fmt"

func main() {
    n := 10
    for n > 0 {
        fmt.Println("counting down:", n)
        n--
    }
}
```

Drop the init and post-step, and you get a `while` loop. `for cond { ... }` is exactly `while (cond) { ... }` in C. Many Go developers use this form for "loop until something happens" loops.

### Infinite `for`

```go
package main

import "fmt"

func main() {
    i := 0
    for {
        fmt.Println("forever", i)
        i++
        if i >= 3 {
            break
        }
    }
}
```

Drop all three parts — `for { ... }` — and you get an infinite loop. You typically combine this with a `break` statement to exit when some condition is met (often an error from I/O, like end-of-file).

One subtle rule: **`++` and `--` are statements, not expressions** in Go. You cannot write `x = i++` or `arr[i++] = ...` like in C or Java. `i++` is a statement that increments `i` and returns nothing — it can only stand alone on a line. This removes a whole category of subtle bugs (the "undefined order of evaluation" bugs that plague C codebases), at the cost of a little expressiveness.

---

## 3. `range`

When you want to iterate over a slice, array, map, or string, Go gives you the `for ... range` form. It is the equivalent of Python's `for item in items` or JavaScript's `for...of`.

```go
package main

import "fmt"

func main() {
    fruits := []string{"apple", "banana", "cherry"}
    for i, fruit := range fruits {
        fmt.Printf("fruits[%d] = %s\n", i, fruit)
    }
}
```

- `for i, fruit := range fruits {` — `range` yields two values on each iteration: the **index** (here, `i`) and the **value** (here, `fruit`).
- `fruits` is the slice/array/map/string being iterated.
- The body of the loop runs once per element.

If you only need the value, you can skip the index with the **blank identifier** `_`:

```go
for _, fruit := range fruits {
    fmt.Println(fruit)
}
```

If you only need the index, omit the second variable entirely:

```go
for i := range fruits {
    fmt.Println(i)
}
```

`range` works on **maps** too, but the order of iteration is **random** (Go's runtime deliberately randomizes map iteration order to prevent you from depending on it). It also works on **strings** — and when ranging a string, you get **runes** (Unicode code points), not bytes, which is usually what you want:

```go
package main

import "fmt"

func main() {
    s := "Hello, 世界"
    for i, r := range s {
        fmt.Printf("byte-offset %d: %c (codepoint %d)\n", i, r, r)
    }
}
```

Notice that `i` is the **byte offset** of the rune, not its rune index — a multi-byte UTF-8 character takes up multiple bytes, so the offsets are not consecutive. If you need consecutive rune indices, convert the string to `[]rune` first.

---

## 4. `switch`

Go's `switch` looks like C's or Java's, but with one critical difference: **cases do not fall through by default**. Each case is its own branch — when one case matches, its body runs and the switch ends, without needing an explicit `break`.

```go
package main

import "fmt"

func main() {
    day := "Monday"
    switch day {
    case "Saturday", "Sunday":
        fmt.Println("Weekend!")
    case "Monday":
        fmt.Println("Start of work week")
    case "Friday":
        fmt.Println("Almost weekend")
    default:
        fmt.Println("Midweek")
    }
}
```

- `switch day {` — the value being matched. Unlike C, the value does not need to be an integer — it can be a string, a struct, anything comparable.
- `case "Saturday", "Sunday":` — multiple values separated by commas. This matches if `day` is `"Saturday"` OR `"Sunday"`.
- `default:` — the case that runs if no other case matches. Optional but recommended.
- **No `break` needed!** After matching a case, the switch ends automatically. This is the opposite of C and Java, where forgetting `break` causes fall-through bugs.

If you actually want fall-through behavior (rare, but sometimes useful), you must use the explicit `fallthrough` keyword:

```go
package main

import "fmt"

func main() {
    n := 2
    switch n {
    case 1:
        fmt.Println("one")
        fallthrough
    case 2:
        fmt.Println("two")
        fallthrough
    case 3:
        fmt.Println("three")
    }
}
```

This prints:

```text
two
three
```

`fallthrough` transfers control to the **next case body unconditionally**, regardless of what that case's value is. It is a "raw" fall-through — it does not re-test the next case's condition. Most Go code never uses `fallthrough`; it is included for completeness and for translating C code.

Go also supports **switch without a value** — a cleaner way to write long if/else chains:

```go
package main

import "fmt"

func main() {
    age := 25
    switch {
    case age < 13:
        fmt.Println("Child")
    case age < 20:
        fmt.Println("Teenager")
    case age < 65:
        fmt.Println("Adult")
    default:
        fmt.Println("Senior")
    }
}
```

Each case is a boolean expression; the first one that evaluates to `true` runs. This form is preferred over a long `if/else if/else if/else` chain because it is easier to read and easier to add cases to.

---

## 5. `break`, `continue`, and Labels

`break` exits the enclosing loop immediately. `continue` skips to the next iteration of the enclosing loop.

```go
package main

import "fmt"

func main() {
    for i := 0; i < 10; i++ {
        if i%2 == 0 {
            continue // skip even numbers
        }
        if i == 7 {
            break // stop the loop entirely
        }
        fmt.Println(i)
    }
}
```

This prints:

```text
1
3
5
```

When you have **nested loops**, `break` and `continue` only affect the innermost loop. To break out of an outer loop, Go supports **labels**:

```go
package main

import "fmt"

func main() {
Outer:
    for i := 0; i < 5; i++ {
        for j := 0; j < 5; j++ {
            if i*j >= 6 {
                fmt.Printf("breaking at i=%d, j=%d\n", i, j)
                break Outer
            }
        }
    }
    fmt.Println("done")
}
```

- `Outer:` — a label, declared before the outer loop. By convention, labels are capitalized (they are not exported — labels are not subject to the visibility rule — but capitalization is idiomatic).
- `break Outer` — break out of the loop labeled `Outer`, not just the inner loop.

Labels are uncommon in idiomatic Go — most nested loops are refactored into separate functions, where `return` does the job of `break Outer` more clearly. But for the cases where a function refactor would be awkward, labels work well.

---

## 6. `defer`

`defer` schedules a function call to run when the surrounding function returns — not immediately. It is Go's elegant solution to the "did I remember to close this file?" problem that plagues C, Python, and Java.

```go
package main

import (
    "fmt"
    "os"
)

func main() {
    f, err := os.Open("example.txt")
    if err != nil {
        fmt.Println("could not open file:", err)
        return
    }
    defer f.Close() // runs when main returns, no matter what

    // ... read from f ...
    fmt.Println("file opened successfully")
}
```

- `defer f.Close()` — schedules `f.Close()` to run when `main` returns.
- The deferred call runs **no matter how `main` returns** — whether it returns normally, returns early on an error, or panics. This means you can `return` from anywhere in the function and the file will still be closed.

When you defer multiple calls, they run in **LIFO (last-in, first-out) order**:

```go
package main

import "fmt"

func main() {
    defer fmt.Println("first deferred")
    defer fmt.Println("second deferred")
    defer fmt.Println("third deferred")
    fmt.Println("function body")
}
```

Output:

```text
function body
third deferred
second deferred
first deferred
```

The deferred calls run in reverse order of how they were declared. This is the right behavior for cleanup: if you open file A, then file B, then file C, you want to close them in the reverse order (C, B, A) — closing C before B is safer because B might have depended on C.

One important gotcha: **deferred calls evaluate their arguments at the time the `defer` statement runs**, not at the time the deferred call runs. This means:

```go
package main

import "fmt"

func main() {
    x := 10
    defer fmt.Println("deferred x:", x)
    x = 20
    fmt.Println("normal x:", x)
}
```

Output:

```text
normal x: 20
deferred x: 10
```

The deferred `fmt.Println("deferred x:", x)` captured the value of `x` at the time the `defer` statement ran (which was `10`), even though `x` later became `20`. If you want to defer a call that uses the latest value, wrap it in a closure:

```go
defer func() { fmt.Println("deferred x:", x) }()
```

Now `x` is read when the deferred function actually runs, not when the `defer` statement was scheduled.

`defer` is one of Go's most beloved features. It makes resource cleanup (files, locks, network connections, database transactions) impossible to forget. The pattern is: **the moment you acquire a resource, defer its release.** Everything after that can `return` freely without you having to remember to clean up.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Rewrite a `while` Loop as a Go `for`

Write a program that prints the numbers from 1 to 10 using a `for` loop in while-style (init the variable before the loop, check the condition in `for`, increment inside the body). Then write the same logic in C-style `for` (init, condition, post-step all inside `for`). Confirm both produce the same output. Notice that the C-style form is more concise for this case, but the while-style form is clearer when the loop variable is shared with code outside the loop.

### Exercise 2: Use `switch` Without a Value

Write a program that takes an integer score (0–100) and prints a letter grade: `A` for 90+, `B` for 80–89, `C` for 70–79, `D` for 60–69, `F` below 60. Use the **value-less `switch`** form (each case is a boolean expression). Then add a `default` case that handles scores outside the 0–100 range by printing `"invalid"`. Notice how the switch form reads more clearly than a long if/else if/else chain.

### Exercise 3: Practice `defer` and LIFO Order

Write a function called `cleanupDemo` that defers three `fmt.Println` calls (in order: "first", "second", "third"). After the defers, print "function body". Call this function from `main` and observe the output. Then modify the function to open a file (using `os.Create`) and defer `f.Close()` immediately — even if you do not write anything to the file, the deferred close ensures the file descriptor is released when the function returns.

---

## Common Mistakes

### Mistake 1: Using `while` (which does not exist)

```go
// WRONG — Go has no `while` keyword
package main

import "fmt"

func main() {
    i := 0
    while i < 5 {
        fmt.Println(i)
        i++
    }
}
```

```go
// RIGHT — use `for` with just a condition
package main

import "fmt"

func main() {
    i := 0
    for i < 5 {
        fmt.Println(i)
        i++
    }
}
```

Go has exactly one loop keyword: `for`. There is no `while`, no `do-while`, no `foreach`. If you write `while`, the compiler stops with `syntax error: unexpected while, expecting for`. The fix is to use `for cond { ... }` — this is the while-style form. Once you get used to it, having one keyword for all loops is simpler than remembering the difference between `while`, `do-while`, and `foreach` in C or Java.

### Mistake 2: Forgetting braces around the `if` body

```go
// WRONG — Go does not allow braces-less if bodies
package main

import "fmt"

func main() {
    x := 5
    if x > 0
        fmt.Println("positive")
}
```

```go
// RIGHT — braces are mandatory, and the { must be on the same line as `if`
package main

import "fmt"

func main() {
    x := 5
    if x > 0 {
        fmt.Println("positive")
    }
}
```

In Python, indentation alone defines blocks. In C and Java, you can write `if (x > 0) printf("positive");` with no braces — that's valid but error-prone (Apple's infamous `goto fail` SSL bug came from this). Go refuses both styles: braces are **mandatory**, even for one-line bodies, and the opening `{` must be on the **same line** as the `if` keyword. The compiler stops with `syntax error: unexpected semicolon or newline before {` if you put the brace on the next line. Always let `gofmt` format your code — it does the right thing automatically.

### Mistake 3: Expecting `switch` fall-through

```go
// WRONG — assuming cases fall through like in C
package main

import "fmt"

func main() {
    n := 2
    switch n {
    case 1:
        fmt.Println("one")
    case 2:
        fmt.Println("two")
    case 3:
        fmt.Println("three")
    }
}
```

In C and Java, this would print:

```text
two
three
```

...because `case 2` would fall through to `case 3` (no `break` between them). In Go, **cases do not fall through by default**. The above program prints only:

```text
two
```

If you actually want fall-through behavior, you must use the explicit `fallthrough` keyword at the end of the case body. Without `fallthrough`, each case is its own branch and the switch ends as soon as one case's body finishes running. This was a deliberate design choice — fall-through bugs in C are common, and Go removes them entirely.

### Mistake 4: Using `++` as an expression

```go
// WRONG — `i++` is a statement, not an expression
package main

import "fmt"

func main() {
    i := 0
    j := i++   // compile error: i++ is a statement, returns nothing
    fmt.Println(i, j)
}
```

```go
// RIGHT — increment first, then assign
package main

import "fmt"

func main() {
    i := 0
    i++
    j := i
    fmt.Println(i, j)
}
```

In C and Java, `i++` is an expression that returns the **old** value of `i` (and `++i` returns the **new** value). This makes `j = i++` valid C — it assigns the old value of `i` to `j`, then increments `i`. Go deliberately removed this: `i++` is a **statement**, not an expression. It increments `i` and returns nothing, so you cannot assign its result. The compiler stops with `syntax error: unexpected ++ at end of statement`. This removes the entire `i++` vs `++i` confusion and a class of subtle bugs that come from using increment as a side effect in a larger expression.

---

## Summary

- Go's `if` requires no parentheses around the condition, but mandatory braces, and the opening `{` must be on the same line as `if`.
- The init-statement form `if x := f(); x > 0 { ... }` scopes `x` to the if/else blocks only.
- The condition must be exactly `bool` — Go has no truthiness, unlike Python or JavaScript.
- `for` is Go's **only** loop keyword. It does C-style, while-style (`for cond { }`), and infinite (`for { }`).
- `for i, v := range xs` iterates slices, arrays, maps, and strings; use `_` to skip the index or value.
- `switch` does **not** fall through by default — each case is its own branch. Use `fallthrough` for explicit fall-through.
- The value-less `switch` form (`switch { case cond1: ... }`) is the idiomatic replacement for long if/else chains.
- `break` exits the innermost loop; labeled breaks (`break Outer`) exit outer loops.
- `defer` schedules a function call to run when the surrounding function returns, in LIFO order. Always pair resource acquisition with a `defer` cleanup.

You now understand Go's control flow. In Lesson 05 we will look at functions — including Go's signature feature, multiple return values, which makes error handling dramatically cleaner than in C, Java, or Python.

---

**Next:** [Lesson 05: Functions →](./05-functions.md)
