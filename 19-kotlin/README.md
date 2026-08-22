# Module 19: Kotlin — The Modern JVM Language

<p align="center"><img src="../logorepo.png" alt="Learn Dev Logo" width="120" height="120" /></p>

Welcome to Module 19: Kotlin. In the next 7 lessons you will learn a language that takes everything verbose and painful about Java — the boilerplate getters and setters, the NullPointerException crashes, the 15-line class declarations for what should be a 1-line data holder — and replaces it with concise, null-safe, expressive syntax that feels almost like a high-level scripting language while still running on the rock-solid Java Virtual Machine. If you completed Module 17 (Java), much of this module will feel like a breath of fresh air. If you skipped Java, that is fine — Kotlin is approachable on its own, and we will cover what you need.

Kotlin is the language Google officially recommends for Android development. It is also the language of choice for modern server-side JVM work at companies like JetBrains, Atlassian, Netflix, and Slack. By the end of this module you will understand why a `data class User(val name: String, val age: Int)` does the work of a 30-line Java POJO, how Kotlin's type system prevents null-pointer crashes at compile time, and how `when`, sealed classes, and extension functions make code that reads almost like prose.

## What is Kotlin?

**Kotlin** is a **statically-typed, general-purpose, object-oriented programming language** that runs on the **Java Virtual Machine (JVM)** and is **100% interoperable with Java**. It was created by **JetBrains** — the company behind IntelliJ IDEA, PyCharm, WebStorm, and most of the popular IDEs developers use every day. JetBrains started the project in **2010** as an internal effort to fix the verbosity and design problems they ran into writing Java all day long inside their own IDEs. The first public preview shipped in **July 2011**, and Kotlin **1.0** — the first stable release — shipped in **February 2016**. The name comes from **Kotlin Island**, a small island in the Gulf of Finland near St. Petersburg, where much of JetBrains' original Kotlin team was based. This naming echoes Java, which was named after coffee, and follows JetBrains' tradition of naming things after islands near St. Petersburg (their Kotlin office was on the mainland facing Kotlin Island).

Kotlin's defining moment came in **May 2017**, when **Google officially announced Kotlin as a first-class language for Android development** at Google I/O. Two years later, in **May 2019**, Google went further and declared Kotlin the **preferred language for Android** — meaning all new Android documentation, samples, and APIs would be written in Kotlin first, with Java treated as a legacy option. This single decision made Kotlin one of the most strategically important languages to learn today: every Android job posting now lists Kotlin, and most existing Java Android codebases are actively being migrated. Beyond Android, Kotlin has steadily replaced Java in server-side JVM work because it is more concise (often 30–50% less code than equivalent Java) while running on the exact same battle-tested JVM and reusing the entire Java ecosystem of libraries.

Kotlin's modern feature set reads like a wish-list from someone who had used Java for years and knew exactly what to fix. **Null safety** is built into the type system: a `String` cannot be `null`, and a `String?` can — the compiler refuses to compile code that might dereference a null. **Data classes** auto-generate `equals`, `hashCode`, `toString`, `copy`, and destructuring in a single line. **Sealed classes** model restricted hierarchies (like Rust enums) and work beautifully with the `when` expression for exhaustive pattern matching. **Coroutines** make asynchronous code look sequential — no callback hell, no `CompletableFuture.thenCompose` chains. **Extension functions** let you add methods to existing classes without modifying them, even to Java classes like `String` or third-party library types. **Scope functions** (`let`, `run`, `with`, `apply`, `also`) give you concise ways to initialize and transform objects. Kotlin is used by **Google, JetBrains, Atlassian, Netflix, Slack, Trello, Square, Zomato, and most modern Android apps** — Instagram, Twitter, Reddit, Duolingo, and Tinder all ship Kotlin code to millions of users daily.

## Why Learn Kotlin?

