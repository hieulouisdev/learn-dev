# Lesson 01: What is Java?

> Module: Java · Lesson 1 of 7
> Estimated time: 30–45 minutes

Welcome to your first Java lesson. In the next 30 minutes, you will learn what Java is, where it came from, how the JVM works its famous "Write Once, Run Anywhere" magic, how to install Java on your computer, and how to write and run your first Java program. By the end, you will have typed `System.out.println("Hello, World!")` into a real Java source file and watched the compiler turn it into bytecode that the JVM then runs.

You do not need any prior Java experience for this lesson. If you completed the Python, Rust, Go, or TypeScript modules, you already understand variables, functions, and control flow — and that is more than enough. Java feels familiar because it uses C-style syntax (curly braces, semicolons, the same `if`/`for`/`while` keywords you saw in Go and TypeScript), but it is stricter about types and more verbose about object orientation. Take a deep breath, open a terminal, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what Java is, where it came from, and why it was created.
2. Describe how the JVM turns `.java` source files into bytecode and runs them on any platform.
3. Install OpenJDK 17 (or newer) on Windows, Mac, or Linux and verify it from the terminal.
4. Write your first Java program (`HelloWorld.java`) and explain what each word in `public static void main` actually means.

---

## 1. What is Java, Really?

Java is a **general-purpose, statically-typed, object-oriented, garbage-collected programming language** that runs on the **Java Virtual Machine (JVM)**. "General-purpose" means Java is not tied to one domain — it powers web servers, mobile apps, big-data pipelines, desktop tools, and embedded systems. "Statically-typed" means every variable has a type known to the compiler at compile time, like Rust, Go, and TypeScript — type errors are caught before your program runs. "Object-oriented" means almost everything in Java lives inside a class — there are no standalone functions outside a class and no global variables. "Garbage-collected" means Java automatically reclaims memory you no longer use, so you never call `free()` manually the way you do in C. Unlike Rust, which uses compile-time ownership rules, and unlike Go, which uses a runtime garbage collector, Java uses a sophisticated generational garbage collector inside the JVM.

The language was started in **1991** at **Sun Microsystems** by a small team led by **James Gosling**, **Mike Sheridan**, and **Patrick Naughton**. Gosling is the name you will hear most — he is the engineer credited as Java's principal designer, and he later went on to work on the Java language at Sun, then Oracle, then Google, then Amazon. The project was originally called **"Oak"**, named after an oak tree that stood outside Gosling's office at Sun's offices in Menlo Park, California. The goal was ambitious for 1991: build a portable language for the next generation of consumer electronics — set-top boxes, interactive TVs, handheld PDAs — devices with different CPUs and operating systems that all needed to run the same code. The consumer-electronics market was not ready, however, and Oak nearly died before its public release.

The pivot that saved Java was the **World Wide Web**. In 1994–1995, Sun's engineers realized that the newly-popular web browser was the perfect host for a portable language: web pages could embed small Java programs called "applets" that would run inside any browser on any operating system. Sun **renamed the language "Java"** — the legend says it was named after Java coffee, allegedly from the Indonesian island of Java, and that the engineering team drank a lot of coffee from a nearby shop while writing the language. Sun **officially released Java 1.0 in January 1996**. Applets never became the dominant force Sun hoped for (JavaScript won the browser), but the JVM's portability made Java the obvious choice for server-side enterprise development — and that is where Java truly flourished.

**Sun Microsystems was acquired by Oracle in 2010**, and Oracle has owned Java ever since. Java continues to evolve: Java 8 (2014) added lambdas and streams, Java 11 (2018) was the next LTS after Java 8, Java 17 (2021) added sealed classes and pattern matching, Java 21 (2023) added virtual threads. The current LTS releases are **Java 17 and Java 21**, and this module targets Java 17+. **Java is one of the most widely-used programming languages in the world** — consistently in the top three of the TIOBE Index, with an estimated **3 billion+ devices** running Java. It powers most enterprise backends, big-data tools like **Hadoop** and **Apache Spark**, the Android platform (until Kotlin replaced it in 2017), and countless mission-critical systems you depend on every day.

