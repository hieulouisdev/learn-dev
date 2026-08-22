# Lesson 07: Swift Cheatsheet

> Module: Swift · Lesson 7 of 7
> Estimated time: 30–45 minutes

This is the Swift reference page. It collects the syntax, idioms, and standard-library types you have learned in the previous six lessons into one quick-reference document. Use it as a memory aid while you write Swift, and as a checklist of what to study next. The structure mirrors a typical Swift file from top to bottom — declarations, types, control flow, functions, collections, structs and classes, protocols, error handling, common idioms — and ends with a "common pitfalls" section that catches the bugs most beginners hit.

Unlike the previous lessons, this page is not a tutorial — it is a reference. Read it through once to refresh your memory, then bookmark it and come back as needed. The examples are intentionally minimal — each one shows the syntax without the long explanations from earlier lessons.

---

## Learning Objectives

After this lesson, you will be able to:

1. Look up any Swift syntax construct quickly — from `var`/`let` to `Optional` to `Result` to protocol extensions.
2. Identify and avoid the most common Swift pitfalls (force-unwrapping `nil`, using structs where you need a class, forgetting argument labels, expecting `s[0]` to work on strings).
3. Use the most important standard-library types (`Array`, `Dictionary`, `Set`, `String`, `Optional`, `Result`, `Codable`) without having to look them up.
4. Know what comes next in your programming journey — this is the final language module in the curriculum, and the "What's Next?" section at the end of this lesson tells you exactly how to apply what you have learned.

---

## 1. Hello, World! — Minimal Program

```swift
print("Hello, World!")
```

- Top-level code in a script — no `main()` function or `import` needed for `print`.
- Run as a script with `swift hello.swift`, or compile with `swiftc hello.swift -o hello` and run with `./hello`.

For a SwiftPM executable module, use the `@main` attribute:

```swift
@main
struct Main {
    static func main() {
        print("Hello, World!")
    }
}
```

- The `@main` attribute marks the entry point. The type must have a `static func main()`.

---

## 2. Compiler Commands

| Command | Purpose |
|---------|---------|
| `swift --version` | Check the Swift driver version |
| `swiftc --version` | Check the underlying Swift compiler version |
| `swift hello.swift` | Run a `.swift` file as a script (compiles in-memory) |
| `swiftc hello.swift -o hello` | Compile a `.swift` file to a native binary `hello` |
| `swift package init --type executable` | Scaffold a new executable SwiftPM package |
| `swift package init --type library` | Scaffold a new library SwiftPM package |
| `swift build` | Build the package (debug) |
| `swift build -c release` | Build the package (release, optimized) |
| `swift run` | Build (if needed) and run the executable |
| `swift run mytool --flag value` | Build and run with arguments |
| `swift test` | Run the package's tests in `Tests/` |
| `swift package update` | Update dependencies to latest versions |
| `swift` (no args) | Start the Swift REPL (interactive prompt) |

---

## 3. `var` vs `let`

```swift
let name = "Hieu"   // constant — assigned once
var age = 25          // mutable — can be reassigned
age = 26              // OK
// name = "Bob"       // COMPILE ERROR — name is let
```

| Keyword | Mutability | Use when |
|---------|------------|---------|
| `let` | Constant (assigned once) | **By default, almost always** |
| `var` | Mutable (reassignable) | You genuinely need to reassign |

The same distinction as Kotlin's `val`/`var`, Rust's `let`/`let mut`, JavaScript's `const`/`let`. Default to `let`; switch to `var` only if you must reassign.

---

## 4. Basic Types

| Type | Description | Example |
|------|-------------|---------|
| `Int` | Signed integer, platform-sized (64-bit on modern systems) | `42` |
| `Int8`, `Int16`, `Int32`, `Int64` | Fixed-width signed integers | `Int8(127)` |
| `UInt`, `UInt8`, ... `UInt64` | Unsigned integers (use only when needed) | `UInt(255)` |
| `Double` | 64-bit floating-point (**default** for `3.14`) | `3.14159` |
| `Float` | 32-bit floating-point (use only when storage matters) | `Float(3.14)` |
| `Bool` | Boolean — `true` or `false` | `true` |
| `String` | Unicode-compliant value type (a struct) | `"Hello"` |
| `Character` | A single Unicode extended grapheme cluster | `"A"` |
| `Tuple` `(T1, T2, ...)` | Grouped values of possibly different types | `(1, "two")` |
| `Optional<Wrapped>` aka `Wrapped?` | Either a value or `nil` | `Int?` |
| `Array<Element>` aka `[Element]` | Ordered collection | `[1, 2, 3]` |
| `Dictionary<K, V>` aka `[K: V]` | Key-value map | `["a": 1]` |
| `Set<Element>` | Unordered unique collection | `Set([1, 2, 3])` |
| `Result<Success, Failure>` | Either a success value or an error | `Result<Int, Error>` |
| `Range<Int>` aka `1..<5` | Half-open range | `1..<5` |
| `ClosedRange<Int>` aka `1...5` | Closed range | `1...5` |

