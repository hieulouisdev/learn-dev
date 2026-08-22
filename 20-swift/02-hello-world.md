# Lesson 02: Hello, World!

> Module: Swift · Lesson 2 of 7
> Estimated time: 30–45 minutes

In Lesson 01 you ran your first Swift program with `swift hello.swift` and compiled it with `swiftc hello.swift -o hello`. In this lesson we slow down and dissect what a real Swift file looks like: the difference between **script mode** (run with `swift file.swift`) and **compiled module mode** (with an explicit `@main` entry point), how comments work (and the surprising fact that block comments can *nest* in Swift), the differences between `print`, `print(..., terminator: "")`, and `dump`, and how Swift's string interpolation with `\()` works — including how to write multi-line strings with `"""`.

By the end of this lesson you will be able to look at any Swift file and identify the entry point, the imports, the comments, and the print calls. You will also know why Swift's string interpolation syntax uses `\(...)` instead of the `$variable` of Kotlin or the `{variable}` of Python f-strings.

---

## Learning Objectives

After this lesson, you will be able to:

1. Distinguish between running a Swift file as a **script** (`swift file.swift`) and as a **compiled module** (with `@main`), and know when to use each.
2. Use all four kinds of Swift comments: `//` line, `/* */` block (which can nest!), `///` documentation, and `// MARK:`/`// TODO:`/`// FIXME:` pragmas.
3. Use `print`, `print(..., terminator:)`, and `dump` correctly and know the difference.
4. Write string interpolation with `\()`, multi-line strings with `"""`, and explain why Swift chose `\()` over the syntax Python and Kotlin use.

---

## 1. Script vs Compiled

Swift gives you two ways to run code, and a real developer uses both. The first is **script mode**: you write top-level code in a `.swift` file and run it directly with the `swift` driver:

```bash
swift hello.swift
```

In script mode, the Swift driver reads `hello.swift`, compiles it in-memory to a temporary executable, runs it, and discards the executable. The file does not need a `main` function or `@main` attribute — any top-level statements in the file are the program. This is fantastic for **quick experiments, ad-hoc scripts, and learning** — the file behaves like a Python script. You can write `let x = 5; print(x)` at the top level and it just runs.

The second way is **compiled mode**: you compile the file to a native binary with `swiftc` and then run the binary:

```bash
swiftc hello.swift -o hello
./hello
```

In compiled mode you get a real executable file (`hello`) that you can ship, share, or run repeatedly without recompiling. The first compile takes a fraction of a second; subsequent runs are instant because the binary already exists. For larger programs with multiple files, you typically use the **Swift Package Manager (SwiftPM)** to manage compilation:

```bash
swift package init --type executable
swift build
swift run
```

- `swift package init --type executable` — scaffolds a new executable Swift package in the current directory. It creates `Package.swift`, `Sources/`, and `Tests/` folders with starter files.
- `swift build` — compiles the package. The output binary lands in `.build/release/` or `.build/debug/`.
- `swift run` — builds (if needed) and then runs the executable. You can also pass arguments: `swift run mytool --flag value`.

SwiftPM is the standard way to structure any non-trivial Swift project — it handles dependencies, builds, tests, and releases. For this module, however, we will mostly use script mode (`swift file.swift`) because it is faster to iterate. Just remember that any script-mode file can also be compiled with `swiftc file.swift -o program` if you want a standalone binary.

---

## 2. Program Anatomy

Let's look at a slightly larger Swift program that uses imports, a `@main` entry point, and a function definition. Save this as `greeting.swift`:

```swift
import Foundation

struct Greeter {
    let name: String

    func sayHello() {
        print("Hello, \(name)!")
    }
}

@main
struct Main {
    static func main() {
        let greeter = Greeter(name: "Hieu")
        greeter.sayHello()
    }
}
```

Let's walk through it line by line:

- `import Foundation` — imports Apple's Foundation framework, which gives you `URL`, `Date`, `JSONEncoder`, file I/O, and many other utilities. In script mode, the Swift standard library (`Swift`, `Dispatch`, `os`) is implicitly imported; `Foundation` is not, so you import it explicitly when you need it. On Linux, `Foundation` is part of the cross-platform swift-corelibs-foundation project.
- `struct Greeter { ... }` — defines a struct named `Greeter` with a stored property `name` and a method `sayHello()`. Structs are value types in Swift — we cover them in depth in Lesson 06.
- `let name: String` — a stored property named `name`, declared as a constant of type `String`. The `: String` is an explicit type annotation.
- `func sayHello() { ... }` — a method. We cover functions and methods in Lesson 05.
- `print("Hello, \(name)!")` — string interpolation with `\(...)`. We explain this in Section 5 below.
- `@main` — an attribute that marks the next type as the program's entry point. When you compile this file as part of a module, the `@main` attribute tells the Swift runtime "this is where execution begins."
- `struct Main { static func main() { ... } }` — a struct with a static `main()` method. The `@main` attribute requires this exact shape: a type with a `static func main()`. The body of `main()` is what runs when the program starts.
- `let greeter = Greeter(name: "Hieu")` — constructs an instance of `Greeter`, passing `"Hieu"` as the `name` argument. Notice the argument label `name:` — Swift requires argument labels on function and initializer calls by default.
- `greeter.sayHello()` — calls the method on the instance.

The `@main` attribute is the **module mode** entry point. When you build a SwiftPM executable package, the entry point is a `@main`-attributed type (a struct, class, or enum) with a `static func main()`. In script mode (running with `swift greeting.swift`), you would get an error — script mode allows top-level code, but `@main` is only allowed when compiling as a module via `swift build` or `swiftc`. For most of this module we use script mode and write top-level code directly; just remember `@main` exists when you start building real SwiftPM packages.

---

## 3. Comments

Swift has four kinds of comments. They are all useful and you should use all four:

```swift
// This is a line comment. It runs from // to the end of the line.
let x = 5  // You can also put a comment after code on the same line.

/* This is a block comment.
   It can span multiple lines.
   Use it for longer explanations. */

/// This is a documentation comment.
/// It is rendered by DocC (Swift's documentation tool) and shows up in Xcode's
/// Quick Help when you Option-click on the symbol.
func add(_ a: Int, _ b: Int) -> Int {
    a + b
}

// MARK: - Section Header
// Xcode renders "MARK:" lines as section dividers in the jump bar.

// TODO: Refactor this when we have time
// FIXME: This breaks if input is empty
```

Line-by-line:

- `// ...` — line comment, runs to the end of the line. Identical to C, Java, JavaScript, and most C-style languages.
- `/* ... */` — block comment, can span multiple lines. **Surprise: in Swift, block comments can nest.** This is unusual — in C, C++, Java, JavaScript, CSS, the first `*/` ends the comment, even if you opened more `/*` before it. In Swift, the parser tracks nesting depth, so `/* outer /* inner */ still comment */` is a single valid comment. This lets you comment out a chunk of code that already contains block comments — a small but genuinely useful feature.
- `/// ...` — documentation comment. Three slashes instead of two. The lines immediately above a declaration (function, struct, class, property) become its documentation. Xcode renders them in Quick Help (Option-click), and Apple's DocC tool generates HTML documentation from them. You can use Markdown: `/// - Parameter a: the first number`, `/// - Returns: the sum`.
- `// MARK: -` — a pragma that Xcode picks up. The `-` after `MARK:` adds a separator line in the source editor's jump bar (the dropdown at the top of the editor that lists functions). `// MARK: - Lifecycle` shows up as a section header. Useful for organizing large files.
- `// TODO:` — marks something to do later. Xcode shows these in the jump bar.
- `// FIXME:` — marks something broken that needs fixing. Also shows in the jump bar.

The `// MARK:`, `// TODO:`, and `// FIXME:` pragmas are Xcode-specific — the `swift` command-line compiler ignores them. But if you ever edit Swift in Xcode, they show up in the jump bar and the issue navigator, making it easy to find all TODOs and FIXMEs across a project. Get into the habit of using them.

---

## 4. Printing

