# Lesson 05: Functions

> Module: Swift · Lesson 5 of 7
> Estimated time: 30–45 minutes

In Lesson 04 you learned Swift's control flow: `if`, `switch`, `for`, `while`, and labeled loops. In this lesson we cover functions — the workhorses of every Swift program. Swift functions have a distinctive feature that few other mainstream languages share: **argument labels**. Every parameter has both an "argument label" (used by the caller) and a "parameter name" (used in the function body), and the two can differ. This sounds exotic at first, but it produces API call sites that read almost like English: `greet(person: "Hieu", withGreeting: "Hello")`.

We will cover function definitions, argument labels, default values, variadic parameters, `inout` parameters (Swift's way to mutate caller variables by reference), function types (functions as values), closures (anonymous functions with trailing closure syntax), and throwing functions (`throws`/`try`/`catch` — Swift's error handling model). By the end you will be writing idiomatic Swift functions that feel natural to call.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define a Swift function with `func`, parameters, return type, and argument labels.
2. Use default values, variadic parameters, and `inout` parameters correctly.
3. Treat functions as values with function types, and write closures with the trailing closure syntax.
4. Define throwing functions with `throws`, call them with `try`, and handle errors with `do-catch`.

---

## 1. Defining Functions

A Swift function is defined with the `func` keyword, followed by a name, parameter list, return type, and body:

```swift
func add(_ a: Int, _ b: Int) -> Int {
    return a + b
}

let sum = add(3, 4)        // 7
print(sum)
```

Line by line:

- `func` — the keyword that declares a function. Swift uses `func` (the same keyword Go uses). Every function starts with `func`.
- `add` — the function name.
- `(_ a: Int, _ b: Int)` — the parameter list. Each parameter has an optional argument label, a parameter name, and a type. Here, `_` means "no argument label" — the caller passes values positionally. `a` and `b` are the internal parameter names used in the body.
- `-> Int` — the return type. The arrow `->` separates the parameter list from the return type. Use `-> Void` or omit the arrow entirely for a function that returns nothing.
- `return a + b` — returns the sum. In single-expression functions, you can omit `return`: `func add(_ a: Int, _ b: Int) -> Int { a + b }` is equivalent.
- `let sum = add(3, 4)` — calls the function. Because we used `_` for argument labels, the caller writes `add(3, 4)` (no labels). Result: `7`.

For a function that returns nothing, omit the return type or write `-> Void`:

```swift
func greet(name: String) {
    print("Hello, \(name)!")
}

greet(name: "Hieu")   // Hello, Hieu!
```

- `func greet(name: String)` — no `-> ReturnType`, so the function returns `Void`. The `name` parameter uses `name` as *both* the argument label and the internal parameter name (when you write only one identifier, it serves both roles).
- `greet(name: "Hieu")` — the caller writes `name:` because that is the argument label.

---

## 2. Parameter Labels

This is Swift's distinctive feature. Every parameter can have **two names**: an *argument label* (external, used by the caller) and a *parameter name* (internal, used in the body). There are three forms:

### Form 1: Single name — used as both label and parameter name

```swift
func greet(name: String) {
    print("Hello, \(name)!")   // body uses "name"
}
greet(name: "Hieu")            // caller writes "name:"
```

- `name: String` — when you write only one identifier, it serves as both the argument label (external) and the parameter name (internal). The caller writes `greet(name: "Hieu")` and the body uses `name`. This is the default behavior.

### Form 2: Underscore `_` — no argument label

```swift
func greet(_ name: String) {
    print("Hello, \(name)!")   // body uses "name"
}
greet("Hieu")                  // caller writes nothing
```

- `_ name: String` — `_` means "no argument label." The caller passes the value positionally: `greet("Hieu")`. The body still uses `name`. Use `_` for unlabeled parameters where the meaning is obvious from context — `add(_ a: Int, _ b: Int)` is clearer than `add(a: 3, b: 4)` because `3` and `4` are obviously the two operands.

### Form 3: Two names — explicit argument label and parameter name

```swift
func greet(person name: String) {
    print("Hello, \(name)!")   // body uses "name"
}
greet(person: "Hieu")          // caller writes "person:"
```

- `person name: String` — `person` is the argument label (external), `name` is the parameter name (internal). The caller writes `greet(person: "Hieu")` but the body uses `name`. Use this when the call site should read like English but the body needs a shorter internal name.

