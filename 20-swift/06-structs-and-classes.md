# Lesson 06: Structs and Classes

> Module: Swift · Lesson 6 of 7
> Estimated time: 45–60 minutes

This is the most important lesson in the entire Swift module. Swift is often described as a **protocol-oriented programming language** with **value types by default** — and in this lesson we explore both of those design choices in depth. You will learn the difference between `struct` (value type, copied on assignment) and `class` (reference type, shared on assignment), how to choose between them, how properties (stored, computed, lazy, observed) work, how initializers and inheritance function, and how Swift's `protocol` system enables a programming style that is quite different from Java or C# class hierarchies.

We also revisit **optionals** in depth — they were introduced briefly in Lesson 03 but here we explore them as a full type (`Optional<Int>` is actually an `enum`), covering `if let`, `guard let`, optional chaining, nil-coalescing, and forced unwrapping. Finally we cover **error handling** with `do`/`try`/`catch` and **enums with associated values** — a feature Swift borrowed from Rust and ML that makes modeling complex data both concise and exhaustive.

By the end of this lesson you will understand why a Swift developer reaches for `struct` 95% of the time, why most of the Swift standard library (`Array`, `Dictionary`, `Set`, `String`, `Int`, `Double`, `Bool`) is built from structs not classes, and how protocol extensions enable code reuse without inheritance. Budget extra time — this lesson is the longest in the module.

---

## Learning Objectives

After this lesson, you will be able to:

1. Define **structs** (value types) and **classes** (reference types), explain how they differ on assignment, and choose between them following Apple's "struct by default" guideline.
2. Use stored, computed, lazy, and observed properties, and write initializers for both structs and classes (including calling `super.init()`).
3. Define **protocols**, adopt them in structs and classes, and provide default implementations via **protocol extensions** (protocol-oriented programming).
4. Use all of Swift's optional-handling tools (`if let`, `guard let`, optional chaining `?`, nil-coalescing `??`, forced unwrap `!`) and explain why `Optional` is implemented as an `enum`.

---

## 1. Structs: Value Types

A **struct** in Swift is a **value type**. When you assign a struct to a new variable, pass it to a function, or return it from a function, you get an *independent copy* — not a shared reference. This is the single most important fact about Swift structs.

```swift
struct Point {
    var x: Double
    var y: Double
}

var p1 = Point(x: 3, y: 4)
var p2 = p1              // p2 is a COPY of p1
p2.x = 100               // mutate p2 only
print(p1.x, p2.x)        // 3.0 100.0 — p1 unchanged
```

Line by line:

- `struct Point { var x: Double; var y: Double }` — defines a struct with two stored properties. Structs use the same `var`/`let` distinction as variables: `var x` means the property is mutable when the struct itself is mutable; `let x` would make it always immutable.
- `var p1 = Point(x: 3, y: 4)` — constructs an instance using Swift's auto-generated **memberwise initializer**. The compiler synthesizes `init(x: Double, y: Double)` for you automatically — you do not have to write it.
- `var p2 = p1` — assigns `p1` to `p2`. **Crucially, this is a copy**, not a reference. `p2` is a brand-new struct with its own storage.
- `p2.x = 100` — mutates `p2`'s `x`. Because `p2` is an independent copy, `p1` is unaffected.
- `print(p1.x, p2.x)` — prints `3.0 100.0`. `p1` is still `3.0`.

If you came from Java, C#, JavaScript, or Python (where most user-defined types are reference types by default), this is a big mental shift. In those languages, `var p2 = p1` would create a second reference to the *same* object, and mutating `p2` would also mutate `p1`. In Swift with structs, this never happens — the assignment is a copy.

**Mutating methods.** Methods on a struct that modify the struct's properties must be marked with the `mutating` keyword:

```swift
struct Counter {
    var count: Int = 0

    // Non-mutating method — reads but does not modify
    func current() -> Int {
        return count
    }

    // Mutating method — modifies a property
    mutating func increment() {
        count += 1
    }
}

var c = Counter()
c.increment()
print(c.current())   // 1

let d = Counter()
// d.increment()   // ERROR — d is let, cannot call mutating methods on it
```

- `func current() -> Int { return count }` — a non-mutating method. Reads `count` but does not modify it.
- `mutating func increment() { count += 1 }` — a **mutating** method. The `mutating` keyword tells the compiler "this method modifies the struct's properties." Without it, the compiler refuses to compile `count += 1`.
- `var c = Counter()` — `c` is mutable, so you can call `mutating` methods on it.
- `let d = Counter()` — `d` is a *constant*. Calling `mutating` methods on a constant struct is a compile error: `cannot use mutating member on immutable value: 'd' is a 'let' constant`.