Swift has three main functions for outputting values to standard output. Knowing which one to use is a small but important skill.

### `print(...)`

```swift
let name = "Hieu"
let age = 25
print(name)
print("Name:", name, "Age:", age)
```

- `print(name)` — prints `Hieu` followed by a newline.
- `print("Name:", name, "Age:", age)` — prints `Name: Hieu Age: 25` followed by a newline. Multiple arguments are separated by a single space, like Python's `print`. Each `print` call ends with a newline.

### `print(..., terminator: "")`

```swift
print("Loading", terminator: "")
print("...")
```

- `print("Loading", terminator: "")` — prints `Loading` *without* a newline.
- `print("...")` — prints `...` followed by a newline, so the output is `Loading...` on one line followed by a newline.
- The `terminator:` parameter takes the string that goes *after* the printed value. The default is `"\n"`. Pass `""` to suppress the newline, or pass `" | "` to use a custom separator.

### `print(..., separator: "")`

```swift
print(2024, 9, 12, separator: "-")
```

- The `separator:` parameter controls what goes *between* multiple arguments. Default is `" "` (single space).
- `print(2024, 9, 12, separator: "-")` prints `2024-9-12` followed by a newline.

### `dump(...)`

```swift
struct Person { let name: String; let age: Int }
let p = Person(name: "Hieu", age: 25)
dump(p)
```

`dump` prints a detailed, recursive structure of the value — useful for debugging:

```text
▿ Person
  - name: "Hieu"
  - age: 25
```

- `dump(p)` — prints the type, then each property with its value, indented. Use it when you want to *see inside* a value, not just print a single line.
- `print(p)` — prints the default `description` (often just `Person(name: "Hieu", age: 25)` for structs with default `String` conversion). Less detail than `dump`.

The practical rule: **use `print` for ordinary output** (most of the time), **use `print(..., terminator: "")` when you want to print without a newline** (progress bars, prompts), and **use `dump` when debugging a complex value** and you want to see its structure.

---

## 5. String Interpolation

Swift uses **`\(...)`** for string interpolation — backslash, open parenthesis, expression, close parenthesis. This is different from Python (`f"{variable}"`), JavaScript (`` `${variable}` ``), Kotlin (`"$variable"`), and Ruby (`"#{variable}"`). It is the same shape as C#'s `$"{variable}"` no — actually C# uses `{}`, so Swift is unique here.

```swift
let name = "Hieu"
let age = 25
print("Hello, \(name)!")
print("Next year you will be \(age + 1).")
```

Line by line:

- `let name = "Hieu"` — declares a constant `name` of type `String`.
- `let age = 25` — declares a constant `age` of type `Int`.
- `print("Hello, \(name)!")` — interpolates the value of `name` into the string. The `\(...)` is replaced by the variable's value, so this prints `Hello, Hieu!`.
- `print("Next year you will be \(age + 1).")` — interpolation can contain *any expression*, including arithmetic. `\(age + 1)` evaluates `age + 1` (which is `26`) and inserts the result. Prints `Next year you will be 26.`

Why `\(...)` instead of `$variable`? Two reasons. **First, it can contain any Swift expression** — `\(arr.map { $0 * 2 })`, `\(user.fullName() ?? "anonymous")`, `\(calculateTax(amount: 100))` — all valid. Languages with `$variable` syntax (like Kotlin and Bash) can only interpolate simple variable references; complex expressions require string concatenation. **Second, the parentheses make the boundaries unambiguous.** `$name$surname` is hard to read — where does the first variable end and the second begin? `\(name)\(surname)` is unambiguous. The trade-off is more typing, but the result is more powerful.

```swift
let scores = [85, 92, 78]
print("Average: \(Double(scores.reduce(0, +)) / Double(scores.count))")
```

- `scores.reduce(0, +)` — sums all elements (85 + 92 + 78 = 255).
- `Double(...)` — converts to `Double` for floating-point division.
- `Double(scores.count)` — `3` as `Double`.
- The whole expression `255.0 / 3.0 = 85.0` is interpolated into the string.
- Prints `Average: 85.0`.

