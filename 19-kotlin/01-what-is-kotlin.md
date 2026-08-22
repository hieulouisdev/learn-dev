# Lesson 01: What is Kotlin?

> Module: Kotlin · Lesson 1 of 7
> Estimated time: 30–45 minutes

Welcome to your first Kotlin lesson. In the next 30 minutes you will learn what Kotlin is, where it came from, why JetBrains built it, how it relates to Java (and whether you need to know Java first), how to install it, and how to write and run your first program. By the end you will have typed `println("Hello, World!")` into a real Kotlin file and watched the compiler turn it into JVM bytecode.

You do not need any prior Java experience for this lesson. If you completed any of the Python, Rust, Go, TypeScript, Java, or C++ modules, you already understand variables, functions, and control flow — and that is more than enough. Kotlin uses C-style syntax (curly braces, `if`/`for`/`while` keywords) but it is far less verbose than Java. Take a deep breath, open a terminal, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what Kotlin is, where it came from, and why JetBrains created it.
2. Compare Kotlin and Java side-by-side and explain what Kotlin fixes (verbosity, null safety, boilerplate).
3. Install Kotlin (via IntelliJ IDEA, SDKMAN!, Homebrew, or the online playground) and verify with `kotlin -version`.
4. Write your first Kotlin program (`hello.kt`), compile it with `kotlinc`, and run it with `java -jar`.

---

## 1. What is Kotlin, Really?

**Kotlin** is a **statically-typed, general-purpose, object-oriented programming language** that runs on the **Java Virtual Machine (JVM)** and is **100% interoperable with Java**. "Statically-typed" means every variable has a type known to the compiler at compile time, like Rust, Go, and TypeScript — type errors are caught before your program runs. "General-purpose" means Kotlin is not tied to one domain — it powers Android apps, server-side web apps, desktop applications, and increasingly iOS via Kotlin Multiplatform. "Object-oriented" means Kotlin has classes and inheritance, but unlike Java it does not force you to wrap everything in a class — Kotlin supports top-level functions and top-level properties, so a "Hello, World" can fit on a single line. "Runs on the JVM" means Kotlin compiles to JVM bytecode, the same target Java compiles to, which gives Kotlin the entire Java ecosystem of libraries, frameworks, and tooling for free.