**Why value types are good.** Three reasons. **First, no shared mutable state.** Two variables holding the "same" struct are actually independent copies — mutating one never affects the other. This eliminates an entire class of bugs where you accidentally mutate a value through an unexpected alias. **Second, automatic thread safety.** Because value types have no shared mutable state, you can pass them between threads without synchronization — they cannot race. **Third, predictable behavior.** When you pass a struct to a function, you know the function cannot modify your copy. There is no spooky-action-at-a-distance. This is why Swift's standard library — `Array`, `Dictionary`, `Set`, `String`, `Int`, `Double`, `Bool` — is built almost entirely from structs. Apple's API Guidelines say: "Use structs by default. Use classes only when you specifically need reference semantics."

---

## 2. Classes: Reference Types

A **class** in Swift is a **reference type**. When you assign a class instance to a new variable, you get a *reference* to the same underlying instance — not a copy. Both variables now point at the same object.

```swift
class Account {
    var balance: Double

    init(balance: Double) {
        self.balance = balance
    }
}

let a1 = Account(balance: 100)
let a2 = a1              // a2 is a REFERENCE to the same Account
a2.balance = 500         // mutate through a2
print(a1.balance)        // 500.0 — a1 also sees the change
print(a1 === a2)         // true — same instance in memory
```

Line by line:

- `class Account { var balance: Double; init(balance: Double) { self.balance = balance } }` — defines a class with one stored property `balance` and an explicit initializer. Classes do **not** get an auto-generated memberwise initializer (unlike structs) — you must write `init` yourself.
- `let a1 = Account(balance: 100)` — constructs an instance. `a1` is `let`, but as we saw in Lesson 03, `let` on a class only freezes the *binding* — you can still mutate properties of the instance.
- `let a2 = a1` — `a2` is a reference to the *same* `Account` instance. No copy is made.
- `a2.balance = 500` — mutates the shared instance through `a2`.
- `print(a1.balance)` — prints `500.0`. Because `a1` and `a2` are references to the same instance, the mutation is visible through both.
- `print(a1 === a2)` — `===` is the *identity* operator in Swift. It returns `true` if both references point to the same instance. (Contrast with `==`, which compares values and is something you must implement yourself for classes.)

**`===` vs `==`.** In Swift, `===` checks reference identity (only valid on classes) — "are these the same object in memory?" `==` checks value equality — "do these represent the same value?" For classes, you must implement `==` yourself (by conforming to `Equatable`); the default `==` for classes is reference identity. For structs, the compiler synthesizes `==` for you if all the properties are `Equatable` (and the struct conforms to `Equatable`).

**ARC (Automatic Reference Counting).** Class instances are deallocated when their reference count drops to zero. Swift inserts `retain` and `release` calls at compile time, so there is no garbage-collection pause. The trade-off is **retain cycles**: if two objects hold strong references to each other (a parent → child → parent loop), neither is ever deallocated, and you have a memory leak. Break the cycle with `weak` or `unowned` references:

```swift
class Person {
    var name: String
    var residence: Residence?   // strong reference

    init(name: String) { self.name = name }
}

class Residence {
    var address: String
    weak var occupant: Person?   // weak — does not keep occupant alive

    init(address: String) { self.address = address }
}

let person = Person(name: "Hieu")
let home = Residence(address: "123 Main St")
person.residence = home
home.occupant = person   // would be a retain cycle without `weak`
```

- `weak var occupant: Person?` — a `weak` reference does not keep the referenced object alive. When the strong reference count of `Person` drops to zero, the `Person` is deallocated and `occupant` is automatically set to `nil`. `weak` references must be `Optional` (because they can become `nil`).
- `unowned var occupant: Person` — an `unowned` reference also does not keep the referenced object alive, but it is *not* optional. Use `unowned` when you can guarantee the referenced object outlives the referrer — if you access an `unowned` reference after the object is deallocated, the program crashes.

In practice, `weak` is more common — use it for parent/child back-references, delegates, and observer patterns. `unowned` is used when the lifetime relationship is guaranteed by your app's logic (e.g., a closure that captures `self` but is guaranteed to fire before `self` is deallocated).

---

## 3. When to Use Struct vs Class

Apple's API Guidelines are explicit: **"Use structs by default."** Use classes only when you specifically need reference semantics. Concretely, choose a **struct** when:

- The type's primary purpose is to *hold* data (a `Point`, a `User`, a `Coordinate`, a `Money` value).
- Equality is based on the data, not identity — two `Point(x: 1, y: 2)` values should be `==`.
- The type will be copied on assignment — mutation of one copy should not affect others.
- You want automatic thread safety without synchronization.
- The type is small and short-lived.

Choose a **class** when:

- You need *identity* — multiple references to the same instance, and mutations visible through all of them (a `Database`, a `NetworkSession`, an `Account`).
- You need *inheritance* — sharing behavior with subclasses (only classes support inheritance).
- You need *Objective-C interoperability* — many Apple frameworks expect NSObject subclasses.
- The type has a non-trivial lifecycle (deinit, observers) or large shared state.