---

## 6. Multi-line Strings

Swift has multi-line string literals, demarcated by three double quotes `"""`:

```swift
let poem = """
Roses are red,
Violets are blue,
Swift compiles fast,
And so can you.
"""
print(poem)
```

Line by line:

- `let poem = """` — opens the multi-line string. The opening `"""` must be the last thing on its line — content starts on the next line.
- The lines `Roses are red,` through `And so can you.` are the content of the string.
- `"""` on its own line closes the multi-line string. The position of the closing `"""` matters — Swift strips leading whitespace from every line based on how far the closing `"""` is indented. This is called "indentation normalization."

This prints:

```text
Roses are red,
Violets are blue,
Swift compiles fast,
And so can you.
```

Indentation normalization is the killer feature of multi-line strings. Consider:

```swift
func makePoem() -> String {
    let poem = """
    Roses are red,
    Violets are blue,
    """
    return poem
}
```

- The content lines are indented 4 spaces (because they are inside the function).
- The closing `"""` is also indented 4 spaces.
- Swift strips the first 4 spaces from every content line, so the resulting string is `Roses are red,\nViolets are blue,\n` — no leading whitespace.

This lets you embed multi-line strings inside indented code without the strings themselves becoming full of leading spaces. If you want literal newlines escaped, you can put a `\` at the end of a line:

```swift
let singleLine = """
This is one long line \
even though it spans \
multiple source lines.
"""
```

- The `\` at the end of each line means "ignore this newline." The resulting string is `This is one long line even though it spans multiple source lines.` on a single line.

You can also interpolate inside multi-line strings:

```swift
let name = "Hieu"
let greeting = """
Hello, \(name)!
Welcome to Swift.
"""
```

- `\(name)` interpolates `name` inside the multi-line string. Multi-line strings support all the same interpolation features as single-line strings.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Print Your Bio

Write a script `bio.swift` that declares `let name = "Your Name"` and `let age = 25` (or your actual age), then uses string interpolation to print `My name is [name] and I am [age] years old.` Then add a second print: `In 10 years I will be [age + 10].` Run with `swift bio.swift` and confirm both lines appear. Experiment with `separator:` and `terminator:` arguments — try `print("a", "b", "c", separator: " | ")` and see what gets printed.

### Exercise 2: Multi-line String

Write a script `poem.swift` that declares a multi-line string with at least four lines of a poem, limerick, or haiku. Print it with `print(poem)`. Confirm the output preserves the newlines. Then try writing the same string using `\n` escapes inside a regular single-line string (`"Line 1\nLine 2\n..."`) and notice how much harder it is to read. Multi-line strings exist for exactly this reason.

### Exercise 3: Use `dump` to Inspect a Tuple

Write a script that declares a tuple like `let person = (name: "Hieu", age: 25, city: "Hanoi")`, then call `print(person)` and `dump(person)` on separate lines. Notice that `print` shows `(.0 "Hieu", .1 25, .2 "Hanoi")` (or similar), while `dump` shows a more detailed indented structure with the named keys. Tuples are covered in detail in Lesson 03 — for now, just observe the difference between `print` and `dump`.

---

## Common Mistakes

### Mistake 1: Using `println` (Java or Kotlin habit)

```swift
// WRONG — println does not exist in Swift
println("Hello, World!")
```

```swift
// RIGHT — Swift uses print, which adds a newline by default
print("Hello, World!")
```

If you are coming from Java (Module 17) or Kotlin (Module 19), your fingers will type `println` from muscle memory. Swift 1.0 (2014) had both `print` (no newline) and `println` (with newline), but Swift 2.0 (2015) removed `println` and changed `print` to add a newline by default. If you write `println` in modern Swift the compiler stops with `cannot find 'println' in scope`. Use `print`, and pass `terminator: ""` if you ever need to suppress the newline.

### Mistake 2: Using `$variable` in interpolation (Kotlin habit)

```swift
// WRONG — Kotlin-style $ interpolation does not work in Swift
let name = "Hieu"
print("Hello, $name!")
```

```swift
// RIGHT — Swift uses \(variable) for interpolation
let name = "Hieu"
print("Hello, \(name)!")
```

If you are coming from Kotlin (Module 19), Bash, or PHP, you are used to `$variable` interpolation. Swift does *not* support `$variable` — the `$` character has a different meaning in Swift (it refers to positional closure arguments like `$0`, `$1`). To interpolate in Swift, use `\(...)`. The compiler will not catch `$name` — it just treats it as a literal `$name` in the output string, so you might see `Hello, $name!` printed when you expected `Hello, Hieu!`. If your interpolated variable shows up literally in the output, you forgot the `\(...)`.

### Mistake 3: Forgetting that block comments DO nest in Swift

```swift
// In C/Java, this would be a syntax error:
/* outer comment
   /* inner comment */
   still outer comment
