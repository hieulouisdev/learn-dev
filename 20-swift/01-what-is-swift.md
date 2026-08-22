# Lesson 01: What is Swift?

> Module: Swift · Lesson 1 of 7
> Estimated time: 30–45 minutes

Welcome to your first Swift lesson. In the next 30 minutes you will learn what Swift is, where it came from, why Chris Lattner and Apple built it, how it relates to Objective-C (the language it replaced), how to install it on your machine, and how to write and run your first program. By the end you will have typed `print("Hello, World!")` into a real Swift file and watched the Swift compiler turn it into a native binary.

You do not need any prior Apple or Objective-C experience for this lesson. If you completed any of the Python, Rust, Go, TypeScript, Java, C++, or Kotlin modules, you already understand variables, functions, and control flow — and that is more than enough. Swift uses C-style syntax (curly braces, `if`/`for`/`while` keywords) but adds modern features like optionals, value types, and protocol-oriented programming that we will explore across the module. Take a deep breath, open a terminal, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what Swift is, where it came from, and why Chris Lattner and Apple built it.
2. Describe Swift's relationship to Objective-C and what Swift eliminates (pointers, header files, manual `nil` checks).
3. Install Swift (via Xcode, Command Line Tools, swift.org, or the online playground) and verify with `swift --version`.
4. Write your first Swift program (`hello.swift`), run it as a script with `swift hello.swift`, and compile it with `swiftc hello.swift -o hello`.

---

## 1. What is Swift, Really?

**Swift** is a **statically-typed, compiled, general-purpose programming language** created by **Chris Lattner** at **Apple** starting in **2010**. "Statically-typed" means every variable has a type known to the compiler at compile time, like Rust, Go, Kotlin, and TypeScript — type errors are caught before your program runs. "Compiled" means Swift source files (`.swift`) are translated directly into native machine code by LLVM, producing a `.exe`, `.app`, or ELF binary — there is no virtual machine, no interpreter, no JIT. "General-purpose" means Swift is not tied to one domain: it powers iOS and macOS apps, server-side web apps via Vapor, scripts, command-line tools, and even embedded systems research.

The language was started in **2010** by **Chris Lattner** — a name worth knowing. Lattner is the engineer who, as a graduate student at the University of Illinois Urbana-Champaign in 2000, created **LLVM** (Low Level Virtual Machine), the compiler infrastructure that now powers Clang, Rust, Julia, Zig, and many other modern languages. Apple hired Lattner in 2005 to lead LLVM integration into Apple's developer tools. By 2010 Lattner was a Director of Developer Tools at Apple, with deep knowledge of Objective-C's pain points — every Objective-C developer griped about pointer-heavy syntax, header files, the lack of namespaces, and the constant `nil` crashes. He began sketching a new language, working on it privately during evenings and weekends, and pitched the idea internally at Apple in **July 2010**.

Apple gave Lattner a small team and let the project stay secret for almost four years. The first public reveal of Swift happened on **June 2, 2014**, during the **WWDC** (Worldwide Developers Conference) keynote at Moscone West in San Francisco. The audience of Apple developers watched in shock as Lattner demoed a language that looked more like Python or Rust than Objective-C — no header files, no `[bracket message syntax]`, no pointers, type inference, optionals, and a `print("Hello, World!")` that fit on one line. **Swift 1.0** shipped on **September 9, 2014**, the same day iOS 8 launched. Apple immediately encouraged iOS developers to start writing new code in Swift.