Roughly **95% of types in the Swift standard library are structs** — `Int`, `Double`, `Bool`, `String`, `Array`, `Dictionary`, `Set`, `Optional`, `Result`. The only classes you will commonly encounter are UI types (`UIView`, `UIViewController`, `NSWindow`), framework types (`URLSession`, `NotificationCenter`), and `NSObject` subclasses from Objective-C. The shift toward structs is a deliberate Swift design choice — value types eliminate whole categories of bugs (shared mutable state, reference cycles, race conditions) that plagued Objective-C.

A practical test: ask "If I make a copy of this value and mutate the copy, do I expect the original to change?" If the answer is "no, that would be a bug," use a `struct`. If the answer is "yes, that's the whole point — I want all references to see the same state," use a `class`. Most data types fall in the first category. Things like database connections, network sessions, and UI windows fall in the second.

---

## 4. Properties

Swift supports four kinds of properties on structs and classes:

### Stored Properties

```swift
struct User {
    var name: String       // variable stored property
    let id: Int            // constant stored property (set in init only)
    var loginCount = 0     // stored property with default value
}
```

- `var name: String` — variable stored property. Can be mutated if the containing struct is mutable.
- `let id: Int` — constant stored property. Can only be set inside an initializer; after that, immutable.
- `var loginCount = 0` — stored property with a default value of `0`. The default is used if the initializer does not set it.

### Computed Properties

```swift
struct Rectangle {
    var width: Double
    var height: Double

    var area: Double {
        return width * height
    }

    var perimeter: Double {
        width * 2 + height * 2   // single-expression: omit return
    }
}

let r = Rectangle(width: 3, height: 4)
print(r.area)         // 12.0
print(r.perimeter)     // 14.0
```

- `var area: Double { return width * height }` — a **computed property**. No storage; instead, a getter is called every time you read `area`. The body computes the value on demand.
- `var perimeter: Double { width * 2 + height * 2 }` — single-expression computed property. The `return` keyword can be omitted just like in single-expression functions.

Computed properties can also have setters:

```swift
struct Temperature {
    var celsius: Double

    var fahrenheit: Double {
        get { celsius * 9 / 5 + 32 }
        set { celsius = (newValue - 32) * 5 / 9 }
    }
}

var t = Temperature(celsius: 25)
print(t.fahrenheit)   // 77.0
t.fahrenheit = 100
print(t.celsius)       // 37.77...
```

- `get { ... }` — the getter. Called when you read `t.fahrenheit`.
- `set { celsius = (newValue - 32) * 5 / 9 }` — the setter. Called when you assign `t.fahrenheit = 100`. The new value is bound to the implicit `newValue` variable.

### Lazy Stored Properties

```swift
struct ImageProcessor {
    lazy var thumbnail: UIImage = {
        print("Generating thumbnail...")
        return generateThumbnail()
    }()

    func generateThumbnail() -> UIImage {
        // expensive computation
        return UIImage()
    }
}
```

- `lazy var thumbnail: UIImage = { ... }()` — a **lazy** stored property. The initializer (here, a closure that is immediately invoked) is *not* run when the struct is constructed — it runs the first time `thumbnail` is accessed.
- Useful for properties that are expensive to compute and may not always be needed.
- `lazy` requires `var` (not `let`) — the property has no value until first access.
- On classes, `lazy` is not thread-safe by default; on structs it is fine because structs are usually used in single-threaded contexts.

### Property Observers

```swift
struct StepCounter {
    var steps: Int = 0 {
        willSet { print("About to set steps to \(newValue)") }
        didSet { print("Steps changed from \(oldValue) to \(steps)") }
    }
}

var sc = StepCounter()
sc.steps = 100   // prints both the willSet and didSet messages
```

- `willSet { ... }` — runs *before* the value is changed. The new value is bound to `newValue`.
- `didSet { ... }` — runs *after* the value is changed. The old value is bound to `oldValue`.
- Property observers are great for triggering side effects — logging, UI updates, dependent-value caching.

---

## 5. Initializers

An **initializer** is the `init(...)` method that prepares a new instance for use. Structs get a **memberwise initializer** for free; classes require an explicit `init`:

```swift
// Struct — memberwise initializer is auto-generated
struct Player {
    var name: String
    var score: Int
}
let p = Player(name: "Hieu", score: 100)   // works without writing init

// Class — must write init explicitly
class Vehicle {
    var wheels: Int

    init(wheels: Int) {
        self.wheels = wheels
    }
}
let v = Vehicle(wheels: 4)
```