---

## 5. Strings

```swift
let greeting = "Hello, World!"

// Interpolation
let name = "Hieu"
print("Hello, \(name)!")            // Hello, Hieu!
print("2 + 2 = \(2 + 2)")           // 2 + 2 = 4

// Multi-line strings
let poem = """
Roses are red,
Violets are blue,
Swift compiles fast,
And so can you.
"""

// Common methods
greeting.count                      // 13 — NOT .length
greeting.uppercased()               // "HELLO, WORLD!"
greeting.lowercased()               // "hello, world!"
greeting.contains("World")          // true
greeting.hasPrefix("Hello")         // true
greeting.hasSuffix("!")             // true

// Concatenation
let full = "a" + "b" + "c"          // "abc"

// Comparison
"a" == "a"                          // true (value equality)

// Indexing (awkward but Unicode-correct)
let s = "Swift"
s[s.startIndex]                     // "S"
s[s.index(after: s.startIndex)]     // "w"
s[s.index(s.startIndex, offsetBy: 2)] // "i"
// s[0]                              // ERROR — strings cannot be indexed with Int
```

---

## 6. Optionals

```swift
let maybeAge: Int? = 25
let nothing: Int? = nil

// Forced unwrap (CRASHES if nil)
let age = maybeAge!                 // 25
// let bad = nothing!                // CRASH

// Optional binding
if let name = maybeName {
    print(name)                     // runs only if non-nil
}

// Guard for early exit
func greet(name: String?) {
    guard let name = name else { return }
    print(name)                     // non-optional here
}

// Nil-coalescing — default if nil
let displayName = nickname ?? "Anonymous"

// Optional chaining
let city = user.address?.city       // String? — short-circuits to nil

// try? for throwing calls
let n = try? parse("42")            // Optional(42)

// Optional map / flatMap
let len = maybeName.map { $0.count }  // Optional<Int>
```

---

## 7. Control Flow

```swift
// if / else if / else — condition must be Bool
if score >= 90 { print("A") }
else if score >= 80 { print("B") }
else { print("F") }

// if as expression (Swift 5.9+)
let color = if isDark { "black" } else { "white" }

// Ternary
let color2 = isDark ? "black" : "white"

// switch — exhaustive, no fall-through
switch day {
case 1, 2, 3, 4, 5: print("Weekday")
case 6, 7: print("Weekend")
default: print("Invalid")
}

// switch with ranges
switch score {
case 90...100: print("A")
case 80..<90: print("B")
default: print("F")
}

// switch with tuple pattern matching
switch (x, y) {
case (0, 0): print("origin")
case (_, 0): print("on x-axis")
case let (a, b) where a == b: print("diagonal")
default: break
}

// for loop — ranges, arrays, enumerated
for i in 1...5 { print(i) }          // 1, 2, 3, 4, 5 (closed)
for i in 1..<5 { print(i) }          // 1, 2, 3, 4 (half-open)
for x in arr { print(x) }
for (i, x) in arr.enumerated() { print("\(i): \(x)") }
for char in "Swift" { print(char) }
for i in stride(from: 0, to: 10, by: 2) { print(i) }  // 0, 2, 4, 6, 8

// while / repeat-while
while n > 0 { n -= 1 }
repeat { n += 1 } while n < 5

// break / continue / labeled
outerLoop: for i in 1...3 {
    for j in 1...3 {
        if i * j == 6 { break outerLoop }
    }
}
```

---

## 8. Functions