*/
```

In C, C++, Java, JavaScript, and CSS, the first `*/` ends the entire block comment — even if you opened more `/*` before it. The above code in C would have the second `*/` outside any comment, causing a syntax error. **In Swift, block comments nest properly** — the parser tracks the nesting depth, and the comment only ends when the depth returns to zero. This means you can comment out a chunk of code that already contains block comments without breaking anything — a small but genuinely useful feature. Just do not assume the same is true in other languages; it is one of Swift's pleasant surprises.

### Mistake 4: Using `\n` inside a single-line string when a multi-line string would be cleaner

```swift
// WORKS — but is hard to read and easy to miscount
let poem = "Roses are red,\nViolets are blue,\nSwift compiles fast,\nAnd so can you."
```

```swift
// RIGHT — multi-line string is clearer and harder to miscount
let poem = """
Roses are red,
Violets are blue,
Swift compiles fast,
And so can you.
"""
```

Single-line strings with embedded `\n` escapes work, but they are hard to read and easy to miscount (did you add the trailing comma? did you forget the `\n`?). For any string with more than one or two newlines, use a multi-line string literal with `"""`. The result is more readable, easier to edit, and supports indentation normalization so you can indent the content to match surrounding code without the indentation appearing in the output.

---

## Summary

- Swift runs in two modes: **script mode** (`swift file.swift`) for quick experiments with top-level code, and **compiled module mode** (`swiftc` or `swift build` producing a binary) for production. Larger projects use the **Swift Package Manager (SwiftPM)** with `swift package init`, `swift build`, `swift run`, and `swift test`.
- In module mode, the entry point is a struct/class/enum with `@main` and a `static func main()`. In script mode, top-level statements are the entry point — no `@main` needed.
- Swift has four comment kinds: `//` line, `/* */` block (which *nest* in Swift — unusual!), `///` documentation (rendered by DocC and Xcode Quick Help), and `// MARK:` / `// TODO:` / `// FIXME:` pragmas (Xcode-specific).
- `print(...)` adds a newline by default. Use `terminator: ""` to suppress the newline, `separator: " | "` to change the multi-argument separator, and `dump(...)` to print a detailed recursive structure for debugging.
- String interpolation uses `\(...)`, not `$variable` (Kotlin) or `{variable}` (Python f-strings). Interpolation can contain any Swift expression: `\(age + 1)`, `\(arr.reduce(0, +))`.
- Multi-line strings with `"""` span multiple source lines, support indentation normalization based on the closing `"""`'s indent, and support interpolation. Use `\` at end of a line to join lines without a newline.
- Swift's `print` is a top-level function — no `System.out.` prefix, no `fmt.` prefix. Use it directly.
- Block comments in Swift can nest — `/* outer /* inner */ still outer */` is a single valid comment. Do not assume this is true in other languages.

You now understand the anatomy of a Swift file. You can identify the entry point, read comments (including the unusual nesting behavior), and write string interpolation correctly. In Lesson 03 we cover Swift's most important daily decision: when to use `var` versus `let`, type inference, the basic types (`Int`, `Double`, `Bool`, `String`, `Character`, tuples), and why `Int + Double` does not compile in Swift.

---

**Next:** [Lesson 03: Variables and Types →](./03-variables-and-types.md)