- For `Player`, the compiler synthesizes `init(name: String, score: Int)` automatically — you do not have to write it.
- For `Vehicle`, you must write `init` yourself. The `self.wheels = wheels` assignment sets the property. `self` disambiguates the property from the parameter when they share a name.

**Failable initializers** return `nil` if initialization cannot succeed:

```swift
struct Email {
    let value: String

    init?(value: String) {
        guard value.contains("@") else { return nil }
        self.value = value
    }
}

if let email = Email(value: "hieu@example.com") {
    print(email.value)   // hieu@example.com
}
let bad = Email(value: "not-an-email")
print(bad == nil)   // true
```

- `init?(value: String)` — the `?` after `init` marks this as a *failable* initializer. It returns an `Optional<Email>` — `nil` if validation fails, the value otherwise.
- `guard value.contains("@") else { return nil }` — early exit with `nil` if validation fails.
- The caller uses optional binding (`if let email = Email(...)`) to handle both outcomes.

---

## 6. Inheritance

Only **classes** can inherit from other classes. Structs and enums cannot inherit (they use protocol conformance instead). Subclasses use the `:` syntax and can override parent methods with the `override` keyword:

```swift
class Animal {
    var name: String

    init(name: String) {
        self.name = name
    }

    func speak() -> String {
        return "\(name) makes a sound"
    }
}

class Dog: Animal {
    var breed: String

    init(name: String, breed: String) {
        self.breed = breed
        super.init(name: name)   // must call parent's init
    }

    override func speak() -> String {
        return "\(name) the \(breed) barks: Woof!"
    }
}

let dog = Dog(name: "Rex", breed: "Labrador")
print(dog.speak())   // Rex the Labrador barks: Woof!
```

- `class Animal { ... }` — base class. Defines `name` and `speak()`.
- `class Dog: Animal { ... }` — `Dog` inherits from `Animal`. The `:` syntax is the same as Java, C#, and Kotlin.
- `init(name: String, breed: String)` — `Dog`'s initializer takes both `name` and `breed`.
- `self.breed = breed` — assigns `Dog`'s own property *before* calling super.init (Swift requires this for safety).
- `super.init(name: name)` — calls the parent's initializer. Required: a subclass must initialize itself, then call `super.init` to finish initializing the parent's properties.
- `override func speak() -> String { ... }` — the `override` keyword is *required* when overriding a parent method. Forgetting it is a compile error. The compiler also refuses to compile if you write `override` on a method that does not actually override anything (a typo check).

**Final classes.** Mark a class with `final` to prevent subclassing:

```swift
final class ImmutableAccount {
    // cannot be subclassed
}
```

- `final class ImmutableAccount` — no class can inherit from this. Use `final` aggressively — it enables compiler optimizations and prevents accidental inheritance.

---

## 7. Protocols

A **protocol** is a contract — a list of methods, properties, and initializers that any conforming type must provide. Protocols are similar to interfaces in Java and C#, traits in Rust, and type classes in Haskell — but Swift's protocols have one superpower that Java/C# interfaces do not: **default implementations via protocol extensions** (covered in the next section).

```swift
protocol Greeter {
    var name: String { get }               // read-only property requirement
    func greet() -> String                 // method requirement
}

struct EnglishGreeter: Greeter {
    let name: String
    func greet() -> String { "Hello, \(name)!" }
}

struct FrenchGreeter: Greeter {
    let name: String
    func greet() -> String { "Bonjour, \(name)!" }
}

func sayHello(to g: Greeter) {
    print(g.greet())
}

sayHello(to: EnglishGreeter(name: "Hieu"))   // Hello, Hieu!
sayHello(to: FrenchGreeter(name: "Hieu"))    // Bonjour, Hieu!
```

Line by line:

- `protocol Greeter { var name: String { get }; func greet() -> String }` — defines the `Greeter` protocol. It requires a readable `name` property (the `{ get }` declares read access; `{ get set }` would require read-write) and a `greet()` method returning `String`.
- `struct EnglishGreeter: Greeter { ... }` — `EnglishGreeter` *adopts* the `Greeter` protocol. The `:` syntax is the same as for inheritance, but for structs it means protocol adoption (structs cannot inherit, but they can adopt protocols).
- `struct FrenchGreeter: Greeter { ... }` — another conforming type.
- `func sayHello(to g: Greeter) { print(g.greet()) }` — the function takes *any* type conforming to `Greeter`. Both `EnglishGreeter` and `FrenchGreeter` are valid arguments. This is **polymorphism** — the function works on any type that satisfies the protocol.

A struct or class can adopt **multiple** protocols, separated by commas:

```swift
struct User: Greeter, Equatable, CustomStringConvertible {
    let name: String
    func greet() -> String { "Hi, I'm \(name)" }
    var description: String { "User(\(name))" }
}

let u1 = User(name: "Hieu")
let u2 = User(name: "Hieu")
print(u1 == u2)         // true — Equatable synthesized
print(String(describing: u1))   // User(Hieu) — CustomStringConvertible
```