Why does Swift do this? Because it produces **APIs that read like English sentences**. Consider:

```swift
// Reads naturally at the call site:
db.delete(row: 42, from: "users")
arr.insert(item, at: 0)
view.backgroundColor = .red
```

- `db.delete(row: 42, from: "users")` — reads as "delete row 42 from users."
- `arr.insert(item, at: 0)` — reads as "insert item at index 0." The first parameter has `_` (no label) because "insert item" is self-explanatory; the second has `at:` because "insert at 0" reads better than "insert index 0."
- This naming is enforced by Apple's API Design Guidelines, which all of Apple's frameworks follow.

The default convention is: **use the parameter name as the label, except for the first parameter, which gets `_`** if its meaning is clear from the function name. For example, `add(_ a: Int, _ b: Int)` uses `_` for both because the function name `add` makes it clear. `insert(_ item: Element, at index: Int)` uses `_` for the item but `at:` for the index because the position matters.

---

## 3. Default Values

Swift lets you specify **default values** for parameters. Parameters with defaults can be omitted by the caller:

```swift
func greet(name: String = "World") {
    print("Hello, \(name)!")
}

greet(name: "Hieu")   // Hello, Hieu!
greet()               // Hello, World!  (uses default)
```

- `func greet(name: String = "World")` — `name` has a default value of `"World"`. The `= "World"` syntax assigns the default.
- `greet(name: "Hieu")` — overrides the default with `"Hieu"`. Prints `Hello, Hieu!`.
- `greet()` — no arguments; `name` falls back to its default `"World"`. Prints `Hello, World!`.

Parameters with defaults must come *after* parameters without defaults. This is the same rule as Python and most other languages with default arguments:

```swift
// OK — required first, default second
func makeURL(host: String, port: Int = 80, path: String = "/") -> String {
    return "http://\(host):\(port)\(path)"
}

makeURL(host: "localhost")                       // http://localhost:80/
makeURL(host: "localhost", port: 8080)           // http://localhost:8080/
makeURL(host: "localhost", port: 8080, path: "/api")  // http://localhost:8080/api

// ERROR — default parameter cannot precede required parameter
// func bad(port: Int = 80, host: String) -> String { ... }
```

- `func makeURL(host: String, port: Int = 80, path: String = "/")` — required `host` first, then two parameters with defaults.
- The caller can omit `port` and `path`, or provide either one.
- Attempting to put `port: Int = 80` before `host: String` is a compile error.

---

## 4. Variadic Parameters

A **variadic parameter** accepts zero or more values of the same type. Inside the function body, the variadic parameter is an `Array`:

```swift
func sum(_ numbers: Int...) -> Int {
    var total = 0
    for n in numbers {
        total += n
    }
    return total
}

print(sum(1, 2, 3))          // 6
print(sum(10, 20, 30, 40))   // 100
print(sum())                 // 0
```

- `func sum(_ numbers: Int...) -> Int` — the `Int...` syntax declares a variadic parameter named `numbers`. Inside the function, `numbers` has type `[Int]` (an array).
- `for n in numbers { ... }` — iterates the array.
- `sum(1, 2, 3)` — passes three arguments. `numbers` is `[1, 2, 3]`.
- `sum(10, 20, 30, 40)` — passes four arguments. `numbers` is `[10, 20, 30, 40]`.
- `sum()` — passes zero arguments. `numbers` is `[]`.

A function can have at most one variadic parameter. Variadic parameters are convenient for "list-like" functions: `max(1, 5, 3, 7)`, `min(...)`, `print(...)` (Swift's built-in `print` is variadic — that's why you can write `print(a, b, c)`).

---

## 5. In-Out Parameters

Function parameters in Swift are **constants by default** — you cannot modify them inside the body. To mutate a parameter and have the change visible to the caller, mark it as `inout`:

```swift
func swapValues(_ a: inout Int, _ b: inout Int) {
    let temp = a
    a = b
    b = temp
}

var x = 5
var y = 10
swapValues(&x, &y)
print(x, y)   // 10 5
```

