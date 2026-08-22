# Module 14: Rust — Fearless Systems Programming

<p align="center">
  <img src="../logorepo.png" alt="Learn Dev Logo" width="120" height="120" />
</p>

Welcome to Module 14. You have come a long way — HTML for structure, CSS for style, and Python for general-purpose programming. Now we descend one layer closer to the metal and meet **Rust**, a modern systems programming language that promises the speed of C++ with the safety of languages like Java or Python — but without a garbage collector. Rust is what powers critical pieces of Firefox, parts of the Windows kernel, Discord's backend, Cloudflare's edge, and countless command-line tools you already use.

This module is harder than Python, so budget extra time. The payoff is huge: by the end you will understand memory in a way that makes every other language you ever learn feel simpler. Rust forces you to think clearly about who "owns" each piece of data, and that mental model transfers to C, C++, Go, and even garbage-collected languages. Take it slow, type every example, and welcome the compiler as your teacher — its error messages are famously helpful.

---

## What is Rust?

Rust is a **statically-typed, compiled systems programming language** sponsored by the Rust Foundation and originally developed at Mozilla. "Statically-typed" means every variable has a type known at compile time — the compiler catches type errors before your program ever runs. "Compiled" means Rust source code is translated directly into machine code ahead of time, producing a stand-alone executable that runs as fast as hand-written C. "Systems programming" means Rust is designed for the low-level, performance-critical work — operating systems, browsers, game engines, file systems, embedded devices — where every millisecond and every byte matters.

The language was created by a Mozilla employee named **Graydon Hoare** as a personal project in 2006. He had returned to his apartment in Vancouver to find his elevator had crashed (yet again) because of a memory bug in the elevator's software. Frustrated by how often C and C++ programs crash because of memory issues that compilers *could* catch, he started sketching a language that would make those bugs impossible. Mozilla adopted the project in 2009, announced it publicly in **2010**, and after five years of intense refinement shipped the stable **1.0 release in May 2015**. Today the language is governed by the **Rust Foundation**, a non-profit whose members include Amazon, Google, Microsoft, Meta, Huawei, and the Linux Foundation.