- `struct User: Greeter, Equatable, CustomStringConvertible` — adopts three protocols.
- `Equatable` — the compiler synthesizes `==` for structs whose properties are all `Equatable`. So `u1 == u2` returns `true`.
- `CustomStringConvertible` — requires a `var description: String { get }` property. Used by `String(describing:)` and `print` to produce a custom string representation.

Protocols are the backbone of Swift's type system. They let you write code that works on *any* type satisfying the contract, without inheritance and without reference types. Apple's API Guidelines say: "When designing a protocol, prefer composition over inheritance."

---

## 8. Protocol-Oriented Programming

**Protocol-oriented programming (POP)** is Swift's signature design philosophy, articulated in Apple's famous WWDC 2015 talk "Protocol-Oriented Programming in Swift." The idea is to design your software around small composable protocols with **default implementations** provided by protocol extensions, rather than around deep class hierarchies with inheritance.

```swift
protocol Describable {
    func describe() -> String
}

// Default implementation via protocol extension
extension Describable {
    func describe() -> String {
        return "A \(type(of: self))"
    }
}

struct Car: Describable { }   // gets describe() for free
struct Book: Describable {
    var title: String
    func describe() -> String {   // override the default
        return "Book: \(title)"
    }
}

print(Car().describe())           // A Car
print(Book(title: "1984").describe())   // Book: 1984
```

Line by line:

- `protocol Describable { func describe() -> String }` — declares the protocol with a `describe()` method requirement.
- `extension Describable { func describe() -> String { return "A \(type(of: self))" } }` — provides a *default implementation* of `describe()` via a protocol extension. Any type that adopts `Describable` automatically gets this implementation, unless it overrides it.
- `struct Car: Describable { }` — `Car` adopts `Describable` but provides no `describe()`. It uses the default implementation, returning `"A Car"`.
- `struct Book: Describable { var title: String; func describe() -> String { return "Book: \(title)" } }` — `Book` provides its own `describe()`, overriding the default.
- `type(of: self)` — returns the dynamic type of `self`. For a `Car` instance, this is `Car`.

This is the key difference from Java/C# interfaces: protocols can provide default method implementations *without* requiring the conforming type to write boilerplate. The result is code reuse without inheritance. You compose small protocols (each doing one thing) and let your types adopt the ones they need, picking up default implementations automatically.

```swift
// Composing small protocols
protocol Named { var name: String { get } }
protocol Aged { var age: Int { get } }

// A protocol can require conformance to other protocols
protocol Person: Named, Aged { }

struct Employee: Person {
    let name: String
    let age: Int
    let role: String
}

// Generic function constrained to Person (which requires Named + Aged)
func birthday<T: Person>(for p: T) {
    print("Happy birthday, \(p.name)! You are now \(p.age).")
}
```

- `protocol Person: Named, Aged { }` — `Person` *composes* `Named` and `Aged`. Any type conforming to `Person` must satisfy both.
- `func birthday<T: Person>(for p: T)` — a generic function constrained to types conforming to `Person`. Generic constraints replace Java's `<? extends Person>` syntax.

POP is too large a topic for one lesson, but the key takeaway is: **prefer protocol + extension over inheritance**. Define small protocols with default implementations. Let your types adopt multiple protocols. Use protocol composition (`&`) and generic constraints (`where T: Protocol`) instead of class hierarchies. This produces code that is more flexible, more testable, and less tightly coupled than deep inheritance trees.

---

## 9. Optionals — Deep Dive

We met optionals briefly in Lesson 03. Now let's look at them in depth, because understanding optionals is essential to reading Swift code.

`Int?` is **syntactic sugar** for `Optional<Int>`. `Optional` is itself an `enum` with two cases:

```swift
enum Optional<Wrapped> {
    case none              // represents nil
    case some(Wrapped)     // represents a value
}
```

- `case none` — the absence of a value. This is what `nil` is.
- `case some(Wrapped)` — the presence of a value. `Wrapped` is the inner type.

So `Int?` is just an `enum` of `.none` (which we write as `nil`) or `.some(42)` (which we write as `42`). Optionals are *not* a special language feature bolted on — they are a regular `enum` in the standard library, and the `?` syntax is just sugar. This is one of Swift's most elegant design choices: a major language feature is implemented as a regular library type.

**Forced unwrap `!`.** You can forcibly unwrap an optional with `!`:

```swift
let maybeAge: Int? = 25
let age = maybeAge!   // 25 — unwrap with !
print(age)

let nothing: Int? = nil
// let crash = nothing!   // CRASH — fatal error: unexpectedly found nil
```

