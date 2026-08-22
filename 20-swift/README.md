# Module 20: Swift — Apple's Modern Language

<p align="center"><img src="../logorepo.png" alt="Learn Dev Logo" width="120" height="120" /></p>

Welcome to Module 20: Swift. In the next 7 lessons you will learn a language designed from scratch to replace Objective-C as the way we build apps for iPhone, iPad, Mac, Apple Watch, and Apple TV — and which, since being open-sourced in 2015, has grown into a serious option for server-side and systems programming too. Swift combines the speed of compiled languages like Rust and C++ with the readability of Python and the type safety of Kotlin. If you have completed any of the Python, Rust, Go, TypeScript, Java, C++, or Kotlin modules in this curriculum, you will find Swift's syntax familiar in shape but distinct in its own opinions — value types by default, optionals enforced by the compiler, protocol-oriented programming, and argument labels on every function parameter.

By the end of this module you will understand why a `struct Person { var name: String; var age: Int }` in Swift behaves very differently from a `class` with the same fields (it gets copied on assignment, not shared), why `Int?` and `Int` are different types (and why that prevents entire categories of null crashes), and how Swift's `switch` statement can pattern-match on ranges, tuples, and enums with associated values in a single expressive construct. You will also learn why `if let` and `guard let` show up in almost every Swift file you will ever read.

## What is Swift?

**Swift** is a **statically-typed, compiled, general-purpose programming language** created by **Chris Lattner** at **Apple** starting in **2010**. Chris Lattner is the same engineer who built LLVM (the compiler infrastructure that now powers Rust, Clang, Julia, and many other languages) and who later led teams at Apple, Tesla, Google Brain, and SiFive. He began Swift as a personal research project — sketching ideas in a notebook during evenings and weekends — and pitched it inside Apple in **July 2010**. The project stayed secret for almost four years. Apple announced Swift publicly on **June 2, 2014** at the company's annual **WWDC** (Worldwide Developers Conference) in San Francisco, alongside the iOS 8 SDK. The first stable version, **Swift 1.0**, shipped on **September 9, 2014**, the same day iOS 8 was released.

A pivotal moment came on **December 3, 2015**, when Apple **open-sourced Swift** under the **Apache 2.0 license** and launched the swift.org community site. Before that day Swift was a closed, Apple-only language; after it, anyone could port Swift to Linux, Windows, or any other platform. Today Swift runs on macOS, iOS, iPadOS, watchOS, tvOS, visionOS, Linux (Ubuntu, CentOS, Amazon Linux), and Windows. The next major milestone was **Swift 5.0**, released on **March 25, 2019**, which brought **ABI stability** — meaning apps no longer had to bundle the Swift runtime inside every `.ipa`/`.app` file because the operating system itself shipped a stable Swift runtime. ABI stability was the unlock that made Swift viable as a long-term Apple platform language.

Swift was **designed to replace Objective-C** as Apple's recommended language for app development. Objective-C, dating back to the early 1980s, is a thin layer of Smalltalk-style object-oriented syntax on top of C — powerful but verbose and pointer-heavy. Swift keeps the Objective-C runtime model (so old Objective-C libraries still work) but introduces modern features the Objective-C world never had: **optionals** (the compiler enforces null safety at the type level), **value types by default** (most standard library types like `Array`, `Dictionary`, and `String` are structs, not classes), **protocol-oriented programming** (writing small composable protocols with default implementations rather than deep inheritance hierarchies), **generics** (true parametric polymorphism like Rust and C#), **error handling** (errors as values via `throws`/`try`/`catch`), and **memory safety with ARC** — Automatic Reference Counting, which is *not* garbage collection. ARC inserts `retain` and `release` calls at compile time, so there is no runtime pause for memory management the way there is in Java or Kotlin.

Swift is named after the **swift bird** — a small, fast-flying bird found across most of the world. The name was chosen to convey the language's two goals: speed of execution (Swift benchmarks close to C++ for many tasks) and speed of development (concise syntax that lets you write code quickly). Today Swift is used to build iOS apps, macOS apps (including much of Apple's own software like the Maps app, Notes, and the Calculator), watchOS apps, tvOS apps, visionOS apps, server-side web apps via **Vapor** and **Hummingbird**, and even some embedded systems work. Companies using Swift in production include **Apple, Airbnb, LinkedIn, Lyft, Slack, Uber, and Square**.

## Why Learn Swift?