---

## 2. The JVM: Write Once, Run Anywhere

Java's biggest idea is the **Java Virtual Machine**. The JVM is a program (a piece of software) that runs on your computer and executes Java bytecode. The trick is that there is a different JVM for every operating system — a Windows JVM, a Mac JVM, a Linux JVM, even a JVM for IBM mainframes — but they all understand the same bytecode format. So you compile your Java source code once, and the resulting bytecode runs on any of them.

The compilation pipeline looks like this:

```text
              javac                    java
HelloWorld.java  →  HelloWorld.class  →  "Hello, World!" printed
   source file       bytecode file       JVM runs the bytecode
   (human text)      (JVM instructions)  (on Windows, Mac, or Linux)
```

Here is what happens at each step:

- You write a source file: `HelloWorld.java`. This is plain text you can read and edit in any text editor.
- You compile it with the `javac` command: `javac HelloWorld.java`. The `javac` compiler reads your source file, checks the types, and produces a file called `HelloWorld.class`. That `.class` file contains **bytecode** — instructions for the JVM, not for any specific CPU.
- You run the bytecode with the `java` command: `java HelloWorld`. The JVM loads `HelloWorld.class`, interprets the bytecode (and optionally compiles hotspots to native machine code with its Just-In-Time compiler), and your program runs.

Contrast this with **C and C++**, which compile directly to native machine code for one specific platform. A C program compiled for Windows (an `.exe`) will not run on a Mac or Linux without being recompiled for each. The C source is portable, but the compiled binary is not. Java's bytecode is portable — the same `.class` file runs everywhere there is a JVM. That is what **"Write Once, Run Anywhere"** means.

This is also why languages like **Kotlin, Scala, and Clojure** run on the JVM: any language that compiles to JVM bytecode gets the same portability, the same garbage collection, and the same standard library that Java has. Learning Java means you understand the JVM, and the JVM is a whole ecosystem.

---

## 3. Installing Java

Before you can write Java, you must install a **JDK** — a Java Development Kit. The JDK includes the compiler (`javac`), the runtime (`java`), the standard library, and a few other tools (`jar`, `javadoc`, `jshell`). The JRE (Java Runtime Environment) is a smaller package that only runs Java programs but cannot compile them — modern Java bundles the JRE inside the JDK, so you only need to install the JDK.

