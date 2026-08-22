# Lesson 01: What is Rust?

> Module: Rust · Lesson 1 of 7
> Estimated time: 30–45 minutes

Welcome to your first Rust lesson. In the next 30 minutes, you will learn what Rust is, where it came from, how to install the toolchain on your computer, and how to write and run your first Rust program. By the end, you will have typed `println!("Hello, World!")` into a real Rust source file and seen the compiler turn it into a running executable.

You do not need any prior systems-programming experience for this lesson. If you completed the Python module, you already understand variables, functions, and control flow — and that is enough. Rust feels different from Python at first because it is statically typed and compiled, but the ideas are the same. Take a deep breath, open a terminal, and let's begin.

---

## Learning Objectives

After this lesson, you will be able to:

1. Explain, in plain English, what Rust is, where it came from, and why it exists.
2. Install the Rust toolchain with `rustup` on Windows, Mac, or Linux and verify it works.
3. Distinguish between `rustc`, `cargo`, and `rustup` — the three tools that ship with Rust.
4. Write your first Rust program (`println!("Hello, World!");`) and understand what each character does, including the unusual `!` after `println`.

---

## 1. What is Rust, Really?

Rust is a **statically-typed, compiled systems programming language** that promises the speed of C and C++ without their famous safety bugs. "Statically-typed" means every variable has a type known to the compiler at compile time — type mismatches are caught before your program ever runs. "Compiled" means Rust source code is translated directly into native machine code ahead of time, producing a stand-alone executable that runs without a runtime or interpreter. "Systems programming" means Rust is designed for low-level, performance-critical work — operating systems, browsers, game engines, file systems, embedded devices, and other software where every microsecond and every byte of memory matters.

The language was started as a personal project in **2006** by a Mozilla employee named **Graydon Hoare**. The story goes that he returned to his apartment in Vancouver to find his elevator had crashed yet again — the cause was a memory bug in the elevator's software, the kind of bug C and C++ compilers cannot catch. Frustrated, Hoare began sketching a language that would make those bugs impossible by design. Mozilla adopted the project in 2009, publicly announced Rust at a conference in **2010**, and after five more years of intense refinement shipped the stable **1.0 release in May 2015**. From that point on, Rust has guaranteed backward compatibility: code written for 1.0 in 2015 still compiles today on Rust 1.70+.

The name "Rust" is, fittingly, somewhat obscure. Hoare has said the name was inspired by **rust fungi** — a kind of fungus — but the connection is more poetic than technical. The community's official mascot, however, is much more famous: **Ferris the crab**, a friendly orange crab who appears in error messages, conference talks, and community art. If you see crab plushies at programming conferences, those are Ferrises. The community refers to itself affectionately as "Rustaceans," a play on "crustacean."

Why does Rust exist at all? Because the existing choices force a painful trade-off. **C and C++ are fast but unsafe** — it is trivially easy to write a C program that crashes the whole process with a segmentation fault, leaks memory, or has a security hole. **Java, Python, JavaScript, and Go are safe but slow** — they use a garbage collector that periodically pauses your program to reclaim memory, which costs performance and predictability. Rust aims to give you **both safety and speed** by enforcing memory-safety rules at compile time, with zero runtime cost. The rules are called **ownership and borrowing**, and you will meet them in Lesson 06. For now, just know that Rust's defining promise is: *if your code compiles, it will not crash with a memory bug.*

---

## 2. Installing Rust

Before you can write Rust, you must install the Rust toolchain — the compiler, the package manager, and the standard library. The official and recommended way to do this on every operating system is a small installer called **`rustup`**. `rustup` is a "version manager" for Rust: it installs the toolchain, lets you switch between stable and nightly versions, and can update everything with one command (`rustup update`). It is the Rust equivalent of `nvm` for Node or `pyenv` for Python, except it is the officially-blessed tool — almost every Rust developer uses it.

### On Mac and Linux

