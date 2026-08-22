# Lesson 02: Hello, World!

> Module: Kotlin · Lesson 2 of 7
> Estimated time: 30–45 minutes

In Lesson 01 you wrote your first Kotlin program — a single `println("Hello, World!")` inside `fun main()`. You compiled it with `kotlinc`, ran it with `java -jar`, and saw the output. That was the hardest part of learning any new language: getting the toolchain working. Now we slow down and dissect the anatomy of a Kotlin program in detail. You will learn how a `.kt` file is organized (packages, imports, top-level functions), how the `kotlinc` compiler differs from Java's `javac`, how to use `println` and `print` correctly, how to write comments in three different flavors, and how to interpolate variables into strings with Kotlin's string templates.

The good news: most of this lesson feels like a simpler, less verbose version of what you would write in Java. There are no `public class` wrappers required, no `public static void main` boilerplate, and no need to type `System.out.println` every time. Kotlin's standard library promotes common operations (printing, reading input, math) to top-level functions you can call directly. By the end of this lesson you will be able to read a typical Kotlin file and explain what every line does.

---

## Learning Objectives

After this lesson, you will be able to:

1. Describe the structure of a typical Kotlin file: package declaration, imports, top-level functions, and (optionally) classes.
2. Compile and run a Kotlin program in three ways — IntelliJ run button, `kotlinc` + `java -jar`, and the online Playground — and explain the difference between `.kt` and `.kts` files.
3. Use `println` and `print` correctly, and interpolate variables into strings with `$variable` and `${expression}` string templates.
4. Write line comments, block comments, and KDoc comments, and explain what each is used for.

---

## 1. Program Structure

A Kotlin source file has a flexible, top-down structure. Unlike Java, where everything must live inside a class, Kotlin lets you declare functions, properties, and classes at the top level of a file. Here is a representative example showing all the major pieces:

```kotlin
package com.example.hello

import kotlin.math.PI
import kotlin.system.measureTimeMillis

const val APP_NAME = "Greeter"

fun greet(name: String): String {
    return "Hello, $name!"
}

class Greeter {
    fun sayHello(name: String) = println(greet(name))
}

fun main() {
    println("App: $APP_NAME")
    println(greet("Hieu"))
    Greeter().sayHello("World")
}
```

Let's go through each piece:

- `package com.example.hello` — the **package declaration**. This line tells the compiler that everything in this file belongs to the `com.example.hello` package. Packages organize code into namespaces and prevent name collisions. They map directly to Java packages — `com.example.hello.Greeter` in Kotlin is `com.example.hello.Greeter` in Java. If you omit the package declaration, the file's contents go into the "default package", which is generally discouraged.
- `import kotlin.math.PI` and `import kotlin.system.measureTimeMillis` — **import statements**. They bring in specific names from other packages so you can use them unqualified (`PI` instead of `kotlin.math.PI`). Kotlin's import syntax is identical to Java's. Unlike Go, Kotlin does not have automatic semicolons or special import forms — you list each name on its own line, or use `import com.example.*` to import everything from a package.
- `const val APP_NAME = "Greeter"` — a **top-level property**. `const` means this is a compile-time constant (must be a primitive type or string, evaluated at compile time). `val` means read-only (Lesson 03). Top-level properties are accessible from anywhere in the file and from other files via the package name.
- `fun greet(name: String): String { ... }` — a **top-level function**. Notice it lives outside any class — Kotlin allows this. The parameter type comes after the name (`name: String`, not `String name` like Java). The return type comes after the colon (`: String`). This is "Pascal-style" type syntax, also used by Swift and Rust.
- `class Greeter { ... }` — a class declaration, identical in form to Java's. Classes are optional in Kotlin; you can write an entire program with just top-level functions if you want.
- `fun main() { ... }` — the entry point. The JVM looks for `main` (with no arguments in modern Kotlin) and runs it first. This `main` lives at the top level — no class wrapper needed, unlike Java's `public static void main(String[] args)`.

