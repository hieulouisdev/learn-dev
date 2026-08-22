# Lesson 03: Variables and Types

> Module: Swift · Lesson 3 of 7
> Estimated time: 30–45 minutes

In Lesson 02 you wrote your first Swift programs and used string interpolation. In this lesson we slow down and look at the heart of every Swift program: **variables and types**. Swift is statically-typed, which means every variable has a type known at compile time. But Swift is also heavily type-inferred, which means you rarely have to write the type yourself. The result feels like Python's flexibility but with Rust's safety.

We will cover the single most important daily decision in Swift — `var` versus `let` — then move through type inference, the basic types (`Int`, `Double`, `Bool`, `String`, `Character`, tuples), numeric literals, type conversion (and why Swift refuses to mix `Int` and `Double` automatically), strings as value types, and type aliases. By the end you will understand why a Swift developer reaches for `let` by default and only uses `var` when they have to.

---

## Learning Objectives

After this lesson, you will be able to:

1. Choose correctly between **`var`** (mutable) and **`let`** (constant) in any situation, and explain why Swift defaults to `let`.
2. Read Swift's type inference and write explicit type annotations only when needed.
3. Use the basic types — `Int`, `Double`, `Float`, `Bool`, `String`, `Character`, tuples — and convert between them explicitly.
4. Explain why Swift strings are value types, why `.count` (not `.length`) is used, and why `s[0]` does not work the way you might expect.

---

## 1. `var` vs `let`

The single most important decision in Swift is whether to declare a variable with `var` or `let`. **`var` is mutable** — you can reassign it. **`let` is a constant** — once assigned, it cannot be changed. The rule is simple:

> **Default to `let`. Use `var` only when you genuinely need to mutate the value.**

```swift
let name = "Hieu"          // constant — name can never be reassigned
var score = 0              // variable — score can be changed later
score = 10                 // OK — score is var
// name = "Bob"            // ERROR — name is let
```

Line by line:

- `let name = "Hieu"` — declares `name` as a constant of type `String`, initialized to `"Hieu"`. The compiler infers the type. The `let` keyword tells the compiler "this binding will never be reassigned," and the compiler enforces it — any attempt to reassign `name` is a compile error.
- `var score = 0` — declares `score` as a variable of type `Int`, initialized to `0`. The `var` keyword means "this binding may be reassigned."
- `score = 10` — valid — `score` is `var`, so reassignment is allowed.
- `// name = "Bob"` — would be a compile error: `cannot assign to value: 'name' is a 'let' constant`. Uncomment to see.

This is the same distinction as Kotlin's `val`/`var`, Rust's `let`/`let mut`, JavaScript's `const`/`let`, and Java's `final`/non-`final`. Swift's design choice — and the Swift community enforces this strongly — is to **default to `let`** and only switch to `var` when you have a concrete reason to mutate. This produces safer code because immutability prevents an entire class of bugs: accidentally modifying a value you expected to stay the same.

There is one subtlety. `let` on a **value type** (struct — covered in Lesson 06) makes the entire value immutable — you cannot modify any field. But `let` on a **reference type** (class) only makes the *binding* constant — you cannot reassign the variable to point at a different instance, but you *can* mutate properties of the instance it points to. We revisit this in Lesson 06; for now just remember that the question "does `let` mean immutable?" has a yes-for-structs, partial-for-classes answer.

```swift
struct Player { var score: Int }
class Game { var score: Int = 0 }

let p = Player(score: 10)
// p.score = 20  // ERROR — p is let, structs are fully immutable

let g = Game()
g.score = 20     // OK — g is let but g is a class instance; mutating its property is allowed
// g = Game()   // ERROR — g itself is let, cannot be reassigned
```

- The struct `p` is fully immutable because `Player` is a struct (value type) and `p` is `let`.
- The class instance `g` allows property mutation because `Game` is a class (reference type). `let` only freezes the *binding*, not the object's properties.

---

## 2. Type Inference

Swift's compiler is good at inferring types. In most cases you do not need to write the type — just initialize the variable and the compiler figures it out:

```swift
let name = "Hieu"      // inferred: String
let age = 25           // inferred: Int
let pi = 3.14159       // inferred: Double (not Float — Double is the default)
let isReady = true     // inferred: Bool
let scores = [85, 92]  // inferred: [Int]
```

- `let name = "Hieu"` — string literal, so `name` is inferred as `String`.
- `let age = 25` — integer literal, so `age` is inferred as `Int` (Swift's default integer type).
- `let pi = 3.14159` — floating-point literal, so `pi` is inferred as `Double` (Swift's default floating-point type — *not* `Float`).
- `let isReady = true` — boolean literal, so `isReady` is inferred as `Bool`.
- `let scores = [85, 92]` — array literal of integers, so `scores` is inferred as `[Int]` (which is sugar for `Array<Int>`).

You can always write the type explicitly with the `:` syntax — useful for disambiguation, documentation, or when there is no initializer:

```swift
let age: Int = 25
let pi: Double = 3.14159
let name: String = "Hieu"
let isLoggedIn: Bool    // ERROR — let requires an initializer
var counter: Int        // OK — var without initializer (defaults to 0? No, actually this is also an error)
```

- `let age: Int = 25` — explicit type annotation. Useful when you want to make the type obvious to a reader.
- `var counter: Int` — Swift does *not* default-initialize variables the way Java does (Java would set `counter` to `0`). Swift requires every variable to be initialized before use; an uninitialized `var` is a compile error unless you assign it later in the same scope before reading.

The compiler also does **type inference across functions** — the return type of a function with a single-expression body is inferred, and generics resolve automatically:

```swift
func add(_ a: Int, _ b: Int) -> Int { a + b }   // return type annotated
let result = add(3, 4)                            // result inferred as Int
let numbers = [1, 2, 3].map { $0 * 2 }            // numbers inferred as [Int]
```

- `add` returns `Int` (annotated with `-> Int`). The body `a + b` has no `return` keyword — Swift allows single-expression functions to omit `return`.
- `result` is inferred as `Int` from `add`'s return type.
- `numbers` is inferred as `[Int]` — the closure `{ $0 * 2 }` returns `Int`, and `map` on `[Int]` returns `[Int]`. We cover `map` and closures in Lessons 05 and 07.

---

## 3. Basic Types

Swift has a small set of built-in types in the standard library:

| Type | Description | Example |
|------|-------------|---------|
| `Int` | Signed integer, platform-sized (64-bit on modern systems) | `42` |
| `Double` | 64-bit floating-point (default for `3.14`) | `3.14159` |
| `Float` | 32-bit floating-point (use only when storage matters) | `Float(3.14)` |
| `Bool` | Boolean — `true` or `false` | `true` |
| `String` | Unicode-compliant string (a struct, value type) | `"Hello"` |
| `Character` | A single Unicode extended grapheme cluster | `"A"` |
| `Tuples` | Grouped values `(Int, String)` | `(1, "two")` |

For integers, Swift also has fixed-width variants: `Int8`, `Int16`, `Int32`, `Int64` (signed) and `UInt8`, `UInt16`, `UInt32`, `UInt64`, `UInt` (unsigned). Use these only when you have a specific storage or protocol need — the default `Int` is platform-sized (64-bit on a 64-bit OS) and is recommended for general use. Apple's API Guidelines say: "Use `Int` even when the value is non-negative — using a typed unsigned integer like `UInt` to model a non-negative quantity can cause subtle bugs when an unsigned subtraction underflows."

For floating-point, `Double` is the default — every floating-point literal like `3.14` is a `Double`. Use `Float` only when you specifically need 32-bit storage (e.g., graphics buffers). The Swift API Guidelines say: "Always prefer `Double`."

`Bool` is `true` or `false` — two literal keywords, lowercase. There is no truthiness: `if 1` does not compile, `if "non-empty"` does not compile. Conditions in `if`, `while`, and ternary must be `Bool` typed. Lesson 04 covers this in detail.

`Character` represents a single **Unicode extended grapheme cluster** — which is a fancy way of saying "what a user thinks of as a single character." This handles combining marks, emoji with skin tones, and flags correctly. `"é"` is one `Character` whether it is encoded as a single Unicode scalar (`U+00E9`) or as `e` + combining accent (`U+0065` + `U+0301`). Emoji like `"👨‍👩‍👧"` (family emoji, made of multiple scalars joined with zero-width joiners) is also one `Character`.

```swift
let letter: Character = "A"
let emoji: Character = "🎉"
let family: Character = "👨‍👩‍👧"  // one Character, even though it is multiple scalars
```

- `let letter: Character = "A"` — a single ASCII character. The type annotation is needed because `"A"` would otherwise be inferred as `String`.
- `let emoji: Character = "🎉"` — emoji characters are valid `Character`s.
- `let family: Character = "👨‍👩‍👧"` — the family emoji is one `Character` even though it consists of multiple Unicode scalars joined with zero-width joiners. Swift handles this correctly.

---

## 4. Numbers

Swift supports several numeric literal formats with convenient readability features.

```swift
let million = 1_000_000        // underscores for readability — ignored by compiler
let hex = 0xFF                  // hexadecimal (255 in decimal)
let binary = 0b1010             // binary (10 in decimal)
let octal = 0o17               // octal (15 in decimal)
let exp = 1.5e10               // exponential (1.5 × 10^10 = 15,000,000,000)
let pi = 3.141_592_653          // underscores in floating-point literals too
```

Line by line:

- `let million = 1_000_000` — underscores in numeric literals are ignored by the compiler but make large numbers readable. You can put them anywhere: `1_000_000`, `100_00_00`, `1_0_0_0_0_0_0` all equal one million.
- `let hex = 0xFF` — hexadecimal literals start with `0x`. `0xFF` is 255 in decimal. Useful for colors, bitmask flags, and memory addresses.
- `let binary = 0b1010` — binary literals start with `0b`. `0b1010` is 10 in decimal. Useful for bit flags and low-level code.
- `let octal = 0o17` — octal literals start with `0o` (zero + lowercase o, not zero + zero). `0o17` is 15 in decimal. Octal is rare but supported.
- `let exp = 1.5e10` — exponential notation. `1.5e10` means 1.5 × 10^10 = 15,000,000,000. The result is a `Double`.
- `let pi = 3.141_592_653` — underscores work in floating-point literals too. Useful for long decimals.

The underscore-as-separator rule is identical to Java, Python, Rust, and JavaScript — Swift adopted the same syntax deliberately. Use it generously for any number with more than 4 digits: `let population = 8_000_000` is easier to read than `let population = 8000000`.

---

## 5. Type Safety and Conversion

Swift is **strict about type conversion** — much stricter than C, JavaScript, or Python. You cannot mix `Int` and `Double` in arithmetic without an explicit conversion. This is a deliberate design choice: implicit numeric conversions are a frequent source of bugs in C and C++ (loss of precision, surprising truncation), and Swift refuses to compile code where the conversion might be ambiguous.

```swift
let a: Int = 5
let b: Double = 2.5

// let sum = a + b           // ERROR — binary operator '+' cannot be applied to operands of type 'Int' and 'Double'
let sum = Double(a) + b       // OK — explicitly convert Int to Double
let truncated = Int(b)        // OK — converts Double to Int (truncates toward zero, so 2)
let rounded = Int(b.rounded())// OK — explicitly round first, then convert (2)
```

- `let a: Int = 5` — `a` is an `Int`.
- `let b: Double = 2.5` — `b` is a `Double`.
- `let sum = a + b` — **compile error**. Swift refuses to silently promote `Int` to `Double` (or vice versa). The error message is `binary operator '+' cannot be applied to operands of type 'Int' and 'Double'`.
- `let sum = Double(a) + b` — correct. `Double(a)` constructs a `Double` from the `Int` value `5`, giving `5.0`. Then `5.0 + 2.5 = 7.5`.
- `let truncated = Int(b)` — converts `2.5` to `Int` by truncating toward zero. Result: `2`. (Note: it does *not* round — `Int(2.9)` is `2`, not `3`.)
- `let rounded = Int(b.rounded())` — `b.rounded()` returns `3.0` (a `Double` rounded to the nearest integer), then `Int(...)` converts to `3`.

This strictness trips up developers coming from C, JavaScript, or Python, where `5 + 2.5` just works (and gives `7.5`). In Swift, the compiler forces you to be explicit about whether you want the result as `Int` or `Double`. The rule: **if you mix types, you must convert one of them.** This is annoying at first but it eliminates an entire class of precision-loss bugs.

```swift
let intDiv = 7 / 2            // 3 (integer division — both operands are Int)
let doubleDiv = 7.0 / 2.0     // 3.5 (floating-point division — both operands are Double)
let mixed = Double(7) / 2.0   // 3.5 (convert Int to Double, then divide)
```

- `let intDiv = 7 / 2` — `7` and `2` are both `Int` literals, so `/` is integer division. Result: `3`.
- `let doubleDiv = 7.0 / 2.0` — `7.0` and `2.0` are both `Double` literals, so `/` is floating-point division. Result: `3.5`.
- `let mixed = Double(7) / 2.0` — explicitly convert `Int` to `Double`, then divide by `Double`. Result: `3.5`. Without the `Double(...)`, this would not compile (can't mix `Int` and `Double`).

---

## 6. Strings

Swift's `String` type is a **value type** — a `struct`, not a class. When you assign a string to a new variable or pass it to a function, you get an *independent copy*, not a shared reference. This means mutating one string variable never affects another, which makes string code much easier to reason about.

```swift
let greeting = "Hello, World!"
print(greeting.count)               // 13 — Swift uses .count, NOT .length
print(greeting.uppercased())         // HELLO, WORLD!
print(greeting.lowercased())         // hello, world!
print(greeting.contains("World"))    // true
print(greeting.hasPrefix("Hello"))   // true
print(greeting.hasSuffix("!"))       // true
```

Line by line:

- `let greeting = "Hello, World!"` — a 13-character string.
- `greeting.count` — Swift uses `.count` for the length of a string. Java uses `.length()` (with parentheses, since it is a method). C# uses `.Length` (capital L). JavaScript uses `.length` (no parentheses). Python uses `len(...)`. Swift uses `.count`. **This is the most common Java/C# habit that breaks in Swift.**
- `greeting.uppercased()` — returns a new string with all characters uppercased. The original is unchanged (strings are immutable in Swift, like in most languages).
- `greeting.lowercased()` — returns a new lowercase string.
- `greeting.contains("World")` — returns `true` if the substring is found.
- `greeting.hasPrefix("Hello")` — returns `true` if the string starts with the given prefix.
- `greeting.hasSuffix("!")` — returns `true` if the string ends with the given suffix.

String concatenation uses `+`:

```swift
let first = "Hello"
let second = "World"
let combined = first + ", " + second + "!"   // "Hello, World!"
let interpolated = "\(first), \(second)!"     // "Hello, World!" — usually cleaner
```

- `first + ", " + second + "!"` — concatenation with `+`. Works but is verbose.
- `"\(first), \(second)!"` — interpolation. Cleaner, more readable, and faster (the compiler optimizes interpolation into a single string allocation).

Strings compare with `==` for *value* equality (since they are value types):

```swift
let s1 = "Hello"
let s2 = "Hello"
print(s1 == s2)   // true — value equality
```

- `s1 == s2` — returns `true`. Swift's `==` for strings compares values, not references (which makes sense because strings are value types).

**Important gotcha: Swift strings are not arrays of `Character`.** You cannot index into a string with `s[0]` — that does not compile. Instead you use `s.startIndex` and `s.index(after:)`:

```swift
let s = "Swift"
let firstChar = s[s.startIndex]                 // "S"
let secondChar = s[s.index(after: s.startIndex)] // "w"
let thirdIndex = s.index(s.startIndex, offsetBy: 2)
let thirdChar = s[thirdIndex]                  // "i"

// let ch = s[0]    // ERROR — Swift strings cannot be indexed with Int
```

- `s[s.startIndex]` — the first character. Every collection in Swift has a `startIndex`.
- `s[s.index(after: s.startIndex)]` — the character after the start. Swift string indices are abstract `String.Index` values, not integers, because Unicode characters have variable byte lengths.
- `s.index(s.startIndex, offsetBy: 2)` — an index 2 positions after the start. This is the closest thing to `s[2]` in other languages.
- `s[0]` — **compile error**. Swift strings cannot be indexed with `Int`. This trips up almost everyone coming from Python, JavaScript, Java, or C#.

Why is this so awkward? Because Swift strings are Unicode-correct. A character like `"é"` may take 1 or 2 bytes depending on encoding; an emoji like `"👨‍👩‍👧"` takes 11 bytes but is one `Character`. Integer indexing would be ambiguous (byte index? character index? UTF-16 code unit?). Swift forces you to be explicit by using opaque `String.Index` values. We will revisit this in Lesson 07's cheatsheet.

---

## 7. Booleans and Tuples

`Bool` is Swift's boolean type with two literal values: `true` and `false` (lowercase keywords, no truthiness):

```swift
let isLoggedIn: Bool = false
let hasPermission = true

if isLoggedIn {
    print("Welcome back!")
} else {
    print("Please log in.")
}
```

- `let isLoggedIn: Bool = false` — explicit `Bool` annotation. `false` is a literal keyword.
- `let hasPermission = true` — inferred as `Bool` from the `true` literal.
- The `if` condition must be a `Bool` — `if 1` or `if "non-empty"` do not compile (we cover this in Lesson 04).

**Tuples** group multiple values into a single compound value. The values can be of different types:

```swift
let pair: (String, Int) = ("Hieu", 25)
print(pair.0)   // "Hieu" — access by .0, .1, .2 ...
print(pair.1)   // 25

let unnamed = (true, "yes", 42)
print(unnamed.2)   // 42
```

- `let pair: (String, Int) = ("Hieu", 25)` — a tuple of type `(String, Int)`. The first element is accessed with `.0`, the second with `.1`, and so on.
- `print(pair.0)` — prints `"Hieu"`. The `.0` syntax feels awkward but it works.
- `let unnamed = (true, "yes", 42)` — inferred type is `(Bool, String, Int)`.
- `print(unnamed.2)` — prints `42`, the third element.

Unnamed tuple elements are hard to read. **Named tuples** are much better:

```swift
let person = (name: "Hieu", age: 25, city: "Hanoi")
print(person.name)    // "Hieu"
print(person.age)     // 25
print(person.city)    // "Hanoi"

// Destructure into separate constants
let (n, a, c) = person
print(n)              // "Hieu"
print(a)              // 25

// Ignore elements with _
let (name, _, _) = person
print(name)           // "Hieu"
```

- `let person = (name: "Hieu", age: 25, city: "Hanoi")` — a named tuple. The names (`name`, `age`, `city`) become properties you can access with dot syntax.
- `person.name` — much more readable than `person.0`.
- `let (n, a, c) = person` — destructuring. The tuple is broken into three separate constants.
- `let (name, _, _) = person` — `_` is a wildcard that ignores the value at that position. Useful when you only care about one element.

Tuples are great for **returning multiple values** from a function:

```swift
func minMax(_ arr: [Int]) -> (min: Int, max: Int) {
    var lo = arr[0]
    var hi = arr[0]
    for n in arr {
        if n < lo { lo = n }
        if n > hi { hi = n }
    }
    return (lo, hi)
}

let result = minMax([3, 1, 4, 1, 5, 9, 2, 6])
print(result.min, result.max)   // 1 9
```

- The function returns a named tuple `(min: Int, max: Int)`.
- The caller accesses the values with `.min` and `.max` — much clearer than indexing with `.0` and `.1`.

Tuples are value types, just like structs. They are best used for *small, temporary* groupings — for anything more substantial, define a `struct` (Lesson 06).

---

## 8. Type Aliases

Swift lets you create new names for existing types with `typealias`:

```swift
typealias ID = UInt64
typealias Score = Int
typealias Point = (x: Double, y: Double)

let userId: ID = 12345
let highScore: Score = 9500
let origin: Point = (x: 0, y: 0)
```

- `typealias ID = UInt64` — `ID` is now an alias for `UInt64`. The two are interchangeable; the compiler treats them as the same type.
- `typealias Score = Int` — `Score` is an alias for `Int`.
- `typealias Point = (x: Double, y: Double)` — `Point` is an alias for a named tuple type.
- `let userId: ID = 12345` — declares `userId` with type `ID` (which is `UInt64`).
- `let origin: Point = (x: 0, y: 0)` — declares `origin` with type `Point` (which is the named tuple).

`typealias` does not create a *new* type — it just gives an existing type a new name. This is useful for **semantic clarity** (so you can write `userId: ID` instead of `userId: UInt64`) and for **platform abstraction** (the standard library uses `typealias` to map `Int` to `Int32` or `Int64` depending on platform). It is also useful for **simplifying complex generic types**:

```swift
typealias StringDict = [String: String]
let config: StringDict = ["host": "localhost", "port": "8080"]
```

- `typealias StringDict = [String: String]` — `StringDict` is now a synonym for `[String: String]`.
- `let config: StringDict = [...]` — uses the alias.

Type aliases do not provide type safety — `ID` and `UInt64` are the same type, so you can accidentally pass a `Score` where an `ID` is expected and the compiler will not catch it. For type-safe wrappers, use a `struct` with a single property (the "newtype" pattern) instead.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: `var` vs `let` Audit

Open any small Swift file you wrote in Lessons 01 or 02. Audit every `var` and ask: "Does this need to be reassigned?" If the answer is no, change it to `let`. Notice that Xcode (or the Swift compiler with `-warn-long-expression-type-checking`) will warn you when a `var` could be a `let` — pay attention to those warnings. Aim to have 80%+ of your variables be `let`.

### Exercise 2: Type Conversion Practice

Write a Swift script that declares `let a: Int = 10` and `let b: Double = 3.5`. Try to compute `a + b` without explicit conversion — confirm it does not compile. Then fix it by converting `a` to `Double`. Then try to assign the result back to an `Int` (you will need to truncate) — write `let result: Int = Int(Double(a) + b)`. Print `result` and confirm it is `13` (10.0 + 3.5 = 13.5, truncated to 13).

### Exercise 3: Named Tuple

Define a named tuple `let book = (title: "The Swift Programming Language", author: "Apple", year: 2024)`. Print each field with `print(book.title)`, `print(book.author)`, `print(book.year)`. Then destructure it into three constants with `let (title, author, year) = book` and print them again. Then destructure again with `let (title, _, _) = book` to ignore `author` and `year`, and print only `title`.

---

## Common Mistakes

### Mistake 1: Using `var` where `let` would do

```swift
// WORKS — but Xcode will warn: "variable was never mutated; consider changing to let"
var name = "Hieu"
print(name)
```

```swift
// RIGHT — use let when you do not need to mutate
let name = "Hieu"
print(name)
```

Swift's style guide, Xcode's built-in analyzer, and Apple's linter (SwiftLint) all warn you when a `var` is never mutated and could be a `let`. Pay attention to these warnings — they are usually right. Default to `let` and only switch to `var` when you have a concrete reason to reassign. This makes your code safer (immutability prevents accidental mutation) and communicates intent (a `var` in your code signals "this will be mutated," which helps the reader).

### Mistake 2: Mixing `Int` and `Double` without conversion

```swift
// WRONG — Swift refuses to implicitly convert
let count = 3
let total = 10.0
let average = total / count   // ERROR — cannot divide Double by Int
```

```swift
// RIGHT — explicitly convert
let count = 3
let total = 10.0
let average = total / Double(count)   // 3.333...
```

If you are coming from C, JavaScript, Python, or Kotlin (which all silently promote `Int` to `Double` when needed), this will trip you up. Swift refuses to mix `Int` and `Double` — you must explicitly convert one to the other with `Double(...)` or `Int(...)`. The compiler error is `binary operator '/' cannot be applied to operands of type 'Double' and 'Int'`. The fix is to wrap the integer in `Double(...)`. This is annoying at first but it eliminates an entire class of precision-loss bugs (silent integer truncation, unsigned underflow, etc.).

### Mistake 3: Using `.length` instead of `.count` on strings

```swift
// WRONG — Java and C# habit; Swift strings do not have .length
let s = "Hello"
print(s.length)   // ERROR — value of type 'String' has no member 'length'
```

```swift
// RIGHT — Swift uses .count for the length of a string
let s = "Hello"
print(s.count)    // 5
```

If you are coming from Java (Module 17) or C# (where strings have `.Length` with a capital L), your fingers will type `.length` from muscle memory. Swift strings use `.count` — the same name used by Swift's `Array`, `Dictionary`, and `Set` types. The compiler error is `value of type 'String' has no member 'length'`. The fix is to use `.count`. Arrays also use `.count`, not `.length` — Swift's collection API is consistent across types.

### Mistake 4: Expecting `s[0]` to work on strings

```swift
// WRONG — Swift strings cannot be indexed with Int
let s = "Swift"
let first = s[0]   // ERROR — subscript (index) requires a String.Index, not Int
```

```swift
// RIGHT — use s.startIndex and s.index(after:)
let s = "Swift"
let first = s[s.startIndex]                       // "S"
let second = s[s.index(after: s.startIndex)]      // "w"
let third = s[s.index(s.startIndex, offsetBy: 2)] // "i"
```

This trips up almost everyone coming from Python, JavaScript, Java, or C#. Swift strings are **not** arrays of `Character` — they are Unicode-correct collections of extended grapheme clusters, where each `Character` may take a variable number of bytes. You cannot index them with `Int`. Instead, you use `s.startIndex` to get the first index, `s.index(after:)` to advance one position, and `s.index(_:offsetBy:)` to skip multiple positions. The opaque `String.Index` type is what Swift uses to abstract over variable-length encoding. It is awkward but it is the cost of being Unicode-correct. We revisit this in Lesson 07's cheatsheet.

---

## Summary

- **`var` is mutable; `let` is a constant.** Default to `let`; use `var` only when you genuinely need to reassign. This matches Kotlin's `val`/`var` and Rust's `let`/`let mut`.
- Swift has strong **type inference** — `let name = "Hieu"` infers `String`, `let age = 25` infers `Int`, `let pi = 3.14` infers `Double`. Write explicit type annotations only when needed for disambiguation.
- The basic types are `Int` (default integer, platform-sized), `Double` (default floating-point), `Float`, `Bool`, `String`, `Character`, and tuples. Apple recommends `Int` and `Double` as the defaults.
- Numeric literals support underscores (`1_000_000`), hex (`0xFF`), binary (`0b1010`), octal (`0o17`), and exponential notation (`1.5e10`).
- **Type safety is strict** — `Int + Double` does not compile. You must explicitly convert with `Double(...)` or `Int(...)`. Swift refuses to silently truncate or promote.
- Strings are **value types** (structs), Unicode-compliant, immutable. Use `.count` (not `.length`), `.uppercased()`, `.lowercased()`, `.contains()`, `.hasPrefix()`, `.hasSuffix()`. Compare with `==` (value equality).
- **Swift strings cannot be indexed with `Int`** — use `s.startIndex` and `s.index(after:)` instead. This is the cost of being Unicode-correct.
- Tuples group multiple values: `let p: (String, Int) = ("Hieu", 25)`. Access with `.0`, `.1` or with named elements: `let p = (name: "Hieu", age: 25); p.name`. Destructure with `let (n, a) = p`. Use `_` to ignore elements.
- `typealias` creates a synonym for an existing type — useful for semantic clarity (`typealias ID = UInt64`) but does not provide type safety. For that, use a `struct`.

You now understand Swift's type system foundations. You can choose `var` vs `let` correctly, infer types, convert between `Int` and `Double` explicitly, work with strings and tuples, and avoid the `.length` and `s[0]` traps. In Lesson 04 we cover control flow: `if`/`else` (and `if` as an expression in Swift 5.9+), the powerful `switch` with pattern matching, `for` loops with ranges (`1...5` inclusive, `1..<5` exclusive), `while` and `repeat-while`, and labeled `break` and `continue`.

---

**Next:** [Lesson 04: Control Flow →](./04-control-flow.md)
