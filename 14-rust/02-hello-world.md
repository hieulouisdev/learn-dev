# Lesson 02: Hello, World!

> Module: Rust · Lesson 2 of 7
> Estimated time: 30–45 minutes

Welcome to Lesson 02. In Lesson 01 you wrote a Rust program with a single line of code and compiled it with the bare `rustc` command. That works for tiny programs, but real Rust projects never use `rustc` directly — they use **`cargo`**, Rust's official build tool and package manager. In this lesson you will create a proper Cargo project, understand the file layout, learn how comments work in Rust, and finally understand why `println!` ends with that mysterious exclamation mark.

Cargo is one of the most loved parts of the Rust ecosystem. As you work through this lesson, you will see why: a single command scaffolds a project, manages dependencies, runs tests, builds optimized binaries, and even formats your code. By the end of this lesson you will have a mental map of every file Cargo creates for you and what each one is for.

---

## Learning Objectives

After this lesson, you will be able to:

1. Create a new Rust project with `cargo new` and explain the purpose of every file Cargo generates.
2. Read and write the `Cargo.toml` manifest, including the `[package]` section and the `edition` field.
3. Use all three kinds of Rust comments: `//`, `/* */`, and `///` doc comments.
4. Explain why `println!` is a macro rather than a function, and what compile-time benefits that gives you.

---

## 1. Creating a Project with Cargo

Open a terminal, navigate to the folder where you want to keep your Rust projects, and run this single command:

```bash
cargo new hello_world
```

Cargo responds by creating a new folder called `hello_world` with this exact structure:

```text
hello_world/
├── Cargo.toml
├── src/
│   └── main.rs
└── .git/
    └── (git repository initialized automatically)
```

Let's go through each piece. The outer folder `hello_world/` is your project root — everything related to this project lives inside it. The `src/` folder is where your Rust source code goes; Cargo convention is that all `.rs` files live inside `src/`. The `main.rs` file is the entry point of your program — Cargo generates it with a default `Hello, world!` program already inside. The `Cargo.toml` file at the project root is the **manifest** — it tells Cargo the project's name, version, edition, and lists its dependencies. Finally, the hidden `.git/` folder means Cargo has already initialized a Git repository for you, ready to commit.

### The `Cargo.toml` Manifest

Open `Cargo.toml` in your editor. It should look like this:

```toml
[package]
name = "hello_world"
version = "0.1.0"
edition = "2021"

[dependencies]
```

Let's break it down line by line.