For older codebases, you may still see `fun main(args: Array<String>)` — the older form that accepts command-line arguments. Both forms work in Kotlin 1.9+; the no-argument version is preferred when you do not need command-line arguments. If you do need them, use the `args` form.

---

## 2. Compiling and Running

There are three primary ways to compile and run Kotlin code. Each has a slightly different workflow.

### Way A: IntelliJ IDEA Run Button

In IntelliJ, open a `.kt` file that contains a `fun main()`. A green play button appears in the gutter next to `fun main()`. Click it (or press `Ctrl+Shift+F10` on Linux/Windows, `Cmd+Shift+R` on Mac). IntelliJ compiles the file, runs it, and shows the output in the "Run" tool window at the bottom of the screen. This is the simplest workflow and the one most Kotlin developers use day to day.

### Way B: Command-Line Compiler

From a terminal, use `kotlinc` to compile a `.kt` file:

```bash
kotlinc hello.kt -include-runtime -d hello.jar
java -jar hello.jar
```

- `kotlinc` — the Kotlin compiler (analogous to Java's `javac`).
- `hello.kt` — the source file to compile.
- `-include-runtime` — bundles the Kotlin standard library into the resulting `.jar` so the program can run anywhere a JVM is installed (no separate Kotlin runtime required).
- `-d hello.jar` — destination of the compiled output. The `-d` flag is short for "destination".
- `java -jar hello.jar` — runs the jar using the standard `java` command from the JDK. Because the Kotlin runtime was bundled, this works on any machine with Java installed, even without Kotlin.

If you only want to compile to a `.class` file (without bundling the runtime), drop the `-include-runtime` flag and change `-d` to a directory:

```bash
kotlinc hello.kt -d out/
```

This produces `out/com/example/hello/HelloKt.class`. You can then run it with `kotlin -cp out com.example.hello.HelloKt` (the `kotlin` runner, not `java`) — this requires the Kotlin runtime to be installed on the machine.

### Way C: Online Playground

Browse to [play.kotlinlang.org](https://play.kotlinlang.org). Paste your Kotlin code into the left panel, click the green Run button, and the output appears on the right. You can switch between JVM and JS targets using the dropdown at the top. This is the fastest way to try a code sample from a lesson — no installation required.

### `.kt` vs `.kts` Files

Kotlin source files come in two flavors:

- `.kt` — regular Kotlin source files, what we have been using.
- `.kts` — **Kotlin script** files. These are run directly by the `kotlinc` script runner without a separate compile step:

```bash
kotlinc -script hello.kts
```

Scripts are useful for build automation, small utilities, and Gradle build files (the `build.gradle.kts` file in Gradle Kotlin DSL projects). You can also start an interactive Kotlin REPL with `kotlinc` (no arguments) and type expressions one at a time:

```bash
kotlinc
>>> println("Hello, REPL!")
Hello, REPL!
>>> 2 + 2
res0: kotlin.Int = 4
>>> :quit
```

The REPL is a great way to experiment with small Kotlin expressions without writing a full program.

---

## 3. The `println` and `print` Functions

Kotlin's standard library provides two top-level printing functions:

```kotlin
fun main() {
    print("Hello, ")
    print("World!")
    println()        // prints just a newline
    println("Second line.")
}
```

- `print(x)` — prints `x` to standard output WITHOUT a trailing newline.
- `println(x)` — prints `x` to standard output AND appends a newline. The name is short for "print line".
- `println()` — with no arguments, prints just a newline.

Running this program prints:

```text
Hello, World!
Second line.
```

Notice that `"Hello, "` and `"World!"` are printed on the same line because `print` does not add a newline. `println()` adds one. Then `println("Second line.")` prints the text plus a newline.

These are top-level functions in `kotlin.io` (the package `kotlin.io` is auto-imported, so you never need to write `import kotlin.io.println`). They are convenience wrappers around `System.out.print` and `System.out.println` — Kotlin's designers decided that exposing `println` directly was cleaner than forcing developers to write `System.out.println` the way Java does.

### Reading Input

To read a line from standard input, use `readln()` (Kotlin 1.6+):

```kotlin
fun main() {
    print("Enter your name: ")
    val name = readln()
    println("Hello, $name!")
}
```

- `readln()` — reads one entire line from standard input (blocks until the user presses Enter), returns it as a `String`. Newer Kotlin code uses `readln()`; older code uses `readLine()!!` which can return null and requires the `!!` operator (covered in Lesson 03).

Run this program in IntelliJ or the playground, type `Hieu`, press Enter, and you should see `Hello, Hieu!`.

---

## 4. Comments

Kotlin has three flavors of comments, all borrowed from Java:

```kotlin
// This is a single-line comment.
// It starts with two slashes and runs to the end of the line.

/*
 * This is a multi-line block comment.
 * It starts with /* and ends with */.
 * It can span multiple lines.
 */

/**
 * This is a KDoc comment.
 * It starts with /** and ends with */.
 * It is used by the Dokka documentation generator
 * to produce API reference docs.
 *
 * @param name The user's display name.
 * @return A friendly greeting string.
 */
fun greet(name: String): String = "Hello, $name!"
```

- `//` — a **line comment**. Everything from `//` to the end of the line is ignored by the compiler.
- `/* ... */` — a **block comment**. Everything between `/*` and `*/` is ignored. Kotlin block comments CAN be nested, unlike Java's — `/* outer /* inner */ still outer */` is valid Kotlin.
- `/** ... */` — a **KDoc comment**. Same syntax as a block comment, but the compiler treats it as documentation. Tools like **Dokka** (Kotlin's equivalent of Java's Javadoc) read these comments and produce HTML API references. Inside KDoc, you can use Markdown-style formatting (bold, italic, code spans) and tag references like `@param`, `@return`, `@see`, `@sample`.

A practical tip: use `//` for short, single-line notes ("TODO: handle null case", "// Workaround for issue #123"). Use `/* ... */` for longer explanations inside functions. Use `/** ... */` for every public function, class, and property you write — your future self and your colleagues will thank you when Dokka generates clean API docs from your code.

---

## 5. String Templates

One of Kotlin's most-loved features is **string templates** — the ability to interpolate variables and expressions directly into string literals. There is no need for `String.format` or string concatenation with `+`.

```kotlin
fun main() {
    val name = "Hieu"
    val age = 25

    // Simple variable interpolation: $variable
    println("Hello, $name!")

    // Expression interpolation: ${expression}
    println("Next year you will be ${age + 1}.")

    // Inside ${} you can write any expression
    println("Name length: ${name.length}")

    // Even call functions inside ${}
    println("Upper: ${name.uppercase()}")
}
```

- `$name` — the simplest form. Inserts the value of the variable `name` into the string. The compiler reads the variable name (an identifier) immediately after `$` and substitutes its `toString()` result.
- `${age + 1}` — when you need to interpolate an expression (not just a single variable), wrap it in `${}`. Anything inside the braces is evaluated as a Kotlin expression, then converted to a string via `toString()`.
- `${name.length}` — you can access properties and call methods inside `${}`.
- `${name.uppercase()}` — function calls work too.

Output:

```text
Hello, Hieu!
Next year you will be 26.
Name length: 4
Upper: HIEU
```

### Multi-line Raw Strings

Kotlin has a second kind of string literal — the **raw string** — written with three double quotes (`"""`). Raw strings can span multiple lines, do not require escaping backslashes, and still support string templates:

```kotlin
fun main() {
    val name = "Hieu"
    val multiline = """
        Hello, $name!
        This string spans multiple lines.
        No need for \n escape sequences.
        Indentation is preserved.
    """.trimIndent()
    println(multiline)
}
```

- `"""..."""` — opens and closes a raw string. Newlines inside are preserved literally.
- `.trimIndent()` — a standard-library extension function that removes common leading whitespace from every line, so your raw string lines up cleanly in the source code but prints without the awkward indentation. You will see this idiom constantly in Kotlin codebases.
- `$name` — string templates work inside raw strings exactly like in regular strings.

Raw strings are perfect for embedding JSON, SQL queries, HTML templates, or multi-line configuration strings directly in your code without escape-character gymnastics.

---

## 6. Type Inference

Kotlin is statically typed — every variable has a type known at compile time — but you usually do not need to write the type. The compiler infers it from the value:

```kotlin
fun main() {
    val x = 5              // inferred as Int
    val pi = 3.14          // inferred as Double
    val name = "Hieu"     // inferred as String
    val flag = true        // inferred as Boolean
    val letter = 'A'       // inferred as Char

    // You can be explicit if you want
    val age: Int = 25
    val score: Double = 95.5
}
```

- `val x = 5` — the compiler sees the integer literal `5`, infers `Int`, and gives `x` the type `Int`. You never need to write `Int` explicitly here.
- `val pi = 3.14` — `3.14` is a floating-point literal, inferred as `Double` (the default floating-point type in Kotlin).
- `val name = "Hieu"` — string literal, inferred as `String`.
- `val letter = 'A'` — single quotes denote a `Char` (a single Unicode character), not a `String`. Double quotes are for `String`, single quotes are for `Char`.
- `val age: Int = 25` — explicit type annotation. Use this form when you want to be specific (e.g., to make the type obvious in a public API) or when the inferred type is not what you want (e.g., `val x: Long = 5` would infer `Long` if you wrote the annotation, but `Long` from `5` alone is not the inferred type — `Int` is).

Lesson 03 covers the full type system in detail. For now, remember: Kotlin is statically typed, the compiler catches type errors at compile time (like Rust and TypeScript), but you rarely have to write the types yourself.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Print a Profile Card

Write a program that prints a small profile card for yourself. Use `val` to define your name (a `String`), your age (an `Int`), and your city (a `String`). Then use string templates to print all three on separate lines, like this:

```text
Name: Hieu
Age: 25
City: Hanoi
```

Recompile and run. Notice how much shorter this is than the equivalent Java program — no `public class`, no `public static void main`, no `System.out.println`. The whole program should fit in 6–7 lines.

### Exercise 2: Use `print` and `println` Together

Modify your program so the output uses `print` for "Name: " and `println` for the value, on the same line. Do the same for Age and City. The output should look identical to Exercise 1, but the code uses `print(...)` followed by `println(...)` for each line. Notice that `print` leaves the cursor on the same line and `println` moves it to the next line.

### Exercise 3: Multi-line Raw String

Write a program that uses a `"""..."""` raw string to print a small ASCII art banner for your name. Use `.trimIndent()` to clean up the indentation. Your output should look something like:

```text
+----------+
|   Hieu   |
+----------+
```

Use the `${name}` template inside the raw string to interpolate your name. Experiment with adding extra lines like `Welcome to Kotlin!` below the banner.

---

## Common Mistakes

### Mistake 1: Forgetting `$` in string templates

```kotlin
// WRONG — prints literally "Hello, name!"
fun main() {
    val name = "Hieu"
    println("Hello, name!")
}
// Output: Hello, name!
```

```kotlin
// RIGHT — $ interpolates the variable's value
fun main() {
    val name = "Hieu"
    println("Hello, $name!")
}
// Output: Hello, Hieu!
```

In Kotlin, `$name` inside a string interpolates the variable; without the `$`, the text `name` is printed literally. This is a frequent beginner mistake that produces confusing output. If your program prints `Hello, name!` instead of `Hello, Hieu!`, check that you used the `$` prefix. Use `${expression}` (with braces) when the interpolation is more than a simple variable name — for example, `${user.name}` for a property access or `${age + 1}` for an arithmetic expression.

### Mistake 2: Using single quotes for strings

```kotlin
// WRONG — single quotes denote a Char, not a String
fun main() {
    val message = 'Hello, World!'  // Compile error
    println(message)
}
```

```kotlin
// RIGHT — double quotes denote a String
fun main() {
    val message = "Hello, World!"
    println(message)
}
```

In Kotlin (as in Java and C++), single quotes denote a single character (`Char`), and double quotes denote a string (`String`). `'A'` is a `Char` — a 16-bit Unicode code unit. `"Hello"` is a `String` — a sequence of `Char`s. If you write `val message = 'Hello'` with single quotes, the compiler stops with `too many characters in character literal ''Hello''`. The fix is always to switch to double quotes for any sequence of more than one character.

### Mistake 3: Confusing `==` (structural) with `===` (reference)

```kotlin
// In Kotlin: == calls .equals() (structural equality), === compares references
// This is the OPPOSITE of Java
fun main() {
    val a = "hello"
    val b = "hello"
    println(a == b)   // true — structural equality
    println(a === b)  // usually true due to string interning, but not guaranteed

    val list1 = listOf(1, 2, 3)
    val list2 = listOf(1, 2, 3)
    println(list1 == list2)   // true — structural equality (elements match)
    println(list1 === list2)  // false — different List objects in memory
}
```

In Java, `==` compares references (identity) and `.equals()` compares values. In Kotlin, `==` calls `.equals()` (so it compares values), and `===` is the operator for reference identity (Java's `==`). The practical rule: use `==` almost always to compare values; reach for `===` only when you genuinely need to know whether two variables point at the same object in memory. This catches many Java developers off guard when they first switch to Kotlin.

### Mistake 4: Using `System.out.println` instead of `println`

```kotlin
// WRONG — Java habit, compiles but is unidiomatic
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

In Java you must write `System.out.println(...)` because `println` is a method on `System.out`, a static field. In Kotlin the standard library promotes `println` and `print` to top-level functions in `kotlin.io`, which is auto-imported. Kotlin's `println` actually delegates to `System.out.println` under the hood, so the behavior is identical — but using the top-level form is shorter, more idiomatic, and matches every other Kotlin developer's code. Whenever you see your fingers typing `System.out.println` from Java muscle memory, delete it and use `println` instead.

---

## Summary

- A Kotlin source file has four optional parts: `package` declaration, `import` statements, top-level functions/properties, and class declarations. Classes are optional — you can write a whole program with just top-level functions.
- Parameter and return types use Pascal-style syntax: `fun greet(name: String): String`. The type comes AFTER the name, not before (unlike Java's `String greet(String name)`).
- Compile a Kotlin file with `kotlinc hello.kt -include-runtime -d hello.jar` and run with `java -jar hello.jar`. Or use the IntelliJ run button, or paste into play.kotlinlang.org.
- `.kt` files are regular Kotlin source. `.kts` files are Kotlin scripts you can run with `kotlinc -script file.kts` (used for Gradle build files and utilities).
- `print(x)` prints without a trailing newline; `println(x)` prints plus a newline; `println()` prints just a newline. `readln()` reads one line from standard input.
- Three comment styles: `//` line comments, `/* */` block comments (which can be nested — unlike Java), and `/** */` KDoc comments processed by Dokka.
- String templates: `$variable` for simple variables, `${expression}` for any Kotlin expression. Works in both `"..."` and `"""..."""` raw strings.
- Type inference means you rarely write the type — `val x = 5` infers `Int`. Single quotes `'A'` denote `Char`; double quotes `"A"` denote `String`.

You now understand the anatomy of a Kotlin file. In Lesson 03 we go deep on variables and types — the critical distinction between `val` and `var`, Kotlin's basic types, and the headline feature that makes Kotlin famous: **null safety**.

---

**Next:** [Lesson 03: Variables and Types →](./03-variables-and-types.md)
