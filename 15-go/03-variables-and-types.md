# Lesson 03: Variables and Types

> Module: Go · Lesson 3 of 7
> Estimated time: 30–45 minutes

In Lesson 02 you learned how Go projects are organized. Now we move to the language itself. In this lesson you will learn the three ways to declare a variable in Go, the concept of zero values (Go's answer to "uninitialized variables"), the basic types Go ships with, the strict rules Go applies to type conversion, and how constants work. By the end, you will be able to read and write any variable declaration in idiomatic Go.

If you are coming from Python, the biggest mental shift is that Go is statically typed — every variable has a type, and that type cannot change. If you are coming from Rust, Go will feel familiar but more permissive: Go has a garbage collector, so pointers are less scary, and there is no borrow checker arguing with you about ownership.

---

## Learning Objectives

After this lesson, you will be able to:

1. Use all three forms of variable declaration in Go — `var x int = 5`, `var x = 5`, and `x := 5` — and know when each is appropriate.
2. Explain Go's **zero values** — what `int`, `string`, `bool`, and pointers default to when you do not initialize them — and why this is a safety feature.
3. Distinguish between Go's numeric types (`int`, `int8`..`int64`, `uint` variants, `float32`, `float64`) plus `bool`, `string`, `byte`, and `rune`.
4. Perform **explicit type conversion** — because Go has no implicit type promotion, unlike C or Java.
5. Use `const` and `iota` to declare constants and enumerated values, and use pointers safely without arithmetic.

---

## 1. Variable Declaration

Go gives you **three forms** of variable declaration. They look different but they all do the same thing: bind a name to a value of a specific type. Knowing when to use which is a matter of idiomatic style.

### Form 1: `var x int = 5` — full form

```go
package main

import "fmt"

func main() {
    var x int = 5
    fmt.Println(x)
}
```

- `var` — the keyword that declares a variable.
- `x` — the variable's name.
- `int` — the variable's type, written explicitly.
- `= 5` — the initial value.
- `;` — not needed; Go does not require semicolons at the end of statements (the inserter handles it automatically).

Use the full form when you want to be explicit about the type — usually because the inferred type would not match your intent (e.g. you want `int32` instead of `int`).

### Form 2: `var x = 5` — type inferred

```go
package main

import "fmt"

func main() {
    var x = 5
    var y = 3.14
    var z = "Gopher"
    fmt.Println(x, y, z)
}
```

Here you omit the type, and Go infers it from the value. `5` becomes `int`, `3.14` becomes `float64`, `"Gopher"` becomes `string`. This form is useful at package level (outside any function) where the short form below is not allowed.

### Form 3: `x := 5` — short declaration, inside functions only

```go
package main

import "fmt"

func main() {
    x := 5
    y := 3.14
    z := "Gopher"
    fmt.Println(x, y, z)
}
```

The `:=` operator (colon-equals) is the **short declaration form**. It declares a new variable, infers the type, and assigns the value all in one step. The catch: **it only works inside a function**. You cannot use `:=` at the package level. This is the most common form you will see in Go code — use it inside `func main()` and every other function, and use `var` for package-level declarations.

Here is the rule of thumb: **inside a function, use `:=`; outside a function, use `var`.** The exception is when you already declared a variable and want to assign to it again — then use plain `=`:

```go
package main

import "fmt"

func main() {
    x := 5
    fmt.Println(x) // 5
    x = 10
    fmt.Println(x) // 10
}
```

If you wrote `x := 10` on the third line, Go would complain that `no new variables on left side of :=`. The fix is to use `=` (plain assignment) instead of `:=` (declaration + assignment).

You can also declare multiple variables at once, which is useful for swapping or for receiving multiple return values (Lesson 05):

```go
package main

import "fmt"

func main() {
    a, b := 3, 4
    fmt.Println(a, b) // 3 4
    a, b = b, a       // swap without a temp variable
    fmt.Println(a, b) // 4 3
}
```

---

## 2. Zero Values

In C or C++, declaring `int x;` without initializing it leaves `x` containing whatever garbage happened to be in that memory location — a famous source of security bugs. Go refuses to allow this. **Every variable in Go is initialized to its type's zero value automatically**, even if you do not write `= ...`. This is called the **zero value** and it is one of Go's most important safety features.

The zero values for the common types are:

| Type | Zero value |
|------|------------|
| `int`, `int64`, `uint`, etc. | `0` |
| `float64`, `float32` | `0.0` |
| `bool` | `false` |
| `string` | `""` (empty string) |
| pointer (`*int`, `*Person`, etc.) | `nil` |
| slice, map, channel, function, interface | `nil` |

Here is the zero value in action:

```go
package main

import "fmt"

func main() {
    var x int
    var s string
    var b bool
    var p *int
    fmt.Printf("x: %d, s: %q, b: %t, p: %v\n", x, s, b, p)
}
```

Output:

```text
x: 0, s: "", b: false, p: <nil>
```

Notice that we did not initialize any of these variables, yet they all have predictable values. `x` is `0` (not random garbage), `s` is the empty string `""` (not `null` and not a pointer to nowhere), `b` is `false` (not undefined), and `p` (a pointer to `int`) is `nil` (Go's equivalent of `null`, but only for pointers, interfaces, slices, maps, channels, and functions — never for `int` or `string`).

The zero value is more than a convenience — it is a **compile-time safety guarantee**. Because every variable starts in a known state, Go programs never have "use of uninitialized variable" bugs. You can write `var count int` and immediately call `count++` without worrying what `count` started at. This single design choice eliminates an entire class of bugs that plague C, C++, and even (in subtler ways) Java codebases.

---

## 3. Basic Types

Go has a rich set of built-in types. Here is the complete list of types you will use most often, grouped by category.

### Integer types

Go gives you signed and unsigned integers in four widths:

| Type | Range | Notes |
|------|-------|-------|
| `int8` | -128 to 127 | 1 byte, signed |
| `int16` | -32768 to 32767 | 2 bytes, signed |
| `int32` | -2^31 to 2^31-1 | 4 bytes, signed; same as `rune` |
| `int64` | -2^63 to 2^63-1 | 8 bytes, signed |
| `uint8` | 0 to 255 | 1 byte, unsigned; same as `byte` |
| `uint16` | 0 to 65535 | 2 bytes, unsigned |
| `uint32` | 0 to 2^32-1 | 4 bytes, unsigned |
| `uint64` | 0 to 2^64-1 | 8 bytes, unsigned |
| `int` | platform-dependent (32 or 64 bits) | the default; use this unless you have a reason |
| `uint` | platform-dependent (32 or 64 bits) | unsigned version of `int` |

When you write `var x = 5`, Go gives you an `int` (the default signed integer), which is either 32 or 64 bits depending on your platform. For almost all application code, `int` is the right choice — use the sized variants (`int32`, `int64`) only when you have a specific reason, like a network protocol that requires exactly 4 bytes.

### Floating-point types

Go has two float types: `float32` and `float64`. Both follow IEEE 754. The default when you write `3.14` is `float64` (double precision), which is what you want almost always — `float32` exists for graphics code and embedded systems where memory is tight.

```go
package main

import "fmt"

func main() {
    var f float64 = 3.14
    g := 2.718
    fmt.Printf("f: %f, g: %f\n", f, g)
}
```

### Boolean

The `bool` type has two values: `true` and `false`. There is no truthiness in Go — you cannot write `if x { ... }` where `x` is an integer (unlike C or Python). The condition must be exactly `bool`. We will revisit this in Lesson 04.

### String

A Go `string` is a **read-only sequence of UTF-8 bytes**. Strings are immutable — you cannot change a character in place. To modify a string, you build a new one. The length of a string in bytes is `len(s)`; to get the number of Unicode characters (runes), use `utf8.RuneCountInString(s)` or convert to a `[]rune` first.

```go
package main

import "fmt"

func main() {
    s := "Hello, World!"
    fmt.Println(s)         // Hello, World!
    fmt.Println(len(s))    // 13 (number of bytes)
    fmt.Println(s[0])      // 72 (byte value of 'H', NOT 'H' itself)
}
```

Note that `s[0]` returns the **byte value** `72`, not the character `'H'`. This trips up many beginners — we will revisit it in the Common Mistakes section.

### `byte` and `rune`

Go has two type aliases that exist for readability:

- `byte` is an alias for `uint8`. Use it when you are working with raw bytes (file I/O, network data).
- `rune` is an alias for `int32`. Use it to represent a **Unicode code point** — a single character. The name comes from the Old Norse word for a letter or symbol.

```go
package main

import "fmt"

func main() {
    var b byte = 'A'      // b is uint8 with value 65
    var r rune = '世'     // r is int32 with value 19990
    fmt.Printf("b: %d, r: %d\n", b, r)
}
```

### Pointers

A pointer holds the **memory address** of another variable. You declare a pointer with `*` and get a pointer with `&`:

```go
package main

import "fmt"

func main() {
    x := 42
    p := &x          // p is *int, holds the address of x
    fmt.Println(*p)  // 42 (dereference: read the value at that address)
    *p = 100         // change the value at that address
    fmt.Println(x)   // 100 (x was changed through the pointer)
}
```

Unlike C, **Go has no pointer arithmetic** — you cannot write `p++` to walk to the next memory address. This is another safety decision: pointers in Go cannot accidentally point past the end of an array. We will look at pointers more carefully in Section 6.

---

## 4. Type Conversion

Go has **no implicit type conversion**. If you have an `int` and need a `float64`, you must explicitly convert it. This is one of the strictest aspects of Go's type system and it is a deliberate design choice: implicit conversions are a famous source of bugs in C and C++.

```go
package main

import "fmt"

func main() {
    var i int = 5
    var f float64 = float64(i)   // explicit conversion: int to float64
    var j int = int(f)           // explicit conversion: float64 to int (truncates)
    fmt.Println(i, f, j)         // 5 5 5
}
```

The syntax is `TargetType(value)` — you write the target type like a function call, with the value in parentheses. Notice that even converting `int` to `float64` (which is mathematically lossless) requires an explicit conversion. Go does not care whether the conversion loses information — it just refuses to do any conversion implicitly.

This strictness catches real bugs. Consider:

```go
package main

import "fmt"

func main() {
    var count int32 = 100
    var total int64 = 1000
    // This would be a compile error:
    // average := total / count   // mismatched types int64 and int32
    average := total / int64(count)
    fmt.Println(average)
}
```

In C, `total / count` would silently promote `count` to `int64` and work — sometimes correctly, sometimes not. Go refuses to compile this; you must write `int64(count)` to say "yes, I really want this conversion." The same rule applies to numeric types of the same width: `int32` and `int` are not interchangeable even on a 32-bit platform.

For strings, conversion is also explicit. To convert an `int` to a string, you do not get the digits — you get the Unicode character with that code point, which is usually not what you want:

```go
package main

import (
    "fmt"
    "strconv"
)

func main() {
    n := 65
    fmt.Println(string(n))                // "A" (Unicode code point 65 = 'A')
    fmt.Println(strconv.Itoa(n))          // "65" (the digits, as text)
}
```

Use `strconv.Itoa(n)` (integer to ASCII) to convert an `int` to its decimal string representation. The bare `string(n)` form is almost never what you want — it converts the int to a Unicode code point. We will revisit this in Common Mistakes below.

---

## 5. Constants

A **constant** is a value that cannot change after it is declared. Go constants can be numbers, strings, or booleans, and they are declared with the `const` keyword:

```go
package main

import "fmt"

const Pi = 3.14159
const Greeting = "Hello, World!"

func main() {
    fmt.Println(Pi, Greeting)
}
```

Constants are evaluated at compile time, not runtime. This means they can be used in places where variables cannot — for example, as the size of an array:

```go
const Size = 10
var arr [Size]int   // OK because Size is a const
```

Constants also support **untyped values**. When you write `const Pi = 3.14159`, Go gives `Pi` an "untyped float" kind, which means it can be used as a `float32` or a `float64` depending on context. This is the only place Go relaxes its strict typing rules, and it is convenient for mathematical constants.

### `iota` — enumerated constants

Go does not have an `enum` keyword. Instead, it uses `const` blocks and a special identifier called **`iota`** to generate sequential values. `iota` starts at `0` in each `const` block and increments by 1 for each line:

```go
package main

import "fmt"

type Weekday int

const (
    Sunday Weekday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)

func main() {
    fmt.Println(Sunday, Monday, Saturday)   // 0 1 6
}
```

- `const (...)` — a const block, where each line gets the next value.
- `Sunday Weekday = iota` — Sunday is type `Weekday` and value `0` (iota starts at 0).
- `Monday` — Monday is type `Weekday` and value `1` (iota incremented).
- ... and so on. Each subsequent line takes the previous line's type and increments iota.

This is the idiomatic Go way to write what other languages call an enum. The convention is to define a custom type (here, `Weekday`) and make the constants of that type, which gives you type safety — you cannot accidentally pass a `Weekday` to a function that expects an `int`.

---

## 6. Pointers

A pointer is a variable that holds the **memory address** of another variable. Go has pointers, but unlike C it has no pointer arithmetic — you cannot write `p++` to move to the next memory slot. This makes Go pointers much safer than C pointers, at the cost of some flexibility.

You get a pointer with `&` (address-of) and read the value it points to with `*` (dereference):

```go
package main

import "fmt"

func main() {
    x := 42
    p := &x              // p is *int, holds address of x
    fmt.Println(p)       // 0xc000012028 (memory address, varies)
    fmt.Println(*p)      // 42 (read the value through the pointer)

    *p = 100              // write through the pointer
    fmt.Println(x)       // 100 — x changed!
}
```

The most common use of pointers is in function arguments, so a function can **modify the caller's variable**. We will cover this in detail in Lesson 05 and Lesson 06 (with struct receivers), but here is the basic idea:

```go
package main

import "fmt"

func increment(n *int) {
    *n = *n + 1
}

func main() {
    count := 5
    increment(&count)
    fmt.Println(count)   // 6 — increment modified our variable
}
```

If `increment` had taken `n int` (a value, not a pointer), Go would have copied `count` into a new local variable and modified only the copy — `count` itself would not change. Passing a pointer is the way to say "this function may modify my variable."

The zero value of a pointer is **`nil`** — a pointer that does not point to anything. Dereferencing a `nil` pointer crashes the program with a panic, so always check before dereferencing:

```go
package main

import "fmt"

func main() {
    var p *int   // p is nil
    if p != nil {
        fmt.Println(*p)
    } else {
        fmt.Println("p is nil, cannot dereference")
    }
}
```

Go also has a built-in function called **`new`** that allocates a zero-value of any type and returns a pointer to it. It is rarely used — you will see `&Person{}` more often than `new(Person)` — but it is good to recognize:

```go
p := new(int)   // p is *int, *p is 0
*p = 42
fmt.Println(*p) // 42
```

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Explore Zero Values

Write a program that declares one variable of each of these types without initializing it: `int`, `float64`, `string`, `bool`, and `*int`. Print each one with `fmt.Printf` using the `%v` verb. Confirm that you see `0 0 "" false <nil>`. Now try assigning a value to each and printing again. The lesson here is that **Go never has uninitialized variables** — every variable starts in a known, safe state.

### Exercise 2: Type Conversion Practice

Write a program with one `int` (say, `7`), one `float64` (say, `2.5`), and one `string` (say, `"42"`). Convert the `int` to a `float64` and divide it by the existing `float64`. Convert the string `"42"` to an `int` using `strconv.Atoi`. Print each result. Notice that `strconv.Atoi` returns two values — the integer and an error — and we will cover that pattern in Lesson 05. For now, capture both with `n, err := strconv.Atoi("42")` and ignore `err` with `_` if you do not want to check it yet.

### Exercise 3: Build a Weekday Enumeration

Re-create the `Weekday` const block from Section 5. Add a function called `IsWeekend(day Weekday) bool` that returns `true` for `Sunday` and `Saturday`, `false` for the rest. Call this function with each day of the week and print the results. Notice how using a custom type (`Weekday`) makes the function signature self-documenting — readers know immediately what kind of value to pass in.

---

## Common Mistakes

### Mistake 1: Using `:=` outside a function

```go
// WRONG — short declaration is not allowed at package level
package main

import "fmt"

x := 5

func main() {
    fmt.Println(x)
}
```

```go
// RIGHT — use var at package level
package main

import "fmt"

var x = 5

func main() {
    fmt.Println(x)
}
```

The `:=` short declaration only works **inside a function body**. At the package level (outside any `func`), you must use `var`. The compiler stops with `syntax error: non-declaration statement outside function body`. Inside `func main()`, the same `x := 5` works perfectly. The reason for the restriction is that Go's parser uses `:=` to disambiguate statements from declarations, and that disambiguation only makes sense inside a function.

### Mistake 2: Comparing different numeric types

```go
// WRONG — int32 and int64 are different types, even on a 64-bit platform
package main

import "fmt"

func main() {
    var a int32 = 5
    var b int64 = 5
    if a == b {       // compile error
        fmt.Println("equal")
    }
}
```

```go
// RIGHT — convert one to the other's type explicitly
package main

import "fmt"

func main() {
    var a int32 = 5
    var b int64 = 5
    if int64(a) == b {
        fmt.Println("equal")
    }
}
```

Go does no implicit numeric conversion. `int32` and `int64` are different types, and even `int` and `int32` are different on a 32-bit platform (where they happen to be the same width). The compiler stops with `mismatched types int32 and int64`. The fix is always to convert one side: `int64(a) == b` or `a == int32(b)`. The same rule applies to `<`, `>`, `+`, `-`, and every other operator.

### Mistake 3: Indexing a string returns a byte, not a character

```go
// WRONG — expecting s[0] to be the first character 'H'
package main

import "fmt"

func main() {
    s := "Hello"
    fmt.Println(s[0])        // 72 (byte value, not 'H')
    fmt.Println(string(s[0])) // "H" (now it's a string)
}
```

```go
// RIGHT — convert to []rune to get Unicode code points
package main

import "fmt"

func main() {
    s := "Hello, 世界"
    runes := []rune(s)
    fmt.Println(runes[0])        // 72 (rune for 'H')
    fmt.Println(string(runes[7])) // "世" (the 8th rune, not the 8th byte)
}
```

Go strings are stored as UTF-8 bytes, and `s[i]` returns the **byte at position i**, not the i-th Unicode character. For ASCII strings (where every character is one byte), this distinction is invisible. But for any string containing non-ASCII characters (accented letters, Chinese, emoji), `s[7]` may give you the first byte of a multi-byte character, not the character itself. To work with characters, convert to `[]rune` first: `[]rune(s)` gives you a slice of Unicode code points that you can index safely.

### Mistake 4: Assigning `nil` to a non-pointer variable

```go
// WRONG — int cannot be nil; only pointers, interfaces, slices, maps, channels, and functions can
package main

import "fmt"

func main() {
    var x int = nil   // compile error
    fmt.Println(x)
}
```

```go
// RIGHT — use 0 for int, or use a pointer if you really need "no value"
package main

import "fmt"

func main() {
    var x int       // x is 0 (the zero value for int)
    var p *int      // p is nil (the zero value for *int)
    fmt.Println(x, p)
}
```

In Python, `None` can be assigned to anything. In Java, every object reference can be `null`. Go is stricter: only pointers, interfaces, slices, maps, channels, and functions can be `nil`. You cannot assign `nil` to an `int`, `float64`, `string`, `bool`, or `struct`. The compiler stops with `cannot use nil as type int value in variable declaration`. If you need to express "this integer has no value", use `*int` (a pointer) and check for `nil`, or use the new generic `option` pattern, or just use `0` and a separate `hasValue bool` flag.

---

## Summary

- Go has three variable declaration forms: `var x int = 5` (full), `var x = 5` (type inferred), and `x := 5` (short, inside functions only).
- Every Go variable is initialized to its type's **zero value** even if you do not assign one — `0` for numbers, `""` for strings, `false` for bools, `nil` for pointers.
- Integer types come in signed/unsigned and 8/16/32/64-bit variants; the default is `int` (platform-dependent).
- `float32` and `float64` are IEEE 754 floats; the default is `float64`. `bool` has only `true` and `false`.
- `string` is an immutable sequence of UTF-8 bytes; `s[i]` returns a byte, not a character.
- `byte` is an alias for `uint8`; `rune` is an alias for `int32` and represents a Unicode code point.
- Go has **no implicit type conversion** — `int` to `float64` requires `float64(i)`, even though it is mathematically lossless.
- Constants are declared with `const`; `iota` generates sequential values for enumerated constants (Go's replacement for `enum`).
- Pointers exist (`*int`, `&x`, `*p`) but there is **no pointer arithmetic** — Go pointers are safe by design.

You now understand Go's type system. In Lesson 04 we will look at control flow — Go's `if`, the surprising fact that `for` is the only loop, and `defer` for scheduling cleanup.

---

**Next:** [Lesson 04: Control Flow →](./04-control-flow.md)
