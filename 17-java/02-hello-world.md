# Lesson 02: Hello, World!

> Module: Java · Lesson 2 of 7
> Estimated time: 30–45 minutes

In Lesson 01 you wrote, compiled, and ran your first Java program. In this lesson we slow down and dissect the anatomy of a Java source file in detail. You will learn about packages (Java's way of organizing code), imports (Java's way of using code from other packages), comments (including Javadoc, which generates HTML documentation), and the different ways to print output. By the end you will be able to read a moderately complex Java file and understand what every line does.

Java's structure may feel rigid if you are coming from Python or JavaScript, where files are just scripts. In Java, every file is part of a class, every class is part of a package, and every package corresponds to a directory on disk. This strict structure is one of Java's superpowers in enterprise codebases — it makes large projects navigable because the file system mirrors the package structure. Take your time with this lesson; the conventions you learn here apply to every Java file you will ever write.

---

## Learning Objectives

After this lesson, you will be able to:

1. Describe the anatomy of a Java source file: package declaration, imports, class declaration, fields, and methods.
2. Use packages to organize your code and explain why the directory structure must match the package name.
3. Write all three styles of Java comments (line, block, Javadoc) and know when to use each.
4. Print output using `System.out.println`, `System.out.print`, and `System.out.printf` with format specifiers.

---

## 1. The Anatomy of a Java Program

Every Java source file follows a strict layout. Here is a more complete version of "Hello, World!" with every part labeled:

```java
package com.example.hello;           // 1. Package declaration (optional but recommended)

import java.util.Scanner;            // 2. Imports (one per line, after package)
import java.util.List;

public class HelloWorld {            // 3. Class declaration
    // Fields go here (variables inside the class)

    public static void main(String[] args) {   // 4. Method declaration
        System.out.println("Hello, World!");  // 5. Statements
    }
}
```

The order matters. A Java file follows this sequence, top to bottom:

1. **Package declaration** (optional but recommended). Declares which package this file belongs to. If omitted, the file is in the "default package" — fine for quick experiments, bad style for real projects.
2. **Imports**. Tell the compiler which classes from other packages you want to use without typing the full package name every time. Imports come after the package declaration and before the class declaration.
3. **Class declaration**. `public class ClassName { ... }`. There can be only one `public` class per file, and the file's name must match the class name. You may have additional non-`public` classes in the same file, but for beginners one class per file is the rule.
4. **Fields and methods**. Inside the class body, you declare fields (variables that belong to the class or its instances) and methods (functions that belong to the class or its instances).
5. **Statements**. Inside methods, you write statements — each one ending with a semicolon `;`. Statements do the actual work: print, assign, call methods, return values.

The braces `{` and `}` define blocks. A block can contain other blocks (a class contains a method, a method contains an `if` block, an `if` block contains a `for` loop, and so on). Java does not care about indentation — it only cares about braces and semicolons — but the convention is to indent four spaces per level, and IDEs like IntelliJ enforce this automatically. Comments are ignored by the compiler but read by humans; we cover them in Section 5 below.

The strict structure has practical consequences. The compiler can find any class by looking for a file whose path matches the package name plus the class name. If you write `com.example.hello.HelloWorld`, the compiler expects to find the file at `com/example/hello/HelloWorld.java` on disk. This is more rigid than Python (where imports work but you can put files anywhere) but it scales: a 500,000-line Java codebase is navigable because the file system is a map of the package hierarchy.

---

## 2. Compiling and Running

You saw the basic compile-and-run cycle in Lesson 01, but let's look at it more carefully. Suppose your source file is `HelloWorld.java`. Open a terminal in the folder containing the file and run:

```bash
javac HelloWorld.java
```

The `javac` command invokes the Java compiler. It reads `HelloWorld.java`, parses the source, checks the types, and — if everything is valid — produces one `.class` file for each class declared in your source. For our `HelloWorld` example, the compiler produces `HelloWorld.class`. If your source file declares additional non-public classes (say, a `Helper` class), the compiler produces `HelloWorld.class` AND `Helper.class`.