- `let age = maybeAge!` — unwraps the optional. If `maybeAge` is `.some(25)`, `age` is `25`.
- `let crash = nothing!` — if `nothing` is `nil`, this *crashes your program* with `fatal error: unexpectedly found nil while unwrapping an Optional value`. **Never force-unwrap unless you are 100% certain the value is non-nil.**

**Optional binding `if let`.** The safe way to unwrap:

```swift
let maybeName: String? = "Hieu"

if let name = maybeName {
    print("Hello, \(name)!")   // runs only if maybeName was non-nil
} else {
    print("No name provided")
}
```

- `if let name = maybeName { ... }` — if `maybeName` is `.some(value)`, the value is unwrapped and bound to `name`, then the `if` body runs. If `maybeName` is `nil`, the body is skipped and the `else` branch runs.

**`guard let` for early exit.** `guard` is the inverse of `if let` — it lets you exit a function early if an optional is nil:

```swift
func greet(name: String?) {
    guard let name = name else {
        print("No name provided")
        return
    }
    print("Hello, \(name)!")
    // name is available as a non-optional String from here on
}

greet(name: "Hieu")        // Hello, Hieu!
greet(name: nil)           // No name provided
```

- `guard let name = name else { print(...); return }` — if `name` (the optional parameter) is `nil`, the `else` branch runs and we `return`. If it is non-nil, `name` is unwrapped to a non-optional `String` and is available for the rest of the function — *not* just inside the `if` body, like with `if let`. This is why `guard` is preferred for "early exit" patterns.

**Optional chaining `?`.** Use `?` to safely access properties/methods of an optional. If any link in the chain is nil, the whole expression short-circuits to nil:

```swift
class Address { var city: String = "Hanoi" }
class Person { var address: Address? = nil }

let person = Person()
print(person.address?.city ?? "Unknown")   // Unknown

person.address = Address()
print(person.address?.city ?? "Unknown")   // Hanoi
```

- `person.address?.city` — `person.address` is `Address?` (optional). The `?` means "if `address` is nil, stop and return nil; if it's non-nil, access `.city`." The result is `String?` (optional).
- `?? "Unknown"` — **nil-coalescing operator**. If the left side is nil, use the right side. So if `address` is nil, the result is `"Unknown"`.

**Nil-coalescing `??`.** Provides a default value when an optional is nil:

```swift
let nickname: String? = nil
let displayName = nickname ?? "Anonymous"
print(displayName)   // Anonymous
```

- `nickname ?? "Anonymous"` — if `nickname` is non-nil, `displayName` is the unwrapped value. If nil, `displayName` is `"Anonymous"`. The result type is `String` (non-optional), because we have provided a fallback.

**When to use which.** Use `if let` when you want to run code only in the non-nil case. Use `guard let` when you want to early-exit on nil and continue otherwise. Use `??` when you have a sensible default. Use optional chaining `?` for safe property/method access on nested optionals. Use `!` *only* when you have a logical guarantee that the value is non-nil — and even then, prefer `guard let` to make the assumption explicit.

---

## 10. Error Handling

Swift's error handling uses **errors as values** via the `throws`/`try`/`catch` mechanism. We introduced this in Lesson 05; let's go deeper here.

Define errors with an enum conforming to `Error`:

```swift
enum NetworkError: Error {
    case badURL
    case timeout
    case unauthorized
    case http(statusCode: Int)   // associated value
}
```

- `enum NetworkError: Error { ... }` — an error enum. Each case is a distinct error. The `: Error` conformance marks it as throwable.
- `case http(statusCode: Int)` — an error with an *associated value*. The case can carry data.

Throw and catch:

```swift
func fetch(_ url: String) throws -> String {
    guard url.hasPrefix("https://") else {
        throw NetworkError.badURL
    }
    if url.contains("slow") {
        throw NetworkError.timeout
    }
    return "200 OK"
}

do {
    let response = try fetch("https://example.com/data")
    print(response)   // 200 OK
} catch NetworkError.badURL {
    print("Bad URL")
} catch NetworkError.timeout {
    print("Timed out")
} catch NetworkError.http(let code) {
    print("HTTP \(code)")
} catch {
    print("Unknown error: \(error)")
}
```

- `func fetch(_ url: String) throws -> String` — marked `throws`.
- `throw NetworkError.badURL` — raises the error. The function exits immediately.
- `do { let response = try fetch(...) }` — `try` is required before any call to a throwing function.
- `catch NetworkError.http(let code)` — pattern matching on the case, binding the associated value to `code`.
- `catch { print("Unknown error: \(error)") }` — the catch-all. `error` is an implicit variable holding the thrown error.

**`Result` type.** For non-throwing APIs (e.g., async callbacks), Swift provides the built-in `Result<Success, Failure>` type — an enum with `.success(value)` and `.failure(error)` cases:

```swift
func fetchUser(id: Int, completion: (Result<String, NetworkError>) -> Void) {
    if id < 0 { completion(.failure(.badURL)) }
    else { completion(.success("User \(id)")) }
}

fetchUser(id: 42) { result in
    switch result {
    case .success(let name): print("Got: \(name)")
    case .failure(let error): print("Failed: \(error)")
    }
}
```

- `(Result<String, NetworkError>) -> Void` — instead of throwing, the function takes a completion callback.
- The caller pattern-matches with `switch` to extract either the success value or the error.

---

## 11. Enums with Associated Values

Swift enums are much more powerful than C or Java enums. They can carry **associated values** — like Rust's enums and Kotlin's sealed classes:

```swift
enum Weather {
    case sunny(temperature: Int)
    case rainy(inches: Double)
    case cloudy(coverage: Double)
    case snowy(inches: Double, temp: Int)
}

let today = Weather.snowy(inches: 3.5, temp: 28)

switch today {
case .sunny(let temp):
    print("Sunny at \(temp)°F")
case .rainy(let inches):
    print("\(inches) inches of rain")
case .cloudy(let coverage):
    print("\(coverage * 100)% cloud cover")
case .snowy(let inches, let temp):
    print("\(inches) inches of snow at \(temp)°F")
}
```

Line by line:

- `enum Weather { case sunny(temperature: Int); case rainy(inches: Double); ... }` — an enum where each case carries different data. `sunny` has a temperature, `rainy` has inches, `snowy` has both inches and temperature.
- `let today = Weather.snowy(inches: 3.5, temp: 28)` — creates a `Weather` value of the `.snowy` case with associated data.
- `switch today { case .snowy(let inches, let temp): print(...) }` — pattern matching. Each case binds the associated values to constants (`inches`, `temp`) and prints them.
- The leading `.` in `.sunny(...)` is shorthand for `Weather.sunny(...)` — when the type is known, you can omit the enum name.

Because `switch` must be exhaustive, the compiler forces you to handle every case. If you add a new case to `Weather` later (say, `case tornado`), every `switch` on `Weather` fails to compile until you handle the new case. This is a powerful safety feature — it ensures you never forget a case when the enum evolves.

**Optional revisited.** `Optional` itself is just an enum with associated values, exactly like our `Weather` example. `Optional<Int>.some(42)` and `Optional<Int>.none` are the two cases. When you write `if let x = maybeX`, you are pattern-matching on `Optional.some`, binding the associated value to `x`. Optionals feel like a magic language feature, but they are just an ordinary enum with pattern matching — one of Swift's most beautiful design choices.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 10–15 minutes each.

### Exercise 1: Struct vs Class

Define a `struct Point { var x: Double; var y: Double }` and a `class Box { var value: Int = 0 }`. Declare `var p1 = Point(x: 1, y: 2); var p2 = p1; p2.x = 99` and print `p1.x` and `p2.x` — confirm they are different (copy semantics). Then declare `let b1 = Box(); let b2 = b1; b2.value = 99` and print `b1.value` and `b2.value` — confirm they are the same (reference semantics). Add `print(b1 === b2)` to verify identity.

### Exercise 2: Protocol-Oriented Design

Define a protocol `Animal { var name: String { get }; func sound() -> String }`. Provide a default implementation of `sound()` via a protocol extension that returns `"<name> is silent"`. Then write `struct Dog: Animal { let name: String }` (uses default) and `struct Cat: Animal { let name: String; func sound() -> String { "\(name) says meow" } }` (overrides). Write a function `func describe(_ a: Animal)` that calls both `name` and `sound()`. Pass a `Dog` and a `Cat` to it and confirm the output.

### Exercise 3: Optional Chaining

Define `class User { var name: String = ""; var address: Address? = nil }` and `class Address { var city: String = "" }`. Create a `User` without an address and use optional chaining to safely access `user.address?.city` — fall back to `"Unknown"` with `??`. Then set `user.address = Address(); user.address?.city = "Hanoi"` and confirm the same expression now returns `"Hanoi"`. Then write `if let city = user.address?.city { print(city) }` to confirm optional binding works through the chain.

---

## Common Mistakes

### Mistake 1: Overusing classes when structs would work

```swift
// WRONG — class for a pure data type
class Point {
    var x: Double
    var y: Double
    init(x: Double, y: Double) { self.x = x; self.y = y }
}
// Class gives reference semantics — copying a Point unexpectedly shares state
```

```swift
// RIGHT — use a struct for data types
struct Point {
    var x: Double
    var y: Double
}
// Memberwise initializer is free; value semantics make copying safe
```