The next pivotal moment was **December 3, 2015**, when Apple **open-sourced Swift** under the **Apache 2.0 license** and launched [swift.org](https://www.swift.org). Before that day Swift was a closed, Apple-only language. After it, anyone could contribute, port, or fork Swift — and ports to Linux, Windows, and embedded platforms followed within months. The third major milestone was **Swift 5.0** on **March 25, 2019**, which brought **ABI stability**. ABI (Application Binary Interface) stability means apps no longer need to bundle the entire Swift runtime inside every `.ipa` and `.app` — the operating system itself ships a stable Swift runtime, so app binaries are smaller and compiled libraries work across Swift versions. Today Swift is at version 5.9+, with **Swift 6** bringing strict concurrency checking.

The name **"Swift"** was chosen to evoke the **swift bird** — a small, fast-flying bird found across most of the world, capable of flying at over 100 km/h and spending most of its life airborne. The name signals the language's two goals: speed of execution (Swift benchmarks close to C++ for many tasks because it compiles to native code via LLVM) and speed of development (concise syntax with type inference). The Swift logo — a stylized bird in flight — was designed by Apple's marketing team for the WWDC 2014 reveal and has remained the language's visual identity since.

---

## 2. Relationship to Objective-C

Swift **replaces** Objective-C as Apple's recommended language for app development — but it does so without breaking the past. **Objective-C** is a general-purpose language dating back to the early 1980s, created by Brad Cox and Tom Love at Stepstone. It is a thin layer of Smalltalk-style object-oriented message-sending syntax layered on top of plain C. Apple inherited Objective-C when it bought NeXT in 1996 — NeXT's operating system, NeXTSTEP, became the foundation of Mac OS X, and Objective-C became the language of macOS and (later) iOS development. For nearly 30 years, every iPhone app was written in Objective-C.

Swift's design goal was to *keep the Objective-C runtime model* (so existing Objective-C libraries still work) but *replace the language on top of it* with something modern. The result is that Swift and Objective-C can coexist in the same Xcode project — you can have a Swift file calling Objective-C code and an Objective-C file calling Swift code via a "bridging header." When Apple rewrote the Stocks app, Calculator, and many of its own apps in Swift, they did it file by file, not as a giant rewrite. If you ever inherit an old iOS codebase, you will likely see a mix of Swift and Objective-C.

What does Swift eliminate? Several painful things Objective-C developers had to live with. **No more header files** — Swift uses a single `.swift` file per unit, no `.h`/`.m` split. **No more `[bracket message syntax]`** — Swift uses `object.method(args)`, like every other modern language. **No more pointers everywhere** — Swift hides pointers behind safe abstractions, and you only see them in low-level interop code. **No more manual `nil` checks** — Swift's optionals make the compiler enforce that you handle the absence of a value before you use it. **No more `id` type erasure** — Swift has generics, so `[String]` is a real array of strings, not a loosely-typed `NSArray` of `id`. The result is a language that is dramatically safer and more concise than Objective-C while running on the same runtime.

---

## 3. Installing Swift

There are four main paths to install Swift. Pick whichever fits your workflow and operating system.

### Path A: Xcode on Mac (recommended for beginners)

If you are on a Mac, **install Xcode from the Mac App Store**. Xcode is Apple's free IDE and it bundles everything: the Swift compiler (`swiftc`), the Swift Package Manager, the iOS Simulator, the SwiftUI visual editor, the Interface Builder for UIKit, and a feature called **Swift Playgrounds** that lets you write Swift interactively. The download is multi-gigabyte, so start it and let it finish. Once Xcode is installed, open Terminal and verify:

```bash
swift --version
```

You should see something like `Apple Swift version 5.9.2 (swiftlang-5.9.2.2.108)`. If you see `command not found`, you may need to launch Xcode once to accept the license agreement — run `sudo xcodebuild -license` and follow the prompts. After that, `swift` and `swiftc` work from any Terminal window.

### Path B: Command Line Tools only (smaller download)

If you do not want the full Xcode IDE (you prefer VS Code, Vim, or another editor), install just the **Command Line Tools**. In Terminal, run:

```bash
xcode-select --install
```

A dialog appears — click Install and wait a few minutes. This gives you `swift`, `swiftc`, `git`, `clang`, and other command-line developer tools without the full Xcode IDE. It is roughly 1.5 GB instead of 30+ GB. Verify with `swift --version`.

### Path C: Linux or Windows (official swift.org toolchain)

If you are on **Linux** (Ubuntu, CentOS, Amazon Linux, Fedora) or **Windows 10/11**, head to [swift.org/install](https://www.swift.org/install/) and download the official toolchain for your platform. The Linux toolchains are fully supported by Apple — they are built from the exact same source tree as the Mac toolchains. After downloading, extract the archive to a location like `/opt/swift`, add `/opt/swift/usr/bin` to your PATH, and verify:

```bash
swift --version
```

On Ubuntu you may need to install a few dependencies first (`libcurl4`, `libpython3`, `libxml2`). The swift.org download page lists the exact `apt install` command for your distribution. On Windows, run the official `.exe` installer — it adds Swift to your PATH automatically.

### Path D: Online Playground (no installation)

If you do not want to install anything yet, head to [swiftfiddle.com](https://swiftfiddle.com) — the online Swift Playground. You can write Swift in a browser text box, click Run, and see the output without installing anything. It is the fastest way to try a code sample from a lesson. The underlying engine runs the official Swift toolchain in a sandbox, so the syntax you practice here matches what `swift` does on your laptop.

Whichever path you choose, verify with both commands. `swift --version` shows the version of the Swift driver; `swiftc --version` shows the underlying compiler:

```bash
swift --version
swiftc --version
```

Both should report a version number starting with `5.9` or higher for this module. If you see `command not found`, close every terminal and open a fresh one so the PATH update takes effect.

---

## 4. Your First Program

It is finally time to write Swift. Create a folder for your Swift experiments, and inside it create a file called `hello.swift` (the `.swift` extension marks a Swift source file). Type this exact code:

```swift
print("Hello, World!")
```

Save the file. Notice what is *missing* compared to Java or C++ — there is no `public class HelloWorld { public static void main(String[] args) { ... } }` wrapper. Swift allows **top-level code** in a script file, so `print("Hello, World!")` is a complete program on its own. There is also no `import` statement needed for `print` — it lives in Swift's standard library, which is implicitly available in every Swift file. This is the simplest "Hello, World" in any compiled language.

Now run the program. Swift has two modes — you can run a script directly (interpreted on-the-fly by the Swift driver) or compile to a native binary first. The script mode is fastest for experimentation:

```bash
swift hello.swift
```

You should see this printed in your terminal:

```text
Hello, World!
```

To compile the same file into a native binary, use `swiftc` (the Swift compiler):

```bash
swiftc hello.swift -o hello
```

- `swiftc` — the Swift compiler. It translates `.swift` source files into LLVM IR, then into native machine code.
- `hello.swift` — the input source file.
- `-o hello` — the `-o` flag tells the compiler where to put the output binary. We name it `hello`.

After compilation finishes (a fraction of a second for a file this small), you should see a new file called `hello` in your folder. Run it like any other executable:

```bash
./hello
```

You should see `Hello, World!` printed again. **Congratulations. You just wrote, ran as a script, compiled, and executed your first Swift program.**

Let's break the program into its parts:

```swift
print("Hello, World!")
```

- `print` — a function in Swift's standard library that prints its argument to standard output, followed by a newline. Notice there is no `System.out.` prefix like Java, no `console.log` like JavaScript, no `fmt.Println` like Go. `print` is a top-level function available everywhere.
- `(` — opens the function call. Like C, Java, Kotlin, and most C-style languages, Swift uses parentheses for function calls.
- `"Hello, World!"` — a string literal. Swift strings use double quotes, like Python, Java, and most languages. The string is one argument passed to `print`.
- `)` — closes the function call.
- The newline ends the statement. Semicolons are *optional* in Swift — you only need them if you put two statements on the same line (which is rare). Best practice: omit semicolons entirely on single statements.

That is the whole program. Notice how clean it is. There is no boilerplate, no class wrapper, no `import` of a `System` module, no `main` function declaration — Swift treats a single expression at the top level of a `.swift` file as the entry point when run as a script. (In a larger compiled module, you would use the `@main` attribute on a struct or enum to declare the entry point — we cover this in Lesson 02.) This is the simplest "Hello, World" of any compiled language, and it is a deliberate choice: Swift wants to feel as approachable as Python while delivering the performance of C++.

---

## 5. Swift's Big Ideas

Swift is built around five big ideas that distinguish it from Objective-C, Java, and most other mainstream languages:

**Type safety.** Swift is statically-typed and strict — you cannot pass an `Int` where a `String` is expected, and the compiler refuses to compile code that might dereference a null. This catches an entire class of bugs at compile time that dynamic languages (Python, JavaScript) only catch at runtime. Swift's type inference means you rarely have to write the types yourself — `let name = "Hieu"` is enough — but the compiler always knows the type and enforces it.

**Optionals.** Swift's signature feature. `String` cannot be `nil`; `String?` can. The `?` is part of the type — they are *different* types. To use an optional's value, you must explicitly unwrap it (via `if let`, `guard let`, `??`, or `!`). The compiler refuses to compile code that might dereference a nil. This eliminates the entire category of `NullPointerException` crashes that plague Java and `EXC_BAD_ACCESS` crashes that plagued Objective-C. We explore optionals in depth in Lessons 03 and 06.

**Value types by default.** Most of Swift's standard library — `Array`, `Dictionary`, `Set`, `String`, `Int`, `Double`, `Bool` — are **structs**, not classes. Structs are **value types**: when you assign one to a new variable or pass it to a function, you get an *independent copy*, not a shared reference. This means no spooky-action-at-a-distance: mutating one variable never affects another. Lesson 06 explores this in depth, but the practical effect is that Swift code is naturally thread-safe and predictable.

**Protocol-oriented programming.** Swift encourages you to design with **protocols** (similar to interfaces in Java or traits in Rust) rather than deep class hierarchies. You write small composable protocols, give them default implementations via protocol extensions, and have your types adopt multiple protocols. Apple famously called this "protocol-oriented programming" in a 2015 WWDC talk, and it has become Swift's signature design philosophy.

**ARC memory management (not garbage collection).** Swift uses **Automatic Reference Counting** — at compile time, the compiler inserts `retain` and `release` calls into your code that increment and decrement a reference count on each object. When the count reaches zero, the object is deallocated *immediately*, not at some future garbage-collection pause. ARC is *not* garbage collection — there is no runtime tracing, no stop-the-world pause, no separate GC thread. The trade-off is that you must occasionally think about **retain cycles** (two objects holding strong references to each other) and break them with `weak` or `unowned` references, which we cover in Lesson 06.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Verify Your Installation

Open a terminal and run `swift --version`. Write down the version number you see. If it is anything below 5.9, follow the install instructions in Section 3 to upgrade. Then run `swiftc --version` to confirm the underlying compiler also reports a 5.9+ version. If both commands work and `swift --version` reports 5.9 or newer, your Swift installation is complete and ready for the rest of the module. If you are using the swiftfiddle.com online playground, you can pick the Swift version from a dropdown in the top-right corner.

### Exercise 2: Print Something Different

Open your `hello.swift` file and change the message from `"Hello, World!"` to `"My name is [Your Name] and I am learning Swift."`. Save the file, run it with `swift hello.swift`, and confirm your custom message appears. Then compile it with `swiftc hello.swift -o hello` and run `./hello` — notice that the compiled binary is fast to start (typically under 5 ms) because there is no runtime to spin up. Notice also that after editing the source, you must re-run `swift hello.swift` or recompile with `swiftc` to see the new output — Swift is a compiled language, not interpreted.

### Exercise 3: Two `print` Lines

Add a second `print` line below the first. Your program should look like this:

```swift
print("Hello, World!")
print("I am learning Swift.")
```

Run with `swift hello.swift`. Confirm both lines are printed, each on its own line — `print` automatically adds a newline after each call. Then experiment: read ahead in Lesson 02 if you want, but try replacing `print(...)` with `print(..., terminator: "")` on the second line and notice the output runs together on one line — the `terminator: ""` argument tells `print` not to add a newline. Lesson 02 covers `terminator` and `dump` in detail.

---

## Common Mistakes

### Mistake 1: Using `println` (which does not exist in modern Swift)

```swift
// WRONG — println was removed in Swift 2.0 (2015)
println("Hello, World!")
```

```swift
// RIGHT — Swift uses print, which adds a newline by default
print("Hello, World!")
```

If you are coming from Java (`System.out.println`) or Kotlin (`println`), your fingers may type `println` from muscle memory. In Swift 1.0 (2014) both `print` (no newline) and `println` (with newline) existed, but in Swift 2.0 (2015) `println` was removed and `print` was changed to add a newline by default. Modern Swift has only `print`. If you write `println`, the compiler stops with `cannot find 'println' in scope`. Use `print` and pass `terminator: ""` if you ever need to suppress the newline.

### Mistake 2: Using `NSLog` from Objective-C habits

```swift
// WORKS — but is the wrong tool for simple output
import Foundation
NSLog("Hello, World!")
```

```swift
// RIGHT — print is what you want for ordinary terminal output
print("Hello, World!")
```

If you read old Stack Overflow answers or iOS tutorials written before 2015, you will see `NSLog` everywhere. `NSLog` comes from Apple's Foundation framework — it dates back to NeXTSTEP in the early 1990s and prints with a timestamp, process name, and OS log level prefix. `NSLog` still compiles in modern Swift (via `import Foundation`) and is appropriate when you are writing logs that go to Apple's unified logging system (Console.app). But for ordinary "print this to the terminal" output, use `print`. `NSLog` is roughly 10× slower than `print` because it synchronizes with the OS log daemon.

### Mistake 3: Adding semicolons to every line (Java/C habit)

```swift
// WORKS — but is not idiomatic Swift
let name = "Hieu";
let age = 25;
print(name);
```

```swift
// RIGHT — semicolons are optional and almost always omitted
let name = "Hieu"
let age = 25
print(name)
```

Swift inherited C's rule that statements *may* end with a semicolon, but Swift's style guide and every popular Swift project omit them. The compiler accepts both styles, but adding semicolons everywhere makes your code look like Java or C++ instead of Swift. The only time you need a semicolon is when you put two statements on the same physical line: `let x = 5; let y = 10`. Even then, prefer splitting the statements onto two lines. Your code will look cleaner and match Swift's idiomatic style.

### Mistake 4: Expecting Swift to be like Java or C# (different defaults)

```swift
// WRONG — assuming Swift has exceptions for control flow and class-by-default
try {
    let n = Int("abc")
    print(n)
} catch {
    print("not a number")
}
```

```swift
// RIGHT — Swift uses optionals for "may fail" and throws only for real errors
if let n = Int("abc") {
    print(n)
} else {
    print("not a number")
}
```

Swift's defaults differ from Java and C# in several ways. First, **Swift has no checked exceptions** — you cannot throw an `IOException` from a `try` block the way Java does. Swift's `throws` is reserved for genuine errors (file not found, network failure, parse error). For "this might fail and return nothing," Swift uses **optionals** — `Int("abc")` returns `Int?`, not a thrown exception. Second, **Swift defaults to value types** — when you write `struct`, your type is copied on assignment; you do not get the implicit reference semantics of Java objects. Third, **Swift has no `void` return type for "returns nothing"** — it uses `Void` (which is a typealias for the empty tuple `()`) and you almost never write it explicitly. These differences feel strange at first but they are the source of Swift's safety and predictability.

---

## Summary

- Swift is a statically-typed, compiled, general-purpose language created by **Chris Lattner** at **Apple** starting in **2010**.
- It was announced at **WWDC on June 2, 2014**, shipped **1.0 on September 9, 2014**, and was **open-sourced December 3, 2015** under the Apache 2.0 license — launching swift.org.
- **Swift 5.0 (March 2019) brought ABI stability** — apps no longer bundle the Swift runtime; the operating system ships it.
- Swift **replaces Objective-C** but can interoperate with it: they coexist in the same Xcode project via bridging headers.
- Install on Mac via **Xcode** (full IDE) or **`xcode-select --install`** (command-line tools only). On Linux and Windows, download the official toolchain from **swift.org/install**. Online: **swiftfiddle.com**. Verify with `swift --version` and `swiftc --version`.
- Your first program is `print("Hello, World!")` saved as `hello.swift`. Run as a script with `swift hello.swift`, or compile with `swiftc hello.swift -o hello` and run with `./hello`. No `import` needed for `print`; no `main()` wrapper needed for scripts.
- Swift's big ideas: **type safety**, **optionals** (compiler-enforced null safety), **value types by default** (structs are copied, not shared), **protocol-oriented programming** (small composable protocols with default implementations), and **ARC memory management** (Automatic Reference Counting — not garbage collection).
- In modern Swift use `print` (not `println`, which was removed in 2015, and not `NSLog`, which is for OS-level logging).

You wrote, ran, and compiled your first Swift program. The hardest step — getting the toolchain working — is done. In Lesson 02 we go deeper into the anatomy of a Swift program: script mode versus compiled mode, the `@main` attribute, comments (which can nest in Swift!), the difference between `print`, `terminator:`, and `dump`, and string interpolation with `\()`.

---

**Next:** [Lesson 02: Hello, World! →](./02-hello-world.md)