- **Future-proof for Android.** Google's preferred Android language since 2019 — every new Android job, sample, and library is Kotlin-first.
- **Modern syntax, far less boilerplate.** A `data class User(val name: String, val age: Int)` does what a 30-line Java POJO does. Most Kotlin code is 30–50% shorter than the equivalent Java.
- **Null safety built in.** The type system distinguishes nullable (`String?`) from non-nullable (`String`) types. The compiler catches null-pointer bugs at compile time — no more `NullPointerException` surprises in production.
- **Fully interoperable with existing Java code.** You can call Java from Kotlin and Kotlin from Java seamlessly. You can use every Java library ever written. Migrating an existing Java codebase to Kotlin can be done one file at a time.
- **Runs everywhere Java runs, plus more.** JVM (most common), Kotlin/JS (compiles to JavaScript for the browser), Kotlin/Native (compiles to native binaries for iOS and embedded), and Kotlin Multiplatform (KMP) — share business logic across Android, iOS, web, and desktop from one codebase.

## Lesson Index

| # | Lesson | Focus |
|---|--------|-------|
| 01 | [What is Kotlin?](./01-what-is-kotlin.md) | Origin, JetBrains, JVM, installing, your first program |
| 02 | [Hello, World!](./02-hello-world.md) | Program structure, compiling, `println`, comments, string templates |
| 03 | [Variables and Types](./03-variables-and-types.md) | `val` vs `var`, type inference, basic types, null safety, safe calls, Elvis |
| 04 | [Control Flow](./04-control-flow.md) | `if` as expression, `when`, `for` ranges, `while`, break/continue with labels |
| 05 | [Functions](./05-functions.md) | Defaults, named args, vararg, extension functions, lambdas, higher-order |
| 06 | [Classes and Data Classes](./06-classes-and-data-classes.md) | Properties, constructors, `open`/`override`, interfaces, `data class`, `sealed class`, `object`, `companion object` |
| 07 | [Kotlin Cheatsheet](./07-kotlin-cheatsheet.md) | All-in-one reference: syntax, idioms, scope functions, common pitfalls |

## How to Practice

The easiest way to get a world-class Kotlin experience is to **install IntelliJ IDEA Community Edition** (free) from [jetbrains.com/idea/](https://www.jetbrains.com/idea/). IntelliJ is built by the same company that builds Kotlin, so the Kotlin plugin ships bundled with the IDE — you get syntax highlighting, autocompletion, refactoring, inline compiler errors, and a built-in Kotlin REPL out of the box. Community Edition is free and more than enough for everything in this module. Create a new project, select "Kotlin" → "JVM | IDEA", name it `kotlin-playground`, and click Create. IntelliJ auto-creates a `Main.kt` file you can edit and run with the green play button.

If you do not want to install anything yet, the **online Kotlin Playground** at [play.kotlinlang.org](https://play.kotlinlang.org) lets you write and run Kotlin directly in your browser — no setup, no account required. It supports both JVM and JS compilation targets, and you can share snippets by URL. This is the fastest way to try a code example from a lesson. The **Kotlin Playground** also powers interactive code samples embedded in JetBrains' official documentation.

If you prefer the command line, install the **Kotlin command-line compiler**. On Mac and Linux, the easiest path is **SDKMAN!** — run `curl -s https://get.sdkman.io | bash`, restart your shell, then `sdk install kotlin`. On Mac with Homebrew, run `brew install kotlin`. On Windows, download the GitHub release zip from `github.com/JetBrains/kotlin/releases`, extract it, and add the `kotlinc\bin` folder to your PATH. Verify with:

```bash
kotlin -version
```

You should see something like `Kotlin version 1.9.22 (JRE 21+...)`. If you see `command not found`, your PATH was not updated — close every terminal and open a new one, or add the `kotlinc/bin` folder to PATH manually. You can compile a file with `kotlinc hello.kt -include-runtime -d hello.jar` and run it with `java -jar hello.jar` — Lesson 01 walks through this in detail.

## Estimated Time

- **8–12 hours** if you already know Java (most syntax maps 1:1, so you focus on what is new).
- **15–20 hours** without prior Java experience — the JVM toolchain, packages, and visibility modifiers will all be new.

Each lesson is 30–45 minutes of reading plus 15–30 minutes of exercises. Lesson 06 (Classes and Data Classes) is the longest because it covers the features most Kotlin developers consider the heart of the language — budget extra time for it.

---

Ready? Open [Lesson 01: What is Kotlin? →](./01-what-is-kotlin.md) and let's begin.