The language was started in **2010** inside **JetBrains** — the Czech software company behind IntelliJ IDEA, PyCharm, WebStorm, Rider, and many other popular IDEs. JetBrains writes enormous amounts of Java code every day building their IDEs, and by 2010 the team was frustrated by Java's verbosity: a simple data class with two fields needed 30+ lines of boilerplate (fields, getters, setters, `equals`, `hashCode`, `toString`), null-pointer exceptions crashed their software regularly, and modern features like lambdas, type inference, and pattern matching were nowhere on Java's roadmap. (Java 8's lambdas shipped in 2014 — four years after JetBrains started Kotlin.) Dmitry Jemerov, the project's initial lead, wrote in 2011 that JetBrains had evaluated Scala as a replacement but found it too slow to compile and too complex for everyday use — they wanted something simpler, with first-class IDE support from day one.

The first **public preview** of Kotlin shipped in **July 2011**. JetBrains committed to a long, careful stabilization period: they used Kotlin internally for years, refined it with real-world feedback, and only released **Kotlin 1.0** in **February 2016** — five and a half years after the project started. The name "Kotlin" comes from **Kotlin Island** (Котлин in Russian), a small island in the Gulf of Finland about 30 kilometers west of St. Petersburg. Much of JetBrains' original Kotlin team was based in St. Petersburg, and the island faces the city across the bay. The naming pattern deliberately echoes Java, which was named after coffee — both are short, easy to pronounce, and avoid trademark conflicts. (Java's mascot is a coffee cup; Kotlin's mascot is a cheerful teal-and-orange kangaroo-like creature.)

Kotlin's commercial breakthrough came in **May 2017**, when **Google announced Kotlin as a first-class language for Android development** at Google I/O. Two years later, in **May 2019**, Google went further and declared Kotlin the **preferred language for Android** — all new Android documentation, samples, and APIs would be Kotlin-first. This single decision made Kotlin one of the most strategically important languages to learn in the 2020s. Beyond Android, Kotlin has steadily displaced Java in server-side JVM work because it is 30–50% more concise while running on the exact same JVM and reusing every Java library ever written. Today Kotlin is used by **Google, JetBrains, Atlassian, Netflix, Slack, Trello, Square, Zomato**, and most major Android apps you use every day.

---

## 2. Kotlin vs Java — Same Program, Different Verbosity

The easiest way to understand why Kotlin exists is to see the same program written in both languages. Here is a Java POJO — a plain old Java object holding a name and an age — that prints its own string representation (shown in plain text so the comparison stays focused on the structure):

```text
// Java POJO — roughly 25 lines for two fields
public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public int getAge() { return age; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof User)) return false;
        User u = (User) o;
        return age == u.age && name.equals(u.name);
    }

    @Override
    public int hashCode() { return 31 * name.hashCode() + age; }

    @Override
    public String toString() { return "User(name=" + name + ", age=" + age + ")"; }
}
```

That is roughly 25 lines for a class that holds two fields. In Java you also have to write the constructor by hand, write the getters, override `equals`/`hashCode`/`toString` (or use Lombok to generate them), and you get no help from the compiler for null safety — `new User(null, -5)` compiles fine and crashes much later. Here is the same class in Kotlin:

```kotlin
data class User(val name: String, val age: Int)
```

That single line does everything the Java version did, plus generates a `copy()` method and component destructuring (`val (n, a) = user`). The `data` keyword tells the Kotlin compiler to auto-generate `equals`, `hashCode`, `toString`, `copy`, and `componentN()` based on the properties declared in the primary constructor. Furthermore, `name` is non-nullable by default — `User(null, 5)` is a compile error, not a runtime crash. This single comparison explains why Kotlin developers often say they wrote the same program in half the lines.

---

## 3. Where Kotlin Runs

Kotlin is not limited to one platform. The Kotlin compiler can target **three different backends** from the same source code:

```text
                       ┌─────────────────────────────┐
                       │        Kotlin source        │
                       │         (.kt files)          │
                       └──────────────┬───────────────┘
                                      │
        ┌─────────────────────────────┼─────────────────────────────┐
        ▼                             ▼                             ▼
   Kotlin/JVM                    Kotlin/JS                    Kotlin/Native
   Compiles to                   Compiles to                  Compiles to
   JVM bytecode                  JavaScript                  native binaries
   (.class / .jar)               (.js)                       (.exe / .klib)
        │                             │                             │
        ▼                             ▼                             ▼
   Java libraries,              Node.js, browsers,           iOS, embedded,
   Android, servers             React, Vue                   macOS, Linux
```

- **Kotlin/JVM** is the most common target. Kotlin compiles to JVM bytecode, runs on any machine with a JVM, and can use every Java library ever written. This is what powers Android apps and most server-side Kotlin.
- **Kotlin/JS** compiles Kotlin to JavaScript. You get Kotlin's type safety and tooling, but the output runs in the browser or in Node.js — useful when you want to share code between a Kotlin backend and a JS frontend.
- **Kotlin/Native** compiles Kotlin to native machine code (a `.exe`, `.klib`, or platform binary). It targets platforms without a JVM — iOS, embedded systems, microcontrollers, macOS native apps. Kotlin/Native is the foundation that lets Kotlin run on iOS.

**Kotlin Multiplatform (KMP)** is the unifying story: write your business logic once in Kotlin, then compile it to JVM, JS, and Native from the same source. The platform-specific bits (UI, networking, database) live in `expect`/`actual` declarations, but the bulk of the code — view models, validation, business rules — is shared across Android, iOS, web, and desktop. KMP is one of the most exciting things happening in cross-platform development today.

---

## 4. Installing Kotlin

There are three main paths to install Kotlin. Pick whichever fits your workflow.

### Path A: IntelliJ IDEA Community Edition (recommended for beginners)

This is the easiest path and gives you the best Kotlin experience on Earth — JetBrains literally built Kotlin inside IntelliJ. Download **IntelliJ IDEA Community Edition** (free) from [jetbrains.com/idea/](https://www.jetbrains.com/idea/). Install it, then create a new project: **File → New → Project**, choose **Kotlin** → **JVM | IDEA**, name it `kotlin-playground`, and click Create. IntelliJ ships with a Kotlin plugin bundled and the Kotlin compiler included — you do not need to install anything else. Click the green play button next to `fun main()` to run your code.

### Path B: Command-line compiler

If you prefer terminals over IDEs, install the Kotlin compiler (`kotlinc`). On Mac and Linux the easiest path is **SDKMAN!**:

```bash
curl -s https://get.sdkman.io | bash
source ~/.sdkman/bin/sdkman-init.sh
sdk install kotlin
```

On Mac with Homebrew, a one-liner works:

```bash
brew install kotlin
```

On Windows, download the GitHub release zip from `github.com/JetBrains/kotlin/releases`, extract it, and add the `kotlinc\bin` folder to your PATH environment variable. After installing, verify with `kotlin -version`:

```bash
kotlin -version
```

You should see something like `Kotlin version 1.9.22 (JRE 21+9-LTS)`. If you see `command not found`, close every terminal and open a fresh one so the PATH update takes effect. The Kotlin compiler depends on a working Java installation — if `java -version` works, `kotlin -version` will work too.

### Path C: Online Playground

If you do not want to install anything yet, head to [play.kotlinlang.org](https://play.kotlinlang.org). The Playground runs Kotlin entirely in your browser — you can edit, compile, and run Kotlin code with zero setup, share snippets by URL, and switch between JVM and JS compilation targets. It is the fastest way to try a code sample from a lesson.

---

## 5. Your First Program

It is finally time to write Kotlin. Create a folder for your Kotlin experiments, and inside it create a file called `hello.kt` (the `.kt` extension marks a Kotlin source file). Type this exact code:

```kotlin
fun main() {
    println("Hello, World!")
}
```

Save the file. Then compile it with the Kotlin compiler. The `-include-runtime` flag bundles the Kotlin standard library into the resulting `.jar` so it can run anywhere a JVM is installed:

```bash
kotlinc hello.kt -include-runtime -d hello.jar
```

The `-d hello.jar` flag tells the compiler where to put the output. After compilation finishes (a few seconds), you should see a new file called `hello.jar` in your folder. Run it with the standard `java` command:

```bash
java -jar hello.jar
```

You should see this printed in your terminal:

```text
Hello, World!
```

**Congratulations. You just wrote, compiled, and ran your first Kotlin program.** If you are using IntelliJ IDEA, you skip the command-line steps — just click the green play button next to `fun main()` and IntelliJ compiles and runs in one step.

Let's break the program into its parts:

```kotlin
fun main() {
```

- `fun` — the keyword that declares a function. Kotlin uses `fun` (the same way Go uses `func`, Rust uses `fn`, Python uses `def`). Every function in Kotlin starts with `fun`.
- `main` — the function name. The JVM looks for a function called `main` when the program starts. In modern Kotlin (1.3 and later), `main` can take no arguments — the older `fun main(args: Array<String>)` form still works but is no longer required.
- `()` — empty parentheses mean this function takes no parameters.
- `{` — the opening brace that begins the function body. Like Java and C++, Kotlin uses braces, not Python-style indentation, to delimit blocks.

```kotlin
    println("Hello, World!")
```

- `println` — a top-level function in the Kotlin standard library that prints its argument followed by a newline. Notice there is no `System.out.` prefix — Kotlin promotes `println` and `print` to top-level functions so you never write `System.out.println` the way you do in Java.
- `"Hello, World!"` — a string literal. Kotlin strings use double quotes, like Java and C++.
- `;` is OPTIONAL in Kotlin — you can put a semicolon at the end of the line if you want, but most Kotlin code does not use them. Use newlines to separate statements.

```kotlin
}
```

- `}` — closing brace of the `main` function.

That is the whole program. Notice what is missing compared to Java: no `public class HelloWorld { ... }` wrapper around `main`. Kotlin allows top-level functions — you can write `fun main()` directly in a file, no class needed. This is one of the simplest "Hello, World" programs in any mainstream language.

---

## 6. Kotlin's Big Ideas

Kotlin is built around four big ideas that distinguish it from Java and most other JVM languages:

**Null safety.** In Kotlin, types are non-nullable by default. `var name: String = "Hieu"` means `name` can never be `null` — the compiler refuses to compile `name = null`. To allow null, you must explicitly opt in with `?`: `var name: String? = null`. The compiler then forces you to handle the null case before you can use `name`. This single feature eliminates the entire category of `NullPointerException` crashes that plague Java codebases — Lesson 03 covers it in depth.

**Conciseness.** Kotlin's design principle is "the same code should be shorter, but not cryptic." A `data class` declaration in one line replaces 30 lines of Java boilerplate. Type inference (`val x = 5`) removes redundant type annotations. Single-expression functions (`fun double(x: Int) = x * 2`) collapse trivial one-liners. Extension functions let you add methods to existing classes without writing wrapper utility classes. The result: most Kotlin programs are 30–50% shorter than the equivalent Java, while remaining more readable.

**Interoperability with Java.** Kotlin and Java can coexist in the same project. You can call Java from Kotlin and Kotlin from Java seamlessly. You can use any Java library — Spring, Hibernate, Guava, Apache Commons — from Kotlin with no glue code. You can migrate an existing Java codebase to Kotlin one file at a time, file by file, class by class. This is why Kotlin's adoption has been so rapid in industry: it does not require a "rewrite everything" decision.

**Coroutines for async.** Asynchronous programming in Java has historically been painful — callback hell, then `CompletableFuture` chains, then reactive streams (RxJava, Project Reactor) with steep learning curves. Kotlin's **coroutines** let you write async code that looks synchronous and sequential. A `suspend fun fetchUser(): User` can pause and resume without blocking a thread, and the call site reads like normal sequential code. We will get a brief preview of coroutines in the cheatsheet (Lesson 07); a deep dive is beyond this 7-lesson module but is the natural next step after this curriculum.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Verify Your Installation

Open a terminal and run `kotlin -version`. Write down the version number you see. If it is anything below 1.9, follow the install instructions in Section 4 to upgrade. Then run `java -version` to confirm a JVM is installed (Kotlin needs Java). If both commands work and `kotlin -version` reports 1.9 or newer, your Kotlin installation is complete and ready for the rest of the module. If you are using IntelliJ IDEA, you can skip this step — the IDE bundles Kotlin.

### Exercise 2: Print Something Different

Open your `hello.kt` file and change the message from `"Hello, World!"` to `"My name is [Your Name] and I am learning Kotlin."`. Save the file, recompile with `kotlinc hello.kt -include-runtime -d hello.jar`, and run with `java -jar hello.jar`. Confirm your custom message appears in the terminal. Notice that you must recompile after every change — Kotlin runs compiled bytecode, not source files. If you forget to recompile, you will see the old output.

### Exercise 3: Two `println` Lines

Add a second `println` line below the first. Your program should look like this:

```kotlin
fun main() {
    println("Hello, World!")
    println("I am learning Kotlin.")
}
```

Recompile and run. Confirm both lines are printed, each on its own line. Then experiment: replace `println` with `print` on the second line and notice the output runs together on one line — `print` does not add a newline. Lesson 02 covers `println` versus `print` in detail.

---

## Common Mistakes

### Mistake 1: Using `System.out.println` from Java habits

```kotlin
// WRONG — Java habit, compiles but is not idiomatic Kotlin
fun main() {
    System.out.println("Hello, World!")
}
```

```kotlin
// RIGHT — Kotlin promotes println to a top-level function
fun main() {
    println("Hello, World!")
}
```

If you are coming from Java (Module 17), your fingers will type `System.out.println` from muscle memory. Kotlin actually lets this compile — it can call Java's `System.out` because of JVM interoperability — but it is not idiomatic Kotlin. Kotlin promotes `println` and `print` to top-level functions in its standard library. Use `println(...)` directly. Your code will be shorter, easier to read, and aligned with how every Kotlin developer on Earth writes code.

### Mistake 2: Forgetting the `fun` keyword

```kotlin
// WRONG — no fun keyword, looks like a variable declaration
main() {
    println("Hello, World!")
}
```

```kotlin
// RIGHT — every function in Kotlin starts with fun
fun main() {
    println("Hello, World!")
}
```

Unlike C, C++, or Java (where `main` is declared with a return type like `int` or `void`), Kotlin requires the `fun` keyword before every function. If you forget it, the compiler stops with `expecting member declaration` or `expecting a top-level declaration`. The fix is always the same: prefix your function with `fun`. This applies to every function — not just `main`.

### Mistake 3: Expecting `==` to compare references (it calls `.equals()` in Kotlin)

```kotlin
// In Java: == compares references (identity), .equals() compares values
// In Kotlin: == calls .equals() (structural equality), === compares references
data class User(val name: String, val age: Int)

fun main() {
    val u1 = User("Alice", 30)
    val u2 = User("Alice", 30)
    println(u1 == u2)  // true — Kotlin == is structural equality
    println(u1 === u2) // false — === is reference identity (like Java's ==)
}
```

This is the opposite of Java and trips up many Java developers switching to Kotlin. In Java, `==` compares references (identity) and `.equals()` compares values. In Kotlin, `==` calls `.equals()` under the hood (so it is structural equality), and `===` is the reference-identity operator (Java's `==`). The practical rule: in Kotlin, use `==` to compare values almost always, and reach for `===` only when you genuinely need to know whether two variables point at the same object in memory. Lesson 03 covers this in more depth.

### Mistake 4: Mixing Java types and Kotlin types carelessly

```kotlin
import java.util.Optional

// WRONG — using Java's Optional when Kotlin has nullable types built in
fun findUser(id: Int): Optional<String> {
    return Optional.of("Alice")
}

fun main() {
    val name = findUser(1)
    println(name.get()) // Verbose, not idiomatic Kotlin
}
```

```kotlin
// RIGHT — use Kotlin's built-in nullable types
fun findUser(id: Int): String? {
    return "Alice"
}

fun main() {
    val name = findUser(1)
    println(name ?: "Guest") // Concise, idiomatic
}
```

Kotlin's nullable types (`String?`) replace Java's `Optional<String>` — they are built into the type system, not a wrapper class, so they have zero runtime overhead. When you write Kotlin, prefer Kotlin's nullable types, collection types (`List`, `Set`, `Map`), and standard-library functions over their Java equivalents. You can still call Java code that returns `Optional` or `java.util.ArrayList`, but wrapping every value in `Optional` in idiomatic Kotlin code is a red flag. Lesson 03 dives deeper into null safety.

---

## Summary

- Kotlin is a statically-typed, general-purpose, object-oriented language that runs on the JVM and is 100% interoperable with Java.
- It was created by **JetBrains** starting in **2010**, publicly previewed in **July 2011**, and reached **1.0** in **February 2016**. Named after **Kotlin Island** near St. Petersburg.
- **Google made Kotlin the preferred Android language in May 2019** — every Android developer today is expected to know Kotlin.
- Kotlin compiles to three targets: **JVM bytecode** (most common), **JavaScript** (Kotlin/JS), and **native binaries** (Kotlin/Native). **Kotlin Multiplatform** shares code across all three.
- Install via **IntelliJ IDEA Community Edition** (easiest, recommended), **SDKMAN!** (`sdk install kotlin`), **Homebrew** (`brew install kotlin`), or try online at **play.kotlinlang.org**. Verify with `kotlin -version`.
- Your first program is `fun main() { println("Hello, World!") }`, saved as `hello.kt`. Compile with `kotlinc hello.kt -include-runtime -d hello.jar`, run with `java -jar hello.jar`.
- Kotlin's big ideas: **null safety** (no `NullPointerException`), **conciseness** (30–50% less code than Java), **Java interoperability** (use any Java library, migrate file by file), and **coroutines** (sequential-looking async code).
- In Kotlin, `==` calls `.equals()` (structural equality) and `===` compares references — the opposite of Java's `==`.

You wrote, compiled, and ran your first Kotlin program. The hardest step — getting the toolchain working — is done. In Lesson 02 we go deeper into the anatomy of a Kotlin program: top-level functions, packages and imports, comments, string templates, and the difference between `print` and `println`.

---

**Next:** [Lesson 02: Hello, World! →](./02-hello-world.md)