- `func swapValues(_ a: inout Int, _ b: inout Int)` — both parameters are `inout`. Inside the body, `a` and `b` are mutable, and any changes propagate back to the caller.
- `let temp = a; a = b; b = temp` — classic swap. Possible because `a` and `b` are `inout`.
- `swapValues(&x, &y)` — the caller passes `&x` and `&y`. The `&` is required for `inout` arguments, similar to C++ references.
- After the call, `x` is `10` and `y` is `5`.

Why `inout`? Because Swift is a **value type**-heavy language. Without `inout`, you would have to wrap the value in a class to mutate it across function calls — `inout` lets you do it directly with value types. Note that `inout` is *not* the same as passing by reference in C++ — it is a copy-in/copy-out mechanism, semantically. The compiler is free to optimize this to true pass-by-reference under the hood, but the language model is "copy in at the start, copy out at the end."

You can only pass *variables* (not constants or literals) as `inout` arguments:

```swift
let z = 100
// swapValues(&z, &y)   // ERROR — z is let, cannot be passed as inout
// swapValues(&5, &10)  // ERROR — literals cannot be passed as inout
```

---

## 6. Return Values and Tuples

Functions can return multiple values via tuples, which is one of Swift's nicest small features:

```swift
func minMax(_ array: [Int]) -> (min: Int, max: Int)? {
    guard !array.isEmpty else { return nil }
    var lo = array[0]
    var hi = array[0]
    for n in array {
        if n < lo { lo = n }
        if n > hi { hi = n }
    }
    return (lo, hi)
}

if let result = minMax([3, 1, 4, 1, 5, 9, 2, 6]) {
    print(result.min, result.max)   // 1 9
}
```

- `func minMax(_ array: [Int]) -> (min: Int, max: Int)?` — returns an *optional* named tuple `(min: Int, max: Int)?`. The `?` means the function might return `nil` (e.g., for an empty array).
- `guard !array.isEmpty else { return nil }` — early exit if the array is empty. We cover `guard` more in Lesson 06; for now just know it returns early.
- `return (lo, hi)` — returns a named tuple.
- `if let result = minMax([...])` — *optional binding*. If the function returned a tuple, `result` is unwrapped and the body runs. If the function returned `nil`, the body is skipped. Lesson 06 covers optionals in depth.

The caller accesses the named tuple elements with `.min` and `.max` — much clearer than `.0` and `.1`.

---

## 7. Function Types

Every Swift function has a **type** — `(ParameterTypes) -> ReturnType`. Function types are first-class: you can assign them to variables, pass them as arguments, and return them from other functions:

```swift
func add(_ a: Int, _ b: Int) -> Int { a + b }
func multiply(_ a: Int, _ b: Int) -> Int { a * b }

let op: (Int, Int) -> Int = add
print(op(3, 4))   // 7

op = multiply
print(op(3, 4))   // 12
```

- `func add(_ a: Int, _ b: Int) -> Int { a + b }` — defines a function `add` of type `(Int, Int) -> Int`.
- `func multiply(_ a: Int, _ b: Int) -> Int { a * b }` — same type, different body.
- `let op: (Int, Int) -> Int = add` — declares `op` as a variable of function type `(Int, Int) -> Int`, initialized to `add`. Functions are first-class values.
- `op(3, 4)` — calls `op` like a function. Since `op` currently points at `add`, the result is `7`.
- `op = multiply` — reassign `op` to `multiply`. Function types are values.
- `op(3, 4)` — now returns `12`.

**Higher-order functions** take functions as arguments or return them:

```swift
func apply(_ op: (Int, Int) -> Int, to a: Int, and b: Int) -> Int {
    return op(a, b)
}

print(apply(add, to: 3, and: 4))         // 7
print(apply(multiply, to: 3, and: 4))   // 12
```

- `func apply(_ op: (Int, Int) -> Int, to a: Int, and b: Int) -> Int` — the first parameter `op` is itself a function of type `(Int, Int) -> Int`. The other parameters `a` and `b` are the values to pass to it.
- `apply(add, to: 3, and: 4)` — passes the `add` function (by name, no parentheses) and the values `3` and `4`.

This is how `map`, `filter`, `reduce`, and `sorted` work — they all take a function (often a closure) as a parameter.

---

## 8. Closures

A **closure** is an anonymous function — a block of code you can pass around without naming it. Closures in Swift have several forms, ranging from fully explicit to extremely terse:

```swift
// Full form
let add1 = { (a: Int, b: Int) -> Int in
    return a + b
}

// Type inferred from context
let add2: (Int, Int) -> Int = { a, b in
    return a + b
}

// Single-expression: omit "return"
let add3: (Int, Int) -> Int = { a, b in a + b }

// Shorthand argument names: $0, $1, ...
let add4: (Int, Int) -> Int = { $0 + $1 }

print(add1(3, 4), add2(3, 4), add3(3, 4), add4(3, 4))   // 7 7 7 7
```

Line by line:

- `let add1 = { (a: Int, b: Int) -> Int in return a + b }` — full form. The closure's parameters `(a: Int, b: Int)` and return type `-> Int` are written inside the braces. The `in` keyword separates the parameter/return signature from the body.
- `let add2: (Int, Int) -> Int = { a, b in return a + b }` — when the closure is assigned to a variable with a known function type, the parameter types and return type can be omitted.
- `let add3: (Int, Int) -> Int = { a, b in a + b }` — for single-expression closures, you can omit the `return` keyword.
- `let add4: (Int, Int) -> Int = { $0 + $1 }` — shorthand argument names. `$0` is the first parameter, `$1` is the second, and so on. The `in` keyword is omitted because there are no named parameters. This is the most idiomatic form for short closures.

**Trailing closure syntax** — if a function's last parameter is a closure, you can write the closure *after* the function call's parentheses, which makes the code read more naturally:

```swift
func run(_ task: () -> Void) {
    print("Starting...")
    task()
    print("Done.")
}

// Standard form
run({ print("Working...") })

// Trailing closure
run {
    print("Working...")
}
```

- `func run(_ task: () -> Void)` — takes a closure of type `() -> Void` (no parameters, returns nothing).
- `run({ print("Working...") })` — standard form: closure inside parentheses.
- `run { print("Working...") }` — trailing closure: parentheses omitted, closure written after `run`. When the only argument is a trailing closure, you can omit the parentheses entirely.

This is what powers Swift's collection methods:

```swift
let numbers = [1, 2, 3, 4, 5]

let doubled = numbers.map { $0 * 2 }            // [2, 4, 6, 8, 10]
let evens = numbers.filter { $0 % 2 == 0 }      // [2, 4]
let sum = numbers.reduce(0) { $0 + $1 }          // 15
```

- `numbers.map { $0 * 2 }` — `map` takes a transform closure. With trailing closure syntax and shorthand argument names, this reads almost like English: "map each element x to x times 2."
- `numbers.filter { $0 % 2 == 0 }` — keep elements where the predicate is true.
- `numbers.reduce(0) { $0 + $1 }` — combine all elements with `+`, starting from `0`. `$0` is the accumulator, `$1` is the current element.

Closures **capture** values from their surrounding scope:

```swift
func makeCounter() -> () -> Int {
    var count = 0
    return {
        count += 1
        return count
    }
}

let counter = makeCounter()
print(counter())   // 1
print(counter())   // 2
print(counter())   // 3
```

- `func makeCounter() -> () -> Int` — returns a closure of type `() -> Int`.
- `var count = 0` — a local variable. Normally this would be deallocated when `makeCounter` returns.
- `return { count += 1; return count }` — the returned closure *captures* `count` by reference. Each call to the closure mutates the same `count`.
- `counter()` — each call increments and returns the captured `count`. This is a classic "closure capturing" example, similar to JavaScript.

---

## 9. Throwing Functions

Swift's error handling model uses **errors as values**, not exceptions for control flow. A function that can fail is marked with `throws`, and callers must use `try`:

```swift
enum ParseError: Error {
    case emptyInput
    case invalidCharacter(Character)
}

func parseInteger(_ s: String) throws -> Int {
    guard !s.isEmpty else {
        throw ParseError.emptyInput
    }
    for ch in s {
        if !ch.isNumber {
            throw ParseError.invalidCharacter(ch)
        }
    }
    return Int(s) ?? 0
}

do {
    let n = try parseInteger("42")
    print(n)   // 42
} catch ParseError.emptyInput {
    print("Error: input was empty")
} catch ParseError.invalidCharacter(let ch) {
    print("Error: invalid character \(ch)")
} catch {
    print("Unknown error: \(error)")
}
```

Line by line:

- `enum ParseError: Error { ... }` — defines an error enum conforming to the `Error` protocol. Each case is a distinct error. You can attach associated values like `invalidCharacter(Character)` to carry context.
- `func parseInteger(_ s: String) throws -> Int` — the `throws` keyword (between the parameters and return type) marks this function as able to throw an error.
- `guard !s.isEmpty else { throw ParseError.emptyInput }` — early exit if input is empty. `throw` raises the error.
- `for ch in s { if !ch.isNumber { throw ParseError.invalidCharacter(ch) } }` — iterate characters; throw if any is not a digit.
- `return Int(s) ?? 0` — convert string to `Int`. Returns `0` if conversion fails (should not happen after our check, but the `??` handles it).
- `do { let n = try parseInteger("42") }` — `do` opens a try-block. `try` is required before any call to a throwing function.
- `catch ParseError.emptyInput { ... }` — catches the specific case.
- `catch ParseError.invalidCharacter(let ch) { ... }` — catches the case and binds the associated value to `ch`.
- `catch { ... }` — the catch-all. The error is bound to the implicit `error` variable.

This is conceptually similar to Java's checked exceptions, but cleaner: errors are *values* (enum cases), the `throw`/`try`/`catch` keywords are reserved for error handling only, and Swift's ARC means there is no expensive stack unwinding. We explore error handling more in Lesson 06.

For convenience, Swift has `try?` (converts error to `nil`) and `try!` (crashes on error, like forced unwrap):

```swift
let a = try? parseInteger("42")    // Optional(42)
let b = try? parseInteger("")      // nil
let c = try! parseInteger("42")    // 42 (would crash if parse failed)
```

- `try?` returns an `Optional` — the value if successful, `nil` if an error was thrown.
- `try!` force-unwraps — returns the value if successful, crashes the program if an error was thrown. Use only when you are absolutely certain the call cannot fail.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Argument Labels

Write a function `func format(price: Double, currency: String = "USD") -> String` that returns a string like `"$42.50 USD"`. The function should use argument labels: `format(price: 42.5, currency: "USD")` should return `"$42.50 USD"`. Then call it with only `price:` to use the default currency. Add a third parameter `decimalPlaces: Int = 2` and use `String(format:)` to format the price with the given precision. Try calling it with all three arguments and confirm the output.

### Exercise 2: Higher-Order Function

Write a function `func apply(_ op: (Int, Int) -> Int, to a: Int, and b: Int) -> Int` that takes a binary integer operation and applies it to two integers. Define three top-level functions `add`, `sub`, `mul`, and pass them to `apply`. Then rewrite the calls using inline closure syntax: `apply({ $0 + $1 }, to: 3, and: 4)`. Then try the trailing closure form — but note that trailing closures only work when the closure is the *last* argument, so this particular function needs the closure first to use trailing closure syntax.

### Exercise 3: Throwing Function

Write a `throws` function `func divide(_ a: Int, by b: Int) throws -> Int` that throws `DivisionError.divisionByZero` if `b == 0`. Define `enum DivisionError: Error { case divisionByZero }`. Call it with `try` inside a `do { ... } catch { ... }` block — handle the error case by printing "Cannot divide by zero." Try `try?` and `try!` variants and observe the difference: `try?` returns `nil`, `try!` crashes if you pass `0`.

---

## Common Mistakes

### Mistake 1: Forgetting argument labels

```swift
// WRONG — Swift requires argument labels by default
func greet(name: String) {
    print("Hello, \(name)!")
}
greet("Hieu")   // ERROR — missing argument label 'name:'
```

```swift
// RIGHT — include the label
func greet(name: String) {
    print("Hello, \(name)!")
}
greet(name: "Hieu")
```

If you are coming from Python, JavaScript, Java, or C# (where positional arguments are the norm), this trips you up. Swift requires the argument label by default. The compiler error is `missing argument label 'name:' in call`. If you genuinely want positional arguments, write `_` as the argument label: `func greet(_ name: String)`, then `greet("Hieu")` works. But Swift's convention is to *keep* the labels — they make call sites more readable.

### Mistake 2: Using the parameter name as the label when an external label would be clearer

```swift
// WORKS — but the call site reads awkwardly
func remove(item: String, index: Int) { ... }
remove(item: "x", index: 3)   // "remove item x at index 3" — does the index refer to item or to the array?
```