There are many JDK distributions, but **Eclipse Temurin** (free, open-source, maintained by the Eclipse Foundation) is the most popular for developers. It is essentially OpenJDK — the reference implementation of Java — packaged with a permissive license. Download it from [adoptium.net](https://adoptium.net/). Pick **JDK 17 LTS** for this module; **JDK 21 LTS** also works. Avoid Oracle's JDK unless you understand Oracle's licensing terms — Temurin is the same codebase with a fully free license.

### On Windows

Download the `.msi` installer from adoptium.net (something like `OpenJDK17U-jdk_x64_windows_hotspot_17.0.x.msi`). Double-click it and follow the prompts. The installer **automatically adds Java to your PATH** — you usually do not need to tick any checkbox. When installation finishes, close any open terminal and open a new one so the PATH change takes effect. Verify with `java --version` (see below).

### On Mac

Download the `.pkg` installer from adoptium.net and run it. The installer places Java at `/Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home` and adds it to your PATH automatically. If you use **Homebrew**, you can also run `brew install --cask temurin@17` to install the same JDK from the command line. After installation, open a fresh Terminal window so the new command is visible.

### On Linux

Most distributions package OpenJDK in their repositories. On Debian/Ubuntu, run `sudo apt install openjdk-17-jdk`. On Fedora, run `sudo dnf install java-17-openjdk-devel`. On Arch, run `sudo pacman -S jdk17-openjdk`. The `-devel` or `-jdk` suffix matters — without it you may install just the JRE, which cannot compile Java code. Alternatively, download the `.tar.gz` from adoptium.net, extract it to `/opt/jdk-17`, and add `/opt/jdk-17/bin` to your PATH manually.

### Verifying the Installation

Open a fresh terminal and run both commands. You should see version banners from each.

```bash
java --version
javac --version
```

If you see something like `openjdk version "17.0.10" 2024-01-16` for `java` and `javac 17.0.10` for `javac`, congratulations — Java is installed and ready. If you see `command not found`, your shell did not pick up the PATH change. Close every terminal window and open a new one. If that does not help, the install directory is not on your PATH — add it manually.

A brief note on **IDEs**: you can write Java in any text editor (VS Code, Vim, Notepad, Nano) and compile it with `javac` from the terminal. For a more polished experience, download **IntelliJ IDEA Community Edition** (free) from [jetbrains.com/idea/](https://www.jetbrains.com/idea/). IntelliJ is built by JetBrains — the same company that designed Kotlin — and is widely considered the best Java IDE in the world. It handles project setup, autocompletion, refactoring, and debugging automatically. The Community Edition is free and more than enough for this entire module.

---

## 4. Your First Program

It is finally time to write Java. Open a terminal, create a folder for your Java experiments, and inside it create a file called `HelloWorld.java` (the filename MUST match the class name exactly, including capitalization — we will explain why in a moment). Type this exact code:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

Save the file. Then compile it with `javac`:

```bash
javac HelloWorld.java
```

If the compiler is happy, it produces no output and exits silently — Java's compiler is famously quiet on success. List the files in your folder with `ls` (Mac/Linux) or `dir` (Windows), and you will see a new file called `HelloWorld.class`. That is your bytecode. Run it with `java`:

```bash
java HelloWorld
```

You should see this printed in your terminal:

```text
Hello, World!
```

**Congratulations. You just wrote, compiled, and ran your first Java program.** Now let's break the file into its parts, because every word in `public static void main` matters and Java's verbosity surprises beginners.

Here is the program again with each line explained:

```java
public class HelloWorld {
```

- `public` — an **access modifier**. It means this class is visible to all other classes. The `public` keyword is what allows `java HelloWorld` (the JVM) to find your class from outside.
- `class` — the keyword that declares a class. Every Java program is made of classes; you cannot have code outside a class.
- `HelloWorld` — the **class name**. By convention, class names are PascalCase (each word capitalized, no spaces).
- `{` — the opening brace that begins the class body. Everything inside belongs to the class.

```java
    public static void main(String[] args) {
```

- `public` — the `main` method must be `public` so the JVM can call it from outside.
- `static` — the `main` method belongs to the **class itself**, not to an instance of the class. The JVM starts your program without creating any objects first, so `main` must be callable without an instance. We will cover `static` in depth in Lessons 05 and 06.
- `void` — the return type. `void` means the method returns nothing.
- `main` — the method name. The JVM specifically looks for a method called `main` with this exact signature when it starts your program.
- `String[] args` — the parameter. `String[]` is an array of `String` objects. `args` holds command-line arguments passed when you run the program (for example, `java HelloWorld foo bar` would give `args = ["foo", "bar"]`).
- `{` — opening brace of the method body.

```java
        System.out.println("Hello, World!");
```

- `System` — a built-in class in the `java.lang` package that contains useful system-level utilities.
- `.out` — a static field on `System` of type `PrintStream`. It represents standard output (your terminal).
- `println` — a method on `PrintStream` that prints its argument followed by a newline. (Note the lowercase `l` in `println` — it stands for "print line", not "print In".)
- `"Hello, World!"` — a string literal. Java strings use double quotes.
- `;` — every Java statement ends with a semicolon. The compiler will refuse to compile without it.

```java
    }
}
```

- `}` — closing brace of the `main` method.
- `}` — closing brace of the `HelloWorld` class.

That is a lot of words for "Hello, World!" — and that is Java. Every word is explicit, which makes Java verbose but also makes Java code readable to the next engineer who joins your team. You will get used to the verbosity quickly.

---

## 5. Java's Design Philosophy

Every programming language has a personality, and Java's personality is **explicit, verbose, object-oriented, and platform-independent**. The Java team made deliberate choices that prioritize readability and predictability over brevity. Where Python writes `print("hi")`, Java writes `System.out.println("hi")`. Where Go lets you write `func add(a, b int) int { return a + b }`, Java requires `public static int add(int a, int b) { return a + b; }`. Each keyword tells the reader exactly what is going on: the access level (`public`), the binding (`static`), the return type (`int`), and the parameter types are all spelled out. This is a feature when the next reader is a junior engineer joining a 500,000-line codebase.

Java is **strongly and statically typed**. Every variable has a declared type, and the compiler refuses to mix incompatible types. `int x = "hello";` is a compile error. You cannot add a `String` to an `int` without an explicit conversion. This catches bugs before your program runs, exactly like Rust and TypeScript do. The trade-off is verbosity — you write more types than in Python or JavaScript, but in return you get better autocomplete, safer refactoring, and self-documenting APIs.

Java is **object-oriented almost everywhere**. Everything lives inside a class. There are no standalone functions outside a class (well — Java 16+ introduced "records" and "static" methods, but they still live inside a class). There are no global variables. Even a string literal like `"hello"` is an instance of the `String` class with methods you can call (`"hello".length()`, `"hello".toUpperCase()`). The few exceptions are the 8 primitive types (`int`, `double`, `boolean`, etc.) which are not objects for performance reasons — Java provides "wrapper classes" (`Integer`, `Double`, `Boolean`) for when you need an object version.

Finally, Java is **platform-independent by design**. The JVM abstracts away the underlying operating system. Your code calls `System.out.println` and does not care whether it ends up on Windows, Mac, or Linux — the JVM translates the call appropriately. This is why a Java program built once can be deployed to a Windows desktop, a Linux server, and an IBM mainframe without recompiling. The price is performance overhead — Java programs start slower than Go programs because the JVM must load first, and memory usage is higher than in C or Rust. But for enterprise software that runs for months at a time, neither of those trade-offs matters much.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Verify Your Installation

Open a terminal and run `java --version` and `javac --version`. Write down both version numbers. If `java` reports anything below 17, go back to [adoptium.net](https://adoptium.net/) and install a newer version. Then run `java -version` (single dash, older form) — you should see additional details about the JVM (the "Runtime Environment" and "Server VM"). If both commands work and report version 17 or newer, your Java installation is complete and ready for the rest of the module.

### Exercise 2: Print Something Different

Open your `HelloWorld.java` file and change the message from `"Hello, World!"` to `"My name is [Your Name] and I am learning Java."`. Save the file, recompile with `javac HelloWorld.java`, and run with `java HelloWorld`. Confirm your custom message appears in the terminal. Notice that you must recompile after every change — Java does not run `.java` files directly; it runs `.class` files. If you forget to recompile, you will see the old output.

### Exercise 3: Pass a Command-Line Argument

Modify your program so it prints the first command-line argument instead of the fixed string. Replace the body of `main` with `System.out.println(args[0]);`. Recompile, then run `java HelloWorld Alice` — you should see `Alice` printed. Try running `java HelloWorld` with no arguments — you will see an `ArrayIndexOutOfBoundsException`. That is Java's way of telling you that you tried to access index 0 of an empty array. We will cover arrays and exceptions in later lessons; for now, just note the behavior.

---

## Common Mistakes

### Mistake 1: Filename does not match class name

```java
// File saved as Hello.java — note the mismatch with the class name
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

```java
// File saved as HelloWorld.java — class name and filename match exactly
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

Java's compiler requires that any `public class` be saved in a file whose name matches the class name exactly, including capitalization. If you save `public class HelloWorld` in a file called `Hello.java`, `javac` stops with `class HelloWorld is public, should be declared in a file named HelloWorld.java`. This rule exists so the compiler can find a class by looking for a file with the same name. The fix is to either rename the file or rename the class — pick one and make them match. (Non-`public` classes can live in any file, but for beginners the simplest rule is to always match filenames to class names.)

### Mistake 2: Forgetting the exact `main` signature

```java
// WRONG — missing "static", missing "String[] args", or wrong return type
public class HelloWorld {
    public void main() {
        System.out.println("Hello, World!");
    }
}
```

```java
// RIGHT — the JVM requires exactly this signature
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

The JVM looks for a method with the exact signature `public static void main(String[] args)` when it starts your program. If you forget `static`, omit the parameters, or change the return type, the JVM will silently fail to find your `main` and throw an error: `Error: Main method not found in class HelloWorld, please define the main method as: public static void main(String[] args)`. The fix is to type the signature exactly as written above. Modern IDEs like IntelliJ generate it for you with a shortcut, but until you use an IDE, type it carefully.

### Mistake 3: Using `print` when you mean `println`

```java
// WRONG — System.out.print does NOT add a newline
public class HelloWorld {
    public static void main(String[] args) {
        System.out.print("Hello, ");
        System.out.print("World!");
    }
}
// Output: Hello, World!  (no newline at the end, terminal prompt may run into it)
```

```java
// RIGHT — System.out.println adds a newline at the end
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, ");
        System.out.println("World!");
    }
}
// Output:
// Hello,
// World!
```

`System.out.println` (print **line**) adds a newline character at the end; `System.out.print` does not. If you forget the `ln`, your outputs will run together on one line and your terminal prompt may appear on the same line as your output, which looks confusing. If you see your output running into the next shell prompt, check whether you meant `println`. There is also `System.out.printf` (formatted printing, covered in Lesson 02), which lets you use C-style format specifiers like `%d` and `%s`.

### Mistake 4: Putting a semicolon after class or method declarations

```java
// WRONG — semicolons after the class and method braces
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    };
};
```

```java
// RIGHT — no semicolons after class or method declarations
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