If you are coming from Java or C# (Modules 16 or 17), your instinct is to make everything a class. Swift's instinct is the opposite — make everything a struct unless you have a specific reason for a class. Use a class only when you need: reference identity (`===`), inheritance, `deinit`, or Objective-C interop. For pure data types (Point, User, Money, Coordinate), use a struct. You get value semantics (safe copying), a free memberwise initializer, automatic `Equatable` synthesis, and no retain cycles.

### Mistake 2: Expecting structs to inherit (they can't)

```swift
// WRONG — structs cannot inherit from other structs
struct Base { var x: Int }
struct Derived: Base { var y: Int }   // ERROR — inheritance from non-protocol type
```

```swift
// RIGHT — use protocols for shared behavior across structs
protocol HasX { var x: Int { get } }

struct Base: HasX { var x: Int }
struct Derived: HasX { var x: Int; var y: Int }
```

Structs and enums in Swift **cannot inherit** from other structs or enums — only classes can inherit. The `:` syntax on a struct is exclusively for adopting *protocols*. If you want shared behavior across structs, define a protocol and provide default implementations via protocol extensions. This forces protocol-oriented design (which Apple recommends anyway). The compiler error is `inheritance from non-protocol, non-class type 'Base'`.

### Mistake 3: Forced-unwrapping optionals with `!`

```swift
// DANGEROUS — will crash if the value is nil
let input = readLine()
let number = Int(input!)   // CRASH if input is nil
```

```swift
// RIGHT — use guard let or if let
guard let input = readLine() else { print("No input"); return }
guard let number = Int(input) else { print("Not a number"); return }
print(number)
```

`!` (forced unwrap) is the "I'm sure this is non-nil" operator. If you are wrong, your program crashes. Use `!` *only* when you have a logical guarantee — for example, an IBOutlet that the system has wired up by the time `viewDidLoad` is called. For every other case (user input, network responses, JSON parsing, file reads), use `guard let` or `if let`. The same applies to `try!` (force-try) and `as!` (force-cast) — prefer `try?` and `as?`.

### Mistake 4: Forgetting `mutating` on struct methods that modify properties

```swift
// WRONG — modifying a struct property requires mutating
struct Counter {
    var count: Int = 0
    func increment() {   // ERROR — cannot mutate without mutating keyword
        count += 1
    }
}
```

```swift
// RIGHT — mark the method as mutating
struct Counter {
    var count: Int = 0
    mutating func increment() { count += 1 }
}
```

In Swift, methods on structs are non-mutating by default — they cannot modify the struct's properties. To allow mutation, you must mark the method with `mutating`. The compiler error is `cannot assign to property: 'self' is immutable`. The same rule applies to enums with `mutating func`. Classes do not need `mutating` because they are reference types.

---

## Summary

- **Structs are value types** (copied on assignment); **classes are reference types** (shared on assignment). Apple's guideline: **use structs by default**.
- Structs get a free **memberwise initializer**; classes require an explicit `init`. Struct methods that modify properties must be marked `mutating`.
- Classes use **ARC** (Automatic Reference Counting — not garbage collection). Watch out for retain cycles, broken with `weak` or `unowned` references.
- Properties come in four kinds: **stored** (`var x: Int`), **computed** (`var area: Double { width * height }`), **lazy** (`lazy var`), and **observed** (`willSet`/`didSet`).
- **Only classes can inherit.** Subclasses use the `:` syntax, override methods with `override`, and must call `super.init`. Mark a class `final` to prevent subclassing.
- **Protocols** are contracts (like Java interfaces). Structs and classes adopt protocols with the `:` syntax. A type can adopt multiple protocols.
- **Protocol-oriented programming** — provide default implementations via protocol extensions. Compose small protocols instead of inheriting deeply. This is Swift's signature design philosophy.
- `Optional<Wrapped>` is an `enum` with `.none` (nil) and `.some(Wrapped)` cases. `Int?` is sugar for `Optional<Int>`. Use `if let` for safe unwrap, `guard let` for early exit, optional chaining `?` for safe property access, nil-coalescing `??` for defaults, and forced unwrap `!` *only* when you have a logical guarantee.
- **Error handling** uses `throws`/`try`/`catch` with `Error`-conforming enums. For async-style APIs, use the `Result<Success, Failure>` type. Errors are values, not exceptions for control flow.
- **Enums with associated values** are powerful — `case http(statusCode: Int)` carries data. The `switch` must be exhaustive, so adding a new case forces you to handle it everywhere. `Optional` itself is implemented this way.

You have now learned the heart of Swift — value types, reference types, protocols, optionals, and error handling. In Lesson 07 we put it all together with a complete cheatsheet: every Swift idiom you will reach for daily, plus the most common standard library types (`Array`, `Dictionary`, `Set`, `Codable`, `JSONEncoder`, `JSONDecoder`), common pitfalls, and the most-used standard library tools.

---

**Next:** [Lesson 07: Swift Cheatsheet →](./07-swift-cheatsheet.md)