```swift
// RIGHT — explicit argument labels read like English
func remove(_ item: String, at index: Int) { ... }
remove("x", at: 3)   // "remove x at 3" — clear
```

Swift's API Design Guidelines recommend that the *first* parameter have `_` (no label) when the function name already mentions it (e.g., `remove(item:at:)` is verbose; `remove(_:at:)` reads better). The *second* and later parameters should have labels that, combined with the function name, form a phrase: `remove(item, at: index)`. Apple's frameworks follow this convention religiously — that's why you see `arr.insert(item, at: 0)` and `dict.removeValue(forKey: key)` rather than `arr.insert(item: item, index: 0)`.

### Mistake 3: Trying to mutate a parameter without `inout`

```swift
// WRONG — function parameters are constants by default
func increment(_ n: Int) {
    n += 1   // ERROR — cannot assign to value: 'n' is a 'let' constant
}
```

```swift
// RIGHT — mark the parameter as inout and pass with &
func increment(_ n: inout Int) {
    n += 1
}

var x = 5
increment(&x)
print(x)   // 6
```

Function parameters in Swift are *constants* by default — you cannot modify them inside the body. This is a deliberate safety feature: it prevents accidental mutation of values that the caller did not expect to be changed. If you genuinely need to mutate the caller's value (rare — usually you should return a new value instead), mark the parameter `inout` and pass it with `&` at the call site. The compiler error for forgetting `inout` is `cannot assign to value: 'n' is a 'let' constant`.

### Mistake 4: Forgetting `try` when calling a throwing function

```swift
// WRONG — must mark calls to throwing functions with try
func parseInteger(_ s: String) throws -> Int { ... }

let n = parseInteger("42")   // ERROR — call can throw but is not marked with try
```

```swift
// RIGHT — use try inside a do-catch, or try? or try!
do {
    let n = try parseInteger("42")
    print(n)
} catch {
    print("Failed: \(error)")
}
```

Swift forces you to acknowledge that a function can throw. The compiler error for forgetting `try` is `call can throw but is not marked with 'try'`. The fix is to wrap the call in a `do { try ... } catch { ... }` block, or use `try?` (returns `nil` on error) or `try!` (crashes on error). This is similar to Java's checked exceptions, but the syntax is cleaner and the runtime cost is lower — Swift's `throws` is implemented as a hidden return value, not stack unwinding.

---

## Summary

- Swift functions are declared with `func name(params) -> ReturnType { body }`. Single-expression functions can omit `return`.
- **Argument labels** are Swift's distinctive feature. Every parameter has both an argument label (external, used by the caller) and a parameter name (internal, used in the body). Three forms: single name (`name:`), underscore (`_`), or two names (`external internal:`).
- **Default values**: `func greet(name: String = "World")`. Default parameters must come after required ones.
- **Variadic parameters** (`Int...`) accept zero or more values; inside the body they are arrays. At most one variadic per function.
- **`inout` parameters** let you mutate the caller's variable. Mark with `inout` and pass with `&` at the call site. Parameters are constants by default.
- Functions can return tuples (and optionals of tuples) for multi-value returns: `-> (min: Int, max: Int)?`.
- **Function types** like `(Int, Int) -> Int` are first-class — you can assign, pass, and return them.
- **Closures** are anonymous functions. Forms: full `{ (a: Int, b: Int) -> Int in return a + b }`, inferred `{ a, b in a + b }`, shorthand `{ $0 + $1 }`. **Trailing closure syntax** writes the closure after the function call's parentheses when it's the last argument.
- **Throwing functions** are marked with `throws`. Callers must use `try` (in a `do-catch` block), `try?` (returns nil on error), or `try!` (crashes on error). Errors are values (enum cases conforming to `Error`), not exceptions for control flow.
- Swift's `print`, `map`, `filter`, `reduce`, `sorted` are all functions that take closures — closures are everywhere in Swift code.

You now understand Swift's function model. In Lesson 06 — the most important lesson in this module — we cover the heart of Swift: **structs vs classes** (value vs reference types), properties, initializers, inheritance, protocols, protocol-oriented programming, optionals in depth, error handling, and enums with associated values. Lesson 06 is the longest lesson — budget extra time.

---

**Next:** [Lesson 06: Structs and Classes →](./06-structs-and-classes.md)