The `.class` file contains **bytecode** — instructions for the JVM, not for any specific CPU. Bytecode is binary (you cannot read it as text), but it is portable. The same `HelloWorld.class` file runs on Windows, Mac, and Linux, as long as each has a JVM installed.

Run the program with:

```bash
java HelloWorld
```

The `java` command launches the JVM. The argument `HelloWorld` is the **fully qualified class name** (or the simple name if no package) of the class whose `main` method the JVM should call. The JVM loads `HelloWorld.class`, finds the `main` method, and calls it.

Notice that you do NOT pass `HelloWorld.class` — you pass the class name `HelloWorld` without any extension. The JVM figures out where to find the bytecode file. If you write `java HelloWorld.class` (with the extension), you will get an error because Java interprets `.class` as part of the class name.

Here is the full cycle in one block:

```bash
javac HelloWorld.java   # produces HelloWorld.class
java HelloWorld         # runs the bytecode, prints "Hello, World!"
```

This two-step process — compile, then run — is the same pattern you will use for every Java program you write. Some IDEs hide the steps from you (IntelliJ's "Run" button does both at once), but underneath they are doing exactly this.

---

## 3. Using Packages

A **package** is a namespace for classes. You declare it with a `package` statement at the top of your source file:

```java
package com.example.hello;

public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello from a package!");
    }
}
```

The `package` statement must be the **very first line** of the file (ignoring comments and blank lines). It tells the compiler: "this class belongs to the `com.example.hello` package." The full name of this class is now `com.example.hello.HelloWorld`, not just `HelloWorld`. If you want to use this class from another file, you must either import it (`import com.example.hello.HelloWorld;`) or use its fully qualified name (`com.example.hello.HelloWorld`).

Why use packages? Two reasons. **First, to avoid name conflicts.** Imagine you write a `List` class for your project. Without packages, your `List` would clash with `java.util.List` from the standard library — the compiler would not know which one you meant. With packages, `com.example.List` and `java.util.List` are different types and the compiler can tell them apart. **Second, to organize code.** A package name is a path that mirrors the file system. The package `com.example.hello` lives in the folder `com/example/hello/`. This means a 500,000-line codebase is browsable — you can navigate it by folder.

The directory structure MUST match the package name exactly. If your file declares `package com.example.hello;`, the file must live at `com/example/hello/HelloWorld.java` on disk. To compile it from the project root, run:

```bash
javac com/example/hello/HelloWorld.java
java com.example.hello.HelloWorld
```

Notice that the `java` command uses dots (`com.example.hello.HelloWorld`) while the file system uses slashes (`com/example/hello/`). The JVM translates the dots in the class name to slashes when looking up the file. If the directory does not match the package, you get a runtime `NoClassDefFoundError` that is confusing for beginners. The rule is simple: **directory structure mirrors package name**.

Java's package naming convention is **reverse domain name**. If your company owns `example.com`, your packages start with `com.example`. Sun's own classes are in `java.lang`, `java.util`, `java.io` — these are not "reverse" because they predate the convention, but newer packages follow it (Jakarta EE uses `jakarta.*`, Spring uses `org.springframework.*`).

---

## 4. Imports

When you want to use a class from another package, you have two choices. You can write the fully qualified name every time (`java.util.Scanner sc = new java.util.Scanner(System.in);`), or you can import the class once at the top of the file and use its short name (`Scanner sc = new Scanner(System.in);`). The `import` statement lets you do the second.

There are two forms of imports:

```java
import java.util.Scanner;   // Import just the Scanner class
import java.util.*;         // Import every class in java.util (wildcard)
```

The first form imports one specific class. The second form (with `.*`) imports every public class in the package — but it does NOT recursively import sub-packages. `import java.util.*` gives you `Scanner`, `List`, `ArrayList`, `Map`, `HashMap`, `Random`, and dozens more, but it does NOT give you `java.util.concurrent.Executor` (a sub-package). You must add `import java.util.concurrent.*` separately.

The compiler resolves imports at compile time. Using `.*` does not slow down your program — the compiler pulls in only what you actually use. The choice between specific imports and wildcard imports is a style preference. Most Java codebases prefer specific imports because they make it explicit where each class comes from. IntelliJ IDEA has a setting to auto-expand wildcard imports into specific ones for you.

Some classes are **auto-imported**. The `java.lang` package contains classes so fundamental (String, System, Math, Integer, Object) that Java imports them for you automatically. You never need to write `import java.lang.String;` — just use `String` directly. The same applies to `System`, `Math`, `Object`, `Thread`, `Exception`, and every wrapper class (`Integer`, `Double`, `Boolean`, etc.). If you accidentally add `import java.lang.String;` to your file, the compiler lets it through with a warning that the import is redundant — it is not an error, just noise.

If two imported classes have the same name (for example, `java.util.Date` and `java.sql.Date`), you cannot import both with the short name. You must use the fully qualified name for one of them in your code: `java.util.Date d = new java.util.Date();`. This is rare but does happen in real codebases.

---

## 5. Comments

Java has three styles of comments:

```java
// This is a line comment. Everything from // to the end of the line is ignored.

/* This is a block comment.
   It can span multiple lines.
   Block comments are useful for temporarily disabling chunks of code. */

/** This is a Javadoc comment.
 *  It uses /** to begin and */ to end.
 *  Each line starts with a * (optional but conventional).
 *  Javadoc is used to generate HTML documentation with the `javadoc` tool.
 *  @param args command-line arguments
 */
```

Line comments (`//`) are the most common. Use them for short notes about a single line of code. Block comments (`/* ... */`) are useful for longer explanations or for temporarily commenting out multiple lines. Javadoc comments (`/** ... */`) are special — they describe classes and methods and can be processed by the `javadoc` tool (which ships with the JDK) to generate HTML documentation.

Here is an example with all three:

```java
package com.example.hello;

import java.util.Scanner;

/**
 * A simple program that reads the user's name and greets them.
 * @author Your Name
 */
public class Greeter {

    // The main method is where the JVM starts the program
    public static void main(String[] args) {
        /* Use Scanner to read input from the keyboard.
           System.in is the standard input stream. */
        Scanner scanner = new Scanner(System.in);
        System.out.print("What is your name? ");
        String name = scanner.nextLine();
        System.out.println("Hello, " + name + "!");
    }
}
```

The `javadoc` command processes the `/** ... */` blocks and produces HTML pages. For the file above, running `javadoc -d docs Greeter.java` would create a folder called `docs` containing HTML files you can open in a browser. Real Java libraries publish their Javadoc as a website — for example, the standard library docs at `docs.oracle.com/javase/17/docs/api/` are produced this way. Common Javadoc tags include `@param` (describes a parameter), `@return` (describes the return value), `@throws` (describes an exception), `@see` (cross-reference), and `@author` (author name). We will not write Javadoc heavily in this module, but you should recognize it when you see it.

---

## 6. Printing

Java's `System.out` is a `PrintStream` object that prints to standard output (your terminal). It has three main printing methods: `println`, `print`, and `printf`. Each has a different behavior.

```java
public class Printing {
    public static void main(String[] args) {
        System.out.println("Hello, World!");     // prints "Hello, World!" + newline
        System.out.print("Hello, ");              // prints "Hello, " (no newline)
        System.out.print("World!");               // prints "World!" (still no newline)
        System.out.println();                     // prints just a newline
        System.out.println("Line above is empty.");
    }
}
```

Output:

```text
Hello, World!
Hello, World!

Line above is empty.
```

- `System.out.println(x)` — prints `x` followed by a newline. If you call `println()` with no argument, it just prints a newline.
- `System.out.print(x)` — prints `x` with no newline. The next print call continues on the same line.
- `System.out.printf(format, args...)` — prints with **C-style format specifiers**. This is the most powerful of the three.

The `printf` method takes a format string with placeholders and a list of arguments to fill in. Common placeholders:

- `%d` — decimal integer (`int`, `long`)
- `%f` — floating-point number (`float`, `double`)
- `%s` — string
- `%c` — character
- `%n` — platform-specific newline (use this instead of `\n` in `printf` for portability)
- `%%` — literal percent sign

Here is an example:

```java
public class Formatted {
    public static void main(String[] args) {
        String name = "Alice";
        int age = 30;
        double height = 1.68;

        System.out.printf("Name: %s%n", name);
        System.out.printf("Age: %d years%n", age);
        System.out.printf("Height: %.2f meters%n", height);   // .2 means two decimal places
        System.out.printf("%s is %d years old and %.2f m tall.%n", name, age, height);
    }
}
```

Output:

```text
Name: Alice
Age: 30 years
Height: 1.68 meters
Alice is 30 years old and 1.68 m tall.
```

The `%.2f` format specifier tells `printf` to print a floating-point number with exactly two digits after the decimal point. `1.6789` becomes `1.68` (rounded). The `%n` is preferred over `\n` in `printf` because it produces the correct newline for the platform (`\r\n` on Windows, `\n` on Unix).

A useful alternative is `String.format(format, args...)`, which works like `printf` but returns the formatted string instead of printing it. Use it when you need the formatted string for something other than immediate printing (e.g., storing it in a variable or passing it to another method).

```java
String greeting = String.format("Hello, %s! You are %d.", name, age);
System.out.println(greeting);
```

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Add a Package

Take your `HelloWorld.java` from Lesson 01 and add `package com.example.hello;` as the first line. Move the file into a folder structure that matches the package name: `com/example/hello/HelloWorld.java`. From the project root (the folder containing `com/`), compile with `javac com/example/hello/HelloWorld.java` and run with `java com.example.hello.HelloWorld`. Confirm the program still prints `Hello, World!`. This exercise teaches you how the file system mirrors the package name.

### Exercise 2: Use `printf` for Formatting

Write a program that declares three variables — a String `name` (your name), an int `birthYear`, and a double `temperature` (today's temperature in Celsius). Use `System.out.printf` to print one sentence like: `Alice was born in 1994 and today's temperature is 22.50 degrees Celsius.` Note the `%.2f` format specifier to force two decimal places on the temperature. Try changing the temperature to `22.5` and confirm `printf` still prints `22.50`.

### Exercise 3: Read Input with Scanner

Add `import java.util.Scanner;` to your file. Inside `main`, write `Scanner scanner = new Scanner(System.in);` and then `String name = scanner.nextLine();`. Print a prompt like `System.out.print("What is your name? ");` BEFORE reading the input, then greet the user with their name. Compile and run; the program should wait for you to type a name and press Enter. (We will cover `Scanner` properly in Lesson 03 — for now, just confirm it works.)

---

## Common Mistakes

### Mistake 1: Filename does not match public class name

```java
// File saved as Hello.java — mismatch!
package com.example;
public class HelloWorld {  // public class is HelloWorld, but file is Hello.java
    public static void main(String[] args) { System.out.println("hi"); }
}
```

```java
// File saved as HelloWorld.java — match!
package com.example;
public class HelloWorld {
    public static void main(String[] args) { System.out.println("hi"); }
}
```

Every `public` class must be saved in a file whose name matches the class name exactly (including capitalization). The compiler stops with `class HelloWorld is public, should be declared in a file named HelloWorld.java`. Non-`public` classes can live in any file, but the simplest rule for beginners is: one `public` class per file, and the file name matches the class name. If you ever see this compiler error, rename the file (or the class) to make them match.

### Mistake 2: Forgetting to compile before running

```bash
# WRONG — you changed HelloWorld.java but forgot to recompile
$ java HelloWorld
Hello, World!    # old output, not your new code

# RIGHT — recompile, then run
$ javac HelloWorld.java
$ java HelloWorld
Hello, NEW message!    # fresh output
```

Java runs `.class` files, not `.java` files. If you edit `HelloWorld.java` and run `java HelloWorld` without recompiling, you are running the OLD bytecode. Your new code is sitting in the source file, but the JVM does not see it. Beginners often change their code, run the program, see no change, and assume the compiler is broken — the real issue is they forgot `javac`. Develop the habit of always running `javac` before `java`. IDEs like IntelliJ handle this for you (the Run button does both), but on the command line you must do it yourself.

### Mistake 3: Wrong package directory structure

```text
# WRONG — package says com.example but the file is in the wrong folder
$ cat HelloWorld.java
package com.example;
public class HelloWorld { ... }

$ javac HelloWorld.java      # compiles fine (compiler does not check folders)
$ java com.example.HelloWorld
Error: Could not find or load main class com.example.HelloWorld
Caused by: java.lang.NoClassDefFoundError
```

```text
# RIGHT — file lives in com/example/ folder
$ ls com/example/
HelloWorld.java

$ javac com/example/HelloWorld.java
$ java com.example.HelloWorld
Hello, World!
```

The JVM maps the dots in a package name to slashes on the file system. `com.example.HelloWorld` must live at `com/example/HelloWorld.class`. The compiler does not enforce the folder structure — it happily compiles a file with `package com.example;` from any folder — but the JVM cannot find the class at runtime if the folder structure is wrong. The fix is to put the file in the folder that matches the package name. If you see `Could not find or load main class` and your class definitely exists, check the folder structure.

### Mistake 4: Importing a class that is already auto-imported

```java
// WRONG — String, System, Math etc. are auto-imported from java.lang
package com.example;

import java.lang.String;     // redundant — String is auto-imported
import java.lang.System;     // redundant — System is auto-imported

public class HelloWorld {
    public static void main(String[] args) {   // String works without the import
        System.out.println("Hello, World!");   // System works without the import
    }
}
```

```java
// RIGHT — java.lang is auto-imported, do not import it explicitly
package com.example;

public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

The `java.lang` package is so fundamental that Java auto-imports every class in it. `String`, `System`, `Math`, `Object`, `Integer`, `Double`, `Boolean`, `Exception`, `Thread`, and dozens more are all available without an `import` statement. If you add `import java.lang.String;`, the compiler accepts it but prints a warning: `java.lang.String is already implicitly imported` (or similar). The fix is to delete the redundant import. IntelliJ will grey out redundant imports and offer to remove them for you. As a rule: never import anything from `java.lang`.

---

## Summary

- Every Java file follows a strict layout: package declaration, imports, class declaration, fields, methods.
- The `package` declaration must be the first line (ignoring comments). The directory structure on disk must mirror the package name.
- Use `import` to bring in classes from other packages. `import java.util.Scanner;` imports one class; `import java.util.*;` imports every class in the package (no recursion into sub-packages).
- The `java.lang` package is auto-imported — never write `import java.lang.String;` or `import java.lang.System;`.
- Java has three comment styles: `//` line, `/* */` block, and `/** */` Javadoc (processed by `javadoc` into HTML).
- Printing: `System.out.println` adds a newline; `System.out.print` does not; `System.out.printf` uses C-style format specifiers like `%d`, `%s`, `%.2f`, `%n`.
- The compile-and-run cycle is two steps: `javac HelloWorld.java` produces `HelloWorld.class`, then `java HelloWorld` runs it. Re-run `javac` every time you change the source.
- `String.format(fmt, args)` returns the formatted string instead of printing it — useful when you need the string for something else.

You can now read a moderately complex Java file and understand every line. In Lesson 03 we cover Java's type system in detail: the eight primitive types, reference types, `var`, type conversion, and the all-important difference between `==` and `.equals()` for comparing strings.

---

**Next:** [Lesson 03: Variables and Types →](./03-variables-and-types.md)
