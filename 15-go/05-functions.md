# Lesson 05: Functions

> Module: Go · Lesson 5 of 7
> Estimated time: 30–45 minutes

In Lesson 04 you learned Go's control flow. Now we move to functions — and this is where Go truly shines. Go has a feature that few mainstream languages share: **multiple return values**. Combined with Go's `error` interface convention, this single feature completely changes how error handling works. There is no `try/catch`, no exceptions, no `Result<T, E>` type — just functions that return `(value, error)`.

By the end of this lesson, you will be writing idiomatic Go functions: multi-return, named returns, variadic arguments, and closures. You will also meet the mysterious `init()` function that runs before `main()`.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define functions with `func`, including the shorthand for shared parameter types.
2. Return multiple values from a function — Go's signature feature — and handle the `(value, error)` convention.
3. Use **named return values** and the "naked return" syntax.
4. Define **variadic functions** that accept any number of arguments (`sum(1, 2, 3, 4)`).
5. Write **closures** — anonymous functions that capture their surrounding variables.
6. Recognize and use the special **`init()` function** that runs before `main()`.

---

## 1. Defining Functions

Go functions are declared with the `func` keyword, followed by a name, parameters in parentheses, a return type, and a body in braces. The general form is:

```go
func name(param1 type1, param2 type2) returnType {
    // body
    return value
}
```

Here is a simple example:

```go
package main

import "fmt"

func add(a int, b int) int {
    return a + b
}

func main() {
    fmt.Println(add(3, 4)) // 7
}
```

- `func add` — declares a function called `add`.
- `(a int, b int)` — two parameters, both of type `int`.
- `int` after the parentheses — the return type.
- `return a + b` — returns the sum.

When **consecutive parameters share the same type**, you can write the type once at the end. So `func add(a int, b int) int` can be shortened to:

```go
func add(a, b int) int {
    return a + b
}
```

This is idiomatic Go — `gofmt` accepts both forms but the short form is preferred when consecutive params share a type. For functions with many parameters of the same type (like `func move(x, y, dx, dy int)`) this saves a lot of repetition.

A function with **no parameters** has empty parentheses. A function with **no return value** has no return type at all — Go infers it as "returns nothing":

```go
func greet() {
    fmt.Println("Hello!")
}
```

You call `greet()` like any other function — `greet()` — and you cannot capture a return value because there is none.

---

## 2. Multiple Return Values

This is Go's signature feature, and once you get used to it you will miss it in every other language. A Go function can return **multiple values**, separated by commas. The most common pattern is to return `(value, error)` — the value if everything went well, or an error if something went wrong.

```go
package main

import (
    "errors"
    "fmt"
)

func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("cannot divide by zero")
    }
    return a / b, nil
}

func main() {
    result, err := divide(10, 2)
    if err != nil {
        fmt.Println("error:", err)
        return
    }
    fmt.Println("result:", result) // result: 5

    _, err = divide(10, 0) // discard the result with _
    if err != nil {
        fmt.Println("error:", err) // error: cannot divide by zero
    }
}
```

- `func divide(a, b float64) (float64, error)` — the return type is `(float64, error)`, two values. The parentheses around the return types are how Go knows there is more than one return value.
- `return 0, errors.New("cannot divide by zero")` — returns two values: the zero `float64`, and an error created by `errors.New`. The convention is: if there is an error, return the zero value of the result type plus the error.
- `return a / b, nil` — the happy path. `nil` is the zero value of the `error` interface — it means "no error".
- `result, err := divide(10, 2)` — the caller captures both return values.
- `if err != nil { ... return }` — the idiomatic Go error-handling pattern. You see this everywhere — almost every Go function call is followed by an `if err != nil` check.
- `_, err = divide(10, 0)` — if you only care about the error and want to discard the value, use the **blank identifier `_`**. The blank identifier is a write-only placeholder that says "I know there is a value here but I am ignoring it."

This pattern — return `(value, error)`, check `if err != nil` — is so universal in Go that it has its own name: **"the err nil idiom."** It is the Go equivalent of Python's `try/except` or Rust's `Result<T, E>`. The advantage over exceptions is that error handling is **visible at the call site** — you cannot accidentally forget to handle an error because the compiler forces you to assign both return values. The disadvantage is verbosity: Go code is full of `if err != nil { return err }` blocks, which is the most common complaint about Go's style.

A function can return **more than two** values too, though this is less common:

```go
package main

import "fmt"

func swap(a, b int) (int, int) {
    return b, a
}

func main() {
    x, y := swap(3, 4)
    fmt.Println(x, y) // 4 3
}
```

`swap` returns two `int`s — no error involved. This is the idiomatic way to "swap two values" in Go, and it generalizes to any function that needs to return multiple pieces of information.

---

## 3. Named Return Values

Go lets you **name** your return values, which can make function signatures more self-documenting and allows the "naked return" syntax.

```go
package main

import "fmt"

func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return // naked return — uses the named return values
}

func main() {
    fmt.Println(split(17)) // 7 10
}
```

- `func split(sum int) (x, y int)` — the return values are named `x` and `y`, both of type `int`. They are declared as variables inside the function, initialized to their zero values (`0` and `0`).
- `x = sum * 4 / 9` — assign to the named return value `x` directly. No `var x int` needed — the names in the signature already declared them.
- `return` — the **naked return**. With named return values, you can write `return` with no arguments, and Go returns the current values of `x` and `y`.

Named returns are most useful for **documenting** what each return value means. Compare:

```go
func divide(a, b float64) (float64, error)
func divide(a, b float64) (result float64, err error)
```

The second form makes it explicit that the first return is the `result` and the second is an `err`. Go's documentation tooling uses these names in generated docs — when you call `go doc divide`, the named-return form produces clearer output.

Naked returns are **controversial** in the Go community. They are great for short functions where it is obvious what is being returned, but in longer functions they make it hard to track what value is actually being returned. The convention is: **use named returns for documentation, use naked returns only in short functions (under 10 lines or so).** For longer functions, write the explicit `return x, y` even when you have named returns.

---

## 4. Variadic Functions

A **variadic function** accepts any number of arguments of the same type. The most famous example is `fmt.Printf` — you can pass it `"name: %s, age: %d"` followed by `"Gopher"` and `5`, or any other combination. The `...` syntax marks a parameter as variadic:

```go
package main

import "fmt"

func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}

func main() {
    fmt.Println(sum(1, 2, 3))       // 6
    fmt.Println(sum(10, 20, 30, 40)) // 100
    fmt.Println(sum())              // 0
}
```

- `func sum(nums ...int) int` — `nums ...int` means "any number of `int` arguments." Inside the function, `nums` is a **slice** of `int` (`[]int`), and you can iterate over it with `range`.
- `sum(1, 2, 3)` — pass any number of `int`s, separated by commas.
- `sum()` — even zero arguments is valid; `nums` is an empty slice.

You can pass a slice to a variadic function by **spreading** it with `...`:

```go
package main

import "fmt"

func sum(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}

func main() {
    numbers := []int{1, 2, 3, 4, 5}
    fmt.Println(sum(numbers...)) // 15
}
```

- `sum(numbers...)` — the `...` after `numbers` spreads the slice into individual arguments, so it is equivalent to `sum(1, 2, 3, 4, 5)`.

The `...` syntax is also used in a different way (in **go commands** like `go run ./...` meaning "all packages"), which can confuse beginners. When `...` follows a slice value, it spreads. When `...` follows a type (`...int`), it declares a variadic parameter.

---

## 5. Closures

In Go, **functions are first-class values** — you can assign them to variables, pass them as arguments, and return them from other functions. A **closure** is an anonymous function that captures variables from its surrounding scope. Closures are how Go does functional-style programming.

```go
package main

import "fmt"

func main() {
    add := func(a, b int) int {
        return a + b
    }
    fmt.Println(add(3, 4)) // 7
}
```

- `add := func(a, b int) int { ... }` — assigns an anonymous function to the variable `add`. The function has no name of its own; the variable `add` is what you call.
- `add(3, 4)` — call the closure like any other function.

Closures really shine when they capture variables from the enclosing function:

```go
package main

import "fmt"

func makeCounter() func() int {
    count := 0
    return func() int {
        count++
        return count
    }
}

func main() {
    c := makeCounter()
    fmt.Println(c()) // 1
    fmt.Println(c()) // 2
    fmt.Println(c()) // 3
}
```

- `func makeCounter() func() int` — `makeCounter` returns a **function** that takes no arguments and returns an `int`. The return type of `makeCounter` is itself a function type: `func() int`.
- `count := 0` — local variable in `makeCounter`. Normally, when `makeCounter` returns, its local variables would be destroyed.
- `return func() int { count++; return count }` — the returned closure **captures** `count` by reference. Each call to the returned function increments the same `count`.
- `c := makeCounter()` — `c` is now a counter function. Each call increments its captured `count`.