Open a terminal and paste this one-line installer command, copied from [rustup.rs](https://rustup.rs/):

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Let's break this down. `curl` downloads the installer script from the official Rust URL. The flags `--proto '=https'` and `--tlsv1.2` force `curl` to use modern HTTPS only, so the download cannot be silently downgraded to insecure HTTP. The `-sSf` flags tell `curl` to be quiet (`-s`) but still show errors (`-S`), and to fail on HTTP errors instead of saving an error page (`-f`). The output is piped (`|`) into `sh`, which runs the script. The installer will ask you a few questions — the defaults are correct for almost everyone — then download and install the toolchain. When it finishes, close and reopen your terminal so the new commands are on your PATH.

### On Windows

Go to [rustup.rs](https://rustup.rs/) and download `rustup-init.exe`. Double-click it to run the installer in a terminal window (PowerShell or Command Prompt both work). The installer will offer to install the MSVC toolchain by default, which requires the Visual Studio C++ Build Tools — the installer will detect whether you have them and offer to download them if you do not. Follow the prompts and accept the defaults. When it finishes, close and reopen your terminal.

### Verifying the Installation

Open a fresh terminal and type these two commands. You should see version banners.

```bash
rustc --version
cargo --version
```

If you see something like `rustc 1.74.0 (79eaff1f7 2023-11-13)` and `cargo 1.74.0 (ecb9851af 2023-10-18)`, congratulations — Rust is installed and ready. If you see `command not found`, your shell did not pick up the PATH change — close every terminal window and open a new one, or run `source "$HOME/.cargo/env"` (Mac/Linux) to reload the environment. If you are on Windows and still see `command not found`, run `rustup` once to confirm it is installed; if it is, your PATH may need a manual fix.

---

## 3. The Toolchain

Rust ships with three command-line tools. You will use all of them constantly, so let's get familiar with each one before writing any code.

### `rustc` — the compiler

`rustc` is the Rust compiler itself. It takes a `.rs` source file and turns it into a native executable. For a single-file program you can run `rustc hello.rs` and get an executable named `hello` (or `hello.exe` on Windows). In practice you will almost never invoke `rustc` directly — `cargo` calls it for you — but it exists under the hood and is the tool that actually does the work of compiling.

### `cargo` — the build tool and package manager

`cargo` is Rust's all-in-one build tool, package manager, test runner, documentation generator, and formatter. It is the tool you will actually use 99% of the time. Run `cargo new my_project` to scaffold a new project, `cargo run` to compile and execute it, `cargo build` to compile only, `cargo build --release` to produce an optimized binary, `cargo test` to run tests, `cargo fmt` to format your code, and `cargo clippy` to lint it. `cargo` downloads dependencies from [crates.io](https://crates.io/) (the Rust package registry) and pins them in a file called `Cargo.lock`. It is one of the most loved features of the Rust ecosystem.

### `rustup` — the version manager

`rustup` is the installer and version manager you used to install Rust in the first place. It is also how you update Rust: run `rustup update` to download the latest stable release. It can install alternate toolchains like the `nightly` build (for unstable features) or specific older versions, and it can switch between them per-project. For most beginners, you will only ever run `rustup update` from time to time and otherwise forget `rustup` exists — which is exactly the point.

---

## 4. Your First Program

It is finally time to write Rust. Open a terminal, create a folder for your Rust experiments (call it `rust-playground` or whatever you like), and inside it create a file called `hello.rs` with your code editor. Type this exact code into the file:

```rust
fn main() {
    println!("Hello, World!");
}
```

Save the file. Then compile and run it with a single command:

```bash
rustc hello.rs -o hello && ./hello
```

On Windows, the executable will be `hello.exe` instead of `hello`, so the command becomes `rustc hello.rs -o hello.exe && .\hello.exe`. Either way, you should see this printed in your terminal:

```text
Hello, World!
```

**Congratulations. You just wrote, compiled, and ran your first Rust program.**

Let's break this single program into its parts, because every character matters and Rust syntax is pickier than Python's.

- `fn` — the keyword that declares a **function** (short for "function"). Every executable Rust program must have a function named `main` — that is where execution begins.
- `main` — the name of the function. The parentheses `()` after the name indicate this function takes no parameters. The empty pair of curly braces `{ }` contains the function body.
- `println!` — the name of a **macro** that prints a line of text to the terminal. The exclamation mark `!` is what makes this a macro rather than a regular function call. We will explain macros in Lesson 02 — for now, just remember the `!` is required.
- `"Hello, World!"` — a **string literal**. Anything between matching double quotes is text data. Rust treats whatever is between the quotes as a single piece of text, including spaces and punctuation.
- `;` — the semicolon that ends the **statement**. In Rust, every statement (with a few exceptions you will meet later) must end with a semicolon. Forgetting the semicolon is one of the most common beginner errors.
- The whole thing — `println!("Hello, World!");` — is one complete statement. Rust reads the statement, runs it, then moves to the next line.

---

## 5. Rust's Big Promise

Now that you have a working installation, let's talk about why Rust is worth your time. Rust makes three big promises, and the rest of this module is essentially a tour of how each promise is kept.

**Memory safety without a garbage collector.** In most safe languages (Java, Python, JavaScript, Go), a runtime garbage collector periodically scans memory and frees what is no longer used. This is convenient but costs speed and predictability. Rust enforces memory safety at **compile time** through a system of rules called ownership and borrowing — the compiler refuses to build a program that could leak memory, free memory twice, or read freed memory. The runtime cost is zero. You will meet ownership in Lesson 06.

**Fearless concurrency.** Most concurrency bugs in other languages — data races, deadlocks, shared-state mutations gone wrong — are also memory-safety bugs. Because Rust already enforces memory safety at compile time, it gets concurrent safety almost for free: if two threads could access the same data in an unsafe way, the compiler refuses to build your program. The slogan "fearless concurrency" means you can write multi-threaded code without the usual terror.

**Zero-cost abstractions.** High-level features in Rust — iterators, generics, traits, async/await — compile down to machine code that is as fast as the equivalent hand-written low-level code. You do not pay a runtime penalty for using abstractions. A `for` loop over a `Vec` with a closure, when compiled in release mode, often produces the exact same machine code as a hand-written C `for` loop with index arithmetic. This is the principle Rust inherited from C++: *what you don't use, you don't pay for; what you do use, you couldn't write better by hand.*

---

## 6. What Happens When You Compile

When you ran `rustc hello.rs -o hello` earlier, a lot happened in that fraction of a second. It is worth understanding the high-level pipeline, because the same five stages run every time you build a Rust project — and several Rust error messages refer to them by name. Knowing the order will help you read error messages and reason about why the compiler is asking you a particular question.

The five stages are: **parsing**, **macro expansion**, **type checking** (also called the "borrow check" stage for the ownership-specific checks), **LLVM code generation**, and **linking**. First the compiler reads your source code as text and parses it into an abstract syntax tree (AST). Then it expands any macros it finds — `println!`, `vec!`, and so on — into ordinary Rust code, because macros are just shorthand that gets rewritten at compile time. Then it type-checks the result and runs the borrow checker, which is where most ownership and borrowing errors get caught. If all of that passes, the AST is handed to LLVM, which generates optimized machine code for your platform. Finally the linker combines your machine code with the Rust standard library and any dependencies, producing the stand-alone executable you can run.

The important takeaway is that all of this happens **before** your program ever runs. That is why a Rust program never crashes with a "type error at runtime" or a "null pointer exception" the way Python or JavaScript can — those bugs were caught during type checking, long before the program started. The trade-off is that the compile-time experience is stricter: you will spend more time fixing compiler errors, but in exchange your running program almost never crashes for the reasons that plague C, C++, or Python code. As the Rust community likes to say, "if it compiles, it usually works."

---

## Try It Yourself

Before moving on, do these three exercises. They should take 5–10 minutes each.

### Exercise 1: Verify Your Installation

Open a terminal and run `rustc --version` and `cargo --version`. Write down both version numbers you see. Then run `rustup --version` to confirm the version manager is installed too. If all three commands print version banners, your installation is complete and you are ready to write Rust.

### Exercise 2: Print Something Different

Open your `hello.rs` file and change the message from `"Hello, World!"` to `"My name is [Your Name] and I am learning Rust."`. Save the file, recompile with `rustc hello.rs -o hello`, and run the executable. Confirm your custom message appears in the terminal. Notice how fast compilation is — under a second for a program this small.

### Exercise 3: Try the REPL

Install the Rust REPL with `rustup component add rust-analyzer` (optional, but useful for editor support) and explore the Rust playground at [play.rust-lang.org](https://play.rust-lang.org/). Paste the `hello.rs` program into the playground, click Run, and observe the output. The playground is a great way to try snippets when you cannot use your own terminal.

---

## Common Mistakes

### Mistake 1: Forgetting the `!` after `println`

```rust
// WRONG — println without the ! is not a valid macro call
fn main() {
    println("Hello, World!");
}
```

```rust
// RIGHT — the ! marks this as a macro invocation
fn main() {
    println!("Hello, World!");
}
```

In Rust, `println!` is a **macro**, not a function. Macros are invoked with an exclamation mark after the name. If you forget the `!`, the compiler will give you an error like `cannot find function \`println\` in this scope` and suggest you might have meant the macro. Just add the `!` and the error disappears.

### Mistake 2: Using `=` where Rust wants `==`

```rust
// WRONG — single = is assignment, not comparison
fn main() {
    let x = 5;
    if x = 6 {
        println!("x is six");
    }
}
```

```rust
// RIGHT — == is the equality comparison operator
fn main() {
    let x = 5;
    if x == 6 {
        println!("x is six");
    }
}
```

In some languages (notably C and older JavaScript), `if (x = 6)` is valid syntax that silently assigns `6` to `x` and then tests whether `6` is truthy — a classic source of bugs. Rust refuses to compile this. The single `=` is an assignment operator, and `if` requires a boolean expression — so the compiler stops you with `mismatched types, expected \`bool\`, found integer`. Always use `==` for comparison.

### Mistake 3: Forgetting the semicolon

```rust
// WRONG — missing semicolon at the end of the statement
fn main() {
    println!("Hello, World!")
}
```

```rust
// RIGHT — every statement ends with a semicolon
fn main() {
    println!("Hello, World!");
}
```

In Rust, a semicolon turns an expression into a statement. Inside a function body, statements must end with semicolons. If you forget one, you will get an error pointing at the line — often `expected \`;\`` or something about the next line. The fix is always the same: add the missing semicolon.

### Mistake 4: Skipping `rustup` and trying to install Rust from a package manager

```bash
# WRONG — apt's rustc is often years out of date
sudo apt install rustc
```

```bash
# RIGHT — use rustup to get the latest stable Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Some Linux distributions package Rust in their official repositories, but those packages are often months or years behind the latest stable release and do not include `rustup`. You will end up with an old `rustc`, no `cargo` updates, and confusing errors when you try to use modern features. Always install Rust with `rustup` — it is the only method the Rust project officially supports, and it ensures you can update with `rustup update` whenever a new version ships.

---

## Summary

- Rust is a statically-typed, compiled systems programming language sponsored by the Rust Foundation and originally developed at Mozilla.
- It was started as a personal project by Graydon Hoare in 2006, announced by Mozilla in 2010, and shipped stable 1.0 in May 2015.
- The name comes from "rust fungi" (somewhat obscure); the community mascot is Ferris the crab, and Rust developers call themselves "Rustaceans."
- Rust exists because C/C++ are fast but unsafe, while Java/Python are safe but slow — Rust aims to be both fast and safe.
- Install Rust with `rustup` — the official installer and version manager. On Mac/Linux: `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`.
- The three tools you installed are `rustc` (compiler), `cargo` (build tool and package manager), and `rustup` (version manager).
- Your first program is `fn main() { println!("Hello, World!"); }` — note the `!` after `println`, which marks it as a macro.
- Rust's big three promises: memory safety without a garbage collector, fearless concurrency, and zero-cost abstractions.

You wrote and ran your first Rust program. The hardest step — getting the toolchain working — is done. In Lesson 02 we will replace the bare `rustc` workflow with `cargo`, Rust's excellent project manager, and learn why every Rust project in the world starts with `cargo new`.

---

**Next:** [Lesson 02: Hello, World! →](./02-hello-world.md)