In Java, semicolons terminate **statements**, not declarations. The closing brace of a class or method is its terminator; you do not put a `;` after it. A `;` after a `}` is technically legal in some contexts (it is treated as an "empty statement"), but it is bad style and confuses beginners because the file still compiles but the semicolon does nothing. Statements that need semicolons are things like `int x = 5;`, `System.out.println("hi");`, `return 0;`, and `import java.util.Scanner;`. Class declarations, method declarations, `if`/`for`/`while` blocks, and `switch` blocks do not get a semicolon after their closing brace.

---

## Summary

- Java is a general-purpose, statically-typed, object-oriented, garbage-collected language that runs on the **Java Virtual Machine (JVM)**.
- It was started in 1991 at Sun Microsystems by James Gosling's team, originally called **"Oak"**, renamed **"Java"** in 1995 after Java coffee, and acquired by Oracle in 2010.
- The **JVM** turns `.java` source files into `.class` bytecode files (via `javac`), then runs the bytecode on any operating system (via `java`). This is "Write Once, Run Anywhere."
- Install a JDK — **Eclipse Temurin** (free OpenJDK distribution from [adoptium.net](https://adoptium.net/)) is recommended. Pick **JDK 17 LTS** or **JDK 21 LTS**.
- Verify your installation with `java --version` (runtime) and `javac --version` (compiler). Both must work.
- Your first program is `public class HelloWorld { public static void main(String[] args) { System.out.println("Hello, World!"); } }`, saved as `HelloWorld.java`.
- Compile with `javac HelloWorld.java`, run with `java HelloWorld`. The filename MUST match the public class name exactly.
- Java is verbose by design — every keyword (`public`, `static`, `void`, `String[] args`) tells the next reader exactly what you meant.

You wrote, compiled, and ran your first Java program. The hardest step — getting the toolchain working — is done. In Lesson 02 we go deeper into the anatomy of a Java program: packages, imports, comments, and the various ways to print output.

---

**Next:** [Lesson 02: Hello, World! →](./02-hello-world.md)