This is a **closure**: a function that captures variables from where it was defined. Closures are commonly used for callbacks, event handlers, decorators, and iterators. Go's standard library uses them everywhere — for example, `http.HandleFunc` takes a closure as its second argument, and `sort.Slice` takes a closure that compares two elements.

A more practical example: deferred cleanup using closures. We saw `defer f.Close()` in Lesson 04 — but if you need to do more than just call a method, you can defer a closure:

```go
defer func() {
    if r := recover(); r != nil {
        fmt.Println("recovered from panic:", r)
    }
}()
```

This pattern catches panics in the surrounding function — `recover()` only works inside a deferred function. We will not cover panics in depth in this module, but the closure form of `defer` is what makes panic recovery possible.

---

## 6. `init` Functions

Go has a special function called **`init()`** that runs **automatically before `main()`**. You never call `init()` directly — the Go runtime calls it for you. Every package can have multiple `init()` functions, even in the same file, and they all run (in the order they are declared) when the package is loaded.

```go
package main

import "fmt"

var config map[string]string

func init() {
    fmt.Println("init running")
    config = map[string]string{
        "env":  "production",
        "port": "8080",
    }
}

func init() {
    fmt.Println("second init running")
}

func main() {
    fmt.Println("main running")
    fmt.Println("config:", config)
}
```

Output:

```text
init running
second init running
main running
config: map[env:production port:8080]
```

- `func init()` — special function name. No arguments, no return value. The Go runtime calls it automatically before `main()`.
- Multiple `init()` functions in the same file are allowed — they run in declaration order.
- `init()` is most often used to set up package-level state, register types with a central registry, validate configuration, or perform one-time setup that must happen before any other code in the package runs.

The execution order is: **package-level `var` declarations first, then `init()` functions, then `main()`.** This is why the example above prints `init running` before `main running`, even though `fmt.Println("main running")` is in `main()`. If `init()` modifies a package-level variable, `main()` sees the modified value.

In practice, you should use `init()` sparingly. Hidden initialization makes code harder to read — a reader looking at `main()` cannot easily tell that some setup happened first. Reserve `init()` for cases where you genuinely need to register something at package load time (like a database driver or a custom `flag` parser), and put most of your initialization code in `main()` itself, where it is visible.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Write a Multi-Return Function

Write a function called `minMax(nums ...int) (int, int, error)` that takes a variadic list of ints and returns the minimum, the maximum, and an error. If no arguments are passed, return `0, 0, errors.New("empty list")`. Call it with `minMax(3, 1, 4, 1, 5, 9, 2, 6)` and print both results and the error. Then call it with no arguments and confirm the error path is hit.

### Exercise 2: Use a Closure as a Counter

Write a function called `makeAdder(base int) func(int) int` that returns a closure. The closure takes an `int` and returns `base + that int`. Use it like this: `addTen := makeAdder(10); fmt.Println(addTen(5))` — which should print `15`. Confirm that each closure you create has its own private `base` — `addTen` and `addHundred` should not interfere with each other.

### Exercise 3: Build an `init()` Function

Write a program with a package-level variable called `greeting string`. Initialize it to `"uninitialized"` at declaration. Add an `init()` function that sets `greeting = "Hello from init"`. In `main()`, print `greeting`. Run the program and confirm the output — `greeting` should be `"Hello from init"`, proving that `init()` ran before `main()`. Then add a second `init()` function to the same file and confirm both run, in declaration order.

---

## Common Mistakes

### Mistake 1: Ignoring the returned error with `_`

```go
// WRONG — discarding the error means you cannot tell if it failed
package main

import (
    "fmt"
    "strconv"
)

func main() {
    n, _ := strconv.Atoi("not a number")
    fmt.Println(n + 1) // prints 1, but the conversion failed
}
```

```go
// RIGHT — always check err before using the result
package main

import (
    "fmt"
    "strconv"
)

func main() {
    n, err := strconv.Atoi("not a number")
    if err != nil {
        fmt.Println("conversion failed:", err)
        return
    }
    fmt.Println(n + 1)
}
```

Using `_` to discard an error is the Go equivalent of catching an exception and ignoring it — it hides bugs. `strconv.Atoi("not a number")` returns `(0, err)`, and if you discard the error you get `0` and silently continue, which produces wrong results downstream. Always check `if err != nil` before using the result. The linter `errcheck` (and `go vet` in some cases) will flag `_`-discarded errors — most projects run these in CI to enforce the rule.