- **The only official language for native iOS development.** Apple's frameworks — SwiftUI, UIKit, Foundation, Core Data, ARKit — are all Swift-first in 2025. Every new iOS job posting lists Swift.
- **Modern and clean syntax.** Swift's design borrows the best ideas from Rust, Haskell, Python, and C# — type inference, optionals, value types, generics, pattern matching — without the verbose ceremony of Java or the manual memory management of C++.
- **Growing server-side ecosystem.** Frameworks like **Vapor** and **Hummingbird** let you write HTTP backends in Swift that compile to a single fast native binary — no JVM, no Node runtime, no interpreter.
- **Open-source so you can use it on Linux too.** Swift on Linux is fully supported by swift.org. You do not need a Mac to learn Swift anymore — only to ship to the App Store.
- **Performance comparable to C++ for many tasks.** Swift compiles to native machine code via LLVM and uses value types and ARC instead of garbage collection, so predictable low-latency performance is realistic.
- **Strong type safety reduces runtime bugs.** Optionals, exhaustive `switch`, type-safe string interpolation, and `throws`/`try`/`catch` make entire categories of bugs (null dereferences, unmatched cases, malformed format strings, silently swallowed errors) compile errors instead of runtime crashes.

## Lesson Index

| # | Lesson | Focus |
|---|--------|-------|
| 01 | [What is Swift?](./01-what-is-swift.md) | Origin, Chris Lattner, WWDC 2014, open-sourcing, installing, your first program |
| 02 | [Hello, World!](./02-hello-world.md) | Script vs compiled, program anatomy, comments, printing, string interpolation, multi-line strings |
| 03 | [Variables and Types](./03-variables-and-types.md) | `var` vs `let`, type inference, basic types, type safety, strings, tuples, type aliases |
| 04 | [Control Flow](./04-control-flow.md) | `if` as expression, `switch` with ranges and tuples, `for` with ranges, `while`, `repeat-while`, labeled loops |
| 05 | [Functions](./05-functions.md) | Argument labels, defaults, variadic, `inout`, function types, closures, trailing closures, `throws`/`try` |
| 06 | [Structs and Classes](./06-structs-and-classes.md) | Value vs reference types, properties, initializers, inheritance, protocols, POP, optionals deep dive, errors, enums |
| 07 | [Swift Cheatsheet](./07-swift-cheatsheet.md) | All-in-one reference: syntax, idioms, collections, common pitfalls, what's next |

## How to Practice

The easiest way to get a world-class Swift experience is to **install Xcode on a Mac** from the Mac App Store. Xcode is Apple's free IDE and it bundles the Swift compiler (`swiftc`), the Swift Package Manager, the SwiftUI visual editor, the iOS Simulator, and a feature called **Swift Playgrounds** that lets you write Swift interactively. Xcode is multi-gigabyte, so the download takes time — start it, grab a coffee, and let it finish. If you do not need the full IDE, you can install just the **Command Line Tools** by running `xcode-select --install` in Terminal — this gives you `swift` and `swiftc` without the GUI.

If you are on **Linux** (Ubuntu, CentOS, Amazon Linux, Fedora) or **Windows**, head to [swift.org/install](https://www.swift.org/install/) and download the official toolchain for your platform. Extract it, add the `usr/bin` directory to your PATH, and verify with `swift --version`. The Linux toolchain is fully supported by Apple — it is not a port or community project. You can build and run Swift Package Manager projects the same way you would on a Mac.

If you do not want to install anything yet, the **online Swift Playground** at [swiftfiddle.com](https://swiftfiddle.com) lets you write and run Swift directly in your browser — no setup, no account required. This is the fastest way to try a code sample from a lesson. Whichever path you choose, verify your installation by running this command in a terminal:

```bash
swift --version
```

You should see something like `swift-driver version: 1.90 Apple Swift version 5.9.2 (swiftlang-5.9.2.2.108 clang-1500.0.298)`. If you see `command not found`, close every terminal and open a fresh one so the PATH update takes effect. You can also check the underlying compiler with `swiftc --version`.

## Estimated Time

- **10–14 hours** for the full module if you are new to compiled languages — Swift's type system, value semantics, and optionals take time to internalize.
- **6–8 hours** if you have already completed the Rust, Kotlin, or TypeScript modules — optionals, value types, and generics will be familiar concepts, so you can move faster.

Each lesson is 30–45 minutes of reading plus 15–30 minutes of exercises. Lesson 06 (Structs and Classes) is the longest because it covers the features most Swift developers consider the heart of the language — budget extra time for it.

---

Ready? Open [Lesson 01: What is Swift? →](./01-what-is-swift.md) and let's begin.