```swift
// Definition with argument labels
func greet(name: String) -> String { "Hello, \(name)!" }
greet(name: "Hieu")

// Underscore — no argument label
func add(_ a: Int, _ b: Int) -> Int { a + b }
add(3, 4)

// Explicit external label + internal name
func greet(person name: String) -> String { "Hi, \(name)" }
greet(person: "Hieu")

// Default values
func makeURL(host: String, port: Int = 80) -> String { ... }

// Variadic
func sum(_ numbers: Int...) -> Int { numbers.reduce(0, +) }
sum(1, 2, 3, 4)

// Inout — mutate caller's value
func increment(_ n: inout Int) { n += 1 }
var x = 5
increment(&x)

// Function type
let op: (Int, Int) -> Int = add

// Trailing closures
let doubled = numbers.map { $0 * 2 }
let evens = numbers.filter { $0 % 2 == 0 }
let total = numbers.reduce(0) { $0 + $1 }

// Throwing functions
enum MyError: Error { case badInput }
func parse(_ s: String) throws -> Int {
    guard !s.isEmpty else { throw MyError.badInput }
    return Int(s) ?? 0
}

do {
    let n = try parse("42")
} catch MyError.badInput {
    print("bad input")
} catch {
    print("unknown: \(error)")
}

let n1 = try? parse("42")  // Optional(42)
let n2 = try! parse("42")  // 42 — crashes if error
```

---

## 9. Collection Types

```swift
// Array
var arr: [Int] = [1, 2, 3]
arr.append(4)                       // [1, 2, 3, 4]
arr.insert(0, at: 0)                // [0, 1, 2, 3, 4]
arr.remove(at: 2)                   // [0, 1, 3, 4]
arr.count                           // 4
arr.isEmpty                         // false
arr.contains(3)                     // true
arr.first                           // Optional(0)
arr.last                            // Optional(4)
arr[1...3]                          // ArraySlice [1, 3, 4]

// Dictionary
var dict: [String: Int] = ["a": 1, "b": 2]
dict["c"] = 3                       // add
dict["a"] = 10                      // update
dict.removeValue(forKey: "b")       // remove
dict.count                          // 2
for (k, v) in dict { print(k, v) }
let val = dict["a"]                 // Int? — Optional(10)

// Set
var set: Set<Int> = [1, 2, 3, 3]    // {1, 2, 3} — duplicates removed
set.insert(4)
set.contains(2)                     // true
set.union([5, 6])                   // {1, 2, 3, 4, 5, 6}
set.intersection([2, 3, 7])         // {2, 3}

// Common higher-order methods
let nums = [1, 2, 3, 4, 5]
nums.map { $0 * 2 }                  // [2, 4, 6, 8, 10]
nums.filter { $0 % 2 == 0 }         // [2, 4]
nums.reduce(0, +)                   // 15
nums.forEach { print($0) }
nums.sorted()                       // [1, 2, 3, 4, 5]
nums.sorted(by: >)                  // [5, 4, 3, 2, 1]
nums.reversed()                     // ReversedCollection
nums.first(where: { $0 > 3 })       // Optional(4)
nums.allSatisfy { $0 > 0 }          // true
```

---

## 10. Structs vs Classes

| Aspect | Struct | Class |
|--------|--------|-------|
| **Semantics** | Value type (copied) | Reference type (shared) |
| **Default choice** | **Yes — use by default** | Only when you need identity or inheritance |
| **Inheritance** | No (protocols only) | Yes (single inheritance) |
| **Memberwise init** | Free (auto-generated) | Must write `init` |
| **Mutating methods** | Need `mutating` keyword | Mutate freely |
| **ARC** | No (stack-allocated when possible) | Yes — watch for retain cycles |
| **Identity `===`** | Not available | Available |
| **`Equatable` synthesis** | Yes (if all properties are Equatable) | No — implement manually |
| **Objective-C interop** | Limited | Full (`NSObject` subclasses) |
| **Examples in stdlib** | `Int`, `Double`, `String`, `Array`, `Dictionary`, `Set`, `Optional` | `URLSession`, `NotificationCenter`, UI types |

```swift
// Struct — value type
struct Point { var x: Double; var y: Double }
var p1 = Point(x: 1, y: 2)
var p2 = p1               // copy
p2.x = 99                 // p1.x still 1.0

// Class — reference type
class Account {
    var balance: Double
    init(balance: Double) { self.balance = balance }
}
let a1 = Account(balance: 100)
let a2 = a1               // same instance
a2.balance = 999          // a1.balance also 999
print(a1 === a2)          // true

// Inheritance — only classes
class Dog: Animal {
    override func speak() -> String { "Woof" }
}

// Final — prevent subclassing
final class ImmutableAccount { ... }
```

---

## 11. Protocols