### Mistake 2: Wrong number of return values

```go
// WRONG — divide returns two values, but we tried to capture one
package main

import "fmt"

func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("divide by zero")
    }
    return a / b, nil
}

func main() {
    result := divide(10, 2) // compile error: assignment mismatch: 1 variable but divide returns 2 values
    fmt.Println(result)
}
```

```go
// RIGHT — capture both, or use _ to discard the error
package main

import "fmt"

func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("divide by zero")
    }
    return a / b, nil
}

func main() {
    result, _ := divide(10, 2)
    fmt.Println(result)
}
```

If a function returns multiple values, the caller must capture (or discard) all of them. You cannot write `result := divide(10, 2)` because `divide` returns two values — Go refuses to silently pick one. The compiler stops with `assignment mismatch: 1 variable but divide returns 2 values`. Either capture both (`result, err := divide(...)`) or discard the unwanted one with `_` (`result, _ := divide(...)`).

### Mistake 3: Treating `error` as a string

```go
// WRONG — error is an interface, not a string
package main

import (
    "errors"
    "fmt"
)

func main() {
    err := errors.New("file not found")
    if err == "file not found" { // compile error: cannot compare err == string
        fmt.Println("matched")
    }
}
```

```go
// RIGHT — compare errors with errors.Is, or compare with nil
package main

import (
    "errors"
    "fmt"
)

var ErrNotFound = errors.New("file not found")

func main() {
    err := ErrNotFound
    if errors.Is(err, ErrNotFound) {
        fmt.Println("matched")
    }
}
```

`error` is an **interface** in Go, not a string. It has a single method `Error() string`, but it is not a string itself. You cannot compare an `error` to a string with `==` — the compiler stops with `mismatched types error and untyped string`. To check what error you have, use `errors.Is(err, someSentinel)` (for sentinel errors like `io.EOF`) or `errors.As(err, &target)` (for typed errors that wrap a struct). The most common check is simply `err != nil`, which is true whenever there is an error.

### Mistake 4: Forgetting that `defer` runs in LIFO order

```go
// WRONG — assuming defers run in declaration order, not LIFO
package main

import "fmt"

func main() {
    defer fmt.Println("open database")
    defer fmt.Println("open transaction")
    defer fmt.Println("open cursor")
    fmt.Println("querying...")
}
```

```text
Actual output:
querying...
open cursor
open transaction
open database
```

```go
// RIGHT — defers run LIFO (last-in, first-out), so order your cleanups accordingly
package main

import "fmt"

func main() {
    defer fmt.Println("close database")  // runs last
    defer fmt.Println("close transaction") // runs middle
    defer fmt.Println("close cursor")    // runs first
    fmt.Println("querying...")
}
```

When you defer multiple calls, they run in **reverse order** of how they were declared. The first defer is the last one to run; the last defer is the first to run. This is the right behavior for cleanup: if you open database, then transaction, then cursor, you want to close them in reverse order (cursor first, then transaction, then database). The mistake is to declare defers in the same order as the closes — that would close the database before the cursor, which is wrong. Write your defers in the order you would want them to *run if they were not deferred*, and the LIFO execution will do the right thing.

---

## Summary

- Functions are declared with `func name(params) returnType { ... }`. Consecutive params of the same type can share: `func add(a, b int) int`.
- Go's signature feature is **multiple return values** — most commonly `(value, error)`.
- The idiom `if err != nil { return err }` is Go's replacement for `try/catch`. Almost every Go function call that returns an error is followed by this check.
- Use the blank identifier `_` to discard a return value you do not need, but never use it on an error you should be checking.
- **Named return values** (`func f() (x, y int)`) document what each return means and allow the "naked return" (`return` with no arguments).
- **Variadic functions** use `...int` to accept any number of arguments; inside the function, the parameter is a slice.
- Pass a slice to a variadic function by spreading it: `f(slice...)`.
- **Closures** are anonymous functions that capture variables from their enclosing scope — used for callbacks, counters, and deferred cleanups.
- **`init()` functions** run automatically before `main()`, in declaration order. Use them sparingly for package-level setup.

You now understand Go's function model. In Lesson 06 we will look at structs and interfaces — Go's answer to "how do I organize data and behavior?" without classes or inheritance.

---

**Next:** [Lesson 06: Structs and Interfaces →](./06-structs-and-interfaces.md)