The single feature that makes Rust famous is **memory safety without a garbage collector**. In most safe languages (Java, Python, JavaScript, Go, C#), a runtime garbage collector periodically scans memory and frees what is no longer used — this is convenient but costs speed and predictability. In Rust, safety is enforced at **compile time** by a set of rules called **ownership and borrowing**. The compiler literally refuses to build a program that could leak memory, free memory twice, or read freed memory. The runtime cost is zero. This is Rust's defining innovation and the reason it has been voted **Stack Overflow's "most-loved language"** every year from 2016 through 2024 — developers who try Rust tend to love it.

Under the hood, Rust uses **LLVM** as its backend optimizer, the same compiler infrastructure used by Clang for C and C++ and by Swift. Rust binaries are typically as fast as equivalent C, often faster than Go, and dramatically faster than Python or JavaScript. The language is used in production by **Mozilla** (most of Firefox's CSS engine and rendering code), **Discord** (rewrote a read-states service from Go to Rust and cut latency spikes dramatically), **Dropbox** (core storage engine), **Cloudflare** (edge networking), and **Microsoft** (parts of the Windows kernel are being rewritten in Rust). In 2023, Rust was officially accepted into the Linux kernel — a historical milestone, as Linux had been C-only for over thirty years.

---

## Why Learn Rust?

You already know Python. Why add Rust now? Here are the strongest reasons.

- **Performance comparable to C and C++.** Rust compiles to native machine code with no runtime and no garbage collector, which means your programs run at the same speed as carefully written C. If you have ever written a Python script that was too slow and wondered how people write fast native code, Rust is the answer. The same `cargo build --release` command that takes two seconds to run produces an executable that runs in nanoseconds per operation.

- **Memory safety with no runtime cost.** Rust's compiler catches an entire category of bugs — use-after-free, double-free, null pointer dereferences, data races — at compile time, before your code ever runs. This means production Rust programs almost never crash with the segmentation faults and memory corruption that plague C and C++ codebases. You get the safety of Java or Python without paying for a garbage collector.

- **Modern tooling built around `cargo`.** Rust ships with a best-in-class build tool and package manager called `cargo`. One command (`cargo new`) scaffolds a project, one command (`cargo run`) compiles and runs it, one command (`cargo build --release`) produces an optimized binary, one command (`cargo test`) runs your test suite, one command (`cargo fmt`) formats your code, and one command (`cargo clippy`) lints it. The tooling is so good that other language ecosystems have copied it.

- **A growing, well-paying job market.** Rust job postings have grown every year for the past five years, and Rust engineers are consistently among the highest-paid developers in salary surveys. Roles in systems programming, blockchain, embedded systems, infrastructure, game engines, and increasingly web backends (via the `axum` and `actix-web` frameworks) all list Rust as a desired or required skill. The ecosystem is also friendly to remote work.

- **It teaches you how memory really works.** Even if you spend most of your career writing Python or JavaScript, learning Rust will make you a better developer in those languages too. The concepts of ownership, borrowing, and lifetimes reveal what garbage collectors actually do for you, why some operations are expensive, and how data moves through a program. It is the closest you can get to "learning how computers actually work" without writing assembly by hand.

---

## How This Module Works

This module contains 7 lessons. Each lesson is a separate Markdown file in this folder. Read them in order — Rust is unforgiving about gaps in your understanding, and Lesson 06 (ownership) depends on every concept in Lessons 01 through 05.

| # | Lesson | What You Will Learn |
|---|--------|---------------------|
| 01 | [What is Rust?](./01-what-is-rust.md) | What Rust is, how to install it with `rustup`, and your first program. |
| 02 | [Hello, World!](./02-hello-world.md) | `cargo new`, the project layout, comments, and macros vs functions. |
| 03 | [Variables and Types](./03-variables-and-types.md) | `let`, `mut`, shadowing, primitive types, tuples, arrays. |
| 04 | [Control Flow](./04-control-flow.md) | `if` expressions, `loop`, `while`, `for`, `match`, `if let`. |
| 05 | [Functions](./05-functions.md) | `fn`, statements vs expressions, return values. |
| 06 | [Ownership and Borrowing](./06-ownership-and-borrowing.md) | The defining Rust concept — moves, references, the borrow checker. |
| 07 | [Rust Cheatsheet](./07-rust-cheatsheet.md) | A printable one-page summary of everything. |

---

## How to Practice

For this entire module, you will need three things. The setup takes about 10 minutes and you only do it once.

1. **Install Rust via `rustup`.** Go to [rustup.rs](https://rustup.rs/) and follow the instructions for your operating system. On Mac and Linux, the installer gives you a one-line command that you paste into your terminal:

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   On Windows, download `rustup-init.exe` from the same site and run it. The installer downloads `rustc` (the compiler), `cargo` (the package manager), and `std` (the standard library). Once it finishes, close and reopen your terminal so the new commands are on your PATH. Verify with `rustc --version` and `cargo --version`.

2. **Install Visual Studio Code** with the official `rust-analyzer` extension. Download VS Code from [code.visualstudio.com](https://code.visualstudio.com/). Once installed, open the Extensions panel (`Ctrl+Shift+X` / `Cmd+Shift+X`), search for "rust-analyzer" (the official Rust language server), and click Install. This gives you inline type hints, autocompletion, go-to-definition, and inline error squiggles — the same compiler that builds your code now highlights problems in your editor as you type.

3. **Use `cargo` to create and run projects.** In a terminal, navigate to a folder where you want to keep your code, then run `cargo new hello_world` to scaffold a new project. Enter the folder with `cd hello_world`, run `cargo run` to compile and execute it, and you will see `Hello, world!` printed. For an optimized build that you can ship to friends, run `cargo build --release` — the resulting binary lives in `target/release/hello_world` and runs as fast as the compiler can make it.

---

## Estimated Time

If you spend 30 to 60 minutes per lesson (reading carefully, typing out every example, and doing the exercises), you should complete this module in roughly **12 to 18 hours of total study**. Rust takes longer than Python because of ownership and borrowing — these concepts are not difficult once they click, but they are genuinely new and your brain will need time to absorb them. Do not rush Lesson 06.

If you get stuck, the Rust compiler's error messages are famously helpful — read them carefully, they usually tell you exactly what to fix. The official book, [The Rust Programming Language](https://doc.rust-lang.org/book/), is free online and is an excellent companion reference. Take breaks often. Ownership is the single hardest concept in this module, and it is normal to re-read Lesson 06 two or three times before it fully clicks.

---

<p align="center">
  Ready? Open <a href="./01-what-is-rust.md">Lesson 01: What is Rust?</a> and write your first Rust program.
</p>