```swift
// Define
protocol Greeter {
    var name: String { get }
    func greet() -> String
}

// Adopt in a struct
struct EnglishGreeter: Greeter {
    let name: String
    func greet() -> String { "Hello, \(name)" }
}

// Default implementation via protocol extension
extension Greeter {
    func greet() -> String { "Hi, \(name)" }
}

// Struct gets the default for free
struct User: Greeter { let name: String }

// Multiple protocol conformance
struct Player: Greeter, Equatable, CustomStringConvertible {
    let name: String
    var description: String { "Player(\(name))" }
}

// Use as existential type
func describe(_ g: Greeter) { print(g.greet()) }

// Generic with protocol constraint
func birthday<T: Greeter>(for g: T) { print("Happy birthday, \(g.name)") }

// Protocol composition
protocol Named { var name: String { get } }
protocol Aged { var age: Int { get } }
typealias Person = Named & Aged   // both must be satisfied
```

---

## 12. Error Handling

```swift
// Define error enum
enum NetworkError: Error {
    case badURL
    case timeout
    case http(statusCode: Int)        // associated value
}

// Throwing function
func fetch(_ url: String) throws -> String {
    guard url.hasPrefix("https://") else { throw NetworkError.badURL }
    return "200 OK"
}

// do-try-catch with pattern matching
do {
    let r = try fetch("https://example.com")
    print(r)
} catch NetworkError.badURL {
    print("bad URL")
} catch NetworkError.http(let code) {
    print("HTTP \(code)")
} catch {
    print("unknown: \(error)")
}

// Result type for async APIs
func fetchUser(id: Int, completion: (Result<String, NetworkError>) -> Void) {
    if id < 0 { completion(.failure(.badURL)) }
    else { completion(.success("User \(id)")) }
}

fetchUser(id: 42) { result in
    switch result {
    case .success(let name): print(name)
    case .failure(let error): print(error)
    }
}
```

---

## 13. Common Idioms

```swift
// guard let for early exit
func process(_ data: Data?) {
    guard let data = data else { return }
    // data is non-optional Data from here
}

// defer for cleanup (runs when scope exits)
func readFile() throws {
    let f = openFile()
    defer { closeFile(f) }       // always runs — even on throw
    try useFile(f)
}

// @main for entry point
@main
struct Main {
    static func main() {
        // program starts here
    }
}

// Property wrappers (used in SwiftUI and elsewhere)
struct ContentView: View {
    @State private var count = 0      // SwiftUI view state
    @AppStorage("theme") var theme = "light"  // UserDefaults
}

class ViewModel: ObservableObject {
    @Published var items: [Item] = []  // SwiftUI reactive
}

// Nil-coalescing for defaults
let name = inputName ?? "Anonymous"

// Map over optional
let len = maybeName.map { $0.count }   // Int?

// Compact map (filter nils)
let ids: [Int?] = [1, nil, 3, nil, 5]
let valid = ids.compactMap { $0 }     // [1, 3, 5]

// Reduce into a Dictionary
let counts = words.reduce(into: [:]) { counts, word in
    counts[word, default: 0] += 1
}

// Tuple swap (with multi-assignment)
var (a, b) = (1, 2)
(a, b) = (b, a)                       // now a = 2, b = 1
```

---

## 14. Common Pitfalls

```swift
// ❌ Forced unwrap on nil — crashes
let input: String? = nil
let n = Int(input!)                   // CRASH
// ✅ Use guard let or if let
guard let input = input else { return }
let n = Int(input) ?? 0

// ❌ Assuming structs are reference types
struct Counter { var n = 0 }
var c1 = Counter()
var c2 = c1
c2.n = 99
print(c1.n)                           // 0 — copy semantics, NOT 99
// ✅ Use a class if you want shared mutable state
class Counter2 { var n = 0 }

// ❌ Forgetting argument labels
func greet(name: String) { ... }
greet("Hieu")                         // ERROR
// ✅ Use the label (or _ to omit it)
greet(name: "Hieu")

// ❌ Expecting s[0] to work on strings
let s = "Swift"
let first = s[0]                      // ERROR
// ✅ Use startIndex and index(after:)
let first = s[s.startIndex]           // "S"
let second = s[s.index(after: s.startIndex)]  // "w"

// ❌ Using .length (Java/C# habit)
let len = "hello".length              // ERROR
// ✅ Use .count
let len = "hello".count               // 5

// ❌ Mixing Int and Double without conversion
let a = 3
let b = 0.5
let c = a + b                         // ERROR
// ✅ Convert explicitly
let c = Double(a) + b                 // 3.5

// ❌ Forgetting mutating on struct methods
struct S { var x = 0; func bump() { x += 1 } }  // ERROR
// ✅ Mark mutating
struct S { var x = 0; mutating func bump() { x += 1 } }

// ❌ Forgetting default in switch
switch day {
case 1: print("Mon")                  // ERROR — not exhaustive
}
// ✅ Add default
switch day {
case 1: print("Mon")
default: print("Other")
}

// ❌ Expecting fall-through in switch (C habit)
switch x { case 1: print("one"); case 2: print("two") }
// In Swift, only "one" prints when x == 1.
// ✅ Use fallthrough explicitly if you need it
switch x { case 1: print("one"); fallthrough; case 2: print("two") }

// ❌ Using println (removed in Swift 2.0, 2015)
println("hi")                         // ERROR
// ✅ Use print
print("hi")
```