- `[package]` — a TOML section header marking the start of the package metadata. Everything below this line, until the next section header, describes the package itself.
- `name = "hello_world"` — the package name. This is what Cargo uses to identify your project, what `cargo run` looks for, and what the binary will be called (after `cargo build`, you get `target/debug/hello_world`).
- `version = "0.1.0"` — the package version, using [Semantic Versioning](https://semver.org/). Bump the major number for breaking changes, the minor number for new features, the patch number for bug fixes.
- `edition = "2021"` — the **Rust edition** the project uses. Editions are how Rust evolves without breaking old code. The 2015 edition was the original, 2018 added module-system improvements, and 2021 (the current default) added a few small breaking changes around closures and iterators. You almost never need to think about editions as a beginner — just leave it at `2021`.
- `[dependencies]` — an (initially empty) section where you list third-party crates (Rust's word for packages). To use the popular `serde` serialization library, for example, you would add `serde = { version = "1.0", features = ["derive"] }` under this section, then run `cargo build` and Cargo would download and compile it automatically.

### The `src/main.rs` File

Open `src/main.rs`. Cargo has already written a default program for you:

```rust
fn main() {
    println!("Hello, world!");
}
```

You already know what this does from Lesson 01. The only difference from your hand-written `hello.rs` is the lowercase `w` in `"world!"` — Cargo's default message uses a lowercase `w`, the Rust convention. You can change it if you like.

---

## 2. Anatomy of a Rust Program

Let's dissect the default `main.rs` line by line. There is more going on than meets the eye.

```rust
fn main() {
    println!("Hello, world!");
}
```

- `fn` — keyword declaring a **function**. Functions are the basic unit of code organization in Rust, just like in Python or JavaScript.
- `main` — the special name of the program's entry point. When you run the executable, the operating system calls `main` first. Every executable Rust program must have exactly one `main` function.
- `()` — empty parameter list. This `main` takes no arguments. (In Lesson 05 you will see how to write functions with parameters.)
- `{` — opening curly brace marking the start of the function body. Rust uses curly braces for blocks, unlike Python which uses indentation.
- `println!` — the **macro** that prints a line of text to standard output, then a newline. The `!` marks it as a macro invocation.
- `("Hello, world!")` — the arguments to the macro, in parentheses. Here we pass a single string literal.
- `;` — the semicolon that ends the statement.
- `}` — closing curly brace marking the end of the function body.

### Format Strings

`println!` is far more powerful than just printing a fixed string. It supports **format strings** with `{}` placeholders that get replaced with the values of expressions you pass in:

```rust
fn main() {
    let name = "Ada";
    let year = 1815;
    println!("Hello, {}! You were born in {}.", name, year);
}
```

Let's break this down.

- `let name = "Ada";` — declares a variable named `name` and binds it to the string literal `"Ada"`. We will cover `let` in detail in Lesson 03.
- `let year = 1815;` — declares a variable named `year` bound to the integer `1815`. Rust infers the type as `i32` by default.
- `println!("Hello, {}! You were born in {}.", name, year);` — the format string contains two `{}` placeholders. The first `{}` is replaced by the value of `name`, and the second `{}` is replaced by the value of `year`.

When you run this, you will see:

```text
Hello, Ada! You were born in 1815.
```

The format string is checked at compile time: if you have two `{}` placeholders but only pass one argument, the program will not compile. This is one of the benefits of `println!` being a macro rather than a function — we will explore that in Section 5.

### Semicolons as Statement Separators

Every statement in Rust ends with a semicolon. Unlike Python (where newlines end statements) or JavaScript (where the parser often inserts semicolons for you), Rust is strict: forget a semicolon and you get a compile error. The semicolon turns an expression into a statement, which is a deeper concept we will explore in Lesson 05. For now, just remember: every line in a function body that does something needs a semicolon at the end.

---

## 3. Comments

Comments are notes you write in your code that the compiler ignores. They exist for humans reading your code — including your future self. Rust has three kinds of comments.

### Line comments with `//`

The most common comment is the **line comment**, which begins with two slashes `//` and runs to the end of the line:

```rust
fn main() {
    // Calculate the user's age in days
    let years = 30;
    let days = years * 365; // approximate, ignores leap years
    println!("You are about {} days old.", days);
}
```

Anything after `//` on the same line is ignored by the compiler. Line comments are perfect for short notes. They can also be placed at the end of a line of code, as shown above — the code before the `//` runs normally, and only the comment is ignored.

### Block comments with `/* */`

Rust also supports **block comments** that span multiple lines, opened with `/*` and closed with `*/`:

```rust
fn main() {
    /* This is a block comment.
       It can span multiple lines.
       Useful for longer explanations. */
    let x = 5;
    println!("x is {}", x);
}
```

Block comments are rarely used in idiomatic Rust — most developers prefer a series of `//` line comments because they are easier to comment out again later. Block comments do have one useful feature: they can be **nested**, so `/* outer /* inner */ still outer */` is a single valid comment, which makes them handy for temporarily disabling large blocks of code that already contain block comments.

### Doc comments with `///`

The third kind of comment is the **documentation comment**, opened with three slashes `///`. These are special: the compiler treats them as documentation, and `cargo doc` can render them into a searchable HTML documentation website:

```rust
/// Adds two numbers together and returns the result.
///
/// # Examples
///
/// ```
/// let sum = add(2, 3);
/// assert_eq!(sum, 5);
/// ```
fn add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    println!("2 + 3 = {}", add(2, 3));
}
```

The text inside `///` comments becomes the official documentation for the function. Run `cargo doc --open` in your project folder and Cargo generates a complete HTML documentation website for your crate, including any `///` comments you have written. The code blocks inside doc comments are even compiled and run as tests by `cargo test` — a feature called doctests — to make sure your examples actually work.

---

## 4. Running Your Code

Cargo gives you several commands for compiling and running your code. Each one is useful in a different situation. Let's cover the three you will use most.

### `cargo run` — compile and execute in one step

The most common command. From inside the `hello_world/` folder, run:

```bash
cargo run
```

Cargo checks whether your source files have changed since the last compile. If they have not, it just runs the existing binary. If they have, it recompiles (only the changed parts) and then runs the new binary. The first compile takes a second or two; subsequent runs are nearly instant because Cargo caches everything. You will see something like:

```text
   Compiling hello_world v0.1.0 (/Users/you/hello_world)
    Finished dev [unoptimized + debuginfo] target(s) in 0.45s
     Running `target/debug/hello_world`
Hello, world!
```

### `cargo build` — compile only, produce a debug binary

If you want to compile without running (perhaps to check for errors), use:

```bash
cargo build
```

This produces an executable at `target/debug/hello_world` (or `target\debug\hello_world.exe` on Windows). The `target/` folder is where Cargo puts all build artifacts — compiled binaries, intermediate object files, dependency build outputs. You should never edit anything inside `target/` directly; it is fully regenerated from your source code. The `debug` profile is **unoptimized** but compiles fast, which is what you want while developing.

### `cargo build --release` — compile with full optimizations

When you are ready to ship your program or benchmark its performance, add the `--release` flag:

```bash
cargo build --release
```

This produces an executable at `target/release/hello_world`. The `release` profile runs the LLVM optimizer at its highest setting, which can make your program 10x to 100x faster than the debug build — but compilation is much slower (often 30+ seconds for medium projects). Use `--release` for production binaries, benchmarks, and any time speed matters. Use the default (debug) build during development, because the faster compile-test cycle is more valuable than runtime speed while you are still writing the code.

---

## 5. Macros vs Functions

You have probably been wondering: why does `println!` have that exclamation mark? The answer is that `println!` is a **macro**, not a function. Macros and functions look similar when you call them, but they behave very differently under the hood.

A **function** is called at runtime: the arguments are evaluated, control jumps to the function body, the function runs, and control returns. The compiler does not know (or care) what the arguments *mean* — it just passes them along. This is why a Python `print(f"{x}")` or a C `printf("%d", x)` only fails at runtime if you pass the wrong type for the format specifier.

A **macro**, by contrast, is expanded at **compile time**. The macro's code runs as part of the compiler, takes the arguments you wrote (as raw syntax), and generates new Rust code that is then compiled normally. This means a macro can inspect the structure of its arguments — for example, it can check at compile time that a format string has the right number of `{}` placeholders for the arguments you passed.

Let's see a concrete benefit. Try to compile this program:

```rust
fn main() {
    let name = "Ada";
    // One placeholder, but two arguments — this should fail
    println!("Hello, {}!", name, 1815);
}
```

The Rust compiler refuses to build this, with an error like:

```text
error: 2 positional arguments passed but only 1 were used
```

This is impossible in languages where `printf` is a regular function — you would only discover the mismatch at runtime, when the program printed garbage or crashed. Because `println!` is a macro, the compiler catches the mismatch before your program ever runs. The same is true for type mismatches: `println!("{}", 5)` works, but `println!("{}", )` (no argument) is a compile error, not a runtime crash.

Macros are a big topic and you will not write your own in this module. For now, just remember: **the `!` after a name means it is a macro**, the macro is expanded at compile time, and the most common macros you will use are `println!`, `print!`, `eprintln!`, `vec![]`, `format!`, `assert!`, and `panic!`. Macros give Rust the safety of compile-time checks for things that would otherwise be runtime errors.

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Create and Run a Cargo Project

Open a terminal, navigate to a folder where you want to keep your Rust projects, and run `cargo new my_first_project`. Enter the new folder with `cd my_first_project`. Open `Cargo.toml` and `src/main.rs` in your editor and read them. Then run `cargo run` and confirm you see `Hello, world!` printed. Run `cargo build --release` and then run `./target/release/my_first_project` (or `.\target\release\my_first_project.exe` on Windows) to execute the optimized binary directly.

### Exercise 2: Use a Format String

Edit `src/main.rs` to use a format string with two placeholders. Declare two variables — one with your name, one with your age — and use `println!` to print a sentence using both. Recompile with `cargo run` and confirm the output looks like `My name is Ada and I am 30 years old.` Then deliberately pass the wrong number of arguments to confirm the compiler catches the mistake.

### Exercise 3: Write Doc Comments

Add a doc comment (`///`) above the `main` function explaining what your program does. Then run `cargo doc --open` from the project folder. Your browser should open and display the HTML documentation Cargo generated, with your doc comment rendered nicely. Try adding a `# Examples` section with a code block — `cargo doc` will format it as a runnable example, and `cargo test` will actually compile and run it.

---

## Common Mistakes

### Mistake 1: Forgetting the `!` on `println!`

```rust
// WRONG — println is a macro, not a function
fn main() {
    println("Hello, world!");
}
```

```rust
// RIGHT — the ! is required to invoke the macro
fn main() {
    println!("Hello, world!");
}
```

This is the same mistake from Lesson 01, but it bites every Rust beginner at least three times. If you forget the `!`, the compiler error will say something like `cannot find function \`println\` in this scope` and suggest you might have meant `println!`. Just add the `!` and move on. After a few days of practice your fingers will type `println!` automatically.

### Mistake 2: Mixing up `cargo run` and `cargo build`

```bash
# WRONG — cargo build does not run your program
cargo build
# Where is my output?!
```

```bash
# RIGHT — cargo run compiles and runs in one step
cargo run
```

`cargo build` only compiles — it produces a binary in `target/debug/` but does not execute it. If you run `cargo build` and expect to see your program's output, you will be confused. Use `cargo run` when you want to compile and immediately execute. Use `cargo build` only when you want to check that your code compiles without running it (for example, when iterating on a function with `println!` statements that you do not want to spam your terminal).

### Mistake 3: Editing files inside `target/`

```text
# WRONG — never edit anything inside target/
target/debug/hello_world   ← do not edit this binary
target/debug/hello_world.d  ← do not edit this file
```

```bash
# RIGHT — only edit your source files in src/
src/main.rs
Cargo.toml
```

The `target/` folder is entirely machine-generated. Cargo regenerates it from your source code every time you build. If you try to "fix" a problem by editing a file in `target/`, your change will be overwritten on the next `cargo build` and your fix is lost. Worse, you might introduce inconsistencies that confuse the compiler. Treat `target/` as a black box — only ever edit files in `src/` and `Cargo.toml`. If you ever want a truly clean build, run `cargo clean` to delete `target/` entirely.

### Mistake 4: Forgetting `fn main()` in an executable

```rust
// WRONG — no main function means this is not a runnable program
println!("Hello, world!");
```

```rust
// RIGHT — every executable Rust program starts with fn main()
fn main() {
    println!("Hello, world!");
}
```

In Python you can write a single line `print("Hello")` in a file and run it. In Rust, every executable program must have a `fn main()` function — that is where execution begins. If you write top-level statements without wrapping them in `main`, the compiler will complain with `expected item, found \`println\`` or a similar syntax error. (Note: library crates do not have a `main`, but you will not be writing those yet — Lesson 01 and 02 use only binary crates.)

---

## Summary

- Real Rust projects use `cargo new <name>` to scaffold a project, not `rustc` directly.
- A new Cargo project has `Cargo.toml` (the manifest) at the root and `src/main.rs` (the entry point) inside `src/`.
- `Cargo.toml` uses TOML format and has sections like `[package]` (name, version, edition) and `[dependencies]` (third-party crates).
- The `edition = "2021"` field selects which Rust edition the project uses; you almost never need to change it.
- Rust has three comment kinds: `//` line comments, `/* */` block comments (rare), and `///` doc comments (rendered by `cargo doc`).
- `cargo run` compiles and runs in one step; `cargo build` only compiles; `cargo build --release` produces an optimized binary.
- `println!` is a **macro**, not a function — the `!` marks it. Macros are expanded at compile time, which lets the compiler check format strings.
- The `target/` folder is machine-generated — never edit files inside it directly.

You now have a proper Cargo project and understand every file in it. In Lesson 03 we will start writing real Rust code that does more than print a fixed string — you will learn about variables, immutability, primitive types, and the surprising concept of "shadowing."

---

**Next:** [Lesson 03: Variables and Types →](./03-variables-and-types.md)