---

## 15. The 10 Most-Used Standard Library Things

1. **`Array<T>` (`[T]`)** — ordered collection, value type. `append`, `insert`, `remove`, `map`, `filter`, `reduce`, `sorted`, `enumerated`, `compactMap`.
2. **`Dictionary<K, V>` (`[K: V]`)** — key-value map, value type. `dict["k"]`, `dict["k"] = v`, `removeValue(forKey:)`, `for (k, v) in dict`, `keys`, `values`.
3. **`Set<T>`** — unordered unique collection, value type. `insert`, `contains`, `union`, `intersection`, `subtracting`.
4. **`String`** — Unicode-correct value type. `count`, `uppercased()`, `lowercased()`, `contains`, `hasPrefix`, `hasSuffix`, `split(separator:)`, `joined(separator:)`. Remember: no integer indexing.
5. **`Optional<Wrapped>` (`Wrapped?`)** — value-or-nil. `if let`, `guard let`, `??`, `?` (chaining), `!` (forced unwrap, avoid), `map`, `compactMap`.
6. **`Result<Success, Failure: Error>`** — success-or-error enum. `.success(value)`, `.failure(error)`, used for async/throwing APIs without `throws`.
7. **`Codable`** — automatic JSON (and other formats) encoding/decoding. Conform your struct to `Codable` and use `JSONEncoder().encode(value)` / `JSONDecoder().decode(Type.self, from: data)`.
8. **`JSONEncoder` and `JSONDecoder`** — convert between `Codable` values and `Data` containing JSON. The standard way to handle JSON in Swift.
9. **`Collection` protocol** — the unifying protocol behind `Array`, `Dictionary`, `Set`, `String`, `Range`. Any function written against `Collection` works on all of them.
10. **`AsyncSequence` and `async`/`await`** (Swift 5.5+) — modern concurrency. `async func fetch() -> Data`, `let data = await fetch()`. The successor to completion-handler-based async, and the foundation of Swift 6's strict concurrency model.

Bonus: also learn `URL` (Foundation), `URLSession` (networking), `Data` (byte buffer), `Date` (Foundation), `NumberFormatter`, and the SwiftUI essentials: `View`, `@State`, `@Binding`, `@EnvironmentObject`, `@AppStorage`.

---

## What's Next?

You have now explored 8 programming languages across this curriculum — HTML, CSS, Python, Rust, Go, TypeScript, Java, C++, Kotlin, and Swift. The best next step is to **pick the language you enjoyed most and build a small real project in it** — a todo app, a CLI tool, a personal website, or a tiny game. You can always revisit any module in this curriculum to deepen your understanding, and the upcoming modules (JavaScript, Git, the command line, responsive design, Node.js, databases, frameworks, and career readiness) will build on what you have learned here.

A few specific next directions if Swift was your favorite:

- **iOS app development with SwiftUI.** Apple's modern UI framework. The official tutorial at developer.apple.com/tutorials/swiftui is the canonical starting point. You will write Swift code from day one and see your app in the iOS Simulator.
- **Server-side Swift with Vapor or Hummingbird.** Build HTTP APIs in Swift that compile to a single fast native binary. vapor.codes and hummingbird.codes are the two most popular frameworks.
- **Swift Package Manager.** If you have not yet, run `swift package init --type executable` to scaffold a SwiftPM project. It is the standard way to structure any non-trivial Swift codebase and integrates with the entire ecosystem of open-source packages at swiftpackageindex.com.
- **Swift Concurrency (async/await).** Available since Swift 5.5 and required in Swift 6. Read the Swift concurrency manifesto and Apple's "Meet async/await" WWDC session to learn the modern Swift concurrency model.
- **Swift Evolution proposals.** Swift is shaped by a public evolution process at github.com/swiftlang/swift-evolution. Reading the proposals is a great way to understand why Swift is the way it is.

Whatever you choose to build next, the fundamentals you have learned in this module — value types, optionals, protocols, pattern matching, error handling — will serve you in every other language too. Most modern languages have adopted some or all of these ideas; learning them deeply once pays dividends forever.

Good luck. Build something you are proud of.
